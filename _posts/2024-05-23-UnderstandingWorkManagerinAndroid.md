---
title: "안드로이드에서 Work Manager 이해하기"
description: ""
coverImage: "/assets/img/2024-05-23-UnderstandingWorkManagerinAndroid_0.png"
date: 2024-05-23 12:54
ogImage:
  url: /assets/img/2024-05-23-UnderstandingWorkManagerinAndroid_0.png
tag: Tech
originalTitle: "Understanding Work Manager in Android"
link: "https://medium.com/@riztech.dev/understanding-work-manager-in-android-aa970cbf654d"
---

![Work Manager](/assets/img/2024-05-23-UnderstandingWorkManagerinAndroid_0.png)

워크 매니저(Work Manager)는 안드로이드 젯팩(Android Jetpack)의 중요한 구성 요소로, 백그라운드 작업의 미룰 수 있고 보장된 실행을 간단하게 만들기 위해 설계되었습니다. 앱이 종료되거나 장치가 다시 시작되더라도 실행이 보장되어야 하는 작업을 수행해야 하는 경우, Work Manager는 견고하고 유연하며 효율적인 솔루션을 제공합니다. 다양한 안드로이드 버전에서 작동하는 통합 API를 제공하여 현대적인 안드로이드 개발에 필수적인 도구입니다.

## Work Manager의 주요 기능

- 보장된 실행: Work Manager는 앱이 종료되거나 장치가 다시 시작되더라도 백그라운드 작업이 완료되도록 보장합니다. 로그 업로드, 데이터 동기화, 파일 처리와 같은 작업에 특히 유용합니다.
- 미룰 수 있고 비동기적: 미룰 수 있고 비동기적으로 실행될 수 있는 작업에 대해 설계되었습니다. 이러한 작업은 즉시 실행되지 않아도 되지만 결국 완료되어야 합니다.
- 배터리 효율적: Work Manager는 JobScheduler, AlarmManager 및 BroadcastReceiver를 내부적으로 사용하여 API 수준과 제공된 제약 조건에 따라 가장 적합한 구현을 선택하여 배터리 수명을 최적화합니다.
- 제약 조건: 개발자는 네트워크 가용성, 충전 상태 및 작업이 실행되기 전에 충족해야 하는 저장소 조건과 같은 제약 조건을 지정할 수 있습니다. 이는 리소스를 효율적으로 관리하는 데 도움이 됩니다.
- 작업 연결: Work Manager를 사용하면 여러 작업을 연결하고 복잡한 작업 시퀀스를 관리할 수 있습니다. 이는 이전 작업이 완료된 후에 다음 작업이 시작되어야 하는 종속 작업에 유용합니다.
- 모니터링 기능:LiveData 또는 콜백 리스너를 통해 작업 상태를 모니터링하고 작업 실행에 대한 제어 및 통찰력을 제공할 수 있습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## Work Manager 시작하기

안드로이드 프로젝트에 Work Manager를 통합하려면 다음 단계를 따르세요:

1. 의존성 추가: build.gradle 파일에 필요한 종속 항목을 포함해야 합니다:

```js
implementation "androidx.work:work-runtime-ktx:2.7.1"
```

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

테이블 태그를 마크다운 형식으로 변경해주세요.

| Header One | Header Two | Header Three  |
| ---------- | ---------- | ------------- |
| Row 1      | Data 1     | Description 1 |
| Row 2      | Data 2     | Description 2 |

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

4. 제약 조건 처리: 작업에 제약 조건을 지정할 수 있습니다. 네트워크 연결이 필요한 경우와 같이:

```js
val constraints = Constraints.Builder()
    .setRequiredNetworkType(NetworkType.CONNECTED)
    .build()

val workRequest = OneTimeWorkRequestBuilder<MyWorker>()
    .setConstraints(constraints)
    .build()
WorkManager.getInstance(context).enqueue(workRequest)
```

## 고급 사용법

1. 주기적인 작업: 정기적으로 반복되어야 하는 작업에는 PeriodicWorkRequest을 사용하세요:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

```js
val periodicWorkRequest = PeriodicWorkRequestBuilder<MyWorker>(1, TimeUnit.HOURS)
    .setConstraints(constraints)
    .build()
WorkManager.getInstance(context).enqueue(periodicWorkRequest)
```

2. Chaining Tasks: Work Manager allows chaining multiple tasks sequentially:

```js
val workRequest1 = OneTimeWorkRequestBuilder<Worker1>().build()
val workRequest2 = OneTimeWorkRequestBuilder<Worker2>().build()

WorkManager.getInstance(context)
    .beginWith(workRequest1)
    .then(workRequest2)
    .enqueue()
```

3. Observing Work Status: You can observe the status of your work requests using LiveData:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

```js
WorkManager.getInstance(context).getWorkInfoByIdLiveData(workRequest.id)
    .observe(this, Observer { workInfo ->
        if (workInfo != null && workInfo.state.isFinished) {
            // 작업 완료 처리
        }
    })
```

# 안드로이드에서 Work Manager 사용하는 데 최선의 방법

Work Manager는 안드로이드 애플리케이션의 백그라운드 작업을 효율적으로 관리하는 강력한 도구입니다. 이를 효과적으로 활용하기 위해서는 특정한 최상의 방법을 준수해야 합니다. 이러한 관행은 작업이 효율적으로, 신뢰성 있게 실행되며 시스템 리소스에 미미한 영향을 미칠 수 있도록 보장합니다.

## 1. 적절한 유형의 Work Request 선택하기

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

**일회성 작업 요청 대 비주기적 작업 요청:**

