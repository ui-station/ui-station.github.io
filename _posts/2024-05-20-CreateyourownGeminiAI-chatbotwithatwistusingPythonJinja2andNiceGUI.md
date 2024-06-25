---
title: "제목 Python, Jinja2 및 NiceGUI를 사용하여 독특한 Gemini AI-챗봇 만들기"
description: ""
coverImage: "/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_0.png"
date: 2024-05-20 18:41
ogImage:
  url: /assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_0.png
tag: Tech
originalTitle: "Create your own Gemini AI-chatbot with a twist using Python, Jinja2 and NiceGUI"
link: "https://medium.com/data-engineer-things/create-your-own-gemini-ai-chatbot-with-a-twist-using-python-jinja2-and-nicegui-7d35ac981a63"
---

## 파이썬을 사용하여 베르텍스AI를 통해 Gemini의 기본 사항을 알아보고 NiceGUI를 사용하여 웹 UI를 만들며 모듈식 프롬프트를 구성하는 Jinja2를 사용하는 방법

![이미지](/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_0.png)

본문에서는 Python, NiceGUI, Jinja2 및 VertexAI를 사용하여 LLM 기반 웹 애플리케이션을 만들어보겠습니다. 이 프로젝트를 처음부터 만드는 방법과 기본 개념의 개요를 얻을 수 있습니다.

먼저 🚀 기술 스택에 대한 간단한 개요로 시작해 보겠습니다:

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

- Python 3.12
- NiceGUI를 사용하여 파이썬으로 프론트엔드를 코딩합니다.
- 의존성 관리를 위해 Poetry 사용
- 모듈식 프롬프트 생성을 위해 Jinja2 템플릿 사용

# Poetry를 사용한 프로젝트 설정

우리는 Python에서 의존성 관리와 패키징을 위한 도구인 Poetry를 사용하여 프로젝트를 생성하고 의존성을 어떻게 관리하는지 살펴볼 것입니다.

Poetry가 도와줄 수 있는 세 가지 주요 작업은 빌드, 게시 및 추적입니다. 의존성을 관리하고 프로젝트를 공유하며 의존성 상태를 추적할 수 있는 결정론적인 방법이 되는 것이 아이디어입니다.

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

시, Poetry는 가상 환경을 생성하는 작업도 담당합니다. 기본적으로 시스템 내의 중앙 폴더에 위치하는데요. 하지만 저와 같이 프로젝트 관련 가상 환경을 프로젝트 폴더 내에 가지고 싶다면, 간단한 설정 변경이 필요합니다:

```js
poetry config virtualenvs.in-project true
```

Poetry new를 사용하여 새로운 Python 프로젝트를 만들 수 있습니다. 이 작업은 시스템의 기본 Python과 링크되는 가상 환경이 생성됩니다. 이것을 pyenv와 결합하면 특정 버전을 사용하는 프로젝트를 유연하게 만들 수 있습니다. 또한, Poetry에 직접 사용할 Python 버전을 알려줄 수도 있습니다: poetry env use /full/path/to/python.

새 프로젝트를 생성하면, poetry add로 종속성을 추가할 수 있습니다.

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

새 프로젝트를 만들어 시작해봐요:

```js
poetry config virtualenvs.in-project true
poetry new my-gemini-chatbot
cd my-gemini-chatbot
```

프로젝트에 대한 메타데이터 및 해당 버전과 종속성은 .toml 및 .lock 파일에 저장됩니다.

<img src="/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_1.png" />

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

이제 시작하기 위해 필요한 종속성을 추가해 봅시다:

```js
poetry add 'google-cloud-aiplatform>=1.38'
poetry add 'nicegui'
```

# NiceGUI를 사용한 기본 웹 UI

