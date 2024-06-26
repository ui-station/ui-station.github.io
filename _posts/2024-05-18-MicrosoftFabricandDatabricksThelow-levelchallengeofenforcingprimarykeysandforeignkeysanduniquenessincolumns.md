---
title: "마이크로소프트 Fabric 및 Databricks 열에서 기본 키와 외래 키, 그리고 고유성을 강제하는 낮은 수준의 도전"
description: ""
coverImage: "/assets/img/2024-05-18-MicrosoftFabricandDatabricksThelow-levelchallengeofenforcingprimarykeysandforeignkeysanduniquenessincolumns_0.png"
date: 2024-05-18 16:31
ogImage:
  url: /assets/img/2024-05-18-MicrosoftFabricandDatabricksThelow-levelchallengeofenforcingprimarykeysandforeignkeysanduniquenessincolumns_0.png
tag: Tech
originalTitle: "Microsoft Fabric and Databricks: The low-level challenge of enforcing primary keys and foreign keys and uniqueness in columns"
link: "https://medium.com/@christianhenrikreich/microsoft-fabric-and-databricks-the-low-level-challenge-of-enforcing-primary-keys-and-foreign-8c7fb6ebbe8f"
---

![이미지](/assets/img/2024-05-18-MicrosoftFabricandDatabricksThelow-levelchallengeofenforcingprimarykeysandforeignkeysanduniquenessincolumns_0.png)

전통적인 관계형 데이터베이스의 가장 강력한 기능 중 하나는 데이터 일관성과 데이터 품질과 관련된 주요 키와 해당 고유성을 보장하는 것입니다. 이는 외래 키와 고유 열에도 해당됩니다.

Microsoft Fabric Warehouse와 Databricks with Unity Catalog에서 주요 키를 선언하는 것은 가능하지만 이것들은 강제되지 않습니다. Microsoft Fabric에서 심지어 우리는 주요 키를 강제하지 않도록 선언합니다.

![이미지](/assets/img/2024-05-18-MicrosoftFabricandDatabricksThelow-levelchallengeofenforcingprimarykeysandforeignkeysanduniquenessincolumns_1.png)

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

![이미지](/assets/img/2024-05-18-MicrosoftFabricandDatabricksThelow-levelchallengeofenforcingprimarykeysandforeignkeysanduniquenessincolumns_2.png)

기본 키 열이 고유한 값을 보유하는 것을 보장할 수 없다는 것을 의미합니다. 고유 제약 조건이 있는 열에 대해서도 마찬가지입니다. 이 문제에 대한 완벽한 해결책은 없지만, 이를 이해하면 솔루션을 설계할 때 도움이 될 수 있습니다.

# 왜 오래된 기술에서는 작동할까요?

이것이 강제되지 않는 이유 중 하나를 이해하려면 SQL Server, Postgres 등과 같은 오래된 기술에서 왜 작동하는지 살펴보는 것으로 시작합니다. 많은 전통적인 데이터베이스는 메모리 내 데이터베이스로 볼 수 있습니다.

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

많은 전통적인 데이터베이스는 인메모리 데이터베이스로 볼 수 있습니다. 전체 데이터베이스가 메모리에 맞지 않을 경우 데이터는 디스크에도 저장되고 교환됩니다. DBA가 하는 일은 핫 데이터를 메모리에 정리하고 콜드 데이터를 디스크에 남겨두는 것입니다.

테이블은 데이터 페이지라 불리는 행 단위로 저장됩니다. 페이지는 다양한 방식으로 정리될 수 있으며, 가장 일반적인 방법은 연속적인 heap 또는 B+ 트리 구조에서 인덱싱되는 것입니다. 테이블이 전체 메모리에 있지 않아도 검색할 수 있습니다.

힙에서 처럼 단일 값(기본 키와 같은)을 검색할 때는 처음 페이지의 첫 번째 레코드부터 시작하여 마지막 페이지의 마지막 레코드까지 모든 페이지를 읽습니다. 검색 시간은 테이블 크기에 따라 증가하며 이는 최적화되지 않은 방법입니다.

![이미지](/assets/img/2024-05-18-MicrosoftFabricandDatabricksThelow-levelchallengeofenforcingprimarykeysandforeignkeysanduniquenessincolumns_3.png)

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

B+ 트리로 색인화된 데이터를 가지고 있는 경우, 많은 데이터베이스 시스템에서처럼, 값을 조회하는 것은 테이블의 크기에 관계없이 3~4 페이지를 조금 건너뛰면 됩니다. 트리는 일반적으로 값을 빠르게 조회하는 데 알려져 있습니다. 나는 색인화된 레코드를 검색하는 여행을 표시했어요. 예를 들어 기본 키가 될 수 있는 단일 값을 검색하는 것처럼요.

![이미지](/assets/img/2024-05-18-MicrosoftFabricandDatabricksThelow-levelchallengeofenforcingprimarykeysandforeignkeysanduniquenessincolumns_4.png)

