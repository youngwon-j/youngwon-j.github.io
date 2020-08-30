---
layout: post
title: Java Java8 Date Time API
---
저번 포스팅에서 DateFormat 에 대해서 알아보았다.
Java8 에서는 기존 Java가 지원하던 Date 보다 더욱 우아한 방법을 지원한다.
이번 포스팅에서는 Java 8 의 Date에 대해 알아보겠다.

*순수히 공부를하며 쓴 글이기에 부족한 점이 많으므로, 수정할 점이 있다면 댓글 달아주셨으면 좋겠습니다.^^
*해당 포스팅은 Java8 Date 문서 http://www.oracle.com/technetwork/articles/java/jf14-date-time-2125367.html 를 참조하였습니다.


LocalDate 와 LocalTime
Local Date, Local Time은 Date와 Time에 관련된 내용을 제공하고 우리가 사용중인 달력, 시계와 동일하게 동작한다
해당 클래스는 아래와 같이 사용할 수 있다.

{% highlight ruby %}
    LocalDateTime timePoint = LocalDateTime.now(); // 현재 Date, Time
    LocalDate.of(2012, Month.DECEMBER, 12); // from values
    LocalDate.ofEpochDay(150); // middle of 1970
    LocalTime.of(17, 18); // 17시 18분
    LocalTime.parse("10:15:30"); // String으로부터 날짜를 가져올 수 있다.
{% endhighlight %}

아래와 같이 LocalDate 클래스를 사용하여 간단히 날짜 및 시,분,초 등을 가져올 수 있다.
{% highlight ruby %}
    LocalDate theDate = timePoint.toLocalDate();
    Month month = timePoint.getMonth();
    int day = timePoint.getDayOfMonth();
    int min = timePoint.getMinute();
    int second = timePoint.getSecond();

    System.out.println(month);
    System.out.println(day);
    System.out.println(min);
    System.out.println(second);

    출력 결과물
    MARCH
    29
    34
    37
{% endhighlight %}

DateTimeFormatter 클래스를 이용하여, 아래와 같이 간단하게 time formatting을 할 수 있다.
{% highlight ruby %}
      DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm");
      DateTimeFormatter formatter2 = DateTimeFormatter.ofPattern("yyyy/MM/dd");
      LocalDateTime dateTime = LocalDateTime.now();
      String formattedDateTime = dateTime.format(formatter);
      System.out.println(formattedDateTime);
      formattedDateTime = dateTime.format(formatter2);
      System.out.println(formattedDateTime);


      출력결과
      2015-03-29 14:54
      2015/03/29
{% endhighlight %}

날짜 연산 또한 간단하게 수행할 수 있다.
아래 페이지에서 지원되는 Methods에 대한 상세 정보를 알 수 있다.
http://docs.oracle.com/javase/8/docs/api/java/time/LocalDate.html
{% highlight ruby %}
    LocalDateTime thePast = timePoint.withDayOfMonth(03).withYear(2015);
    LocalDateTime yetAnother = thePast.plusWeeks(1).plus(1, ChronoUnit.WEEKS);
{% endhighlight %}

또한 truncatedTo 함수를 사용하여 날짜, 시간, 일을 잘라서 표현할 수 있다.
{% highlight ruby %}
//14:46:33.869
LocalTime truncatedTime = LocalTime.now();
//14:46:33
LocalTime truncatedTime = time.truncatedTo(ChronoUnit.SECONDS);
{% endhighlight %}

신기한 기능은 Period라는 기능이다.
Period를 사용하면 아래와 같이 '1년 1개월 1일 뒤'의 시간 또한 알 수 있다.
{% highlight ruby %}
    LocalDate oldDate = LocalDate.now();
    ZonedDateTime oldDateTime = ZonedDateTime.now();
    Period period = Period.of(1, 1, 1);
    LocalDate newDate = oldDate.plus(period);
    System.out.println(oldDate);
    System.out.println(newDate);

    출력결과
    2015-03-29
    2016-04-30
{% endhighlight %}

