---
layout: post
title:  "Ktor는 Retrofit을 대신할 수 있을까?"
feature-img: "assets/img/post/20210321_thumb.jpeg"
thumbnail: "assets/img/post/20210321_thumb.jpeg"
description: "Ktor을 소개하고 앞으로의 가능성을 예측해봅니다."
date:   2021-3-21 12:42:00 +0900
categories: Android, Kotlin, Ktor
---

# Ktor가 뭔데?

![Ktor]({{ "/assets/img/post/20210321_ktor_title.png" | relative_url}})
> 공식 페이지 https://ktor.io/

<br/>

**Ktor**는 Kotlin을 만든 **Jetbrains**사에서 만든 오픈소스 코틀린 통신 프레임워크 입니다. <br/>
아무래도 코틀린을 만든 회사가 만들었기 때문에 공식 라이브러리로서의 기대감을 받고 있습니다. <br/>
이 글에서는 서버보다는 주로 클라이언트 관점에서 Ktor에 대해 분석해봅니다.<br/>
<br/>

💁‍♂️ 참고로 `Kay-tor` 라고 발음합니다. 대략 `케이토어` 정도로 발음하면 되겠네요. ([출처](https://ktor.io/docs/faq.html#pronounce))<br/>


## Ktor의 특징


### 1. 서버와 클라이언트를 한방에

가장 큰 특징은 서버와 클라이언트 모두 지원한다는 점입니다. <br/>
그래서 사실 클라이언트 보다는 서버 구축에 초점이 맞춰져있는 느낌도 있긴 하지만<br/>
하나의 프로젝트에서 하나의 프레임워크로 서버/클라이언트를 모두 개발할 수 있습니다.<br/>

### 2. 멀티플랫폼 지원

순수한 코틀린으로만 구현되어 있다보니 코틀린 멀티플랫폼을 지원합니다. <br/>
즉 웹서버, 안드로이드, iOS까지 하나의 프레임워크로 해결할 수 있다는 의미입니다.<br/>
결과적으로 위의 서버/클라이언트를 모두 지원하는 장점과 이어지는 것 같아요. <br/>
기존에 자바진영에서 많이 사용하던 다른 통신모듈과 가장 큰 차별점이 이부분이라고 생각합니다.<br/>

### 3. 공식 라이브러리

[코틀린 공식 페이지](https://kotlinlang.org/lp/server-side/)에서도 소개가 되고 있듯이, 공식적으로 미는 프레임워크입니다.<br/>
바꿔말하면 지금은 조금 부족하더라도 앞으로도 꾸준히 지원될 것을 기대해볼 수 있습니다.<br/>
또한 IntelliJ 플러그인 등을 통해 좀 더 손쉽게 개발할 수 있도록 여러가지 지원을 하고 있습니다.<br/>

<br/><br/><br/>

# 샘플 프로젝트

![Sample_run]({{ "/assets/img/post/20210321_run.jpeg" | relative_url}})
<br/>
- 샘플 프로젝트 링크 : https://github.com/HaenaraShin/Ktor-sample
<br/>

일단은 한번 써봐야 이게 좋은지 않좋은지 알 수 있겠죠?<br/>
서버 구성 코드도 함께 있다면 좋겠지만.. 이 글에서는 Android 기준으로 작성합니다.<br/>
예제 코드에서는 core 모듈을 분리하여 작성하였기 때문에 <br/>
안드로이드를 모르시더라도 순수 Kotlin(JVM)으로 구현하는 코드를 참고하실 수 있습니다.<br/>
<br/>

> 샘플 작성하면서 Kotlin, Ktor 버전에 따라서 동작차이가 컸습니다.
> 혹시 따라해보시다가 동작이 이상하다면 반드시 버전을 꼭 확인해주세요.

<br/>
해당 샘플은 아래 버전 기준으로 작성되었습니다.
- AndroidStudio 4.1.3
- Kotlin 버전 1.4.30
- Ktor 버전 1.5.2

샘플에서 사용하는 API는 🐈 랜덤한 고양이 이미지 URL을 제공하는 [TheCatApi](https://thecatapi.com/)입니다.<br/>
API의 자세한 사양은 API 공식 문서를 확인해주세요.<br/>

<br/>

## 의존성 추가 

root 수준의 `build.gralde` 에 다음과 같이 의존성을 추가해주세요.

```groovy
buildscript {
    ...
    dependencies {
        ...
        classpath 'org.jetbrains.kotlin:kotlin-gradle-plugin:1.4.30'
        classpath 'org.jetbrains.kotlin:kotlin-serialization:1.4.30'
    }
}
```

app 수준의 `build.gralde` 에 다음과 같이 플러그인과 의존성을 모두 추가해주세요.

```groovy
plugins {
    ...
    id 'org.jetbrains.kotlin.plugin.serialization'
}

dependencies {
    ...
    implementation 'io.ktor:ktor-client-core:1.5.2'
    implementation 'io.ktor:ktor-client-android:1.5.2'
    implementation 'io.ktor:ktor-client-serialization:1.5.2'
    implementation 'io.ktor:ktor-client-serialization-jvm:1.5.2'
    implementation 'org.jetbrains.kotlinx:kotlinx-serialization-json:1.1.0'
}
```

## 데이터 모델객체 추가

Cat 데이터 응답은 다음과 같은 형태로 옵니다. (불필요한 내용은 생략했습니다.)

```json
[
   {
      "id":"36e",
      "url":"https://cdn2.thecatapi.com/images/36e.jpg",
      "width":1280,
      "height":853
   }
]
```

이것을 Kotlin Data 클래스로 바꾸면 아래와 같이 됩니다.

```kotlin
import kotlinx.serialization.Serializable

@Serializable
data class Cat(
    val height: Int,
    val id: String,
    val url: String,
    val width: Int
)
```

해당 예제에서는 직렬화를 사용하기 위해 `@Serializable` 어노테이션을 사용했습니다.<br/>

#### ⚠️ 혹시 에러가 나진 않나요?

Serializable 어노테이션이 제대로 인식되지 않거나 에러로 인식되어 빨간 밑줄이 뜨는 경우가 있습니다.<br/>
그러나 이 경우에도 사실 빌드를 해보면 잘 됩니다. 😱<br/>
AndroidStudio 4.2.0 이상을 사용하면 정상적으로 해결됩니다만<br/>
이런 것이 신경쓰이는 분이 아니시라면 4.2.0은 아직 베타버전이므로 굳이 설치하기를 권장드리지는 않습니다. <br/>

## API 호출하기

제일 먼저 HttpClient를 생성해봅니다.

```kotlin
val client = HttpClient() 
```

### 통신 엔진

여기서 중요한 특징은 HttpClient 안에 생성자안에 통신 엔진을 명시할 수 있습니다.<br/>
<br/>
만약 적지 않으면 기본 엔진으로 동작하며, 플랫폼에 따라 다른 엔진을 적용하는 것도 가능합니다.<br/>
OkHttp 엔진을 사용하여 기존에 사용하던 Interceptor 등을 연동하는 등<br/>
호환성을 위해 여러가지를 고려한 것이 눈에 띕니다.<br/>
<br/>
엔진에 대해 좀 더 자세히 알고 싶다면 [공식문서](https://ktor.io/docs/http-client-engines.html)를 참고해보세요.<br/>

### 기능(Feature) 설정하기

단순히 통신만 하는 것이 아니라 인증, Json직렬화 등 다양한 기능을 지원합니다.<br/>
이러한 기능을 적용하려면 HttpClient에 Feature를 설치해야 합니다.<br/>
예제에서는 Json직렬화를 사용해보았습니다.<br/>
<br/>

기능에 대해서 좀 더 자세히 알고 싶다면 [공식문서](https://ktor.io/docs/http-client-features.html)를 참고해보세요.<br/>


```kotlin
val client = HttpClient() {
    install(JsonFeature) {
        serializer = KotlinxSerializer(
            Json {
                prettyPrint = true
                ignoreUnknownKeys = true
                isLenient = true
                encodeDefaults = false
            }
        )
    }
}
```

### 진짜로 API 호출해보기

`HttpClient`를 생성하고 `get()` 함수로 호출해봅시다.<br/>
지금 API는 **HTTP GET메소드**라서 `get()`이고 <br/>
만약 **POST**로 호출해야 한다면 `post()`를 쓰시면 됩니다. <br/>

```kotlin
client.get<List<Cat>>("https://api.thecatapi.com/v1/images/search")
```

해당 예제에서는 직렬화를 사용했기 때문에 바로 `Cat` 모델로 받을 수 있습니다.<br/>

#### ⚠️ 혹시 에러가 나진 않나요?

`get()` 함수는 코루틴의 `suspended` 함수이므로 상위 함수도 `suspended` 지정자를 붙여주어야 합니다.<br/>


## 안드로이드에 적용해보기

아까 생성한 API 호출코드와 받아온 이미지를 Glide로 보여줍니다.

```kotlin
suspend fun findCat() {
    val cats = MyClass().getCatFromApi()
    showUrl(cats.first())
}

fun showUrl(cat: Cat) {
    CoroutineScope(Main).launch {
        Glide.with(this@MainActivity)
            .load(cat.url)
            .into(imageView)
    }
}
```

마지막으로 버튼을 누르면 코루틴 함수를 호출할 수 있도록 합니다.
```kotlin
button.setOnClickListener {
    CoroutineScope(IO).launch {
        findCat()
    }
}
```

## 예제 실행 결과

![sample_run]({{ "/assets/img/post/20210321_samplerun.gif" | relative_url}})

어떤가요? 😊



# 써보니까 어때? Retrofit 보다 좋아?

![likeit]({{ "/assets/img/post/20210321_like.png" | relative_url}})
> Ktor 써보니까 좋아? Ktor가 Retrofit보다 좋아??

🤔 솔직히 더 좋다고 말씀드리기는 어려운 것 같아요.<br/>
멀티플랫폼을 고려하는 것이 아니라면 안드로이드에선 아직 Retrofit이 나을 것 같습니다.<br/>
그렇게 생각하는 이유는 다음과 같습니다.<br/>

### 1. 참고할 자료가 너무 적다

공식문서가 잘 되어있긴 한데.. 공식문서 외에는 예제나 자료를 찾는게 너무 어려웠습니다.<br/>
공식문서에 나와있지 않은 내용은 찾기가 힘들었고 StackOverflow 조차도 참고할 것이 많지 않았습니다.<br/>
특히나 자료가 클라이언트 사이드보단 서버사이드가 압도적으로 많았고<br/>
예외가 많을 수 밖에 없는 통신 모듈에서 참고할 사례와 자료가 적다는 것은 프로덕션에 적용하기에 큰 리스크라고 생각됩니다.<br/>

### 2. 코루틴을 써야 한다.

사실 당연한 점이라서 이건 단점이라고 하기도 좀 그렇습니다만<br/>
개인적으로는 RxJava로 적용된 프로젝트를 마이그레이션 하는 것을 고려했었기 때문에 아쉬운 점으로 꼽았습니다.<br/>
코루틴이 적용된 것은 분명 장점이긴 하지만 RxJava와 같은 다른 프레임워크를 사용할 수 없다는 한계가 있었습니다.<br/>
물론 멀티플랫폼이기 때문에 어쩔 수 없는 점은 이해하지만 한편으로는 아쉬운 점이었습니다.<br/>
Retrofit이 Callback, RxJava, 코루틴 모두 적용할 수 있다는 점에서 상대적으로 아쉬운 점입니다.<br/>

### 3. 코틀린 버전에 민감함

샘플작성하면서 사실 버전때문에 굉장히 많은 삽질을 했습니다.<br/>
결과적으로는 Kotlin, Ktor, Android Studio 셋의 버전을 모두 최신으로 올려서 해결했지만<br/>
중간에 어노테이션이 에러로 뜨지만 빌드는 된다던가 하는 경우 등 난해한 문제들을 겪었습니다.<br/>

# 요약하자면...

위 단점을 모두 모아 한줄로 써보자면 '아직은 아쉽다' 정도가 되겠네요.<br/>
세가지 단점 모두 시간이 지나면 해결될 문제들로 생각됩니다. <br/>
<br/>
즉 쓸만해보이고 앞으로 더욱 쓸만해보이지만<br/>
아직까지는 (특히나 클라이언트 사이드에서는) 좀 성숙도가 떨어지지 않나?🤔 하는 생각이 듭니다.<br/>
공식적으로 미는 프레임워크인 만큼 앞으로의 기대가 크지만<br/>
당장 상용 프로젝트에 적용하기에는 이른 감이 있다고 생각합니다.<br/>
