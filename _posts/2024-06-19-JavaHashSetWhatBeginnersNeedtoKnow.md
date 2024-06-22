---
title: "자바 HashSet 초보자가 꼭 알아야 할 내용"
description: ""
coverImage: "/assets/img/2024-06-19-JavaHashSetWhatBeginnersNeedtoKnow_0.png"
date: 2024-06-19 10:08
ogImage: 
  url: /assets/img/2024-06-19-JavaHashSetWhatBeginnersNeedtoKnow_0.png
tag: Tech
originalTitle: "Java HashSet— What Beginners Need to Know"
link: "https://medium.com/@AlexanderObregon/java-hashset-what-beginners-need-to-know-ea36588989b8"
---


<img src="/assets/img/2024-06-19-JavaHashSetWhatBeginnersNeedtoKnow_0.png" />

# 소개

Java HashSet은 Java에서 가장 일반적으로 사용되는 데이터 구조 중 하나입니다. 고유한 요소를 저장하고 효율적인 조회를 수행하는 간단하면서 강력한 방법을 제공합니다. 이 기사에서는 HashSet이 무엇인지, 어떻게 사용하는지, 일반적인 작업 및 사용 사례에 대해 탐구할 것입니다. 이 기사는 이미 HashSet에 익숙한 사람들에게도 좋은 리프레셔 역할을 할 것입니다.

# HashSet이란 무엇인가?

<div class="content-ad"></div>

HashSet은 Java Collections Framework의 일부이며 Set 인터페이스를 구현합니다. 이는 해시 테이블 (실제로는 HashMap 인스턴스)에 기반하며 중복 요소를 허용하지 않습니다. HashSet의 주요 특징은 다음과 같습니다:

- 중복 요소 없음: HashSet은 중복을 자동으로 처리합니다. 집합에 이미 존재하는 요소를 추가하려고 시도하면 무시됩니다.
- 순서 없음: HashSet의 요소는 특정 순서로 저장되지 않습니다. 요소가 추가되고 제거됨에 따라 순서가 변경될 수 있습니다.
- 효율적: HashSet은 해시 함수가 요소를 적절하게 버킷에 분산시키는 경우 추가, 제거, 포함 여부, 크기 등의 기본 작업에 대해 상수 시간 성능을 제공합니다.

## HashSet는 어떻게 동작합니까?

HashSet은 해싱을 사용하여 요소를 저장하고 검색합니다. HashSet에 요소를 추가할 때 다음 단계가 발생합니다:

<div class="content-ad"></div>

- 해시 코드 계산: 객체의 hashCode 메서드를 호출하여 해시 코드를 계산합니다. 이 해시 코드는 객체의 메모리 주소를 간단히 나타내는 정수 값입니다.
- 해시 코드 모듈로 연산: 그런 다음 해시 코드를 해시 테이블의 버킷(슬롯) 수로 나누고 나머지를 사용하여 요소를 저장할 버킷의 인덱스로 사용합니다. 이를 통해 해시 코드가 사용 가능한 버킷의 범위 내에 매핑되도록 합니다.
- 충돌 처리: 두 요소가 동일한 해시 코드를 가지는 경우(충돌), 이들은 같은 버킷에 저장되지만 해당 버킷 내에서 목록 또는 트리 구조로 연결됩니다.
- 요소 저장: 결정된 버킷에 요소가 저장되고 해당 요소에 대한 참조가 유지됩니다.

contains 또는 remove와 같은 작업을 수행할 때 요소의 해시 코드가 계산되고 동일한 프로세스를 사용하여 적절한 버킷이 찾아지며 해당 버킷에서 요소가 발견되거나 제거됩니다.

## 왜 HashSet을 사용해야 하는가?

HashSet은 다양한 시나리오에 적합하게 만드는 여러 장점을 제공합니다.

<div class="content-ad"></div>

