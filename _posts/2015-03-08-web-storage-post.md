---
layout: post
title: Web Storage에 대하여.
---

개발자라면, Web Storage 에 대해서 들어본 적이 있을 것이다.
WebStorage는 웹 사이트의 정보들을 클라이언트 ( 브라우저 ) 에 저장하는 용도로 사용된다.
Web Storage에 대한 대강의 개념만 알고 있던 중, 호기심이 생겨 WebStorage에 대해 공부한 내용을 포스팅 하도록 하겠다.

*순수히 공부를하며 쓴 글이기에 부족한 점이 많으므로, 수정할 점이 있다면 댓글 달아주셨으면 좋겠습니다.^^

Web Storage

        Web Storage는 Cookie처럼 소량의 데이터를 저장하는 용도로 사용된다.
        HTML5 부터 나온 개념이며, 포스팅 중 언급하겠지만 Cookie의 단점을 보완하기 위해 생겨난 개념이다.
        Key-Value 쌍으로 데이터를 저장하고 키를 기반으로 데이터를 조회한다.
        Web-Storage는 영구저장소 (Local-Storage)와 임시저장소(Session Storage)로 구성된다.


vs Cookie

        앞서 언급한 것 처럼, WebStorage는 'Cookie'의 단점을 보완한다.
        1. Cookie는 만료일자를 지정하게 되있어 영구 데이터를 저장하지 못하지만, WebStorage는 만료일자가 없어 영구 데이터 저장이 가능하다.
        2. 알려진 바에 의하면 Cookie는 한 사이트당 최대 20개, 쿠키 하나당 약 4kb의 용량 제한이 있지만 WebStorage는 용량 제한이 없다.
        3. Cookie는 객체 정보를 저장할 수 없다. 하지만 WebStorage의 경우, 객체 정보를 저장할 수 있다. ( Ex. 스크립트 )
        4. Cookie는 매번 서버로 전송되지만, WebStorage는 클라이언트에 존재하며 서버로 전송하지 않는다. 그러므로 트래픽이 감소한다.

Local Storage, Session Storage

        Web Storage는 영구저장소 (Local-Storage)와 임시저장소(Session Storage)로 구성된다.
        1. 영구저장소(Local-Storage)
        window.localStorage
        저장한 데이터를 영구보관할 수 있으며 도메인별로 생성된다.
        다른 도메인에서는 접근 불가하며 물리적으로 저장된다.
        크롬의 경우 SQLite에, Safari의 경우 /Library/Application Support/Google/Chrome/Default/Local Storage 경로에 저장된다.
        2. 임시저장소(Session-Storage)
        window.sessionStorage
        저장한 데이터를 영구보관할 수 없다. 저장된 데이터는 브라우저와 같은 생존기간을 가진다.
        같은 도메인에 다른 브라우저가 접속시, 세션 스토리지는 각각 생성된다.
        새창, 새 탭으로 같은 도메인 접근시, 세션 스토리지값을 복사하며 그 이후 수정된 내용은 각 창 / 탭 별로 관리된다.

Support

    아래의 간단한 코드로 브라우저의 WebStorage 지원 여부를 알 수 있다.
    {% highlight ruby %}
    if( ('localStorage' in window) && window['localStorage'] !== null) {

        alert("현재 브라우저는 WebStorage를 지원합니다")

    }
    else{

        alert("현재 브라우저는 WebStorage를 지원하지 않습니다")

    }
    {% endhighlight %}

Example
간단한 예제를 통하여 로컬스토리지 / 세션 스토리지 사용법을 알 수 있다.
{% highlight ruby %}
<script type="text/javascript">
    //로컬스토리지에 저장
    window.addEventListener('storage', storageEventHandler, false);

    function storageEventHandler(evt) {
        console.log("storage event called key: " + evt.key);
    }


    function setLocalStorage() {
        var local_value = document.querySelector('#local_value');
        window.localStorage[document.querySelector('#local_key').value] = local_value.value;
    }
    //로컬스토리지 조회
    function getLocalStorage() {

        var resultDiv = document.getElementById('readLocalStorage');
        var key = document.getElementById('local_key_read').value;
        resultDiv.innerHTML = window.localStorage[key];
    }
    //로컬스토리지 삭제
    function removeLocalStorage() {
        var value = document.querySelector('#local_key_read').value;
        window.localStorage.removeItem(value);
    }

    //세션스토지에 저장
    function setSessionStorage() {
        var session_value = document.querySelector('#session_value');
        window.sessionStorage[document.getElementById('session_key').value] = session_value.value;
    }
    //세션스토리지 조회
    function getSessionStorage() {
        var resultDiv = document.getElementById('readSessionStorage');
        var key = document.getElementById('session_key_read').value;
        resultDiv.innerHTML = window.sessionStorage[key];
    }

    //세션스토리지 삭제
    function removeSessionStorage() {
        var value = document.querySelector('#session_key_read').value;
        window.sessionStorage.removeItem(value);
    }
</script>
{% endhighlight %}


-- 참고 블로그 --
http://blog.naver.com/sbd38/220143282026
http://lemonreadingbox.tistory.com/108
