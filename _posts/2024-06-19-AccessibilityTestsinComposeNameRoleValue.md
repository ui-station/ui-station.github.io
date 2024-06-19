---
title: "접근성 테스트에서 코틀린 Compose - 이름, 역할, 값"
description: ""
coverImage: "/assets/img/2024-06-19-AccessibilityTestsinComposeNameRoleValue_0.png"
date: 2024-06-19 13:42
ogImage: 
  url: /assets/img/2024-06-19-AccessibilityTestsinComposeNameRoleValue_0.png
tag: Tech
originalTitle: "Accessibility Tests in Compose — Name, Role, Value"
link: "https://medium.com/proandroiddev/accessibility-tests-in-compose-name-role-value-7fc70bfb674a"
---


<img src="/assets/img/2024-06-19-AccessibilityTestsinComposeNameRoleValue_0.png" />

앱을 위한 테스트를 작성할 때는 접근성 관련 사항을 테스트하는 것도 고려해야 합니다. 이해합니다. 어디서부터 시작해야 할지 알기 어려울 수 있죠. 그래서 저는 어떻게 일부 접근성 측면을 테스트할지에 대해 이 블로그 글을 쓰기로 결정했습니다.

이 글에서는 클릭 가능, 선택 가능, 토글 가능한 수정자를 사용하여 구성된 세 가지 사용자 정의 구성 요소에 대한 일부 접근성 관련 테스트를 추가할 것입니다. 이러한 구성 요소는 제가 썼던 블로그 글에서 만들어진 것입니다: Jetpack Compose에서 수정자를 활용한 Android 접근성 향상.

# 무엇을 테스트하는 중인가요?

<div class="content-ad"></div>

우리가 작성하는 테스트는 컴포넌트에 이름, 역할 및 값이 있는지 확인합니다. 하지만 이 그룹은 어디에서 왔을까요? 배경은 웹 콘텐츠 접근성 가이드라인(WCAG)이 "이름, 역할, 값"이라는 성공 기준을 가지고 있어요. 이는 모든 요소가 프로그래밍 방식으로 결정 가능한 이름과 역할을 가지고 있음을 보장합니다. 또한 사용자가 변경할 수 있는 상태, 속성 및 값은 프로그래밍 방식으로 변경 가능해야 합니다.

그리고 지금, "웹"이라는 것을 언급하는 이유가 궁금하다면, WCAG은 이름과는 상관없이 모바일 앱의 최소 접근성 수준을 결정하는 데도 사용됩니다.



여기서 '이름'은 이용자에게접근성을 제공하는 이름을 의미합니다. 요소의 텍스트 표현이 될 수 있습니다. 예를들어 버튼의 텍스트, 아이콘 버튼의 내용 설명, 스위치의 레이블 등이 있습니다. 스크린 리더를 사용하는 사람이 듣는 내용입니다. 음성 액세스 사용자는 대화형 요소를 활성화할 때 사용합니다.

그리고 '역할'은 요소의 역할을 말합니다. 예를들어 버튼일 수 있습니다 - 이것은 사용자에게 '여기 버튼이 있어요, 그리고 이 버튼은 버튼처럼 행동해야 해'라고 알려줍니다. 역할은 작동해야 하는 방식에 대한 약속입니다. 따라서 역할을 추가한다면 올바른 상호작용도 추가해야 합니다. 그러나 역할은 웹보다는 Android에서 덜 사용됩니다.

<div class="content-ad"></div>

값은 요소의 상태, 속성 또는 값에 대한 참조를 할 수 있습니다. 각 요소마다 정확한 의미가 다릅니다. 예를 들어, 체크박스의 경우 값은 체크 여부를 알려주고, 아코디언의 경우 상태가 열렸는지 닫혔는지를 나타냅니다.

다음 섹션에서는 소개 부분에서 언급된 몇 가지 사용자 지정 컴포넌트에 대한 "이름, 역할, 값" 성공 기준을 테스트하는 구체적인 예제를 살펴보겠습니다.

# 테스트 작성, 예시

처음에 언급한 대로, 이러한 테스트는 이전에 작성한 블로그 게시물을 위한 컴포넌트에 대해 작성된 것입니다. 세 가지 컴포넌트를 어떻게 테스트할지 살펴보겠습니다: 스위치, 라디오 버튼 그룹 및 클릭 가능한 행.

<div class="content-ad"></div>

블로그 게시물의 구성 요소가 예제를 위해 단순화되었기 때문에 이 테스트들도 간소화되었습니다. 실제 프로덕션 코드에서는 일반적으로 테스트되는 구성 요소를 찾는 더 정교한 전략이 있습니다.

## Toggleable

테스트하는 첫 번째 구성 요소는 아래 사진에 나와있는 스위치와 같습니다:

![Toggleable Component](/assets/img/2024-06-19-AccessibilityTestsinComposeNameRoleValue_1.png)

