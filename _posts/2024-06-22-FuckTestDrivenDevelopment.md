---
title: "테스트 주도 개발TDD을 피해야 하는 이유"
description: ""
coverImage: "/assets/img/2024-06-22-FuckTestDrivenDevelopment_0.png"
date: 2024-06-22 23:49
ogImage:
  url: /assets/img/2024-06-22-FuckTestDrivenDevelopment_0.png
tag: Tech
originalTitle: "Fuck Test Driven Development"
link: "https://medium.com/@efsunengine/fuck-test-driven-development-a6ad9be895c9"
---

![이미지](/assets/img/2024-06-22-FuckTestDrivenDevelopment_0.png)

안녕하세요!

저희는 현재 브랜딩 전략을 개발 중에 있으며 곧 자세한 정보를 공유할 예정입니다. 현재 Efsun은 포괄적인 게임 엔진 플랫폼으로, 우리는 이후 Efsun 이름 아래에서 기사를 발표하고자 합니다. 여기 TDD에 관한 새로운 기사가 있습니다. 오래 여정을 준비해주세요.

# 서식지

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

제 기사에서는 역사, 사회학, 심리학 등과 같은 학문에 크게 의존하고 있어요. 소프트웨어 분야에 깊이 몰두하고 있지만, 인간의 영향력이 여전히 중요합니다. 인공지능이 모든 것을 독점하지는 못하기 때문이죠. 코딩과 기술에 종사하는 사람들은 역사, 사회학, 심리학과 같은 보다 광범위한 요인들에 불가피하게 영향을 받고 있어요.

결국, 모든 현상을 기술적이거나 방법론적인 시각만으로 이해할 수 없어요. 이에 따라, 저는 TDD(테스트 주도 개발) 패러다임을 비판하거나 검토함으로써 특히 문제가 있다고 생각되는 사례를 강조하겠습니다. 또한, 제 사회학적인 통찰력이 다양한 분야에 적용될 수 있다고 주장하고 싶어요.

# 무책임함

저는 사람들을 관찰하는 것에 깊은 매혹을 느낍니다. 이는 동시에 즐겁고 매우 깨우침을 줍니다. 저는 인류의 가장 강력한 중독은 헤로인이 아니라 오히려 선뜻 따르고 순종하며 믿음에 기대하는 성향이라고 믿어요. 사람들은 어떤 물질보다 의심하지 않는 순종과 믿음에 더 중독되어 있는 것처럼 보입니다. 이 무조건적인 순종의 근원적인 이유는 놀랍게도 간단해요: “무책임함.”

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

부주의는 모든 계층과 분야의 개인들에게 궁극적인 피난처이자 탈출이죠. 인간의 자부심이 직접적으로 받아들이지 못하게 하지만, 문제없는 복종과 믿음으로부터 오는 책임과 양심에서의 해방만큼 자유로운 약이 존재하지 않습니다.

한번 잠시 멈추어서 생각해보세요. 회사의 업무를 계속해서 완전한 책임을 지는 것이 더 직접적이고 선호되는 방식일까요, 아니면 그 책임을 매니저나 누군가에게 넘기는 것이 낫을까요?

성공할 때, 대부분의 직원들은 대개 "상사가 모든 단계에 대해 지시했기 때문에 칭찬은 그 들에게, 나에게는 아닌 것"이라고 말하지 않습니다. 그러나 실패나 문제가 발생할 때, 대부분의 직원들은 "상사가 나에게 그렇게 하라고 했기 때문에 책임은 그들이 져야 한다"고 주장하는 경향이 있습니다. 네, 친애하는 독자여, 솔직해지죠, 아마 당신도 이와 같을 것입니다.

"상사가 지시한 대로 했을 뿐이에요"라고 생각하면 마음이 편안해지기도 했나요? 어려운 순간에는 종종 책임을 다른 사람에게 전가하는 게 더 쉬운 법이지요. 많은 사람들이 누군가에게 책임을 맡기는 데 대한 안도를 경험해본 적이 있습니다. 솔직히 말해서, 그 느낌이 환상적이었죠.

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

