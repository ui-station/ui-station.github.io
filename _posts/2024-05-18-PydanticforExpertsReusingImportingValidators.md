---
title: "파이다닉Pydantic 전문가를 위한 Validator 재사용과 가져오기"
description: ""
coverImage: "/assets/img/2024-05-18-PydanticforExpertsReusingImportingValidators_0.png"
date: 2024-05-18 15:52
ogImage:
  url: /assets/img/2024-05-18-PydanticforExpertsReusingImportingValidators_0.png
tag: Tech
originalTitle: "Pydantic for Experts: Reusing , Importing Validators"
link: "https://medium.com/data-engineer-things/pydantic-for-experts-reusing-importing-validators-2a4300bdcc81"
---

## 파이썬 모델들 사이에서 검증을 재사용하고 가져오는 고급 기술들.

## 축하드려요 🎉

만약 이 글을 읽고 계신다면, 여러분은 아마 파이썬 기술을 향상시키고 몇 가지 고급 pydantic 기능들을 배우고 싶을 것입니다.

![이미지](/assets/img/2024-05-18-PydanticforExpertsReusingImportingValidators_0.png)

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

# 배경

일반적으로 스키마 강제가 데이터베이스 레이어에서 이루어졌습니다 — 델타나 아이스버그와 같은 오픈 테이블 형식의 프레임워크도 스키마를 강제할 수 있습니다.

Pydantic은 스키마 강제에 대한 새로운 사고 방식을 소개합니다 — 정책 및 조작으로써 사용됩니다. 다시 말해, 스키마는 다양한 입력에서 변환될 수 있다고 가정하면, 이러한 모든 시도에 실패하면, 해당 객체는 유효하지 않습니다. 이는 꽤 멋진 패러다임이며, 더 많은 데이터 도구가 이를 따르기를 원합니다.

## Pydantic은 스키마 강제를 위해 3가지 방법을 제공합니다:

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

- 엄격 모드:
  객체에 대해 강제 변환을 수행하지 않습니다 → 예: '"number": "123"'은 실패할 것입니다, 왜냐하면 "123"은 숫자가 아니라 문자열이기 때문입니다.
- 관대 모드:
  "합리적 형식"에서 예상 형식으로 변환을 수행합니다. 예를 들어 위의 예시는 성공할 것입니다 → 대체: 정수 필드의 부울 값은 0 또는 1로 변환됩니다.
- 사용자 정의 유효성 검사기:
  내부적인 pydantic 유효성 검사 전후에 사용자 정의 유효성 검사 논리를 수행할 수 있습니다. 예를 들어, float(`inf`)를 999_999_999으로 대체할 수 있습니다. 참고: 사용자 정의 유효성 검사기는 엄격 모드 또는 관대 모드와 함께 작동합니다.

사용자 정의 유효성 검사기에 관한 ↓

## pydantic 철학은 간단합니다:

- 데이터는 예상된 유형을 갖습니다.
- 데이터는 다양한 입력 형식으로부터 예상된 유형으로 변환되어야 합니다.
- 일반적 (또는 "합리적") 변환은 기본 동작입니다 (관대 모드와 함께).
- 사용자 지정 변환은 사용자 정의 동작을 처리하기 위해 구현될 수 있습니다.

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

# 소개

Pydantic의 유효성 검사 도구는 데이터 작업 시 강력한 도구입니다. 유효성 검사 도구를 재사용하면서 코드를 DRY 상태로 유지하는 것은 중요한 기술입니다. 이 문서에서는 유효성 검사 도구를 재사용하는 여러 방법에 대해 논의할 것입니다.

# 문제 명시:

제목 형식이어야 하는 필드가 있다고 가정해 봅시다. 이러한 제약 조건을 필드 수준에서 다음과 같이 강제할 수 있습니다:

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
from pydantic import field_validator, BaseModel


class Model(BaseModel):
    first_name: str = "Samuel"

    @field_validator('first_name')
    def must_be_title_case(cls, v: str) -> str:
        if v != v.title():
            raise ValueError("must be title cased")
        return v
