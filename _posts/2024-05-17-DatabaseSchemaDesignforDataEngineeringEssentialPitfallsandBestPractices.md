---
title: "데이터 엔지니어링을 위한 데이터베이스 스키마 디자인 필수 주의 사항과 최선의 방법들"
description: ""
coverImage: "/assets/img/2024-05-17-DatabaseSchemaDesignforDataEngineeringEssentialPitfallsandBestPractices_0.png"
date: 2024-05-17 19:05
ogImage:
  url: /assets/img/2024-05-17-DatabaseSchemaDesignforDataEngineeringEssentialPitfallsandBestPractices_0.png
tag: Tech
originalTitle: "Database Schema Design for Data Engineering: Essential Pitfalls and Best Practices"
link: "https://medium.com/@yu-ishikawa/database-schema-design-for-data-engineering-essential-pitfalls-and-best-practices-9d3d8e3eba6d"
---

<img src="/assets/img/2024-05-17-DatabaseSchemaDesignforDataEngineeringEssentialPitfallsandBestPractices_0.png" />

현재 데이터 중심의 세계에서 데이터베이스 스키마 디자인은 데이터 엔지니어링, 분석 및 전반적인 비즈니스 인텔리전스에 중요한 역할을 합니다. 애플리케이션 개발자로서, 스키마 디자인은 애플리케이션 요구 사항을 충족시키는 것뿐만 아니라 조직 전체에서 데이터 이전, 분석 및 활용을 효율적으로 가능하게 하는 데 중요하다는 것을 이해하는 것이 중요합니다. 이 기사를 쓰게 된 동기는 데이터 엔지니어로서 다양한 과제를 직면했을 때 일시적인 스키마 디자인이 데이터 접근성과 사용성을 방해했던 경험에서 비롯됩니다. 일반적인 함정을 해결하고 최상의 방법을 채택함으로써 견고하고 확장 가능한 데이터 인프라를 만들 수 있습니다. 이 협력적인 노력은 데이터 엔지니어, 데이터 분석가, 데이터 과학자, 애플리케이션 개발자의 참여를 포함하여 데이터 활용의 모든 측면이 고려되도록 합니다. 함께 이러한 필수적인 통찰력을 탐색하여 스키마 디자인을 개선하고 데이터의 전체 잠재력을 발휘해 보세요.

# 소개

## 데이터 엔지니어링을 위한 데이터베이스 스키마 디자인 개요

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

강력하고 효율적인 데이터베이스 스키마를 설계하는 것은 모든 애플리케이션에 대한 중요한 작업입니다. 그러나 데이터 엔지니어링에 관련하여는 데이터를 데이터 웨어하우스로 효과적으로 전송하고 분석 및 머신러닝(ML)용으로 활용할 수 있도록 하는 추가적인 고려사항이 필요합니다. 이에는 다양한 데이터 전송 방법을 이해하고 잠재적인 문제점을 예측하며, 애플리케이션 및 데이터 소비 요구 사항과 일치하는 최상의 방법을 채택하는 것이 포함됩니다.

AI 및 ML이 더 중요해지는 가운데, 데이터베이스에 저장된 데이터에 쉽게 액세스하고 활용할 수 있는 능력은 데이터 분석과 데이터 과학에 더 중요해집니다. AI 및 ML 모델은 대량의 고품질 데이터에 의존합니다. 효과적인 스키마 설계는 데이터가 AI 및 ML 워크플로에 얼마나 매끄럽게 통합될 수 있는지에 직접적인 영향을 미치며, 더 나은 의사결정, 예측 분석 및 자동화를 지원합니다.

## 스키마 설계 시 데이터 전송을 고려하는 중요성

데이터베이스 스키마 설계의 주요 도전 중 하나는 데이터가 데이터 웨어하우스로 효율적으로 전송될 수 있는지를 보장하는 것입니다. 잘못 설계된 스키마는 데이터 추출, 변환 및 로딩(ETL) 프로세스에서 상당한 어려움을 야기할 수 있으며, 성능 병목 현상과 데이터 불일치로 이어질 수 있습니다. 따라서 데이터 전송 요구 사항을 고려하여 스키마를 설계하는 것이 중요하며, 원활하고 효율적인 데이터 파이프라인을 가능케 합니다.

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

