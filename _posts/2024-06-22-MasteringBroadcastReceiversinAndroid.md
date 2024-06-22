---
title: "안드로이드 브로드캐스트 리시버 완벽 마스터하기"
description: ""
coverImage: "/assets/img/2024-06-22-MasteringBroadcastReceiversinAndroid_0.png"
date: 2024-06-22 22:36
ogImage: 
  url: /assets/img/2024-06-22-MasteringBroadcastReceiversinAndroid_0.png
tag: Tech
originalTitle: "Mastering Broadcast Receivers in Android"
link: "https://medium.com/@riztech.dev/mastering-broadcast-receivers-in-android-362a75e7b2b1"
---


<img src="/assets/img/2024-06-22-MasteringBroadcastReceiversinAndroid_0.png" />

안드로이드 앱 개발에서 브로드캐스트 수신기는 시스템 전반에 걸친 브로드캐스트 공지를 수신하기 위한 앱의 기능을 활성화하는 데 중요한 역할을 합니다. 장치의 배터리가 낮아지거나 인터넷 연결이 변경되었을 때, 또는 새로운 SMS가 도착했을 때를 감지하는 것처럼, 브로드캐스트 수신기는 이러한 이벤트에 대응하기 위한 주요 구성 요소입니다. 이 기사에서는 브로드캐스트 수신기가 무엇인지, 어떻게 작동하는지, 그리고 안드로이드 앱에서 효과적으로 활용하는 방법에 대해 살펴보겠습니다.

# 브로드캐스트 수신기란?

브로드캐스트 수신기는 활동(Activities), 서비스(Services), 콘텐트 제공자(Content Providers)와 함께 안드로이드 앱의 네 가지 주요 구성 요소 중 하나입니다. 브로드캐스트 수신기를 통해 앱은 다른 애플리케이션이나 시스템으로부터의 브로드캐스트 메시지를 수신하고 응답할 수 있습니다. 이러한 브로드캐스트는 시스템이나 다른 앱이 서로 통신할 수 있는 방법을 제공하는 것으로 생각할 수 있습니다.

<div class="content-ad"></div>

이제 본문을 한국어로 번역해보겠습니다.

이제 본문을 한국어로 번역해보겠습니다.

두 가지 주요 방송 유형이 있습니다:

- 시스템 방송: Android 시스템이 특정 시스템 이벤트(예: 기기를 충전할 때 또는 화면이 켜질 때) 발생 시 보내는 방송입니다.
- 사용자 정의 방송: 앱이나 기타 제3자 앱이 특정 이벤트를 전달하기 위해 보내는 방송입니다.

# 방송 수신기 동작 방식

방송 수신기는 관심 있는 방송을 지정하는 IntentFilter를 정의함으로써 동작합니다. 이 필터와 일치하는 방송이 전송되면 수신기의 onReceive 메서드가 호출되어 앱이 해당 이벤트에 대응할 수 있게 됩니다.

<div class="content-ad"></div>

# 브로드캐스트 수신기 정의하기

브로드캐스트 수신기는 AndroidManifest.xml 파일에 정적으로 또는 코드에서 동적으로 정의할 수 있습니다.

## 정적 등록

정적 등록은 앱의 AndroidManifest.xml 파일에 수신기를 선언하는 것을 의미합니다. 이 방법은 앱이 실행 중이 아닌 경우에도 브로드캐스트를 수신하는 데 유용합니다.

<div class="content-ad"></div>

```js
<receiver android:name=".MyBroadcastReceiver">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="android.net.conn.CONNECTIVITY_CHANGE"/>
    </intent-filter>
</receiver>
```

## 동적 등록

동적 등록은 런타임에서 수행되며 일반적으로 Activity 또는 Service 내에서 수행됩니다. 이 접근 방식을 통해 앱의 라이프사이클에 따라 수신기의 등록 및 해제를 더 유연하게 처리할 수 있습니다.

```js
val receiver = MyBroadcastReceiver()
val filter = IntentFilter().apply {
    addAction("android.intent.action.BOOT_COMPLETED")
    addAction("android.net.conn.CONNECTIVITY_CHANGE")
}
registerReceiver(receiver, filter)
```

<div class="content-ad"></div>

# 브로드캐스트 수신기 구현

다음은 브로드캐스트 수신기 구현의 간단한 예제입니다:

