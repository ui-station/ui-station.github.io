---
title: "제18장 CompactMapValues 사용 방법 Swift"
description: ""
coverImage: "/assets/img/2024-06-23-Chapter18CompactMapValues_0.png"
date: 2024-06-23 21:28
ogImage: 
  url: /assets/img/2024-06-23-Chapter18CompactMapValues_0.png
tag: Tech
originalTitle: "Chapter 18: CompactMapValues"
link: "https://medium.com/@daviddoswell/chapter-18-compactmapvalues-67bd366d0281"
---


CompactMapValues 개요

![이미지](/assets/img/2024-06-23-Chapter18CompactMapValues_0.png)

compactMapValues 함수는 Swift에서 제공되는 고차 함수로, 사전(Dictionary)의 값을 변환하고 결과적으로 발생하는 nil 값을 제거합니다.

이 함수는 주어진 클로저를 사전의 각 값에 적용하고, nil이 아닌 결과만을 포함하는 새로운 사전을 반환합니다.

<div class="content-ad"></div>

이 함수는 사전(Dictionary)에서 데이터를 정리하고 변환할 때 특히 유용합니다. 여기서는 유효하지 않거나 누락된 값들을 걸러내면서 기존 값들을 변환하는 작업을 할 수 있습니다.

예시:

```js
let data: [String: String] = ["name": "Alice", "age": "25", "height": "five-five"]
let numericData = data.compactMapValues { Int($0) }
print(numericData) // 결과: ["age": 25]
```

설명:

<div class="content-ad"></div>

- let data: [String: String] = ["name": "Alice", "age": "25", "height": "five-five"] : 문자열 키와 값으로 구성된 딕셔너리를 정의합니다.
- let numericData = data.compactMapValues { Int($0) }: compactMapValues 함수를 사용하여 딕셔너리의 값을 변환하며, 각 값을 정수로 변환을 시도합니다.
- 변환에 실패하면 nil 값을 제거합니다.
- print(numericData): 결과 딕셔너리 ["age": 25]를 출력합니다.

실행 시간 복잡도: compactMapValues 함수의 시간 복잡도는 O(n)이며, 여기서 \( n \)은 딕셔너리의 요소 수입니다.

이 복잡성은 함수가 각 값에 대해 정확히 한 번 처리하기 때문에 발생합니다.

실제 사용 사례

<div class="content-ad"></div>

사용자 입력의 Dictionary를 정리하고 변환하기 위해 CompactMapValues를 사용하기:

```js
let userInputs: [String: String] = ["username": "david", "age": "twenty-five", "score": "42", "height": "170"]
let validData = userInputs.compactMapValues { Int($0) }
print(validData) // 출력은 ["score": 42, "height": 170] 입니다.
```

해설:

- let userInputs: [String: String] = ["username": "john_doe", "age": "twenty-five", "score": "42", "height": "170"]: string 키와 사용자 입력을 나타내는 값으로 이루어진 Dictionary를 정의합니다.
- let validData = userInputs.compactMapValues ' Int($0) ': compactMapValues 함수를 사용하여 Dictionary의 값을 변환하며 각 값을 정수로 변환을 시도합니다.
- 변환에 실패하면 nil 값이 제거됩니다.
- print(validData): 결과인 딕셔너리 ["score": 42, "height": 170]를 출력합니다.

<div class="content-ad"></div>

주석: compactMapValues 함수는 특히 사용자 입력이나 외부 소스에서 가져온 데이터와 같이 유효하지 않거나 숫자가 아닌 값이 포함될 수 있는 경우에 딕셔너리를 변환하고 정리하는 데 특히 유용합니다.

compactMapValues를 사용하면 기존 값의 변환과 함께 유효하지 않은 항목을 걸러내어 단일하고 표현력 있는 문장으로 처리할 수 있습니다.

결론

compactMapValues 함수는 Swift에서 강력한 고차 함수로, 딕셔너리의 값들을 변환하고 정리하여 nil 결과를 제거할 수 있습니다.

<div class="content-ad"></div>

시간 복잡도가 O(n)인 이것은 데이터 클리닝 및 변환 작업을 효율적으로 수행하는 방법을 제공합니다.

이것은 예정된 무료 eBook 중 일부이며, 1분 만에 스위프트 데이터 구조 및 알고리즘 마스터하기를 시작하세요. Beginner Swift에서 구독하세요.