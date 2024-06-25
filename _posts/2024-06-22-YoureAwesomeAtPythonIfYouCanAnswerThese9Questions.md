---
title: "이 9가지 질문에 답할 수 있다면 당신은 파이썬 전문가입니다"
description: ""
coverImage: "/assets/img/2024-06-22-YoureAwesomeAtPythonIfYouCanAnswerThese9Questions_0.png"
date: 2024-06-22 23:01
ogImage:
  url: /assets/img/2024-06-22-YoureAwesomeAtPythonIfYouCanAnswerThese9Questions_0.png
tag: Tech
originalTitle: "You’re Awesome At Python If You Can Answer These 9 Questions"
link: "https://medium.com/gitconnected/can-you-answer-all-9-difficult-python-questions-correctly-most-cant-8bf404721692"
---

아래는 링크한 이미지입니다.

9가지 까다로운 파이썬 문제가 있어요. 대부분의 독자들이 적어도 7개 이상의 문제를 벌써 보지 않고 정확하게 대답할 수 없을 거라고 확신해요. 하지만 제가 틀렸다는 것을 증명해 주세요.

범죄는 코드를 실행하거나 다른 자료를 찾는 것을 의미해요.

# 1) 데코레이터 관련 부분

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

def add(symbol):
def wrapper1(func):
def wrapper2(*args, \*\*kwargs):
return func(*args, \*\*kwargs) + symbol
return wrapper1

@add('!!')
def hello(name):
return 'hello' + name

print(hello('tom'))

위 코드를 실행하면 무엇이 출력됩니까?

- A) hello tom
- B) hello tom!
- C) hello tom!!
- D) `function add.`locals`.wrapper1.`locals`.wrapper2 at 0x1053c1080`
- E) TypeError: ‘NoneType’ object is not callable
- F) SyntaxError: iterable argument unpacking follows keyword argument unpacking

# 2) 어떤 마법과 같은 메소드들

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
class Dog:
    def __init__(self, name):
        self.name = name

    def __getattr__(self, key):
        return '사과'

    def __getattribute__(self, key):
        return '오렌지'

    def __getitem__(self, key):
        return '배'

dog = Dog('rocky')
print(dog.name)      #??
```

이것은 무엇을 출력합니까?

- A) `rocky`
- B) `사과`
- C) `오렌지`
- D) `배`
- E) 구문 오류
- F) KeyError 오류

# 3) 별의 다발

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
x = [*[1,2]*2*3]

print(x)
```

x를 출력하면 무엇이 됩니까?

- A) SyntaxError
- B) ValueError: too many values to unpack
- C) [1, 2, 2, 3]
- D) [[1, 2], [1, 2], [1, 2], [1, 2], [1, 2], [1, 2]]
- E) [1, 2, 1, 2, 1, 2, 1, 2, 1, 2, 1, 2]
- F) [[1, 2, 1, 2, 1, 2, 1, 2, 1, 2, 1, 2]]

# 4) List Comprehension Shenanigans

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
out = []
for i in [1,2,3]:
    row = []
    for j in [4,5,6]:
        row.append(i+j)
    out.append(row)
```

위와 동일한 결과를 얻는 리스트 컴프리헨션은 무엇입니까?

- A) out = [[i+j for j in [4,5,6]] for i in [1,2,3]]
- B) out = [[i+j for i in [4,5,6]] for j in [1,2,3]]
- C) out = [i+j for i in [4,5,6] for j in [1,2,3]]
- D) out = [i+j for j in [1,2,3] for i in [4,5,6]]
- E) out = [i+j for i,j in zip([1,2,3], [4,5,6])]
- F) out = [[i,j] for i,j in enumerate(zip(_[1,2,3], _[4,5,6]))]

# 5) Switching Shenanigans

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
a, b, c, d = 1, 2, 3, 4
a, b, c, d = b, c, d, a
a, b, c, d = b, c, d, a
a, b, c, d = b, c, d, a
a, b, c, d = b, c, d, a

print(a, b, c, d)
```

여기서 출력되는 값은?

A) 1 2 3 4
B) 2 3 4 1
C) 3 4 1 2
D) 4 1 2 3
E) 4 3 2 1
F) 1 4 3 2

# 6) 람다 함수

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

어떤 람다 함수가 잘못되었나요?

- A) lambda a, b, c: [*(a, b, c)]
- B) lambda \*a, \*\*b: print(a, b)
- C) lambda \**b, *a: print(a, b)
- D) lambda a, b: map(int, [a, b])
- E) 위의 모든 람다 함수
- F) 위의 람다 함수 중에 없음

# 7) 클래스에서의 속임수

```js
class Dog:
    def __getattr__(self, key):
        return Dog()

    def __getitem__(self, key):
        return 0

dog = Dog()
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

다음 중 어떤 옵션이 오류를 발생시키나요?

- A) dog._.\_\_._.**_._.**.**\_\_\_**.\_.**\_.\_\_\_**.\_\_
- B) dog.狗.狗.狗.狗.狗.狗.狗.狗.狗.狗.狗
- C) dog.狗.狗[`apple 3.14159`]
- D) dog.**123._1234._**654
- E) dog[dog]
- F) dog.14*.\_14.14*.\_14

```js
x = -1--2---3----4-----5
print(x)
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

