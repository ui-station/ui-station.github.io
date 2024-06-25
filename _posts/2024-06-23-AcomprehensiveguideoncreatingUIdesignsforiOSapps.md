---
title: "iOS 앱을 위한 UI 디자인 완벽 가이드"
description: ""
coverImage: "/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_0.png"
date: 2024-06-23 23:44
ogImage:
  url: /assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_0.png
tag: Tech
originalTitle: "A comprehensive guide on creating UI designs for iOS apps"
link: "https://medium.com/user-experience-design-1/guide-on-creating-ui-design-for-ios-apps-5bed644b1667"
---

<img src="/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_0.png" />

iOS 앱을 위한 UI 디자인을 만드는 포괄적인 가이드를 공유하고 싶어요. 이 가이드는 우리가 애플 가이드라인과 일관성을 유지하면서 고품질 디자인을 만들기 위해 알아야 하는 필수 주제를 다룹니다.

구성:

- 레이아웃 및 그리드
- 색상
- 타이포그래피
- 아이콘
- 컴포넌트
- 전환

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

# 레이아웃과 그리드

Figma에서 iPhone용 다양한 아트보드 크기를 찾을 수 있고, 디자인을 위해 어떤 것을 선택해야 하는지 고민할 수 있습니다. 하지만 코드 수준에서 XCode는 자동으로 선택한 기기에 따라 레이아웃을 조정해줍니다. 따라서 가장 인기 있는 iPhone의 아트보드 크기를 선택하고, 이 모델의 레이아웃 사양을 고려하여 UI를 설계할 수 있습니다.

아래 이미지는 iPhone 14 Pro의 레이아웃 영역과 사양을 보여줍니다.

![iPhone 14 Pro Layout](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_1.png)

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

☝️ 버튼 및 컨트롤과 같은 UI 요소는 상태 표시줄과 홈 인디케이터 영역에 가려지지 않는 영역에만 배치해야 합니다. 이렇게 하면 iOS 시스템 요소와의 상호 작용에 방해가 되지 않습니다.

그리드는 콘텐츠를 구조적으로 배열하는 데 도움이 됩니다. 일반적으로 반응형 웹사이트 디자인에 사용되지만, 모바일 앱에서도 유용합니다.

모바일 앱을 위한 그리드 정의 방법:

- 화면 위에 요소를 정확히 배치하기 위해 8포인트 그리드를 생성합니다.
- 좌우 여백을 16포인트의 최소 크기로 정의하세요. 이것은 iOS의 표준입니다.
- 필요에 맞게 최적의 열 수를 정의하세요. 16포인트의 거터 크기가 가장 일반적이지만, 더 작거나 큰 거터 크기를 정의해야 한다면, 스트레치 그리드에서는 거터 크기가 열 너비에 영향을 미치므로, 거터 크기가 클수록 열 너비가 작아집니다.

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

👉 이 멋진 기사를 확인해보세요! 여기서 모바일 그리드에 대해 더 배울 수 있어요.

![image](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_2.png)

# 색상

iOS에는 활력, 접근성 설정 및 외형 모드에 자동으로 적응되는 시스템 및 의미론적 색상이 정의되어 있어요.

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

시멘틱 컬러는 목적을 설명하는 이름을 가진 색상입니다. 각 시멘틱 컬러에는 기본, 보조, 제3의 색상 변형이 있어서 등급에 따라 콘텐츠를 구분하는 데 사용됩니다. 예를 들어, 기본 배경에 대한 사용자 정의 색상을 정의하려면 색상 이름을 앱은 브랜드/앱의 약어이며 PrimaryBackground는 색상의 시멘틱 이름입니다. 사용자 지정 색상의 이름은 iOS 시스템 색상과 같아서는 안 됨을 언급할 가치가 있습니다.

![image](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_3.png)

🎨 애플의 가이드라인을 고려하여 사용자 정의 색상 팔레트를 만드는 방법을 살펴보겠습니다.

## 기본 색상

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

앱의 개성을 전달하는 데 기본적인 색상이 중요합니다. 이것은 팔레트의 기본 색상입니다. 대부분의 화면과 구성 요소에서 자주 표시되는 이 색상은 주요 작업을 나타내는 데 사용됩니다. 특정 UI 요소를 강조하거나 탭, 누름과 같은 상호 작용을 위한 UI 상태를 나타내는 데 주요 색상 쉐이드를 사용할 수도 있습니다.

