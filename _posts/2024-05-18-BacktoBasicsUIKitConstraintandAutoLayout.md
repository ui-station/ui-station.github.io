---
title: "Basic 우선 UIKit Constraint와 Auto Layout"
description: ""
coverImage: "/assets/img/2024-05-18-BacktoBasicsUIKitConstraintandAutoLayout_0.png"
date: 2024-05-18 15:36
ogImage:
  url: /assets/img/2024-05-18-BacktoBasicsUIKitConstraintandAutoLayout_0.png
tag: Tech
originalTitle: "Back to Basics: UIKit Constraint and Auto Layout"
link: "https://medium.com/@alvinmatthew/back-to-basics-uikit-constraint-and-auto-layout-2141efb89f43"
---

<img src="/assets/img/2024-05-18-BacktoBasicsUIKitConstraintandAutoLayout_0.png" />

안녕하세요 여러분, 오늘은 UIKit의 제약 조건과 오토 레이아웃에 대해 다시 한번 살펴보고 깊게 이해해 보겠습니다. 이 기사는 세 가지 부분으로 나뉘어질 것입니다: 먼저, 내재 콘텐츠 크기를 탐구할 것이고, 다음으로는 제약 조건과 오토 레이아웃에 대해 논의할 것이며, 마지막으로는 제약 조건과 Auto Layout이 UIScrollView 및 UICollectionView 내에서 어떻게 작동하는지 살펴볼 것입니다.

이 기사에서는 실제 사용 사례로서 티커 뷰를 만드는 것을 사용할 것입니다. 티커 뷰는 뉴스 헤드라인이나 주가 같은 연속적인 정보 스트림을 표시하는 일반적인 UI 구성 요소입니다. 이 구성 요소를 구축함으로써, 우리는 UIKit의 제약 조건과 오토 레이아웃 원칙을 효과적으로 적용하는 방법을 살펴볼 것입니다. 이 실습 예제를 통해 내재 콘텐츠 크기를 이해하고, 제약 조건을 구성하고, 레이아웃이 UIScrollView 또는 UICollectionView 내에서 올바르게 적용되도록 보장하는 방법을 이해할 수 있을 것입니다. 이 과정을 통해 여러분은 자신의 프로젝트에서 오토 레이아웃을 구현하고 문제 해결하는 방법에 대해 더 깊은 이해를 얻을 것입니다.

<img src="/assets/img/2024-05-18-BacktoBasicsUIKitConstraintandAutoLayout_1.png" />

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

# 내재 콘텐츠 크기 이해

상기에서는 티커 뷰의 해부학을 설명했습니다. 그 구성에 사용된 UIKit 구성 요소는 UILabel, UITextView, UIImageView, UIStackView, UIScrollView 및 UIView입니다.

동적 높이와 제한된 너비를 갖는 티커를 만드는 맥락에서 구성 요소의 내재 콘텐츠 크기를 고려하는 것이 중요합니다. 아래는 내재 콘텐츠 크기에 대한 설명입니다:

내재 콘텐츠 크기를 가진 뷰들

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

- UILabel은 텍스트에 따라 결정되는 고유 크기를 제공하여 동적 높이 조정에 적합합니다.
- UITextView도 텍스트에 따라 결정되는 고유 크기를 가지며, 콘텐츠에 기반한 동적 높이 조정이 가능합니다. 스크롤링을 비활성화해야 합니다. 이렇게 하면 구성 요소가 불필요한 스크롤링 없이 콘텐츠에 맞게 적응할 수 있습니다.

고유 콘텐츠 크기가 없는 뷰

- UIImageView는 일반적으로 고유 크기가 없습니다. 크기는 콘텐츠나 제약 조건에 따라 결정됩니다.
- UIStackView는 고유 크기가 없으며, 배치된 하위 뷰에 따라 동적으로 크기가 조정됩니다.
- UIScrollView는 고유 크기가 없으며, 크기는 콘텐츠와 제약 조건에 따라 결정됩니다.
- UIView는 UIImageView와 유사하게 일반적으로 고유 크기가 없습니다. 크기는 콘텐츠와 제약 조건에 따라 영향을 받습니다.

# 제약 조건과 자동 레이아웃

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

세부 내용에 들어가기 전에 콘텐츠 허깅, 압축, 그리고 제약의 기본 개념을 이해하는 것이 중요합니다.

콘텐츠 허깅

콘텐츠 허깅 우선순위는 UI 요소가 내재된 콘텐츠 크기보다 커지는 것에 얼마나 저항하는지를 측정한 값입니다.

