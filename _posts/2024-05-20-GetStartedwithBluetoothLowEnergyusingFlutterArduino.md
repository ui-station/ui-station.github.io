---
title: "플러터와 아두이노를 활용한 블루투스 Low Energy 시작하기"
description: ""
coverImage: "/assets/img/2024-05-20-GetStartedwithBluetoothLowEnergyusingFlutterArduino_0.png"
date: 2024-05-20 19:32
ogImage:
  url: /assets/img/2024-05-20-GetStartedwithBluetoothLowEnergyusingFlutterArduino_0.png
tag: Tech
originalTitle: "Get Started with Bluetooth Low Energy using Flutter , Arduino"
link: "https://medium.com/@danielwolf.dev/get-started-with-bluetooth-low-energy-using-flutter-arduino-bdf5d790edc"
---

이 기사에서는 실용적인 방식으로 Bluetooth Low Energy (BLE) 기본 사항 중 일부를 설명하고 있습니다. 아두이노 Nano 33 BLE Sense에 주변(서버)을 구현함으로써, BLE을 실제로 경험해볼 수 있습니다. 이후 두 번째 부분에서는 Flutter를 사용하여 Nano에서 일부 데이터를 검색하는 중심(클라이언트) 앱을 만드는 방법을 보여줄 것입니다. 가능한 한 간단히 유지하려고 하므로, 초보자도 따르기 쉬울 것입니다.

우리의 실용적인 예제에서는 사용자가 방 온도에 관심이 있을 때 앱(중앙)을 통해 BLE을 통해 아두이노 Nano(주변)에 연결하고 온도 센서의 실시간 데이터를 확인할 수 있도록 할 것입니다.

## 왜 Bluetooth Low Energy를 사용해야 하나요?

이미 이름에서 알 수 있듯이, BLE은 다른 기술(예: Wi-Fi)에 비해 적은 에너지를 사용하도록 설계되었습니다. 배터리로 작동되는 IoT 장치에 완벽하게 맞을 수 있으며 데이터를 제공하는 동안 최대한 오랫동안 지속되어야 하는 요구 사항과 잘 어울립니다. 다른 기술과의 자세한 비교는 이 곳에서 찾을 수 있습니다.

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

# 주변기기

## 준비물

NANO 33 제품 페이지는 기본 정보를 얻기에 항상 좋은 출발점입니다. 아두이노를 처음 사용해보는 경우, 해당 페이지로 이동하여 설정 가이드 단추를 눌러 설치 과정을 진행하십시오. 아두이노 IDE를 성공적으로 설치한 후에는 NANO 보드를 위해 IDE를 설정해야 합니다. Mbed OS를 사용하고 있으므로 이곳에서 단계별 지침을 찾을 수 있습니다.

아두이노 IDE를 실행하고 “파일” → “예제” → “기본” → “깜박임”을 선택합니다. 스케치가 열리면 상단 왼쪽 드롭다운 메뉴에서 “Arduino Nano 33 BLE”를 선택합니다. 마지막으로 업로드 버튼 (오른쪽 화살표)을 누릅니다. 몇 초 후에 아두이노의 주황색 LED가 매초 켜졌다 꺼집니다. 이제 귀하의 장치와 NANO가 준비된 상태여야 합니다.

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

<img src="/assets/img/2024-05-20-GetStartedwithBluetoothLowEnergyusingFlutterArduino_0.png" />

## Arduino BLE

BLE를 사용하는 방법을 처음 알아보려면 Nano 33 BLE Sense 치트 시트의 Bluetooth 섹션을 확인해보세요. 거기에서 볼 수 있듯이, Bluetooth 기능을 사용하려면 추가적인 라이브러리가 필요합니다.

아두이노 IDE로 이동하여 라이브러리 관리자 (왼쪽 막대기의 책 아이콘)를 열고 "ArduinoBLE"를 검색해보세요. "설치" 버튼을 눌러 사용 가능한 최신 버전을 가져오세요.

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

![Bluetooth Low Energy 사용 시작하기 Flutter와 Arduino](/assets/img/2024-05-20-GetStartedwithBluetoothLowEnergyusingFlutterArduino_1.png)

