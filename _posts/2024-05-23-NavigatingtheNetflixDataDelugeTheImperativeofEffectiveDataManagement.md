---
title: "넷플릭스 데이터 쇄도를 탐색하며 효과적인 데이터 관리의 필수성"
description: ""
coverImage: "/assets/img/2024-05-23-NavigatingtheNetflixDataDelugeTheImperativeofEffectiveDataManagement_0.png"
date: 2024-05-23 14:00
ogImage:
  url: /assets/img/2024-05-23-NavigatingtheNetflixDataDelugeTheImperativeofEffectiveDataManagement_0.png
tag: Tech
originalTitle: "Navigating the Netflix Data Deluge: The Imperative of Effective Data Management"
link: "https://medium.com/@netflixtechblog/navigating-the-netflix-data-deluge-the-imperative-of-effective-data-management-e39af70f81f7"
---

By Vinay Kawade, Obi-Ike Nwoke, Vlad Sydorenko, Priyesh Narayanan, Shannon Heh, Shunfei Chen

소개

오늘날, 디지털 시대에 데이터가 전례없는 속도로 생성되고 있습니다. 예를 들어, Netflix를 살펴보겠습니다. 세계 각지의 Netflix 스튜디오에서 매년 수백 PB의 에셋이 생성됩니다. 콘텐츠는 텍스트와 이미지 시퀀스부터 소스 인코딩용 큰 IMF¹ 파일에 이르기까지 다양합니다. 때로는 생성된 프록시와 중간 파일도 있습니다. 스튜디오로부터 흘러나오는 이 방대한 데이터 양은 실질적인 통찰을 얻기 위한 효율적인 데이터 관리 전략에 대한 중대한 필요성을 강조합니다. 주목할 점은 모든 콘텐츠의 상당 부분이 미사용 상태로 남아있는 것입니다.

본 기사에서, 저희 미디어 인프라스트럭처 플랫폼 팀은 생산 데이터를 효과적으로 관리하기 위한 해결책인 Garbage Collector의 개발을 개요로 설명합니다.

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

The Magnitude of Data Generation

매주 전 세계의 수백 개의 Netflix 스튜디오에서 약 2 페타바이트의 데이터가 생산됩니다. 이는 텍스트, 이미지, 이미지 시퀀스, IMF 등 다양한 소스로부터 생성된 복합 데이터입니다. 이 규모의 데이터는 엄청납니다. 이 정보를 효과적으로 총정리하고 분석하는 것이 더 어려워지고 있습니다. 또한 이로 인해 저장 비용이 크게 증가했습니다. 우리는 역대 기록적인 저장 비용 증가률인 매년 50% 증가를 보고 있습니다. 동시에 내부 연구에 따르면 적어도 데이터의 40%가 사용되지 않고 낭비되고 있다고 합니다.

[이미지]

효과적인 데이터 관리의 필요성

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

데이터가 계속해서 증가함에 따라, 특히 스튜디오에서 수집된 데이터는 일반적으로 업로드 - 읽기 - 재업로드 - 미해당을 따르는 수명주기를 거칩니다. 효율적으로 관리하는 작업은 점점 중요성을 갖게 됩니다. Netflix에서는 미디어 인프라 및 저장 플랫폼 팀이 사용자 조치 또는 사전 구성된 수명 주기 정책에 따라 파일 객체를 모니터링하고 정리하는 확장 가능하고 비동기적인 Garbage Collector (GC)를 사용한 데이터 수명주기 관리 솔루션을 개발했습니다. GC는 팀의 Baggins 서비스의 구성 요소로 S3 위에 미디어 특정 사용 사례에 맞춘 내부 추상화 계층입니다.

아키텍처

상위 수준에서 수명주기 관리자를 설계하여 안전하게 삭제하거나 더 차가운 저장소로 이동할 수 있는 데이터를 수동으로 모니터링하고 제거합니다. Garbage Collection에 대해 "표시 및 정리"의 관점을 취합니다.

먼저, 우리는 모든 바이트를 AWS의 Simple Storage Service (S3)에 저장합니다. 그러나, S3의 각 파일에 대해 Baggins에 각 파일의 일부 메타데이터를 유지합니다. 이 메타데이터는 카산드라 데이터베이스에 저장되며 파일의 메타해시 목록 체크섬인 SHA-1, MD5 및 전체 파일에 대한 XXHash, 클라이언트 측 암호화된 객체를 위한 암호화 키와 같은 여러 필드가 포함되어 있습니다. S3에서 이러한 파일과 상호 작용하는 수백 개의 내부 넷플릭스 응용 프로그램이 있으며, 종종 여러 프록시, 파생물, 클립 등을 생성합니다. 이러한 응용 프로그램에는 프로모 미디어 생성, 마케팅 통신, 콘텐츠 인텔리전스, 자산 관리 플랫폼 등의 워크플로우가 포함됩니다. 이러한 응용 프로그램은 불필요한 파일을 필요할 때마다 삭제하거나 객체의 TTL을 사전 구성하여 파일이 자동으로 생성된 이후 일정 간격마다 삭제할 수 있습니다. 이 기간은 7일, 15일, 30일, 60일 또는 180일로 설정할 수 있습니다. 삭제 API를 통해 데이터베이스에서 해당 객체를 소프트 삭제로 표시하고 S3에서 해당 객체에 대한 액세스를 중지합니다. 이 소프트 삭제는 99% 분위수에서 삭제 API의 초당 15밀리초 미만의 초저지연을 유지합니다.

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