## 애플리케이션 개발자의 책임 범위를 넘어선 부분

일반적으로 애플리케이션 개발자들은 주로 애플리케이션의 즉각적인 요구 사항에 주안점을 두곤 합니다. 그러나 데이터 엔지니어, 데이터 분석가, 데이터 과학자들이 데이터를 활용할 방식을 고려하는 것도 중요합니다. 이 종합적인 관점은 데이터가 다양한 분석 및 과학적 목적에 대해 쉽게 접근 가능하고 활용 가능하도록 보장합니다. 개발자들은 다른 이해관계자들과 협력하여 그들의 데이터 요구 사항을 파악하고 데이터베이스 스키마가 해당 요구를 지원함을 확실히 해야 합니다.

이러한 보다 광범위한 고려 사항을 고려함으로써 애플리케이션 개발자들은 자신들의 애플리케이션 요구 사항뿐만 아니라 조직 전체에서의 효율적인 데이터 전송 및 사용을 용이하게 하는 데이터베이스 스키마를 설계할 수 있습니다. 이는 더 효과적인 데이터 엔지니어링, 더 나은 분석, 그리고 궁극적으로 더 명확한 비즈니스 의사 결정으로 이어집니다.

# 데이터베이스에서 데이터 웨어하우스로의 데이터 전송 유형

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

다양한 종류의 데이터 전송 방법을 이해하는 것은 데이터 크기, 업데이트 빈도 및 조직의 특정 요구 사항에 기초하여 적절한 방법을 선택하는 데 중요합니다. 응용 프로그램 개발자는 효율적인 데이터 전송을 용이하게 하는 스키마를 설계하기 위해 이러한 기술에 대해 인식해야 합니다. 여기에서는 데이터 전송의 주요 유형, 장단점 및 각 접근 방식에 대한 고려 사항을 탐색해봅니다.

## 전체 테이블 전송

설명: 전체 테이블이 데이터 웨어하우스로 전송됩니다.

장점:

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

- 다른 방법들에 비해 구현이 간단합니다.
- 변경 사항을 추적하기 위한 복잡한 논리가 필요하지 않습니다.
- 데이터 웨어하우스가 전송 시점에 테이블의 완전한 스냅숏을 보유하도록 보장합니다.

단점:

- 대량의 데이터 전송으로 대형 테이블에 비효율적입니다.
- 전송 중에 성능 병목 현상을 유발할 수 있으며, 특히 테이블이 매우 큰 경우에 해당합니다.

사용 사례: 변경 빈도가 낮은 소규모에서 중규모의 테이블이나 자주 변경되지 않는 테이블에 적합합니다.

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

**고려 사항:**

- 성능 병목 현상을 피하기 위해 테이블 크기를 관리할 수 있는 수준으로 유지해야 합니다.
- 테이블 크기가 크지 않다면, SELECT \* FROM '테이블'과 같이 간단한 쿼리를 사용하여 데이터를 추출할 수 있습니다.
- 자동 증가 정수 고유 키가 있는 대규모 테이블의 경우, 정수 인덱스의 범위를 사용하여 테이블을 청킹하여 전체 테이블을 효율적으로 전달할 수 있습니다. 이는 Apache Spark와 같은 분산 처리를 가능하게 합니다.

## 부분 테이블 점진적 전송

설명: 특정 기준(일반적으로 타임스탬프 열)에 따라 새로운 레코드 또는 업데이트된 레코드만 전송됩니다.

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

장점:

- 대량이거나 자주 업데이트되는 테이블에 효율적인 데이터 전송 양을 줄입니다.
- 전송 과정에서 데이터베이스 부하를 최소화합니다.

단점:

