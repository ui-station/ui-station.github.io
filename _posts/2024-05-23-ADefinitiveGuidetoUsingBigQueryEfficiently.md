---
title: "효율적으로 BigQuery를 사용하는 결정적 가이드"
description: ""
coverImage: "/assets/img/2024-05-23-ADefinitiveGuidetoUsingBigQueryEfficiently_0.png"
date: 2024-05-23 16:02
ogImage:
  url: /assets/img/2024-05-23-ADefinitiveGuidetoUsingBigQueryEfficiently_0.png
tag: Tech
originalTitle: "A Definitive Guide to Using BigQuery Efficiently"
link: "https://medium.com/towards-data-science/burn-data-rather-than-money-with-bigquery-the-definitive-guide-1b50a9fdf096"
---

## 빅쿼리 사용을 최대한 활용하여 실질적 가치를 창출할 때, 돈 대신 데이터를 소모하세요. 실용적 기술로 진짜 가치를 창출해보세요.

- 📝 소개
- 💎 빅쿼리 기본 및 비용 이해
  - 저장
  - 계산
- 📐 데이터 모델링
  - 데이터 유형
  - 정규화에서 비정규화로의 전환
  - 파티셔닝
  - 클러스터링
  - 중첩 반복 열
  - 인덱싱
  - 물리 바이트 저장 요금 청구
  - 기본 키 및 외래 키를 활용한 조인 최적화
- ⚙️ 데이터 작업
  - 데이터/테이블 복사
  - 데이터 로드
  - 파티션 삭제
  - 테이블의 고유한 파티션 가져오기
  - 계산된 측정값을 영속적으로 저장하지 말기
- 📚 요약
  - 데이터 모델링 최상의 방법 도입
  - 비용 효율성을 위한 데이터 작업 마스터
  - 효율성을 위한 설계 및 불필요한 데이터 영속화 피하기

참고: 빅쿼리는 지속적으로 개발되는 제품이며, 가격은 언제든지 변경될 수 있습니다. 이 글은 제 개인적인 경험을 기반으로 작성되었습니다.

# 📝 소개

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

데이터 웨어하우징 분야에서는 보편적인 진실이 있어요: 데이터 관리는 비용이 많이 들 수 있다는 거죠. 마치 보물을 지키는 드래곤처럼, 저장된 각 바이트와 실행된 각 쿼리는 금화의 일부를 요구합니다. 하지만 제가 여러분께 드래곤을 달래는 마법 주문을 전해드리겠어요: 돈을 사용하는 게 아니라 데이터를 태워버리세요!

이 기사에서는 비용을 줄이고 효율성을 높이는 빅쿼리 주술의 기술을 풀어보겠습니다. 모든 바이트가 소중한 동전인 비용 최적화의 심연으로 함께 여행해보세요.

# 💎 빅쿼리 기본 사항 및 비용 이해

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

빅쿼리는 그냥 도구가 아니라 구글에서 관리하는 확장 가능한 컴퓨트 및 저장 기술의 패키지입니다. 빠른 네트워크를 통해 모든 것을 관리합니다. 빅쿼리는 분석 목적을 위한 서버리스 데이터 웨어하우스로, 빅쿼리 ML과 같은 기능이 내장되어 있습니다. 빅쿼리는 구글의 Jupiter 네트워크를 통해 저장과 컴퓨트를 분리하여 1 Petabit/sec의 총 이분면 대역폭을 활용합니다. 저장 시스템은 구글의 반정형 데이터를 위한 전용 열 지향 저장 형식인 Capacitor를 사용하며, 하부 파일 시스템은 구글의 분산 파일 시스템인 Colossus입니다. 컴퓨트 엔진은 Dremel을 기반으로 하며, 수천 개의 Dremel 작업을 클러스터 내에서 실행하는 클러스터 관리에 Borg를 사용합니다.

다음 그림은 빅쿼리의 기본 아키텍처를 보여줍니다:

![빅쿼리 아키텍처](/assets/img/2024-05-23-ADefinitiveGuidetoUsingBigQueryEfficiently_1.png)

데이터는 Colossus에 저장될 수 있지만, 구글 클라우드 스토리지에 저장된 데이터 위에 빅쿼리 테이블을 만드는 것도 가능합니다. 이 경우 쿼리는 여전히 빅쿼리 컴퓨트 인프라를 사용하여 처리되지만 GCS에서 데이터를 읽습니다. 이러한 외부 테이블은 일부 단점이 있지만 경우에 따라 GCS에 데이터를 저장하는 것이 더 비용 효율적일 수 있습니다. 때로는 빅 데이터가 아니라 기존 CSV 파일에서 데이터를 간단히 읽는 것뿐인 경우도 있습니다. 이러한 종류의 테이블을 사용하는 것이 간단하고 편리할 수 있습니다.

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

**Markdown 형식으로 테이블 태그 변경**

<img src="/assets/img/2024-05-23-ADefinitiveGuidetoUsingBigQueryEfficiently_2.png" />

BigQuery의 모든 잠재력을 활용하기 위해서는 데이터를 BigQuery 저장소에 저장하는 것이 보편적입니다.

비용의 주요 구성 요소는 저장소와 컴퓨트이며, Google은 저장소와 컴퓨트 사이의 네트워크 전송과 같은 다른 부분에 대해 요금을 부과하지 않습니다.

