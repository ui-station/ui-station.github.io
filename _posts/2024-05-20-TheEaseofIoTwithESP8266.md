---
title: "ESP8266를 이용한 IoT의 편리함"
description: ""
coverImage: "/assets/img/2024-05-20-TheEaseofIoTwithESP8266_0.png"
date: 2024-05-20 19:29
ogImage:
  url: /assets/img/2024-05-20-TheEaseofIoTwithESP8266_0.png
tag: Tech
originalTitle: "The Ease of IoT with ESP8266"
link: "https://medium.com/@igorkemcoban/the-ease-of-iot-with-esp8266-72c5ee1c9c33"
---

안녕하세요! 🌟

얼마 전에 전자 공작 취미의 일환으로 ESP8266을 실험해 보았어요. 이 칩은 탁월하게 작지만 강력해서 다양한 DIY IoT 프로젝트를 다룰 수 있어요. Wi-Fi 간섭이 최소화된 것에 자신이 있다면 전문가용 프로젝트도 가능합니다. 오늘은 이 강력한 칩을 사용하여 내 성취를 문서화하기로 결정했어요.

최근에 ESP8266을 사용하여 실제 문제 해결에 활용할 수 있다는 것을 발견했어요: 12V 어댑터를 원격으로 제어하여 LED 네온 스트립을 켜고 끄는 것입니다. 완벽하게 작동했어요! 이 가이드에서는 이 작은 칩으로 비슷한 결과를 얻는 방법을 설명할 거예요.

맞아요! 이 가이드를 마치면, 자리를 떠나지 않고도 조명을 제어할 수 있을 거에요. 지금 바로 시작해 봅시다.

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

## 아두이노 클라우드로 들어가 보세요: IoT용 쉬운 버튼

전자기기에 관심이 있었다면 아두이노에 대해 들어봤을 거에요. 그들은 끝없는 DIY 프로젝트의 핵심인 멋진 마이크로컨트롤러 보드를 만들어요. 그리고 아두이노 클라우드라는 것도 있는데, 이것은 여러분의 기기를 인터넷에 연결하는 놀이터처럼 작동해요. 또한 아주 유용한 MQTT를 제공해요. 그리고 놀랍게도, 이것 때문에 원격으로 켜고 끄는 프로젝트가 생각보다 쉬워지더라고 말이에요.

## ESP-01을 만나보세요

이 프로젝트에서는 ESP-01 보드를 사용했는데, ESP8266 칩을 기반으로 하고 있어요. 이건 정말 작은 것이고 어디에나 맞출 수 있어요. 외부 마이크로컨트롤러 없이 자체적으로 코드를 실행할 수 있어요.

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

![Image](/assets/img/2024-05-20-TheEaseofIoTwithESP8266_0.png)

## 필요한 준비물

우선, 필요한 재료들을 모아봅시다:

- ESP8266 (NodeMCU 또는 유사한 제품, 저는 ESP-01을 사용했습니다)
- 전구 또는 LED
- 릴레이 모듈 (전구 또는 LED에 높은 전압을 제어하는 데 사용)
- 아두이노 클라우드 계정
- 점퍼 와이어 몇 개
- 브레드보드 (선택사항이지만 도움이 됩니다, 초기 회로가 정말 복잡할 수 있음)

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

안녕하세요! 아무 것도 가지고 있지 않다면 걱정하지 마세요. 대부분의 장비는 저렴하고 온라인에서 쉽게 구할 수 있어요.

# ESP8266을 위한 Arduino IDE 설정하기

좋아요, 시작해봅시다.

먼저 Arduino IDE를 설정하여 ESP8266 플래시를 설정합니다.

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

- 아두이노 IDE 설치: 아직 설치하지 않았다면, 아두이노 IDE를 다운로드하고 설치해보세요. 무료이며 매우 사용자 친화적입니다.
- 아두이노 IDE에 ESP8266 추가: 파일 ‘환경설정’으로 이동하여 “추가 보드 관리자 URL”란에 http://arduino.esp8266.com/stable/package_esp8266com_index.json을 입력하세요. 그런 다음 도구 `보드` 보드 관리자로 이동하여 "esp8266"을 검색하고 설치하세요.