- 높은 우선순위: 높은 콘텐츠 허깅 우선순위는 요소가 내재된 콘텐츠 크기에 머무르거나 가까이 유지하길 선호하며 확장에 저항합니다.
- 사용 사례: 뷰가 컴팩트한 크기를 유지하려고 하는데 크기를 확장해야 하는 강력한 이유가 없을 때 유용합니다.

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

내용 압축 저항

내용 압축 저항 우선순위는 UI 요소가 내재 콘텐츠 크기보다 작아지는 것에 얼마나 저항하는지를 측정하는 지표입니다.

- 높은 우선순위: 높은 내용 압축 저항 우선순위는 요소가 내재 크기 아래로 크기를 줄이는 것을 피하려고 하는 것을 의미합니다.
- 사용 사례: 한정된 공간이 있는 경우 특히 뷰가 너무 작아지는 것을 방지하고 싶을 때 유용합니다.

![이미지](/assets/img/2024-05-18-BacktoBasicsUIKitConstraintandAutoLayout_2.png)

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

기본 제약 사항

제약 사항은 뷰 내의 UI 요소의 레이아웃과 동작을 정의하는 규칙입니다.

- 제약 사항의 종류: 제약 사항은 너비, 높이, 간격, 요소 간의 관계 등과 같은 속성을 지정할 수 있습니다.
- 자동 레이아웃: UIKit에서 자동 레이아웃은 제약 사항을 정의하고 관리하는 시스템입니다.
- 적응형 레이아웃: 제약 사항을 통해 다양한 화면 크기와 방향에 맞게 조절되는 적응형 레이아웃을 구현할 수 있습니다.

요약하자면, 내용 흡수 및 내용 압축 저항 우선 순위는 UI 요소의 본질적인 내용 크기에 기반하여 자동 레이아웃이 어떻게 크기를 처리해야 하는지를 안내하는 중요한 요소입니다. 반면에 제약 사항은 뷰 내의 이러한 요소의 레이아웃을 정의하는 규칙과 관계입니다.

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

이전에 제공된 분해에 따라 내재적 크기를 갖는 구성 요소를 우선으로 하는 것이 중요합니다. 이를 달성하기 위해 몇 가지 효과적인 제약 조건과 자동 레이아웃 트릭에 대해 알아봅시다.

- UILabel 설정

- 여러 줄 텍스트를 위해 줄 수를 0으로 설정하여 내용에 따라 높이를 동적으로 조절할 수 있도록 합시다.
- 콘텐츠 허그 및 압축 우선순위가 필수로 설정되어 있는지 확인하세요

```js
titleLabel.numberOfLines = 0
// 아래 코드는 우선 순위가 제대로 되어 있는지 확인하기 위한 것입니다. 기본적으로 이미 필요한 우선순위로 설정되어 있습니다.
titleLabel.setContentHuggingPriority(.required, for: .vertical)
titleLabel.setContentCompressionResistancePriority(.required, for: .vertical)
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

2. UITextView 설정

- isScrollEnabled를 false로 설정하여 UITextView가 콘텐츠에 따라 크기를 조정하지 못하도록 하고, 본질적인 크기를 효율적으로 활용합니다.

```js
contentTextView.isScrollEnabled = false;
```

3. UIImageView 설정

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

- UIImageView에는 콘텐츠에 기반한 본질적인 내용 크기가 없기 때문에, 이미지 뷰의 크기를 정의하기 위해 폭과 높이 제약 조건을 명시적으로 설정해야 합니다.

```js
NSLayoutConstraint.activate([
    iconImageView.widthAnchor.constraint(equalToConstant: 24),
    iconImageView.heightAnchor.constraint(equalToConstant: 24)
])
```

4. UIStackView 설정

- 티커의 해부학을 다시 살펴보면, 두 개의 스택 뷰가 티커 구성 요소를 캡슐화하고 있음을 알 수 있습니다. 내부 수직 스택은 titleLabel과 contentTextView을 감싸고, 이를 다시 iconImageView와 함께 수평 스택 내에 포함시킵니다.
- UIStackView는 배치된 하위 뷰인 titleLabel 및 contentTextView의 본질적인 콘텐츠 크기에 기반하여 크기를 조정합니다. 이 동적 조정은 구성 요소의 본질적인 크기와 스택 뷰 내에서 설정된 정렬 및 분배 옵션에 의해 영향을 받습니다.

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
let labelStackView = UIStackView()
labelStackView.addArrangedSubview(titleLabel)
labelStackView.addArrangedSubview(contentTextView)
labelStackView.axis = .vertical
labelStackView.spacing = 4
```

