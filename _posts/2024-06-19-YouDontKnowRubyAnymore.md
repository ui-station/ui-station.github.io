---
title: "루비를 잘 몰라요 더 이상"
description: ""
coverImage: "/assets/img/2024-06-19-YouDontKnowRubyAnymore_0.png"
date: 2024-06-19 10:14
ogImage: 
  url: /assets/img/2024-06-19-YouDontKnowRubyAnymore_0.png
tag: Tech
originalTitle: "You Don’t Know Ruby (Anymore!)"
link: "https://medium.com/@ihcnemed/you-dont-know-ruby-anymore-30da3b47d0e0"
---


조심하세요! 시니어 루비 개발자분들께는 마음을 다치게 할 수도 있는 기사입니다.

루비는 모든 현대 프로그래밍 언어처럼 발전하고 있습니다. 커뮤니티는 계속해서 새로운 기능을 소개하고 있지만 모두가 알지 못하는 것이 있습니다. 새로운 기능을 따라가지 않고 채택하지 않는 개발자들은 아마도 루비 3.3 시대에도 루비 1.9.2 코드를 작성하고 있을지도 모릅니다!

루비의 성숙함과 오랜 역사에도 불구하고 사용자들 사이에는 오래된 관행을 고수하려는 경향이 있습니다. 새로운 기능을 받아들이기를 꺼리는 이러한 저항은 유감스럽습니다. 새로운 기능을 도입하는 것이 루비의 관련성을 유지하고 현대 프로그래밍 언어 중 하나로 자리 잡는 데 도움을 준다는 것을 명심해야 합니다.

최근 루비 버전에서 중요한 새로운 기능과 개선 사항들을 소개하겠습니다. 더불어 커뮤니티에서는 아직 충분히 주목받지 못한 오래된 기능들도 소개할 예정입니다.

<div class="content-ad"></div>

# RBS (루비 서명)

루비 코드에서 유형 안전성을 보장하려면 방대한 테스트와 문서 작업이 필요했고, 이로 인해 잠재적인 런타임 오류와 생산성 저하가 발생했죠.

RBS (루비 서명)는 루비 3.1과 함께 소개된 새로운 언어로, 루비 코드의 유형과 인터페이스를 설명하는 데 사용됩니다. 이를 통해 정적 유형 확인과 IDE 자동 완성이 가능해져 코드 품질과 개발자 생산성이 향상됩니다.

Sorbet 및 유사한 커뮤니티 주도의 대안이 존재하지만, RBS는 Matz에 의해 지지된 점에서 돋보입니다. 별도의 젬으로 패키징되었지만, 프로그래밍 언어 환경에서 유형 확인 트렌드를 받아들이는 공식 방법으로 간주됩니다.

<div class="content-ad"></div>

```js
# Point.rbs
class Point
  attr_reader x: Integer
  attr_reader y: Integer

  def initialize: (x: Integer, y: Integer) -> void
end

# Point.rb
class Point
  attr_reader :x, :y

  def initialize(x, y)
    @x = x
    @y = y
  end
end
```

Steep은 루비를 위한 정적 타입 검사 도구로, 타입 안전성을 보장하고 개발 과정에서 오류를 일찍 발견하는 데 도움을 줄 수 있어요.

일반적인 GitHub Actions CI/CD 파이프라인에서는 Steep을 테스트 단계의 일부로 설정할 수 있어요. 이를 통해 루비 코드베이스를 RBS 파일에 대해 분석하고, 타입 오류를 확인하여 개발자에게 피드백을 제공할 수 있어요.

```js
# .github/workflows/ci.yml
name: CI

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Setup Ruby
        uses: actions/setup-ruby@v1
        with:
          ruby-version: '3.3'

      - name: Install dependencies
        run: bundle install

      - name: Run Steep
        run: bundle exec steep check
```

<div class="content-ad"></div>

