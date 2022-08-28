---
layout: post
title:  "개발자답게 이슈를 해결하는 법"
feature-img: "assets/img/post/20220727_thumb.jpeg"
thumbnail: "assets/img/post/20220727_thumb.jpeg"
description: 다년간 이슈를 해결하면서 나름대로 배운 교훈들을 간단하게 정리해보며 이슈의 증상과 원인을 찾아서 해결하는 요령을 소개합니다.
date:   2022-07-27 22:27:00 +0900
categories: Developer
---

> 다년간 이슈를 해결하면서 나름대로 배운 교훈들을 간단하게 정리해보며 이슈의 증상과 원인을 찾아서 해결하는 요령을 소개합니다.

<br/>

# TLDR; 

1. 사람을 믿지 말고 증상을 분석하라
2. 원인을 바이너리 서치와 Truth Table로 찾아라 
3. 해결 후 리뷰를 하자

<br/>

![01]({{ "/assets/img/post/20220727_01.jpeg" | relative_url}})

안드로이드 앱 처럼 클라이언트를 개발하다보면 버그가 발생했을 때 원인과 상관 없이 버그가 클라이언트를 통해 노출되므로 다른 개발자들 보다 CS나 이슈레포트를 가장 먼저 접하는 경우가 왕왕있다. 이슈 레포트 또는 CS를 받는 일은 썩 유쾌한 일은 아니다. 그럼에도 우리는 돈을 받고 일하는 직장인이므로 해당 이슈를 해결해야 할 의무가 있다. 비록 경력이 그닥 많은 것은 아니지만 다년간 이슈를 해결하면서 나름대로 배운 교훈들을 바탕으로 **이슈를 개발자 답게 접근하는 방법들**을 간단하게 정리해본다.

<br/><br/>

# 이슈를 해결하는 단계

이슈를 찾아서 해결하려면 다음 3단계를 거쳐야 한다.

1. 증상 파악
2. 원인 분석
3. 문제 해결

각 단계별로 어떻게 진행되는지 순서대로 차근차근 설명해보고자 한다.

<br/><br/><br/>

# 1. 증상 파악

![02]({{ "/assets/img/post/20220727_02.jpeg" | relative_url}})

버그는 기획서와 그에 준하는 문서에서 정의되지 않은 동작을 의미한다. 말하자면 버그냐 아니냐의 기준은 기획서라는 의미이다. 그러므로 이슈(혹은 버그)의 증상을 정의하기 위해서는 기획서를 보고 아래 두가지 질문에 따라 이슈의 증상을 정의해야 한다.

1. 의도된 동작이 무엇이었는지?
2. 실제 동작이 기대 동작과 어떻게 다른지?

> 만약 기획서가 없다면... 애초에 기대 동작이 무엇이어야 하는지부터 파악하는 과정이 필요하다.

<br/>

## 재현이 어려운 이슈

때로는 개발자가 증상 확인이 어려운 환경일 수도 있다. 예를 들어 특정 사용자에게만 발생하고 비슷한 증상의 단말을 찾을 수 없는 경우가 이에 해당한다. 이 경우에는 향후 해결해도 해당 이슈를 재현해서 정말 해결되었는지 확인하기가 번거로워진다. 가능하다면 최대한 증상이 재현되는 상황을 만들고, 그게 어렵다면 증상을 목업으로라도 구현해서 재현 해야한다. 그러나 목업 등으로 임의로 증상을 재현했을 때에는 이후 이슈를 해결한 뒤에 정말로 해결됐는지 증명이 어려워진다. 따라서 가능하다면 되도록 이슈가 재현되는 상황을 만들도록 하자. 

<br/>

## "네스호의 괴물" 버그

![ness]({{ "/assets/img/post/20220727_ness.jpeg" | relative_url}})

> 네스호의 괴물 버그 : 다시는 재현되지 않고 목격자가 1명뿐인 버그.

아주 가끔이지만 딱 한번 목격했지만 두번 다시는 재현할 수 없는 버그가 있다. **재현되지 않는 버그는 버그가 아니다. 왜? 고칠 수가 없으니까!** 좀 더 정확히는 고쳐도 고쳤는지 재현이 안되면 확인이 불가능 할테니까 이미 해결한 문제와 같다. 만약 재현이 불가능하고, 증상도 명확하지 않고, 원인조차 가늠이 안된다면 안타깝지만 방어코드와 해당 시나리오일 때의 로그를 상세히 남기도록 추가하고 이후 상황을 지켜보도록 후속조치를 약속하는 수밖에 없다. 물론 이것은 **정말로 재현이 불가능한 경우**에만 해당된다. 어설프게 살펴보고 재현할 수 없다고 판단해서는 절대 안되고 재현이 불가능한지 꼼꼼히 살펴본 뒤 도저히 불가능 할 때의 이야기이다.

