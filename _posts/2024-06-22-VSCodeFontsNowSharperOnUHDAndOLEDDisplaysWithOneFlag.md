---
title: "VSCode의 글꼴이 UHD 및 OLED 디스플레이에서 더 선명해진 방법 한 가지 설정으로 해결"
description: ""
coverImage: "/assets/img/2024-06-22-VSCodeFontsNowSharperOnUHDAndOLEDDisplaysWithOneFlag_0.png"
date: 2024-06-22 22:48
ogImage:
  url: /assets/img/2024-06-22-VSCodeFontsNowSharperOnUHDAndOLEDDisplaysWithOneFlag_0.png
tag: Tech
originalTitle: "VSCode Fonts Now Sharper On UHD And OLED Displays With One Flag"
link: "https://medium.com/@tomaszs2/vscode-now-sharper-on-uhd-and-oled-displays-5dd655e02632"
---

<img src="/assets/img/2024-06-22-VSCodeFontsNowSharperOnUHDAndOLEDDisplaysWithOneFlag_0.png" />

# 마침내, OLED 및 UHD 모니터에서 VSCode 글꼴이 흔들리는 문제를 해결할 수 있게 되었습니다!

# 새로운 플래그

일반적으로 VSCode의 개선 사항은 생산성, 개발자 경험 및 Copilot과 같은 통합과 같은 개발자 도구를 중심으로합니다.

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

하지만 항상 그렇지만은 않고 때로는 변화가 상당히 놀라울 수 있어요.

2024년 5월 버전의 개선 목록 중에서 여러 탭을 동시에 선택할 수 있는 기능, 프로필, 에디터 창 작업 아이콘을 항상 표시할 수 있는 기능 또는 Copilot 대답에 대한 텍스트 음성 변환 기능을 발견할 수 있어요.

그 중에서도 가장 흥미로운 것은 "Set disable-lcd-text as a runtime argument"라는 수수께끼 같은 제목으로 가능한 개선이에요.

간단한 설명에서는 다음과 같은 것을 배울 수 있어요:

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

Illustration을 확인해보세요:

![image](/assets/img/2024-06-22-VSCodeFontsNowSharperOnUHDAndOLEDDisplaysWithOneFlag_1.png)

왼쪽에 보면 플래그가 true로 설정된 버전이 있습니다. 이는 LCD 텍스트가 비활성화됨을 의미합니다.

보이는 바에 따르면, 그 버전은 UHD 모니터를 사용하는 몇몇 개발자들, Retina 디스플레이를 사용하는 Mac 노트북 사용자들을 포함한 다른 많은 사람들에게 더 나은 선택이 될 것 같습니다.

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

하지만 모든 사람이 화면에서 변경 사항을 보지 못할 수도 있고, 모든 사람이 그것을 좋아하지도 않을 것입니다.

# 서브픽셀의 폰트가 개선된 이유

하지만 이것이 정말 어떤 것인가요? 우리가 비활성화해야 하는 LCD 텍스트 기능은 무엇일까요?

이것을 이해하려면 90년대로 돌아가 봐야 합니다. 사람들이 점점 더 컴퓨터를 사용하기 시작했습니다. 운영 체제 개발 회사들은 소비자 시장에 더 집중하기로 결정했습니다. 이를 위해 최상의 사용자 경험을 제공해야 했습니다.

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

온라인 비디오 플랫폼이 없었기 때문에 사람들은 800x600 픽셀 모니터에서 텍스트를 작성하고 마인스위퍼를 플레이하던 컴퓨터를 주로 사용했어요.

해당 해상도의 한계는 픽셀이 일론 머스크의 자아보다 더 크다는 것이에요. 그래서 눈으로 그들을 볼 수 있었어요.

그 중 가장 피해를 입은 것은 텍스트였어요. 그 이유가 뭘까요? 사용자 인터페이스를 구축하고 해상도가 무엇인지 알고 있다면 이를 조절하여 멋지게 보이도록 조정할 수 있어요. 이런 경우에는 픽셀 아트처럼 해상도의 제한과 사용자 인식을 고려하여 아이콘과 인터페이스를 픽셀 단위로 그려 조정하는 것을 뜻해요.

하지만 텍스트의 경우는 그렇게 쉽지 않아요. 다른 요소들과 비교했을 때 글자와 기호들은 매우 구체적인 구조를 가지고 있어요. 그것들을 많이 변경할 수 없는 이유는 더 이상 가독성이 없어지거나 다른 글자와 유사해질 수 있기 때문이에요. 예를 들어, 글자 O와 숫자 0은 구분되어야 하므로 약간 다르게 보이도록 해야 해요.

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

작은 해상도에서는 정말로 어려워요.

그 문제를 해결하고 나니, 90년대에 또 다른 문제가 뚜렷해졌어요. 폰트가 정말로 못생겼어요.

폰트는 날카롭고 화소로 이루어져 있었죠. 그래서 작은 폰트는 읽기 어렵고 큰 폰트는 마인크래프트 같이 보였어요.

