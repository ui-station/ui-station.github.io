---
title: "Java에서 문자열 마스터하는 방법"
description: ""
coverImage: "/assets/img/2024-06-22-MasteringStringsinJava_0.png"
date: 2024-06-22 22:01
ogImage: 
  url: /assets/img/2024-06-22-MasteringStringsinJava_0.png
tag: Tech
originalTitle: "Mastering Strings in Java"
link: "https://medium.com/@keerthanaj0603/mastering-strings-in-java-c62627e86bfe"
---


문자열 클래스

자바의 문자열 클래스는 java.lang 패키지의 일부이며, 문자 시퀀스를 생성하고 조작하는 데 사용됩니다. 문자열 클래스는 final로 표시되어 있습니다. 아무도 문자열 메서드 중 하나의 동작을 재정의할 수 없기 때문에 Java의 문자열은 변경할 수 없습니다.

새 문자열 생성

이 경우에는 "Java"가 문자열 상수 풀(String Constant Pool, SCP)에 배치되고 s가 그것을 가리킵니다.

<div class="content-ad"></div>

**String Constant Pool in Java**

Java의 문자열 상수 풀(String Constant Pool)은 JVM이 사용하는 힙 메모리의 일부로, 문자열 상수를 저장하는 데 사용됩니다. 문자열 풀의 주요 목적은 기존의 문자열 인스턴스를 재사용하여 메모리를 절약하는 것입니다.

2) `String s = new String("Vamika");` // 이 코드는 두 개의 문자열 객체와 하나의 참조 변수를 생성합니다.

<div class="content-ad"></div>

이 경우에는 새 키워드를 사용하므로 heap 영역에 새로운 String 객체가 생성되고 s가 해당 객체를 가리키게 됩니다. 게다가 리터럴 "Vamika"는 SCP에 배치됩니다. 단, 해당 객체가 pool 영역에 존재하지 않는 경우에 한합니다. 이미 동일한 객체가 존재하는 경우, 기존 객체가 재사용됩니다 (기존 String은 추가 참조를 갖게 됩니다).

참고: 이 규칙은 heap에는 적용되지 않습니다. 따라서 heap 영역에는 중복 콘텐츠가 있을 수 있지만, pool 영역에는 중복이 허용되지 않습니다.

![Mastering Strings in Java](/assets/img/2024-06-22-MasteringStringsinJava_1.png)

문자열은 변경할 수 없습니다.

<div class="content-ad"></div>

우리는 모두 한 번 String 객체가 생성되면 그것을 변경할 수 없다고 들어봤어요 — 그렇다면 우리가 그것을 변경하려고 시도하면 어떻게 될까요? 알아보겠습니다.

한 번 String에 값을 할당하면, 그 값은 변경될 수 없습니다 — 즉, 불변성이라는 것이에요.

예를 들면,

String s="Java"; //이 코드는 값이 "Java"인 String 객체를 생성하고, s가 그것을 참조합니다

<div class="content-ad"></div>

s.concat(" Programming"); // JVM은 "Java Programming"이라는 두 번째 문자열 객체를 생성하지만 어떤 변수도 이를 참조하지 않습니다. 따라서 이 객체는 즉시 손실됩니다.

![Mastering Strings in Java](/assets/img/2024-06-22-MasteringStringsinJava_2.png)

다른 예제를 살펴봅시다.

s = s.concat(" Learning"); // JVM은 "Java Learning"이라는 또 다른 문자열 객체를 생성하고 변수 s가 이를 참조합니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-22-MasteringStringsinJava_3.png" />

알아보셨나요? 마지막 단계에서 새로운 String 객체 "Java Learning"이 생성되고 s가 이를 참조하며 이전 객체 "Java"는 버려집니다.

한 번 String 객체를 생성하면 기존의 String 객체에서 어떠한 작업도 수행할 수 없습니다. 만약 작업을 수행하면 해당 변경 사항이 있는 새로운 String 객체가 생성됩니다. 이러한 String의 변경할 수 없는 특성을 문자열의 불변성으로 알려져 있습니다.

## String 객체들 간의 비교

<div class="content-ad"></div>

Case 1:

`![image](/assets/img/2024-06-22-MasteringStringsinJava_4.png)`

`![image](/assets/img/2024-06-22-MasteringStringsinJava_5.png)`

Here, two different String objects are created in the heap memory. Each new String(“Vamika”) creates a new instance of a String object, even though they contain the same sequence of characters.

<div class="content-ad"></div>

'==' 연산자를 사용한 비교:
'==' 연산자는 's1'과 's2'가 메모리에서 동일한 객체를 참조하는지를 확인합니다. 's1'과 's2'는 new 키워드를 사용하여 생성된 두 개의 별개 객체이기 때문에 이 비교는 false를 반환합니다.
equals() 메서드를 사용한 비교:
'equals()' 메서드는 's1'과 's2'의 값이 동일한지를 확인합니다. 's1'과 's2' 모두 동일한 문자열 ("Vamika") 시퀀스를 포함하고 있기 때문에 이 비교는 true를 반환합니다.

Case 2:

<img src="/assets/img/2024-06-22-MasteringStringsinJava_6.png" />

Case 3:

<div class="content-ad"></div>

모든 String 상수마다 하나의 객체가 SCP 영역에 배치됩니다. 또는 실행 시 연산으로 인해 생성되는 객체는 힙 영역에만 배치됩니다.

예시:

![image](/assets/img/2024-06-22-MasteringStringsinJava_7.png)

## String 클래스의 중요한 메소드

<div class="content-ad"></div>

- charAt(int index) — 지정된 인덱스에 위치한 문자 값을 반환합니다
- concat(String s) — 다른 문자열을 하나의 끝에 추가합니다 (“+”도 작동합니다)
- equals(Object o) — 내용을 비교하며, 대소문자도 같아야 함
- equalsIgnoreCase(String s) — 내용을 비교하며, 대소문자를 무시합니다
- length() — 문자열의 문자 수를 반환합니다
- replace(char old, char new) — 문자의 발생을 새로운 문자로 바꿉니다
- substring(int startIndex) — 문자열의 시작 인덱스부터 끝까지의 부분 문자열을 반환합니다
- substring(int startIndex) — 문자열의 시작 인덱스부터 끝-1 인덱스까지의 부분 문자열을 반환합니다
- toLowerCase() — 대문자를 소문자로 변환한 문자열을 반환합니다
- toUpperCase() — 소문자를 대문자로 변환한 문자열을 반환합니다
- trim() — 문자열의 양쪽 공백을 제거합니다
- intern() — 힙 메모리에 있는 문자열의 정확한 사본을 생성하여 문자열 상수 풀에 저장합니다
- indexOf(char ch) — 지정된 문자의 첫 번째 발생 위치를 반환합니다
- lastIndexOf(char ch) — 지정된 문자의 마지막 발생 위치를 반환합니다

![이미지](/assets/img/2024-06-22-MasteringStringsinJava_8.png)