NiceGUI는 웹 브라우저용 그래픽 사용자 인터페이스(GUI)를 만들 수 있게 해주는 Python 라이브러리입니다. 초보자들도 빠르게 시작할 수 있지만, 고급 사용자들을 위한 맞춤 설정 옵션도 풍부하게 제공합니다. 웹 뷰는 Quasar Framework를 기반으로 하며, 다양한 구성 요소를 제공합니다. 그리고 이는 TailwindCSS를 사용하기 때문에 NiceGUI 페이지에서도 직접 TailwindCSS 클래스를 사용할 수 있습니다.

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

특히 나에게는 백엔드 소프트웨어 개발에서 온 데이터 엔지니어로써, Python을 사용하여 작은 웹 UI를 만드는 것은 좋은 방법입니다. 물론 더 복잡한 프론트엔드에 대해서는 충분한 해결책이 되지 않을 수도 있지만, 범위가 상대적으로 작다면 빠르게 결과를 확인할 수 있습니다. NiceGUI를 사용하면 Python 코드에 집중할 수 있습니다. 왜냐하면 NiceGUI가 모든 웹 개발 작업을 처리하기 때문입니다.

NiceGUI는 버튼, 슬라이더, 텍스트 상자와 같은 일반적인 UI 구성 요소를 사용하고 유연한 레이아웃을 사용하여 페이지에 배열합니다. 이러한 구성 요소는 Python 코드의 데이터와 연결될 수 있어 데이터가 변경될 때 인터페이스가 자동으로 업데이트됩니다. 또한 응용 프로그램의 외관을 필요에 맞게 스타일링할 수도 있습니다.

이 작업 방식을 설명하는 가장 쉬운 방법은 직접 보여주는 것입니다. 그래서 최소 예제를 만들기 시작해 봅시다.

내 경우에는 내 모듈의 main.py (내 경우 my_gemini_chatbot)를 생성합니다. 이 파일은 모든 어플리케이션과 프론트엔드 로직에 사용될 것입니다.

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

다음 코드로 간단한 레이블이 포함된 페이지를 얻을 수 있습니다:

```js
from nicegui import ui

ui.label('Hello NiceGUI!')
ui.run()
```

<img src="/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_2.png" />

애플리케이션을 실행하면 포트 8080에서 사용할 수 있습니다. 또한 스크립트를 실행할 때 페이지가 자동으로 열립니다. 이것이 페이지가 보이는 방법입니다:

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

<img src="/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_3.png" />

축하합니다: 순수 Python으로 만든 첫 프론트엔드 😉.

# 챗봇 웹 UI 준비

다음 단계는 우리 챗봇을 위한 웹 UI를 준비하는 것입니다. 물론, 위의 예제보다는 조금 더 복잡할 것이지만 NiceGUI를 사용하여 컴포넌트를 배치하는 기본 아이디어를 알면 더 쉬워질 것입니다.

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

먼저 몇 가지 레이아웃 기본 사항을 이해해야 합니다. 컴포넌트가 페이지 상에 배치되는 방식을 제어하는 여러 가지 방법이 있습니다. 일반적인 방법 중 하나는 그리드 레이아웃인데, 우리는 이를 사용할 것입니다.

NiceGUI에서 우리는 다음과 같이 그리드를 생성할 수 있습니다:

```js
from nicegui import ui

with ui.grid(columns=16).classes("w-3/4 place-self-center gap-4"):
    ui.markdown("# 🚀 My Gemini Chatbot").classes("col-span-full")

ui.run()
```

이를 하나씩 분해하여 더 잘 이해해 봅시다. ui.grid(columns=16)은 16개의 열로 분할된 그리드 레이아웃을 초기화합니다. 이들은 모두 동일한 너비를 갖습니다. 이것은 그리드의 실제 너비에 대해 아무 말도 하지 않으며 그리드가 몇 개의 열로 나눠질지만을 지정합니다. 16개의 열로, 우리는 충분한 유연성을 얻을 수 있습니다.

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