CI/CD 파이프라인에 Steep를 통합하면 코드 품질을 유지하고 유형 안정성을 강화하여 런타임 오류를 방지할 수 있습니다. 이는 최상의 관행을 촉진하며 Ruby 애플리케이션의 신뢰성을 향상시킵니다.

RBS 도입은 학습과 도구 설정에 상당한 투자가 필요할 수 있습니다. 또한 코드베이스 옆에 유형 주석을 유지하는 데 추가 작업을 도입할 수 있습니다.

# 패턴 매칭

패턴 매칭은 Ruby 2.7에서 가장 기대되는 기능 중 하나였습니다. Ruby 창시자인 매츠는 Ruby를 더 표현력 있게 만들고 현대 프로그래밍 언어 트렌드에 맞추기 위해 패턴 매칭을 포함하고 싶다고 언급했습니다. 커뮤니티는 Ruby 메일링 리스트와 이슈 트래커에서 이에 대해 광범위히 논의했습니다. 해당 기능은 최종 포함되기 전에 여러 번의 반복과 수정을 거쳤습니다.

<div class="content-ad"></div>

패턴 매칭 이전에는 복잡한 데이터 구조를 비구조화하고 일치시키기 위해 다양한 if나 case 문을 사용하여 반복적이고 장황한 코드가 필요했습니다. 이로 인해 코드의 가독성과 유지보수성이 낮아지는 경우가 많았습니다.

루비 2.7부터 도입된 패턴 매칭은 루비 3.0에서 더 발전하여 더 간결하고 표현력 있는 데이터 비구조화와 일치를 가능케 합니다. 이로써 코드가 더 깔끔하고 유지보수가 편해집니다.

예시:

```ruby
def company_location_contact_id(company_location_id)
  query = <<~GRAPHQL
    query($company_location_id: ID!) {
      companyLocation(id: $company_location_id) {
        ...
      }
    }
  GRAPHQL

  response =
    @client.query(
      query:,
      variables: { company_location_id: "gid://shopify/CompanyLocation/#{company_location_id}" }
    ).body

  case response.deep_symbolize_keys
  in errors: [{ message: error_message }]
    Rollbar.error("#{error_message} for", company_location_id:)
  in data: { companyLocation: nil }
    Rollbar.error("Company location not found", company_location_id:)
  in data: { companyLocation: { roleAssignments: { edges: [{ node: { companyContact: { id: contact_id } } }] } } }
    contact_id
  end
end
```

<div class="content-ad"></div>

패턴 매칭을 사용하지 않으면, 코드는 일반적으로 중첩 조건문이나 응답 데이터의 수동 구문 분석으로 인한 오류를 유발할 수 있습니다. 같은 기능을 패턴 매칭 없이 어떻게 구현할 수 있는지 살펴봅시다:

```js
def 회사_위치_연락처_ID(회사_위치_ID)
  쿼리 = <<~GRAPHQL
    query($company_location_id: ID!) {
      companyLocation(id: $company_location_id) {
        ...
      }
    }
  GRAPHQL

  응답 = @client.query(
    query: 쿼리,
    variables: { company_location_id: "gid://shopify/CompanyLocation/#{회사_위치_ID}" }
  ).body

  데이터 = 응답.deep_symbolize_keys

  if 데이터.key?(:errors) && 데이터[:errors].is_a?(Array) && 데이터[:errors].first.key?(:message)
    오류_메시지 = 데이터[:errors].first[:message]
    Rollbar.error("#{오류_메시지} for", company_location_id: 회사_위치_ID)
  elsif 데이터.key?(:data) && 데이터[:data].is_a?(Hash) && 데이터[:data][:companyLocation].nil?
    Rollbar.error("회사 위치를 찾을 수 없음", company_location_id: 회사_위치_ID)
  elsif 데이터.key?(:data) && 데이터[:data].is_a?(Hash) &&
        데이터[:data][:companyLocation].is_a?(Hash) &&
        데이터[:data][:companyLocation][:roleAssignments].is_a?(Hash) &&
        데이터[:data][:companyLocation][:roleAssignments][:edges].is_a?(Array) &&
        데이터[:data][:companyLocation][:roleAssignments][:edges].first.is_a?(Hash) &&
        데이터[:data][:companyLocation][:roleAssignments][:edges].first[:node].is_a?(Hash) &&
        데이터[:data][:companyLocation][:roleAssignments][:edges].first[:node][:companyContact].is_a?(Hash) &&
        데이터[:data][:companyLocation][:roleAssignments][:edges].first[:node][:companyContact].key?(:id)
    데이터[:data][:companyLocation][:roleAssignments][:edges].first[:node][:companyContact][:id]
  end
end
```

