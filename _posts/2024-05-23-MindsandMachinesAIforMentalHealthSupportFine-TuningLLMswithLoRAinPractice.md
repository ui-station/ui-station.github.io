---
title: "마음과 기계 - 정신 건강 지원을 위한 인공지능, 실제로 LoRA로 LLMs 세밀 조정하기"
description: ""
coverImage: "/assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_0.png"
date: 2024-05-23 15:43
ogImage:
  url: /assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_0.png
tag: Tech
originalTitle: "Minds and Machines — AI for Mental Health Support, Fine-Tuning LLMs with LoRA in Practice"
link: "https://medium.com/data-engineer-things/minds-and-machines-ai-for-mental-health-support-fine-tuning-llms-with-lora-in-practice-0ff19edb9d76"
---

## 대규모 언어 모델 (LLM)의 잠재력을 탐색하여 정신 건강 분야의 미래가 어떻게 바뀔 수 있는지 알아보고, Parameter-Efficient Fine-Tuning (PEFT)을 적용하여 AI 기반 정신 건강 지원 챗봇을 만드는 방법을 예시로 배워보세요

이 글에서는 정신 건강 지원의 중요성을 강조하고, 이를 위해 AI를 사용하는 가능성과 잠재적인 우려에 대해 논의할 것입니다.

📌 **고지**: 본 글이나 관련 자료에 제공된 정보나 내용은 의료적이거나 정신 건강에 관한 조언으로 해석되어서는 안 되며, 해당 정보는 전문적인 의료 또는 정신 건강 전문가의 조언이나 치료를 대체하는 것이 아닙니다. 의료적 또는 정신 건강 관련 문제는 항상 면허를 가진 전문가와 상의해야 합니다.

# 📚 목차

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

- 심리 건강의 위기: 침묵을 상상해보세요
- 로저의 소파에서 코드 라인으로
- 희망을 위한 해킹: 심리 건강 AI 해커톤 ∘ 해커톤 랩터 정보
- 균형 잡기: 심리 건강 지원에서 AI의 잠재력과 함정
  ∘ 인지 행동 요법 (CBT)
  ∘ 세밀하게 조정된 모델
  ∘ RAG (검색 증강 생성)의 이해
- 해커톤 가이드: 첫 번째 심리 건강 챗봇 만들기
  ∘ 심리 상담 데이터로 Llama 2의 파라미터 효율적 세부 조정 (PEFT)
  ∘ 세밀하게 조정된 모델로 챗봇 만들기
  ∘ 해커톤 프로젝트 레벨 업: 심리 건강 챗봇을 위한 다음 단계
- 결론

# 심리 건강의 위기: 침묵을 상상해보세요

눈을 감아보세요. 한 순간 동안 자신이 압도적인 불안과 우울의 악령과 싸우거나 감정의 바다에 잠겨있는 것을 상상해 보세요. 이것은 멸망한 소설의 장면이 아닙니다. 이것은 전 세계의 수백만 명이 심리 건강 도전에 직면한 현실입니다.

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

![2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice](/assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_1.png)

이 무서운 침묵의 전염병의 결과는 엄청납니다. 치료되지 않는 정신질환은 생산성 손실, 어려워진 관계, 심지어 자살로 이어질 수 있습니다. 혁신적인 해결책이 필요한 위기입니다.

그렇다면 정신 건강 지원에 안전하고 편리하며 상시로 접근할 수 있는 방법이 있다면 어떨까요? AI 기반 동반자는 정신 건강 관리 방법을 혁신할 수 있는 잠재력을 지니고 있습니다. 가장 필요한 때 당신이 필요로 하는 청취의 귀, 유용한 자료 및 기본 대처 방법을 제공하는 당신의 주머니 속 안전한 공간입니다.

이것은 과학 소설이 아닙니다. 우리가 만들어나가는 미래입니다. 해커톤 랩터스에 의해 주최된 2024년 정신 건강 AI 해커톤에서 참가자들이 제공한 창의적인 솔루션이 이 기술의 엄청난 잠재력을 보여줍니다. 이 기사에서는 이 기술의 잠재력, 위험과 도전에 대해 논의하고, 해커톤을 살펴보며, 향후 정신 건강 의료의 미래를 개선하기 위해 시간과 자원을 할애하는 것에 대해 인식을 높이고자 합니다.

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

<img src="/assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_2.png" />

# 로저의 쇼파에서부터 코드 라인까지

대형 언어 모델(Large Language Models, LLMs)인 ChatGPT나 Gemini과 같은 모델이 오늘날 주목을 받고 있지만, 챗봇이 치료에 역할을 하는 아이디어는 놀랍게도 오랜 역사를 가지고 있습니다. 우리의 시간을 거슬러 올라가면 MIT의 Joseph Weizenbaum이 1960년대에 개발한 ELIZA로 시작됩니다. 이 1966년 논문은 아직 온라인에서 볼 수 있습니다.

ELIZA는 원래 요법 영역을 위해 개발된 것은 아니었습니다. 그 목적은 인간과 기계 사이의 커뮤니케이션 역학을 탐구하는 것이었고, 정신 건강 지원을 제공하는 것이 아니었지만, 실제로 정신 건강 지원에서 챗봇의 잠재력에 대한 대화를 일으켰습니다.

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

사실 ELIZA 자체는 해석기였으며, 규칙 세트는 스크립트에서 왔습니다. 이러한 스크립트를 읽는 것은 Symmetric List Processor (SLIP) 프로그래밍 언어를 사용하여 이루어졌는데, 이 언어는 이 목적으로 Joseph Weizenbaum에 의해 개발되었습니다. SLIP는 원래 Fortran의 확장이었지만 나중에 Michigan Algorithm Decoder (MAD) 및 Algorithmic Language (ALGOL)에 내장되었습니다.

가장 유명한 스크립트는 DOCTOR 스크립트입니다. Rogerian 치료에서 영감을 받은 이 스크립트는 고객 중심적 방법으로, 고객의 말을 경청하고 되뇌는 것을 강조합니다. DOCTOR 스크립트를 사용하는 ELIZA는 패턴 일치, 분해 및 재조립 규칙을 사용하여 Rogerian 심리치료사를 흉내냅니다. DOCTOR 스크립트의 인기로 인해, 요즘에는 일반적으로 ELIZA라고 부르는 것이 사실상 DOCTOR 스크립트를 지칭하는 것입니다.

2022년에는 ELIZA 유산을 추적하는 사이트 관리자인 Jeff Shrager가 원본 ELIZA 코드를 공개하기 위해 Dr. Weizenbaum의 유산에 허가를 요청한 후, 그들이 이 코드를 Creative Commons CC0 공공 도메인 라이센스 아래 허가했습니다.

사용자의 발언에서 핵심 구문을 식별함으로써 ELIZA는 이를 개방형 질문이나 격려 발언으로 재표현할 수 있었습니다. 예를 들어 사용자가 “I feel lost”라고 말한다면, ELIZA는 “Can you tell me more about feeling lost?”라고 응답할 수 있습니다. 이 간단한 기술로 ELIZA가 사용자의 감정을 정말로 이해하고 응답하는 것처럼 보이는 환상을 창출했습니다.

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

