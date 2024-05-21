---
title: "ESP32와 Firebase 시작하기"
description: ""
coverImage: "/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_0.png"
date: 2024-05-18 18:42
ogImage: 
  url: /assets/img/2024-05-18-GettingstartedwithESP32andFirebase_0.png
tag: Tech
originalTitle: "Getting started with ESP32 and Firebase"
link: "https://medium.com/firebase-developers/getting-started-with-esp32-and-firebase-1e7f19f63401"
---


## IOT

![Getting started with ESP32 and Firebase](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_0.png)

이 튜토리얼에서는 ESP32를 Google의 Firebase 백엔드에 가장 빠르게 연결하는 방법을 보여드리겠습니다. Firebase는 개발자들이 아이디어를 신속하게 프로토타이핑할 수 있는 매우 편리한 옵션으로 자리 잡았습니다. 본 튜토리얼에서는 Apple 워치를 위한 실시간 온도 모니터링 솔루션 개발을 위한 기초 작업을 다루며, 이 기사는 ESP32와 Firebase 간의 링크 설정에 중점을 두고 있습니다. 이후 기사에서는 watchOS 애플리케이션에 중점을 둘 것입니다.

# 하드웨어 요구사항

<div class="content-ad"></div>

이 튜토리얼을 완료하기 위해 다음 하드웨어가 필요합니다:

- ESP32 개발 보드 (어떤 ESP32 보드든 상관없습니다. 이 튜토리얼에서는 ESP32 DevKit v3를 사용할 것입니다).
- DHT11 온도 및 습도 센서.

# 백엔드 설정

백엔드로 Google의 Firebase를 사용할 것이며, 시작하려면 Firebase로 이동하여 Google 계정으로 로그인하십시오.

<div class="content-ad"></div>

새 프로젝트를 생성하려면 추가 프로젝트 옵션을 선택하세요. 프로젝트 이름을 입력하고 적절한 이름을 선택한 후 파란색 계속 버튼을 선택하세요. 내 프로젝트를 "스마트 홈이"라고 부르겠어요.

![이미지](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_1.png)

이 프로젝트에 대해 구글 애널리틱스를 사용 안 함으로 설정하세요. 이 프로젝트에는 필요하지 않습니다. 마지막으로, 프로젝트를 만들기 위해 "프로젝트 만들기"를 선택하세요.

![이미지](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_2.png)

<div class="content-ad"></div>

Firebase에서 프로젝트 설정을 시작합니다. 완료되면 계속 선택하고 Firebase 콘솔의 프로젝트 개요 페이지로 이동됩니다.

![프로젝트 개요 페이지](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_3.png)

첫 번째로 할 일은 프로젝트의 인증 옵션을 설정하는 것입니다. 왼쪽 상단의 인증 메뉴 옵션을 선택하면 인증 페이지로 이동합니다. '시작하기' 버튼을 선택하세요.

![인증 페이지](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_4.png)

<div class="content-ad"></div>


![ESP32 Firebase Tutorial Step 1](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_5.png)

첫 번째 단계로 ESP32 장치에 대해 익명 로그인을 사용할 것입니다. 이후 자습서에서 더 똑똑한 로그인 방법을 만들 것입니다. 아래와 같이 익명 로그인을 활성화하고 저장 옵션을 선택하세요.

![ESP32 Firebase Tutorial Step 2](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_6.png)

다음으로 모든 센서 데이터를 저장할 데이터베이스를 만들어야 합니다. 이를 위해 좌측 상단에 있는 Realtime Database 메뉴 옵션을 선택하고 Realtime Database 페이지로 이동합니다. 데이터베이스 생성 메뉴를 초기화하려면 데이터베이스 생성 버튼을 선택하세요.


<div class="content-ad"></div>


![image](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_7.png)

![image](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_8.png)

