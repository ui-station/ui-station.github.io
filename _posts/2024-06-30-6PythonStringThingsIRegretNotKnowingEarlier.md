---
title: "일찍 알았더라면 좋았을 6가지 Python 문자열 팁"
description: ""
coverImage: "/assets/img/2024-06-30-6PythonStringThingsIRegretNotKnowingEarlier_0.png"
date: 2024-06-30 21:58
ogImage: 
  url: /assets/img/2024-06-30-6PythonStringThingsIRegretNotKnowingEarlier_0.png
tag: Tech
originalTitle: "6 Python String Things I Regret Not Knowing Earlier"
link: "https://medium.com/@zlliu/6-python-string-things-i-regret-not-knowing-earlier-6c777942e8c2"
---


<img src="/assets/img/2024-06-30-6PythonStringThingsIRegretNotKnowingEarlier_0.png" />

개발자로서, 우리는 우연히 문자열을 다루게 됩니다. 여기 개발자로서의 삶을 편리하게 만들어준 멋진 파이썬 문자열 사실 6가지가 있어요.

# 1) 'string' 모듈

모든 알파벳이 필요할 때 실제로 모두 타이핑할 필요가 없습니다.

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
알파벳 = 'abcdefghijklmnopqrstuvwxyz'
```

대신에, 내장된 문자열 모듈을 가져와서 사용할 수 있어요:

```js
from string import ascii_lowercase

print(ascii_lowercase)
# abcdefghijklmnopqrstuvwxyz
```

문자열 모듈에는 대문자와 구두점과 같은 다른 유용한 문자열 상수들이 포함되어 있어요.

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
첫 번째 표

```
| 모듈 | import 구문 |
| --- | --- |
| string | (
| | ascii_uppercase,
| | ascii_letters,
| | punctuation
) |


print(ascii_uppercase)
# abcdefghijklmnopqrstuvwxyz

print(ascii_letters)
# abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ

print(punctuation)
# !"#$%&'()*+,-./:;<=>?@[\]^_`{|}~


두 번째 표

유니 코드 문자

Unicode를 통해 특수 문자를 출력할 수 있습니다. 중국어, 일본어, 한국어, 이모티콘, 상자 문자 등을 문자열에서 출력할 수 있습니다.

```js
print('옴 심볼: \u03A9')

# 옴 심볼: Ω
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

유니코드 문자를 인쇄하려면:

- 유니코드 코드를 찾아야 합니다. 예: 오메가(옴) 기호의 경우 30A9를 구글/위키 등에서 찾습니다.
- 유니코드 코드 앞에 \u를 추가합니다. 예: \u30A9
- 문자열에 추가합니다. 예: `오메가 기호: \u30A9`
- 인쇄합니다! 그게 다입니다.

참고 — \u03A9와 같이 유니코드 문자는 한 개의 문자로 처리됩니다.

이 방법을 사용하여 많은 특수 문자를 출력할 수 있습니다!

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
print('alpha: \u03B1')
# alpha: α

print('beta: \u03B2')
# beta: β

print('inverted ?: \u00BF')
# inverted ?: ¿

print('arrow:', '\u2192')
# arrow: →

print('summation:', '\u2211')
# summation: ∑
```

# 3) f-strings (formatted strings)

처음에 문자열을 더하는 방법을 배웠을 때:

```js
name = 'tom'
age = 30

x = 'my name is ' + name + ' and I am ' + str(age)

print(x) # my name is tome and I am 30
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

일부 문제점:

- 변수를 처리해야 할 때 더 많은 변수가 추가될수록 계속 타이핑하는 것이 점점 귀찮아집니다.
- 우리는 non-string 변수에 대해 str(var)를 사용해야 합니다. 이는 string에 string만 추가할 수 있기 때문에(이것이 귀찮음) 필요합니다.

F-strings(형식화된 문자열)를 사용하면 우리의 삶이 더 쉬워집니다:

```js
name = 'tom'
age = 30

x = f'my name is {name} and I am {age}'

print(x) # my name is tom and I am 30
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

- 우리는 문자열을 형식화된 문자열로 만들기 위해 따옴표 앞에 f를 추가합니다.
- f-문자열 안에 변수를 포함시키기 위해 'var'를 사용합니다.

F-문자열은 강력한 형식 지정 구문을 제공합니다:

```js
# 변수 이름을 출력하려면 var 뒤에 = 을 추가합니다

name = 'tom'
age = 30

print(f'{name=} {age=}') 
# name='tom' age=30
```

```js
# 소수점 n 자리까지 반올림을 수행하려면 :.nf를 추가합니다

pi = 3.14159265

print(f'{pi:.2f} {pi:.4f}')
# 3.14 3.1416
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

```js
# 날짜 및 시간을 쉽게 포맷팅하기
# 참고: 날짜 및 시간 포맷에 대한 기호들은 Python 문서에서 찾을 수 있습니다

from datetime import datetime
date = datetime(2024, 3, 7)

print(f"{date:%y-%m-%d}")  # 24-03-07
print(f"{date:%d %B %Y}")  # 07 March 2024
```

# 4) 원시 문자열

문자열 내부에 \n (개행 문자)을 추가하면 새 줄에 출력됩니다:

```js
x = 'hello\nworld'

print(x)
# hello
# world
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

특정 이유로 만약 실제로 "hello\nworld"를 출력하고 싶다면, \ 문자를 또 다른 \ 문자를 사용하여 이스케이프해야 합니다:

```js
x = 'hello\\nworld'

print(x)
# hello\nworld
```

우리는 원시 문자열(raw strings)을 사용하여 이를 피할 수 있습니다 - 따옴표 앞에 r을 추가함으로써

