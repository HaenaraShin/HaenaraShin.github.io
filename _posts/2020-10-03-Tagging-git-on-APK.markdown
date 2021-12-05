---
layout: post
title:  "Android APK 이름에 Git 리비전 태그 붙이기"
description: Android APK 생성 시 APK 파일 이름에 Git 리비전 태그를 붙이는 방법을 설명합니다.
date:   2020-10-03 17:42:00 +0900
categories: Android
---

Android APK 생성 시 APK 파일 이름에 Git 리비전 태그를 붙이는 방법을 설염합니다. 

안드로이드에서 처음 앱을 빌드하면 apk는 보통 

`app-debug.apk` 또는 `app-release.apk`

가 보통 나옵니다.

버전관리를 위해 APK 이름에 빌드 날짜와 git 리비전을 남기고 싶다면
아래와 같이 작성해보세요.

# 1. 앱 수준의 build.gradle(app)

앱 수준의 build.gradle(app) 에서 android{ } 안에 아래와 같이 추가 하면 됩니다

```groovy
android {
    ...
    android.applicationVariants.all { variant ->
        changeAPKName(variant)
    }
}
```

# 2.  changeAPKName 함수 추가

그 다음 android{ }밖에 changeAPKName 함수를 추가합니다.

```groovy
def changeAPKName(variant) {
    variant.outputs.all { output ->
        def apkName = {
            def appName = 'sample'
            def buildType = name.split('-').last() // debug or release
            def currentDate = { return new Date().format('yyyyMMdd') }
            def gitHash = { ->
                def stdout = new ByteArrayOutputStream()
                exec {
                    commandLine 'git', 'rev-parse', '--verify', '--short', 'HEAD'
                    standardOutput = stdout
                }
                return stdout.toString().trim()
            }
            return "${appName}_${buildType}_${currentDate()}_${gitHash()}.apk"
        }
        outputFileName = new File(apkName())
        logger.info('APK FILE NAME : ' + apkName())
    }
}
```

그럼 아래와 같은 형식으로 바뀝니다.

![sample_result]({{ "/assets/img/post/20201003_01.jpeg" | relative_url}})<br/>

만약 다른 방식을 원한다면 changeAPKName 함수를 수정해보세요.
