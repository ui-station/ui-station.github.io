---
title: "SQL 설명 공통 테이블 표현식"
description: ""
coverImage: "/assets/img/2024-05-23-SQLExplainedCommonTableExpressions_0.png"
date: 2024-05-23 15:55
ogImage: 
  url: /assets/img/2024-05-23-SQLExplainedCommonTableExpressions_0.png
tag: Tech
originalTitle: "SQL Explained: Common Table Expressions"
link: "https://medium.com/towards-data-science/sql-explained-common-table-expressions-fc23e4675890"
---


SQL에서 CTE(Common Table Expressions, CTE로 알려진)는 다른 SQL 쿼리에서 파생된 중간 데이터를 포함하는 일시적인 이름이 지정된 결과 세트입니다. CTE에 데이터가 있는 경우 동일한 쿼리 내에서 해당 데이터를 한 번 이상 참조할 수 있습니다.

위 설명을 보면 CTE가 SQL의 일반적인 일시적 테이블과 많이 닮았다고 생각할 수 있습니다. 어떤 면에서는 맞지만 왜 CTE를 사용해야 하는지 궁금할 수 있습니다. 이에 대답하기 위해 일시적 테이블의 주요 단점 두 가지를 살펴보겠습니다.

일시적 테이블은 특히 대규모 SQL 스크립트의 다양한 부분을 횡단해서 사용될 경우처럼 복잡한 코드 작성에 기여할 수 있습니다. 이들은 명시적으로 생성, 삭제되어야 하며 필요시 인덱스도 구축되어야 합니다. 이로 인해 SQL 및 세션 관리에 부하가 추가됩니다.

또한, 일시적 테이블은 물리적 저장 공간을 소비하며, 여유 공간이 부족하고 많은 양의 일시적 테이블이 있다면 이는 고려할 사항일 수 있습니다. 게다가 일시적 테이블을 사용하는 쿼리를 살펴볼 때, 일시적 테이블에 어떤 데이터가 포함되어 있는지, 데이터가 어디서 왔는지 명확하지 않을 수 있습니다.

<div class="content-ad"></div>

CTE(공통 테이블 표현식)에는 위에서 언급한 문제가 없어요. 일단, CTE는 일시적이기 때문에 SQL 세션이 종료되면 CTE가 범위에서 벗어나고 사용한 모든 메모리가 해제됩니다.

그리고 CTE에 들어 있는 데이터를 정확히 확인할 수 있어요. 그 생성과 채움은 SQL 스크립트 안에 바로 있어요.

CTE의 이점뿐만 아니라, 특정 단점도 있어서 아래 경우에는 사용하지 않는 것이 좋을 수 있어요.

- 쿼리에서 CTE에 포함될 데이터를 한 번 이상 참조해야 하는 경우. 이는 CTE가 참조될 때마다 다시 채워져야 하기 때문에 발생하는 문제예요. 그러나 CTE의 데이터 양이 작다면 이것이 사용 시 제약 사항이 되지 않을 수 있어요.
- 위와 관련하여, CTE는 인덱싱할 수 없기 때문에 데이터 양이 많으면 인덱싱된 임시 테이블보다 성능이 떨어질 수 있어요.

<div class="content-ad"></div>

그러니까, 다음 시나리오에서 CTE를 사용해 보세요.

- 임시 테이블을 사용하지 않으려고 할 때
- CTE의 데이터 양이 비교적 낮을 때
- 쿼리에서 CTE 데이터를 한 번만 참조하는 경우 (데이터 양에 따라 두 번일 수도 있음)

아직 언급하지 않은 CTE의 마지막 이점은 많은 최신 SQL 방언에서 재귀 CTE를 지원한다는 것입니다. 즉, CTE가 자기 자신을 참조하는 경우입니다. 당연히 이는 재귀 및 계층 기반 SQL 쿼리를 코딩하기가 훨씬 쉬워진다는 뜻입니다. 나중에 이에 대한 몇 가지 예제를 살펴볼 것입니다.

이제 CTE의 정의와 기능을 보다 자세히 이해했으니, 사용 예제를 살펴보겠습니다.

<div class="content-ad"></div>

## 테스트 환경 설정하기.

저는 Oracle의 라이브 SQL 웹사이트를 사용하여 테스트를 실행합니다. 이 서비스에 액세스하고 사용하는 방법에 대해 이전에 작성한 SQL에서 Grouping Sets, Rollup 및 Cube를 사용하는 기사에서 설명했습니다. 설정하고 사용하는 데 완전히 무료입니다. 해당 기사 링크는 아래에 제공되어 있습니다.

## 샘플 테이블 생성 & 데이터 삽입

재귀적이지 않은 CTE 예제를 위해 고객 거래 내역 테이블을 사용할 것입니다. 입력 테이블 및 데이터를 재생성하는 데 필요한 테이블 생성 및 데이터 삽입 문을 여기에 제공합니다.

<div class="content-ad"></div>