- 중복 제거: HashSet은 각 요소가 고유하다고 자동으로 보장합니다. 이는 중복이 허용되지 않는 항목의 컬렉션을 저장해야 하는 경우 유용합니다. 예를 들어, 고유한 사용자 이름이나 제품 ID 목록 등.
- 효율적인 조회: HashSet은 추가, 삭제, 요소 존재 여부 확인과 같은 기본 작업에 대해 상수 시간 성능을 제공합니다. 이로 인해 캐싱 메커니즘이나 멤버십 테스팅과 같이 빠른 조회가 중요한 사용 사례에 이상적입니다.
- 메모리 효율성: HashSet은 해시 테이블로 지원되기 때문에 다른 자료구조인 트리나 연결 리스트와 비교했을 때 특히 요소 수가 많을 때 더 메모리 효율적일 수 있습니다.
- 집합 연산: HashSet은 합집합, 교집합, 차집합과 같은 수학적 집합 연산을 수행하는 데 사용할 수 있습니다. 이러한 연산은 데이터 필터링, 컬렉션 간의 공통 요소 찾기, 특정 항목을 집합에서 제외하는 등의 시나리오에서 유용합니다.

## HashSet 생성

Java에서 HashSet을 생성하는 것은 간단합니다. 다음은 간단한 예제입니다:

```java
import java.util.HashSet;

public class HashSetExample {
    public static void main(String[] args) {
        // HashSet 생성
        HashSet<String> set = new HashSet<>();
        
        // HashSet에 요소 추가
        set.add("Avocado");
        set.add("Peach");
        set.add("Cherry");
        
        // HashSet 출력
        System.out.println("HashSet: " + set);
    }
}
```

<div class="content-ad"></div>

이 예제에서는 문자열의 HashSet을 만들고 세 가지 요소를 추가합니다. System.out.println 문은 세트의 요소를 출력합니다. 요소의 순서가 보장되지 않는 점을 유의하세요.

## HashSet의 내부 작동

내부 작동에 대해 더 알아보려면 다음 사항을 고려해보세요:

- 해시 함수: 객체의 hashCode 메서드가 중요합니다. 잘 설계된 해시 함수는 요소를 균일하게 버킷에 분산시켜 충돌 가능성을 줄이고 성능을 유지합니다.
- 버킷 구조: HashSet의 각 버킷은 실제로 연결된 리스트나 균형 잡힌 트리(대규모 집합의 경우)입니다. 여러 요소가 동일한 버킷으로 해싱되면 이 리스트/트리에 저장됩니다.
- 로드 팩터: 로드 팩터는 해시 테이블의 용량이 자동으로 증가되기 전에 얼마나 가득 찰 수 있는지를 측정하는 지수입니다. 기본 로드 팩터 0.75는 시간과 공간 비용 사이의 적절한 균형을 유지합니다.
- 다시 해싱: 요소 수가 로드 팩터와 현재 용량의 곱을 초과하면 해시 테이블이 리사이징되고(일반적으로 크기가 두 배로 늘어남) 요소가 새 버킷에 다시 해싱됩니다. 이를 통해 HashSet이 성능 특성을 유지합니다.

<div class="content-ad"></div>

이 내부 메커니즘을 이해함으로써, 자바의 HashSets의 효율성과 유연성을 더 잘 이해할 수 있습니다.

# HashSets에서의 기본 작업

## 요소 추가 및 제거

HashSet에 요소를 추가하는 방법은 add 메서드를 사용하는 것입니다. 이미 HashSet에 요소가 있는 경우 다시 추가되지 않습니다. 요소를 제거하는 방법은 remove 메서드를 사용하여 수행할 수 있습니다. 이 작업을 보여주는 예제는 다음과 같습니다:

<div class="content-ad"></div>

```java
import java.util.HashSet;

public class HashSetOperations {
    public static void main(String[] args) {
        // Creating a HashSet
        HashSet<String> set = new HashSet<>();
        
        // Adding elements to the HashSet
        set.add("Avocado");
        set.add("Peach");
        set.add("Cherry");
        set.add("Avocado"); // Duplicate element, won't be added
        
        System.out.println("HashSet after adding elements: " + set);
        
        // Removing an element
        set.remove("Peach");
        
        System.out.println("HashSet after removing Peach: " + set);
    }
}
```

