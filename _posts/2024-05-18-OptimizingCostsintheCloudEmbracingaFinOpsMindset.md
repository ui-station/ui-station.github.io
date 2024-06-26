---
title: "클라우드 비용 최적화 핀옵스 마인드셋 수용하기"
description: ""
coverImage: "/assets/img/2024-05-18-OptimizingCostsintheCloudEmbracingaFinOpsMindset_0.png"
date: 2024-05-18 16:21
ogImage:
  url: /assets/img/2024-05-18-OptimizingCostsintheCloudEmbracingaFinOpsMindset_0.png
tag: Tech
originalTitle: "Optimizing Costs in the Cloud: Embracing a FinOps Mindset"
link: "https://medium.com/towards-aws/optimizing-costs-in-the-cloud-embracing-a-finops-mindset-c48acde94a94"
---

![image](/assets/img/2024-05-18-OptimizingCostsintheCloudEmbracingaFinOpsMindset_0.png)

서버리스 서비스를 설계할 때, 퍼즐의 각 조각, 선택하는 각 관리 서비스는 구매 선택입니다. 게다가 프로덕션 급 클라우드 서비스가 추가 비용을 야기하며, 주의를 기울이지 않으면 비용이 빠르게 증가할 수 있습니다.

이 블로그에서는 아낌없는 조직이 FinOps 마인드셋에 기대어 클라우드 서비스의 비용을 최적화하고 효율을 극대화하는 데 중요한 역할을 하는 방법을 배우게 될 것입니다. 클라우드에서 재무와 운영 목표를 조율하는 데 필수적인 전략과 실행 항목을 공유할 것입니다.

서버리스 서비스를 예로 들며, 클라우드 서비스 및 선택한 기술에 도움이 되는 통찰력, 자동화, 문화에 대해 설명할 것입니다.

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

이러한 통찰은 저의 경험에 근거하여 CyberArk에서 AWS 기반 SaaS 제공 업체의 서버리스 서비스를 설계한 것에 기인합니다.

![이미지](/assets/img/2024-05-18-OptimizingCostsintheCloudEmbracingaFinOpsMindset_1.png)

이 블로그 게시물은 원래 "Ran The Builder" 웹사이트에 게시되었습니다.

# 서버리스 비용에 대한 오해

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

AWS 서버리스 히어로로서, 클라우드 비용과 서버리스 서비스에 대해 이야기할 때 놓치지 말아야 할 부분을 다루고 싶어요.

서버리스는 사용한 만큼만 지불하고 0으로 스케일링이 가능하다는 것으로 알려져 있죠.

만약 그게 사실이라면, 생산급 서버리스 서비스 비용이 비서버리스 서비스보다 낮을 거라고 생각할 수 있겠죠?

음, 그건 가끔 정확한 말이에요. 확실히 Lambda, SNS, SQS, DynamoDB와 같은 "참" 서버리스 서비스에 대해서는 사실이지만, 서버리스에는 더 많은 서비스가 포함되어 있고, 매년 새로운 AWS 서버리스 서비스가 등장하고 있어요.

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

예를 들어, DynamoDB가 더 이상 요구 사항과 일치하지 않는 것을 깨달을 수도 있습니다. Amazon Aurora 서버리스를 사용하거나 Elasticache 서버리스를 혼합해 캐시를 추가하거나 OpenSearch 서버리스로 키워드 검색을 최적화할 수도 있습니다. 이러한 서비스들은 서버리스 변형이 있지만 영구로 스케일되지 않습니다. 고객 트래픽이 없더라도 최소 요금을 지불해야 합니다. 따라서 아마도 이러한 서비스들을 AWS가 관리하지만 진정한 서버리스 서비스는 아닌 것으로 부르는 것이 가장 좋을 수 있습니다. 또한, 이러한 서비스들은 VPC가 필요하며, ENI, VPC 엔드포인트 등으로 매월 고정 비용이 추가됩니다.

그리고 Jeremy Daly는 Allen Helton의 훌륭한 팟캐스트에서 AWS의 최신 서비스들의 서버리스 또는 서버리스가 아닌 특성에 대해 논의했는데, 권해드립니다.

# 프로덕션 등급은 추가 비용이 발생합니다.