## 저장소

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

저장 공간은 GB당 $0.02이며, 활성 데이터는 GB당 $0.04, 비활성 데이터(최근 90일간 수정되지 않은 데이터)는 GB당 $0.01입니다. 테이블이나 파티션을 90일 연속으로 수정하지 않으면 장기 저장으로 간주되어 저장 공간 비용이 자동으로 50% 떨어집니다. 할인은 테이블 또는 파티션 단위로 적용됩니다. 데이터 수정 시 90일 카운터가 재설정됩니다.

## 계산

BigQuery는 쿼리 실행 시간이 아닌 스캔된 데이터에 대해 청구하며, 저장 공간에서 컴퓨트 클러스터로 전송하는 비용은 청구되지 않습니다. 컴퓨트 비용은 위치에 따라 달라지며, 예를 들어 europe-west3의 비용은 TB당 $8.13입니다.

즉,

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

쿼리를 실행할 때, BigQuery는 처리될 데이터량을 추정합니다. BigQuery Studio 쿼리 편집기에 쿼리를 입력한 후에는 오른쪽 상단에 추정치를 확인할 수 있어요.

![이미지](/assets/img/2024-05-23-ADefinitiveGuidetoUsingBigQueryEfficiently_3.png)

위 스크린샷에서와 같이 1.27 GB가 나오고, 쿼리가 europe-west3 지역에서 처리된다면, 비용은 다음과 같이 계산할 수 있어요:

```js
1.27 GB / 1024 = 0.0010 TB * $8.13 = $0.0084 총 비용
```

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

대부분 예측은 비관적인 계산이며, 종종 옵티마이저는 캐시된 결과, 자재화된 뷰 또는 기타 기술을 사용하여 실제 청구 바이트가 예측보다 낮게 될 수 있습니다. 여전히 작업의 영향을 대략적으로 파악하기 위해 이 예측을 확인하는 것이 좋은 실천법입니다.

또한 쿼리에 대한 청구 바이트의 최대값을 설정할 수도 있습니다. 쿼리가 제한을 초과하면 실패하고 전혀 비용이 발생하지 않습니다. 이 설정은 '더 보기 - 쿼리 설정 - 고급 옵션 - 최대 청구 바이트'로 이동하여 변경할 수 있습니다.

![image](/assets/img/2024-05-23-ADefinitiveGuidetoUsingBigQueryEfficiently_4.png)

![image](/assets/img/2024-05-23-ADefinitiveGuidetoUsingBigQueryEfficiently_5.png)

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

아쉽게도 현재까지는 쿼리당 기본값을 설정하는 것이 불가능합니다. 하루에 사용자당 프로젝트당 또는 프로젝트당 모든 바이트에 대해 청구된 바이트를 제한하는 것만 가능합니다.

BigQuery를 처음 사용할 때는 대부분 온디맨드 컴퓨트 요금 모델을 계속 사용할 것입니다. 온디맨드 가격 책정을 사용하면 일반적으로 하나의 프로젝트에서 모든 쿼리 사이에서 공유되는 최대 2000개의 동시 슬롯에 액세스할 수 있으며, 대부분의 경우 충분합니다. 슬롯은 쿼리 DAG의 작업 단위에서 작동하는 가상 CPU와 같습니다.

월별 지출이 특정 금액에 도달하면 더 예측 가능한 비용을 제공하는 용량 가격 책정 모델을 고려할 가치가 있습니다.

# 📐 데이터 모델링

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

## 데이터 유형

저장 및 계산 비용을 줄이기 위해 항상 열에 가능한 가장 작은 데이터 유형을 사용하는 것이 매우 중요합니다. 다음 개요를 따라 특정 행 수에 대한 비용을 쉽게 추정할 수 있습니다:

```js
유형        | 크기
-----------|---------------------------------------------------------------
ARRAY      | 요소의 크기 합계
BIGNUMERIC | 32 논리 바이트
BOOL       | 1 논리 바이트
BYTES      | 2 논리 바이트 + 값의 논리 바이트
DATE       | 8 논리 바이트
DATETIME   | 8 논리 바이트
FLOAT64    | 8 논리 바이트
GEOGRAPHY  | 16 논리 바이트 + 지오 타입의 정점 개수 * 24 논리 바이트
INT64      | 8 논리 바이트
INTERVAL   | 16 논리 바이트
JSON       | JSON 문자열의 UTF-8 인코딩된 논리 바이트
NUMERIC    | 16 논리 바이트
STRING     | 2 논리 바이트 + UTF-8 인코딩된 문자열 크기
STRUCT     | 0 논리 바이트 + 포함된 필드의 크기
TIME       | 8 논리 바이트
TIMESTAMP  | 8 논리 바이트
```

NULL은 0 논리 바이트로 계산됩니다.

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

예시:

```js
CREATE TABLE gold.some_table (
  user_id INT64,
  other_id INT64,
  some_String STRING, -- 최대 10 자
  country_code STRING(2),
  user_name STRING,   -- 최대 20 자
  day DATE
);
```

위의 정의와 데이터 유형 테이블을 사용하여 100,000,000개의 행의 논리적 크기를 추정할 수 있습니다:

