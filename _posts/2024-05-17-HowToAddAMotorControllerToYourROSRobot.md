---
title: "로봇에 모터 컨트롤러를 추가하는 방법"
description: ""
coverImage: "/assets/img/2024-05-17-HowToAddAMotorControllerToYourROSRobot_0.png"
date: 2024-05-17 19:25
ogImage:
  url: /assets/img/2024-05-17-HowToAddAMotorControllerToYourROSRobot_0.png
tag: Tech
originalTitle: "How To Add A Motor Controller To Your ROS Robot"
link: "https://medium.com/exploring-ros-robotics/how-to-add-a-motor-controller-to-your-ros-robot-fd17352cd5e3"
---

로봇에 직접주행 및 오도메트리 기능을 부여하세요!

![이미지](/assets/img/2024-05-17-HowToAddAMotorControllerToYourROSRobot_0.png)

ROS/ROS2와 함께 사용할 모바일 로봇을 만들고 있다면, 먼저해야 할 일 중 하나는 모터 컨트롤러를 통합하는 것입니다. 모터 컨트롤러의 목적은 네비게이션 스택과 같은 상위 레벨 소프트웨어로부터 메시지를 수신하고, 이를 모터를 구동하는 신호로 변환하는 것입니다. 또한 모터의 인코더에서 정보를 받아 로봇의 속도와 위치를 계산할 수 있습니다. 추가로 배터리 전압이 변동하거나 지형이 다를 때에도 일관된 바퀴 제어를 얻을 수 있습니다. 흥미가 생겼나요? 전형적인 차이 드라이브 로봇을 위한 ROS 모터 컨트롤러를 만들기 위해 필요한 모든 것과 참고용으로 사용할 수 있는 작동하는 코드가 준비되어 있습니다!

이 프로젝트의 전체 코드는 GitHub 레포지토리에 있습니다.

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

배경

내가 전에 썼던 ROS가 유용한 이유, ROS가 하는 일, 그리고 물리적 로봇과 어떻게 통합되는지에 대한 개요에 대해 읽은 것으로 가정하겠습니다.

나는 아두이노, Pi Pico 또는 Teensy와 같은 일반적인 마이크로컨트롤러에 어느 정도 익숙하다고 가정합니다. 이 경우 Teensy를 사용할 것이지만, 다양한 마이크로컨트롤러 유형에 걸쳐 개념은 거의 동일합니다.

중요한 개념

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

모터 컨트롤러의 역할은 전형적으로 /cmd_vel과 같은 주제에 Twist 유형의 메시지를 받는 것입니다. 이 메시지는 두 구성 요소로 이루어져 있습니다. 첫째, 미터/초로 분할된 원하는 선형 속도를 정의합니다. 두 번째로는 원하는 각속도인 라디안/초의 회전 속도를 포함합니다.

예를 들어, 내비게이션 스택은 이 메시지를 보낼 수 있으며, 이 메시지는 로봇을 1m/s로 직진하면서 초당 0.1 라디안으로 회전하도록 모터 컨트롤러에 지시합니다. x 방향은 앞쪽으로, Z 방향은 지면과 직교한 상단 방향이고, y는 좌우입니다. 결과적으로 곡선 경로가 생성됩니다.

```js
linear: x: 1.0;
y: 0.0;
z: 0.0;
angular: x: 0.0;
y: 0.0;
z: 0.1;
```

평범한 미끄럼 방지 구동 방식의 로봇은 일반적으로 가로 이동하거나 직접 올라가는 능력이 없으므로 선형 구성 요소는 주로 x 값을 사용합니다. 마찬가지로, 이 유형의 대부분 로봇은 Z 축을 중심으로 명령된 각속도만 사용할 것입니다. 방향과 단위에 대한 ROS 규칙은 REP 103에서 찾을 수 있습니다.

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

