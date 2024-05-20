---
title: "Home Assistant를 통해 Tuya Smart Life IR 장치 제어하기 - 해결책"
description: ""
coverImage: "/assets/img/2024-05-20-ControlTuyaSmartLifeIRdevicesviaHomeAssistantaworkaround_0.png"
date: 2024-05-20 19:17
ogImage: 
  url: /assets/img/2024-05-20-ControlTuyaSmartLifeIRdevicesviaHomeAssistantaworkaround_0.png
tag: Tech
originalTitle: "Control Tuya Smart Life IR devices via Home Assistant — a workaround"
link: "https://medium.com/daniels-tech-world/control-tuya-smart-life-ir-devices-via-home-assistant-a-workaround-b86e6332d3ac"
---


만약 홈 어시스턴트에 진입하기 전에 IR 블라스터 몇 개를 구입했다면 저의 고안한 해결책이 있어요. 이를 통해 IR 블라스터를 (약간) 홈 어시스턴트를 통해 제어할 수 있는 방법을 소개해 드릴게요.

제 사무실에는 Moes IR 블라스터가 있어서 에어컨을 제어하고 있어요. 홈 어시스턴트에서 작동시키는 방법이 없다고 해요. 제가 임시로 고안한 "해결책"을 소개해 드릴게요.

IR은 일방적인 프로토콜이기 때문에, 저는 간단히 에어컨을 위해 '냉방 모드', '난방 모드', '끄기'를 실행하는 3가지 씬을 만들었어요.

<div class="content-ad"></div>

"매 '씬'마다(투야 스마트 라이프에서 구성함) 원하는 에어컨 상태 매개변수로 설정했습니다:

![Scene Example](/assets/img/2024-05-20-ControlTuyaSmartLifeIRdevicesviaHomeAssistantaworkaround_1.png)

각 씬에 서술적인 이름을 부여했습니다:

![Scene Names](/assets/img/2024-05-20-ControlTuyaSmartLifeIRdevicesviaHomeAssistantaworkaround_2.png)"

<div class="content-ad"></div>

홈 어시스턴트에서 투야 인티그레이션을 강제로 다시 불러와서 새로 추가한 씬을 적용하는 것을 도와주겠어요:

![이미지](/assets/img/2024-05-20-ControlTuyaSmartLifeIRdevicesviaHomeAssistantaworkaround_3.png)

그런 다음 구성하려는 각 에어컨 상태별로 헬퍼 버튼을 만들어 보세요:

![이미지](/assets/img/2024-05-20-ControlTuyaSmartLifeIRdevicesviaHomeAssistantaworkaround_4.png)

<div class="content-ad"></div>

그 동작에 해당하는 버튼을 만들어보세요:

그러고 나서 Home Assistant에서 버튼을 누르는 것을 Smart Life에서 동기화된 "scenes"로 매핑하는 자동화를 생성하세요:

![Image](/assets/img/2024-05-20-ControlTuyaSmartLifeIRdevicesviaHomeAssistantaworkaround_5.png)

그리고 Home Assistant에서 버튼을 누르는 것을 Smart Life에서 동기화된 "scenes"로 매핑하는 자동화를 생성하세요:

<div class="content-ad"></div>

<table>
  <tr>
    <img src="/assets/img/2024-05-20-ControlTuyaSmartLifeIRdevicesviaHomeAssistantaworkaround_6.png" />
  </tr>
</table>

'액션'은 장면을 활성화하는 것입니다:

<table>
  <tr>
    <img src="/assets/img/2024-05-20-ControlTuyaSmartLifeIRdevicesviaHomeAssistantaworkaround_7.png" />
  </tr>
</table>

액션 설정하기:

<div class="content-ad"></div>

```markdown
![HVAC Control Dashboard](/assets/img/2024-05-20-ControlTuyaSmartLifeIRdevicesviaHomeAssistantaworkaround_8.png)

I built a little HVAC control dashboard where I’m adding the buttons:

![Add Buttons](/assets/img/2024-05-20-ControlTuyaSmartLifeIRdevicesviaHomeAssistantaworkaround_9.png)

Add the buttons to the dashboard:
```

<div class="content-ad"></div>

아래는 마지막 설명입니다:

<img src="/assets/img/2024-05-20-ControlTuyaSmartLifeIRdevicesviaHomeAssistantaworkaround_11.png" />

이제 비-HA 제어 IR 블래스터를 통합한 HVAC 또는 기타 IR 제어 가전제품의 상태를 제어할 수 있습니다!