"파일" → "예제" → "ArduinoBLE" → "페리페럴" 메뉴에서 BLE 라이브러리를 사용하는 유용한 예제를 찾을 수 있습니다. "BatteryMonitor" 예제를 열어보겠습니다.

이 스케치는 주변 장치에서 중앙 장치로 지속적으로 변경되는 값을(배터리) 보내는 기본 구조를 보여줍니다. 다행히도, 우리가 원하는 바는 배터리 레벨이 아닌 온도를 사용하는 것입니다! 코드를 조금 더 자세히 살펴보겠습니다.

```js
BLEService batteryService("180F");
BLEUnsignedCharCharacteristic batteryLevelChar("2A19", BLERead | BLENotify);
```

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

먼저 UUID가 "180F"인 서비스 인스턴스를 생성합니다. 그리고 아래에서는 같은 방식으로 UUID가 "2A19"인 특성도 생성됩니다. 이후에는 설정 함수에서 특성을 서비스에 추가하고, 서비스를 전체적인 "BLE" 클래스에 추가하게 됩니다.

```js
batteryService.addCharacteristic(batteryLevelChar);
BLE.addService(batteryService);
```

이 동작은 BLE 프로토콜의 일반 속성 프로파일 (GATT) 계층 사양을 반영하며, 이는 주변 기기가 여러 개의 특성을 그룹화한 여러 서비스를 가질 수 있음을 의미합니다. 이러한 특성들을 통해 중앙 기기는 연결이 설정되면 읽기 및 쓰기 작업을 수행하거나, 새 값을 기기 측에 기록할 때마다 알림/표시를 수신할 수 있습니다.

알림(Notify)은 중앙 기기가 특성을 구독하고 주변 기기 측에 기록된 새 값(예: 배터리 수준 또는 온도)마다 업데이트를 받는 것을 의미하며, "읽기"는 항상 중앙 기기에 의해 시작되며 호출 시에만 발생합니다.

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

중앙 장치에서 주변 장치로 값을 전송하려면, 중앙 장치는 특성의 쓰기 작업을 사용할 수 있습니다. "batteryLevelChar"의 인스턴스화에서 보듯이, 이 특성에는 읽기와 알림이 모두 사용될 수 있습니다.

아래 코드는 중앙 장치가 주변 장치에 연결되어 있고 200밀리초가 경과한 경우에만 알림 작업이 실행되도록 보장합니다.

```js
while (central.connected()) {
  long currentMillis = millis();
  if (currentMillis - previousMillis >= 200) {
    previousMillis = currentMillis;
    updateBatteryLevel();
  }
}
```

그런 다음, 배터리 값은 A0 핀에서 계속 읽히고, 값이 변경된 경우에만 특성에 writeValue를 호출하여 청취하는 중앙 장치에 전파됩니다.

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
void updateBatteryLevel() {
  int battery = analogRead(A0);
  int batteryLevel = map(battery, 0, 1023, 0, 100);

  if (batteryLevel != oldBatteryLevel) {
    Serial.print("Battery Level % is now: ");
    Serial.println(batteryLevel);
    batteryLevelChar.writeValue(batteryLevel);
    oldBatteryLevel = batteryLevel;
  }
}
```

마지막 단계는 근처 장치가 근처 장치에게 광고 패키지를 브로드캐스팅하도록 하여 페리퍼럴을 발견 가능하게 하는 것입니다. 이것은 advertise를 호출하여 수행됩니다.

```js
BLE.setLocalName("BatteryMonitor");
BLE.advertise();
```

“setLocalName” 메서드는 광고 패키지 내의 모든 근처 장치로 전송될 이름 정보를 정의합니다. 이것은 예를 들어 모바일 폰과 연결하고 싶을 때 장치를 식별하는 데 도움이 됩니다. 더 자세한 정보는 “ArduinoBLE” 라이브러리 페이지를 확인해 주세요.

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

이제 우리가 핸드폰의 배터리 레벨의 지속적인 업데이트를 받을 수 있는지 확인해 봅시다. 간단한 디버깅을 위해 LightBlue나 NRF Connect for Mobile을 사용할 수 있습니다. 이 두 앱은 주변 기기를 발견하고 그들의 특성 동작을 시도하는 데 꽤 좋습니다.

위의 비디오에서는 "LightBlue" 앱을 사용하여 Nano에 연결하는 방법, 배터리 레벨 특성과 해당 속성을 표시하는 방법, 읽기 동작을 트리거하고 배터리 레벨 업데이트를 구독하는 방법을 보여줍니다. 이러한 기능들은 우리가 나중에 우리 자신의 중앙 앱에서 복제할 것입니다.

## 주변 기기 적응

배터리 샘플은 이미 온도를 보내는 등 우리가 필요한 모든 것을 수행하고 있기 때문에 조금 수정하여야 합니다. 나노에서 현재 온도를 받을 수 있는 방법은 다시 한 번 치트 시트를 확인해 봅시다.

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

먼저 라이브러리 관리자로 이동하여 이전에 "ArduinoBLE"와 같이 HS3003 라이브러리를 설치해주세요. 그런 다음 다음 코드로 변경할 것입니다:

```js
#include <ArduinoBLE.h>
#include <Arduino_HS300x.h>