우리는 .classes를 통해 사용자 정의 TailwindCSS 클래스를 추가할 수 있습니다. 여기서 우리는 그리드에 3가지 클래스를 추가했습니다:

- w-3/4: 그리드는 항상 브라우저 전체 너비의 3/4을 차지해야 합니다.
- place-self-center: 그리드 자체가 브라우저 창 가운데에 위치해야 합니다.
- gap-4: 그리드 내 요소 사이에는 4개의 픽셀 간격이 있어야 합니다.

위 예시에서는 그리드 안에 요소 하나를 배치했습니다:

```js
with ui.grid(columns=16).classes("w-3/4 place-self-center gap-4"):
    ui.markdown("# 🚀 My Gemini Chatbot").classes("col-span-full")
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

위에서 확인할 수 있듯이, 우리는 다시 col-span-full이라는 사용자 정의 클래스를 할당했습니다. 이는 NiceGUI에게 이 요소가 첫 번째 행의 모든 사용 가능한 열을 사용해야 함을 알려줍니다. 우리의 경우: 모든 16열을 사용합니다.

모든 열의 양에 대한 클래스가 있으므로 첫 번째 요소에 col-span-10을 할당하고 두 번째 요소에 col-span-6을 할당하여 한 행에 2개의 요소를 채울 수도 있습니다.

<img src="/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_4.png" />

이 지식을 바탕으로 우리의 챗봇에 필요한 모든 요소를 추가할 수 있습니다:

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

```python
from nicegui import ui

with ui.grid(columns=16).classes("w-3/4 place-self-center gap-4"):
    ui.markdown("# 🚀 우리의 Gemini 챗봇").classes("col-span-full")
    ui.input(label="프롬프트").classes("col-span-10")
    ui.select(
        options=["기본", "산타 클로스"],
        value="기본",
        label="성격 선택"
    ).classes("col-span-6")
    ui.button("Gemini로 전송").classes("col-span-full")

    with ui.card().classes("col-span-full"):
        ui.markdown("## Gemini 응답")
        ui.separator()
        ui.label("Gemini에 프롬프트를 보내고 응답을 여기서 확인하세요.")

ui.run()
```

위의 코드는 다음과 같은 웹 UI를 만들어냅니다:

<img src="/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_5.png" />

Python으로 완전히 작성된 UI로는 꽤 괜찮은 결과가 나온 것 같네요.

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

# 기본 기능 추가하기

우리의 다음 작업은 기본 기능을 추가하는 것입니다. 아직 VertexAI나 Gemini와 상호 작용하지는 않겠지만, Gemini로 전송 버튼을 클릭하면 사용자 입력을 반영하는 알림이 표시되도록 기능을 추가하려고 합니다.

중요한 개념 하나를 설명해 드리겠습니다: 우리의 프론트엔드는 우리 Python 스크립트의 한 인스턴스에 의해 제공됩니다. 이제 사용자 입력을 전역 변수에 저장하고 있는데, 동시에 챗봇을 사용하고 있는 다른 사용자가 다른 값을 제출한다고 상상해 보십시오. 그러면 첫 번째 사용자의 값이 덮어쓰여지기 때문에 재밌지만 예상치 못한 동작이 발생할 것입니다.

최근 NiceGUI에서는 이러한 상황을 다루기 위해 '저장소' 기능을 소개했습니다. 이는 데이터 지속성을 위한 간단한 메커니즘으로, 클라이언트 측에 데이터를 저장하는 것과 서버 측에 데이터를 저장하는 것을 포함한 다섯 가지 내장 저장 유형을 기반으로 합니다.

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

그러나 저장소 기능은 페이지 빌더의 컨텍스트에서만 사용할 수 있습니다. 기본적으로 이것은 다음을 의미합니다: 메인 스크립트에서 웹 페이지를 간단히 코딩하는 대신, 각 페이지마다 함수로 래핑합니다. 우리는 한 페이지만 가지고 있으므로 하나의 함수만 필요합니다: index(). 그런 다음 해당 함수가 메인 인덱스 페이지인 /로 정의된 페이지를 나타낸다고 NiceGUI에 알리기 위해 데코레이터를 사용합니다.

이제 페이지 데코레이터를 사용하면 저장소 기능도 사용할 수 있습니다. 간단한 클라이언트 측 저장소를 사용할 것입니다. 이를 위해 nicegui에서 app을 가져와야하며, 다음과 같이 사전 기반 저장소인 app.storage.client에 액세스할 수 있습니다.

NiceGUI의 또 다른 기능으로 데이터 작업을 쉽게 만들어주는 기능이 있습니다. 그 방법은 입력 요소를 변수에 바인딩하는 것입니다. 이렇게 함으로써 사용자 프롬프트를위한 입력 요소를 앞서 언급한 클라이언트 저장소에 저장된 변수에 바인딩할 수 있습니다:

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
ui.input((label = "Prompt"))
  .bind_value(app.storage.client, "prompt")
  .classes("col-span-10");
```

