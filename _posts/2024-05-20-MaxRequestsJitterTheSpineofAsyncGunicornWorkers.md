---
title: "최대 요청 지터 - 비동기 Gunicorn 워커의 중추"
description: ""
coverImage: "/assets/img/2024-05-20-MaxRequestsJitterTheSpineofAsyncGunicornWorkers_0.png"
date: 2024-05-20 16:24
ogImage:
  url: /assets/img/2024-05-20-MaxRequestsJitterTheSpineofAsyncGunicornWorkers_0.png
tag: Tech
originalTitle: "Max Requests Jitter — The Spine of Async Gunicorn Workers"
link: "https://medium.com/@vengaligangadhar105/max-requests-jitter-the-spine-of-async-gunicorn-workers-02b9f1fa2c3d"
---

<img src="/assets/img/2024-05-20-MaxRequestsJitterTheSpineofAsyncGunicornWorkers_0.png" />

안녕하세요! 어느 날 갑작스럽게 기본 백엔드 서버가 다운되었어요. 이런 상황은 모든 소프트웨어 엔지니어가 두려워하는 일이지요. 다행히도 저희는 메인 백엔드 어플리케이션을 위해 두 개의 ECS 작업(각 작업을 서버로 생각하시면 돼요)를 구성해 두었어요. 우리의 Auto Scaling 그룹은 항상 두 개의 작업이 실행 중인 상태를 유지하도록 설정되어 있어, 서버들은 자동으로 다시 시작되었답니다. 그러나 문제는 계속 발생했고 때로는 간헐적으로 일어났어요.

멈춘 서버 작업의 로그를 조사하기 시작했어요. 관련 정보가 거의 없는 것을 발견했지만, Gunicorn이 oom-killer를 호출한 흔적을 발견했습니다.

낮은 메모리가 문제일까요?

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

Gunicorn이 UTC 오전 05:43:20에 out-of-memory (oom) 킬러를 호출했습니다. 이전에 UTC 오전 05:42:36에 ECS에서 작업이 건강하지 않다고 판단되었습니다. 낮은 메모리로 인해 Gunicorn이 요청을 처리하지 못했을 가능성이 있습니다. 이로 인해 요청에 응답을 중단하고 일정한 시간 초과 또는 버퍼 기간 후 oom 킬러를 호출했을 수 있습니다.

양 서버에서 'top' 명령어를 통해 메모리가 잠재적으로 문제가 될 수 있다는 것을 시사했습니다. 이는 Gunicorn이 oom-killer를 호출하여 더 확신시켰습니다. 이러한 요소들로 인해 문제가 메모리 부족임이 확인되었습니다.

초반에는 많은 양의 요청들이 서버의 메모리를 고갈시킬 수도 있다고 가정했습니다. 따라서 주어진 기간 내에 서비스된 요청 수를 추적하는 Athena 로그를 조사하기 시작했습니다. 그러나 결과는 놀라웠습니다. 서버가 멈출 때 API 요청은 없었고 건강 상태 확인 API 호출만 있었습니다. 저희의 설정에는 건강 상태 확인 API를 5초마다 호출하는 건강 상태 확인 구성이 포함되어 있습니다. 연속해서 두 번의 실패가 발생하면 호스트가 건강하지 않은 상태로 표시됩니다.

어딘가 메모리 누수가 있는 건가요?

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

우리는 메모리 사용량이 증가하는지 모니터링하기 위해 특정 요청 수를 초당 전송하는 군사 스크립트를 실행했습니다.

애플리케이션이 동기 워커를 사용하여 실행되었을 때, 메모리 사용량은 330MB 정도로 안정적이었습니다.

그러나 애플리케이션이 비동기 Gevent 워커를 사용하여 실행될 때, 메모리 사용량이 175MB에서 560MB로 증가했습니다. 군사 스크립트를 후속 실행할 때, 메모리 사용량은 550–560MB 정도로 안정적으로 유지되었습니다.

이 동작은 워커가 요청을 처리한 후에도 메모리를 소비하고 해제하지 않았음을 시사했습니다.

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

Gunicorn 설명서에서는 메모리 누수의 영향을 완화하기 위해 최대 요청 수에 제한을 설정하는 것을 권장합니다. max-requests 매개변수는 워커가 다시 시작되기 전에 처리할 수 있는 최대 요청 수를 정의합니다. 또한, max-requests-jitter 매개변수를 설정하여 워커 다시 시작을 무작위화하여 모든 워커가 동시에 다시 시작되는 것을 방지할 수 있습니다.

https://docs.gunicorn.org/en/stable/settings.html#max-requests

max-requests 및 max-requests-jitter로 테스트

