---
title: "Google Cloud IoT Core에서 설정 및 상태"
description: ""
coverImage: "/assets/img/2024-05-17-ConfigurationandStateinGoogleCloudIoTCore_0.png"
date: 2024-05-17 19:20
ogImage:
  url: /assets/img/2024-05-17-ConfigurationandStateinGoogleCloudIoTCore_0.png
tag: Tech
originalTitle: "Configuration and State in Google Cloud IoT Core"
link: "https://medium.com/rockedscience/config-state-google-cloud-iot-core-ffd2382f1b51"
---

## IoT 코어에서 온라인 및 오프라인 장치 사용 방법

![Configuration and State in Google Cloud IoT Core](/assets/img/2024-05-17-ConfigurationandStateinGoogleCloudIoTCore_0.png)

Google Cloud IoT Core 서비스가 중단되어 파트너 기업들로 이전되고 있습니다. 자세한 정보는 여기서 확인하세요. 이 기사는 뉴스가 전해지자마자 발표 예정이었습니다. 저는 잠깐 동안 서랍에 보관해두다가 결국 이를 공개적으로 공유하기로 결정했습니다. 이는 아직도 IoT 코어 호환 서비스에 구글 파트너 솔루션을 구현하는 사람들에게 유용할 수 있습니다.

# 디지털 트윈 및 "그림자"에 대해

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

IoT 장치는 종종 클라우드의 가상 표현을 통해 작동됩니다. 해당 가상화된 장치와 함께 작업하는 주요 이유는 다음과 같습니다:

- 시뮬레이션 및 백테스팅을 실행하여 동작을 예측하거나 모델을 검증합니다.
- 장치의 마지막 알려진 상태에 액세스하거나 명령을 실행하거나 상태를 변경합니다. 가능한 빨리 요청합니다.

위에 나열된 첫 번째 경우는 일반적으로 "디지털 트윈"이라고 알려져 있습니다. 장치나 플리트에서 상태 및 텔레메트리 데이터의 이력을 저장하고, 나중에 이 데이터를 사용하여 예측 모델을 구축하거나 다양한 운영 조건 하에서 가능한 결과를 시뮬레이션할 수 있게 합니다. 위키피디아에 따르면:

두 번째로 매우 흔한 경우는 AWS가 "장치 섀도우"라고 참조하는 것입니다: 연결 상태와 관계없이 원격 장치와 완전한 읽기-쓰기 모드로 상호 작용할 수 있는 안정적인 방법입니다.

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

IoT 장치가 오프라인 상태가 되는 다양한 이유가 있습니다: 현재 위치에서 휴대전화 또는 Wi-Fi 네트워크 범위가 좋지 않을 수 있으며, 배터리가 방전될 수 있고, 사용자가 전원을 껐다 켤 수 있으며, 네트워크에 문제가 발생할 수 있습니다...
따라서 비즈니스 연속성을 보장하고 사용자 경험을 원할하게 만들기 위해 중요한 것은 클라우드에서 최신 장치 상태 데이터를 읽을 수 있는 기능과 온라인 상태로 돌아오면 전달되는 "메시지를 남기는" 기능을 확보하는 것입니다.

첫 번째 사용 사례("디지털 트윈")는 논의가 더 길어질 수 있으므로 본 문서의 범위는 매우 일상적인 "장치 그림자" 사용으로 한정됩니다: 장치에서 원활한 작업을 보장하는 것입니다.

# Google Cloud IoT Core의 "그림자"와 "트윈"

IoT Core는 명시적인 "그림자" 또는 "디지털 트윈" 기능을 제공하지 않지만, 장치의 상태 및 구성 관리의 내장된 기능을 통해 그들의 경량 버전을 제공합니다.

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

## 상태

IoT 코어에 연결된 기기들은 현재 상태를 보고할 것으로 예상됩니다.
공식 문서에는 다음과 같이 설명되어 있습니다:

클라우드에서 기기의 상태를 업데이트하려면, 기기는 MQTT 브릿지 또는 HTTP 브릿지를 통해 상태를 발행해야 합니다. 상태는 다음과 같을 것입니다:

- 가로채지고 자동으로 제한된 히스토리 저장소에 저장됩니다 (현재 시점에서는 장치당 최근 10개의 상태가 유지됩니다), 이 저장소는 API 또는 SDK를 통해 액세스할 수 있습니다.
- 게시된 내용이 목적지 주제의 구독자에게 Pub/Sub을 통해 전달될 것입니다.

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

장치 상태의 오랜 기록을 유지할 필요가 없다면 내장된 상태 저장소를 사용해보세요. 실제 프로젝트에서는 좀 더 긴 기록을 저장하기를 권장합니다. 그것은 가치 있는 정보입니다. 대규모로 이 데이터를 분석하고, 고객 지원팀에 귀중한 통찰을 제공할 수 있습니다.

## 구성

구성은 클라우드에 저장되며 다음과 같습니다:

- MQTT를 통해 IoT Core에 연결된 장치에 빠르게 전송됩니다.
- IoT Core에 연결하는 장치에게 MQTT를 통해 전달됩니다 (장치가 올바른 주제에 가입했는지 확인; 새 구성이 설정되지 않았다면, 최신 기존 버전이 매번 전송됩니다).
- IoT Core HTTP API를 통해 가져올 수 있습니다.

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

