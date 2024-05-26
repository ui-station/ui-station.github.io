---
title: "안녕하세요, 지그비 세계, 제23부  지그비 장치 그룹들"
description: ""
coverImage: "/assets/img/2024-05-23-HelloZigbeeWorldPart23GroupsofZigbeedevices_0.png"
date: 2024-05-23 16:19
ogImage:
  url: /assets/img/2024-05-23-HelloZigbeeWorldPart23GroupsofZigbeedevices_0.png
tag: Tech
originalTitle: "Hello Zigbee World, Part 23 — Groups of Zigbee devices"
link: "https://medium.com/@omaslyuchenko/hello-zigbee-world-part-23-groups-of-zigbee-devices-0a826b7e9312"
---

<img src="/assets/img/2024-05-23-HelloZigbeeWorldPart23GroupsofZigbeedevices_0.png" />

가끔은 유사한 장치 그룹을 제어하고 싶을 수 있습니다. 전형적인 사용 사례는 단일 스위치로 여러 램프를 제어하는 것이지만, 이는 램프에만 국한되지는 않습니다. Zigbee는 네트워크 수준에서 그룹 주소 지정 기능을 제공하여 여러 장치가 동일한 명령을 처리할 수 있습니다. 장치를 그룹에 할당하기 위해 Groups 클러스터가 사용됩니다. 이 기능을 이번 글에서 살펴보도록 하겠습니다.

내가 처음부터 스마트 Zigbee 스위치를 만들고 있다는 것을 아시다시피. 그룹 기능은 스위치에 좋은 추가 기능일 수 있어서 여러 램프를 제어할 수 있습니다. 스마트 스위치는 또한 램프 릴레이 역할을 할 수 있고, 홈 자동화 시스템이나 다른 스위치로 제어될 수 있습니다. 여기에도 그룹 기능이 적합할 수 있어서 이 장치가 그룹의 일부가 될 수 있습니다. 오늘은 두 가지 경우를 살펴보겠습니다.

이것은 Hello Zigbee 시리즈의 다음 파트로, 처음부터 Zigbee 장치 펌웨어를 작성하는 방법을 설명합니다. 내 코드는 이전 글에서 생성된 코드를 기반으로 할 것입니다. 개발 보드는 NXP JN5169 마이크로컨트롤러를 기반으로 한 EBYTE E75-2G4M10S 모듈을 사용할 것입니다.

<div class="content-ad"></div>

# Zigbee 그룹 클러스터 공부

보통 제공된 사양 정보를 먼저 소화해 봅시다.

컴퓨터 네트워크에서 일반적으로 2 종류의 메시지가 있습니다:

- 유니캐스트(Unicast): 메시지가 특정 수신자에게 전송될 때
- 브로드캐스트(Broadcast): 메시지가 네트워크 내 모든 장치에 전송될 때

<div class="content-ad"></div>

직비 그룹 메시지는 중간에 위치한 것입니다. 기술적으로, 그룹 메시지는 네트워크를 통해 전파되는 브로드캐스트 패킷입니다. 동시에 이 메시지에는 수신기 필드에 그룹 주소가 포함되어 있습니다. 기기는 내부적으로 해당 기기가 속한 그룹 주소 목록을 유지합니다. 대상 그룹의 일부인 기기는 메시지를 수신하고, 다른 기기는 단순히 패킷을 폐기합니다.

따라서 스위치는 빛 그룹에 On 명령을 보낼 수 있습니다. 이 그룹에 속한 모든 조명이 켜집니다.

네트워크 계층에서 그룹 전송이 처리되지만, 그룹을 제어하는 작업은 그룹 클러스터에 의해 수행됩니다. 이는 기기 엔드포인트를 그룹에 추가하거나, 기기가 속한 그룹 목록을 나열하거나, 기기를 그룹에서 제거할 수 있는 특수 클러스터입니다.

![Hello Zigbee World](/assets/img/2024-05-23-HelloZigbeeWorldPart23GroupsofZigbeedevices_1.png)

<div class="content-ad"></div>