종교들이 메시아, 마다이, 그리고 성인 등의 구원자 인물을 가지는 이유는 우리가 용기와 인내심이 부족할 때 다른 누군가가 부담을 지도록 바라기 때문입니다. 이 경향은 다양한 종교에서 구원자 인물들이 나타나는 것을 통해 볼 수 있으며, 우리들이 어려운 업무를 처리할 수 있는 타인을 찾으려는 성향을 대변합니다.

당신이 무언가를 개발하고, 코드를 작성하고, 실패한다면 누군가가 그 책임을 짊어지고 결과에 직면합니다. 정말 놀라운 순환 구조가 아닌가요?

우리는 종종 주장합니다. "코딩 대신 이메일을 작성하는 것"이거나, "바쁘게 이메일을 보내느라 코딩할 시간이 없다"는 것입니다. 간단히 말해, 대부분의 이메일은 책임을 피하기 위한 수단에 불과합니다. 아무도 "이 작업을 완료하겠습니다"라고 말하려고 하지 않아서, 우리는 책임을 회피합니다.

# The Poison

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

전문가 세계에서 약 23년을 보낸 경험이 있습니다. 20년 이상 소프트웨어 또는 IT 작업으로 수입을 올린 적이 있어요. 제 여정은 최악의 상사가 있는 회사, 가장 흥미로운 스타트업, 가장 명문적인 기업 환경을 경험했어요.

그래서 거의 모든 수준에서 독소를 만났고 여러 가지 쓴 경험을 했어요. 소프트웨어 산업에서 모든 고통 속에서 특히 치명적인 두 가지 독약을 식별했어요:

- 근본적인 문제 대안보다 빠른 해결책을 선택하고 책임을 식별하는 것.
- 방법론을 코드와 코드 품질의 세부사항보다 더 중요하게 여기는 것.

# Test-Driven Development Creed

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

이 멘토들은 저가 존경하며, 하지만 절대로 그들이나 다른 누군가의 신념이나 말을 맹목적으로 받아들이지 않습니다.

종교와 분파의 공통 특성은 검토와 비판에 대한 저항이라고 볼 수 있습니다. 그래서 제가 TDD를 사이비 종교나 종교로 특징지어 보는 겁니다. 그들은 교리주의적이고 정확하며 무오류로 간주됩니다. 이 시점에서 Google과 ChatGPT를 통해 TDD를 탐험해보겠습니다. 두 플랫폼에 동일한 질문을 해주세요.

제가 Opera 브라우저를 통해 이 질문들을 제기했는데, 개인 탭과 VPN을 사용했습니다. 가능한 한 브라우저 히스토리가 결과에 미치는 영향을 최소화하기 위해 노력했습니다. (원하시는 설정을 사용하셔도 됩니다.)

- Test-Driven 개발의 비용은 무엇입니까?
- Test Driven Development의 단점은 무엇입니까?
- Test Driven Development 비용은?
- Test Driven Development의 단점은?

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

결과는 Google과 ChatGPT가 TDD에 대해 과도하게 부정적인 응답을 제공하는 것을 피하는 것을 보여줍니다. 그들은 약점을 강조할 때에도 그것을 장점과 균형있게 조화시키려고 노력합니다. 부정적인 측면이나 비판은 일반적으로 완화되거나 최소화됩니다.

# 테스트 주도 개발의 실제 비용

내 관측에서는 이 문제에 대한 실질적인 연구를 만나지 못했습니다. 이 주제에 대한 실질적인 연구가 부족합니다. 몇몇 기사는 TDD가 개발 시간을 13-30% 증가시킨다고 제안합니다. 이런 경우에 대부분 최악의 경우 시나리오를 생각하며, 대략 30%로 고려합니다. 그럼에도 불구하고, TDD에 필요한 추가 시간은 훨씬 더 높을 것으로 믿어져서, 60%를 초과할 수도 있습니다. TDD 유지 관리 단계를 계산하는 것은 거의 불가능하기 때문입니다.

이 관점을 더 탐구하되, TDD의 심층 기술적 측면이 아닌 개발 주기에 초점을 맞추어보겠습니다.

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