이것이 지나치게 복잡해 보일 수도 있어요. 메시지 유형이 로봇이 물리적으로 따를 수 없는 구성요소를 가지는 이유가 뭘까요? 메시지 유형은 여러 유형의 로봇에서 사용될 것이기 때문이에요. 홀로노믹 드라이브 로봇은 옆으로 이동해서 미끄러짐 없이 움직일 수 있어요. 쿼드콥터 로봇은 회전하지 않고 동시에 위, 옆, 앞으로 움직일 수 있어요. 이 메시지 유형은 다양한 종류의 로봇에서 사용할 수 있도록 설계되었어요.

각도 구성요소를 신경 써야 하는 이유가 뭘까요? 나중에 이것이 정말 유용해질 거에요. 직선으로 운전 중에 조금씩 벗어나기 시작할 경우에 코스 수정을 쉽게 보낼 수 있어요. 제자리에서 회전 명령을 쉽게 보낼 수 있어요. 우리가 아래에서 볼 때, 수학도 쉬워질 거에요.

그래서 모터 컨트롤러는 cmd_vel에서 특정 선형 및 각속도 혼합을 주문받고, 그것이 모터 속도로 어떤 의미인지 계산해야 해요. 여기가 멋진 곳이에요. 곧 수학으로 돌아올게요 - 무서워하지 마세요. 먼저, 모터 속도를 정확히 제어하는 법에 대해 이야기해야 해요.

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

간단한 로봇을 만들었다면, 아마도 DC 모터의 속도를 제어할 수 있다는 개념에 익숙할 것입니다. 전압을 펄스로 제어함으로써 DC 모터의 속도를 조절할 수 있다는 아이디어에 편안해지셨을 겁니다. 전압이 켜져 있는 비율이 높을수록 모터는 최고 속도까지 빨리 회전합니다. 이를 펄스 폭 변조(Pulse Width Modulation)이라고 하며, 단순한 로봇에서도 동작하지만 문제가 있습니다. 가장 큰 문제는 모터 속도가 배터리 전압에 따라 변경된다는 것입니다. 모터가 회전하는 속도는 적용된 전압의 함수이므로 배터리가 방전될수록, 주어진 속도를 유지하기 위해 더 높은 켜는 시간 비율이 필요합니다. 또 다른 문제는 주어진 전압에 대해 한 모터가 조금이라도 다른 모터보다 빨리 회전한다면 어떻게 될까요? 양쪽에 동일한 비율을 보낸다면 로봇은 곡선 형태로 이동합니다. 또한 지형에 의해 바퀴 중 하나가 부분적으로 막혀 있다면 특정 바퀴 속도를 달성하는 데 더 많은 전력이 필요합니다. 일정한 속도를 명령하고 실제로 그 속도를 얻고 싶다면 휠로부터 피드백을 받아야 합니다. 우리는 폐쇄 루프 시스템이 필요합니다.

운전 모터의 경우, 통합 자기 엔코더가 있는 모터를 사용하고 싶습니다. 홀 센서는 모터 축에있는 자석이 센서를 지날 때마다 펄스를 생성하는 데 사용됩니다. 이 펄스를 마이크로컨트롤러로 보내어 카운트하고 실제 바퀴 속도를 계산합니다. (이를 위해 기어 비율 및 기타 몇 가지 세부 정보가 필요합니다 — 나중에 자세히 살펴보겠습니다)

아래 이미지에서 각 모터의 뒤쪽에 엔코더가 달려 있는 것을 볼 수 있습니다.

![image](/assets/img/2024-05-17-HowToAddAMotorControllerToYourROSRobot_1.png)

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

그래서 이제 마이크로컨트롤러는 PWM 펄스폭을 변화시킴으로써 모터의 속도를 제어하고, 샤프트의 실제 속도를 읽을 수 있게 되었습니다. 이는 배터리 전압, 지형, 또는 로봇의 왼쪽 구동 바퀴에 감겨있는 골든 리트리버 털과는 독립적으로 작동합니다. 우리는 이제 폐쇄 루프를 가지고 있으며 이제 어디론가 가고 있습니다. 그래서.. 우리가 원하는 바퀴 속도를 어떻게 얻을까요?

PID 제어