위에서 언급한 것처럼, 우리는 얼마 지나지 않아 '하드 삭제' 문제에 직면하게 될 것입니다. 이것은 삭제된 파일과 관련된 모든 흔적이 우리 시스템에서 제거되어야 함을 의미합니다. 이는 S3에서 바이트를 정리하고, Elastic Search에서 수행된 모든 색인을 삭제하며, 마지막으로는 Cassandra DB에서 모든 메타데이터를 삭제하는 것을 포함합니다. 우리는 다음과 같은 요구 사항을 가지고 시작했습니다.

- 삭제 API(소프트 삭제)를 낮은 대기 시간으로 유지합니다.
- 24시간 내내 수동 정리를 수행합니다.
- 온라인 데이터베이스에 영향을 미치기 시작하면 정리를 제한합니다.
- 정리가 급격히 증가해도 쉽게 확장할 수 있습니다.
- 매일 정리를 편리하게 시각화합니다.
- 아카이빙과 같은 다른 데이터 최적화 작업을 실행하기 위한 일반적인 프레임워크를 구축합니다.

시스템 하의 구조

다음은 시스템의 고수준 아키텍처입니다.

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

<img src="/assets/img/2024-05-23-NavigatingtheNetflixDataDelugeTheImperativeofEffectiveDataManagement_1.png" />

클라이언트 관점에서는 ~15밀리초의 대기 시간을 소비하는 삭제 API를 간단히 호출합니다. 그러나 이후에는 이 객체 및 모든 메타데이터를 적절하게 정리하고 모든 위치에서 제대로 정리하는 일이 많이 발생합니다. 이 다이어그램은 처음에 약간 복잡해 보일 수 있지만, 화살표를 추적하면 이해가 쉬워지고 전체 그림이 명확해집니다. 위의 주기는 매일 수행되며, 수동 및 자동화된 방식으로 작동하여 그 날과 지금까지 누적된 삭제 대상 데이터와 백로그를 정리합니다.

- 객체 키 가져오기: 모든 S3 객체는 Cassandra 데이터베이스에 해당 레코드가 있습니다. Cassandra 메타데이터 테이블의 항목들은 주요 참조 지점으로 기능합니다. 대응하는 행에 삭제 표시자를 추가하여 해당 행의 객체 키를 소프트 삭제하도록 표시합니다. 동일한 데이터베이스에는 버킷 수준 구성 정보를 보유하는 테이블도 있으며, 버킷 수준 사전 구성 TTL과 같은 정보를 포함합니다. 그러나 Cassandra는 파티션 키를 제공하지 않으면 TTL이 지정되거나 소프트로 삭제된 행을 필터링하는 것을 허용하지 않습니다. 또한, 메타데이터는 여러 테이블에 분산되어 있으며 정규화가 필요합니다. 여기서 Casspactor와 Apache Iceberg가 필요합니다.
- Casspactor: 이는 Netflix의 내부 도구로, Cassandra 테이블을 Iceberg로 내보내는 데 사용됩니다. Casspactor는 Cassandra의 백업 복사본을 기반으로 작동하며 온라인 데이터베이스에 대한 지연시간을 발생시키지 않습니다. Iceberg는 Netflix에서 만들어진 오픈 소스 데이터 웨어하우스 솔루션으로, 구조화된/구조화되지 않은 데이터에 대한 SQL과 유사한 액세스를 제공합니다. TTL이 지정된/소프트 삭제된 행을 얻기 위해 Cassandra를 쿼리하는 대신 Iceberg의 복사된 행을 대상으로 쿼리할 수 있게 되었습니다.
- 우리는 Netflix의 Workflow Orchestration 프레임워크 Maestro를 사용하여 매일 Iceberg를 호출하여 그 날 정리 작업 가능한 키를 필터링하는 일일 워크플로우를 설정합니다. 이 흐름은 버킷 수준 TTL 구성 및 객체 수준 삭제 표시자를 고려합니다.
- Maestro 워크플로우는 다른 임시 Iceberg 테이블에 결과를 집계하여 이후 작업의 소스로 사용합니다.
- 그 날 삭제해야 할 모든 관심 있는 행이 준비된 후, 우리는 데이터 이동 및 처리를 위한 홈그로운 솔루션을 사용하여 이를 모두 Kafka 큐로 내보냅니다. Iceberg 커넥터와 Kafka 싱크로 구성된 Data Mesh 파이프라인을 만들었습니다.
- 우리는 이 Kafka 주제를 청취하는 몇 개의 Garbage Collector (GC) Worker를 실행하고 각 행에 대해 삭제 작업을 수행합니다. 이러한 작업은 수평 확장 가능하며 Kafka 파이프라인에서 발생하는 스파이크나 백로그에 기반해 자동으로 확장됩니다.
- GC 워커는 객체의 완전한 정리를 처리합니다. 먼저 S3에서 모든 바이트를 삭제합니다. 이는 AWS S3에서 부과하는 5TB 최대 파일 크기 제한을 초과하는 경우 단일 파일 또는 다중 파일이 될 수 있습니다.
- 그런 다음 우리는 로컬 Elastic Search에서 이 소프트 삭제된 파일에 대한 모든 참조를 정리합니다.
- 마지막으로, 근무자들은 온라인 Cassandra 데이터베이스에서 이 항목을 제거하여 루프를 완료합니다. GC 워커의 자동 스케일링도 현재 부하 및 지연 시간에 대한 Cassandra 데이터베이스의 입력을 받습니다. 이러한 매개 변수들은 데이터 삭제 속도에 대한 제어된 속도 조절에 반영됩니다.
- 마지막 단계는 매일 삭제하는 파일 수와 크기에 대한 세부 내용을 제공하는 대시보드를 강화합니다. 이를 위해 Apache Superset을 사용합니다. 이를 통해 AWS 송장과 미래 비용을 예측하는 데 사용할 수 있는 상당한 데이터를 확보할 수 있습니다.