![Test Driven Development](/assets/img/2024-06-22-FuckTestDrivenDevelopment_1.png)

프로젝트 내에서 테스트할 클래스와 함수들이 결정되며, 이를 위한 원자 단위 테스트가 작성됩니다.

- 함수 성공과 유닛 테스트에 관한 사항

- 함수는 유닛 테스트를 통과하면 성공으로 간주됩니다.
- 함수가 유당 테스트를 통과하지 못하면, 밑바닥의 이슈나 버그를 해결하기 위해 대응하도록 유도됩니다.
- 유당 테스트는 함수의 성공을 검증하며, 어떤 실패도 버그 해결과 재테스트의 필요성을 나타냅니다.

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

2. TDD에서 'Refactoring'으로 버그 해결하는 것은 종종 'Refactoring'이라고 합니다만, 이 용어를 혼동하지 않는 것이 중요합니다.

- TDD 주기에서 버그 해결은 'Refactoring'으로 분류되지만, 이는 전통적인 버그 해결 방법과 구분됩니다.
- TDD 과정은 버그 해결을 'Refactoring'으로 레이블링하지만, 이는 일반적인 버그 해결 방법과 다릅니다.

3. 버그 해결과 리팩터링 구분하기

- TDD의 'Refactoring' 단계를 통해 버그를 해결하는 것은 표준적인 버그 해결과 동일하지 않습니다.
- TDD의 버그 수정과 실제 리팩터링 활동을 구별하는 것이 중요합니다.
- TDD에서 'Refactoring'의 일환으로 버그를 해결하는 것은 전통적인 버그 해결 방법과 동의어가 아닙니다.

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

그래서 TDD 리팩터링과 더 리팩터링은 꽤 다른 전략입니다. TDD를 간단히 설명하자면 리팩터링은 버그를 해결하는 것뿐이에요.

질문들

- TDD를 구현하면 버그 없는 프로젝트를 보장할까요? 그렇지 않을 때는 어떻게 대응하나요?
- TDD를 사용하는 프로젝트에서 버그를 찾는 것은 모순적이지 않나요?
- TDD 원칙을 준수하는 프로젝트에서도 어떻게 버그가 발생할 수 있나요?
- 프로젝트에서 TDD를 사용할 때 UAT 테스트에서 발견된 버그를 식별하고 해결하는 방법은 무엇인가요?
- TDD를 사용해도 최종 제품에서 최종 사용자가 버그를 발견하는 것은 정상인가요?
- TDD를 적용한 프로젝트에서 최종 사용자가 만난 버그를 잡고 해결하기 위해 어떤 단계를 거쳐야 할까요?

위 질문들을 확장하고 다양화할 수 있지만, TDD가 충분히 답하지는 못할 수도 있어요. TDD는 이상적인 실험실 환경, 특정 매개변수, 알려진 계산 및 예측 가능한 반환 값으로 작동한다는 가정하에 운영됩니다.

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

결과적으로 작성된 테스트는 오직 이 이상적인 시나리오만 다룹니다. 하지만 사용자나 테스터들이 예기치 못한 동작을 수행하는 현실 세계에서는, TDD로 개발된 많은 함수들이 기대와 다르게 동작하지 않거나 에러를 발생시킬 수 있습니다. 다시 말해, 사용자나 테스터가 예기치 않은 동작을 수행할 때, 많은 TDD 적용 함수들이 기대대로 동작하지 않거나 에러를 발생할 수 있습니다.

고등학교 화학에서 우리는 일반적인 조건과 이상적인 조건이 다르다는 것을 알고 있습니다. 예를 들어, 어떤 자동차 제조사는 그들의 T 모델이 100 km당 5리터를 사용한다고 명시할지라도, 이것은 제조사에 의해 설정된 이상적인 조건에 기반한 것이며, 90 km/h의 일정 속도를 의미합니다.

실제 세계에서는 다양한 조건으로 인해 결과가 다르게 나타나며, 심지어 반복적인 테스트도 변동성을 보일 것입니다. 따라서, 이 차이점은 이상적인 조건과 일반적인 조건 간의 차이를 강조하며, TDD가 비이상적인 조건에서 기능적이고 효과적이지 않을 수 있다는 것을 나타냅니다.

