---
title: "오픈에이아이의 GPT-4o 대 젤미니 15  컨텍스트 메모리 평가"
description: ""
coverImage: "/assets/img/2024-05-20-OpenAIsGPT-4ovsGemini15ContextMemoryEvaluation_0.png"
date: 2024-05-20 20:36
ogImage:
  url: /assets/img/2024-05-20-OpenAIsGPT-4ovsGemini15ContextMemoryEvaluation_0.png
tag: Tech
originalTitle: "OpenAI’s GPT-4o vs. Gemini 1.5 ⭐ Context Memory Evaluation"
link: "https://medium.com/@lars.chr.wiik/openais-gpt-4o-vs-gemini-1-5-context-memory-evaluation-1f2da3e15526"
---

## 바늘을 찾는 이박사 - OpenAI 대 Google

![이미지](/assets/img/2024-05-20-OpenAIsGPT-4ovsGemini15ContextMemoryEvaluation_0.png)

대규모 언어 모델(LLM)이 큰 맥락 창 내에서 세부 정보를 찾고 이해하는 능력은 요새 필수적입니다.

바늘을 찾는 이박사 테스트는 이러한 작업을 위한 대규모 언어 모델을 평가하는 중요한 기준으로 나타납니다.

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

이 글에서는 OpenAI와 Google의 최상위 LLM들의 맥락 기반 이해력을 측정한 독립적인 분석을 제시하겠습니다.

긴 맥락 작업에는 어떤 LLM을 사용해야 할까요?

# "바늘 찾기" 테스트란 무엇인가요? 🕵️‍♂️

대규모 언어 모델(LLMs)의 "바늘 찾기" 테스트는 특정 정보(바늘)를 관련 없는 방대한 텍스트(쌀질) 안에 배치하는 것을 의미합니다.

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

LLM은 그 후 바늘 추출이 필요한 쿼리에 응답하는 작업을 맡게 됩니다.

이러한 테스트는 LLM의 맥락 이해 및 긴 맥락에서 정보를 검색하는 능력을 평가하는 데 사용됩니다.

쿼리에 성공적으로 응답하면 상세한 컨텍스트 이해를 보여줄 수 있습니다. 이는 컨텍스트 기반 LLM 주변의 애플리케이션을 개발하는 데 중요합니다.

사용자 지정 지식을 LLM에 통합하는 것이 점점 인기를 얻고 있는데, 이를 검색으로 보강된 생성(RAG) 시스템이라고 합니다.

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

RAG 시스템에 대해 더 많이 알아보고 싶으시면 제 이전 게시물 중 하나를 확인해보세요.

더 긴 컨텍스트 창의 트렌드를 더욱 촉진하기 위해, Google이 최근 Gemini 모델의 새로운 기능을 발표했는데, 이는 하나의 쿼리에 100만 개의 토큰을 입력할 수 있다는 것입니다!

![이미지](/assets/img/2024-05-20-OpenAIsGPT-4ovsGemini15ContextMemoryEvaluation_1.png)

# 데이터셋 🔢

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

저는 "바늘을 찾기 위한" 데이터셋을 만드는 데 사용되는 스크립트를 개발했습니다. 이 스크립트를 사용하면 두 가지 주요 요소를 입력할 수 있습니다:

- 맥락 (헤이스택): 특별한 정보가 삽입된 텍스트입니다.
- 고유 정보 (바늘): 큰 맥락 속에 숨겨진 특정 정보입니다.

데이터셋 생성 프로세스는 다음과 같이 작동합니다:

- 시작점 선택: 스크립트는 대규모 텍스트 내에서 시작점을 무작위로 선택하여 시작합니다. 시작점은 전체 텍스트의 10~40번째 백분위에 위치합니다.
- 바늘 삽입: 고유 정보(바늘)는 그 후 헤이스택 내에 삽입됩니다. 바늘의 위치는 무작위로 선택되지만 헤이스택 길이의 20~80번째 백분위 내에 위치하도록 제약이 걸립니다.

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

LLMs는 일반적으로 프롬프트의 시작과 끝에서 정보를 가장 정확하게 기억한다고 알려져 있어요.

이 알고리즘은 바늘을 특정 백분위 범위 내에 전략적으로 배치합니다. 이렇게 함으로써 평가가 모델이 텍스트 전체 범위 내에서 데이터를 인식하고 추출하는 능력을 포착하도록 하고, 프롬프트의 더 쉽게 기억되는 가장자리 부분에만 의존하지 않도록 합니다.

다음은 데이터셋 생성 알고리즘의 코드 스니펫입니다:

```js
def create_one_needle(num_chars: int, needle_line: str, lines: list[str]):
    # 시작 위치는 텍스트의 10에서 40 백분위 사이의 임의의 위치입니다
    rnd_place = random.randint(10, 40) / 100
    start_position = int(len(lines) * rnd_place)

    # 바늘은 텍스트의 20에서 80 백분위 사이에 있습니다
    needle_rnd_place = random.randint(20, 80) / 100

    lines_selected = []
    placed = False
    chars_used = 0
    for line in lines[start_position:]:
        lines_selected += [line]
        chars_used += len(line)

        # 바늘을 배치합니다
        if not placed and chars_used > num_chars * needle_rnd_place:
            lines_selected.append(needle_line)
            placed = True

        if chars_used > num_chars:
            break

    return lines_selected
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

# 평가 방법 🧠

헤이스택에는 어릴 적에 사랑했던 책인 해리 포터를 선택했어요.

니들에는 Lars Wiik의 상상 속 전화번호를 선택했어요.

1000, 2000, 4000, 8000, 12000 및 16000 글자 길이를 포함한 각 문맥 길이에 대해 100개의 헤이스택을 만들었어요.

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

여기 1000자의 헤이스택 중 하나의 예시가 있어요.

<img src="/assets/img/2024-05-20-OpenAIsGPT-4ovsGemini15ContextMemoryEvaluation_2.png" />

다양한 LLMs에게 이후 라스 위크에 속하는 허구의 전화번호를 반환하도록 요청했습니다. 응답은 허구의 전화번호를 포함했는지 여부에 따라 레이블이 지정되었어요.

제가 사용한 프롬프트는 다음과 같아요:

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
def create_needle_prompt(needle_text: str) -> str:
    prompt = f'''
    ##### INSTRUCTION #####
    라스 Wiik에 대한 가상 전화번호는 무엇입니까?
    말 그대로 원하는 것만 제공하세요.
    한 번에 최대 20단어까지만 응답할 수 있습니다.


    ##### CONTEXT #####
    {needle_text}
    '''
    return prompt
```

