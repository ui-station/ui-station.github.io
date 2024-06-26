---
title: "파이썬 면접 질문"
description: ""
coverImage: "/assets/img/2024-06-19-PythonInterviewQuestions_0.png"
date: 2024-06-19 10:44
ogImage:
  url: /assets/img/2024-06-19-PythonInterviewQuestions_0.png
tag: Tech
originalTitle: "Python Interview Questions"
link: "https://medium.com/@niharrdg/python-interview-questions-bfb139bf491d"
---

안녕하세요 MassCoders! 저는 많은 웹사이트를 돌아다니면서 많은 인터뷰에서 나온 중요한 질문들을 수집했어요. 도움이 되길 바랄게요 :)

![Python Interview Questions](/assets/img/2024-06-19-PythonInterviewQuestions_0.png)

## 쉬운 질문

- 파이썬이란 무엇인가요?

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

- 파이썬은 간단하고 가독성이 좋다는 것으로 알려진 고수준의 인터프리터형 프로그래밍 언어입니다. 절차지향, 객체지향 및 함수형 프로그래밍을 지원합니다.

2. 파이썬의 주요 기능은 무엇인가요?

- 파이썬은 쉽게 읽을 수 있는 구문, 동적 타이핑, 자동 메모리 관리, 큰 표준 라이브러리 및 소규모 및 대규모 프로젝트를 모두 지원하는 것으로 유명합니다.

3. 파이썬에서 리스트를 어떻게 생성하나요?

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

- 목록은 대괄호를 사용하여 생성됩니다. 예를 들어: my_list = [1, 2, 3].

4. 파이썬에서 문자열을 숫자로 변환하는 방법은 무엇인가요?

- int(), float(), 또는 complex() 함수를 사용하여 각각 문자열을 정수, 부동 소수점 또는 복소수로 변환할 수 있습니다.

5. 리스트에 대한 append() 및 extend() 메서드의 차이점은 무엇인가요?

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

- append()은 리스트의 끝에 단일 요소를 추가하고, extend()는 다른 리스트와 같은 이터러블에서 요소를 추가하여 여러 요소를 추가합니다.

6. 리스트에서 remove()와 pop() 메서드의 차이점을 설명해주세요.

- remove()는 지정된 값의 첫 번째 발견을 삭제하며, pop()은 지정된 인덱스의 요소를 제거하고 반환합니다.

7. 파이썬에서 튜플이란 무엇인가요?

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

- 튜플은 변경할 수 없는 순서가 있는 요소의 모음으로, 괄호를 사용하여 정의됩니다. 예를 들어: my_tuple = (1, 2, 3).

8. Python에서 딕셔너리를 어떻게 만드나요?

- 딕셔너리는 키-값 쌍을 사용하여 중괄호로 만듭니다. 예를 들어: my_dict = {'key': 'value'}.

9. Python에서 세트란 무엇인가요?

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

- 세트는 고유 요소의 순서 없는 컬렉션입니다. 세트는 중괄호를 사용하여 정의됩니다: my_set = '1, 2, 3'.

10. Python에서 pass 문의 목적은 무엇인가요?

- pass는 동작이 필요하지만 기능, 루프 또는 클래스에서 문법적으로 필요한 코드가 없는 경우에 자리 표시자로 사용되는 빈 문입니다.

11. Python 키워드는 무엇인가요?

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

- 키워드는 Python에서 특별한 의미를 가지는 예약어로, if, else, for, while, return, True, False, None 등이 있습니다.

12. Python 리터럴이란 무엇인가요?

- 리터럴은 Python에서 고정된 값을 나타냅니다. 예로는 문자열 리터럴 ("Hello"), 숫자 리터럴 (123), 부울 리터럴 (True, False), 특별한 리터럴 (None) 등이 있습니다.

13. .py와 .pyc 파일의 차이점은 무엇인가요?

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

- .py 파일은 Python 소스 코드를 포함하고 있고, .pyc 파일은 Python 인터프리터가 .py 파일로부터 생성한 바이트 코드를 빠르게 실행하기 위해 포함하고 있습니다.

14. Python에서 슬라이싱이란 무엇인가요?

- 슬라이싱은 리스트, 튜플 및 문자열과 같은 시퀀스에서 요소 범위에 액세스하는 데 사용됩니다. 구문: sequence[start:end:step].

15. Python 모듈이란 무엇인가요?

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

- 모듈은 Python 정의와 문장을 포함하는 파일입니다. 모듈을 사용하면 코드를 재사용하고 구성할 수 있습니다.

## 중간 단계 질문

1. Python에서 람다 함수란 무엇인가요?

- 람다 함수는 람다 키워드로 정의된 작은 익명 함수입니다. 여러 인수를 가질 수 있지만 하나의 표현식만 가질 수 있습니다. 예: lambda x: x + 1.

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

2. `map()`과 `filter()` 함수의 차이점을 설명하겠습니다.

- `map()` 함수는 이터러블의 모든 항목에 함수를 적용하고 결과 리스트를 반환합니다. `filter()` 함수는 함수가 True를 반환하는 항목들의 리스트를 반환합니다.

3. Python에서 데코레이터란 무엇인가요?

- 데코레이터는 다른 함수를 수정하여 기능을 추가하지만 구조를 변경하지 않는 함수입니다. 데코레이터는 @ 기호로 표시됩니다.

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

**args 와 **kwargs 개념을 설명해 드릴게요.

