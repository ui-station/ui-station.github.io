---
title: "효율적인 BigQuery 데이터 모델링 저장 및 계산 비교"
description: ""
coverImage: "/assets/img/2024-05-23-EfficientBigQueryDataModelingAStorageandComputeComparison_0.png"
date: 2024-05-23 15:56
ogImage:
  url: /assets/img/2024-05-23-EfficientBigQueryDataModelingAStorageandComputeComparison_0.png
tag: Tech
originalTitle: "Efficient BigQuery Data Modeling: A Storage and Compute Comparison"
link: "https://medium.com/google-cloud/efficient-bigquery-data-modeling-a-storage-and-compute-comparison-ca7f3744e467"
---

## BigQuery 저장 및 컴퓨팅 동적을 비교한 정규화, 비정규화 및 중첩 모델: 실행 가능한 최적화, 권장사항 및 모범 사례를 포함한 심층 분석.

![Image](/assets/img/2024-05-23-EfficientBigQueryDataModelingAStorageandComputeComparison_0.png)

# 소개

좋은 데이터 모델링은 최상의 스키마 설계를 선택하고 그대로 고수하는 것이 아니라 여러 스키마 디자인을 결합하는 것입니다. 대신, 좋은 데이터 모델은 정규화, 중첩 또는 비정규화 테이블 및 뷰의 실용적인 혼합물입니다.

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

이 글은 여러분의 맥락에 최선의 도움이 되도록 처음부터 이 결론까지 안내하고자 합니다.

본 글은 초심자나 전문가들이 자신의 맥락에서 데이터 모델을 설계할 때 좋은 질문을 하고 더 자유롭게 할 수 있도록 도와주기 위해 작성되었습니다.

저희는 중첩, 정규화 및 비정규화 스키마 설계를 재정의하는 기본 지식부터 시작하겠습니다. 그런 다음 BigQuery 아키텍처를 탐구하며 저장 및 컴퓨팅 자원 측면에서 성능 테스트 및 비교를 진행할 것입니다.

그러나 비용 최적화만으로는 충분하지 않습니다. 이 글의 끝에는 다른 요인들, 권장 사항 및 최상의 실천 방법을 여러분과 공유하겠습니다.

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

# 요약

∙ 소개
∙ 중첩 스키마
∙ 정규화된 스키마
∙ 비정규화된 스키마
∙ 모두 합치기
∙ 저장소 비교
∙ BigQuery 아키텍처
∙ 컴퓨트 비교
∙ 크기 대 성능
∙ BigQuery 스키마 디자인 선택
∙ 권장 사항
∙ 결론

# 중첩 스키마

가장 복잡한 스키마 디자인부터 시작해보겠습니다.

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

제가 발견한 것은 약간의 역설인데, 중첩 스키마가 일반적으로 가장 무서운 것으로 여겨집니다. 그러나 때로는 데이터를 표현하는 가장 자연스러운 방법이기도 합니다. 그런데 왜 그런지를 이해하기 위해 과거로 돌아가 봅시다.

중첩 및 반복 데이터 구조는 주로 NoSQL 데이터베이스와 대조적으로 SQL 데이터베이스(또는 관계형 데이터베이스)와 연관되어 있습니다. 역사적으로 이는 데이터를 저장하는 자연스러운 방법이 아니었습니다. 데이터는 전통적으로 정규화되었습니다. 우리는 나중에 정규화된 스키마에 대해 다시 이야기할 것이지만, 지금은 SQL이 처음에는 행과 열을 처리하기 위해 만들어졌다는 점만 이해해 주세요. 중첩 구조가 아니었습니다.

데이터 웨어하우스와 대규모 데이터 및 분석 데이터베이스(안녕하세요, BigQuery)의 발전과 함께, 다양한 형태와 구조가 등장했습니다. 네, 쿼리하는 것이 더 복잡할 것이라 동의합니다. 아마 그것이 왜 그렇게 무서운지의 이유일지도 모릅니다...

얘기는 여기까지 하고, 저희 예시로 들어가 봅시다. 소매업계의 세계에 오신 것을 환영합니다!

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