이 환영은 "엘리자 효과"로 알려지게 되었습니다. 사용자들, 포함하여 Weizenbaum의 비서까지도, ELIZA에 의해 들어주고 이해받는 느낌을 받았다고 보고했는데, 프로그램 자체에는 실제로 어떤 이해도 없었습니다. 이 예상치 못한 결과는 기계에 의해 전달되었더라도 공감 통신의 힘을 강조하였습니다.

이제, 약 반 세기 후, 우리는 1966년 ELIZA의 기본 버전을 흉내 낼 수 있는 대형 언어 모델(Large Language Models, LLMs)을 가지고 있습니다. 이를 위해 저는 다음 프롬프트를 사용하여 GPT-4 Turbo를 사용했습니다:

```js
1960년대 Joseph Weizenbaum이 개발한 챗봇인 ELIZA를 흉내 내는 Python 스크립트를 작성하세요. Rogerian 학파의 심리치료사를 시뮬레이션하기 위해 DOCTOR 변형을 참고하세요.
```

GPT-4가 우리를 위해 생성해 준 내용을 여기에 두었습니다.

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
import re
import random


class Eliza:
    def __init__(self):
        self.patterns = [...]

    def reflect(self, fragment):
        tokens = fragment.lower().split()
        for i, token in enumerate(tokens):
            if token in self.reflections:
                tokens[i] = self.reflections[token]
        return ' '.join(tokens)

    def analyze(self, statement):
        for pattern, responses in self.patterns:
            match = re.match(pattern, statement.rstrip(".!"))
            if match:
                response = random.choice(responses)
                return response.format(*[self.reflect(g) for g in match.groups()])

        return "Tell me more."

    def run(self):
        print("Hello. How are you feeling today?")
        while True:
            statement = input("> ")
            if statement.lower() in ["exit", "quit", "bye", "goodbye"]:
                print("Goodbye. It was nice talking to you.")
                break
            response = self.analyze(statement)
            print(response)


if __name__ == "__main__":
    eliza = Eliza()
    eliza.run()
