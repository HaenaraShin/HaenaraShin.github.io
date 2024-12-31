---
layout: post
title:  "PR 리뷰하면서 코드 분석하는 요령"
feature-img: "assets/img/post/20230604_thumb.jpeg"
thumbnail: "assets/img/post/20230604_thumb.jpeg"
description: "PR 리뷰할 때 여러가지 팁들과 함께 코드를 읽는 요령들을 소개합니다."
date:   2023-06-04 23:36:00 +0900
categories: Daily
---

![01]({{ "/assets/img/post/20230604_01.jpeg" | relative_url}})<br/>

다른 사람의 PR을 보면서 코드리뷰를 하다보면 내가 잘 모르는 코드를 읽어야하는 경우가 왕왕 있다. 이번엔 PR 리뷰할 때 여러가지 팁들과 함께 코드를 읽는 요령들을 작성해보았다. 예시는 Github과 안드로이드 기준으로 작성했으나, 기본적인 원리는 다른 환경과 플랫폼에서도 동일하게 충분히 적용가능 할 것이다.<br/><br/>

우선은 GitHub 웹페이지에서 우선적으로 확인해야할 내용들이다. <br/>

# 스펙 확인

![02]({{ "/assets/img/post/20230604_02.jpeg" | relative_url}})<br/>

학부생시절 SW 엔지니어링에서 배운 것이 있다면 모든 SW의 개발은 SW 요구사항(SW requirement)에서 출발한다. 새로운 기능이 되었든, 버그 수정이 되었든 어떠한 SW 요구사항을 충족시키기 위해서 코드를 작성하는 것이다. 조직에 따라 이 SW 요구사항을 스펙이라고 부르기도 하는데, **PR 작성자도, 리뷰어도 이 스펙을 확실히 이해하는 것이 개발의 시작이라 할 수 있겠다.**

따라서 PR 역시 새로운 기능개발이나 버그 어느쪽이든 반드시 기획서(혹은 그에 준하는 문서)를 바탕으로 AS-IS와 TO-BE가 명확하게 정의되어 있어야 한다. PR description에는 그 기획서를 바탕으로 무엇을 수정하고자하는 지 의도가 무엇인지를 설명하고 있어야 한다. **리뷰어 역시 해당 스펙을 확인해서 PR의 의도를 파악하는 것이 중요하다.** 그래서 PR의 목표를 이해해야 해당 commit들이 목표를 충실히 달성하고 있는지를 파악할 수 있다. 리뷰어가 스펙을 명확하게 파악하지 못한다면 단순한 오탈자 수준의 리뷰밖에 할 수가 없고, 만에 하나 엉뚱한 수정을 하더라도 '그런갑다'하고 넘어가게 되어버릴 수 있다.

<br/><br/>

# Github plugin

![03]({{ "/assets/img/post/20230604_03.jpeg" | relative_url}})<br/>

- https://chrome.google.com/webstore/detail/better-pull-request-for-g/nfhdjopbhlggibjlimhdbogflgmbiahc

better pull requst for Github 을 이용하면 수정된 파일이 어느 패키지에 있는지 확인할 수 있다. 이것만 먼저 잘 살펴봐도 어느 feature 또는 module이 수정된 것인지 쉽게 파악할 수 있다. 결과적으로 수정범위와 영향도를 한눈에 살펴볼 수 있다.

![04]({{ "/assets/img/post/20230604_04.jpeg" | relative_url}})<br/>

- https://chrome.google.com/webstore/detail/octotree-github-code-tree/bkhaagjahfmjljalopjnoealnfndnagc

octotree를 이용하면 github에서도 패키지를 쉽게 펼쳐볼 수 있다. 후술 하겠지만 PR을 직접 로컬에 체크아웃해서 IDE로 보는 편이 더 낫지만, 웹에서 간단하게 확인할 필요가 있을 때 octotree를 이용하면 편리하게 이용 가능하다. 다만 아쉬운 점은 Github Enterprise를 사용하고 있다면 PRO버전을 구매해서 사용해야 한다.<br/><br/><br/>

Github 웹페이지로 PR을 자세히 보는 것도 분명 필요하지만 충분하지는 않다. 이번에는 로컬에서 확인하는 요령들을 설명한다.<br/>

# PR 다운받기

오탈자 혹은 리소스 수정과 같이 아주 간단한 것이라면 굳이 PR을 다운받아 볼 필요가 없지만 되도록이면 PR은 코드를 workspace에 직접 받아서 IDE에서 수정을 확인하는 것이 좋다. 

기존 함수나 클래스를 사용하는 경우 github PR에서는 수정되지 않은 코드는 확인이 번거롭다. 따라서 PR의 diff만 본다면 수박 겉핥기처럼 확인하게 되어버린다. IDE에서 확인한다면 실제 구현체를 코드 흐름에 따라 파악할 수도 있고, 같은 디렉토리의 다른 파일들과 비슷한 용례들을 찾아보면서 분석할 수 있다. 아니면 실제로 빌드를 해서 실행보면서 실제 동작을 비교하면서 테스트해볼 수도 있다. 또한 IDE에서 제공하는 code analysis 기능을 통해 실수한 부분은 없는지 확인해 볼 수도 있다. 결론은 직접 받아서 보아야 자세히 볼 수 있다는 의미이다.

