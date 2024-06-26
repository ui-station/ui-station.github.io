---
title: "인터넷을 통한 사물인터넷의 시작 ESP32와 ESP8266을 WiFi에 연결하기"
description: ""
coverImage: "/assets/img/2024-05-20-TheGatewaytoIoTConnectingESP32andESP8266toWiFi_0.png"
date: 2024-05-20 19:35
ogImage:
  url: /assets/img/2024-05-20-TheGatewaytoIoTConnectingESP32andESP8266toWiFi_0.png
tag: Tech
originalTitle: "The Gateway to IoT: Connecting ESP32 and ESP8266 to WiFi"
link: "https://medium.com/@vaibhav01gour/the-gateway-to-iot-connecting-esp32-and-esp8266-to-wi-fi-6cef590cf20a"
---

## Bits, Bots

![image](https://miro.medium.com/v2/resize:fit:1400/1*T8gZxLjRsz3Tn69ovLuIQg.gif)

## 소개

안녕하세요! "Bits & Bots" 시리즈에서 또 다른 흥미진진한 챕터로 오신 것을 환영합니다! 오늘은 IoT 프로젝트의 필수 첫 단계인 ESP32 및 ESP8266 마이크로컨트롤러를 Wi-Fi에 연결하는 방법에 대해 살펴보겠습니다. 이 중요한 연결이 확립되면 여러분의 장치가 인터넷의 거대한 세계에 접속하여 데이터를 보내고 받고, 다른 장치들과 상호작용하며 IoT 기적의 가능성을 최대로 발휘할 수 있게 됩니다.

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

## 기본 사항

Wi-Fi 연결의 세부 사항을 들어가기 전에 기본 개념을 이해해 봅시다. Wi-Fi는 무선 네트워킹 기술로, 장치가 인터넷에 연결되어 로컬 네트워크를 통해 서로 통신할 수 있게 해줍니다. ESP32와 ESP8266 마이크로컨트롤러는 내장 Wi-Fi 기능을 갖추고 있어 IoT 프로젝트에 이상적인 후보들입니다.

Wi-Fi는 2.4 GHz 및 5 GHz 주파수 대역에서 작동하며, 간섭을 피하고 안정적인 통신을 보장하기 위해 다양한 채널을 제공합니다. ESP32는 2.4 GHz와 5 GHz 대역을 모두 지원하는 반면, ESP8266은 2.4 GHz 대역만 지원합니다. 이 내장된 Wi-Fi 기능은 마이크로컨트롤러를 네트워크에 연결하는 과정을 단순화하여 추가 하드웨어가 필요 없게 합니다.

Wi-Fi 네트워크에 연결할 때 여러 주요 개념들이 관련됩니다:

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

SSID (Service Set Identifier): 이것은 연결하려는 Wi-Fi 네트워크의 이름입니다. 다른 Wi-Fi 네트워크를 구별하는 식별자입니다. 기기를 열어 사용 가능한 네트워크를 찾을 때 보이는 목록은 SSID들로 구성되어 있습니다.

패스워드: 무단 접속을 방지하기 위해 Wi-Fi 네트워크는 대부분 패스워드로 보호됩니다. 이 패스워드는 WPA (Wi-Fi Protected Access) 또는 WPA2와 같은 암호화 프로토콜을 사용하여 보안이 적용됩니다. WPA2는 가장 흔하고 안전한 방법입니다.

IP 주소: 네트워크에 연결된 후 각 기기에는 고유한 IP 주소가 할당되어 다른 기기와 통신할 수 있도록 합니다. IP 주소는 DHCP (Dynamic Host Configuration Protocol) 서버에 의해 동적으로 할당되거나 정적으로 설정될 수 있습니다.

MAC 주소: 이는 각 네트워크 인터페이스에 할당된 고유한 식별자입니다. 네트워크는 이 MAC 주소를 사용하여 기기를 식별하고 관리합니다.

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

## 환경 설정 준비

시작하려면 Arduino IDE 및 ESP32 및 ESP8266을 위한 필수 보드 정의가 포함된 개발 환경을 설정해야 합니다. 아직 수행하지 않았다면, 모든 것을 원활하게 작동하도록 설정하는 친절한 안내서를 따르세요.

이 기사를 참고하여 환경을 설정할 수 있습니다 - 개발 환경 설정하기 : ESP32/ESP8266

## Wi-Fi에 연결

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

자, 이제 본격적으로 시작해 봅시다. Arduino 스케치에서 WiFi.h 라이브러리를 포함하여 ESP32 및 ESP8266 플랫폼에서 제공하는 Wi-Fi 기능에 액세스하세요.

WiFi.begin() 함수를 사용하여 Wi-Fi 네트워크에 연결을 초기화하고 매개변수로 SSID와 암호를 제공하세요. 아래는 단계별 예시입니다:

```js
#include <WiFi.h>  // ESP32 및 ESP8266용 WiFi 라이브러리 포함

const char* ssid = "your_SSID";  // 사용 중인 네트워크 SSID로 바꿔주세요
const char* password = "your_PASSWORD";  // 사용 중인 네트워크 암호로 바꿔주세요

void setup() {
  // 시리얼 모니터 초기화
  Serial.begin(115200);

  // WiFi 연결 시작
  Serial.print("연결 중: ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);

  // 연결 대기
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.print(".");
  }

  // 연결되었을 때
  Serial.println("");
  Serial.println("WiFi 연결됨.");
  Serial.print("IP 주소: ");
  Serial.println(WiFi.localIP());
}

void loop() {
  // 여기에 주요 코드를 작성하세요
}
```

## 결론

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

여기에서 ESP32와 ESP8266을 Wi-Fi에 연결하는 포괄적인 안내서가 완성되었습니다. 이 중요한 단계를 완료하면 여러분의 장치들은 IoT 여행을 떠날 준비가 되었고, 세계와 통신하며 마법을 일으킬 수 있게 됩니다. "Bits & Bots" 시리즈에서 더 많은 모험을 기대해주세요. 거기서는 IoT 개발의 고급 주제를 탐구하고 이 놀라운 마이크로컨트롤러들의 모든 잠재력을 발휘할 것입니다.

## 다가오는 이야기!

Wi-Fi로 연결된 프로젝트를 다음 수준으로 이끌 준비가 되셨나요? 저희의 다음 블로그 글을 기대해주세요. 거기서는 ESP32와 ESP8266로 원격 데이터 통신과 제어의 흥미로운 세계를 탐험할 것입니다. 그 때까지, 즐거운 개발되세요!
