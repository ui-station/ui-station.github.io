---
title: "Shadowfax 안드로이드 앱 40 더 빠르게 만드는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-MakingShadowfaxAndroidApp40faster_0.png"
date: 2024-06-23 01:14
ogImage: 
  url: /assets/img/2024-06-23-MakingShadowfaxAndroidApp40faster_0.png
tag: Tech
originalTitle: "Making Shadowfax Android App 40% faster"
link: "https://medium.com/shadowfax-newsroom/making-shadowfax-android-app-40-faster-995cd36b6e5e"
---



![Image](/assets/img/2024-06-23-MakingShadowfaxAndroidApp40faster_0.png)

# 1. 목표 설정

모바일 앱 성능에는 모든 밀리초가 중요합니다. 앱이 빨리 로드될수록 사용자가 머물 가능성이 높아집니다.

매일 10만 명 이상의 DAU를 보유한 Shadowfax Rider 앱은 앱이 실행되는 데 약 3.5초가 걸리는 문제에 직면했습니다!


<div class="content-ad"></div>

목표는 다음과 같이이 시간을 단축하는 것이었습니다:

- 90 백분위에는 2 초
- 중간 사용자에 대해 800ms

## 2. 앱 시작 시간 측정

Firebase에 따르면 앱 시작 시간은 런처에서 앱이 시작되어 첫 번째 액티비티의 `onResume()` 메서드가 호출 될 때까지의 지속 시간입니다. 이 기간은 다음과 같이 logcat에도 보고됩니다:

<div class="content-ad"></div>

여기에서 더 많은 정보를 읽을 수 있습니다. Firebase에서 Startup 시간이 진실의 근원이었습니다.

만약 onResume이 호출된 후 어떤 시점에 앱이 완전히 로드된 것으로 간주한다면 (지도가 완전히 그려진 후와 같이), 해당 시점을 시스템 및 Firebase에 Activity.reportFullyDrawn()으로 보고할 수 있습니다.

만약 Perfetto를 사용하고 있다면, 나중에 그에 대해 자세히 설명하겠습니다.

# 3. 자세히 들여다보기

<div class="content-ad"></div>

앱의 시작 시간을 기간별로 분해하기 위해 Firebase 성능 라이브러리의 @trace 어노테이션을 app 클래스의 onCreate() 함수, BaseActivity 및 MainActivity의 onCreate() & onStart()에 추가했어요. 기본적으로 모든 것을 최상위 수준에서 측정하므로 주요 원인을 파악하고 거기서부터 드릴다운합니다.

![이미지](/assets/img/2024-06-23-MakingShadowfaxAndroidApp40faster_1.png)

Main 및 Base 액티비티 이외에도 앱 클래스가 앱 시작 시간의 30%를 차지했기 때문에 2가지 작업을 수행했어요:

# 3.1 라이브러리 및 콘텐츠 제공자 지연 로드 (-10% 시작 시간)

<div class="content-ad"></div>

애플리케이션 클래스는 일반적으로 많은 라이브러리를 초기화합니다. 필요하지 않은 라이브러리는 앱 시작 시 즉시 초기화하는 대신 백그라운드에서 초기화하도록 변경했습니다. Content Provider가 있다면 Startup 라이브러리를 사용하여 그것들을 나중에 로딩할 수도 있습니다.

다음은 몇 가지 SDK를 백그라운드 스레드에서 초기화하는 방법입니다:

```js
class MyApp : Application() {
    @AddTrace(name = "backgroundInitializationsTrace")
    private fun performBackgroundInitializations() {
        runWithLooper {
            // 메인 스레드에서 초기화가 필요하지 않은 SDK 초기화
        }
    }
}

object MyUtils {
    // 백그라운드 스레드에서 초기화를 위한 유틸리티 함수
    fun runWithLooper(runnable: Runnable) {
        val threadHandler = HandlerThread("Thread${System.currentTimeMillis()}")
        try {
            threadHandler.start()
            val handler = Handler(threadHandler.looper)
            handler.post {
                runnable.run()
            }
        } catch (e: OutOfMemoryError) {
            firebaseCrashlytics.recordException(e)
            runWithinMainLooper(runnable)
        }
    }
    // 대체 방법
    fun runWithinMainLooper(mainThread: Runnable) {
        val handler = Handler(Looper.getMainLooper())
        handler.post {
            mainThread.run()
        }
    }
}
```

