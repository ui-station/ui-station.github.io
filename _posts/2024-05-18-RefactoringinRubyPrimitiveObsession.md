---
title: "루비에서의 리팩터링  기본적 허락"
description: ""
coverImage: "/assets/img/2024-05-18-RefactoringinRubyPrimitiveObsession_0.png"
date: 2024-05-18 15:19
ogImage: 
  url: /assets/img/2024-05-18-RefactoringinRubyPrimitiveObsession_0.png
tag: Tech
originalTitle: "Refactoring in Ruby | Primitive Obsession"
link: "https://medium.com/@kroolar/refactoring-in-ruby-primitive-obsession-3d38c702db03"
---


Primitive Obsession은 도메인 개념을 나타내기 위해 전용 클래스를 만들기보다 문자열, 숫자 또는 배열과 같은 기본 데이터 유형을 과도하게 사용하는 것을 의미합니다. 이는 이해하기 어려우며 유지보수 및 확장이 어려운 코드로 이어질 수 있습니다.

![image](/assets/img/2024-05-18-RefactoringinRubyPrimitiveObsession_0.png)

Ruby 언어에 관심이 있다면, 해당 언어에서 리팩터링 및 디자인 패턴에 대해 더 많이 알아볼 수 있습니다: [https://rubyhub.vercel.app/](https://rubyhub.vercel.app/). 현재 웹사이트는 준공 중이지만 미래에 더 많은 주제가 등장할 것입니다.

# 문제들

<div class="content-ad"></div>

- 코드 중복 - 코드베이스 전체에서 프리미티브 데이터 유형(예: 문자열 또는 해시)을 조작하는 동일한 로직이 반복되면 코드 중복이 발생할 수 있습니다.
- 의미 부족 - 문자열이나 정수와 같은 프리미티브 데이터 유형에는 의미적 의미가 부족합니다.
- 표현 능력 제한 - 프리미티브 데이터 유형은 사용자 정의 클래스와 비교하여 제한된 표현 능력을 제공합니다. 도메인 개념을 위해 전용 클래스를 만들면 속성과 메서드에 의미 있는 이름을 제공하여 코드를 자기 설명적으로 만들고 이해하기 쉽게 할 수 있습니다.
- 확장성에 대한 어려움 - 프리미티브 데이터 유형을 사용할 때 도메인 개념과 관련된 동작을 확장하거나 새 기능을 추가하는 것이 어려울 수 있습니다.
- 오류가 발생하기 쉬운 데이터 조작 - 프리미티브 데이터 유형을 직접 조작하는 것은 복잡한 데이터 구조나 비즈니스 규칙을 다룰 때 특히 오류가 발생할 수 있습니다.
- 테스트 복잡성 - 프리미티브 데이터 유형을 많이 의존하는 코드를 테스트하는 것은 복잡할 수 있습니다. 이러한 유형과의 다양한 상호작용을 목업하거나 스텁 처리해야 합니다.

# 실제 예시

이 코드 냄새를 나타내는 가장 일반적인 데이터는 전화 번호나 금액과 같은 데이터입니다. 처음에는 일반 변수에 할당하지만 코드를 개발하는 과정에서 그들에게 더 많은 기능을 추가해야 한다는 것을 알게 됩니다.

달러로 급여를 받는다고 상상해보세요(상상할 필요 없는 경우 제외). 스페인에서 집을 사려고 하는데, 그 집의 가격이 유로로 설정되어 있습니다. 그런 집을 구입하는 데 몇 달이 걸릴지 계산할 수 있는 간단한 프로그램을 작성해 봅시다.

<div class="content-ad"></div>


# 달러로 표시된 당신의 월급
salary = 5000

# 유로로 표시된 집값
house_cost = 100,000

eur_to_usd_rate = 1.09

((house_cost * eur_to_usd_rate) / salary).ceil # => 21


우리는 보듯이, 이 코드는 그리 좋아보이지 않습니다. 어떤 값이 어떤 통화에 있는지 알기 위해서는 코드에 추가적인 주석을 달 필요가 있습니다. 또한 값을 다른 통화로 변환할 수 있는 변수를 정의해야 합니다.

계속 이 프로그램을 작업하기 쉽게 만들기 위해, 우리가 돈을 보다 쉽게 다룰 수 있는 새로운 클래스를 생성해야 할 것입니다.


class Money
  attr_reader :amount, :currency

  def initialize(amount, currency)
    @amount = amount
    @currency = currency
  end

  def dollar?
    currency == "$"
  end

  def euro?
    currency == "€"
  end

  def to_euro
    return amount if euro?

    amount * 0.92
  end
  
  def to_dollar
    return amount if dollar?

    amount * 1.09
  end
end


<div class="content-ad"></div>

이제 새 클래스를 사용해 보겠습니다.

```js
house_cost = Money.new 100_000, "€"
salary = Money.new 5000, "$"

(house_cost.to_dollar / salary).ceil # => 21
```

# 장단점

## 장점

<div class="content-ad"></div>

- 가독성과 표현력 향상 - 도메인별 클래스는 도메인 개념을 더 잘 반영하는 의미 있는 추상화를 제공합니다.
- 향상된 유형 안정성 - 도메인별 클래스는 행동과 유효성 검증 논리를 캡슐화하여 더 강력한 유형 안정성을 제공합니다.
- 중앙 집중화된 로직 - 기본 허영 주의의 리팩터링은 도메인 개념과 관련된 로직의 중앙 집중화를 가능하게 합니다.
- 유지 보수 및 확장 용이성 - 도메인별 클래스를 사용하면 도메인 로직의 변경을 한 곳에서 처리할 수 있어 유지 보수 및 확장이 용이해집니다.

## 단점

- 복잡성 증가 - 도메인별 클래스 도입은 코드베이스에 복잡성을 추가할 수 있습니다. 특히 제대로 관리되지 않을 경우입니다. 개발자는 추가된 추상화가 코드를 지나치게 복잡하게 만들지 않도록 주의해야 합니다.
- 학습 곡선 - 도메인별 클래스를 도입하는 것은 개발자들이 새로운 개념과 API를 학습할 필요가 있을 수 있습니다. 특히 도메인이나 사용된 설계 패턴에 익숙하지 않은 경우입니다.
- 성능 오버헤드 - 도메인별 클래스는 추가적인 유효성 검사나 형식 지정 논리와 관련된 경우 기본 유형을 사용하는 것과 비교하여 약간의 성능 오버헤드를 도입할 수 있습니다. 그러나 대부분의 경우 이러한 오버헤드는 무시될 수 있습니다.
- 과도한 공학 - 기본 허영 주의를 리팩터링할 때 너무 복잡한 추상화를 만들어서 응용 프로그램 요구 사항으로 정당화할 수 없는 경우에는 과도한 공학의 위험이 있습니다.
- 의존성 관리 - 도메인별 클래스를 도입하면 코드베이스의 다른 부분 간의 의존성이 증가할 수 있으므로 긴밀한 결합을 피하고 모듈성을 유지하기 위해 주의 깊게 관리해야 합니다.

![이미지](/assets/img/2024-05-18-RefactoringinRubyPrimitiveObsession_1.png)

<div class="content-ad"></div>

루비에서 리팩터링에 관한 책을 작업 중이에요. 이 주제에 관심이 있다면, 제 뉴스레터에 가입하실 수 있어요 📪️: [https://mailchi.mp/e3dd49dfada1/medium](https://mailchi.mp/e3dd49dfada1/medium). 제 구독자들은 출판 후 즉시 무료로 전자책 링크를 받을 수 있어요. 🆓