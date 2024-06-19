---
title: "자바 어려운 면접 질문"
description: ""
coverImage: "/assets/img/2024-06-19-Javatrickyinterviewquestions_0.png"
date: 2024-06-19 22:01
ogImage: 
  url: /assets/img/2024-06-19-Javatrickyinterviewquestions_0.png
tag: Tech
originalTitle: "Java tricky interview questions"
link: "https://medium.com/@abhishek.talakeriv/java-tricky-interview-questions-fe3aa2b71c0b"
---


```markdown
![이미지](/assets/img/2024-06-19-Javatrickyinterviewquestions_0.png)

안녕하세요! 오늘은 핵심 Java에 관련된 어려운 인터뷰 질문을 살펴보겠습니다. 이 자료는 면접 준비 중인 개인들을 돕기 위해 특별히 설계되었습니다. 기술 면접을 해결하거나 이해력을 향상시키려는 경우, 이 시리즈는 성공으로 향하는 여정에서 귀하에게 가치 있는 자산이 되도록 목표하고 있습니다.

- 다음 코드 스니펫의 출력은 무엇입니까?

```java
public static void main(String[] args) {

    int a = 5;
    int b = 10;
    System.out.println(a++ + ++b);

}
```
```

<div class="content-ad"></div>

질문: 16

2. 이 코드 실행의 결과는 무엇입니까?

```java
public static void main(String[] args) {

  String str = null;
  if (str instanceof String) {
   System.out.println("True");
  } else {
   System.out.println("False");
  }

 }
```

답변: False

<div class="content-ad"></div>

3. 다음 코드의 출력은 무엇입니까?

```java
public static void main(String[] args) {

  int x = 10;
  int y = (x > 5) ? (x < 10 ? 1 : 2) : 3;
  System.out.println(y);

}
```

답: 2

4. 다음 코드의 출력은 무엇입니까?

<div class="content-ad"></div>

```js
int a = 10;
System.out.println(a += (a = 5) * (a / 5));
```

답: 15

5. 이 코드의 결과는 무엇입니까?

```js
public static void main(String[] args) {

  String s1 = "Java";
  String s2 = "Ja" + "va";
  System.out.println(s1 == s2);

 }
```

<div class="content-ad"></div>

답변: True

6. 출력 결과는 무엇입니까?

```java
 public static void main(String[] args) {

  int[] arr = new int[5];
  System.out.println(arr[0]);

 }
 ```

답변: 0

<div class="content-ad"></div>

7. 아래 코드의 출력은 무엇입니까?

```java
public static void main(String[] args) {

  int i = 0;
  int j = i++ + ++i;
  System.out.println(j);

}
```

답: 2

8. 다음 코드는 무엇을 출력합니까?

<div class="content-ad"></div>

```java
```js
public static void main(String[] args) {

  System.out.println("Hello" == new String("Hello"));

 }
```

답변: False

9. 아래 코드의 결과는 무엇입니까?

```java
int a = 5;
int b = 10;
a ^= b ^= a ^= b;
System.out.println(a + "-" + b);
```

<div class="content-ad"></div>

0-5 사이의 답변입니다.

10. 이 코드는 무엇을 출력합니까?

```js
public static void main(String[] args) {

  System.out.println(10 + 20 + "30" + 40 + 50);

 }
```

답변 : 30304050

<div class="content-ad"></div>

11. 아웃풋은 무엇인가요?

```java
 public static void main(String[] args) {

  String s1 = "abc";
  String s2 = new String("abc");
  System.out.println(s1.equals(s2) && s1 == s2);

 }
```

답: False

12. 이 코드는 무엇을 출력하나요?

<div class="content-ad"></div>

```md
 ```java
public static void main(String[] args) {

 Integer a = 127;
 Integer b = 127;
 System.out.println(a == b);

}
```
```

답변: True

13. 다음 코드는 무엇을 출력합니까?

```java
public static void main(String[] args) {

 for (int i = 0; i < 3; i++) {
  switch (i) {
  case 0:
   System.out.print("A");
  case 1:
   System.out.print("B");
   break;
  case 2:
   System.out.print("C");
  }
 }

}
``` 

<div class="content-ad"></div>

여기 답변이 있습니다:

14. 이 코드의 결과는 무엇인가요?

```java
public static void main(String[] args) {

  String a = "apple";
  String b = "apple";
  a = a.replace("p", "b");
  System.out.println(a + b);

}
```

답변: abbleapple

<div class="content-ad"></div>

15. 출력은 무엇입니까?

```java
public static void main(String[] args) {

  boolean a = true;
  boolean b = false;
  boolean c = a || b && !a;
  System.out.println(c);

 }
```

정답: true

16. 결과는 무엇입니까?

<div class="content-ad"></div>

```java
public static void main(String[] args) {

  int x = 2;
  int y = 0;
  for (; y < 10; ++y) {
    if (y % x == 0)
      continue;
    else if (y == 8)
      break;
    else
      System.out.print(y + " ");
  }

}
```

Answer : 1 3 5 7 9

17. What will this code output?

```java
public static void main(String[] args) {

  try {
    int a = 10 / 0;
    System.out.println("This will not be printed");
  } catch (Exception e) {
    System.out.println("Exception caught");
  } finally {
    System.out.println("Finally block executed");
  }

}
```

<div class="content-ad"></div>

답변:

예외가 발생했습니다

마지막으로 블록이 실행되었습니다

18. 결과는 무엇입니까?

<div class="content-ad"></div>

```java
public static void main(String[] args) {

  int a = 5;
  int b = 10;
  if ((a = 3) == b) {
   System.out.println(a);
  } else {
   System.out.println(a + b);
  }

 }
```

답변: 13

19. 이 코드는 무엇을 인쇄합니까?

```java
public static void main(String[] args) {

  System.out.println(Math.min(Double.MIN_VALUE, 0.0d));

 }
```
```

<div class="content-ad"></div>

답변: 0.0

20. 이 코드의 출력은 무엇입니까?

```js
public static void main(String[] args) {

  int a = 0;
  System.out.println(a++ == ++a);

}
```

답변: false

<div class="content-ad"></div>

21. 이 코드는 무엇을 출력합니까?

```js
public static void main(String[] args) {

   String s1 = new String("xyz");
   String s2 = "xyz";
   System.out.println(s1.intern() == s2);

}
```

답: True

22. 다음의 출력물은 무엇입니까?

<div class="content-ad"></div>

```markdown
테이블 태그를 Markdown 형식으로 변경하실래요.

```

<div class="content-ad"></div>

질문 : 6

24. 다음 코드의 출력은 무엇입니까?

```java
 public static void main(String[] args) {

  int x = 10;
  int y = 20;
  int z = x += y -= x += y;
  System.out.println(z);

 }
```

답변 : 0

<div class="content-ad"></div>

25. 이 코드의 출력은 무엇입니까?

```js
 public static void main(String[] args) {

  int[] nums = { 1, 2, 3, 4, 5 };
  for (int num : nums) {
   if (num % 2 == 0) {
    continue;
   }
   System.out.print(num + " ");
  }
 }
```

답변: 1 3 5

제 글을 끝까지 읽어주셔서 감사합니다. 글에서 유익한 통찰과 지식을 얻으셨기를 진심으로 바랍니다. 글이 즐겁고 유익했다면, 친구들과 동료들과 공유해 주시기를 부탁드립니다.

<div class="content-ad"></div>

해당 글을 즐겨 보셨다면 팔로우, 구독 또는 박수를 부탁드립니다.

다른 글들도 한번 살펴보세요.