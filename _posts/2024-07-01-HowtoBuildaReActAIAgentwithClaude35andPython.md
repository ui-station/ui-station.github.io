---
title: "Claude 35와 Python으로 ReAct AI 에이전트 만드는 방법"
description: ""
coverImage: "/assets/img/2024-07-01-HowtoBuildaReActAIAgentwithClaude35andPython_0.png"
date: 2024-07-01 16:09
ogImage: 
  url: /assets/img/2024-07-01-HowtoBuildaReActAIAgentwithClaude35andPython_0.png
tag: Tech
originalTitle: "How to Build a ReAct AI Agent with Claude 3.5 and Python"
link: "https://medium.com/ai-advances/how-to-build-a-react-ai-agent-with-claude-3-5-and-python-95423f798640"
---


리즌+액트(ReAct) 에이전트들은 사고의 연쇄를 결합하고 외부 도구에 접근하며 해결책으로 나아가는 능력을 활용하여 복잡한 추론 작업을 수행할 수 있습니다.

에이전트의 중요한 구성 요소는 시스템 프롬프트로, 에이전트의 전반적인 행동을 정의합니다(곧 예시를 볼 것입니다).

처리 과정은 문제의 해결책을 요청하는 사용자 프롬프트로 시작됩니다. 시스템 프롬프트에 따라 에이전트는 문제에 대해 사고하고 해결에 도움이 되는 외부 도구를 적절하게 선택합니다.

에이전트가 도구를 호출하고 응답을 받은 후, 더 많은 처리가 필요한지 판단합니다. 필요하다면 다시 도움을 요청할 수 있습니다. 에이전트는 문제를 해결하고 사용자에게 결과를 반환할 때까지 추론과 조치(도구 호출)를 반복합니다.

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

아래 다이어그램에서 과정이 설명되어 있습니다.

<img src="/assets/img/2024-07-01-HowtoBuildaReActAIAgentwithClaude35andPython_0.png" />

ReAct 에이전트의 작동 방식을 설명하는 가장 쉬운 방법은 간단한 사례 연구로 설명하는 것입니다. 아래는 간단한 산술 문제를 해결하는 에이전트의 예시 응답입니다.

우리는 "20 * 15은 무엇인가요?" 라는 질문으로 시작해서, 그에 대한 응답으로 에이전트가 '소리내어 생각'하기 시작합니다.

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

에이전트의 응답 앞에는 '사고' 레이블이 있는데, 이는 에이전트가 어떻게 해야 하는지 고민합니다. 이 경우, 문제를 해결하기 위해 'calculate' 동작을 사용하기로 결정했습니다('calculate'는 에이전트에 제공된 도구입니다).

다음 응답은 '동작' 레이블로 시작되며, 에이전트가 'calculate' 도구를 사용하여 답변을 검색하는 것을 볼 수 있습니다. 이 후에는 '관찰'이 나오는데, 이는 도구에서의 응답입니다.

마지막 '답변'은 에이전트가 초기 질문과 'calculate' 도구의 출력으로부터 응답을 작성한 결과입니다.

보통 이곳에서 특별하게 벌어지는 일은 없어 보이지만, 에이전트가 우리가 제공한 도구를 사용하고 있다는 점에서 조금은 특별한 점이 있습니다.

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

그것이 ReAct 에이전트의 기본입니다: 에이전트는 이성을 사용하여 행동합니다.

## 반복

더 흥미로워지는 부분은 에이전트가 결론에 도달하지 못했다고 판단하고 다른 이성/행동 시퀀스를 거쳐야 한다는 경우입니다. 에이전트는 유효한 결론에 도달할 때까지 추론/행동 시퀀스를 반복할 수 있습니다.

위의 예시는 사소하지만 기본적인 이벤트 시퀀스를 보여줍니다.

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

좀 더 복잡한 쿼리를 시도해보면 실제 반복을 볼 수 있어요.

저는 다음과 같은 질문을 에이전트에게 했어요: "농구 팀의 선수 수를 라크로스 팀의 선수 수로 곱하면 얼마가 되나요?".

이 질문에 대한 답변을 하기 위해 에이전트는 각 스포츠의 선수 수를 찾아 곱해야 해요. 곱셈을 위해 calculate 도구를 사용할 수 있지만, 농구와 라크로스에 대해 알아내기 위해서는 다른 도구가 필요해요 — 위키피디아죠. 위키피디아 도구를 사용하면 에이전트가 어떤 것이든 검색하고 결과를 얻을 수 있어요.

아래에서 에이전트가 만족스러운 답변을 도출하기 전에 생각-행동-관찰 순서를 세 번 거치는 방법을 볼 수 있어요.

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