그룹 클러스터는 전체 장치가 아닌 엔드포인트 엔티티임을 유념하는 것이 중요합니다. 멀티채널 릴레이 스위치가 있다고 가정해보세요. 각 엔드포인트가 특정 지역의 조명을 제어하는 경우를 생각해보세요. 모든 라인을 모두 함께 제어하고 싶을 것입니다. 하지만 같은 장치에 존재하기 때문에 별도로 제어할 수 있도록 각 지역을 자체 그룹의 일부로 가져야 합니다. 즉, 독립적으로 작동해야 하는 각 엔드포인트는 자체 Groups 클러스터 인스턴스를 가져야 합니다.

그룹 주소는 네트워크 관리자가 그룹에 할당한 가상 16비트 네트워크 주소입니다. zigbee2mqtt 시스템에서는 그룹은 'Groups' 탭에서 생성됩니다.

새 그룹을 만든 후, 이 그룹은 여전히 가상 개념에 불과합니다. 이 그룹은 코디네이터의 데이터베이스에만 존재합니다. 장치는 그룹에 추가되어야 하며, 장치가 해당 그룹의 일부임을 알 수 있어야 합니다. 그룹 탭은 장치를 그룹에 추가하는 간단한 메커니즘을 제공합니다.

<div class="content-ad"></div>

![Hello Zigbee World Part 23](/assets/img/2024-05-23-HelloZigbeeWorldPart23GroupsofZigbeedevices_3.png)

Zigbee 코디네이터는 하드웨어 내부에서 장치 endpoint로 AddGroup 메시지를 보내어, 그 장치가 이제 언급된 그룹 ID로 보내진 그룹 메시지를 수신해야 한다는 것을 나타냅니다.

![Hello Zigbee World Part 23](/assets/img/2024-05-23-HelloZigbeeWorldPart23GroupsofZigbeedevices_4.png)

# 대량생산된 장치들의 동작


<div class="content-ad"></div>

그룹이 작동하는 방식을 분석해 봅시다. 대량생산 장치가 어떻게 동작하는지 배우는 가장 쉬운 방법입니다. zigbee2mqtt에서 그룹을 만들고 거기에 몇 개의 조명을 추가해 보죠.

![image](/assets/img/2024-05-23-HelloZigbeeWorldPart23GroupsofZigbeedevices_5.png)

스위치에 대해서는 사양이 약간 모호합니다. 그룹 개념을 설명하고, 장치가 그룹의 일부가 될 수 있으며, 장치가 보낼 수 있거나 받을 수 있는 가능한 메시지 목록을 설명합니다. 그러나 사양은 컨트롤러 장치가 제어하는 그룹의 일부여야 하는지 여부에 대해 설명하지 않습니다. 주변에 2 종류의 장치가 놓여 있지만, 그들은 매우 다르게 작동합니다.

예를 들어, IKEA Tradfri와 IKEA Styrbar 컨트롤러는 그룹에 추가할 수 없습니다. 그룹에 추가하려고 시도하면 오류가 발생하지 않지만, 기본 응답에는 작업이 지원되지 않는다는 표시가 나타납니다.

<div class="content-ad"></div>


![Image](/assets/img/2024-05-23-HelloZigbeeWorldPart23GroupsofZigbeedevices_6.png)

To allow these devices to control a group, you need to create a new binding to the group. A device can control multiple groups by adding more bindings.

![Image](/assets/img/2024-05-23-HelloZigbeeWorldPart23GroupsofZigbeedevices_7.png)

Here is an example of how the bind request appears.


<div class="content-ad"></div>


![Hello Zigbee World Part 23 Groupsof Zigbee devices 8](/assets/img/2024-05-23-HelloZigbeeWorldPart23GroupsofZigbeedevices_8.png)

From now on, these controllers not only generate regular messages to the coordinator to report the button press but also On and Off commands to the bound group.

![Hello Zigbee World Part 23 Groupsof Zigbee devices 9](/assets/img/2024-05-23-HelloZigbeeWorldPart23GroupsofZigbeedevices_9.png)

As expected, all the lights in the group turn on and off simultaneously.


<div class="content-ad"></div>

