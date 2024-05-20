---
title: "OpenAI API를 통해 GPT-4o에 접속하기"
description: ""
coverImage: "/assets/img/2024-05-20-AccessingGPT-4oviaOpenAIAPI_0.png"
date: 2024-05-20 21:08
ogImage: 
  url: /assets/img/2024-05-20-AccessingGPT-4oviaOpenAIAPI_0.png
tag: Tech
originalTitle: "Accessing GPT-4o via OpenAI API"
link: "https://medium.com/@atulkumar_68871/accessing-gpt-4o-via-openai-api-edaa9a2adf11"
---


```markdown
![image](/assets/img/2024-05-20-AccessingGPT-4oviaOpenAIAPI_0.png)

# 소개

OpenAI가 최근에 발표한 GPT-4o는 텍스트, 이미지, 비디오 및 오디오 분석에 강력한 능력을 갖춘 첫 번째 멀티 모달 모델입니다. 이는 생성적 AI 모델의 응용 프로그램을 크게 확장시켰습니다. 이 블로그에서는 현재 텍스트 및 이미지 입력을 지원하는 API를 통해 이 모델을 사용하는 방법을 보여 드리려고 합니다. 모든 기능이 아직 제공되지 않았지만, OpenAI가 곧 출시할 예정입니다.

# 특징
```

<div class="content-ad"></div>

- 일반 텍스트 생성
- JSON 모드의 텍스트 생성
- 이미지 이해
- 함수 호출

초기 설정

OpenAI 시크릿 키로 라이브러리를 설치하고 가져온 후, 환경 변수를 설정해주세요.

```js
pip install --upgrade openai --quiet
```

<div class="content-ad"></div>

첫 번째 단계는 OpenAI 클라이언트를 설정하는 것입니다. 이를 위해 먼저 시크릿 키로 환경 변수를 만들어야 합니다. OPENAI_KEY=xyz와 같이 OpenAI 시크릿 키가 저장된 .env 파일을 만들어주세요.

작업이 완료되면 dotenv를 사용하여 키에 액세스할 수 있습니다.

```python
import os
from openai import OpenAI
from dotenv import load_dotenv

load_dotenv()
## API 키 및 모델 이름 설정
MODEL="gpt-4o"

api_key = os.getenv('OPENAI_KEY')
client = OpenAI(api_key=api_key)
```

일반 텍스트 생성

<div class="content-ad"></div>

```js
completion = client.chat.completions.create(
  model=MODEL,
  messages=[
    {"role": "system", "content": "당신은 도움이 되는 조수입니다. 수학 숙제를 돕습니다!"}, # <-- 모델에 맥락을 제공하는 시스템 메시지입니다
    {"role": "user", "content": "안녕하세요! 2+2를 해결할 수 있나요?"}  # <-- 모델이 응답을 생성할 사용자 메시지입니다
  ]
)

print("조수: " + completion.choices[0].message.content)
```

출력:

물론이죠! 2 + 2 = 4. 다른 도움이 필요하시면 언제든지 물어보세요!

Json 모드에서 텍스트 생성하기
```

<div class="content-ad"></div>

```markdown
completion = client.chat.completions.create(
  model=MODEL,
  response_format={"type": "json_object"},
  messages=[
    {"role": "system", "content": "You are a trainer who always responds in JSON"},
    {"role": "user", "content": "Create a weekly workout routine for me"}
  ]
)