만약 결과를 자세히 읽으시면 에이전트가 결과를 생성하기 전에 이유를 생각하고 도구를 상담하는 방법을 볼 수 있고, 그러면서 지식을 쌓아 가게 됩니다.

# 매우 적은 양의 코드

ReAct 에이전트를 구현하는 데 필요한 코드는 놀랍게도 매우 적습니다. 이는 대부분의 작업이 프롬프트에서 수행되기 때문입니다.

그러니까 먼저 그것을 살펴보죠.

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


프롬프트는 문제에 어떻게 접근해야 하는지에 대해 설명하며, 이전에 본 과정의 레이블이 지정된 부분을 반복합니다.

그런 다음 계산 및 위키피디아라는 작업이 설명되고, 예제 세션이 이어집니다.

이 프롬프트는 간단한 예제이며 명확히 시연용으로만 사용됩니다. 프롬프트 설명은 도구의 사용이 하드코딩되어 있으며, 실제 제품 시스템에서는 프로그래밍 방식으로 이를 확장할 수 있어야 합니다. (곧 확인할 것이지만, calculate는 Python 함수 eval()을 사용하여 구현됩니다. 이는 코드 삽입 공격에 매우 취약한 부분이므로 매우 위험한 방법입니다.)


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

이 시점에서 Simon Willison의 작업을 인정해야 할 필요가 있습니다. 이 코드의 많은 부분은 Simon Willison의 작품에 기반합니다 — LLMs를 위한 ReAct 패턴의 간단한 Python 구현 참조 | Simon Willison의 TILs — 해당 코드는 Apache 2 라이선스가 있습니다.

이 코드와 프롬프트의 기본 구조는 Simon의 것이지만, 저는 Anthropic의 Claude 3.5 Sonnet LLM을 사용하도록 수정하고 코드와 프롬프트를 간소화했습니다.

## Claude 3.5

Claude Sonnet 3.5은 Anthropic의 최근 릴리스로, 릴리스 발표에서 "Claude 3.5 Sonnet은 인텔리전스에 대한 산업 기준을 높여 다양한 평가에서 경쟁 모델과 Claude 3 Opus보다 우월하며, 중급 모델인 Claude 3 Sonnet의 속도와 비용을 가지고 있습니다." 라고 설명합니다.

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

그것을 맥락에 맞춰 설명하자면, Anthrop have는 저렴하고 빠른 하이쿠 모델부터 똑똑하지만 더 비싼 오퍼스까지 세 가지 버전의 Claude LLM을 보유하고 있습니다. 소넷은 그 중간 모델로서 이 글을 작성하는 시점에 버전 3.5 릴리스가 있습니다.

나는 어느 때부터 Claude를 살펴보고 싶어했었는데, 이런 좋은 기회였습니다.

코드는 필요한 라이브러리를 불러오는 부분으로 시작됩니다:

```js
import anthropic
import re
import httpx
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

그 답변은 많지 않네요. LLM을 위한 추론, 정규 표현식 및 Wikipedia에 액세스할 수 있는 HTTP 라이브러리가 필요하겠네요.

이 코드를 실행하려면 물론 Anthropic 계정이 필요하고 사용 요금이 부과될 거에요. 하지만 가격은 꽤 저렴하답니다: 클로드 3.5 소네는 이전 버전보다 더 강력해지면서 더 저렴해진 것 같아요 — 제가 여기서 코드를 여러 번 실행해 봤는데 수십 센트 정도만 청구받았어요.

이 코드는 먼저 클라이언트를 만든 후 챗봇을 구현할 파이썬 클래스를 정의하는 방식으로 시작합니다. 오픈AI와는 달리, Claude는 시스템 프롬프트를 사용자나 어시스턴트의 것과 분리하여 ChatBot을 인스턴스화할 때 초기화합니다. `__call__` 함수는 사용자 메시지를 저장하고 챗봇의 응답을 처리하고 에이전트를 실행하는 `execute`를 호출하는 역할을 합니다.

```python
client = anthropic.Anthropic(api_key="여기에 API 키를 입력하세요")

class ChatBot:
    def __init__(self, system=""):
        self.system = system
        self.messages = []

    def __call__(self, message):
        self.messages.append({"role": "user", "content": message})
        result = self.execute()
        self.messages.append({"role": "assistant", "content": result})
        return message

    def execute(self):
        message = client.messages.create(
            model="claude-3-5-sonnet-20240620",
            max_tokens=1000,
            temperature=0,
            system=self.system,
            messages=self.messages
        )
        return message.content
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

