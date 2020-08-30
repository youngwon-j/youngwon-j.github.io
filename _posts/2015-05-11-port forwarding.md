---
layout: post
title: osx mavericks에서 포트포워딩하기
---
mavericks 이전에는 iptables 를 이용하여 포트포워딩 설정을 할 수 있었지만,
mavericks 업데이트 이후 iptables를 이용한 포트포워딩을 할 수 없게되었다.
pfctl 명령어를 사용하여 osx에서 간단히 포트포워딩 하는 방법을 알아보자.

/etc/pf.anchors 폴더로 이동하여 아래 내용의  'com.forwading'  파일을 작성한다.

    rdr pass on lo0 inet proto tcp from any to 127.0.0.1 port 80 -> 127.0.0.1 port 80
    rdr pass on lo0 inet proto tcp from any to any port 80 -> 127.0.0.1 port 8080
    rdr pass on lo0 inet proto tcp from any to any port 8080 -> 127.0.0.1 port 8080

/etc/pf.conf 파일을 열어 anchor를 추가한다.

    dr-anchor "forwading"
    load anchor "forwading" from "/etc/pf.anchors/com.forwading"

추가가 완료되면 콘솔에서 아래의 명령어를 입력한다.

    sudo pfctl -f /etc/pf.conf
    sudo pfctl -e

이제 8080 포트가 80으로 포워딩 됨을 알 수 있다.

* com.forwading 파일에서 rdr pass on lo0 inet proto tcp from any to any port 80 -> 127.0.0.1 port 8080
부분을 누락하면 톰켓 구동 시 timeout 이 날 수 있으므로 주의한다.



