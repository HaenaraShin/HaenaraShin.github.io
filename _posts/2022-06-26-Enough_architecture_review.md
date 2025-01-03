---
layout: post
title:  "[도서리뷰] 적정 소프트웨어 아키텍처"
feature-img: "assets/img/post/20220626_thumb.png"
thumbnail: "assets/img/post/20220626_thumb.png"
description: 적정 소프트웨어 아키텍처 ~ 리스크 주도 접근법 책을 읽고 리뷰한 당신의 SW 아키텍처가 실패한 이유.
date:   2022-06-26 23:45:00 +0900
categories: Book
---

> 한빛 미디어 서평단 활동의 일환으로 책을 제공 받고 작성된 리뷰입니다.

# SW 아키텍처 전문가가 말해주는 당신의 설계가 실패한 이유

![book]({{ "/assets/img/post/20220626_01.jpeg" | relative_url}})
<br/><br/><br/>

이 책은 유독 추상적이며 비유가 많고 동시에 비슷한 SW 설계 개념과 용어들이 즐비하다. 어떻게 보면 불친절하다는 뜻이지만 나름 실제 설계 예제를 이용하여 독자를 설득하고자 노력하고 있는 것을 보면 애초에 SW 설계 자체가 추상적이고 어려운 개념이라서 어쩔 수 없겠다는 생각도 든다. 어떤 이유가 됐든 주니어 개발자에게는 추천하기 어려운 책이라는 사실은 변함이 없다. 저자의 비유를 빌려보자면 저자는 아키텍처 선생님보다는 아키텍처 코치에 가깝다. 새로운 개념을 차근차근 새로 가르치는 친절한 선생님을 원했다면 이 책은 적절한 선택이 아닐 것이다. 선생님과 코치의 차이는 여기서 나온다. 선생님은 모르는 것을 가르쳐주지만, 코치는 이미 알지만 고쳐지지 않는 문제 또는 미쳐 생각지 못한 부분을 짚어주고 고쳐준다. 그러나 코치라고 표현한 이유는 이 책이 당신이 아키텍처를 실패한 이유를 정확하게 짚어 주기 때문이다. 

SW는 복잡했고 복잡해져왔으며 앞으로 더더욱 복잡해 질 것이다. 수학적 귀납법으로 추론된 이 절망스런 사실 앞에 우리 개발자들은 어떤 자세를 가져야 할까?
이 책에서는 분할, 지식, 추상화라는 무기를 통해 맞서 싸울 것을 각오하고 있다. 저자는 분할, 지식, 추상화 각각을 실제 아키텍처 설계 관점에서 어떻게 적용하여 다룰 지 설명한다. 나에게는 코드 수준을 넘어 시스템 수준에서의 SOLID 패턴의 개방-폐쇄 원칙(Open-Closed Priciple)을 적용하는 느낌이었다. 다르게 말하면 객체 수준이 아닌 시스템 수준에서 확장은 개방적으로, 수정은 폐쇄적으로 대해야 한다는 것이다. 여기서 중요한 점은 '딱 필요한 만큼 만 개방-폐쇄적이어야 하고 이것을 이 '적정 아키텍처 기법'을 `리스크 주도 모델`을 통해 설명한다.


`리스크 주도 모델`을 설명하기에 앞서 저자의 `리스크` 정의를 먼저 살펴본다.

```
리스크 = 인지한 실패 확률 X 인지한 영향
```

쉽게 말해 **실패의 기댓값**이다. 리스크에는 선임 개발자의 교통사고나 고객의 낮은 이해도로 인한 사용자 오류와 같은 프로젝트 관리 리스크부터 응답 메세지 파싱코드 오류 다발, 1000명 동시 접속 할 서버 증설 불가와 같은 소프트웨어 엔지니어링 리스크가 있다. 저자는 이 리스크의 종류와 리스크를 식별하고 우선순위를 지정하는 다양한 기법과 원칙들을 설명한다. SW의 기능이 요구사항을 해결하는 것이라면 아키텍처는 이 SW 리스크를 예방 또는 최소화 해야 한다는 것이다. 저자는 같은 말을 다음 한줄로 요약하여 리스크 주도 모델이 무엇인지 정의한다.

> <리스크>가 있다면, 이를 줄이는 <기법>을 고려하자.


![enough]({{ "/assets/img/post/20220626_thumb.png" | relative_url}})
<br/><br/><br/>


그러나 모든 것에는 Trade-Off가 존재한다. 개발자에게는 주로 발생하는 개발 리소스와 비용을 의미할 것이다. 따라서 모든 리스크를 예방하는 것은 물리적으로 불가능하며 애초에 리스크를 기댓값으로 계산한 이유도 여기에 있다. 결론적으로 **SW 설계에 드는 노력이 프로젝트 리스크에 비례해야 한다**는 것이다. 즉 지나친 선행 설계(Big Design Up Front)를 지양하며 딱 적당한 수준의 아키텍처, 다시 말해 **적정 소프트웨어 아키텍처**를 통해 딱 적정 수준의 아키텍처 설계로 적정수준의 리스크에 대응하라는 것이 이 책의 핵심이다.

이 책을 저와 같이 아키텍처를 설계해 보고 또 실패해본 경험이 있는 개발자에게 추천한다. 저자는 코치가 되어 당신의 아키텍처가 실패한 이유를 정확하게 짚어 줄 것이다. 추상적인 아키텍처가 실패한 구체적인 이유를 통해 다음번엔 더 나은 아키텍처가 될 수 있기를 기원한다.