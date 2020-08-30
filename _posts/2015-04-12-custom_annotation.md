---
layout: post
title: custom annotation
---
@Override, @Depricated 등 Annotation 문법에 대해 많이 접해보았을 것이다.
Annotation문법은 JEE5부터 추가된 문법 이며 쉽게 말해 코드 주석을 다는 행위 라고 생각하면 된다.
Annotation의 사전적 의미는 ' 주석 ' 이다.
여기서 말하는 주석은 일반적인 주석에서 더 나아가, 코드를 설명해주고, 실행의 흐름까지 영향을 미치는 주석 이라고 이해하면 된다.

Annotation은 컴파일러에게 정보전달, 수행할 작업 지시, 실행 시 별도로직 처리가 필요할 때 사용할 수 있다.
비즈니스 로직에 영향을 주지 않는 부분들을 Annotation으로 선언하여 사용하면 가독성도 좋아지며 깔끔한 코드 유지가 가능하다.

일반적으로는 @Override, @Deprecated, @SuppressWarnings 등이 사용되고 있으며
정의된 Annotation 외에 별도로 사용자가 Annotation을 정의하여 사용할 수 있다.
이번 포스팅에서는  Annotation을 Custom하여 사용하는 법에 대해 다뤄보도록 하겠다.



*순수히 공부를하며 쓴 글이기에 부족한 점이 많으므로, 수정할 점이 있다면 댓글 달아주셨으면 좋겠습니다.^^

예제 코드와 함께 설명하겠다.
Annotation은 아래와같이 정의할 수 있다.

{% highlight ruby %}
@Target({java.lang.annotation.ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface sampleAnnotation {
}
 {% endhighlight %}
코드를 보면 @Target, @Retention이라는 어노테이션이 사용되었다.
이 부분은 Annotation 선언 시 사용되는 메타 어노테이션 ( 내장 어노테이션 ) 이다.
Meta-Annotation은
@Target, @Retention, @Documented, @Inherited가 있으며, 차례대로 알아보겠다.

        @Target
        어디에 어노테이션을 넣을 수 있는지, 어디에 어노테이션을 적용할 지 선언한다.됨
        여러개의 Target을 설정할 수 있다.

         * PACKAGE - 패키지 선언에만 사용가능
         * FIELD - 필드 선언에만 사용가능
         * TYPE - 클래스, 인터페이스 등에 사용가능
         * METHOD - 메소드 선언에만 사용가능
         * PARAMETER - 파라메터 선언에만 사용가능
         * LOCAL_VARIABLE - 지역 변수 선언 시에만 사용 가능
         * CONSTRUCTOR - 생성자에만 사용 가능
         * ANNOTATION_TYPE - 어노테이션 타입에만 사용 가능


        @Retention
        Annotation의 용도를 정한다.
        Retention은 한가지만 선택 가능하다.

        *Source 컴파일 시 어노테이션 정보 사라짐. 단순 주석 용으로 사용
        *Class 컴파일러에 의해 참조 가능 , 가상머신에서는 무시됨
        *Runtime 컴파일 시 포함됨

        @Deprecated
        어노테이션의 JavaDoc에 포함 여부를 선언한다.

        @Inherited
        자식 클래스의 부모 클래스 어노테이션 사용가능 여부를 선언한다.

즉 아래와 같이 사용 가능하다.

{% highlight ruby %}

@Target({java.lang.annotation.ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface sampleAnnotation {
    public int sampleNumber();
    public String sampleString() default "sample";
}
 {% endhighlight %}

 위 Custom Annotation은 Method에 사용가능한 Annotation으로 sampleNumber와 sampleString값을 가진다.
 그러므로 아래와 같이 메서드 위에서 어노테이션을 사용한 메타정보 관리가 가능하다.


{% highlight ruby %}

@SampleAnnotation(sampleNumber = 10, sampleString = "STRING")
public void sampleMethod {
   ...
}
 {% endhighlight %}

이렇게 선언된 Annotation을 어떻게 사용할까?
선언된 정보들을 Interceptor에서 읽어와 특정 처리를 한다고 가정해 보자.
그렇다면 아래와 같이 사용가능하다.

{% highlight ruby %}
@Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        HandlerMethod handlerMethod = (HandlerMethod)handler;
        SampleAnnotation annotation = handlerMethod.getMethod().getAnnotation(SampleAnnotation.class);
        int sampleNumber = annotation.sampleNumber();
        String sampleString = annotation.sampleString();

        ....

    }
 {% endhighlight %}

지금까지 Java Annotation에 대해 간단히 알아보았다.
여러 메타정보 관리를 위해 Annotation을 효율적으로 사용한다면 체계적인 코드의 운용이 가능할것으로 보인다.