이 예제에서는 문자열 HashSet을 만들고 중복되는 "Avocado"를 포함한 네 가지 요소를 추가합니다. 중복 요소는 무시됩니다. 그런 다음 세트에서 "Peach"를 제거합니다.

## 요소 확인

HashSet이 특정 요소를 포함하는지 확인하려면 contains 메서드를 사용할 수 있습니다. 이 메서드는 요소가 있으면 true를 반환하고 그렇지 않으면 false를 반환합니다.

<div class="content-ad"></div>

```java
import java.util.HashSet;

public class HashSetContains {
    public static void main(String[] args) {
        // Creating a HashSet
        HashSet<String> set = new HashSet<>();
        set.add("Avocado");
        set.add("Peach");
        set.add("Cherry");
        
        // Checking for elements
        boolean containsAvocado = set.contains("Avocado");
        boolean containsGrape = set.contains("Grape");
        
        System.out.println("Contains Avocado: " + containsAvocado);
        System.out.println("Contains Grape: " + containsGrape);
    }
}
```

이 예제에서는 HashSet에 "Avocado"와 "Grape" 요소가 포함되어 있는지 확인합니다. 출력 결과에 따르면 "Avocado"가 존재하지만 "Grape"는 존재하지 않습니다.

## 요소 반복

for-each 루프나 반복자(iterator)를 사용하여 HashSet의 요소를 반복할 수 있습니다. HashSet은 요소의 특정 순서를 유지하지 않기 때문에 반복 순서는 보장되지 않습니다.

<div class="content-ad"></div>

For-Each Loop을 사용하는 방법

```java
import java.util.HashSet;

public class HashSetIteration{
    public static void main(String[] args) {
        // HashSet 생성
        HashSet<String> set = new HashSet<>();
        set.add("Avocado");
        set.add("Peach");
        set.add("Cherry");

        // For-Each 루프 사용
        System.out.println("For-Each 루프 사용:");
        for(String fruit : set) {
            System.out.println(fruit);
        }
    }
}
```

Iterator 사용하는 방법

```java
import java.util.HashSet;
import java.util.Iterator;

public class HashSetIterator {
    public static void main(String[] args) {
        // HashSet 생성
        HashSet<String> set = new HashSet<>();
        set.add("Avocado");
        set.add("Peach");
        set.add("Cherry");
        
        // Iterator 사용
        System.out.println("Iterator 사용:");
        Iterator<String> iterator = set.iterator();
        while(iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }
}
```

<div class="content-ad"></div>

이 예제에서는 for-each 루프와 반복자가 모두 HashSet의 요소를 순회하는 데 사용됩니다. 요소들은 콘솔에 출력되지만 순회 순서는 지정되지 않습니다.

## HashSet의 크기

HashSet에있는 요소 수를 얻으려면 size 메서드를 사용하세요. 이 메서드는 현재 집합에 있는 요소 수를 나타내는 정수를 반환합니다.

```js
import java.util.HashSet;

public class HashSetSize {
    public static void main(String[] args) {
        // HashSet 생성
        HashSet<String> set = new HashSet<>();
        set.add("Avocado");
        set.add("Peach");
        set.add("Cherry");
        
        // HashSet의 크기 가져오기
        int size = set.size();
        
        System.out.println("HashSet의 크기: " + size);
    }
}
```

<div class="content-ad"></div>

이 예에서는 size 메서드를 사용하여 HashSet의 요소 수를 결정하고 그 값을 콘솔에 출력합니다.

## HashSet 지우기

HashSet에서 모든 요소를 제거하려면 clear 메서드를 사용할 수 있습니다. 이 메서드는 모든 요소를 제거하여 집합을 효과적으로 비웁니다.

```js
import java.util.HashSet;

public class HashSetClear {
    public static void main(String[] args) {
        // HashSet 생성
        HashSet<String> set = new HashSet<>();
        set.add("Avocado");
        set.add("Peach");
        set.add("Cherry");
        
        // HashSet 지우기
        set.clear();
        
        System.out.println("HashSet 지우기 후: " + set);
    }
}
```

