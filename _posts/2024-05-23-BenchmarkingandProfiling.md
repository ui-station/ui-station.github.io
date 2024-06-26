---
title: "성능 평가와 프로파일링"
description: ""
coverImage: "/assets/img/2024-05-23-BenchmarkingandProfiling_0.png"
date: 2024-05-23 12:46
ogImage:
  url: /assets/img/2024-05-23-BenchmarkingandProfiling_0.png
tag: Tech
originalTitle: "Benchmarking and Profiling"
link: "https://medium.com/@backslashzero/benchmarking-and-profiling-6fd2b428f6f0"
---

이 안내서는 모든 언어와 프레임워크에 적용됩니다. 일부 제안 사항은 루비에 맞게 조정되어 있습니다.

# 벤치마크

벤치마크는 정의된 프로시저에 의해 사용되는 자원 또는 시간의 합성 측정입니다.

합성 측정 - 프로덕션에서 발생하는 조건을 완벽하게 재현하지는 않습니다. 대략적인 추정 값을 제공하는 것이 목적입니다.

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

자원 또는 시간, 절차 - 우리는 메서드가 실행되는 데 얼마나 오랜 시간이 걸렸는지, 초당 몇 번의 반복이 실행되었는지 또는 얼마나 많은 객체가 생성되었는지와 같은 정보를 얻을 수 있습니다 - 이 모든 것은 메서드나 컨트롤러 액션과 같은 정의된 절차에 의해 가능합니다.

다른 범위 수준을 가진 3 가지 종류의 벤치마킹 -

- Micro - 이러한 벤치마크는 작은 기능을 테스트합니다. 많은 반복이 실행됩니다. 예: 코드 한 줄이 100만 번 실행됩니다. 이 코드 라인은 자체적으로 더 많은 메서드를 호출할 수 있습니다. 이 개별 LOC에 대한 최적화는 LOC가 많은 곳에서 호출되지 않는 경우에는 유용하지 않을 수 있습니다.
- Macro - 이러한 벤치마크는 비교적 큰 기능을 테스트합니다. 상대적으로 적은 반복이 실행됩니다. 예: 컨트롤러 액션 또는 서비스 객체가 수천 번 실행됩니다. 이곳에서의 최적화는 유용할 수 있습니다. 결과는 벤치마킹 결과에서 확인할 수 있습니다.
- App - 이러한 벤치마크는 훨씬 큰 기능을 테스트합니다. 매우 적은 반복이 실행됩니다. 예: 전체 기능이 수백 번 실행됩니다. 이곳에서의 최적화는 유용할 수 있습니다. 그러나 최적화의 효과는 많은 코드를 실행하므로 벤치마킹 결과에서는 보이지 않을 수 있습니다.

루비 세계의 몇 가지 벤치마킹 젬은 - benchmark-ips와 benchmark-ipsa가 있습니다.

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

일부 벤치마킹 매개변수 —

- 워밍업 시간 — 벤치마킹 이전에 데이터를로드하고 캐시를 채우면서 코드가 안정 상태에 도달하는 데 필요합니다. 안정적인 결과를 얻기 위해 코드를 충분히 워밍업하십시오. 예를 들어, CRuby는 JRuby나 TruffleRuby보다 훨씬 빠르게 안정 상태에 도달합니다. 따라서 후자의 경우 더 긴 워밍업 시간이 필요할 수 있습니다.
- 벤치마킹 시간 — 실제 벤치마킹이 실행될 시간을 결정합니다. 분산을 낮추기 위해 충분히 길어야 합니다. 분산이 높은 경우 벤치마킹 시간을 늘리면 도움이 될 수 있습니다.

팁: 실제로 중요한 것만 측정하도록 합니다. 벤치마킹 중인 코드를 정확히 알고 있는지 확인하십시오. 벤치마킹 블록 외에 필요하지 않은 코드는 모두 제외하도록 합니다.

팁: 쓴 모든 벤치마크를 /benchmarks 폴더와 같은 위치에 저장하여 손쉽게 접근할 수 있도록 합니다.

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

# 프로파일링

프로파일은 프로시저의 여러 서브루틴에 의해 사용된 자원의 상대적 소비를 **단계별로** 기록한 것입니다.

단계별 계산 - 초당 몇 번의 반복이 실행되었는지와 같이 누적 또는 단일 값 대신 우리는 많은 정보를 얻습니다. 예를 들어, 우리는 1번째 줄에서 10%의 시간을 보내고 2번째 줄에서 5%를 사용했으며, 이와 같은 방식으로 매우 세부적인 수준에서 정보를 얻을 수 있습니다.

상대적 소비 - 100밀리초의 시간을 소비했다는 벤치마크가 하는 것처럼 절대 숫자를 제공하지 않습니다. 대신 백분율과 같은 상대적 측정치를 제공합니다.

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

리소스 — 우리는 메모리나 시간과 같은 다양한 리소스를 프로파일링할 수 있어요.

