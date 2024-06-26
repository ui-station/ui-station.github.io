---
title: "롱혼Longhorn을 위한 최상의 관례"
description: ""
coverImage: "/assets/img/2024-05-17-BestPracticesforLonghorn_0.png"
date: 2024-05-17 18:24
ogImage:
  url: /assets/img/2024-05-17-BestPracticesforLonghorn_0.png
tag: Tech
originalTitle: "Best Practices for Longhorn"
link: "https://medium.com/@petolofsson/best-practices-for-longhorn-067d4ccb5fdd"
---

![image](/assets/img/2024-05-17-BestPracticesforLonghorn_0.png)

# 소개

Longhorn은 Rancher에서 설계하고 Kubernetes 클러스터의 Storage Classes를 관리하기 위해 만들어진 오픈 소스 분산 스토리지 시스템입니다.

Longhorn은 웹 UI 및 기타 도구로 설치 및 관리가 쉽게 설계되었습니다.

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

하지만, 클러스터 관리와 쿠버네티스 작업을 하는 모든 사람들은 대화에서 '쉽다'는 말을 듣기 쉽지 않다는 것을 알고 있습니다.

이 기사에서는 Longhorn과의 여정에서 모은 정보를 제가 아닌 다른 많은 사람들에게 유용할 수 있다고 생각하는 내용을 제공하겠습니다.

# 전제 조건

우선, 문서를 읽어보세요. 왜냐하면...

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

나의 어리석음 속에서, 나는 롱혼(Longhorn)을 다룰 수 있을 것이라고 생각했고, 말 그대로 황소의 뿔을 잡을 수 있을 것으로 생각했다. 내가 한 것처럼 하지 마세요. 스스로를 많은 시간과 고통으로부터 구해주세요. 문서를 읽고 나서, 롱혼의 작동 방식을 근본적으로 이해했기 때문에 문제 해결이 가속화되었어요.

여기 좋은 시작점이 있어요:
롱혼(Longhorn) - 아키텍처와 개념
롱혼(Longhorn) - 최선의 방법론

이제 우리는 스스로에게 질문해 봅시다: 우리가 분산 저장 시스템(Distributed Storage System)을 생성하길 원할 때 우리가 찾고 있는 것은 무엇인가요?

그것은 빠른 I/O, 혁신적인 백업 시스템, AI 디버깅 또는 어떤 반짝이는 새로운 기능이 될 수 있지만, 제 경우에는 간단합니다: 안정성과 신뢰성이 모든 분산 저장의 안내등표(Guiding Beacons)가 되어야 한다고 생각해요. 왜냐하면 핵심 데이터 손실은 어떤 클러스터에게든 가장 최악의 일이 일어날 수 있는 것이기 때문이에요.

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

이 문맥에서의 안정성은 저장 시스템이 작업 부하나 환경 조건에 관계없이 항상 예상대로 작동하는 것을 의미합니다. 이는 대량의 데이터를 처리하고 고가용성을 지원하며, 무거운 부하나 네트워크 분할 중에도 성능 수준을 유지하는 것을 포함합니다. 안정적인 시스템은 읽기, 쓰기 및 업데이트와 같은 데이터 작업이 예상치 못한 중단이나 실패 없이 발생하도록 보장합니다.

신뢰성은 시스템이 데이터 무결성을 보호하고 필요할 때 언제든지 데이터에 접근할 수 있도록 하는 능력을 말합니다. 이는 강력한 오류 검출 및 수정 메커니즘을 구현하고, 데이터를 여러 노드로 복제하여 데이터 손실을 방지하며, 하드웨어 장애나 기타 문제 발생 시 효과적인 복구 프로세스를 갖는 것을 의미합니다. 신뢰할 수 있는 시스템은 빠르게 데이터를 복원하여 장애가 발생한 경우에 데이터를 빠르게 복원하는 강력한 백업 및 재해 복구 계획을 포함합니다.

그리고 이 두 가지 단어를 떠올리며 분산 저장 시스템에서 안정성과 신뢰성을 실현하는 방법에 대한 통찰을 제공하기 위해 본 문서를 작성하고 있습니다.

# Best Practices

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

좋아요! 이제 문서를 읽어보았고 분산 저장 시스템에서 원하는 것을 알게 되었어요.

시작해볼까요?

이 가이드를 따라하면 Longhorn의 성능, 신뢰성, 그리고 Kubernetes 환경에서의 관리 기능을 최적화할 수 있을 거예요.

# 시작 지점

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

- 안정적인 빌드만 사용하세요. 더 나아가 최신 빌드를 사용하지 않는 것이 좋습니다. 아직 발견되지 않은 버그가 있을 수 있으니까요.
- Longhorn을 실행할 때, 네트워크가 바쁜 클러스터 트래픽으로 인해 발생하는 네트워크 중단으로 인해 마운트가 실패하는 시나리오가 발생할 수 있으므로, 전용 스토리지 네트워크에서 실행하는 것을 고려해보세요.

- 전용 스토리지 네트워크의 대안 또는 보완책은 순수 LonghornNodes를 가지는 것입니다. 이는 볼륨만 관리하며 다른 형태의 워크로드를 처리하지 않는 노드를 가지는 것을 의미합니다. 즉, NoSchedule 또는 NoExecute 효과를 적용하여 Longhorn에 전용 노드를 할당하는 것을 의미합니다.

