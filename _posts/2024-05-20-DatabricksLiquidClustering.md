---
title: "데이터브릭스 리퀴드 클러스터링"
description: ""
coverImage: "/assets/img/2024-05-20-DatabricksLiquidClustering_0.png"
date: 2024-05-20 16:57
ogImage: 
  url: /assets/img/2024-05-20-DatabricksLiquidClustering_0.png
tag: Tech
originalTitle: "Databricks Liquid Clustering"
link: "https://medium.com/@rahulsoni4/have-you-ever-wondered-if-theres-a-dynamic-solution-to-the-relentless-challenge-of-data-9e9956bd6bf9"
---


이전에 데이터 레이크하우스 세계에서 데이터 분할의 끊임없는 도전에 대한 동적 해결책이 있는지 궁금했나요?

음, 저는 궁금했어요! 그럼 함께 이야기해보죠.

## 고정된 데이터 레이아웃의 도전

다음 그래프를 살펴보세요.

<div class="content-ad"></div>

```
![DatabricksLiquidClustering](/assets/img/2024-05-20-DatabricksLiquidClustering_0.png)

이 그래프는 연도별 테이블 행 수를 예측하고 데이터 분포에서 상당한 치우침을 보여줍니다. 이 치우침은 소비자가 쿼리에서 연도 열을 자주 필터로 사용하기 때문에 특히 관련이 있습니다.

이 테이블은 생성 시 year 및 month 열을 사용하여 파티션으로 설정되었습니다. 이것이 바로이 테이블을 위한 DDL의 형태입니다.

```js
%sql
CREATE TABLE kaggle_partitioned (
  year_month STRING,
  exp_imp TINYINT,
  hs9 SMALLINT,
  Customs SMALLINT,
  Country BIGINT,
  quantity BIGINT,
  value BIGINT,
  year STRING,
  month STRING
) USING delta PARTITIONED BY (year, month);
```

<div class="content-ad"></div>

여기 문제가 있어요. 테이블의 전체 데이터 중 약 83%가 2개의 파티션에 있습니다.

![이미지](/assets/img/2024-05-20-DatabricksLiquidClustering_1.png)

위의 정보를 바탕으로 테이블이 너무 적게 파티셔닝되었는지, 아니면 너무 많이 파티셔닝되었는지 어떻게 생각하시나요?

이 테이블의 데이터 분포를 더 깊게 살펴보겠습니다. 다음 차트는 연간 행 수의 월별 분할을 보여줍니다.

<div class="content-ad"></div>


![데이터 분포의 그림적 표현](/assets/img/2024-05-20-DatabricksLiquidClustering_2.png)

더 자세히 데이터 분포를 나타낸 그림을 살펴보면, 2020년 3월에 가장 많은 데이터가 있고, 그 뒤를 이어서 1월과 2월이, 다른 달들은 더 작은 파티션으로 이어지고 있습니다.

그래서, 테이블이 과분할되었는지? 아니면 과소분할되었는지? 아니면 둘 다인가요?

다음 그림이 어떤 연관성이 있나요? 무슨 의미를 전달하나요?


<div class="content-ad"></div>

<img src="/assets/img/2024-05-20-DatabricksLiquidClustering_3.png" />

현재 파티셔닝 전략대로,

- 2020–03과 같은 파티션의 경우, 한 시간치 데이터를 쿼리하기 위해 많은 양의 데이터를 읽어야 할 지도 모릅니다. 
- 반면에 다른 극단적인 상황에서는, 적은 양의 데이터를 가진 고객을 위한 쿼리를 수행하기 위해 여러 파티션을 가로질러 많은 작은 파일을 스캔해야 할 수도 있습니다. 
- 마지막으로, 테이블을 주/일/월 단위로 다시 파티셔닝해야 할 경우, 전체 테이블을 다시 작성해야 합니다. 또 다시요!

이제 우리 테이블에서 데이터 쓰기 시나리오를 논의해 봅시다. 제 생각에는 이미지가 제 의견을 요약해 주고 있으므로 글로 다시 작성할 필요는 없을 것 같아요 ;)

<div class="content-ad"></div>

<img src="/assets/img/2024-05-20-DatabricksLiquidClustering_4.png" />

지금! 이 기사의 맨 첫 줄을 한 번 더 반복합시다!

데이터 레이크하우스 세계에서 데이터 파티셔닝의 끊임없는 도전에 대한 동적 솔루션이 있는지 궁금했던 적이 있나요?

리퀴드 클러스터링이 등장했습니다! 데이터 레이아웃 결정을 간소화하고 쿼리 성능을 향상시키며, 지속적인 모니터링 및 조정을 요구하지 않습니다.

<div class="content-ad"></div>

그래서 어떻게 하는 건가요?

- 빠름: 튜닝된 분할 테이블과 유사한 쓰기 및 읽기 속도
- 자체 튜닝: 과다 및 부족한 분할을 피함
- 점진적: 새 데이터의 자동 부분 클러스터링
- 스쿠 내성: 일관된 파일 크기와 낮은 쓰기 증폭
- 유연함: 클러스터링 열을 변경하고 싶나요? 문제 없어요!
- 더 나은 병행성: 테이블에서 행 수준의 병행성 활성화

좀 더 자세히 이해해 보겠습니다. 이전에 보여드린 샘플 레이아웃 다이어그램을 사용할 거예요.

![다이어그램](/assets/img/2024-05-20-DatabricksLiquidClustering_5.png)

<div class="content-ad"></div>

요렇게 Liquid Clustering이 어떻게 도와주는지 확인해보세요! Liquid Clustering은 군집화와 파일 크기의 효율적인 균형을 이룹니다.

![Liquid Clustering](/assets/img/2024-05-20-DatabricksLiquidClustering_6.png)

작은 파티션을 자동으로 처리할 뿐만 아니라, 큰 파티션에서 시간별 데이터만 가져오려면 더 효율적인 쿼리를 위해 더 나눌 수 있습니다.

![Liquid Clustering](/assets/img/2024-05-20-DatabricksLiquidClustering_7.png)

<div class="content-ad"></div>

실제로 보여드릴게요! 여기 파티션된 테이블의 파일 크기 분포입니다.

![이미지](/assets/img/2024-05-20-DatabricksLiquidClustering_8.png)

이 파티션된 테이블에서 클러스터된 테이블을 생성해보겠습니다. CTAS를 사용할 거에요.

```js
CREATE TABLE kaggle_clustered CLUSTER BY(year, month) AS
SELECT
  *