<br/>

## 보다 명확하게 증상을 정의하자

> 고객 불만사항 : 회원 가입이 안돼요! 😱

종종 CS는 위 예시처럼 "회원가입이 안된다"처럼 모호하게 오는 경우가 종종 있다. 여기서 증상을 특정해야하는데, 회원가입 페이지가 접속이 안되는 것인지, 본인 인증이 안되는 것인지, 아니면 마지막 가입 버튼을 눌렀을 때 에러가 나는건지 등 어떤 증상인지 명확하게 정의해야 한다. 아니면 애초에 문제가 아니었거나 아예 엉뚱한 문제일 수도 있다. 따라서 **이 과정에서 중요한 점은 사람을 믿지말라는 것이다.** (이 '사람'에는 본인 스스로도 포함된다.) 절대로 사람의 말과 기억력에 의존하지 말고 반드시 직접 증상을 눈으로 보고 로그를 확인해야 한다. 따라서 가능하다면 이슈를 공유해준 사람으로부터 반드시 화면녹화나 스크린샷을 요청하거나 step by step으로 이슈재현을 부탁하도록 한다. <br/>

예를 들어 `회원가입이 안된다`라는 CS가 있다면 말 그대로 받아들이는 대신 이슈의 증상을 다음과 같이 보다 명확하게 정의해야 한다.

- `회원가입 페이지에서 SMS 본인 인증 문자가 오지 않는다.`
- `로그인 화면을 갱신할 때 앱이 종료된다.`

만약 로그를 분석한다면 다음과 같이 좀 더 정확하게, 혹은 개발자답게 증상을 정의할 수도 있을 것이다.

- `SMS 본인 인증 API 요청 시 Timeout이 발생한다.`
- `로그인 화면을 갱신 시 특정 코드에서 NullPointerException이 발생하여 앱이 종료된다.`

<br/>

## 아직 보고는 이르다

증상만 찾았더라도 아직은 상사와 팀원들에게 보고/공유하기는 조금 이르다. 아주 긴급한 상황이 아니라면 보고/공유는 되도록 원인까지 빨리 확인하고 공유하도록 한다. 증상만 확인하고 이슈를 보고/공유하면 부정확한 정보를 공유하게되거나 아니면 추가적인 증상에 대한 불필요한 피드백이 올 수 있다. 이는 결과적으로 본인의 신뢰를 깎아먹거나 일을 괜히 더 번거롭게 만들 수 있다. 촌각을 다투는 긴급한 상황이 아니라면 원인부터 찾고 해결가능한지를 알려주는 편이 좋다.<br/>

> 어차피 상사라면 결국 듣고 싶은 말은 "해결했습니다." 또는 "해결 할 수 있습니다." 뿐이니 너무 조급해 하지 말고 차분히 원인을 찾자.

그렇다면 원인은 어떻게 찾아야 할까?

<br/><br/><br/>

# 2. 원인 분석

![03]({{ "/assets/img/post/20220727_03.jpeg" | relative_url}})

## **증상과 원인은 다르다.**

증상과 원인은 다른 것인데 가끔 혼용하거나 놓치는 경우가 있다. 에러가 발생하는 것은 **증상**이다. **원인**은 해당 에러가 어느 코드 또는 기술적 문제로 인한 발생인지를 의미한다. 예를 들어 윗 문단의 회원가입이 안되는 증상에 대해 원인은 다음과 같은 형태로 정의할 수 있다.

- `SMS 본인 인증 API의 주소가 잘못되어 Timeout이 발생한다.`
- `로그인 화면을 벗어날 때 초기화되지 않은 User객체에 접근하여 NullPointerException이 발생한다.`

만약 원인을 찾기 어렵다면 증상분석이 부족한 것일 수 있으니 다시 전단계로 돌아가서 증상을 면밀히 살펴보도록 한다. 

<br/>

## 증상과 원인은 1:1 대응하지 않는다. 

원인을 찾을 때 잊지 말아야 할 점은 **증상과 원인은 1:1 대응하지 않는다는 것이다.** 하나의 원인으로 하나의 증상이 나타나는 것이 일반적이겠지만 그렇지 않은 경우도 많다. 하나의 원인으로 여러가지 증상이 나올수도 있으며, 하나의 증상이 사실은 여러가지 원인이 동시에 복합적으로 작용할 수도 있기 때문이다. 그러므로 정말로 원인이 하나인지, 혹은 찾은 원인이 미처 파악하지 못한 다른 버그를 만들고 있지는 않았는지 확인해야 한다.

