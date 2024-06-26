---
title: "눈꽃 폴라리스와 데이타브릭스 유니티 카탈로그 오픈 및 상호 운용 가능한 메타스토어 시대"
description: ""
coverImage: "/assets/img/2024-06-19-SnowflakePolarisandDatabricksUnityCatalogTheageofOpenandInteroperableMetastores_0.png"
date: 2024-06-19 12:26
ogImage:
  url: /assets/img/2024-06-19-SnowflakePolarisandDatabricksUnityCatalogTheageofOpenandInteroperableMetastores_0.png
tag: Tech
originalTitle: "Snowflake Polaris and Databricks Unity Catalog: The age of Open and Interoperable Metastores"
link: "https://medium.com/@sachinksdata/snowflake-polaris-and-databricks-unity-the-age-of-open-and-interoperable-catalogs-fe52d355cc4a"
---

<img src="/assets/img/2024-06-19-SnowflakePolarisandDatabricksUnityCatalogTheageofOpenandInteroperableMetastores_0.png" />

이번 달에는 두 대형 클라우드 데이터 플랫폼인 Snowflake와 Databricks에서 비슷한 성격의 주요 발표가 이뤄졌습니다.

6월 초 한 주쯤에, Snowflake는 매년 개최되는 컨퍼런스에서 Apache Iceberg 위에 구현된 오픈 카탈로그인 Polaris Catalog를 발표했습니다. 그리고 그 한 주 뒤에, Databricks가 데이터 거버넌스를 위한 통합 솔루션을 제공하는 Unity Catalog 제품을 오픈소스로 공개했습니다. 이 짧은 기사에서 자세히 살펴보도록 하겠습니다.

# 데이터 호수(Datalake)에서의 메타데이터 카탈로그

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

![SnowflakePolarisandDatabricksUnityCatalogTheageofOpenandInteroperableMetastores_1.png](/assets/img/2024-06-19-SnowflakePolarisandDatabricksUnityCatalogTheageofOpenandInteroperableMetastores_1.png)

이 제품에 대해 파헤치기 전에, 데이터 레이크의 맥락에서 정의에 대해 먼저 간단히 논의해보겠습니다. 메타데이터 카탈로그, 또는 메타스토어라고도 알려진 것은 데이터셋을 테이블로 표현하여 객체 저장소에 있는 데이터에 대한 추상화 레이어를 만듭니다. 데이터에 대한 접근은 메타스토어를 통해 관리되며, 상호 작용을 테이블에 저장된 것처럼 변환하여 저장소에서 필요한 작업을 수행합니다.

# Databricks Unity Catalog

처음 접하는 분들을 위해 - 2013년 Apache Spark의 창시자들에 의해 설립된 Databricks는 데이터 레이크와 데이터 웨어하우스를 결합하여 기업이 데이터 및 AI 솔루션을 구축, 관리 및 확장하는 데 도움이 되는 클라우드 기반 데이터 인텔리전스 플랫폼입니다.

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

약 2021년 중반쯤, 유니티 카탈로그가 독점 소스로 출시되었습니다. 이는 플랫폼 에코시스템 내에서 데이터 및 AI 자산을 접근하고 관리하기 위한 솔루션이었습니다. 이는 중앙 집중식 접근 제어, 감사, 계보, 공유 및 데이터 발견 기능과 같은 여러 기능을 제공했습니다.

![이미지](/assets/img/2024-06-19-SnowflakePolarisandDatabricksUnityCatalogTheageofOpenandInteroperableMetastores_2.png)

오픈 테이블 형식 (OTFs)인 Iceberg, Delta 및 Hudi가 인기를 얻으면서, 주요 데이터 플랫폼 공급 업체들은 이 세 가지 중 하나를 선택해야 했습니다. 당연히 Databricks의 창립자로서 델타 레이크가 주요 형식으로 선택되었습니다.

