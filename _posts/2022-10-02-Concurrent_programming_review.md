---
layout: post
title:  "[도서리뷰] 동시성 프로그래밍"
feature-img: "assets/img/post/20221002_thumb.png"
thumbnail: "assets/img/post/20221002_thumb.png"
description: "당신을 SW의 복잡도 늪에서 당신을 꺼내줄 지침서 O'REILLY Concurrent Programming 를 읽고 리뷰합니다."
date:   2022-10-02 23:15:00 +0900
categories: Book
---
# O'REILLY Concurrent Programming 리뷰

![01]({{ "/assets/img/post/20221002_thumb.png" | relative_url}})

> 한빛 미디어 서평단 활동의 일환으로 책을 제공 받고 작성된 리뷰입니다.

# SW의 복잡도 늪에서 당신을 꺼내줄 지침서

날이 갈수록 SW는 점점 더 복잡해지고 있다. 여러가지 복잡한 연산과 동작을 동시에 처리해야 하고 그와 동시에 다루는 데이터의 용량은 훨씬 커지고 있다. 이를 해결하기 위해 사용하는 것이 동시성의 개념이다. 아이러니 하게도 정작 개발 언어와 프레임워크는 이런 복잡한 처리 과정을 굳이 알 필요 없도록 프로그래밍 자체는 점점 더 쉬운 방향으로 발전하고 있다. 결과적으로 동시성 프로그래밍을 누구나 손쉽게 필수적으로 사용하게 되었지만, 동시에 개발자는 이런 한꺼풀 아래 숨겨진 동시성 개념과 원리를 더 이상 고민 하려 하지 않는다. 하지만 눈에 보이지 않는다 하더라도 동시성 프로그래밍은 여전히 치명적이게 중요하다. 
<br/>
평소에는 동시성 프로그래밍에 대한 이해 없이 사용하더라도 당장은 큰 문제가 되지 않을 것이다. 하지만 어느 순간 마구잡이로 개발하다간 개발 실력과 어플리케이션 모두 **데드락(교착상태)**에 빠져버릴 지도 모른다. 그리고 이런 문제를 해결하기 위한 누더기 코드는 심각한 성능저하로 이어질 수 있다. 당신이 동시성 프로그래밍을 공부해야 하는 이유가 여기에 있다. 수준높은 개발자가 되고 싶다면, 또는 최적화 된 코딩을 하고 싶다면 이 책이 당신에게 복잡한 SW 세상에서 살아남기 위한 지침서가 되어 줄 것이다.

<br/><br/>

# 이 책은 

이 책은 기본적으로 동시성 프로그래밍의 기본적인 개념과 패턴을 설명한다. 컴퓨터공학 전공자라면 운영체제에서 배웠던 멀티 태스킹 챕터를 조금 더 상세히 다룬다고 이해할 수 있을 것이다. 이런 개념 설명을 위해 C와 Rust의 예제를 사용하고 있으며 멀티 스레드 관리, 액터 모델 등 참고하기 좋은 다양한 예제를 포함하고 있다. 이런 멀티 태스킹 자체가 직관적이지 못해서 설명이 어렵다 보니 다양한 그래프와 이미지로 설명을 돕고 있으며 적절한 예제들로 구성되어 있다. 또한 병렬 처리, 비동기 프로그래밍, 멀티 테스크 등 동시성 프로그래밍 전반에 대해 다루고 있다. 그래서 꼭 C나 Rust 개발자가 아니더라도 고수준의 언어(이른바 Managed language)에 익숙한 분들도 코루틴과 같은 비동기 프로그래밍에 대한 이해도 챙기실 수 있을 것이다.
<br/>
C와 Rust의 경우는 기본 문법을 설명하고는 있긴 하지만 이는 예제를 이해시키기 위한 빌드업 수준이다. 하지만 메모리 수준에서의 동작까지도 설명하고 있으므로 메모리 관리를 알아서 해주는 고수준의 언어에 익숙한 분들이라면 생소할 수는 있겠으나, 언어레벨 밑에서 어떤 동작들이 이루어 지는지를 이해할 수 있게 된다. 예를 들면 Java에서 원시 타입(primitive type)이 왜 따로 분류되고 Call-by-value 와 Call-by-reference의 차이가 생기는 지 자연스럽게 이해할 수 있게 될 것이다.