json.loads(completion.choices[0].message.content)
```

출력:

'‘workoutRoutine’: '‘week’: 1, ‘days’: '‘Monday’: '‘muscleGroup’: ‘Chest and Triceps’, ‘exercises’: ['‘name’: ‘Bench Press’, ‘sets’: 4, ‘reps’: 12', '‘name’: ‘Incline Dumbbell Press’, ‘sets’: 4, ‘reps’: 12', '‘name’: ‘Tricep Dips’, ‘sets’: 3, ‘reps’: 15', '‘name’: ‘Tricep Pushdown’, ‘sets’: 3, ‘reps’: 15']', ‘Tuesday’: '‘muscleGroup’: ‘Back and Biceps’, ‘exercises’: ['‘name’: ‘Pull-Ups’, ‘sets’: 4, ‘reps’: 10', '‘name’: ‘Deadlifts’, ‘sets’: 4, ‘reps’: 12', '‘name’: ‘Barbell Rows’, ‘sets’: 4, ‘reps’: 12', '‘name’: ‘Bicep Curls’, ‘sets’: 3, ‘reps’: 15']',...
```
- 이미지 이해

<div class="content-ad"></div>

로컬 이미지 사용하기

```js
from IPython.display import Image, display, Audio, Markdown
import base64

IMAGE_PATH = "triangle.png"

# 컨텍스트를 위한 이미지 미리보기
display(Image(IMAGE_PATH))
```

<img src="/assets/img/2024-05-20-AccessingGPT-4oviaOpenAIAPI_1.png" />

```js
# 이미지 파일 열고 base64 문자열로 인코딩하기
def encode_image(image_path):
    with open(image_path, "rb") as image_file:
        return base64.b64encode(image_file.read()).decode("utf-8")

base64_image = encode_image(IMAGE_PATH)

response = client.chat.completions.create(
    model=MODEL,
    messages=[
        {"role": "system", "content": "당신은 Markdown으로 응답하는 유용한 도우미입니다. 내 수학 숙제를 도와주세요!"},
        {"role": "user", "content": [
            {"type": "text", "text": "삼각형의 면적은 얼마인가요?"},
            {"type": "image_url", "image_url": {
                "url": f"data:image/png;base64,{base64_image}"}
            }
        ]}
    ],
    temperature=0.0,
)

print(response.choices[0].message.content)
```

<div class="content-ad"></div>

삼각형의 면적을 찾기 위해 직갛각 삼각형의 면적을 구하는 공식을 사용할 수 있습니다: \[ \text{면적} = \frac{1}{2} \times \text{밑변} \times \text{높이} \] 이 삼각형에서 밑변은 20 cm이고 높이는 15 cm입니다. \[ \text{면적} = \frac{1}{2} \times 20 \, \text{cm} \times 15 \, \text{cm} \] \[ \text{면적} = \frac{1}{2} \times 300 \, \text{cm}² \] \[ \text{면적} = 150 \, \text{cm}² \] 따라서, 삼각형의 면적은 \( 150 \, \text{cm}² \)입니다.

URL을 사용하는 예시

```js
response = client.chat.completions.create(
    model=MODEL,
    messages=[
        {"role": "system", "content": "마크다운으로 응답하는 유용한 도우미입니다."},
        {"role": "user", "content": [
            {"type": "text", "text": "이 이미지에서 무엇을 보고 무슨 감정이 표현되었는지 설명해주세요."},
            {"type": "image_url", "image_url": {
                "url": "https://pbs.twimg.com/media/GNeb4-Ua8AAuaKp?format=png&name=small"}
            }
        ]}
    ],
    temperature=0.0,
)

print(response.choices[0].message.content)
```

<div class="content-ad"></div>

이미지:

![이미지](/assets/img/2024-05-20-AccessingGPT-4oviaOpenAIAPI_2.png)

결과:

이 이미지는 웃는 사람을 보여줍니다. 전달되는 감정은 행복이나 만족으로 보입니다. 웃음은 긍정적이고 즐거운 기분을 시사합니다.

<div class="content-ad"></div>

기능 호출

