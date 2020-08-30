---
layout: post
title: Google Client id
---
요즈음의 서비스에서는 여러 IDP 들을 연동한다.
Facebook, Google, Twitter,  네이버 등 주요 SNS 및 포털 IDP를 로그인에 연동하여 사용하는데, 필자가 얼마전 연동했었던  Google 의 경우에는 다른 IDP들과 조금 다른  Client_id정책이 있어 공유하고자 한다.

*순수히 공부를하며 쓴 글이기에 부족한 점이 많으므로, 수정할 점이 있다면 댓글 달아주셨으면 좋겠습니다.^^

### client_id 발급
여느 사이트들과 같이, Google 또한 Client_id 발급은 굉장히 간단하다.
발급 과정을 설명해보겠다.

![google_clientId_1.png](/assets/google_clientId_1.png)
1. 구글 개발자 콘솔 https://console.developers.google.com 로 접속하여 프로젝트를 만든다.
![google_clientId_2.png](/assets/google_clientId_2.png)
2. Api 및 인증 > 사용자 인증 정보 내 새 client_id만들기로 클라이언트 ID를 생성한다.
![google_clientId_2.png](/assets/google_clientId_3.png)
3. 자신이 사용할 응용의 성격에 맞는 Client_id를 발급받는다.

### Google client_id
과정은 굉장히 심플하다.
하지만, 구글은 응용의 성격에 따라 다른 client_id를 발급한다.
예를 들자면, 웹 어플리케이션 및 설치된 애플리케이션으로 client_id를 발급받는다면
![google_clientId_4.png](/assets/google_clientId_4.png)
위와 스크린샷을 참조하면
                웹 어플리케이션의 경우에는 730506221514-jq2i5ml9tdjoc2uj7n85bkvmoqvr6jfp.apps.googleusercontent.com,
                설치 된 어플리케이션의 경우에는 730506221514-i0gjvd628qk2iut8h0eg1691fcnh10tl.apps.googleusercontent.com
의 client_id를 발급받는다.

다른 IDP의 경우, 웹 / 앱 의 관계 없이 동일한 client_id를 부여하지만, 구글의 경우는 응용의 성격에 따라 다른 client_id를 발급한다.
하지만 client_id의 구성에 있어, 앞 12자리의 숫자 ( 730506221514 ) 는 서로 동일하게 부여된다.
테스트를 해보면, client_id 730506221514 로 웹, 앱 모두 동일하게 구글 IDP Oauth 로그인이 가능하다.
하지만 분명 다른 client_id인데 앞의 동일한 숫자로 성격이 다른 두 응용이 정상동작 한다니, 무언가 마음 한구석에 찜찜한 점이 남아있을 것이다.

해당 내용으로 google 에 문의 결과, 서버와의 통신에 있어서는 "완전한" client_id가 필요하다고 한다.
응용의 성격에 따라 달라질 수는 있지만, 서버는 특정 길이(최소 몇 자리 이상 몇 자리 이하 등)의 Client ID 를 요구한다고 한다.
그 조건이 충족되지 않을 경우, 서버 측에서는 1) string 을 채우려 하거나, 혹은 2) 요청된 작업을 fail 시킨다고 한다.
즉, 안전한 사용을 위해서는 발급받은 full - client_id 를 사용하는것이 정답이다.

여러 IDP를 연동하는 인증 관련 시스템을 개발하다 보면, client_id 관리 또한 General-Case에 맞출 수 밖에 없다.
하지만 google의 경우처럼 조금 특이한 케이스도 있으니, 참고하면 개발에 조금이나마 도움이 되지 않을까 하는 생각이 든다.