```js
100,000,000개 행 * (
  8바이트 (INT64) +
  8바이트 (INT64) +
  2바이트 + 10바이트 (STRING) +
  2바이트 + 2바이트 (STRING(2)) +
  2바이트 + 20바이트 (STRING) +
  8바이트 (DATE)
) = 6200000000 바이트 / 1024 / 1024 / 1024
  = 5.78 GB
```

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

우리가 이 테이블에 대해 SELECT _를 실행한다고 가정하면, Europe-west3에서는 5.78 GB / 1024 = 0.0056 TB _ $8.13 = $0.05가 든다.

데이터 모델을 설계하기 전에 이러한 계산을 하는 것은 좋은 생각입니다. 데이터 유형 사용을 최적화하는 것뿐만 아니라 작업 중인 프로젝트에 대한 비용을 예상할 수도 있기 때문입니다.

## Denormalization으로의 이동

데이터베이스 설계 및 관리 영역에서 데이터 정규화와 비정규화는 효율적인 저장, 검색 및 조작을 위해 데이터 구조를 최적화하기 위한 근본적인 개념들입니다. 전통적으로 정규화는 중복을 줄이고 데이터 무결성을 보존하는 것을 강조한 최고의 실천 방법으로 광명해왔습니다. 그러나 BigQuery와 다른 현대 데이터 웨어하우스의 맥락에서는 상황이 변하며 비정규화가 종종 선호되는 접근 방식으로 떠오르게 됩니다.

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

정규화된 데이터베이스에서는 데이터가 여러 개의 테이블로 구조화되어 있으며, 각 테이블은 서로 다른 개체나 개념을 나타내며, 일대일, 일대다 또는 다대다와 같은 관계를 통해 연결됩니다. 이 접근 방식은 데이터베이스 정규화 양식에 의해 제시된 원칙을 따르며, 예를 들어 제1정규형(1NF), 제2정규형(2NF), 제3정규형(3NF) 등이 있습니다.

이로 인해 중복 감소, 데이터 무결성 및 결과적으로 저장 공간 사용량이 줄어드는 이점이 있습니다.

![image](/assets/img/2024-05-23-ADefinitiveGuidetoUsingBigQueryEfficiently_6.png)

데이터 정규화는 전통적인 관계형 데이터베이스에서는 가치가 있는 반면, BigQuery와 같은 현대적인 분석 플랫폼과 상호작용할 때는 패러다임이 변경됩니다. BigQuery는 대량의 데이터를 처리하고 규모에 맞는 복잡한 분석 쿼리를 수행하기 위해 설계되었습니다. 이 환경에서는 저장 공간을 최소화하는 것보다 쿼리 성능을 최적화하는 데 중점이 두어집니다.

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

BigQuery에서는 여러 이유로 비정규화가 선호되는 전략으로 부상합니다:

- 쿼리 성능: BigQuery의 분산 아키텍처는 대용량 데이터를 병렬로 스캔하는 데 뛰어납니다. 비정규화된 테이블은 복잡한 조인이 필요 없어져 쿼리 실행 시간이 빨라집니다.
- 비용 효율성: 질의 처리에 필요한 계산 리소스를 최소화함으로써, 비정규화는 데이터 처리 양에 기반한 BigQuery의 쿼리 비용을 절감할 수 있습니다.
- 단순화된 데이터 모델링: 비정규화된 테이블은 데이터 모델링 프로세스를 간단하게 만들어 분석 목적으로 스키마를 설계하고 유지하기 쉽게 합니다.
- 분석 워크로드에 최적화: 비정규화된 구조는 집계, 변환 및 복잡한 쿼리가 일반적인 분석 워크로드에 적합합니다.

또한, 저장소가 계산보다 훨씬 저렴하기 때문에:

![image](/assets/img/2024-05-23-ADefinitiveGuidetoUsingBigQueryEfficiently_7.png)

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

비정규화는 일반적으로 모든 상황에 적합한 해결책은 아니지만, 비용 및 성능 최적화를 위해 고려되어야 합니다. 그러나 다양한 측면이 있어 비용 효율적인 설계로 이어질 수 있습니다.

특히 JOIN 오른쪽에 작은 테이블이 있는 경우, BigQuery는 Broadcast Join을 사용하여 각 슬롯에 전체 테이블 데이터 집합을 브로드캐스트하여 더 큰 테이블을 처리하는 방식으로 사용합니다. 이로 인해 정규화는 성능에 부정적인 영향을 미치지 않습니다. 사실, 그 반대가 되며 데이터 중복이 줄어들어 성능이 향상됩니다.

BigQuery가 Broadcast Join을 사용하지 않을 때는 해시 조인 방식을 사용합니다. 이 경우 BigQuery는 해시 및 셔플 작업을 사용하여 일치하는 키가 동일한 슬롯에서 처리되도록 하여 로컬 조인을 수행합니다. 그러나 Broadcast Join과 비교할 때 이 작업은 데이터 이동이 필요하기 때문에 비용이 많이 들 수 있습니다.

해시 조인이 사용되는 상황에 있다면, 여전히 성능을 개선할 수 있는 방법이 있습니다. 적어도 조인 열을 클러스터 열로 정의하는 것을 목표로 설정하세요. 이렇게 하면 데이터가 동일한 열 파일에 공존되어 셔플링의 영향이 줄어듭니다.

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