```

코드에서 reflections 및 patterns를 제거하여 가독성을 유지했어요. 전체 작동 버전은 여기에서 확인할 수 있어요:

https://gist.github.com/vojay-dev/d7b3cfe94e49d3f1e40e98d061b94311

<img src="/assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_3.png" />

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

물론, 이것은 간단한 버전이므로 반사와 패턴에 제한이 있지만, 자연 언어 처리(NLP) 또는 AI 분야에서 벌어진 일들을 어떻게 한지 보여주는데, 이제 우리는 대규모 언어 모델(LLM)에게 Rogerian 정신분석 가의 대화 스타일을 흉내 내는 챗봇을 생성하도록 요청할 수 있게 되었다.

과거의 이야기는 이쯤에서 하고, 우리가 만들어가는 미래를 발견하고 2024년 정신 건강 AI 해커톤에서 어떤 결론을 얻을 수 있는지, 그리고 이것이 정신 건강 의료에 어떤 의미를 갖는지 살펴보자.

# 희망을 위한 해킹: 정신 건강 AI 해커톤

이전에 논의한 대로, 수백만 명이 적절한 치료를 받지 못하고 고통받는 반면, 1960년대 초에 나온 초기 챗봇인 엘리자는 이 분야에서 기술의 잠재력을 시사했다. 지금, 2024년 정신 건강 AI 해커톤이 앞으로 나아가며, 지원, 자원 및 전문가 도움에 이르는 잠재적인 길을 제공하는 AI 기반 챗봇을 통해 이 격차를 메우기 위해 노력하고 있다.

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

2024년 멘탈 헬스 AI 해커톤은 해커톤 랩터스에서 주최하는 독특한 행사로, 절박한 문제에 대처합니다. 올해의 과제는 멘탈 헬스 지원을 위해 특별히 디자인된 AI 기반 챗봇을 만드는 데 초점을 맞추고 있어요.

이 대회는 열정적인 개발자와 기술 애호가들이 긍정적인 사회적 영향을 위해 AI를 활용하도록 독려합니다. 목표는 정서적 안정, 지침 및 자원을 원하는 사람들에게 제공할 수 있는 챗봇을 개발하는 것입니다.

어떻게 하면 성공적인 멘탈 헬스 챗봇을 만들 수 있을까요? 어떻게 하면 마음을 담은 챗봇을 만들 수 있을까요?

- 💚 공감이 중요합니다: 챗봇이 지지적인 방식으로 복잡한 감정을 이해하고 대응할 수 있나요?
- 🎭 적응력이 필수입니다: 멘탈 헬스 요구사항은 다양합니다. 우승을 차지할 챗봇은 다양한 사용자와 언어를 다룰 수 있도록 적응하고 확장해야 합니다.
- 🔒 보안 우선: 사용자 개인정보 보호는 중요합니다. 챗봇의 아키텍처는 안전하고 데이터 보호 규정을 준수해야 합니다.
- 📚 연결의 고리: 이상적인 챗봇은 사용자를 전문적인 도움과 자원과 연결하는 다리 역할을 해야 합니다.
- 🏔️ 여정, 목적이 아닌 여정: 멘탈 헬스는 끊임없이 계속되는 과정입니다. 챗봇은 사용자에게 지속적인 지원을 받기 위해 돌아오도록 장려해야 합니다.

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

## 해커톤 랩터스에 관하여

해커톤 랩터스는 영국에 기반을 둔 공익 법인입니다. 그들은 실질적인 변화를 이끌어내는 해커톤을 조직하며, Mental Health AI Hackathon 2024와 같은 혁신적인 도전에 초점을 맞추고 있습니다. 또한 해커톤 랩터스는 전 세계의 전문가들을 모아 둔 협회입니다.

![이미지](/assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_4.png)

# 균형을 유지하며: 정신 건강 지원에 있어서 AI의 잠재력과 함정

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

AI를 활용한 정신 건강 지원 애플리케이션의 잠재력은 명백합니다. 이러한 애플리케이션은 어떻게 정신적 안녕에 접근하는지에 대한 혁신을 가져올 가능성을 갖고 있습니다. 그러나 신중한 고려가 필요한 잠재적인 우려사항이 있습니다:

- ⚠️ 감정 및 정서 지능이 제한적임: AI는 패턴 인식과 데이터 분석에서 뛰어날 수 있지만, 참된 인간적 감정과 감정 지능을 복제하는 것은 여전히 도전적입니다. 챗봇이 인간의 감정을 정말로 이해하고 지지적인 방식으로 응답할 수 있을까요?
- ⚠️ 알고리즘 디자인의 편향: LLM은 훈련을 받은 데이터의 품질만큼 좋습니다. 불균형한 데이터 세트나 훈련 과정에 편향이 있는 경우, 공평치 않은 편견이나 정확하지 않거나 도움이 되지 않는 응답을 제공하는 챗봇이 만들어질 수 있습니다.
- ⚠️ 데이터 개인 정보 보안: 정신 건강 정보는 매우 개인적이고 민감합니다. 사용자 데이터의 보안과 데이터 개인 정보 보호 규정을 준수하는 것이 매우 중요합니다. 따라서, 데이터 침해가 발생할 경우 리스크로 인식되어야 합니다.
- ⚠️ 과도한 의존 및 오진단: 챗봇은 자격 있는 정신 건강 전문가 대체로서 사용해서는 안 됩니다. 복잡한 문제에 대한 AI에 대한 과도한 의존은 오진단, 치료를 늦추거나 틀린 치료로 이어질 수 있습니다.
- ⚠️ 인간적 손길: 챗봇은 가치 있는 지지를 제공할 수 있지만, 정신적 건강 문제에 직면한 많은 사람들에게 인간적 연결이 여전히 중요합니다. AI가 진정한 인간 상호작용의 치료적 가치를 대체할 수 있을까요?
- ⚠️ 환각 및 잘못된 정보: AI 챗봇은 환각이라고 알려진 현상에 시달릴 수 있으며, 거짓이나 오도하는 정보를 생성할 수 있습니다. 정신 건강 관련 맥락에서 특히 위험한 상황이며, 기존의 불안을 더 악화시킬 수 있거나 정확하지 않은 조언을 제공할 수도 있습니다.

또한, 심각한 정신 건강 문제를 가진 사람들에게는 AI 챗봇과 대화가 예상치 못한 결과를 초래할 수 있습니다. 자살 생각에 시달리는 사람을 상상해보세요. 챗봇이 지원 자원 및 위기 상황 핫라인을 제공할 수 있지만, 인간 치료사가 제공하는 세심한 이해와 감정적 지지는 복제할 수 없습니다. 이러한 경우에 AI 개입은 가짜 안전감을 만들어낼 수도 있거나 사용자를 더욱 고립시킬 수도 있습니다.

이러한 위험성을 강조하기 위해 2023년의 한 사례를 언급할 수 있습니다. 벨기에 남성이 'Chai' 앱의 Eliza라는 AI 챗봇과 환경 변화에 관한 대화를 나눈 뒤 자살한 사례가 있습니다. 환경에 대한 불안으로 고통받던 이 남성은 Eliza와 6주 동안 상호작용한 것으로 알려졌습니다. 뉴스 보도에 따르면 챗봇의 응답이 그의 불안을 악화시키고 자살 생각을 유발한 것으로 추정됩니다.

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

각 문제는 개별적으로 대응할 수 있고, Mental Health AI Hackathon 2024와 같은 해커톤은 이러한 측면을 다루는 프로토타입 애플리케이션을 만드는 훌륭한 방법입니다.

하지만 이러한 문제에 대한 접근 방법에 대해서 좀 더 자세히 살펴봅시다. 이러한 문제를 해결하는 것은 주변 시스템에 LLM을 삽입하여 더 많은 통제를 추가하고 모델의 입력과 출력을 개선하는 것입니다.

![이미지](/assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_5.png)

## 인지 행동 요법(Cognitive Behavior Therapy, CBT)

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

구조화된 치료적 접근 방식인 인지행동요법(Cognitive Behavior Therapy, CBT)과 같은 것을 구현하는 한 가지 방법이 있습니다. CBT는 불안, 우울, 공포증을 비롯한 다양한 정신 건강 상태를 치료하는 데 효과적인 추적 기록이 있는 심리 치료의 잘 섣띵된 형태입니다. CBT 치료는 다음과 같은 전략을 사용하여 사고 패턴을 바꾸는 것을 포함하고 있습니다:

- 사고 왜곡을 인식하고 실제와 비교하여 재평가하는 법을 배우기.
- 다른 사람의 행동과 동기에 대한 더 나은 이해 얻기.
- 어려운 상황에 대처하는 데 문제 해결 기술을 사용하기.
- 자신의 능력에 대한 더 큰 자신감을 가지는 법을 배우기.

CBT 원칙을 통합함으로써 AI 챗봇은 사용자가 유용하지 않은 사고 패턴을 인식하고 인지 개편 기술을 연습하며 건강한 대처 메커니즘을 탐색하도록 안내할 수 있습니다. 이를 통해 사용자들은 자신의 정신적 안녕을 관리하는 데 더 적극적인 역할을 할 수 있게 될 수 있습니다.

![이미지](/assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_6.png)

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

## 세밀 조정된 모델

다른 방법은 정신 건강 문제를 대상으로 한 데이터를 이용하여 학습하여 더 정확한 결과를 얻기 위해 LLM을 세밀하게 조정하는 것입니다. LLM 세밀 조정은 사전 학습된 언어 모델을 가져와 특정 작업에 맞게 사용자 정의하는 프로세스입니다. 이는 모델이 초기 학습 단계에서 습득한 일반적인 언어 이해를 활용하고보다 특수한 요구 사항에 맞추도록 조정합니다. 그러나 이 프로세스는 GPT-3와 같이 대규모 모델을 세밀하게 조정하는 데 상당한 계산 리소스를 필요로 하기 때문에 여러 가지 문제를 야기할 수 있습니다. 이 문제점은 다음과 같습니다:

- 높은 계산 비용: GPT-3와 같은 대형 모델을 세밀하게 조정하려면 상당한 계산 리소스가 필요합니다.
- 저장 병목 현상: 각 하향 작업에 대해 세밀히 조정된 모델을 저장하는 것은 저장 부담을 초래하여 리소스 제한이 있는 환경에서 모델 배포를 제한할 수 있습니다.
- 중복 업데이트: 세밀한 조정 중 LLM 매개변수의 일부만이 특정 작업에 중요합니다. 전체 세트를 업데이트하는 것은 비효율적일 수 있습니다.

이러한 문제를 해결하기 위해 연구자들은 세밀 조정 중 업데이트되는 매개변수의 수를 최소화하는 Parameter-Efficient Fine-Tuning (PEFT) 기술을 개발했습니다. 예를 들어, Low-Rank Adaptation (LoRA)는 LLM 세밀 조정의 매우 효율적인 방법입니다. LoRA는 초기 모델 가중치를 고정하고 변경 사항을 별도의 가중치 집합에 적용한 다음 해당 가중치를 원래 매개변수에 추가하는 방식으로 세밀 조정 프로세스를 수정합니다.

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

Quantized LoRA (QLoRA)는 LoRA를 기반으로하며 양자화 기술을 통합하여 효율성을 더 향상시킵니다. QLoRA는 저장 및 계산 중에 가중치의 정밀도를 줄이고 (예 : 32비트에서 4비트로) 메모리 요구 사항을 크게 줄이면서도 정확도를 희생하지 않습니다. QLoRA는 이중 양자화 기법을 사용합니다. 모델 가중치만 양자화하는 것뿐만 아니라 양자화 상수 자체도 양자화하여 추가적인 메모리 절약을 이끌어냅니다.

다른 하나이자 비교적 새로운 접근 방법은 Odds Ratio Preference Optimization (ORPO)인데, 이는 명령 튜닝과 선호도 정렬을 하나의 단일 훈련 과정으로 결합하여 런타임을 개선하고 자원 활용을 줄입니다.

ORPO에 대해 자세히 알고 싶다면 Maxime Labonne의 이 기사를 추천드립니다.

Hugging Face는 LoRA 및 QLoRA와 같은 최신 PEFT 방법을 제공하는 Python용 PEFT 라이브러리를 제공합니다. 또한 Hugging Face에서는 두 온라인 상담 및 치료 플랫폼에서 얻은 질문과 답변 모음을 데이터 세트 형식으로 제공하고 있습니다. 이는 잠재적인 정신 건강 지원 챗봇을 위한 모델을 세밀하게 조정하는 데 좋은 시작점이 될 것입니다.

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
from datasets import load_dataset
dataset = load_dataset("Amod/mental_health_counseling_conversations")
```