```js
CREATE TABLE transactions (
    TransactionID INT PRIMARY KEY,
    CustomerID INT,
    Amount DECIMAL(10, 2),
    TransactionDate DATE
);

INSERT INTO Transactions (TransactionID, CustomerID, Amount, TransactionDate)
VALUES (1, 1001, 150, TO_DATE('2021-01-01', 'YYYY-MM-DD'));

INSERT INTO Transactions (TransactionID, CustomerID, Amount, TransactionDate)
VALUES (2, 1002, 200, TO_DATE('2021-01-04', 'YYYY-MM-DD'));

INSERT INTO Transactions (TransactionID, CustomerID, Amount, TransactionDate)
VALUES (3, 1001, 100, TO_DATE('2021-01-04', 'YYYY-MM-DD'));

INSERT INTO Transactions (TransactionID, CustomerID, Amount, TransactionDate)
VALUES (4, 1003, 250, TO_DATE('2021-01-05', 'YYYY-MM-DD'));

INSERT INTO Transactions (TransactionID, CustomerID, Amount, TransactionDate)
VALUES (5, 1002, 300, TO_DATE('2021-01-05', 'YYYY-MM-DD'));

INSERT INTO Transactions (TransactionID, CustomerID, Amount, TransactionDate)
VALUES (6, 1003, 180, TO_DATE('2021-01-08', 'YYYY-MM-DD'));

INSERT INTO Transactions (TransactionID, CustomerID, Amount, TransactionDate)
VALUES (7, 1001, 190, TO_DATE('2021-01-08', 'YYYY-MM-DD'));
```

```js
SELECT * FROM Transactions


TRANSACTIONID CUSTOMERID AMOUNT TRANSACTIONDATE
============= ================= ===============
1             1001       150    01-JAN-21
2             1002       200    04-JAN-21
3             1001       100    04-JAN-21
4             1003       250    05-JAN-21
5             1002       300    05-JAN-21
6             1003       180    08-JAN-21
7             1001       190    08-JAN-21
```

표준 CTE 구문은 놀랍도록 간단합니다. 단순히,

```js
WITH cte_name [(column_list)] AS (cte_query_definition) statement
```

<div class="content-ad"></div>

다음과 같은 표가 있습니다:

- cte_name은 CTE에 지정된 이름입니다
- column_list는 CTE의 열 이름 목록입니다(선택 사항)
- cte_query_definition은 CTE의 결과 집합을 정의하는 쿼리입니다
- statement은 CTE를 참조하는 단일 SELECT, INSERT, UPDATE, DELETE 또는 MERGE 문입니다

## 테스트 1 — 간단한 CTE

```js
WITH CustomerTotals AS (
    SELECT CustomerID, SUM(Amount) AS TotalSpent
    FROM Transactions
    GROUP BY CustomerID
)
SELECT CustomerID, TotalSpent
FROM CustomerTotals
WHERE TotalSpent > 250;
```

<div class="content-ad"></div>

```js
CUSTOMERID | TOTALSPENT
---------- | ----------
1001       | 440
1002       | 500
1003       | 430
```

이 경우, 이 쿼리의 non-CTE 버전도 간단합니다. 다음과 같습니다.

```js
SELECT CustomerID, SUM(Amount) AS TotalSpent
FROM Transactions
GROUP BY CustomerID
HAVING SUM(Amount) > 250;
```

## 테스트 2 — 더 복잡한 CTE

<div class="content-ad"></div>

CTE가 자신을 발휘하는 곳은 이와 유사한 쿼리를 갖고 있을 때입니다.

CTE를 사용하면 이와 같은 쿼리는 비교적 쉬워집니다. 필요한 두 집계값은 각각 두 개의 개별 CTE로 분리할 수 있고, 그 결과를 단순히 비교하여 얻을 수 있습니다.

```js
WITH CustomerAverages AS (
    SELECT CustomerID, AVG(Amount) AS AvgAmount
    FROM Transactions
    GROUP BY CustomerID
),
OverallAverage AS (
    SELECT AVG(Amount) AS OverallAvg
    FROM Transactions
)
SELECT a.CustomerID, a.AvgAmount
FROM CustomerAverages a, OverallAverage o
WHERE a.AvgAmount > o.OverallAvg;
```

```js
CUSTOMERID AVGAMOUNT
========== =========
1002       250
1003       215
```

<div class="content-ad"></div>

저는 이것을 비-CTE 버전과 비교해 봤을 때,

```js
SELECT CustomerID, AVG(Amount) AS AvgAmount
FROM transactions
GROUP BY CustomerID
HAVING AVG(Amount) > (
    SELECT AVG(sub.AvgAmount)
    FROM (
        SELECT AVG(Amount) AS AvgAmount
        FROM transactions
        GROUP BY CustomerID
    ) sub
);
```

내가 생각하기에, CTE를 사용하면 SQL 작성자의 의도가 더 명확해지고 무엇이 일어나고 있는지 더 명확해집니다.

## 테스트 3 — 재귀 CTEs

<div class="content-ad"></div>