# 3.2 베이스라인 프로필 (-7% 시작 시간)

<div class="content-ad"></div>

Google은 첫 번째 앱 시작 시간을 개선하기 위해 기본 프로필을 설정하는 것을 권장합니다. 전체 앱 시작 시간이 7% 향상되었음을 확인했습니다. 실제 결과는 다를 수 있지만 꼭 시도해 보세요.

그래서 우리는 좋은 시작을 했지만 더 심층적으로 파헤쳐야 했습니다.

### 4. Perfetto 사용하기

안드로이드 스튜디오에서 시스템 추적을 실행하여 각 함수의 수행 시간을 측정할 수 있으며, 그런 다음 앱을 실행하고 추적을 Perfetto 비주얼라이저에 로드할 수 있습니다.

<div class="content-ad"></div>

Android Studio에 내장된 프로파일러를 사용할 수 있지만, Perfetto가 더 나은 탐색 및 세부 정보를 제공합니다.

시스템 추적 방법은 여기에 있지만 앱 실행 시간을 프로파일링하려면 앱을 보통 실행하지 마세요. 다음과 같이 해야 합니다:

- 정확한 결과를 얻기 위해 앱의 릴리스 빌드 변형을 선택하세요. 디버그 빌드 대신
- Android Studio의 실행 버튼 근처에 있는 3점 메뉴를 클릭하세요
- 그런 다음 "오버헤드 낮은 상태로 앱 프로파일링"을 선택하여 앱을 실행하세요
- 이제 앱이 완전히 로드될 때까지 대기한 후 녹화를 중지하세요
- 마지막으로 Profiler의 저장 아이콘을 사용하여 추적을 내보낸 다음 Perfetto Web UI로 가져오세요

<img src="/assets/img/2024-06-23-MakingShadowfaxAndroidApp40faster_2.png" />

<div class="content-ad"></div>

퍼페토에 추적 파일을 로드하면 화면에 수많은 색상이 나타나더라도 놀라지 마세요. 그 모든 것을 다룰 필요는 없습니다.

# 4.1 퍼페토에서 시작 시간 지표 찾기

![이미지](/assets/img/2024-06-23-MakingShadowfaxAndroidApp40faster_3.png)

“startup”을 검색하면 앱 시작 시간의 시각화를 볼 수 있습니다. 초록 막대를 클릭한 후 키보드에서 ‘M’을 누르세요. 이렇게 하면 그 막대의 시작과 끝을 표시할 수 있습니다. 지금은 이에만 집중하고 나머지는 그냥 잡음입니다.

<div class="content-ad"></div>

# 4.2 원인을 찾아보세요

지금 시작 시간 막대 아래에 패키지 이름을 찾아 확장하세요. 그래프의 표시된 영역에만 집중하고 메인 스레드를 보세요. x축에 각 함수의 지속 시간이 나올 것이며, y축에 중첩된 막대가 있는 경우 중첩된 함수를 의미합니다.

먼저 수평으로 긴 막대부터 살펴보세요. 그 막대가 가장 많이 소요된 시간을 나타낼 것입니다. 예상보다 시간이 더 많이 소요된 함수가 어떤 것인지 확인하고 기록해보세요. 그런 다음 상위 4~5개의 주범을 최적화할 수 있을 것입니다.

![그래프](/assets/img/2024-06-23-MakingShadowfaxAndroidApp40faster_4.png)

<div class="content-ad"></div>

프레임은 16밀리초 내에 렌더링되어야 60fps를 달성할 수 있어요

팁: WASD 키를 사용하여 이동하세요. perfetto UI에 대해 더 읽고 싶다면 여기를 확인해보세요. 문서는 약간 오래되었지만 핵심 원칙은 같아요.

# 5. 토끼굴에 들어가다

긴 지속 시간을 가진 함수들이 있을 수 있지만 결과물이 없을 수도 있어요.

<div class="content-ad"></div>

저기요, 이 BaseActivity의 onResume() 메서드가 엄청 길어요. 하지만 이 그래프만으로는 무엇이 시간을 차지하는지 알 수 없어요.