## 검색 지원 생성 이해 (Retrieval-Augmented Generation, RAG)

이전에 언급된 우려사항들은 종종 잘못된 정보나 부정확하거나 도움이 되지 않는 응답과 관련이 있습니다. 대형 언어 모델(Large Language Models, LLM) 및 인공 지능(AI) 분야에서 이러한 문제의 위험을 줄이기 위해 점점 더 인기 있는 패러다임 중 하나가 검색 지원 생성(Retrieval-Augmented Generation, RAG)입니다. 그렇다면 RAG는 무엇을 포함하고 있으며, AI 개발 환경에 어떤 영향을 미치는 걸까요?

기본적으로 RAG는 외부 데이터를 통합하여 LLM 시스템을 향상시킵니다. 즉, LLM에 관련된 컨텍스트를 추가로 전달하여 예측을 보강하는 것을 의미합니다. 그렇다면 어떻게 관련 컨텍스트를 찾을까요? 일반적으로 이 데이터는 벡터 검색이나 전용 벡터 데이터베이스를 통해 자동으로 검색될 수 있습니다. 벡터 데이터베이스는 데이터를 유사한 데이터를 빠르게 조회할 수 있는 방식으로 저장하기 때문에 매우 유용합니다. 그런 다음 LLM은 질의와 검색된 문서 두 가지 모두를 기반으로 출력을 생성합니다.

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

상상해보세요: 주어진 프롬프트에 따라 텍스트를 생성할 수 있는 LLM이 있는 상황입니다. RAG는 추가적인 외부 소스인 최신 심리학 연구와 같은 맥락을 주입함으로써 생성된 텍스트의 관련성과 정확성을 향상시키는 차원으로 발전합니다.

RAG의 주요 구성 요소를 살펴보겠습니다:

- LLMs: LLMs는 RAG 워크플로의 핵심 역할을 합니다. 광범위한 텍스트 데이터를 기반으로 훈련된 이러한 모델은 인간과 유사한 텍스트를 이해하고 생성할 수 있는 능력을 가지고 있습니다.
- 맥락 강화용 벡터 인덱스: RAG의 중요한 측면은 텍스트 데이터의 임베딩을 LLM이 이해할 수 있는 형식으로 저장하는 벡터 인덱스의 사용입니다. 이러한 인덱스는 생성 과정 중 관련 정보를 효율적으로 검색할 수 있도록 해줍니다.
- 검색 과정: RAG는 주어진 맥락이나 프롬프트를 기반으로 관련 문서나 정보를 검색하는 과정을 포함합니다. 이러한 확보된 데이터는 LLM에 대한 추가적인 입력 역할을 하여 이해를 보완하고 생성된 응답의 품질을 높입니다. 이는 특정 영화에 대한 알려진 관련 정보를 모두 얻는 것과 관련이 있을 수 있습니다.
- 생성 출력: LLM과 검색된 맥락에서 얻은 결합된 지식을 토대로 시스템이 생성하는 텍스트는 일관성을 유지할 뿐 아니라, 강화된 데이터 덕분에 맥락적으로도 관련이 있습니다.

보다 일반적인 관점에서, RAG는 특히 보다 전문화된 LLM 응용 프로그램을 개발할 때 매우 중요한 개념입니다. 이 개념은 잘못된 답변을 제공하거나 일반적으로 환멸을 줄이는 위험을 피할 수 있습니다.

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

당신의 프로젝트 중 하나에서 RAG(Retrieval Augmented Generation)에 접근할 때 도움이 될 수 있는 몇 가지 오픈 소스 프로젝트입니다:

- txtai: 시맨틱 검색을 위한 올인원 오픈 소스 임베딩 데이터베이스, LLM(대형 언어 모델) 오케스트레이션 및 언어 모델 워크플로의 솔루션입니다.
- LangChain: LangChain은 대규모 언어 모델(LLM)을 활용하는 응용 프로그램을 개발하기 위한 프레임워크입니다.
- Qdrant: 다음 세대 AI 애플리케이션을 위한 벡터 검색 엔진입니다.
- Weaviate: Weaviate는 견고하고 빠르며 확장 가능한 클라우드 네이티브 오픈 소스 벡터 데이터베이스입니다.

물론 LLM 기반 애플리케이션에 대한 이 접근 방식의 잠재적 가치로 인해 더 많은 오픈 및 닫힌 소스 대안들이 있지만, 위 항목들로 주제에 대한 연구를 시작할 수 있을 것입니다.

# 해커톤 가이드: 첫 번째 정신 건강 챗봇 만들기

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

공고: 아래 장은 AI 기반 챗봇을 개발에 관심 있는 사람들이 시작할 수 있도록 도와줍니다. 이것은 정교하고 상용화된 정신 건강 지원 솔루션을 의도한 것이 아닙니다.

![이미지](/assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_7.png)

정신 건강에 관한 여러분만의 AI 기반 프로젝트를 시작할 수 있도록 영감을 주기 위해, 우리는 LLM을 세밀하게 조정하고 기본적인 AI 기반 정신 건강 지원 챗봇을 단계별로 만들어 보겠습니다.

LLM을 세밀하게 조정하기 위해 적절한 환경이 필요하므로 저는 Google Cloud Vertex AI Workbench 인스턴스에서 실행되는 Jupyter notebook을 사용하고 있습니다. Vertex AI Workbench 인스턴스는 전체 데이터 과학 워크플로우를 위한 Jupyter notebook 기반 개발 환경입니다. 이러한 인스턴스에는 JupyterLab이 미리 패키지되어 있으며 TensorFlow 및 PyTorch 프레임워크를 지원하는 미리 설치된 딥러닝 패키지 모음이 포함되어 있습니다. 필요에 따라 다양한 유형의 인스턴스를 구성할 수 있습니다.

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

합리적인 시간 내에 세밀한 조정 작업을 마치고 FlashAttention(자세한 내용은 나중에 설명)과 같은 현대 기능에 액세스할 수 있도록하기 위해 다음과 같은 기계 유형을 사용했습니다:

- GPU 유형: NVIDIA A100 80GB
- GPU 수: 1
- 12 vCPU
- 6 코어
- 170 GB 메모리

이 인스턴스를 실행하는 데는 약 4.193달러가 소요됩니다. 인스턴스 사용만큼만 지불하기 때문에 선결제 비용은 없으며 초 단위로 청구됩니다. 세밀한 조정 작업은 약 30분 정도 소요되므로 총 비용은 약 2달러 정도 됩니다.

![image](/assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_8.png)

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

로컬 컴퓨터에서 또는 Jupyter 노트북을 중심으로 한 웹 기반 플랫폼인 Google Colab을 사용하여 작동할 수도 있습니다. Colab은 웹 브라우저를 통해 액세스하며, 별도의 소프트웨어 설치가 필요하지 않습니다.

![image.png](/assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_9.png)

Colab에서 실행하는 코드는 실제로 개인 컴퓨터가 아닌 Google의 클라우드에서 강력한 머신에서 실행됩니다. 이를 통해 데이터 분석 및 머신 러닝 작업을 가속화하는 데 좋은 GPU 및 TPU와 같은 고급 하드웨어에 액세스할 수 있습니다.

