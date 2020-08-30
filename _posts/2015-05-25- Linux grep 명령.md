---
layout: post
title: Linux Grep
---
### Linux Grep 활용하기

대용량의 로그파일을 핸들링 하다 보면, Grep 을 수없이 많이 쓰게 되는데, Grep의 일반 옵션만 잘 숙지하고 있어도
많은 도움이 된다. 이번 포스팅은 Grep 의 여러 옵션들을 주제로 해보겠다.


* Grep 명령어는 주어진 패턴과 매칭되는 라인을 검색해준다.
* 정렬 옵션을 사용하면 정렬 후 출력할 수도 있다.
* Grep의 검색 범위는 메모리 제한을 넘어가지 않는 범위에서 라인의 제한이 없다.

        Grep의 형식
        grep [옵션] [패턴] [파일명]

Grep의 유용한 Option

        예제파일
        banana
        strawberry
        melon
        watermelon
        Orange
        kiwi
        apple

-b : 검색된 라인에 블록 번호를 붙여 출력
{% highlight ruby %}
cat test | grep -b banana
0:banana
{% endhighlight %}

-c : 매칭된 라인의 수를 출력
{% highlight ruby %}
cat test | grep -c banana
1
{% endhighlight %}

-i 문자열의 대소문자를 무시하고 검색
{% highlight ruby %}
cat test | grep -i orange
Orange
{% endhighlight %}

-n 매칭된 라인 출력 시 파일상의 라인 번호를 출력
{% highlight ruby %}
cat test | grep -n strawberry
2:strawberry
{% endhighlight %}

-v 매칭되지 않는 라인만 출력
{% highlight ruby %}
cat test | grep -v strawberry
banana
melon
watermelon
Orange
kiwi
apple
{% endhighlight %}

정규표현식으로 검색
{% highlight ruby %}
cat test | grep '[b]'
banana
strawberry
{% endhighlight %}

*grep 뿐 아니라 egrep, fgrep도 있는데
egrep의 경우 추가적인 정규표현식 메타문자를 사용할 수 있고 fgrep은 정규표현식 메타문자를 사용할 수 없다. ( 특수문자 및 $를 그대로 인식 )