마지막으로, 최상의 접근 방식은 데이터 모델의 구체적인 내용과 정규화된 테이블의 크기에 따라 다릅니다. 정규화된 구조로 중복을 줄일 수 있고 동시에 JOIN 테이블의 크기를 작게 유지하여 Broadcast Joins를 사용할 수 있다면, 이것은 비정규화된 접근 방식보다 나은 해결책입니다. 그러나 10G 이상의 테이블에 대해서는 구체적인 벤치마킹을 통해 평가해야 하며, 이로 인해 아래의 황금 규칙이 나오게 됩니다:

벤치마킹이 필수입니다! 이론에만 의존하지 마세요. 다양한 접근 방식(정규화, 비정규화, 중첩/반복)을 시험하여 특정 사용 사례에 가장 효율적인 해결책을 찾아보세요.

## 파티션

파티션은 한 가지 특정 열을 기준으로 테이블을 세그먼트로 나눕니다. 파티션 열은 3가지 접근 방식 중 하나를 사용할 수 있습니다:

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

🗂️ 정수 범위 분할: 시작, 끝 및 간격을 기준으로 정수 열에 따라 분할하기

⏰ 시간 단위 분할: 시간 단위, 일별, 월별 또는 연간 단위로 테이블의 날짜, 타임스탬프 또는 날짜/시간 열에 따라 분할하기

⏱️ 적재 시간 분할: 현재 시간을 기준으로 데이터를 삽입할 때 \_PARTITIONTIME이라는 가상 열을 사용하여 파티션 자동 할당하기

파티션 열을 정의하는 것은 여러분에게 달려 있지만, 처리된 바이트량/청구액을 줄일 수 있는지 현명하게 선택하는 것이 매우 권장됩니다.

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

![image](/assets/img/2024-05-23-ADefinitiveGuidetoUsingBigQueryEff_8.png)

예시:

```js
CREATE TABLE IF NOT EXISTS silver.some_partitioned_table (
  title STRING,
  topic STRING,
  day DATE
)
PARTITION BY day
OPTIONS (
  partition_expiration_days = 365
);
```

위 예시에서는 partition_expiration_days 옵션을 설정하는 방법도 확인할 수 있어요. 이 옵션은 X일 이전의 파티션을 자동으로 삭제해줍니다.

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

## 클러스터링

클러스터는 각 파티션 내의 데이터를 하나 이상의 열을 기반으로 정렬합니다. 쿼리 필터에서 클러스터 열을 사용할 때 이 기술을 사용하면 빅쿼리가 어떤 블록을 스캔해야 하는지 결정할 수 있어 실행 속도가 향상됩니다. 특히 다음 예제의 제목 열과 같은 고 카디널리티 열과 함께 사용하기를 권장합니다.

최대 네 개의 클러스터 열을 정의할 수 있습니다.

예시:

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

```js
CREATE TABLE IF NOT EXISTS silver.some_partitioned_table (
  title STRING,
  topic STRING,
  day DATE
)
PARTITION BY day
CLUSTER BY topic
OPTIONS (
  partition_expiration_days = 365
);
```

## 중첩된 반복 열

데이터 비정규화시 정보의 중복이 도입될 수 있습니다. 이러한 데이터 중복은 쿼리에서 처리해야 할 추가적인 저장 공간과 바이트를 추가합니다. 그러나 중복을 없애면서 비정규화된 테이블 디자인을 가질 수 있는 방법이 있습니다. 중첩 반복 열을 사용합니다.

중첩된 열은 struct 타입을 사용하여 특정 속성을 하나의 객체로 결합합니다. 중첩 반복 열은 테이블의 단일 행에 대해 저장된 struct 배열입니다. 예를 들어, 사용자의 각 로그인마다 한 줄씩 저장하는 테이블이 있다면 해당 사용자의 ID 및 등록 국가와 함께 각 사용자의 로그인마다 ID 및 국가에 대한 중복이 발생합니다.

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

한 사용자당 하나의 행을 저장하고 ARRAY STRUCT 유형의 열에 그 사용자의 모든 로그인을 저장할 수 있습니다. STRUCT는 날짜 및 장치와 같은 로그인에 연결된 모든 속성을 보유합니다. 다음 그림은 이 예시를 시각화한 것입니다:

![그림](/assets/img/2024-05-23-ADefinitiveGuidetoUsingBigQueryEfficiently_9.png)

예시:

```js
CREATE TABLE silver.logins (
    user_id INT64,
    country STRING(2),
    logins ARRAY<STRUCT<
        login_date DATE,
        login_device STRING
    >>,
    day DATE
)
PARTITION BY day
CLUSTER BY country, user_id
OPTIONS (
    require_partition_filter=true
);
```

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

위의 예제는 partition column에 필터링이 없는 쿼리를 방지하는 require_partition_filter의 활용을 보여줍니다.

이 데이터 모델링 기술은 저장 및 처리된 바이트를 크게 줄일 수 있습니다. 그러나 이것이 모든 비정규화나 데이터 모델링 경우에 대한 만병통치약은 아닙니다. 주요 단점은 다음과 같습니다: 구조체의 속성에 클러스터 또는 partition column을 설정할 수 없습니다.