이제 입력 요소의 값은 항상 다음과 같이 액세스할 수 있습니다: app.storage.client.get("personality").

그리고 NiceGUI는 버튼 및 기타 요소에 on_click 매개변수를 정의할 수 있도록 합니다. 이 매개변수는 일반 Python 함수에 대한 참조를 취합니다. 이렇게 하면 웹 애플리케이션을 상호 작용 가능하게 할 수 있습니다.

먼저 send() 함수를 소개하겠습니다. 나중에 Gemini LLM과 상호 작용하기 위해 사용할 것입니다. 현재 입력 양식의 현재 입력 값을 사용자에게 간단히 보여줄 것입니다.

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
from nicegui import ui, app

def send():
    prompt = app.storage.client.get("prompt")
    personality = app.storage.client.get("personality")
    ui.notify(
        f"Prompt: {prompt}, Personality: {personality}",
        type="info"
    )

@ui.page('/')
def index():
    with ui.grid(columns=16).classes("w-3/4 place-self-center gap-4"):
        ui.markdown("# 🚀 My Gemini Chatbot").classes("col-span-full")
        ui.input(label="Prompt").bind_value(app.storage.client, "prompt").classes("col-span-10")
        ui.select(
            options=["Default", "Santa Claus"],
            value="Default",
            label="Personality"
        ).bind_value(app.storage.client, "personality").classes("col-span-6")
        ui.button("Send to Gemini", on_click=send).classes("col-span-full")

        with ui.card().classes("col-span-full"):
            ui.markdown("## Gemini Response")
            ui.separator()
            ui.label("Send your prompt to Gemini and see the response here.")