Figma에서 색상 쉐이드를 생성하려면 이 플러그인을 사용할 수 있습니다.

![이미지](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_4.png)

![이미지](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_5.png)

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

## 보조 및 강조 색상

보조 색상은 주요 색상을 지원하고 디자인에 깊이와 다양성을 더합니다.

![보조 색상](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_6.png)

보조 색상 외에도 "강조" 색상 용어를 만날 수 있습니다. 강조 색상은 중요한 조치 및 컨트롤을 강조하는 데 사용되는 보조 색상의 일부로 고려할 수 있습니다.

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

강조색은 다음에 가장 적합합니다:

- 슬라이더 및 스위치와 같은 선택 컨트롤
- 선택된 텍스트 강조
- 진행 표시 막대
- 링크 및 헤드라인

기본 색을 보완하는 보조 색상을 찾으려면 Coolors와 같은 색 구성 생성기를 사용해보세요. 일반적으로 앱에는 1개의 주요 색상, 1-2개의 보조 색상 및 1-3개의 강조색이 있어야 합니다.

![이미지](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_7.png)

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

## 채우기 색상

iOS에서는 채우기 색상이 UI 요소에 사용되며, 이를 통해 배경 색상이 나타납니다. 채우기 색상은 동일한 색상 값을 공유하지만 각 색상 변형마다 다양한 불투명도 수준을 갖고 있습니다.

iOS 채우기 색상 변형:

- 기본 채우기 색상 — 슬라이더의 트랙과 같은 얇고 작은 모양에 사용됩니다.
- 보조 채우기 색상 — 스위치의 배경과 같은 중간 크기의 모양에 사용됩니다.
- 제 3위 채우기 색상 — 입력 필드, 검색 바 또는 버튼과 같은 큰 모양에 사용됩니다.

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

![image 8](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_8.png)

![image 9](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_9.png)

## 배경색상

iOS는 콘텐츠 및 요소의 그룹화를 구분하기 위해 배경색상의 색상 변형을 정의합니다.

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

- 전반적인 뷰의 기본적인 배경을 위한 기본(primary).
- 전반적인 뷰 내에서 콘텐츠나 요소들을 그룹화하기 위한 보조(secondary).
- 보조 요소 내에서 콘텐츠나 요소들을 그룹화하기 위한 제3 단계(tertiary).

![이미지](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_10.png)

![이미지](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_11.png)

## 레이블 색상

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

라벨 색상에는 주요, 보조, 제 3 및 제 4 등급의 색상 변형이 있습니다. 중요도 수준에 따라 색상은 해당 투명도 수준을 갖습니다.

![이미지](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_12.png)

- 주요 레이블 색상은 사용자에게 강조해야 할 중요한 텍스트나 콘텐츠를 강조하는 데 사용됩니다. 예를 들어, 제목 및 헤드라인, 내비게이션 막대 페이지의 레이블, 양식에서의 필드 레이블 또는 입력 컨트롤에 주요 색상을 사용할 수 있습니다.
- 보조 레이블 색상은 자막이나 지원 정보를 표시하는 데 사용됩니다. 보조 레이블은 양식과 입력 필드에서 필드나 입력 컨트롤에 대한 추가 컨텍스트나 지시사항을 제공하기 위해 사용될 수 있으며, 목록이나 테이블에서 목록이나 테이블 항목에 대한 보조 정보와 세부 정보를 표시하는 데 사용됩니다.
- 제 3 등급 레이블 색상은 더 중요하지 않은 텍스트나 콘텐츠를 표시하는 데 사용됩니다. 더 이상 필수가 아닌 보충 정보나 앱 사용자의 이해에 필수적이지 않은 세부 정보를 표시하기 위해 사용됩니다.

![이미지](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_13.png)

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

## 구분선 색상

구분선은 콘텐츠 그룹을 시각적으로 구분하는 데 사용되는 가는 수평 선입니다. 표 뷰, 컬렉션 뷰 및 사용자 인터페이스의 다른 부분에서 시각적 계층 구조를 만들고 사용자가 콘텐츠의 구조를 이해하는 데 도움이 되도록 자주 사용됩니다.

iOS에서는 불투명도 수준을 가진 Separator 및 불투명 선 (일반 선) 두 가지 유형의 구분선 색상을 정의합니다.