<br/><br/>

# 동시성의 어려움

> "Java에서 Exception과 Error의 차이가 무엇인가요?"

현재 재직 중인 회사의 면접에서 받았던 질문이다. <br/>Java에서 `Exception`은 보통 소스코드의 로직을 잘못 짜거나 충분히 예상 가능한 문제가 발생하는 예외이다. 가령 예를 들면 엉뚱한 값을 요구하거나 호출해서는 안되는 코드를 호출하거나 하는 경우에 발생한다. 즉 같은 조건에서 같은 구문을 실행한다면 동일하게 발생하는 경우가 많고 try-catch로 예외처리를 한다면 프로그램은 원치 않는 동작을 할 지언정 죽지는 않는다. `Exception`은 코딩을 하다보면 자주 만나게 되고 또 대부분은 원인만 찾으면 어렵지 않게 해결할 수 있다.
<br/>
그에 비해 `Error`는 시스템상의 치명적인 오류를 의미한다. 메모리 부족, 데드락(교착상태), 빌드가 잘못되어 클래스의 정의가 애초에 누락되어 있는 등 소스코드 밖의 문제거나 근본적으로 무언가가 심각하게 잘못되어 있을 때 발생하는 경우가 많다. 그래서 Exception과 달리 try-catch로 예외처리를 해도 프로그램이 죽거나 애초에 해결이 어려운 경우가 왕왕 있다. 
<br/>
동시성 프로그래밍은 이 `Error`를 발생시키는 주범이다. 이 점이 동시성 프로그래밍을 유독 어렵게 만든다. 복잡한 만큼 특유의 버그와 문제점을 내포하고 있다. 또한 테스트가 어렵고 직관적으로 이해하기가 어렵기 때문에 많은 개발자들에게 어렵게 다가올 수 밖에 없다. 대표적으로는 데드락, 재귀락, 메모리 버그 등 심각한 문제를 초래할 수 있다. 게다가 직관적이지 않은 탓에 동시에 코드 한두줄 대충 바꿔서 해결하려다가 오히려 더 심각한 문제를 발생시키기도 한다. 결국 본질적인 동시성 프로그래밍에 대한 이해가 없으면 

<br/><br/>

# 이런 분들께 추천드립니다.

C기반 언어를 다루시거나 Rust를 하시는 분들에게는 강력 추천드린다. 이런 분들에게는 이미 최적화된 코딩이 전제되어 있으므로 이런 동시성 프로그래밍 개념들이 필연적으로 다가올 것이다. 특히 다양한 예제와 친절한 그림으로 직관적이지 않은 어려운 내용도 비교적 명확하고 차분하게 설명하고 있으며, 멀티 스레드 관리, 액터 모델 등 한번 익혀두면 두고두고 사용할법한 예제들이 다양하게 포함되어 있기 때문에 분명 도움이 될 것이다.
<br/>
신입개발자에게는 조금 어려울 수 있는 내용이지만, 학부생 때 전공 수업에서 한번씩 배우는 내용임을 감안하면 못읽을 정도는 아니므로 용기만 있다면 읽어보기를 추천드린다. 여기서 배우는 동시성, 비동기 기법들이 이해가 어렵더라도 언젠간 반드시 필요한 순간이 오기 때문에 나중에라도 이 책을 다시 펼쳐보게 될 날이 반드시 올 것이다. 
<br/>
물론 모든 개발자가 동시성 프로그래밍을 익혀야 할 것은 아닐지도 모른다. 메모리 관리와 동시성 까지도 알아서 처리해주는 것에 익숙해져서 그 필요성을 못느끼는 것도 무리는 아닐 것이다. 그러나 더 좋은 SW를 개발하고 싶고, 더 최적화 된 코드를 짜고 싶은 욕심이 있으신 분들이라면 이 책을 통해 우리가 그 동안 우리가 잊고 있었던 동시성 프로그래밍의 중요성과 최적화의 실마리를 찾아내는 계기가 되실 수 있으리라 생각한다. 여러분 모두 한단계 더 깊은 내공을 얻어 가시길 바라는 마음에서 이 책을 추천 드린다.