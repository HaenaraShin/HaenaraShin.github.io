---
layout: post
title:  "AndroidStudio에서 Firebase 앱 내부배포를 한번의 클릭으로 해결하기"
feature-img: "assets/img/post/20210327_"
thumbnail: "assets/img/post/20210327_"
description: "안드로이스 스튜디오에서 Firebase 내부배포 자동화 하기"
date:   2021-3-27 14:11:00 +0900
categories: Android,Firebase
---

# Firebase 앱 내부배포?

Firebase에서는 개발자를 위한 정말 다양한 서비스를 제공하고 있는데 오늘 소개할 서비스는 **Firebase 앱 배포** 입니다. 
**Firebase 앱 배포**를 통해서 스토어에 올리지 않고 팀원들에게만 공유하여 다운받을 수 있도록 배포할 수 있습니다.
새로운 버전을 출시할 때 마다 테스터느 손쉽게 다운 받아서 테스트 해볼 수 있으며 
버전별로 테스트할 수 있도록 이전 버전의 다운로드도 지원합니다.

자세한 소개는 [공식문서](https://firebase.google.com/docs/app-distribution)를 참고해보세요.

## 내부배포를 Android Studio 에서?

보통은 `Firebase Console`을 웹으로 접속하여 APK파일을 직접 업로드하여 배포하지만 
**Android Studio**에서도 Firebase에서 app-distribute 플러그인을 제공하여 아주 손쉽게 배포할 수 있습니다.
사실 CLI, Fastlane 등을 통해서도 배포할 수 있지만 
이번 포스팅에서는 Gradle 플러그인을 이용한 방법을 소개합니다.

자세한 내용은 [공식문서](https://firebase.google.com/docs/app-distribution/android/distribute-gradle)를 참조하세요.

### 의존성 추가 및 플러그인 적용

우선 플러그인 사용을 위해 build.gradle(root)에 다음과 같이 추가합니다.

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

build.gradle(app)에는 다음과 같이 플러그인을 적용합니다.

```groovy
plugins {
    id 'com.google.firebase.appdistribution'
}
```

### 빌드 타입에 Firebase 배포 설정

플러그인 적용이 완료되었다면 이제 출시노트와 대상 테스터를 설정합니다.
빌드타입 또는 `ProductFlavor`에 `firebaseAppDistribution` 설정을 추가합니다.
출시노트와 테스터는 String 객체로 직접 입력할 수도 있고 파일을 참조하도록 할 수도 있습니다.
테스터의 경우는 "," 단위로 여러명을 지정할 수도 있고 아예 그룹이름 단위로 선택할 수도 있습니다.
아래 예시에서는 debug 빌드일 때 프로젝트 내의 `RELEASE_NOTE.txt`파일을 참조하도록 하였습니다.

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

좀 더 자세한 활용방법은 [공식문서](https://firebase.google.com/docs/app-distribution/android/distribute-gradle)를 참조하세요.


# 버전 관리 배포하기

#### 🤔 결국 출시노트는 일일히 적어야 하는거 아냐?

플러그인을 적용하면 분명 콘솔에 접속 할 필요도, APK를 직접 업로드 할 필요도 없어서 간편한 것은 사실이지만
아쉬운 점은 출시노트를 매번 일일히 수동으로 적고 버전도 매번 하나씩 올려주어야 한다는 것입니다.
하지만 기왕 자동화를 시작할꺼면 끝까지 해야겠지요? 🤩

이번 포스팅에서는 Git 명령어를 이용해서 출시노트를 자동으로 생성하고 버전 역시 자동으로 하나씩 올리는 방법을 소개합니다.

## 버전을 별도의 파일로 관리하기

우선 버전을 `version.properties`라는 별도 파일로 생성하여 관리해봅시다.
별도의 파일로 관리하는 이유는 gradle에서 손쉽게 버전을 읽고 덮어씌워 저장하기 위해서입니다.
일단 프로젝트에 `version.properties`파일을 다음과 같이 생성해주세요.

```
version_name=1.0.0
version_code=1
```

이후 해당 프로퍼티 파일을 읽어서 버전네임과 코드를 사용하도록 `build.gradle(app)`에 다음과 같이 코드를 추가합니다.

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

`version.properties`의 버전을 하나씩 올리고 새로 저장하는 함수는 아래와 같습니다.
해당 함수도 `build.groovy(app)`에 추가해주세요.

```groovy
def updateVersionProperties(name, code) {
    def versionProp = new File("${rootDir}", 'version.properties')
    def newVersion = name.substring(0, name.lastIndexOf('.')) +
            ".${Integer.valueOf(name.split('\\.').last()) + 1}"
    versionProp.text = "version_name=${newVersion}\n" +
            "version_code=${code + 1}"
}
```

## Git Log로 출시노트 만들기

이제 출시노트를 자동생성해볼 차례입니다.
Git Log를 이용하여 그간 작업한 내용을 출시노트로 변환하는 작업을 할 겁니다.

일단 마지막 태그부터 최신까지의 커밋 메세지를 출력하는 git 명령어는 다음과 같습니다.
```shell
git log $(git describe --tags --abbrev=0)..HEAD --oneline --format="%s"
```

이제 이 명령어를 gradle에서 실행하면 되는데...
문제는 `$(git describe --tags --abbrev=0)`와 같이 명령어 안의 명령어를 한번에 실행할 수가 없다는 점입니다.

따라서 

1. 마지막 태그 버전 가져오기
2. 특정 버전부터 최신까지의 로그 출력하기 

이렇게 두가지 커맨드를 각각 함수로 호출해주고 출력한 내용을 `RELEASE_NOTE.txt` 파일로 저장해야 합니다.
아래의 함수가 두가지 동작을 처리하는 함수인데 이 역시 `build.gradle(app)`에 추가합니다.

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

참고로 `--format=%s` 는 커밋 메세지만 출력하도록 하는 포맷인데 
%h를 추가하면 커밋 리비전도 함께 출력할 수 있고 필요에 따라 포맷을 원하는대로 수정해서 쓸 수 있습니다.

> RELEASE_NOTE.txt 파일은 .gitignore 파일에 추가해서 커밋에 추가되지 않도록 하는 것을 권장합니다.

## 버전별 Git Tag 남기기

이제 버전 네임과 코드가 올라간 것을 다른 팀원들과도 Commit으로 공유하고 출시를 tag 로 남겨봅시다.
아래 함수도 역시 `build.gradle(app)`에 추가합니다.

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

## 빌드 및 배포 하기

task를 생성하고 dependsOn 으로 의존관계를 만들어 둡니다.

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

task에서 group으로 task의 그룹을 지정할 수 있습니다.
dependsOn으로 해당 태스크가 실행되기 전에 어떤 task가 실행될 것인지 지정할 수 있습니다.
위 예제에서 dependsOn의 순서는 되도록 지켜주세요.

# 🚀 이제 남은 것은 실전 뿐 

task 1, 2를 순서대로 실행합니다.

> 만약 RELASE_NOTE를 수정해서 배포하고 싶다면 
> task1 실행 이후 수동으로 RELASE_NOTE를 수정하신 다음 task2 실행하시면 됩니다.

그리고 배포된 앱을 확인합니다.

## 샘플 

각 부분별로 설명하다보니 조금 복잡해보이고 이해가 어려울 수 있을 것 같습니다.
아래 샘플 프로젝트를 참고해서 어떤식으로 구성 되어 있는지 확인해보시면 좋을 것 같습니다.
아마 README와 **build.gradle(app)** 파일만 훑어보셔도 충분히 참고가 될 것 같습니다 😄

https://github.com/HaenaraShin/Firebase-App-Distribution-Plugin-Sample

도움이 되셨다면 샘플 프로젝트에 스타도 한번씩 부탁드립니다 ⭐️

