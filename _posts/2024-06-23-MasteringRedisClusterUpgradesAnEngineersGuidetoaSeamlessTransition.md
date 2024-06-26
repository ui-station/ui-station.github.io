---
title: "엔지니어를 위한 Redis 클러스터 업그레이드 마스터하기 무중단 전환 가이드"
description: ""
coverImage: "/assets/img/2024-06-23-MasteringRedisClusterUpgradesAnEngineersGuidetoaSeamlessTransition_0.png"
date: 2024-06-23 20:36
ogImage:
  url: /assets/img/2024-06-23-MasteringRedisClusterUpgradesAnEngineersGuidetoaSeamlessTransition_0.png
tag: Tech
originalTitle: "Mastering Redis Cluster Upgrades: An Engineer’s Guide to a Seamless Transition"
link: "https://medium.com/@chaitanyawaikar1993/mastering-redis-cluster-upgrades-an-engineers-guide-to-a-seamless-transition-5d026f9e03a5"
---

# 소개

AWS ElastiCache에서 Redis 클러스터를 업그레이드하는 것은 지속적인 성능과 보안을 보장하기 위해 꼼꼼한 계획과 실행이 필요한 중요한 작업입니다. 여러 번의 Redis 업그레이드를 직접 이끈 경험을 통해 해당 블로그 게시물에서 과정을 반복 가능한 일련의 단계로 정리했습니다. Redis 클러스터 업그레이드의 복잡성에 대해 자세히 살펴보며, 최선의 사례, 세부적인 전개 전략, 그리고 필수적인 롤백 계획에 대해 다룰 것입니다.

이 과정은 준비 단계, 전개 단계 및 전개 후 단계로 자연스럽게 나눌 수 있습니다.

# 준비 단계

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

이 부분은 아마도 가장 많은 시간을 소비하지만 매우 중요한 단계로 볼 수 있습니다. 주요 고려 사항은 다음과 같습니다:

# 1. 호환성 매트릭스

Redis 및 선택한 클라이언트 라이브러리(예: Java용 Jedis)에서 제공한 호환성 매트릭스를 철저히 검토해보세요. 이 매트릭스는 버전 간의 잠재적인 호환성 문제를 강조해줍니다.

![MasteringRedisClusterUpgradesAnEngineersGuidetoaSeamlessTransition_0](/assets/img/2024-06-23-MasteringRedisClusterUpgradesAnEngineersGuidetoaSeamlessTransition_0.png)

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

Redis 자체의 릴리스 노트 및 클라우드 제공 업체 문서(예: AWS 문서)를 면밀히 살펴보세요. 이를 통해 새로운 기능, 버그 수정, 사용 중단 예정 기능 및 패치 베이스가 있는 변경 사항을 알 수 있습니다.

## 2. 릴리스 노트

Redis 및 클라이언트 라이브러리의 릴리스 노트를 주의 깊게 읽는 것이 매우 중요합니다. 이는 새로운 기능, 버그 수정, 사용 중단 예정 기능 및 변경 사항에 대한 중요한 정보를 제공합니다. 이러한 릴리스 노트는 Redis 문서에서 찾을 수 있습니다.

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

모든 Redis 호환 서비스를 제공하는 클라우드 제공업체는 변경 사항을 요약하여 제공합니다. 예를 들어 AWS 문서는 데이터베이스 엔진에 대한 중요한 변경 사항을 잘 요약합니다.

# 3. 스테이징 환경에서 테스트

프로덕션 설정과 유사한 스테이징 환경을 만듭니다. 다음을 포함합니다:

- 인스턴스 유형 및 크기: 프로덕션과 동일한 AWS 인스턴스 유형 및 크기 사용.
- 네트워크 구성: VPC, 서브넷 및 보안 그룹 설정을 미러링.
- 데이터 양: 동일한 양의 데이터로 스테이징 환경을 채웁니다. 이것은 실제 업그레이드를 시작하기 전에 데이터 백업을 수행하는 데 걸리는 시간을 이해하는 데 매우 중요합니다.

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

스테이징 환경이 생성되면 새로운 Redis 버전과 함께 애플리케이션을 테스트하기 시작하는 시간입니다. 테스트는 다음 세 가지 하위 섹션으로 나눌 수 있습니다.

![이미지](/assets/img/2024-06-23-MasteringRedisClusterUpgradesAnEngineersGuidetoaSeamlessTransition_2.png)

- 기능 테스트
  새 버전과 완벽하게 작동하는지 확인합니다. 애플리케이션 코드베이스에 있는 모든 Redis 명령이 호환되는지 확인해야 합니다.
  Lua 스크립트가 있는 경우 모든 사용자 정의 스크립트가 올바르게 실행되는지 확인하는 것이 중요합니다.
