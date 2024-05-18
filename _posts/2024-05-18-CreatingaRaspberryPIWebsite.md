---
title: "라즈베리 파이 웹사이트 만들기"
description: ""
coverImage: "/assets/img/2024-05-18-CreatingaRaspberryPIWebsite_0.png"
date: 2024-05-18 19:11
ogImage: 
  url: /assets/img/2024-05-18-CreatingaRaspberryPIWebsite_0.png
tag: Tech
originalTitle: "Creating a Raspberry PI Website"
link: "https://medium.com/@almmathis/creating-a-raspberry-pi-website-b338ba40168f"
---


아래는 메인 시리즈 "Hackable Lego Train"의 미니 파트입니다. 

다음은 메인 시리즈를 확인할 수 있는 링크입니다: 

Hackable Lego Train | Part 1 | Stux | 작성자: Stux | 2024년 5월 | Medium

<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-CreatingaRaspberryPIWebsite_1.png" />

프로젝트의 아이디어는 열차 해킹을 시뮬레이션하고 운영 기술 환경에서 취약한 애플리케이션과 관련된 전반적인 사이버 위험을 보여주는 것입니다.

그러니까 PI에 간단한 웹사이트를 만든 방법에 대해 설명해드릴게요.

전체적인 단계는 다음과 같습니다:

<div class="content-ad"></div>

- 새로운 OS(Kali)로 Pi 이미지 다시 만들기
- Pi에서 SSH 활성화
- 웹 사이트를 위한 폴더 디렉토리 생성
- 웹 사이트를 위한 파일 구성
- 웹 사이트에 로깅 추가

시작해 보세요!

주 컴퓨터에서 라즈베리 파이 이미지 소프트웨어를 다운로드하고 설치하세요.

라즈베리 파이 OS — 라즈베리 파이

<div class="content-ad"></div>

![Creating a Raspberry PI Website Image 2](/assets/img/2024-05-18-CreatingaRaspberryPIWebsite_2.png)

라즈베리 파이에 Kali를 설치하고 싶었어요.

- 저는 Kali를 잘 알고 있고 손에 익숙해요.
- 이 프로젝트 이외에도 네트워크 침투 분야에서 다른 계획이 있어요.
- 어차피 해킹 프로젝트니까 나에게 가장 쉽게 만들어야겠죠 :)

![Creating a Raspberry PI Website Image 3](/assets/img/2024-05-18-CreatingaRaspberryPIWebsite_3.png)

<div class="content-ad"></div>

호스트 컴퓨터에 PI SD 카드를 넣어주세요. 그리고 카드에 Kali를 WRITE해주세요.

작업이 끝나면 SD 카드를 PI에 다시 넣고 부팅해주세요.

원하는 방식으로 Kali를 설정하고 따라가보세요.

로그인하세요!

<div class="content-ad"></div>

여기서부터는 SSH를 사용해서 작업을 계속합니다. 그래서 셸 명령어에 익숙하신 것 같아요.

![CreatingaRaspberryPIWebsite_4](/assets/img/2024-05-18-CreatingaRaspberryPIWebsite_4.png)

새 시스템에 SSH로 접속한 후, 먼저 웹사이트 디렉토리 구조를 만들고 싶어요.

![CreatingaRaspberryPIWebsite_5](/assets/img/2024-05-18-CreatingaRaspberryPIWebsite_5.png)

<div class="content-ad"></div>

아래는 제 예시입니다:

```js
big_cabooses/
│
├── static/
│   └── css/
│       └── styles.css
│   └── images/
│       └── background.jpg
├── templates/
│   └── index.html
├── app.py
└── train_schedule.py
```

app.py 파일을 편집하여 다음 내용을 추가하십시오:

```js
from flask import Flask, render_template
from train_schedule import get_train_schedule

app = Flask(__name__)

@app.route('/')
def home():
    schedule = get_train_schedule()
    return render_template('index.html', schedule=schedule)

if __name__ == '__main__':
    app.run(debug=True)
```

<div class="content-ad"></div>