패턴 매칭을 사용하지 않으면 코드가 상당히 장황하고 오류 발생 가능성이 높아집니다. 각 조건을 주의 깊게 확인하고 중첩해야 하므로 가독성 문제와 버그 발생 위험이 커집니다. 패턴 매칭을 통해 복잡한 데이터 구조를 해체하고 일치시키는 간결하고 가독성 있는 방법을 제공함으로써 이러한 프로세스를 간편화할 수 있습니다.

그러나 패턴 매칭이 과도하게 사용되거나 부적절하게 사용된 경우 복잡성을 증가시킬 수도 있습니다. 또한 개발자가 새로운 구문과 패턴을 익히고 채택 속도가 처음에는 느려질 수도 있습니다.

<div class="content-ad"></div>

# 원 라인 패턴 매칭:

해시나 배열의 구조 해체는 종종 여러 줄의 코드가 필요하여 간단한 할당이 다소 장황하고 복잡해지는 경우가 있습니다.

루비 2.7에서 소개된 원 라인 패턴 매칭은 구조 해체를 한 줄로 처리하여 코드를 보다 간결하고 가독성 있게 만들어줍니다.

```js
data = { user: { name: "Alice", details: { age: 25, city: "Paris" } } }
data => { user: { name:, details: { age:, city: } } }
puts "Name: #{name}, Age: #{age}, City: #{city}"
```

<div class="content-ad"></div>

한 줄 구문은 복잡한 패턴에 대해 가독성이 떨어질 수 있고, 개발자들은 새로운 구문을 완전히 활용하기 위해 익숙해져야 합니다.

**오른쪽 할당:**

이전 기능과 유사하게, `=` 연산자는 해체 및 패턴 매칭 없이 변수를 할당하는 방식으로 사용될 수 있으며, 코드의 흐름 방향과 더 자연스럽게 느낄 수 있는 다른 순서로 변수를 할당하는 방식입니다.

루비 3.0에서 소개된 오른쪽 할당은 더 읽기 쉽고 간결한 방식으로 바로 할당을 수행할 수 있도록 구문을 간단화합니다.

<div class="content-ad"></div>

```js
read_data() => user_data => { user: { name:, details: { age:, city: } }
save!(user_data)
puts "Name: #{name}, City: #{city}"
```

이 구문은 일부 개발자들에게 익숙하지 않고 혼란스러울 수 있습니다. 또한 복잡한 표현식에서 과도하게 사용하면 코드를 덜 읽기 쉽게 만들 수 있습니다.

# 개선 사항

핵심 클래스를 전역적으로 수정하는 것은 예상치 못한 부작용과 충돌을 야기할 수 있으며, 특히 대규모 코드베이스나 공유 라이브러리에서는 특히 그렇습니다.

<div class="content-ad"></div>

루비 2.0에서 소개된 Refinements는 코어 클래스에 범위 지정 수정을 할 수 있는 방법을 제공합니다. 이를 통해 변경 사항을 특정 컨텍스트로 제한하여 부작용의 위험을 줄일 수 있습니다.

```js
module ArrayExtensions
  refine Array do
    def to_hash
      Hash[*self.flatten]
    end
  end
end

class Converter
  using ArrayExtensions

  def self.convert(array)
    array.to_hash
  end
end

puts Converter.convert([[:key1, "value1"], [:key2, "value2"]])
```

