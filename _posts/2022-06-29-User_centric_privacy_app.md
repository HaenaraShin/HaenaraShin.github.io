---
layout: post
title:  "Goolge I/O 2022 유저 중심의 안전한 앱 개발하기"
feature-img: "assets/img/post/20220629_01.png"
thumbnail: "assets/img/post/20220629_01.png"
description: 해당 포스팅은 Google I/O 2022의 Developing privacy user-centric apps 세션을 보며 유저 중심의 안전한 앱 개발하기 위한 새로운 안드로이드의 기능들을 살펴봅니다.
date:   2022-06-29 23:55:00 +0900
categories: Android
---

![01]({{ "/assets/img/post/20220629_01.png" | relative_url}})

해당 포스팅은 Google I/O 2022의 [Developing privacy user-centric apps](https://www.youtube.com/watch?v=opGkUl8C-HM&list=PLWz5rJ2EKKc_gLZhqjTRn0vGssFiP_4Kb) 세션을 recap 한 내용입니다.


이번 Google I/O 2022를 통해서 Android 12와 13에서의 새로운 개인정보 보안을 위한 다양한 기능들이 공개되었다. 주목할 만한 점은 단순히 보안성을 높이는 것 뿐만 아니라 정보의 투명화와 가시화에 초점을 두어 **사용자가 보안 이슈를 인지하게끔** 유도하고 있다는 점에 있다. 반대로 개발자 입장에서는 보안 상 민감할 수 있는 사항들을 이전처럼 대충 사용자 몰래 혹은 얼렁뚱땅 넘어가지 못하게 되어 번거로워 졌다. 그러나 후술하겠지만 사용자에게 정보가 투명해질수록 사용자로 하여금 해당 기능을 사용하도록 유도하기가 더 유리해진다는 점에서 개발자와 사용자 모두 Win-Win 하는 셈이니 긍정적인 방향인 셈이다. 

<br/><br/>

# 안전한 앱을 만드는 3가지 원칙

#### 1. Pay attention to data accesses

**데이터 접근에 주목하기**정도로 풀어볼 수 있는데, 여기서 중요한 점은 **주어가 개발자가 아닌 사용자이다.** 즉 사용자가 데이터 접근을 인지하고 주목할 수 있도록 좀 더 보안의 투명성과 가시성을 통해 강조해야 한다는 것 이다.

#### 2. Choose privacy centric alternatives

**보안 중심의 대안 선택**로 해석 가능하다. 개발자 입장 또는 서비스 입장에서 쉬운 선택을 하는 대신 사용자 입장에서 되도록 간편하고 안전한 대안을 선택하고 꼭 필요할 때만 개인정보에 접근할 것을 시사한다. 

#### 3. Limit access to data

**데이터 접근 최소화**로 말그래도 되도록이면 애초에 불필요한 개인정보에 접근하지 말고 꼭 필요한 만큼만 접근할 것을 강조한다. 

<br/>
---------------------------------------------------------------
<br/><br/>

# 1. Pay attention to data accesses<br/>(데이터 접근에 주목하기)

## [Privacy Dashboard(개인정보 대시보드)](https://developer.android.com/training/permissions/explaining-access#privacy-dashboard)

![02]({{ "/assets/img/post/20220629_02.png" | relative_url}})


Android 12에 추가된 개인정보 대시보드는 앱의 사용자 정보 접근내역별로(위치, 카메라, 마이크) 가시화하여 사용자에게 제공한다.<br/> 또한 타임라인으로 접근 이력 또한 확인할 수 있다. 설명에 따르면 개인정보 대시보드에서 40% 이상의 앱 상세접근을 관리하고 있다. 원치 않는 앱의 접근도 이 대시보드에서 바로 비활성화해버릴 수 있다. 따라서 얼렁뚱땅 사용하고 있던 기능/권한이 있다면 이제는 사용자가 주기적으로 여기서 비활성화 해버릴 수 있다는 점을 개발자가 항상 유의해야 한다.

![03]({{ "/assets/img/post/20220629_03.png" | relative_url}})
![04]({{ "/assets/img/post/20220629_04.png" | relative_url}})

개인정보 대시보드에서는 데이터에 접근한 시점의 타임라인이 표시되며 각각에 대해 접근한 근거를 표시할 수 있다. 따라서 개발자가 데이터 접근 시 이를 명확하게 사용자에게 알려서 사용자가 원하는 기능만 허용하도록 권장하고 있다. 해당 세션에서는 이렇게 명확하게 알릴 때 사용자가 해당 기능을 허용하도록 유도할 가능성이 높다는 점을 시사하고 있다. 

- 만약 내 앱과 3rd파티 SDK에서 사용하고 있는 데이터가 궁금하다면 [audit-API](https://developer.android.com/guide/topics/data/audit-access)를 이용하여 사전에 어떤 데이트를 확인하고 있는지 확인할 수 있다.

<br/>

## Notification Permission

![05]({{ "/assets/img/post/20220629_05.png" | relative_url}})

Android 13부터 앱 알림 기능을 사용하기 위해 `POST_NOFICIATIONS` 권한이 필요하다. `targetSdk32`이하의 앱은 최초 구동 시 시스템에서 강제로 해당 권한 체크한다고 하니 따로 처리가 필요하지는 않다. 알림을 사용하기 전에 `PermissionChecker`로 권한 승인여부 확인할 수 있다. iOS에서는 예전부터 알림 권한을 요청했었는데 기획자 입장에서는 다행일지도..?

<br/>
## Active Apps

![06]({{ "/assets/img/post/20220629_06.png" | relative_url}})

알림에서 Foreground service를 제어할 수 있게 끔 **Active Apps**라는 메뉴가 신설되었다. <br/>이전에는 Foreground service 알림을 지워도 여전히 동작했다면, Android 13에서부터는 알림의 Active Apps 항목에서 현재 실행 중인 foreground service 전부 확인인할 수 있고 또 강제로 종료시켜버릴 수도 있다. 

<br/>
---------------------------------------------------------------
<br/><br/>

# 2. Choose privacy centric alternatives<br/>(보안 중심의 대안 선택)

해당 파트에서는 UseCase별 적절한 데이터 저장소를 선택하는 방법을 순서대로 설명해준다. <br/>
만일 샘플 코드를 원한다면 [https://goo.gle/android-storage-samples](https://github.com/android/storage-samples)에서 확인할 수 있다.

<br/><br/>

## Internal Storage

![07]({{ "/assets/img/post/20220629_07.png" | relative_url}})

일반적인 경우에는 데이터를 저장할 때는 내부 저장소를 활용한다.<br/> 내부저장소는 외부 저장소보다 빠르고 간편하며 별도의 권한도 필요 없다. 다만 내부 용량이 모자라진다면 외부 저장소를 사용해야 한다. 당연히 다른 앱에서는 내부저장소에 접근할 수 없다.

- `filesDir()`메소드로 내부 저장소에 접근
- `File.getUsableSpace()`메소드로 내부 저장소의 여유용량을 확인

<br/>

## External Storage

![08]({{ "/assets/img/post/20220629_08.png" | relative_url}})


저장하려는 데이터의 용량이 너무 커서 내부저장소를 활용하기 어려운 경우에 SD카드 등의 외부저장소를 활용하고 싶은 경우에 활용한다. 이전에는 이 외부저장소를 활용하여 다른 앱에서 접근할 수 있는 공유저장소로 활용했으나, Android 11 이상에서는 외부 앱에서 접근이 불가능하다. 

- `getExternalFilesDir()` 메소드로 접근

<br/>

## Shared Storage

![09]({{ "/assets/img/post/20220629_09.png" | relative_url}})


예전 처럼 외부 저장소를 앱간의 공유 저장소를 활용하고 싶었다면 이제는 불가능하다. 만약 다른 앱에서 사용할 수 있도록 공유하고 싶은 경우 (특히 미디어) Shared Storage를 활용한다. Android 10 이후여도 별도의 권한없이 다른 앱에서 접근 가능하다.

- `getExternalStoragePublicDirectory(DIRECTORY_PICTURES)`로 접근
 
<br/>

## [Storage Access Framework API](https://developer.android.com/guide/topics/providers/document-provider)

![10]({{ "/assets/img/post/20220629_10.png" | relative_url}})

공유된 파일에 접근하기 위해 사용하는 `Storage Access Framework API`가 필요하다. 해당 API를 활용하면 별도의 권한 불필요하다. `OpenDocument`라는 Android Jetpack 라이브러리로 호출하면 파일 네비게이터를 통해 파일을 선택할 수 있다. 

- `ACTION_OPEN_DOCUMENT` Intent 사용

<br/>

## 미디어 권한 분리

![11]({{ "/assets/img/post/20220629_11.png" | relative_url}})


이전의 저장 권한이었던 `READ_EXTERNAL_STORAGE`, `WRITE_EXTERNAL_STORAGE` 권한이 Android 13 부터 deprecated 되었다. 대신 미디어별로 권한이 다음과 같이 분리 되었다. 

- `READ_MEDIA_IMAGE`
- `READ_MEDIA_VIDEO` 
- `READ_MEDIA_AUDIO` 

다행스러운 것은 매번 각각 요청하지 않고 해당 권한을 한꺼번에 요청하면 사용자에게 각각에 대해 권한을 물어보지 않고 한번에 요청한다. 만약 이전에 `READ/WRITE_EXTERNAL_STORAGE` 권한이 허용되어있다면 새로운 권한도 자동으로 허용된다.

- Android 13 타겟팅 앱에서 이전권한 사용 시 `maxSdkVersion="30"` 을 추가 필요

<br/>

## PhotoPicker 

![12]({{ "/assets/img/post/20220629_12.png" | relative_url}})

런타임 권한 허용 없이도 이미지에 접근 가능하도록 `PhotoPicker`가 소개되었다. Android 13부터 적용되며 GooglePlay System만 업데이트하면 Android 11, 12 에서도 사용가능할 예정이다. 이전엔 이미지 피커를 매번 구현하던가 아니면 파일 브라우저를 여는 식이어서 번거롭고 불편했어서 상당히 기대가 된다.

<br/>
---------------------------------------------------------------
<br/><br/>

# 3. Limit access to data<br/>(데이터 접근 최소화)

## COARSE LOCATION 

![13]({{ "/assets/img/post/20220629_13.png" | relative_url}})

Android 12부터 추가된 내용으로 아주 상세한 위치 권한과 대략적인 위치 권한중 사용자가 선택하여 권한을 허용하도록 변경되었다.<br/> 만약 상세 위치 정보가 필요없다면 대략적인 위치 권한만 요청할 수도 있다. 그러나 반대로 상세 위치 정보만 필요하더라도 이전 처럼 상세 위치권한만 바로 요청할 수는 없고 반드시 대략적인 위치 권한과 함께 요청해야 한다. 단 대략적인 위치 권한이 이미 허용된 상황에서 상세 위치 권한으로 업그레이드 할 수는 있다. 따라서 위치권한을 필요로 하는 모든 앱은 이전과 달리 **대략적인 위치 권한만 허용 되었을 때의 UseCase도 고려해야 한다.**

- https://developer.android.com/training/location/permissions

<br/>

## Nearby Devices

![14]({{ "/assets/img/post/20220629_14.png" | relative_url}})
![15]({{ "/assets/img/post/20220629_15.png" | relative_url}})

이전까지는 Bluetooth, Wifi 장비 검색을 위해서 엉뚱하게 위치 권한을 요청해야 했다. 앞으로는 위치 권한을 받는 대신 Nearby Device 권한 사용하여 사용자에게 좀 더 직관적인 설명이 가능해졌다. 또한 Manifest에서 `usePermissionFlags="neverForLocation"` 로 위치정보가 필요 없다고 명시할 수도 있다.

<br/>

## Downgrade 

![16]({{ "/assets/img/post/20220629_16.png" | relative_url}})

구글에서는 더 이상 사용하지 않는 권한들을 삭제하기를 권장한다.<br/> 이를 위해 더 이상 사용하지 않는 권한을 downgrade 할 수 있는 API가 공개되었다. Android 13 또는 targetApi 33 이상에서 지원한다. 개인정보 대시보드 등을 통해서 사용자가 모니터링 하는 것을 염두에 둔다면 삭제된 기능등으로 인한 불필요한 권한은 제때제때 삭제해 주는 것이 좋을 것이다.

<br/>

## Clipboard 

![17]({{ "/assets/img/post/20220629_17.png" | relative_url}})

기본 적으로 사용자가 이해할 수 있는 클립보드만을 사용할 것을 권장하고 있다.<br/>이에 맞춰 다른 앱에서 복사한 클립보드를 사용하게되면 토스트 메세지로 사용자에게 알림이 뜬다. 또한 Android 13부터는 한시간 이상 사용하지 않는 클립보드는 삭제된다. 


<br/>
---------------------------------------------------------------
<br/><br/>

# 결론

매년 업데이트되는 안드로이드 최신 개인정보 보안 사항은 개발자 입장에서는 번거로운 숙제같은 존재다. 그러나 사용자에게 정보가 투명해질수록 사용자로 하여금 해당 기능을 사용하도록 유도하기가 더 유리해진다는 점에서 개발자와 사용자 모두 Win-Win 하는 셈이니 너무 부정적으로만 생각하지 말고 꾸준히 눈여겨 보도록 하자.



