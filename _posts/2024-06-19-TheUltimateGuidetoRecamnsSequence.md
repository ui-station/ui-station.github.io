---
title: "레카만 수열의 궁극적인 안내"
description: ""
coverImage: "/assets/img/2024-06-19-TheUltimateGuidetoRecamnsSequence_0.png"
date: 2024-06-19 10:42
ogImage:
  url: /assets/img/2024-06-19-TheUltimateGuidetoRecamnsSequence_0.png
tag: Tech
originalTitle: "The Ultimate Guide to Recamán’s Sequence"
link: "https://medium.com/cantors-paradise/the-ultimate-guide-to-recam%C3%A1ns-sequence-874cdbb28a4a"
---

“단순함은 궁극의 세련미이다.” — 레오나르도 다빈치 (1452–1519)

많은 해 동안 나는 음악과 수학이 교차하는 부분에 매료되어 왔어요. 과학 아메리칸지 1985년에 발표된 만델브로 패턴에 대한 표지 이야기를 읽은 이후로, 수학을 음악으로 나타내는 방법에 대해 고민했습니다. 이제는 음악에서 수학적 패턴을 듣고 숫자의 패턴에서 발생할 수 있는 소리를 상상하는 것이 자연스러워졌어요.

가끔 Numberphile 비디오를 확인하고 최근 Neil Sloane이 나온 영상을 보았어요. 그는 브리티시-아메리칸 수학자로, 정수 수열 온라인 백과사전(OEIS)의 창시자로 가장 잘 알려져 있어요. 특정 주제를 기억하지는 못하지만, 그는 비디오에서 OEIS에서 가장 음악적인 소리가 나는 수열은 Recamán의 수열일 수도 있다고 언급했어요. 저는 이 수열에 대해 들어본 적이 없었고, 궁금해졌어요.

## 수열

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

Recaman 시퀀스를 생성하는 규칙은 간단해요:

![image](/assets/img/2024-06-19-TheUltimateGuidetoRecamnsSequence_0.png)

다시 말해, 우리는 항상 인덱스 n을 시퀀스의 이전 값에서 빼려고 노력해요. 결과가 음수이거나 이미 시퀀스에 나타난 경우, n을 더해요. 실제로 이 작동 방식을 보려면 처음 몇 개의 항을 계산해 보죠:

- a₀ = 0
- a₁ = 0 + 1 = 1 (1-1 = 0이 이미 시퀀스에 있으므로 0에 1을 더해요)
- a₂ = 1 + 2 = 3 (1-2 = -1이 음수이므로 1에 2를 더해요)
- a₃ = 3 + 3 = 6 (3-3 = 0이 이미 시퀀스에 있으므로 3에 3을 더해요)
- a₄ = 6-4 = 2
- a₅ = 2 + 5 = 7

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

이 시퀀스는 다음과 같이 시작합니다:

'0, 1, 3, 6, 2, 7, 13, 20, 12, 21, 11, 22, 10, 23, 9, 24, 8, 25, 43, 62, 42, 63, 41, ...'.

처음 100개 값의 플롯은 다음과 같습니다:

![Plot](/assets/img/2024-06-19-TheUltimateGuidetoRecamnsSequence_1.png)

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

다음은 처음 1000개의 값입니다:

![image](/assets/img/2024-06-19-TheUltimateGuidetoRecamnsSequence_2.png)

위에서 본 이어진 플롯 형식 위에 각 점을 겹쳐 놓은 결과는 처음 24,000개의 항에 대해 다음과 같습니다:

![image](/assets/img/2024-06-19-TheUltimateGuidetoRecamnsSequence_3.png)

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

이제 우리는 번갈아 가면서 짝수 또는 홀수 인덱스의 집합에 해당하는 기욕한 수평 선들을 볼 수 있습니다. 이웃한 요소들의 집합이 1씩 증가하거나 감소하는 것처럼 보입니다.

전체적으로, 이 시퀀스는 상대적으로 순서가 있는 기간들 사이에 큰 도약이 뒤섞인 예측 불가능한 과정을 보여줍니다. 여기에서 무언가 흥미로운 일이 일어나고 있음은 분명합니다.

1991년, 콜롬비아 수학자인 베르나르도 레카만 산토스가 이 신기한 시퀀스를 Neil Sloane에게 보내어 Neil Sloane이 이를 매우 흥미롭다고 생각하여 OEIS에 추가하는 것 외에도, 레카만의 이름을 따라 이름 붙였습니다. 현재 OEIS 로고에는 처음 14개 용어가 나타납니다.

