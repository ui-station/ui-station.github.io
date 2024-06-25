---
title: "좋은 코드와 나쁜 코드란 비전문가를 위한 일러스트 예제"
description: ""
coverImage: "/assets/img/2024-06-23-WhatisgoodbadcodeAnillustratedexamplefornon-programmers_0.png"
date: 2024-06-23 21:16
ogImage:
  url: /assets/img/2024-06-23-WhatisgoodbadcodeAnillustratedexamplefornon-programmers_0.png
tag: Tech
originalTitle: "What is good bad code? An illustrated example for non-programmers"
link: "https://medium.com/@todbotts.triangles/what-is-good-bad-code-an-illustrated-example-for-non-programmers-1222b600a0f0"
---

한 번 어딘가에서 다음 내용을 담은 것 같은 격언을 읽은 적이 있어요:

어떤 대중적인 출판물에 나온 것 같다는 기억이 나요. 기사의 주제가 “나쁜 코드에 주의하세요. 죽을 수도 있어요. 아, 그렇지만 보이지 않으니 걱정 마세요!” 정도의 내용이었던 것 같아요. 독자들에게는 진정한 위안이 되는 메시지네요, 그렇지 않나요? 회상해 보면, 그 기사는 우리 주변에 점점 늘어나는 항공기, 기차, 그리고 자율 주행 자동차들 속에 숨어 있는 ‘나쁜 코드’를 선동하는 취지였던 것 같아요. 그리고 독자들의 관심을 끌려고 한 것 같기도 해요.

# 일반인으로서 ‘나쁜 코드’란 무엇일까요?

하루에 약 5시간 동안 코드를 작성하고 검토하며 다시 작성하는 일(코드 리팩토링: 보다 간결하고 사용하기 편한 방식으로 재작성하는 것)을 하는 사람으로써, 코드가 무엇인지(나쁜 코드가 무엇인지도, 확실히 알아요!). 많은 사람들이 코드가 무엇인지 모른다는 것을 잊을 때가 종종 있어요.

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

위 문제에 대한 다음 대답을 제공합니다. “너는 코더인데, 무슨 일을 하는 거야?”

위 대답이 언급한 대로, 소프트웨어 개발자/코더/프로그래머는 사실적인 존재를 가진 것을 만들고 있지만 그것들은 보기에는 너무 작아서 실체를 파악하기 어렵습니다 (하지만, 어셈블리 또는 작은 문 모음집으로 상상할 수 있습니다). 그 자체만으로 충분히 미친 듯한 일이지만, 적어도 그것을 상상할 수 있다면 "코딩"이 무엇인지 개념적으로 이해했다고 축하해요! 그러나 이 섹션 제목의 질문으로 돌아갑시다. — '나쁜 코드'란 무엇일까요?

작은 문 모음집 비유를 유지하면, 나쁜 코드란 너무 많은 문이 있는데 그 문들이 불필요하게 반복되거나 복잡한 방식으로 배치된 것을 뜻합니다.

문 모음집 비유로는 시각화하기나 더 설명하기 어렵기 때문에, 이 시점에서 아래에 작성하고 설명하는 다른 비유를 제공하고 싶습니다.

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

# 나사 돌리기 장치 만들기!

우리가 나사 돌리기 장치를 만들고 싶다고 가정해 봅시다. 회전 가능한 나사를 만들어서 먼 곳에 있는 다른 나사도 함께 회전시킬 수 있는 장치입니다. 제품 요구 사항은 아래와 같습니다. 한 개의 나사를 돌리면, 멀리 떨어진 다른 나사도 회전할 것입니다:

![이미지](/assets/img/2024-06-23-WhatisgoodbadcodeAnillustratedexamplefornon-programmers_0.png)

나쁜 코드는 일단 문제를 해결하는 데만 집중하며, 나사와 나사 돌리기 장치를 반죽으로 연결하는 가장 쉬운 해결책을 제안합니다. 나쁜 코드는 초반에 Occam의 면도날 상을 수상합니다. 좋은 코드는 초반에는 조금 지나친 것처럼 보이며, 고무 벨트와 2개의 바퀴를 활용합니다.

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