#define BUFFER_SIZE 20

BLEService temperatureService = BLEService("00000000-5EC4-4083-81CD-A10B8D5CF6EC");
BLECharacteristic temperatureCharacteristic = BLECharacteristic("00000001-5EC4-4083-81CD-A10B8D5CF6EC", BLERead | BLENotify, BUFFER_SIZE, false);

int oldTemperature = 0;
long previousMillis = 0;

void setup() {
  Serial.begin(9600);
  while (!Serial)
    ;

  if (!HS300x.begin()) {
    Serial.println("Failed to initialize HS300x.");
    while (1)
      ;
  }

  // initialize the built-in LED pin to indicate when a central is connected
  pinMode(LED_BUILTIN, OUTPUT);

  if (!BLE.begin()) {
    Serial.println("starting BLE failed!");

    while (1)
      ;
  }

  BLE.setLocalName("BLE-TEMP");
  BLE.setDeviceName("BLE-TEMP");

  temperatureService.addCharacteristic(temperatureCharacteristic);
  BLE.addService(temperatureService);
  temperatureCharacteristic.writeValue("0.0");
  BLE.advertise();

  Serial.println("Bluetooth® device active, waiting for connections...");
}

void loop() {
  BLEDevice central = BLE.central();

  if (central) {
    Serial.print("Connected to central: ");
    Serial.println(central.address());
    digitalWrite(LED_BUILTIN, HIGH);

    while (central.connected()) {
      long currentMillis = millis();
      if (currentMillis - previousMillis >= 200) {
        previousMillis = currentMillis;
        updateTemperature();
      }
    }

    digitalWrite(LED_BUILTIN, LOW);
    Serial.print("Disconnected from central: ");
    Serial.println(central.address());
  }
}