재귀 CTE(Commmon Table Expressions)는 쿼리 중 하나의 CTE가 자신을 참조하는 쿼리입니다. 예제에서 별명 정보를 사용하여 위의 CTE 예제를 재귀 CTE를 사용하여 작성할 수 있다는 것을 기믈했을지도 모릅니다.

```js
WITH CustomerAverages AS (
    SELECT CustomerID, AVG(Amount) AS AvgAmount
    FROM Transactions
    GROUP BY CustomerID
),
OverallAverage AS (
    SELECT AVG(AvgAmount) AS OverallAvg
    FROM CustomerAverages
)
SELECT a.CustomerID, a.AvgAmount
FROM CustomerAverages a, OverallAverage o
WHERE a.AvgAmount > o.OverallAvg;
```

두 번째 CTE인 OverallAverage에 필요한 정보는 이미 첫 번째 CTE인 CustomerAverages에 포함되어 있었기 때문에 첫 번째 CTE의 데이터를 두 번째 CTE의 계산에 사용할 수 있었습니다. 

```js
with recursive cte(col1, col2 etc…) as my_cte
...
...
```

<div class="content-ad"></div>

오라클은 CTE가 쿼리 내에서 자기 자신을 참조한다면 재귀적이라고 가정하기 때문에 특별한 재귀 키워드가 필요하지 않습니다.

최종 예제로, 우리는 원본 시계열 데이터의 간격을 채우기 위해 재귀적인 CTE를 사용할 것입니다.

원본 테이블 데이터로 돌아가면, 1월 2일 및 3일, 그리고 1월 6일 및 7일의 고객 데이터 항목이 누락되어 있다는 것을 알 수 있습니다.

우리의 작업은 매일의 고객 지출 총액을 보여주는 보고서를 생성하는 것입니다. 날짜에 대한 항목이 없는 경우, 해당 날짜의 고객 지출 값의 합계를 위해 제로를 반환합니다.

<div class="content-ad"></div>

이 재귀 CTE에 대한 좋은 사용 사례입니다.

```js
WITH DateRange (dt) AS (
    SELECT MIN(TRANSACTIONDATE) 
    FROM Transactions
    UNION ALL
    SELECT dt + INTERVAL '1' DAY 
    FROM DateRange WHERE dt < (SELECT MAX(TRANSACTIONDATE) FROM Transactions)
),
AggregatedData AS (
    SELECT TRANSACTIONDATE, SUM(AMOUNT) AS TOTAL_SPEND
    FROM Transactions
    GROUP BY TRANSACTIONDATE
)
SELECT dr.dt AS TRANSACTIONDATE,
       NVL(ad.TOTAL_AMOUNT, 0) AS TOTAL_SPEND
FROM DateRange dr
LEFT JOIN AggregatedData ad ON dr.dt = ad.TRANSACTIONDATE
ORDER BY dr.dt;

TRANSACTIONDATE  TOTAL_SPEND
===============  ===========
01-JAN-21        150
02-JAN-21        0
03-JAN-21        0
04-JAN-21        300
05-JAN-21        550
06-JAN-21        0
07-JAN-21        0
08-JAN-21        370
```

- DateRange CTE: 이 부분은 가장 이른 날짜부터 가장 늦은 거래일까지 연속된 날짜 범위를 생성하여 날짜 간격이 없음을 보장합니다.
- AggregatedData CTE: 각 날짜의 거래를 합산합니다.
- 최종 SELECT: 날짜 범위를 집계된 거래 데이터와 조인하며, NVL 함수를 사용하여 NULL 값을 0으로 대체하여 거래가 없는 날은 총액이 0으로 나타나도록 합니다.

## 요약

<div class="content-ad"></div>

결론적으로, Common Table Expressions(CTEs)는 SQL에서 가독성, 유지 관리 및 복잡한 쿼리의 실행을 개선할 수 있는 다재다능하고 강력한 기능입니다. 이 글을 통해 CTE의 기본 구조와 기능을 탐색하며, 비재귀 및 재귀 유형을 살펴 다양한 시나리오에서 그 유틸리티를 보여주었습니다.

포함된 세 가지 테스트 케이스는 CTE가 복잡한 SQL 작업의 관리를 단순화하고 이를 더 쉽게 다룰 수 있는 부분으로 나누어주며, 재귀 데이터를 처리하는 능력을 보여주었습니다.

또한, CTE 사용은 더 깨끗하고 조직적인 SQL 스크립트로 이어지며, 개발자와 분석가가 데이터베이스 쿼리를 작성, 디버깅 및 최적화하는 작업이 쉬워집니다.

아직도 CTE에 대해 확신이 없다면, 제 3번 테스트 예제에서 CTE를 사용하지 않고 어떻게 문제를 해결할지 생각해보세요. 이런 문제에 대한 전통적인 SQL 접근 방식은 더 불편하고 효율적이지 않다는 것을 알게 될 것입니다.

<div class="content-ad"></div>

이 컨텐츠가 마음에 드셨다면, 이 아티클도 흥미롭게 보실 것 같아요.