---
title: "Postgres RDS가 빅데이터 솔루션 구현에 적합하지 않은 이유 우리가 겪은 문제 및 해결방안 포함"
description: ""
coverImage: "/assets/img/2024-06-23-WhyPostgresRDSdidntworkforusandwhyitwontworkforyouifyoureimplementingabigdatasolution_0.png"
date: 2024-06-23 22:37
ogImage:
  url: /assets/img/2024-06-23-WhyPostgresRDSdidntworkforusandwhyitwontworkforyouifyoureimplementingabigdatasolution_0.png
tag: Tech
originalTitle: "Why Postgres RDS didn’t work for us (and why it won’t work for you if you’re implementing a big data solution)"
link: "https://medium.com/@mkremer_75412/why-postgres-rds-didnt-work-for-us-and-why-it-won-t-work-for-you-if-you-re-implementing-a-big-6c4fff5a8644"
---

# 배경

우리는 마케팅 효과를 통계적으로 측정하는 스타트업을 만들고 있었습니다. 이를 위해 온라인 사용자 행동에 대한 많은 데이터를 수집해야 했습니다. 모든 클릭, 모든 입력 필드, 모든 액션을 모으는 작업이 필요했습니다. 데이터는 포스트그레스에 저장되었고, 최종 사용자들은 UI를 통해 상기 (때로는 매우 큰) 데이터 집합을 분석하는 대시보드 및 보고서를 실행했습니다. 여러 테넌트를 지원하는 멀티 테넌트 SAAS 애플리케이션을 구축하고 있기 때문에 데이터는 수백 개의 테넌트별로 수집되고 저장되었습니다. 이 시스템은 확장 가능하고 성능이 좋아야 했습니다. 우리는 AWS RDS 관리형 포스트그레SQL로 시작했습니다.

![이미지](/assets/img/2024-06-23-WhyPostgresRDSdidntworkforusandwhyitwontworkforyouifyoureimplementingabigdatasolution_0.png)

# EBS와 관련된 큰 문제: 처리량 투명성

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

EBS의 처리량을 정확하게 측정하는 것은 거의 불가능합니다. AWS가 제공하는 지표는 지연 시간과 IOPS입니다. 이 둘 다 처리량을 측정하는 방법은 제공하지 않습니다. 디스크에 10GB의 크기를 가지는 시간 순서 테이블(예: 2000만 개 행)을 저장하고 테이블을 스캔하려고 할 때(어떤 이유로든 - 예를 들어 순차 스캔이 필요한 경우) 디스크에서 데이터를 스캔하는 데 얼마나 오랜 시간이 걸릴지 이해하려면 처리량을 정확히 측정해야 합니다.

# 폭발과 제한된 IOPS - 사태의 시작

위의 쿼리와 같은 작업들은 IO 크레딧을 빨리 소진시킵니다. 우리는 더 많은 IOPS를 원하기 때문에 어떻게 해야 할까요? 당연히 스토리지 볼륨을 늘려서 작은 볼륨으로부터 얻을 수 있는 3000 IOPS보다 높은 10000 IOPS 기본선을 얻을 수 있도록 합니다. 하지만 백업 비용이 증가하게 되고, 전체 볼륨을 지불하게 되므로 비용이 증가합니다. 비용은 증가하는데 성능이 거의 개선되지 않는다 - 이러한 상황은 더 악화됩니다.

우리는 또한 캐싱을 위해 더 많은 메모리를 얻기 위해 인스턴스 크기를 계속해서 늘렸습니다. 모든 것을 메모리에 맞게 만들면 DB를 빠르게 만드는 것이 쉽습니다. 불행히도 PG v10에서 제공되는 더 큰 병렬 처리는 IO 제한 때문에 실제로 많은 혜택을 가져다주지 않았습니다.

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

쓰루풋을 측정하는 데 문제가 있는 내용은 여기에서 자세히 설명되어 있습니다:

