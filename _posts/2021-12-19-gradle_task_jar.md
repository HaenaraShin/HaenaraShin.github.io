---
layout: post
title:  "Android 프로젝트에 task로 유틸 프로그램 실행하기"
feature-img: "assets/img/post/20211219_thumb.jpeg"
thumbnail: "assets/img/post/20211219_thumb.jpeg"
description: "Android 프로젝트에서 Gradle task로 유틸 JAR파일 실행하여 업무 자동화 하는 방법을 소개합니다."
date:   2021-12-05 21:00:00 +0900
categories: Kotlin
---


# Jar 파일을 Gradle task로 실행하기

![GRADLE]({{ "/assets/img/post/20211219_01.jpeg" | relative_url}})<br/>


## Jar 파일을 Gradle task로?
유틸프로그램 또는 스크립트를 Java 또는 Kotlin으로 작성해서
간단한 업무 자동화를 적용해보려는 시도를 해보신 적이 있으신가요?
Kotlin DSL을 활용한다면 물론 jar 파일 없이도 코틀린을 gradle에서 실행할 수 있지만
굳이 그러지 않더라도 별도의 jar파일로 프로젝트에 추가하여 gradle에서 task로 실행할 수 있습니다.

## 어떤 식으로 활용할 수 있나요?
저는 Google Sheet에 정리된 다국어 목록을 안드로이드 리소스 파일로 변환해주는 유틸 프로그램과
프로젝트에서 자주 사용하는 코드 템플릿을 한꺼번에 자동으로 생성해주는 유틸 프로그램을 코틀린으로 작성하여
해당 스크립트를 jar 파일로 실행하여 사용하고 있습니다.
jar파일 생성하는 법은 지난 포스팅 [Kotlin으로 실행 가능한 JAR 파일 만들기](https://haenarashin.github.io/kotlin/2021/07/31/Excutable-jar.html)을 참고해주세요.

## Task 추가하기

### Groovy인 경우 (build.gradle)
```groovy
task sample_task(type: JavaExec) {
    group = "_sample_"
    description = "샘플 유틸 태스크"
    classpath = files("$rootDir/Util.jar")
    main = 'dev.haenara.util.MainKt'
    args = ['arg1', 'arg2']
}
```
### Kotlin DSL 인 경우 (build.gradle.kts)
```kotlin
tasks.register("sample_task", JavaExec::class) {
    group = "_sample_"
    description = "샘플 유틸 태스크"
    classpath = files("$rootDir/Util.jar")
    main = "dev.haenara.util.MainKt"
    args = listOf("arg1", "arg2")
}
```

> 위와 같이 
만약에 Path에 `/`를 넣어서 윈도우에서 동작하지 않는다면 아래와 같이 path를 사용하여 해결한다.
단 함수위에 Experimental 어노테이션을 추가해야한다.

```kotlin
@OptIn(ExperimentalPathApi::class)
tasks.register("sample_task", JavaExec::class) {
    ...
    classpath = files(path(rootDir) { this / "Util.jar" })
    ...
}
```


## 예제 실행

![change]({{ "/assets/img/post/20211219_02.png" | relative_url}})<br/>

gradle 탭에 다음과 같이 Task가 생성된 것을 확인할 수 있습니다.