![image](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_14.png)

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

<img src="/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_15.png" />

## 기능적인 색상

기능적인 색상은 요소의 상태나 상태를 나타내거나 사용자에게 맥락이나 메시지를 제공하는 데 사용됩니다.

기능적인 색상에 대한 지침:

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

- 성공 상태: 성공 상태를 나타내기 위해 녹색이 자주 사용됩니다. 이는 작업이 성공적으로 완료되었거나 양식이 올바르게 작성되었음을 나타내는 데 사용될 수 있습니다.
- 경고 상태: 노랑 또는 주황색이 경고 상태를 나타내는 데 흔히 사용됩니다. 이는 사용자에게 잠재적인 문제를 경고하거나 필수 조치가 아직 완료되지 않았음을 나타내는 데 사용될 수 있습니다.
- 오류 상태: 빨강은 오류 상태를 나타내는 데 자주 사용됩니다. 이는 텍스트 또는 배경 색상, 또는 아이콘이나 버튼과 같은 요소에 사용될 수 있습니다.

![이미지](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_16.png)

![이미지](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_17.png)

## 색상 관리

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

많은 Figma 플러그인이 있어서 색상 관리에 도움이 되는데요:

- Color Palette Generator는 제공하는 단일 색상을 기반으로 색 팔레트를 생성합니다.
- Color Contrast Checker는 두 색상 사이의 대조 비율을 확인하여 색상 선택이 접근성 지침을 준수하는지 확인하는 데 도움을 줍니다.
- Colorblind Simulator을 사용하면 특정 유형의 색상 시각 결여를 가진 사람이 볼 때 디자인을 확인할 수 있습니다. 이를 통해 디자인이 모든 사용자에게 접근 가능한지 확인할 수 있습니다.
- Dark Mode Magic을 사용하면 생성한 Light 테마를 기반으로 Dark 테마를 생성할 수 있습니다.

# Typography

Apple은 San Francisco와 New York과 같은 내장된 글꼴 모음을 가지고 있습니다.
San Francisco에는 SF Pro를 포함한 여러 가지 변형이 있으며, SF Pro는 해당 글꼴의 표준 버전입니다.

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

원하는 경우 사용자 정의 글꼴을 계속 사용할 수 있습니다. 몇 가지 고려해야 할 사항이 있습니다:

- 글꼴이 가독성이 좋도록 해야 합니다. 가독성에 영향을 미치는 요소에는 문자 인식, 선 두께(문자를 구성하는 선의 두께), 트래킹(글자 간격), 리딩(줄 간격), 글꼴 크기 및 글꼴 스타일이 있습니다.
- 모바일 앱에서 최대 두 가지 글꼴을 사용하여 간단하고 조화로운 인터페이스를 유지하는 것이 좋습니다.
- 글꼴이 서로 어떻게 보왁 주위서 상치하는지를 고려하는 것이 중요합니다. 서로 너무 비슷한 글꼴 유형을 결합하는 것을 피해야 하며, 그렇지 않으면 서로 구별하기 어려울 수 있습니다.

Google Fonts를 사용하여 다른 글꼴 조합을 미리보기하는 것을 고려해보세요. 또한 Figma가 제작한 준비된 Google 글꼴 유형 조합 팔레트를 사용할 수도 있습니다.

## 글꼴 스타일 및 글꼴 크기

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

다양한 글꼴 크기와 굵기를 사용하여 제목, 헤딩, 본문 텍스트 및 캡션이란 텍스트 스타일을 구분합니다. 기본적으로 iOS에는 Large Title, Title 1, Title 2 등과 같은 미리 정의된 텍스트 스타일이 있습니다. 이러한 텍스트 스타일마다 기본 글꼴 크기와 다이나믹 타입 크기가 부여됩니다.

다이나믹 타입은 iOS의 기능 중 하나로, 사용자가 앱 내 텍스트의 글꼴 크기를 조절할 수 있습니다. 사용자 정의 글꼴을 사용하려는 경우 기본 글꼴 크기뿐만 아니라, 다이나믹 타입 크기도 코드 수준에서 정의되어야 사용자가 텍스트의 글꼴 크기를 조절할 수 있도록 보장해야 합니다.

사용자 정의 글꼴 스타일에 대해 기본 글꼴 크기를 선택하는 가이드로 다음 표를 고려해 보세요.