클라우드 서비스를 위해 선택한 기술에 관계없이 언젠가는 프로덕션을 준비해야 합니다. 규정, 보안 및 관측 요구 사항을 해결해야 하며, 이는 추가되는 과소 평가된 비용을 야기합니다.

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

우리의 사전 제품 서비스에 이러한 기능 몇 가지를 추가해 보는 건 어떨까요?

우리는 서비스에 고객 데이터 암호화 기능을 추가해야 합니다. 우리는 KMS CMK를 사용하여 고객 데이터를 암호화하거나 서비스 간 통신을 용이하게 할 수 있습니다. CMK는 API 호출을 포함하지 않고 제공만 되어도 매달 1달러가 들고, 키 자동 회전을 활성화하면 추가 1달러가 소비됩니다. 10000명의 고객이 있을 것으로 예상하시나요? 멋져요, 당신의 AWS 청구서에 매달 추가로 20000달러가 더해집니다.

프로덕션 준비 관행으로 넘어가 봅시다. 웹 보안과 관찰성을 더해봅시다.

API Gateway나 CloudFront 분산에 웹 어플리케이션 방화벽을 활성화하고 CloudWatch 대시보드를 통해 관찰성을 향상시킬 수 있습니다. 이러한 리소스들은 매달 지속적인 가격이 부여되며, 서비스가 매달 트래픽을 전혀 받지 않더라도 이용료가 부과됩니다. 이와 유사한 사례들이 많이 있습니다.

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

서비스를 제품으로 제작할 때 수많은 작은 비용이 쌓입니다. 사람들은 이를 인식해야 합니다. 그럼에도, 그저 다른 CMK일 뿐이죠; AWS CDK에서 생성하는 한 줄 뿐이니까; 해로운 점이 뭐가 있을까요?

여러 개의 계정을 사용한다면(당신이 해야 하는 대로) - 개발, 테스트, 제품 용으로 - 모든 비용 추가는 보유한 계정 수에 곱해질 수 있습니다. 한 계정당 5개의 지역에 배포하나요? 그럼 추가적으로 15(5\*3 - 계정 수)개의 CMK가 생깁니다. 다시 한 번 곱하세요.

이러한 비용은 개발 계정에서 특히 크게 누적됩니다. 자원이 배포되고 삭제되며 종종 콘솔을 통해 수동으로 생성해서 잊혀지기 쉽기 때문입니다. 하지만 AWS는 그 기록을 기억하고 당신은 달이 끝날 때 청구서를 받을 것입니다.

## 고객이 다가온다

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

마지막으로, 대부분의 분들에게는 분명한 사실일 것이지만, 고객을 많이 모은다면 규모, API 호출 횟수, 그리고 저장 데이터 양이 커질 것입니다. 이 모든 것이 AWS 클라우드 요금 증대로 이어집니다. 이러한 추가 비용을 계획하고 수익 모델에 반영하지 않으면 비즈니스가 유지될 수 없습니다.

요점은 프로덕션급 서버리스나 서버리스가 빠르게 증가할 수 있는 추가 고정 비용이 있다는 것입니다. 여러 비트와 바이트에 대해 지불해야 하며 처음부터 이를 인식해야 합니다. 예상 고객 트래픽 규모를 위한 예산을 설정하고 이를 지속적으로 모니터링하여 비용이 억제되도록 해야 합니다.

이제 문제를 이해했으니 당신의 조직이 이 문제를 대처하고 비용을 줄이며 효율성을 향상시킬 수 있는 방법에 대해 이야기해 봅시다. 그것은 FinOps 마인드셋을 채택함으로써 가능합니다.

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

FinOps를 채택하고 비용을 인식하는 것은 조직의 C급 임원에게 중요합니다. 그러나 위에서 논의한 대로, 모든 설계 선택, 모든 CloudFormation 스택 배포, 모든 IT/DevOps 예약 작업 실행은 구매 선택입니다.

귀하의 팀은 매일 AWS 계정에서 돈을 소비합니다.

