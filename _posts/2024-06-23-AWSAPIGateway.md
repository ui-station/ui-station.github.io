---
title: "2024년 최신 AWS API Gateway 사용 방법 및 기능 완벽 가이드"
description: ""
coverImage: "/assets/img/2024-06-23-AWSAPIGateway_0.png"
date: 2024-06-23 00:28
ogImage:
  url: /assets/img/2024-06-23-AWSAPIGateway_0.png
tag: Tech
originalTitle: "AWS API Gateway"
link: "https://medium.com/@augustomarinho/aws-api-gateway-bd26fe89df98"
---

## AWS API Gateway를 효과적으로 사용하는 필수 팁

![AWS API Gateway](/assets/img/2024-06-23-AWSAPIGateway_0.png)

# 소개

모놀리식 또는 마이크로서비스 아키텍처를 사용하더라도 API에 접근하기 위한 중앙 진입점은 중요합니다.

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

기업에서는 비즈니스의 다른 부분에 작업을 수행하는 여러 팀이 있고, 각 팀은 다른 애플리케이션에 서비스를 노출하기 위해 API를 구현합니다.

시간이 지나면서 조직의 아키텍처에 대한 포괄적인 시각 없이, API 진입점, 트래픽 관리, 보안, 그리고 이러한 제어를 구현하기 위한 규칙이 없다는 문제에 직면할 수 있습니다. 이러한 도전은 특히 인터넷을 통해 상호 작용이 발생할 때 특히 어렵습니다.

본 문서는 HTTP 기반 애플리케이션 상호 작용에서의 일반적인 도전 과제를 강조하고, API 게이트웨이가 이러한 문제에 대해 어떻게 대응할 수 있는지에 대해 살펴봅니다. 구체적으로 AWS API 게이트웨이 사용에 중점을 두고 구현하기 전에 알아야 할 사항에 대해 설명하겠습니다.

# API 게이트웨이가 왜 필요한가

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

현대 소프트웨어 아키텍처는 클라이언트가 데이터를 사용하고 생성하는 방법을 제어하고 동시에 보호 기술을 구현하는 인프라 요소가 필요합니다.

이 토론에서 자주 물들어 있는 질문은 API 게이트웨이가 이 보호와 분할을 구현하는 유일한 해결책인가요? 확실한 대답은 아니에요.

역 프록시나 로드 밸런서를 사용할 수 있지만 네트워킹과 관련한 여러 이유로 API 게이트웨이가 선호됩니다.

API 게이트웨이는 OSI 모델의 응용 계층(레이어 7)에서 작동하여 기본 SSL 종료나 백엔드 인스턴스 간 요청 분배 이상의 고급 기능을 제공합니다.

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

API Gateway 솔루션은 인증 및 권한 부여, 속도 제한/쓰로틀링, 액세스 로깅, HTTP 프로토콜에 대한 자세한 내용, 회로 차단기 구현 등과 같은 책임을 통합합니다.

API Gateway를 네트워킹 아키텍처에서 DMZ와 비교할 수 있습니다.

클라우드 컴퓨팅이 급부상함에 따라 소프트웨어 아키텍처 프로토타입은 레고 블록을 사용하여 구축하는 것과 같습니다. 핵심은 요구 사항과 기술을 기존 자원과 도구로 결합하는 것입니다.

인터넷을 통해 API를 노출시키기 위해 여러 솔루션을 유지하고 싶지 않다면, API Gateway가 최선의 방법입니다. AWS를 사용하고 하드 제한이 문제가 되지 않는다면, AWS API Gateway가 강력한 선택입니다.

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

다음에는 귀하의 요구 사항에 가장 가까운 옵션을 선택하는 방법에 대해 더 자세히 설명하겠습니다.

# 어떤 종류의 API 게이트웨이가 필요할까요?

좋은 시작점은 AWS API Gateway 문서를 읽는 것입니다. 여기에서 REST API와 HTTP API 간의 선택 사항에 대한 자세한 내용을 찾을 수 있습니다.

본 문서에서 읽을 수 있는 것과 똑같은 내용을 설명할 의도는 없지만, 리전별, 엣지, 또는 프라이빗 API 게이트웨이와 같은 엔드포인트 유형에 대한 개념을 깊이 이해하는 것의 중요성을 강조하고 싶습니다. REST API 또는 HTTP API 인스턴스에서 사용할 수 있는 각 유형에 대해 알아보세요.

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

인스턴스 유형에 대한 간단한 설명은 다음과 같습니다:

- 보안, 관리 및 인증 기능이 더 필요하고 수요가 지리적으로 분산되어 있다면 (인터넷을 통해) REST API 인스턴스를 사용하세요(엣지 최적화, REST API의 기본 값).
- 동일 지리적 지역 내에서 API Gateway를 내부적으로 사용하고 수요가 낮다면 HTTP API 인스턴스(지역)를 사용하세요.
- VPC 내에서 사용하는 경우 VPC 엔드포인트를 통해 REST API 인스턴스를 사용하세요.

실제 예제를 통해 엣지 최적화 또는 지역 AWS API Gateway 인스턴스를 선택하는 차이를 보여주는 것이 중요합니다.

저는 PetStore 예제를 사용하여 미국 동부 버지니아 (us-east-1) 지역에 두 개의 AWS API Gateway 인스턴스를 생성했습니다.

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

![이미지](/assets/img/2024-06-23-AWSAPIGateway_1.png)

첫 번째 인스턴스(ID w43fgx5wk6)는 엣지 최적화되었고, 두 번째 인스턴스(ID 16hx5blsm5)는 리전 최적화되었습니다.

AWS API 게이트웨이 설명서에 따르면 엣지 최적화는

또한, 이 정보를 HTTP 헤더 응답을 검사하여 확인할 수 있습니다.

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

![AWS API Gateway](/assets/img/2024-06-23-AWSAPIGateway_2.png)

x-amz-cf-pop 헤더는 예약된 HTTP 헤더이며, 이를 검사하여 엣지 최적화된 AWS API Gateway를 확인할 수 있습니다.

이 ID들과 관련이 있는 POP (Points of Presence) 목록에 대해 더 많은 정보가 필요합니다. 하지만 구체적으로 링크에서 ID G1G51-P2를 발견했습니다.

![AWS API Gateway](/assets/img/2024-06-23-AWSAPIGateway_3.png)

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

지역 인스턴스를 분석하면이 유형의 인스턴스가 POP을 통과하지 않는 것을 확인할 수 있습니다.

![AWS API Gateway](/assets/img/2024-06-23-AWSAPIGateway_4.png)

## 각각의 지연 시간은 무엇인가요?

먼저, 각 AWS 지역과 관련된 지연 시간을 이해해야합니다. 이 측정을 위해 https://clients.amazonworkspaces.com/Health.html을 사용합니다.

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

<img src="/assets/img/2024-06-23-AWSAPIGateway_5.png" />

위의 이미지를 보면 가장 가까운 지역은 남아메리카(상파울루)입니다. 이 정보를 기반으로 지리적으로 분산된 지연 시간을 확인하여 엣지 최적화된 AWS API 게이트웨이 인스턴스와 지역별 인스턴스의 장점을 비교할 수 있습니다.

측정을 위해 저는 인터넷을 통해 전 세계의 다른 지역에서 클라이언트 경험 액세스를 시뮬레이션하는 데 사용되는 Pingdown을 사용합니다.

지연 시간을 비교하기 위해 북아메리카, 남아메리카 및 아시아에서 엣지 최적화된 인스턴스 및 지역별 인스턴스에 액세스를 시뮬레이션했습니다.

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

아래 이미지는 엣지 최적화 인스턴스 (w43fgx5wk6)에 대한 결과를 보여줍니다.

![AWS API Gateway](/assets/img/2024-06-23-AWSAPIGateway_6.png)

북미와 라틴 아메리카 사이의 응답 시간에 주목해 주시기 바랍니다. 두 지역 간의 거리를 고려할 때, 약 20ms 차이는 미미합니다.

아시아의 경우 응답 시간이 상당히 높으며, 숫자를 더 자세히 조사해야 합니다.

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

아래 이미지는 이전에 제시된 것과 유사하며, AWS 지역 간의 내 지연 시간을 보여줍니다. 특히, 도쿄까지의 지연 시간은 732밀리초입니다.

![이미지](/assets/img/2024-06-23-AWSAPIGateway_7.png)

도쿄에서 시작된 요청으로 PetStore API를 호출하는 PingDown과 비교했을 때, 도쿄에서 미국 동부 버지니아 북부까지의 지연 시간이 760밀리초임을 확인할 수 있습니다. 흥미롭게도, 도쿄에서 버지니아까지의 지연 시간은 남아메리카에서 도쿄까지의 지연 시간과 유사합니다.

![이미지](/assets/img/2024-06-23-AWSAPIGateway_8.png)

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

28ms의 델타에서 주요 포인트는 대부분의 시간이 네트워크 왕복 시간에 소비된다는 것입니다. 이러한 숫자들은 다양한 위치에서 아시아로의 요청 지연 시간에 대한 소중한 통찰을 제공합니다.