이것은 비교적 오래된 (그러나 소중한) 기능으로, 너무 적은 코드베이스에서 사용된 것을 본 적이 있습니다. 단점이 있지만, 우리가 그것을 인식하고 있을 때 강력한 도구로 작용합니다.

Refinement를 사용할 때 중요한 함정 중 하나는, 코드가 실행되는 컨텍스트에서 Refinement가 활성화되어 있지 않을 때 예기치 못한 동작이 발생할 수 있다는 것입니다. Refinement는 특정 렉시컬 스코프 내의 코드에만 영향을 미치기 때문에, Refinement에 의존하는 코드는 실행되는 위치에 따라 다르게 동작할 수 있습니다. 이는 일관성이 떨어지고, 코드가 재사용되거나 다양한 컨텍스트에서 실행될 경우 진단하기 어려운 버그를 유발할 수 있습니다.

<div class="content-ad"></div>

그냥 알아두세요.

# Enumerator::Lazy

대량 컬렉션에 대해 연산을 체인화하는 것은 중간 배열이 생성되어 메모리와 처리 시간을 소모하여 비효율적일 수 있습니다.

루비 2.0에서 소개된 지연(Leazy) 열거자는 중간 배열을 만들지 않고 효율적인 연산 체인을 가능케 합니다. 이는 대량 컬렉션에 대해 더 나은 성능과 낮은 메모리 사용량을 가져옵니다.

<div class="content-ad"></div>


lazy_numbers = (1..Float::INFINITY).lazy
result = lazy_numbers.select { |n| n % 2 == 0 }
                     .map { |n| n * n }
                     .take(10)
                     .to_a

puts result.inspect


단점: 지연 열거자는 연산이 체인의 끝까지 연기되기 때문에 디버깅이 더 어려울 수 있습니다. 또한, 모든 열거자 메서드가 지연 열거자에서 사용 가능한 것은 아닙니다.

# Enumerator::Chain

여러 열거 가능한 항목을 연결하는 경우 연결이 필요했으며 표현력이 낮아 종종 가독성이 떨어지는 코드로 이어졌습니다.


<div class="content-ad"></div>

루비 2.6에 소개된 Enumerator::Chain은 열거자를 체인으로 묶는 깔끔하고 표현력 있는 방법을 제공하여 가독성과 유지보수성을 향상시킵니다.

```js
evens = (2..10).step(2)
odds = (1..9).step(2)
combined = evens.each.chain(odds.each)

puts combined.to_a.inspect
```

단점: 체이닝을 간단하게 만들지만, 개발자가 배워야 할 다른 개념을 소개할 수 있습니다. 복잡한 체이닝 시나리오에서 과도하게 사용될 경우 데이터의 원천을 가리거나 불분명하게 만들 수도 있습니다.

# Module#prepend

<div class="content-ad"></div>

테이블 태그를 Markdown 형식으로 바꾸는 것이 조금 더 간단하게 됩니다.

<div class="content-ad"></div>

# 끝이 없는 메서드 정의

단순한 메서드를 정의하는 데 여러 줄의 코드가 필요했었습니다. 메서드 본문이 하나의 표현식인 경우에도 말이죠. “;”를 사용하여 한 줄 메서드 정의를 작성하는 방법은 있었지만, 그렇게 하면 보기 좋지 않았습니다.

```js
def power(base, exponent); base**exponent ; end
```

Ruby 3.0에서 소개된 끝이 없는 메서드 정의는 단일 표현식을 반환하는 메서드에 대해 간결한 구문을 제공하여, 보일러플레이트를 줄이고 가독성을 향상시킵니다.

<div class="content-ad"></div>

```js
class Calculator
  def add(a, b) = a + b
  def multiply(a, b) = a * b
  def power(base, exponent) = base**exponent
end

calc = Calculator.new
puts calc.add(2, 3)       # 결과: 5
puts calc.multiply(4, 5)  # 결과: 20
puts calc.power(2, 3)     # 결과: 8
```