다음 단계는 챗봇의 인스턴스를 사용하는 query() 함수를 정의하는 것입니다. query 함수는 작업이 더 이상 없을 때까지 루프를 실행합니다(또는 최대 반복 횟수에 도달할 때까지). 이 함수는 작업을 감지하고 작업의 이름을 정규식을 사용하여 추출합니다. 작업이 호출된 후 더 이상 작업이 없는 경우, 챗봇 메시지가 반환됩니다.

```js
action_re = re.compile('^Action: (\w+): (.*)$')

def query(question, max_turns=5):
    i = 0
    bot = ChatBot(prompt)
    next_prompt = question
    while i < max_turns:
        i += 1
        result = bot(next_prompt)
        print(result)
        actions = [action_re.match(a) for a in result.split('\n') if action_re.match(a)]
        if actions:
            # 실행할 작업이 있습니다
            action, action_input = actions[0].groups()
            if action not in known_actions:
                raise Exception("Unknown action: {}: {}".format(action, action_input))
            print(" -- running {} {}".format(action, action_input))
            observation = known_actions[action](action_input)
            print("Observation:", observation)
            next_prompt = "Observation: {}".format(observation)
        else:
            return bot.messages
```

이제 wikipedia 및 eval과 같은 작업 함수(도구)를 정의하고 해당 함수에 대한 참조를 사전에 저장해야 합니다.

```js
def wikipedia(q):
    return httpx.get("https://en.wikipedia.org/w/api.php", params={
        "action": "query",
        "list": "search",
        "srsearch": q,
        "format": "json"
    }).json()["query"]["search"][0]["snippet"]

def calculate(what):
    return eval(what)

known_actions = {
    "wikipedia": wikipedia,
    "calculate": calculate
}
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

다음은 봇이 생성한 마지막 메시지를 출력하는 유틸리티 함수입니다.

```js
def get_last_message():
    for m in bot.messages[-1]['content'][0].text.split('\n'):
        print(m)
```

그리고 그 모든 것이 끝나면 에이전트를 사용할 수 있습니다.

```js
query("What is 20 * 15")
get_last_message()
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

위의 코드는 기사 맨 위에 본 매우 첫 번째 예제에 결과를 초래하며 아래의 코드는 ReAct 에이전트가 다양한 조치를 순환하여 유효한 결론에 도달하는 방식의 또 다른 예제를 보여줍니다.

에이전트가 도구에 대한 호출을 순환해야 하는 또 다른 예제가 여기 있습니다. 이 경우, 에이전트는 위키피디아만 사용하지만 답변을 도출하기 위해 응담을 지능적으로 분석해야 합니다.

```js
query("스페인에서 사용되는 언어 중 프랑스에서도 사용되는 언어는 무엇인가")
get_last_message()
```

위의 응답은 에이전트가 적절한 답변을 도출하기 위해 거치는 합리적인 과정을 보여줍니다.

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

저는 또한 에이전트가 잘 다루는 것으로 보이는 좀 더 복잡한 질문도 시도해 보았습니다 (다운로드 가능한 코드에 더 많은 예제를 볼 수 있습니다).

## 결론

여기 제시된 코드는 ReAct 에이전트가 작동하는 방식을 보여줍니다. 이 코드는 견고하지 않으며 제품용으로 적합하지 않습니다. 하지만 이제 ReAct 에이전트의 원리에 대해 어느 정도 이해하고 구현할 수 있다는 것을 바라겠습니다.

더 복잡한 예제로 코드를 시도해보고, 아마도 더 많은 도구를 추가해 보세요. 여러분의 실험에 대해 알고 싶어합니다.

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

물론, Langchain과 Llamaindex를 사용한 더 간단한 해결책도 있습니다(예:). 나는 아마도 미래에 이를 다룰 것 같습니다. 그러나 이들의 추상화된 구현은 에이전트의 내부 작업을 숨기는데, 저는 이 글과 Simon의 코드가 이를 명확히 만들었다고 기대합니다.

모든 코드와 예제는 내 [Github 레포지토리](GitHub — alanjones2/claudeapps)에서 확인할 수 있습니다 — 자유롭게 다운로드하십시오.

더 많은 기사를 보려면, 여기 Medium에서 나를 팔로우하시거나, 가끔씩 소식지를 구독해 주세요. 이전 기사들은 내 웹페이지에 나와 있습니다.

# 노트와 참고 문헌

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

- Shunyu Yao 등, ReAct: 언어 모델에서 추론과 행동을 시너지적으로 결합하기, The Eleventh International Conference on Learning Representations (ICLR), 2023. (https://arxiv.org/pdf/2210.03629에서 검색, 2024년 6월 27일에 접속)
- Simon Willison의 블로그는 여기에 있습니다.
- 모든 이미지와 삽화는 별도로 표시되지 않는 한 저자 본인이 만들었습니다.

본 기사의 버전이 여기에 게시되어 있습니다.