다른 유형의 장치를 확인해 보죠. 특히 Tuya Smart Knob의 작동 방식을 살펴보겠습니다. 이 장치는 그룹에 자체를 추가할 수 있어서 즉시 해당 그룹의 모든 조명을 제어할 수 있습니다. 노브를 누르거나 돌릴 때마다 해당 장치는 속한 그룹으로 해당하는 On/Off 또는 LevelCtrl 명령을 보냅니다. 바인딩과 같은 추가적인 준비 단계가 필요하지 않아 사용자에게 더 편리합니다.

Tuya Smart Knob은 그룹에 바인딩하는 기능을 지원하지 않습니다. 기술적으로 바인드 요청을 수락하지만, 명령이 바인딩 요청에 명시된 그룹이 아닌 0x0000 그룹으로 전송됩니다.

또한, Tyua Smart Knob은 한 번에 하나의 그룹에만 참여할 수 있습니다. 장치를 다른 그룹에 추가하면 새로운 그룹 설정이 이전 그룹 설정을 덮어씁니다. 이로 인해 이 장치가 여러 그룹을 제어하는 것이 불가능해집니다.

이 접근 방식의 또 다른 단점은 zigbee2mqtt에서 종종 'Failed to poll onOff from tuya knob' 메시지를 볼 수 있다는 것입니다. 기본적으로 Z2M은 그룹의 모든 장치를 구동 장치로 간주합니다. 이 장치에 대한 On/Off 클러스터의 주기적인 보고를 설정하려고 시도하고, 이를 할 수 없을 때 해당 장치를 주기적으로 검사합니다. 그러나 이 장치는 컨트롤러 장치이므로 어떠한 방식으로도 On/Off 클러스터 상태를 보고할 수 없기 때문에 이 메시지가 표시됩니다.

<div class="content-ad"></div>

이러한 관찰은 몇 가지 기기 유형에 대한 것으로 제한되지만, 컨트롤러를 그룹에 추가하는 접근 방식이 매우 올바르지 않다고 생각하게 만듭니다. 이 프로젝트에서는 첫 번째 접근 방식을 따를 것입니다 - 컨트롤러는 그룹의 일부가 아니라 대신 그룹 수신기에 명령을 바인딩하는 것입니다.

코딩 부분으로 넘어가기 전에 몇 가지 조명 장치도 확인해 봅시다. 조명이 여러 그룹의 일부가 될 수 있는지 알아보고 싶었습니다. 그리고 그렇습니다. 단일 장치에 대한 그룹 수는 크기 또는 내부 그룹 테이블의 한계로 제한됩니다. 조명이 수백 개의 그룹에 참여한다고 기대하지 마십시오. 그러나 장치를 몇 개의 그룹에 할당하는 것은 가능합니다.

그룹이 어떠한 방식으로도 동기화되지 않는다는 것에 유의하십시오 - 그룹은 여러 수신자에게 동시에 메시지를 전달하는 방법일 뿐입니다. 다음 시나리오를 고려해 주세요

- 2개의 조명과 2개의 그룹이 있다고 가정합니다. 그룹1은 Light1과 Light2를 포함하고, 그룹2는 Light2만 포함합니다.
- Group2에 토글 명령을 보내면 Light2가 켜집니다.
- Group1에 토글 명령을 보내면 Light1이 켜지지만, Light2도 토글되어 꺼집니다.

<div class="content-ad"></div>

이 경우에는 원하는 동작이 아닐 수 있습니다. 특정 On 및 Off 명령을 보내는 컨트롤러를 사용해야 합니다. 토글 대신. LevelControl 명령도 비슷한 이유로 사용하는 것이 좋습니다. 컨트롤러가 '빛을 50%로 설정'하는 명령을 보내면 그룹 내 모든 장치에 대해 동일하게 처리됩니다. 그러나 '빛을 10% 줄이기'는 빛의 초기 수준에 따라 다를 것입니다.

이 섹션의 마지막 주제는 자율적인 작업입니다. 연결된 메시지는 코디네이터가 다운된 상태에서도 잘 작동합니다. 컨트롤러 장치는 빛에 의해 처리되는 On/Off 및 LevelCtrl 명령을 보내고, 이 과정에서 z2m의 참여가 필요하지 않습니다(바인딩 설정 외). 이것은 시스템의 내구성을 심각하게 향상시킵니다. 집 서버가 작동하지 않아서 빛을 켜지 못하는 상황에 처하지 않게 될 것입니다.