싱가포르, 홍콩, 시드니 등 아시아의 다른 위치들은 더 나은 지연 시간 값을 보여줍니다. 여기서 중요한 점은 아메리카에서 아시아로의 요청은 적어도 이 간단한 예에서 더 높은 지연 시간 패널티가 부과된다는 것입니다. 기반 시설과 API 배포를 계획할 때 이 정보를 염두에 두세요.

지역별 인스턴스에 관한 이미지는 다음과 같습니다.

![이미지](/assets/img/2024-06-23-AWSAPIGateway_9.png)

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

북미(로컬 API Gateway 인스턴스가 실행 중인 곳)에서 멀어질수록 미리측된 밀리초 증가가 더 두드러집니다. 지연 시간은 북미에서 라틴 아메리카, 그리고 아시아로 이동함에 따라 거의 두 배로 증가하는 것 같습니다.

이 분석 결과, 지역 또는 엣지 최적화된 AWS API Gateway 인스턴스 중에서 선택하는 것이 솔루션의 성능에 상당한 영향을 미친다는 것을 보여줍니다.

중요한 주의 사항은 테스트 기간의 시간 창이 아주 짧다는 것입니다. 이러한 숫자를 제시하는 목적은 시간이 흐름에 따라 다양한 이유로 상당히 달라질 수 있는 파트너의 경험에 대한 지연 시간 영향을 설명하기 위한 구체적인 데이터를 제공하는 것입니다.

이 관점은 중요하지만 AWS API Gateway를 효과적으로 사용하기 위해서는 다른 특성들을 이해해야 합니다.

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

# 어떤 요구 사항이 있나요?

AWS 문서에서 속한 제한에 관한 부분을 읽을 때 중요합니다. 특히, 제한을 증가 요청할 수 없는 제한에 대한 부분이요. AWS API Gateway의 경우, 여기에서 이러한 제한을 확인할 수 있어요.

귀하의 경우에 관련된 특정 제한 사항은 다를 수 있기 때문에, 사용 가능한 제한 사항과 귀하의 요구 사항을 신중히 맞추어 정보를 얻을 수 있도록 하는 것이 중요해요.

고려해야 할 주요 제한 사항은 다음과 같아요:

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

이 할당량은 문서에 따라 증가될 수 있지만, 귀하의 계정과 지역에 있는 모든 AWS API Gateway 인스턴스를 고려하여 10K RPS의 양에 주의를 기울이고 싶습니다.

고려해야 할 또 다른 점은 문서에서 할당량이 증가될 수 있다고 명시되어 있지만, 이를 쉽거나 싸게 처리하기 어려울 수 있습니다. 어떤 경우에는 AWS 지원팀이 요청을 정당화하고 해당 지역에서 서비스에 영향을 미치지 않도록 보장하기 위해 상세 증거를 요청할 수 있습니다.

AWS API Gateway REST API 인스턴스의 최대 타임아웃은 대략 29초입니다. 대부분의 API 통신에는 충분합니다. 그러나 장기 실행이 포함된 경우 해당 제한을 염두에 두시기 바랍니다.

최근 AWS는 타임아웃을 증가시킬 수 있는 가능성을 발표했지만, 이는 계정 수준의 쓰로틀 할당량 제한을 감소시키는 데에 처벌이 따릅니다. 문서에서는 쓰로틀링 할당량이 얼마나 감소할 지 명시되어 있지 않지만, 이는 중요한 고려 사항입니다.

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

RPS는 동일한 계정 및 지역의 모든 API 게이트웨이 인스턴스의 요청 합계를 계산하는 것과 같이 API 키도 동일한 규칙을 따릅니다. 그러나 중요한 차이점은 API 키의 수를 늘릴 수 없으며, 모든 인스턴스 사이에서 공유되는 API 키의 하드 제한이 10K개이다.

이 할당량에 대한 주요 포인트는 쓰로틀링과의 직접적인 관련성입니다. 쓰로틀링은 다양한 특성이 있으며, 제가 요약해 보겠습니다.

쓰로틀링은 계정 수준에 적용될 수 있습니다.

이전에 언급한대로, 계정 수준의 쓰로틀링은 모든 AWS API 게이트웨이 인스턴스의 초당 요청을 제한하기 위해 초당 모든 요청을 합계하여 적용됩니다. 이 쓰로틀링에 대해 우리는 제어할 수 없습니다.

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

테이블 태그를 Markdown 형식으로 변경해주세요.

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