![이미지 1](/assets/img/2024-05-20-TheEaseofIoTwithESP8266_1.png)

![이미지 2](/assets/img/2024-05-20-TheEaseofIoTwithESP8266_2.png)

3. 보드 관리자 열기. 도구 `보드` 보드 관리자로 이동하세요.

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

<img src="/assets/img/2024-05-20-TheEaseofIoTwithESP8266_3.png" />

4. 이제 "ESP8266 by ESP8266 Community"을 보면 설치하십시오.

<img src="/assets/img/2024-05-20-TheEaseofIoTwithESP8266_4.png" />

이제 ESP8266에 플래시할 모든 것을 설치했습니다. 나중에 ESP8266을 플래시할 수 있습니다.

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

ESP8266 작업을 위한 준비하기

자, 이제 ESP8266에 필요한 배선 작업을 해봅시다. 저는 ESP01 보드를 사용했기 때문에 여기 참고 사항이 있어요:

- GPIO0: 이것은 "플래시" 버튼입니다. ESP8266을 프로그래밍 모드로 전환하려면 접지(GND)에 연결해야 합니다. 이것은 매우 중요합니다. 그렇지 않으면 ESP8266 칩에 플래시할 수 없습니다. 또한 플래시 후에 ESP8266에서 코드를 실행하려면 GND에서 분리하는 것을 잊지 마세요.
- RX 및 TX: 이것은 통신 라인입니다. 이것들을 아두이노 Uno의 TX 및 RX 핀에 연결하여 코드를 ESP8266으로 전송합니다.
- GPIO2: 이것은 우리가 전구를 제어하는 데 사용할 GPIO 핀입니다. 이것을 내 두 채널 릴레이에 연결했습니다.
- 3.3V 및 GND: 이것은 전원 라인입니다. ESP8266이 3.3V(5V가 아님!)의 안정적인 전원을 받고 있는지 확인하고, 그 접지가 Uno의 접지에 연결되어 있는지 확인하세요. 조심하세요, ESP01은 3.3V 이상에서 작동하지 않으며, 3.3V 이상으로 전원을 공급하면 칩을 망가뜰 수 있습니다.

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

올바르게 연결하면 다음과 같은 화면을 볼 수 있습니다.

![image1](/assets/img/2024-05-20-TheEaseofIoTwithESP8266_6.png)

![image2](/assets/img/2024-05-20-TheEaseofIoTwithESP8266_7.png)

ESP-01이 불이 들어오는 것을 볼 수 있는데, 이와 관련해서 연결을 확인해보세요.

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

좋아요, 이제 아두이노 클라우드의 매력을 설정해 봅시다!

## 물건과 장치: 아두이노 클라우드 해독하기

아두이노 클라우드에서 모든 것은 두 가지 주요 개념을 중심으로 움직입니다:

- 물건(Thing): 이것은 빛을 킴 또는 다른 가전제품을 연결한 가상 표현입니다. 이는 클라우드에 살고 있는 빛을 킴이나 LED의 디지털 쌍둥이같은 존재입니다.
- 장치(Device): 이것은 실제 하드웨어인 ESP8266 칩으로, 빛을 킨 물건을 제어하는 것입니다.

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

다음과 같이 생각해보세요: '것'은 여러분의 전구의 개념이고, 장치는 전구 자체입니다. 아두이노 클라우드를 사용하면 이 둘을 연결하여 가상의 대응물을 통해 실제 장치를 제어할 수 있습니다. Boolean, integer 및 float와 같은 유형을 가진 변수도 추가할 수 있습니다. 그러면 코드에서 '것'의 상태를 평가할 수 있습니다. 참 멋지죠? 이 곳에는 많은 가능성이 있습니다.

## 첫 번째 장치 만들기 (ESP8266 추가):