즉, 위의 예제에서 login_device로 필터링하는 경우 전체 테이블 스캔이 필요하며 이를 클러스터링으로 최적화할 수있는 옵션이 없습니다. 특히 테이블이 Excel 또는 PowerBI와 같은 타사 소프트웨어의 데이터 원본으로 사용될 경우 이것은 문제가 될 수 있습니다. 이러한 경우에는 중복성을 제거하는 이점이 클러스터링을 통한 최적화 부족을 보상하는지 신중히 평가해야 합니다.

## 색인화

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

하나 이상의 열에 대한 검색 인덱스를 정의함으로써 BigQuery는 이를 사용하여 SEARCH 함수를 사용한 쿼리를 가속화할 수 있습니다.

검색 인덱스는 다음과 같이 CREATE SEARCH INDEX 문을 사용하여 생성할 수 있습니다:

```js
CREATE SEARCH INDEX example_index ON silver.some_table(ALL COLUMNS);
```

ALL COLUMNS를 사용하면 인덱스가 모든 STRING 및 JSON 열에 대해 자동으로 생성됩니다. 또한 더 선택적이며 열 이름 목록을 추가할 수도 있습니다. SEARCH 함수로는 인덱스를 활용하여 모든 열 또는 특정 열 내에서 검색할 수 있습니다:

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

```js
SELECT * FROM silver.some_table WHERE SEARCH(some_table, 'needle');
```

이 글을 쓰는 시점에 새로운 기능인 해당 기능은 미리 보기 상태에 있습니다. 추가로 =, IN, LIKE, STARTS_WITH와 같은 연산자에 대해 인덱스를 활용하는 것도 가능합니다. 이는 엔드 사용자가 직접 PowerBI나 Excel과 같은 제3자 도구를 통해 사용하는 데이터 구조에 매우 유용할 수 있으며, 일부 필터 작업의 속도를 높이고 비용을 절감하는 데 도움이 될 수 있습니다.

이에 대한 자세한 내용은 공식 검색 인덱스 문서에서 확인할 수 있습니다.

## 물리적 바이트 저장 요금

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

빅쿼리는 저장에 대해 두 가지 요금 체계를 제공합니다: 표준 및 물리 바이트 저장 요금 청구. 올바른 모델을 선택하는 것은 데이터 액세스 패턴과 압축 능력에 따라 다릅니다.

표준 모델은 간단합니다. 데이터 당 기가바이트당 일정 가격을 지불하며, 90일 동안 수정되지 않은 데이터에 대해 약간의 할인이 적용됩니다. 이 모델은 간단하게 사용할 수 있으며 다른 저장 구분을 관리할 필요가 없습니다. 그러나 데이터가 높은 압축률로 압축되었거나 자주 액세스하지 않는 경우에는 더 비싸게 나올 수 있습니다.

물리 바이트 저장 요금 청구는 다른 방식으로 접근합니다. 저장하는 논리적 데이터의 양에 따라 비용을 지불하는 대신, 디스크에 차지하는 실제 공간에 따라 비용을 지불합니다. 액세스 빈도나 압축 정도에 상관없이, 이 모델은 높은 압축률로 저장된 데이터나 자주 액세스하지 않는 데이터에 대해 훨씬 저렴할 수 있습니다. 그러나 이 모델은 자주 액세스하는 데이터와 장기 보관용 데이터 두 가지 별도의 저장 클래스를 관리해야 하므로 복잡성이 더해집니다.

그렇다면 어떤 모델을 선택해야 할까요? 여기에 간단한 안내가 있습니다:

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

표에 있는 내용을 Markdown 형식으로 변경해주세요.

표:

| 항목           | 설명                                                                                                       |
| -------------- | ---------------------------------------------------------------------------------------------------------- |
| 표준 모델 선택 | - 데이터가 고도로 압축되지 않았을 때. <br> - 간단하고 쉽게 관리할 수 있는 방식을 선호할 때.                |
| PBSB 선택      | - 데이터가 고도로 압축되었을 때. <br> - 비용을 최적화하기 위해 다양한 저장 등급을 관리하는 데 편안한 경우. |

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

데이터셋의 고급 옵션에서 요금 청구 모델을 변경할 수 있어요. 또한 테이블 세부정보를 확인하여 논리적 vs 물리적 바이트를 비교할 수 있어서 모델 선택이 더 쉬워집니다.

![image](/assets/img/2024-05-23-ADefinitiveGuidetoUsingBigQueryEfficiently_10.png)

## 기본 키 및 외래 키를 사용한 조인 최적화

2023년 7월 이후로 BigQuery는 미적용된 기본 키 및 외래 키 제약 조건을 도입했어요. BigQuery가 고전적인 RDBMS가 아니라는 것을 염두에 두세요. 이 기능을 사용하여 데이터 모델을 정의하면 그런 느낌을 받을 수 있지만요.

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

만약 키가 강제로 적용되지 않고 우리가 아는 관계형 데이터베이스가 아니라면 이것의 의미가 무엇일까요? 답은 다음과 같습니다: 쿼리 옵티마이저가 Inner Join Elimination, Outer Join Elimination, Join Reordering과 같은 개념을 사용하여 쿼리를 더 잘 최적화할 수 있습니다.