<div class="content-ad"></div>

우리는 세 가지를 테스트하고 싶어합니다: 먼저, 컴포넌트에 접근 가능한 이름(따라서, 스위치의 레이블)이 있는지 확인하고 싶습니다. 둘째, 역할이 올바른지-토글 가능한 컴포넌트여야 합니다. 셋째, 스위치를 토글한 후에도 값이 올바른지 확인하고 싶습니다, 따라서 스위치가 켜져 있는지 꺼져 있는지에 상관없이.

테스트를 작성해봅시다:

첫째, 테스트에는 설정이 필요하므로, composeTestRule 및 콘텐츠 설정과 같은 것들이 필요합니다. 그런 다음 테스트 가능한 컴포넌트를 test 태그인 accessible-toggle로 가져옵니다. 마지막으로, 이름, 역할, 값에 대한 테스트가 있습니다.

이름을 확인하는 테스트는 간단합니다: 요소의 텍스트 내용이 레이블의 단어와 같은지 확인하고 싶습니다. assertTextEquals를 사용하여 이를 확인할 수 있습니다. 역할을 테스트하기 위해 유용한 assert 함수인 assertIsToggleable을 사용할 수 있습니다. 마지막으로 값(즉, 확인된 상태)이 올바른지 확인하기 위해, assertIsOff 및 assertIsOn과 같은 유틸리티 함수를 사용할 수 있으며, 상태를 토글하기 위해 performClick을 사용할 수 있습니다.

<div class="content-ad"></div>

## 선택 가능한 요소

다음으로 테스트하는 컴포넌트는 라디오 버튼 그룹입니다. 사진에서 확인할 수 있습니다:

![이미지](/assets/img/2024-06-19-AccessibilityTestsinComposeNameRoleValue_2.png)

이 컴포넌트에서 우리는 두 옵션이 모두 이름(즉, "옵션 A"와 "옵션 B" 레이블), 선택 가능한 역할 및 선택된 항목의 값을 가지고 있는지 확인합니다.

<div class="content-ad"></div>

이 구성 요소의 테스트는 다음과 같습니다:

구조는 이전 테스트와 매우 유사합니다. 먼저 설정하고, 그런 다음 요소를 가져 와서 이름, 역할 및 값에 대해 단언합니다. 요소의 레이블(즉, 이름)을 확인하기 위해 동일한 assertTextEquals를 사용합니다. toggleable과 마찬가지로, 선택 가능한 요소의 역할과 값을 확인하는 함수가 있습니다: isSelectable(), assertIsSelected(), 그리고 .assertIsNotSelected().

## 클릭 가능

이 블로그 글의 마지막 맞춤형 구성 요소는 아이템을 즐겨찾기하는 데 사용할 수 있는 맞춤형 버튼입니다.

<div class="content-ad"></div>

```markdown
![Accessibility test](/assets/img/2024-06-19-AccessibilityTestsinComposeNameRoleValue_3.png)

우리는 아이템에 이름(즉, "이 항목 즐겨찾기")과 버튼 역할, 그리고 해당 항목이 즐겨찾기되었는지 여부를 알리는 상태가 있는지 확인하고 싶습니다.

다음 테스트는 다음을 보장합니다:

다시 말하지만, 이름의 설정과 확인은 다른 두 구성 요소와 유사합니다. 그러나 구성 요소가 버튼 역할을 갖고 있는지 확인하려면 SemanticsMatcher를 사용해야 합니다.
```

<div class="content-ad"></div>

`SemanticsMatcher`은 시맨틱 노드를 매칭하기 위한 래퍼입니다. 요소의 시맨틱 속성 Role이 Role.Button과 일치하는지 확인하고 싶습니다. 이를 위해서 SemanticMatcher로 우리의 체크를 래핑하고, element에서 element.config.getOrNull(SemanticsProperties.Role)을 사용해 element의 SemanticProperties.Role을 가져와 해당 값이 일치하는지 확인할 수 있습니다.

이와 같은 패턴은 element의 state description을 테스트하는 데에도 동일하게 적용됩니다. 코드 중복을 피하기 위해 element의 state description을 확인하는 데 사용되는 extension function인 `assertStateDescription`을 만들었습니다.

# 마무리

이 블로그 포스트에서는 WCAG 성공 기준 4.1.2: Name, Role, Value에 대한 접근성 테스트 작성에 대해 논의했습니다. 이러한 테스트는 모바일 접근성에 항상 적용되는 것은 아니지만, 본 블로그 포스트에서는 접근성 테스트 작성 방법에 대한 예시를 제공하고 있습니다.

<div class="content-ad"></div>

안드로이드에서 접근성에 대한 테스트를 작성해 보았나요? 공유해 주시면 감사하겠어요!

# 블로그 글 링크

- Jetpack Compose에서 Modifier를 사용하여 안드로이드 접근성 향상하기
- 이름, 역할, 값
- SemanticsMatcher