- 마찬가지로, 수평 스택 뷰는 컨텐츠에 따라 동적으로 크기를 조정합니다. UIImageView 제약 조건이 설정되고 분배가 기본적으로 채우도록 구성된 경우, 수평 스택 뷰는 첫 번째 자식을 우선시하고 압축하여, 이 과정에서 두 번째 자식인 labelStackView를 확장합니다.

```js
let mainStackView = UIStackView(arrangedSubviews: [
 iconImageView,
 labelStackView
])
mainStackView.axis = .horizontal
mainStackView.alignment = .center
mainStackView.spacing = 8
```

5. mainStackView 제약 조건 설정하기

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

- 이어서 mainStackView의 제약 조건을 설정해주십시오. 이를 위해 mainStackView의 상단, 좌측 및 우측 가장자리를 해당 UIView 컨테이너에 맞춥니다.
- 이러한 제약 조건은 스택 뷰가 전체 너비에 걸쳐 있음을 보장하지만, 하단 제약 조건을 포함하는 것이 중요합니다. 이 추가적인 제약 조건은 내용으로부터 나오는 내재 높이와 함께, mainStackView의 수직 위치를 정확히 정의합니다.

- 하단 제약 조건을 설정함으로써, Auto Layout에게 mainStackView를 특정 방식으로 위치하도록 명시적으로 지시하여, 해당 UIView에 대해 명확히 정의된 위치를 갖게 됩니다. 이는 동적 레이아웃을 처리하거나 내재 내용의 크기만으로 전체 수직 위치를 완전히 결정하지 못하는 시나리오에서 특히 중요해집니다.

```js
mainStackView.translatesAutoresizingMaskIntoConstraints = false
addSubview(mainStackView)

let inset = 8.0
NSLayoutConstraint.activate([
 mainStackView.leadingAnchor.constraint(equalTo: leadingAnchor, constant: inset),
 mainStackView.trailingAnchor.constraint(equalTo: trailingAnchor, constant: -inset),
 mainStackView.topAnchor.constraint(equalTo: topAnchor, constant: inset),
 mainStackView.bottomAnchor.constraint(equalTo: bottomAnchor, constant: -inset)
])
```

이 다섯 단계를 완료하면, 구성 요소의 내재 크기에 따라 적응성 있게 조정 가능한 동적 높이를 가진 티커 콘텐츠 레이아웃을 성공적으로 생성하였습니다.

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

# UIScrollView vs UICollectionView

페이지별 캐로셀의 여러 콘텐츠를 관리하는 경우, UIScrollView와 UICollectionView 두 가지 옵션이 있습니다. 이 섹션에서는 우리의 목표를 달성하기 위해 더 적합한 것을 평가하고 결정할 것입니다.

내재 크기에 적응하는 측면에서는, UICollectionView이 일반적으로 UIScrollView보다 뛰어납니다. 그러나 UICollectionView 또는 UIScrollView 중 어느 것이 전체 높이에 가장 높은 콘텐츠 높이를 수용하는지 확인해야 할 때는 구체적인 고려 사항이 있습니다.

UICollectionView의 경우, 게으른 로딩 동작 때문에 콘텐츠 크기를 동적으로 계산하는 것이 최적의 접근 방식입니다. 이는 모든 콘텐츠를 동시에 로드할 수 없으므로 최대 높이를 식별하기가 어려워집니다.

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

UIScrollView와 달리, UIScrollView는 콘텐츠 뷰 내의 모든 내용을 미리로드하여 콘텐츠의 총 높이를 기준으로 contentSize를 쉽게 결정할 수 있습니다. 이 기능을 통해 UIScrollView의 전체 높이로 가장 큰 높이의 콘텐츠를 식별하고 적용할 수 있습니다.

UICollectionView와 UIScrollView 사이의 고려 사항을 요약한 비교 테이블은 다음과 같습니다:

![표](/assets/img/2024-05-18-BacktoBasicsUIKitConstraintandAutoLayout_3.png)

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

주어진 고려사항을 고려할 때, UIScrollView가 모든 요구사항을 충족시키는 적합한 선택인 것으로 판명되었습니다. 따라서 UIScrollView를 선택하였습니다. 이제 구현 세부사항을 살펴보겠습니다.