![표](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_18.png)

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

## 텍스트 레이아웃

트래킹과 리딩은 최적의 가독성과 가시성을 위해 텍스트 레이아웃을 세밀하게 조정할 수 있는 필수적인 텍스트 속성입니다.

트래킹은 글자 사이의 간격을 의미합니다. CSS에서는 "letter-spacing"으로 불리며, 트래킹과 letter-spacing은 동의어입니다.

리딩은 텍스트 줄 사이의 간격을 말합니다. Figma와 CSS에서는 이 속성을 "line-height"라고 합니다. 줄 간격을 늘리거나 줄이는 것은 가독성을 향상시키는 데 도움이 될 수 있습니다. 리딩은 텍스트 기준선부터 측정됩니다.

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

👉 추적 및 리딩에 대한 몇 가지 팁:

- 가독성을 높이려면 리딩을 증가시키세요. Figma는 가독성 있는 텍스트를 얻기 위한 최소한의 라인 높이로서 글꼴 크기의 1.125배(112.5%)로 설정할 것을 권장합니다. 그러나 값은 글꼴에 따라 다를 수 있습니다. 예를 들어, Apple은 본문 텍스트(SF Pro 글꼴)를 위한 리딩을 129%(22 pt)로 최소값으로 정의합니다. 그러나 긴 문단의 텍스트에 대한 좋은 가독성을 얻고 싶은 경우 141%(24 pt)로 리딩을 증가시키는 것을 권장합니다.

![이미지](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_19.png)

- 좋은 가독성을 유지하려면 폰트 크기에 대한 추적 값을 너무 높거나 너무 낮게 사용하는 것을 피하는 것이 좋습니다. 시스템 글꼴의 최적 추적 값을 설명하는 Apple의 추적 테이블을 참고할 수 있습니다.

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

<img src="/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_20.png" />

# 아이콘

아이콘은 인터페이스 항목, 작업 및 상태의 의미를 전달해야 하는 그래픽 요소입니다. 효과적인 아이콘은 사용자에게 간단하고 익숙하며 이해하기 쉬워야 합니다.

iOS에는 iOS 앱에서 사용할 수 있는 벡터 아이콘(SF Symbols) 세트가 제공됩니다. 여기 SF Symbols를 찾을 수 있는 Figma 파일이 있습니다. 또한 SF Symbols를 편집하고 사용자 정의하는 방법에 대한 상세한 비디오도 있습니다.

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

iOS 앱에서 아이콘을 사용할 때 몇 가지 팁:

- 사용자 정의 아이콘을 사용하려면 일관된 스타일, 크기 및 두께를 유지하는지 확인하세요.

![Custom Icons](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_21.png)

- 아이콘의 명암비를 테스트하세요. W3C에 따르면 아이콘과 같은 "그래픽 객체"는 적어도 3:1의 명암비를 가져야 합니다.

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

![이미지](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_22.png)

- 아이콘이 한눈에 알아볼 수 없으면 가능한 경우 텍스트 레이블을 추가하세요. 아래 예시에서 Headspace가 텍스트 레이블이 없다면, 왼쪽 아이콘(Today)의 의미를 빠르게 이해하기 어려울 수 있습니다.

![이미지](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_23.png)

- 모든 표준 화면에서 작동할 수 있도록 SVG와 같은 벡터 형식의 아이콘을 사용하세요. 1배 해상도의 표준 화면과 2배, 3배 고해상도의 레티나 디스플레이에 모두 작동해야 합니다.
- 아이콘은 가독성이 있고 탭하기 쉬워야 합니다.
- 일반 탭 바 아이콘의 크기는 적어도 25x25pt이어야 하며, 내비게이션 바의 아이콘은 20x20 또는 30x30pt가 될 수 있습니다.

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

iOS 아이콘에 대한 자세한 정보는 Apple의 문서를 확인해보세요.

# 구성 요소

iOS에는 다양한 종류의 구성 요소 라이브러리가 있습니다. 이들을 모두 문서에서 찾을 수 있습니다. 여기서는 거의 모든 iOS 모바일 앱에서 찾을 수 있는 가장 인기 있는 세 가지 구성 요소 — 목록, 탭 바 및 네비게이션 바 — 에 대해 다루고, 각각에 대한 최선의 방법을 강조하고 싶습니다.

## 목록과 표

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

