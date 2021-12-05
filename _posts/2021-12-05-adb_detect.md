---
layout: post
title:  "Android 앱에서 ADB 연결 막기"
feature-img: "assets/img/post/20211205_thumb.jpeg"
thumbnail: "assets/img/post/20211205_thumb.jpeg"
description: "Android 앱에서 ADB연결을 감지하고 차단하는 방법을 소개합니다."
date:   2021-12-05 21:00:00 +0900
categories: Kotlin
---

# ADB와 보안

![change]({{ "/assets/img/post/20211205_01.jpeg" | relative_url}})<br/>

## ADB란?

ADB는 Android Debug Bridge의 약자로 <br/>
개발자가 안드로이드 디버깅을 위해 단말에 USB 또는 TCP프로토콜로 연결하여 사용하는 <br/>
디버깅용 툴 프로그램 입니다.<br/>
<br/>
ADB를 이용하여 다양한 명령어 조작과 shell 스크립트 동작을 사용할 수 있어서<br/>
경우에 따라서는 안드로이드 앱에서 여러가지 보안 정책을 보안상의 이유로 ADB 연결을 막는 경우가 있습니다. <br/>
보통은 크게 신경쓰지 않지만 일부 높은 보안수준이 요구되는 금융앱의 경우<br/>
실제로 이 ADB 연결을 막기도 합니다.<br/>
<br/>

![sample]({{ "/assets/img/post/20211205_1.jpeg" | relative_url}})<br/>
> 실제 부산은행 썸뱅크 앱에서 ADB 연결시 나오는 메세지

사실 원리는 간단합니다.<br/>
<br/>
**USB연결 여부**와 **USB디버깅 모드**가 켜져있는지 두가지를 확인하는 방법입니다.<br/>


<br/><br/><br/>

## 1. 👷 USB 디버깅이 켜져있는지 확인하는 방법

안드로이드 세팅에 USB 디버깅 모드가 켜져있는지 여부를 가지고 있습니다.<br/>
android api 16 이하에서는 아래와 같은 방법으로 확인할 수 있습니다.<br/>

```kotlin
// 아래 값이 0이 아니면 개발자 모드가 켜진 것이다.
Settings.Secure.getInt(context.contentResolver, Settings.Secure.ADB_ENABLED, 0)
```

<br/>
android api 17 부터는 아래와 같은 방법으로 확인해야 합니다.<br/>

```kotlin
// 아래 값이 0이 아니면 개발자 모드가 켜진 것이다.
Settings.Secure.getInt(context.contentResolver,Settings.Global.ADB_ENABLED, 0)
```
<br/>
즉 정리하면 아래와 같이 됩니다.<br/>

```kotlin
/**
 * 개발자모드에서 USB디버깅이 허용되어 있는지 확인
 * @param context
 * @return
 */
fun isDebugEnable(): Boolean {
    return when {
        Build.VERSION.SDK_INT == Build.VERSION_CODES.JELLY_BEAN -> Settings.Secure.getInt(
            mContext.contentResolver,
            Settings.Secure.ADB_ENABLED, 0
        ) != 0
        Build.VERSION.SDK_INT >= Build.VERSION_CODES.JELLY_BEAN_MR1 -> Settings.Secure.getInt(
            mContext.contentResolver,
            Settings.Global.ADB_ENABLED, 0
        ) != 0
        else -> false
    }
}
```

<br/><br/><br/>

## 2.  🔌 USB 연결이 되어있는지 확인하는 법


USB가 연결되어 있는지 확인하는 방법은 아래와 같이 인텐트 필터를 이용합니다.<br/>

```kotlin
/**
 * USB 연결이 되어있는지 확인
 * @param context
 * @return
 */
private fun isUsbConnected(): Boolean {
    val intent = context.registerReceiver(
        null,
        IntentFilter("android.hardware.usb.action.USB_STATE")
    )
    return intent != null && intent.extras != null &&
            intent.extras!!.getBoolean("connected")
}
```

따라서 USB가 연결되어 있고 USB 디버깅 모드가 허용되어 있다면<br/>
USB 디버깅을 하고 있다는 뜻 입니다.<br/>

```kotlin
/**
* USB연결 정책 위반 여부 확인
* USB 연결 여부, USB디버깅설정 여부 모두 true 면 true
* @return
*/
fun checkUsbDebuggingMode(): Boolean {
    return isDebugEnable() and isUsbConnected()
}
```