그래서 무슨 소란일까요? 일단, 이에 대해 수론적 측면에서는 매우 적게 알려져 있습니다. Sloane은 모든 숫자가 결국 나타나리라는 추측을 했습니다. 그러나 2018년, Benjamin Chaffin은 처음 10²³⁰개 항을 계산했을 때 숫자 852,655가 아직 나타나지 않았습니다! 실제로 그것만이 결여된 것은 아닙니다. 그 범위 내에서는 많은 다른 상대적으로 작은 결여된 숫자들이 있습니다. 또한, 시퀀스가 비단사임을 알고 있습니다 — 첫 반복 값은 a₂₄ = a₂₀ = 42에서 발생합니다.

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

## 시퀀스 시각화

영국의 수학자이자 예술가인 Edmund Harriss는 숫자 패턴을 시각화하는 멋진 방법을 고안했습니다. 인접한 용어를 연결하여 반원을 사용하여 숫자 패턴을 시각화했습니다. 여기 그의 원본 방법의 컬러 버전이 100개의 항을 보여줍니다:

![image](/assets/img/2024-06-19-TheUltimateGuidetoRecamnsSequence_4.png)

자체적으로 시각화를 탐색하고 싶다면, 위 이미지에 대한 간단한 의사 코드가 여기에 있습니다:

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
# 길이 n의 Recaman 시퀀스에 대해:

# 플롯 범위 정의
plotRange = {-5, 235}, {-60, 60}

# 종횡비 계산
aspRat = y 범위를 x 범위로 나눈 값

# 프레임 함수 정의
function frame(i):
    # 호를 보관할 빈 리스트 초기화
    arcs = []

    # 인덱스 i까지 Recamán 시퀀스를 반복
    for j from 1 to i:
        # 호의 중심과 반지름 계산
        center = (recamanSequence[j + 1] + recamanSequence[j]) / 2
        radius = recamanSequence[j + 1]과 recamanSequence[j]의 차이의 절댓값 / 2

        # j가 짝수인지 홀수인지에 따라 시작 및 끝 각도 결정
        if j가 짝수이면:
            startAngle = 0
            endAngle = Pi
        else:
            startAngle = Pi
            endAngle = 2 * Pi

        # 특정 색상으로 호를 리스트에 추가
        arcs.append({j를 n으로 나눈 색조, 중심, 반지름, 시작 각도 및 끝 각도로 이루어진 호})

    # 배경을 검은색으로, 종횡비->aspRat, 축 끄기, 플롯 범위->plotRange 및 이미지 크기->800으로 설정하여 화형 표현 생성 및 반환
    return 호에 대한 그래픽스를 사용합니다. arcs, 배경은 검은색이고, 종횡비->aspRat, 축 끄기, 플롯 범위->plotRange, 이미지 크기->800으로