저장 통계

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

이렇게 우리 대시보드가 어떻게 보이는지 알려드릴게요,

![대시보드 스냅샷](/assets/img/2024-05-23-NavigatingtheNetflixDataDelugeTheImperativeofEffectiveDataManagement_2.png)

![대시보드 스냅샷](/assets/img/2024-05-23-NavigatingtheNetflixDataDelugeTheImperativeofEffectiveDataManagement_3.png)

필수 통찰력

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

- 데이터 정리 전략 수립을 우선시하세요. 데이터 정리 프로세스는 초기 설계의 중요한 부분이어야 합니다. 후속 고려 사항이 아닙니다. 시간이 흐를수록 데이터 정리는 적절히 관리되지 않으면 압도적인 작업으로 번질 수 있습니다.
- 지출을 지속적으로 모니터링하고 기록하세요. 비용 없는 부분은 없습니다. 데이터의 각 바이트는 금전적 영향을 가지고 있습니다. 따라서 저장된 모든 데이터에 대한 포괄적인 계획이 필수적입니다. 이는 특정 기간 이후 데이터를 삭제하거나 사용되지 않을 때 더 비용 효율적인 저장 계층으로 이전하거나, 적어도 미래 참조 및 의사 결정을 위해 무기한 보유를 위한 사유를 유지하는 것을 포함할 수 있습니다.
- 디자인은 다양한 작업과 환경에서 사용할 수 있는 유연성을 가져야 합니다. 활성 데이터부터 비활성 데이터 보관, 그리고 중간에 있는 모든 것까지 관리할 수 있어야 합니다.

**결론**

지수적인 데이터 증가 시대를 계속해서 탐색함에 따라 효과적인 데이터 관리의 필요성은 지나치게 강조될 수 없습니다. 데이터의 양을 다루는 것뿐만 아니라 품질과 관련성을 이해하는 것입니다. 포괄적인 데이터 관리 전략에 투자하는 기ꢣ치는 새로운 데이터 중심 환경에서 선도할 기업들이 될 것입니다.

용어

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

IMF (Interoperable Master Format): 이것은 오디오와 비디오 마스터 파일의 디지털 전송과 저장에 사용되는 표준화된 형식입니다. 더 많은 정보를 원하시면 이 개요를 방문해보세요.

MHL (미디어 해시 목록): 이것은 미디어 파일에서 체크섬을 보존하기 위해 전송 중에 그리고 정적 상태에서 저장하는 데 사용되는 표준입니다. 더 많은 정보를 원하시면 https://mediahashlist.org 를 방문해보세요.

PB (페타바이트): 디지털 정보 저장의 단위로, 천 테라바이트 또는 백만 기가바이트에 해당합니다.

TTL (Time-to-Live): 이 용어는 데이터가 폐기되기 전에 저장되는 기간을 의미합니다.

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

감사의 말씀

마에스트로 팀, 캐스팩터 팀, 아이스버그 팀의 업무에 기여한 동료인 엠리 쇼, 앙쿠르 케트라팔, 에샤 팔타, 빅터 예레비치, 미나크시 진달, 페이지 후, 동동 우, 챈텔 양, 그레고리 알몬드, 아비 칸다사미, 그 외 훌륭한 동료들께 특별한 감사를 전합니다.