데이터베이스 생성 메뉴에서 가장 가까운 위치를 선택한 다음, "다음"을 선택하세요. 데이터베이스를 잠긴 모드 또는 테스트 모드로 초기화할 수 있는 옵션이 표시됩니다. 현재는 테스트 모드를 선택하세요. 주요 차이점은 테스트 모드에서는 30일 동안 데이터베이스에 무단 액세스를 허용하는 데이터베이스 액세스 규칙이 적용됩니다. 프로젝트를 제품으로 이동할 예정이라면 향후 이 기능을 비활성화해야 합니다. "활성화" 버튼을 선택하세요.

![image](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_9.png)


<div class="content-ad"></div>

마지막으로 새로운 빈 데이터베이스가 있는 새 페이지가 나타날 것입니다. 이제 모든 준비가 끝났어요!

![이미지](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_10.png)

계속 진행하기 전에 임베디드 애플리케이션에서 나중에 사용할 두 가지 항목을 복사하여 보관해야 합니다. 먼저 필요한 것은 실시간 데이터베이스 URL입니다. 실시간 데이터베이스 페이지의 URL을 복사하여 찾을 수 있으며, 이 경우에는 https://smart-hommie-default-rtdb.firebaseio.com 입니다.

![이미지](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_11.png)

<div class="content-ad"></div>

프로젝트를 API 키를 저장해야 할 두 번째 항목입니다. API 키를 얻으려면 페이지 상단 오른쪽에 있는 설정 아이콘을 선택한 다음 프로젝트 설정 메뉴 항목을 선택하여 프로젝트 설정 페이지로 이동하세요.

![프로젝트 설정 페이지](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_12.png)

프로젝트 설정 페이지에서 Web API 키가 표시됩니다. 이를 복사하여 저장하세요.

![Web API 키](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_13.png)

<div class="content-ad"></div>

저희들은 임베디드 애플리케이션을 시작할 준비가 되었습니다.

## 개발 환경 설정

가장 먼저 해야 할 일은 ESP32 개발 환경을 설정하는 것입니다. 이 튜토리얼에서는 Visual Studio Code 및 PlatformIO 플러그인을 사용할 것입니다.

만약 아직 VSCode를 설치하지 않았다면, 여기서 다운로드하고 설치해주세요.

<div class="content-ad"></div>

아래 지침에 따라 PlatformIO를 설치해야 합니다. 모든 것이 잘 진행되었다면 아래 첨부된 이미지처럼 오른쪽 메뉴에 PlatformIO가 나타날 것입니다.

![PlatformIO Menu](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_14.png)

먼저 우리가 해야 할 일은 PlatformIO용 ESP32 플랫폼을 설치하는 것입니다. PlatformIO 메뉴 아이콘을 선택하여 PlatformIO 홈페이지에 들어가십시오.

![PlatformIO Home Page](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_15.png)

<div class="content-ad"></div>

플랫폼 메뉴 옵션을 선택하고 임베디드 페이지를 선택합니다. "Esp"를 입력하여 Espressif 프레임워크를 찾은 다음 Espressif 32 옵션을 선택합니다. 설치 버튼을 선택하여 플랫폼을 설치합니다.

![이미지](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_16.png)

![이미지](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_17.png)

이제 임베디드 어플리케이션을 위한 새 프로젝트를 만들어 봅시다. 홈페이지로 돌아가서 "새 프로젝트 만들기" 메뉴를 선택합니다. 프로젝트 위저드가 표시되며, 프로젝트에 이름을 지정하고 EPS32 보드를 선택해야 합니다. 이 프로젝트에서는 Arduino 라이브러리를 사용할 예정이므로 기본 설정을 유지하고 '완료'를 선택하세요.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_18.png" />

마지막으로 프로젝트에서 사용할 mobizt의 라이브러리를 추가해야 합니다. 홈페이지에서 라이브러리 페이지로 이동하고 아래 그림과 같이 FirebaseESP32 라이브러리를 검색하십시오.

<img src="/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_19.png" />