<br/>

## 무엇이 달라졌는지 확인하라 

기본적으로 언제부터(혹은 어느 버전부터) 발생했는지와 해당 시점이후 무엇이 바뀌었는 지를 확인하면 큰 힌트를 얻을 수 있다. 가령 예를 들어 새로운 버전의 앱 배포 이후에 기존 기능이 동작을 안한다면 배포범위 중 해당 기능에 영향을 미치는 구간을 살펴보아야 한다. 반대로 새로운 앱 배포는 없고 서버의 패치 배포가 있었는데 멀쩡히 정상 동작하던 기능이 어느 순간부터 동작을 안한다면 우리는 합리적 추론에 의해 서버를 의심해 볼 필요가 있다. 물론 이전 버전에서 발생하지 않았다고 이전 버전에 문제가 없다고 단정해서는 안된다. 

<br/>

## 탑다운, 바텀업

![topdown-bottomtop]({{ "/assets/img/post/20220727_012.jpeg" | relative_url}})
> 이슈를 위에서 아래로 내려다 볼 것인가? 아래에서 위로 올려다 볼 것인가?

`⚠️ 여기서의 탑다운, 바텀업은 일반적으로 SW업계에서 통용되는 개념은 아니고 필자가 임의로 정한 내용이다.`

모바일이나 웹과 같은 클라이언트라면 보통 UI로 증상을 확인할 수 있을 것이고, 서버라면 API 종단 또는 DB로의 결과 값 등이 있을 것이다. 편의상 UI나 API, DB와 같이 외부 프레임워크를 통해 개발자가 눈으로 결과를 볼 수 있는 종단점을 `저수준`으로 칭하자. 반대로 해당 기능을 처리하는 순수한 논리의 코드, 이른바 **도메인 로직**이나 내부 로직, 함수 등을 `고수준`으로 칭하자. <br/>
(여기서 `저수준`과 `고수준`은 추상화 단계의 의미의 높고 낮음이지, 어렵거나 쉽거나, 고상하거나 저급하거나 등의 의미가 아님을 미리 밝힌다.)<br/>

`저수준`은 기본적으로 눈에 보이는 결과를 나타낸다. UI라면 단말의 디스플레이를 통해서, API라면 클라이언트 입장에서는 서버 상의 로그나 결과, 반대로 서버라면 클라이언트에서 그 응답 결과를 확인할 수 있다. DB나 그 외 입출력은 저장된 파일이나 데이터로 내가 작성한 코드 밖에서 결과를 확인할 수 있따. 즉 **프로그램 외부에서 그 결과를 확인할 명확한 방법**이 존재한다는 것이다.<br/>

반대로 `고수준`은 코드 중간에 돌아가는 로직으로 숨겨져 있다. 디버깅 모드로 코드 중간에 값을 출력하거나, 로그로 중간단계의 값을 출력하지 않는다면 내부에서 어떤 동작이 일어나는지 확인할 길이 없다. 또한 여러 단계를 거쳐서 복잡한 로직이 되기도 한다.<br/>

**탑다운(Top-Down)**은 `고수준에서 저수준`으로 이슈에 접근하는 방식을 의미하고 **바텀업(Bottom-Up)**은 `저수준에서 고수준`으로 가는 방식을 의미한다. 눈에 보이는 증상이 명확하고 복잡한 로직이 아니라면 **바텀업** 방식으로 분석하는 것이 유리하다. 가령 예를 들면 문제가 되는 UI 출력값을 낼 수 있는 원인을 찾아 UI 렌더링 로직부터 내부 로직으로 올라가며 찾을 수 있다. 반대로 눈에 보이는 증상이 명확하지 않고 내부 로직이 여러단계를 거친다면 **탑다운** 방식이 유리할 수 있다. 즉 내부로직에서 부터 문제점을 찾아가며 그 문제로 인한 증상이 맞는지를 찾아갈 수도 있다. <br/>

중요한 것은 탑다운이든 바텀업이든 어느 방식이 더 좋고 나쁜 것이 아니라 그때그때 어떻게 접근해야 할지 적합한 방법이 무엇인지 고려해보아야 한다. 필요하다면 둘다 활용해가면서 이슈의 증상과 원인을 찾아나가야 한다.

<br/>

## 바이너리 서치로 찾아라

![binary-search]({{ "/assets/img/post/20220727_bin.png" | relative_url}})