마지막 부분에야 전체 시간의 1/4를 차지하는 중첩 함수가 보이네요.

그럼 이제 어떻게 해볼까요?

![이미지](/assets/img/2024-06-23-MakingShadowfaxAndroidApp40faster_5.png)

<div class="content-ad"></div>

트레이싱을 더 추가해야 돼요. 자바 및 코틀린에서 사용 가능한 트레이싱 라이브러리로 쉽게 할 수 있어요. 자세한 내용은 여기를 참조할 수 있지만 코드 일부를 살펴봐요:

```js
class MyClass {
  fun foo(pika: String) {
    trace("MyClass.foo") {
    // 기존의 함수 로직이 여기에 있어요...
    }
  }
}
```

이제 MyClass.foo가 Perfetto에 나타날 거예요. 따라서 디버그 중인 함수 내 모든 중첩 함수에 대한 트레이싱을 추가한 후 트레이스를 다시 기록하고 Perfetto에서 분석해보세요. 각 라이프사이클 함수에 대해 이 과정을 계속 반복하세요.

![이미지](/assets/img/2024-06-23-MakingShadowfaxAndroidApp40faster_6.png)

<div class="content-ad"></div>

# 6. 우리의 솔루션

이 연습을 통해 매우 명확한 근본 원인과 목표를 얻었습니다. 이제 각 원인을 하나씩 해결할 때가 왔습니다. Perfetto와 SysTrace의 관측을 바탕으로 다음과 같은 다양한 최적화가 실행되었습니다:

## 6.1. 홈 화면 레이아웃 최적화 (-15% 시작 시간)

ConstraintLayout으로 뷰를 중첩하지 않고 hidden views 대신 viewstubs를 사용하여 최대 600ms까지 감소하였습니다. 이 접근 방식은 기본적으로 숨겨져 있고 사용자 상호작용 후에만 표시되는 뷰의 불필요한 측정 및 확장을 방지합니다. view stubs에 대해 더 알아보기.

<div class="content-ad"></div>

200~300 밀리초가 걸리는 mapView가 onResume()이 호출된 후에 init으로 이동되었기 때문에 핵심 UI에서 주문 수와 상태를 표시한 후에 로드됩니다.

```js
private fun HomeFragment.lazyLoadMap() {
  Handler(Looper.getMainLooper()).post {
    // 이 Runnable은 메인 스레드가 HomeFrag를 확장한 후에만 실행됩니다.
    initMap()
  }
}
```

# 6.2 MainActivity 최적화하기 (-5% 시작 시간)

반복된 성능 테스트에서 LinearLayout이 Fragment에 대한 컨테이너로만 사용되는 MainActivity에서 ConstraintLayout보다 성능이 더 우수했습니다. 실제로 LinearLayout으로 변경하면 MainActivity가 특히 웜 스타트에서 2배 더 빨리 시작되었습니다.

<div class="content-ad"></div>

## 6.3 MainActivity SDK들의 지연 로딩(-10% 시간)

퍼페토는 MainActivity에서 초기화되는 단일 3rd party SDK가 onCreate() 지속 시간의 70%를 차지하고 있다는 것을 보여주었습니다. 우리는 앱 시작 시 즉시 필요하지 않았기 때문에 백그라운드에서 이를 지연로딩하기 시작했습니다.

## 7. 실제 성능 결과

이러한 솔루션을 여러 릴리스를 통해 출시한 뒤, 시작 시간의 90분위가 3.5초에서 거의 2초 미만으로 점진적으로 개선되어, 놀라운 42%의 감소를 보았습니다.

<div class="content-ad"></div>

![image](/assets/img/2024-06-23-MakingShadowfaxAndroidApp40faster_7.png)

더 많은 병목 현상을 찾고 앱 속도를 높이는 데 노력하고 있습니다. 이를 통해 파트너들이 더 생산적일 수 있도록 돕고 있습니다.

프레펫토를 사용하여 병목 현상을 발견하는 것은이 프로젝트에 있어 중요했으며, 각 문제를 해결함으로써 얼마나 많은 성능 향상을 이끌어낼 수 있는지를 알기에 자신감을 갖게 되었습니다.

앱 시작 시간을 개선하는 데 도움을 준 Burhan & Vishnu에게 감사드립니다.