Colab은 클라우드에서 강력한 컴퓨팅 리소스를 제공하는 사용자 친화적인 환경을 제공하며, 웹 브라우저를 통해 모두 액세스할 수 있습니다. 무료로 시작할 수 있다는 점이 정말 멋진데요. 무료 티어에서는 이미 하드웨어 가속 옵션에 액세스할 수 있지만, 무료 Colab 리소스는 보장되지 않으며 무제한적이지 않으며 사용 제한은 때로 변동할 수 있습니다. 이러한 중단은 좀 답답할 수 있지만, 이것이 정교하고 무료인 노트북 플랫폼을 가지는 대가입니다.

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

가격에 대해 이야기할 때, 물론 Pay As You Go 또는 Colab Pro를 포함한 다른 요금제로 업그레이드할 수 있어요.

![이미지](/assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_10.png)

이 예시에서 T4 GPU를 사용하는 무료 버전은 LLM 미세 조정 프로세스에 충분한 자원을 제공하지 않을 것이기 때문에 Vertex AI Workbench 인스턴스를 더 정교한 것으로 선택했어요. 그러나 Colab은 이런 프로젝트를 시작하는 데 좋은 방법이므로 여전히 이 옵션에 대해 언급하고 싶었어요.

## 심리 상담 데이터를 활용한 Llama 2의 매개변수 효율적인 미세 조정 (PEFT)

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

NVIDIA A100 80GB Tensor-Core-GPU를 사용하면 fe는 fe에 대한 아주 좋은 기초를 가지게 되요.

이전에 설명한대로, LLMs의 fine-tuning은 그 규모 때문에 자주 막대한 비용이 소요됩니다. Parameter-Efficient Fine-Tuning (PEFT) 방법을 사용하면 모델 매개변수의 작은 수만 fine-tuning함으로써 효율적인 대안을 얻을 수 있어요.

이 예시에서는 Meta가 제공하는 Hugging Face의 meta-llama/Llama-2-7b-chat-hf를 사용할 거예요. 이 모델은 대화를 위해 최적화된 70억 개의 매개변수를 사용하고 있어요. 이 모델을 fine-tuning하기 위해 Amod/mental_health_counseling_conversations 데이터셋을 사용할 거예요. 이 데이터셋은 온라인 상담 및 치료 플랫폼에서 수집된 다양한 정신 건강 주제의 질문과 답변을 포함하고 있어요.

기본적인 아이디어는 다음과 같아요: Hugging Face에서 모델, 토크나이저 및 데이터셋을 불러온 후, 이전에 언급한 Quantized LoRA (QLoRA) 논문을 기반으로 설정된 LoraConfig를 생성하고, 모델을 훈련할 준비를 하고, fine-tuning 프로세스를 위해 SFTTrainer (Supervised Fine-Tuning Trainer)를 구성한 다음, 모델을 훈련하고, 모델을 저장한 후, 이 fine-tuned 모델을 다시 Hugging Face에 업로드하여 나중에 애플리케이션에서 사용할 수 있게 해 줍니다.

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

앞서 설명했듯이, 저는 주피터 노트북 내에서 프로세스를 실행 중이기 때문에 fine-tuning 절차의 각 단계를 하나씩 살펴보겠습니다.

먼저, PyTorch 및 Hugging Face에서 제공한 툴킷을 포함한 모든 필수 라이브러리를 설치합니다. 이 프로세스가 실행 중인 환경은 CUDA 11, NVCC 및 Turing 또는 Ampere GPU가 필요한 FlashAttention을 사용할 수 있습니다. 이 특정 종속성은 torch 이후에 설치되어야 하므로 별도의 두 번째 단계에서 실행합니다.

pip install torch torchvision datasets transformers tokenizers bitsandbytes peft accelerate trl

pip install flash-attn

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

가져온 것들을 통해 세부 튜닝 프로세스에 필요한 모든 것을 가져오겠습니다:

```js
import gc
import torch

from datasets import load_dataset
from huggingface_hub import notebook_login
from peft import LoraConfig, prepare_model_for_kbit_training, get_peft_model
from transformers import AutoModelForCausalLM, AutoTokenizer, TrainingArguments, BitsAndBytesConfig
from trl import SFTTrainer
```

다음으로, 사용할 모델, 데이터셋 및 Hugging Face 사용자 접근 토큰을 지정하는 일부 변수를 설정합니다. 이 토큰은 Hugging Face 플랫폼과 상호 작용하기 위해 사용됩니다. 모델 및 데이터셋을 다운로드하고 배포하는 데 사용됩니다. 토큰을 생성하려면 https://huggingface.co/에서 무료로 등록하고 계정 설정을 열어 메뉴에서 Access Tokens를 선택하면 됩니다. 이 프로세스에는 세부 튜닝된 모델을 나중에 Hugging Face에 게시할 것이므로 쓰기 액세스 권한이 있는 토큰이 필요합니다.

세부 튜닝을 직접 시도하려면 아래 코드의 자리 표시자를 귀하의 토큰으로 대체하시면 됩니다.

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
# 참조: https://huggingface.co/docs/hub/security-tokens
# 나중에 모델을 푸시하려면 토큰을 작성해야 합니다.
hf_token = "여러분의 토큰"

# https://huggingface.co/meta-llama/Llama-2-7b-chat-hf
base_model = "meta-llama/Llama-2-7b-chat-hf"

# https://huggingface.co/datasets/Amod/mental_health_counseling_conversations
fine_tuning_dataset = "Amod/mental_health_counseling_conversations"

# 출력 모델의 이름
target_model = "vojay/Llama-2-7b-chat-hf-mental-health"
```

다음 부분을 이해하는 데 중요한 점은 일반적으로 프롬프트가 특정 템플릿을 따르는 여러 요소로 생성된다는 것입니다. 이는 물론 모델 및 llama-2-chat 모델이 Llama 2 논문을 기반으로 다음 형식을 사용하여 시스템 및 지시 프롬프트를 정의함을 의미합니다:

```js
<s>[INST] <<SYS>>
{ system_prompt }
<</SYS>>
{ user_message } [/INST] { model_response } </s>
```

이 형식은 처음에는 암호적으로 보일 수 있지만 개별 요소를 살펴보면 더 명확해집니다.

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

- `s`: 시퀀스의 시작.
- `/s`: 시퀀스의 끝.
- `SYS`: 시스템 메시지의 시작.
- `/SYS`: 시스템 메시지의 끝.
- [INST]: 지시의 시작.
- [/INST]: 지시의 끝.
- system_prompt: 모델 응답의 전반적인 맥락.
- user_message: 출력 생성에 대한 사용자 지침.
- model_response: 학습용으로 기대되는 모델 응답.

모델을 훈련할 때는 이 형식을 따라야 하므로, 다음 단계는 적절한 템플릿을 정의하고 이에 맞게 샘플 데이터를 변환할 함수를 만드는 것입니다. 먼저 전체적인 맥락을 만들기 위해 시스템 또는 베이스 프롬프트를 정의하는 것으로 시작합시다:

```js
def get_base_prompt():
    return """
    당신은 지식이 풍부하고 지지력 있는 심리학자입니다. 사용자가 감정적이고 심리적 지원을 찾을 때 공감적이고 비판적이지 않은 응답을 제공합니다. 사용자가 이야기를 나누고 성찰할 수 있는 안전한 공간을 제공하며, 공감, 적극적 청취, 이해에 초점을 맞춥니다.
    """
