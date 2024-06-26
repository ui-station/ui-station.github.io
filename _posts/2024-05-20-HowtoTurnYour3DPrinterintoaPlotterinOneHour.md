---
title: "1시간 안에 3D 프린터를 플로터로 변환하는 방법"
description: ""
coverImage: "/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_0.png"
date: 2024-05-20 19:25
ogImage:
  url: /assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_0.png
tag: Tech
originalTitle: "How to Turn Your 3D Printer into a Plotter in One Hour"
link: "https://medium.com/@urish/how-to-turn-your-3d-printer-into-a-plotter-in-one-hour-d6fe14559f1a"
---

## 펜, 색연필, 크레용 등 종이에 그리고 쓸 수 있도록 3D 프린터에게 가르치기! 🖍

플로터를 만드는 것이 오래전부터 제 리스트에 있었어요. 플로터는 펜을 사용해 종이에 텍스트와 그림을 그릴 수 있는 장치에요. 몇 주 전, 이스라엘 메이커들이 오래된 CD 드라이브로 작은 플로터를 만든 뒤, 제가 직접 플로터를 만드는 것이 이제는 될 때라고 결심했어요.

하드웨어를 계획하던 중 갑자기 제 3D 프린터에 펜을 부착하고 그리라는 방법만 찾으면 되고 필요한 모든 하드웨어와 전자기기를 이미 포함하고 있다는 좋은 속임수가 있을 수 있음을 깨달았어요.

한 시간 뒤에 작동하는 시제품을 만들었어요. 처음에 예상했던 것보다 훨씬 쉬웠어요. 이 블로그 포스트에서 여러분도 여러분만의 프린터를 그리게 할 수 있는 방법을 배울 수 있게 될 거예요.

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

![이미지](/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_0.png)

## 3D 프린터를 2D 프린터로 변환하기 🖨

3D 프린터를 종이 위에 2D 그림을 그릴 수 있는 것으로 변환하는 것에 대해 궁금할 수 있습니다. 결국, 기존 프린터는 이미 몇십 년 동안 그 일을 수행하고 있습니다. 이게 역행이 아닌가요?

먼저, 내가 만드는 모든 것이 유용해야 한다고 한 사람이 누구인가요?

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

두 번째로, 플로터를 사용하면 펜, 색연필, 크레용, 마커, 심지 심지를 이용해 그리거나 심지를 사용할 수 있습니다. 실제로 종이에 흔적을 남길 수 있는 모든 것이 사용될 수 있습니다. 다른 재료에도 그릴 수 있으며, 예를 들어 천판이나 유리에 그리기도 가능합니다. 또한 황금, 은, 혹은 암흑 속에서 빛나는 것과 같이 독특한 잉크 종류로 창의적으로 활용할 수도 있습니다.

## 단계 1 — 펜 부착

프린터 머리에 펜을 안정적으로 부착하고, 펜의 끝이 노즐 아래로 조금 들어가도록 보장하고 싶을 겁니다. 제가 이를 위해 디자인한 작은 부품을 사용해 시작했습니다. 이 부품은 프린터 머리에 클립으로 고정되며, 작은 M3 나사를 사용해 펜을 고정합니다:

<img src="/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_1.png" />

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

모델은 여기서 구할 수 있어요. Creality Ender-3 또는 CR-20 프린터에 맞을 것이라고 생각되며, 다른 Creality 프린터에도 사용할 수 있을 겁니다. 또한 사용자 정의가 가능하기 때문에, 큰 마커를 맞추거나 프린터와 더 밀착되도록 조정할 수 있어요.

다른 프린터를 위해서는 자체 마운팅 메커니즘을 설계해야 해요 (만약 필요하면, 댓글에 링크를 공유해주세요). 펜을 마운트하기 위해 나사를 사용하는 것도 가능하지만, 저는 개인적으로 클리핑 메커니즘을 선호해요. 이렇게 하면 3D 프린터와 플로터 모드를 빠르게 전환할 수 있을 뿐만 아니라 펜을 빠르게 교체할 수 있기 때문이에요.

## 단계 2 — 캘리브레이션

프린터에 펜을 성공적으로 장착했다면, 이제 캘리브레이션을 해보아야 해요. 프린터 베드에 종이를 부착해야 할 거에요:

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

![plotter](/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_2.png)

다음으로 인쇄 높이를 올바르게 설정해야 합니다. 이를 위해 이 튜토리얼에서는 Repetier Host를 사용하는 것을 권장합니다. (처음에는 Cura를 사용했지만, Repetier Host가 모든 것을 훨씬 쉽게 만들어 주었기 때문에 이 튜토리얼에서는 계속 사용하겠습니다).