복잡하거나 긴 예시 대신, 제품을 조회하는 간단한 UI - API Backend - Database 흐름을 검토해보겠습니다. 어떤 이유로 제품에 연속적인 ID를 할당하고 이 ID를 쿼리에서 사용합니다. 사용된 기술 스택은 다음과 같습니다:

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

- **UI** – JavaScript
- **API Backend I** – JavaScript
- **API Backend II** – Java
- **Database** – MongoDB

**Query Flow** – UI – API Backend I – API Backend II – Database

JavaScript은 타입 안전하지 않은 언어입니다. 데이터 형식에 관계없이 실행하고 작업을 수행하려고 합니다.

Java는 타입 안전한 언어로 데이터 형식에 기반하여 작동합니다.

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

MongoDB은 NoSQL 데이터베이스이며, 순차 ID가 숫자로 정의되어 있을 때 형식 검사가 항상 보장되지는 않습니다. 따라서 소프트웨어 개발자는 형식 검사를 관리하기 위한 조치를 취해야 합니다.

### 테스트 유형

- 단위 테스트
- 통합 테스트 (INT)
- 사용자 수용 테스트 (UAT)
- 사용자 경험 테스트 (최종 또는 실제 테스트)

# 단위 테스트의 다양한 측면

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

제품 쿼리 플로우를 검토해 볼까요? 각 쿼리 단계에 대해 요구 사항에 따라 하나 이상의 함수를 만들 수 있습니다. 우리는 몇 가지를 만들었어요.

- UI에서 사용자가 제품 ID를 입력할 수 있는 폼이 생성됩니다.
- 백엔드 I가 UI에서 요청을 처리하고 편집한 후 백엔드 II로 전달됩니다.
- 백엔드 II가 백엔드 I로부터의 요청을 처리하고 데이터베이스로 전송하여 결과를 검색한 후 백엔드 I로 반환합니다.
- 백엔드 I가 백엔드 II의 결과를 처리하고 UI로 전송합니다.
- UI가 사용자에게 결과를 표시합니다.

제품 쿼리 테스트 플로우를 검토해 볼까요? 이 테스트는 단위 테스트로 간주되며, 서로 독립적으로 작성하고 실행해야 합니다. 이러한 테스트에는 유형 또는 Null 체크 등이 포함될 수 있습니다.

- UI: 폼의 필드를 유효성 검사하는 일부 단위 테스트가 포함된 UI가 작성됩니다.
- 백엔드: UI에서 요청을 처리하는 백엔드 함수에 대한 단위 테스트가 작성됩니다.
- 데이터베이스: 데이터베이스 필드와 사양의 무결성을 확인하는 단위 테스트를 작성할 수 있습니다.

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

