---
title: "4가지 방법으로 플러터에서 널 체크  연산자를 피하는 방법"
description: ""
coverImage: "/assets/img/2024-06-19-4WaystoAvoidTheNullCheckOperatorOnFlutter_0.png"
date: 2024-06-19 11:24
ogImage: 
  url: /assets/img/2024-06-19-4WaystoAvoidTheNullCheckOperatorOnFlutter_0.png
tag: Tech
originalTitle: "4 Ways to Avoid The Null Check (!) Operator On Flutter"
link: "https://medium.com/@mario-gunawan/4-ways-to-avoid-the-null-check-operator-on-flutter-e2b8e7d965d"
---


```markdown
![4 Ways to Avoid the Null Check Operator on Flutter](/assets/img/2024-06-19-4WaystoAvoidTheNullCheckOperatorOnFlutter_0.png)

널 체크 연산자는 컴파일러에게, 널일 수 있는 변수가 실제로 널이 아님을 알려주는 연산자입니다. 이것은 변수 뒤에 느낌표를 붙여서 수행됩니다. 예를 들어, 아래 코드는 (!) 없이 실행시 오류를 발생시킵니다:

```js
String getString() {
  final String? nullableString = null;

  return nullableString!;
}
```

이는 기본적으로 "믿어봐, 안전해!"라고 컴파일러에 말하며, 코드의 오류를 평가하는 린터가(위의 예제에서는 널이 아닌 함수에서 널이 가능한 문자열을 반환하는 부분) 오류를 발생시키는 것을 방지합니다.
```

<div class="content-ad"></div>

테이블 태그를 마크다운 형식으로 변경해보세요.

<div class="content-ad"></div>

```markdown
![이미지](/assets/img/2024-06-19-4WaystoAvoidTheNullCheckOperatorOnFlutter_1.png)

문제에서 쉽게 벗어나는 방법입니다. 기본적으로 널 병합 연산자가 하는 일은 연산의 왼쪽 부분을 확인합니다. 만약 왼쪽 부분이 널이 아니라면 그 값을 사용하고, 그렇지 않다면 대신 오른쪽 부분을 사용합니다. 반환 값인 nullableString ?? “”은 다음과 같이 해석할 수 있습니다:

```js
if(nullableString != null) {
  return nullableString;
}
else {
  return "";
}
```

## 전략 2: 선택적 체이닝 (?.)과 널 병합 (??) 사용하기
```

<div class="content-ad"></div>

```markdown
![image](/assets/img/2024-06-19-4WaystoAvoidTheNullCheckOperatorOnFlutter_2.png)

옵셔널 체이닝 연산자는 오브젝트가 널일 수 있는 상황에서 유용합니다. nullableString?.contains("haha") ?? false 코드는 다음과 같이 해석할 수 있습니다:

```js
bool value = false;
if(nullableString != null) {
  value = nullableString.contains("haha");
}
```

또한 이 연산자를 사용하여 조건부로 작업을 호출할 수도 있습니다. 예를 들어 API에서 가져온 널 가능한 데이터 페쳐가 있다면 다음과 같이 호출할 수 있습니다:
```

<div class="content-ad"></div>

```js
dataFetcher?.getData();
```

대신에:

```js
dataFetcher != null ? dataFetcher.getData() : undefined;
```

## 전략 3: 삼항 연산자(?) 사용하기```

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-4WaystoAvoidTheNullCheckOperatorOnFlutter_3.png" />

만약 삼항 연산자에 대해 익숙하지 않다면, 다음과 같이 사용됩니다

```js
condition ? valueIfTrue : valueIfFalse
```

기본적으로 우리는 사용할 값을 결정하기 위해 미리 조건을 평가합니다. 그러나 긴 메소드에 대해 삼항 연산자를 사용하면 코드가 지저분해 보일 수 있으니 조심하세요, 예를 들어:

<div class="content-ad"></div>

```markdown
![image](/assets/img/2024-06-19-4WaystoAvoidTheNullCheckOperatorOnFlutter_4.png)

그래서 제 시니어로부터 받은 피드백을 기반으로, 긴 삼항 연산 대신 간단한 if-else 문을 사용하는 것이 더 나은 것 같아요. 그들의 규칙은 한 줄이라면 삼항 연산을 사용하는 것이라고 합니다. 저도 그것에 동의합니다.

## 전략 4: 배열에 조건 스프레딩(…?) 사용하기 

![image](/assets/img/2024-06-19-4WaystoAvoidTheNullCheckOperatorOnFlutter_5.png)
```

<div class="content-ad"></div>

오늘 이것을 배웠어요. 기본적으로, 우리는 조건부로 목록을 퍼뜨릴 수 있어요. 퍼뜨리기는 한 목록의 모든 값을 다른 목록으로 복사하는 거예요. 이건 배열을 결합하는 데 유용합니다. 위의 코드가 제안한 대로, 조합하는 데 유용한 방법이죠. 만약 배열이 널일 경우 문제가 생길 수 있는데, 그 때는 느낌표(!)를 사용하지 않으면 퍼뜨릴 수 없을 것 같아요. 다행히도 우리에게 조건부 퍼뜨리기는 있어요. 배열이 널이면 아무것도 전달되지 않아요.

코드 [...?item1, ...?item2]은 다음과 같습니다:
```js
final list = [];
list.add(item1);
list.add(item2);
``` 

또는, 멋있게 하고 싶다면:

<div class="content-ad"></div>

```js
[]..add(item1)..add(item2);
```

이상입니다. 읽어주셔서 감사합니다!