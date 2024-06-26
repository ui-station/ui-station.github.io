---
title: "내부 데이터 제품을 데이터 엔지니어로서 고려하는 방법"
description: ""
coverImage: "/assets/img/2024-05-18-HowtothinkaboutInternalDataProductsasaDataEngineer_0.png"
date: 2024-05-18 15:56
ogImage:
  url: /assets/img/2024-05-18-HowtothinkaboutInternalDataProductsasaDataEngineer_0.png
tag: Tech
originalTitle: "How to think about Internal Data Products as a Data Engineer"
link: "https://medium.com/data-engineer-things/how-to-think-about-internal-data-products-as-a-data-engineer-42cef9081ebf"
---

# 나에 대해

안녕하세요! 저는 휴고 루입니다. 런던에서 M&A 업무를 시작으로 JUUL로 이직하여 데이터 엔지니어링에 빠져들었습니다. 잠시 금융 분야로 돌아온 후, 런던 소재 핀테크 기업 Codat에서 데이터 부서를 이끌었습니다. 지금은 Orchestra의 CEO입니다. Orchestra는 데이터 팀이 데이터를 안정적이고 효율적으로 프로덕션 환경에 배포할 수 있도록 도와주는 데이터 릴리스 파이프라인 도구입니다. 🚀

우리의 Substack와 내부 블로그도 꼭 확인해주세요! ⭐️

# 소개

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

현재는 데이터 팀 인원의 급격한 감소와 데이터에서 가치 추출 능력의 감소 때문에 Data Products이 현재 핫한 주제입니다. 데이터를 제품으로 생각하거나 "데이터를 제품처럼 취급하기"는 데이터를 이해하고 관리하며 데이터 엔지니어링 팀이 비즈니스를 위해 생산하는 Data Products의 속도와 수용을 향상시키는 인기있는 접근 방식입니다.

이 기사에서는 데이터 엔지니어로써 내부 데이터 제품을 어떻게 생각해야 하는지에 대해 알아볼 것입니다. 데이터 제품에 관한 몇 가지 일반적인 질문에 답하고, 여러분이 처음 데이터 제품을 만드는 것에 대해 어떻게 생각하는지 보여줄 것입니다.

# 데이터 제품이란?

다양한 강연, 소셜 미디어 게시물, 블로그 게시물 및 사고 리더십 자료들을 제외하고 데이터 제품은 본질적으로 간단한 개념입니다.

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

아이디어는 이중으로 이루어져 있어요. 첫 번째는 데이터에 제품 관리 원칙을 적용하는 것입니다. 작업 방식이 있습니다. 아래에 나열된 아이디어의 예시는 다음과 같아요:

- 왜부터 시작하기
- 문제를 이해하기
- 열심히 집중하기
- 팀에 권한 부여하기
- 불확실성 수용하기
- 입력, 출력, 결과 및 학습 균형 맞추기
- 반복, 반복, 반복

위의 원칙들을 보면, 이것들은 전형적인 데이터 엔지니어 직무 설명에서 보는 것과 매우 다릅니다 (우리가 고민하는 이유가 분명합니다).

두 번째 아이디어는 데이터를 다른 사람에게 제공하는 서비스로 보는 대신에 제품으로 취급하는 것입니다. 이것은 Moody's나 Factset과 같은 기업에게는 훨씬 더 많은 의미를 갖게 됩니다. 그들의 시장은 데이터를 판매하는 것이기 때문에 이것은 훨씬 더 명확합니다.

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

거의 모든 기업이 데이터 기업이 되고 있다는 말에 흥분을 느낄 수 있지만, 대부분의 경우에는 현실적이지 않거나 지속할 수 없는 것입니다. 따라서 더 큰 데이터 제품 흥행은 내부 데이터 제품에 집중되어 있거나 비즈니스의 다른 이해 관계자들을 위해 데이터 엔지니어링 팀이 하는 일에 대해 생각하는 것입니다.

이에 대해 중점을 두게 될 것입니다.

# 내부 데이터 제품의 측면

## 제품으로서의 데이터를 고려하기 전에

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

- 데이터 팀은 서비스 데스크로 운영되며 요청을 수락합니다.
- 데이터 팀이 제공하는 것: 테이블, 대시보드, 가끔 예측 모델
- 데이터 팀이 하지 않는 것: 비즈니스와 논의하는 일