새로운 구문은 일부 개발자에게는 익숙하지 않을 수 있어 혼란스러울 수 있습니다. 또한, 더 복잡한 메소드에 과도하게 사용될 경우 가독성이 떨어질 수도 있습니다.

# `it` 매개변수

블록 변수를 명시적으로 사용하는 것은, 특히 map과 같이 단일 매개변수가 필요한 Enumerable 메소드들에 대한 간단한 작업에 대해 최대한 설명하기 위한 경우, 반복적이고 간결하지 않은 코드를 작성할 수 있습니다.


<div class="content-ad"></div>

Ruby 3.0에서 소개된 it 매개변수는 기본 블록 매개변수를 제공하여 블록 구문을 간략화하며 단순한 작업에 대해 코드를 더 간결하고 가독성 있게 만들어줍니다.

```js
["apple", "banana", "cherry"].map { it.upcase.reverse }
```

Ruby 2.7에서는 번호 매개변수(_1, _2 등)가 실험적 기능으로 도입되어 블록 구문을 간소화하고 인수를 자동으로 참조함으로써 목표를 달성했습니다. 그러나 커뮤니티 내에서 읽기 어려움과 혼란 가능성에 대한 우려가 제기되었고, 이는 기본 it 매개변수 주변의 토론과 유사합니다. Rubocop은 매개변수 _1 이외의 번호 매개변수 사용을 방지하도록 설정을 기본값으로 하며, 이는 it를 사용하는 것과 정확히 동일하지만 더 좋은 이름을 가지고 있습니다.

단점: it의 암시적 성격은 코드를 더 복잡한 작업에 대해 읽기 어렵게 만들 수 있습니다. 또한 개발자가 배워야 할 새로운 규칙을 도입합니다.

<div class="content-ad"></div>

# String#casecmp?

대소문자를 구별하지 않는 문자열 비교를 위해서는 복잡한 코드를 작성해야 했는데, 그것은 종종 여러 메서드 호출이나 정규 표현식을 포함하여 가독성을 떨어뜨리곤 했습니다.

Ruby 2.4에서 소개된 String#casecmp?는 대소문자를 구분하지 않는 비교를 위한 간편한 메서드를 제공하여 코드를 더 간결하고 표현력 있게 만들어줍니다.

```ruby
strings = ["Hello", "world", "HELLO"]
matches = strings.select { |s| s.casecmp?("hello") }
puts matches.inspect # => ["Hello", "HELLO"]
```

<div class="content-ad"></div>

# Object#yield_self 그리고 then

객체에 대한 연산을 연쇄적으로 사용할 때 중간 변수 없이는 가독성이 떨어질 수 있어 더 장황하고 부자연스러운 코드가 될 수 있습니다. 호출이 중첩되어 있을 때 코드 읽기 방향도 자연스럽지 않게 느껴질 수 있습니다.

루비 2.5에서 소개된 Object#yield_self와 Ruby 3.0에서 소개된 then 별칭을 사용하면 더 깔끔한 연산 체이닝이 가능해져 코드 가독성과 유연성이 향상됩니다.

```js
result = "hello"
         .yield_self { |str| str.upcase }
         .then { |str| str + " WORLD" }

puts result  # Output: "HELLO WORLD"
```

<div class="content-ad"></div>

**단점:** 이러한 방법은 쉽게 오용되거나 과도하게 사용될 수 있어서, 체인이 너무 길거나 복잡해지면 코드가 덜 가독성이 될 수 있습니다.

# 끝없는 범위:

상한 또는 하한이 없는 범위를 정의하는 데는 Float::INFINITY 또는 다른 해결책이 필요했는데, 이는 번거로울 수 있고 직관적이지 않을 수 있습니다.

Ruby 2.6에서 소개된 끝없는 범위는 한쪽에 제한이 없이 범위를 정의할 수 있게 해주어 코드를 보다 간결하고 표현력있게 만들어줍니다. 특히 무한한 시퀀스나 무한 범위에 대해서 특히 유용합니다.