- 장치: 아두이노 클라우드에서 "장치" 탭으로 이동합니다. 여기에서 계정에 연결된 모든 물리적 기기를 관리할 수 있습니다.
- 장치 추가: "장치 추가" 버튼을 클릭합니다. 일반적으로 페이지의 우측 상단이나 중앙에 위치합니다.
- 타사 장치: "타사 장치" 옵션을 선택합니다. ESP8266이 공식 아두이노 보드가 아니라 우리의 그룹에 환영받는 친구 외부인이기 때문입니다.
- ESP8266 선택: 지원되는 장치 목록에서 "일반 ESP8266 모듈"을 선택합니다.

<img src="/assets/img/2024-05-20-TheEaseofIoTwithESP8266_8.png" />

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

- 디바이스명 설정: ESP8266에 이름을 부여하거나 Arduino Cloud에서 생성된 기본 이름을 사용할 수 있습니다.
- 중요한 키: Arduino Cloud는 ESP8266을 위한 고유한 디바이스 ID와 시크릿 키를 생성합니다. 이것들은 클라우드 상에서 디바이스의 신원을 확인하기 위한 칩의 비밀 악수처럼 작용합니다. 이 키들을 안전한 곳에 복사하고 저장해두세요. 디바이스를 나중에 물건에 연결할 때 필요할 것입니다. 또는 Arduino Cloud가 제공하는 PDF를 다운로드하여 시크릿 키를 포함한 것을 확인할 수 있습니다.

![이미지](/assets/img/2024-05-20-TheEaseofIoTwithESP8266_9.png)

- 물건과 연결: 키를 저장한 후, 기존 물건과 디바이스를 연결하거나 새로 만듭니다. 이 단계에서 물리적인 ESP8266을 Arduino Cloud상의 가상 표현과 연결합니다.

## 첫 번째 물건 만들기 (전구 또는 LED 추가):

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

- 클라우드로 이동하세요: 아두이노 클라우드에서 "Things" 탭으로 이동하세요. 이것들은 당신의 장치(ESP8266)에 의해 물리적 세계에서 제어될 구성 요소/위젯들입니다.
- 장치에 이름 붙이기: 당신의 장치에 이름을 지어주세요. 아니면 아두이노 클라우드에서 생성된 기본 이름을 사용할 수도 있습니다.
- 장치 선택하기: "장치 연결"을 클릭하고 ESP8266을 위해 아두이노 클라우드에서 만든 장치를 선택하거나, 해당 장치에 지정한 이름이나 기본 이름, 또한 "일반 ESP8266 모듈" 유형을 확인할 수 있습니다.
- 장치 연결하기: 오른쪽에있는 "네트워크" 탭 아래에서 "구성"을 클릭하고, 새로 만든 물건과 ESP8266을 연결하는 지침에 따르세요. 일반적으로 Wi-Fi 자격 증명을 입력해야 합니다. 이는 ESP8266으로 코드를 플래시하기 위한 스케치를 생성합니다.

![이미지](/assets/img/2024-05-20-TheEaseofIoTwithESP8266_10.png)

![이미지](/assets/img/2024-05-20-TheEaseofIoTwithESP8266_11.png)

- 장치가 생겼어요! 이제 클라우드에서 가상의 집을 갖게 된 당신의 전구나 LED(또는 연결한 것)이 있습니다.

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

## 변수를 항목에 추가하기:

이제 항목을 상호 작용 가능하게 만들기 위해 변수를 추가해야 합니다. 이러한 변수는 제어하거나 모니터링하려는 다른 항목을 나타냅니다. 우리의 전구 또는 LED를 위해 "ON" 또는 "OFF"가 될 수있는 부울 변수 유형인 lightState라는 변수를 만들 것입니다.

# 대시보드 만들기

이제 프로젝트를 더 멋지게 만들어 보죠. Arduino Cloud에 대시보드를 설정하여 어디서나 IoT 장치를 모니터링하고 제어할 수 있습니다. Arduino Cloud의 대시보드는 버튼, 토글 스위치, 푸시 버튼 등을 만들 수 있어서 활용도가 높습니다. 사용하기 쉽고 즐겁습니다!

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

여기에서 만드는 대시보드는 모바일 장치에서도 사용할 수 있습니다.

