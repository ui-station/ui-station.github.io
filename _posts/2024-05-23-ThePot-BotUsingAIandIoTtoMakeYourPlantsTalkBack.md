---
title: "더 나은 식물 생장을 위해 인공지능과 사물인터넷을 활용해 보세요"
description: ""
coverImage: "/assets/img/2024-05-23-ThePot-BotUsingAIandIoTtoMakeYourPlantsTalkBack_0.png"
date: 2024-05-23 16:17
ogImage:
  url: /assets/img/2024-05-23-ThePot-BotUsingAIandIoTtoMakeYourPlantsTalkBack_0.png
tag: Tech
originalTitle: "The Pot-Bot: Using AI and IoT to Make Your Plants Talk Back!"
link: "https://medium.com/@barzik/the-pot-bot-using-ai-and-iot-to-make-your-plants-talk-back-a49c271df40d"
---

만약 꽃병이 말할 수 있다면 어떨까요? 인공 지능과 Raspberry Pi의 마법을 활용하면 이제 가능합니다! 식물 화분에게 말을 건네는 방법을 알려드릴게요.

![화분이 말하는 Pot-Bot](/assets/img/2024-05-23-ThePot-BotUsingAIandIoTtoMakeYourPlantsTalkBack_0.png)

# 계획

프로젝트의 핵심은 Raspberry Pi로 이루어진 것입니다. 화분 내 센서에서 정보를 수집하고, 이 데이터는 Python 코드를 사용하여 처리됩니다. 센서의 정보를 가볍고 Tiny Dolphin이라는 모델로 변환한 후 이를 장치 내에서 실행되는 espeak로 전송합니다. 그리고 Tiny Dolphin에서 나온 출력은 화분 옆의 Bluetooth 연결 스피커로 전송됩니다.

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

![image](/assets/img/2024-05-23-ThePot-BotUsingAIandIoTtoMakeYourPlantsTalkBack_1.png)

What you will need:

- Raspberry Pi, and the knowledge to SSH into it.
- Moisture sensor.
- Jumper wires.
- Bluetooth speaker or 3.5mm jack speaker.
- A pot.

# Preparing the Environment

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

첫째로, Raspberry Pi를 업그레이드하세요:

```js
sudo apt-get update
sudo apt-get upgrade
```

다음으로, LLM 모델을 관리하는 Ollama를 설치해보겠습니다. Ollama는 로컬 모델을 손쉽게 설치할 수 있습니다:

```js
curl -fsSL https://ollama.com/install.sh | sh
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

<img src="/assets/img/2024-05-23-ThePot-BotUsingAIandIoTtoMakeYourPlantsTalkBack_2.png" />

설치가 완료된 후, Ollama 웹사이트에서 Tiny Dolphin 모델을 선택하세요. 이 모델은 가벼우면서 우리의 요구에 적합합니다:

```js
ollama run tinydolphin
```

"Send a message (/? for help)"가 나타나면 모델이 준비된 것입니다.

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

![Image 1](/assets/img/2024-05-23-ThePot-BotUsingAIandIoTtoMakeYourPlantsTalkBack_3.png)

# Raspberry Pi 스피커 연결

3.5mm 케이블이나 블루투스를 사용하여 스피커를 연결할 수 있습니다.

![Image 2](/assets/img/2024-05-23-ThePot-BotUsingAIandIoTtoMakeYourPlantsTalkBack_4.png)

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

## 블루투스 연결 설정

Raspberry Pi 3과 4에는 내장 블루투스가 있습니다. 연결 관리를 위해 `bluetoothctl`을 사용하실 수 있어요:

1. 블루투스 콘솔 열기:

```js
bluetoothctl;
```

2. 장치 검색:

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
scan on
```

<img src="/assets/img/2024-05-23-ThePot-BotUsingAIandIoTtoMakeYourPlantsTalkBack_5.png" />

3. 스피커를 찾아 페어링하세요 (예: Mi Portable Speaker):

```js
pair 4C:65:A8:5E:CE:95
connect 4C:65:A8:5E:CE:95
trust 4C:65:A8:5E:CE:95
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

<img src="/assets/img/2024-05-23-ThePot-BotUsingAIandIoTtoMakeYourPlantsTalkBack_6.png" />

## 오디오 스트리밍 설정

PulseAudio와 pacmd가 설치되어 있는지 확인하세요:

```js
pulseaudio - version;
pacmd - version;
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