- 변경 사항을 정확히 추적해야 합니다.
- 신뢰할 수 있는 타임스탬프 데이터와 인덱싱에 의존합니다.

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

표태그로부터 Markdown 형식으로 변경하세요.

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

설명: 테이블에만 발생한 변경 사항(삽입, 업데이트, 삭제)을 캡처하고 전송합니다.

장점:

- 실시간 데이터 동기화.
- 동적 데이터에 매우 효율적인 최소한의 데이터 전송 양.
- 데이터 웨어하우스가 항상 최신 변경 사항과 동기화됨을 보장.

단점:

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

- CDC를 시작하기 전에 day-0 데이터를 준비하는 것이 까다로울 수 있습니다; 전체 테이블을 데이터 웨어하우스에 미리로드해야 합니다. 초기 스냅샷과 CDC 시작 사이에 간격이 생기면 데이터가 누락될 수 있습니다.
- 듀플리케이션 또는 들어오는 데이터를 기존 데이터와 일치시키는 것은 계산적 및 재정적으로 소모적일 수 있습니다.
- CDC 유지 관리는 복잡하고 지속적인 노력이 필요할 수 있습니다.

사용 사례: 데이터베이스와 데이터 웨어하우스 간에 거의 실시간 데이터 동기화가 필요한 시스템에 적합합니다.

고려 사항:

- 데이터베이스 트리거, 로그 기반 캡처 또는 제3자 도구와 같은 CDC 메커니즘을 구현하여 이 프로세스를 용이하게 합니다.
- 데이터의 초기 전체 로드가 CDC 프로세스와 정확하게 동기화되어 데이터 불일치를 방지하도록 합니다.

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

## Zero-ETL: 신흥 개념

Zero-ETL은 전통적인 추출, 변환 및 로드 (ETL) 프로세스의 필요성을 줄이거나 제거하여 시스템 간 데이터에 직접 액세스할 수 있도록 하는 것을 목표로 합니다. 이 접근 방식은 데이터 워크플로우를 간소화할 수 있지만 전통적인 데이터 전송 방법에서 마주하는 도전 과제와 유사한 문제점을 제기할 수도 있습니다.

예시: BigQuery 페더레이티드 쿼리: BigQuery에서 Cloud Spanner 및 Cloud SQL의 데이터를 직접 쿼리할 수 있어 데이터 이동의 필요성을 줄여줍니다.

제한사항 및 고려 사항:

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

- 데이터 유형 호환성: 시스템 간에 모든 데이터 유형이 호환되지 않아 직접 접근이 복잡해집니다.
- 복잡한 데이터 유형: 데이터베이스에서 복잡한 데이터 유형을 사용하면 Zero-ETL 솔루션을 효과적으로 활용하는 것이 어려워질 수 있습니다.
- 문제에 직면하기: Zero-ETL은 대형 테이블과 점진적 데이터 전송의 어려움과 같은 기존 방법과 유사한 문제에 직면할 수 있습니다.

Zero-ETL은 데이터 엔지니어링의 흥미로운 발전을 대표하며, 더 간소하고 효율적인 데이터 워크플로우를 제공하는 잠재력을 제시합니다. 그러나 아직 신흥 컨셉이며 모든 사용 사례와 기술적 도전에 완전히 대응하지 못할 수 있습니다.

## 데이터 전송 방법에 대한 마지막 생각

적절한 유형의 데이터 전송 방법을 선택하는 것은 조직의 특정 요구 사항과 제약 조건에 따라 다릅니다. 전체 테이블을 전송하는 것은 비교적 간단할 수 있지만 대량 데이터셋과 잘 맞지 않을 수 있습니다. 부분 테이블을 점진적으로 전송하는 것은 성능과 효율성 사이의 균형을 제공하며, CDC는 복잡성에 대한 대가로 실시간 동기화를 제공합니다. Zero-ETL은 데이터 액세스를 단순화하는 유망한 접근 방식이지만 데이터 유형 호환성과 시스템 기능을 신중히 고려해야 합니다. 이러한 방법과 그 영향을 이해함으로써 응용 프로그램 개발자는 각 이해관계자의 요구 사항을 충족하는 더 효과적이고 적응 가능한 데이터 파이프라인을 설계할 수 있습니다.

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