- "대시보드" 섹션으로 이동합니다.
- "대시보드 만들기"를 클릭하고 이름을 지정합니다.
- 대시보드에 스위치 위젯을 추가합니다. 이 스위치를 이전에 만든 lightState 변수에 링크합니다.
- 원하는 경우 대시보드의 모양과 느낌을 사용자 정의합니다.

![대시보드 이미지](/assets/img/2024-05-20-TheEaseofIoTwithESP8266_12.png)

생성된 스케치 다운로드하기

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

모든 단계가 완료되었으면, Arduino Cloud는 ESP8266 WiFi 자격 증명을 제공한 구성에 맞게 구성하고 "Things" 탭에서 정의된 변수에 대한 MQTT 수신기를 추가했습니다.

이제 생성된 스케치를 다운로드해야 합니다.

- 스케치 보기: Arduino Cloud에서 "스케치"를 열기
- 장치용 스케치 찾기: 표에 표시된대로, 장치용으로 생성된 스케치를 찾을 수 있습니다. 세 개의 점을 클릭하고 "스케치 다운로드"를 선택하세요

![이미지](/assets/img/2024-05-20-TheEaseofIoTwithESP8266_13.png)

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

- 압축 해제 및 업로드: 다운로드한 파일을 압축 해제하고 .ino 파일(스케치의 주요 부분입니다)을 Arduino IDE에서 엽니다.

ArduinoIoTCloud 라이브러리 설치: 연결을 강화

ESP8266가 Arduino Cloud와 대화하기 전에 번역기가 필요합니다. 여기서 ArduinoIoTCloud 라이브러리가 필요합니다. 이 편리한 라이브러리는 칩이 클라우드의 언어를 이해하도록 도와주어 통신이 간편해집니다.

- Arduino IDE를 엽니다.
- “Sketch” - “Include Library” - “Manage Libraries”로 이동합니다.
- 검색란에 “ArduinoIoTCloud”를 검색합니다.

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

<img src="/assets/img/2024-05-20-TheEaseofIoTwithESP8266_14.png" />

- 최신 버전에 "설치"를 클릭하세요.

와우, 아두이노 클라우드 및 설정에 대해 모두 설정을 하셨네요! 이제 플래싱 부분으로 넘어가서, 저희는 대장정에 가까워지고 있어요!

ESP8266 플래싱: 프로그래머가 필요 없을 때도 문제 없습니다!

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

그렇습니다! 온라인 자료에서 ESP8266을 플래싱하려면 외부 프로그래머가 필요하다고 보셨을 수도 있지만, 실제로는 그렇지 않습니다. 신뢰할 수 있는 아두이노 Uno를 ESP8266 프로그래머로 변신시킬 수 있어요. 예, 멋진 외부 하드웨어는 필요 없어요!

아두이노 Uno의 비밀 능력:

아두이노 Uno에는 숨겨진 재능이 있어요. USB-시리얼 변환기처럼 작동할 수 있어요. 바로 이 기능을 통해 코드를 ESP8266에 업로드할 수 있게 되는거죠. Uno가 컴퓨터와 칩 사이의 번역기 역할을 하는 것 같아요.

이제 코드를 플래싱할 시간입니다. 아두이노 IDE에서 '일반 ESP8266 모듈'을 보드로 선택하세요. 이전 지침을 따라왔다면, 이 보드 유형이 보드 관리자에 사용 가능해야 합니다.

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

<img src="/assets/img/2024-05-20-TheEaseofIoTwithESP8266_15.png" />

ESP-01은 플래싱이나 부팅용 내장 버튼이 없습니다. 플래시 모드로 설정하려면 GPIO0 핀을 접지해야 합니다. GPIO0은 활성화가 LOW일 때 플래싱이 가능하므로, 접지에 연결하는 것이 중요합니다.

GPIO0를 접지한 후, ESP-01을 다시 시작하세요. 이를 위해 VCC 핀을 잠시 연결 해제한 다음 다시 연결하거나, 연결하신 경우 RST 핀을 사용할 수 있습니다.
그 후, 아두이노 IDE에서 "업로드"를 클릭하여 코드를 업로드할 수 있습니다. 이것은 아두이노 보드에 코드를 업로드하는 것과 동일하지만, 이번에는 코드가 ESP8266으로 업로드됩니다.

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