시작하기 전에 프린터의 베드가 수평이고 노즐과 프린터 베드 모두 식어있는지 확인하세요.

프린터를 홈 위치로 이동한 다음 Repetier 내의 "Manual Control" 탭으로 이동하여 인쇄 헤드를 위로 이동시켜 펜 끝이 종이 위에 올라가게 합니다. 그런 다음 X / Y 축을 그림을 그리려는 위치의 가장자리로 이동시킵니다. 마지막으로 Z 축을 0.1mm 간격으로 내려가며 펜 끝이 종이에 닿을 때까지 이동합니다. 그럼 X/Y를 조금 이동시켜 펜이 실제로 종이에 흔적을 남기는지 확인하세요. 완료되면 상단 라인에 표시된 X/Y/Z 값을 메모해 두십시오:

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

![이미지](/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_3.png)

나의 경우에는 X에 47, Y에 40, Z에 14.6의 값을 넣었습니다. 이 값들은 곧 생성할 GCode 파일을 인쇄할 때 사용할 것입니다.

## 단계 3— 무엇을 인쇄할지 선택하기

이게 어려운 문제에요. 옵션이 너무 많죠. 그러나 이것을 벡터 형식으로 얻어야 해요. Google 이미지를 사용하면 검색 쿼리 끝에 type:svg를 추가해야 합니다. 또한 JPEG와 PNG 이미지를 SVG로 변환할 수 있지만, 일단 벡터로 제공되는 이미지부터 시작하는 것이 좋습니다.

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

## 단계 4 - GCode 생성

이 프로젝트를 시작할 때 하드웨어를 맞추는 데 몇 시간을 소비할 것으로 생각했었어요. 하드웨어 부분이 얼마나 빨리 작동하게 되었는지 놀랐어요. 그러나 소프트웨어는 완전히 다른 이야기였어요 - 언제나 그렇듯이, 실제 복잡성이 있는 곳이 바로 소프트웨어네요.

수십 시간의 연구 끝에, 신뢰할 만한 작업 흐름을 찾았어요. 그러나 설정은 필요합니다. 위에서 언급된 Repetier Host 외에도 PCB 아트를 만들고 싶다면 유용한 Inkscape도 설치해야 합니다!

Inkscape 안에서 새 파일을 만든 후, 파일 메뉴 -> 문서 속성(단축키: Ctrl+Shift+D)로 이동하세요. 그런 다음 문서 크기를 프린트 베드 크기보다 작게 설정하고, 단위로 mm을 사용해야 합니다.

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

![How to Turn Your 3D Printer into a Plotter - Step 4](/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_4.png)

After setting the desired size, you can either import an SVG file of your choice or use the Text tool to draw text:

![How to Turn Your 3D Printer into a Plotter - Step 5](/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_5.png)

Once you are finished, make sure your text is still selected, then go to Path menu → Object to Path (Shift+Ctrl+C). This action will transform the text into a sequence of points linked by lines, which is necessary for the printer input. You can include additional elements like spirals and star shapes, repeating the "Object to Path" step for each element:

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

![이미지](/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_6.png)

인쇄할 때, 객체가 채워지지 않으므로 채우기 색상을 제거하고 펜의 색상(또는 원하는 색상)을 검정색으로 설정하여 최종 결과물을 더 정확하게 확인할 수 있습니다. 모든 객체를 선택(Ctrl+A)한 후에 채우기 제거하고 선 색상을 검정색으로 설정하세요(Ctrl+Shift+F):

![이미지](/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_7.png)

결과물에 만족하면 프린터용 G코드를 생성할 차례입니다! "Gcodetools"라는 확장 기능을 사용할 것인데, 이는 Inkscape에 번들로 제공됩니다 (그렇지 않은 경우, 더 오래된 버전을 사용 중이므로 업그레이드해야 합니다).

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

우선적으로 프린터가 화면 상의 선들을 종이 위에 어떻게 매핑할 지를 알려주는 Orientation Points를 정의하겠습니다. Extensions Menu로 이동하시고 → Gcodetools → Orientation Points...를 선택한 다음 “2-point” 모드가 선택되어 있는지 확인한 후 Apply를 클릭한 후 Close를 클릭해주세요. 이제 드로잉 하단에 두 개의 새로운 텍스트 요소가 추가된 것을 보실 수 있을 것입니다:

<img src="/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_8.png" />

이것이 Orientation Points입니다. 각 점은 프린터의 좌표 시스템에서 해당 지점의 목표 위치를 지정하는 X, Y, Z 좌표 리스트입니다. 이를 균정 단계에서 찾은 X / Y와 일치시키고 Z (세 번째 좌표)를 0으로 설정해야 합니다.