## 데이터를 제품으로 보고나서

- 데이터 팀은 이해관계자들과 적 pressing한 비즈니스 문제를 해결하기 위해 미리 소통합니다.
- 데이터 팀은 비즈니스 이해관계자와 함께 끝단 요구사항을 이루고자 하는 시각에서 이해합니다(데이터 테이블? 대시보드? 다른 것? 조합?).
- 데이터 팀은 데이터 자산 이상을 만들어내며, 내부 마케팅 활동에 참여하고, 교육 세션을 운영하며, 문서 작성하고, 경보를 활성화하며, 설명 영상을 제작합니다 등.
- 데이터 팀은 데이터 제품의 성능을 모니터링하고 이를 우선순위 설정에 활용합니다.

이것이 잠재적으로 흥미로운 데이터 제품의 "작업 방식" 변화입니다. 자원소모가 더 많을 수 있지만, 더 적으면 더 나은 것이고, 자가 서비스 분석이 신화라면, 이 접근 방식은 많은 데이터 팀에게 더 나은 옵션일 수 있습니다.

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

# 데이터 제품 예시

이것을 예시로 살펴봅시다.

내부 소프트웨어 제품의 사용량을 보여주는 대시보드가 데이터 제품의 한 예입니다.

- 최종 사용자: 제품 팀
- 문제: 로드맵 결정을 위한 사용량 트렌드 이해 및 오류 디버깅
- 해결책: 관련 정보가 담긴 대시보드, 그러나 특정 오류에 대한 관련 이해 관계자에 대한 자동 슬랙 알림도 함께 제공. 누가 무엇을 제어하는지에 관한 Confluence 문서화
- 추가 기능: 전송된 알림 수, 대시보드 사용량, 및 파이프라인 비용 추적

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

오케스트라에서는 "데이터 제품이란 무엇을 의미하는가"에 대한 의미에서, 우리가 이 중 하나를 가지고 있으며, 바로 위에 설명된 것과 똑같습니다.

우리에게는 대시보드가 있습니다:

![대시보드 이미지](/assets/img/2024-05-18-HowtothinkaboutInternalDataProductsasaDataEngineer_0.png)

의사 결정이 필요할 때 이해관계자들을 알리기 위한 알림이 있습니다:

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

`<img src="/assets/img/2024-05-18-HowtothinkaboutInternalDataProductsasaDataEngineer_1.png" />`

내부 데이터 제품에 대한 생각 방식으로써 데이터 엔지니어링자로서 어떻게 고려해야 하는지에 대한 내용입니다.

그리고 데이터 제품의 사용은 면밀히 모니터링됩니다:

`<img src="/assets/img/2024-05-18-HowtothinkaboutInternalDataProductsasaDataEngineer_2.png" />`

이러한 기능 집합과 서비스 데스크 스타일 메인에서 변경되는 프로세스는 데이터 제품 주도 접근 방식과 표준 데이터 기능 접근 방식의 차이입니다.

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

# 데이터 제품을 만드는 방법은 무엇인가요?

뭐든 많은 일처럼 보이죠? 결국 아무도와 대화하지 않고 문서화되지 않은 테이블을 작성하는 것이 위의 모든 것보다 훨씬 빠를 것입니다!

하지만 시작할 때는 덜이 더 좋습니다. 데이터 제품 후보를 고를 때 사용하는 기준은 다음과 같습니다.

## 고가치

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

고가치 사용 사례를 선택하세요. 이상적으로 첫 번째 데이터 제품은 중요한 사람에게 중요한 기능을 제공하거나 중요한 문제를 해결해야 합니다. 초기 단계부터 데이터 제품의 가치를 증명하는 데 중요합니다.

## 간편함

구현하기 쉬운 파이프라인을 선택해 보세요. 이곳에서 하지 말아야 할 좋지 않은 예는 마케팅 프로젝트입니다. 마케팅 데이터는 알려진 바와 같이 복잡하며 다양한 곳에서 가져옵니다. 마케팅 ROI를 계산하는 것은 간단하지 않습니다.

더 나은 예는 간단한 제품 사용 대시보드나 영업팀을 위한 지능형 분석이 될 수 있습니다. 후자의 예에서는 일반적으로 소수의 소스에서 데이터를 추출하여 내부 데이터 제품을 구축하는 원형 과정을 단순화할 수 있습니다.

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