# 노드 구성

- 고가용성을 위해 레플리카가 여러 노드와 가용 영역에 분산되어 있는지 확인하세요.
- Longhorn 컴포넌트에 해당하는 허용을 추가하여 전용 스토리지 노드에 스케줄되도록 할 수 있습니다.
- StorageClass가 필요에 따라 풀을 분할해야 하는 경우 노드 태그를 Longhorn에 추가하여 nodeSelector를 통해 해당 노드를 대상으로 할 수 있습니다.

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

# 저장 구성

- 최적의 디스크 성능을 위해 전용 SATA/NVMe SSD 또는 유사한 성능의 디스크 드라이브를 사용하세요.
- 노드 간에 10 Gbps 네트워크 대역폭을 보유하도록 하세요.
- 루트 디스크 대신 Longhorn 저장을 위한 전용 디스크를 사용하세요.

# 레플리카 개수

- 데이터 가용성을 달성하고 디스크 공간 사용량을 더욱 개선하며 시스템 성능에 덜 영향을 미치도록 기본 레플리카 수를 "2"로 설정하세요.
- 더 많은 레플리카는 읽기 집계를 늘리지만, 더 많은 공간과 네트워크 대역폭을 사용합니다.
- 기본값은 "3"이지만 Longhorn 공식 문서에서는 "2"라고 명시합니다. 특히 데이터 집약적인 애플리케이션에 매우 유용합니다.

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

# 볼륨 구성

- 볼륨에는 기본 ext4 파일 시스템을 사용하세요.
- 네트워크가 추가 트래픽을 처리할 수 없을 경우 노드를 불안정하게 만들 수 있으므로 가능하면 ReadWriteMany (RWX) 액세스 모드를 피하십시오. 가능한 경우 볼륨에는 ReadWriteOnce (RWO) 액세스 모드를 사용하세요. RWX와 RWO 사이의 성능 문제는 무시해도 좋습니다.
- 주기적으로 네트워크 부하로 인해 마운트할 수 없는 RWX 마운트가 있는지 확인하려면 mount | grep 10.43을 사용하세요. 이를 식별하는 데 cron 작업이 설정되어 있습니다(추후 기사에서 자세한 내용을 다룰 예정).

- 모든 노드 사이에 워크로드를 균형 있게 분산하는 것은 어려울 수 있습니다. Replica Node Level Soft Anti Affinity 및 Replica Auto Balance을 확인하여 시작하세요.

- 이것이 도움이 되지 않으면 워크로드를 균형 있게 분산하기 위해 Descheduler를 사용할 수 있습니다. 그러나 Descheduler는 공식 제품이 아니므로 연구를 진행해야 합니다.

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

# 데이터 지역성

- Longhorn StorageClasses의 기본 데이터 지역성으로 최선을 다하십시오. 최선을 다한다는 것은 Longhorn이 첫 번째 복제본을 컨트롤 엔진이 있는 동일한 노드에 배치하려고 시도한다는 것을 의미합니다. 첫 번째 볼륨을 동일한 노드에 배치하면 I/O 및 효율성이 향상될 수 있습니다. 기억해 주십시오. 최선을 다하는 것이 기본 설정이 아닙니다.
- 데이터 복제를 지원하는 응용 프로그램의 경우 strict-local 옵션을 사용하여 볼륨 당 복제본이 하나만 생성되도록합니다.
- NodeSelectors 또는 Taints를 사용하여 특정 storage-tagged 노드에 데이터 집중 작업을 스케줄링하십시오.

# 유지 보수 및 업그레이드

- Longhorn 호스트를 다시 부팅할 때, 동일한 노드의 복제본 중 하나를 의도적으로 삭제하여 다시 구축 프로세스를 트리거하고 복제본을 노드 간에 균형 있게 배치합니다.
- 라이브 볼륨과 관련된 문제를 피하려면 Longhorn을 업그레이드하기 전에 볼륨을 분리하는 것이 항상 바람직합니다.
- 안정적이고 지원되는 버전으로만 업그레이드하십시오.
- 일정한 간격으로 Longhorn UI에서 손상된 마운트를 확인하십시오. 건강한 복제본 수만큼 복제본 수를 업데이트하고 다시 정상 복제본 수로 업데이트하십시오.

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

# 스냅숏 및 백업

- 필요한 만큼만 유지하면서 주기적으로 시스템에서 생성한 스냅숏을 정리하십시오.
- 복제 기능이 있는 애플리케이션의 경우 정기적으로 모든 유형의 스냅숏을 삭제하십시오.
- 중요한 애플리케이션 볼륨을 위한 반복 백업 작업을 생성하십시오.
- 주기적인 시스템 백업을 실행하십시오.

# 출처

DevOps 이야기 LONGHORN: Best Practices — r/kubernetes Longhorn — Best Practices Rancher 포럼: Longhorn 재부팅시 Best Practice Harvester: Longhorn 디스크 성능 최적화를 위한 Best Practices Descheduling: 고가용성의 비밀

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

# 끝맺음

본 작업은 다른 사람들의 경험과 제 경험의 결합물입니다. 이 글을 함께 만들어준 분들에게 감사를 표합니다. 또한, 저는 Longhorn 전문가가 아니므로 궁극적으로는 여러분들이 직접 조사해 보시기 바랍니다.

행운을 빕니다!