제약 조건을 정의하는 것은 다른 SQL 방언과 유사하지만, 이를 NOT ENFORCED로 지정해야 한다는 것에 주의하세요:

```js
CREATE TABLE gold.inventory (
 date INT64 REFERENCES dim_date(id) NOT ENFORCED,
 item INT64 REFERENCES item(id) NOT ENFORCED,
 warehouse INT64 REFERENCES warehouse(id) NOT ENFORCED,
 quantity INT64,
 PRIMARY KEY(date, item, warehouse) NOT ENFORCED
);
```

# ⚙️ 데이터 작업

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

## 데이터 / 표 복사

하나의 위치에서 다른 위치로 데이터를 복사하는 것은 데이터 엔지니어로서 일상적인 업무입니다. 예를 들어, BigQuery 데이터 세트인 bronze에서 다른 데이터 세트인 silver로 데이터를 복사하는 작업이라고 가정해 보겠습니다. 이를 위한 간단한 SQL 쿼리는 다음과 같습니다:

```js
CREATE OR REPLACE TABLE project_x.silver.login_count AS
SELECT
    user_id,
    platform,
    login_count,
    day
FROM project_x.bronze.login_count;
```

위 쿼리는 변환을 허용하지만, 많은 경우 단순히 데이터를 한 곳에서 다른 곳으로 복사하고 싶을 뿐입니다. 위 쿼리로 청구되는 바이트는 복사 대상에서 읽어야 하는 데이터 양에 해당합니다. 그러나 다음 쿼리로 이를 무료로 수행할 수도 있습니다:

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

```js
토론_가장 login_count
사본 project_x.bronze.login_count;
```

또는 bq CLI 도구를 사용하여 동일한 결과를 얻을 수 있습니다:

```js
bq cp project_x:bronze.login_count project_x:silver.login_count
```

이렇게 하면 데이터를 복사하는 데 비용이 발생하지 않습니다.

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

## 데이터 로드

데이터 수집에는 Google Cloud Storage가 문제 해결에 실용적인 방법입니다. CSV 파일이든, 하둡 생태계의 ORC / Parquet 파일이든 또는 다른 소스에서 가져온 것이든 상관없이 데이터를 쉽게 업로드하고 저렴한 비용으로 저장할 수 있습니다.

또한 GCS에 저장된 데이터 위에 BigQuery 테이블을 만들 수도 있습니다. 이러한 외부 테이블은 여전히 BigQuery의 컴퓨팅 인프라를 활용하지만 일부 기능과 성능을 제공하지는 않습니다.

ORC 스토리지 형식을 사용하는 분할된 하이브 테이블에서 데이터를 업로드한다고 가정해 봅시다. 데이터를 업로드하는 것은 distcp를 사용하거나 단순히 먼저 HDFS에서 데이터를 가져와서 사용 가능한 CLI 도구 중 하나를 사용하여 GCS로 업로드하는 것으로 이루어질 수 있습니다.

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

가정하에 우리가 월이라는 하나의 파티션을 포함한 파티션 구조를 갖고 있다고 가정하면, 파일은 다음과 같이 보일 것입니다:

```js
/some_orc_table/month=2024-01/000000_0.orc
/some_orc_table/month=2024-01/000000_1.orc
/some_orc_table/month=2024-02/000000_0.orc
```

이 데이터를 GCS에 업로드했을 때, 외부 테이블 정의는 다음과 같이 생성될 수 있습니다:

```js
CREATE EXTERNAL TABLE IF NOT EXISTS project_x.bronze.some_orc_table
WITH PARTITION COLUMNS
OPTIONS(
  format="ORC",
  hive_partition_uri_prefix="gs://project_x/ingest/some_orc_table",
  uris=["gs://project_x/ingest/some_orc_table/*"]
);
```

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

ORC 파일에서 스키마를 파생하고 파티션 열도 감지할 수 있습니다. GCS에서 BigQuery 스토리지로 데이터를 이동하는 가장 단순한 접근 방법은 이제 BigQuery에 테이블을 만든 다음 실용적인 INSERT INTO ... SELECT FROM 접근 방법을 따르는 것입니다.

그러나 이전 예제와 마찬가지로 청구된 바이트는 gs://project_x/ingest/some_orc_table에 저장된 데이터 양을 반영할 것입니다. 데이터를 로드하는 데 0 비용으로 동일한 결과를 얻는 다른 방법이 있습니다. 이것은 LOAD DATA SQL 문을 사용하는 것입니다.

```js
LOAD DATA OVERWRITE project_x.silver.some_orc_table (
  user_id INT64,
  column_1 STRING,
  column_2 STRING,
  some_value INT64
)
CLUSTER BY column_1, column_2
FROM FILES (
  format="ORC",
  hive_partition_uri_prefix="gs://project_x/ingest/some_orc_table",
  uris=["gs://project_x/ingest/some_orc_table/*"]
)
WITH PARTITION COLUMNS (
  month STRING
);
```

이 문을 사용하면 직접 데이터가 포함된 BigQuery 테이블을 얻게 되며, 먼저 외부 테이블을 만들 필요가 없습니다! 또한 이 쿼리는 0 비용으로 제공됩니다. OVERWRITE는 선택 사항입니다. 데이터가 추가되는 대신 테이블을 덮어쓸 수도 있습니다.

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

