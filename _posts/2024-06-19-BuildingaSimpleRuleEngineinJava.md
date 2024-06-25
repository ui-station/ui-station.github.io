---
title: "자바로 간단한 규칙 엔진 구축하기"
description: ""
coverImage: "/assets/img/2024-06-19-BuildingaSimpleRuleEngineinJava_0.png"
date: 2024-06-19 22:08
ogImage:
  url: /assets/img/2024-06-19-BuildingaSimpleRuleEngineinJava_0.png
tag: Tech
originalTitle: "Building a Simple Rule Engine in Java"
link: "https://medium.com/@kiarash.shamaii/building-a-simple-rule-engine-in-java-2d88eff7b465"
---

![Building a Simple Rule Engine in Java](/assets/img/2024-06-19-BuildingaSimpleRuleEngineinJava_0.png)

소프트웨어 개발에서는 종종 데이터를 필터링하거나 처리하기 위해 일련의 규칙이나 조건을 적용해야 하는 상황이 있습니다. 전통적인 if 및 else 문을 사용하여 이러한 규칙을 관리하면 유지 관리하기 어렵고 번거로울 수 있습니다. 규칙 엔진은 이러한 규칙을 정의하고 실행하는 더 유연하고 조직화된 방법을 제공합니다. 이 기사에서는 함수형 프로그래밍 원칙을 사용하여 Java에서 간단한 규칙 엔진을 구축하는 방법을 탐색하겠습니다.

준비 사항

규칙 엔진을 구축하기에 앞서 필요한 구성 요소를 정의해 보겠습니다:

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

# 규칙:

규칙은 우리가 평가하고 싶은 조건 또는 기준을 나타냅니다. 규칙은 주어진 객체가 규칙의 기준을 충족하는지 여부를 결정하는 술어로 구성됩니다. Enum 또는 Map`key, value`를 사용하세요 (이 부분에서 Enum을 사용합니다).

# 규칙 엔진:

규칙 엔진은 규칙 컬렉션을 관리하고 이를 객체 집합에 적용하는 것을 담당합니다. 정의된 규칙에 기반하여 객체를 필터링하고 필터된 결과를 반환합니다.

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

# 대상-객체:

규칙을 사용하여 필터링하고자 하는 간단한 Person 클래스를 예시 객체로 사용하겠습니다. 이름과 나이와 같은 속성이 있습니다.

# 규칙 엔진 구축하기

규칙 엔진을 단계별로 구축해 봅시다.

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

규칙 정의:
먼저 Rule이라는 enum을 사용하여 규칙을 정의합니다. enum의 각 규칙은 우리가 평가하고 싶은 특정 조건이나 기준을 나타냅니다. 예를 들어, 코드에서 우리는 AGE_GREATER_THAN_30과 NAME_STARTS_WITH_B 두 가지 규칙을 정의했습니다. 각 규칙에는 해당 규칙의 조건을 정의하는 프레디케이트가 있습니다.

```js
import java.util.function.Predicate;

// 여러 enum을 가질 수 있어요. 이를 통해 규칙이나 조건을 관리할 수 있어요.
public enum Rule implements TestRule {
    AGE_GREATER_THAN_30(person -> person.getAge() > 30),
    NAME_STARTS_WITH_B(person -> person.getName().startsWith("B"));

    private final Predicate<Person> predicate;

    Rule(Predicate<Person> predicate) {
        this.predicate = predicate;
    }

    public Predicate<Person> getPredicate() {
        return predicate;
    }
}
```

Rule Engine 생성:
다음으로 RuleEngine 클래스를 생성합니다. 이 클래스는 규칙의 관리와 실행을 처리할 것입니다. 규칙 목록을 유지하고 이러한 규칙에 기반한 새로운 규칙을 추가하고 개체를 필터링하기 위한 메서드를 제공합니다.

필터링 로직 구현:
RuleEngine 클래스의 filter 메서드에서는 개체 목록을 반복하고 Java 8 Stream API의 allMatch 메서드를 사용하여 각 규칙을 적용합니다. 이 메서드는 주어진 개체에 대해 모든 규칙이 통과되었는지 확인합니다. 모든 규칙이 통과되면 해당 개체를 필터링된 목록에 추가합니다.

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
public interface TestRule {
    <T> Predicate<T> getPredicate();
}
```

```js
import java.util.ArrayList;
import java.util.List;

public class RuleEngine<T> {
    private List<TestRule> rules;

    public RuleEngine() {
        this.rules = new ArrayList<>();
    }

    public void addRule(TestRule rule) {
        rules.add(rule);
    }

    public List<T> filter(List<T> items) {
        List<T> filteredItems = new ArrayList<>();
        for (T item : items) {
            if (rules.stream().allMatch(rule -> rule.getPredicate().test(item))) {
                filteredItems.add(item);
            }
        }
        return filteredItems;
    }
}
```

샘플 대상 객체 만들기:

```js
public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
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

모두 함께 해보기

Main 클래스에서는 우리의 규칙 엔진 사용법을 보여줍니다. Person 객체의 리스트를 생성하고 RuleEngine을 AGE_GREATER_THAN_30과 NAME_STARTS_WITH_B 두 가지 규칙으로 초기화합니다. 그런 다음 규칙 엔진의 filter 메소드를 호출하여 Person 객체의 리스트를 전달합니다. 규칙 엔진은 각 사람에게 규칙을 적용하고 필터링된 리스트를 반환합니다.

마지막으로 필터링된 리스트를 반복하고 지정된 규칙을 충족하는 사람들의 이름을 출력합니다.

```java
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<Person> people = List.of(
                new Person("Alice", 25),
                new Person("Bob", 31),
                new Person("Charlie", 35)
        );

        RuleEngine<Person> ruleEngine = new RuleEngine<>();
        ruleEngine.addRule(Rule.AGE_GREATER_THAN_30);
        ruleEngine.addRule(Rule.NAME_STARTS_WITH_B);

        List<Person> filteredPeople = ruleEngine.filter(people);

        // 이 부분은 결과만 출력합니다.
        for (Person person : filteredPeople) {
            System.out.println(person.getName());
        }
    }
}
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

# 결론

이 글에서는 Java로 간단한 규칙 엔진을 구축하는 방법을 살펴보았습니다. 이 규칙 엔진을 사용하면 프레디케이트를 사용하여 규칙을 정의하고 객체 컬렉션에 적용할 수 있습니다. 함수형 프로그래밍 원칙을 활용하여 유연하고 확장 가능한 규칙 엔진을 만들어 다양한 시나리오에서 사용할 수 있습니다.

여기서 제시된 개념과 코드를 이해하면 이 기반을 확장하여 특정 필요에 맞는 보다 복잡한 규칙 엔진을 구축할 수 있습니다.

https://github.com/KiaShamaei/ruleEngine