클라우드는 또한 구성의 어떤 버전이 기기에 실제로 전달되었는지, 생성 및 전달 시간을 추적합니다. 아직 전달되지 않은 구성은 "null" 전달 타임스탬프가 있습니다.

전달된 구성이 반드시 적용된 구성은 아닙니다. 그래서 기기들은 항상 상태를 클라우드로 푸시해야 합니다.

상태에 관해서는, 10개의 최신 구성 버전이 유지됩니다.

## 상태와 구성 비교

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

구성이 적용되었는지 확인하려면 기기가 상태에 대한 구성 객체의 동일한 키의 실제 값을보고해야합니다. 그래서 다시 Virtual Smart Plug 데모 프로젝트로 돌아가보겠습니다:

- Smart Plug의 상태는 다음과 같습니다: ' "switch": "off" '
- 새 구성이 전송됩니다: ' "switch": "on" '
- 구성이 성공적으로 적용되었다면, Smart Plug은 새로운 상태를 보고해야합니다: ' "switch": "on" '

매우 간단한 기기의 경우 구성 및 상태에 동일한 "스키마"를 사용할 수 있습니다.

실제 프로젝트에서 상태에는 일반적으로 기기의 구성뿐만 아니라 더 많은 정보가 포함됩니다. 스마트 플러그는 상당히 간단한 기기이지만, 구성이 스위치의 간단한 켜기/끄기 상태에 대한 것일 수 있지만, 상태에는 일반적으로 장치 클라이언트 응용 프로그램 (모바일 앱 또는 스마트 홈 대시 보드)에서 표시하는 현재 사용 중인 전력과 같은 정보도 포함됩니다.

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

Google은 Configuration과 State에 동일한 JSON 스키마를 사용하도록 권장합니다. 실제로 기기 대시보드에서 상태를 선택하고 상태 페이로드 끝에 있는 Revert 버튼을 누르면 해당 상태가 Configuration으로 기기로 다시 전송됩니다.

가상 스마트 플러그에서 이를 시도해 봅시다.
목록에서 상태를 선택하고 Revert 버튼을 누릅니다:

![image](/assets/img/2024-05-17-ConfigurationandStateinGoogleCloudIoTCore_1.png)

Configuration & State 대시보드의 목록은 다음과 같이 변경됩니다:

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

![Configuration Overview](/assets/img/2024-05-17-ConfigurationandStateinGoogleCloudIoTCore_2.png)

새로운 "대기 중" 구성이 작성되었고 장치로 전달되기를 기다리고 있습니다.

만약 장치 시뮬레이터가 실행 중이라면 (또는 다음에 시작할 때) 다음 출력을 볼 수 있습니다:

![Configuration State](/assets/img/2024-05-17-ConfigurationandStateinGoogleCloudIoTCore_3.png)

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

변경된 Configuration 및 State가 그에 맞게 업데이트됩니다:

![Configuration and State](/assets/img/2024-05-17-ConfigurationandStateinGoogleCloudIoTCore_4.png)

만약 State에 대한 계층적인 형식으로 JSON과 같은 형식을 사용한다면, 보고된 Configuration은 State의 하위 노드에 게시될 수 있습니다. 이로써 Configuration JSON을 State의 하위 JSON 노드와 쉽게 1:1로 비교할 수 있게 됩니다 (내장 대시보드 도구에는 해당하지 않음). Configuration과 State는 텍스트로 저장 및 전달되지만, 텍스트로 직렬화될 수 있는 어떤 형식이라도 사용할 수 있습니다.
Payload 크기에 Configuration 및 State는 모두 64KB의 제한이 있으므로, 실제 정보에 대한 일부 바이트를 저장하기 위해 복잡한 마크업 및 닫히는 태그가 포함된 형식은 추천하지 않습니다.
Google은 자체 문서에서 Configuration 및 State에 JSON을 사용합니다.

# 명령(Command) vs. 구성(Configuration)

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

Google Cloud IoT Core은 MQTT를 사용하여 장치로 명령을 보낼 수도 있습니다.
처음에는 조금 혼란스러울 수 있습니다. 명령과 구성 사이의 차이가 뭘까요?

저는 디자인 선택을 할 때 이 간단한 프레임워크를 사용합니다:

- 즉시 실행이 필요한 단기 작업은 명령입니다.
  자판기용 실물, 디지털 결제 장치를 생각해보세요: 거래를 빨리 처리해야 합니다. 서버에 연결할 수 없을 때는 그냥 오류를 반환해야 합니다. 거래를 연기하면 의도하지 않은 결과로 이어질 수 있습니다. 사용자는 판매가 취소된 것으로 생각하고 그냥 떠날 수도 있습니다.
- 장치 상태나 동작에 대한 장기적인 변경은 구성입니다.
  우물에 설치된 스마트 전구를 상상해보세요: 루틴은 저녁에 불을 켜고(~6pm) 취침 전에 꺼야 합니다(~10pm). 6pm부터 6.15pm 사이에 네트워크에 연결할 수 없더라도 저녁에 불이 켜지길 원할 것이고, 약간의 지연을 감내할 수도 있습니다.

명령과 구성 사이에는 약간의 기술적 차이가 있습니다. 이는 공식 Google 문서에서 가장 잘 설명됩니다.
또한, HTTP 연결을 사용하는 장치는 명령을 수신할 수 없습니다(그들은 구성을 풀 모드로 요청할 수는 있습니다).