```

여러 필드를 유효성 검사하는 것도 매우 간단합니다:

```python
from pydantic import field_validator, BaseModel

class Model(BaseModel):
    first_name: str = "Samuel"
    last_name: str = "Colvin"

    @field_validator('first_name', 'last_name')
    def must_be_title_case(cls, v: str) -> str:
```

그러나 여러 모델에서 여러 필드에 걸쳐 이 유효성 검사기를 공유하려면 어떻게 해야 할까요?

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

# 솔루션 개요:

밸리데이터를 재사용하는 여러 기술이 있습니다:

- 재사용 가능한 밸리데이터
- 주석이 달린 밸리데이터
- 클래스 메서드

# 옵션 1. 모델 간에 재사용할 수 있는 하나의 유효성 검사 함수 정의하기:

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

이는 프로젝트 내에서 유효성 검사기를 가져와 재사용할 수 있도록 가능하게 합니다.

```python
from pydantic import field_validator, BaseModel


def must_be_title_case(v: str) -> str:
    """프로젝트 전체에서 사용할 수 있는 유효성 검사기"""
    if v != v.title():
        raise ValueError("첫 글자는 대문자로 입력되어야 합니다")
    return v


class Model1(BaseModel):
    first_name: str = "Samuel"
    last_name: str = "Colvin"

    validate_fields = field_validator("first_name", "last_name")(must_be_title_case)


class Model2(Model1):
    """Model1에서 필드를 상속받음"""
    organization: str = "Pydantic"

    validate_fields = field_validator("organization")(must_be_title_case)
```

원하는 경우 자식 클래스에서만 필드 유효성 검사를 정의할 수 있습니다:

```python
class Model1(BaseModel):
    first_name: str = "Samuel"
    last_name: str = "Colvin"

class Model2(Model1):
    """Model1에서 필드를 상속받음"""
    organization: str = "Pydantic"

    validate_fields = field_validator("first_name", "last_name", "organization")(must_be_title_case)
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

다음은 더 많은 예시를 보여드리겠습니다:

```js
from pydantic import BaseModel, field_validator, ValidationError


def must_be_title_case(v: str) -> str:
    """이것은 전체에서 사용할 수 있는 유효성 검사기입니다."""
    if v != v.title():
        raise ValueError("첫 글자가 대문자이어야 합니다.")
    return v


class Model1(BaseModel):
    first_name: str = "Samuel"
    last_name: str = "Colvin"

    # 자식에게 유효성 검사기가 전달되지 않는 문제를 피하기 위해 데코레이터로 정의됨
    @field_validator('first_name', 'last_name')
    @classmethod
    def wrap_must_be_title_case(cls, v):
        return must_be_title_case(v)


def cannot_contain_letter_L(v):
    """임의의 규칙입니다."""
    if 'L' in v.upper():
        raise ValueError
    return v

class Model2(Model1):
    """Model1로부터 필드를 상속받습니다."""
    organization: str = "Pydantic"

    validate_fields = field_validator("organization", "last_name")(cannot_contain_letter_L)


for v in [
    "colvin",  # 부모의 유효성 검사에 실패할 것이며, 첫 글자가 대문자여야 합니다.
    "Colvin"   # 자식의 유효성 검사에 실패할 것이며, L을 포함해서는 안 됩니다.
]:
  try:

    m = Model2(last_name="colvin")
  except ValidationError as e:
    print(e)
```

여기서 유효성 검사기들은 정의된 순서대로 실행됩니다. 즉, 부모의 유효성 검사기가 먼저 실행되고, 자식의 유효성 검사기가 나중에 실행됩니다. 부모의 유효성 검사에서 실패하면 자식의 유효성 검사기는 실행되지 않습니다.

# 옵션 2. Annotated Validator로 유효성 검사 정의하기:

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

이것은 재사용 가능한 유효성 검사된 "타입"을 정의할 수 있게 해줍니다. 매우 높은 유연성을 제공합니다.