조직의 사고방식을 비용 인식적으로 바꾸고 싶다면, 그것은 아래부터 시작되어야 합니다. 아키텍트와 팀 리더가 선도할 수 있지만, 아래에 있는 병력들은 따라와서 목표를 이해해야 합니다. 개발자들이 다양한 리소스를 배포하는 것을 막는 자동화를 추가할 수 있지만, 장기적으로 이는 개발팀의 독립성을 방해하고 확장되지 않을 것입니다. 사람들은 FinOps 사고방식을 받아들이고, 이해하며, 개발의 모든 단계에서 클라우드 비용에 대해 생각해야 합니다.

다음 섹션에서는 귀사에서 구현할 수 있는 구체적인 조치 항목을 설명하겠습니다. 아키텍트부터 DevOps, IT, 개발자까지 페르소나는 다를 수 있지만, 아이디어는 같습니다: AWS 비용을 줄이기 위해서는 한 마을이 필요하며, 모두가 함께해야 합니다.

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

# 비용 고려한 디자인

클라우드 아키텍트로써, 우리는 조직에서 전체 AWS 비용에 가장 큰 영향을 미치는 몇 안 되는 사용자 중 하나입니다.

지난 AWS re:Invent에서 Vogels는 "절약하는 아키텍트" 가이드라인을 논의했는데, 이는 제 블로그 게시물 "클라우드 아키텍트의 고수준 디자인 템플릿"과 관련이 있습니다.

그의 세션에서 주요 포인트는 모든 클라우드 아키텍처 디자인 선택이 구매 선택과 관련이 있다는 것입니다.

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

건축가로서, 우리는 서비스의 고수준 디자인 뿐만 아니라 저수준 디자인에도 영향을 미칩니다. 우리 회사에서는 개발자들이 제 동행과 함께 저수준 디자인과 컨셉 증명을 수행합니다. 따라서, 이른 시기에 비용을 고려하는 것이 중요합니다.

서버리스 서비스를 설계할 때는 종종 여러 가능한 아키텍처가 있습니다. 예를 들어, SNS 또는 EventBridge를 사용하여 이벤트를 구독자에게 발행할 수 있습니다.

최선의 결정을 내리기 위해, 양쪽 솔루션을 비교할 수 있는 의사 결정 매트릭스를 사용하고 예상 비용을 비기능적 요구 사항으로 고려하는 것을 권장합니다.

이 프로세스에 대해 자세히 설명한 블로그 포스트는 Cloud Architect's High-Level Design Template에서 확인할 수 있습니다.

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

고객 수 및 규모에 대한 예상을 고려하고 이러한 숫자를 AWS 요금 계산기에 입력하세요. 이후에 AWS 비용이 나중에 급증하고 손쉽게 대체할 수 없는 비용이 들어간 제품이 프로덕션 환경에 배포되었을 때 놀라지 마세요.

하지만 여기서 끝이 아닙니다. 아키텍트로서 모든 것을 감시할 수는 없고, 개발자들은 기능을 구현할 때 가격 선택을 합니다. 예를 들어, 내부 대시보드용으로 사용자 정의 CloudWatch 메트릭을 추가하지만 차원을 너무 많이 사용하여 비용이 크게 증가하는 경우가 있다(실제 사례!). 팀이 비용을 종합적으로 고려해 독립적으로 이러한 선택을 할 수 있도록 하여, 상당히 중요합니다. 거의 모든 리소스 배포나 API 호출이 추가 클라우드 비용을 발생시킨다는 점을 이해해야 합니다.

# FinOps 문화

조직 전체의 팀이 클라우드 비용을 이해하고 활발히 줄이고 최적화할 수 있도록 하는 문화적 실천 방법을 검토해봅시다.

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

## 금융 오퍼레이션 챔피언

먼저, 각팀 (개발자, IT, 데브옵스 등)에서 금융 오퍼레이션 챔피언을 뽑는 것이 좋습니다. 그들은 GitHub PR이 예상치 못한 비용 증가를 유발하지 않도록 확인하는 역할을 맡고 있습니다. 이 챔피언들은 설계 검토 중에 비용 우려 사항을 제기하고 팀이 비용을 염두에 두도록 도와줍니다.

이상적으로, 금융 오퍼레이션 챔피언은 항상 클라우드 비용을 고려합니다. 설계, 구현 또는 프로덕션 배포 단계에서 모두 해당합니다.