x를 출력하면 무엇이 발생하나요?

- A) SyntaxError
- B) OperatorError
- C) IndentationError
- D) MemoryError
- E) -1
- F) -3

## 9) 비트 조작

```js
x = 9;
x = ((((~~~~x << 5) >> 2) << 5) >> 8) | ((9 & 9) ^ 16 ^ 16);
print(x);
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

위의 표를 Markdown 형식으로 변경하면 다음과 같습니다.

---

**What happens when we print x?**

- A) 0
- B) -9
- C) 9
- D) 25
- E) ZeroDivisionError
- F) SyntaxError

---

# 경고 — 아래에 정답이 있습니다

이를 확인하시기 전에 조금 시간을 내어 스스로 시도해보시고, 가능하면 참고하지 않고 풀어보세요!

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

# 1) 장식자 관련

```javascript
def add(symbol):
    def wrapper1(func):
        def wrapper2(*args, **kwargs):
            return func(*args, **kwargs) + symbol
    return wrapper1

@add('!!')
def hello(name):
    return 'hello' + name

print(hello('tom'))
```

무엇이 출력됩니까?

- A) hello tom
- B) hello tom!
- C) hello tom!!
- D) `function add.`locals`.wrapper1.`locals`.wrapper2 at 0x1053c1080`
- E) TypeError: 'NoneType' object is not callable
- F) SyntaxError: iterable argument unpacking follows keyword argument unpacking

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

다음을 출력합니다.

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

- A) `rocky`
- B) `apple`
- C) `orange`
- D) `pear`
- E) Syntax 오류 발생
- F) KeyError 발생

- `__getattr__`은 우리가 `dog.key`를 할 때 key가 존재하지 않을 때의 동작을 정의합니다.
- `__getattribute__`은 우리가 `dog.key`를 할 때 key가 존재 여부에 관계없이 동작을 정의합니다. 이는 `__getattr__`을 덮어씁니다.
- `__getitem__`은 `dog[key]`를 할 때의 동작을 정의합니다. 이 메서드는 사용되지 않습니다.

# 3) 별의 무리

```js
x = [*[1,2]*2*3]

print(x)
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

`x`를 출력하면 무엇이 될까요?

- A) SyntaxError
- B) ValueError: too many values to unpack
- C) [1, 2, 2, 3]
- D) [[1, 2], [1, 2], [1, 2], [1, 2], [1, 2], [1, 2]]
- E) [1, 2, 1, 2, 1, 2, 1, 2, 1, 2, 1, 2]
- F) [[1, 2, 1, 2, 1, 2, 1, 2, 1, 2, 1, 2]]

```js
x = [*[1,2]*2*3]
  = [* ([1,2]*2*3) ]
  = [* ([1,2,1,2]*3) ]
  = [* [1,2,1,2,1,2,1,2,1,2,1,2] ]
  = [1,2,1,2,1,2,1,2,1,2,1,2]
```

^ 처음 *은 언팩하고, 이후 *는 곱셈합니다.

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

# 4) 리스트 내포 흥분

```js
out = []
for i in [1,2,3]:
    row = []
    for j in [4,5,6]:
        row.append(i+j)
    out.append(row)
```

위와 동일한 작업을 하는 리스트 내포는 무엇입니까?

- A) out = [[i+j for j in [4,5,6]] for i in [1,2,3]]
- B) out = [i+j for i,j in [i for i in [[1,2,3],[4,5,6]]]]
- C) out = [i+j for i in [4,5,6] for j in [1,2,3]]
- D) out = [i+j for j in [1,2,3] for i in [4,5,6]]
- E) out = [i+j for i,j in zip([1,2,3], [4,5,6])]
- F) out = [[i,j] for i,j in enumerate(zip(_[1,2,3], _[4,5,6])]

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

B, E 그리고 F는 무의미해요. C와 D는 중첩된 목록을 생성하지 않아요.

# 5) 변환 광기

```js
a, b, c, (d = 1), 2, 3, 4;
a, b, c, (d = b), c, d, a;
a, b, c, (d = b), c, d, a;
a, b, c, (d = b), c, d, a;
a, b, c, (d = b), c, d, a;

print(a, b, c, d);
```

여기서 무엇이 출력될까요?

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

- A) 1 2 3 4
- B) 2 3 4 1
- C) 3 4 1 2
- D) 4 1 2 3
- E) 4 3 2 1
- F) 1 4 3 2

```js
a, b, c, d = 1, 2, 3, 4    # a=1 b=2 c=3 d=4
a, b, c, d = b, c, d, a    # a=2 b=3 c=4 d=1
a, b, c, d = b, c, d, a    # a=3 b=4 c=1 d=2
a, b, c, d = b, c, d, a    # a=4 b=1 c=2 d=3
a, b, c, d = b, c, d, a    # a=1 b=2 c=3 d=4
```