- \*args 는 함수가 임의의 개수의 위치 인자를 튜플로 전달받을 수 있게 해줍니다. \*\*kwargs 는 함수가 딕셔너리로 전달되는 키워드 인자를 받을 수 있도록 해줍니다.

파이썬에서 재귀란 무엇인가요?

- 재귀는 같은 문제의 더 작은 인스턴스를 해결하기 위해 자신을 호출하는 함수입니다. 예를 들어, 숫자의 팩토리얼을 계산하는 것이 있습니다.

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

6. 파이썬에서 global 키워드의 목적을 설명해보겠어요.

- global 키워드는 함수 내부의 변수가 전역 변수로 간주되어 지역 변수로 처리되지 않아야 함을 선언하는 데 사용됩니다.

7. 딕셔너리 컴프리헨션은 무엇인가요?

- 딕셔너리 컴프리헨션은 딕셔너리를 만드는 간결한 방법입니다. 문법: 'key: value for item in iterable'.

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

8. 파이썬에서 nonlocal 키워드란 무엇인가요?

- nonlocal 키워드는 중첩된 함수 내에서 변수가 감싸고 있는 범위의 변수를 참조한다는 것을 나타냅니다.

9. 파이썬에서 예외를 처리하는 방법은 무엇인가요?

- 예외를 처리하기 위해 try, except, else 및 finally 블록을 사용합니다. 이 구조를 사용하면 오류를 우아하게 처리할 수 있습니다.

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

10. 얕은 복사와 깊은 복사의 차이는 무엇인가요?

- 얕은 복사는 새 객체를 생성하지만 그 안에 참조를 삽입합니다. 깊은 복사는 새 객체를 생성하고 원본에 있는 모든 객체를 재귀적으로 복사합니다.

11. Global Interpreter Lock (GIL)은 무엇인가요?

- GIL은 Python 객체에 대한 액세스를 보호하는 뮤텍스로, 여러 스레드가 동시에 Python 바이트코드를 실행하는 것을 방지합니다.

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

12. 파이썬에서 제너레이터의 개념을 설명해 드리겠습니다.

- 제너레이터는 한 번에 모든 항목을 반환하는 대신 항목을 생성하는 반복자입니다. yield 키워드를 사용하여 정의됩니다.

13. 파이썬에서 메타클래스란 무엇인가요?

- 메타클래스는 클래스의 동작을 정의하며 클래스의 클래스입니다. 클래스 생성을 사용자 정의할 수 있도록 해줍니다.

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

14. 파이썬에서 메모리 관리는 Python의 메모리 관리자에 의해 관리되는 개인 힙 공간과 가비지 수집에 의해 지원됩니다.

15. 파이썬에서 몽키 패칭이란 런타임에서 클래스나 모듈의 동적 수정을 의미합니다.

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

# 어려운 질문들

1. ****init****와 ****new**** 메소드의 차이를 설명해주세요.

   - ****init****은 인스턴스가 생성된 후에 초기화합니다. ****new****는 클래스의 새로운 인스턴스를 생성하는 역할을 합니다.

2. 파이썬에서 멀티스레딩을 구현하는 방법은 무엇인가요?

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

- 멀티스레딩은 threading 모듈을 사용하여 구현할 수 있어요. 파이썬 스레드는 GIL로 인해 실제로 병렬적이지는 않지만, I/O 바운드 작업에 유용할 수 있어요.

3. 파이썬에는 어떤 내장 데이터 유형이 있나요?

- 파이썬의 내장 데이터 유형에는 숫자 유형(int, float, complex), 시퀀스 유형(str, list, tuple, range), 매핑 유형(dict), 집합 유형(set), 논리 유형(bool)이 포함돼요.

4. ==와 is 연산자의 차이점은 무엇인가요?

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

- ==은 값의 동등성을 확인하는 반면 is는 객체 식별성을 확인하며, 즉 두 변수가 동일한 객체를 참조하는지 확인합니다.

5. if **name** == "**main**": 문장의 목적은 무엇인가요?

- 해당 문장은 스크립트가 직접 실행될 때에만 코드 블록이 실행되도록 보장합니다. 모듈로 가져올 때에는 실행되지 않습니다.

6. 얕은 복사와 깊은 복사의 차이점은 무엇인가요?

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

- 얕은 복사는 객체를 복사하지만 해당 객체를 참조하는 객체를 복사하지 않습니다. 깊은 복사는 객체와 해당 객체를 참조하는 모든 객체를 재귀적으로 복사합니다.

7. Python에서 데코레이터의 사용을 설명하시오.

- 데코레이터는 함수나 메소드의 동작을 수정합니다. 종종 원본 함수 코드를 변경하지 않고 기능을 확장하는 데 사용됩니다.

8. 클래스 메소드에서 self의 목적은 무엇인가요?

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

- self는 클래스의 인스턴스를 가리키며, 해당 메서드 내에서 클래스의 속성과 메서드에 접근할 수 있게 합니다.

9. Python에서 예외 처리하는 방법은 무엇인가요?

- 예외는 try, except, else 및 finally 블록을 사용하여 처리합니다. 이를 통해 오류 처리와 자원 정리가 우아하게 이루어집니다.

10. Python에서의 다형성이란 무엇인가요?

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

- 다형성은 메서드가 작동하는 객체에 기반하여 다른 작업을 수행할 수 있게 해주어 다른 클래스가 동일한 인터페이스를 통해 처리될 수 있게 합니다.

더 많은 정보를 찾으시려면 LinkedIn에서 저를 팔로우하세요 :)
