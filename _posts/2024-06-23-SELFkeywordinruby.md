---
title: "루비에서 SELF 키워드 사용하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-SELFkeywordinruby_0.png"
date: 2024-06-23 20:46
ogImage: 
  url: /assets/img/2024-06-23-SELFkeywordinruby_0.png
tag: Tech
originalTitle: "SELF keyword in ruby"
link: "https://medium.com/@avi4791/self-keyword-in-ruby-926cd1cc36ad"
---


Ruby에서 self의 목적은 무엇인가요?

<img src="/assets/img/2024-06-23-SELFkeywordinruby_0.png" />

Ruby에서 self는 현재 객체를 나타내는 특별한 변수입니다. self의 의미는 사용되는 문맥에 따라 달라집니다. self를 이해하는 것은 다른 범위 내에서 어떻게 메서드와 변수에 접근하고 조작하는지에 영향을 미치므로 중요합니다. 이 글에서는 Ruby에서 self의 목적에 대해 자세히 살펴보고 상세한 예시를 제시하겠습니다.

# Ruby에서 self의 목적

<div class="content-ad"></div>

- 인스턴스 변수 및 메소드에 접근하기: 인스턴스 메소드 내부에서 self는 클래스의 인스턴스를 가리키며, 인스턴스 변수와 메소드에 접근할 수 있게 합니다.
- 클래스 메소드 정의하기: 클래스 정의 내부에서 self는 클래스 자체를 가리킵니다. 이는 클래스 메소드를 정의하는 데 유용합니다.
- 싱글톤 메소드: self를 사용하여 클래스의 모든 인스턴스가 아닌 단일 객체에 속하는 메소드를 정의할 수 있습니다.
- 메타프로그래밍: self는 메타프로그래밍에서 자주 사용되어 동적으로 메소드를 정의하고 객체를 조작하는 데 활용됩니다.
- 문맥 인식: self는 코드 실행 문맥을 이해하는 데 도움이 되어 메소드 해결과 범위 관리에 중요합니다.

# self 사용 예시

## 1. 인스턴스 변수와 메소드에 접근하기

이 문맥에서 self는 인스턴스 메소드 내부에서 인스턴스 변수와 메소드에 접근 가능하도록 합니다.

<div class="content-ad"></div>

```javascript
class Person
  def initialize(name, age)
    @name = name
    @age = age
  end

  def birthday
    self.age += 1
  end

  def introduce
    "Hello, I'm #{self.name} and I'm #{self.age} years old."
  end

  # Getter and setter methods
  def name
    @name
  end

  def age
    @age
  end

  def age=(new_age)
    @age = new_age
  end
end

person = Person.new("Alice", 30)
puts person.introduce # 출력: "안녕, 나는 Alice이고 30살이야."
person.birthday
puts person.introduce # 출력: "안녕, 나는 Alice이고 31살이야."
```

## 2. 클래스 메소드 정의하기

클래스 내부에서 사용될 때 self는 클래스 자체를 가리키며, 클래스 메소드를 정의하는 데 유용합니다.

```javascript
class MathUtils
  def self.square(num)
    num * num
  end

  def self.cube(num)
    num * num * num
  end
end

puts MathUtils.square(3) # 출력: 9
puts MathUtils.cube(3)   # 출력: 27
```

<div class="content-ad"></div>

```js
class MathUtils
  class << self
    def square(num)
      num * num
    end

    def cube(num)
      num * num * num
    end
  end
end

puts MathUtils.square(3) # Output: 9
puts MathUtils.cube(3)   # Output: 27
```

## 3. Singleton Methods

self can be used to define methods on a single object (singleton methods).

<div class="content-ad"></div>


str = "Hello"

def str.shout
  self.upcase + "!!!"
end

puts str.shout # Output: "HELLO!!!"


## 4. 메타프로그래밍

메타프로그래밍에서 자주 self는 메소드를 동적으로 정의하고 객체를 조작하는 데 사용됩니다.

```ruby
class MyClass
  def self.create_method(name)
    define_method(name) do
      "Method #{name} called"
    end
  end
end

MyClass.create_method(:greet)
obj = MyClass.new
puts obj.greet # Output: "Method greet called"
```

<div class="content-ad"></div>

self는 메소드 해결 및 범위 관리에 중요한 실행 컨텍스트를 이해하는 데 도움이 됩니다.

```js
class Animal
  def speak
    "동물이 말합니다"
  end

  def call_speak
    self.speak
  end
end

class Dog < Animal
  def speak
    "멍멍!"
  end
end

dog = Dog.new
puts dog.call_speak # 출력: "멍멍!"
```

## 6. 속성 작성기와 self 사용

self를 사용하는 것은 속성 작성기(setters)를 호출할 때 중요합니다. 이는 로컬 변수 할당과 모호함을 피하기 위해 필요합니다.

<div class="content-ad"></div>

```js
class User
  attr_accessor :name

  def initialize(name)
    @name = name
  end

  def rename(new_name)
    self.name = new_name # setter method `name=`이(가) 사용됨
  end
end

user = User.new("John")
user.rename("Doe")
puts user.name # 출력: "Doe"
```

# 요약

- 인스턴스 컨텍스트: 인스턴스 메서드 내에서 self는 클래스의 현재 인스턴스를 가리킵니다.
- 클래스 컨텍스트: 클래스 메서드 및 클래스 정의 내에서 self는 클래스 자체를 가리킵니다.
- 싱글톤 메서드: self는 개별 객체에 대해 메서드를 정의하는 데 사용될 수 있습니다.
- 메타프로그래밍: self는 동적으로 메서드를 정의하고 객체를 조작하는 데 사용됩니다.
- 컨텍스트 인식: self는 현재 실행 컨텍스트를 이해하는 데 도움을 주며, 메서드 해결과 범위에 영향을 줍니다.
- 속성 쓰기: self는 로컬 변수 할당과 구별하기 위해 세터 메서드를 호출하는 데 필요합니다.

Rubyself의 사용을 숙달하면 더 유연하고 재사용 가능하며 유지 관리가 쉬운 코드를 작성할 수 있습니다.