# 그룹 지원 구현

이제 코딩을 시작해봅시다. 그전에 스마트 스위치에는 버튼(컨트롤러)과 LED(빛)이 모두 있음을 상기시켜 드립니다. 이 둘을 함께 사용할 수도 있습니다(버튼이 내부 LED를 제어), 또는 별도로 사용할 수도 있습니다(버튼이 다른 빛을 제어하고, 내부 LED는 외부 컨트롤러에 의해 제어됨). 그룹 클러스터의 맥락에서 우리는 디바이스 버튼으로 그룹을 제어하는 경우와 디바이스 LED가 그룹의 일부이며 외부 장치에 의해 제어되는 경우를 모두 살펴볼 것입니다.

<div class="content-ad"></div>

장치 버튼이 그룹을 제어하는 첫 번째 경우에는 새로운 코드를 작성할 필요가 없습니다. 이미 바인딩 기능을 구현했으므로 모든 것이 이미 준비되어 있습니다. 그룹에 대한 바인딩을 생성하기만 하면 장치가 해당 그룹으로 Toggle 명령을 보내기 시작합니다.

또한, 저희 장치의 다른 버튼은 서로 다른 그룹에 바인딩될 수 있습니다.

![image](/assets/img/2024-05-23-HelloZigbeeWorldPart23GroupsofZigbeedevices_10.png)

다른 경우는 조금 까다로운데요. LED는 자체 상태를 가지고 있으며 그룹의 일부가 될 수도 있습니다. 이를 위해 엔드포인트에 Groups 클러스터를 추가해야 합니다. 장치에 2개의 스위치 채널이 있기 때문에 양쪽 엔드포인트가 각각 고유한 Groups 클러스터 인스턴스가 필요합니다. 클라이언트 및 서버 클러스터에 대한 추론을 따르면 여기에서 클러스터의 서버 버전이 필요하다는 것을 알 수 있습니다. 이 클러스터는 그룹에 기기를 추가하거나 제거하는 명령을 수락하기 때문입니다.

<div class="content-ad"></div>

프로젝트에 Groups 클러스터를 추가하는 방법은 이전에 추가한 다른 클러스터와 다를바 없습니다:

- Zigbee3ConfigurationEditor에서 SWITCH 엔드포인트 양쪽에 Groups Server (Input) 클러스터를 추가합니다.
- zps_gen.c/h 파일 생성
- zcl_options.h에서 CLD_GROUPS 및 GROUPS_SERVER를 선언합니다.
- 프로젝트 빌드에 그룹 클러스터 ZCL 구현 파일을 추가합니다.
- 그룹 테이블 크기 설정

여기에서 중요한 변경 사항은 Zigbee3ConfigurationEditor에서 그룹 테이블의 크기를 설정하는 것뿐입니다. 그렇지 않으면 그룹 테이블이 너무 작게 생성될 수 있습니다.

<img src="/assets/img/2024-05-23-HelloZigbeeWorldPart23GroupsofZigbeedevices_11.png" />

<div class="content-ad"></div>

그룹 클러스터 인스턴스를 초기화하는 것은 이전 클러스터와 별반 다르지 않아요:

```js
struct OnOffClusterInstances
{
...
    tsZCL_ClusterInstance sGroupsServer;
...

class SwitchEndpoint: public Endpoint
{
protected:
...
    tsCLD_Groups sGroupsServerCluster;
    tsCLD_GroupsCustomDataStructure sGroupsServerCustomDataStructure;
```

```js
void SwitchEndpoint::registerGroupsCluster()
{
    // 그룹 클러스터의 서버로 인스턴스를 생성합니다
    teZCL_Status status = eCLD_GroupsCreateGroups(&sClusterInstance.sGroupsServer,
                                                  TRUE,
                                                  &sCLD_Groups,
                                                  &sGroupsServerCluster,
                                                  &au8GroupsAttributeControlBits[0],
                                                  &sGroupsServerCustomDataStructure,
                                                  &sEndPoint);
    if( status != E_ZCL_SUCCESS)
        DBG_vPrintf(TRUE, "SwitchEndpoint::init(): Failed to create Groups Cluster instance. status=%d\n", status);
}
```