GitHub에서 Clone받은 프로젝트에서 아래 명령어를 콘솔에 입력하면 PR을 로컬 워크스페이스에 체크아웃 할 수 있다. 

> {PR번호} 안에 # 없이 PR번호를 입력하면 된다. <br>
> PR번호는 PR제목 옆에 #...처럼 작성되어 있는 것으로 확인 가능하다.

```bash
git pull origin pull/{PR번호}/head:pr-{PR번호}
```

매번 명령어를 치는 것이 번거롭다면 아래와 같이 shell 스크립트로 짤 수도 있다.

```shell
#!/bin/sh

if [ "$#" -gt "0" ]
then
	cd {프로젝트경로}
	echo "`git reset --hard HEAD`"
	echo "`git fetch origin pull/$1/head:pr-$1`"
	echo "`git checkout pr-$1`"
else
	echo "PR number not found."
fi
```

위 스크립트를 `get_pr.sh`와 같이 파일로 저장하고 실행할 땐 콘솔창에서 아래와 같이 PR 번호를 같이 입력하여 실행한다.

```bash
$ bash get_pr.sh 54574
```

<br/><br/><br/>

# 종으로 횡으로

호출된 코드들의 흐름에 따라 각각의 정의를 타고 들어가면서 수직(종)으로 확인하는 방법과, 구현체와 같은 수준에 정의되거나 비슷한 기능을 하는 다른 클래스들을 함께 둘러보는 수평(횡)으로 확인하는 방법을 각각 소개한다.

## 종으로 분석

코드를 읽을 때 수직적으로 상속관계를 확인하는 방법이 가장 직관적으로 도움이 많이 된다. 해당 구현체가 어떤 변수와 함수들로 구현되어 있는지 **실제 구현체**를 확인하는 방법이다. 대부분의 IDE에서는 이런 분석을 도와주는 편의기능들과 단축키가 존재한다. **이런 기능과 단축키는 반드시 숙지하고 있어야 한다.** 파악해두어야 하는 필수 기능들은 아래와 같다. (IDE마다, OS마다 단축키가 다르겠지만 MAC용 Android Studio 기준으로 작성하였다.)

- 정의로 이동 (cmd + 클릭)
- 사용구간 검색 (opt + F7)
- 구현체 확인 (cmd + opt + B) 

이를 통해 실제 실제 코드 실행 흐름에 따라 어떤 구현체체가 어떤 함수를 호출하는지, 어떤 변수가 값이 변하는지 등을 파악할 수 있다. **정의로 이동**을 통해 어떤 클래스인지, 어떤 함수인지, 무엇을 하는지 파악할 수 있다. **사용구간 검색**으로 해당 함수나 변수가 언제 호출되는지를 파악할 수 있다. **구현체 확인**으로 인터페이스나 추상 클래스를 어디서 상속받았는지를 확인할 수 있다.

<br/>

## 횡으로 분석

일반적으로 코드 분석을 하다보면 자연스럽게 수직적으로 실행 흐름에 따라 분석하게 된다. 그러나 수직적으로만 분석하면 전체적인 구조적인 부분에서 이해하지 못하는 경우가 생길 수 있다. 단순히 실행에 문제가 없는지가 아니라 아키텍처 혹은 구조적인 이해를 하기 위해서는 수평적으로 분석할 필요가 있다. 

수평적으로 분석한다는 의미는 같은 패키지(디렉토리) 레벨에서 정의된 다른 클래스들과 비슷한 기능을 하는 다른 클래스들을 확인하는 것을 의미한다. 이를 통해 단순히 실행되는 로직 뿐 아니라 어떤 계층으로 클래스를 나누었는지, 클래스마다의 책임과 역할이 무엇인지, 통일성이 잘 지켜졌는지 등을 파악할 수 있다. 

<br/><br/>

# 실제로 실행해보기

안드로이드의 경우는 실행 중인 activity를 확인하는 방법이 있다.
콘솔창에서 아래와 같이 실행하면 실행중인 activity 목록을 확인할 수 있다.

```shell
adb shell dumpsys activity | grep -e Run
```

디버그를 걸고 한줄 한줄 브레이크를 걸면서 실제 실행이 어떻게 되는지 확인할 수도 있다. 특히 동적으로 주입된 객체의 경우 실제로 어떤 클래스가 호출되는지 파악할 때 디버깅을 직접 해보는 편이 가장 정확하고 속편하다. 

물론 코드상에서 로그를 찍도록 해서 구간마다 실행되는 여부를 직접 로그로 확인할 수도 있지만, Android Studio 에선 디버거로 로그를 출력할 수도 있다. 

<br/><br/><br/>