우리의 기사와 첫 번째 예제를 시작해봅시다. 쇼핑 바구니(또는 영수증)의 경우를 고려해보겠습니다. 영수증이란 무엇인가요? 상점에서 고객이 구매한 총 가격과 특정 날짜를 나타내는 객체입니다. 하지만 총 가격 이상으로, 여러 줄을 포함하고 있습니다. 이러한 줄들은 판매된 제품, 구매 가격, 수량 등을 나타냅니다.

간단하죠? 여러 개의 하위 객체를 포함하는 하나의 객체가 있습니다. 바구니는 여러 제품을 포함합니다.

BigQuery나 소매업계에 입문하신 분이라면, 저는 간단한 바구니 테이블을 만들었습니다. 이 테이블에는 RECORD 타입과 REPEATED 모드인 상세 설명 열이 포함되어 있습니다.

중첩 스키마를 사용하여 현실 세계에서 일어나는 일을 반영했습니다. 한 행은 하나의 바구니 헤더를 나타냅니다. 각 바구니 헤더는 세부사항을 포함하고 있습니다. 상세 항목의 각 "하위 행"은 판매된 제품을 나타냅니다.

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

저는 https://www.mockaroo.com/에서 가짜 데이터를 사용하여 이 표를 채웠고, 수백 번의 데이터를 복제하여 약 10GB 크기의 표를 만들었습니다.

여기 있습니다. 데이터를 발견하는 자연스럽고 우아한 방법입니다. 위 이미지에 두 개의 행이 있어 두 개의 바구니 주문이 있습니다. 각 주문은 detail 필드 내에 있고 구조체 배열로 구성된 임의의 수의 품목이 포함되어 있습니다.

# 정규화된 스키마

정규화된 스키마 또는 스타 스키마(보다 복잡한 경우 스노우플레이크 스키마)는 각 테이블이 특정 엔티티나 관계에 집중된 구조로, 외래 키를 사용하여 테이블 간 관계를 설정합니다.

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

관계형 데이터베이스에 대해 생각하는 표준 방법입니다. 이는 제 1 정규형(1NF)을 따릅니다. 모든 열은 단일 값 속성(배열 없음)이어야하며 복합 값(구조체 또는 중첩 없음)을 포함해서는 안됩니다.

1NF 규칙을 따르면, 바구니 테이블을 바구니*헤더와 바구니*세부 테이블로 분해합니다. 위의 쿼리를 고려하면서.

거의 대부분의 데이터베이스에서는 이러한 종류의 스키마 설계가 있습니다. 이는 관계형 데이터베이스를 관리하는 역사적(그리고 가장 쉬운) 방법입니다.

"가장 쉬운"이라고 말한 이유는 기본 SQL 쿼리로 데이터를 얻을 수 있기 때문입니다. 한 테이블은 한 개체/기능/객체입니다. 개체 간의 관계는 외부 키로 설정됩니다. 우리의 경우, 각 바구니*헤더 행이 한 개 이상의 바구니*세부 행에 연결될 것임을 알고 있습니다.

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

위 이미지에서 보는 것처럼, 이러한 종류의 구조의 장점은 각 테이블이 특정 entity에 대한 답변을 제공하는 방식에 있습니다. 바구니 헤더는 자체 테이블에, 바구니 상세 정보도 독립적인 테이블에 있습니다. 데이터가 분할되어 있지만 바구니 헤더의 기본 키로 쉽게 결합할 수 있습니다 (이미지에서의 화살표).

또한, 데이터가 복제되거나 중복되지 않음을 유의하십시오. 정보를 업데이트해야 할 경우, 한 테이블과 한 행(즉, 한 값)에서만 수정하면 됩니다.

# 비정규화 스키마

비정규화는 사전 계산된 조인의 결과로 볼 수 있는 스키마 설계입니다.

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

이름에서 볼 때 정규화의 반대로 이해할 수 있지만, 이 스키마를 그렇게 보면 안 돼요. 사실 이 구조는 정규화된 스키마를 잘 보완해주죠. (여러 스키마 디자인을 선택해 데이터를 표현하는 가장 좋은 방법이 어떤 것인지 예측하고 있죠?)

이러한 유형의 스키마가 어떻게 가치를 더하는지 그리고 비정규화가 다른 데이터 모델을 어떻게 보완하는지 이해하려면 다음 섹션을 기다려야 할 거예요.