```js
# NBA 게임 점수를 가져 오기 위한 Mock 함수
def get_nba_game_score(team):
    print('get_nba_game_score가 호출되었습니다.')
    """주어진 팀에 대한 NBA 게임의 현재 점수를 가져옵니다."""
    if "lakers" in team.lower():
        return json.dumps({"team": "Lakers", "score": "102", "opponent": "Warriors", "opponent_score": "98"})
    elif "bulls" in team.lower():
        return json.dumps({"team": "Bulls", "score": "89", "opponent": "Celtics", "opponent_score": "95"})
    else:
        return json.dumps({"team": team, "score": "N/A", "opponent": "N/A", "opponent_score": "N/A"})
```

필요한 경우 도구를 통해 함수 호출:

```js
def function_calling():
    # 단계 1: 사용자 메시지로 대화를 초기화합니다
    messages = [{"role": "user", "content": "레이커스 게임 점수가 어떻게 되나요?"}]

    # 모델이 사용할 수있는 도구(함수) 정의
    tools = [
        {
            "type": "function",
            "function": {
                "name": "get_nba_game_score",
                "description": "주어진 팀의 NBA 게임의 현재 점수를 가져옵니다.",
                "parameters": {
                    "type": "object",
                    "properties": {
                        "team": {
                            "type": "string",
                            "description": "NBA 팀의 이름, 예: 레이커스, 불스",
                        },
                    },
                    "required": ["team"],
                },
            },
        }
    ]

    # 단계 2: 대화 컨텍스트와 사용 가능한 도구를 모델에게 전송합니다
    response = client.chat.completions.create(
        model=MODEL,
        messages=messages,
        tools=tools,
        tool_choice="auto",  # auto가 기본값입니다. 명시적으로 지정해줍니다.
    )

    # 모델의 응답을 추출합니다.
    response_message = response.choices[0].message
    tool_calls = response_message.tool_calls  # 모델이 도구를 호출하도록 요청하는지 확인합니다

    # 단계 3: 모델이 요청한 도구 호출이 있는지 확인합니다
    if tool_calls:
        # 사용 가능한 함수 정의
        available_functions = {
            "get_nba_game_score": get_nba_game_score,
        }  # 이 예제에서는 함수가 한 개뿐이지만 확장할 수 있습니다

        # 모델의 응답을 대화 기록에 추가합니다
        messages.append(response_message)

        # 단계 4: 모델에서 요청된 함수를 호출합니다
        for tool_call in tool_calls:
            function_name = tool_call.function.name
            function_to_call = available_functions[function_name]
            function_args = json.loads(tool_call.function.arguments)

            print(f"도구 호출: {tool_call}")

            # 추출 된 인수로 함수를 호출합니다
            function_response = function_to_call(
                team=function_args.get("team"),
            )

            # 함수 응답을 대화 기록에 추가합니다
            messages.append(
                {
                    "tool_call_id": tool_call.id,
                    "role": "tool",
                    "name": function_name,
                    "content": function_response,
                }
            )

        # 단계 5: 업데이트된 기록을 사용하여 대화를 계속합니다
        second_response = client.chat.completions.create(
            model=MODEL,
            messages=messages,
        )  # 함수 응답을 확인할 수 있는 모델의 새로운 응답을 받습니다

        return second_response

# 대화를 실행하고 결과를 인쇄합니다
response = function_calling()
print(response.choices[0].message.content)
```

<div class="content-ad"></div>

출력:

도구 호출: ChatCompletionMessageToolCall(id='call_k2lcfdlVAcQ8PTUL1uwu3fYz', function=Function(arguments=' "team": "Lakers" ', name='get_nba_game_score'), type='function') get_nba_game_score 호출됨

현재 레이커스 경기 점수는 레이커스 102, 워리어스 98 입니다.

마무리

<div class="content-ad"></div>

이제 전체 기사를 읽으셔서 GPT-4o 모델을 활용해 텍스트 생성, JSON 모드, 이미지 이해, 그리고 함수 호출을 OpenAI API를 통해 사용할 준비가 되셨습니다. API에 오디오 및 비디오 지원이 추가되면 다시 블로그를 쓸 계획입니다. 그 때까지 계속 탐험하고 배우세요!