그렇게 해서 서버 그룹 클러스터를 활성화하는 데 필요한 모든 것이에요. 이 코드를 가지고 있다면, 지그비 프레임워크가 기본 그룹 클러스터 구현에 대한 요청을 자동으로 라우팅할 거예요.

<div class="content-ad"></div>

해당 디바이스를 그룹에 추가하는 방법은 다음과 같습니다:

![device](/assets/img/2024-05-23-HelloZigbeeWorldPart23GroupsofZigbeedevices_12.png)

ZCL 코드는 AddGroup 요청을 자동으로 처리하고 언급된 엔드포인트를 그룹 목록에 추가합니다. 한 번 엔드포인트가 그룹에 추가되면 해당 그룹으로 대상화된 On/Off 명령을 자동으로 처리합니다. 지그비 네트워크 코드는 받은 그룹 On/Off/Toggle 명령을 일반적인 OnOff 클러스터 명령으로 변환하며, 이는 기본 스위치 기능을 만들 때 언급한 것과 유사합니다.

부팅 후에도 그룹 정보가 유지되도록 하려면 그룹 테이블 덤프 함수를 작성할 수 있습니다. 이 함수는 선택 사항이며, 호기심을 위해 추가된 것이며, 제품용 코드에는 필수가 아닙니다.

<div class="content-ad"></div>

```c
void vDisplayGroupsTable()
{
    // 포인터 가져오기
    ZPS_tsAplAib * aib = ZPS_psAplAibGetAib();
    ZPS_tsAplApsmeAIBGroupTable * groupTable = aib->psAplApsmeGroupTable;
    uint32 groupTableSize = groupTable->u32SizeOfGroupTable;
    ZPS_tsAplApsmeGroupTableEntry * groupEntries = groupTable->psAplApsmeGroupTableId;

    // 테이블 출력
    DBG_vPrintf(TRUE, "\n+++++++ 그룹 테이블:\n");
    for(uint32 i = 0; i < groupTableSize; i++)
    {
        DBG_vPrintf(TRUE, "    그룹 %04x:", groupEntries[i].u16Groupid);
        for(uint8 j=0; j<(242 + 7)/8; j++)
            DBG_vPrintf(TRUE, " %02x", groupEntries[i].au8Endpoint[j]);

        DBG_vPrintf(TRUE, "\n");
    }
}
```

기기 부팅 시 이 함수를 호출하면 그룹 멤버십이 EEPROM에 저장되어 있고, 기기를 다시 부팅할 때도 유지된다는 것을 쉽게 확인할 수 있습니다.

<img src="/assets/img/2024-05-23-HelloZigbeeWorldPart23GroupsofZigbeedevices_13.png" />

이 장치는 엔드포인트 #2에 해당하는 비트를 설정하여, 해당 그룹에 디바이스 엔드포인트가 속해 있음을 나타냅니다.

<div class="content-ad"></div>

의외로 그룹 메시징은 On/Off 명령뿐만 아니라 다른 클러스터도 전송합니다. 디바이스가 속한 그룹에 메시지를 받으면 해당 클러스터로 메시지를 전파합니다. 예를 들어, Identify 요청을 그룹에 보내봅시다. z2m 대시보드에는 전용 UI가 없지만 MQTT 익스플로러를 통해 mqtt 명령을 보낼 수 있습니다.

![image](/assets/img/2024-05-23-HelloZigbeeWorldPart23GroupsofZigbeedevices_14.png)

이 요청은 클러스터 3 (Identify cluster)에 명령 0 (Identify Request)을 생성하고, 이를 test_group 그룹에 전송합니다. 결과적으로 현재 그룹에 등록된 디바이스 엔드포인트 #2가 이 요청을 처리하고 LED를 깜박이게 할 것입니다.

# 테스트

<div class="content-ad"></div>

그리고, 그룹 기능을 위한 자동화 테스트를 개발하기 위해 자연스럽게 노력했습니다. 제가 한 모든 단계를 설명하고 싶지는 않아요. 오히려 그룹 기능과는 무관한 몇 가지 리팩터링을 하여 테스트 가독성을 개선했어요. 그룹 테스트에 관련된 몇 가지 주요 변경 사항만 간략히 언급하겠습니다.