개발자라면 무언가를 찾을 때 순차적으로 찾지 말고 바이너리 서치를 활용하자. 코드나 히스토리에서 특정 범위에서 이슈 발생 구간을 찾거나 비교할 때 위에서부터 하나씩 순차적으로 찾는 대신 바이너리 서치로 항상 중간 지점을 기준으로 찾는다. 예를 들어 버그 발생 소지가 있는 코드 구간을 찾을 때 **바텀업**으로 찾는다고 하면 코드를 저수준인 UI에서 고수준 로직으로 하나씩 비교해가며 찾는 대신, 중간 지점을 기준으로 나누어서 어느 쪽에 문제가 있고 어느 쪽이 문제가 없는 지 찾는다. 다른 예시로 깃 로그를 부면서 마지막으로 잘 동작한 버전(또는 커밋)을 찾을 때 위에서 부터 하나씩 비교하는 대신 항상 중간부터 확인하며 바이너리 서치로 어느 버전에서 문제가 생겼는 지를 찾아야 한다. 

<br/>

## 비교군과 대조군으로 찾아라

> "한번에 하나 씩만 바꿔라"<br/>
> 신입시절 사수에게 많이 혼나면서 배웠던 가르침

디버깅 과정 등에서 동작 비교를 할 때 반드시 하나씩만 바꿔서 찾는다. 과학시간에 이것을 **비교군과 대조군**이라고 배우는데, 정확한 비교를 위해서는 **비교군과 대조군은 반드시 1개의 변인요소 만** 있어야 한다. 쉽게 말해 코드를 여러구간을 다 수정하고 동작 비교를 하게 된다면 원하는 결과를 얻었을 때 정확한 원인을 알 수 없다. 따라서 정확한 비교를 하기 위해 비교군과 대조군을 비교할 땐 변인요소가 하나뿐인지 확인해야 한다. 비교를 위해 바꿔가면서 확인할 때도 하나씩만 바꿔서 비교하도록 한다. 하지만 의심되는 구간이 많다면 여러개를 동시에 수정하고 위에서 말한 것 처럼 바이너리 서치로 절반씩 바꿔가며 찾을 수도 있다. 

<br/>

## TruthTable과 카르노맵

![21]({{ "/assets/img/post/20220727_021.png" | relative_url}})
> 카르노 맵(영어: Karnaugh map, 간단히 K-map)은 논리 회로 용어로, 불 대수 위의 함수를 단순화하는 방법이다. 불 대수에서 확장된 논리 표현을 사람의 패턴인식에 의해 연관된 상호관계를 이용하여 줄이는 방법이다.<br/>

결과적으로 비교군과 대조군의 결과가 나왔다면 TruthTable(진리표) 혹은 카르노맵을 그려본다. 정말로 변인요인이 하나씩이라면 TruthTable을 통해 빠르게 찾을 수 있다. 변인요소가 많더라도 하나씩 분리하여 카르노맵을 그리면 논리적으로 무엇이 문제인지 금방 찾을 수 있을 것이다. 

<br/>

## 이슈 보고하기 

원인을 발견했다면 이제 해당 이슈를 다른 사람들에게 보고/공유해도 된다. 왜냐하면 원인을 발견한 시점에서 아래 세가지를 파악할 수 있기 때문이다.

- **해결이 가능한지 여부** : 가장 중요한 문제
- **이슈의 영향도** : 어떤 증상들이 발생할 수 있는지
- **해결 계획과 소요시간** : 이슈를 해결하는 데 얼마나 걸릴지

위 세가지 내용을 정리해서 보고하도록 하자.

<br/><br/><br/>

# 3. 문제 해결

원인을 정확하게 분석했다면 이제는 해결할 차례다. 문제가 되는 코드를 수정하고 배포하자. 그리고 증상이 재현되는지 직접 눈으로 확인한다.

<br/>

## 사람을 질책하지 말라 

![05]({{ "/assets/img/post/20220727_05.jpeg" | relative_url}})

> 누구나 실수는 한다. 그러므로 버그는 미워해도 개발자를 미워하진 말자. 

다른 팀 또는 팀원으로 인한 이슈일 지라도 질책하지 말고 최대한 정중히 협조 요청하도록 한다. 질책은 더 이슈를 찾기 어렵게 만들 수도 있고 이후의 협업을 어렵게 만들 수도 있다. 직접 겪은 경험담을 이야기해보자면 아주 오래전에 다녔던 회사에서는 이슈만 보면 인격모독 수준의 질책을 하던 상사가 있었는데 결과적으론 부하직원들이 이슈가 생겼을 때 숨기거나 남탓을 하거나 심지어 거짓보고를 하는 수순에 이르게 된 적이 있었다. 이는 단순히 직원들의 사기가 떨어지는 수준의 문제가 아니라 잠재적인 위험을 키우는 꼴이다. 사람이라면 누구나 언젠간 실수하기 마련이니 사람에겐 관대해지고 대신 **같은 이슈를 방지할 수 있는 시스템을 견고히 다지는 것**이 필요하다. 

