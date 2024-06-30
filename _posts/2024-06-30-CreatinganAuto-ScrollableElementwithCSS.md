---
title: "CSS로 자동 스크롤 가능한 요소 만드는 방법"
description: ""
coverImage: "/assets/img/2024-06-30-CreatinganAuto-ScrollableElementwithCSS_0.png"
date: 2024-06-30 22:42
ogImage: 
  url: /assets/img/2024-06-30-CreatinganAuto-ScrollableElementwithCSS_0.png
tag: Tech
originalTitle: "Creating an Auto-Scrollable Element with CSS"
link: "https://medium.com/@albyianna/creating-an-auto-scrollable-element-with-css-b7d814c73522"
---


웹 페이지에 자동으로 스크롤되어 가장 최신 콘텐츠를 표시하는 채팅이나 로그 표시를 구현해보고 싶은 적이 있으신가요? 몇 줄의 CSS 코드만으로 쉽게 이를 구현할 수 있습니다.

![image](https://miro.medium.com/v2/resize:fit:1036/1*3Kw8USr6Jfn_cR0JHLw2-Q.gif)

이 튜토리얼에서는 CSS를 사용하여 자동으로 스크롤되는 요소를 만들어 새로운 콘텐츠가 추가됨과 동시에 자동으로 아래로 스크롤되는 방법을 안내해 드리겠습니다.

여기 필요한 CSS 코드입니다:

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

```css
.autoscrollable-wrapper {
  overflow: auto;
  max-height: 100%;
  display: flex;
  flex-direction: column-reverse;
}
```

여기에 HTML 코드가 있어요:

```html
<div class="autoscrollable-wrapper">
  <div class="autoscrollable-content">
    <!-- 여기에 자동으로 스크롤이 필요한 콘텐츠를 넣으세요 -->
  </div>
</div>
```

CSS 코드의 각 줄을 하나씩 살펴보면서 그 역할을 이해해 볼까요?

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

- 오버플로 속성은 자동으로 설정되어 있습니다. 이는 콘텐츠가 넘칠 경우 해당 요소에 스크롤바가 표시됨을 의미합니다.
- 최대 높이 속성은 100%로 설정되어 있어 부모 요소의 높이를 100%로 제한합니다. 높이 대신 최대 높이를 설정하는 것은 요소가 높이를 동적으로 조절할 수 있게 해주기 때문에 유용합니다. 이로써 콘텐츠가 자동으로 맨 위에 표시되고 새 콘텐츠가 아래에 나타날 수 있게 됩니다. 즉시 콘텐츠를 아래쪽에 표시하려면 높이를 사용할 수 있습니다.
- 디스플레이 속성은 플렉스로 설정되어 있어 해당 요소가 플렉스 컨테이너가 됩니다. 플렉스 방향 속성은 column-reverse로 설정되어 있어 플렉스 아이템이 수직으로 역순으로 배치됩니다. 이것이 새 콘텐츠가 아래쪽에 나타나며 자동 스크롤을 유발하는 이유입니다.

요약하면 CSS에서 flex 및 flex-direction: column-reverse를 사용하면 자동 스크롤 가능한 요소가 바닥에 스크롤되고 추가됨에 따라 최신 콘텐츠가 표시되도록 유지됩니다.

저는 몇 가지 자바스크립트 코드가 포함된 CodePen 예제를 만들었습니다. 해당 코드는 요소에 텍스트를 계속 출력하여 새 텍스트가 추가될 때 요소가 스크롤되는 것을 보여줍니다.

# 참고문헌

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

- 스택 오버플로우 답변 — 이 튜토리얼을 영감을 주는 원본 솔루션입니다.