먼저, Z2M 브릿지 요청을 처리할 Bridge 도우미 클래스를 만들었어요. 이 방법은 zigbee2mqtt/bridge/request/ _ 토픽으로 요청을 보내고 해당 zigbee2mqtt/bridge/response/ _ 토픽에서 응답을 수신하는 편리한 방법이에요. Bridge는 그룹을 생성하고 제거하며, 디바이스를 그룹에 추가/제거하는 역할을 담당해요.

<div class="content-ad"></div>

그룹을 다루기 위해 새로운 Group 클래스가 생성되었습니다.

```js
class Group:
    def __init__(self, zigbee, bridge, name, id):
        self.zigbee = zigbee
        self.bridge = bridge
        self.name = name
        self.id = id

    def create(self):
        payload = {"friendly_name": self.name, "id": self.id}
        return self.bridge.request('group/add', payload)

    def delete(self):
        payload = {"friendly_name": self.name, "id": self.id}
        return self.bridge.request('group/remove', payload)

    def add_device(self, device):
        payload = {
            "device": device.get_full_name(),
            "group": self.name,
            "skip_disable_reporting": "true"
            }
        return self.bridge.request('group/members/add', payload)

    def remove_device(self, device):
        payload = {
            "device": device.get_full_name(),
            "group": self.name,
            "skip_disable_reporting": "true"
            }
        return self.bridge.request('group/members/remove', payload)
```

이러한 기능들은 API 설명대로 그룹과 그들의 멤버십을 유지하기 위해 대응하는 브릿지 API를 호출합니다.

하지만 여기서 중요한 부분은 'skip_disable_reporting' 옵션입니다. 그룹에서 추가되거나 제거되는 일부 장비들은 그들의 리포팅을 변경합니다. z2m은 이 리포팅을 재설정하고 장비에서 코디네이터로 추가적인 바인딩을 생성하려고 합니다. 명령 바인딩 문서에서 기억하시다시피, 장비는 그룹에 바인딩 되면 행동을 바꿉니다. 자동화된 테스트가 실패할 수 있습니다. 다행히도 이러한 원치 않는 동작은 이 옵션을 사용하여 끌 수 있습니다.

<div class="content-ad"></div>

우리 테스트를 위한 좋은 설정을 만들기 위해 몇 가지 fixtures을 만들어야 합니다.

```js
# A fixture that creates a test_group, and cleans it up after all tests completed
@pytest.fixture(scope="session")
def group(zigbee, bridge):
    grp = Group(zigbee, bridge, 'test_group', 1234)

    # 이전에 생성된 그룹을 삭제합니다 (있는 경우). 그룹이 이전에 없었을 경우에는 오류를 무시합니다.
    resp = grp.delete()

    # 새로운 그룹을 생성합니다
    resp = grp.create()
    assert resp['status'] == 'ok'

    # 그룹을 사용합니다
    yield grp

    # 우리 그룹을 정리합니다
    resp = grp.delete()
    assert resp['status'] == 'ok'
```

이 fixture은 이전에 생성된 그룹을 제거하고 새 그룹을 생성합니다. 또한, 테스트가 완료될 때 만들어진 그룹을 정리합니다. 아마도 이 코드는 그저 새 그룹을 생성하는 것으로 단순화될 수 있습니다.

비슷한 방법으로 장치를 그룹에 추가할 수 있습니다.

<div class="content-ad"></div>


# 테스트 그룹에 스위치를 추가하는 fixture입니다
@pytest.fixture(scope="function")
def switch_on_group(switch, group):
    # 이미 그룹에 해당 기기가 있으면 제거합니다
    resp = group.remove_device(switch)

    # 새 그룹을 생성합니다
    resp = group.add_device(switch)
    assert resp['status'] == 'ok'

    yield switch

    # 그룹 멤버십 정리
    resp = group.remove_device(switch)
    assert resp['status'] == 'ok'


이제 테스트를 시작할 시간입니다. 간단한 테스트는 그룹에 On/Off/Toggle 명령을 보내고, 장치가 이러한 명령을 올바르게 수신하고 처리하는지 확인하는 것입니다.

