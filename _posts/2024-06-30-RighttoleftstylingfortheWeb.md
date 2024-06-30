---
title: "웹에서 오른쪽에서 왼쪽으로 스타일링 하는 방법"
description: ""
coverImage: "/assets/img/2024-06-30-RighttoleftstylingfortheWeb_0.png"
date: 2024-06-30 22:39
ogImage: 
  url: /assets/img/2024-06-30-RighttoleftstylingfortheWeb_0.png
tag: Tech
originalTitle: "Right to left styling for the Web"
link: "https://medium.com/@krishnakiran1992/right-to-left-styling-for-web-apps-b4573d8979db"
---


## HTML에서 오른쪽에서 왼쪽 텍스트를 다루는 방법에 대한 이해

![image](/assets/img/2024-06-30-RighttoleftstylingfortheWeb_0.png)

# 왜 RTL?

세계에는 (아랍어, 히브리어 등) 좌에서 우로 읽는 대신 우측에서 좌측으로 텍스트를 읽는 몇 개의 언어가 있습니다. 웹사이트에 RTL 지원을 추가하는 것은 간단하며 그것의 시장을 크게 높일 수 있습니다. RTL 언어에 대응하기 위해서는 개발자들은 특별한 방식으로 개발 프로세스를 적응시켜야 합니다.

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

# RTL 지원 방법

웹 애플리케이션에서 RTL 언어를 지원하는 경우 텍스트, 스크롤 막대, 진행 표시기, 버튼 등 모든 것이 반전되거나 뒤집힙니다. RTL을 지원하기 위한 개발 지침을 살펴보겠습니다.

## HTML에 dir 및 lang 추가

- `html` 요소에 dir="rtl"을 추가합니다. 브라우저는 dir 태그를 해석하여 웹 사이트의 콘텐츠를 자동으로 뒤집고 렌더링합니다.
- `html` 요소에 lang="ar"과 같은 적절한 lang 속성을 추가합니다. lang 글로벌 속성은 요소의 언어를 정의하는 데 도움이 됩니다. 편집할 수 없는 요소의 언어를 작성하는 언어이거나 사용자가 편집 가능한 요소를 작성해야 하는 언어를 의미합니다.

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

<img src="/assets/img/2024-06-30-RighttoleftstylingfortheWeb_1.png" />

## 오른쪽에서 왼쪽으로 레이아웃 지원하도록 CSS 조정하기

브라우저가 direction 태그를 해석한다는 것을 배웠으니, 이제 CSS가 어떻게 해석되는지 살펴보겠습니다. 이미지와 텍스트가 서로 인접한 카드를 예로 들어보겠습니다.

오른쪽의 카드는 텍스트가 오른쪽에서 왼쪽으로 렌더링되었지만, 여백과 이미지는 올바르게 해석되지 않습니다. RTL을 지원하려면 CSS를 수정해야 합니다.

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

방향 속성 값 수정
이미지를 오른쪽으로 띄우고 왼쪽에 마진을 적용하면 렌더링 문제가 해결됩니다.

```js
.media img {
    float: right;
    margin-left: 20px;
}
```

이 방법은 간단하고 이해하기 쉽지만 CSS를 추가로 작성해야 한다는 점이 단점입니다. 대안적인 방법을 살펴보겠습니다...

논리적 CSS 속성 사용
추가 CSS를 작성하는 수고를 덜기 위해, '논리적 속성'인 '시작'과 '끝'을 사용해 컨텐츠를 오른쪽에서 왼쪽으로 자동으로 정렬할 수 있습니다. '왼쪽'이나 '오른쪽' 대신에 '시작'이나 '끝'과 같은 논리적 속성을 사용하세요.

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

우리 코드를 수정하여 이미지에서 float를 제거하고 margin-right 대신 margin-inline-end를 추가해주세요.

```js
.media {
    display: flex;
}
.media img {
    margin-inline-end: 20px;
}
```

여러 개의 스타일시트 생성하기
가끔 논리적 속성이 원하는 결과를 내지 못할 때는 dir 속성으로 속성값을 덮어쓰는 것에 의존해야 합니다. 이때 CSS가 복잡해지고, 모든 오른쪽에서 왼쪽으로 가는 CSS를 별도의 파일로 이동하여 최적화할 필요가 있습니다.

```js
.wrapper {
    position: absolute;
    left: 0;
}
.media {
    margin-left: 20px;
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

모든 RTL 오버라이드를 포함하는 rtl.css 파일을 만들어보세요. 이 파일은 로케일(언어)에 따라 선택적으로 로드할 수 있습니다.

```js
[dir="rtl"].wrapper {
    right: 0;
    left: initial;
}
[dir="rtl"].media {
    float: right;
    margin-right: 20px;
}
```

큰 프로젝트의 경우, 모듈화하고 하나의 rtl.output.css 파일로 컴파일하는 것이 좋습니다.

RTL 스타일을 자동화하세요
전통적인 LTR 언어용과 RTL용 두 개의 별도 CSS 파일을 컴파일하는 것은 최적화하는 또 다른 방법입니다. RTLCSS와 같은 도구를 사용하면 CSS의 RTL 대응파일을 자동으로 생성할 수 있습니다.

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
output.css
p {
    padding-left: 1em;
    font-size: 1rem;
}

output.rtl.css:
p {
    padding-right: 1em;
    font-size: 1rem;
}
```

RTLCSS는 모든 방향에 민감한 CSS 속성을 완전히 지원합니다. 더 나아가 CSS 주석을 통해 처리 지시문 (무시, 앞에 추가, 대체, 제거, 이름 바꾸기 등)을 제공하여 명시적으로 제어할 수 있습니다. RTLCSS가 어떻게 작동하는지 확인해보세요.

## 논리적 속성 더 알아보기

CSS 논리적 속성과 값은 물리적이 아닌 논리적 방향 및 차원 매핑을 통해 레이아웃을 제어하는 기능을 제공합니다. 논리적 속성은 해당하는 물리적 속성의 방향상 등가물을 정의합니다.

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

모든 주요 브라우저에서 상호 운용 가능한 지원을 받는 일반적으로 사용되는 논리 값들 —

```js
text-align: start | end
justify-content: flex-start | flex-end
align-content: flex-start | flex-end
grid-column-start: <value>
grid-column-end: <value>
inline-size: <width>
margin-block-start/end: <value>
margin-inline-start/end: <value>
padding-block-start/end: <value>
padding-inline-start/end: <value>
border-inline-start/end: <value>
```

논리 속성과 값의 브라우저 지원 테스트 결과를 참조해주세요.

## 마지막으로

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

이 기사는 RTL 웹사이트에서 작업하는 구체적인 사항을 간략히 설명하는 것을 목표로 합니다. 더 많은 정보는 제공된 링크를 참조해주시기 바랍니다.

이 기사를 읽어주셔서 감사합니다. 의겄하신 점에 대한 코멘트를 남겨주세요.

감사합니다,
-KK