지금은 장바구니의 헤더와 세부사항을 결합한 결과를 살펴봐야 해요.

새로 만든 이 테이블에서 보듯이, 비정규화는 쿼리 복잡성을 줄여줍니다. 결합할 테이블이 여러 개 없으며 중첩/반복되는 필드도 없어요. 간단히 말해, 조인은 이미 완료되었고 영수증 세부내역만 남아 있어요.

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

데이터 쿼리가 이 테이블 스키마로 쉬워진다는 장점이 있지만, 중복 데이터가 포함되어 있습니다. 여기서 헤더 데이터는 각 라인마다 반복됩니다. 이로 인해 중복성이 높아지고 일관성 문제가 발생할 수 있습니다. 게다가 헤더에 업데이트를 적용하는 것이 더 어려워보입니다. 한 가지 더 언급할 점은 데이터가 x번 복제되기 때문에 헤더에 대한 업데이트를 적용하는 것이 더 복잡해진다는 것입니다. 이 테이블의 또 다른 어려움은 "조인(join)"의 수가 증가함에 따라 테이블의 세분성(granularity)을 인식하는 것입니다.

# 전부가 다 함께

잘했어요. 이제 세 가지 가장 흔한 스키마 디자인을 보았고(네, 그 외에도 많이 있습니다), 우리 테이블은 테스트할 준비가 되었습니다.

성능 테스트로 넘어가기 전에 특정 사항에 주목해 주고 싶습니다. 언제든 다른 스키마로 전환할 수 있습니다. 이겦거보시죠: 이 기사에서는 먼저 중첩 형식을 만들고, 이 초기 테이블을 기반으로 다른 형식을 구성했습니다.

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

요약해보면 다음과 같습니다:

- 중첩형: 현실 세계를 잘 표현한 우아한 형식이지만 쿼리하기에는 복잡할 수 있습니다.
- 정규화된 형식: 역사적인 형식으로 모든 이가 익숙해하지만, 계산을 위해 최적화되진 않을 수도 있습니다.
- 비정규화된 형식: 데이터베이스에서 계산에 매우 최적화되어 있지만 중복을 초래할 수 있는 흥미로운 데이터 형식입니다.

# 저장소 비교

다음 표는 BigQuery에서 세 가지 다른 스키마 디자인 기술을 사용하여 동일한 10GB 데이터를 저장하는 데 필요한 저장소를 비교합니다.

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

중첩 형식이 데이터를 계층 구조로 저장하여 자연스러운 압축을 가능하게 하기 때문입니다. 정규화 형식은 그렇게 멀지 않습니다. 중복은 없지만 기본 키에 대한 추가 열이 작성되어야 합니다. 반면에 비정규화 형식의 평면 구조는 데이터 반복으로 인해 가장 공간 효율적이지 않음을 입증합니다.

그러나 이 비교에 조금의 색깔을 더해볼까요? 중첩 형식을 기준으로 삼아보겠습니다 (베이스 1로).

중첩 데이터 모델이 가장 최적화되어 있으며 데이터 중복을 최소화하고 최대 2배 또는 3배의 저장 공간을 절약할 수 있습니다. (귀하의 FinOps가 귀하를 사랑할 것입니다!)

물리적 또는 논리적 (기본) 저장소 가격 아래에 있더라도 중첩 스키마 설계는 더 저렴한 송장으로 이어집니다.

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

# BigQuery 아키텍처

여기에 BigQuery 아키텍처에 대한 자세한 섹션을 포함할 계획이 있었어요. 하지만 책을 쓰는 게 아니니까, 그건 다른 시간에 남겨두기로 해요. 한편, 다른 기사들로 링크를 제공할게요.

BigQuery가 어떻게 구성되어 있는지 이해하는 것은 여전히 중요해요. 직관력을 통해 다음 섹션에서 컴퓨팅에 미치는 영향을 다룰 거에요.

![이미지](/assets/img/2024-05-23-EfficientBigQueryDataModelingAStorageandComputeComparison_1.png)

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

왜 스토리지의 차이점만 다른 부분에서 다루었느냐고 물으면, 이는 이해하기 쉬운 섹션을 만들기 위해서이기도 하지만 BigQuery 아키텍처의 논리를 따르기 위해서입니다.

