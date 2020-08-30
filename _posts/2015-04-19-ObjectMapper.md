---
layout: post
title: ObjectMapper
---
웹개발을 하다보면, Object -  JSON Object 변환 혹은 획득한 데이터를 VO 객체로 변환해야하는 경우를 빈번히 만나게 된다.
변환해야 하는 한두개 쯤이라면 직접 바인딩 시켜줄 수 있겠지만, 그 수가 10~20개를 넘어간다면 Binding하는 방법을 찾아야 할 것이다.
오늘 소개할 ObjectMapper는 Object - JSON 변환 혹은 Map - Object 변환 등 다양한 바인딩을 지원하며
굉장히 편리하게 사용할 수 있다.

*순수히 공부를하며 쓴 글이기에 부족한 점이 많으므로, 수정할 점이 있다면 댓글 달아주셨으면 좋겠습니다.^^

ObjectMapper는 Jackson 라이브러리에 포함되어있다.
(http://jackson.codehaus.org/)
http://jackson.codehaus.org/1.1.2/javadoc/org/codehaus/jackson/map/ObjectMapper.html 에서 상세 정보를 볼 수 있다.

예제 코드와 함께 설명하겠다.
Obejct를 Json 문자열 으로 변경하고 싶을 때, 아래의  writeValueAsString 함수를 사용하면 된다.
{% highlight ruby %}

        ExampleVO vo = new ExampleVO();
        vo.setFoo("foo");
        vo.setBar("bar");
        String jsonString = new ObjectMapper().writeValueAsString(vo);
        System.out.println(jsonString);

 {% endhighlight %}

출력 결과물은 {"foo":"foo","bar":"bar"} 이다.
그렇다면, jsonString을  VO로 매핑시켜보겠다.
아래와 같이 readValue 함수를 사용하면 간단히 매핑이 가능하다.
{"foo":"foo2","bar":"bar2"}  라는 간단한 json 문자열을 VO에 매핑시켜보겠다.
{% highlight ruby %}
        ExampleVO vo2 = mapper.readValue(jsonString, ExampleVO.class);
        System.out.println(vo2.getBar());
        System.out.println(vo2.getFoo());
 {% endhighlight %}
 출력 결과물은 bar2, foo2 이다.

 JSON 뿐만 아니라 Map 형태도 변환시킬 수 있다.
 아래와 같이 convertValue 함수를 사용하면 간단히 매핑이 가능하다.
{% highlight ruby %}
    ObjectMapper mapper = new ObjectMapper();
        Map<String,Object> exampleMap = new HashMap<String, Object>();
        exampleMap.put("foo", "foo3");
        exampleMap.put("bar", "bar3");

        ExampleVO vo = mapper.convertValue(exampleMap, ExampleVO.class);
        System.out.println(vo.getFoo());
        System.out.println(vo.getBar());
 {% endhighlight %}
 출력결과는 foo3, bar3이다.

 데이터를 변환하는 과정에서, NULL인 것들은 지나치고 싶은 경우가 있다.
 예를들면 Object를 Json으로 변환하는 과정에서 null 값인 경우, JSON 문자열에 포함시키고 싶지 않다면
 아래와 같이 setSerializationInclusion 을 사용하여 mapper 속성을 정해주면 된다.
{% highlight ruby %}
    ObjectMapper mapper = new ObjectMapper();
     mapper.setSerializationInclusion(Inclusion.NON_DEFAULT);
 {% endhighlight %}

 Inclusion의 경우 아래와 같다.
{% highlight ruby %}
    public static enum Inclusion {
        ALWAYS, NON_NULL, NON_DEFAULT, NON_EMPTY;
    }
{% endhighlight %}

지금까지 Jackson ObjectMapper 대해 간단히 알아보았다.
간단히 데이터를 매핑할 수 있는 기능이라, 사용 빈도가 높은  기능 중 하나이다.