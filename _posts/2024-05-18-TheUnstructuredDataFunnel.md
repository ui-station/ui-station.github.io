---
title: "비정형 데이터 퍼널"
description: ""
coverImage: "/assets/img/2024-05-18-TheUnstructuredDataFunnel_0.png"
date: 2024-05-18 16:32
ogImage:
  url: /assets/img/2024-05-18-TheUnstructuredDataFunnel_0.png
tag: Tech
originalTitle: "The Unstructured Data Funnel"
link: "https://medium.com/towards-data-science/the-unstructured-data-funnel-245f72925176"
---

만약 당신이 Medium 회원이 아니라면, 여기서 무료로 읽을 수 있습니다.

# 소개

비구조화 데이터는 다양한 형태를 가지고 있습니다. 일반적으로 텍스트가 많지만, 날짜, 숫자, 사전과 같은 데이터도 포함될 수 있습니다. 데이터 엔지니어들은 주로 깊게 중첩된 json 형식의 비구조화 데이터를 다루게 됩니다. 그러나 "비구조화" 데이터라는 용어는 실제로 탭형식이 아닌 모든 것을 말합니다; 사실, 세계 데이터의 80%가 비구조화 데이터입니다.

비구조화 데이터는 우리 데이터 전문가들에게는 무해해 보일 수 있지만, 전체적으로는 큰 파장을 일으키고 있습니다. 실제로 GPT 모델은 모두 비구조화 데이터로 훈련됩니다. 이는 최근 Snowflake의 수익 발표에 관한 Tomasz Tunguz의 기사에서 올바르게 관찰되었습니다.

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

<img src="/assets/img/2024-05-18-TheUnstructuredDataFunnel_0.png" />

금융 및 거시경제적 맥락에서 비구조적 데이터를 보는 것이 이상할 수 있습니다. 제 첫 직장은 투자은행이었기 때문에 이런 것을 읽을 때는 향수가 솟습니다. "비구조적 데이터는 성장 엔진"은 제게는 말이 되는 것 같아요 — 정말 큰 시장 선풍 같아요!

하지만 파워포인트 상자를 정렬하던 시절이 오래되었죠. 개념적으로 비구조적 데이터는 이제 처리되기를 기다리는 깊게 중첩된 JSON입니다. 하지만 수익 발표를 보면 비구조적 데이터는 이제 단순히 JSON이 아니라 텍스트, 문서, 동영상 등이라는 것이 분명합니다.

중요한 유도자 중 일부에 비구조적 데이터가 자리하고 있으며, 이를 처리하는 곳은 데이터 세계의 두 큰 기업인 Databricks와 Snowflake에게 매우 중요합니다. 왜 그런지 알아봅시다.

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

# 왜 비구조화 데이터가 중요한가요?

GPT 모델은 데이터를 기반으로 동작합니다. 특히, 비구조화 데이터를 기반으로 동작합니다. 이는 텍스트 문서, HTML 파일 및 코드 조각과 같은 데이터를 의미합니다. 기업들이 LLM을 제품에 구현하려는 노력이 더해질수록, 이러한 데이터를 처리하는 가치가 증가하며 그에 따라 공급이 증가합니다. 그 결과, Snowflake 및 Databricks와 같은 공급업체에게 해당 데이터의 가치가 상승합니다.

그러나 특정 유형의 비구조화 데이터를 처리하는 데는 두 번째 요소가 있습니다. 중첩된 JSON을 예로 들 수 있습니다. 중첩된 JSON은 처리될 때 언네스팅되거나 정리됩니다. 이는 다음과 같이 변환될 수 있음을 의미합니다:

