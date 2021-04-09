---
layout: post
title:  "AndroidStudio에서 Firebase 앱 내부배포를 한방에 해결하기"
feature-img: "assets/img/post/20210327_thumb.jpeg"
thumbnail: "assets/img/post/20210327_thumb.jpeg"
description: "안드로이스 스튜디오에서 Firebase 내부배포 플러그인과 git 커맨드를 이용한 배포 프로세스를 간략화 하는 방법을 소개합니다."
date:   2021-3-27 14:11:00 +0900
categories: Android,Firebase
---

# Firebase 앱 내부배포?

![Firebase]({{ "/assets/img/post/20210327_firebase.png" | relative_url}})

Firebase에서는 개발자를 위한 정말 다양한 서비스를 제공하고 있는데 오늘 소개할 서비스는 **Firebase 앱 배포** 입니다.  <br/>
**Firebase 앱 배포**를 통해서 스토어에 올리지 않고 팀원들에게만 공유하여 다운받을 수 있도록 배포할 수 있습니다. <br/>
새로운 버전을 출시할 때 마다 테스터느 손쉽게 다운 받아서 테스트 해볼 수 있으며  <br/>
버전별로 테스트할 수 있도록 이전 버전의 다운로드도 지원합니다. <br/>
<br/>

자세한 소개는 [공식문서](https://firebase.google.com/docs/app-distribution)를 참고해보세요.<br/>
<br/>
## 내부배포를 Android Studio 에서?

보통은 `Firebase Console`을 웹으로 접속하여 APK파일을 직접 업로드하여 배포하지만 <br/>
**Android Studio**에서도 Firebase에서 app-distribute 플러그인을 제공하여 아주 손쉽게 배포할 수 있습니다.<br/>
사실 CLI, Fastlane 등을 통해서도 배포할 수 있지만 <br/>
이번 포스팅에서는 Gradle 플러그인을 이용한 방법을 소개합니다.<br/>
<br/>

자세한 내용은 [공식문서](https://firebase.google.com/docs/app-distribution/android/distribute-gradle)를 참조하세요.<br/>
<br/><br/>

### 의존성 추가 및 플러그인 적용

우선 플러그인 사용을 위해 build.gradle(root)에 다음과 같이 추가합니다. <br/>

```groovy
buildscript {
    repositories {
        google()
    }
    dependencies {
        classpath 'com.google.firebase:firebase-appdistribution-gradle:2.1.0'
    }
}
```

build.gradle(app)에는 다음과 같이 플러그인을 적용합니다. <br/>

```groovy
plugins {
    id 'com.google.firebase.appdistribution'
}
```

<br/><br/><br/>

### 빌드 타입에 Firebase 배포 설정

플러그인 적용이 완료되었다면 이제 출시노트와 대상 테스터를 설정합니다.<br/>
`buildTypes` 또는 `ProductFlavor`에 `firebaseAppDistribution` 설정을 추가합니다.<br/>

출시노트와 테스터는 String 객체로 직접 입력할 수도 있고 파일을 참조하도록 할 수도 있습니다.<br/>
테스터의 경우는 "," 단위로 여러명을 지정할 수도 있고 아예 그룹이름 단위로 선택할 수도 있습니다.<br/>
아래 예시에서는 debug 빌드일 때 프로젝트 내의 `RELEASE_NOTE.txt`파일을 참조하도록 하였습니다.<br/>

```groovy
android {
    ...
    buildTypes {
        ...
        debug {
            ...
            firebaseAppDistribution {
                releaseNotesFile = "${rootDir}/RELEASE_NOTE.txt"
                testers = "qatester@test.moc"
            }
        }
    }
}
```

좀 더 자세한 설정 및 활용방법은 [공식문서](https://firebase.google.com/docs/app-distribution/android/distribute-gradle)를 참조하세요.<br/>

<br/><br/><br/><br/>

### 배포 해보기

![launch]({{ "/assets/img/post/20210327_01.jpeg" | relative_url}})

실제로 배포를 해봐야 동작하는 지 알 수 있겠죠?<br/>
우선 배포를 하기 전에 인증 부터 해야하는데요 [인증 방법](https://firebase.google.com/docs/app-distribution/android/distribute-gradle#step_2_authenticate_with_firebase)은 세가지가 있습니다.<br/>

1. 플러그인의 로그인 작업을 통해 Google 계정에 로그인
2. Firebase 서비스 계정 사용자 인증 정보 사용
3. Firebase CLI를 사용하여 로그인

인증하는 방법이 몇가지 있겠지만 커맨라인이 익숙하신 분들이라면 CLI를 추천드립니다.<br/>
나머지 인증 방법이 궁금하시다면 [공식문서](https://firebase.google.com/docs/app-distribution/android/distribute-gradle#step_2_authenticate_with_firebase)를 참조하세요.<br/><br/><br/>

#### CLI로 인증하기

CLI 인증을 위해선 Firebase CLI 를 다운로드 받아야 합니다.<br/>
콘솔창에 아래와 같이 입력하면 쉽게 다운 받을 수 있습니다.<br/>

```shell
$ curl -sL https://firebase.tools | bash
```

설치가 완료되었다면 다음 명령어를 입력하고 브라우저에서 인증 합니다.<br/>

```shell
$ firebase login
```

브라우저에서 인증을 완료하면 다음과 같은 화면을 확인할 수 있습니다.<br/>

![auth]({{ "/assets/img/post/20210327_auth.png" | relative_url}})

<br/><br/><br/>

#### 이젠 정말 배포 해보기

플러그인이 정상적으로 설정되었다면 gradle task 목록에 다음과 같이 태스크가 추가 되어 있는 것을 확인할 수 있습니다.<br/>

![plugin]({{ "/assets/img/post/20210327_plugin.png" | relative_url}})

해당 태스크를 실행하여 실제로 파이어베이스 콘솔에 앱이 잘 배포되는지 확인합니다.<br/>

<br/><br/><br/><br/><br/>


## 🤔 그런데 결국 출시노트는 일일히 적어야 하는거 아냐?

저는 이쯤에서 이런 고민을 했습니다.<br/>
<br/>
😠 *결국 출시노트는 여전히 손으로 다 적어야 하네*<br/>
😣 *버전도 매번 일일히 올려야 하는 거잖아*<br/>
😩 *그냥 웹에서 하던걸 안드로이드 스튜디오로 가져온게 다라면 그게 무슨 의미가 있을까?*<br/>
<br/>
플러그인을 적용하면 분명 콘솔에 접속 할 필요도, APK를 직접 업로드 할 필요도 없어서 간편한 것은 사실이지만<br/>
아쉬운 점은 **출시노트를 매번 일일히 수동**으로 적고 **버전도 매번 하나씩 올려주어야 한다**는 것입니다.<br/><br/>

하지만 기왕 자동화를 시작할꺼면 끝까지 해야겠지요? 🤩<br/>
결과적으로 저는 버전관리와 출시노트를 자동으로 생성하는 것 까지 한세트로 묶어 자동화를 시도합니다.<br/>
<br/><br/>

# 버전 관리 배포하기

![auto]({{ "/assets/img/post/20210327_03.jpeg" | relative_url}})
<br/>

Git 명령어를 이용해서 출시노트를 자동으로 생성하고 버전 역시 자동으로 하나씩 올리는 방법을 소개합니다.<br/><br/>
## 버전을 별도의 파일로 관리하기

우선 버전을 `version.properties`라는 별도 파일로 생성하여 관리해봅시다.<br/>
별도의 파일로 관리하는 이유는 gradle에서 손쉽게 버전을 읽고 덮어씌워 저장하기 위해서입니다.<br/>
일단 프로젝트에 `version.properties`파일을 다음과 같이 생성해주세요.<br/>

```
version_name=1.0.0
version_code=1
```

이후 해당 프로퍼티 파일을 읽어서 버전네임과 코드를 사용하도록 `build.gradle(app)`에 다음과 같이 코드를 추가합니다.<br/>

```groovy
def versionProp = {
    def versionProp = new Properties()
    versionProp.load(new FileInputStream("$project.rootDir/version.properties"))
    versionProp.each { prop ->
        project.ext.set(prop.key, prop.value)
    }
    return versionProp
}

def verCode = {
    def prop = versionProp()
    return Integer.valueOf("${prop['version_code']}")
}

def verName = {
    def prop = versionProp()
    return "${prop['version_name']}"
}

android {
    ...

    defaultConfig {
        versionCode verCode()
        versionName verName()
        ...
    }
}
```

`version.properties`의 버전을 하나씩 올리고 새로 저장하는 함수는 아래와 같습니다.<br/>
해당 함수도 `build.gradle(app)`에 추가해주세요.<br/>

```groovy
def updateVersionProperties(name, code) {
    def versionProp = new File("${rootDir}", 'version.properties')
    def newVersion = name.substring(0, name.lastIndexOf('.')) +
            ".${Integer.valueOf(name.split('\\.').last()) + 1}"
    versionProp.text = "version_name=${newVersion}\n" +
            "version_code=${code + 1}"
}
```

<br/><br/><br/>

## Git Log로 출시노트 만들기

이제 출시노트를 자동생성해볼 차례입니다.<br/>
Git Log를 이용하여 그간 작업한 내용을 출시노트로 변환하는 작업을 할 겁니다.<br/>
<br/>
일단 마지막 태그부터 최신까지의 커밋 메세지를 출력하는 git 명령어는 다음과 같습니다.<br/>

```shell
git log $(git describe --tags --abbrev=0)..HEAD --oneline --format="%s"
```

이제 이 명령어를 gradle에서 실행하면 되는데...<br/>
문제는 `$(git describe --tags --abbrev=0)`와 같이 명령어 안의 명령어를 한번에 실행할 수가 없다는 점입니다.<br/>
<br/>
따라서 <br/>

#### 1. 마지막 태그 버전 가져오기

#### 2. 특정 버전부터 최신까지의 로그 출력하기 

이렇게 두가지 커맨드를 각각 함수로 호출해주고 출력한 내용을 `RELEASE_NOTE.txt` 파일로 저장해야 합니다.<br/>
아래의 함수가 두가지 동작을 처리하는 함수인데 이 역시 `build.gradle(app)`에 추가합니다.<br/><br/>

```groovy
def createReleaseNote() {
    def lastTag = {
        def stdout = new ByteArrayOutputStream()
        exec {
            commandLine 'git', 'describe', '--tags', '--abbrev=0'
            standardOutput = stdout
        }
        return stdout.toString().trim()
    }

    def stdout = new ByteArrayOutputStream()
    exec {
        commandLine 'git', 'log', "${lastTag()}..HEAD", '--oneline', '--format=%s'
        standardOutput = stdout
    }
    def text = stdout.toString().trim()
    new File("${rootDir}", "RELEASE_NOTE.txt").text = text
    return text
}
```

참고로 `--format=%s` 는 커밋 메세지만 출력하도록 하는 포맷인데 <br/>
%h를 추가하면 커밋 리비전도 함께 출력할 수 있고 필요에 따라 포맷을 원하는대로 수정해서 쓸 수 있습니다.<br/>
<br/>

> RELEASE_NOTE.txt 파일은 .gitignore 파일에 추가해서 커밋에 추가되지 않도록 하는 것을 권장합니다.

<br/><br/>

## 버전별 Git Tag 남기기

이제 버전 네임과 코드가 올라간 것을 다른 팀원들과도 Commit으로 공유하고 출시를 tag 로 남겨봅시다.<br/>
아래 함수도 역시 `build.gradle(app)`에 추가합니다.<br/>

```groovy
def gitCommitAndTagVersion(releaseNote, verName) {
    try {
        exec { commandLine 'git', 'reset', 'HEAD' }
        exec { commandLine 'git', 'add', "${rootDir}/version.properties" }
        exec { commandLine 'git', 'commit', '-m', "v${verName} is released\n\n${releaseNote}" }
        exec { commandLine 'git', 'tag', "v${verName}" }
    }
    catch (Exception e) {
        e.printStackTrace()
    }
}
```

<br/>

> 혹시 해당 기능이 동작 하지 않는다면 터미널이나 커맨드 콘솔에서 git 동작하는지 우선 확인해야 합니다. <br/>
> 윈도우의 환경변수나 맥/리눅스의 PATH에 git 경로를 추가해주시면 해결할 수 있습니다.

<br/><br/>

## 빌드 및 배포 하기

task를 생성하고 dependsOn 으로 의존관계를 만들어 둡니다.<br/>

```groovy
task _1_updateVersion(group: '_sample_') {
    doLast {
        // check if anything has changed
        def releaseNote = createReleaseNote()
        if (releaseNote.isBlank())
            throw new GradleException('Nothing has changed from last tag.')

        updateVersionProperties(verName(), verCode())
        gitCommitAndTagVersion(releaseNote, verName())
    }
}

task _2_buildAndDistribute(dependsOn: 'clean', group: '_sample_') {}
_2_buildAndDistribute.dependsOn('assemble')
_2_buildAndDistribute.dependsOn('appDistributionUploadDebug')
```

task에서 group으로 task의 그룹을 지정할 수 있습니다.<br/>
dependsOn으로 해당 태스크가 실행되기 전에 어떤 task가 실행될 것인지 지정할 수 있습니다.<br/>
위 예제에서 dependsOn의 순서는 되도록 지켜주세요.<br/>

<br/><br/><br/>

# 🚀 이제 남은 것은 실전 뿐 

![tasks]({{ "/assets/img/post/20210327_tasks.png" | relative_url}})

task 1, 2를 순서대로 실행합니다.<br/>

> 만약 RELASE_NOTE를 수정해서 배포하고 싶다면 <br/>
> task1 실행 이후 수동으로 RELASE_NOTE를 수정하신 다음 task2 실행하시면 됩니다.

<br/>
그리고 배포된 앱을 확인합니다.<br/>

![result]({{ "/assets/img/post/20210327_05.png" | relative_url}})

<br/>어때요? 참 쉽죠??<br/><br/><br/>

<br/><br/>

## 샘플 

각 부분별로 설명하다보니 조금 복잡해보이고 이해가 어려울 수 있을 것 같습니다.<br/>
아래 샘플 프로젝트를 참고해서 어떤식으로 구성 되어 있는지 확인해보시면 좋을 것 같습니다.<br/>
아마 README와 **build.gradle(app)** 파일만 훑어보셔도 충분히 참고가 될 것 같습니다 😄<br/>
<br/><br/><br/>


![repo]({{ "/assets/img/post/20210327_04.png" | relative_url}})
<br/>
[https://github.com/HaenaraShin/Firebase-App-Distribution-Plugin-Sample](https://github.com/HaenaraShin/Firebase-App-Distribution-Plugin-Sample
)

도움이 되셨다면 샘플 프로젝트에 스타도 한번씩 부탁드립니다 ⭐️<br/>
<br/>
<br/><br/><br/>