분석 데이터베이스의 강점은 네 가지 간단한 개념으로 요약할 수 있습니다:

- 스토리지와 컴퓨팅의 분리: BigQuery에서 데이터 스토리지와 계산 작업이 분리됩니다. 이 분리를 통해 스토리지와 컴퓨트를 최적화하여 성능을 극대화할 수 있습니다.
- 열 지향 스토리지: BigQuery는 데이터를 열 지향 형식으로 저장합니다. 이는 각 열의 값이 함께 저장된다는 것을 의미합니다. 이 스토리지 방법은 분석 작업에 최적화되어 있어 주어진 쿼리에 필요한 열만 읽을 수 있게 함으로써 (전체 행이 아닌) 대기 시간을 줄이고 성능을 향상시킵니다.
- 분산 컴퓨팅: BigQuery의 데이터는 병렬 처리를 위해 여러 컴퓨트 노드(슬롯)에 분산되어 있습니다. 이를 통해 쿼리 처리를 빠르게 할 수 있으므로 쿼리 복잡성이나 입력 데이터 크기에 관계없이 성능을 향상시킬 수 있습니다.
- 네트워크: BigQuery는 스토리지와 컴퓨트 간 데이터 전송을 위한 고대역폭을 제공합니다. 이는 대량의 데이터를 처리할 때에도 높은 성능을 보장합니다.

이러한 기본 원칙을 이해한다면 BigQuery가 어떻게 고성능이고 확장 가능한 데이터 분석을 실현하는지 알 수 있을 것입니다. 그리고 확실히 최적화하는 방법도 이해하게 될 것입니다.

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

# 연산 비교

다시 비교로 돌아가봅시다. 중첩 형식의 저장소가 가장 최적화되어 있어 중복을 최소화한다는 것을 보았습니다. 그렇다면 연산 자원은 어떨까요?

총 처리된 바이트의 양에는 놀라운 점이 없습니다. 이전 섹션에서 본 것과 동일합니다. 하지만 이것을 간과해서는 안 됩니다: 데이터가 적을수록 연산 비용도 낮아지는 것이 기본입니다!

자연 및 알고리즘적 데이터 압축은 저장 속에서 쿼리 가격에 중요한 초기 영향을 미칩니다. 이 아이디어를 염두에 두고 연산에 특화된 차이점을 살펴보겠습니다.

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

첫 번째 중첩 및 비정규화 형식을 비교해보면 쿼리 응답 시간이 유사함을 알 수 있습니다. BigQuery는 컴퓨팅을 병렬화하는 방식으로 관리하기 때문에 사용자에게 컴퓨팅 리소스가 우수했음을 알리지 않습니다. 그러나 실제로는 그렇습니다. 컴퓨팅 리소스는 약 2배 더 높아요! (슬롯 수인 "완료된 단위" 및 "총 슬롯 시간"을 참조하세요).

짜증날 수 있는 부분, 조인으로 넘어가서 이제 정규화된 스키마에서 조인이 필요로 하는 컴퓨팅 리소스를 비교해봅시다.

다음 섹션에서 더 자세히 살펴보겠습니다. 현재는 단순히 이해하려고 해봐요: 조인은 왼쪽 테이블의 각 행을 오른쪽 테이블의 행과 비교합니다. 이는 BigQuery가 이 계산을 돕기 위해 더 많은 컴퓨팅 리소스와 중간 저장 공간을 할당하도록 요구합니다 (셔플이 발생하는 곳이죠).

BigQuery에서 오른쪽 테이블이 상당한 크기를 갖게 되면 셔플링과 복잡한 병렬 계산이 트리거되어 두 테이블을 조인합니다. 일반적으로 이 크기 제한은 약 10MB 정도로 이해되지만, Google이 정확한 숫자를 발표하지 않으며 쿼리 계획에 따라 달라질 수 있습니다.

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

약 10GB의 근사 데이터 양을 가지고 비교를 진행할 때, 우리는 이 셔플링을 명확하게 관찰하며 추가적인 계산 리소스가 필요함을 알 수 있습니다. 이 "작은" 양에 대해 우리는 약 10배 정도의 수요를 관찰하고 있습니다! (그러므로, 슬롯 기반 요금 모델 하에서 운영 중이라면 주의가 필요합니다).

