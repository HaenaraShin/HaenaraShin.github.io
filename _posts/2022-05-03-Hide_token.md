---
layout: post
title:  "오픈소스 Android Project에서 민감한 정보 숨기기"
feature-img: "assets/img/post/20220317_thumb.jpeg"
thumbnail: "assets/img/post/20220317_thumb.jpeg"
description: "Android 프로젝트에서 중요한 토큰이나 API key 등 중요한 정보를 유출되지 않도록 숨기는 방법을 소개합니다."
date:   2022-05-03 21:49:00 +0900
categories: Android, Gradle
---

# 만약 내 API Key가 GitHub에 노출된다면..?!

![01]({{ "/assets/img/post/20220317_01.jpeg" | relative_url}})
<br/><br/>

GitHub에 오픈소스 안드로이드 프로젝트를 올릴 때 유의하실 점이 있습니다. <br/>
소스코드는 공개되더라도 비밀정보는 숨겨야한다는 점인데요. 🤫<br/>
잘못 공개될 경우 라이선스 위반이 되거나, 악용될 소지도 있고 심한 경우는 금전적 피해가 발생할 수 있습니다.<br/>
실제로 제 주변에서도 AWS 토큰이 잠깐 노출되었다가 약 2천만원 상당의 과금이 부과된 사례도 있었습니다. 😱<br/>

<br/><br/><br/>

### 어떤 정보를 숨겨야 할까? 🔐

Android 프로젝트에서 숨겨야 할 것이 있다면 **Sign Key와 비밀번호**와 **Token** 또는 **API Key** 등이 있습니다. <br/>
이번 포스팅에서는 이런 비밀정보를 오픈소스로 노출되지 않도록 프로젝트를 구성하는 방법을 알아봅니다.<br/>

> Firebase의 프로젝트 설정 파일 `google-service.json`처럼 
> 파일 통째로 숨겨야 한다면 아예 .gitignore로 숨기는게 좋습니다. 👨‍🚒

<br/><br/><br/>

# 🔑 Sign Key 숨기기 