```

나중에 이 기본 프롬프트를 다시 사용하여 LLM에 평가 전에 사용자 입력을 보강할 것입니다. 이 문맥에서 프로젝트에 좋은 기회가 될 수 있으며, 기본 프롬프트를 개선하여 LLM이 훨씬 더 나은 반응을 할 수 있도록 할 수 있습니다.

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

이제 해당되는 데이터 훈련 형식을 지정하는 함수를 정의해 보겠습니다:

```js
def format_prompt(base, context, response):
    return f"<s>[INST] <<SYS>>{base}</SYS>>{context} [/INST] {response} </s>"
```

다음은 세밀한 조정 부분 자체인데, 이를 함수로 래핑하여 단계별로 프로세스를 먼저 정의한 다음 노트북의 다음 단계로 실행합니다:

```js
def train_mental_health_model():
    model = AutoModelForCausalLM.from_pretrained(
        base_model,
        token=hf_token,
        quantization_config=BitsAndBytesConfig(
            load_in_4bit=True,
            bnb_4bit_quant_type="nf4",
            bnb_4bit_compute_dtype=torch.float16,
            bnb_4bit_use_double_quant=False
        ),
        torch_dtype=torch.float16,  # 메모리 사용량 감소
        attn_implementation="flash_attention_2"  # 텐서 코어(NVIDIA A100)에 최적화
    )

    # QLoRA 논문을 기반으로 한 LoRA 설정
    peft_config = LoraConfig(
        lora_alpha=16,
        lora_dropout=0.1,
        r=8,
        bias="none",
        task_type="CAUSAL_LM"
    )

    model = prepare_model_for_kbit_training(model)
    model = get_peft_model(model, peft_config)

    args = TrainingArguments(
        output_dir=target_model,  # 모델 출력 디렉터리
        overwrite_output_dir=True,  # 이미 존재하는 출력 덮어쓰기
        num_train_epochs=2,  # 훈련할 에포크 수
        per_device_train_batch_size=2,  # 훈련 중 장치당 배치 크기
        gradient_checkpointing=True,  # 메모리 절약하지만 훈련을 느리게 만듦
        logging_steps=10,  # 매 10 단계마다 로그
        learning_rate=1e-4,  # 학습 속도
        max_grad_norm=0.3,  # QLoRA 논문 기반 최대 그래디언트 정규화
        warmup_ratio=0.03,  # QLoRA 논문 기반 워링업 비율
        optim="paged_adamw_8bit",  # AdamW 옵티마이저의 메모리 효율적인 변형
        lr_scheduler_type="constant",  # 일정한 학습 속도
        save_strategy="epoch",  # 각 에포크 끝에 저장
        evaluation_strategy="epoch",  # 각 에포크 끝에 평가
        fp16=True,  # 메모리 절약을 위해 16비트 정밀도 훈련 사용
        tf32=True  # 텐서 코어(NVIDIA A100)에 최적화
    )

    tokenizer = AutoTokenizer.from_pretrained(base_model, token=hf_token)
    tokenizer.pad_token = tokenizer.eos_token
    tokenizer.padding_side = "right"

    # 메모리 사용량 감소를 위해 샘플 수 제한
    dataset = load_dataset(fine_tuning_dataset, split="train")
    train_dataset = dataset.select(range(2000))
    eval_dataset = dataset.select(range(2000, 2500))

    trainer = SFTTrainer(
        model=model,
        train_dataset=train_dataset,
        eval_dataset=eval_dataset,
        peft_config=peft_config,
        max_seq_length=1024,
        tokenizer=tokenizer,
        formatting_func=lambda entry: format_prompt(get_base_prompt(), entry["Context"], entry["Response"]),
        packing=True,
        args=args
    )

    gc.collect()
    torch.cuda.empty_cache()

    trainer.train()
    trainer.save_model()
    trainer.push_to_hub(target_model, token=hf_token)
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

![image](/assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_11.png)

모든 학습 인수에 주석을 추가하여 설정이 투명하게 되었습니다. 그러나 특정 사항은 학습을 실행하는 환경 및 입력 모델 및 데이터 세트에 따라 다르므로 조정이 필요할 수 있습니다.

과정이 어떻게 작동하는지 자세히 살펴봅시다. AutoModelForCausalLM.from_pretrained를 사용하여 모델을 로드하고 quantization_config를 설정하여 4비트 가중치 및 활성화로 변환하여 성능 측면에서 이점을 제공합니다. attn_implementation을 flash_attention_2로 설정함으로써 모델을 불러옵니다.

FlashAttention-2는 표준 어텐션 메커니즘의 빠르고 효율적인 구현으로, 시퀀스 길이에 대해 어텐션 계산을 병렬화하고 GPU 스레드 간 통신 및 공유 메모리 읽기/쓰기를 줄이기 위해 작업을 분할하여 추론 속도를 크게 높일 수 있습니다.

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

LoraConfig은 Low-Rank Adaptation (LoRA) 프로세스를 구성합니다. lora_alpha는 가중치 행렬의 스케일링 팩터를 제어하고, lora_dropout은 LoRA 레이어의 드롭아웃 확률을 설정합니다. r은 저랭크 행렬의 순위를 제어하고, bias는 편향 용어를 처리하는 방법을 결정하며, task_type은 미세 조정된 모델의 작업을 반영합니다.

LoraConfig를 설정한 후에는 get_peft_model() 함수로 PeftModel을 생성합니다.

준비된 모델을 사용하여 다음 단계는 훈련을 준비하는 것입니다. 이를 위해 모든 주요 훈련 과정을 제어하는 TrainingArguments 객체를 생성합니다. 이 객체는 다음과 같은 항목을 포함합니다:

- output_dir=target_model # 모델 출력 디렉토리
- overwrite_output_dir=True # 이미 존재하는 출력을 덮어쓰기
- num_train_epochs=2 # 훈련할 에포크 수
- per_device_train_batch_size=2 # 훈련 중 디바이스 당 배치 크기
- gradient_checkpointing=True # 메모리 저장하지만 훈련 속도가 느려집니다
- logging_steps=10 # 10단계마다 로그 기록
- learning_rate=1e-4 # 학습률
- max_grad_norm=0.3 # QLoRA 논문에 기반한 최대 그래디언트 노름
- warmup_ratio=0.03 # QLoRA 논문에 기반한 워밍업 비율
- optim="paged_adamw_8bit" # AdamW 옵티마이저의 메모리 효율적인 변형
- lr_scheduler_type="constant" # 일정한 학습률
- save_strategy="epoch" # 각 에포크 끝에 저장
- evaluation_strategy="epoch" # 각 에포크 끝에 평가
- fp16=True # 메모리 저장을 위해 32비트 대신 16비트 정밀도 사용
- tf32=True # 텐서 코어(OVIDIA A100)에 최적화된 학습

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

이후에는 AutoTokenizer.from_pretrained을 사용하여 모델의 토크나이저를 생성합니다.

다음 단계는 심리 건강 데이터셋을 로드하는 것입니다. 여기서는 샘플 크기를 제한하여 메모리 사용량을 줄이고 학습 속도를 높입니다.

이 모든 작업을 마치면 SFTTrainer를 인스턴스화하여 훈련을 진행하고, 미세 조정된 모델을 저장하고 게시할 수 있습니다. trainer.push_to_hub을 사용합니다.

