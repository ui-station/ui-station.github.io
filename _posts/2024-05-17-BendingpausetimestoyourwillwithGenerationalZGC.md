---
title: "국영세대 ZGC로 일시 중지 시간을 제어하세요"
description: ""
coverImage: "/assets/img/2024-05-17-BendingpausetimestoyourwillwithGenerationalZGC_0.png"
date: 2024-05-17 17:41
ogImage: 
  url: /assets/img/2024-05-17-BendingpausetimestoyourwillwithGenerationalZGC_0.png
tag: Tech
originalTitle: "Bending pause times to your will with Generational ZGC"
link: "https://medium.com/netflix-techblog/bending-pause-times-to-your-will-with-generational-zgc-256629c9386b"
---


Z Garbage Collector 세대의 놀라운 혜택과 그리 놀라운 혜택.

Danny Thomas가 쓴 JVM 에코시스템 팀의 기사

![GenerationalZGC](/assets/img/2024-05-17-BendingpausetimestoyourwillwithGenerationalZGC_0.png)

최신 장기 지원 릴리스인 JDK는 Z Garbage Collector를 위한 세대 지원을 제공합니다. Netflix는 주요 이유로 동시 가비지 수집의 중요한 혜택을 인한 것으로, JDK 21 및 그 이후부터 G1에서 Generational ZGC로 기본 전환했습니다.

<div class="content-ad"></div>

저희 핵심 스트리밍 비디오 서비스 중 절반 이상이 Generational ZGC와 JDK 21에서 실행 중이에요. 이제 제 경험과 얻은 혜택에 대해 이야기할 때가 좋아졌네요. Netflix에서 Java를 어떻게 사용하는지 궁금하시다면, Paul Bakker의 강연인 'Netflix가 정말로 Java를 사용하는 방법'이 좋은 시작점이 될 거예요.

# Tail Latency 감소

저희 GRPC와 DGS 프레임워크 서비스에서 GC 일시 중단은 Tail Latency의 중요한 원인 중 하나에요. 특히 GRPC 클라이언트와 서버에서는 시간 초과로 인한 요청 취소가 재시도, 헤징 및 폴백과 같은 신뢰성 기능과 상호작용하죠. 이러한 에러마다 요청이 취소되어 재시도되는 것인데, 이런 감소로 인해 전체 서비스 트래픽이 더욱 줄어들었어요: 

![이미지](/assets/img/2024-05-17-BendingpausetimestoyourwillwithGenerationalZGC_1.png)

<div class="content-ad"></div>

잦은 일시 정지의 소음을 제거하면 실제 지연 원천을 식별할 수 있어서 그렇지 않으면 소음에 감춰져 있을 수 있습니다. 최대 일시 중지 시간 이상치는 상당히 중요할 수 있습니다:

![image](/assets/img/2024-05-17-BendingpausetimestoyourwillwithGenerationalZGC_2.png)

# 효율

평가 결과가 매우 유망하게 나타난 후에도 ZGC의 채택을 교환할 것으로 예상했습니다: 스토어 및 로드 버리어, 스레드 로컬 핸드셰이크에서 수행되는 작업 및 응용프로그램 자원 경합으로 인한 GC로 인해 약간의 응용프로그램 처리량이 줄어드는 경우. 우리는 그것을 수용 가능한 교환이라고 생각했는데, 일시 정지를 피함으로써 그 부가적인 오버헤드보다 더 큰 이점을 제공했습니다.

<div class="content-ad"></div>

사실 우리의 서비스와 아키텍처에 대해 조사한 결과, 그러한 교환이 없다는 것을 발견했습니다. 특정 CPU 활용 목표에 대해 ZGC는 G1과 비교했을 때 평균 및 P99 대기 시간을 개선하면서 CPU 활용률도 동등하거나 더 나아진다는 것을 알 수 있었습니다.

우리가 관찰한 많은 서비스에서 요청률, 요청 패턴, 응답 시간 및 할당률의 일관성이 ZGC를 도와주지만, ZGC가 덜 일관된 워크로드를 처리하는 능력도 뛰어나다는 것을 확인했습니다 (물론 예외는 있습니다; 자세한 내용은 아래에서 다루겠습니다).

# 운영의 간편함

서비스 소유자들은 종종 너무 긴 일시 중지 시간에 대한 질문이나 튜닝 도움을 요청합니다. 우리는 주기적으로 많은 양의 힙 데이터를 새로 고치는 여러 프레임워크를 보유하고 있어서 외부 서비스 호출을 효율적으로 피하기 위해 이를 사용합니다. 힙 데이터의 주기적 갱신으로 G1을 깜짝놀라게 하는 이러한 작업은 기본 일시 중지 시간 목표를 크게 넘는 이상값에 이르게 합니다.

<div class="content-ad"></div>

아래는 Markdown 형식으로 테이블을 변환한 것입니다.


| Feature        | Description                                                                                                                                                                                  |
|----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Generational ZGC | Improved CPU utilization by nearly 10% compared to G1 for the same workload.                                                                                                                 |
| Hollow library | Used by half of the streaming video services for on-heap metadata, which led to the removal of array pooling mitigations and saved hundreds of megabytes of memory for allocations.       |
| Operational simplicity | ZGC's heuristics and defaults require no explicit tuning to achieve optimal results. Allocation stalls are rare and shorter than with G1.                                                        |
| Memory overhead | The long-lived on-heap data was the main reason we previously avoided non-generational ZGC, but generational ZGC helped improve the situation significantly.                                   |


