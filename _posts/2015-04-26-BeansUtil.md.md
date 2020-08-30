---
layout: post
title: BeanUtils
---
지난번 ObjectMapper에 이어 객체바인딩을 조금 더쉽게할 수 있는 BeanUtil에 대해 소개하겠다.
사실 Java Reflection을 사용하여 객체 속성을 파악하고, 동적으로 객체 처리를 해줄 수 있지만 이 부분은 어느정도 귀차니즘?과 까다로움을 수반하는 일이므로
BeansUtils와 같은 API를 적절히 사용하는것도 유연한 개발을 위해 필요하다.
org.apache.commons.beanutils 패키지에 포함되어있는  BeanUtils에 대해 알아보겠다.

*순수히 공부를하며 쓴 글이기에 부족한 점이 많으므로, 수정할 점이 있다면 댓글 달아주셨으면 좋겠습니다.^^

먼저 BeanUtils 클래스를 보면, 모든 함수는 static으로 선언되어있으며 BeanUtilsBean 싱글톤 클래스를 사용한다.
예를 들면  아래와 같다.
{% highlight ruby %}
    public static Object cloneBean(Object bean) throws IllegalAccessException, InstantiationException, InvocationTargetException, NoSuchMethodException {
        return BeanUtilsBean.getInstance().cloneBean(bean);
    }

    public static void copyProperties(Object dest, Object orig) throws IllegalAccessException, InvocationTargetException {
        BeanUtilsBean.getInstance().copyProperties(dest, orig);
    }

    public static void copyProperty(Object bean, String name, Object value) throws IllegalAccessException, InvocationTargetException {
        BeanUtilsBean.getInstance().copyProperty(bean, name, value);
    }
{% endhighlight %}

또한 BeanUtilsBean 의 생성자를 살펴보면 아래와 같이 ConvertUtilsBean, PropertyUtilsBean를 사용함을 알 수 있다.
{% highlight ruby %}
    public BeanUtilsBean() {
        this(new ConvertUtilsBean(), new PropertyUtilsBean());
    }
{% endhighlight %}

BeanUtils 는 아래와 같은 메서드를 지원하는데, 그 중에서도 describe, populate가 가장 많이 사용되는 함수이다.
네이밍은 꽤나 직관적이라 이름에서 그 기능을 유추해 볼 수 있다.

        public static Object cloneBean(Object bean)
        public static void copyProperties(Object dest, Object orig)
        public static void copyProperty(Object bean, String name, Object value)
        public static String[] getArrayProperty(Object bean, String name)
        public static String getIndexedProperty(Object bean, String name)
        public static String getIndexedProperty(Object bean, String name, int index)
        public static String getMappedProperty(Object bean, String name)
        public static String getMappedProperty(Object bean, String name, String key)
        public static String getNestedProperty(Object bean, String name)
        public static String getProperty(Object bean, String name)
        public static String getSimpleProperty(Object bean, String name)
        public static void setProperty(Object bean, String name, Object value)

         * bean 을 Map으로 변환
        public static Map describe(Object bean)

         * Map을 Bean으로 변환
        public static void populate(Object bean, Map properties)


가장 빈번히 쓰이는 describe, populate에서 간단히 알아보겠다.
사용법은 굉장히 간단하다.

describe의 Bean을 Map으로 변환해준다.
{% highlight ruby %}
        ExampleVO vo = new ExampleVO();
        vo.setBar("bar");
        vo.setFoo("foo");
        Map<String, String> resultMap = BeanUtils.describe(vo);
        System.out.println(resultMap);
{% endhighlight %}

출력 결과는 {bar=bar, foo=foo, class=class com.test.example.ExampleVO} 이다.
ExampleVO의 필드 뿐 아니라 class라는 key값에 ExampleVO의 패키지명이 들어가는 것이 특이하다.


describe 함수는 아래와 같이 구현되어있는데, 파라메터로 전달받은 bean의 필드를 읽어온 후 Map으로 바인딩 시켜주는 간단한 로직을 가진다.

{% highlight ruby %}
public Map<String, String> describe(Object bean) throws IllegalAccessException, InvocationTargetException, NoSuchMethodException {
        if (bean == null) {
            return new HashMap();
        }

        if (this.log.isDebugEnabled()) {
            this.log.debug("Describing bean: " + bean.getClass().getName());
        }

        Map description = new HashMap();
        if (bean instanceof DynaBean) {
            DynaProperty[] descriptors = ((DynaBean)bean).getDynaClass().getDynaProperties();

            for (int i = 0; i < descriptors.length; ++i) {
                String name = descriptors[i].getName();
                description.put(name, getProperty(bean, name));
            }
        } else {
            PropertyDescriptor[] descriptors = getPropertyUtils().getPropertyDescriptors(bean);

            Class clazz = bean.getClass();
            for (int i = 0; i < descriptors.length; ++i) {
                String name = descriptors[i].getName();
                if (getPropertyUtils().getReadMethod(clazz, descriptors[i]) != null) {
                    description.put(name, getProperty(bean, name));
                }
            }
        }
        return description;
    }
{% endhighlight %}


populate의 경우 Map을 Bean으로 변환해준다.

{% highlight ruby %}
        Map<String, String> resultMap = new HashMap<String, String>();
        resultMap.put("foo", "foo");
        resultMap.put("bar", null);

        ExampleVO vo = new ExampleVO();

        BeanUtils.populate(vo, resultMap);

        System.out.println(vo.getFoo());
        System.out.println(vo.getBar());
{% endhighlight %}
출력결과는 resultMap에 세팅한 대로 foo, null 이다.

populate 함수의 경우 아래와같이 파라메터로 받은 properties 의 key, value로 setProperty함수를 호출한다.
{% highlight ruby %}
    public void populate(Object bean, Map<String, ? extends Object> properties) throws IllegalAccessException, InvocationTargetException {
        if ((bean == null) || (properties == null)) {
            return;
        }
        if (this.log.isDebugEnabled()) {
            this.log.debug("BeanUtils.populate(" + bean + ", " + properties + ")");
        }

        for (Map.Entry entry : properties.entrySet()) {
            String name = (String)entry.getKey();
            if (name == null) {
                continue;
            }

            setProperty(bean, name, entry.getValue());
        }
    }
    {% endhighlight %}


setProperty 함수는 아래와 같이 getPropertyUtils().getProperty 함수를 사용하여 Bean의 필드를 체크한 후, Map의 key, value와 바인딩해준다.
{% highlight ruby %}
getPropertyUtils().getProperty(target, resolver.next(name));
 {% endhighlight %}


지금까지 BeanUtils에 대해 알아보았다.
결론적으로 BeansUtils는 여러 데이터로 객체를 다룰 때 편리하고 유용하게 사용할 수 있는 모듈이다.
아래 의존성 추가 후 사용할 수 있다.
{% highlight ruby %}
        <dependency>
            <groupId>commons-beanutils</groupId>
            <artifactId>commons-beanutils</artifactId>
            <version>1.9.2</version>
        </dependency>
 {% endhighlight %}