<br/><br/><br/>

## 3. ⚡ USB 연결을 감지

위 방법을 이용하면 USB가 연결되어 있는지 그 순간에만 확인할 수 있고<br/>
앱 실행 중에  USB가 연결되는 것은 확인할 수 없습니다.⚡<br/>

앱 실행 중 USB가 연결되는 것을 확인하려면 BroadcastReceiver를 등록하여 <br/>
하드웨어에서 USB가 연결된 이벤트를 받아서 처리할 수 있습니다.<br/>
<br/>
단 USB가 연결되었을 때 USB 디버깅이 켜져있는지 먼저 확인해야 합니다.<br/>
USB 연결을 감지하는 BroadcastReceiver 의 코드 예제는 아래와 같습니다.<br/>

```kotlin
/**
 * USB 디버깅을 방지하기 위한 클래스
 * release 빌드에서 USB가 연결되고 개발자모드의 USB디버깅(ADB)이 허용되어 있으면
 * 앱을 강제로 종료하는 리시버 클래스
 */
class UsbDebugReceiver : BroadcastReceiver() {
    // 하드웨어 이벤트 감지시 onReceive 가 실행
    override fun onReceive(context: Context, intent: Intent) {
        // USB디버깅 모드가 허용되어 있는지 확인한다. 
        val adbEnabled = AdbDetector(context).checkUsbDebuggingMode()
        intent.extras?.let {
            if (it.getBoolean("connected") && adbEnabled) {
                // USB 디버깅이 감지되었으므로
                // 여기에서 이후 처리를 하면 된다.
            }
        }
    }
}
```
<br/><br/>

> ⚠️⚠️⚠️주의할 점⚠️⚠️⚠️<br/>
이 USB 이벤트는 수초 안에 여러번 발생하는 경우가 있으니<br/>
방어코드를넣지 않으면 여러번 실행될 수 있습니다!!!<br/>

리시버를 실제 Application 또는 Activity 에서 등록하는 코드는 아래와 같습니다.<br/>


```kotlin
// USB 연결을 감지하는 필터를 등록
val filter = IntentFilter()
filter.addAction("android.hardware.usb.action.USB_STATE")
context.registerReceiver(UsbDebugReceiver(), filter)
```
<br/>

>⚠️⚠️⚠️주의할 점⚠️⚠️⚠️<br/>
어떤 동작을 할 건지에 따라 Foreground 에 있을 때만 동작하고<br/>
Background로  내리갈 땐 리시버 해지가 필요할 수도 있습니다.<br/>
앱을 강제 종료하는 액티비티 실행과 같은 액션일 경우<br/>
실행중이지 않고 백그라운드에 있는데 강제 종료 액티비티가 실행되는 등<br/>
불필요한 액션이 발생할 수 있습니다.<br/>

<br/><br/><br/>

## 4. 🚀 예제 실행

![sample]({{ "/assets/img/post/20211205_03.gif" | relative_url}})<br/>

USB을 연결하자 마자 다이얼로그를 띄우고 앱을 종료시켜버립니다.<br/>

<br/><br/><br/>

## 5.  📝 소스코드와 예제 

소스코드와 예제는 아래 URL에서 확인하실 수 있습니다.<br/>

![sample_code]({{ "/assets/img/post/20211205_02.png" | relative_url}})<br/>


https://github.com/HaenaraShin/AdbDetector<br/>


## 6.  🎬 그리고 반전…

이제와서 반전은...<br/>
사실 앱수준에서 근본적인 레벨에서 ADB를 막을 방법은 없습니다.<br/>
왜냐면 지금까지 설명한 것은 USB연결을 통한 ADB는 막을 수 있어도<br/>
WiFi를 통한 무선 ADB 연결을 막을 수는 없기 때문입니다..!!😱😱😱<br/>
이 때문에 사실 USB 디버깅은 대부분 신경 쓰지 않습니다….<br/>

<br/>
다만 일전에 취약점 점검을 위해 모바일 앱 모의해킹을 진행한 적이 있는데<br/>
담당자가 WiFi ADB를 모르는 사람이었습니다. (100% 실화)<br/>
<br/>
따라서 꼭 의미 없는 것만은 아닐지도 모르겠습니다. <br/><br/>