블루투스 스피커를 기본 오디오 출력 장치로 설정해보세요:

```js
pacmd list-sinks | grep -E 'name:|index'
pacmd set-default-sink bluez_sink.4C_65_A8_5E_CE_95.a2dp_sink
```

.wav 파일로 테스트해보세요:

```js
wget https://freewavesamples.com/files/Police-Siren.wav
aplay Police-Siren.wav
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

# 텍스트 음성 변환기 설치하기

espeak 모듈을 설치하고 테스트해보세요:

```js
sudo apt-get install espeak
espeak "hello"
```

# 모든 것을 함께 적용하기

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

프로젝트 폴더와 `index.py` 파일을 만들어주세요. 이 스크립트는 CPU 온도를 읽고 Tiny Dolphin에 프롬프트를 보내며 응답을 음성으로 변환합니다.

```python
import os
import time
import subprocess
import json

def get_cpu_temperature():
    """시스템 파일에서 CPU 온도를 읽어옵니다."""
    with open("/sys/class/thermal/thermal_zone0/temp", "r") as file:
        temp = float(file.read()) / 1000
    return temp

def run_command(command):
    """시스템 명령을 실행하고 결과를 반환합니다."""
    result = subprocess.run(command, shell=True, capture_output=True, text=True)
    return result.stdout.strip()

def main():
    while True:
        temp = get_cpu_temperature()
        message = f"CPU 온도에 관한 10단어 메시지를 작성하세요: {temp} 섭씨"
        print("메시지를 API로 전송합니다")
        print(message)
        # POST 요청을 보내기 위한 curl 명령어 작성
        curl_command = f"curl -s http://localhost:11434/api/generate -H 'Content-Type: application/json' -d '{{\"model\": \"tinydolphin\", \"prompt\": \"{message}\", \"stream\": false}}'"
        print("curl 명령어 실행 중...")
        print(curl_command)
        curl_output = run_command(curl_command)
        print(f"Curl 출력: {curl_output}")
        try:
            # JSON 응답을 파싱하여 응답 속성을 가져옴
            json_response = json.loads(curl_output)
            ollama_output = json_response['response']
            print(f"API 응답: {ollama_output}")
        except json.JSONDecodeError:
            ollama_output = "JSON 응답을 해독하는데 실패했습니다."
            print(ollama_output)
        except KeyError:
            ollama_output = "JSON 응답에서 'response' 키가 누락되었습니다."
            print(ollama_output)

        # ollama 실행 결과를 음성으로 출력
        os.system(f"espeak '{ollama_output}'")
        time.sleep(120)  # 120초 동안 대기

if __name__ == "__main__":
    main()
```

이 코드를 실행하면 모든 것이 연결되어 작동하는지 확인할 수 있습니다. 현재 LLM이 CPU 온도에 대해 이야기하고 있습니다. 더 흥미로운 내용일 수 있겠지만, 이겁니다만 말하는 식물이 아니라 중요한 이정표입니다. 모든 것이 작동하나요? 멋져요!

# 센서 연결하기

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

라즈베리 파이의 GPIO에 직접 센서를 연결하여 냄비가 CPU 온도가 아닌 자신에 대해 이야기하도록 설정해보세요.

GPIO를 사용하면 다양한 센서로부터 입력을 받아 라즈베리 파이를 실제 IoT 장치로 만들 수 있습니다. 우리는 라즈베리 파이 핀배치도를 사용하여 다양한 종류의 GPIO를 확인합니다. 예를 들어, 이 핀배치도에서 5V 전원 핀과 GND 핀을 표시했습니다.

[이미지: "2024-05-23-ThePot-BotUsingAIandIoTtoMakeYourPlantsTalkBack_7.png"]

이제 습도 센서를 연결해보겠습니다. 많은 온라인 상점에서 구할 수 있습니다. 디지털 센서가 필요하며, 더 쉽고 테스트하기 편한 워터 센서를 사용할 수도 있습니다.

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

![image](/assets/img/2024-05-23-ThePot-BotUsingAIandIoTtoMakeYourPlantsTalkBack_8.png)

There should be three pins with VCC, GND, and DO—if there are four pins (with AO), it`s OK, but disregard the DO.

