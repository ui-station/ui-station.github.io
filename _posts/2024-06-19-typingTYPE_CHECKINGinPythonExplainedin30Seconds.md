---
title: "파이썬에서 typingTYPE_CHECKING이 30초 안에 설명하기"
description: ""
coverImage: "/assets/img/2024-06-19-typingTYPE_CHECKINGinPythonExplainedin30Seconds_0.png"
date: 2024-06-19 10:53
ogImage: 
  url: /assets/img/2024-06-19-typingTYPE_CHECKINGinPythonExplainedin30Seconds_0.png
tag: Tech
originalTitle: "“typing.TYPE_CHECKING” in Python Explained in 30 Seconds"
link: "https://medium.com/gitconnected/typing-type-checking-in-python-explained-in-30-seconds-4ee494f94143"
---


<img src="/assets/img/2024-06-19-typingTYPE_CHECKINGinPythonExplainedin30Seconds_0.png" />

근무 중인 Python 코드베이스에서 이러한 코드 조각을 본 적이 있을 수 있습니다. 이 코드가 무슨 일을 하는지 궁금했던 적이 있다면, 오늘은 여기 있어서 설명해 드리겠습니다.

# typing.TYPE_CHECKING은 그냥 False입니다

```python
from typing import TYPE_CHECKING

print(TYPE_CHECKING)

# False
```

<div class="content-ad"></div>

일반적인 경우에는 TYPE_CHECKING 변수가 그냥 False로 설정되어 있습니다. 하지만 이 경우에 왜 사용하는 걸까요?

typing.TYPE_CHECKING은 정적 타입 체크(myppy 등)를 할 때 True로 설정된다고 가정됩니다.

그러나 코드를 보통 실행할 때는 단순히 False입니다. 그러면 왜 이걸 사용하는 걸까요?

# 경우 1 — 개 대 인간

<div class="content-ad"></div>

여기에는 서로 가져오는 dog.py와 human.py가 있습니다.

```python
# dog.py

from human import Human

class Dog:
    def get_human() -> Human:
      ...
```

```python
# human.py

from dog import Dog

class Human:
    def get_dog() -> Dog:
        ...
```

여기서 서로를 가져오는 순환 포함이 있다는 것을 주의하세요:

<div class="content-ad"></div>

- dog.py에서 human.py를 가져오고
- human.py에서는 dog.py를 가져옵니다

그래서 dog.py나 human.py 또는 dog.py나 human.py를 가져오는 다른 Python 스크립트 중 하나를 실행하면 순환 임포트 오류가 발생합니다:

```js
ImportError: cannot import name 'Human' from 
partially initialized module 'human' 
(most likely due to a circular import) 
```

# Case 2 — Dog VS Human, but with TYPE_CHECKING

<div class="content-ad"></div>

Dog와 Human만 유형 주석에 필요하기 때문에 `-` Human과 `-` Dog와 같이 전체 클래스를 가져올 필요는 실제로 없습니다. 원형 가져오기 문제를 피하기 위해 다음 구문을 사용할 수 있습니다.

```js
# dog.py
from typing import TYPE_CHECKING

if TYPE_CHECKING:
  from human import Human

class Dog:
    def get_human() -> "Human":
      ...
```

```js
# human.py
from typing import TYPE_CHECKING

if TYPE_CHECKING:
  from dog import Dog

class Human:
    def get_dog() -> "Dog":
        ...
```

여기에서 이전 코드에 몇 가지 변경 사항을 가했습니다.

<div class="content-ad"></div>

- `typing`에서 `TYPE_CHECKING`을 import하는 부분을 추가했어요.
- `TYPE_CHECKING` 조건문 아래에서 import 문들을 옮겼어요.
- Dog와 Human 타입 어노테이션 주변에 따옴표를 추가했어요.

그리고 코드를 실행해보면, 이제는 순환 import 문제가 없어졌어요.

# 왜 순환 import 문제가 사라졌을까요?

```js
def get_human() -> Human:
```

<div class="content-ad"></div>

위의 예제는 다음과 같이 변경되었습니다:

```python
def get_human() -> "Human":
```

따라서 Python은 더 이상 human.py에서 실제 Human 클래스를 가져오려고 시도하지 않습니다 (이는 순환 임포트 문제를 발생시킵니다).

# 그렇다면 TYPE_CHECKING이 왜 필요한가요?

<div class="content-ad"></div>

큰 파이썬 프로젝트에서는 어쩌면 언젠가는 mypy와 같은 정적 타입 체커를 사용할 것입니다.

정적 타입 체커를 실행할 때 typing.TYPE_CHECKING 변수를 True로 설정하고 실제로 클래스를 가져옵니다. (걱정 마세요 - 정적 타입 체커는 순환 Import 문제를 다른 방식으로 처리할 수 있습니다)

하지만 코드를 보통 실행하고 정적 타입 체커를 다룰 필요가 없을 때는 실제 클래스를 가져올 필요가 없습니다.

요컨대, TYPE_CHECKING이 추가된 이유는:

<div class="content-ad"></div>

- 순환 가져오기 문제가 발생하지 않습니다 
- 해당 스크립트를 실행할 때 정적 유형 검사를 여전히 제대로 수행할 수 있습니다 (원래 유형 주석을 제거할 필요가 없음)

# 결론

확실하고 이해하기 쉬웠기를 바랍니다.

# 만약 제작자로서 저를 지원하고 싶다면

<div class="content-ad"></div>

- 이 이야기에 대해 50번 박수를 쳐주세요
- 생각을 말씀해 주시는 댓글을 남겨주세요
- 이야기 중 가장 좋아하는 부분을 강조해주세요

감사합니다! 이 작은 행동들이 큰 도움이 되고, 정말 감사드립니다!

YouTube: https://www.youtube.com/@zlliu246

LinkedIn: https://www.linkedin.com/in/zlliu/

<div class="content-ad"></div>

제 Ebooks: [https://zlliu.co/ebooks](https://zlliu.co/ebooks)