<br/>

## 해결이 불가능한 이슈

여러가지 사정으로 인해 기술적/물리적으로 해결할 방법이 정말 아예 없는 경우가 아주 가끔 있다. 우회 해서라도 해결 할 수 있다면 우회해서 해결해야겠지만 정말 가끔은 정말 물리적으로 어쩔 수 없는 경우가 있다. 가령 예를 들어 모바일 앱이 운영체제, 단말 기종, 외부 라이브러리, 통신사 등에 의해 서비스가 마비 또는 장애가 발생하는 경우가 있다. 이럴 땐 어쩔 수 없이 싹싹빌고 대안과 대응계획을 약속 해야한다. 비록 불편한 대안이더라도 반드시 대안 찾아서 제시하고 이후 대응 일정을 약속하고 공유하는 것으로 마무리 한다. 후속 조치로 같은 일이 발생하지 않도록 예방책을 준비하는 것도 잊지 말자. 필자가 신입 개발자 시절 데이터센터에 화재가 발생하여 서비스가 마비가 된 적이 있었는데 애초에 데이터 센터가 마비된 상황이라 긴급 공지 API조차 발송할 수 없었던 적이 있었다. 결국 사용자 전원에게 서비스 중단 안내 SMS 메세지를 발송하고 화재가 마무리 된 이후에 긴급공지 API를 자사 서버가 아닌 외부 서버를 이용해서 서버가 완전히 죽어도 공지(사실상 유언)를 남길 수 있도록 API를 이원화하는 후속조치를 취했다.

<br/>


## 리뷰하기

이후의 이슈를 미연에 방지하기 위해 이슈를 찾고 해결하는 과정에서 문제가 없었는지 파악하는 리뷰를 하도록 하자.

- 로그가 부족 하진 않았는가? 로그를 충분히 잘 남기고 있는가?
- 크래시레포트는 적용했는가? 잘 동작했는가? 
- 비슷한 실수를 사전에 예방할 방법은 없을까?
- 테스트코드가 작성되어 있는가? 테스트코드에 문제는 없었는가?
- 향후 본인 스스로나 다른 개발자가 같은 실수를 반복할 여지가 있지는 않은가?
- 혹시 리팩토링이 필요한 시점은 아닌가?
- 디버깅 툴 활용하는데 어려움은 없었는가?

 팀에 따라서는 오답노트처럼 해당 이슈에 대한 기록과 반성을 기록하는 **이슈노트**를 적는 팀도 있다고 하던데 신입 개발자라면 한번 쯤 이런 습관을 들여보는 것도 도움이 많이 될 것이라 생각한다.

<br/>


# 그 외 소소한 팁

![04]({{ "/assets/img/post/20220727_04.jpeg" | relative_url}})

- 컴퓨터는 거짓말을 하지 않는다. (적어도 사람보다는 믿을만 하다.)
- 원인 분석이 어렵다면 러버덕 디버깅을 해보자.
- 간헐적 이슈는 보통은 타이밍 이슈다. 
- 웹에서 특정사용자에게만 발생한다면 AdBlock같은 플러그인부터 의심해본다.
- 이슈 재현할 땐 되도록 화면 녹화를 써서 기록해두자.
- OS나 시스템 이슈는 공식 포럼, 홈페이지, 커뮤니티에서 비슷한 증상을 찾고 해결책이 있는지 확인한다.

<br/><br/>

# 결론

![06]({{ "/assets/img/post/20220727_06.jpeg" | relative_url}})

사실 이슈를 너무 잘 찾아서 이슈 찾는 법을 감히 정리한 것은 아니다. 나에게도 끝까지 원인을 찾지 못한 이슈도 있었다. 이제와서 적기엔 민망하지만 이 글은 이슈를 잘 찾는 요령일 뿐이지, 이슈로 도달하는 정답을 이야기 하고 있지 않다. 따라서 위 내용만으로는 해결이 어려운 이슈도 많이 있을 것이다. 다만 이슈를 찾는게 어렵거나 힘들다면 위 방법대로 해보면서 차근차근 접근해보시기를 바란다. 결과적으로 **현실의 문제를 개발자 답게 논리적으로 접근**하여 해결하는 요령과 습관이 몸에 밴다면, 그리고 그 **과정을 개발자 답게 시스템화** 할 수 있다면 어느새 성장해있는 본인을 발견할 수 있으리라 확신한다.


<br/><br/><br/>