<div class="content-ad"></div>

힙의 압축된 참조를 잃어버리기 때문에 64비트 객체 포인터가 필요한 색상 포인터 때문에 32G의 힙에서 압축된 참조를 잃어버리는 것이 가비지 콜렉터 선택의 주요 요소가 될 것으로 예상했습니다.

우리는 그것이 스탑-더-월드 GC들에 대해 중요한 고려 사항이긴 하지만, 작은 힙에서라도 할당 속도의 증가가 효율성과 운영 개선에 의해 상쇄된다는 점을 발견했습니다. 오라클의 Erik Österlund씨에게 감사드립니다. 동시 가비지 콜렉터에 있어서 색상 포인터의 직관적이지 않은 이점을 설명해 준 덕분에 우리는 초기 계획보다 ZGC를 더 폭넓게 평가하게 되었습니다.

대부분의 경우 ZGC는 응용 프로그램에 더 많은 메모리를 일관되게 제공할 수도 있습니다.

<div class="content-ad"></div>

ZGC는 힙 크기의 3%에 대한 고정 오버헤드가 있어 G1보다 더 많은 원시 메모리를 필요로 합니다. 몇 가지 예외를 제외하고는 더 많은 여유 공간을 위해 최대 힙 크기를 낮출 필요가 없었으며, 그것들은 평균 이상의 원시 메모리 요구가 있는 서비스들이었습니다.

참조 처리는 또한 ZGC에서 주요 콜렉션 시에만 수행됩니다. 직접 바이트 버퍼의 할당 해제에 특히 주의를 기울였지만, 지금까지는 영향을 볼 수 없었습니다. 이 참조 처리의 차이로 JSON 쓰레드 덤프 지원에서 성능 문제가 발생했지만, 이것은 프레임워크가 실수로 각 요청마다 사용되지 않는 ExecutorService 인스턴스를 생성하여 발생한 특이한 상황입니다.

# Transparent huge pages

ZGC를 사용하지 않더라도 거대 페이지를 사용해야 할 것입니다. 투명 거대 페이지가 그들을 사용하는 가장 편리한 방법입니다.

<div class="content-ad"></div>

ZGC는 힙에 대해 공유 메모리를 사용하며, 많은 Linux 배포판에서는 shmem_enabled를 'never'로 설정하여 -XX:+UseTransparentHugePages를 사용하는 경우 ZGC가 거대 페이지를 사용하지 못하게 만듭니다.

이렇게 변경된 부분을 제외하고 아무런 변경 없이 배포된 서비스가 있습니다. shmem_enabled가 'never'에서 'advise'로 변경되면 CPU 사용률이 저하되었습니다.

아래는 기본 구성입니다:

![이미지](/assets/img/2024-05-17-BendingpausetimestoyourwillwithGenerationalZGC_4.png)

저희의 기본 구성:

<div class="content-ad"></div>

- 힙의 최소 및 최대를 같은 크기로 설정합니다.
- -XX:+UseTransparentHugePages -XX:+AlwaysPreTouch을 구성합니다.
- 다음과 같은 transparent_hugepage 구성을 사용합니다:

```js
echo madvise | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
echo advise | sudo tee /sys/kernel/mm/transparent_hugepage/shmem_enabled
echo defer | sudo tee /sys/kernel/mm/transparent_hugepage/defrag
echo 1 | sudo tee /sys/kernel/mm/transparent_hugepage/khugepaged/defrag
```

# 어떤 워크로드가 적합하지 않았나요?

가장 좋은 가비지 컬렉터는 없습니다. 각각은 가비지 컬렉터의 목표에 따라 콜렉션 처리량, 응용프로그램 대기 시간 및 자원 이용을 교환합니다.

<div class="content-ad"></div>

G1 대비 ZGC에서 더 나은 성능을 보인 작업 부하들은 대부분 처리량을 중심으로 한 경향이 있습니다. 매우 변덕스러운 할당 속도와 예측할 수 없는 기간 동안 객체를 보유하는 장기 실행 작업들이 포함되어 있습니다.

한 가지 주목할만한 예시는 매우 변덕스러운 할당 속도와 많은 수의 고수명 객체를 가진 서비스였는데, 이는 G1의 일시 중지 시간 목표와 오래된 영역 수집 휴리스틱에 특히 적합했습니다. G1이 ZGC가 처리하지 못한 GC 사이클의 비생산적인 작업을 피할 수 있게 해주었습니다.

기본적으로 ZGC로 전환함으로써 응용프로그램 소유자들이 가비지 수집기의 선택에 대해 고민할 수 있는 좋은 기회가 제공되었습니다. G1을 기본으로 사용하던 여러 배치/준비 계산 사례들이 병렬 수집기로부터 더 나은 처리량을 얻었을 것입니다. 대규모 준비 계산 작업에서는 G1 대비 응용프로그램 처리량이 6~8% 향상되어 배치 시간이 한 시간 단축되는 것을 확인할 수 있었습니다.

# 직접 해보세요!

<div class="content-ad"></div>

가정과 기대를 의심하지 않으면, 우리는 10년 동안 우리의 운영 기본 설정 중 가장 영향력 있는 변화 중 하나를 놓치게 될 수 있습니다. 우리는 여러분께 직접 제너레이셔널 ZGC를 시도해 보라고 권장합니다. 여러분에게 우리가 놀란 만큼 놀라운 경험을 줄 수도 있습니다.