```python
class Group:
    ...

    def switch(self, state):
        # 그룹 상태 응답을 기다리기 위해 준비합니다
        self.zigbee.subscribe(self.name)

        # 요청 발행
        payload = {"state": state}
        self.zigbee.publish(self.name + "/set", payload)

        # zigbee2mqtt에서 응답을 기다립니다 (실제로 상태가 실제로 관련이 없을 수 있지만, 혹시 모르니까 반환합니다)
        return self.zigbee.wait_msg(self.name)
```

이전 글에서는 장치로 On/Off 명령을 보내고, 동일한 함수에서 장치의 응답을 확인했습니다. 새로운 설계에서는 그룹과 장치가 별도의 개체로 다뤄져야 합니다.


<div class="content-ad"></div>

```js
def test_on_off(group, switch_on_group):
    group.switch('ON')
    switch_on_group.wait_device_state_change('ON')
    switch_on_group.wait_zigbee_state_change()  # 기기로부터 발생한 상태 변경을 기다립니다.
    assert switch_on_group.wait_zigbee_state_change() == 'ON'

    group.switch('OFF')
    switch_on_group.wait_device_state_change('OFF')
    switch_on_group.wait_zigbee_state_change()  # 기기로부터 발생한 상태 변경을 기다립니다.
    assert switch_on_group.wait_zigbee_state_change() == 'OFF'
```

`wait_device_state_change()`은 장치 UART를 통해 보고된 상태 변경을 기대합니다. 이름에서 짐작할 수 있듯이 `wait_zigbee_state_change()`는 Zigbee 네트워크를 통해 장치로부터의 보고를 기다립니다.

두 번의 `wait_zigbee_state_change()` 호출에 주의하세요. 이는 z2m 그룹 구현 때문입니다. 그룹에 On/Off 명령을 보내면, zigbee2mqtt가 모든 그룹 장치 대신에 상태 변경을 추측하여 자동으로 응답합니다. 이 테스트는 이러한 메시지를 무시하고, 대신 그 다음에 오는 실제 장치 상태 보고를 기다립니다.

# Summary


<div class="content-ad"></div>

그룹 클러스터는 지그비 라이트 링크(ZLL) 사양에 따라 필수 구성 요소입니다. 이를 통해 동일한 그룹에 속한 여러 장치에 명령을 전송할 수 있습니다.

스위치와 같은 각 컨트롤러는 반드시 그룹을 제어할 수 있어야 합니다. 마찬가지로 모든 조명 장치는 그룹에 추가될 수 있는 기능을 지원해야 합니다. 그룹을 제어하는 것은 본질적으로 On/Off 및 Level Control 클러스터를 조명 장치에 바인딩하는 능력을 필요로 합니다. 이 과정은 바인딩 기사에서 다룬 내용 이상의 특별한 코딩을 요구하지 않습니다.

반면, 조명 장치를 그룹에 추가하는 것은 그룹 클러스터를 장치의 엔드포인트에 통합하는 과정을 포함합니다. 다행히도, 이 과정도 그룹 클러스터를 초기화하는 것을 제외하고는 특정 코딩을 요구하지 않습니다. 이는 그룹 관리에 필요한 기능이 이미 SDK(소프트웨어 개발 키트)에서 구현된 그룹 클러스터에 내장되어 있기 때문입니다. 장치가 그룹에 추가되면 해당 그룹으로 보내는 명령에 자동으로 응답하게 되어 마치 명령이 해당 장치를 직접 대상으로 했던 것처럼 동작합니다.

<div class="content-ad"></div>

- 깃허브 프로젝트
- JN-UG-3113 ZigBee 3.0 스택 사용자 가이드
- JN-UG-3114 ZigBee 3.0 장치 사용자 가이드
- JN-UG-3076 ZigBee 홈 자동화 사용자 가이드
- ZigBee 클래스 라이브러리 사양
- Zigbee 트래픽 스니핑하는 방법
- zigbee2mqtt에 새로운 장치 추가

# 지원

이 프로젝트는 개인 프로젝트로 무료로 개발되고 있습니다. 동시에 작은 기부로 프로젝트를 지원할 수도 있습니다.

![HelloZigbeeWorldPart23GroupsofZigbeedevices_15](/assets/img/2024-05-23-HelloZigbeeWorldPart23GroupsofZigbeedevices_15.png)