FROM
  kaggle_partitioned;
```

<div class="content-ad"></div>

그리고 군집 테이블의 파일 크기 분포도가 여기 있어요.

![file size distribution](/assets/img/2024-05-20-DatabricksLiquidClustering_9.png)

작은 파일 대부분이 통합되어 더 최적화된 파일이 생성된 것이 명백하게 나타나네요.

액체 클러스터링은 일부/게으른 클러스터링을 활용하여 효율적으로 적재를 돕습니다.

<div class="content-ad"></div>

그걸 어떻게 이해하는지 알아봅시다.

- 2021-01은 제가 파티션 테이블에 데이터가 없는 파티션입니다.
- 해당 날짜 범위의 데이터를 적재하기 시작하면, 모든 고객을 포함하는 파일이 생성됩니다.
- 데이터 집합이 증가함에 따라, Liquid Clustering은 고객을 위한 파일을 분할하기 시작합니다.
- 분할 작업은 가끔 작은 파일로 나누어지지만, 테이블 유지 관리는 이 작은 파일을 자동으로 큰 파일로 병합하여 읽기 성능에 영향을 미치지 않도록 합니다.

# 그래서! 테이블에서 어떻게 사용하나요?

첫 번째로! 클러스터링은 파티셔닝이나 ZORDER와 호환되지 않으며, Databricks 클라이언트가 테이블의 데이터에 대한 모든 레이아웃 및 최적화 작업을 관리해야 합니다.

<div class="content-ad"></div>

이제 리퀴드 클러스터링을 사용하여 델타 테이블을 생성하는 방법을 살펴보겠습니다.

```js
--빈 테이블 생성하기
CREATE TABLE table1(col0 int, col1 string) USING DELTA CLUSTER BY (col0);


--CTAS 문 사용하기
CREATE EXTERNAL TABLE table2 CLUSTER BY (col0) --테이블 이름 뒤에 클러스터링을 지정하고, 서브쿼리에는 사용하지 않기
LOCATION 'table_location' AS
SELECT
  *
FROM
  table1;


--구성 복사를 위해 LIKE 문 사용하기
CREATE TABLE table3 LIKE table1;
```

# 클러스터링을 트리거하는 방법

간단히 테이블에 OPTIMIZE 명령을 사용하면 됩니다. 아래 예시를 참고하세요.

<div class="content-ad"></div>

```js
테이블 이름을 최적화하세요;
```

리퀴드 클러스터링은 증분 방식으로 작동합니다. 따라서 클러스터링해야 하는 데이터를 수용하기 위해서만 데이터가 다시 작성됩니다. 클러스터링 키를 갖는 데이터 파일이 클러스터링해야 하는 데이터와 일치하지 않는 경우 재작성되지 않습니다.

데이터를 클러스터링하고 최상의 성능을 얻기 위해 정기적으로 최적화 작업을 실행해야 합니다. 리퀴드 클러스터링은 증분 방식이므로 대부분의 클러스터링된 테이블의 최적화 작업이 빠르게 실행됩니다.

# 리퀴드 클러스터링은 무엇에 사용되나요?```

<div class="content-ad"></div>

Databricks 문서에 따르면, 모든 새로운 Delta 테이블에는 리퀴드 클러스터링을 사용하는 것이 권장됩니다. 클러스터링이 유용한 시나리오의 예시는 다음과 같습니다:

- 높은 카디널리티 열을 기반으로 자주 필터링되는 테이블.
- 데이터 분포에서 상당한 불균형이 있는 테이블.
- 빠르게 성장하여 유지보수와 조정 노력이 필요한 테이블.
- 동시에 쓰기 요구사항이 있는 테이블.
- 시간이 지남에 따라 액세스 패턴이 변경되는 테이블.
- 전형적인 파티션 키가 테이블에 너무 많거나 너무 적은 파티션을 남길 수 있는 테이블.

## 리퀴드 클러스터링 사용 시 고려해야 할 사항

- 클러스터링 키로 수집된 통계가 있는 열만 지정할 수 있습니다. 기본적으로 Delta 테이블의 처음 32열에는 통계가 수집됩니다.
- 최대 4개의 열을 클러스터링 키로 지정할 수 있습니다.
- 구조화된 스트리밍 워크로드는 라이트 시 클러스터링을 지원하지 않습니다.