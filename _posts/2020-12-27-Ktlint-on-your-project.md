---
layout: post
title:  "코틀린 코드를 깔끔하게 정리하는 비결 ~ ktlint"
feature-img: "assets/img/post/20201227_03.jpeg"
description: "ktlint를 적용하는 방법 한방 정리"
date:   2020-12-27 22:42:00 +0900
categories: Kotlin, Android, ktlint
---

> ktlint를 적용하는 방법 한방 정리

# `ktlint`란?

![ktlint]({{ "/assets/img/post/20201227_ktlint.png" | relative_url}})

Ktlint는 Kotlin을 위한 Lint로 코드의 coding convention, style guide 준수 여부를 검사해주는 정적 분석 툴입니다. <br/><br/>

ktlint를 적용하여 프로젝트 내의 코드 스타일을 통일하도록 **⚠️강제⚠️**할 수 있습니다.<br/><br/>
아래에서 설명하듯이 **git commit**시에 반드시 ktlint를 통과하도록 강제할 수 있어서<br/>
팀내에서 코드 스타일을 서로 맞추고 싶다면 ktlint 사용을 적극 추천드립니다.<br/><br/>

**💁‍♂️ 좀 더 자세한 소개는 아래의 ktlint 공식 페이지를 참고해보세요.**

