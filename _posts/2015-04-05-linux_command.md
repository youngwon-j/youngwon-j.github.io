---
layout: post
title: 리눅스 기본명령
---

리눅스 기본명령

서버 개발을 하다보면 vi 편집기를 이용하여 서버 설정을 바꿔야 한다던지, Apache, Tomcat 에서 필요한 키워드로 로그를 뽑아야 하는 경우를 자주 접할 수 있다.
필자 또한 처음에는 까만 화면에 명령어로 여러 작업들을 수행하는 것들이 굉장히 생소했지만, 기본 키워드를 하나둘 씩 배워가며 점차 익숙해졌다.
서버개발자에게, 개발능력 이외에 리눅스에서 제공하는 기본 커맨드들을 잘 익히는것은 많은 도움이 된다.
그러므로 이번 포스팅은 리눅스 기본 명령어를 포스팅하도록 하겠다.

*순수히 공부를하며 쓴 글이기에 부족한 점이 많으므로, 수정할 점이 있다면 댓글 달아주셨으면 좋겠습니다.^^

예제 코드와 함께 설명하겠다.

1. tail 명령어
파일의 마지막 부분을 출력한다.
최근 서버로그를 보기 위해 자주 사용되며 기본 문법은 tail  [option] ... [file] ... 이다.

ex1 file
banana
strawberry
blueberry
melon
apple
cherry
orange

ex2 file
watermelon
melon
mango
kiwi
grape
citron
lemon



내용으로 구성된 ex file 을 예로 들어보겠다.
파일의 마지막 5줄을 출력한다.
{% highlight ruby %}
tail -n 5 ex1.txt
출력결과
melon
apple
cherry
orange
 {% endhighlight %}

 로그 파일의 경우, 계속해서 내용이 추가되는데 그 때에는  -f 옵션을 이용하면 된다.
{% highlight ruby %}
tail -f ex1.txt //
 {% endhighlight %}

여러개의 파일을 함께 볼 수도 있다.
{% highlight ruby %}
tail -f ./*

출력결과
==> ./ex1 <==
ex1 file
banana
strawberry
blueberry
melon
apple
cherry
orange


==> ./ex2 <==
ex2 file
watermelon
melon
mango
kiwi
grape
citron
lemon

 {% endhighlight %}


 파일, 디렉토리 등에서 특정 텍스트를 찾고 싶을 때는 grep 명령어를 사용한다.
 {% highlight ruby %}
 cat ex1 | grep banana
출력결과
banana
 {% endhighlight %}

-n 옵션을 사용하여 검색결과가 몇번째 라인에 위치한지도 알 수 있다.

 {% highlight ruby %}
 grep -n banana
출력결과
2:banana
 {% endhighlight %}

 awk 명령어를 사용하여 특정 white-space를 기준으로 문자열을 찾을 수 도 있다.
 예를들어 hello world! bye world! 라는 awk_example이라는 파일이 있다고 하면
 {% highlight ruby %}
cat awk_example | grep hello | awk '{print$2}'
출력결과
world!
 {% endhighlight %}

파일의 내용들을 정렬하고 싶을때는 sort 명령어를 사용한다.
9
5
5
4
4
4
2
1
1
으로 구성된 파일에 sort 명령을 적용해보겠다.

 {% highlight ruby %}
출력결과
cat sort_ex | sort
1
1
2
4
4
4
5
5
9
 {% endhighlight %}

내림차순으로 정렬이 되는 것을 확인할 수 있다.
파일은 1이 2번, 4가 3번, 5가 2번 중복되어 있는데, uniq 명령어를 사용하여 중복된 값을 카운팅 한 후 소팅할 수도 있다.
 {% highlight ruby %}
cat sort_ex | sort | uniq -c
출력결과
   2 1
   1 2
   3 4
   2 5
   1 9
 {% endhighlight %}