소비자들이 좋아하지 않는 것이었죠. 그래서 그 시기에는 물리적 해상도를 늘릴 수 없었기 때문에 많은 해결 방안이 제안되었어요.

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

# 안티 앨리어싱으로 구제!

이 문제 중 하나는 안티 앨리어싱입니다. 흰 배경에 검은 텍스트가 있는 경우, 회색 픽셀로 모서리를 부드럽게 만들어 줍니다. 그렇게 하면 확대할 때 글꼴이 덜 날카롭지만 부드럽고 픽셀화가 줄어듭니다:

![이미지](/assets/img/2024-06-22-VSCodeFontsNowSharperOnUHDAndOLEDDisplaysWithOneFlag_2.png)

이 방법은 픽셀 아트에서 디자인을 부드럽게 만드는 데에도 사용됩니다.

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

하지만 글꼴의 경우는 세부 사항 때문에 일반적인 안티앨리어싱만으로는 충분하지 않았습니다. 그래서 1990년대에 Microsoft와 Apple은 유사한 솔루션을 도입했습니다.

Windows의 경우, ClearType이 그것입니다. 화면에 표시되는 텍스트의 표시 방법을 개선하는 세트의 방법입니다.

그러나 일반적인 안티앨리어싱과 비교하여 ClearType은 서브픽셀에서 작동합니다.

화면에서 본 픽셀은 사실 빨강, 녹색 및 파랑(RGB)과 같은 서로 가깝게 배치된 서로 다른 색상의 서브픽셀입니다.

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

이 서블 픽셀의 위치를 알고 있다면 LCD 스크린에 대해 90년대에 알려졌듯이 이 서블 픽셀을 사용하여 글꼴의 안티 앨리어싱을 할 수 있습니다. 이렇게 하면 이웃하는 서블 픽셀들이 부드럽게 변할 수 있는 더 세련된 효과를 낼 수 있습니다.

연구 결과를 보면 ClearType를 활성화한 상태에서 텍스트를 읽는 것이 더 좋다고 한 사람들이 많았습니다. 그러나 90년대에는 시력이 좋은 사람들조차도 이 방식을 짜증냈다고 합니다.

폰트가 서로 닮아보이기 시작하고 독특한 특징을 잃게 되었다고 보고했으며, 어떤 디스플레이에서는 이 방식으로 인해 흐릿하고 붉은 블루 하로가 나타나 짜증이 났다고 합니다.

그럼에도 불구하고 모든 사람들이 만족했습니다. ClearType를 비활성화할 수 있었기 때문이죠.

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

# 새로운 디스플레이로 LCD 최적화 방법이 오래되었습니다

2024년 상황이 달라졌어요. 실제로 지금은 서브픽셀이 어떻게 배치되는지에 대한 규칙이 없어졌어요. 점점 더 많은 디스플레이가 삼각형 구성 또는 다른 유형의 배치를 사용하고 있어요.

이것은 폰트 최적화가 적용되었을 때 텍스트가 더 신기해진다는 것을 의미해요. 왜냐하면 실제 서브픽셀의 위치와는 다른 곳을 가정하기 때문이죠. 결과적으로 이상한 효과가 발생하는데요, 심지어 보다 더 눈에 띄는 빨간색과 파란색의 이동 및 가장자리의 선이 현상되는 효과도 있어요.

예를 들면 이렇게요:

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

![2024-06-22-VSCodeFontsNowSharperOnUHDAndOLEDDisplaysWithOneFlag_3.png](/assets/img/2024-06-22-VSCodeFontsNowSharperOnUHDAndOLEDDisplaysWithOneFlag_3.png)

OLED 및 다른 비-LCD 장치에서 특히 선명해졌어요.

시스템 UI의 ClearType를 제어할 수 있지만, 앱에서 변경할 지 어떻게 변경할 지를 결정하는 것은 앱 개발자에 따라 달라요.

예를 들어, 많은 브라우저에서는 어떤 종류의 글꼴 평활화를 적용할 지를 사용자가 결정할 수 있어요. 이 옵션은 웹 앱이 액세스할 수 없는 많은 변수(하위화소 레이아웃과 같은)에 따라 결정해야 하는 매우 특정한 경우에만 사용되어야 해요.

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

만일 VSCode에 대해 구체적으로 언급한다면, 이 IDE는 TypeScript로 작성되었고 Electron과 Chromium을 사용합니다.

그리고 Chromium은 LCD 스크린을 위한 최적화 비활성화를 위한 플래그를 제공하지만, VSCode에서는 이를 노출하지 않았습니다.

2024년 5월 릴리스로 인해 가능해졌습니다. 그래서 고해상도 스크린, OLED 또는 다른 스크린을 사용하시는 경우, 글꼴 최적화를 비활성화하는 것이 글꼴 표시 방법을 향상시킬 수 있는지 확인할 수 있습니다.

고해상도 스크린의 경우 글꼴 부드럽게 처리는 필수가 아닙니다. 높은 픽셀 밀도는 서브픽셀 최적화를 점점 더 사용하지 않게 만듭니다.