이것이 대시보드 상류에 정규화되지 않은 테이블(사전 계산된 쿼리의 결과)을 자주 볼 수 있는 이유입니다. 따라서 계산 리소스를 적게 요구하며 정규화된 스키마 디자인보다 응답 시간이 훨씬 더 좋습니다.

# 테이블 크기 대 성능

조인이 상당한 계산 리소스를 필요로 하는 이유에 대해 약속했었죠. 이 질문에 답하고 데이터 양이 스키마 디자인 간의 성능에 미치는 영향에 대해 탐구해볼 것입니다.

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

이번에는 1GB에서 1TB 범위의 초기 데이터를 대상으로 정확히 동일한 SELECT COUNT(\*) FROM... 쿼리를 사용했어요.

단순함을 위해 중첩 스키마 디자인과 정규화된 스키마 디자인만을 비교했고, 중첩된 디노멀라이즈된 디자인은 입력 데이터 양에 따라 선형적으로 발전한다고 가정합니다.

조인할 데이터 양이 클수록 더 많은 리소스가 필요하며, 단순히 선형 관계가 아니라 지수 함수 관계임을 짐작하셨을 겁니다.

시각적으로 나타내기 위해 다음 그래프를 고려해보세요. 읽기 어려운 부분이 있어 마인드에 그래프 상 각 점에 대한 안내 문구를 제공했어요.

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

알았어요. 테이블 안의 데이터 양이 처리된 데이터 양과 선형 관계에 있습니다 (네, 둘은 같은 것이므로 이해가 됩니다).

우리는 조인되는 테이블의 크기가 필요한 자원에 엄청난 영향을 미치고, 필요한 슬롯의 수에 대해 명확히 지수적이라고 관찰했습니다. 이제, 몇십 테라바이트에 해당하는 데이터 양을 상상해보세요. 그럼 FinOps의 문제에 직면하게 될거에요!

저는 특히 흥미로운 메트릭인 "바이트당 슬롯 시간(영어: Slot Time per Byte)"을 추가했어요. 이는 총 입력 바이트 당 필요한 시간을 측정하는 것인데요. 다시 말해, 이는 흥미로운 최적화 메트릭이며, 저에게 가장 신뢰할 만한 요소입니다. 이 메트릭을 사용하는 경향이 있고, 이 메트릭이 100GB를 넘어서는 지점에서 특히 두드러지게 변화한다는 점을 알아봤어요. 다시 말해, 저에게 의하면, 100GB 이상의 경우, 정규화된 형식은 성능과 비용에 상당한 부정적인 영향을 미칩니다.

요약하자면, 온디맨드 가격 모델(테라바이트 당 요금)을 사용하는 경우, 최적화되지 않은 스키마 디자인에 대해 더 많은 비용을 지불할 필요는 없지만 응답 시간이 크게 증가할 것입니다.

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

용량 요금 모델 (슬롯-시간 당)을 사용 중이시라면, 청구서에서 정규화된 스키마 모델의 영향을 볼 수 있을 거예요. FinOps는 당신을 좋아하지 않을 것 같아요. 하지만 중첩된 스키마 모델로 전환해서 비용을 아주 많이 낮출 수 있고, FinOps를 당신의 친구로 만들어보세요!

# BigQuery 스키마 디자인 선택하기

다양한 데이터 스키마 모델링 옵션의 장단점을 요약해보겠습니다.

정규화된 스키마의 장점은 중복을 최소화하고 쿼리를 용이하게 만들며 데이터 업데이트(UPDATE)를 쉽게 허용하는 것입니다. 그러나 이 데이터를 쿼리하기 위해 테이블을 조인하는 것은 비용이 많이 들 수 있어요.

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

Denormalized 스키마는 데이터 쿼리 중 응답 및 연산 시간을 최소화하는 장점을 제공합니다. 이는 정규화된 모델에서 쿼리의 결과로 생각할 수 있는 표현입니다. 그러나 더 많은 저장 공간을 사용하고 데이터에 중복을 도입합니다.

