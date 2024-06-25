---
title: "제이미니 API 사용 전에 읽어보세요"
description: ""
coverImage: "/assets/img/2024-05-23-ReadthisBeforeUsingGeminiAPI_0.png"
date: 2024-05-23 13:15
ogImage:
  url: /assets/img/2024-05-23-ReadthisBeforeUsingGeminiAPI_0.png
tag: Tech
originalTitle: "Read this Before Using Gemini API"
link: "https://medium.com/@jessicajimantoro/read-this-before-using-gemini-api-4933becad899"
---

## Gemini API를 최대한 활용할 수 있도록 도와주는 개발자 안내서

안녕하세요 👋🏻

앱에 Gemini API를 사용하고 싶으신가요? Gemini AI 매개변수를 효과적으로 활용하는 방법은 무엇인가요? Gemini AI를 사용할 때 주의해야 할 점은 무엇일까요?

![이미지](/assets/img/2024-05-23-ReadthisBeforeUsingGeminiAPI_0.png)

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

Gemini AI를 처음 사용하시는 분이라면, 이는 텍스트, 이미지 및 오디오 등 다양한 유형의 데이터 입력에서 콘텐츠를 생성할 수 있는 모델로 정의되는 생성적 AI입니다.

## Gemini API를 사용해야 하는 이유

아마도 몇몇 분들은 "Gemini 앱이 있는데, 왜 Gemini API를 사용해야 하죠?" 라고 물을지도 모릅니다.

Gemini API는 AI 기반 응용 프로그램을 만들고 싶은 분들을 대상으로 하고 있습니다. 게다가 유료 버전에서는 귀하의 프롬프트 및 응답을 제품 개선에 사용하지 않습니다.

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

# 토큰

생성적 AI 모델은 텍스트 데이터를 처리하기 위해 토큰이라는 단위로 분해합니다. 토큰은 문자, 단어 또는 구(phrase)가 될 수 있습니다. 이는 Gemini가 어떻게 단어를 분해하는지에 따라 다릅니다.

# Top-K, Top-P 및 Temperature

## topK

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

topK 매개변수는 모델이 출력 토큰을 선택하는 방식을 변경합니다. 간단히 말해, 가능한 출력 토큰을 K개로 제한합니다.

예시:
다음 단어를 완성해야 합니다:
The quick brown fox jumps over the …

다음 단어의 확률:

1. squirrel: 0.08
2. sleeping rabbit: 0.3
3. lazy dog: 0.9
4. dog: 0.7

예시에서...

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

만약 topK를 1로 설정하면 (탐욕 디코딩), 출력 결과는 가장 높은 확률을 가진 단어만 표시됩니다. 즉, "게으른 개"가 됩니다.

만약 topK를 N(N ≥ 1)으로 설정하면, 출력 결과는 가장 높은 확률을 가진 N개의 단어 중 하나가 나오게 됩니다. 예를 들어, "게으른 개", "개", "자는 토끼"가 있을 때, 이 중에서도 topP에 따라 단어가 더 필터링되고, 최종 출력은 온도를 이용하여 선택됩니다.

## topP

topP 매개변수, 또한 넉클리어 샘플링이라고 불리는 것은 모델이 출력 토큰을 선택하는 방식을 변경합니다. 간단히 말해, 출력 토큰의 확률 합이 topP 값과 동일하거나 그 이상인 경우까지만 토큰을 생성하는 방식입니다. topP 값은 0.0에서 1.0 사이의 범위로 설정할 수 있습니다.

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

예시:
다음 단어를 완성해야 합니다:
수영은 매우…

다음 단어의 확률:

1. 인기 있는: 0.3
2. 치유적인: 0.1
3. 건강한: 0.4
4. 어려운: 0.2

이때 topP는 0.5로 설정합니다.

가장 높은 확률을 가진 단어는 "건강한" (0.4)이지만, 임계 값을 충족하지 못하므로 두 번째로 높은 "인기 있는" (0.3)으로 이동합니다. 따라서 출력은 "건강한" (0.4)과 "인기 있는" (0.3)입니다.

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

## 온도

온도는 topP 및 topK가 적용된 이후의 토큰 선택의 무작위성을 제어합니다. 또한 0.0에서 1.0 범위 내에서 온도 값을 설정할 수 있습니다. 우리는 여기서 식을 포함하지 않을 것입니다.

알아두어야 할 것은 높은 온도와 낮은 온도를 언제 사용해야 하는지입니다.

- 낮은 온도 (≤ 0.5)는 더 구체적이거나 덜 창의적인 출력이 필요할 때 적합합니다. 사용 사례 예시로는 요약, 질문 응답, 팩트-체킹, 번역 등이 있습니다.
- 높은 온도 (≥ 0.5)는 더 창의적인 출력이 필요할 때 적합합니다. 사용 사례 예시로는 이야기 작성, 퀴즈 생성 등이 있습니다.

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

# 가격 책정

가격은 입력 및 출력 토큰에 따라 다르므로 비용을 최적화하기 위해 콘텐츠 생성 전에 입력 및 출력 토큰을 제한할 수 있습니다.

다트(Dart)에서의 예시는 다음과 같습니다:

```js
Future<void> main() async {
  final GenerativeModel model = GenerativeModel(
    model: 'gemini-1.5-pro-latest',
    apiKey: 'YOUR_API_KEY',
    generationConfig: GenerationConfig(
      responseMimeType: 'application/json',
      maxOutputTokens: 150,
    ),
  );

  final token = await model.countTokens(
    [Content.text('Hello World')],
  );
  print(token.totalTokens);
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

# JSON 생성하기

가끔 앱에서 JSON을 생성해야 할 때가 있습니다. 최신 Gemini 1.5 Pro에서는 이미 JSON 출력을 지원합니다.

Dart에서 GenerationConfig 내부에 responseMimeType: 'application/json' 속성을 추가할 수 있습니다.

Gemini 1.0 Pro를 사용하지 않는 경우에는 프롬프트할 때 출력을 지정할 수 있습니다. ' '예제': '문자열' ' 이런 JSON 구조로 ...를 생성해줘 라고 간단히 요청하면 됩니다.

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

여기 다트(Dart)에서의 예시가 있어요:

```dart
void main() {
  final jsonString = jsonEncode({'story': 'string'});
  final content = await model.generateContent([
    Content.text(
      'Generate me a story with this JSON structure: $jsonString',
    ),
  ]);
  print(content.text);
}
```

# 시스템 지침

시스템 지침은 사용자가 특정 요구 사항과 사용 사례에 따라 모델의 동작을 조정할 수 있도록 합니다. AI 역할, 언어 스타일 또는 출력을 지정할 수 있어요.

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

다트 코드 예시를 보여드립니다:

```dart
void main() {
  final GenerativeModel model = GenerativeModel(
    model: 'gemini-1.5-pro-latest',
    apiKey: 'YOUR_API_KEY',
    systemInstruction: Content.system(
      '''You are a horror story teller.
      You will receive a title, then turn it into a short horror story
      and respond it to the JSON object contains title and story''',
    ),
  );
}
```

# 보안

클라이언트 측에서 직접 Gemini API를 호출하는 것은 API 키 유출, 서비스 거부(DoS) 공격 등과 같은 위협에 노출될 수 있습니다. 클라이언트 측은 프로토타입에만 사용해야 합니다. 가장 안전한 방법은 서버 측에서 구현하는 것입니다.

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

모두 끝났어요!

아래에 댓글을 남기시고 망고가 Linkedin에서 우릴 이어도록 합시다!

독해주셔서 감사합니다! 🐈
