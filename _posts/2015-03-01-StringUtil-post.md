---
layout: post
title: StringUtil 클래스 분석
---

개발을 하다보면, String 문자열에 대한 다양한 처리를 해야 하는 경우가 있다.
org.springframework.util 패키지 위치한 StringUtil 클래스에 이러한 문자열 처리를 다양하게 지원해주는 함수들이 있음을 알게 되어
그 내용을 공유하고자 한다.

*순수히 공부를하며 쓴 글이기에 부족한 점이 많으므로, 수정할 점이 있다면 댓글 달아주셨으면 좋겠습니다.^^


StringUtil은 유틸리티 클래스이다.
클래스 내의 함수들은 Static 으로 선언되어있어
StringUtil.isEmpty(str) 과 같이 바로 사용할 수 있어 편리하다.

필자 또한 문자열 처리를 할 떄 필요한 기능들을 많이 만들어 쓰곤 했는데, 그럴 때마다
생각지못한 부분에서 황당한 버그들이 발생한 경험이 있다.
그럴 때에는 검증된 라이브러리를 사용하는것도 나쁘지 않은 방법인 것 같다.


StringUtils docs
http://docs.spring.io/spring/docs/3.2.9.RELEASE/javadoc-api/index.html?org/springframework/util/StringUtils.html

위 주소에 접속해보면, StringUtils가 제공하는 다양한 함수가 나열되어있다.
그 중 사용 빈도가 빈번할 것 같은 함수 몇가지를 나열해보겠다.

replace 함수
문자열을 대치하는 함수이다.
아래 소스코드를 보면, StringBuilder 로 패턴 비교 후 대치된 문자열을 만들어 리턴해 주는 것을 볼 수 있다.

{% highlight ruby %}
public static String replace(String inString, String oldPattern, String newPattern) {
        if (!hasLength(inString) || !hasLength(oldPattern) || newPattern == null) {
            return inString;
        }
        StringBuilder sb = new StringBuilder();
        int pos = 0; // our position in the old string
        int index = inString.indexOf(oldPattern);
        // the index of an occurrence we've found, or -1
        int patLen = oldPattern.length();
        while (index >= 0) {
            sb.append(inString.substring(pos, index));
            sb.append(newPattern);
            pos = index + patLen;
            index = inString.indexOf(oldPattern, pos);
        }
        sb.append(inString.substring(pos));
        // remember to append any characters to the right of a match
        return sb.toString();
    }
{% endhighlight %}

delimitedListToStringArray 함수
delimiter 로 구분된 문자열을 Array로 바꿔주는 역할을 한다.
csv와 같은 데이터 처리 시 유용하게 사용될 수 있는 메서드로 보인다.

parameter로 String과, delimiter를 받고있으며 charsToDelete 파라미터는 해당 메서드에서는 null이다.
Result 라는 List를 만든 후 While문을 수행하며 delimiter로 구분된 값들을 List에 assign하는 것을 볼 수 있다.

{% highlight ruby %}

public static String[] delimitedListToStringArray(String str, String delimiter) {
        return delimitedListToStringArray(str, delimiter, null);
    }


public static String[] delimitedListToStringArray(String str, String delimiter, String charsToDelete) {
        if (str == null) {
            return new String[0];
        }
        if (delimiter == null) {
            return new String[] {str};
        }
        List<String> result = new ArrayList<String>();
        if ("".equals(delimiter)) {
            for (int i = 0; i < str.length(); i++) {
                result.add(deleteAny(str.substring(i, i + 1), charsToDelete));
            }
        }
        else {
            int pos = 0;
            int delPos;
            while ((delPos = str.indexOf(delimiter, pos)) != -1) {
                result.add(deleteAny(str.substring(pos, delPos), charsToDelete));
                pos = delPos + delimiter.length();
            }
            if (str.length() > 0 && pos <= str.length()) {
                // Add rest of String, but not in case of empty input.
                result.add(deleteAny(str.substring(pos), charsToDelete));
            }
        }
        return toStringArray(result);
    }
{% endhighlight %}