- max-requests를 100, max-requests-jitter를 30으로 설정합니다.
- 이렇게 하면 k가 무작위 지터 값인 경우에 워커는 100 + k 요청 후에 다시 시작됩니다. 이 값은 [0, 30] 범위 내에서 지연된 다시 시작을 보장합니다.

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

관찰:

# 새로운 문제의 탄생

![이미지](/assets/img/2024-05-20-MaxRequestsJitterTheSpineofAsyncGunicornWorkers_1.png)

구니콘 구성을 업데이트하는 도중에 max-requests 100을 구현하는 중요한 변경 사항이 도입되었습니다. 이 업데이트로 인해 서버 응답 시간에 예기치 않은 영향이 발생했으며, 우리의 메트릭에서 이러한 수치가 상당히 증가하는 것을 보고했습니다. 이 변경 사항은 중요하지 않았으며, 우리 제품 환경에서 여러 p99 경보가 트리거되어 잠재적인 문제의 명백한 표시가 되어 추가 조사와 해결이 필요하다는 것을 나타냈습니다.

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

## 이러한 경고에 대응하여, 우리는 max_requests와 max_requests jitter의 다양한 조합을 활용한 포괄적인 테스트 세트를 시작했습니다.

![](/assets/img/2024-05-20-MaxRequestsJitterTheSpineofAsyncGunicornWorkers_2.png)

우리가 결과를 조사하면서, 특정 구성이 눈에 띄었습니다. max_requests=250 및 max_requests_jitter=15의 조합은 테스트한 다른 구성과 비교했을 때 가장 유리한 결과를 보여주었습니다. 이 설정은 Maximum Memory 사용량이 468 mb로, 우리가 테스트한 다른 구성보다 현저히 낮은 측정값을 보여주었습니다. 또한, 모든 구성 중 가장 낮은 p99 결과인 1345를 나타냈습니다. 실패한 요청의 비율은 5/3330으로, 다른 구성의 비율과 일치했습니다.

그러나, 테스트에서 유망한 결과를 얻었지만, 이러한 설정을 우리의 프로덕션 환경에 배포해도 p99 문제를 해결할 수 없었습니다. 이 차이로 인해 우리는 문제를 깊이 있게 들여다보고, 가능한 원인과 해결책에 대해 더 고찰해야 했습니다.

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

한 가설은 작업자가 특정 수의 요청을 처리한 후 (이 경우 250+k를 초과하는 어떤 수), 재시작을 시작할 것이라는 것이었습니다. 이 재시작은 처음에는 무해해 보이지만 약간의 지연을 초래할 수 있었고, 이후에는 요청 대기열의 누적으로 이어질 수 있습니다.

우리는 max_requests와 max_requests_jitter 매개변수에 대해 더 큰 숫자를 사용하여 실험을 실행하기로 결정했습니다: max_requests=3000 max_requests_jitter=50.

우리의 수정된 가설은 작업자가 특정 수의 요청을 처리한 후 (구체적으로 250+k보다 큰 수), 재시작을 거쳐야 한다는 것을 제안했습니다. 이 재시작은 약간의 지연을 유발할 수 있어 대기 중인 요청이 늘어나게 될 수 있습니다. 우리는 250가 상대적으로 작은 수이므로, 작업자들이 자주 재시작되어 재시작 프로세스 중 간헐적인 지연이 발생한다고 추정했습니다.

이에 대처하기 위해, 우리는 max_requests를 더 큰 수로 증가시키기로 결정했습니다. 이는 작업자 재시작 빈도를 줄이고, 동시에 이전의 메모리 누수 문제를 다시 도입하지 않도록 하는 목적으로 수행되었습니다.

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

위 전략을 따라서 max_requests를 3000으로, max_requests_jitter를 50으로 증가시켰습니다. 이러한 조치의 목적은 빈번한 워커 재시작과 이로 인한 지연 문제를 피하는 것이었습니다. 이러한 새로운 설정으로 워커가 다시 시작되기 전의 최대 메모리 소비량이 600MB로 관측되었습니다. 이는 gunicorn이 메모리 임계값인 700MB에 도달한 후에만 out of memory (OOM)을 발생시키기 때문에 수용 가능한 수준으로 판단되었습니다. p99는 로드 테스트에서 max_requests = 250, max_requests_jitter = 15 구성에서의 1345ms 대비 728ms로 상당히 낮아졌습니다. 이 실험적인 접근법은 p99 문제와 메모리 문제를 효과적으로 해결하여, 서버 관리에서 계속해서 테스트, 가설 형성, 혁신적 문제 해결의 중요성을 입증했습니다.