마지막으로 아래 그림과 같이 라이브러리를 프로젝트에 추가하십시오.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_20.png" />

# 임베디드 어플리케이션

프로젝트 탐색기에서 main.cpp 파일을 엽니다.

<img src="/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_21.png" />

<div class="content-ad"></div>

우리 애플리케이션에서 필요한 모든 라이브러리를 불러와야 해요. Arduino 라이브러리는 기본적으로 이미 있어야 합니다:

```js
#include <Arduino.h>
#include <WiFi.h>
#include <FirebaseESP32.h>
```

다음으로 FirebaseESP32 라이브러리에서 사용되는 몇 가지 도우미 라이브러리를 추가해야 해요. TokenHelper 라이브러리는 토큰 생성 프로세스를 관리하는 데 사용되고, RTDBHelper 라이브러리는 Firebase Realtime Database에서 오는 데이터를 출력하기 위한 도우미 함수를 제공해요:

```js
//토큰 생성 프로세스 정보를 제공합니다.
#include "addons/TokenHelper.h"
//RTDB payload 출력 정보 및 다른 도우미 함수를 제공합니다.
#include "addons/RTDBHelper.h"
```

<div class="content-ad"></div>

여러 센서에서 오는 데이터를 구분하는 데 사용할 수 있는 고유한 장치 ID를 정의해야 합니다.

```js
// 장치 ID
#define DEVICE_UID "1X"
```

다음으로 WiFi 자격 증명을 입력해야 합니다. WIFI_AP를 WiFi 식별자로, WIFI_PASSWORD를 WiFi 비밀번호로 대체하세요.

참고: 임베디드 애플리케이션에 암호 정보를 하드코딩하는 것은 좋지 않은 생각입니다. 제품 케이스에서는 안전한 장치 등록 프로세스를 포함하는 장치 프로비저닝 전략을 적용해야 합니다.

<div class="content-ad"></div>


// Wi-Fi 인증 정보
#define WIFI_SSID "WIFI_AP"
#define WIFI_PASSWORD "WIFI_PASSWORD"


다음으로, Firebase 프로젝트 API 키를 저장하는 상수를 추가해야 합니다. 앞서 언급했듯이 Firebase 프로젝트 설정 페이지에서 API 키를 얻을 수 있습니다. API_KEY를 여러분의 API 키로 대체해주세요. 비슷하게 Firebase 실시간 데이터베이스 URL을 포함하여 본인의 URL로 대체해야 합니다.


// 여러분의 Firebase 프로젝트 웹 API 키
#define API_KEY "API_KEY"
// 여러분의 Firebase 실시간 데이터베이스 URL
#define DATABASE_URL "https://smart-hommie-default-rtdb.firebaseio.com"


그 다음으로, FirebaseESP32 라이브러리의 3개 객체를 초기화해야 합니다. 이 객체는 애플리케이션을 Firebase에 연결하는 데 중요합니다.


<div class="content-ad"></div>

먼저 유용한 몇 가지 전역 변수를 정의합니다.

```js
// 장치 위치 설정
String device_location = "거실"; 
// Firebase 실시간 데이터베이스 객체
FirebaseData fbdo; 
// Firebase 인증 객체
FirebaseAuth auth; 
// Firebase 구성 객체
FirebaseConfig config; 
// Firebase 데이터베이스 경로
String databasePath = ""; 
// Firebase 고유 식별자
String fuid = ""; 
// 장치 부팅 후 경과된 시간을 저장합니다.
unsigned long elapsedMillis = 0; 
// 센서 업데이트 주기를 10초로 설정합니다.
unsigned long update_interval = 10000; 
// 초기 Firebase 업데이트를 테스트하기 위한 더미 카운터
int count = 0; 
// 장치 인증 상태를 저장합니다.
bool isAuthenticated = false;
```

