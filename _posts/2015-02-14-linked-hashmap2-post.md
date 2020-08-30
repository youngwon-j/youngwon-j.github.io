---
layout: post
title: LinkedHashMap 분석 및 HashMap 과의  Performance 비교-2
---

지난번 포스팅 (LinkedHashMap 분석 및 HashMap 과의  Performance 비교)
에서 LinkedHashMap의 주요 특징으로


2. LinkedHashMap은 순서를 유지하기 때문에 HashMap과 비교하여 당연히 더 많은 메모리를 필요로 하는것으로 알려져있다.

를 언급한 바 있다.
하지만 해당 포스팅에서는 LinkedHashMap 과 HashMap의 속도만 비교했을 뿐,
메모리 사용량은 비교하지 않았기에 그 둘을 비교하기 위해 Performance 테스트를 수행했다.




*순수히 공부를하며 쓴 글이기에 부족한 점이 많으므로, 수정할 점이 있다면 댓글 달아주셨으면 좋겠습니다.^^


Performance 테스트 결과

테스트는 각 인스턴스 생성 후 메모리 사용량을 측정하였으며 그 결과값의 평균치로 그래프를 그렸다.

![memory_compare.png](/assets/memory_compare.png)
[그림1]  Performance 테스트 수행결과

Y 축이 메모리 사용량임을 감안할 때 LinkedHashMap이 HashMap과 비교하여 약 1.25배의 메모리를 더 사용하는것으로 나타났다.


결론

'순서를 보장' 하기 위해서는 분명 어딘가에 '순서를 기록' 해야만 한다.
LinkedHashMap은 LinkedList를 사용하여 순서를 기록하고 있으며, 이는 당연히 메모리 사용량의 증가로 이어질 수 밖에 없다.

'순서를 보장할'일이 없다면 당연 HashMap을 사용하는 것이 좋다.

하지만 두 차례의 Performance 테스트 결과에서 볼 수 있듯, 표본이 크지 않은 경우에 순서를 보장해야한다면
LinkedHashMap을 사용하는 것이 크게 성능을 저해하는 주된 요인으로 꼽히지는 않을것으로 보인다.