상용 안드로이드 프로젝트라면 반드시 서명을 해야만 하는데요,<br/>
이 경우 key 자체와 서명할 때 사용하는 비밀번호 모두 숨겨야 합니다.<br/>
보통 `local.properties` 를 이용해서 숨기는 것이 일반 적인데요<br/>
실제로 [구글 공식문서](https://developer.android.com/studio/publish/app-signing#secure-shared-keystore)에서도 이런 방법을 추천하고 있습니다.<br/>
([https://developer.android.com/studio/publish/app-signing#secure-shared-keystore](https://developer.android.com/studio/publish/app-signing#secure-shared-keystore)) <br/>
<br/>
가끔 private repository라고 방심하여<br/>
프로젝트 내에 sign key를 포함하고 비밀번호를 하드코딩 하는 경우가 있는데<br/>
**만에 하나라도 노출**되면 돌이킬수 없는 사고로 이어질 수 있습니다. <br/>
<br/>
절대 노출 될 일이 없다구요?<br/>
<br/>
Github OAuth token으로 서드파티 어플리케이션 연동 중 실수로 프로젝트가 노출되는 사례도 있으므로<br/>
**절대로** 방심은 금물입니다. 😈😈😈<br/>

<br/><br/>

## Properties 설정

`local.properties`파일은 기본적으로 .gitignore에 등록되어 노출될 위험이 없습니다.<br/>
`local.properties`파일에 아래와 같이 `key=value`형태로 값을 할당합니다.<br/>

```properties
keystore=... 
key_alias=...
key_password=...
store_password=...
```

> keystore에는 sign key의 경로, 나머지에는 각각에 해당하는 서명 정보를 입력합니다.

<br/><br/>

## build.gradle

이제 `local.properties`파일로부터 필요한 정보들을 읽어올 차례 입니다.<br/>
`signingConfigs`에서 받아온 정보로 서명 정보를 구성하면 됩니다. <br/>

<br/>

#### groovy 인 경우

```groovy
android {
    ...중략...
    // 서명키 설정 --> local.properties 에서 서명키 정보 관리
    signingConfigs {
        Properties properties = new Properties()
        properties.load(new FileInputStream("$project.rootDir/local.properties"))
        properties.each { prop ->
            project.ext.set(prop.key, prop.value)
        }
        release {
          storeFile file(properties['keystore'])
          keyAlias properties['key_alias']
          keyPassword properties['key_password']
          storePassword properties['store_password']
        }
    }
}

```

<br/>

#### Kotlin-DSL 인 경우 

```kotlin
android {
    ...중략...
    // 서명키 설정 --> local.properties 에서 서명키 정보 관리
    signingConfigs {
        val properties = Properties().apply {
            load(FileInputStream("${rootDir}/local.properties"))
        }
        create("release") {
            storeFile = file("${properties["storeFile"]}")
            keyAlias = "${properties["keyAlias"]}"
            keyPassword = "${properties["keyPassword"]}"
            storePassword = "${properties["storePassword"]}"   
        }
    }
}
```

<br/><br/><br/>

# 🪙 API Key, Token 숨기기

만약 API 인증 등을 위해 필요한 토큰이나 API key를 사용하고 있다면 <br/>
빌드 시에 자동으로 생성되는 `BuildConfig.java` 파일에 상수로 추가하도록 할 수 있습니다.<br/>
특히 저는 AWS나 GitHub token, Open API key 를 숨길 때 주로 사용합니다. <br/>

<br/><br/>

## Properties 설정

위 서명키와 마찬가지로 여기서도 `local.properties`파일을 이용하여 값을 숨깁니다.<br/>
`local.properties`파일에 아래와 같이 `key=value`형태로 값을 할당합니다.<br/>

```properties
api_key=...
```

<br/><br/>

## build.gradle

#### groovy 인 경우

```groovy
android {
    ...중략...
    Properties properties = new Properties()
    properties.load(project.rootProject.file('local.properties').newDataInputStream())
    def apiKey = properties.getProperty('api_key') ?: ""

    defaultConfig {
        ...중략...
        buildConfigField "String", "API_KEY", "\"$apiKey\""
    }
}
```

<br/>

#### Kotlin-DSL 인 경우

```kotlin
android {
    ...중략...
    val properties = Properties().apply {
        load(FileInputStream("${rootDir}/local.properties"))
    }
    val apiKey = properties["api_key"] ?: ""

    defaultConfig {
        ...중략...
        buildConfigField("String", "API_KEY", "\"$apiKey\"")
    }
}

```

<br/>

## BuildConfig.java (자동생성)

build.gradle 설정 후 자동으로 생성되는 `BuildConfig.java` 파일에 아래와 같이 생성됩니다.<br/>만약 gradle sync를 해도 바뀌지 않는다면 **clean 후 assemble**을 다시 해보시면 됩니다. <br/>


```java
public final class BuildConfig {
  ...중략...
  // Field from default config.
  public static final String API_KEY = "...";
}
```

<br/><br/>

## 만약 String이 아니라면

가령 예를 들어 만약 정수라면 아래와 같이 작성하면 됩니다.
```groovy
buildConfigField "int", "API_KEY", "$apiKey"
```

만약 객체라면 아래와 같이 처리할 수 있습니다.<br/>
이 경우에는 따로 import를 할 수 없으므로<br/> 
클래스앞에 패키지명을 전부 붙여서 작성합니다.<br/>

```groovy
buildConfigField "com.sample.pacakge.Foo", "Config", "com.sample.package.Foo(\"$apiKey\")"
```


# 요약

레포지토리에서 공개되지 않는 `local.properties`에 민감한 값을 넣고 `build.gradle`에서 그 값에 접근하는 방식으로<br/>
GitHub에 값을 노출시키지 않고도 안전하게 그 값을 사용할 수 있습니다.<br/>
혹시 나의 레포지토리 중 민감한 정보가 노출되고 있지는 않은지 확인해보시고<br/>
숨겨야할 게 있다면 한번 반드시 정리해보시기를 바랍니다.<br/>

<br/><br/><br/>