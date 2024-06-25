---
title: "자바 가상 스레드가 플랫폼 스레드보다 느린 이유는 무엇인가요"
description: ""
coverImage: "/assets/img/2024-06-19-WhyaremyJavavirtualthreadsslowerthantheplatformthreads_0.png"
date: 2024-06-19 21:54
ogImage:
  url: /assets/img/2024-06-19-WhyaremyJavavirtualthreadsslowerthantheplatformthreads_0.png
tag: Tech
originalTitle: "Why are my Java virtual threads slower than the platform threads?"
link: "https://medium.com/ascend-developers/why-are-my-java-virtual-threads-slower-than-the-platform-threads-74612a1587f3"
---

혹시 팀 애플리케이션 서비스에 기능을 구현하려다가 예상대로 되지 않은 적이 있나요? 저도 정확히 그런 일이 발생했어요!

# 제가 겪은 일을 설명하기 전에 가상 스레드를 살펴보죠

"가상 스레드는 대량 처리 동시 응용 프로그램의 작성, 유지 관리 및 관찰 노력을 크게 줄이는 가벼운 스레드입니다."
— 참고: (https://openjdk.org/jeps/444)

플랫폼 스레드의 문제는 I/O 작업 완료를 기다리는 시간입니다. 해당 스레드가 다른 작업을 수행할 수 없어 기본적으로 아이들 상태가 됩니다. 이는 많은 동시 요청을 처리하는 응용 프로그램에게 특히 비효율적입니다.

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

위의 사진에서 보여지는 시나리오에서 플랫폼 스레드가 두 번 블록되어 프로그램이 크게 느려질 수 있습니다. 이는 흔히 발생하는 일입니다. 그러나 이러한 상황을 해결하기 위해 리액티브 프레임워크라 불리는 비차단 솔루션이 등장했습니다.

Spring Boot은 Project Reactor를 기반으로 한 Spring WebFlux 스택을 제공합니다. Spring WebFlux를 사용하는 개발자들은 전체 개발 프로세스를 수정해야 합니다.

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

2017년, 자바 엔지니어들은 문제를 해결하기 위해 "프로젝트 룸"을 시작했습니다. 결국, 그들은 "가상 스레드"를 개발했습니다.

![이미지](/assets/img/2024-06-19-WhyaremyJavavirtualthreadsslowerthantheplatformthreads_2.png)

가상 스레드의 주요 개념은 단일 가상 스레드가 여러 플랫폼 스레드에 장착될 수 있다는 것입니다. 가상 스레드는 블로킹 I/O 작업에서 오류가 발생할 때 현재 플랫폼 스레드에서 해제됩니다. 이 절차는 가상 스레드가 I/O 작업이 완료될 때까지 대기하는 동안 플랫폼 스레드가 다른 작업을 처리할 수 있도록 합니다. I/O 작업이 완료되면, 가상 스레드는 새로운 사용 가능한 플랫폼 스레드에 장착되어 실행을 계속할 수 있습니다.

![이미지](/assets/img/2024-06-19-WhyaremyJavavirtualthreadsslowerthantheplatformthreads_3.png)

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

한 팀원이 점심 시간에 (블로킹 I/O)다른 플랫폼 스레드로 이동하여 소포 트럭이 중단되지 않고 여정을 계속할 수 있도록 하는 택배 회사의 전략을 상상해 보세요. 이 방식은 물류 운영 시간을 단축시키고 생산성을 높이며 회사를 경쟁력이 더 뛰어나게 만들었습니다.

플랫폼 스레드 간으로 이동하여 마운트 및 언마운트함으로써 가상 스레드는 I/O 작업이 완료될 때까지 블록되지 않을 수 있습니다. 이는 플랫폼 스레드를 더 효율적으로 사용할 수 있게 하고 전반적인 대기 시간을 줄입니다.

간단히 말하면, 단 한 줄의 코드 변경으로 서비스 성능을 향상시킬 수 있는 가장 간단한 방법입니다.

Spring Boot에서 Java 가상 스레드를 활성화하려면 "application.properties"에 이 구성을 추가하면 됩니다.

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
spring.threads.virtual.enabled = true; // 클릭 한 번으로 쉽게 설정 !!
```

참고: 이 설정은 JDK 21 이상과 Spring Boot 3.2 이상을 필요로 합니다.

# 성능 비교를 위한 테스트 API 빌드

```js
@RestController
public class PerformanceTestController {

    @GetMapping("/test")
    public ResponseEntity<String> testPerformance() throws InterruptedException {

        System.out.println("Sleeping ...");
        // CPU 바인드되지 않는 작업 시뮬레이션, 3초 동안 sleep
        Thread.sleep(3000);

        return ResponseEntity.ok("테스트 완료");
    }

}
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

# Apache HTTP 서버 벤치마킹 도구를 사용하여 서비스 성능을 측정할 수 있어요

다양한 부하로 테스트하고 결과를 분석하여 개선할 수 있어요.

여기 명령어에요.

```js
ab -c 300 -n 1000 -r “http://localhost:8443/test"
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

- -c 300: 동시 요청 수입니다. 이 경우에는 서버를 동시에 폭격하기 위해 가상 사용자 [300]명이 시뮬레이션됩니다.
- -n 1000: 최종적으로 실행될 총 요청 수입니다 [1000].
- -r “http://localhost:8443/test”: 벤치마크 테스트 대상 URL입니다.

테스트를 시작합니다. 첫 번째는 기존 방식을 사용하고, 두 번째는 가상 스레드가 활성화됩니다.

가상 스레드 강력해요! 플랫폼 스레드보다 약 20% 빠른 속도로 동일한 작업을 완료합니다 (65.21 RPS 대 54.80 RPS). 그렇다면, 왜 제목에서 가상 스레드가 플랫폼 스레드보다 느리다고 말했을까요?

# 실세계에서의 가상 스레드

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

우리 팀의 API 분석

- GET 방식 사용 중
- 요청 방식 검증
- 데이터베이스에서 데이터 쿼리
- RestClient를 통해 다른 서비스로 요청 보내기
- 데이터베이스에 데이터 업데이트/저장

성능 테스트 결과

```js
플랫폼 스레드: 587.24 TPS, 평균 응답 시간은 56.52ms
가상 스레드: 502.80 TPS, 평균 응답 시간은 66.54ms
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

가상 스레드가 플랫폼 스레드보다 느리다고 테스트 결과에서 나왔다니, 무슨 실수를 한 걸까요?

# 핀닝, 발동한 숨겨진 함정

![이미지](/assets/img/2024-06-19-WhyaremyJavavirtualthreadsslowerthantheplatformthreads_4.png)

캐리어 스레드는 가상 스레드가 현재 실행 중인 플랫폼 스레드를 정의하는 또 다른 용어입니다. 핀닝은 가상 스레드가 플랫폼 스레드에 매핑되어 있는 상태를 말하며, 캐리어 스레드에 붙어서 떨어질 수 없는 상태를 묘사합니다. 이는 가상 스레드의 상태를 힙 메모리에 저장할 수 없기 때문에 발생합니다. 핀닝된 스레드는 다른 스레드가 동일한 플랫폼 스레드를 사용하는 것을 막습니다. 몇 가지 가능한 원인을 살펴봅시다.

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

- 동기화된 블록 또는 메서드: 한 번에 한 스레드만 들어갈 수 있습니다. 다른 스레드들은 현재(실행 중인) 스레드가 나갈 때까지 차단됩니다. 경합 조건을 방지하고 데이터의 정확한 상태를 유지하기 위해 공유 리소스에 중단되지 않은 액세스가 필요합니다.

```js
class MyService {
  public void calculateFee() {
    synchronized (this) {
      ...
    }
  }
}
```

- 네이티브 메서드: 이 메서드를 사용하면 다른 언어의 코드(C 또는 C++ 등)를 Java 프로젝트에 통합할 수 있습니다. 네이티브 메서드는 Java 가상 머신 내에서 호출되지만 Java의 제어 범위 밖에서 실행됩니다.

MyService.java

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

```java
public class MyService {
  public static void main(String[] args) {
    System.out.println(calculateFee(5, 3));
  }
  public static native int calculateFee(int a, int b);
  static {
    // Loads the native library from file named "nativemethod.c".
    System.loadLibrary("nativemethod");
  }
}
```

nativemethod.c

```c
#include <jni.h>

JNIEXPORT jint JNICALL Java_calculateFee (JNIEnv *env, jobject obj, jint a, jint b) {
  return a * b;
}
```

- 외부 함수: 다른 프로그래밍 언어로 작성된 함수로서, Foreign Function Interface (FFI)를 통해 가상 스레드에 노출된 함수입니다. FFIs는 다른 언어로 작성된 코드들이 함께 동작할 수 없는 상황에서 사용됩니다.

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

```java
public interface PythonFunctions extends Library {
  /**
   * 이 정적 final 필드는 `PythonFunctions` 인터페이스의 싱글톤 인스턴스를 생성합니다.
   * 이는 `Library.getInstance` 메서드에서 인스턴스를 검색하고 이 인터페이스 유형으로 캐스팅합니다.
   */
  PythonFunctions INSTANCE = (PythonFunctions) Library.getInstance("python_functions");
  doubled calculateFee(doubled a, doubled b);
}
```

# 다음 JVM 매개변수를 옵션으로 사용하여 고정된 스레드를 추적하세요

- -Djdk.tracePinnedThreads=full: 고정된 상태에서 스레드가 고정되어 있을 때 완전한 스택 추적을 출력하며, 네이티브 프레임과 모니터를 보유한 프레임을 강조합니다.
- -Djdk.tracePinnedThreads=short: 문제가 있는 프레임만을 포함하여 출력을 제한합니다.

로그에는 가상 스레드 내에서의 메서드 호출의 스택 추적이 표시됩니다. 이 스레드는 JDBC(Java Database Connectivity) 및 특히 MySQL 연결과 상호 작용하는 메서드를 실행합니다.

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

주목할 점은 이러한 메서드 호출 중 몇 가지가 `= monitors:1` 표기를 포함하고 있어서 가상 스레드가 이 코드 실행 블록 내에서 계속 고정되어 있는 것을 나타낸다.

# 버그 #110512 기여: synchronized를 ReentrantLock으로 교체

마침내 깨달았어요. 저는 사용 중인 MySQL Connector/J 버전 8에서 가상 스레드를 지원하지 않는다는 것을.

다음 단계로 나아가는 내 선택지입니다.

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

- MySQL Connector/J 버전 9.0.0 이상으로 업그레이드하세요: 이 버전은 가상 스레드가 데이터베이스 상호 작용을 최적으로 수행하도록 합니다.
- 새 라이브러리 탐험: 새 라이브러리를 찾아 새로운 코드를 작성하고 테스트해 보세요. 도전적이며 상당한 노력이 필요할 수 있습니다.

# 한 발 물러서 두 발 앞으로!

플랫폼 스레드에서 가상 스레드로의 전환을 통해 성능을 향상시키려는 시도는 "피닝"과 관련하여 예상치 못한 제약으로 인해 속도가 느려지는 문제가 발생했습니다. 결과적으로, 우리는 다시 플랫폼 스레드를 사용하도록 돌아갔습니다.

지금은 성능을 개선하기 위한 대안 전략을 탐색하면서 버전 9.0.0의 릴리스를 기다리고 있습니다. 이는 새로운 기술을 도입하는 데 따르는 도전을 강조하며, 실행 전 철저한 테스트의 중요성을 강조합니다.