ESP8266가 클라우드에 연결되었는지 확인하는 중입니다.

플래싱 후 ESP8266을 다시 시작하려면 GPIO0을 GND에서 분리하십시오 (GND에 연결되어 있으면 코드가 실행되지 않을 수 있습니다. 플래싱 모드로 설정되기 때문입니다). 그리고 VCC에서 잠시 분리하거나 RST 핀을 사용하여 ESP8266을 다시 시작하십시오.

그런 다음 Arduino Cloud 장치에서 연결이 성공적으로 이루어져 있는지 확인할 수 있습니다.
![이미지](/assets/img/2024-05-20-TheEaseofIoTwithESP8266_16.png)

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

GPIO 코드 트리거링

이제 ESP01의 GPIO2에 작성할 코드를 추가해 보겠습니다.

"yourvariableOnChange"라는 이름으로 Arduino Cloud에서 코드에 추가된 리스너를 볼 수 있을 겁니다. 우리의 경우에는 onLightStateChange일 것입니다.

함수를 다음과 같이 수정해주세요:

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
void onLightStateChange()  {
  if(lightState) {
    digitalWrite(2, HIGH)
  } else {
    digitalWrite(2, LOW)
  }
}
```

그냥 간단히 말해서, 우리는 lightState가 true이면(기억해요, 우리는 "things" 섹션에서 불리언 변수 형식을 설정했어요) ESP01의 GPIO2를 트리거합니다.

빛을 제어하기: 릴레이로

우리 ESP8266은 작은 칩이기 때문에, 전구나 LED를 직접 켜는 데 필요한 고전압을 처리할 수 없습니다. 여기서 릴레이가 필요해요. 릴레이는 스위치처럼 작용하여 ESP8266이 과부하되지 않고 전구로 전기의 흐름을 제어할 수 있도록 합니다.

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

나의 선택한 릴레이: 두 채널 릴레이

이 프로젝트에서 두 채널 릴레이를 사용하기로 결정했어요. 이 뜻은 한 개의 릴레이로 두 개의 조명을 제어할 수 있다는 거예요. 하지만 걱정 마세요, 한 개의 조명만 제어하고 싶다면 단일 채널 릴레이를 마찬가지로 사용할 수 있어요.

![이미지](/assets/img/2024-05-20-TheEaseofIoTwithESP8266_17.png)

이 릴레이는 UNO의 5V로 전원을 공급받고, 입력 트리거는 ESP01의 GPIO2에 연결돼 있어요. 이전에 말했듯이 GPIO 포트가 조명을 트리거하는 데 사용될 거예요.

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

3.5V 입력이 릴레이에 입력되고 ESP8266을 통해 Arduino Cloud 입력을 트리거하는 테스트입니다.

![이미지](https://miro.medium.com/v2/resize:fit:1200/1*LfvnsCP7DW443oqhwnVrOA.gif)

전압이 얼마나 빨리 0V로 떨어지는지 주목하세요 (Arduino Cloud의 메시지 브로커가 메시지를 효율적으로 처리하고 있음을 알 수 있습니다)

이제 전원 어댑터를 LED 스트립에 연결할 시간입니다. 12V 어댑터의 양(+) 선을 릴레이의 입력 단자에 연결하는 것부터 시작하세요.

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

그 다음, 점퍼 와이어를 사용하여 릴레이의 출력 단자를 LED 스트립의 양극(+) 선에 연결합니다.

마지막으로 LED 스트립의 접지(GND) 선을 어댑터의 접지(GND) 선에 연결합니다. 이 연결은 회로를 완성하는 데 필수적입니다.

모든 것이 올바르게 연결되었다면, 이제 전 세계 어디에서나 Arduino Cloud 앱을 사용하여 LED 또는 전구를 원격으로 제어할 수 있습니다(물론 인터넷 연결이 있어야 합니다)!

![이미지](https://miro.medium.com/v2/resize:fit:1200/1*Szzv4DC3sx2zFPSgFOv44g.gif)

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

표 태그를 Markdown 형식으로 변경하세요.