이 챔피언은 제가 아래에 설명할 '청구서 모니터링' 섹션에 적극적으로 참여할 수 있습니다.

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

## FinOps Guild

두 번째 실천 방법은 부서 간 및 조직 간 지식 공유입니다. 한 팀이 새로운 Lambda에서 프로비저닝된 동시성의 비용을 줄이는 최상의 실천 방법을 발견했다고 가정해 봅시다. 한 팀이 그 문제를 해결한 것은 좋지만, 동일한 최상의 실천 방법이 조직 전체에 구현되는 것이 더 좋을 것입니다. 이를 위해 지식을 공유할 수 있는 메커니즘이 필요합니다.

그런 메커니즘 중 하나는 모든 FinOps 챔피언들이 매월 한 번 지식을 공유하는 FinOps 길드를 시작하는 것일 수 있습니다.

내부 조직 외부 사람들을 만나 IT, 데브옵스, 제품 등과의 새로운 관계를 형성하는 것도 추가 가치입니다.

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

참고로, 공급된 사전 할당량 및 차가운 시작에 대해 더 알고 싶다면 여기에 있는 내 게시물을 확인해보세요.

## FinOps 내부 교육

길드 회의는 정보를 공유하는 좋은 방법입니다. 그러나 더 작은 대상을 대상으로 하는 한계가 있어서 이전 회의의 최상의 실천법이 잊혀질 수 있으므로 귀하의 최상의 실천법을 문서화하는 것이 가장 좋습니다. 간단한 내부 문서부터 시작하거나 내부 비디오 강좌를 만들기 위해 노력해보세요. 강좌는 전문적으로 편집된 비디오일 필요는 없습니다. 팀/슬랙 회의 녹화일 수도 있습니다. 콘텐츠는 신규 FinOps 챔피언을 신속하게 승격시키고 비용 절감 관행을 확산시키기 위한 빠른 방법으로 기능해야 합니다.

## FinOps 해커톤

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

이것은 재미있는 아이디어입니다. FinOps를 게임처럼 만들고 직원들이 개선, 자동화 및 기타 비용 절감 방법에 대한 아이디어를 제시할 수 있는 해커톤을 소개해 보세요. 이를 흥미롭게 만들기 위해 가장 영향력 있는 아이디어에 대한 상품을 제공해 보세요.

## 성공을 축하하세요

이 행동 항목은 모든 문화에 중요한 부분이므로 가장 중요할 수 있습니다. 누구나 회사 이익을 개선하는 일에 대한 피드백을 감사히 받을 것이며, 특히 그 작업이 회사 수익을 증가시킬 때 더욱 그렇습니다. 조직 전체에서 성공을 인정하는 것은 긍정적인 효과를 창출하고 다른 팀들이 FinOps 방법을 도입하고 자신들의 비용을 줄일 때를 축하할 동기를 주게 할 것입니다.

# 청구서를 모니터링하세요

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

클라우드 재무를 관리할 때는 예산 계획을 따르는 것이 매우 중요합니다. 돈을 현명하게 쓰기 위해서는 AWS 비용 탐색기나 Anodot과 같은 타사 서비스와 같은 도구를 활용하여 지출 패턴을 파악하는 것이 좋습니다.

예산 초과에 대한 경보를 설정하고 매달 지출 내역을 정기적으로 모니터링하여 각 팀이나 서비스와 관련된 비용에 집중할 수 있습니다.

리소스마다 태그를 추가하여 팀 이름, 마이크로서비스 이름 또는 다른 태그를 포함하여 각 팀에 대한 비용을 파악할 수 있습니다. 여러 팀이 동일한 AWS 계정을 공유하는 경우 비용을 이해하는 데 도움이 됩니다. 그런 다음 AWS 비용 탐색기에서 필터링할 수 있습니다.

더 자세한 내용은 이곳에서 확인할 수 있습니다.

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

또한 SaaS (Software as a Service) 제공업체이면 각 고객의 비용을 예산 편성, 라이선스 및 수익성을 위해 추정(또는 많은 경우에는 "guesstimate")하는 것이 중요합니다. 이 작업은 간단한 일이 아니며, 풀 또는 사일로 모델 테넌트 격리 전략을 사용하는 경우 도전이 매우 다릅니다. 그러나 이 주제는 이 게시물의 범위 내에서 다루기에는 너무 방대합니다.