ui.run()
```

이제 사용자가 "Send to Gemini" 버튼을 클릭할 때마다, 입력 요소의 값이 표시되는 send() 함수를 통해 알림이 표시됩니다.

![image](/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_6.png)

# Jinja2를 활용한 모듈화된 프롬프트들

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

시간이 되었어요! 🌪️ 사용자 프롬프트를 Gemini에 단순히 전송하는 대신, 사용자 입력을 기반으로 모듈식 프롬프트를 구성할 거에요. 그러면 프롬프트에 개성 부분을 프로그래밍적으로 추가해서 AI가 선택한 사용자에 따라 다른 성격으로 응답할 수 있어요.

Jinja2는 파이썬을 위한 템플릿 엔진이에요. Jinja2는 다양한 영역에서 동적 콘텐츠를 생성하는 데 도움이 되죠. 이는 로직과 표현을 분리하여 깨끗하고 유지보수 가능한 코드베이스를 유지할 수 있게 해줘요.

Jinja2는 다음과 같은 핵심 개념을 사용해요:

- 템플릿: 사용 사례에 따라 내용이 포함된 텍스트 파일 (예: HTML, 설정 파일, SQL 쿼리).
- 환경: 템플릿 구성을 관리해요 (예: 구분자, 자동 이스케이핑).
- 변수: 이중 중괄호 ('' 변수 '')를 사용하여 템플릿에 삽입돼요.
- 블록: '% ... %' 태그로 정의돼요 (예: 반복, 조건문을 위한) 제어 흐름.
- 주석: 코드 가독성을 위해 '# ... #'로 묶여 있어요.

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

Jinja2은 웹 개발에서 많이 사용되지만, 동적 콘텐츠를 생성하는 데 유용하기 때문에 Airflow와 같은 다른 경우에도 사용됩니다.

이 프로젝트에서는 특정 인격과 사용자 프롬프트로 대체되는 변수를 포함하는 일반 템플릿을 정의하는 데 사용할 것입니다. 이렇게 하면 Python 코드가 깔끔하게 유지되고 쉽게 확장할 수 있는 모듈식 솔루션을 갖게 됩니다. 스포일러: 나중에 매우 재미있는 인격을 소개할 예정입니다.

Jinja2를 사용하기 전에 프로젝트 의존성에 추가해야 합니다. Poetry를 사용하고 있으므로 다음을 통해 수행할 수 있습니다:

```js
poetry add jinja2
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

우리는 템플릿을 저장할 폴더도 필요합니다. 좋은 기본 사례는 모듈 폴더에 templates라는 폴더를 추가하는 것입니다. 그러니 이 경우에는:

```js
mkdir my_gemini_chatbot/templates
```

<img src="/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_7.png" />

Jinja2를 사용하려면 환경을 설정해야 합니다. 위에서 설명한 대로, 환경은 일반 템플릿 구성을 관리합니다. 우리는 간단하게 유지하고 Jinja2가 우리의 폴더에서 템플릿을 찾도록 만들겠습니다:

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

env = Environment(
loader=PackageLoader("my_gemini_chatbot"),
autoescape=select_autoescape()
)

이제 템플릿을 준비할 시간입니다. templates/ 폴더 내에 prompt.jinja, default.jinja 및 santaclaus.jinja 세 개의 파일을 생성하십시오. default.jinja 파일은 비워두세요. 기본 펄스널리티는 Gemini의 정상 동작일 것이므로요.

![image](/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_8.png)

다음 내용을 prompt.jinja 템플릿에 추가해 봅시다. 이것은 기본 템플릿입니다:

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

{ 성격 }

{ 프롬프트 }

이제, santaclaus.jinja에 다음 내용을 추가하여 산타클로스 성격을 정의해 봅시다:

```js
당신은 산타클로스이며 크리스마스를 사랑합니다. 당신의 답변에는 크리스마스와 관련된 사실과 퀴즈를 최대한 추가해 보세요. 또한 답변을 항상 엄격하게 "Ho ho ho"로 시작하고 "메리 크리스마스"로 끝내세요. 당신은 진정한 크리스마스 애호가입니다.
```

빠른 알림: 웹 UI에는 성격을 선택하는 select 요소가 있습니다.

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
ui.select(
  (options = ["Default", "Santa Claus"]),
  (value = "Default"),
  (label = "성격")
)
  .bind_value(app.storage.client, "personality")
  .classes("col-span-6");
```

작은 도우미 함수를 사용하겠습니다. 이 함수는 선택한 값을 템플릿 파일로 매핑합니다:

```js
def get_personality_file(value):
    match value:
        case "Default":
            return "default.jinja"
        case "Santa Claus":
            return "santaclaus.jinja"
        case _:
            return "default.jinja"
```

이제 이 도우미 함수와 Jinja2 환경의 get_template 기능을 사용하여 템플릿을 사용한 프롬프트를 만들 수 있습니다:

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

```python
from jinja2 import Environment, PackageLoader, select_autoescape
from nicegui import ui, app


env = Environment(
    loader=PackageLoader("my_gemini_chatbot"),
    autoescape=select_autoescape()
)