```js
from typing_extensions import Annotated

from pydantic import BaseModel, ValidationError, field_validator
from pydantic.functional_validators import AfterValidator


# 이전과 같은 함수
def must_be_title_case(v: str) -> str:
    """전체에서 사용할 유효성 검사기"""
    if v != v.title():
        raise ValueError("제목이어야 함")
    return v


# Annotated (유효성 검사된) 타입 정의:
MySpecialString = Annotated[str, AfterValidator(must_be_title_case)]


# 이제 모델에서 사용자 정의 타입을 사용합니다.
class Model1(BaseModel):
    first_name: MySpecialString = "Samuel"
    last_name: MySpecialString = "Colvin"


class Model2(Model1):
    organization: MySpecialString = "Pydantic"
```

여기에서 어노테이션된 타입에서 무슨 일이 일어나는지 간단히 설명해 드리겠습니다:

- 기본 타입은 문자열입니다.
- Pydantic은 입력 값을 문자열로 변환하려고 할 것입니다. 이것이 "핵심 유효성 검사" 단계로 간주됩니다.
- Pydantic의 유효성 검사 후, 우리의 유효성 검사기 함수를 실행할 것입니다 (AfterValidator로 선언됨) — 이것이 성공하면 반환된 값이 설정됩니다.
- 대신 어노테이션에서 BeforeValidator를 선언하여 값을 변환하기 전에 함수를 실행할 수도 있습니다.

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

## 사용자 정의 유형 가져오기:

일반적인 디자인 원칙으로, 내가 주로 지지하는 것은 사용자 정의 유형을 자체 서브모듈에 만드는 것입니다. 내가 설계했다면 다음과 같은 디렉토리 구조가 될 수 있습니다:

```js
project-root
|
├── models
│   ├── base.py       <---- 프로젝트당 사용자 정의 기본 클래스를 사용하는 편입니다
|   |
│   ├── custom_types  <---- 사용자 정의 유형이 이곳에 위치합니다
│   │   └── strings.py
│   │
│   ├── request_models
│   │   └── people.py
│   │
│   ├── response_models
│   │   └── people.py
│   │
│   └── interfaces
│       └── aws.py
|
└── main.py  <---- models에서 필요한 것을 가져옵니다
```

request_models.people에 사용자 정의 유형이 필요한 경우, 다음과 같이 코드를 작성할 수 있습니다:

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
from models.base import MyCustomBaseClass
from models.custom_types.strings import MyCustomString


class PersonRequestModel(MyCustomBaseClass):
    first_name: MyCustomString
    ...
```

위에서 보듯이 주석이 달린 유형 내에 포함된 유효성 검사기를 가져오는 것은 정말 쉽고 관심사의 청결한 분리를 강제할 수 있습니다.

마찬가지로 유효성 검사기에 대한 동일한 구분된 서브모듈을 만들 수 있지만, 그들이 어떻게 구성되어야 하는지 및 어떤 종류여야 하는지가 덜 명확해집니다.

# 옵션 3. 주석이 달린 유형 상속하기

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

특별한 유형 MySpecialString을 상속하고 유효성 검사기를 추가하려면 다음과 같이 할 수 있습니다:

```js
def cannot_contain_letter_L(v):
    if 'L' in v.upper():
        raise ValueError
    return v


MySpecialString2 = Annotated[
  MySpecialString, AfterValidator(cannot_contain_letter_L)
]


class Model1(BaseModel):
    first_name: MySpecialString2 = "Samuel"
    last_name: MySpecialString2 = "Colvin"
```

상속 수준에 따라 검증 순서가 실행되며 루트부터 시작합니다. 동작의 추가 설명은 위의 테스트 케이스를 참고하세요.

특히, 기존 동작을 추가해야 할 대규모 pydantic 모델 코드베이스와 상호 작용할 때 이 기능이 매우 유용합니다.

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

# 옵션 4. 상속된 모델 유효성 검사기(Class validators)를 사용하여 필드를 서로 비교하십시오.

이전 방법들은 여러 필드를 개별적으로 유효성을 검사하는 방법을 보여줍니다. 그렇다면 2개의 값을 비교하려면 어떻게 해야 할까요?

여러 필드를 비교하는 가장 좋은 방법은 model_validator(즉, v1에서 root_validator)를 사용하는겁니다:

```js
class ValidatorBase(BaseModel):
    """재사용되는 유효성 검사기를 선언하는 데 사용되는 기본 클래스"""

    @model_validator(mode="after")
    def validate_fields(self):
        if self.organization == self.last_name:
            raise ValueError()
        return self