void updateTemperature() {
  float temperature = HS300x.readTemperature();

  if (temperature != oldTemperature) {
    char buffer[BUFFER_SIZE];
    int ret = snprintf(buffer, sizeof buffer, "%f", temperature);
    if (ret >= 0) {
      temperatureCharacteristic.writeValue(buffer);
      oldTemperature = temperature;
    }
  }
}
```

첫 번째 변경점은 "BLEUnsignedCharCharacteristic" 대신 "BLECharacteristic"를 사용했다는 것입니다. 이를 통해 중앙에 데이터를 보내는 방식에 대해 더 많은 유연성을 제공합니다. 여기에서 보다시피, "ArduinoBLE" 라이브러리는 여러 가지 유형의 특성을 정의하고 선택할 수 있습니다. 그러나 "BLECharacteristic"를 사용하면 특정 유형으로 제한되지 않습니다.

BLECharacteristic는 보내려는 바이트 버퍼의 최대 크기 및 데이터를 보낼 때 이 버퍼의 길이를 고정할지 여부를 더 많이 제공받길 원합니다. BLE 4.0 이상과 호환되기 위해 이번 케이스에서는 온도 값을 보내려고 하므로 20바이트를 넘지 않아야합니다. 데이터 페이로드를 보내는 데 제한 사항에 대한 자세한 내용은 여기에서 확인할 수 있습니다.

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

"updateTemperature" 함수를 좀 더 자세히 살펴봅시다. 온도 값을 부동 소수점으로 읽은 후 "snprintf"를 사용하여 문자열로 변환하고 중심에 보낼 바이트 버퍼에 기록합니다. 호환성을 위해 이 버퍼의 길이가 20바이트를 초과하지 않도록 주의하세요. 버퍼를 보내려면 캐릭터리스틱에 "writeValue"를 호출하면 됩니다.

"setLocalName" 아래에 추가한 한 줄을 눈치채셨을 수도 있습니다.

```js
BLE.setLocalName("BLE-TEMP");
BLE.setDeviceName("BLE-TEMP");
```

이전에 언급했던대로, "setLocalName"은 광고 패키지를 통해 전송되는 이름을 설정합니다. 반면에 "setDeviceName" 메서드는 일반 액세스 서비스(1800)의 디바이스 이름(2A00) 특성 값을 설정합니다. 외형 특성(2A01)과 함께 이는 GATT 명세에 따라 필수이며 따라서 미리 정의되어 있습니다.

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

iOS 및 macOS에서는 예를 들어, 페리페럴이 이전에 연결된 적이 없는 경우, 광고 패키지 내의 이름이 발견 중에 표시됩니다. 그런 다음 페리페럴에 연결하면 기기 특성의 기기 이름이 자동으로 읽혀 OS에 캐시됩니다. 다음 발견 프로세스 중에 OS는 광고된 이름 대신에 캐시된 기기 이름을 표시합니다. 이러한 동작이 혼란을 야기할 수 있으므로 두 값을 동일하게 설정했습니다. 여기에 몇 가지 추가 정보가 있습니다.

스케치를 업로드하고 디버깅 앱으로 다시 확인하면 온도 값이 수신되는 것을 볼 수 있어야 합니다. 수신된 데이터를 문자열로 변환하는 것을 잊지 마세요.

## UUIDs

앱 부분으로 향하기 전에 서비스와 특성을 위한 UUID를 선택하는 방법에 대해 논의하고 싶습니다. 배터리 예제에서 이전에 선택한 16비트 UUID 대신에 이제 서비스와 특성에 128비트 UUID를 제공했음을 알아차리셨을 것입니다.

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

서비스 및 특성은 항상 128비트 UUID를 사용합니다. 여기에서 할당된 숫자 문서를 보시면, 배터리 서비스와 배터리 레벨이 Bluetooth SIG에 의해 이미 정의되어 있고, 그에 따라 16비트 별칭(0x180F 및 0x2A19)로 참조할 수 있음을 알 수 있습니다. 모든 미리 정의된 서비스 및 특성에 대해 128비트 UUID는 다음과 같이 계산됩니다:

BluetoothBaseUUID가 00000000-0000-1000-8000-00805F9B34FB인 경우입니다.

만약 미리 정의된 UUID에 구속받지 않고 자체 UUID를 선택하고 싶다면, 미리 정의된 UUID와 충돌하지 않도록 주의해야 합니다.

# 중앙

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

## 전제 조건

이미 Flutter 앱을 만들었고 모든 것이 올바르게 설정되어 있다면, 이 섹션을 건너뛰실 수 있습니다.

Android Studio 다운로드 페이지로 이동하여 설치 지침을 따라 Android Studio를 설치하세요. Android Studio를 열고 "Plugins"로 이동하세요. "Dart"를 검색하여 아래 그림에 표시된 대로 Dart 및 Flutter 플러그인을 설치하세요.

![그림](/assets/img/2024-05-20-GetStartedwithBluetoothLowEnergyusingFlutterArduino_2.png)

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

iOS 애플리케이션도 배포하고 싶다면 Xcode도 추가로 필요합니다.

다음으로 플러터 설치 페이지로 이동하여 Flutter를 설치하도록 지침에 따르세요. 모든 것이 올바르게 설정되어 있다면 터미널에서 "flutter doctor" 명령을 실행하여 오류 메시지가 표시되지 않아야 합니다. 무언가 잘못되었을 경우 flutter doctor 명령행 도구의 조언에 따르세요.

![Image](/assets/img/2024-05-20-GetStartedwithBluetoothLowEnergyusingFlutterArduino_3.png)

## 프로젝트 생성

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

안녕하세요! Android Studio를 열고 "프로젝트"로 이동한 다음 "새 플러터 프로젝트"를 선택하세요. "Generators" 아래 왼쪽 창에서 "Flutter"를 선택한 후 "다음"을 눌러주세요.

![이미지](/assets/img/2024-05-20-GetStartedwithBluetoothLowEnergyusingFlutterArduino_4.png)

프로젝트에 이름을 지정하고 원하는 플랫폼(안드로이드 및 iOS)을 선택한 다음 "생성"을 눌러주세요.

![이미지](/assets/img/2024-05-20-GetStartedwithBluetoothLowEnergyusingFlutterArduino_5.png)

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

지금부터 프로젝트가 초기화되고 Android Studio에서 열릴 것입니다.

## Flutter BLE

iOS 및 Android에서 Bluetooth를 작동하게하려면 적절한 BLE 라이브러리가 필요합니다. 여러 가지가 있지만 제 생각에 가장 신뢰할 수있는 것은 "flutter_reactive_ble"입니다. 이 라이브러리는 정기적으로 유지보수되고 있습니다. 사용 방법을 확인하려면 readme를 확인하세요.

라이브러리를 작동하려면 다음 단계를 수행해야합니다:

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

프로젝트 루트의 "pubspec.yaml" 파일에 라이브러리 종속성을 추가하고 명령줄을 통해 "flutter pub get"을 실행하거나 Android Studio에서 "Pub get"을 실행하세요:

```yaml
dependencies:
  flutter:
    sdk: flutter
  flutter_reactive_ble: ^5.2.0