<img src="/assets/img/2024-06-23-WhatisgoodbadcodeAnillustratedexamplefornon-programmers_1.png" />

# 요구 사항 변경! 손잡이가 다시 위치로 이동되어야 합니다!

개발 주기에서 항상 발생하는 것처럼 어느 순간 고객 요구 사항이 변경됩니다. 이 비유에서 고객은 이제 원래 손잡이의 앞쪽과 옆쪽에 위치한 다른 손잡이를 회전시킬 수 있는 손잡이를 원합니다. 아래 그림과 같이요:

<img src="/assets/img/2024-06-23-WhatisgoodbadcodeAnillustratedexamplefornon-programmers_2.png" />

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

나쁜 코드는 몇 개의 간단한 부품을 추가해야 하며, 이는 전체 시스템을 보다 부서지기 쉽고 고장 발생 가능성이 높게 만듭니다. 좋은 코드는 새로운 요구 사항이 생겨도 약간의 조정만으로 문제를 해결할 수 있으며, 그냥 더 긴 고무 벨트를 사용하여 이 문제를 해결합니다.

![이미지](/assets/img/2024-06-23-WhatisgoodbadcodeAnillustratedexamplefornon-programmers_3.png)

# 요구 사항 변경! 손잡이의 회전 속도를 늦춰야 합니다!

마침내, 고객님이 손잡이를 서로 다른 속도로 회전하길 원하는 결정을 내렸습니다. 입력 손잡이를 작게 돌리면 연결된 손잡이가 크게 회전해야 합니다.

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

새 요구 사항에 따라, 나쁜 코드는 시스템에 더 많은 구성 요소를 추가해야 하므로 더욱 복잡해집니다. 반면에 좋은 코드는 다시 한번 조정이 필요하며 연결된 조절 부분에 더 큰 휠을 사용하는 것으로 충분합니다:

![이미지](/assets/img/2024-06-23-WhatisgoodbadcodeAnillustratedexamplefornon-programmers_4.png)

# 비유 후기: 그렇다면?

위의 내용처럼, 요구 사항이 간단할 때 좋은 코드는 종종 과잉으로 느껴지지만, 고객 요구 사항이 변경될 때(항상 그렇듯이) 정말 빛을 발합니다. 다시 말해, 잘 확장되고 변경됩니다. 반면에 나쁜 코드는 간단한 문제에 대해 간단하고 좋아 보이지만, 시스템이 변경되거나 복잡성이 증가할 때 악몽이 됩니다.

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

하지만 혹시 "음, 위에서 설명한 두 시스템 모두 결국 같은 결과를 얻을 수 있어요 - 손잡이 회전기는 어느 경우에도 성공적으로 작동해요" 라고 생각하고 계실지도 모릅니다.

맞아요 - 혼자서 개발하거나 취미로 프로젝트를 하는 경우에는 괜찮은 방법입니다. 저도 (제 경우에는 일반 JS 코드) 수만 줄의 나쁜 코드를 작성한 적이 많았는데요, 그렇다고 문제가 없었어요. 그 코드는 작동하고, 배포하고, 한 번도 되돌아본 적이 없어요.

하지만 전문적인 코딩은 본질적으로 협업의 결과물이에요. 그리고 여러분이 작성하는 코드는 현재나 미래에 다른 개발자들에 의해 계속해서 읽히고 수정될 거에요. 코드가 이해하기 쉬울수록 다른 개발자들이 더 쉽고 효율적으로 작업할 수 있으니까요.

- "여기 손잡이 회전기가 있어요. 2개의 바퀴와 벨트로 구성돼요" 또는
- "이것이 연결 막대를 보관하는 삽입이에요. 이것이 연결 막대이고, 이 다른 삽입 위에 있는 작은 구멍에 막대의 끝을 넣어야 해요..."

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

나는 당신이 시니어 개발자로부터 코드베이스를 이어받는 주니어 개발자라면 어떤 것을 더 좋아할지 궁금해요! :)