```js
class MyBroadcastReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        when (intent.action) {
            Intent.ACTION_BOOT_COMPLETED -> {
                // 부팅 완료 처리
                Log.d("MyBroadcastReceiver", "기기 부팅됨")
            }
            ConnectivityManager.CONNECTIVITY_ACTION -> {
                // 연결 상태 변경 처리
                Log.d("MyBroadcastReceiver", "연결 상태 변경됨")
            }
        }
    }
}
```

# 실용적인 사용 사례

<div class="content-ad"></div>

방송 수신기는 다양하고 다양한 시나리오에서 사용할 수 있습니다. 이제 몇 가지 실용적인 사용 사례를 살펴보겠습니다:

# 연결 변경 처리

네트워크 연결이 변경될 때 필요한 작업을 수행하려고 할 수 있습니다. 예를 들어, 기기가 인터넷에 연결될 때 데이터를 동기화하는 등의 작업을 수행할 수 있습니다.

```js
class ConnectivityReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        val connectivityManager = context.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager
        val networkInfo = connectivityManager.activeNetworkInfo
        if (networkInfo != null && networkInfo.isConnected) {
            // 기기가 인터넷에 연결됨
            Log.d("ConnectivityReceiver", "인터넷에 연결됨")
        } else {
            // 기기가 인터넷에 연결되지 않음
            Log.d("ConnectivityReceiver", "인터넷에 연결되지 않음")
        }
    }
}
```

<div class="content-ad"></div>

# 시스템 이벤트에 대응하기

특정 시점에 작업을 수행하려면 방송 수신기(Broadcast Receivers)를 사용할 수 있습니다. 예를 들어 장치 부팅 시 서비스를 시작하는 등의 작업을 수행할 수 있습니다.

```js
class BootCompletedReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        if (intent.action == Intent.ACTION_BOOT_COMPLETED) {
            val serviceIntent = Intent(context, MyService::class.java)
            context.startService(serviceIntent)
        }
    }
}
```

# 사용자 정의 방송 수신하기

<div class="content-ad"></div>

당신의 앱은 자체 사용자 정의 브로드캐스트를 정의하고 수신할 수도 있습니다. 이를 통해 다른 구성 요소 또는 앱 간에 통신할 수 있습니다.

```js
// 사용자 정의 브로드캐스트 보내기
val intent = Intent("com.example.MY_CUSTOM_ACTION")
sendBroadcast(intent)

// 사용자 정의 브로드캐스트 수신하기
class CustomBroadcastReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        if (intent.action == "com.example.MY_CUSTOM_ACTION") {
            // 커스텀 동작 처리
            Log.d("CustomBroadcastReceiver", "커스텀 동작 수신됨")
        }
    }
}
```

# 모범 사례

브로드캐스트 수신기는 강력하지만 흔한 실수를 피하기 위해 신중하게 사용해야 합니다.

<div class="content-ad"></div>

# 전력 소비 관리

방송 수신기 중에는 명시적으로 등록된 수신기가 배터리 수명에 상당한 영향을 미칠 수 있습니다. 더 이상 필요하지 않은동적으로 등록된 수신기는 항상 등록 해제해야 합니다.

# 보안 고려 사항

민감한 방송을 위한 수신기를 정의할 때 주의해야 합니다. 특정 방송을 보낼 수 있거나 수신할 수 있는 애플리케이션은 신뢰할 수 있는 앱들만 보낼 수 있도록 권한 검사를 사용해야 합니다.

<div class="content-ad"></div>

```js
<receiver android:name=".SecureBroadcastReceiver">
    <intent-filter>
        <action android:name="com.example.SECURE_ACTION"/>
    </intent-filter>
    <permission android:name="com.example.permission.SECURE_BROADCAST"/>
</receiver>
```

# 메모리 누수 방지하기

적절한 라이프사이클 메소드에서 동적으로 등록된 리시버를 해제하여 메모리 누수를 방지하세요.

```js
override fun onPause() {
    super.onPause()
    unregisterReceiver(myReceiver)
}
```

<div class="content-ad"></div>

# 결론

브로드캐스트 수신기는 안드로이드 개발에서 기본 구성 요소로, 앱이 다양한 시스템 및 사용자 이벤트를 듣고 대응할 수 있게 합니다. 방송을 효과적으로 등록하고 처리하는 방법을 이해하면, 더 반응적이고 동적인 애플리케이션을 만들 수 있습니다. 전력 소비와 보안을 관리하기 위한 최상의 방법을 준수하여 앱을 효율적이고 안전하게 유지하세요. 이 도구와 기술을 활용하면 안드로이드 프로젝트에서 브로드캐스트 수신기의 모든 잠재력을 활용할 준비가 되어 있습니다.