```

<img src="/assets/img/2024-05-20-GetStartedwithBluetoothLowEnergyusingFlutterArduino_6.png" />

최소한 21로 "android/app/build.gradle"의 "minSdkVersion"을 조정하세요:

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
defaultConfig {
    applicationId "com.example.ble_temp"
    minSdkVersion 21
    targetSdkVersion flutter.targetSdkVersion
    versionCode flutterVersionCode.toInteger()
    versionName flutterVersionName
}
```

다음 권한을 “android/app/src/main/AndroidManifest.xml”에 추가해주세요:

```js
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <uses-permission android:name="android.permission.BLUETOOTH_SCAN" />
    <uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />

    <application
        android:label="@string/app_name"
        android:name="${applicationName}"
        android:icon="@mipmap/ic_launcher">
...
```

Android 12(API 레벨 31 미만) 미만의 기기를 사용하려면 다른 매니페스트 설정이 필요합니다:

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

```json
{
  "manifest": "안드로이드 리소스를 정의합니다.",
  "permissions": [
    {
      "name": "android.permission.BLUETOOTH_SCAN",
      "flags": "위치 정보를 위해 결코 사용하지 않습니다."
    },
    {
      "name": "android.permission.BLUETOOTH_CONNECT"
    },
    {
      "name": "android.permission.ACCESS_COARSE_LOCATION",
      "maxSdkVersion": 30
    },
    {
      "name": "android.permission.ACCESS_FINE_LOCATION",
      "maxSdkVersion": 30
    }
  ],
  "application": {
    "label": "앱 이름",
    "name": "${applicationName}",
    "icon": "@mipmap/ic_launcher"
  }
}
```

게다가 readme에는 Android에서 블루투스 예외 처리가 제대로 되지 않아 일부 수정이 필요하다고 명시되어 있습니다. " /android/app/src/main/kotlin/com/example/ble_temp/MainActivity.kt" 파일을 수정해야 합니다.

```kotlin
package com.example.ble_temp

import io.flutter.embedding.android.FlutterActivity
import io.flutter.embedding.engine.FlutterEngine
import io.reactivex.exceptions.UndeliverableException
import io.reactivex.plugins.RxJavaPlugins
import com.polidea.rxandroidble2.exceptions.BleException

class MainActivity: FlutterActivity() {
    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)
        RxJavaPlugins.setErrorHandler { throwable: Throwable ->
            if (throwable is UndeliverableException && throwable.cause is BleException) {
                println("""Suppressed UndeliverableException: $throwable""")
                return@setErrorHandler   // BleExceptions은 확실히 최소한 한 번 전달되었으므로 무시합니다
            }
            throw RuntimeException("RxJavaPlugins 오류 처리기에 예기치 않은 Throwable이 발생했습니다.", throwable)
        }
    }
}
```