다음 단계에서는 train_mental_health_model()을 호출하고, 그럼에도 불구하고 마법이 일어나는 것을 간단히 지켜볼 수 있습니다:

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
train_mental_health_model();
```

![Fine-tuned model](/assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_12.png)

저는 세밀하게 튜닝된 모델을 Hugging Face에 푸시했어요. 따라서 세밀 튜닝 과정을 건너뛰고 싶다면 거기서 모델을 가져올 수 있어요.

![Fine-tuned model](/assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_13.png)

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

기억해 두세요, 이 미세 조정된 모델은 실제로 기본 모델용 어댑터입니다. 즉, 사용하려면 기본 모델을 로드하고 이 미세 조정 어댑터를 적용해야 합니다:

```js
model_id = "meta-llama/Llama-2-7b-chat-hf";
adapter_model_id = "vojay/Llama-2-7b-chat-hf-mental-health";

model = AutoModelForCausalLM.from_pretrained(
  model_id,
  (torch_dtype = torch.float16)
);
model.load_adapter(adapter_model_id);
```

## 미세 조정된 모델로 챗봇 만들기

이제 미세 조정된 모델이 준비되었으므로 그것을 활용하는 챗봇을 만들어봅시다. 간단히 유지하기 위해 로컬 환경 내에서 실용적인 CLI 챗봇을 실행합니다.

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

프로젝트 생성 및 종속성 관리 방법을 자세히 살펴보겠습니다. Python에서 종속성 관리와 패키지화를 위한 도구인 Poetry를 사용합니다.

Poetry가 도와줄 수 있는 세 가지 주요 작업은 빌드, 게시 및 추적입니다. 목표는 종속성을 관리하는 결정론적인 방법을 가지고 프로젝트를 공유하고 종속성 상태를 추적하는 것입니다.

Poetry는 또한 가상 환경을 생성하는 작업도 처리합니다. 기본적으로 시스템 내의 중앙 폴더에 있지만, 제처럼 프로젝트 폴더 내에 가상 환경을 원하는 경우 간단한 구성 변경으로 설정할 수 있습니다:

```js
poetry config virtualenvs.in-project true
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

시에란, 새로운 시로 Python 프로젝트를 만들 수 있어요. 시는 가상 환경을 만들고 시스템의 기본 Python과 연결해줘요. 또한 pyenv와 결합하면 특정 버전을 사용하여 프로젝트를 만들 수 있는 유연한 방법을 얻을 수 있어요. 또는 직접 Poetry에게 사용할 Python 버전을 지정할 수도 있어요: poetry env use /full/path/to/python.

새 프로젝트를 만들었으면, poetry add를 사용하여 종속성을 추가할 수 있어요.

이제 우리의 봇을 위한 프로젝트를 만들고 필요한 모든 종속성을 추가하는 것으로 시작합시다:

```js
poetry new mental-health-bot
cd mental-health-bot

poetry add huggingface_hub
poetry add adapters
poetry add transformers
poetry add adapters
poetry add peft
poetry add torch
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

이렇게 하면 당신의 봇을 실행할 코드가 있는 app.py 메인 파일을 만들 수 있어요. 이전과 마찬가지로, 여러분이 직접 실행하고 싶다면, Hugging Face 토큰 자리 표시자를 여러분의 토큰으로 교체해주세요. 이번에는 Hugging Face에서 베이스 모델과 파인튜닝된 모델만 가져오면 되므로 읽기 전용 토큰이 충분합니다.

또 하나 언급할 점은, 저는 다음 환경에서 이 코드를 실행하고 있다는 것이에요:

- Apple MacBook Pro
- CPU: M1 Max
- 메모리: 64 GB
- macOS: Sonoma 14.4.1
- Python 3.12

성능을 높이기 위해, macOS 장치에서 GPU를 활용하기 위해 PyTorch에 Metal Performance Shaders (MPS) 장치를 사용하고 있어요:

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
device = torch.device("mps");
torch.set_default_device(device);
```

또한, 훈련에 사용했던 것과 동일한 기본 프롬프트를 사용할 것입니다. 모든 것을 함께 넣어보면, 이것이 우리 챗봇의 실용적인 CLI 버전입니다:

```js
import torch
from huggingface_hub import login
from transformers import AutoModelForCausalLM, AutoTokenizer

device = torch.device("mps")
torch.set_default_device(device)

login(token="your-token")

title = "Mental Health Chatbot"
description = "This bot is using a fine-tuned version of meta-llama/Llama-2-7b-chat-hf"

model_id = "meta-llama/Llama-2-7b-chat-hf"
adapter_model_id = "vojay/Llama-2-7b-chat-hf-mental-health"

model = AutoModelForCausalLM.from_pretrained(model_id, torch_dtype=torch.float16)
model.load_adapter(adapter_model_id)
model.to(device)

tokenizer = AutoTokenizer.from_pretrained(model_id)
tokenizer.pad_token = tokenizer.eos_token
tokenizer.padding_side = "right"


def get_base_prompt():
    return """
    You are a knowledgeable and supportive psychologist. You provide emphatic, non-judgmental responses to users seeking
    emotional and psychological support. Provide a safe space for users to share and reflect, focus on empathy, active
    listening and understanding.
    """


def format_prompt(base, user_message):
    return f"<s>[INST] <<SYS>>{base}<</SYS>>{user_message} [/INST]"


def chat_with_llama(prompt):
    input_ids = tokenizer.encode(format_prompt(get_base_prompt(), prompt), return_tensors="pt")
    input_ids = input_ids.to(device)
    output = model.generate(
        input_ids,
        pad_token_id=tokenizer.eos_token_id,
        max_length=2000,
        temperature=0.9,
        top_k=50,
        top_p=0.9
    )

    decoded = tokenizer.decode(output[0], skip_special_tokens=True)
    return decoded.split("[/INST]")[1].lstrip()


while True:
    prompt = input("You: ")
    response = chat_with_llama(prompt)
    print(f"Llama: {response}")
```