첫 번째 필요한 함수는 WiFi 설정입니다. WiFi_Init()이라는 함수를 정의합니다. Arduino 프레임워크에서 제공하는 내장 WiFi API를 사용합니다. WiFi.begin() 함수를 사용하여 자격 증명을 사용하여 WiFi 연결을 초기화한 후, WiFi.status() 함수를 사용하여 연결이 성공적인지 300ms마다 확인한 후, WiFi.localIP() 함수를 사용하여 로컬 IP 주소를 출력합니다. 사용하고 싶을 수 있는 다른 WiFi 관련 기능은 Arduino WiFi API 설명서를 확인해주세요.

<div class="content-ad"></div>

```js
void Wifi_Init() {
  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  Serial.print("Wi-Fi에 연결 중입니다");
  while (WiFi.status() != WL_CONNECTED){
    Serial.print(".");
    delay(300);
  }
  Serial.println();
  Serial.print("IP 주소로 연결됨: ");
  Serial.println(WiFi.localIP());
  Serial.println();
}
```

이제 Firebase 초기화 함수를 구현해야 합니다. 이 함수는 Firebase 백엔드에 연결하고 디바이스를 인증하며 Firebase 라이브러리를 초기화합니다. 먼저 API 키와 데이터베이스 URL을 구성 객체에 전달하고 설정 시 WiFi 재연결을 활성화합니다. 그런 다음 익명으로 디바이스를 가입하려고 시도합니다. 가입이 성공하면 모든 디바이스 데이터가 기록될 데이터베이스 경로(Temp_Sensor라고 합니다.)를 정의하고, 가입이 성공하면 auth 객체에서 얻은 Firebase 고유 식별자를 통해 Firebase 식별자를 추가합니다. Firebase 인증 기능을 더 자세히 알아보려면 여기에서 라이브러리 문서를 확인하세요. 가입을 완료한 후 Firebase.begin() 함수로 Firebase 라이브러리를 초기화합니다.

참고: Firebase.signUp()을 실행할 때마다 백엔드에 새로운 익명 사용자가 생성됩니다. 이는 디바이스를 켜고 끌 때마다 새로운 사용자가 생성된다는 것을 의미합니다.

```js
void firebase_init() {
  // firebase API Key 구성
  config.api_key = API_KEY;
  // firebase 실시간 데이터베이스 URL 구성
  config.database_url = DATABASE_URL;
  // WiFi 재연결 활성화 
  Firebase.reconnectWiFi(true);
  Serial.println("------------------------------------");
  Serial.println("새 사용자 가입 중...");
  // 익명으로 Firebase에 로그인
  if (Firebase.signUp(&config, &auth, "", ""))
  {
    Serial.println("성공");
    isAuthenticated = true;
    // 이 디바이스에 대해 업데이트가 로드될 데이터베이스 경로 설정
    databasePath = "/" + device_location;
    fuid = auth.token.uid.c_str();
  }
  else
  {
    Serial.printf("실패, %s\n", config.signer.signupError.message.c_str());
    isAuthenticated = false;
  }
  // 장기적인 토큰 생성 작업에 대한 콜백 함수 할당, addons/TokenHelper.h 참조
  config.token_status_callback = tokenStatusCallback;
  // Firebase 라이브러리 초기화
  Firebase.begin(&config, &auth);
}
```  

<div class="content-ad"></div>

위의 모든 것을 종합하면, 우리의 설정 함수는 다음과 같이 보일 것입니다:

```js
void setup() {
// 로컬 진단을 위한 시리얼 통신 초기화
Serial.begin(115200);
// 위치 WiFi와의 연결 초기화
Wifi_Init();
// Firebase 구성 및 익명으로 가입
firebase_init();
}
```