그리고 "android/app/build.gradle"에 "rxandroidble2" 종속성을 추가해야 합니다.

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
의존성 {
    구현 "com.polidea.rxandroidble2:rxandroidble:1.16.0"
}
```

iOS를 사용하려면 "ios/Runner/info.plist" 파일 사전에 다음을 추가해야 합니다:

```js
<dict>
  ...
  <key>NSBluetoothAlwaysUsageDescription</key>
  <string>
    The app uses Bluetooth to find your peripheral, connect to it and to
    exchange data.
  </string>
  <key>NSBluetoothPeripheralUsageDescription</key>
  <string>
    The app uses Bluetooth to find your peripheral, connect to it and to
    exchange data.
  </string>
  ...
</dict>
```

## 구현

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

중앙 코드로 바로 들어가 봅시다. 아래 코드로 "main.dart"를 교체해보세요:

```js
import 'dart:convert';
import 'dart:async';
import 'package:flutter/material.dart';
import 'package:flutter_reactive_ble/flutter_reactive_ble.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.deepPurple),
        useMaterial3: true,
      ),
      home: const MyHomePage(title: 'BLE Demo'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  const MyHomePage({super.key, required this.title});

  final String title;

  @override
  State<MyHomePage> createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  final _ble = FlutterReactiveBle();

  StreamSubscription<DiscoveredDevice>? _scanSub;
  StreamSubscription<ConnectionStateUpdate>? _connectSub;
  StreamSubscription<List<int>>? _notifySub;

  var _found = false;
  var _value = '';

  @override
  initState() {
    super.initState();
    _scanSub = _ble.scanForDevices(withServices: []).listen(_onScanUpdate);
  }

  @override
  void dispose() {
    _notifySub?.cancel();
    _connectSub?.cancel();
    _scanSub?.cancel();
    super.dispose();
  }

  void _onScanUpdate(DiscoveredDevice d) {
    if (d.name == 'BLE-TEMP' && !_found) {
      _found = true;
      _connectSub = _ble.connectToDevice(id: d.id).listen((update) {
        if (update.connectionState == DeviceConnectionState.connected) {
          _onConnected(d.id);
        }
      });
    }
  }

  void _onConnected(String deviceId) {
    final characteristic = QualifiedCharacteristic(
        deviceId: deviceId,
        serviceId: Uuid.parse('00000000-5EC4-4083-81CD-A10B8D5CF6EC'),
        characteristicId: Uuid.parse('00000001-5EC4-4083-81CD-A10B8D5CF6EC'));

    _notifySub = _ble.subscribeToCharacteristic(characteristic).listen((bytes) {
      setState(() {
        _value = const Utf8Decoder().convert(bytes);
      });
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        backgroundColor: Theme.of(context).colorScheme.inversePrimary,
        title: Text(widget.title),
      ),
      body: Center(
          child: _value.isEmpty
              ? const CircularProgressIndicator()
              : Text(_value, style: Theme.of(context).textTheme.titleLarge)),
    );
  }
}
```

iOS 앱을 실행하기 전에 프로비전 프로필을 설정해야 합니다.

![BLE 사용하여 Flutter Arduino 시작하기](/assets/img/2024-05-20-GetStartedwithBluetoothLowEnergyusingFlutterArduino_7.png)

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

또한 iOS 패키지는 프로젝트의 "ios" 하위 폴더로 이동하여 터미널에서 "pod install"을 실행하여 설치해야 합니다.

![image](/assets/img/2024-05-20-GetStartedwithBluetoothLowEnergyusingFlutterArduino_8.png)

Android 기기를 사용할 경우 개발자 모드와 USB 디버깅을 활성화하는 것을 잊지 마세요.

Android 스튜디오의 상단 표시줄에서 장치를 선택하고 실행 버튼을 누르세요. 앱은 몇 초 후에 실행될 것입니다.

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

iOS에서, 앱이 BLE(Bluetooth Low Energy)를 처음 사용할 때 사용자에게 허용 여부를 물어보는 대화 상자가 자동으로 나타납니다.

<img src="/assets/img/2024-05-20-GetStartedwithBluetoothLowEnergyusingFlutterArduino_9.png" />

"OK"로 답변해주세요.

Android에서는 약간 다릅니다. 사용자가 앱이 BLE를 사용하도록 명시적으로 허용하지 않았기 때문에 앱이 첫 번째로 시작할 때 권한 예외를 throw해야 합니다. 앱 내에서 사용자에게 물어보기 위한 프로그램을 작성하지 않았기 때문에 "Settings" → "Apps" → "ble_temp"으로 이동하여 "Location"과 "Nearby Devices" 권한을 허용해야 합니다.

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

![이미지](/assets/img/2024-05-20-GetStartedwithBluetoothLowEnergyusingFlutterArduino_10.png)

앱에서는 나노를 찾는 동안 원형 지시기를 표시해야 합니다. 잠시 후에 현재 온도(섭씨)가 표시됩니다.

![이미지](/assets/img/2024-05-20-GetStartedwithBluetoothLowEnergyusingFlutterArduino_11.png)

이제 코드를 자세히 살펴봅시다. 흥미로운 부분은 "\_MyHomePageState" 클래스 내부에서 시작됩니다. 첫 번째 및 유일한 페이지가 표시되면 디바이스 검색을 즉시 시작하고 업데이트를 수신 대기합니다:

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
@override
initState() {
  super.initState();
  _scanSub = _ble.scanForDevices(withServices: []).listen(_onScanUpdate);
}
```