![image](/assets/img/2024-05-23-ThePot-BotUsingAIandIoTtoMakeYourPlantsTalkBack_9.png)

Let`s connect the jumpers. You should connect the GND to the ground pin, the VCC to the 3V outlet, and the DO to GPIO17.

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

지금 테스트해보세요: 이 Python 코드를 temp.py 파일로 작성하고 "python3 temp.py"로 실행하세요.

```python
import RPi.GPIO as GPIO
import time

# 설정
sensor_pin = 17
GPIO.setmode(GPIO.BCM)
GPIO.setup(sensor_pin, GPIO.IN)

try:
    while True:
        if GPIO.input(sensor_pin):
            print("물 감지됨!")
        else:
            print("물이 감지되지 않았습니다.")
        time.sleep(1)  # 1초마다 읽기

finally:
    GPIO.cleanup()  # GPIO 정리하여 모드 재설정
```

이제 센서를 물잔에 넣어봐서 콘솔에 "물 감지됨!"이 출력되는지 확인해보세요.

![이미지](/assets/img/2024-05-23-ThePot-BotUsingAIandIoTtoMakeYourPlantsTalkBack_10.png)

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

더 많은 센서를 연결할 수 있어요. 연결할수록 식물이 더 많은 데이터를 가지고, 환경에 대해 더 나은 대응을 할 거예요. 하지만 지금은 물 센서로만 진행하죠.

식물을 만들어 봐요!

이제 LLM, 텍스트 음성 변환, 그리고 센서가 모두 준비되었으니, 모두 연결해보는 시간이에요! 센서 데이터를 받아와 LLM으로 전송하고, 식물처럼 동작하도록 만들고, 그 상태를 토론해봅시다.

```python
import os
import time
import subprocess
import RPi.GPIO as GPIO
import json

# GPIO 설정
sensor_pin = 17  # 연결에 따라 변경 가능
GPIO.setmode(GPIO.BCM)
GPIO.setup(sensor_pin, GPIO.IN)

def run_command(command):
    """시스템 명령을 실행하고 결과를 반환합니다."""
    result = subprocess.run(command, shell=True, capture_output=True, text=True)
    return result.stdout.strip()

def main():
    try:
        while True:
            if GPIO.input(sensor_pin):
                message = "당신은 모세라고 불리우는 행복한 식물이에요. 10개의 단어로 설명해 주세요."
            else:
                message = "당신은 모세라고 불리우는 목말라 하는 식물이에요. 10개의 단어로 불평해 주세요."
            print("메시지를 API를 통해 실행 중")
            print(message)
            # POST 요청을 보내기 위한 curl 명령 구성
            curl_command = f"curl -s http://localhost:11434/api/generate -H 'Content-Type: application/json' -d '{{\"model\": \"tinydolphin\", \"prompt\": \"{message}\", \"stream\": false}}'"
            print("curl 명령 실행 중...")
            print(curl_command)
            curl_output = run_command(curl_command)
            print(f"Curl 결과: {curl_output}")
            try:
                # JSON 결과를 파싱하여 응답 속성을 가져옴
                json_response = json.loads(curl_output)
                api_output = json_response['response']
                print(f"API 응답: {api_output}")
            except json.JSONDecodeError:
                api_output = "JSON 응답을 디코딩하는 데 실패했어요."
                print(api_output)
            except KeyError:
                api_output = "JSON 응답에서 'response' 키가 누락되었어요."
                print(api_output)

            # API 출력을 음성으로 출력
            os.system(f"espeak \"{api_output}\"")
            time.sleep(120)  # 매 2분마다 확인
    finally:
        GPIO.cleanup()  # GPIO 정리하여 모드 재설정

if __name__ == "__main__":
    main()
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

그리고... 말합니다!

# 결론

이 설정으로 여러분의 식물 화분이 AI와 IoT를 이용하여 의사소통할 수 있습니다. 프롬프트를 개인 맞춤화하거나 더 많은 센서로 상호작용을 향상시키기 위해 스크립트를 수정해보세요. 가능성은 무한합니다. 옆에 누군가 있을 때만 말을 할 수 있도록 근접 센서를 추가할 수도 있습니다.
