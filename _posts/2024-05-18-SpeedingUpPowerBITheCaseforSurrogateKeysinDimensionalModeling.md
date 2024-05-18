---
title: "Power BI 최적화 차원 모델링에서 서로간키의 필요성"
description: ""
coverImage: "/assets/img/2024-05-18-SpeedingUpPowerBITheCaseforSurrogateKeysinDimensionalModeling_0.png"
date: 2024-05-18 18:05
ogImage: 
  url: /assets/img/2024-05-18-SpeedingUpPowerBITheCaseforSurrogateKeysinDimensionalModeling_0.png
tag: Tech
originalTitle: "Speeding Up Power BI: The Case for Surrogate Keys in Dimensional Modeling"
link: "https://medium.com/data-engineer-things/speeding-up-power-bi-the-case-for-surrogate-keys-in-dimensional-modeling-2a6d060a0ab7"
---


```md
![Surrogate Key](/assets/img/2024-05-18-SpeedingUpPowerBITheCaseforSurrogateKeysinDimensionalModeling_0.png)

# 문제 설명

최근에 나는 대체키를 조합키 대신 사용하는 것을 연구하는 업무를 맡았습니다. 지금까지 우리 팀은 레코드를 고유하게 나타내고 차원과 사실을 차원 모델에 연결하는 데 조합 키를 사용했습니다. 조합 키는 작업을 수행했지만 Power BI 측에서 쿼리 실행 시간이 만족스럽지 않았습니다. 조인이 오랜 시간이 걸렸는데 그 이유는 조합된 키의 열 크기가 큰 것입니다. Power BI에서는 대규모 관계(키) 열이있을 때 쿼리가 훨씬 느리게 실행됩니다.

내 목표는 Power BI에서 쿼리 실행 시간을 줄일 방법을 찾는 것이었습니다. 차원과 사실을 결합하기 위해 대체 키를 조합 키로 교체하는 것은 제가 자세히 탐색한 대안 중 하나였습니다. 이 기사에서는 대체 키의 정의, 목적 및 구현하는 대안 방법에 대해 안내하겠습니다. 실용적인 예제는 PySpark에서 보여집니다.
```

<div class="content-ad"></div>

# 대리 키란 무엇인가요?

남들의 말을 바꾸는 대신, 관계형 데이터베이스의 맥락에서 대리 키 및 다른 유형의 키를 다루는 이 글을 참고해보세요.

내가 대리 키를 정의하는 방식은 이러하다. 대리 키는 인공적이며 비즈니스나 현실 세계와 관련이 없으며 어떠한 비즈니스 개념과도 연결되지 않는다. 레코드를 고유하게 식별하는 데 도움이 되는 고유성을 가지고 있으며 대부분 (항상은 아니지만) 정수 형태이다.

아래 직원 테이블에서 직원 ID 열이 대리 키의 예시입니다.

<div class="content-ad"></div>

```markdown
![Surrogate Key](/assets/img/2024-05-18-SpeedingUpPowerBITheCaseforSurrogateKeysinDimensionalModeling_1.png)

서로게이트 키는 여러 가지 이유로 존재합니다. 하나의 명확한 답변은 해당 테이블에 자연 키의 명백한 후보가 없고 레코드를 고유하게 식별하기 위해 (서로게이트 키라고도 함) 가짜 키를 생성해야하는 경우입니다.

두 번째 명백한 이유는 시간의 시험을 견디는 능력입니다. 이 이유는 데이터 웨어하우스 디자인의 맥락에서 더 관련이 있습니다. 기본 키로 자연 키에 의존하는 것은 데이터베이스 수준에서 시간이 지남에 따라 변경 사항에 취약해질 수 있습니다.

구글의 주식 심볼을 예로 들 수 있습니다.
```

<div class="content-ad"></div>