마지막으로, 모든 것이 올바르게 설정되었는지 테스트하는 샘플 데이터를 Firebase 데이터베이스에 업로드하는 테스트 함수를 구현해 봅시다. database_test()라는 새 함수를 만들겠습니다. 업데이트를 수행하기 전에 10초가 경과했는지 확인하고, 장치가 인증되었는지, 마지막으로 Firebase 라이브러리가 준비되었는지 확인합니다. 업데이트하기 전에 값을 설정하려는 실시간 데이터베이스의 노드 경로를 지정하고, 그 값으로 더미 카운터를 업데이트합니다. 10초마다 Firebase.set() 함수를 사용하여 이전 값들을 새로운 업데이트된 값으로 덮어씁니다. 기타 Firebase 함수는 여기에서 FirebaseESP32 API 문서를 참조하십시오.

```js
void database_test() {
// 10초가 경과하고, 장치가 인증되었으며 Firebase 서비스가 준비되었는지 확인
if (millis() - elapsedMillis > update_interval && isAuthenticated && Firebase.ready())
{
elapsedMillis = millis();
Serial.println("------------------------------------");
Serial.println("Set int test...");
// 데이터의 키 값을 지정하고 경로에 추가
String node = path + "/value";
// 카운트 값을 Firebase 실시간 데이터베이스에 보내기
if (Firebase.set(fbdo, node.c_str(), count++))
{
Serial.println("PASSED");
Serial.println("PATH: " + fbdo.dataPath());
Serial.println("TYPE: " + fbdo.dataType());
Serial.println("ETag: " + fbdo.ETag());
Serial.print("VALUE: ");
printResult(fbdo); //addons/RTDBHelper.h 참조
Serial.println("------------------------------------");
Serial.println();
}
else
{
Serial.println("FAILED");
Serial.println("REASON: " + fbdo.errorReason());
Serial.println("------------------------------------");
Serial.println();
}
}
}
```

<div class="content-ad"></div>

마침내 함수를 메인 루프에 다음과 같이 배치하십시오:

```js
void loop() {
    database_test();
}
```

우리의 코드는 더미 테스트를 수행할 준비가 완료되었습니다. ESP32 보드를 USB 포트에 연결하고 아래에 표시된 하단 패널의 컴파일 버튼을 눌러 코드를 컴파일하십시오:

![이미지](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_22.png)

<div class="content-ad"></div>

빌드가 성공하면, 컴파일 버튼 오른쪽에 있는 업로드 버튼을 사용하여 코드를 ESP32 장치에 업로드합니다.

![upload button](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_23.png)

모든 것이 잘 진행되었다면, Firebase 콘솔의 Realtime Database 브라우저로 돌아가서 아래 그림과 같이 10초마다 업데이트가 오는 것을 확인할 수 있습니다:

![database browser](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_24.png)

<div class="content-ad"></div>

시리얼 통신 인터페이스를 사용하여 10초마다 전송되는 다음 출력값을 확인할 수도 있습니다.

![Image](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_25.png)

# DHT11 온도 센서 통합

마지막으로 온도 센서를 통합해 봅시다.

<div class="content-ad"></div>

먼저, 하드웨어 연결이 올바른지 확인해야 합니다. DHT11 센서는 I2C 주변 장치를 사용하며 두 개의 핀이 필요합니다. DHT11 핀 할당을 검토해 봅시다:

![DHT11 Pin Allocation](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_26.png)

![DHT11 Pinout](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_27.png)

다음 표는 DHT22/DHT11 핀 배치를 보여줍니다. 센서가 당신을 향하고 있을 때, 핀 번호는 왼쪽부터 오른쪽까지 1부터 시작합니다. 이 예제에서는 GPIO4를 사용할 것이므로, 하드웨어 배선은 다음과 같이 될 것입니다:

<div class="content-ad"></div>


![image](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_28.png)

Now that we have our hardware configuration setup, let’s update our embedded application to utilise the DHT11 sensor. Let’s start by adding the DHT library from Adafruit to our project:

![image](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_29.png)

![image](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_30.png)


<div class="content-ad"></div>

DHT 라이브러리를 imports에 추가해 보겠습니다.