위에서 볼 수 있듯이 파티션 열도 지정할 수 있습니다. 변환을 적용할 수 없더라도 한 가지 주요한 장점이 있습니다. 이미 클러스터 열을 정의할 수 있습니다. 이렇게 하면 추가 비용 없이 후속 처리를 위한 대상 테이블의 효율적인 버전을 만들 수 있습니다!

## 파티션 삭제

특정 ETL 또는 ELT 시나리오에서 흔한 워크플로우는 테이블이 날짜별로 파티셔닝되어 있고, 스테이징 / 적재 테이블에서 새 데이터가 기존 파티션을 대체하는 경우가 있습니다.

![image](/assets/img/2024-05-23-ADefinitiveGuidetoUsingBigQueryEfficiently_11.png)

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

BigQuery는 MERGE 문을 제공하지만 순진한 접근 방식은 먼저 대상 테이블에서 영향을 받는 파티션을 삭제한 다음 데이터를 삽입하는 것입니다.

이러한 시나리오에서 파티션을 삭제하는 것은 다음과 같이 수행할 수 있습니다:

```js
DELETE FROM silver.target WHERE day IN (
  SELECT DISTINCT day
  FROM bronze.ingest
);
```

두 경우 모두 day가 파티션 열이라 할지라도, 이 작업은 여러 비용과 관련이 있습니다. 그러나 다시 한 번 0 비용으로 제공되는 대안 솔루션이 있습니다:

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

```js
DROP TABLE silver.target$20240101
```

DROP TABLE을 사용하면 실제로는 $`partition_id` 접미사를 추가하여 하나의 파티션만 삭제할 수도 있어요.

물론 위 예제는 단 하나의 파티션을 삭제하는 것 뿐이에요. 그러나 BigQuery의 프로시저 언어를 사용하면 쉽게 루프에서 문을 실행할 수 있어요.

```js
FOR x IN (SELECT DISTINCT day FROM bronze.ingest)
DO
  SELECT x; -- DROP TABLE으로 바꿔주세요
END FOR;
```

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

또는 Airflow 및/또는 dbt를 사용하여 먼저 파티션을 선택한 다음 특정 템플릿화된 쿼리를 반복해서 실행할 수 있습니다.

그러나 파티션된 테이블의 고유한 파티션을 가져오는 것은 위의 예제처럼 진행할 수 있지만, 한 열만 읽는 경우에도 일부 비용이 발생할 수 있습니다. 그러나 여기에도 다시 한 번 이를 거의 무료로 얻을 수 있는 방법이 있습니다. 이에 대해 다음 챕터에서 알아보겠습니다.

## 테이블의 고유한 파티션 가져오기

위의 예제에서 우리는 파티션이 있는 BigQuery 테이블의 고유한 파티션을 가져오기 위해 다음 방법을 사용했습니다:

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

```js
SELECT DISTINCT day
FROM bronze.ingest
```

이 예시 사용 사례에서 내가 사용한 쿼리가 얼마나 비용이 들었는지:

```js
청구된 바이트: 149.14 GB (= 위치에 따라 $1.18)
```

BigQuery는 테이블, 열 및 파티션에 대한 많은 유용한 메타데이터를 유지합니다. 이 정보에는 INFORMATION_SCHEMA를 통해 액세스할 수 있으며, 이 메타데이터를 사용하면 동일한 결과를 간단히 얻을 수 있습니다.

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

```js
SELECT PARSE_DATE('%Y%m%d', partition_id) AS day
FROM bronze.INFORMATION_SCHEMA.PARTITIONS
WHERE table_name = 'ingest'
```

그리고 위에서 언급한 것과 같은 사용 사례를 비교하면, 쿼리 비용은 다음과 같습니다:

```js
청구된 바이트: 10MB (위치에 따라 $0.00008)
```

149GB와 10MB를 비교하면 엄청난 차이가 납니다. 이 방법을 사용하면 거의 0 비용으로 거대한 테이블에 대한 고유한 파티션을 얻을 수 있습니다.

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

## 계산된 측정치 유지하지 마세요

처음으로 BigQuery를 사용하기 시작할 때는 대부분 온디맨드 컴퓨트 가격 모델을 사용할 것입니다. 온디맨드 가격 적용 시, 단일 프로젝트 내 모든 쿼리에서 공유되는 최대 2000개의 동시 슬롯에 액세스할 수 있습니다. 그러나 정적 가격 책정을 사용하더라도 최소 100개의 슬롯을 사용할 수 있습니다.

많은 일일 ETL / ELT 워크로드 중에는 이러한 슬롯이 실제로 성능의 제한 요소가 아닙니다. "BigQuery - Administration - Monitoring"으로 이동하여 위치를 선택한 후 차트를 "슬롯 사용량"으로 변경하여 이를 직접 확인할 수 있습니다. 많은 경우, 실제 사용 중인 슬롯이 얼마나 적은지 놀랄 만할 것입니다.

![이미지](/assets/img/2024-05-23-ADefinitiveGuidetoUsingBigQueryEfficiently_12.png)

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

