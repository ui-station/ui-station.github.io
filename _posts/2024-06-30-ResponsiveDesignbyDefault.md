---
title: "기본적으로 반응형 디자인 적용하는 방법"
description: ""
coverImage: "/assets/img/2024-06-30-ResponsiveDesignbyDefault_0.png"
date: 2024-06-30 22:38
ogImage: 
  url: /assets/img/2024-06-30-ResponsiveDesignbyDefault_0.png
tag: Tech
originalTitle: "Responsive Design by Default"
link: "https://medium.com/gitconnected/responsive-design-by-default-c-69e0da3790df"
---


당신의 사이트가 표시되어야 하는 방식!

![Responsive Design by Default](/assets/img/2024-06-30-ResponsiveDesignbyDefault_0.png)

반응형 디자인을 위해 추가 작업을 하는 것이 실제로 가치 있는 일인지 궁금했던 적이 있나요?

저는 분명히 궁금해했어요!

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

모든 미디어 쿼리, 추가 테스트 및 추가 작업이 지루하다면서요.

하지만, 여기를 확인해보세요:

![Responsive Design by Default](/assets/img/2024-06-30-ResponsiveDesignbyDefault_1.png)

MD에서 60.67%의 트래픽이 발생한다니 미쳤죠!

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

더 미친 건 뭔지 알아?

암호화폐가 급부상하고 전체적으로 신경망 링크(우리 폰이 계속 연결돼 해쉬를 계산하는 것)가 늘어나면 이 숫자가 더욱 늘어날 것 같아.

사용자 92.3%가 스마트폰을 통해 인터넷에 접속한다고 생각했어!

아이고, 미친 소리 하네!?

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

세상에서 왜 거의 모든 사람들이 모바일 장치를 선호하는지 궁금하시죠?

음, 사실 이 질문에 대해 너무 걱정할 필요는 없어요. 그저 그런 것이니까요. 하지만 그들의 삶을 더 쉽게 만들고 반응형 디자인을 진지하게 다루는 법에 대해 관심을 가지셔야 해요!

제가 그 방법을 보여드리겠어요. 간단하고 쉽고 빠르죠!

시작해볼까요?

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

# 소개

반응형 디자인은 사용자가 다양한 기기에서 훌륭한 경험을 할 수 있어야 한다는 것을 의미합니다.

차이가 무엇인가요?

물론, 컴퓨팅 성능입니다. 하지만 요즘에는 중급 폰조차도 웹사이트의 계산이 중요하지 않을 정도로 많은 성능을 가지고 있습니다.

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

중요한 것은 화면 크기입니다.

![Responsive Design](/assets/img/2024-06-30-ResponsiveDesignbyDefault_2.png)

핸드폰에서 멋지게 보입니다.

표시된 크기는 iPhone 12 Pro용입니다.

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

# 코드의 길

오케이, 파티에 늦었을 수도 있지만, 제가 가장 사랑하고 감사히 여기는 것은: tailwindcss입니다.

이 기사는 빠르고 효율적이며 쉽게 읽기 쉽도록 작성되었으니, 코드와 설명으로 바로 들어가 보겠습니다. 간결하게 유지하겠습니다!

```js
```
 <div className="flex
 flex-col
 md:flex-row
 flex-wrap
 items-center
 space-y-2
 md:space-y-0
  md:space-x-3 ">
 <span className="text-purple-500 border border-purple-400 rounded-full px-2 py-1 shadow-sm shadow-purple-300">Required TH: 16 </span>
 <span className="text-purple-700 border border-purple-600 rounded-full px-2 py-1 shadow-sm shadow-purple-400">Required TH Trophies: 3000 </span>

 <span className="text-blue-500 border border-blue-400 rounded-full px-2 py-1 shadow-sm shadow-blue-300">Required BH: 15 </span>
 <span className="text-blue-700 border border-blue-600 rounded-full px-2 py-1 shadow-sm shadow-blue-400">Required BH Trohpies: 3000 </span>

 <span className="text-green-600 border border-green-400 rounded-full px-2 py-1 shadow-sm shadow-green-500">Members: 39 </span>
</div>


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

여기서 무엇을 하고 있나요?

만약 tailwindcss 확장 기능을 가지고 있다면, 이러한 모든 것들은 의미가 있고 그 뒤에 숨어있는 css 속성을 보여줄 것입니다.

확장 기능에 따라, hover 상태에서 다음과 같이 보일 것입니다:

![Responsive Design by Default](/assets/img/2024-06-30-ResponsiveDesignbyDefault_3.png)

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

정말 멋지죠?

이전 내용으로 돌아가봐요.

여기 tailwindcss의 반응형 디자인에 관한 글이 있어요:

보통 그들은 미리 작성된 지시사항을 함께 제공해요.

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

제가 위에서 보여준 내용 중에는 다음과 같은 것이 있어요:

```js
md:flex-row
```

이것은 중간 크기 이상의 기기에서 유연한 방향이 변경될 것을 의미합니다.

매우 중요한 점은 이것을 변경할 수 있다는 점이지만 일반적으로 특정 픽셀 이상에서 특정한 디자인이 나타난다는 것을 알아야 합니다.

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

저의 사용하는 꿀팁은 항상 핸드폰용으로 먼저 만들고 나서 PC용으로 꾸미는 것입니다!

예시:

PC에서는 이렇게 보입니다 (md:flex-row - 768px 이상)

![Responsive Design by Default](/assets/img/2024-06-30-ResponsiveDesignbyDefault_4.png)

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

이것이 핸드폰에서 보이는 방식입니다

![Responsive Design](/assets/img/2024-06-30-ResponsiveDesignbyDefault_5.png)

```js
<div className="flex flex-col md:flex-row flex-wrap items-center space-y-2 md:space-y-0  md:space-x-3 ">
```

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

여기 있어요!

제 약속을 지켰어요. 간결하고 빠르며, 읽기 쉬울 거에요!

반응형 디자인은 매우 중요하고 사용자 요구 사항에 추가로 신경 써야 해요!

함께 해 주셔서 감사해요! 다음에 또 만나요!

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

행복한 코딩하세요, 엔지니어 여러분!

안전하고 건강하게 지내세요! 잘가 🚓