- 성능 테스트
  Redis는 일반적으로 지연 시간이 민감한 중요한 응용 프로그램에서 사용됩니다. 따라서 Gatling이나 Apache JMeter와 같은 부하 테스트 도구를 사용하여 업그레이드된 Redis 클러스터의 성능을 측정하는 것이 중요합니다.
  Redis 엔진 업그레이드 중에는 더 높은 성능을 제공하는 경우가 있습니다. 이를 확인하기 위해 철저한 부하 테스트를 수행할 수 있습니다. 또한 클라이언트 라이브러리 설정을 조정하여 더 나은 성능을 얻을 수 있습니다.
- 신뢰성 테스트
  신뢰성 테스트는 제품 환경에 영향을 미치기 전에 잠재적인 문제를 발견하기 위한 것입니다. 장애 및 스트레스 시나리오를 시뮬레이션하여 업그레이드된 Redis 클러스터가 실제 환경에서 직면할 도전에 견딜 수 있는지 확인할 수 있습니다.
  애플리케이션의 Redis 클라이언트가 노드 장애, 네트워크 중단 및 클러스터 재구성을 처리하는 방식을 테스트합니다. 이러한 클라이언트가 신속하게 새로운 노드에 연결하고 데이터 불일치를 우아하게 처리하는지 확인합니다.

Redis 클라이언트 라이브러리는 노드 IP를 캐시합니다. 다중 샤딩 및 다중 노드 Redis 클러스터에서는 클라이언트 라이브러리에서 새로 고치고 새로운 노드에 빨리 연결할 수 있도록 클라이언트 라이브러리에서 새로 고치고 새로운 노드에 빨리 연결할 수 있도록 새로 고치고 새로운 노드에 빨리 연결할 수 있도록 새로 고치고 새로운 노드에 빨리 연결할 수 있도록 새로 고치고 새로운 노드에 빨리 연결할 수 있도록 새로 고치고 새로운\_IWPAৰ새로 운용할 수 있도록 해야 합니다.

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

# 4. 위험한 명령어 비활성화

대부분의 경우, CPU가 Redis에서 병목 현상을 일으킵니다. 따라서 이러한 블로킹 명령어의 사용을 비활성화하거나 제한하는 것이 중요합니다. 여기에는 두 가지 범주가 있습니다.

- 블로킹 명령어 — KEYS\* , 대량 데이터 세트에서의 SORT 등
- 비용이 많이 드는 명령어 — HGETALL , 범위가 큰 ZRANGE

Redis는 명령어 이름을 변경하는 기능을 제공하며, FLUSHALL과 같은 명령어를 \_DO_NOT_USE_FLUSHALL과 같이 애매한 이름으로 변경할 수 있습니다. ElastiCache를 사용하면 명령어 동작에 영향을 미치는 매개변수를 수정할 수 있습니다.

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

예를 들어, 고가의 명령을 실행할 수 있는 느린 클라이언트를 자동으로 연결 해제하는 타임아웃 설정을 조정할 수 있습니다.

## 5. 모니터링 및 로깅

### 가. 향상된 모니터링

중요한 메트릭인 CPU 사용량, 메모리 사용량 및 네트워크 처리량과 같은 주요 메트릭을 추적하기 위해 향상된 모니터링을 활성화하세요. AWS Elasticache는 Redis 클러스터에 대해 약 40여 가지 메트릭을 제공하며, 이러한 메트릭은 클러스터의 전반적인 상태를 파악하는 데 도움을 줍니다.

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

## b. 로깅 및 알림

Redis 명령에 대한 로깅을 설정하고 중요 지표에 대한 알림을 구성하십시오. 이는 문제를 신속히 식별하고 진단하는 데 도움이 됩니다.

업그레이드 단계에서는 Engine Logs와 Slow Logs를 활성화하고 이러한 로그 위에 경고를 설정하는 것이 중요합니다.

# 롤아웃 단계

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

항목을 준비해 둔 후, 상세한 전개 계획을 작성하는 시간이에요. 서비스가 적거나 없는 시간대에 일정을 잡아 방해를 최소화 해보세요. 중요한 애플리케이션의 경우, 업그레이드 창을 신중하게 선택해야 해요. 왜냐하면 업그레이드를 위한 데이터 백업 과정은 상당한 Redis CPU 자원을 소비할 수 있기 때문이죠.

꼭 기억해야 할 몇 가지 포인트가 있어요 —

