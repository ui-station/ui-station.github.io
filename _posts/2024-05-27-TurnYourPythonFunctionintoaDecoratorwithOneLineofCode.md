---
title: "한 줄의 코드로 Python 함수를 데코레이터로 바꿔보세요"
description: ""
coverImage: "/assets/img/2024-05-27-TurnYourPythonFunctionintoaDecoratorwithOneLineofCode_0.png"
date: 2024-05-27 16:18
ogImage: 
  url: /assets/img/2024-05-27-TurnYourPythonFunctionintoaDecoratorwithOneLineofCode_0.png
tag: Tech
originalTitle: "Turn Your Python Function into a Decorator with One Line of Code"
link: "https://medium.com/towards-data-science/turn-your-python-function-into-a-decorator-with-one-line-of-code-1ebd738f31c0"
---


```markdown
![이미지](/assets/img/2024-05-27-TurnYourPythonFunctionintoaDecoratorwithOneLineofCode_0.png)

데코레이터를 작성하고 싶지만 구문을 기억하지 못하시나요? 데코레이터는 많은 보일러플레이트 코드가 포함된 꽤 어려운 구문을 갖고 있습니다. 이 기사에서는 데코레이터를 작성하는 더 간단한 방법을 소개합니다. 이 새로운 방법은 훨씬 더 짧고 명확하며 가독성이 뛰어날 것입니다. 함께 코딩해봅시다!

# 데코레이터 작성의 기본 방법

아래 코드는 데코레이터를 생성하는 기본 방법입니다. 데코레이터로 래핑된 함수가 실행되는 시간을 측정합니다. 깊이 파고든 이 기사를 확인해보세요.
```

<div class="content-ad"></div>

```markdown
def timer(name:str) -> Callable:
    def decorator(func:Callable) -> Callable:
        @wraps(func)
        def decorator_implementation(*args, **kwargs):
            try:
                print(f"TIMER:   {name} start")
                strt = time.perf_counter()
                return func(*args, **kwargs)
            finally:
                print(f"TIMER:   {name} finished in {time.perf_counter() - strt}")
        return decorator_implementation
    return decorator
```

이렇게 하면 코드를 다음과 같이 사용할 수 있습니다:

```js
@timer(name="test")
def my_func(name:str, age:int) -> str:
    return f"{name} is {age} years old"

my_func(name="mike", age=34)
# TIMER:   test start
# mike is 34 years old
# TIMER:   test finished in 5.299999999998532e-06
```

## 이 접근 방식의 문제점은 무엇인가요?

<div class="content-ad"></div>

저는 개발자이며 개인적으로 데코레이터를 작성하는 방법을 항상 기억하지 못하고 이전 프로젝트에서 코드를 복사하여 붙여넣어야 합니다. 이는 3개의 중첩된 함수가 포함된 약간 읽기 어려운 구문 때문에 데코레이터를 이해하기 어렵게 만들기 때문이라고 생각합니다. 우리는 이를 어떻게 단순화할 수 있는지 알아보겠습니다.

# 데코레이터를 작성하는 더 쉬운 방법

아래 구현은 이전 섹션의 데코레이터와 정확히 동일한 작업을 하지만 한 가지 함수만 사용합니다. 이는 훨씬 더 읽기 쉽고 @contextmanager 데코레이터를 추가하고 함수를 생성기로 변환하는 것으로 처리됩니다.

```python
@contextmanager
def timer(name:str) -> Generator:
    try:
        print(f"TIMER:   {name} start")
        strt = time.perf_counter()
        yield
    finally:
        print(f"TIMER:   {name} finished in {time.perf_counter() - strt}")
```

<div class="content-ad"></div>

모두 같은 방식으로 함수를 사용할 수 있어요:

```js
@timer(name="AS DEC")
def my_func(name:str, age:int) -> str:
    return f"{name}은(는) {age}살이야"


my_func(name="마이크", age=34)
# TIMER:   AS DEC 시작
# 마이크은(는) 34살이야
# TIMER:   AS DEC 5.399999999995686e-06초 내에 완료됨
```