## 격리

이것은 단순성과 얽혀 있지만, 매우 목표를 정확히 설정하거나 독립적인 사용 사례를 선택하면 데이터 제품을 다른 파이프라인과 독립적으로 제공할 수 있습니다. 이렇게 하면 종속성이 적어지므로 속도가 높아집니다. 예를 들어, 특정하지만 중요한 데이터가 될 수 있습니다.

## 친숙함

이상적으로는, 사람들이 익숙한 파이프라인을 선택해야 합니다. 이렇게 하면 결과물에 대한 이해도가 높으며 기대하는 이해 관계자가 무엇을 기대하는지에 대한 확신이 생깁니다. 이렇게 하면 더 빨리 제공할 수 있습니다(무엇을 제공하는지 알기 때문에)만 일반적인 가치를 빠르게 추가할 수도 있습니다(가치 있는 것으로 이미 알고 있는 것을 보강하게 됩니다). 다른 한편으로는, 완전히 새로운 일을 하지 않도록 노력해보세요.

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

## 참여 독려

비즈니스 다른 곳에서 어떤 지지를 받고 있는 것을 선택하는 것이 좋습니다. 데이터 제품의 수용률을 향상시키는 것 뿐만 아니라 더 접근 가능한 소스로부터 피드백을 수집하는 데도 도움이 됩니다. "데이터 제품을 공개하자" 라는 것이 마침내 실망스러운 피드백만 받아들이고 대상 시청자로부터 신뢰를 잃는 것을 원하지 않을 것입니다.

## 생성적 인공지능 및 AI 제품에 대한 참고

생성적 인공지능이 이 구조에 매우 잘 맞는 것을 알 수 있을 것입니다.

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

AI Chatbot와 같은 제품은 데이터 파이프라인을 통해 활성화됩니다. 비구조적 데이터를 수집하고 Generative AI를 사용하여 정리한 다음 이를 임베딩으로 변환할 수 있습니다.

이러한 임베딩은 ChatBot에서 사용됩니다.

전체적으로 Generative AI 제품은 데이터 파이프라인과 Chatbot으로 구성됩니다. 이 경우 사용량 모니터링은 ChatBot이 상호 작용하는 정도를 확인해야 하기 때문에 조금 더 복잡하지만 데이터 파이프라인의 비용 요소를 모니터링하는 것은 거의 동일합니다!

# 결론

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

이 기사에서는 데이터 제품 주변에 왜 관심이 쏠렸는지와 그 의미에 대해 논의했습니다. 모든 기업이 데이터 기업으로 발전하는 것이 아니라 데이터 팀이 비즈니스 이해관계자를 위해 어떻게 일하는지에 초점을 맞추었습니다.

우리는 데이터 제품을 만드는 것이 현재의 작업 방식에서 벗어난 것으로 보인다는 것을 알았습니다. 이는 좀 더 서비스 중심적인 방식입니다. 데이터 제품 마인드셋으로 전환하는 것은 "문제 해결 티켓을 완료하기"에 더 많은 노력이 필요하지만, 이는 더 나은 최종 제품을 만들어내며 데이터로부터 더 큰 가치를 창출할 수 있게 됩니다.

마지막으로, 갈등을 선택하는 것이 중요하며 단순함, 참여, 격리 정도 및 친숙함과 같은 요소가 데이터 제품의 성공에 영향을 줄 수 있다는 것을 알게 되었습니다.

더 자세히 이야기하거나 생각을 나누고 싶다면 언제든지 알려주세요! DM은 열려 있어요 🌲

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

# 오케스트라에 대해 더 알아보기

오케스트라는 데이터를 최대한 인간적으로 가치를 뽑아내는 플랫폼입니다. 또한 다양한 사용 사례와 솔루션에 대한 해결책을 제공하는 기능이 풍부한 조정 도구입니다. [우리의 문서](https://example.com)를 확인해보세요. 그리고 우리의 통합을 확인해보세요 - 우리가 관리하기 때문에 여러분은 파이프라인을 즉시 시작할 수 있습니다. 저희는 오케스트라 팀과 게스트 작가들이 함께 쓴 [블로그](https://example.com)와 조금 더 심도 있는 내용을 다룬 [화이트페이퍼](https://example.com)도 갖추고 있습니다.
