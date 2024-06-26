---
title: "데이터 인프라 및 대형 언어 모델"
description: ""
coverImage: "/assets/img/2024-05-20-DataInfraandLargeLanguageModel_0.png"
date: 2024-05-20 16:53
ogImage:
  url: /assets/img/2024-05-20-DataInfraandLargeLanguageModel_0.png
tag: Tech
originalTitle: "Data Infra and Large Language Model"
link: "https://medium.com/@yuguang.mou/data-infra-and-large-language-model-80bbcbc4ad12"
---

데이터 인프라 산업, CRM 및 사이버 보안 산업은 언제나 전 세계에서 소프트웨어 부문 중 상위 세 분야 중 하나였습니다. (가트너 2023: 데이터 인프라 15%, CRM 14%, 사이버 보안 10%). 데이터 인프라 분야에서는 Oracle과 같은 거물 기업이 3000억 달러 이상의 가치를 지니고 있습니다. 또한 Snowflake, Databricks, MongoDB와 같은 신생 기술 스택, 포괄적인 제품 포트폴리오를 갖춘 세 가지 주요 클라우드 제공업체와 함께 수백 개의 데이터베이스가 DB-Engines.com에 의해 모니터링되고 있습니다.

지난 5년 동안 데이터 인프라는 클라우드 네이티브 기술을 수용하는 데 중점을 두었다면, 앞으로 5년은 대형 언어 모델의 변혁적인 힘을 받아들이는 데 중점을 둘 것입니다.

# Snowflake CEO 변경

2024년 2월 28일, Snowflake는 회계 연도의 제4 분기 재무 결과를 발표했습니다. 실망스러운 전체 연간 지침을 제공한 후, 회사는 미국 데이터 인프라 산업을 충격받게 할 또 다른 소식을 발표했습니다.

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

미국 소프트웨어 역사상 가장 전설적인 CEO 중 한 명인 프랭크 슬룻만이 Snowflake의 CEO로서 사임을 발표했습니다. 새 CEO는 Neeva의 인도계 창업자 인 스리다르 라마스와미입니다. 스리다르는 지난해 Neeva를 Snowflake에 판매한 후 Snowflake에 합류했고, 모든 새 AI 관련 비즈니스를 감독하는 Snowflake의 AI SVP로 임명되었습니다. 단 년 만에 매각되었던 회사의 창업자에서 상위 회사의 새 CEO로 전환했습니다.

Snowflake CFO 마이클 스카르펠리는 수익 보고서 이후 일주일 뒤 투자자 통화에서 "저희는 프랭크의 퇴임 소식을 수요일에 (수익 보고서를 발표한 날)"만 알게 된 사실을 언급했습니다. 그리고 "그 동안 프랭크가 이사회와 스리다르와 더 많은 시간을 보낸 것으로 보아, 그는 프랭크의 후임자가 될 수 있을 것이라 여겼습니다." 스카르펠리는 프랭크와 오래된 친구이며, 둘은 함께 ServiceNow에서 황금 콤비를 이루었으며, 함께 Snowflake로 합류하기 전에 두 사람은 모두 몬태나 주 보즈만에 거주하고 있어, 저희처럼 놀라실지 모르겠습니다.

Snowflake의 천사 투자자이자 창업 CEO 마이크 스페이서도 프랭크의 사임에 대해 다음과 같이 언급했습니다:

- 스페이서와 두 창업자가 Snowflake를 공동 창업할 때, 제품이 제공될 때 CEO로서 사임할 것을 합의했습니다.
- CEO로서 사임한 후, 스페이서가 다음 CEO로 마이크로소프트의 밥 머글리아를 데려왔고, 제품을 시장에 내놓고 비즈니스 모델을 확립하는 단계에 중점을 둔 "명확한 업그레이드"라고 평가했습니다.
- 이후 이사회는 다음 큰 도전이 공개 상장하고 확장하는 것인 것을 깨닫고, 회사 전체에 높은 강도와 긴급함을 전파할 수 있는 프랭크 슬룻만을 찾아내어 성장을 가속화하고 최종적으로 회사를 공개시켰습니다.
- 스페이서와 슬룻만 모두 스리다르가 Snowflake의 다음 세대 리더로 가장 적절한 지도자라고 믿습니다.

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