왼쪽 점의 텍스트를 편집하고 찾은 X / Y 좌표를 업데이트하세요. 저의 경우, (47; 40; 0)였습니다. 오른쪽 점에 대해서는 X 값에 100을 더하고, 첫 번째 점에서 Y/Z를 복사하여 추가해주세요. 예를 들어 (147; 40; 0)과 같이요:

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

![이미지](/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_9.png)

다음으로, 우리는 도구를 생성하고 속도를 구성해야 합니다. 이 단계는 선택 사항이지만, 이를 수행하지 않으면 프린터가 정말 매우 느리게 그릴 것입니다. 확장 메뉴로 이동하여 → Gcodetools → 도구 라이브러리...를 선택하고 "도구 유형"에서 "기본"을 선택하십시오.

![이미지](/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_10.png)

적용을 클릭한 다음 닫기를 클릭하면 그림에 많은 설정이 추가된 초록색 직사각형이 표시됩니다.

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

![이미지](/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_11.png)

이 직사각형을 이동시켜서 (모든 값과 함께) 그림 위에 오지 않게 할 수 있어요. 그런 다음, 텍스트를 편집하고 "피드", "침투 피드", "통행 피드" 값들을 변경하여 프린터가 그림을 그릴 때의 이동 속도를 설정하면 돼요. 저는 이들 값에 모두 4500을 사용해요 (단위는 mm/min이에요, 그래서 이 값은 75 mm/sec에 해당돼요).

![이미지](/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_12.png)

이제 G코드를 생성할 준비가 됐어요! 그림에서 모든 요소를 선택한 후 (Ctrl+A), Extensions → Gcodetools → Path to Gcode...로 이동하세요.

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

거기, Options 탭으로 이동해서 "Z 축을 따라 확대"를 1로 설정하고, "Z 축을 따라 오프셋"을 교정 단계에서 찾은 Z 값에서 1을 뺀 값으로 설정하세요 (저는 14.6을 찾았으므로 여기서 13.6으로 설정합니다):

![이미지](/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_13.png)

그런 다음, Preferences 탭으로 이동해서 출력 파일의 이름과 저장하고 싶은 디렉토리의 경로를 설정하세요. 프린트 속도를 높이려면 Z 안전 높이 값을 더 낮은 값으로 설정할 수도 있습니다 (저는 5를 사용합니다):

![이미지](/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_14.png)

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

마지막으로 Path to Gcode 탭으로 전환한 후, Depth Function을 1로 설정하고 Apply를 클릭하세요. 몇 초 정도 걸릴 것이며, 선택된 경로가 없다는 경고가 표시될 수 있지만 무시해도 안전합니다. 생성된 Gcode 파일에서 프린트 헤드의 이동을 보여주는 새로운 레이어가 그림 위에 표시될 것입니다.

이 시점에서 .gcode 파일을 텍스트 편집기에서 열어서 Z 값이 캘리브레이션 값과 일치하는지 확인하는 것을 권장합니다.여기에 사용된 이미지를 확인하세요.

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

첫 번째 G00 라인을 편집하고 맨 끝에 F4500을 추가하는 것을 제안합니다. 그렇지 않으면 프린터가 초기 헤드 이동을 매우 느리게 할 수 있습니다:

![image](/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_17.png)

이게 다입니다! 이제 인쇄할 준비가 되었습니다. Repetier 호스트에서 Gcode 파일을 로드하고 화면에 그림을 볼 수 있어야 합니다:

![image](/assets/img/2024-05-20-HowtoTurnYour3DPrinterintoaPlotterinOneHour_18.png)

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

기도를 드리고, "인쇄 시작" 버튼을 클릭하면... 쇼를 즐기세요!

## 축하합니다! 🎉

이제 3D 프린터는 플로터로도 사용할 수 있습니다. Gcodetools의 다양한 설정으로 실험을 할 수도 있어요 — 예를 들어 선의 색에 따라 펜의 높이를 변경하도록 구성하여 플로터가 더 얇거나 더 두꺼운 선을 그리도록 설정할 수 있어요. 또한 "Area" 기능을 사용하여 외곽선만 그리는 대신 모양을 채울 수도 있습니다. ⭐

계속해서 실험을 진행하고 다양한 종류의 펜과 재료를 시도해보세요. 그리고 결과물을 댓글로 공유해주세요 — 얼마나 발전시킬 수 있는지 궁금해요!

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

이제 10월 동안 매일 새로운 글을 쓰는 "포스트버 챌린지"의 다섯 번째 포스트를 올린 거야.

새로운 포스트를 올릴 때마다 트윗할 거니까 약속할게! ✍