그것이 비용 절감과 어떤 관련이 있을까요? 가정해봅시다. 고전적인 펙트 테이블이나 어떤 테이블이든 특정 KPI를 제공하는 테이블이 있다고 합시다. 이 테이블은 그런 다음 Looker, Excel, PowerBI 또는 다른 도구에서 분석/보고용으로 사용됩니다.

이러한 도구는 종종 보고서나 대시 보드에 필요한 데이터를 제공하기 위해 쿼리를 자동으로 생성합니다. 이러한 생성된 쿼리는 BigQuery의 최상의 실행 관행을 적용하는 데 적합하지 않을 수 있습니다. 다시 말해, 필요한 것보다 더 많은 데이터를 스캔하게 되어 청구된 바이트가 증가할 수 있습니다.

이 문제를 피하기 위해 사실 테이블 위에 뷰 레이어를 도입할 수 있습니다. 실제 테이블 대신 뷰로부터 도구에 데이터를 제공하는 것은 매우 중요한 최상의 실행 관행입니다. 이는 스키마 변경 시 더 많은 유연성을 제공하지만 동시에 데이터를 지속하지 않고 뷰 내에서 계산된 측정값을 도입할 수 있는 가능성도 제공합니다.

물론 이러한 측정값이 사용될 때 CPU 사용량이 증가할 수 있지만, 반대로 기본 테이블의 총 크기를 줄일 수 있는 큰 장점이 있습니다.

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

위 원리를 설명하기 위해 다음 사실 테이블을 기초로 삼아보겠습니다:

```js
CREATE TABLE IF NOT EXISTS gold.some_fact_table (
  user_id INT64,
  payment_count INT64,
  value_1 INT64,
  value_2 INT64,
  day DATE
)
PARTITION BY day
CLUSTER BY user_id
OPTIONS (
  partition_expiration_days = 365
);
```

기본 아이디어는 해당 데이터에 액세스하는 이해관계자를 위한 뷰를 도입하고 계산된 측정값으로 확장하는 것입니다:

```js
CREATE OR REPLACE VIEW gold.some_fact_view AS
SELECT
  user_id,
  payment_count,
  value_1,
  value_2,
  payment_count > 0 AS is_paying_user,
  value_1 + value_2 AS total_value,
  day
FROM gold.some_fact_table;
```

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

이 예시에서는 두 개의 INT64 값을 영속화하지 않을 수 있었습니다. 이 중 하나는 8개의 논리적인 바이트를 사용합니다. 만약 우리의 팩트 테이블이 1,000,000,000 행을 가지고 있다면, 이것은 우리가 다음을 저장한다는 것을 의미합니다:

```js
1000000000 행 * 8 B * 2 열 / 1024 / 1024 / 1024 = 15 GB
```

이는 많은 양의 데이터가 아니지만, 이는 특정 상황에서 BigQuery가 15GB 덜 스캔해야 할 수도 있음을 의미합니다. 실제로 스캔해야 하는 데이터를 훨씬 줄일 수 있는 계산된 측정 값들이 있을 수 있습니다.

# 📚 요약

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

용량을 보존하는 것이 아니라, 데이터를 스마트하게 관리하고 최적화하는 방법을 배워봐! 이 뜨거운 접근 방식을 통해 빅쿼리를 비용 부담이 아닌 데이터 탐색을 위한 강력한 엔진으로 변화시킬 수 있어. 이를 통해 돈이 아닌 데이터를 소모할 수 있을 거야!

## 데이터 모델링 최고의 실천법 채택하기

- 저장 및 처리 비용을 최소화하기 위해 가능한 가장 작은 데이터 유형을 활용해.
- 쿼리 성능을 최적화하고 저장 공간을 줄이기 위해 적절한 경우에 비정규화 활용하기.
- 쿼리에 필요한 데이터만 효율적으로 스캔할 수 있도록 파티셔닝 및 클러스터링 구현하기.
- 중첩 반복되는 열을 탐색하여 중복성을 제거하고 데이터 무결성을 유지하되, 클러스터링에 관한 제한 사항을 염두에 두기.

## 비용 효율성을 위한 데이터 작업 마스터하기

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

- 데이터 전송 시 요금을 부과하지 않고 테이블 간 데이터를 복사하려면 CREATE TABLE ... COPY 또는 bq cp 명령을 사용하세요.
- LOAD DATA 문을 사용하여 클라우드 스토리지에서 BigQuery 테이블로 데이터를 직접 로드하는 것도 무료입니다.
- 특정 파티션을 효율적으로 제거하려면 DROP TABLE과 분할 접미사를 활용하세요.
- 전통적인 쿼리에 비해 비용을 크게 절감할 수 있는 DISTINCT 파티션 값을 포함한 테이블 메타데이터를 검색하려면 INFORMATION_SCHEMA를 활용하세요.

## 효율성을 위해 설계하고 불필요한 데이터 지속성을 피하세요

- 계산된 측정값으로 데이터를 제공하기 위한 뷰 레이어를 구현하여 중복 데이터를 저장하지 않습니다.
- BigQuery 슬롯 사용량을 모니터링하여 슬롯 제한이 문제가 될 경우에 대비하여 쿼리 구조를 최적화하는 데 집중할 수 있습니다.

댓글에서 여러분의 경험을 자유롭게 공유해주세요!