CEO 교체는 칼을 바꾸는 것과 같아요. LLM이 주도하는 데이터 인프라의 다가오는 시대에는, Frank가 Snowflake를 위한 가장 적합한 CEO가 아닐 수도 있어요. 이것이 Mike Speiser를 설득했는데, 그는 각 CEO 교체에서 좋은 결과를 보았기 때문에 Sridhar가 다음 단계에 더 적합할 수도 있다고 믿게 되었어요.

나중에 Sridhar는 Snowflake뿐만 아니라 세 대형 클라우드 제공업체가 그를 자신들의 AI 리더로 초대했다고 언급했지만, Sridhar는 최종적으로 Snowflake를 선택했어요. Sridhar는 데이터베이스, LLM 및 경영의 복합 기술 세트를 갖춘 희귀한 재능이에요. 데이터베이스 관련 분야의 박사 학위를 보유하고 있으며, Google에서 10,000명 이상의 팀을 이끈 'Google 광고의 왕'으로서 Meta보다 추천 알고리즘에서 뒤처지지 않고 추월하도록 도왔어요. 그는 나중에 AI 검색 회사 Neeva를 설립했어요.

이것이 데이터 인프라 산업이 LLM 시대의 결정적 전투 이전으로 급속히 밀려들고 있다는 것을 생각하게 해줘요. 이번 전투의 도래에 절박함을 느끼는 기업들만이 Frank와 같은 이전 시대의 훌륭한 지도자를 교체할 결정을 내릴 것이에요.

이 변화는 단순히 Snowflake의 선택일 뿐만 아니라 많은 소프트웨어 회사들이 내릴 결정일 수 있어요. AI 배경을 갖춘 CEO는 어디에 AI에 투자해야 하는지 명확히 이해할 수 있고, 어떤 제품과 기술 능력을 보충해야 하는지, 이 사업을 운영하기 위한 적절한 인재를 찾을 수 있는 곳을 알 수 있어요.

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

선점자 이점, 시간은 아무도 기다려주지 않아요. 다음 섹션에서 이에 대해 더 깊이 파고들어보겠습니다.

# 데이터 인프라에서 수익을 올리는 방법: 교육 파이프라인에 진입함으로써

작년 Q1에 시작된 AI 붐으로 인해 데이터 인프라 기업이 혜택을 받기 시작한 이야기는 스노우플레이크(Snowflake)와 몽고디비(MongoDB) 같은 주요 기업들에게 명확한 AI 수익 기여로 이어지지 않았습니다.

몽고디비의 2023년 Q4 실적 보고서에서, 기업은 이번에 처음으로 전통적인 데이터 인프라 기업들이 이 분야에서 아직 큰 수익을 내지 못한 이유에 대해 설명했습니다:

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

- 데이터 인프라 기업은 대형 언어 모델 (LLM) 영역에서 모델 학습, 미세 조정 및 추론 세 가지 계층에 참여할 수 있습니다.
- MongoDB의 기존 기술 스택은 주로 후자 두 계층 (미세 조정 및 추론)과 관련이 있지만, 현재 고객 사용 사례를 기반으로 하면 대다수의 고객이 여전히 첫 번째 계층 (모델 학습)에 있습니다.
- 고객이 세 번째 계층 (추론)으로 이동할 때까지, 중요한 AI 수익이 MongoDB로 흘러들지 않을 것입니다.

이것은 데이터 인프라 분야의 현재 상업적 현실을 반영합니다. 학습 기술 스택에 관여한 새로운 세대의 데이터 인프라 기업만이 이 분야에서 수익을 얻었습니다. 일반적으로 ETL/특성 엔지니어링, 데이터 레이크, 벡터 데이터베이스, 학습 최적화 프레임워크, 그리고 전통적인 기계 학습에서 자주 사용되는 라이프사이클 관리 및 실험 추적 도구를 포함합니다. Databricks, Pinecone 및 Zilliz, Myscale과 같은 중국 기업을 비롯한 이러한 새로운 세대의 도구들이 이미 AI 학습에서 첫 번째 돈을 벌어들였습니다.

![image](/assets/img/2024-05-20-DataInfraandLargeLanguageModel_0.png)

이전 Relit 블로그 게시물에서 언급된대로, 그들의 대형 언어 모델은 Databricks의 기술 스택과 함께 세 대 주요 클라우드 제공업체의 인프라를 많이 활용하여 모델 학습 프로세스를 완료했습니다.

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