def get_personality_file(value):
    match value:
        case "Default":
            return "default.jinja"
        case "Santa Claus":
            return "santaclaus.jinja"
        case _:
            return "default.jinja"


def send():
    user_prompt = app.storage.client.get("prompt")
    personality = app.storage.client.get("personality")

    personality_template = env.get_template(get_personality_file(personality))
    prompt_template = env.get_template("prompt.jinja")

    prompt = prompt_template.render(
        prompt=user_prompt,
        personality=personality_template.render()
    )

    ui.notify(
        f"Prompt: {prompt}",
        type="info"
    )


@ui.page('/')
def index():
    with ui.grid(columns=16).classes("w-3/4 place-self-center gap-4"):
        ui.markdown("# 🚀 My Gemini Chatbot").classes("col-span-full")
        ui.input(label="Prompt").bind_value(app.storage.client, "prompt").classes("col-span-10")
        ui.select(
            options=["Default", "Santa Claus"],
            value="Default",
            label="Personality"
        ).bind_value(app.storage.client, "personality").classes("col-span-6")
        ui.button("Send to Gemini", on_click=send).classes("col-span-full")

        with ui.card().classes("col-span-full"):
            ui.markdown("## Gemini Response")
            ui.separator()
            ui.label("Send your prompt to Gemini and see the response here.")


ui.run()
```

지금 "Send to Gemini"을 클릭하면 Jinja2 템플릿을 기반으로 만든 모듈화된 프롬프트를 볼 수 있습니다.

<img src="/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_9.png" />

# VertexAI를 통해 Gemini LLM 통합하기

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

젬니아이(GeminiAI)를 사용하기 위해서는 VertexAI를 통해 Gemini에 연결하려면, VertexAI가 활성화된 Google Cloud 프로젝트와 충분한 액세스 권한을 가진 서비스 계정 및 해당 계정의 JSON 키 파일이 필요합니다.

![](/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_10.png)

새 프로젝트를 생성한 후, APIs 및 서비스 - `API 및 서비스 활성화 -` VertexAI API 검색 - ` 활성화로 이동합니다.

![](/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_11.png)

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

서비스 계정을 생성하려면 IAM 및 관리 - 서비스 계정 - 서비스 계정 만들기로 이동하십시오. 적절한 이름을 선택하고 다음 단계로 이동하십시오.

![이미지](/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_12.png)

이제 계정에 미리 정의된 역할 Vertex AI 사용자를 할당하는지 확인하십시오.

![이미지](/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_13.png)

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

마침내 새로운 사용자 → 키 → 키 추가 → 새 키 생성 → JSON을 클릭하여 JSON 키 파일을 생성하고 다운로드할 수 있습니다. 이 파일이 있으면 사용할 준비가 된 것입니다.

![이미지](/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_14.png)

JSON 자격 증명 키 파일을 준비하고 프로젝트 내에 저장한 후에는 VertexAI를 초기화할 수 있습니다.

```js
credentials = service_account.Credentials.from_service_account_file(
  "gcp-vojay-gemini.json"
);
vertexai.init(
  (project = "vojay-329716"),
  (location = "us-central1"),
  (credentials = credentials)
);
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

이제 VertexAI를 통해 모델을 로드할 수 있어요. 저희는 Gemini Pro 모델을 사용할 거예요.

```js
model = GenerativeModel("gemini-pro");
```

이 모델은 대화를 시작하는 start_chat 함수를 제공해요. 이 함수는 Gemini에 데이터를 보내는 send_message 함수를 가진 Chat 객체를 반환해요. 여기서 generation config 매개변수를 조정할 수도 있어요. 여기서는 기본값을 사용할 거예요. Gemini로부터의 응답을 스트리밍하기 때문에, 전체 채팅 응답을 받기 위해 도우미 함수를 사용할 거에요:

```js
def get_chat_response(chat, prompt):
    text_response = []
    responses = chat.send_message(prompt, stream=True)
    for chunk in responses:
        text_response.append(chunk.text)
    return ''.join(text_response)
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