아이템의 컬렉션을 표시하는 방법으로 목록과 표가 있습니다. 이 아이템들은 텍스트, 아이콘/이미지 또는 텍스트와 선택 컨트롤의 조합일 수 있습니다.

![Image 1](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_24.png)

![Image 2](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_25.png)

최선의 방법들

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

- 배경 전체에 다른 색상을 사용하여 배경과 콘텐츠 그룹 간의 시각적 대조를 만드세요.

![Image](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_26.png)

- 테이블 콘텐츠를 논리적 그룹으로 분할하고 시각적으로 구분할 수 있도록 제목을 추가하세요. 이렇게 하면 사용자가 테이블에서 특정 항목을 쉽게 찾을 수 있습니다.

![Image](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_27.png)

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

마크다운 형식으로 테이블 태그를 변경해주세요.

- 표 태그는 유사한 글꼴 스타일을 제목과 행 제목에 사용하지 마세요. 그렇게 하면 사용자가 컨텐츠 그룹을 구별하기 어려워질 수 있습니다.

![image](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_28.png)

## 탭 바

탭 바는 앱 내에서 다른 뷰로 전환할 수 있게 해주는 전역 탐색 컨트롤입니다. 각 탭은 의미 있고 설명적이어야 합니다.

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

**최상의 방법**

- "Home" 또는 "Overview"와 같은 단일 탭에 관련 없는 콘텐츠를 그룹화하는 것을 피하세요. 예를 들어, 아래 이미지는 "Home" 탭이 "검색", "즐겨찾기", "친구"와 같은 관련 없는 기능을 결합한 것을 보여줍니다. 이는 사용자가 필요한 것을 찾기 어렵게 만듭니다. 이러한 문제를 해결하기 위해 핵심 기능과 섹션을 식별하여 콘텐츠를 각각 다른 탭으로 분리하고 관련된 콘텐츠만 그룹화하세요.

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

- 테이블 태그를 Markdown 형식으로 변경해주세요.

![Image](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_31.png)

## 내비게이션 바

내비게이션 바는 앱의 계층 구조를 통해 탐색할 수 있는 탐색 컨트롤입니다.

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

![이미지](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_32.png)

최상의 사용법

- 네비게이션 바에 너무 많은 작업을 추가하는 것은 인터페이스를 지저분하게 만듭니다.

![이미지](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_33.png)

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

- 네비게이션 바와 필터, 세그먼트 컨트롤 등과 같은 다른 요소 간에 충분한 대조가 있는지 확인합니다.

![이미지](/assets/img/2024-06-23-AcomprehensiveguideoncreatingUIdesignsforiOSapps_34.png)

# 전환

전환은 한 뷰 또는 뷰 세트의 외관을 다른 것으로 변경하는 방법입니다.

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

<img src="https://miro.medium.com/v2/resize:fit:1400/1*-TfC1w0q9QTAExsVkopbGg.gif" />

iOS는 일련의 표준 전환 스타일을 제공합니다. 사용자가 적응하는 데 추가 노력을 필요로 하는 것을 방지하기 위해 사용자 정의 애니메이션을 생성하려면 iOS 표준 스타일을 따르도록 확인하십시오. 그렇지 않으면 사용자 만족도가 감소할 수 있습니다.

기억해야 할 권장 사항:

- 사용자에게 혼란을 야기할 수 있는 복잡하거나 화려한 전환을 사용하지 마세요.
- 일관된 지속 시간과 타임팅을 사용하여 사용자에게 부드럽고 연속적인 경험을 제공하세요.
- 사용자의 주의를 산만하게 하는 너무 빠르거나 너무 느린 전환을 사용하지 마세요.
- 코드 수준에서, 사용자 지정 전환은 "모션 줄이기"가 꺼져 있을 때 접근성 설정에 응답할 수 있도록 해야 합니다.

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

코드 없이 사용자 정의 전환을 만들 수 있는 몇 가지 도구:

- LottieFiles
- ProtoPie
- Flow

# 결론

이 글이 도움이 되었기를 바랍니다. 주제에 대한 피드백이나 제안이 있으면 기쁠 것입니다!

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

👉 새 YouTube 채널 구독해보세요! UX/UI 디자인 통찰과 팁에 관한 영상을 만나보세요.

LinkedIn에서 연락 주시거나 Twitter에서 팔로우해주세요. 감사합니다!