여러 계정을 관리하거나 여러 클라우드 공급 업체를 사용하는 조직에게는 Anodot과 같은 도구가 중요한 이점을 제공할 수 있습니다. Anodot은 비용 관리에 대한 집중된 접근 방식을 제공합니다.

마지막으로, FinOps 챔피언 및 기타 이해 관계자가 이러한 도구에 액세스하고 대시보드를 보고 경보를 정의할 수 있도록하세요.

# 비용 방어 자동화

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

클라우드 비용을 절감하는 방법 중 하나로 자원 삭제를 자동화하거나 일부 리소스가 처음부터 생성되지 않도록 하는 조직 정책이나 메커니즘을 추가할 수 있습니다.

떠오르는 몇 가지 아이디어는 다음과 같습니다:

- 사용되지 않는 KMS CMK와 같은 자원들을 자동으로 삭제합니다.
- 삭제되지 못한 CloudFormation 스택 및 "좀비" 리소스가 남아 있는 스택을 자동으로 삭제합니다. 예를 들어 S3 버킷이 비어 있지 않아 삭제되지 못한 스택이 있습니다.
- 개발자가 비인증 지역에 배포하는 것을 방지하는 정책을 시행합니다. AWS 서비스의 가격은 지역에 따라 다릅니다. 필요한 서비스를 사용할 수 있는 지역을 선택하고 고객에게 충분한 응답 시간을 제공할 수 있는 비용 대비 서비스를 선택합니다.
- 비개발 계정에서 콘솔을 통해 자원을 생성하지 못하도록 하고, 인프라스트럭처-애스-코드(IAC) 방식으로만 생성하도록 합니다. 잊혀진 고아 자원을 생성할 위험을 줄입니다.
- 스택에 속하지 않거나 사용하지 않는 고아 자원 또는 "드리프트 된" 자원을 찾아 삭제합니다.
- 잠재적인 비용 급증을 알리는 경고를 설정하여 선제적으로 관리합니다. AWS의 비용 이상 탐지와 같은 도구를 사용하여 초기에 비정상적인 지출 패턴을 식별하는 데 도움을 받습니다.
- 밤에 EC2 인스턴스를 자동으로 종료하는 예약된 작업을 만듭니다.
- 그 외 다양한 방법이 있습니다. 사용 사례에 따라 다를 수 있습니다.

여기서 가장 중요한 교훈은 선제적인 접근 방식을 채택하는 것입니다. 놀라지 마세요. 비용을 적극적으로 줄이기 위해 노력하세요.

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

# 지속적인 학습

기사, AWS 모임 및 기타 교육 자료를 통해 계속해서 학습하는 것이 매우 중요합니다. 언제 어떤 새로운 기능이나 메커니즘을 발견할지 모릅니다. 혁신과 서비스 발표를 계속해서 파악하는 것도 중요합니다.

모든 것은 변화와 리팩터링에 관한 것입니다.

가끔은 HTTP를 REST API 게이트웨이로 대체하거나 Lambda Powertuning과 같은 도구를 활용하여 함수를 최적화하거나 CloudWatch 로그 보존 기간을 줄이고 로그 수준을 변경하는 등의 서비스 변경이 큰 비용 절감을 이끌 수 있습니다.

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

팁들이 지나치게 상세해 보일 수 있지만, 그것들은 단지 빙산의 일각에 불과해요. 각 AWS 서비스는 독특한 최적화 기회를 제공하며, FinOut의 DynamoDB 가격 도전과 Best Practices에서 제시된 구체적인 전략들이 그것을 보여줍니다.

집중해서 연구하고 배우고 최적화하세요. 장기적으로 보면 그 노력이 보람 있을 거예요.

# 요약

이 글에서는 모든 조직이 FinOps 마인드셋을 채택해야 할 관행을 다루었습니다. 클라우드 비용을 예방적으로 관리하고 항상 최적화하고 줄이기 위해 전체 조직의 노력이 필요하다는 점을 논의했어요.

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

이 게시물이 귀하의 조직이 클라우드 비용 낙원을 실현하기 위해 FinOps 여정에서 도움이 되길 바랍니다. 행운을 빕니다!