그럼 이 정도면 괜찮네요. 우리는 준비된 프롬프트와 VertexAI가 초기화된 상태이며, 채팅 응답을 가져오는 도우미 함수를 사용하여 마침내 Gemini를 통합할 수 있게 되었어요.

클라이언트 스토리지에서 변수에 바인딩된 레이블을 추가할 것이고, 이 레이블은 Gemini 응답을 저장하고 렌더링하는 데 사용될 거에요:

```js
ui.label().bind_text(app.storage.client, "response");
```

이제 첫 번째 버전이 준비되었습니다:

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
import vertexai
from google.oauth2 import service_account
from jinja2 import Environment, PackageLoader, select_autoescape
from nicegui import ui, app
from vertexai.generative_models import GenerativeModel

credentials = service_account.Credentials.from_service_account_file(
    "../gcp-vojay-gemini.json"
)
vertexai.init(project="vojay-329716", location="us-central1", credentials=credentials)

env = Environment(
    loader=PackageLoader("my_gemini_chatbot"),
    autoescape=select_autoescape()
)

model = GenerativeModel("gemini-pro")


def get_chat_response(chat, prompt):
    text_response = []
    responses = chat.send_message(prompt, stream=True)
    for chunk in responses:
        text_response.append(chunk.text)
    return ''.join(text_response)


def get_personality_file(value):
    match value:
        case "Default":
            return "default.jinja"
        case "Santa Claus":
            return "santaclaus.jinja"
        case _:
            return "default.jinja"


def send():
    user_prompt = app.storage.client.get("prompt")
    personality = app.storage.client.get("personality")

    personality_template = env.get_template(get_personality_file(personality))
    prompt_template = env.get_template("prompt.jinja")

    prompt = prompt_template.render(
        prompt=user_prompt,
        personality=personality_template.render()
    )

    ui.notify("Sending to Gemini...", type="info")
    chat = model.start_chat()
    response = get_chat_response(chat, prompt)
    ui.notify("Received response...", type="info")

    app.storage.client["response"] = response


@ui.page('/')
def index():
    with ui.grid(columns=16).classes("w-3/4 place-self-center gap-4"):
        ui.markdown("# 🚀 My Gemini Chatbot").classes("col-span-full")
        ui.input(label="Prompt").bind_value(app.storage.client, "prompt").classes("col-span-10")
        ui.select(
            options=["Default", "Santa Claus"],
            value="Default",
            label="Personality"
        ).bind_value(app.storage.client, "personality").classes("col-span-6")
        ui.button("Send to Gemini", on_click=send).classes("col-span-full")

        with ui.card().classes("col-span-full"):
            ui.markdown("## Gemini Response")
            ui.separator()
            ui.label().bind_text(app.storage.client, "response")


ui.run()
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

![GeminiAI Chatbot](/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_16.png)

# AI가 바에 들어가다

저는 아빠가 된 이후로, 가능한 경우에 아빠 농담을 해주는 기회를 즐기고 있어요. 이 장에서는 Jinja2를 사용한 모듈식 접근 방법과 NiceGUI를 사용한 간단한 웹 UI의 이점을 설명하고 싶어 합니다.

새로운 개성을 소개해보죠. dadjokes.jinja와 같이 다른 파일 옆에 새로운 템플릿 파일을 만들어 다음 내용을 추가하세요.

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

넌 자랍니다. 하지만, 거의 모든 문장에 아빠 농담을 추가해야 합니다. 답변에 가능한 한 많은 아빠 농담을 넣고, 입력과 관련된 농담을 만드려고 노력해주세요. 또한, 답변에 많은 이모지를 추가하고 싶어 금을 참지 못할 겁니다. 😉👨‍👧‍👦