# Databricks — 검증된 솔루션으로 지난 10년을 보냈습니다

Databricks은 가장 주목받는 새로운 세대의 데이터 인프라 플레이어 중 하나입니다.

최근 공개된 비즈니스 데이터에 따르면:

- Databricks는 2023년에 16억 달러의 매출을 달성했으며, 연간 약 55%의 성장을 이룩했습니다.
- 16억 달러의 매출은 경쟁사인 Snowflake의 60% 미만이지만, Databricks의 매출 모델은 Snowflake와 다릅니다. Databricks는 SQL Serverless 제품뿐만 아니라, 클라우드 제공업체의 컴퓨팅 및 저장 서비스를 번들로 제공하여 소프트웨어 수익과 클라우드 제공업체의 마진을 모두 확보합니다. 또한, Databricks의 다른 제품 대부분은 소프트웨어 가치만을 판매합니다.
- 더 합리적인 비교는 총 마진입니다. 클라우드 제공업체의 매출을 제외한 경우, Databricks의 총 마진은 Snowflake의 약 65%에 해당하며, 빠른 성장을 고려했을 때, Databricks의 마진은 2023년 제 4 분기에 약 70%로 Snowflake의 마진에 가까워졌습니다 (이는 Databricks의 높은 마진 소프트웨어 비즈니스 비중을 반영합니다).
- 추세를 살펴보면, Databricks는 2023년에 매출 성장을 가속화했으며, 2024년에도 성장률이 60%까지 가속화될 것으로 예상됩니다. 이를 뒷받침하는 증거로, Databricks는 2023년 제 4 분기에 거의 100%의 연간 예약 성장을 기록했습니다.

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

Databricks의 현재 성공에 비해, 지난 10년간의 개발은 원활하지 않았습니다. 이는 검증 과정을 거치는 10년의 여정이었습니다.

Databricks는 오픈 소스 Spark에서 시작하여 후에 대표 제품인 Delta Lake으로 데이터 레이크를 확장했습니다:

- Spark는 Databricks의 초기 제품이자 현재 핵심 제공품으로, 기계 학습과 데이터 엔지니어링을 지원하는 플랫폼으로 처음에 위치했습니다.
- 딥러닝 붐이 일어나기 전까지 Spark는 거의 모든 기계 학습 작업을 처리할 수 있었지만, 딥러닝의 부상으로 인해 Spark는 더 이상 주류 기계 학습 플랫폼이 되지 않았습니다. TensorFlow 이후 PyTorch가 주류가 되었습니다.
- 독립적인 기계 학습 플랫폼 이상으로, Spark는 데이터 엔지니어링 분야를 지배하며 가장 주류인 ETL 도구가 되었고, Databricks에게 ETL/기능 엔지니어링을 통한 대용량 언어 모델 시대로 가는 열쇠를 제공했습니다.

다른 대표 제품 Delta Lake도 Databricks를 가장 큰 상업용 데이터 레이크 서비스 제공업체로 만들었습니다:

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

- 머신 러닝 데이터를 처리할 때는 대량의 구조화되지 않은 데이터가 필요하며, 데이터 레이크가 가장 비용 효율적인 저장 방법이 되었습니다.
- 그러나 스노우플레이크가 거대한 시장 기회와 더 쉽게 이해할 수 있는 데이터 웨어하우스 개념으로 급속히 성장함에 따라, 데이브릭스는 그림자를 드리운 채로 남아 있었습니다.
- "보다 맛있는" 데이터 웨어하우스 사업을 잡기 위해 데이브릭스는 레이크하우스 컨셉을 제안했습니다.
- 데이브릭스 역시 고객에게 더 많은 자율성을 부여하여 고객이 큰 부피로 인해 구름에서 큰 할인을 받을 수 있는 매우 큰 고객들에게 매우 친숙합니다.
- 데이브릭스 SQL은 스노우플레이크에 비해 높은 계산량의 복잡한 시나리오에서 데이터 웨어하우스 능력의 고유한 결함으로 인해 성능 대 가격 비율이 다소 떨어집니다.

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

Databricks는 개발 과정에서 여러 가지 어려움을 겪었지만 결국 승리를 거뒀습니다. 오랫동안 홍보되어온 Spark와 Lakehouse 제품은 대규모 언어 모델 시대에 대한 주요 무기가 되었습니다.