우리가 이름이 "BLE-TEMP"인 페리페랄을 찾았을 때, "found" 플래그를 true로 설정하고 그 페리페랄에 연결합니다. 이 플래그는 앱이 이미 연결을 설정하는 동안 같은 페리페럴이나 이름이 같은 다른 페리페럴에 연결하지 못하도록 합니다. 우리는 특정 페리페랄에 대한 단일 연결만을 원합니다:

```js
void _onScanUpdate(DiscoveredDevice d) {
  if (d.name == 'BLE-TEMP' && !_found) {
    _found = true;
    _connectSub = _ble.connectToDevice(id: d.id).listen((update) {
      if (update.connectionState == DeviceConnectionState.connected) {
        _onConnected(d.id);
      }
    });
  }
}
```

발견된 장치에 연결하려면 해당 ID를 사용해야 합니다. Android에서는 MAC 주소이고, iOS에서는 UUID입니다. 나중에 사용하기 위해 영구적으로 저장하려고 할 때, iOS와 Android 모두 시간이 지남에 따라 이들이 바뀔 수 있으므로 주의해야 합니다.

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

커넥션 상태가 연결되었을 때 "onConnected" 메소드를 호출하여 구독할 특성을 인스턴스화합니다:

```js
void _onConnected(String deviceId) {
  final characteristic = QualifiedCharacteristic(
      deviceId: deviceId,
      serviceId: Uuid.parse('00000000-5EC4-4083-81CD-A10B8D5CF6EC'),
      characteristicId: Uuid.parse('00000001-5EC4-4083-81CD-A10B8D5CF6EC'));
  _notifySub = _ble.subscribeToCharacteristic(characteristic).listen((bytes) {
    setState(() {
      _value = const Utf8Decoder().convert(bytes);
    });
  });
}
```

우리의 값이 문자열로 인코딩되어 있기 때문에 받은 바이트를 변환하고 "setState"를 호출하여 UI를 업데이트해야 합니다. 구독된 커넥션 업데이트는 주변 장치가 전원을 잃거나 범위를 벗어나서 예기치 않게 연결이 끊겼을 때 알려줍니다.

앱이 종료될 때 페이지의 dispose 메소드가 호출됩니다. 여기서 온도 업데이트 구독을 해제하고 주변 디바이스와의 연결을 해제하고 각 구독에 대해 취소를 호출하여 스캔을 중지합니다.

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

```dart
@override
void dispose() {
  _notifySub?.cancel();
  _connectSub?.cancel();
  _scanSub?.cancel();
  super.dispose();
}
```

# 요약

글을 읽어주셔서 감사합니다. 개선 아이디어나 궁금한 점이 있으시면 댓글로 알려주세요. 전체 코드는 여기에서 확인할 수 있습니다.

몇 가지는 꽤 오랜 시간이 걸렸지만 그것들을 이해하는 데 도움이 되었으면 좋겠습니다. 여기서부터는 "ArduinoBLE" 라이브러리의 다른 예제들을 발견하고 글 전체에서 링크한 참고 자료들을 읽음으로써 BLE 지식을 더욱 향상시킬 수 있을 것입니다.
