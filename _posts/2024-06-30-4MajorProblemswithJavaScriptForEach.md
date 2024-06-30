---
title: "JavaScript ForEach 사용 시 겪는 4가지 주요 문제점"
description: ""
coverImage: "/assets/img/2024-06-30-4MajorProblemswithJavaScriptForEach_0.png"
date: 2024-06-30 22:32
ogImage: 
  url: /assets/img/2024-06-30-4MajorProblemswithJavaScriptForEach_0.png
tag: Tech
originalTitle: "4 Major Problems with JavaScript ForEach"
link: "https://medium.com/gitconnected/4-major-problems-with-javascript-foreach-b79f717c61b8"
---


<img src="/assets/img/2024-06-30-4MajorProblemswithJavaScriptForEach_0.png" />

자바스크립트 열정가인 여러분은 이미 forEach 루프를 사용해 본 경험이 있을 것입니다.

그러나 해당 기능을 사용하기 전에 해결해야 할 주요 문제 4가지를 알고 계셔야 합니다.

오늘은 각 문제를 군사 테마의 예시를 통해 살펴보겠습니다.

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

그래서 더 이상 미루지 말고... 바로 시작해 봅시다!

# 1. 중단 또는 계속할 수 없음

시작하기 전에 이 JavaScript 프로그램을 살펴보겠습니다:

```js
const soliders: string[] = ["John", "Daniel", "Cole", "Adam"]

soliders.forEach((soldier, index) => {
  soliders[index] = "Captain " + soldier
})

// [ "Captain John", "Captain Daniel", "Captain Cole", "Captain Adam" ]
console.log(soliders)
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

우리의 시나리오는 간단해요:

- 우리는 병사들의 배열을 가지고 있어요.
- 각 병사마다, "Captain"이라는 말을 이름 앞에 추가하여 승진시킵니다.

하지만 "Daniel"을 대장으로 승진시키고 싶지 않다면 어떻게 해야 할까요?

해당 반복을 건너뛰기 위해 continue 키워드를 사용하고 싶을 수 있어요.

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
const soliders: string[] = ["John", "Daniel", "Cole", "Adam"]

soliders.forEach((soldier, index) => {
  if (soldier === "Daniel") {
    continue
  }
  soliders[index] = "Captain " + soldier
})

// [ "Captain John", "Captain Daniel", "Captain Cole", "Captain Adam" ]
console.log(soliders)
```

하지만 이것은 작동하지 않고 구문 오류가 발생합니다.

forEach 루프의 흐름은 중단할 수 없습니다. 따라서 이 문제를 해결하는 유일한 방법은 조건문을 사용하는 것입니다:

```js
soliders.forEach((soldier, index) => {
  // 루프는 항상 실행되며, 순회를 건너뛸 수 없습니다.
  if (soldier !== "Daniel") {
    soliders[index] = "Captain " + soldier
  }
})

// [ "Captain John", "Daniel", "Captain Cole", "Captain Adam" ]
console.log(soliders)
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

만약 반복을 중단/계속하고 싶다면, "for i" 루프 클래스나 "for of" 루프 문을 사용하는 편이 좋습니다.

# 2. 비동기 실행

병사 진급이 동기적일 때는 진급 순서가 존에서 아담으로 오름차순으로 진행됩니다.

이제 비동기 함수가 있다고 가정해 봅시다.

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

`forEach` 루프는 비동기 함수의 완료를 기다리지 않기 때문에 예상치 못한 출력 순서가 발생할 수 있습니다.

각 반복에 대해 임의의 지연 시간을 설정하여 비동기 함수를 시뮬레이션해 보겠습니다:

```js
const soliders = ["John", "Daniel", "Cole", "Adam"]

soliders.forEach((soldier, index) => {
  setTimeout(() => {
    soliders[index] = "Captain " + soldier
    console.log(soliders)
  }, Math.random() * 1000) // 최대 1초까지의 임의의 지연을 시뮬레이션합니다.
})
```

프로그램을 두 번 실행한 후의 출력 결과는 다음과 같습니다:

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

<img src="/assets/img/2024-06-30-4MajorProblemswithJavaScriptForEach_1.png" />

# 3. 배열 수정 불가능

이 문제는 간단히 보이지만, 사실 배열 내 요소를 변경하는 것은 허용됩니다. 그러나 이는 일반적으로 좋지 않은 방식으로 간주됩니다.

좋지 않은 방식이라고 하는 이유는 forEach 루프가 이 용도로 설계되지 않았기 때문에, 데이터를 과도하게 처리하거나 요소를 건너뛸 수 있기 때문입니다.

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

이것이 forEach 루프 중 첫 번째 병사 "John"을 제거하는 예입니다:

```js
soliders.forEach((soldier, index) => {
  if (soldier === "John") {
    soliders.splice(index, 1)
  } else {
    soliders[index] = "Captain " + soldier
  }
})

// [ "Daniel", "Captain Cole", "Captain Adam" ]
console.log(soliders)
```

이제 결과가 혼란스러울 수 있습니다. "John"이 삭제되었기 때문에 "Daniel"이 건너뜁니다.

이것은 splice() 함수를 사용한 후 배열이 왼쪽으로 이동되어 "Daniel"이 인덱스 1에서 0으로 이동하여 건너뛰어진 결과입니다.

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

# 4. 우선 예외 처리

for나 while 루프와 같은 전통적인 루핑 구조와 달리 forEach에는 내장된 예외 처리 기능이 없습니다.

다시 말해, forEach 내부에서 오류가 발생한다면 해당 오류는 루프 자체에서 catch되지 않으며, 이는 콜백 내에서 명시적으로 예외를 처리해야 한다는 것을 의미합니다.

# 결론

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

오늘은 forEach 루프와 관련된 4가지 주요 문제를 리뷰했어요.

각 문제를 군인 진급의 예시를 통해 시각화했어요.

forEach 루프를 사용할 때는 조심해야 해요. 예기치 않은 오류가 발생할 수 있고 디버그하기 어려울 수 있어요.

# 제휴사

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

- 올인원 SaaS 프로젝트 템플릿
- Figma 홈: 내 모든 프로젝트에서 사용하는 UI 디자인 도구입니다.
- Figma 프로페셔널: 당신이 필요로 할 유일한 UI 디자인 도구입니다.
- FigJam: 직관적인 다이어그램 작성과 브레인스토밍으로 상상력을 발휘하세요.
- Notion: 내 인생 전반을 손쉽게 정리하는 데 사용되는 도구입니다.
- Notion AI: ChatGPT를 능가하며 Notion 작업 흐름을 빠르게 만들어줄 AI 도구입니다.

# 참고 자료

- Foreach W3 Schools