- 현재 단계에서 기술 스택의 완성도와 플랫폼 기능성(목표를 종단-to-종단으로 구현하는 능력)은 단일 기능의 우수한 성능보다 중요합니다.
- 대규모 언어 모델 시대에 비정형 데이터 처리의 폭발적인 성장에서 Delta Lake + Databricks Spark는 비정형 데이터 처리의 황금 콤보가 되어 ETL/Feature Engineering 워크로드의 상당 부분을 차지하게 되었습니다.
- 기계 학습 분야에서의 포괄적인 노하우를 통해 MosaicML을 인수한 후, Databricks는 세 주요 클라우드 및 NVIDIA 이후 또 다른 풀스택 대규모 언어 모델 훈련 플랫폼이 되어, 거의 마지막 퍼즐 조각을 완성하게 되었습니다.
- 그리고 2024년에 Snowflake이 오픈 포맷을 완전히 받아들이고 고객이 자체 저장로드를 사용할 수 있도록 하는 것으로 Lakehouse 경로를 완전히 수용함에 따라, Lakehouse가 빅데이터 시대의 주류가 되었습니다. 호수나 창고에서 들어오든, 궁극적인 해결책은 Lakehouse가 될 것입니다.

# Snowflake의 따라잡기 계획

언제나 비정형 데이터와 기계 학습에 중점을 둔 Databricks와 달리, Snowflake의 경로는 더 다양했으며, 기계 학습 분야에는 상대적으로 적은 투자를 한 다고 말할 수 있습니다.

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

Snowflake의 공동 창업자인 Benoit Dageville은 2023년 이전에 Unistore와 Snowpark에 중점을 둔 Snowflake의 기술 로드맵을 책임지고 있습니다. 먼저 Unistore에 대해 논의해 봅시다:

- Unistore는 HTAP와 유사한 제품으로, 하단에 KV 스토어 디자인이 있습니다. Benoit은 이 제품이 Snowflake이 대규모 데이터베이스 (OLTP) 영역으로의 시장 기회 확대에 도움이 될 것으로 기대했습니다. 그러나 KV 스토어 디자인 때문에 Oracle과 같은 주류 OLTP와 직접적으로 경쟁할 수는 없으며, 주로 OLAP가 주력이고 OLTP가 보조인 기업들이 채택할 수 있는 솔루션이 적합합니다.
- Unistore를 구현하는 기술적 어려움도 상당히 높으며, 데이터 웨어하우스에 약하지만 Snowflake의 고품질 데이터 처리와 안정성에 대한 요구 또한 엄청나게 높습니다. 한편, HTAP도 새로운 기술 솔루션이며, HTAP 선구자들은 이 분야에서 벽을 만나고 있어 HTAP 비즈니스 모델이 증명되었는지 여부를 확신할 수 없게 만듭니다.

Unistore와 비교하여 Snowpark의 논리는 더 직관적입니다:

- Snowpark는 더 간소화된 제품 논리를 갖고 있습니다. 고객이 데이터를 Snowflake에 적재할 때 ETL 처리를 수행해야 하며, 과거의 주류 처리 방법은 Open-Source Spark와 Databricks Spark였습니다. 이제 Snowflake의 네이티브 ETL 도구인 Snowpark를 사용하면 고객은 전송 비용을 절감할 수 있으며 기능적인 차이가 없어 고객이 Snowpark로 전환하여 비용 효율성을 높이는 것이 자연스러운 선택이어야 합니다.
- Open-Source Spark(주로 AWS 고객들 사이에서 EMR 제품으로 판매되는 제품)과 비교하여, Snowpark의 비용 효율성 이점은 매우 명확합니다. 그러나 최적화된 상업용 제품인 Databricks Spark와 비교하면, Snowpark는 이미 Snowflake 제품을 사용 중인 고객들을 위해 데이터 처리에 더 초점을 맞추고 있습니다.
- Snowpark는 데이터 엔지니어링 작업을 신속하게 따라잡을 수 있고 기술적 장벽도 높지 않습니다. 그러나 머신러닝 영역에서는 아직 많은 작업이 남아 있으며, 특히 Spark의 오픈소스 이점에 직면하여 Snowpark는 특정 전통적인 산업을 위한 머신러닝 기능을 제공하는데 초점을 맞추고 있습니다.

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

