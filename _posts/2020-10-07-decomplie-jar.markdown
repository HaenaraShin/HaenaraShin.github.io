---
layout: post
title:  "JAR 라이브러리 뜯어고치기 (.class 바이트코드 수정)"
feature-img: "assets/img/post/20201008_1.jpeg"
thumbnail: "assets/img/post/20201008_1.jpeg"
description: JAR 안에 빌드된 바이트코드(.class)를 수정하는 방법을 소개합니다.
date:   2020-10-07 23:42:00 +0900
categories: Android
---

> 이번 포스팅에서는 JAR 안에 빌드된 바이트코드(.class)를 수정하는 방법을 소개합니다.

{% include aligner.html images="post/20201008_1.jpeg" %}

<br>

Java 개발자로서 살다보면 자주는 아니지만
이미 빌드가 완료된  JAR 파일의 바이트코드를 수정해야 하는 일이 생길 수 있다.
(없다면 다행이고… 🙄)

정말 피치 못할 사정때문에 해야한다면 아래 방법을 참고해보자.

# 0. 🎬 사전 준비
바이트코드를 수정할 수 있는 에디터 JBE를 준비한다.

바이트코드 에디터는 여기 에서 다운로드 받는다.

> * 맥에서 jbe 실행방법은 터미널 해당 경로에서 아래와 같이 친다.

```console
$ java -cp bin ee.ioc.cs.jbe.browser.BrowserApplication
```

# 1. 🚧 JAR 라이브러리 압축 해제

1. JAR 파일의 확장자를 .zip 으로 변경 후 압축해제 하거나 콘솔에서 unzip 명령어로 압축을 푼다.

 > JAR 파일을 압축해제 해보면 Manifest와 .class파일로 구성되어 있다.

# 2. 🛠️ 클래스파일 수정

1. 바이트코드 에디터로 압축해제한 파일에서 수정할 class 파일을 연다.

2. Methods > 수정할 함수이름 > [0] Code > Code Editor 탭으로 이동한다.

3. 원하는 라인을 수정한다.

4. Save method 로 class 파일 저장한다.

> 수정하는 방법은 아래의 Tip 항목을 참고해보자.

# 3. 🤐 JAR 라이브러리 재압축
1. 압축해제한 최상위 경로의 com, META-INF을 다시 zip 파일로 압축한다.

2. zip 파일의 확장자를 .jar 로 수정한다.

> 맥의 기본 압축프로그램을 사용 시 .DS_Store 와 __MACOSX 와 같은 파일이 첨부되므로 WinArchiveLite를 사용하거나 콘솔에서 zip 명령어로 직접 압축한다.

 

 

# + 💁‍♂️ 바이트코드 수정하는 Tip
바이트코드 명령어의 목록은 여기에서 확인할 수 있다.

**위키피디아 링크** : [https://en.wikipedia.org/wiki/Java_bytecode_instruction_listings](https://en.wikipedia.org/wiki/Java_bytecode_instruction_listings)

 

바이트코드 읽는 요령을 아주 간단히만 적자면 (간단할 수가 없지만..)

명령어는 보통 {데이터형} + {명령코드(opcode)} 형태로 되어 있는데
가령 예를들어 정수형 값을 저장한다면 istore, 객체를 저장한다면 astore
만약 정수형 값을 불러온다면  iload, 객체를 불러온다면 aload 와 같은 식이다.
또한 상수값의 경우는 iconst와 같이 const 를 이용하여 불러온다.

 

가령 아래와 같이 Java 코드를 수정한다고 치자.

| Before    | After     |
|-----------|-----------|
| ```return 1;``` | ```return 0;``` |

이를 Before 에서 After로 수정하고 싶다면 아래와 같이 bytecode를 수정하면 된다.

| Before              | After               |
|---------------------|---------------------|
| ```iconst_1```<br>```ireturn``` | ```iconst_0```<br>```ireturn``` |
 

사실 일부사례를 더 적었으나…
단순히 코드와 클래스파일이 정확하게 변형 되는 것이 아니라서
상황에 따라서 틀릴 수 있는 내용이 포함되어 삭제했다… 😥

바이트코드의 명령어의 정의를 찾아보면 좀 더 도움이 될 수 있다.