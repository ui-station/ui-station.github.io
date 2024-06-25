---
title: "루비에서의 메소드 접근 제한자"
description: ""
coverImage: "/assets/img/2024-05-27-MethodaccessmodifiersinRuby_0.png"
date: 2024-05-27 16:09
ogImage:
  url: /assets/img/2024-05-27-MethodaccessmodifiersinRuby_0.png
tag: Tech
originalTitle: "Method access modifiers in Ruby"
link: "https://medium.com/@ekc7590/method-access-modifiers-in-ruby-25ac2dd691a5"
---

## 루비에서의 메소드 액세스 수정자

루비에서의 메소드 액세스 수정자는 클래스 내부 및 외부에서 메소드의 가시성 및 접근성을 정의하는 중요한 역할을 합니다. 기본 설정으로 메소드는 공개(public)이며, 이는 객체 외부 및 내부에서 모두 호출할 수 있다는 것을 의미합니다.

## 공개 메소드

루비에서는 기본적으로 메소드가 공개(public)로 설정됩니다. 따라서 객체의 내부 및 외부에서 모두 호출할 수 있습니다.

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
class Parent
  def initialize(name)
    @name = name
  end

  def public_method
    @name
  end
end

parent = Parent.new('Eric')
p parent.public_method #=> "Eric"
```

## Private Methods

Private methods can only be called from within the context of the defining class. They cannot be called with an explicit receiver, not even self.

```js
class Parent
  def initialize(name)
    @name = name
  end

  private

  def name
    @name
  end
end

class Child1 < Parent
  def other_name(child)
    child.name
  end

  def call_name
    name
  end
end

class Child2 < Parent
end

child1 = Child1.new('Eric')
child2 = Child2.new('John')
p child1.other_name(child2) #=> private method `name' called for #<Child2:…> (NoMethodError)
p child1.call_name #=> "Eric"
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

## 보호된 메서드

보호된 메서드는 정의된 클래스의 인스턴스 또는 하위 클래스의 인스턴스에서 호출할 수 있지만, 이외의 인스턴스에서는 호출할 수 없습니다.

```js
class Parent
  def initialize(name)
    @name = name
  end

  protected

  def name
    @name
  end
end

class Child1 < Parent
  def other_name(child)
    child.name
  end

  def call_name
    name
  end
end

class Child2 < Parent
end

child1 = Child1.new('Eric')
child2 = Child2.new('John')
p child1.other_name(child2) #=> "John"
p child1.name #=> protected method `name' called for #<Child1:…> (NoMethodError)
```

## 접근 제한자 및 범위

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

액세스 한정자는 Ruby에서 범위에 영향을 주지 않습니다; 메서드의 가시성만을 영향을 미칩니다. 외부에서 접근할 수 없는 개인 메서드도 하위 클래스에서는 상속됩니다.

```js
class Parent
  def initialize(name)
    @name = name
  end

  private

  def name
    @name
  end
end

class Child < Parent
  def call_name
    name
  end
end

child = Child.new('Eric')
p child.call_name #=> "Eric"
```

# 실용적인 고려사항

## 보호된 메서드

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

- 보호된 메서드는 Ruby에서 자주 사용되지 않는데, 이는 가시성을 혼동시키고 메서드가 어디에서 어떻게 호출되는지 추적하기 어렵게 만들 수 있기 때문입니다.
- 이는 미묘한 버그를 도입하고 디버깅을 더 어렵게 만들 수 있습니다.

## 비공개 메서드

- 비공개 메서드는 객체의 내부 작동과 공개 인터페이스 사이의 명확한 분리를 제공합니다.
- 캡슐화를 촉진하며 코드를 유지 및 디버그하기 쉽게 만듭니다.

## 결론

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

루비의 접근 제어자는 메소드의 가시성을 정의하고 메소드에 접근 및 상호 작용하는 방법을 제어하는 중요한 도구입니다. 이러한 제어자를 이해하고 적절하게 사용하는 것은 깔끔하고 이해하기 쉽고 유지보수 가능한 코드를 유지하는 데 도움이 될 수 있습니다. 코드베이스가 더 복잡해지면 이러한 차이의 중요성이 더욱 명확해질 것입니다.