서브루틴 — 프로시저의 서브루틴을 프로파일링할 거에요. 이는 Ruby와 같은 프로그래밍 언어의 메서드를 가리킬 수 있어요. 예를 들어 프로시저 내 각 메서드에 얼마나 많은 시간이 소요되었는지를 확인할 수 있어요.

프로파일링 결과 — "무엇이 느린가요?". 이 결과는 상세하게 제공하려고 애쓰지만, 어플리케이션의 성능을 저하시키기 때문에 상용 프로파일러인 New relic, Scout, Skylight과 같은 제품은 앱에 큰 영향을 주지 않으면서도 충분한 데이터를 제공하기 위해 절충할 거에요.

깊은 결과를 얻으려면, 프로파일링 환경이 거의 제품과 동일하게 작동해야 해요. 특히 데이터 양을 고려할 때 말이죠. 여기서 깊은 프로파일링을 활성화할 수 있어요.

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

개발 환경에서 프로덕션과 유사한 동작을 얻기 위해 몇 가지 설정이 있습니다. Ruby on Rails에서 다음과 같은 조건문 뒤에 이 설정들을 넣는 것이 좋습니다. 예를 들어, RAILS_ENV가 "PROFILE"인 경우에만 이 변경 사항을 적용하십시오.

![이미지](/assets/img/2024-05-23-BenchmarkingandProfiling_0.png)

거의 모든 프로파일러에서 시간을 측정하는 두 가지 모드가 있습니다 —

- CPU 시간: 이 모드는 CPU 사이클을 기준으로 시간을 측정합니다. 이는 IO 대기 및 슬립과 같은 작업이 해당 프로세스의 CPU 사이클 동안 발생했기 때문에 이러한 프로필에 나타나지 않습니다.
- WALL 시간: 이는 시계를 기준으로 합니다. 따라서 IO 대기 및 슬립과 같은 모든 것이 나타납니다.

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

사용 사례에 따라 때로는 CPU 시간을 사용하는 것이 좋을 수도 있고 때로는 WALL 시간을 사용하는 것이 좋을 수도 있습니다.

프로파일러에는 두 가지 종류가 있습니다 —

- 통계적 — 사용 가능한 스택 프레임의 일부를 샘플링합니다. 따라서 프로세스 실행을 X 밀리초마다 중단하여 스택 프레임의 스냅샷을 찍은 다음 프로세스를 계속 실행합니다. 그런 다음 그러한 스택 프레임이 프로파일로 집계됩니다.
- 추적 — 언어에 훅을 걸어두는 방식입니다. 메소드를 호출할 때마다 관련 카운터를 증가시키고 해당 메소드가 실행되었다는 사실을 기록합니다.

그래서 추적 프로파일러는 발생하는 모든 일을 기록할 수 있습니다. 반면에 통계적 프로파일러는 전체 데이터 중 1% 정도만 처리할 수도 있습니다.

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

루비 세계에서 주로 사용하는 주요 프로파일러는 2가지가 있어요 —

- Stackprof — 이것은 통계적입니다.
- Ruby-prof — 이것은 추적적입니다.

memory_profiler를 사용하여 루비 세계에서 메모리 프로파일을 작성하세요.

루비 세계에서 memory_profiler와 stackprof와 상호 작용하는 가장 좋은 방법은 rack-mini-profiler를 사용하는 것입니다.

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

얼마나 좋게 측정하더라도 결국 하루 끝에는 모든 프로덕션 시나리오를 비프로덕션에서 완벽히 재현할 수는 없습니다.

프로덕션 환경에서 성능을 측정하는 방법을 따라보세요 —

1. 프로덕션 지표 읽기 — 예를 들어, 조사를 통해 검색이 너무 느린 것을 발견했다고 가정해봅시다.
2. 핫스팟 찾기 위한 프로파일링 — 어디에서 문제가 발생하는지 정확하게 찾기 위해 프로파일링을 수행하세요. 어떤 작업이 가장 느린지 찾아보세요. 예를 들어, 검색 메서드가 가장 느리다고 가정해봅시다.
3. 벤치마크 생성 — 느린 부분을 찾아 해당 부분에 대한 벤치마크를 만들어보세요. 예를 들어, 어떤 것을 검색할 때 각 결과 라인을 렌더링하는 데 얼마나 걸리는지 파악해보세요.
4. 반복 — 벤치마크를 수행하고 문제를 찾았다면, 해결책을 구현하는 것은 꽤 간단합니다. 가장 느린 부분을 개선하기 위해 반복 작업하고 향상을 확인하세요. 예를 들어, 이전에 초당 1000개의 결과를 렌더링했다면, 이제 솔루션을 구현한 후 10000개의 결과를 벤치마크할 수 있습니다.
5. 배포 — 솔루션을 배포하세요.

이 노트는 Nate Berkopec의 강연을 바탕으로 준비되었습니다. 이 강연을 시청하려면 여기를 클릭하세요 —