클라이언트별로 쓰로틀링을 적용하는 유일한 방법은 클라이언트를 API 키와 연관시키는 것입니다. 프로덕션 환경에서 1만 개의 API 키를 활성화하는 것은 불가능하지 않은 것으로 보입니다. 서로 다른 기준에 맞게 API 키를 관리할 수 있으므로 한 클라이언트나 클라이언트와 엔드포인트에 API 키를 사용할 수 있습니다.

이러한 API 키를 쓰로틀링 제한과 연관시키는 중요한 점은 사용 계획 내에서 사용해야 한다는 것입니다.

사용 계획의 할당량은 계정과 지역당 300개로 제한됩니다. 이 할당량은 증가시킬 수 있지만, 이러한 과정은 어려울 수 있고 AWS 지원팀에 상세 정보를 제공해야 할 수도 있습니다.

AWS API Gateway 인스턴스를 프로토타입으로 만들어 클라이언트, 클라이언트-엔드포인트 또는 다른 전략별 API 키를 사용하려면 할당량을 초과하지 않도록 사용 계획과 API 키를 구현하고 연관시키는 것이 중요합니다.

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

내 의견으로는 API 게이트웨이의 관련 기능으로 인해 클라이언트 당 쓰로틀링을 구현해야한다는 이 제한이 가장 어려운 것으로 생각됩니다. 따라서 이 제한이 솔루션에 영향을 미치지 않도록 주의하시기 바랍니다.

AWS API 게이트웨이 인터페이스에서 API 키를 조회하거나 검색하려면 1만 개의 API 키에 도달하면 속도가 매우 느려질 것입니다. 반드시 관리용 API를 사용하여 이를 조회해야 할 것입니다.

마지막으로, 관리용 API의 할당량이 매우 제한적이므로 이 제한도 주의 깊게 확인하셔야 합니다.

# 제약사항은 무엇인가요?

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

특정 제약 사항은 다음과 같은 유형의 제한 사항을 만날 경우 파트너들과의 갈등을 야기할 수 있습니다:

- 감사를 위해 요청과 응답을 저장해야 할 경우, CloudWatch에 저장할 수 있는 페이로드 크기 제한이 문제를 일으킬 수 있습니다. 이 경우 AWS API Gateway 외부에서 이 감사를 구현해야 할 수도 있습니다.

- RFC 3986 (통합 자원 식별자-URI)에 따르면 세미콜론은 특정 목적을 위해 예약된 문자입니다. URI에 세미콜론이나 파이프(|)가 포함되면 요청 처리가 복잡해질 수 있습니다.

- REST API의 AWS API Gateway의 기본 설정은 "백엔드 통합에 전달하기 전에 URL 인코딩된 요청 매개 변수를 디코딩(decoding)"하는 것입니다.

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

여기서 특별한 문제는 여러분의 파트너가 URL 인코딩을 구현해야 한다는 것입니다. 이를 역방향 프록시를 사용하여 해결하려고 하면 어려움을 겪을 수 있습니다. 이 상황을 피하기 위해 AWS Lambda@Edge와 CloudFront를 사용할 수 있지만, 과도한 엔지니어링에 주의해야 합니다.

역방향 프록시 시나리오를 모의하기 위해 edge-optimized API Gateway와 URL https://w43fgx5wk6.execute-api.us-east-1.amazonaws.com/test를 사용했습니다. beeceptor.com을 사용하여 외부 역방향 프록시를 정의했는데, 다음과 같이 구성했습니다:

![이미지](/assets/img/2024-06-23-AWSAPIGateway_10.png)

https://apigateway.free.beeceptor.com/pets를 호출하면 결과가 이렇게 나옵니다:

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

![image](/assets/img/2024-06-23-AWSAPIGateway_11.png)

특정 상황에서 확인해야 할 다른 제약 사항이 있습니다. 결정을 내리기 전에 api-gateway-known-issues에서 더 많은 정보를 읽어보세요.

# 결론

여기서는 AWS API Gateway를 사용하기 전에 공부하고 이해해야 할 중요한 요점을 설명했습니다.

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

AWS API Gateway은 제품 환경에서 사용할 수 있는 견고한 솔루션이에요. 어떤 솔루션을 채택하기 전에 요구 사항을 이해하는 것이 중요해요. AWS API Gateway를 사용하기로 결정하는 것도 마찬가지죠.

계정 당 및 리전 당 10K RPS와 관련된 할당량은 많은 경우에 적합하지만, 언제 아키텍처의 수명이 끝날지 예상하고 항상 준비해두는 것이 중요해요.

이러한 통찰력이 AWS API Gateway를 효과적으로 사용하는 데 도움이 되기를 바라요.