[https://www.datadoghq.com/blog/aws-ebs-latency-and-iops-the-surprising-truth/](https://www.datadoghq.com/blog/aws-ebs-latency-and-iops-the-surprising-truth/)

# Aurora

그래서 우리는 Aurora로 마이그레이션했습니다. 표준 PostgreSQL 대비 5~7배의 성능 향상을 약속받을 수 있다는 것은 놓칠 수 없는 이점입니다. 그러나 이러한 성능 메트릭은 pgbench를 기반으로 하고 있습니다 (PostgreSQL의 경우). 대규모 데이터 집합 및 데이터 마이닝 작업에 대한 성능 지표로는 적합하지 않지만 수많은 병렬 클라이언트와 함께 트랜잭션의 속도(또는 지연 시간)를 측정하고 싶은 OLTP 시스템이 있다면 이러한 결과는 훌륭합니다.

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

비용이 줄어들었는데 오히려 올라갔어요.

이 글에서는 IOPS 및 요금 청구 가능한 IOPS에 관한 내용이 있습니다. https://forums.aws.amazon.com/message.jspa?messageID=835303#835303

다시 한 번 가장 큰 답답함은 처리량의 정확한 측정을 얻는 데 있었습니다. 단순히 tack_io_timing을 사용하여 스캔된 데이터 양을 시간으로 나눈 것을 보면, 대규모 데이터셋에 대한 성능이 SSD 속도처럼이 아니라 자기 디스크와 유사하다는 것을 알 수 있습니다.

부담스럽게 느껴지는 것 중 하나는 지원과의 끝없는 시간입니다. 지원 직원은 항상 매우 지식이 풍부하고 도움이 되려는 자세였지만, 항상 쿼리 조정이나 DB 조정에 차질이 생겼습니다. 다시 말해서 처리량에 관한 문제로 돌아왔죠. "귀하의 작업 부하에 고려해 보기에는 이 서비스를 선택하는 것이 좋지 않다."는 말을 듣고 싶어했지만, 결국 우리 스스로 그것을 해결해야 했습니다.

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

# 해결책: 직접 만들어보세요

저희에게는 해결책이 꽤 간단했습니다. 관리형 데이터베이스 서비스를 사용하지 말고 EC2에서 직접 인프라를 구축했어요.

저희는 상대적으로 저사양의 EC2 인스턴스(저희는 RDS에서 4XL을 실행 중이었습니다) xl과 2xl을 사용했어요.

중요한 점은 마스터와 스탠바이 레플리카 간의 READ 및 WRITE 워크로드를 분리하는 것이었고, 이를 위해 WAL-G(https://github.com/wal-g/wal-g)를 사용하여 최신 상태를 유지하는 것이었습니다.

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

WRITE 노드는 데이터를 처리하기만 하므로 EBS가 충분했지만 ZFS를 사용하여 EBS 처리량을 2배로 늘릴 수 있었습니다.

READ 노드의 경우 일시적인 NVMe 드라이브와 다시 한 번 ZFS를 사용하여 성능을 향상시켰습니다.

결과가 자연스럽게 이야기해줍니다:

비용이 $11K에서 $2100으로 감소했습니다(9월에 예상되는 첫 번째 RDS 부담이 없는 완전한 한 달을 기준으로).

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

아주 길고 끝이 나지 않을 쿼리도 몇 시간이 걸리거나 완전히 시간초과될 정도로 실행되던 것이 몇 초만에 완료되었어요.

유닉스 인스턴스에서 명령 줄 도구(zpool iostat)를 사용하여 실제 처리량을 측정할 수 있습니다.

# 결론

인스턴스를 시작하고 백업과 복구에 대해 걱정하지 않고 한 번 클릭하면 편리할 수 있지만, 이는 저희의 경우에는 비용과 성능 측면에서 비용이 발생합니다. TB 또는 GB 데이터를 처리하는 데 실제 속도가 필요하다면 베어 메탈을 사용하십시오. 베어 메탈을 사용할 수 없다면 NVMe 드라이브가 장착된 EC2를 사용하는 것이 다음으로 좋은 방법입니다. NVMe 드라이브가 장착된 인스턴스를 사용하여 AWS에서 자체 Postgres 클러스터를 시작하는 방법에 대한 조리법을 제공하는 부분 두 번째를 읽어보세요.
