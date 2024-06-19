---
title: "구조화된 복제 JavaScript에서 객체를 깊은 복사하는 가장 쉬운 방법"
description: ""
coverImage: "/assets/img/2024-06-19-structuredCloneTheeasiestwaytodeepcopyobjectsinJavaScript_0.png"
date: 2024-06-19 11:33
ogImage: 
  url: /assets/img/2024-06-19-structuredCloneTheeasiestwaytodeepcopyobjectsinJavaScript_0.png
tag: Tech
originalTitle: "structuredClone(): The easiest way to deep copy objects in JavaScript"
link: "https://medium.com/coding-beauty/structuredclone-js-c2180bf7900a"
---


깊은 복사는 데이터를 전달하거나 저장하기 위한 정기적인 프로그래밍 작업입니다.

- Shallow copy: 객체의 첫 번째 수준만 복사합니다.
- Deep copy: 객체의 모든 수준을 복사합니다.

<div class="content-ad"></div>

하지만 이 시간 동안 완벽한 객체 깊은 복사를 위한 내장 방법이 없었고, 신경 써야 했어요.

우리는 항상 서드파티 라이브러리를 의존하여 깊은 복사와 순환 참조를 유지해야 했어요.

지금은 새로운 structuredClone()을 통해 모든 객체를 깊은 복사할 수 있는 쉽고 효율적인 방법이 생겼어요.

![image](/assets/img/2024-06-19-structuredCloneTheeasiestwaytodeepcopyobjectsinJavaScript_2.png)

<div class="content-ad"></div>

쉽게 순환 참조를 복제하고 있어요:

![이미지](/assets/img/2024-06-19-structuredCloneTheeasiestwaytodeepcopyobjectsinJavaScript_3.png)

JSON stringify/parse 트릭으로는 할 수 없었던 작업이예요:

![이미지](/assets/img/2024-06-19-structuredCloneTheeasiestwaytodeepcopyobjectsinJavaScript_4.png)

<div class="content-ad"></div>

깊게 들어가 보세요:

![image](/assets/img/2024-06-19-structuredCloneTheeasiestwaytodeepcopyobjectsinJavaScript_5.png)

## 알아야 할 제한 사항

structuredClone()은 매우 강력하지만 알아야 할 중요한 약점이 있습니다:

<div class="content-ad"></div>

# 함수나 메소드를 복제할 수 없어요

![이미지](/assets/img/2024-06-19-structuredCloneTheeasiestwaytodeepcopyobjectsinJavaScript_6.png)

사용하는 특별한 알고리즘 덕분이에요.

![이미지](/assets/img/2024-06-19-structuredCloneTheeasiestwaytodeepcopyobjectsinJavaScript_7.png)

<div class="content-ad"></div>

# DOM 요소를 클론할 수 없어요

![이미지1](/assets/img/2024-06-19-structuredCloneTheeasiestwaytodeepcopyobjectsinJavaScript_8.png)

![이미지2](/assets/img/2024-06-19-structuredCloneTheeasiestwaytodeepcopyobjectsinJavaScript_9.png)

![이미지3](/assets/img/2024-06-19-structuredCloneTheeasiestwaytodeepcopyobjectsinJavaScript_10.png)

<div class="content-ad"></div>

# RegExp의 lastIndex 속성을 보존하지 않음

말 그대로, 누가 정규식을 복제하겠어요. 하지만 하나 주목할 점은:

![이미지](/assets/img/2024-06-19-structuredCloneTheeasiestwaytodeepcopyobjectsinJavaScript_11.png)

# 다른 제한 사항

<div class="content-ad"></div>

함수를 사용할 때 예기치 않은 동작을 피하기 위해 그것들을 인지하는 것이 중요합니다.

## 일부 복제하고 이동하기

이것은 좀 더 복잡한 것입니다.

원본에서 클론으로 내부 객체를 복사하는 대신 이동시킵니다.

<div class="content-ad"></div>

해석 결과에 더 이상 수정할 내용이 없습니다. 해당 내용은 다음과 같습니다:

```markdown
![image](/assets/img/2024-06-19-structuredCloneTheeasiestwaytodeepcopyobjectsinJavaScript_12.png)

structuredClone()는 JavaScript 개발자의 도구상자에 속한 귀중한 요소로, 객체 복제를 이전보다 쉽게 만들어줍니다.
```