- 전체 백업: 시작하기 전에 Redis 클러스터 데이터의 전체 백업을 만들어 보세요. 스냅샷이나 선택한 지속성 메커니즘을 사용해보세요. 백업 및 복원 시간을 제품 환경에서 측정하여 업그레이드 창에 미치는 전반적인 영향을 이해하세요.
- 데이터 일관성 확인: 백업 이후, 기본 및 레플리카 노드 간에 데이터 크기 및 키/값 쌍이 일치하는지 확인해보세요. 이 단계는 진행하기 전에 데이터 무결성을 보장하기 위해 중요해요.
- 문서 버전: 철저한 롤백 절차를 문서화해보세요. 이에는 백업에서 복원 및 이전 버전에서 클러스터 다시 시작에 대한 상세 단계가 포함돼야 해요. 롤백을 예상대로 작동하는지 확인하기 위해 롤백의 시뮬레이션을 진행하세요.
- 클라이언트 연결: 애플리케이션 팟이 새 Redis 클러스터에 연결할 수 있는지 확인해보세요. 클라이언트 라이브러리가 DNS 항목을 캐시하는 경우, 최신 연결 정보를 얻기 위해 Refresh Rate를 줄이거나 팟의 Rolling Restart를 실행해야 할 수도 있어요.
- Thundering Herd 완화: Redis가 데이터베이스의 캐시 역할을 한다면, 캐시가 무효화되거나 실패할 경우 "Thundering Herd" 효과에 대비해야 해요. 이는 데이터베이스를 압도할 수 있어요. 데이터베이스 용량을 평가하고, 부담이 더 큰 작업을 처리할 수 있도록 필요에 따라 확장을 고려하세요.
- 단계별 업그레이드: 새 버전의 안정성을 테스트하기 위해 중요하지 않은 노드(예: 읽기 레플리카)부터 업그레이드를 시작해보세요. 모든 것이 순조롭게 진행되면, 점진적으로 나머지 노드를 업그레이드하면서 프로세스 전반에 걸쳐 애플리케이션이 계속 작동하는지 확인하세요.
- 커뮤니케이션: 유지보수 창에 대해 모든 이해당사자 및 애플리케이션/서비스 이용자에게 알리세요. 이를 통해 다른 팀이 즉각적으로 발생한 문제나 비정상적인 현상을 보고할 수 있게 돕습니다.

전개 자체에는 여러 가지 방식이 있을 수 있어요:

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

- 노드별 업그레이드: 중요한 애플리케이션의 경우, 시간이 오래 걸리지만 빠른 문제 감지가 가능한 한 번에 한 노드씩 업그레이드하는 방법이 있습니다.
- 클러스터 재생성 (AWS ElastiCache): AWS 및 다른 클라우드 공급업체는 종종 클러스터 재생성 전략을 사용합니다. 이는 이전 클러스터의 스냅샷을 새 버전이 적용된 새 클러스터에 적용하고 데이터 동기화를 보장한 다음 DNS를 새 클러스터로 전환하는 과정을 포함합니다.

## 중요 사항:

전략의 선택은 특정 요구 사항, 리스크 허용 수준 및 애플리케이션의 성격에 따라 다릅니다.

# 롤아웃 후 단계

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

롤아웃 후기 단계는 주로 모니터링과 문서화에 초점을 맞추며, 업그레이드된 Redis 클러스터의 안정성과 성능을 보장합니다. 클러스터를 단계별로 업그레이드한 후(먼저 읽기 레플리카, 그 다음 비중요 샤드, 마지막으로 주 노드) 다음 단계가 중요합니다:

- 지속적인 모니터링
  업그레이드된 클러스터를 긴 기간 동안 높은 수준으로 모니터링하여 예기치 못한 문제를 신속하게 감지하고 해결하세요. 이전 Redis 클러스터에서 역사적인 메트릭을 내보내고 저장하는 것을 강력히 권장합니다. 이를 통해 현재 성능을 이전 기준선(예: 1개월, 3개월 전)과 비교함으로써 이상 징후를 탐지할 수 있습니다.
- 문서화
  철저한 문서화가 중요합니다. 업그레이드 과정 전체를 기록하고 타임라인, 수행된 구체적인 단계, 그리고 마주친 어려움을 기록하세요. 이 문서화는 미래 업그레이드에 대한 소중한 참고 자료가 되며, 데이터 백업 기간, 동기화 프로세스, 그리고 현재 데이터 양과 관련된 기타 주요 메트릭에 대한 통찰을 제공합니다.

# 결론

Redis 클러스터를 업그레이드하는 것은 원활한 전환과 응용 프로그램의 안정성과 성능을 유지하기 위해 구조화된 방식이 요구됩니다. 이 글에서 제안된 전략과 모범 사례를 따르면, 위험을 크게 줄이고 새로운 Redis 기능 및 향상 사항을 성공적으로 통합할 수 있습니다.

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

여러 번의 Redis 업그레이드 경험과 통찰을 공유했지만, 언제나 더 배우고 싶습니다. 추가 팁, 제안 또는 실제 예시가 있으시면 아래 댓글을 남겨주세요. 여러분의 피드백은 우리 모두가 Redis 업그레이드 프로세스를 더 견고하게 만들고 발전시키는 데 도움이 될 수 있습니다.