그것이 PID 제어 루프의 역할입니다. PID 루프는 다른 사람들에 의해 더 잘 이해되고 다뤄진 기사들에서 충분히 다루어졌기 때문에, 여기서는 다시 다루지 않겠습니다. 좋은 소개는 여기에 있고, 모터 제어에 좀 더 특화된 것은 여기에 있습니다. Youtube에도 좋은 비디오들이 있습니다. PID에 대해 배운 내용을 로봇에 매핑하는 방법을 요약하면 다음과 같습니다. 각 모터마다 한 개의 루프가 필요합니다. 인코더는 펄스 형태로 피드백을 제공하며, 이를 사용하여 바퀴 RPM을 계산합니다. 설정점은 선속도와 각속도에서 계산된 바퀴 속도입니다 (곧 설명될 것). 출력은 주로 0에서 255까지 변할 수 있는 펄스폭의 백분율입니다. 몇 초에 한 번씩 PID 루프는 설정점을 실제 바퀴 속도와 비교하고, 모터의 PWM 출력을 조정하여 배터리 전압, 부하 또는 다른 변수와 관계없이 목표치에 유지합니다. 이에는 나중에 다룰 튜닝 프로세스가 필요하며, 기사의 나중 부분에서도 이에 대해 논의할 것입니다. 지금은 바퀴 속도가 어떻게 계산되는지로 넘어갑시다.

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

모터 컨트롤러는 로봇이 주어진 선속도와 각속도로 동시에 이동하도록 요청받은 메시지를 받았어요. 바퀴가 해야 할 일을 어떻게 계산하는 걸까요?

여기 수학적 설명이 잘 나와 있어요. 거기서 이를 배웠어요. 이제 Twist 메시지가 선속도와 각속도를 따로 분리하는 이유가 분명해질 거예요.

먼저 선속도부터 시작해봅시다. 그것은 상당히 쉬워요. 우리는 특정 바퀴의 속도를 PID 컨트롤러를 사용하여 정확하게 설정하는 방법을 알아요. 직선 운동에서 두 바퀴는 같은 속도로 회전해야 하므로, 우리는 단순히 우리가 원하는 전진 속도를 만들어내기 위한 그 바퀴 속도가 무엇인지 계산하고 두 바퀴 모두 그 속도로 설정하면 되요. 주요 구동 바퀴의 반경을 알아야 하는데, 그것으로부터 바퀴 둘레를 계산할 수 있어요. 그것이 바퀴 한 바퀴 회전마다 로봇이 이동하는 거리를 의미하죠. 그 후, 구동 바퀴의 기어 비율과 바퀴 당 엔코더 틱 수를 알아야 해요. 그럼 필요한 모터 속도를 계산할 충분한 정보가 생기게 되요.

각속도를 계산하는 수학은 약간 복잡해요. 두 바퀴의 속도 차이를 도입해 로봇이 올바른 속도로 회전하도록 해야 해요. 우리는 바퀴베이스를 알아야 하는데, 이는 드라이브 바퀴 사이의 거리를 말해요. 처음에 내가 처음에 한 것처럼 각 바퀴의 안쪽 가장자리부터 측정하지 마세요. 회전 속도에 상당한 오차가 발생할 거에요.

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

다음은 코드가 모이게 됩니다:

변수 data.linear.x는 요청된 속도의 x 구성 요소이고, data.angular.z는 로봇의 요청된 회전입니다. ROS 메시지가 이렇게 속도를 분리하여 제공하면 희망하는 바퀴 속도를 계산하기가 매우 간단해집니다. right_rpm과 left_rpm 변수는 각 모터에 대한 PID 루프의 세트 포인트입니다. 이것은 원하는 동작을 생성하기 위해 필요합니다.

오도메트리 계산