<div class="content-ad"></div>

이 예시에서 clear 메서드는 모든 요소를 제거하여 빈 세트를 만듭니다.

## HashSet이 비어 있는지 확인하기

HashSet이 비어 있는지 확인하려면 isEmpty 메서드를 사용하십시오. 이 메서드는 세트가 요소를 포함하지 않으면 true를 반환하고 그렇지 않으면 false를 반환합니다.

```java
import java.util.HashSet;

public class HashSetIsEmpty {
    public static void main(String[] args) {
        // HashSet 만들기
        HashSet<String> set = new HashSet<>();
        
        // HashSet이 비어 있는지 확인
        boolean isEmpty = set.isEmpty();
        
        System.out.println("HashSet이 비어 있는가? " + isEmpty);
        
        // 요소 추가 후 다시 확인
        set.add("Avocado");
        isEmpty = set.isEmpty();
        
        System.out.println("요소를 추가한 후 HashSet이 비어 있는가? " + isEmpty);
    }
}
```

<div class="content-ad"></div>

이 예제에서는 HashSet에 요소를 추가하기 전후에 비어 있는지 확인합니다. 출력 결과는 집합의 상태 변화를 반영합니다.

# 고급 사용법 및 팁

## HashSet에 사용자 지정 객체

HashSet은 사용자 지정 객체를 저장할 수 있지만, 이러한 객체에서 equals 및 hashCode 메소드를 올바르게 재정의하는 것이 중요합니다. 이렇게 하면 HashSet이 두 객체가 동일한지 올바르게 판별하고 해싱을 효과적으로 처리할 수 있습니다. 다음은 사용자 지정 Person 클래스 예제입니다:

<div class="content-ad"></div>

```java
import java.util.HashSet;
import java.util.Objects;

class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return age == person.age && Objects.equals(name, person.name);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
    
    @Override
    public String toString() {
        return name + " (" + age + ")";
    }
}

public class HashSetCustomObjects {
    public static void main(String[] args) {
        HashSet<Person> set = new HashSet<>();
        set.add(new Person("Alice", 30));
        set.add(new Person("Bob", 25));
        set.add(new Person("Alice", 30)); // Duplicate, won't be added
        
        System.out.println("HashSet: " + set);
    }
}
```

이 예제에서 Person 클래스는 equals 및 hashCode 메서드를 재정의하여 이름과 나이가 같은 두 Person 객체를 동일하게 처리할 수 있도록합니다. HashSet은 이러한 메서드를 사용하여 요소를 올바르게 관리합니다.

## 성능 고려사항

HashSet은 기본 연산에 대해 상수 시간 성능을 제공하지만 여러 요인이 효율에 영향을 미칠 수 있습니다:


<div class="content-ad"></div>

- 해시 함수: 좋은 해시 함수는 성능에 중요합니다. 요소를 균일하게 버킷에 분산시켜 충돌을 최소화해야 합니다. 제대로 구현되지 않은 hashCode 메서드는 많은 충돌을 일으켜 성능을 저하시킬 수 있습니다.
- 초기 용량 및 로드 요소: HashSet의 초기 용량과 로드 요소(기본값은 0.75)는 리해싱이 발생하는 시점을 결정합니다. 리해싱은 해시 테이블을 크기를 조정하고 요소를 재분배하는 비용이 많이 드는 작업을 포함합니다. 예상 요소 수에 기반하여 이러한 매개변수를 조정하면 성능을 최적화할 수 있습니다.
- 충돌 처리: 충돌이 발생하면 요소가 동일한 버킷 내에 연결 목록이나 균형 잡힌 트리에 저장됩니다. 빈번한 충돌은 성능을 떨어뜨릴 수 있으므로 해시 코드의 좋은 분포를 보장하는 것이 중요합니다.

## 일반적인 사용 사례

HashSets는 다양한 시나리오에서 다양하게 사용할 수 있습니다:

- 중복 제거: HashSets는 목록에서 중복 요소를 필터링하는 데 이상적입니다. 예를 들어 더 큰 컬렉션에서 고유한 사용자 이름 목록을 얻기 위해 HashSet를 사용할 수 있습니다.

<div class="content-ad"></div>

```java
import java.util.HashSet;
import java.util.List;
import java.util.ArrayList;

public class RemoveDuplicates {
    public static void main(String[] args) {
        List<String> usernames = new ArrayList<>();
        usernames.add("Alice");
        usernames.add("Bob");
        usernames.add("Alice"); // 중복

        HashSet<String> uniqueUsernames = new HashSet<>(usernames);
        
        System.out.println("고유한 사용자명: " + uniqueUsernames);
    }
}
```

- 멤버십 테스팅: HashSet은 요소가 집합의 일부인지 확인하는 효율적인 방법을 제공합니다. 사용자가 특정 역할을 가지고 있는지 확인하거나 재고에 제품이 있는지 확인하는 등의 시나리오에서 유용합니다.

```java
import java.util.HashSet;

public class MembershipTesting {
    public static void main(String[] args) {
        HashSet<String> roles = new HashSet<>();
        roles.add("Admin");
        roles.add("User");
        roles.add("Guest");

        String roleToCheck = "Admin";
        if (roles.contains(roleToCheck)) {
            System.out.println(roleToCheck + " 역할이 있습니다.");
        } else {
            System.out.println(roleToCheck + " 역할이 없습니다.");
        }
    }
}
```

- 집합 연산: HashSet은 합집합, 교집합, 차집합과 같은 수학적 집합 연산을 수행할 수 있습니다. 이러한 연산은 데이터 필터링, 컬렉션 간 공통 요소 찾기, 특정 항목을 세트에서 제외하는 등의 시나리오에서 유용합니다.

<div class="content-ad"></div>

```java
import java.util.HashSet;

public class SetOperations {
    public static void main(String[] args) {
        HashSet<String> set1 = new HashSet<>();
        set1.add("Avocado");
        set1.add("Peach");
        set1.add("Cherry");

        HashSet<String> set2 = new HashSet<>();
        set2.add("Peach");
        set2.add("Dragonfruit");
        set2.add("Elderberry");

        // Union
        HashSet<String> union = new HashSet<>(set1);
        union.addAll(set2);
        System.out.println("Union: " + union);

        // Intersection
        HashSet<String> intersection = new HashSet<>(set1);
        intersection.retainAll(set2);
        System.out.println("Intersection: " + intersection);

        // Difference
        HashSet<String> difference = new HashSet<>(set1);
        difference.removeAll(set2);
        System.out.println("Difference: " + difference);
    }
}
```

위 예제들에서는 HashSet을 사용하여 중복 제거, 멤버십 테스트 수행, 그리고 합집합, 교집합, 차집합과 같은 집합 연산을 수행하는 방법을 보여줍니다.

# 결론

Java의 HashSet은 고유한 요소의 컬렉션을 관리하는 멋진 도구입니다. 요소 추가, 제거, 확인을 위한 효율적인 작업을 제공하여 성능과 고유성이 중요한 시나리오에 이상적입니다. HashSet의 기본 사항, 고급 사용법 및 주요 사용 사례를 이해함으로써 이 유연한 데이터 구조를 사용하여 더 효과적이고 효율적인 Java 프로그램을 작성할 수 있습니다. 중복 필터링, 집합 연산 수행, 사용자 정의 객체 관리와 같은 작업을 수행할 때 HashSet은 컬렉션 요구 사항에 대한 강력한 솔루션을 제공합니다.

<div class="content-ad"></div>

- Java HashSet 문서
- Java 컬렉션 프레임워크

읽어 주셔서 감사합니다! 만약 이 안내서가 도움이 되셨다면, 하이라이팅, 좋아요, 댓글 작성, Twitter/X를 통해 연락 주시면 정말 감사하겠습니다. 이는 매우 고마우며 이와 같은 콘텐츠를 무료로 제공할 수 있도록 도와줍니다!