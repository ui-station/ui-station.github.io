---
title: "데이터 엔지니어링 디자인 패턴"
description: ""
coverImage: "/assets/img/2024-05-23-DataEngineeringDesignPatterns_0.png"
date: 2024-05-23 14:04
ogImage:
  url: /assets/img/2024-05-23-DataEngineeringDesignPatterns_0.png
tag: Tech
originalTitle: "Data Engineering Design Patterns"
link: "https://medium.com/@gchandra/data-engineering-design-patterns-9e06454ab40e"
---

디자인 패턴은 소프트웨어 엔지니어들만을 위한 것은 아닙니다. 최신 데이터 솔루션을 구축하는 데 도움이 되는 인기있는 데이터 엔지니어링 디자인 패턴을 알아봅시다.

![이미지](/assets/img/2024-05-23-DataEngineeringDesignPatterns_0.png)

ELT 패턴: 추출, 로드, 변환

이는 RDBMS 세계에서 인기있던 ETL 패턴의 후속입니다. 데이터 엔지니어들이 다양한 소스(RDBMS, API 또는 스크래핑)에서 데이터를 추출하고, S3, ADLS Gen 2, 또는 GCS와 같은 객체 스토어에 로드한 후 Databricks와 같은 현대적인 도구를 사용하여 효율적으로 변환하는 인기있는 공통 패턴입니다.

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

![Lakehouse Pattern](/assets/img/2024-05-23-DataEngineeringDesignPatterns_1.png)

Databricks은 Lakehouse 아키텍처의 선두주자입니다. 이는 데이터 레이크(Data Lake)와 데이터 웨어하우스(Datawarehouse)를 통합합니다. 원시 또는 가공된, 구조화된 또는 반구조화된 데이터가 모두 하나의 환경 안에 모두 사용 가능하며 하나의 플랫폼(Databricks)에서 액세스할 수 있습니다.

![Lakehouse Pattern](/assets/img/2024-05-23-DataEngineeringDesignPatterns_2.png)

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

중개 아키텍처 패턴:

메달리온 아키텍처 패턴은 주로 Databricks와 함께 사용되며 이제는 사실상의 표준이 되었습니다. 데이터 처리를 위해 원본 데이터인 청동, 정리 및 풍부화된 데이터인 은, 그리고 비즈니스 수준의 집계한 데이터인 금 레이어로 이루어져 있습니다.

DeltaLake 아키텍처 패턴:

Delta Lake은 초기에 Databricks에서 개발된 오픈 소스 프로젝트입니다. 이 프로젝트는 데이터 호수에 안정성을 제공합니다. Deltalake는 ACID 트랜잭션, 타임 트래벌, z-order, CDC, 스키마 진화 및 기타 최적화로 데이터 호수를 개선합니다.

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

Kappa Architecture Pattern:

Kappa Architecture에서는 모든 데이터가 스트림으로 처리됩니다. 이는 기존에 배치 처리로 다뤄졌을 것들을 연속적인 데이터 스트림으로 처리하는 것을 의미합니다. 시스템은 데이터가 도착하는 즉시 실시간으로 처리하며 별도의 배치로 처리하지 않습니다. Kappa Architecture는 실시간 처리에 최적화되어 있지만, "배치" 데이터로 간주될 수 있는 대규모의 과거 데이터를 스트림으로 재생하여 처리할 수 있습니다. Databricks Autoloader는 Kappa Architecture를 구현하는 데 가장 적합합니다.

Serverless Architecture Pattern:

데이터 엔지니어링에서 서버리스는 클라우드 지역/IP 제약 사항이나 기타 기본 인프라 제약 조건에 대해 걱정하지 않고 데이터 파이프라인을 구축할 수 있습니다. 특히 변수 사용 패턴을 갖는 워크로드에 대한 즉시 클러스터 가용성과 비용 효율적인 스케일링을 위해 유용합니다. 많은 클라우드 제공업체가 이를 제공하고 있습니다. Databricks SQL은 Serverless를 제공하며, SQL 웨어하우스를 3초 미만의 시간 안에 론칭할 수 있습니다.

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

MLFlow 아키텍처 패턴:

MLflow는 Databricks가 개발한 오픈 소스 프로젝트로, 실험, 재현성, 모델 배포 및 모델 제공을 포함한 기계 학습 라이프사이클을 관리하는 데 사용됩니다.

이러한 패턴은 견고하고 확장 가능한 데이터 시스템을 설계하는 데 중요합니다. Databricks를 이용하면 이러한 패턴을 더욱 간편하게 구현할 수 있으며 효율적인 데이터 처리 및 분석을 위한 통합 도구를 제공합니다.

좋아하는 패턴이 빠졌나요? 댓글 섹션에서 알려주세요. 흥미로운 내용이라고 생각되면 반가워 하지 마세요.