스노우파크가 2022년 말에 상용화된 이후, 수익 규모는 Databricks의 ETL 수익의 약 5~10%로 빠르게 성장하고 있어요. Databricks의 경쟁 제품인 Databricks SQL과 비교하면, 그 규모는 Databricks SQL의 약 1/3 수준으로, 출시 시기도 Databricks SQL보다 1년 뒤입니다.

스노우파크는 Snowflake가 대형 언어 모델 시대에 진입하기 위한 열쇠를 제공했고, Snowflake의 미래 대형 모델 지원 제품은 모두 스노우파크를 중심으로 구축될 것입니다:

- 스노우파크는 비정형 데이터를 처리할 수 있는 능력을 Snowflake에 제공하여 대형 언어 모델 시대에서 ETL 및 피처 엔지니어링 요구를 충족할 수 있게 했습니다.
- 스노우파크를 더 발전시킴으로써, Snowflake는 Iceberg 오픈 포맷을 지원하기 시작했고, 이는 Snowflake가 더 많은 비정형 데이터를 유치하고 완전한 Lakehouse 솔루션을 구축하는 기초가 되었습니다.
- 동시에 Snowflake는 Snowpark 컨테이너 서비스를 출시했고, 이는 Snowflake의 주요 업무로 자리 잡았습니다. GPU Workloads를 Snowflake로 가져오며 컨테이너 서비스에서 모델을 Fine-tune하고 배포할 수 있게 했습니다.

Sridhar가 Snowflake에 합류한 후, 그는 새 제품 Cortex에 집중하기도 했다요:

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

- Cortex는 대화 및 관련 분석을 위한 제품을 주로 하는 새 투자인 Mistral AI를 포함한 Snowflake를 위해 외부 대규모 언어 모델 파트너를 가져왔어요.
- Cortex에는 Document AI와 Databricks의 LakehouseIQ와 유사한 Snowflake Copilot도 포함되어 있어요. Text2SQL 및 지식베이스에 대한 솔루션을 제공합니다.
- Sridhar는 이전 회사 Neeva에서 사용한 RAG-Vector Search 솔루션을 Cortex로 통합하고, Snowflake에 벡터 스토리지 및 처리 능력을 곧 추가할 예정이에요. 미래에는 더 많은 컨테이너 서비스 고객을 지원하여 컨테이너 서비스에서 직접 모델을 배포하고 추론할 수 있도록 해줄 거예요.

Sridhar는 Snowflake가 부족한 점을 잘 알고 있고 투자해야 할 노력을 알고 있어요. 이는 Snowflake가 DeepSpeed의 창업자와 그 핵심 팀을 빼앗는 것에서도 확인할 수 있어요:

- Snowflake의 CFO는 후속 커뮤니케이션에서 DeepSpeed로부터 빼앗은 5명의 연간 비용이 2000만 달러였다고 언급했어요. "놀랍게도 높고, 너무 비싸고 뛰어납니다."
- 그러나 Sridhar는 Snowflake가 최고의 타겟인 MosaicML과 같이 훌륭한 대상을 찾을 수 있어야 한다는 것을 잘 알고 있어요. 그들을 인수할 수 없는 경우 직접 인사할 필요가 있어요. DeepSpeed 팀은 지금 가장 인기 있는 대규모 언어 모델 훈련/추론 프레임워크로 거의 최상의 선택이에요.
- Frank 시대에는 상상조차 할 수 없었지만, 새 CEO의 탑-다운 추진 아래에서만 실현할 수 있었던 높은 비용과 "올드 가드"의 회사 의도를 이해하는 데 어려움이 있었어요.

CEO 교체 후, Snowflake는 "All in AI" 전략을 취하고 모든 제품이 AI를 중심으로 한 것으로 나타났어요.

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

회사의 주요 사업이 데이터 웨어하우징인 회사에서 AI를 하는 것은 두 번째 스타트업을 하는 것과 같습니다. 하지만 Snowflake는 아직 멀은 길이 남아 있어요.

# MongoDB의 RAG 이야기

Databricks와 Snowflake와 달리 MongoDB는 분석 쪽이 아니며, 제품은 OLTP에서 비즈니스 데이터의 흐름과 저장을 지원하는 데 더 중점을 두고 있어요.