모터 속도를 올바르게 설정하는 것 외에도 모터 컨트롤러는 로봇의 위치와 방향을 데드 레커닝을 통해 추정해야 합니다. 로봇이 어디에 있고 어느 방향을 향해 있는지 추적하기 위해 모든 이동, 각도 및 선형 이동을 합산합니다. 완벽한 것은 아닙니다 — 바퀴 슬립과 누적 오차로 인해 오랜 시간과 거리에 걸쳐 부정확해지지만, 매우 유용한 도구입니다. 짧은 거리에서는 꽤 정확하며, SLAM 또는 GPS와 같은 다른 위치 결정 도구와 결합하면 개선된 전체 추정치를 얻을 수 있습니다. 위치의 다른 측정치를 결합하여 전반적인 위치 추정치를 더 정확하게 얻는 과정은 센서 퓨전의 예시이며, 일반적으로 칼만 필터를 통해 수행됩니다. 그렇다면 위치를 어떻게 계산할까요?

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

수학 부분이 잘 다루어졌네요. 저는 Andrew Kramer의 예제 코드를 수정하여 계산한 후, 결과로부터 odometry 메시지를 채웠어요.

내 로봇에서, 원래의 마이크로프로세서가 자원이 매우 부족했기 때문에, 나는 PID 루프를 마이크로컨트롤러에서만 실행하고 Raspberry Pi에서 Python 모터 컨트롤러 노드에서 odometry 계산을 수행하기로 선택했어요. 아래 코드 스니펫은 그 작동 방식을 보여줍니다.

Python 노드는 원하는 바퀴 회전 속도를 마이크로컨트롤러로 보내요. 마이크로컨트롤러는 각 모터의 실제 인코더 틱 수를 매 초 20회 응답해요. 아래의 l_tick_cb() 함수는 Python 스크립트에서, 좌측 모터 인코더 틱 수가 수신될 때마다 작동하는 함수에요. 우측 모터의 콜백은 이 코드 스니펫에서 제외되었어요.

실제 모터 틱 수가 수신될 때마다, 마지막 업데이트 이후에 주어진 쪽이 얼마나 이동했는지 계산되고, 로봇의 새로운 위치 추정 값이 계산돼요.

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

변수 theta는 로봇이 시작 방향에 대해 회전한 각도에 대한 현재 추정치입니다. 오도미트리 메시지를 완전히 채우려면 더 많은 정보가 필요합니다. 이 코드는 현재 속도를 계산하고 공분산 및 변환 필드를 채웁니다. 변환에 대해 자세한 내용은 다른 글에서 다루겠지만, 지금 당장 알아두실 점은 이 메시지에 대해 "odom" 및 "base_link" 자식 프레임으로 설정하는 것이 좋다는 것입니다. 이는 ROS 표준을 준수합니다. 곧 이에 대해 기사를 쓸 예정이지만, REP 105 및 최고 TF 설명은 이해를 시작하기에 좋은 자리입니다.

오도멧리 변환은 쿼터니언으로 브로드캐스트됩니다. 함수를 사용하여 엔코더 값 (theta)으로부터 쿼터니언을 생성하며, 롤/피치는 우리의 미분 구동 로봇에 대해 제로로 가정됩니다. 이 코드는 해당 토론에서 가져온 것입니다.

공분산은 주어진 측정치에 대한 예상 오차를 보고하는 메커니즘이며, 칼만 필터와 같은 센서 융합 중에 사용됩니다. 주어진 설정에 대한 계산 방법을 아직 이해하지 못하므로, 작은 미분 구동 로봇에 대해 찾은 전형적인 값들을 사용했습니다. 여기 개선할 수 있는 부분이 많이 있습니다.

이 코드는 Github 리포지토리에서 마이크로컨트롤러에서 실행됩니다. 이 코드는 왼쪽 및 오른쪽 바퀴 RPM을 설정값으로 받아들이고, 모터를 펄스 폭 변조 (PWM)를 통해 제어하는 PID 루프를 실행합니다. 또한 각 바퀴의 사이클 동안 카운트된 엔코더 틱 수를 보고하여 코드 상에서 실제 바퀴 회전수를 계산합니다.

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

어떤 하드웨어가 있는지 궁금하신가요?

여기 메인 구성 요소를 보여주는 블록 다이어그램이 있습니다.