commaDelimitedListToStringArray 라는 콤마로 구분된 String을 List로 바꿔주는 매서드 또한 delimitedListToStringArray 메서드를 사용한다.

{% highlight ruby %}
    public static String[] delimitedListToStringArray(String str, String delimiter) {
        return delimitedListToStringArray(str, delimiter, null);
    }
{% endhighlight %}


역으로 collectionToDelimitedString 함수를 사용하면 Collection 으로 원하는 delimiter로 나뉘어진 문자열을 반환받을 수 있다.
아래 코드를 보면, StringBuilder 인스턴스 생성 후, while문을 수행하며 문자열, delimiter를 StringBuilder에 append하는 것을 볼 수 있다.
{% highlight ruby %}
    public static String collectionToDelimitedString(Collection<?> coll, String delim, String prefix, String suffix) {
        if (CollectionUtils.isEmpty(coll)) {
            return "";
        }
        StringBuilder sb = new StringBuilder();
        Iterator<?> it = coll.iterator();
        while (it.hasNext()) {
            sb.append(prefix).append(it.next()).append(suffix);
            if (it.hasNext()) {
                sb.append(delim);
            }
        }
        return sb.toString();
    }
    {% endhighlight %}


이 외에도 문자열을 생성하는 함수들은 아래와 같이 지원된다.
{% highlight ruby %}
static String   arrayToCommaDelimitedString(Object[] arr)
static String   arrayToDelimitedString(Object[] arr, String delim)
static String   collectionToCommaDelimitedString(Collection coll)
static String   collectionToDelimitedString(Collection coll, String delim)
static String   collectionToDelimitedString(Collection coll, String delim, String prefix, String suffix)
{% endhighlight %}


또한 문자열을 컬렉션 혹은 배열으로 만드는 함수들은 아래와 같이 지원된다.
{% highlight ruby %}
static Set  commaDelimitedListToSet(String str)
static String[] commaDelimitedListToStringArray(String str)
static String[] delimitedListToStringArray(String str, String delimiter)
static String[] delimitedListToStringArray(String str, String delimiter, String charsToDelete)
static String[] tokenizeToStringArray(String str, String delimiters)
static String[] tokenizeToStringArray(String str, String delimiters, boolean trimTokens, boolean ignoreEmptyTokens)
{% endhighlight %}


countOccurrencesOf 라는 함수는 조금 특이한 기능을 제공한다.
이름에서 유추할 수 있듯, 해당 문자열에서 특정 문자열이 몇번 반복되는지에 대한 Count를 제공한다.
구현은 굉장히 심플하게 되어있다.
아래의 코드에서 볼 수 있듯 String의 indexOf 함수를 사용하여, 인덱스를 옮겨가며 해당 문자열을 찾고 Count를 기록한다.
{% highlight ruby %}
if (str == null || sub == null || str.length() == 0 || sub.length() == 0) {
            return 0;
        }
        int count = 0;
        int pos = 0;
        int idx;
        while ((idx = str.indexOf(sub, pos)) != -1) {
            ++count;
            pos = idx + sub.length();
        }
        return count;
{% endhighlight %}

이 외에도 아래의 함수에서 공백문자가 있는지, 문자열이 특정한 prefix, suffix 로 시작,끝나는지, 매칭되는 문자열이 있는지 등을 반환하는 기능이 지원된다.
{% highlight ruby %}
static boolean  containsWhitespace(CharSequence str)
static boolean  containsWhitespace(String str)
static boolean  startsWithIgnoreCase(String str, String prefix)
static boolean  endsWithIgnoreCase(String str, String suffix)
static boolean  (CharSequence str, int index, CharSequence substring)
{% endhighlight %}


doc을 보면 알 수 있듯, StringUtils 클래스는 문자열 처리에 있어 다양한 기능들을 지원한다.
많은 함수들이 있어 모두 다 분석하지는 못했지만, '아 이런 기능도 지원되는구나'를 알아두고
나중에 사용할 일이 있을 때 사용한다면 꽤나 유용하지 않을까 하는 생각이 든다.