UIScrollView 설정하기

- 속성 설정

```js
public final class TickerView: UIView {
  private let scrollView: UIScrollView = {
        let scrollView = UIScrollView()
        scrollView.isPagingEnabled = true
        scrollView.showsVerticalScrollIndicator = false
        scrollView.showsHorizontalScrollIndicator = false
        return scrollView
    }()

  private let contents: [TickerContent]
  ...
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

2. 수평 UIStackView 만들기

```swift
let contentStackView = UIStackView(arrangedSubviews: contentViews)
contentStackView.axis = .horizontal
contentStackView.translatesAutoresizingMaskIntoConstraints = false
scrollView.addSubview(contentStackView)
```

3. 각 contentView를 부모 뷰의 너비에 맞게 구성하여 순조로운 페이지 네비게이션을 가능하게 합니다.

```swift
for contentView in contentViews {
  contentView.translatesAutoresizingMaskIntoConstraints = false
  contentView.widthAnchor.constraint(equalTo: widthAnchor).isActive = true
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

4. UIScrollView에 제약 조건 설정

```js
NSLayoutConstraint.activate([
  contentStackView.leadingAnchor.constraint(equalTo: scrollView.leadingAnchor),
  contentStackView.trailingAnchor.constraint(equalTo: scrollView.trailingAnchor),
  contentStackView.topAnchor.constraint(equalTo: scrollView.topAnchor),
  contentStackView.bottomAnchor.constraint(equalTo: scrollView.bottomAnchor),
  scrollView.heightAnchor.constraint(equalTo: contentStackView.heightAnchor)
])
```

- contentStackView는 scrollView의 leading, trailing, top, bottom 가장자리에 고정됩니다.
- scrollView의 높이는 contentStackView의 높이와 동일하도록 제약을 걸었습니다. 이로써 scrollView의 높이는 스택 뷰 내의 내용에 따라 자동으로 조정됩니다.

scrollView.heightAnchor.constraint(equalTo: contentStackView.heightAnchor)의 자세한 설명

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

위 코드 라인에서 scrollView의 높이가 contentStackView의 높이와 동일하도록 제약 조건이 설정됐네요. 이 제약 조건은 scrollView가 contentStackView 내의 콘텐츠에 대해 어떻게 동작하고 적응하는지에 상당한 영향을 미칩니다.

여기서 자세히 살펴볼게요:

- 높이 제약 조건: 이 제약 조건은 scrollView의 높이와 contentStackView의 높이 간의 관계를 설정합니다. 본질적으로 scrollView의 높이가 콘텐츠인 contentStackView의 높이와 일치하도록 동적으로 조절되어야 한다는 것을 명시합니다.
- 동적 적응: scrollView 높이가 contentStackView 높이와 동일하게 설정되면, scrollView는 가로 스택 내의 콘텐츠 뷰의 결합 높이에 따라 자체 높이를 동적으로 조절합니다. 이를 통해 scrollView가 스택 뷰 내의 모든 콘텐츠를 적절히 표시하고 스크롤할 수 있도록 보장합니다.
- 자동 크기 조정: contentStackView 내의 콘텐츠가 변경되거나 다양한 높이를 가지는 경우, scrollView는 content 뷰 중 최대 높이를 수용하기 위해 자동으로 높이를 조정합니다. 이 동작은 스크롤 가능한 뷰 내에서 다양한 크기의 다수 콘텐츠를 처리하는 데 중요합니다.
- 반응형 레이아웃: 이 제약 조건은 scrollView가 고정된 또는 미리 결정된 높이가 없는 채로 다양한 콘텐츠 시나리오를 효율적으로 관리하고 표시할 수 있도록 반응형 레이아웃을 보장합니다. 이는 높이가 미리 알려진 경우가 아닌 동적 콘텐츠를 다룰 때 특히 유용합니다.

요약하면 이러한 높이 제약을 설정함으로써 scrollView는 contentStackView 내의 콘텐츠 뷰의 다양한 높이에 따라 자신의 높이를 동적으로 조절하는 유연한 컨테이너로 변합니다.

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

상기 설정을 통해 UIScrollView 내에서 다수의 콘텐츠를 수용 가능한 구조를 성공적으로 구현했습니다. 수평 스택 뷰를 활용하여 각 콘텐츠 뷰는 부모 뷰의 너비에 맞게 동적으로 조정되어 다양한 콘텐츠 시나리오를 처리하는 데 효과적이고 적응 가능한 솔루션을 제공합니다.