- 한 번만 실행해야 하는 작업(예: 단일 파일 업로드 또는 특정 시간에 데이터 동기화)은 일회성 작업 요청을 사용합니다.
- 주기적으로 발생하는 작업(예: 정기적 데이터 동기화 또는 주기적 정리 작업)은 비주기적 작업 요청을 사용합니다.

## 2. 적절한 제약 조건 설정하기

제약 조건을 현명하게 사용하세요:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- 필요한 제약 조건만 설정하여 작업 수행이 불필요하게 지연되지 않도록 합니다. 일반적인 제약 조건으로는 네트워크 가용성, 장치 충전 상태 및 배터리 레벨이 있습니다.
- 예시:

```js
val constraints = Constraints.Builder()
    .setRequiredNetworkType(NetworkType.CONNECTED)
    .setRequiresCharging(true)
    .build()
```

## 3. 작업 실행 적절히 처리하기

긴 실행 시간이 걸리는 작업을 피하세요:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- 긴 실행 시간이 소요되는 작업을 작은 관리 가능한 조각으로 나눠보세요. 작업이 상당한 시간이 걸릴 것으로 예상된다면, 여러 작업 요청으로 분할하고 연결하는 것을 고려해보세요.

복잡한 작업에는 ListenableWorker를 사용하세요:

- 비동기 처리가 필요한 작업에는 Worker 대신 ListenableWorker를 사용하세요. 이를 통해 비동기 작업을 더 효율적으로 관리할 수 있습니다.

## 4. 작업 체인 관리

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

의존 작업 체인:

- 의존 작업을 연속적으로 실행하도록 작업 체이닝을 사용하십시오. 이는 한 작업의 출력이 다음 작업에서 필요한 워크플로우에 유용합니다.

```js
WorkManager.getInstance(context)
  .beginWith(workRequest1)
  .then(workRequest2)
  .enqueue();
```

체인된 작업에서 오류 처리:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- 워커 클래스에서 적절한 오류 처리를 구현하여 연쇄적으로 발생하는 실패를 관리하세요. 필요한 경우 Result.retry()를 사용하여 작업을 다시 예약하세요.

## 5. 작업 상태 관찰

작업 상태 관찰을 위해 Flow 사용:

- Kotlin의 Flow를 활용하여 작업 요청의 상태를 관찰하세요. 이는 LiveData보다 더 현대적이고 유연한 접근 방식을 제공합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

```kotlin
import kotlinx.coroutines.flow.collect
import kotlinx.coroutines.flow.map

val workInfoFlow = WorkManager.getInstance(context)
    .getWorkInfoByIdLiveData(workRequest.id)
    .asFlow()

lifecycleScope.launch {
    workInfoFlow.collect { workInfo ->
        if (workInfo != null && workInfo.state.isFinished) {
            // Handle the completion of the work
        }
    }
}
```

## 6. 작업 요청에 태그 사용

식별을 위해 태그 할당:

- 작업 요청에 태그를 할당하여 특정 작업을 쉽게 관리하고 쿼리할 수 있습니다. 특히 특정 작업 세트를 취소하거나 관찰하는 데 유용합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

```js
val workRequest = OneTimeWorkRequestBuilder<MyWorker>()
    .addTag("syncWork")
    .build()
```

태그를 사용하여 작업 취소할 수 있어요:

- 필요한 경우 태그별로 작업 요청을 취소하세요.

## 7. 고유한 작업 요청 처리하기

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

유효한 작업에 고유한 작업 사용하기:

- 중복 작업을 피하기 위해 고유한 작업 요청을 사용하세요. enqueueUniqueWork을 사용하면 특정 작업 요청의 인스턴스가 한 번에 하나만 실행됩니다.

```js
WorkManager.getInstance(context).enqueueUniqueWork(
  "고유작업이름",
  ExistingWorkPolicy.REPLACE,
  workRequest
);
```

## 8. 자원 사용량 최적화

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

리소스 영향 최소화:

- 시스템 리소스에 미치는 영향을 최소화하기 위해 적절한 제약 조건을 사용합니다. 예를 들어, 필요할 때만 네트워크 연결을 요구합니다.
- 장치에 부하를 줄이기 위해 재시도에 지수 백오프를 고려해보세요.

배경 최적화 활용:

- Work Manager의 내장 최적화 기능을 활용하여 배터리 및 네트워크를 고려한 스케줄링을 통해 효율적인 작업 실행을 보장하세요.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 9. 테스트 및 디버깅

작업 요청을 철저히 테스트하세요:

- 다양한 조건에서 작업 요청을 테스트하여 예상대로 작동하는지 확인하세요. 온라인이 아닌, 배터리 부족 또는 재부팅 중인 등 다양한 장치 상태를 포함합니다.

로깅 및 디버깅 도구 사용하기:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- 작업 실행 문제를 해결하기 위해 로깅 및 Work Manager의 디버깅 도구를 활용해보세요. adb shell dumpsys activity service를 사용하면 Work Manager의 내부 상태를 확인할 수 있습니다.

## 결론

Work Manager는 Android 애플리케이션에서 백그라운드 작업을 처리하는 필수 도구로, 앱이 실행되지 않을 때에도 일을 수행할 수 있는 일관적이고 신뢰할 수 있는 방법을 제공합니다. 제약 조건, 작업 체인, 주기적 작업과 같은 기능들을 통해 다양한 유즈케이스에 유연성을 제공합니다. Android 프로젝트에 Work Manager를 통합하면 백그라운드 작업 실행의 효율성과 신뢰성이 크게 향상될 수 있습니다.