```js
x = r'hello\nworld'

print(x)
# hello\nworld
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

- 생 문자열에서 \ 문자는 실제 문자로 처리됩니다
- \n은 2개의 문자로 처리됩니다 — \와 n
- 많은 \ 문자로 이루어진 문자열을 구성해야 할 때 유용합니다

저는 특히 정규 표현식 (regex)에 대해 생 문자열을 유용하게 사용합니다.

```js
# 'apple'을 포함하는 모든 단어를 찾기 위해 regex 사용

string = 'apple orange pear pineapple snapple durian'

import re

regex = r'\b\w*apple\b'
print(re.findall(regex, string))

# ['apple', 'pineapple', 'snapple']
```

정규 표현식에 익숙하지 않은 분들을 위해 추가적인 설명:

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

- \b 는 단어들 사이의 경계를 나타내는 문자이며, 단어, 숫자 또는 밑줄과 매칭됩니다.
- \w 는 단어 문자로, 숫자, 알파벳, 또는 밑줄과 매칭됩니다.

만약 여기서 정규표현식에 원시 문자열을 사용하지 않았다면, 아마도 다음과 같을 것입니다:

```js
# 'apple'을 포함하는 모든 단어를 찾기 위해 정규표현식 사용

string = 'apple orange pear pineapple snapple durian'

import re

regex = '\\b\\w*apple\\b'
print(re.findall(regex, string))

# ['apple', 'pineapple', 'snapple']
```

^ 여전히 작동하지만, 다른 \ 문자를 이스케이프하기 위해 \를 사용해야 한다는 것에 주목해 주세요. 많은 이러한 작업을 다뤄야 한다면 괴롭다는 것을 알 수 있습니다.

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

# 5) 대괄호를 사용하여 문자열 결합

대괄호 안에 연속된 문자열은 자동으로 함께 결합됩니다:

```js
x = (
    'apple '
    'orange '
    'pear'
)

print(x)
# apple orange pear
```

^ 이것은 여러 줄로 나뉘어진 매우 긴 문자열을 처리해야 할 때 유용하며 + 연산자를 사용하고 싶지 않을 때 유용합니다

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
# + 연산자 사용하기
x = 'apple ' + \
    'orange ' + \
    'pear'

print(x)
# apple orange pear
```

^ + 연산자를 여러 줄에 걸쳐 사용할 때는 \을 사용하여 한 줄이 여러 줄에 걸친다고 Python에 알려주어야 합니다. 이것은 간단한 괄호를 사용하는 것만큼 우아하지 않다고 제 생각에는 느껴집니다.

# 6) ANSI 이스케이프 시퀀스

ANSI 이스케이프 시퀀스는 특수한 문자로, 문자열에서 멋진 작업을 할 수 있게 해줍니다. 예를 들어:

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

- 텍스트를 색으로 출력하기
- 텍스트 강조 표시하기
- 커서를 위로 이동하기

예를 들어, 몇 가지 텍스트를 색상으로 출력해봅시다

```js
print('\x1b[31mhello')
print('\x1b[32mhello')
print('\x1b[34mhello')
```

<img src="/assets/img/2024-06-30-6PythonStringThingsIRegretNotKnowingEarlier_1.png" />

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

- \x1b[31m은 빨강의 ANSI 시퀀스입니다.
- \x1b[32m은 초록의 ANSI 시퀀스입니다.
- \x1b[34m은 파랑의 ANSI 시퀀스입니다.

이미 출력된 내용을 지우는 멋진 작업도 할 수 있어요:

```js
CURSOR_UP = "\033[1A"
CLEAR = "\x1b[2K"
CLEAR_LINE = CURSOR_UP + CLEAR

print('apple')
print('orange')
print('pear')
print('pineapple')

print(CLEAR_LINE * 2, end='')

print('durian')
print('grapes')
print('dragonfruit')

# apple
# orange
# durian
# grapes
# dragonfruit
```

- \033[1A는 커서를 한 줄 위로 이동시킵니다.
- \x1b[2K는 커서가 있는 줄을 삭제합니다.
- 두 개를 함께 출력하면 터미널에서 한 줄을 삭제합니다.
- print(CLEAR_LINE * 2, end='')는 이미 출력된 2 줄(pear와 pineapple)을 삭제합니다.
- 그래서 durian이 바로 orange 다음에 나타나는 이유입니다.

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

참고 - 이슈 문자열을 매번 찾아보지 않아도 되도록 Python 패키지를 만들었습니다. pip install unprint을 사용해서 다운로드해보세요.

```js
from unprint import unprint

print('apple')
print('orange')
print('pear')
print('pineapple')

unprint(2)

print('durian')
print('grapes')
print('dragonfruit')

# apple
# orange
# durian
# grapes
# dragonfruit
```

# 부가적인 Python 농담

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

알기 쉽고 명확했기를 바랍니다

# 만약 개발자로서 저를 지원하고 싶다면

- 내 서적을 구매해 주세요! — 파이썬에 대해 알지 못했던 101가지
- 찾을 수 있는 곳: https://payhip.com/b/vywcf
- 이 이야기에 대해 50번 박수를 치세요
- 당신의 생각을 남겨줘요
- 이야기에서 가장 좋아하는 부분을 강조해 주세요

감사합니다! 이 작은 행동들이 큰 도움이 되고, 정말 감사합니다!

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

YouTube: [https://www.youtube.com/@zlliu246](https://www.youtube.com/@zlliu246)

LinkedIn: [https://www.linkedin.com/in/zlliu/](https://www.linkedin.com/in/zlliu/)