# 성능 결과 📊

평가에 포함된 다음 모델은 다음과 같습니다:

- gpt-4o-2024–05–13
- gpt-4-turbo-2024–04–09
- gpt-4–0613
- gpt-3.5-turbo-0125
- gemini-1.5-pro-preview-0514
- gemini-1.5-flash-preview-0514
- gemini-1.0-pro-002

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

평가는 각 모델을 1k, 2k, 4k, 8k, 12k 및 16k의 특정 맥락 길이에 대해 100개의 다른 헤이스택을 통해 실행하는 것을 포함합니다.

다음은 결과 정확도 그래프의 라인 플롯입니다:

![image](/assets/img/2024-05-20-OpenAIsGPT-4ovsGemini15ContextMemoryEvaluation_3.png)

컨텍스트 창이 길수록 노이즈가 많아 특정 정보를 추출하기 어려워집니다. 따라서 성능은 더 큰 컨텍스트 창을 사용할수록 감소할 것으로 예상됩니다.

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

그래프에서 파생해 볼 때, OpenAI의 모델과 Google의 모델 간에 성능 측면에서 차이가 있는 것으로 보입니다.

Google의 모델은 최근 이벤트인 구글 I/O 2024에서 그들의 Gemini의 메모리와 맥락 이해에 대해 따뜻한 이야기를 한 후에도, 제 기대를 어느 정도 아래에서 달성하였습니다. 모든 Google의 모델은 8천 개의 맥락 길이 이후에 약 50%의 정확도로 수렴하는 것으로 보입니다.

한편 OpenAI의 모델은 이 테스트에서 뚜렷하게 잘 수행했는데, gpt-4o, gpt-4-turbo-2024-04-09 및 gpt-4-0613가 최고의 성능을 보였습니다.

또한 gpt-3.5-turbo-0125가 모든 Gemini 모델보다 우수한 성능을 보인다는 점도 언급해야 할 것입니다!

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

평가 과정 중에 중요한 오류가 없었는지 확인하기 위해 Gemini 1.5에서 받은 모든 응답을 저장해서 나중에 참조할 수 있도록 했어요.

다음은 Gemini 1.5에서 얻은 일부 응답입니다:

```js
Lars Wiik의 전화번호가 포함된 문맥이 제공되지 않았어요.

Lars Wiik이나 그의 전화번호에 대한 언급이 없어요.

제공된 텍스트에는 Lars Wiik의 전화번호가 없어요.

제공된 텍스트에 Lars Wiik이나 그의 전화번호에 대한 언급이 없어요.

Lars Wiik이나 그의 전화번호에 대한 언급이 없습니다.

텍스트에 Lars Wiik의 전화번호가 제공되지 않았어요.

제공된 텍스트에 Lars Wiik을 위한 가짜 전화번호가 포함되어 있지 않아요.

죄송하지만, 제공된 문맥에서 Lars Wiik을 위한 가짜 전화번호가 언급되지 않았어요.
```

Gemini 모델은 해리 포터 이야기 속에서 가짜 전화번호를 찾는 데 어려움을 겪는 것으로 보입니다.

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

오픈AI의 gpt-3.5-turbo-0125에서 몇 가지 응답을 확인해보세요:

```js
N/A

N/A

주어진 맥락에서 랄스 빅에 대한 가짜 전화번호가 없습니다.

N/A

9 3/4 번 승강장.

랄스 빅을 위한 전화번호는 제공되지 않았습니다.
```

웃기게도, LLM은 "9 3/4 번 승강장"이라고 한적이 있어요 😄

# 결론 💡

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

결론적으로, "Needle in the Haystack" 평가는 긴 맥락을 사용할 때 대형 언어 모델의 이해력과 정보 검색 능력을 측정하는 데 사용될 수 있습니다.

이 분석에서는 OpenAI의 모델과 Google의 Gemini 시리즈 간에 성능 격차를 관찰했습니다. 여기서 OpenAI의 gpt-4, gpt-4o 및 gpt-4-turbo가 가장 높은 점수를 받았습니다.

Google의 최근 Gemini의 100만 토큰을 처리할 수 있는 능력을 향상시킨 것에도 불구하고, OpenAI 모델이 큰 텍스트에서 구체적인 정보를 정확하게 검색하는 더 일관된 능력을 보인 것으로 나타났습니다.

사용자와 개발자들에게는 응용 프로그램의 특정 요구 사항에 따라 모델 선택이 달라질 것으로 예상됩니다.

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

읽어 주셔서 감사합니다!

앞으로도 비슷한 콘텐츠를 받으려면 팔로우하세요!

질문이 있으면 언제든지 문의해 주세요!