- [Ktlint Page](https://ktlint.github.io/)
- [Ktlint Plugin GitHub Repository](https://github.com/jlleitschuh/ktlint-gradle)


## ktlint 도입의 장점

프로젝트 내에서 코드 스타일이 **강제로 통일**되기 때문에 <br/>
서로 다른 스타일로 작성될 일이 없고 모든 프로젝트 내의 코드가 통일성이 생깁니다.<br/><br/>

이를 통해 코드 리뷰 시에 **서로 다른 스타일로 인한 피로감**이 줄어들고<br/>
**가독성 저하** 또는 **불필요한 소통**의 비용이 줄어들게 됩니다.<br/><br/>

현재 제가 직장에서 담당하고 있는 안드로이드 프로젝트에서 ktlint를 적용하고 있는데<br/>
실제로 서로 코드 스타일이 비슷해지다보니 다른 개발자의 PR 코드리뷰 때 좀 더 눈에도 잘 들어오고<br/>
한줄에 너무 길게 쓴다던가 하는 일도 없어지고<br/>
누가 언제 짠 코드이던 비슷하게 생겨서 어느 장단에 맞춰야 할지 고민할 일이 없어져서 좋습니다.<br/>
`reformat code`를 습관적으로 하게 되는 것도 큰 장점인 것 같습니다.<br/><br/>

다만 초창기에 팀원들의 동의를 받아야하며, 적응기간이 아주 약간 있으니 이점은 유의해야 합니다.<br/><br/>

<br/><br/>

****

<br/><br/><br/>

# 🚀 ktlint 프로젝트에 적용 준비하기

![image]({{ "/assets/img/post/20201227_02.jpg" | relative_url}})<br/>
> 개발자가 깨끗하게 해야할 것은 키보드 뿐만이 아닙니다.

<br/>

프로젝트에 ktlint 적용하는 방법입니다.

<br/><br/>

## 1. build.gradle(root) 수정하기

- 최상단에 plugin `org.jlleitschuh.gradle.ktlint` 을 추가합니다.
- 레포지토리에 maven `https://plugins.gradle.org/m2/` 을 추가합니다.
- classpath 의존성에 `org.jlleitschuh.gradle:ktlint-gradle` 를 추가합니다.<br/>

> 작성일 기준 최신 버전은 9.4.1 입니다. 
> 예제에서는 9.3.0을 사용했지만 어느 버전을 사용해도 무관합니다. 

<br/>

다 적용한다면 아마 아래와 같은 모양이 될 겁니다.

```groovy
apply plugin: 'org.jlleitschuh.gradle.ktlint'

buildscript {

...중략...

    repositories {
        maven {
            url "https://plugins.gradle.org/m2/"
        }
    }

...중략...

    dependencies {
        classpath "org.jlleitschuh.gradle:ktlint-gradle:9.3.0"
    }
}

```

- 💁‍♂️  위에만 봐서는 이해가 잘 안된다면 [샘플 코드](https://github.com/HaenaraShin/Ktlint-Android-Sample/blob/main/build.gradle)를 참고해보세요. 

<br/><br/><br/>

## 2. build.gradle(android module) 수정하기

**안드로이드 app 모듈**에 추가하는 방법입니다. <br/>

- 최상단에 plugin `org.jlleitschuh.gradle.ktlint` 를 추가합니다.
- ktlint 설정값을 추가합니다.

> 예제에서 설정값은 가장 무난한 설정값으로 했습니다. 
> 공식페이지 설명을 보고 프로젝트에 맞게 설정을 수정해보세요.

<br/><br/>

다 적용한다면 아마 아래와 같은 모양이 될 겁니다.

<br/>

```groovy
plugins {
    id 'org.jlleitschuh.gradle.ktlint'
}

... 중략 ...

ktlint {
    android = true
    outputColorName ="RED"
    verbose = true
    disabledRules.addAll("import-ordering")
}
```

- 💁‍♂️  위에만 봐서는 이해가 잘 안된다면 [샘플 코드](https://github.com/HaenaraShin/Ktlint-Android-Sample/blob/main/app/build.gradle)를 참고해보세요. 

<br/><br/><br/>

## 3. build.gradle(non-android module) 수정하기

**순수 Java/Koltin(JAR) 모듈**에 추가하는 방법입니다. <br/>

- 최상단에 plugin `org.jlleitschuh.gradle.ktlint` 를 추가합니다.
- ktlint 설정값을 추가합니다.

> 안드로이드 모듈과 다 동일한데 `android = false`인 것만 유의해주세요.

<br/><br/>

```groovy
plugins {
    id 'org.jlleitschuh.gradle.ktlint'
}
...
ktlint {
    android = false
    outputColorName ="RED"
    verbose = true
    disabledRules.addAll("import-ordering")
}
```

- 💁‍♂️  위에만 봐서는 이해가 잘 안된다면 [샘플 코드](https://github.com/HaenaraShin/Ktlint-Android-Sample/blob/main/lib/build.gradle)를 참고해보세요. 

<br/><br/>

이제 적용이 다 되었다면 실제로 ktlint를 한번 써 봅시다!

<br/><br/><br/><br/>

************
# 🛠 ktlint 적용하기

![image]({{ "/assets/img/post/20201227_01.jpeg" | relative_url}})<br/>
> 코드를 청소할 시간입니다.

<br/><br/>

## 1. 수동으로 Ktlint 체크하기 🔍 

ktlint를 적용하게 되면 수시로 체크하게 되는데요<br/>
언제든지 아래와 같이 수동으로 코딩스타일을 준수하고 있는 지 확인할 수 있습니다.<br/><br/>

Android Studio의 Gradle 탭에서 다음 Task를 실행합니다.<br/>

-  MyProject > Tasks > verification > ktlintCheck

![image]({{ "/assets/img/post/20201227_check.png" | relative_url}})<br/>

또는 콘솔에서 아래와 같이 입력합니다.<br/>

```shell
./gradlew ktlintCheck
```

<br/><br/>

만약 고쳐야 할 것이 있다면 아래와 같이 나옵니다.<br/>

![image]({{ "/assets/img/post/20201227_sample1.png" | relative_url}})<br/><br/>

나름 친절하게 어느 코드 라인에서 틀렸는지 알려주며,<br/>
그 옆에 괄호로 왜 틀렸는지도 알려줍니다.<br/><br/>

다시 다 수정하고 고칠 것이 없다면 아래와 같이 통과하여 Success 합니다.<br/>

![image]({{ "/assets/img/post/20201227_sample2.png" | relative_url}})<br/>

<br/><br/><br/>

## 2. Commit시 Ktlint 체크 적용하기 (Git hook) 🎣

매번 수동으로 체크할수는 없겠죠?<br/>
아래와 같이 git-hook에 적용하고 나면 commit 할 때마다 ktlint를 체크하여<br/>
스타일을 준수하지 않았다면 커밋이 실패하게 됩니다.<br/><br/>

Android Studio의 Gradle 탭에서 다음 Task를 실행합니다.<br/>

- MyProject > Tasks > help > addKtlintCheckGitPreCommitHook

![image]({{ "/assets/img/post/20201227_hook.png" | relative_url}})<br/>

또는 콘솔에서 아래와 같이 입력합니다.<br/>

```shell
./gradlew addKtlintCheckGitPreCommitHook
```

<br/><br/>

성공적으로 적용되었다면 **어디에서 commit**을 하더라도 <br/>
반드시 ktlint가 통과되어야 commit이 됩니다.<br/>

![image]({{ "/assets/img/post/20201227_error.png" | relative_url}})
> 소스트리에서 commit 해도 마찬가지로 ktlint가 통과되어야만 합니다.

<br/><br/><br/>

#### ⚠️ 잠깐! 혹시 에러가 나진 않나요?

> Execution failed for task ':addKtlintCheckGitPreCommitHook'.
> java.io.IOException: No such file or directory

만약 위와 에러가 발생한다면 .git 디렉토리 하위에 hooks 가 없어서 발생하는 것이니 hooks 디렉토리를 생성합니다. 

```shell
mkdir .git/hooks
```

<br/><br/><br/><br/>

## 3. Android Studio formatter를 Ktlint에 맞게 설정 하기 🛠

이제부터는 수시로 `reformat code`를 호출하게 될 텐데, <br/>
안드로이드 스튜디오가 kotlin convention을 따르지 않아서 <br/>
오히려 반대로 자꾸 엉뚱한 스타일로 바꿔버리면 곤란하겠죠?<br/>
아래 방법대로 안드로이드 스튜디오의 코틀린 포맷을 ktlint와 동일하게 맞춰봅시다.<br/><br/>

Android Studio의 Gradle 탭에서 다음 Task를 실행합니다.<br/>

- ClassNote > Tasks > help > ktlintApplyToIdea

![image]({{ "/assets/img/post/20201227_apply.png" | relative_url}})<br/>

또는 콘솔에서 아래와 같이 입력합니다.<br/>

```shell
./gradlew ktlintApplyToIdea
```

<br/><br/><br/><br/>

# 주로 틀리는 것들 📌

주로 틀리는 것들이 있는데, 이 부분은 Android Studio 설정을 조금만 바꿔주면 해결됩니다.

<br/><br/>

## 1. 파일의 끝은 new-line 으로 끝나야 합니다.

파일의 끝은 반드시 **줄내림으로 끝내야 합니다.**<br/><br/>

그 이유는 콘솔에서 diff나 git 등으로 코드를 보거나 할 때 줄내림이 없으면<br/>
마지막 줄이 콘솔에서는 한줄로 보여서 문제가 되기 때문이라고 합니다.<br/><br/>

**Preferences > Editor > General** 페이지의 `Save Files` 항목에서<br/>

`Ensure an empty line at the end of a file on save`를 체크하세요.<br/><br/>

![image]({{ "/assets/img/post/20201227_eof.png" | relative_url}})<br/>

<br/><br/><br/>
## 2. Wild-card imports 

**Preferences > Editor > Code Style > Kotlin > Imports** 항목에서<br/>

`Top-level symbols`와 `Java Statics and Enum Members` 를 모두<br/>

`Use Single name import`로 고쳐주시면 됩니다.<br/>

![image]({{ "/assets/img/post/20201227_import.png" | relative_url}})<br/>

<br/><br/><br/>

## 3. Continuation indent (이어지는 들여쓰기)

Android Studio에서 줄이 이어지는 경우 들여쓰기가 8칸이 기본값이기 때문에 수정이 필요합니다. <br/>

**Preferences > Editor > Code Style > Kotlin > Tabs and Indents** 항목에서<br/>

`Continuation indent` 항목을 **4로 수정해주세요.**<br/>

![image]({{ "/assets/img/post/20201227_ind.png" | relative_url}})<br/>

<br/><br/><br/>