```js
#include <Arduino.h>
#include <WiFi.h>
#include <FirebaseESP32.h>
#include "DHT.h"
```

그러고 나서 DHT11 라이브러리를 설정해야 합니다. 이를 위해 센서 업데이트를 받을 GPIO 핀을 지정하고, DHT 센서 종류도 명시해야 합니다. 라이브러리는 DHT22 센서와도 호환되므로 사용 중인 DHT 센서를 지정해 주어야 합니다.

```js
// DHT 센서와 연결된 디지털 핀
#define DHTPIN 4
#define DHTTYPE DHT11
// DHT 센서 초기화
DHT dht(DHTPIN, DHTTYPE);
```

<div class="content-ad"></div>

그럼 온도를 저장할 전역 변수를 정의해야 합니다.

참고: DHT11은 습도를 추적하기도 하지만, 이 튜토리얼에서는 온도 측정만 다루겠습니다.

```js
// 센서 측정값을 저장할 변수
float temperature = 0;
float humidity = 0;
// 업데이트된 센서 값 저장을 위한 JSON 객체(Firebase로 전송할 값)
FirebaseJson temperature_json;
FirebaseJson humidity_json;
```

그리고 setup() 함수를 업데이트하여 DHT 라이브러리를 초기화해야 합니다. 이 라이브러리는 I2C 설정을 다룰 것입니다:

<div class="content-ad"></div>

```js
void setup() {
// 로컬 진단을 위한 시리얼 통신을 초기화합니다
Serial.begin(115200);
// 위치 WiFi 연결을 초기화합니다
Wifi_Init();
// Firebase 구성 및 익명으로 가입을 초기화합니다
firebase_init();
// DHT 라이브러리를 초기화합니다
dht.begin();
// 온도 및 습도 JSON 데이터를 초기화합니다
temperature_json.add("deviceuid", DEVICE_UID);
temperature_json.add("name", "DHT11-Temp");
temperature_json.add("type", "Temperature");
temperature_json.add("location", device_location);
temperature_json.add("value", temperature);
// 초기 온도 값을 출력합니다
String jsonStr;
temperature_json.toString(jsonStr, true);
Serial.println(jsonStr);
humidity_json.add("deviceuid", DEVICE_UID);
humidity_json.add("name", "DHT11-Hum");
humidity_json.add("type", "Humidity");
humidity_json.add("location", device_location);
humidity_json.add("value", humidity);
// 초기 습도 값을 출력합니다
String jsonStr2;
humidity_json.toString(jsonStr2, true);
Serial.println(jsonStr2);
}
```

그런 다음 센서 데이터를 업데이트하고 후에 Firebase로 보낼 글로벌 JSON 객체 변수에 저장할 updateSensorReadings() 함수를 도입합니다.

```js
void updateSensorReadings() {
Serial.println("------------------------------------");
Serial.println("센서 데이터를 읽는 중...");
temperature = dht.readTemperature();
humidity = dht.readHumidity();
// 읽기 실패 여부를 확인하고 조기에 종료합니다.
if (isnan(temperature) || isnan(humidity)) {
 Serial.println(F("DHT 센서에서 읽기 실패!"));
 return;
}
Serial.printf("온도 읽기: %.2f \n", temperature);
Serial.printf("습도 읽기: %.2f \n", humidity);
temperature_json.set("value", temperature);
humidity_json.set("value", humidity);
}
```