# 스키마 설계에서 자주 발생하는 함정 및 최상의 실천 방법

데이터베이스 스키마를 효과적으로 설계하려면 일반적인 함정과 그들을 피하는 최상의 실천 방법을 이해해야 합니다. 여기에서는 이러한 함정과 해당하는 최상의 실천 방법에 대해 논의하여 응용 프로그램 개발자들이 이러한 개념을 더 잘 이해할 수 있도록 상세한 설명과 맥락을 제공합니다. 이러한 함정은 겹치지 않는 독립적인 문제를 다루는 데 조직화됩니다.

## Timestamp 열이 부족한 경우

함정: 테이블에 timestamp 열 (created_at, updated_at)을 포함하지 않으면 변경을 추적하고 데이터를 점진적으로 전송하는 것이 어려워질 수 있습니다.

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

데이터 전송에 미치는 영향: 이 문제는 특히 부분적인 테이블 증분 전송 및 변경 데이터 캡처(CDC) 방법에 대해 문제가 됩니다. 타임스탬프가 없으면 새로 추가되거나 업데이트된 레코드를 식별하는 것이 어렵기 때문에 효율적인 데이터 추출 프로세스가 어려워집니다.

베스트 프랙티스: 항상 타임스탬프 열을 포함하고 인덱싱되어 있는지 확인하세요. 이렇게 하면 시간 범위를 기반으로 하는 효율적인 증분 데이터 추출이 가능해지며, 데이터 동기화와 역사적 분석이 쉬워집니다.

예시: created_at 및 updated_at 열이 없는 판매 테이블이 있다고 가정해 보겠습니다. 마지막 전송 이후에 추가되거나 업데이트된 레코드만 전송해야 한다면, 타임스탬프 없이는 이러한 레코드를 식별할 방법이 없습니다. 이러한 열을 포함하면 타임스탬프를 기반으로 새로운 레코드나 업데이트된 레코드를 질의할 수 있어 증분 전송을 용이하게 할 수 있습니다.

## 부적절한 인덱싱

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

비폴: 잘못된 또는 누락된 인덱스는 쿼리 성능이 떨어지며 데이터 추출 및 전송 효율성을 떨어뜨릴 수 있습니다.

데이터 전송에 미치는 영향: 모든 유형의 데이터 전송(전체 테이블, 점진적 및 CDC)은 인덱싱이 부족하면 성능이 떨어질 수 있습니다. 인덱싱이 부족한 테이블은 쿼리를 위해 전체 테이블 스캔이 필요하며, 이로 인해 추출 시간이 길어지고 리소스 사용량이 증가할 수 있습니다.

모베스트 프랙티스: 쿼리에서 자주 사용되는 열에 대한 적절한 인덱싱 전략을 구현하십시오. 특히 필터링 및 조인 작업에 관여하는 열에 대해 인덱스를 추가하는 것이 좋습니다.

예: 고객 ID 및 이메일 주소를 기반으로 하는 자주 발생하는 쿼리가 있는 고객 테이블이 있다고 가정해 보겠습니다. 이러한 열에 인덱스가 없으면 각 쿼리마다 전체 테이블을 스캔해야 하므로 성능이 저하됩니다. customer_id 및 email에 인덱스를 추가하면 이러한 쿼리의 속도를 크게 향상시켜 데이터 추출 효율성을 개선할 수 있습니다.

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

## 효율적인 고유 식별자 부재

주의 사항: 효율적인 고유 식별자(예: 정수형 고유 키)가 없는 테이블은 데이터 전송 및 동기화에 문제를 일으킬 수 있습니다.

데이터 전송에 미치는 영향: 이는 점진적으로 부분적인 테이블 이전 및 CDC 방법에 대한 중대한 문제입니다. 고유 식별자는 변경 사항을 추적하고 전송 중 데이터 일관성을 보장하는 데 필수적입니다.

