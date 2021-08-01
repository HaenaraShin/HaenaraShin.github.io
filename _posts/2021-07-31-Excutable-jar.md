---
layout: post
title:  "Kotlin으로 실행 가능한 JAR 파일 만들기"
feature-img: "assets/img/post/20210731_thumb.jpeg"
thumbnail: "assets/img/post/20210731_thumb.jpeg"
description: "Kotlin으로 실행 가능한 JAR 파일 만들기"
date:   2021-07-31 21:11:00 +0900
categories: kotlin
---

이번 포스팅에서는 코틀린으로 실행가능한 JAR파일을 만드는 방법을 소개하고자 합니다.<br/>
<br/>
개인적으로는 코틀린을 최대한 활용하고자, 업무용 스크립트도 코틀린으로 작성하는 편입니다. <br/>
그러기위해서는 결국 코틀린으로 빌드한 실행파일이 필요합니다.<br/>
일반적으로 코틀린으로 JVM 기반의 어플리케이션을 만들기 때문에<br/>
실행가능한 JAR 파일을 만드는 방법을 공유하고자 합니다. <br/>
<br/>

첫번째로 프로젝트를 생성해봅니다.<br/>
저는 안드로이드 개발자다보니 gradle이 익숙하여 **gradle Kotlin/JVM** 으로 프로젝트를 생성합니다. <br/>
<br/>

![project_create]({{ "/assets/img/post/20210731_02.png" | relative_url}})<br/>
![project_create]({{ "/assets/img/post/20210731_03.png" | relative_url}})

<br/><br/><br/>
프로젝트가 생성되었다면 아래와 같이 간단히 hello world 프로그램을 작성해봅니다.<br/>
<br/>

![hello_world]({{ "/assets/img/post/20210731_04.png" | relative_url}})<br/>

IDE에서 직접 실행하면 다음과 같이 잘 실행되는 것을 확인할 수 있습니다.<br/>

![hello_world!]({{ "/assets/img/post/20210731_05.png" | relative_url}})<br/>

그럼 이제 이 프로그램을 다른 곳에서도 실행할 수 있도록 jar 파일로 생성해봅시다.
jar 파일로 만드는 것은 우측 gradle 탭의 태스크에서도 실행 가능합니다.<br/>

![gralde_task_jar]({{ "/assets/img/post/20210731_06.png" | relative_url}})<br/>

참고로 jar 파일은 터미널에서 아래와 같이 입력하여 실행할 수 있습니다.<br/>

```bash
$ java -jar {jar 파일 이름}
```
<br/><br/><br/>

문제는...
jar 파일을 생성하고 실행하면 다음과 같이 나오고 제대로 실행되지 않습니다.😱😱😱<br/>

> `no main manifest attribute, in ExecutableJarExample-1.0-SNAPSHOT.jar`

<br/><br/>

이 문제는 메인클래스를 찾지 못해서 발생하는 문제로<br/>
아래와 같이 `build.gradle`파일에 manifest 설정을 jar 태스크에 추가해주면 됩니다. <br/>

```groovy
jar {
    manifest {
        attributes 'Main-Class': 'dev.haenara.sample.MainKt' // 메인 함수가 담긴 클래스 패키지명
    }
    archiveName 'HelloWrold.jar' // jar 파일 이름
}
```

혹시 gradle 버전이 낮거나 설정이 안맞으면 아래와 같이 에러가 발생할 수 있습니다.<br/>
저는 예전에 이 부분 때문에 상당히 삽질했던 기억이 나네요..😢<br/>

> `Caused by: java.lang.ClassNotFoundException: kotlin.jvm.internal.Intrinsics`

이 경우에는 jar 빌드 스크립트에 아래 내용을 추가합니다. <br/>

```groovy
jar {
    ...중략...
    from { configurations.default.collect { it.isDirectory() ? it : zipTree(it) } }
}
```

이것을 이용하여 안드로이드 프로젝트에 java/kotlin library 을 생성하면 <br/>안드로이드 스튜디오에서도 데스크탑용 앱 또는 jar 스크립트를 만들 수 있습니다.<br/>

![project_create]({{ "/assets/img/post/20210731_07.png" | relative_url}})

- gralde 세팅은 동일하게 구성하면 됩니다. 

이것을 이용한 안드로이드/PC 크로스플랫폼 개발도 가능한데, 이 것은 차후에 기회가 된다면 풀어보도록 하겠습니다.
<br/><br/><br/>