<div class="content-ad"></div>

```js
alphabet = ('a'..'z')
numbers = (1..)
positive_even_numbers = (2..).step(2)
```

ActiveRecord을 사용하면 강력한 단축키가 될 수 있습니다:

```js
@posts = 
  Post.where(some_value: ..min_value).order(:id).paginate(page: params[:page])
  # where('some_value < ?', min_value)와 동일합니다
```

무한 범위는 조심히 사용하지 않으면 예상치 못한 동작으로 이어질 수 있습니다. 특히 범위가 무한대로 반복되는 경우에는 특별히 주의해야 합니다. 지나치게 또는 부적절하게 사용할 경우 가독성이 떨어질 수도 있습니다.

<div class="content-ad"></div>

# 데이터 클래스:

간단한 데이터 구조를 만들 때는 종종 속성 액세서와 초기화를 위한 보일러플레이트 코드를 작성해야 했습니다.

루비 3.0에서 소개된 데이터 클래스는 속성을 가진 클래스를 정의하는 간결한 구문을 제공하여 보일러플레이트를 줄이고 코드 가독성을 향상시킵니다.

```js
class Coordinates < Data
  attribute x: Float
  attribute y: Float
end

point = Coordinates.new(x: 10, y: 20)
puts point.x # 출력: 10
puts point.y # 출력: 20
```

<div class="content-ad"></div>

데이터 클래스는 주로 간단한 데이터 구조를 위해 사용되며 복잡한 클래스 계층구조나 행위 중심 클래스에는 적합하지 않을 수 있습니다. 과도하게 사용할 경우 더 이상 캡슐화되지 않은 코드로 이어질 수도 있습니다.

루비에서 Data와 Struct의 차이는 사용 목적과 기본 동작을 중심으로 하고 있습니다.

Struct는 루비의 내장 클래스로, 이름이 지정된 속성을 가진 가벼운 데이터 구조를 생성할 수 있게 해줍니다. 클래스 전체를 명시적으로 정의하지 않고도 간단한 클래스를 정의하는 편리한 방법을 제공합니다. Struct를 생성할 때는 그 속성을 한 줄로 정의하고, 루비가 각 속성에 대한 접근자 메서드를 자동으로 생성합니다. Struct는 데이터 구조를 정의하는 간단한 구문을 제공하지만, 일반적인 클래스에서 사용 가능한 일부 고급 기능과 사용자 정의 옵션은 제공하지 않습니다.

한편 Data는 루비 3.0에서 Ractor 실험적 API의 일부로 도입된 새로운 기능입니다. 이는 엄격한 속성 타입을 갖는 변경할 수 없는 데이터 클래스를 정의하는 데 특별히 설계되었습니다. Struct가 변경 가능한 속성을 가진 변경 가능한 클래스를 생성하는 데 반해, Data 클래스는 기본적으로 변경할 수 없으며, 인스턴스화 이후에는 속성을 수정할 수 없습니다. 또한 Data 클래스는 속성에 대한 엄격한 타입 지정을 강제하므로 지정된 유형의 값만이 속성에 할당될 수 있습니다. 이러한 변경 불가능성과 엄격한 타입 지정으로 인해 Data 클래스는 데이터 전송 객체(DTO), 데이터 페이로드 표현 및 변경 불가능성 및 유형 안전성이 중요한 시나리오를 모델링하는 데 적합합니다.

<div class="content-ad"></div>

# 결론

루비는 읽기 쉽고 효율적이며 표현성이 향상된 많은 강력한 기능을 갖추고 진화했습니다. 이러한 새로운 기능을 활용함으로써 현대적이고 이디오매틱한 루비 코드를 작성할 수 있습니다. 여전히 루비 1.9 스타일 코드를 작성 중이라면, 최신 루비 표준을 반영하고 따라가기 위해 이러한 새로운 기능을 탐험하고 받아들이는 것이 시간입니다.