그러나 오픈 델타 형식을 사용하는 플랫폼과의 밀접한 결합은 Apache Iceberg 또는 Hudi와 호환되는 쿼리 엔진과의 상호 운용성이 제한되었음을 의미했습니다. Databricks가 이 문제를 해결하기 위한 최초의 시도는 Delta UniForm이었습니다. 이는 복사 또는 변환의 필요성을 제거하고 레이크하우스 상호 운용성을 위한 범용 형식을 제공했습니다.

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

최신 발표에 따르면, Unity Catalog를 오픈 API와 아파치 2.0 라이센스로 빌드한 오픈 소스 서버로 제공하는 Databricks가 기업에게 오픈 데이터 형식(UniForm을 통한)을 지원하며 다양한 쿼리 엔진, 도구, 클라우드 플랫폼 간의 상호 운용성을 제공하는 범용 인터페이스를 제공하여 다음 수준으로 나아갔습니다.

# Snowflake Polaris Catalog

2012년 개발된 Snowflake는 데이터 웨어하우징, 데이터 레이크, 데이터 엔지니어링 및 데이터 과학을 위한 완전히 관리되는 SaaS 플랫폼입니다. Snowflake는 스토리지와 컴퓨팅의 분리, 온디맨드 확장 가능한 컴퓨팅, 데이터 공유, 데이터 복제, 그리고 성장하는 기업의 요구를 처리하기 위한 타사 도구 지원과 같은 기능들을 제공합니다.

Snowflake는 자사의 프로프라이어터리 테이블 형식을 시작한 후, 재미있게도 얼마 전부터 Apache Iceberg와의 통합에 헌신하여 왔습니다.

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

![Snowflake Polaris and Databricks Unity Catalog](/assets/img/2024-06-19-SnowflakePolarisandDatabricksUnityCatalogTheageofOpenandInteroperableMetastores_3.png)

최근 출시된 Polaris 카탈로그는 Iceberg의 오픈 소스 REST 프로토콜을 기반으로 하여 사용자가 Iceberg Rest API를 지원하는 Apache Spark, Flink, Trino 등과 같은 원하는 엔진을 사용하여 데이터에 액세스하고 검색할 수 있는 오픈 표준을 제공합니다. Polaris는 다음 90일간(약 2024년 4분기) 오픈 소스로 공개될 예정입니다.

# 개방성은 호환성을 의미하지 않을 수 있습니다

이러한 프로젝트들을 오픈 소스 Apache 이니셔티브로 오픈하는 것은 긍정적인 단계이지만, 데이터 솔루션 아키텍처적인 측면에서 보면, 코드를 오픈할 필요는 없고 오히려 노출되는 인터페이스가 오픈되어야 합니다. 이 글을 쓰는 동안, Polaris는 원래 Iceberg만을 지원하며, Unity는 UniForm을 사용하여 네이티브 Delta 이외의 다른 OTF(Open Table Format)를 간접적으로 지원하고, Tabular 인수 이후 Iceberg와의 새로운 통합을 수행하고 있습니다.

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

"이상적인 '오픈' 정의에서 이 메타스토어는 모든 표준 테이블 형식을 지원하고, 구성 가능한 저장 계층과 Hive Metastore가 한동안 있었던 것과 같은 메타스토어에 대한 표준 인터페이스를 가져야 합니다.

# 결론

대부분의 기업은 벤더 락인을 피하면서 데이터 생태계를 더 열린, 유연한, 상호 운용 가능한 것으로 하고 싶어합니다. 데이터브릭스 없이 유니티 카탈로그를 구현하거나, 스노우플레이크 없이 폴라리스를 사용하는 것은 흥미로운 전망입니다. 엔지니어링 팀은 이제 이러한 기능을 구매한 플랫폼이나 컨테이너를 사용하여 자체 인프라에서 독립적으로 호스팅하는 유연성을 가지게 되었습니다. 다가오는 몇 달 동안, 개방 커뮤니티의 협력으로 이 제품들의 성장과 채택을 지켜보는 것이 흥미로울 것입니다."