![하드웨어 블록 다이어그램](/assets/img/2024-05-17-HowToAddAMotorControllerToYourROSRobot_2.png)

보시다시피 Teensy는 모터 드라이버 모듈로 PWM 듀티 사이클을 출력하여 원하는 속도로 모터를 구동합니다. 모터 엔코더로부터 ticks를 받아 실제 속도를 계산하고 제어 루프를 닫습니다. 이들은 모터 PID 루프의 피드백입니다.

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

이 컨트롤러에서는 rosserial_arduino를 사용하여 원하는 바퀴 RPM 주제에 대해 구독하고 odometry/velocity 계산을 위해 각 바퀴의 실제 틱을 게시합니다.

내 컨트롤러는 전압 분배기를 통해 팩 전압을 측정하고 전류 센서의 출력을 사용합니다.

출력을 선택할 때는 모터 드라이버에 대해 PWM 출력을 사용해야 하며 엔코더 입력에 대해 일반 디지털 핀을 사용해야 합니다. Teensy에 대한 차트를 아래에서 볼 수 있습니다 — 다른 마이크로컨트롤러에 대한 유사한 차트도 있습니다.

![Teensy 차트](/assets/img/2024-05-17-HowToAddAMotorControllerToYourROSRobot_3.png)

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

PWM 출력은 모터 드라이버의 4개 입력으로 보내집니다. 내 로봇은 저전류 모터를 사용하므로 이와 같은 저렴한 LM298 듀얼 H-브리지 드라이버를 선택했습니다. 다양한 유형의 모터를 구동하는 다양한 보드가 많이 있습니다. 브러시리스 모터는 다른 유형의 컨트롤러를 사용하지만 많은 모터가 PWM 입력을 사용합니다. LM298 사용 방법에 대한 좋은 안내서가 여기 있습니다.

![로봇의 전자 부품 덱](/assets/img/2024-05-17-HowToAddAMotorControllerToYourROSRobot_4.png)

여기에는 로봇의 전자 부품 덱이 있습니다. 라즈베리 파이 4 위에 앉은 쉴드 PCB에 Teensy가 보입니다.

![로봇의 전자 부품 덱](/assets/img/2024-05-17-HowToAddAMotorControllerToYourROSRobot_5.png)

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

마이크로컨트롤러 선택

처음에는 Arduino Uno용으로 이 코드를 작성했었는데, 주로 RAM이 부족해지는 문제가 발생했습니다. rosserial_arduino에는 보다 강력한 마이크로컨트롤러가 강력히 권장됩니다. 이 컨트롤러의 다음 버전은 ROS2용 Pi Pico를 사용하여 micro-ros로 만들 것입니다. 원칙적으로 이 코드는 rosserial_arduino에서 지원하는 모든 프로세서 상에서 작은 수정만으로도 실행되어야 합니다.

Teensy 버전은 매우 잘 실행되었고, 현재 모든 프로젝트를 ROS2로 마이그레이션 중이며, Pico가 더 저렴합니다. 작동이 준비된 경우 해당에 대해 기사를 게시할 예정입니다.

PID 튜닝

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

PID 루프를 조정해야 하는데, 이는 이 글의 범위를 벗어납니다. 웹 검색을 통해 표준 접근 방식을 찾을 수 있습니다. 마이크로컨트롤러 코드에는 ROS 주제 에코 명령을 통해 조정 가능한 P, I 및 D 값을 구독자로 활성화하는 모드가 포함되어 있습니다. 이는 rqt_graph를 사용하여 명령된 rpm 대 실제 rpm을 그래프로 표시하면 튜닝 속도가 크게 향상됩니다.

결론

이 글이 실제 작동 예제를 제시하고 모든 자원을 한 곳에 모아서 작성한 것에 도움이 되었기를 바라며, 여러분의 프로젝트에 유용한 출발점이 되었으면 좋겠습니다. 개선할 점이 있다면 알려주시고, 여러분에게 가치 있었다면 알려주세요.

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

이 프로젝트의 코드는 주로 저의 것이지만, 일부는 다른 소스들을 참고하거나 수정한 것입니다.