원래 구글 주식의 종류는 GOOG 주식 심볼을 가지고 있었습니다. 2014년 4월, 회사는 GOOGLE 주식 심볼 아래 새로운 주식 종류를 만들었습니다. 데이터베이스 디자인에서 구글 주식을 나타내는 주요 키로 원래 GOOG 심볼을 사용하고 있다면, 이 변경 사항을 반영하기 위해 적절한 수정을 해야 할 것입니다. Kimball (1998)은 이를 정확히 이유로 자연 키를 사용하는 것을 지양해야 한다고 주장했습니다. 그는 트랜잭션 데이터베이스의 자연 키는 "생산의 지시에 따라 생성, 형식화, 업데이트, 삭제, 재활용 및 재사용"되기 때문에 트랜잭션 데이터베이스 수준에서 발생하는 변경 사항에 지속적으로 의존해야 한다고 설명했습니다.

대리 키의 세 번째 이유는 쿼리 성능이 더 좋다는 것입니다. 이는 특히 데이터 웨어하우스의 맥락에서 맞닿은 사실입니다. 차원 모델은 차원과 사실 사이에서 많은 조인을 수행해야 한다. 수치 (일반적으로 정수 또는 smallint)인 대리 키를 사용하여 조인하는 것은 문자열로 이루어진 자연 키나 복합 키로 조인하는 것보다 빠릅니다. 이 이유에 대해 더 자세히 알아보려면 여기를 읽어보세요.

# 대리 키 생성의 대체 방법

대리 키 구현에 대한 제 연구로 결과적으로 대리 키를 생성하는 주요 방법은 두 가지라고 결론내렸습니다.

<div class="content-ad"></div>

한 가지 방법은 단조 증가하는 정수를 생성하는 전통적인 함수를 통해 정수를 만드는 것입니다. 이 함수는 테이블의 고유한 값에 대해 정수를 생성합니다.

다른 대안은 암호 해싱 함수를 사용하는 것입니다. 해싱 함수는 데이터 자체에 대해 고유한 값을 생성하며, "동일한 입력 집합은 항상 동일한 출력 집합을 생성한다"는 의미입니다(Connors, 2022). 해싱 함수의 몇 가지 예는 crc32, md5, sha1/ sha2 등이 있습니다.

이 두 가지 방법을 PySpark에서 어떻게 구현하는지 살펴봅시다.

## 단조 증가하는 정수

<div class="content-ad"></div>

쇼핑 목록에 제품 목록과 제품이 목록에 추가된 타임스탬프가 있는 테이블이 있다고 상상해보세요.

| Product      | Timestamp            |
|--------------|----------------------|
| Product A    | 2024-09-15 10:30:00  |
| Product B    | 2024-09-15 11:45:00  |
| Product C    | 2024-09-15 13:20:00  |

제품 차원의 서로 다른 키를 생성하려면 monotonically_increasing_id() 함수를 사용해야 합니다. 이 함수를 적용하기 전에 제품 목록이 고유한지 확인해야 합니다.

<div class="content-ad"></div>

위의 제품 테이블은 바나나와 빵이 2일에 나누어서 입력되어서 두 번씩 나타납니다. 따라서 단계는 제품 열만 선택한 다음, 값을 고유하게 만들고 monotonically_increasing_id() 함수를 적용하는 것입니다.

한 가지 더 고려해야 할 사항은 테이블에 NULL 값이 있는지 여부입니다. 이를 함수를 적용하기 전에 필터링해야 합니다. NULL 값에 대한 대체 키 ID를 부여하고 싶지 않다면 필터링을 해야 합니다.

```js
from pyspark.sql.functions import monotonically_increasing_id, col

#clean
df_select=df.select(
  col('product')
  )
df_distinct=df_select.distinct()

#monotonically_increasing_id 함수를 사용하여 대체 키 생성
df_sk=df_distinct.withColumn(
  "surrogate_key",
  monotonically_increasing_id()
  )
```

출력 결과는 다음과 같을 것입니다:

<div class="content-ad"></div>

