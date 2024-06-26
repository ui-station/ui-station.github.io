---
title: "원래 고전하던 지붕 휘어짐 현상은 3D 프린터 사용자에게 큰 골칫거리였습니다 이제 Elegoo Neptune 4의 침대 휘어짐 문제를 해결하는 방법을 알아보겠습니다"
description: ""
coverImage: "/assets/img/2024-05-23-ElegooNeptune4BedWarping_0.png"
date: 2024-05-23 16:33
ogImage:
  url: /assets/img/2024-05-23-ElegooNeptune4BedWarping_0.png
tag: Tech
originalTitle: "Elegoo Neptune 4 Bed Warping"
link: "https://medium.com/@sharpninja/elegoo-neptune-4-bed-warping-c646633a4fb5"
---

# 문제

제가 두 달 동안 사용한 Elegoo Neptune 4 프린터는 큰 물체(어느 축이든 약 200mm)를 출력할 때 매우 신뢰할 수 없어지고 있습니다. 따라서 침대 레벨링을 조작하고 80도로 침대를 가열하여 10분 동안 가열시킨 후 보정하여 보정을 저장하는 매크로를 작성했습니다. 또한 각 축에 10개의 포인트로 보정의 해상도를 5에서 10으로 두 배로 늘렸습니다.

Tuning 패널에서의 시각화를 기반으로 한 웨이브 형상이 침대에 형성되었습니다. 이는 Fluidd의 튜닝 패널에서 시각화된 낮은 부분이 점착력이 낮음을 나타내고 있으며, 일반적으로 필라멘트가 접착되지 않고 침대에서 벗겨지다가 노즐이 침대의 높은 부분에 도달하면 다시 접착하기 시작한다는 물리적 결과로 백업됩니다. 이러한 문제는 PLA 및 PETG에서 발생하지만 TPU에서는 문제가 발생하지 않았습니다. 또한 초기 레이어를 10mm/s로 출력하고 있습니다.

# 보정 매크로

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

[gcode_macro PREHEAT_BED_WAIT_CALIBRATE]
description: 침대를 예열한 다음 기다렸다가 교정합니다.
gcode:
M118 [PREHEAT_BED_WAIT_CALIBRATE] 침대 예열 중.
M190 S80 ;침대가 인쇄 온도에 도달할 때까지 기다림
M118 [PREHEAT_BED_WAIT_CALIBRATE] 침대 예열 완료.
M118 [PREHEAT_BED_WAIT_CALIBRATE] 교정은 10분 후에 시작됩니다.
G4 P600000; 10분 대기 후 교정
M118 [PREHEAT_BED_WAIT_CALIBRATE] 10분 경과
G29
M118 [PREHEAT_BED_WAIT_CALIBRATE] 교정 완료
M118 [PREHEAT_BED_WAIT_CALIBRATE] 교정 저장 및 클리퍼 재시작
SAVE_CONFIG

## 80도로 10분간 침대에서 발생한 변화 분석

한 가지 흥미로운 점은 침대가 X축과 Y축 모두에 파도 모양을 나타내고 있으며, 아홉 개의 매우 높은 지점과 두 개의 세로 및 가로 도랑이 있습니다. 이는 실제로 가로줄과 세로줄이 표준 초기 패턴과 다르게 보이는데, 마치 틱택토 판가 눌려진 것 같습니다. 나는 나사를 여러 번 수동으로 조절했는데(매번 높이 설정을 위해 새로운 열려 있는 영수증을 사용합니다), 교정 패턴에 실질적인 변경이 없었습니다. 또한, 만약 나사가 정확하지 않으면 패턴은 일반적으로 한 쪽이나 한 코너가 크게 다른 것처럼 균일한 모양을 보이지만, 나는 지금과 같은 파도 모양이 아무런 힌트도 없다고 생각합니다.

내 의심은 침대를 이동 프레임에 고정하는 나사가 침대가 열을 흡수함에 따라 확장되는 만큼 구부러진 선을 만들어내는 것입니다. 나사로 고정되지 않은 침대의 부분들이 침대가 흡수함에 따라 더 많이 팽창하기 때문입니다.

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

또 다른 문제는 코너가 전체 온도에 도달하지 못한다는 것입니다. 침대가 가열된 상태에서도 코너가 완전한 온도에 이르지 않는다는 점이 문제입니다. 30분 동안 가열해도 10분 동안 가열했을 때와 똑같은 패턴이 나타났습니다. 이는 특히 PETG를 인쇄할 때 문제를 일으키는데, 이는 보다 높은 침대 온도가 필요하고 따라서 열원 요소(즉, 코너)에서 멀어질수록 온도 차이가 커지기 때문입니다.

![이미지](/assets/img/2024-05-23-ElegooNeptune4BedWarping_0.png)

# 실온 온도 보정

실험으로, 저는 80도로 예열하는 부분을 제거하고 침대가 10분 동안 실온에 놓인 후 보정을 실행하도록 매크로를 수정했습니다. 이에 다른 문제가 있습니다. 프린터의 온도 프로브가 약간 부정확한 것 같습니다. 제 집의 주변 온도는 화씨 65도(섭씨 18.3도)이지만, 프린터의 프로브는 침대에서 22.3도, 핫엔드에서 23.4도를 표시하고 있습니다. 이 중 어느 하나의 온도라도 정확하다면, 저는 68도에서 땀을 흘리기 시작하고 72도에서는 건강 문제로 인해 실신할 수 있으므로 시원한 환경으로 향할 것입니다. 그러나 정말 궁금한 것은 왜 헤드가 침대에 위치했을 때 온도 차이가 1도 발생하는지 입니다.

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

또한, 실내 온도에서 침대의 휨이 실제로 더 두드러지게 나타납니다.

![침대의 휨 이미지](/assets/img/2024-05-23-ElegooNeptune4BedWarping_1.png)

이것으로 침대가 분명히 휨이 있는 것을 확인할 수 있고, 열처리는 실제로 침대의 평활함을 향상시킵니다. 보증 청구를 시작할 시간이라고 생각합니다. 이것은 정말 안쓰러우며, 이 침대들을 주기적으로 교체해야 할지 궁금해지네요. 또한, 침대를 이동하는 프레임에 더 많은 나사를 사용하여 이러한 문제를 제거할 수 있을까요? 제가 더 큰 프린터가 필요해서 Elegoo Neptune 4 Max를 사려고 생각 중이었는데, 이렇게 작은 침대가 휨이 나면 네 배 크기의 영역을 가진 것은 어떻게 될까요?

# 40도에서 열처리 한 결과

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

40도에서 불려서 다시 교정하기로 결정했어요.

![이미지](/assets/img/2024-05-23-ElegooNeptune4BedWarping_2.png)

요약 — 이게 세 가지 중에 제일 최악이에요.