이를 실행하기 위해 우리는 우리의 도우미 함수인 get_personality_file을 확장해야 합니다. 👨‍💻

```js
def get_personality_file(value):
    match value:
        case "Default":
            return "default.jinja"
        case "Santa Claus":
            return "santaclaus.jinja"
        case "Dad Jokes":
            return "dadjokes.jinja"
        case _:
            return "default.jinja" 😄
```

그리고 우리의 입력 요소에 새 옵션을 추가하여 사용자가 새로운 옵션을 선택할 수 있도록 합시다. 🎁🧔

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
ui.select(
  (options = ["기본", "산타 클로스", "아빠 Jokes"]),
  (value = "기본"),
  (label = "성격")
)
  .bind_value(app.storage.client, "personality")
  .classes("col-span-6");
```

이제 해보기 전에 한 가지 더 구현해 봅시다. 다크 모드를 도입해 보겠습니다! NiceGUI를 사용하면 이 작업이 매우 간단합니다. ui.dark_mode()를 통해 UI 모드를 전환하기 위한 disable 및 enable 두 가지 함수를 제공하는 dark 객체를 얻을 수 있습니다. 그리드 접근 방식과 함께 사용하여 "젬니니로 전송" 버튼 옆에 UI 모드를 전환하는 두 개의 버튼을 쉽게 배치할 수 있습니다. 다음과 같이 코드 작성하세요:

```js
ui.button("젬니니로 전송", (on_click = send)).classes("col-span-8");

dark = ui.dark_mode();
ui.button("라이트 UI", (on_click = dark.disable)).classes("col-span-4");
ui.button("다크 UI", (on_click = dark.enable)).classes("col-span-4");
```

"젬니니로 전송" 버튼이 col-span-full 클래스 대신 col-span-8 클래스를 사용하고 있음을 알 수 있습니다. 또한 16개 열의 그리드를 사용하므로 바로 옆에 각각 col-span-4를 사용하여 두 개의 새로운 버튼을 추가할 수 있습니다.

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

표를 Markdown 형식으로 바꾸는 방법은 다음과 같아요:

| Function    | Description       |
| ----------- | ----------------- |
| ui.run()    | 애플리케이션 실행 |
| ui.page()   | 페이지 만들기     |
| ui.button() | 버튼 생성         |

위와 같이 수정하시면 Markdown 형식으로 표를 작성할 수 있어요!

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

아빠로서 이걸 승인합니다 😂.

![image](https://miro.medium.com/v2/resize:fit:1400/1*9xgCK0hrAI4XznUwk44o-w.gif)

# 결론

농담은 농담이고, 이 글을 통해 Gemini LLM을 기반으로 자체 AI 챗봇을 만들고 VertexAI와 함께 파이썬을 사용하여 NiceGUI로 간단한 웹 UI를 만드는 방법을 배웠습니다. Jinja2 템플릿을 사용하여, 이 비교적 짧은 예제조차도 확장하기 쉬운 모듈식 AI 애플리케이션을 제공했습니다.

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

![GeminiAI Chatbot](/assets/img/2024-05-20-CreateyourownGeminiAI-chatbotwithatwistusingPythonJinja2andNiceGUI_18.png)

파이썬, Jinja2 및 NiceGUI를 사용하면 VertexAI의 Gemini LLM과 상호 작용하는 사용자 친화적 인터페이스를 구축할 수 있습니다. 이를 통해 교육용 챗봇부터 재미있는 개성 기반 챗 체험까지 다양한 창의적인 응용 프로그램이 가능해집니다.

이 블로그 글이 여러분들에게 VertexAI의 잠재력을 탐험하고 자체 AI 애플리케이션을 구축해보는데 영감을 줬기를 희망합니다.

즐기고, 지시를 따르는 데 서툰 AI를 무엇이라고 부를까요? — 절망적 절자 없는 반란자.