```markdown
![image](/assets/img/2024-05-18-SpeedingUpPowerBITheCaseforSurrogateKeysinDimensionalModeling_3.png)

모노토닉하게 증가하는 정수는 결정론적이 아니며, 동일한 값 목록에 대해 이 기능을 실행해도 항상 동일한 대리 키가 할당되지는 않습니다. 예를 들어, 사과는 항상 ID 1이 할당되는 것은 아닙니다. 대리 키의 유지보수는 따라서 몇 가지 추가 작업이 필요할 수 있습니다.

이를 해결하는 한 가지 방법은 사실보다 차원을 먼저 로드하는 것입니다. 저의 팀에서 구현한 방법은 먼저 대리 키를 사용하여 차원을 생성한 다음 사실에서 차원을 사용하여 대리 키를 조회하는 것입니다. 이는 차원과 사실의 참조 무결성을 보장합니다.

## 해싱 함수
```

<div class="content-ad"></div>

해싱 함수에 대한 내 연구는 그들이 무엇인지 이해하고 대체 키 생성을 위한 전통적인 함수들이 언제 선호되는지를 이해하는 데 국한되어 있었습니다. 따라서 해싱 함수들 간의 차이점이나 그들을 어떻게 구현하는지에 대해 자세히 다루지는 않겠습니다. PySpark에서 구현 측면을 다루는 이 Medium 기사를 발견했습니다.

나는 해싱 함수를 선택하지 않은 이유는 해싱 함수를 사용하여 생성된 대체 키들이 종종 더 긴 문자열 값을 갖기 때문입니다. 나가 해결하려고 하는 문제는 Power BI에서 조인의 쿼리 성능을 개선하는 것이었습니다. 긴 합성 키를 해싱 함수에 의해 생성된 긴 키로 바꾸는 것은 나의 문제에 적합한 해결책이 아니었습니다.

하지만 해싱 함수를 매력적으로 만드는 것은 해싱 함수를 통해 생성된 대체 키들이 상기에서 논의된 단조증가 정수보다 유지하기가 훨씬 더 쉽고 (그리고 덜 복잡)이라는 점입니다. 해싱 함수는 결정론적이기 때문에 동일한 입력 세트는 항상 동일한 출력을 생성합니다. 이상적으로는 차원 및 사실을 병렬로 대체 키를 생성할 수 있습니다.

# 요약

<div class="content-ad"></div>

이 기사에서는 Power BI에서 쿼리 성능을 향상시키는 데 중점을 두며, 차원 모델의 컴포지트 키에 대한 효율적인 대안으로서 대리키의 사용을 탐색했습니다.

비즈니스 의미를 가지지 않는 고유 식별자인 대리키는 데이터 웨어하우스에서 차원과 사실 사이의 관계 관리를 크게 최적화할 수 있습니다.

우리는 이러한 키를 생성하는 다양한 방법을 탐구했습니다. PySpark에서 실용적인 예제를 통해 각 접근 방식의 이점과 고려 사항을 고려하여 데이터 아키텍처 요구에 맞는 올바른 전략을 선택하는 데 도움이 될 수 있도록 안내했습니다.

# 자원

<div class="content-ad"></div>

| Author                    | Title                                                                       | Year | Source           |
|---------------------------|-----------------------------------------------------------------------------|------|------------------|
| Ben                       | Database Keys: The Complete Guide (Surrogate, Natural, Composite & More)    | 2022 | Database Star   |
| Connor, Dave              | Surrogate Keys In dbt: Integers or Hashes?                                   | 2022 | dbt             |
| Kimball, R.               | Surrogate Keys                                                              | 1998 | Kimball Group   |
| Stiglich, Peter           | Performance Benefits of Surrogate Keys in Dimensional Models                | -----| EWS Solutions   |

<div class="content-ad"></div>

| 저자 | 제목 | 출판연도 | 출처 |
|---|---|---|---|
| Wikstrom, Max | Power BI Data Types In Relationships- Does It Matter? | 2022 | Data, Business Intelligence and Beyond |
| Zaman, Ahmed Uz | PySpark Hash Functions: A Comprehensive Guide | 2023 | Medium |