귀하의 모법: 신뢰성 있는 데이터 동기화 및 추출을 용이하게 하기 위해 각 테이블이 자동 증가 정수형 키와 같은 효율적인 고유 식별자를 갖도록 합니다.

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

예시: Cloud Spanner에서 정수형 대신 문자열 유형의 고유 키를 사용하면 대규모 테이블의 점진적 추출 및 병렬 처리가 제한될 수 있어서 대량 데이터 처리를 효율적으로 다루는 데 어려움을 겪을 수 있습니다. 자동 증가 정수 키를 사용하면 이 과정을 간단히 할 수 있습니다.

## 복잡한 데이터 유형의 사용

함정: JSON, 배열 또는 사용자 정의 유형과 같은 복잡한 데이터 유형을 활용하면 데이터 추출 및 변환의 용이성이 저하될 수 있습니다.

데이터 전송에 미치는 영향: 이 문제는 모든 데이터 전송 유형에 영향을 미치며, 이러한 유형을 처리하는 복잡성 때문에 데이터 추출, 변환 및 데이터 웨어하우스로 데이터 로드가 어려워집니다.

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

Best Practice: 가능한 한 간단하고 원자적인 데이터 유형을 사용하세요. 보다 복잡한 데이터의 경우, 이러한 유형을 효율적으로 처리할 수 있는 도구와 프로세스가 있는지 확인해야 합니다.

예: JSON 객체로 고객 선호도를 저장하는 것은 응용 프로그램에 편리할 수 있지만, 데이터 추출과 분석이 복잡해질 수 있습니다. 그 대신에 이 정보를 저장하기 위해 구조화된 테이블을 사용해, 쿼리하고 전송하기 쉽도록 만드는 것을 고려해보세요.

## 비정규화된 데이터

위험: 비정규화된 테이블은 데이터 중복과 저장 요구 사항 증가로 이어질 수 있어, 데이터 관리와 전송이 복잡해질 수 있습니다.

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

데이터 전송에 미치는 영향: 전체 테이블 및 증분 전송의 경우 특히 문제가 됩니다. 비정규화된 데이터는 전송되는 데이터의 양과 복잡성을 증가시켜 비효율성을 야기할 수 있습니다.

최상의 실천 방법: 중복을 줄이고 데이터 무결성을 향상시키기 위해 필요한 경우 정규화 원칙을 적용하십시오. 그러나 과도한 정규화는 복잡한 조인 및 성능 저하를 야기할 수 있으므로 성능 고려와 균형을 유지해야 합니다.

예시: 주문마다 반복되는 주소 정보를 포함한 비정규화된 고객 테이블을 고객 및 주소 테이블로 분리하여 중복을 줄이고 데이터 무결성을 향상시킬 수 있습니다. 그러나 정규화가 쿼리를 과도하게 복잡하게 만들고 성능에 영향을 주지 않도록 주의해야 합니다.

## 데이터 파티셔닝을 무시하기

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

문제점: 파티션화되지 않은 대형 테이블은 쿼리 성능을 느리게 만들 수 있고 데이터 추출 프로세스를 복잡하게 만들 수 있습니다.

데이터 전송에 미치는 영향: 이는 대규모 데이터 세트를 처리할 때 특히 모든 데이터 전송 유형에 영향을 줍니다. 파티션화의 부족은 성능 병목 현상과 증가된 자원 사용을 초래할 수 있습니다.

최선의 방법: 쿼리 성능을 향상시키고 데이터 추출을 더욱 관리하기 쉽게 하기 위해 테이블 파티션화를 구현하세요.

예시: 대규모 판매 테이블을 날짜별로 파티션화하면 최근 데이터에 대한 빠른 쿼리와 오래된 데이터의 쉬운 관리가 가능해집니다. 이는 성능을 향상시키고 점진적 데이터 추출을 더 효율적으로 만듭니다.

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