```js
{
  "outer_key1": {
    "inner_key1": {
      "nested_key1": {
        "deeply_nested_key1": "value1",
        "deeply_nested_key2": "value2"
      },
      "nested_key2": {
        "deeply_nested_key3": "value3",
        "deeply_nested_key4": "value4"
      }
    },
    "inner_key2": {
      "nested_key3": {
        "deeply_nested_key5": "value5",
        "deeply_nested_key6": "value6"
      },
      "nested_key4": {
        "deeply_nested_key7": "value7",
        "deeply_nested_key8": "value8"
      }
    }
  },
  "outer_key2": {
    "inner_key3": {
      "nested_key5": {
        "deeply_nested_key9": "value9",
        "deeply_nested_key10": "value10"
      },
      "nested_key6": {
        "deeply_nested_key11": "value11",
        "deeply_nested_key12": "value12"
      }
    },
    "inner_key4": {
      "nested_key7": {
        "deeply_nested_key13": "value13",
        "deeply_nested_key14": "value14"
      },
      "nested_key8": {
        "deeply_nested_key15": "value15",
        "deeply_nested_key16": "value16"
      }
    }
  }
}
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

다음과 같이 변경하겠습니다:

```js
{
  "deeply_nested_key1": "value1",
  "deeply_nested_key2": "value2"
}
```

두 번째 JSON을 처리하는 것은 데이터의 초기 정리에 비해 더 적은 계산이 필요합니다. 처음에 처리되는 훨씬 큰 객체의 경우 "청소"가 데이터 파이프라인 내 어디에서 일어나는지가 사용되는 계산에 상당한 영향을 미칩니다.

모든 비구조화된 데이터가 이러한 패턴을 따릅니다. Snowflake의 문서 AI는 pdf와 같은 문서를 가져와 데이터를 표 형태로 추출합니다. 이는 처리의 주된 부분이 한 번 일어나고 결과 데이터가 훨씬 깨끗하고 처리하기 쉽다는 것을 의미합니다.

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

# 비구조화 데이터 퍼널

클라우드 벤더들인 Snowflake와 Databricks와 같은 곳에게 데이터 처리가 어디에서 발생하는지에 대한 주목이 중요합니다. 왜냐하면 그들은 클라우드 컴퓨팅에 기반하여 요금을 부과하기 때문입니다. 즉, 필요한 계산 파워가 더 많아질수록 그들에게 더 많은 비용을 지불해야 합니다. 우리는 이전 섹션에서 LLMs로 인해 비구조화 데이터가 점점 중요해지고 있음을 확인하였으나, 또한 비구조화 데이터를 처리하는 데 필요한 계산이 계속해서 줄어든다는 것을 봤습니다. 데이터 파이프라인을 통해 데이터가 계속해서 처리될수록 데이터가 깨끗하고 집계된다는 것은 직관적입니다.

우리는 데이터 파이프라인 인프라가 어떻게 보이는지 상상하면서 이를 시각화할 수 있습니다. 대부분의 경우, 우리는 일반적으로 다음과 같은 아키텍처의 하위 집합을 가지고 있습니다:

## 데이터 이동

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

퍼널의 첫 번째 섹션은 데이터 팀이 비구조화된 데이터와 처음으로 접촉하는 곳입니다. 이는 일괄 처리든 스트리밍 아키텍처든 어느 쪽이든 데이터 이동 계층입니다. 이 계층에는 저장 요소가 없지만, Fivetran, Portable 또는 Striim과 같은 벤더들은 몇 가지 변환 작업("ETL" 또는 "ET L"으로 "ELT" 또는 "EL T"가 아닌)을 수행하기 위해 투자하고 있습니다. 이는 계산을 사용하고 데이터의 크기를 줄여 다음 계층으로 이동하는 데이터에 영향을 줍니다.

이러한 도구들은 전체 데이터 이력이 없기 때문에 백필이나 천천히 변하는 차원과 같은 복잡한 작업을 수행할 수 없기 때문에 처리할 수 있는 내용에 제한이 있습니다. 그러나 이러한 도구들은 스트림을 조인하거나 비구조화된 데이터를 해체하는 것과 같은 간단한 변환 작업에 적합합니다. 이러한 대부분의 벤더들은 어차피 텍스트 파일과 같은 비구조화된 데이터를 다루지 않습니다. 이러한 서비스는 Azure EventHub, BigQuery PubSub와 같은 클라우드 네이티브 서비스를 사용할 수 있습니다. 따라서 이 로고들은 퍼널의 이 부분에 영향을 줍니다.

이 계층에서 얻을 수 있는 계산 리소스: 중간

## 데이터 레이크 / 객체 저장소

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

두 번째 층은 Google Object Storage, AWS S3 또는 Azure ADLS Gen-2와 같은 객체 저장소에 있는 데이터를 말합니다. 이들은 모든 파일 형식의 데이터를 저장하는 세 대 클라우드 제공 업체의 저장소 솔루션입니다. 이 퍼널의 이 층은 모든 데이터가 중앙 집중화되고, 모든 형태의 컴퓨팅이 쉽게 사용 가능한 첫 번째 층입니다. 이 층에서는 클라우드 제공 업체로부터 직접 임대하거나 Databricks for Spark와 같은 업체를 통해 컴퓨팅을 이용할 수 있습니다. 이 층은 복잡한 프로세스를 처리하기에 좋은 후보이며, 특히 차원과 복잡성을 줄이는 데 탁월합니다. 이는 관련 컴퓨팅이 매우 높음을 의미합니다.

내 의견으로는 이 층이 비구조화된 데이터를 처리하는 데 가장 적합한 곳입니다. 여기에는 무엇이든 저장할 수 있습니다. 호환 가능한 클라우드 인프라가 엄청나게 많기 때문에(모든 것이 S3와 상호 작용할 수 있음), 처리된 데이터를 위한 준비된 저장소 계층이 있습니다. 데이터 웨어하우스보다 유연성이 높기 때문에 이 유형의 처리를 여기서 하는 것이 매우 합리적합니다. 데이터 레이크는 모든 형식의 데이터를 보관하도록 만들어진 것이기에, 바로 비구조화된 데이터가 그것이 아닌가요?

컴퓨팅 가능성: 매우 높음

## 데이터 웨어하우스 / SQL 층

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

해당 단계는 특정 형식 또는 특정 형식 세트로 저장된 데이터를 가리키며, 일반적으로 SQL과 유사한 문장을 사용하여 쉽게 쿼리하고 조작할 수 있도록 합니다. Snowflake은 자체 파일 형식을 갖고 있어 이를 용이하게 만들어주며, 데이터를 "Snowflake로 가져오기" 위해 "진입 요금"을 내야 하는 이유입니다. Databricks 측에서는 .delta를 갖고 있는데, 이는 사실 .parquet 위에 추상화된 것입니다. 또한 오픈 소스입니다. .iceberg와 같은 다른 포맷도 있으며, 이는 Snowflake의 외부 테이블을 지원하여 (이는 Snowflake를 터널의 더 높은 단계로 끌어올립니다).

이 영역은 구조화된 데이터 또는 표 형식의 데이터를 처리하기에 적합합니다. 그러나 JSON을 평평하게 만드는 작업과 같은 연산도 여기서 수행될 수 있습니다 (예: BigQuery나 Snowflake에서 볼 수 있음). 데이터 엔지니어들 사이에서 이 영역이 SQL 기반 컴퓨팅을 최대한 활용하는 최선의 방법이 아닐 수 있다는 인식이 있습니다. 주요 반대 의견은 이러한 작업을 점진적으로 수행하기가 어렵고 느리며 따라서 비용이 많이 든다는 것입니다. 그러나 실제 비용은 이미 데이터를 이동하는 과정에서 발생합니다. 이미 데이터를 터널을 통해 이동하기 위해 비용을 지불했습니다. 데이터를 변환하거나 구조화를 높은 단계에서 수행하고, 그러면 터널을 통해 이동하는 데이터 양이 최소화될 것입니다.

흥미로운 것은 이 부분이 Snowflake 수익 전화에서 주목을 받은 영역이었다는 것입니다. 대부분의 엔지니어들이 비용을 절약하기 위해 가능한 한 터널의 상위에서 데이터 조작을 선호하는 점을 고려하면, "데이터를 단순히 Snowflake로 가져오기"라는 교리가 기업 데이터 책임자들과 CIO들에게 호응을 일으킬 수 있다는 가능성이 있습니다. DocumentAI나 SnowPark Container Services와 같은 기능들이 SQL 데이터 웨어하우스와 "데이터 레이크하우스" 사이의 경계가 흐려지고 있음을 의미할 수도 있습니다. 동일한 컴퓨팅이 터널의 다른 부분에 있는 데이터에 걸쳐 사용될 수 있습니다.

주의할 점은 데이터 웨어하우스에서 비구조화된 데이터를 처리할 때 발생하는 컴퓨팅 처리의 이중 가격 결제입니다. 만약 Databricks와 같은 서비스를 사용한다면, 클라우드 제공 업체의 컴퓨팅 파워를 사용하여 객체 저장소에 저장된 텍스트 파일을 처리하고 Databricks를 중간 업체로 사용하여 컴퓨팅 처리에 대해 단일 가격 결제를 하게 됩니다. 데이터 웨어하우스를 사용하면 저장소 및 컴퓨팅에 중복적으로 요금을 지불해야 합니다. 또한 그들이 문서를 분석하기 위해 컴퓨팅을 어떻게 사용하는지 정확히 알 수 없으므로 그 부분에서도 약간 더 많은 비용을 지불할 수 있습니다. 데이터 웨어하우스가 외부 테이블을 지원하는 경우에는 전자를 피할 수 있습니다.

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

이겹을 마무리하려면— SQL 워크로드를 효율적으로 실행하려면 데이터는 다른 특정 형식이어야 합니다. 이는 데이터를 .delta와 같은 쿼리 가능하고 변환 가능한 형식으로 변환하는 데이터 웨어하우스 시장을 만들었습니다. 비구조화 데이터는 어떤 면에서는 이와 정반대이며 이미 존재하는 위치, 즉 데이터 레이크에 속하는 것으로 보입니다. 이에 대하여 웨어하우스 레이어의 컴퓨팅 리소스를 사용하여 LLMs의 맥락에서 비구조화 데이터 처리를 수행하는 것은 실질적으로 의미가 없습니다. 실제로 일부 인기 있는 Snowflake 사례들은 이미 Snowpark 컨테이너 서비스와 외부 테이블과 같이 컴퓨팅/저장 모델이 더 많이 레이크하우스와 유사한 것들을 보여줍니다.

이용 가능한 컴퓨팅 자원: 높음

## 데이터 활성화 — 분석의 "마지막 단계"

파이널 먼션은 퍼널의 끝에 대한 것입니다— 데이터 활성화. 이것은 분석의 "마지막 단계"이며, 일반적으로 프로세스가 시작될 수 있는지 확인하기 위해 작은 점검을 수행한 후, 정리된 데이터를 운영 시스템으로 이동시키는 작업을 일컫습니다. 이는 정리된 및 집계된 데이터와 상호작용하는 애플리케이션을 가리킵니다.

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

이것들은 전형적인 역 ETL 사용 사례, 대시 보드 또는 자동화된 슬랙/이메일 알림일 수 있습니다.

이 시점에서 모든 데이터(구조화된 및 비구조화된)가 충분히 정리되었을 것으로 예상됩니다. 데이터 엔지니어링, 분석 엔지니어링 및 제품 팀이 모두 이를 검토하고 테스트하고 정리한 것으로 생각됩니다.

이것은 컴퓨팅 측면에서 실제로 할 일이 남아 있지 않음을 의미합니다.

따라서 비구조화 데이터 퍼널의 요소 중에서는 가장 흥미로운 부분이 아니며, 컴퓨팅에 대한 데이터 팀에 지불할 수 있는 가장 적은 기회를 제공합니다. 모든 데이터가 깨끗하고 평탄하며 완벽하기 때문에 더 이상의 작업이 필요하지 않습니다. 게다가 데이터의 크기도 훨씬 작습니다.

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

## 대회 참가 신청: 낮음

# 결론

본 문서는 비정형 데이터 처리에 대한 데이터 전문가들이 새로운 관점을 제시합니다. 비정형 데이터는 텍스트 문서, HTML 파일, 깊게 중첩된 JSON 등이 모두 포함되어 있어 데이터에서 가치를 추출하기 위해 첫 번째 중요한 계산 단계가 필요합니다. 이 처리 단계가 어디서 일어나느냐는 대규모 데이터 클라우드 공급업체에게 중요한데, 이는 사용량과 수익과 밀접하게 관련되어 있습니다. 현재는 특히 LLMs 및 GPT 모델의 배포로 인해 비교적 쓸모없거나 대다수의 데이터 전문가들에게 흥미롭지 않았던 이 데이터에 대한 수요가 만들어지고 있습니다.

퍼널 그림을 통해 데이터 팀이 이러한 번거로운 처리 단계를 미룰수록 총 이동 데이터 양이 증가하여 비용이 증가한다는 것을 알 수 있습니다. 게다가 다양한 파일 형식과 필요한 작업 유형이 다양하기 때문에, 객체 저장소 수준에서 데이터 처리에 집중하는 것이 가장 합리적인 것으로 보입니다. 이는 Snowflake가 비정형 데이터 처리를 용이하게 하기 위해 "퍼널을 올라가는" 것으로 잘 나타납니다.

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

그리고 클라우드 공급 업체들이 여전히 상승하는 경향을 보일 것인지에 대한 의문을 낳습니다. Gmail에서 이메일을 구문 분석하는 것은 간단할 수 있습니다. 만약 gmail에서 object storage로 데이터를 이동하는 데 특화된 도구가 있다면, 그 도구가 "T"도 수행해야 하는 이유는 무엇일까요? 이 경우에는 유용한 정보를 (아마도 AI를 사용하여) 추출하고 레코드의 평면 테이블을 S3에 직접 덤프해야 하지 않을까요? Google 및 Microsoft에 속하는 클라우드 데이터베이스에 가장 유용한 비구조화 데이터가 있는 사실은 Databricks나 Snowflake가 아니라는 점과 데이터 제품 스위트를 갖추고 있다는 점에 대해 어떻게 생각하십니까?

솔직히 말해서, 이 질문에 대한 정확한 답을 모릅니다. 그러나 가능한 한 많은 데이터를 가장 초기에 처리하는 것에는 엄청난 경제적 인센티브가 있다는 점은 확실히 알고 있습니다. 상승할 수 있는 한 얼마나 먼 거리인지는 논쟁이 될 수 있습니다. Snowflake의 성공을 보면 회사들을 나이트를 시작하기 전에 Snowflake로 데이터를 이동하도록 설득하는 능력을 나타냅니다. 수십만 달러에 이르는 비즈니스 인텔리전스 도구들의 시대는 끝났습니다. S3를 위한 전투가 시작되었습니다! 🔥
