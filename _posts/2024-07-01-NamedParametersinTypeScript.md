---
title: "TypeScript에서 Named Parameters 사용하는 방법"
description: ""
coverImage: "/assets/img/2024-07-01-NamedParametersinTypeScript_0.png"
date: 2024-07-01 16:22
ogImage: 
  url: /assets/img/2024-07-01-NamedParametersinTypeScript_0.png
tag: Tech
originalTitle: "Named Parameters in TypeScript"
link: "https://medium.com/@fab1o/named-parameters-in-typescript-288d90f35639"
---


## TypeScript에서 이름이 지정된 매개변수를 정의하는 최선의 방법은 무엇인가요?

이름이 지정된 매개변수로도 알려져 있습니다. 이 글에서는 TypeScript에서 이름이 지정된 매개변수를 정의하는 좋은 방법에 대해 설명하겠습니다.

<img src="/assets/img/2024-07-01-NamedParametersinTypeScript_0.png" />

## 약간의 배경 정보

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

특정 기능에 대한 매개변수로 사용되는 객체를 만드는 것을 명명된 매개변수 또는 명명된 인수라고 합니다.

일반적으로 매개변수는 함수 매개변수를 의미하고, 인수는 매개변수에 전달되는 값을 의미합니다. 그러나 저는 두 용어를 서로 바꿔 사용할 것입니다.

명명된 인수는 C#, Kotlin, Swift 등 많은 언어의 기본 기능이지만 Javascript/Typescript에는 해당되지 않습니다. 이것은 오랜 시간 동안 제안되어 왔으며 babel에 플러그인이 있습니다.

Javascript/Typescript에서 명명된 인수를 사용하는 방법은 객체를 정의하는 것입니다. 이것은 관행(회피책)입니다.

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

이름이 지정된 매개변수 객체는 프로젝트에서 신경 써야 할 "실제" 유형 또는 인터페이스가 아닙니다. 그냥 함수의 매개변수에 존재하는 이름이 지정된 인수의 관습으로 살아 있습니다.

제가 그렇게 말하는 이유는 이 객체를 이름이 지정된 매개변수로 생각하면 프로젝트에서의 위치가 더 명확해지기 때문입니다.

여기 C#에서의 이름이 지정된 인수 (이는 TS에서 영감을 받은 언어입니다):

```js
void PrintOrderDetails(
    string sellerName,
    int orderNum,
    string productName
) {
// ...
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

그리고 이를 어떻게 호출할 수 있는지:

```js
PrintOrderDetails(
    orderNum: 31,
    productName: "Red Mug",
    sellerName: "Gift Shop"
);
```

이것이 TypeScript에서 지원되면 어떻게 작성할지에 대한 예시입니다.

인자의 순서가 중요하지 않다는 점을 주목해주세요. 이것이 명명된 인자의 강점입니다. 인자를 어떤 순서로든 전달할 수 있고 코드를 읽기 쉽게 만들어줍니다 (어떤 값이 어디로 전달되는지 알 수 있음).

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

# TypeScript에서의 최상의 실천 방법은 무엇인가요?

배경 컨텍스트를 고려하면, 다음이 최상의 실천 방법입니다:

```js
function printOrderDetails(options: {
    sellerName: string,
    orderNum: number,
    productName: string
}): void {
// ...
}

printOrderDetails({
    orderNum: 31,
    productName: "Red Mug",
    sellerName: "Gift Shop"
});
```

이것은 네이티브 네임드 인수와 매우 유사합니다. 이 객체에 대해 타입이나 인터페이스를 만들지 않습니다. 왜냐하면 중요한 인터페이스나 타입이 아니기 때문입니다. 이것은 그냥 네임드 인수입니다. 함수를 호출할 때 다른 목적에 사용되지 않으며 우리가 신경 쓰지 않습니다. 즉, 이것은 필요하지 않으며 오히려 번거로울 수 있습니다.

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
const options = {
    orderNum: 31,
    productName: "Red Mug",
    sellerName: "Gift Shop"
};

PrintOrderDetails(options);
```

우리는 FunctionOptions를 선언하지 않습니다. 왜냐하면 해당 내용을 내보내지 않거나 정의하지 않기 때문입니다. 우리는 신경 쓰지 않습니다.

## 함수 시그니처에서 파괴 할당을 어떻게 생각하시나요?

이것은 별개의 주제입니다. 그리고 실천법은 팀마다 다를 수 있습니다. 예를 들어, React 프로젝트에서는 함수 시그니처에서 파괴 할당하는 것이 일반적이며, 내가 본 대부분의 프로젝트에서 이것이 표준이라고 볼 수 있습니다.

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

내 제안은 팀에 가장 적합한 것을 확인하는 것이 좋습니다. 무엇이 더 읽기 쉬운지 확인해보세요. 저는 함수 시그니처를 해체하지 않는 것을 선호하지만, 리액트 프로젝트를 제외하고는 다른 팀원들이 다른 견해를 가질 수도 있습니다.

그래서 합의와 일관성이 중요합니다.

```js
export function promiseExperience(options: {
    experienceName,
    experienceConfig,
    experienceDiv,
    siteId,
    traversal
}): Promise<BuildComponent> {
    const {
        experienceName,
        experienceConfig,
        experienceDiv,
        siteId,
        traversal
    } = options;
// ...
}
```

합의는 객체에 대한 타입을 만드는 것을 의미할 수도 있습니다:

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
type PromiseExperienceParams = {
    experienceName: string;
    experienceConfig: TodoAny;
    experienceDiv: HTMLDivElement;
    siteId: string;
    traversal: Traversal;
};

export function promiseExperience(options: PromiseExperienceParams): Promise<BuildComponent> {
    const {
        experienceName,
        experienceConfig,
        experienceDiv,
        siteId,
        traversal
    } = options;
// ...
}
```

하지만 타입을 생성할 때는 내보내지 말고, 함수의 인자로 사용하고 함수의 매개변수로 사용하지 마세요.

# 일관성

모든 프로젝트에서 일관성이 중요합니다.


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

일관성이 코드를 읽기 쉽게 만듭니다. 이것이 표준이 됩니다. 프로젝트가 함수 시그니처로 분해된다면, 그것을 따르지 않으면 불일치할 것이고, 그 반대도 마찬가지입니다. 표준적인 방법에서 벗어나는 것은 피하고 싶지 않나요?

이것이 여러분과 팀이 성공하는 데 도움이 되기를 바랍니다.