## 컨텍스트 매니저로서의 기능

개인적으로 새로운 함수가 더 읽기 쉽고 이해하기 쉽다고 생각해요. 몇 가지 간단한 변경이 필요하지만 그만큼 다양한 기능을 제공해요. 함수에 데코레이터를 적용하는 것이 훨씬 쉬워지고 데코레이터 함수(예: 위의 timer)를 데코레이터 및 컨텍스트 매니저로 모두 사용할 수도 있어요.

<div class="content-ad"></div>

```markdown
```js
타이머와 함께(context manager와 함께)

우리는 동일한 데코레이터 함수를 데코레이터 및 context-manager로도 사용할 수 있습니다.

```js
as ctx라는 이름의 타이머와 함께:
    fn_with_ctx_decorator(name="john", age=42)
    print("컨텍스트 매니저 내부")

# 타이머:  as ctx 시작
# 타이머:  AS DEC 시작
# john은 42살입니다
# 타이머:  AS DEC가 3.7000000000023126e-06초에 완료되었습니다
# 컨텍스트 매니저 내부
# 타이머:  as ctx가 3.0000000000002247e-05초에 완료되었습니다
``` 
```

<div class="content-ad"></div>

# 어떻게 작동합니까?

내부적으로 contextlib은 @contextmanager를 사용하여 우리의 데커레이터 함수를 감싸서 데커레이터와 컨텍스트 매니저 역할을 하는 객체로 만들어줍니다. 이 작업 방식은 꽤 기술적이고 매우 흥미로우며 독립된 기사가 필요합니다. 계속 따라와주세요!

이 기사의 범위에서는 contextlib이 @contextmanager 데코레이터를 사용하여 데커레이터 함수를 데커레이터와 컨텍스트 매니저로 모두 사용할 수 있는 객체로 변환한다는 것만 알면 됩니다.

## 단점

<div class="content-ad"></div>

새 데코레이터는 많은 기능을 제공하지만 몇 가지 단점이 있습니다. 그 중 가장 중요한 것은 데코레이터 함수 내에서 실제로 데코레이션하는 함수에 액세스할 수 없다는 점입니다. 또한 해당 함수의 args와 kwargs에 액세스할 수도 없습니다. 이로 인해 이러한 변수를 수정할 수 없지만 제 생각에는 이를 드물게 해야 하는 것입니다.

두 번째 단점은 데코레이터 함수가 제너레이터 함수여야 한다는 점입니다. 이는 해당 함수가 본문에서 어딘가에 yield해야 한다는 의미입니다. 이로 인해 코드를 다시 작성해야 할 수도 있습니다.

# 결론

@contextmanager를 사용하면 데코레이터를 쉽고 가독성있게 작성할 수 있습니다. 많은 쓰기 장치를 처리해주며 심지어 콘텍스트 매니저 역할도 수행합니다. 그러나 이 자동화와 "하드코딩된 마법"으로 인해 함수와 인수에 액세스할 수 있는 제어를 일부 상실하게 됩니다.

<div class="content-ad"></div>

contextlib은 내부에서 작동하는 방식이 상당히 복잡하며 별도의 기사가 필요하므로 관심이 있다면 저를 따라오세요!

이 기사가 제가 희망하는 대로 명확하게 전달되었기를 바라지만, 그렇지 않은 경우 추가 설명이 필요하다면 알려주세요. 그동안 다른 주제들에 대한 제 다른 기사들도 확인해보세요:

- 절대 초보자를 위한 Git: 비디오 게임의 도움으로 Git 이해하기
- 나만의 Python 패키지 작성 및 게시
- FastAPI를 사용해 5줄의 코드로 빠르고 자동 문서화되고 유지보수 가능한 쉽게 사용할 수 있는 Python API 만들기

즐거운 코딩 되세요!

<div class="content-ad"></div>

- Mike

P.S: 제 하는 일 좋아하시나요? 제 팔로우 해주세요!