기본 키나 고유한 열을 만들 때 B+ 색인 트리가 생성됩니다. B+ 트리에 삽입할 때 이미 값이 존재한다면, 키 위반 오류가 발생하며, 그렇지 않으면 삽입합니다. 참조 키 확인을 수행할 때, 그림에 나와 있는 것처럼 트리를 탐색합니다.

메모리에 페이지를 인코딩하고 압축하지 않고, 메모리에 액세스하는 것이 매우 빠르기 때문에, 하드웨어 및 설정에 따라 수백만 건의 단일 조회를 초 단위로 쉽게 수행할 수 있습니다. 일부 테이블 정보가 메모리로 로드되지 않은 경우에는 약간의 문제가 발생할 수 있지만, 처리 중에 검색할 수 있습니다.

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

![이미지](/assets/img/2024-05-18-MicrosoftFabricandDatabricksThelow-levelchallengeofenforcingprimarykeysandforeignkeysanduniquenessincolumns_5.png)

# Microsoft Fabric나 Databricks와 비교해 보면 어떻게 될까요?

컴퓨팅과 스토리지를 분리할 때, 데이터는 기본적으로 스토리지에 저장되며 메모리에 저장되지 않습니다(캐싱되는 경우도 있음).

Spark를 예로 들어보겠습니다. Spark 작업을 시작할 때, 스토리지에 있는 데이터는 파티션을 제거하고 파일을 제거하며, 파케이트에서 제거되어 Spark 메모리로 읽히기 전 성능을 높이기 위해 정리됩니다. 작업 중에 누락된 데이터는 검색할 수 없습니다(이에 대한 로직을 작성하지 않는 한). 데이터를 스토리지에서 메모리로 옮기는 데는 메모리에서 데이터를 옮기는 것보다 더 오랜 시간이 걸립니다. 데이터는 여전히 메모리에서 CPU로 이동해야 하지만, 스토리지에서 메모리로 이동하는 추가 단계가 추가됩니다.

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

저장소에 저장된 데이터는 Parquet 형식으로 인코딩되고 압축되어 있으며, 값을 읽기 위해 해제되고 디코딩되어야 합니다. 데이터는 연속적인 방식으로 열 단위로 저장되며, 값들을 찾을 때 힙과 비슷한 속성을 가지고 있습니다.

![이미지](/assets/img/2024-05-18-MicrosoftFabricandDatabricksThelow-levelchallengeofenforcingprimarykeysandforeignkeysanduniquenessincolumns_6.png)

한 개 또는 몇 개의 레코드를 찾을 때는 경험이 수용 가능합니다. 이제, 주 키 및 외래 키를 강제하고 100,000개의 레코드 배치를 테이블에 삽입하고자 할 때를 상상해보세요.

Spark는 작업 중에 메모리에 있는 것만 처리할 수 있습니다. 따라서 주 키 삽입용 테이블을 메모리에 로드해야 하며 외래 키가 참조하는 테이블도 로드되어야 합니다. Spark는 데이터를 트리와 같은 검색 최적화된 구조로 로드하지 않기 때문에 모든 레코드 삽입 시 전체 테이블이 확인되어야 합니다. 이 경우에는 100,000번이 될 것입니다. 외래 키 확인도 동일합니다. 목표 테이블이 1,000,000이라고 가정해봅시다. 많은 단일 검색과 테이블 스캔이 필요할 것입니다.

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

요약하자면, 모든 테이블을 로드하면 초기 대기 시간이 소요됩니다. 디코딩 및 압축 해제가 이 대기 시간을 더욱 증가시킵니다. 선형 데이터 구조에서 값을 조회하면 성장하는 대기 시간으로 인해 스케일링이 나빠지며, 특히 테이블 크기가 커질수록 B+ 트리와 비교했을 때 성능이 상당히 떨어집니다.

그래서 미이크로소프트 패브릭과 데이터브릭스에서 키 및 고유성 강제가 없는 이유 중 하나는 저장 및 계산의 유연성을 제공하고 집계 및 분석 처리를 위한 성능을 제공하지만 데이터에서 단일 검색에 대해 최악의 검색 시간을 갖게되는 것입니다. 그것이 타협점이며, 우리가 여전히 기다리는 이유 중 하나일 것입니다.

# 토의

많은 사람들은 이것을 문제로 보지 않을 수 있습니다. 데이터 웨어하우징에 관한 많은 텍스트는 로드 성능을 높이기 위해 사실에 제약을 두지 말라고 권장합니다. 무결성은 테이블 로드에 관한 정책을 통해 보장할 수 있습니다. 그런 다음 사람들이 그룹화된 카운트를 사용하는 것과 같은 무결성 테스트를 실시하는 것을 본 적이 있습니다. 강력한 필요성이 있다면 전통적인 데이터베이스가 더 나은 해결책일지도 모릅니다.

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

여전히 일부 처리 엔진 최적화 프로그램은 이 정보를 활용할 수 있으므로, 키를 정의하는 것은 나쁜 습관이 아닙니다.