2023년 초에는 MongoDB가 데이터 인프라 공간에서 최고의 타깃이었는데, 당시 시장 논리는 다음과 같았습니다:

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

- 몽고DB는 문서 데이터베이스를 기반으로 개발되어서 데이터 구조에 대한 과도한 고려 없이 데이터를 먼저 수용하고, 그런 다음 매우 높은 사용 편의성으로 데이터를 처리할 수 있습니다.
- 대규모 언어 모델의 교육과 추론은 많은 비구조화 데이터를 사용하며, 몽고DB의 주요 제품은 반구조화 및 비구조화 데이터를 저장, 읽기, 쓰기 및 쿼리하기 위한 것입니다.
- 교육 측면에서 몽고DB는 비구조화 데이터의 저장 매체로 사용될 수 있으며, 이는 고객의 기술 스택에서 몽고DB의 중요성을 더욱 높일 수 있습니다.
- 몽고DB에는 자체 벡터 데이터베이스를 구축하고 모델 추론 측면에 진입할 기회가 있습니다.
- 더 많은 LLM 응용 프로그램은 더 많은 앱을 의미하며, 이는 LLM 워크플로우에서 반드시 MDB를 사용하지는 않지만, 여전히 챗봇 채팅 기록과 전통 OLTP 워크로드를 저장하기 위해 몽고DB가 필요합니다.

몽고DB는 2023년 제1분기에 Hugging Face와 Tekion과 같은 잘 알려진 기업을 포함한 200개의 새로운 AI 고객을 확보하면서 협력했습니다. 그러나 이후 분기에는 몽고DB가 AI 고객 정보를 더 이상 공개하지 않았습니다.

몽고DB의 주요 관심사는 주로 추론 측면에 집중되어 있으며, 이로 인해 최근 분기에 대형 모델 시나리오가 교육 단계에 여전히 머물러 추론 단계에 진입하지 않았다는 것을 언급해도 매출 기여도가 미미한 것입니다.

추론 측면에서 몽고DB의 기회를 조사하는 것:

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

- 이전 두 회사와 비교했을 때, 추론 측면에서 데이터 애플리케이션 및 API 레이어에 더 중점을 둔 MongoDB는 엔드 유저에게 서비스를 제공할 수 있으며, 이는 OLTP 포지셔닝과 밀접하게 관련되어 있습니다.
- MongoDB의 Atlas Vector Search 서비스는 2024년 초에 가장 먼저 GA 및 상용화된 벡터 검색 기능을 제공했습니다.
- 기존 고객들을 대상으로 전통 기술 스택이 더 신뢰성 있을 수 있으며, 특히 RAG 요구 사항이 아직 초기 단계에 있고 대규모로 확장되지 않은 경우, MongoDB의 벡터 검색 서비스가 이미 요구 사항을 충족시킬 수 있습니다.

하지만 다른 RAG 솔루션과 비교하면, MongoDB는 여전히 추론 개발 초기 단계에 있습니다:

- 대량 데이터 양과 높은 동시성을 갖는 시나리오에서, MongoDB는 아직 인공지능 기반 벡터 데이터베이스보다 뒤쳐지고 있습니다(주로 MongoDB의 벡터 데이터베이스 엔진 알고리즘의 축적이 이러한 전문화된 벡터 데이터베이스와 비교해 약하며, 추론 시나리오가 확대될수록 엔진 기능에 대한 요구사항도 상당히 증가할 것).
- 새로운 세대의 RAG 방법은 밀집 임베딩과 벡터 데이터베이스를 결합하기 때문에 전통적인 BM25에 대해 매우 높은 수준의 요구사항이 있으며, 이 부분에서 MongoDB의 솔루션이 Elastic보다 못할 수도 있습니다.
- MongoDB에는 아직 많은 기능적 격차가 남아 있습니다.

# 세계에는 엔드투엔드 기술 스택이 필요합니다.

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

다음 표에는 세 회사의 LLM 진척 상황이 나와 있습니다. 첫 번째는 교육 분야입니다:

- Databricks는 완전한 프로세스 교육 기술 스택을 갖추고, MosaicML을 통해 마지막 퍼즐 조각을 채웠습니다. 그러나 여전히 대규모 모델 훈련에서 공용 클라우드에 한 발짝 뒤처지고 있습니다.
- Snowflake는 현재 노트북, 데이터 레이크, 모델 훈련 최적화 및 MLFlow 레이어에서 상당한 차이가 있어 보완 작업 중이며, 현재는 고객이 컨테이너 서비스에서 세밀 조정을 수행할 수 있도록 하는 데 보다 집중하고 있습니다.
- MongoDB는 추론 쪽에 초점을 맞추어 기본적으로 교육에 개입하지 않습니다.
- Databricks의 RAG 솔루션이 아직 공개 베타 상태이며, 원 스탑 추론 능력이 아직 갖춰지지 않았지만 올해 중에 완료될 것으로 예상됩니다.
- Snowflake의 Snowpark ML 및 RAG 솔루션이 또한 공개 베타 상태이며, 컨테이너 서비스 데이터 응용프로그램에서 추론 배포에 대한 미래 지원이 더 있으며, 이는 챗봇 및 기업 지식베이스와 같은 시나리오일 수 있습니다.
- MongoDB는 세밀 조정 및 컨테이너에는 관여하지 않지만, 최종 사용자를 위한 RAG 솔루션이 더 중시되며, 보다 폭넓은 고객 기반을 대상으로 합니다.

기술 선도 기업들은 이미 세 가지 큰 클라우드 및 다양한 AI-네이티브 플랫폼의 LLM 기술 스택을 채택하고 있으며, 세 회사에 대한 미래의 주요 증가 기회는 전통적인 기업 시나리오입니다:

- 전통 기업의 경우, 종단 간 기술 스택이 매우 중요합니다. LLM 인재 부족 시대의 고객은 최고의 LLM 팀을 구축할 수 없으므로 교육/추론 프로세스가 간단할수록 좋습니다.
- 전통 기업은 또한 LLM 예산을 늘리고 있습니다. 이는 고객 서비스와 같은 시나리오에 대해 오픈 소스 모델을 교육하거나 다른 써드파티 소프트웨어 응용 프로그램 솔루션을 구매하는 것을 통해 이루어질 수 있습니다.
- 그러나 역사적으로 초기 응용 솔루션 제공 업체는 자체 내장 데이터 인프라를 제공할 수 있지만, 생태계가 통합되면 고객은 모든 써드파티 솔루션을 지원하기 위해 자체 데이터 인프라를 사용할 가능성이 더 높습니다.

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

# 전투는 새로운 장을 여는 새로운 시작이기도 합니다

지난 몇 년간 데이터 인프라 주변의 경쟁은 언제나 다음을 초점으로 했습니다: 클라우드 아키텍처 또는 온프레미스 아키텍처, 레이크 또는 데이터 웨어하우스, NoSQL TP 또는 SQL TP.

이제 LLM에 의해 주도되는 새로운 데이터 인프라 수요로 인해, 이제는 다음과 같은 질문이 되었습니다:

- 그들은 가능한 한 빨리 새로운 제품을 만들어 추가적인 시장을 확보할 수 있을까요?
- 새로운 제품을 만들 수 없고 LLM 팀을 영입하지 못한다면, 영원히 뒤처지고 예전 제품에서도 점유율을 잃을 것인가요?

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

그래서 우리는 Snowflake과 같은 기업이 AI에 올인하기 위해 CEO를 대체하는 움직임을 보게 됩니다.

Databricks와 Snowflake 이외의 다른 회사가 MosaicML을 인수하거나 DeepSpeed 팀을 영입할 수 있을지 상상하기 어렵습니다. 새로운 LLM 인재들은 주요 데이터베이스 기업에만 끌릴 수 있으며, 이는 오픈 소스, 온프레미스, 그리고 남은 데이터베이스와의 격차를 더 확대할 수 있습니다.

이것은 싸움이지만, 더 큰 점증적 기회일 것으로 보입니다.

6월의 연간 제품 이벤트에서 이러한 기업들이 새로운 제품을 대거 선보일 것입니다:

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

- 데이터브릭스는 Vector 검색 및 컨테이너 서비스 솔루션을 GA할 수 있습니다.
- Snowflake는 Cortex 기능, 컨테이너 서비스, SnowparkML, 노트북, Iceberg, Streamlit 솔루션을 GA할 수 있으며, 진행 속도가 빠르다면 Vector 검색도 GA할 수 있습니다.
- MongoDB를 포함한 각 회사는 RAG 기능을 지속적으로 개선하며 시간과의 경쟁을 벌이고 있습니다.