## 데이터 소비 요구 사항을 무시하는 경우

난관: 애플리케이션 개발자들이 데이터 엔지니어링 및 분석에서 데이터 사용 방법을 무시하고 애플리케이션 요구 사항에만 집중한다.

데이터 전송에 미치는 영향: 이는 모든 데이터 전송 유형에 영향을 미친다. 데이터 엔지니어와 분석가의 요구 사항을 무시하면 비효율적인 데이터 모델이 만들어지며 데이터 추출과 분석이 복잡해질 수 있다.

최선의 실천 방법: 스키마 디자인 중 데이터 엔지니어와 분석가의 요구 사항을 고려하여 데이터가 효율적으로 소비되고 분석될 수 있도록 하는 것이 좋다.

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

예시: 데이터 과학자가 데이터를 쿼리하는 방법을 고려하지 않고 스키마를 설계하면 비효율적인 조인 및 복잡한 쿼리가 발생하여 분석이 느려지고 자원 사용량이 증가할 수 있습니다. 데이터 사용자와 협력하여 요구 사항을 이해하고 이에 맞게 스키마를 설계해보세요.

## 전달되지 않은 스키마 변경 사항

주의사항: 데이터 소비자와 협의 없이 고유 키 유형을 변경하는 등 스키마 요소를 수정하는 것은 기존 데이터 파이프라인을 망가뜨릴 수 있습니다.

데이터 전송에 미치는 영향: 특히 점진적으로 일부 테이블을 전송하거나 CDC 방법을 사용하는 경우, 스키마 변경은 데이터 추출 및 동기화 프로세스를 방해할 수 있습니다.

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

**최선의 방법**: 애플리케이션 개발자와 데이터 소비자 간의 의사소통을 촉진하여 스키마 변경 시 모든 이해관계자의 요구사항을 고려합니다.

**예시**: MySQL에서 Google Cloud Spanner로 이전하고 정수 형식의 고유 키를 문자열 형식으로 변경하는 동안 증분 데이터 추출 프로세스가 중단되었습니다. 모든 스키마 변경 사항이 모든 이해관계자와 협의되었음을 확인하세요.

## 미래 데이터베이스 이전을 위한 계획

**주의할 점**: 애플리케이션 개발자들은 종종 미래 데이터베이스 이전을 고려하지 않고 스키마를 설계하는데, 이는 데이터 파이프라인과 데이터 소비에 영향을 줄 수 있습니다.

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

데이터 전송에 미치는 영향: 이는 모든 유형의 데이터 전송에 영향을 줄 수 있으며, 향후 이주 및 업데이트를 더 어렵고 방해할 수 있습니다.

최선의 실천 방법: 잠재적인 미래의 데이터베이스 마이그레이션을 고려하고 해당 마이그레이션이 데이터 파이프라인 및 데이터 소비에 미치는 영향을 고려하십시오. 기술 및 인프라 변화에 대응할 수 있는 스키마를 설계하세요.

예: MySQL에서 다른 데이터베이스 시스템으로 쉽게 마이그레이션할 수 있는 스키마를 설계하면 지속성을 확보하고 데이터 파이프라인에 발생하는 장애 가능성을 줄일 수 있습니다. 이는 표준화된 데이터 유형을 사용하고 마이그레이션을 복잡하게 만드는 데이터베이스별 기능을 피함으로써 이루어집니다.

## 데이터 수용 용량 관리 및 전송 전략 적응하기

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

잘못된 점: 데이터 성장에 대한 계획을 세우지 않으면 데이터 전송과 처리에 어려움이 생길 수 있으며 데이터 양이 증가할수록 이러한 문제가 심화될 수 있습니다.

데이터 전송에 미치는 영향: 이는 모든 데이터 전송 유형에 영향을 미치며 특히 데이터 양이 늘어날수록 더 큰 영향을 줄 수 있습니다. 계획을 세우지 않으면 데이터 전송 프로세스가 비효율적이고 자원을 많이 소모하는 경향이 있습니다.