중첩 스키마는 저장 공간, 응답 시간 및 계산 자원을 극명하게 최소화하는 장점을 제공합니다. 그러나 더 복잡한 형식을 쿼리해야 하는 경우 복잡성이 추가됩니다. 데이터 업데이트를 더 어렵게 만들며 대량의 데이터에 대한 삽입 기록 시나리오에서 선호해야 합니다.

데이터를 모델링하는 방식을 결정할 때 고려해야 할 질문을 매우 간단히 나열해 보겠습니다:

- 데이터 양이 적다면, 정규화된 형식으로 유지합시다.
- 데이터가 불변이 아니고 정기적인 업데이트가 필요한 경우, 정규화된 형식으로 시작합시다.
- 중첩 형식은 반드시 이해하기 쉬워야 하며 실제 세계를 대변해야 합니다. 예를 들어, 영수증에는 라인이 있고, 영수증과 라인 간에는 자연스러운 계층 구조가 있습니다. 존재하지 않는 계층적 표현을 강제로 만들려는 경향이 없도록 주의하세요. 예를 들어, 영수증과 상품 재고는 함께 그룹화할 수 없는 두 가지 독립적인 것입니다.
- 중첩 형식은 복잡합니다. 내가 이것에 익숙할 수도 있고, 당신도 그럴 수 있지만, 모든 사용자의 경우에 해당되는 것은 아닙니다. 이를 고려하세요; 최적화가 항상 재정적인 것은 아닙니다. 대부분의 사람들이 더 쉽게 쿼리할 수 있는 평면화된, 비정규화된 형식으로 최적화하세요. 사용 편의성을 위해 최적화하세요.

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

# 추천 사항

일반적으로, 가능한 한 간단하게 유지하는 것을 추천합니다. 팀이 이미 중첩 형식을 스스로 마스터하거나 쉽게 습득할 수 있다면 작업을 단순화하고 최적화할 수 있습니다. 그러나 팀이나 사용자들에게 이 형식을 사용하도록 강요하는 것은 역산적일 수 있습니다. 비용이 많이 드는 쿼리를 사용하는 것보다 나쁜 쿼리를 사용하는 것이 더 좋습니다.

저의 경우, 저는 중첩 된 테이블을 좋아합니다. 그러나 이는 최종 사용자에게는 "숨겨져" 있습니다. 중첩 테이블 위에 데이터를 정규화하거나 비정규화하는 뷰를 만들 수 있습니다. 이 평평화된 형식은 모두에게 이해되며, 그 아래에 중첩 테이블이 있는 것은 완벽한 최적화입니다!

간단히 말해서, 숙련된 사용자에게 중첩 형식을 제공하고 데이터를 다른 더 "전통적인" 형식으로 펼치는 뷰를 추가로 제공해보세요.

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

이 기사의 초점은 중첩 형식의 장점을 홍보하는 데 있었지만, 여전히 가장 간단한 방법이 가장 좋습니다. 테이블을 파티션하고 클러스터를 추가해보세요; 이렇게 하면 조인을 크게 최적화할 수 있고 거의 100GB에 도달하지 않습니다. 중첩 형식은 참으로 흥미로워지는데, 조인에서 100GB를 넘어설 때입니다. 전체적인 상황을 고려해 볼까요. 100GB는 $0.625입니다. 몇 센트를 절약하는 데 하루를 낭비하나요? 팀에 제공하는 가치에 집중하고, 그런 다음 최적화하세요.

정규화는 중첩 형식이 적용하기 어려울 때 최후의 수단으로 간주될 수 있습니다.

# 결론

이 기사가 여러분에게 가치 있는 통찰력을 제공하고, 미래의 결정을 이끌어내며, 데이터 모델 설계 사이에서 선택할 때 비판적 사고를 가르쳐 주었기를 바랍니다. 각 디자인의 이점을 최대한 활용하고, 데이터 웨어하우스에서 현명하게 결합하여 사용량과 비용을 최적화하세요.

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

이 동안 긴 글을 읽어 주셔서 감사합니다! 만약 도움이 되었다면 이 기사를 공유하고, 박수를 보내거나 댓글을 남기거나 구독하거나 LinkedIn에서 팔로우해 주세요.