class Model1(ValidatorBase):
    first_name: str = "Samuel"
    last_name: str = "Colvin"
    organization: str = "Pydantic"

try:
  m = Model1(last_name="Pydantic", organization="Pydantic")
except ValidationError as e:
  print(e)
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

해당 유효성 검사를 재사용하는 것은 ValidatorBase를 상속받는 것만으로 간단합니다.

## 상속받은 클래스 유효성 검사 가져오기

위와 유사하게, 기본 클래스를 중앙 위치에 구성하고 필요할 때 가져올 수 있습니다. 실행 방법이 명확하기 때문에 더 이상 자세히 설명하지 않겠습니다.

# 옵션 5. 일반 모델 유효성 검사의 매핑 및 사적 속성을 통한 적용

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

또 다른 디자인 접근 방식은 일반적인 모델 유효성 검사자의 매핑을 만들어 키워드를 통해 모델에 할당하는 것입니다.

```js
# 사용자 정의 규칙 생성
def rule_1(self):
    if self.organization == self.last_name:
        raise ValueError()
    return self

def rule_2(self):
    ...
    return self

VALIDATOR_MAPPING = {
    "rule_1": rule_1,
    "rule_2": rule_2
}

class ValidationBase(BaseModel):
    """모델 유효성 검사를 강제하는 기본 클래스"""
    ...

    @model_validator(mode="after")
    def validate_fields(self):
        for rule in self._rules:
            # validators가 값을 변경할 경우 self를 변경합니다.
            self = VALIDATOR_MAPPING[rule](self)

        return self

class Model1(ValidationBase):
    # 또는 집합 내에서 규칙을 함수로 저장할 수 있습니다.
    _rules = {'rule_1', 'rule_2'}

    first_name: str = "Samuel"
    last_name: str = "Colvin"
    organization: str = "Pydantic"
```

이 방식에는 암시적인 면이 많이 있습니다. 모델이 모든 예상 필드를 가지고 있지 않으면 별도의 유효성 검사자에서 실패합니다. (이 경우 getattr을 사용하여 문제를 해결할 수 있습니다) 더 나아가 매핑을 통해 유효성 검사자를 적용하면 성능 저하와 함께 검증이 순차적으로 실행되어 동일한 모델에 대한 여러 유효성 오류가 발생하지 않습니다. 모델 유효성 검사를 필드 유효성 검사와 분리하는 솔루션을 구현하는 것이 더 바람직하지만 항상 가능한 것은 아닙니다.

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

데이터 작업 시 Validator는 중요한 도구입니다. Pydantic은 사용자 정의 검증을 구현하는 여러 방법을 제공합니다. 이 기사에서는 사용자 지정 Validator를 다시 정의하지 않고 동일한 검증 논리를 구현하는 방법을 몇 가지 살펴보았습니다.

# 전문가를 위한 Pydantic 시리즈:

- 이 Pydantic V2의 획기적인 기능들을 보기 전까지 다른 코드 줄을 작성하지 마세요.
  V2에서 소개된 몇 가지 기능에 대한 개요.
- 전문가를 위한 Pydantic: Pydantic V2에서의 판별 된 연합
  모델 선택을 구별하다.

## 참고:

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

- Pydantic 문서
- 이 게시물을 영감을 준 스택 오버플로우 질문: Pydantic 모델의 여러 필드를 유효성 검사하는 방법은?
- Pandera 문서

마지막으로 여러분에게 질문이 하나 있습니다:
다음에 대해 어떤 내용을 작성해 드리면 좋을까요? 아래에 댓글을 남겨주세요.