권장 사항: 데이터 성장을 고려하고 데이터 크기가 증가함에 따라 전송 전략을 조정하세요. 간단한 전체 테이블 전송부터 시작하여 필요에 따라 점진적으로 로드하거나 CDC로 전환하세요.

예시: 'table'에서 SELECT \* FROM을 사용하여 처음에는 작은 테이블을 전송할 수 있지만 테이블이 커지면 증분 로드 또는 CDC를 사용하여 증가된 데이터 양을 효율적으로 처리해야 합니다. 데이터 성장에 맞게 전송 전략을 정기적으로 검토하고 업데이트하여 확장 가능성을 보장하세요.

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

일반적인 함정을 다루고 최선의 방법을 채택함으로써 응용 프로그램 개발자들은 효율적인 데이터 전송 및 분석을 지원하는 데이터베이스 스키마를 설계할 수 있습니다. 이 종합적인 접근 방식은 데이터 인프라가 견고하고 확장 가능하며 다양한 비즈니스 요구를 지원할 준비가 되어 있는지를 보장합니다.

# 결론

응용 프로그램 개발자로서 데이터베이스 스키마 설계가 데이터 활용에 미치는 중요한 영향을 인식하는 것이 중요합니다. 스키마를 설계하는 것은 단순히 응용 프로그램의 즉각적인 요구사항을 충족하는 데 그치는 것이 아니라, 데이터가 데이터베이스에서 데이터 웨어하우스로 전송되는 방식 및 비즈니스 인텔리전스에 효과적으로 분석되고 활용되는 데 중추적인 역할을 합니다.

본 문서에서 다룬 함정과 해결책은 스키마 설계를 개선하기 위한 가치 있는 힌트와 지침을 제공하며, 모든 이해관계자들에게 데이터에 더 쉽게 접근하고 활용할 수 있도록 돕습니다. 이러한 일반적인 함정을 최선의 방법으로 해결함으로써 효율적인 데이터 전송, 동기화 및 분석을 보장하고, 이로써 더 견고하고 확장 가능한 데이터 인프라로 이어집니다.

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

그러나 이러한 함정을 인식하고 대응하는 것은 해결책의 일부에 불과합니다. 다부서간 협업의 중요성은 강조할 수 없을 만큼 큽니다. 스키마를 효과적으로 설계하는 것은 데이터와 그 활용 방법을 설계하는 것을 의미합니다. 이를 위해 데이터 엔지니어, 데이터 분석가, 데이터 과학자, 그리고 응용프로그램 개발자들의 입장과 협력이 필요합니다. 다부서간 노력은 스키마 설계 과정 중 모든 잠재적 문제와 요구 사항이 고려되도록 합니다. 응용프로그램 개발자들은 애플리케이션 요구 사항을 이해하면서 다른 기능들의 데이터 요구 사항을 이해함으로써 이 협력 노력에서 중요한 역할을 합니다. 이러한 협력을 통해 아니면 발생할 문제들을 미리 예방하여 데이터를 더욱 접근하고 사용 가능하게 만들 수 있습니다.

요약하면, 데이터베이스 스키마를 설계하는 것은 데이터 설계의 중요한 부분입니다. 데이터베이스 내의 데이터를 비즈니스 목적으로 어떻게 활용하고 해제할지를 이해하는 것을 의미합니다. 응용프로그램 개발자들은 애플리케이션 측면에 초점을 맞추는 것뿐만 아니라 스키마 설계가 데이터 엔지니어링, 분석 및 전반적인 비즈니스 지능에 미치는 영향을 고려해야 합니다. 최고의 모범 사례를 채택하고 협력적인 환경을 유지함으로써 조직은 다양한 비즈니스 요구 사항을 지원하고 보다 나은 의사 결정과 혁신을 이끌 수 있는 데이터 인프라를 구축할 수 있습니다. 오늘날의 데이터 중심 세계에서 데이터의 최대 잠재력을 발휘하는 데 핵심적인 접근 방식입니다.