매번 a, b, c, d = b, c, d, a를 실행할 때마다, a, b, c, d의 값이 오른쪽으로 한 칸씩 이동합니다. 그러나 이를 4번 반복하면 1, 2, 3, 4로 다시 돌아옵니다.

# 6) 람다 함수

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

어떤 lambda 함수가 잘못되었나요?

- A) lambda a, b, c: [*(a, b, c)]
- B) lambda \*a, \*\*b: print(a, b)
- C) lambda \**b, *a: print(a, b)
- D) lambda a, b: map(int, [a, b])
- E) 위의 모든 람다 함수
- F) 위의 람다 함수 중에 없음

함수를 정의할 때 (람다 함수 포함), 모든 *args는 **kwargs보다 먼저 정의되어야 합니다. C)에서 **b가 *a보다 먼저 정의되어서 틀렸어요.

# 7) Class Shenanigans

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

```kotlin
class Dog {
    override fun __getattr__(key: String): Dog {
        return Dog()
    }

    override fun __getitem__(key: String): Int {
        return 0
    }
}

val dog = Dog()
```

다음 중 어떤 옵션이 오류를 발생시킵니까?

- A) dog._.\_\_._.**_._.**.**\_\_\_**.\_.**\_.\_\_\_**.\_\_
- B) dog.狗.狗.狗.狗.狗.狗.狗.狗.狗.狗.狗
- C) dog.狗.狗[`apple 3.14159`]
- D) dog.**123._1234._**654
- E) dog[dog]
- F) dog.14*.\_14.14*.\_14

- A) **getattr**이 또 다른 Dog 개체를 반환하므로, .\_\_ 등을 사용해도 계속 Dog 개체를 얻게됩니다
- B) A와 같지만, 중국어 유니코드 문자를 사용하여 유효한 변수 이름을 사용합니다
- C) A와 동일합니다. 하지만 키 값을 사용하여 dog[key]를 마지막에 사용하므로 0을 얻습니다
- D) A와 동일합니다. 변수 이름은 \_\_로 시작하고 숫자를 포함할 수 있습니다
- E) 이것은 단순히 0을 반환합니다
- F) 변수 이름은 숫자로 시작할 수 없습니다. 따라서 이는 잘못된 것입니다

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

# 8) -

```js
x = -1--2---3----4-----5
print(x)
```

When we print x, the output will be:

- F) -3

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
x = -5       # x는 -5
x = --5      # x는 5
x = ---5     # x는 -5
x = ----5    # x는 5
x = -----5   # x는 -5
```

^ 숫자 5 앞에 더 많은 -를 추가하면 계속해서 부호가 뒤바뀔 것인데, 음수 음수 5는 5와 같습니다.

```js
x = -1--2---3----4-----5
  = -1-(-2)-(--3)-(---4)-(----5)
  = -1-(-2)-(3)-(-4)-(5)
  = -1+2-3+4-5
  = -3
```

# 9) 비트 연산 광기

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
x = 9;
x = ((((~~x << 5) >> 2) << 5) >> 8) | ((9 & 9) ^ 16 ^ 16);
print(x);
```

x를 인쇄할 때 무슨 일이 벌어질까요?

- A) 0
- B) -9
- C) 9
- D) 25
- E) ZeroDivisionError
- F) SyntaxError

- x `5` 2 `5` 8는 x를 왼쪽으로 5번 이동시키고, 오른쪽으로 2번 이동시키고, 다시 왼쪽으로 5번 이동시키고, 오른쪽으로 8번 이동시킵니다. 이 작업은 x에 아무런 영향을 미치지 않아서 결과적으로 x는 9로 유지됩니다.
- 9 | 9는 단순히 9를 반환합니다.
- 9 & 9는 단순히 9를 반환합니다.
- 9 ^ 16 (XOR 연산자)는 25를 반환합니다.
- 그러나 우리가 두 번 XOR을 한다면, 9를 다시 돌려받을 것입니다.
- (9 ^ 16) ^ 16은 다시 한 번 9를 돌려줄 것입니다.

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

# 결론

이 중 몇 개를 참으로 대답할 수 있었나요? 법을 어겨 대답하지 않고 성공했다면 멋집니다!

- 모두 정확히 대답했다면 잘 했어요!
- 적어도 7개 이상을 맞추었다면 여전히 멋져요!
- 그렇지 않다면 괜찮아요 — 이것이 당신이 파이썬을 잘못 이해했다는 것을 의미하는 것은 아닙니다.

이 문제들을 시도하면서 즐거운 시간 보냈기를 바랍니다!

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

# 만약 나를 창작자로 지원하고 싶다면

- 이 이야기에 대해 50번 박수를 치세요
- 당신의 생각을 나에게 남겨주세요
- 이야기 중 가장 마음에 드는 부분을 강조해주세요

감사합니다! 이 작은 조치들이 큰 영향을 미치고, 정말 감사드립니다!

YouTube: https://www.youtube.com/@zlliu246

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

LinkedIn: [https://www.linkedin.com/in/zlliu/](https://www.linkedin.com/in/zlliu/)