# n - 1을 인자로 하여 프레임 함수 호출
frame(n - 1)
```

Mathematica를 사용한다면, 이 준비된 노트북을 사용할 수 있습니다. 시각화에 대한 많은 다른 접근 방식도 여기에서 찾을 수 있습니다.

## 시퀀스에 음악을 불어넣기

이제 시퀀스의 행동에 대해 알았으니, 그것이 어떤 소리를 내는지 알아봅시다. 먼저, 무서운 면을 처리하겠습니다.

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

아래는 OEIS "Listen" 페이지에서 직접 취한 처음 2048개 용어의 오디오입니다. 악기는 제한된 음역대를 가지고 있기 때문에 용어는 88으로 나눈 나머지로 변환됩니다(MIDI 값은 숫자를 88로 나눈 나머지가 재생됩니다). OEIS 페이지에서 음의 높낮이 모듈러스를 변경할 수 있습니다. 제가 당신에게 제안하는 닐 슬론의 의견처럼 소리에 가장 가까운 것을 사용하여 MIDI 파일을 렌더링했습니다.

아마도 당신의 수면 중 배경음악을 대체하지는 못할 것입니다. 그러나 동시에 상승하고 하강하는 듯한 명확하고 음악적으로 흥미로운 습관이 있습니다. 다음은 처음 60개 마디를 보여주며 이 반대 운동을 보여주는 피아노 롤 뷰입니다:

이를 염두에 두고 내재적인 음악성을 보다 광범위한 청중(아니면 적어도 제게)에게 더 수용 가능한 방식으로 제시하기 위해 어떻게 하면 좋을지 고민했습니다. "좀 무섭다" 버전은 단음계에 매핑되어 있습니다 — 서양 음계의 열두음계에서 모든 음이 나타날 수 있습니다. 그러나 다른 매핑도 쉽게 구현할 수 있습니다.

먼저, 96(8개 옥타브 전체)의 음의 높낮이를 사용하여 순서에 조금 더 여유를 주기로 결정했습니다. 여러 음계 선택을 실험한 후, 사이에 플랫된 다섯번째 음이 포함된 헥사토닉 블루스 음계로 결정했습니다. 또한 값이 0(mod 10)인 모든 음표를 쉼표로 변환하여 일렬을 주도하는 리듬을 추가하기로 결정했습니다.

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

블루스 스케일이 작동하는 것 같아서, 12마디 블루스의 변형을 위해 화성을 나타내는 무난한 베이스 라인을 추가했어요. 마침내 어떤 종류의 퍼커션을 원했는데, 조각스럽고 독특한 이 작품의 성질 때문에 보완적인 그루브를 찾는 것이 어려웠어요. 2번째 코러스의 시작에서 베이스와 함께 시작하는 아프리카 드럼을 선택했어요.

이 맥락에서 종합적인 효과는 때때로 "콜 앤 레스폰스"라고 알려진 작곡 기술과 공감된다는 게 흥미롭네요. 여기서 한 목소리가 음악 구절 형식의 선언을 하고, 다른 목소리가 그 선언을 완성하는 형식의 답변을 제공하는 거죠. 이상한 점은, 이 경우에는 자신과 대화하는 수수께끼 같은 순서라는 거예요.

그렇다면, 여기가 Recamán의 블루스입니다:

이제 순서를 구성하고 시각화하고 음성으로 변환하는 방법을 알았으니, 마지막으로 하나만 남았습니다 — 애니메이션으로 이를 함께 조합하는 거죠. 길이가 n인 순서에 대한 과정입니다:

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

- 위의 코드를 사용하여 n개의 프레임을 생성합니다.
- 우리가 선택한 modulus를 사용하여 노트 범위를 설정합니다.
- 줄어든 시퀀스를 직접 (자세한 자료 참조) 또는 MIDI를 통해 (이후 오디오로 변환) 음직화합니다.
- 오디오의 길이로 n을 나누어 프레임 속도를 계산합니다.
- 계산된 프레임 속도를 사용하여 결합된 프레임을 비디오로 변환합니다.
- 비디오와 오디오를 동기화합니다.

저의 경우, Logic X를 사용하여 오디오를 정렬하고 렌더링하고, 프레임을 생성하고 비디오를 렌더링하며 파일을 동기화하기 위해 외부 프로세스 FFmpeg를 실행하는 Mathematica를 사용했습니다.

이와 같이, 음높이 modulus를 사용해야 하는 필요성으로 인해 순환열베에 사용하는 음향과 시각적인 적절한 상관관계를 파괴하는 것을 발견했습니다. 따라서 연속적인 음높이 값 사이의 평균에 매핑된 직경을 사용하여 실제 음높이 범위를 기반으로 애니메이션을 만들었습니다.

결과는 다음과 같습니다:

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

레캄킨의 수열은 여전히 대부분 미스터리한 상태입니다. 이것은 다양한 변형을 영감을 주었고 스테가노그래피에 응용될 수 있음을 발견했습니다. 아직 발견되지 않은 것들이 있더라도, 로지스틱 맵, 만델브로 집합, 그리고 세포 자동기와 같이 간단한 수학 규칙이 아름답고 예상치 못한 풍부한 패턴으로 이어질 수 있는 훌륭한 예시라고 할 수 있습니다.

읽어 주셔서 감사합니다! 만약 이 글을 즐겁게 읽으셨다면, 저를 팔로우하고 상단의 "박수" 아이콘을 원하는 횟수만큼 눌러 주세요. 또한 제 최신 콘텐츠를 이메일로 받아보려면 구독해 주세요. 저는 주로 수학, 음악, 과학 등 재미있는 주제에 대해 매주 글을 씁니다.

## 추가 자료

- 대안적 접근 방식 시각화
- 수열의 성장을 특성화하는 흥미로운 시도인 David Eppstein의 방법
- 파이썬을 선호하는 경우, 이 코드를 통해 수열을 직접 오디오로 변환하는 Luke Polson의 코드

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

4. \*\*켈리 반 에버트(Kelley van Evert)가 만든 재미있고 상호 작용적인 앱으로 수열을 탐험해보세요.

5. \*\*알렉스 벨로스(Alex Bellos)가 출연한 인기 있는 Numberphile 비디오로 수열을 소개합니다.