모든 이 테스트들은 서로 독립적입니다. 또한 UI -` 백앤드 I -` 백앤드 II -` DB에 걸쳐야 하는 통합 테스트도 작성해야 합니다. TDD에 열정적이고 주석을 달거나, 로깅하거나, 디버깅하는 것을 피하려는 사람들을 위해 이후에 이에 대해 다루겠습니다.

이상적인 TDD 시나리오에서, 테스트 대 코드의 비율은 극단적일 수 있습니다. 1 대 3 또는 1 대 4처럼 말이죠. 만약 한 줄의 코드를 작성하고, 버그 없이 작동하는 것을 세 줄의 테스트 코드로 확인할 수 있다면, 분명 TDD의 열렬한 지지자가 될 것입니다.

"유닛 테스트의 다채로움"을 다시 살펴봅시다. 이 ID 필드를 대상으로 설계된 테스트에서 어떤 일들이 벌어질 수 있을까요?

- Null 값 확인
- 숫자 유효성 검사
- 알파벳 및 숫자 조합 유효성 검사
- 길이 확인

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

UI: 자바스크립트로 코딩되어 있기 때문에 이러한 검증 사항들을 세심하게 설정하고 테스트해야 합니다. 어느 하나도 놓치거나 건너뛸 여유가 없기 때문에 모든 검증을 해야 합니다. 1개의 필드 → 4가지의 검증 → 4개의 단위 테스트로 생각해 보세요.

백엔드: UI가 REST 요청을 통해 백엔드와 상호 작용하는데, 이것을 1개의 필드 당 1개의 API → 1개의 JSON 파서 → 4가지의 검증 → 5개의 단위 테스트로 개념화할 수 있습니다.

데이터베이스: 1개의 영역 → 적어도 4개의 단위 테스트

이러한 검증 사항들을 하나의 단위 테스트에 포함할 수도 있고, 여러 개의 테스트를 작성할 수도 있습니다. 단일 책임 원칙을 자랑스럽게 지키기 위해 TDD 추종자들은 여러 개의 테스트를 옹호할 것입니다. 😊

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

# 문제 발생

이상적인 상황에서 TDD는 마치 원더랜드 같을 수 있어요. 하지만 현실 세계의 복잡성은 더 많은 주의를 요구합니다. 앞서 언급한 모든 점검 사항들을 잊게 되면 광범위한 부정적 영향이나 실패로 이어질 수 있어요. 이런 상황에 직면할 때 TDD의 신뢰성은 매우 중요해집니다. 우리는 예기치 않은 것에 대해 자세히 살펴보겠습니다.

모든 필요한 점검을 완료하고 성공적인 테스트 결과를 달성한 후, JavaScript와 웹 브라우저의 데이터 무결성에 관한 역사적인 신뢰성 부족에 주목하는 것이 중요합니다. JavaScript와 웹 브라우저는 데이터 무결성을 유지하는 데 일관성이 부족하다는 것으로 잘 알려져 있어요.

- 브라우저 오류가 발생하는 경우를 가정해 보겠습니다: 사용자가 제품 검색 필드에 숫자가 아닌 문자를 동시에 입력하여 조회를 시작할 수 있어요. 브라우저는 이 작업을 제한 없이 허용할 수 있습니다.
- 악의적인 사용자가 API를 중간에 가로채서 제품 쿼리에 예상치 못한 문자를 삽입할 수 있어요.
- UI와 백엔드 API에 대해 서로 다른 개발 노력이 진행되어 프로젝트 전반에 일관적으로 이러한 제어가 구현되지 않을 수 있습니다.

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

UI에서 잘못된 ID를 받았고 API-I에서 숫자-알파벳 검사를 놓친 경우 또는 테스트하지 않은 경우, TDD 또는 당신이 취할 행동은 무엇인가요?

- 백엔드 API-I는 JavaScript로 구성되어 있어서 타입 안전을 강제하지 않고 요청을 그 규칙에 따라 처리한 후 API-II로 전달합니다.
- 일부러든 실수든 백엔드 API-2 내부의 오류 처리나 타입 체크에 문제가 발생하면 요청이 여전히 데이터베이스로 전달됩니다.
- 데이터베이스 요청이 단순한 get/select 작업임을 고려하면, 데이터베이스는 쿼리와 일치하는 제품을 찾을 수 없다는 응답을 반환할 가능성이 높습니다. 오류를 throw하지 않습니다.

이러한 과정에서 오류나 이상이 없다면 사용자에게 제품이 없음을 알려줍니다. 그러나 이 요청이 어떤 이유로든 반복되거나 프로세스가 제대로 작동하지 않으면 사용자는 제품을 항상 찾을 수 없을 수 있습니다.

심각한 문제는 사용자/소비자가 다른 API를 사용하는 경우 요청이 끊임없이 반복될 수 있다는 것입니다. 그 API는 올바른 응답을 받을 때까지 서비스를 계속 시도할 수 있습니다. TDD는 이러한 시나리오에 어떻게 대응할까요? (이러한 시나리오는 곱셈될 수 있습니다.)

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

예기치 못한 상황에서 API와 TDD는 모두 마비 상태에 놓일 수 있습니다. TDD가 의인화된다면, 그것은 움직이지 않는 물고기와 같을 것이며 대응할 수 없을 겁니다. 프로젝트 구성 요소 전체의 개발자들은 모든 단위 테스트가 성공적으로 실행되었고 이전에 이러한 문제를 만나지 않았다고 주장할 수 있으며, "내 컴퓨터에서는 작동하는데" 증후군으로 이어집니다.

작동 중에 오류를 감지 못할 경우 문제의 흔적이나 실마리가 없어서 새로운 테스트 시나리오를 개발하고 실행하기 불가능합니다. 그런데, TDD의 신성하고 변혁적인 힘이 어디에 있을까요?

# 진정한 영웅 — 디버그와 로그

이러한 상황에서 프로젝트 테스터 또는 모든 개발자들은 힘을 모아 시나리오를 재현하고 전체 프로젝트를 세심하게 디버깅하고 중요한 위치에 임시 로그를 남겨야 합니다. 디버깅에 대해 주저하는 사람이나 가치를 부정하는 사람들에게: 디버깅은 반드시 필요합니다. 디버깅을 싫어하거나 디버깅이 쓸모없다고 말하는 사람들에게는, 디버깅이 당신의 장례식을 치를 것임을 확신하세요.

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

만약 우리가 주요한 실패나 큰 실수에 대해 이야기한다면, 그것은 결함이 있는 이러한 시나리오의 반복입니다. 대부분의 경우, 이러한 결함이 있는 시나리오를 반복할 수 없습니다. 숙련된 개인이나 광범위한 프로젝트에 깊이 참여한 사람들은 사용자가 마주한 동일한 오류를 잡고 반복하는 것이 종종 오류 자체를 해결하는 것보다 더 어려울 수 있다는 것을 알고 있습니다.

이러한 예측할 수 없는 버그를 포착하는 가장 효과적인 방법은 절망적인 테스트 작성이나 끝없는 테스트 실행이 아니라 견고한 오류 처리 및 로깅을 구현하는 것입니다. 이렇게 함으로써 발생한 오류를 신속하게 식별하고 기록할 수 있습니다.

많은 개발자들과 협업할 때 로깅을 옹호할 때 종종 저항을 마주합니다. 그들은 마치 나를 악마로 보는 듯 합니다. 로깅을 코드에 불필요한 부담으로 여기는 경향이 있습니다. 그리고 일부 개발자는 로깅을 코드 작업의 불필요한 복잡성으로 여기기도 합니다. 그들에 따르면 프로그래밍 언어나 응용 프로그램 서버가 로깅을 충분히 처리하므로 개발자들이 로깅에 시간을 투자할 필요가 없다고 합니다. 이러한 편견 있는 개발자 유형은 TDD나 Agile Development와 같은 종교가 등장한 후에 매우 흔해졌습니다.

# 기억하기

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

저의 개인 경험을 공유하고 싶습니다. 제 목표는 제 행동을 자랑하거나 특정 회사나 팀을 탓하는 것이 아닙니다. 대신 대규모 프로젝트와 팀의 맥락 속에서 발생한 작은 실수들이 어떻게 크고 해결하기 어려운 문제로 번질 수 있는지를 강조하고 싶습니다.

몇 년 전, 저는 터키에 있는 유럽 최대의 결제 서브시스템 개발자 회사의 R&D 사무실에서 잠시 일했습니다. 그 경험이 짧았지만 여전히 지속적인 인상을 남겼습니다. 그 회사의 주요 결제 인프라는 약 20만 줄의 코드를 포함했습니다. 저의 임기 중에는 구조적인 실수로 인해 다양한 은행을 위한 맞춤화가 코드 복제로 이어져 코드베이스가 80만 줄로 확장되었습니다. 저는 그 회사의 규모를 강조하고 코드 저장소의 복잡성을 강조하고 싶었습니다.

처음에 저는 약 20만 줄의 코드로 이루어진 다른 프로젝트를 관리하도록 지정되었는데, 이게 제 첫 만남이었습니다. 새로운 직장에서는 보통 처음 3~4개월 동안 모든 것과 모든 사람을 관찰하고 듣는 것을 선호합니다. 모든 측면에 곧바로 뛰어들거나 즉시 모든 일에 참여하는 것을 삼가고 있죠.

저는 합류했을 때 이미 제 팀이 심각한 문제에 직면하고 있었습니다. 첫날에 동료들이 그 문제에 대해 화이트보드에서 토의하고 있는 상황이었습니다. 테스트와 디버깅을 45~50일 동안 진행한 후에 팀은 "저희는 철저히 테스트하고 디버깅했지만 고객이 오류를 경험했던 시나리오를 재현할 수 없어 문제를 정확히 파악하고 해결할 수가 없습니다." 라며 한탄하고 있었습니다. 동료들에게 몇 가지 질문을 하면서 함수의 작동 및 가능한 시나리오에 중점을 두었습니다. 그들의 설명으로 문제의 근원이 우리 함수에 있지 않다고 확신하였습니다.

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

이 API를 사용하고 있다고 상상해봐요. 60일 후에 문제가 우리 쪽이 아닌 당신 쪽에서 발생했다고 말한다면 나를 대신해 꽤 좌절스러울 거 같아요. 60일이나 걸려 버그가 우리 것인 걸 발견하는 데, 만약 버그가 당신 것이었다면 360일이 걸렸을 것이고, 내 칭찬의 끝은 상당히 어둡지 않았을까 생각해봐요.

상황을 뒤집어 생각하고, 당신이 그 회사의 소유주이거나 매니저라고 상상해봐요. 한 프로젝트에서 미해결된 문제가 60일 동안 계속되고, 당신의 팀은 원인을 명확히 찾아내지 못했다. 고객들이나 최고 관리자들이 점점 스트레스를 받고 공격적이었죠. 그런데 새 팀원이 조인하여 콘솔 로그에 몇 줄을 추가하는 걸 제안하면서 상황이 바뀌었어요. 마지못해 당신의 팀이 이를 실행하자, 수 시간 내에 문제가 당신 것이 아닌 파트너의 외부 API로 인한 것임을 발견했죠.

이 경우에서 어떤 부분이 가장 좌절스럽거나 실망스러울까요?

- 1,440시간 동안 문제를 찾지 못했지만, 새로 온 누군가에 의해 4시간 만에 해결되었네요. 어떻게 그런 일이 일어났을까요?
- 1,440시간 동안 고객들과 다른 프로젝트 소유자들이 삶을 힘들게 했네요.
- 1,440시간이 지난 뒤 문제가 당신의 잘못이 아니었음을 깨달았을 때, 상당한 탓과 좌절을 느낄 수 있었네요.

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

# 다시 TDD

이 API는 이스탄불의 활발한 교통 수집 시스템을 관리하는 다섯 개 플랫폼 중 하나의 구성 요소일 뿐입니다. 다섯 개의 다른 기관이 통합 및 동기화된 응용 프로그램을 사용하여 결제 거래를 처리하고 있습니다. 빠르게 몇 가지 질문을 떠올려 봅시다.

- 5개의 플랫폼이 통합적으로 작동할 때 단위 테스트는 얼마나 중요한가?
- 하나가 제대로 사용하지 않으면 모든 것이 망가질까요?
- 이 혼돈을 어떻게 관리할까요? 혹시 누가 그런 역할을 맡을 수 있을까요?
- 소프트웨어 신뢰성과 품질 측면에서, Agile, 워터폴 또는 다른 방법론을 사용하더라도 통합 및 UAT 테스트가 철저하지 않으면 중요한가요?
- 통합 및 UAT 테스트가 철저하지 않으면 프로그래밍 언어와 디자인 패턴이 중요할까요?
- 생산 환경에서 오류를 발견하는 데 몇 주가 걸리는데, 위 세 가지를 신경 쓰는 것이 의미가 있을까요?

가장 중요한 질문은 다음과 같습니다: 우리는 TDD를 사용하여 이상적인 조건을 철저히 테스트하고 프로젝트를 성공적으로 배포했습니다. 그런데 간단한 오류나 복잡한 오류를 포착하고 관리할 수 있는 메커니즘이 없습니다. 우리의 응용 프로그램 또는 API가 다양한 사용자나 플랫폼에 의해 다양한 방식으로 액세스되는 상황에서 이러한 신흥 오류를 어떻게 해결할 것인가요? 오류 관리가 불가능하거나 하찮다면, 왜 TDD와 같은 신성한 방법론에 투자하는 건가요? 그냥 코딩하고 배포하면 되지 않나요?

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

# TDD에 대한 소강점, 결론

개별적으로 볼 때, 나는 TDD 패러다임에 대해 이의를 제기할 만한 것이 거의 없다고 생각한다. 그러나 광범위한 의미에서 보면, TDD를 완벽하고 틀림없는 해결책으로 다루는 것에 반대한다. 한 때 애자일 방법론과 마이크로서비스가 비슷한 방식으로 찬양받았던 적이 있었다.

예를 들어 제시된 예시처럼 유일하고 반복할 수 없는 상황에서는 TDD나 기타 패키지 솔루션 방법론이 비효과적이거나 마비 상태에 빠질 수 있다. 대규모 프로젝트에서 개발자들이 마주하는 오류 중 적어도 15~20%는 이러한 반복할 수 없는 유형이라는 것을 명심해야 한다.

이러한 오류는 귀여운 TDD 논리로는 잡을 수 없다. 별도의 테스트를 개발하거나 특별한 테스트 팀을 구성해도, 이러한 오류를 찾고 상황을 재현할 확률은 매우 낮다. 특히 현재의 마이크로서비스 열풍 속에서는 거의 불가능하다.

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

상상해보세요. 수천 개의 서비스가 있고 서로 호출하고 있는데, 그 중 하나가 모든 것을 호출하고 모든 것이 한 가지를 호출하는 상황을 말해보세요. 천 개의 서비스가 서로 복잡하게 상호작용하면 인프라가 쉽게 혼란스러운 막다른 골목이 될 수 있습니다.

예를 들어, 클라우드 플랫폼을 개발했다고 합시다. 핵심에는 "미디 서비스" 아키텍처 내에 1,000개가 넘는 엔드포인트를 갖는 30개 이상의 복제 가능한 API가 있습니다. 서비스가 어느 시점에서든 오류를 받으면, 오류의 심각성에 따라 즉시 경고가 생성되거나 로깅됩니다. 오류가 발생하면, 그 오류가 어떻게 발생했는지 묻지 않습니다. 우리는 로그에서 직접 흐름을 검토하고 오류 발생 시점, 오류 기능 및 원인을 찾습니다. 참고: 이 일을 JavaScript/Node.js로 수행하며 성능 손실이 전혀 없습니다. 몇몇 사람이 이해해줄 것 같아요.

잘 설계된 API, 효과적인 디버깅 및 포괄적인 로깅으로 과대포장된 방법론이나 과시된 방법론은 필요하지 않습니다. 사람들은 종종 "작은 프로젝트에는 TDD가 필요 없지만 큰 프로젝트에는 필수적이다"라고 주장합니다. 정말 그럴까요? 저는 이것이 업계에서 가장 큰 신화나 오해 중 하나라고 생각합니다. 그래서 여기 질문이 있습니다:

어떤 것이 더 현명한가요?

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

- TDD로 반복적인 코드 및 테스트 반복에 따른 비용이 발생합니다.
- 잘 설계된 오류 처리 및 로깅 메커니즘에 따른 비용이 발생합니다.

TDD의 유지보수가 가장 중요한 고려 사항 중 하나입니다. 의심스러운 분들께는 TDD로 유지, 업데이트 및 새로운 기능 추가하는 비용이 상당히 높을 수도 있다는 점을 발견할 것입니다. TDD의 결과물이 이러한 비용에 가치 있는지 평가하는 것이 중요합니다. 제가 말씀드리기를, 소프트웨어 개발의 계획 및 모니터링을 간소화하는 것이 매우 효과적이거나 기적적일 수 있습니다, 아마 무엇보다도요.

다음 에피소드에서는 로그 중심 개발이 무엇을 포함하며, 그 기능 및 한계에 대해 심도 있게 다룰 것입니다. 이 방법이 잘 알려진 간결함에도 불구하고 매우 강력할 수 있다는 점을 알아볼 것입니다.

행운을 빕니다,

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

Alper Akalın

연구원 & 엔지니어 @EfsunEngine