다음으로 예전의 database_test() 함수를 삭제하고 새로운 uploadSensorData() 함수로 교체해야 합니다. 노드 설명을 온도와 습도로 변경했습니다. 마지막으로 Firebase.set() 대신 Firebase.setJSON() 함수를 사용하여 온도와 습도 정보를 가진 두 개의 JSON 객체를 전달합니다. 마지막으로 main while 루프에서 database_test() 함수를 새로운 uploadSensorData() 함수로 교체합니다. 최종 코드 버전은 Github에서 확인할 수 있습니다.```

<div class="content-ad"></div>

```js
void uploadSensorData() {
if (millis() - elapsedMillis > update_interval && isAuthenticated && Firebase.ready())
{
 elapsedMillis = millis();
 updateSensorReadings();
 String temperature_node = databasePath + "/temperature"; 
 String humidity_node = databasePath + "/humidity";
 if (Firebase.setJSON(fbdo, temperature_node.c_str(), temperature_json))
 {
 Serial.println("PASSED"); 
 Serial.println("PATH: " + fbdo.dataPath());
 Serial.println("TYPE: " + fbdo.dataType());
 Serial.println("ETag: " + fbdo.ETag());
 Serial.print("VALUE: ");
 printResult(fbdo); //see addons/RTDBHelper.h
 Serial.println("------------------------------------");
 Serial.println();
 }
 else
 {
 Serial.println("FAILED");
 Serial.println("REASON: " + fbdo.errorReason());
 Serial.println("------------------------------------");
 Serial.println();
 }
if (Firebase.setJSON(fbdo, humidity_node.c_str(), humidity_json))
{
 Serial.println("PASSED");
 Serial.println("PATH: " + fbdo.dataPath());
 Serial.println("TYPE: " + fbdo.dataType());
 Serial.println("ETag: " + fbdo.ETag()); 
 Serial.print("VALUE: ");
 printResult(fbdo); //see addons/RTDBHelper.h
 Serial.println("------------------------------------");
 Serial.println();
 }
 else
 {
 Serial.println("FAILED");
 Serial.println("REASON: " + fbdo.errorReason());
 Serial.println("------------------------------------");
 Serial.println();
  }
 }
}
void loop() {
 uploadSensorData();
 }
```

이 새로운 버전을 컴파일하고 업로드하십시오. Firebase 데이터베이스를 확인하여 방 온도의 실시간 온도 값을 표시합니다.

<img src="/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_31.png" />

애플리케이션을 더 현실적으로 만들기 위해 데이터베이스에 흥미로운 센서 데이터를 채워 넣어야 합니다.```

<div class="content-ad"></div>

향후 튜토리얼을 따랐다면, 장치의 위치와 장치 UID를 변경하여 서로 다른 방에서 오는 다양한 센서 데이터를 시뮬레이션할 수 있습니다. 만약 4개의 ESP32와 4개의 DHT11 센서를 가지고 있다면, 각각을 별도의 방에 설치할 수도 있습니다. 이 튜토리얼에서는 다음과 같은 접근 방식을 취했습니다.

![image](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_32.png)

최종 결과는 이제 실시간 데이터베이스에 각 방에서 온 센서 데이터가 포함될 것입니다:

![image](/assets/img/2024-05-18-GettingstartedwithESP32andFirebase_33.png)

<div class="content-ad"></div>

대박이다! 당신은 ESP32 장치를 Firebase와 성공적으로 통합했어요.

# 결론

마무리하면, 우리는 어떠한 ESP32 장치도 익명으로 가입하고 10초마다 온도 측정을 Realtime Database로 전송할 수 있는 새로운 Firebase 프로젝트를 설정했어요. 다음 튜토리얼에서는 모든 방에서의 실시간 업데이트를 제공해주는 watchOS 애플리케이션을 만들 것이에요.

이 튜토리얼의 최종 코드는 여기의 Github에서 찾을 수 있어요.

<div class="content-ad"></div>

마지막으로, mobizt 팀원들에게 무한한 감사와 사랑을 전합니다. 이 멋진 FirebaseESP32 라이브러리를 우리에게 제공해줘서 정말 감사합니다. 그 안에는 Firebase Cloud Messages를 보낼 수 있는 능력을 비롯한 다양한 기능이 있습니다. 다음 IoT 프로젝트 중 하나에 매우 유용할 수 있을 거에요.

# 자료들