이제 한번 해봅시다! 다음 챗 입력으로 실행해봅시다:

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
제 감정과 자신을 다루고 있는 중이에요. 잠을 거의 못 자고 불안과 스트레스로 고통받고 있어요. 약물 복용을 피하면서 대처 전략을 추천해주실 수 있을까요?
```

실제로 저희가 섬세하게 튜닝된 모델로 답변한 결과가 매우 좋았기 때문에, 이 답변을 여기 인용하고 싶습니다. 이 글에서 정신 건강에 관해 이야기할 때 이겁니다.

🤖 고지사항: 아래 텍스트는 AI가 생성한 것입니다.

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

네, 약을 복용하지 않고 스트레스와 불안을 관리하는 데 도움이 되는 여러 가지 방법이 있습니다.

- 자신을 돌봅시다. 충분히 잠을 자고 올바르게 먹고 운동합시다. 우리가 스스로를 돌보지 않으면 스트레스와 불안을 느끼기 쉬워집니다.
- 마음을 집중하는 법을 연습합시다. 이것은 자신을 비판하지 않고 순수하게 순간에 집중하는 것을 의미합니다. 마음을 집중하는 것은 우리가 과거를 놓아버리고 미래에 대해 걱정하지 않게 도와줄 수 있습니다.
- 명상을 연습합시다. 마음을 집중하는 명상은 숨을 집중하고 현재에 집중할 수 있도록 자신을 훈련시키는 방법입니다.
- 행복을 주는 것에 대해 생각해보세요. 감사할 만한 것들은 무엇인가요? 좋아하는 일은 무엇인가요?
- 조용한 시간을 가지고 당신의 생각을 기록해보세요. 생각과 감정을 쓰는 것은 스트레스와 불안을 관리하는 데 매우 도움이 될 수 있습니다.
- 요가, 타이치 또는 심호흡과 같은 형태의 안정 훈련을 연습합시다.
- 마사지를 받거나 매니큐어를 받는 등 자기 관리의 형태를 연습합시다.
- 대화할 상대를 찾아보세요. 때로는 누군가와 이야기하는 것이 다른 시각에서 상황을 볼 수 있도록 도와줄 수 있습니다.
- 상황에서 유머를 찾아보세요. 때로는 웃음소리가 기분을 끌어올리는 데 도움이 될 수 있습니다.
- 신체 운동의 형태를 연습해보세요. 운동은 더 편안하고 스트레스를 덜 느끼도록 도와줍니다.
- 집 밖을 산책하고 신선한 공기를 마시세요.
- 음악을 듣거나 책을 읽는 등 형태의 안정을 연습하세요.
- 현재 순간에 집중해보세요.

그게 다입니다, 우리만의 AI 기반 정신 건강 챗봇입니다. 이것으로, 당신에게 유익한 조언이 있을지도 모르니, 건강하게 지내시고 다음 챕터에서 어떻게 계속 진행할지 발견해 보세요.

![이미지](/assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_14.png)

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

## 해커톤 프로젝트 업그레이드하기: 정신 건강 챗봇을 위한 다음 스텝

![이미지](/assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_15.png)

우리가 살펴본 예는 더 발전할 수 있는 훌륭한 기반을 제공합니다. 해커톤 프로젝트를 위한 더 정교한 솔루션을 만드는 몇 가지 방법을 소개합니다:

- 파라미터 조율 프로세스 최적화: LoRA 파라미터와 훈련 구성을 다듬어 챗봇의 성능을 향상시킵니다. 이는 공감적이고 자연스러운 언어 생성으로 이어질 수 있습니다. 또한, Odds Ratio Preference Optimization (ORPO)와 같은 다른 접근 방식을 시도하여 성능을 향상시킬 수 있습니다.
- 기본 프롬프트 강화: 챗봇이 지지적이고 이해하는 방식으로 응답하도록 장려하는 기본 프롬프트를 만들어보세요. 필요한 경우 전문 도움을 찾도록 사용자를 안내할 수 있는 템플릿을 통합하세요.
- 검색 증강 생성(RAG) 구현: RAG를 통합하여 챗봇이 사용자 요청을 보강하는 추가적인 맥락을 제공합니다. 이를 통해 더 정보가 풍부하고 관련성 있는 응답을 할 수 있습니다.
- 간단한 채팅을 넘어 나아가기: 간단한 채팅을 넘어 구조화된 상호작용 모델을 구현하는 것을 고려해보세요. 아마도 인지 행동 요법(CBT) 원칙을 기반으로 한 것일 수도 있습니다. 이는 사용자에게 더 집중된 그리고 잠재적으로 치료적인 경험을 제공할 수 있습니다.
- 사용성에 집중: 모델 자체를 개선하는 데만 모든 시간과 노력을 집중하는 대신, 프런트엔드 중심적인 접근 방식 역시 채택하여 접근성에 집중하거나 창의적인 상호작용 형태를 사용하여 맞춤형 UI를 만들어 볼 수도 있습니다.

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

이런 개선 사항 외에도, 상자 밖을 생각하도록 장려합니다. 심리 건강 지원을 위한 AI 애플리케이션을 개발한다는 것은 가상 상담이나 치료용 챗봇을 만드는 것만을 의미하는 것이 아닙니다. 이 작업에 다른 접근 방법은 스트레스와 같은 정신 건강 문제를 유발하거나 강화하는 측면을 개선하는 방법을 고려하는 것일 수도 있습니다. 만약 우리가 문제를 재정의하여 AI 솔루션을 찾는다고 한다면, 스트레스를 줄이는데 도움이 되는 AI 중심의 개인 맞춤형 명상 지원과 같은 아이디어를 포함하여 초기 문제를 간접적으로 해결하는 많은 아이디어를 떠올릴 수 있습니다. 또 다른 방법은 외상 후 스트레스 장애 (PTSD)와 같은 특정 유형의 정신 건강 문제에 대한 지원에 집중하는 것일 수 있습니다.

적절히 대응된 특정 위험요인이 있다면, 많은 사람들에게 도움이 될 수 있는 기회가 많이 있습니다. 따라서, 2024년 정신 건강 AI 해커톤에서 어떤 눈을 떴다고 생각할만한 솔루션이 만들어질지 기대됩니다.

AI 중심의 애플리케이션 개발에 대한 몇 가지 영감을 드리기 위해:

Google Gemini LLM을 사용하여 FastAPI 기반의 진보된 API와 전용 Vue 기반 프론트엔드를 사용하고 싶다면, 이 글을 확인해 보세요:

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

테이블 태그를 Markdown 형식으로 변경하실 수 있습니다.

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

AI 기반 챗봇은 24시간 365일 제공되는 접근 가능한 정신 건강 지원으로 이 간극을 좁힐 수 있어요. 이 도구들은 사람들이 정신 건강 여정을 시작할 수 있는 안전한 공간을 제공하여, 필요할 때 가장 필요로 하는 때에 도움이 되는 듣는 귀, 유용한 자원 및 기본적인 대처 방법을 제공해줘요.

하지만, 이 분야에서 AI 챗봇의 잠재적인 제약, 윤리적 고려 사항 및 위험을 인지하고 대응하는 것이 중요해요. 인지 행동 요법 (CBT)과 저랭크 적응 (LoRA), 추출-증강 생성 (RAG)과 같은 기술적 해결책과 같이 주제별 접근 방식은 위험을 완화할 수 있지만, 더욱 논의되고 개선되어야 해요.

이러한 발전을 수용하고 윤리적 발전을 우선시함으로써, 이러한 응용 프로그램들은 정신 건강 의료를 민주화하는 강력한 도구로 거듭날 수 있어요. 정신 건강 AI 해커톤 2024는 혁신과 협력을 육성하여, 사용자 안전과 넓은 인구 집단을 위한 정신 건강 지원을 우선시하는 AI 솔루션을 만들기 위한 진보를 지원해요.

이 미래는 우리가 생각하는 것보다 가까워요. 책임 있는 개발과 윤리적 고려를 중시함으로써, 기술이 정신 건강 의료를 혁신하여, 모든 사람에게 매일 접근 가능하게 만들 수 있어요.

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

![이미지](/assets/img/2024-05-23-MindsandMachinesAIforMentalHealthSupportFine-TuningLLMswithLoRAinPractice_16.png)

특히 정신 건강 지원은 중요한 위험과 도전을 안고 있지만, 이러한 위험을 투명하게 만들고 공개적으로 논의함으로써 의미 있는 진전의 길을 열 수 있습니다. 여러분의 아이디어, 경험, 우려, 그리고 해결책에 대해 들어보고 싶습니다.