```python
def get_train_schedule():
    return [
        {"time": "07:00 AM", "train": "천년비행선", "destination": "타투인"},
        {"time": "09:00 AM", "train": "데스스타", "destination": "엔도어"},
        {"time": "11:00 AM", "train": "X-윙", "destination": "야빈 4"},
        {"time": "01:00 PM", "train": "TIE 전투기", "destination": "호스"},
        {"time": "03:00 PM", "train": "제국 셔틀", "destination": "코르서캔트"},
        {"time": "05:00 PM", "train": "슬레이브 1", "destination": "카미노"},
        {"time": "07:00 PM", "train": "나부 스타파이터", "destination": "나부"},
    ]
```

templates/index.html 수정:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Big Cabooses</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='css/styles.css') }}">
</head>
<body>
    <div class="background"></div>
    <div class="content">
        <h1>Big Cabooses 열차 일정</h1>
        <table>
            <thead>
                <tr>
                    <th>시간</th>
                    <th>열차</th>
                    <th>목적지</th>
                </tr>
            </thead>
            <tbody>
                {% for entry in schedule %}
                <tr>
                    <td>{{ entry.time }}</td>
                    <td>{{ entry.train }}</td>
                    <td>{{ entry.destination }}</td>
                </tr>
                {% endfor %}
            </tbody>
        </table>
    </div>
</body>
</html>
```

<div class="content-ad"></div>

static/css/styles.css 파일에 다음 내용을 입력해주세요:

```js
body, html {
    height: 100%;
    margin: 0;
    font-family: Arial, sans-serif;
}

.background {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-image: url("../images/background.jpg");
    background-size: cover;
    background-position: center;
    z-index: -1;
    opacity: 0.5;
}

.content {
    position: relative;
    z-index: 1;
    padding: 20px;
    background-color: rgba(255, 255, 255, 0.8);
    border-radius: 10px;
    max-width: 600px;
    margin: auto;
    top: 50%;
    transform: translateY(-50%);
    text-align: center;
}

table {
    width: 100%;
    border-collapse: collapse;
}

th, td {
    padding: 10px;
    border: 1px solid #ddd;
    text-align: left;
}

th {
    background-color: #f2f2f2;
}
```

static/background.jpg의 경로에 매력적인 배경 이미지를 업로드해주세요.

ChatGPT를 이용하여 스스로 이미지를 만들 수 있게 위해서 돈을 지불했어요. 여러분도 자금이 있으시다면 강력히 추천합니다.

<div class="content-ad"></div>

웹사이트를 시작하세요!

```js
sudo python3 app.py
```

브라우저를 열고 127.0.0.1:5000으로 이동하세요

BUTTT!!!!

<div class="content-ad"></div>

우리는 로깅을 추가하고 내부 네트워크의 다른 컴퓨터에서 액세스할 수 있는 기능을 추가하길 원해요.

이를 위해 하나 해야 할 일은 우리의 app.py 파일을 변경하는 것뿐입니다:

```js
if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=True)
```

반드시 "host="에 PI의 IP를 넣어 주세요!!!!

<div class="content-ad"></div>

이제 웹 사이트에 로그 파일을 기록하려고 합니다. app.py 파일에 이 모든 내용을 추가해 보겠습니다:

```python
from flask import Flask, render_template
from train_schedule import get_train_schedule
import logging

app = Flask(__name__)

# 로깅 설정
logging.basicConfig(filename='app.log', level=logging.DEBUG,
                    format='%(asctime)s %(levelname)s %(name)s %(threadName)s : %(message)s')

@app.route('/')
def home():
    app.logger.info("홈 페이지에 접근했습니다.")
    schedule = get_train_schedule()
    return render_template('index.html', schedule=schedule)

@app.route('/error')
def error():
    app.logger.error("이것은 오류 예제입니다.")
    return "오류 예제", 500

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=True)
```

이제 /big-cabooses/ 메인 디렉토리에 app.log라는 파일이 생기게 됩니다.

그런 다음, 간단한 접속 로그가 포함된 스타워즈 테마의 레고 기차 웹 사이트를 만들었습니다. 함께 빌드하는 과정을 즐겼습니다!

<div class="content-ad"></div>

어떤 질문이든 하시거나 도움이 필요하시면 언제든지 메시지를 보내주세요. 즐겁게 즐기세요! 이것을 하면서 몇 번 굳었지만, 마침내 성공적으로 해냈고 결과물을 좋아합니다. -Stux