---
title: "자바 불변 객체에 관한 인터뷰 톱 질문"
description: ""
coverImage: "/assets/img/2024-05-20-JavaImmutabilitytopInterviewquestions_0.png"
date: 2024-05-20 16:03
ogImage: 
  url: /assets/img/2024-05-20-JavaImmutabilitytopInterviewquestions_0.png
tag: Tech
originalTitle: "Java Immutability top Interview questions"
link: "https://medium.com/@abhishek.talakeriv/java-top-interview-questions-on-immutability-concept-70b2fce37f5e"
---


by Abhishek Talakeri

![Java Immutability Interview Questions](/assets/img/2024-05-20-JavaImmutabilitytopInterviewquestions_0.png)

안녕하세요 여러분! 오늘은 자바 불변성 개념과 관련된 최상위 면접 질문을 탐구하며 명확한 설명과 간단한 예제를 제공할 것입니다. 이 자료는 면접 준비를 돕기 위해 특별히 설계되었습니다. 기술 면접을 되풀이하거나 이해를 향상시키려는 분들에게 유용한 자료가 되도록 이 시리즈는 여러분의 성공을 향한 여정에서 가치 있는 자산이 되도록 목표하고 있습니다.

- 자바에서 불변 클래스란 무엇인가요?

<div class="content-ad"></div>

자바에서 불변 클래스는 생성된 후 수정할 수 없는 인스턴스를 의미합니다. 불변 객체가 인스턴스화된 후에는 상태가 수명 동안 일정합니다. 이는 필드의 값이 변경될 수 없으며 값을 수정하려는 모든 시도는 새 개체의 생성으로 이어집니다.

불변 클래스를 사용하는 장점은 무엇인가요?

스레드 안전성: 불변 객체는 생성 후 상태 수정을 허용하지 않으므로 동기화 메커니즘이 필요하지 않아 스레드 안전성을 보장합니다.

일관된 상태: 불변 객체는 존재하는 동안 일정한 상태를 유지하여 프로그램 동작을 명확히 이해하고 예상치 못한 상태 변경을 최소화합니다.

<div class="content-ad"></div>

안전한 공유: 변경할 수 없는(Immutable) 객체는 여러 스레드 또는 컴포넌트 간 안전한 공유를 허용하여 코드의 재사용성과 의도하지 않은 수정의 위험이 없이 원활한 상호 운용성을 보장합니다.

동시성 제어: 변경할 수 없는(Immutable) 객체는 복잡한 동시성 제어 메커니즘을 완화시켜 더 간단하고 확장 가능한 동시 프로그래밍 모델로 이끕니다.

3. Java 표준 라이브러리에서 변경할 수 없는(Immutable) 클래스의 몇 가지 예시를 제공할 수 있나요?

i. java.lang.String

<div class="content-ad"></div>

ii. java.lang.Integer, java.lang.Double, java.lang.Boolean 등

iii. java.lang.Character

iv. java.time.LocalDate, java.time.LocalDateTime, java.time.LocalTime

v. java.time.Duration, java.time.Period

<div class="content-ad"></div>

vi. java.math.BigInteger, java.math.BigDecimal

vii. java.awt.Color

4. 수정할 수 없는 클래스를 사용하는 잠재적인 단점은 무엇인가요?

수정할 수 없는 클래스를 사용하면 스레드 안전성 및 예측 가능한 동작과 같은 이점이 있지만, 단점이 있을 수 있습니다. 이러한 단점으로는 각 수정마다 새 인스턴스를 생성하여 메모리 사용량이 증가하고 성능에 영향을 줄 수 있다는 것이 포함됩니다. 불변성은 코드 베이스에 복잡성을 추가하고 특정 시나리오에서 유연성을 제한할 수도 있습니다. 또한, 방어적 복사 오버헤드와 가비지 수집 문제가 발생할 수 있습니다. 그러나 많은 프로그래밍 시나리오에서 불변성의 장점이 이러한 단점을 능가하기도 합니다.

<div class="content-ad"></div>

5. 변경 빈번한 객체 상태 조작이 성능 또는 기능 상 필요한 시나리오에서는 불변성이 적합하지 않을 수 있습니다. 예를 들어, 비디오 스트리밍이나 게임과 같은 실시간 데이터 처리를 다루는 응용 프로그램에서는 반응성을 유지하기 위해 가변 상태에 대한 지속적인 업데이트가 필요한 경우가 있습니다. 또한 대규모 데이터 조작이나 복잡한 알고리즘이 포함된 시나리오에서 불변성은 메모리 사용량이 증가하고 성능 오버헤드가 발생할 수 있습니다. 마찬가지로 라이브러리나 프레임워크와 상호 운용성이 필수인 경우, 불변성은 최적의 선택이 아닐 수 있습니다.

6. 불변 클래스를 설계할 때 주의해야 할 사항은 무엇인가요?

i. 클래스를 Final로 선언: 클래스를 final로 선언하여 하위 클래스화를 방지하여 클래스의 동작이 변경되지 않도록 보장합니다.

<div class="content-ad"></div>

ii. 필드를 final로 선언해주세요: 객체 생성 후에는 수정할 수 없도록 모든 필드를 final로 선언해주세요.

iii. 필드를 private로 만들기: 필드를 private로 캡슐화하여, getter 메서드를 통해서만 제어된 접근을 허용해주세요.

iv. Setter 메서드를 제공하지 말기: 객체의 상태를 수정하는 Setter 메서드를 제공하지 않도록 지양해주세요. 그렇게 하면 불변성이 깨질 수 있습니다.

v. 깊은 불변성 보장하기: 클래스가 변경 가능한 객체에 대한 참조를 포함하더라도 불변성을 유지하도록 보장해주세요. 필요에 따라 방어적 복사를 구현하거나 참조된 객체의 불변 버전을 사용해주세요.

<div class="content-ad"></div>

vi. Equals와 HashCode 재정의: 객체 상태에 기반하여 올바른 동작을 보장하기 위해 올바른 equals()와 hashCode() 메소드를 구현하세요.

7. 변경되지 않은 개념을 구현하는 Java 클래스를 작성하십시오.

```java
public final class BankAccount {
  
  private final String accountNumber;
  private final String accountHolderName;
  private final double balance;
  
  public BankAccount(String accountNumber, String accountHolderName, double balance) {
    this.accountNumber = accountNumber;
    this.accountHolderName = accountHolderName;
    this.balance = balance;
  }
  
  public String getAccountNumber() {
    return accountNumber;
  }
  
  public String getAccountHolderName() {
    return accountHolderName;
  }
  
  public double getBalance() {
    return balance;
  }
  
}
```

8. Date와 같은 가변 필드를 가진 불변 클래스에서 변경 불가성을 어떻게 보장하나요?

<div class="content-ad"></div>

```java
import java.util.Date;

public final class ImmutableWithMutableField {

    private final int id;
    private final Date date;

    public ImmutableWithMutableField(int id, Date date) {

        this.id = id;
        this.date = new Date(date.getTime());
    }

    public int getId() {
        return id;
    }

    public Date getDate() {
        return new Date(date.getTime());
    }

    @Override
    public String toString() {
        return "ImmutableWithMutableField [id=" + id + ", date=" + date + "]";
    }

    // testing

    public static void main(String[] args) throws InterruptedException {

        ImmutableWithMutableField a = new ImmutableWithMutableField(1, new Date());

        System.out.println(a);
        Thread.sleep(3000);
        System.out.println(a);
        Thread.sleep(1000);
        ImmutableWithMutableField a1 = new ImmutableWithMutableField(1, new Date());
        System.out.println(a1);
        Thread.sleep(1000);
        ImmutableWithMutableField a2 = new ImmutableWithMutableField(1, new Date());
        System.out.println(a2);

    }

}
```

Result:

![Image](/assets/img/2024-05-20-JavaImmutabilitytopInterviewquestions_1.png)

9. Immutable 클래스 내에서 가변 컬렉션을 어떻게 처리하시겠습니까?


<div class="content-ad"></div>

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public final class ImmutableWithMutableCollection {

    private final int id;
    private final List<String> mutableList;

    public ImmutableWithMutableCollection(int id, List<String> mutableList) {

        this.id = id;
        this.mutableList = new ArrayList<>(mutableList);
    }

    public int getId() {
        return id;
    }

    public List<String> getMutableList() {
        return Collections.unmodifiableList(mutableList);
    }

}
```

10. 불변 클래스(Immutable Class)에서 가변 객체 참조를 어떻게 처리하시겠습니까?

```java
public class MutableClass {
    private String name;

    public MutableClass(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "MutableClass [name=" + name + "]";
    }

}
```

```java
public final class ImmutableClass {

    private final MutableClass mutable;
    private final String city;

    public ImmutableClass(MutableClass mutable, String city) {

        this.mutable = new MutableClass(mutable.getName());
        this.city = city;
    }

    public MutableClass getMutable() {
        return mutable;
    }

    public String getCity() {
        return city;
    }

    @Override
    public String toString() {
        return "ImmutableClass [mutable=" + mutable + ", city=" + city + "]";
    }

    public static void main(String[] args) {

        MutableClass m = new MutableClass("Abhishek");
        ImmutableClass i = new ImmutableClass(m, "Mumbai");

        System.out.println(i);
        m.setName("Appu");
        System.out.println(i);

    }

}
```

<div class="content-ad"></div>

<table>
    <tr>
        <td>11. Explain defensive copying.</td>
    </tr>
    <tr>
        <td>Defensive copying is a programming technique used to protect against unintended modifications to mutable objects by creating copies of them. When dealing with mutable data structures or objects, defensive copying involves creating a duplicate instance of the object and working with the copy instead of the original. This ensures that changes made to the copy do not affect the original object’s state, maintaining data integrity and preventing unexpected side effects. Defensive copying is commonly employed in scenarios where immutability is desired or when sharing data between different parts of a program to maintain consistency and prevent concurrency issues.</td>
     </tr>
    <tr>
        <td>12. What are stateless objects? How are they different from immutable objects? Which of these two is thread safe?</td>
    </tr>
</table>

<div class="content-ad"></div>

상태가 없는 객체(Stateless objects)는 인스턴스 필드(인스턴스 변수)가 없는 객체들을 말합니다. 해당 클래스에는 컴파일 시간 상수, 즉 static final 필드가 있을 수 있습니다. 불변 객체(Immutable objects)는 상태를 가지지만 초기화 이후에 상태를 변경할 수 없는 객체를 의미합니다. 이 두 가지는 스레드 안전합니다.

13. 해시맵(HashMap)에서 불변 객체가 키로 사용되면 어떤 이점이 있을까요?

불변 객체가 해시맵에서 키로 사용되면 그 안정성(Stability)으로 인해 이점을 가집니다. 생성 이후에 상태를 수정할 수 없기 때문에 해시 코드는 수명 동안 일정하게 유지됩니다. 이 특성은 키의 해시 코드가 일관되게 유지되어 해싱 및 해시맵에서 값 검색을 효율적으로 수행할 수 있도록 합니다. 게다가, 불변성은 키의 상태가 예기치 않게 변경되지 않음을 보장하여 해시맵에서 키 충돌 또는 예기치 않은 동작과 같은 문제를 방지합니다. 이러한 안정성과 예측 가능성은 해시맵의 무결성과 효율성을 보장하기 위해 불변 객체를 이상적으로 만듭니다.

끝까지 제 글을 읽어주셔서 감사합니다. 이 글로부터 유익한 통찰과 지식을 얻으셨기를 진심으로 바랍니다. 만약 글이 즐거우시고 유익하다고 느꼈다면, 제바랍니다 여러분의 친구들과 동료들과 공유해 주시기를 부탁드립니다.

<div class="content-ad"></div>

이 기사를 즐겨 보셨다면, 부디 팔로우하고 구독하며 박수를 보내 주시면 감사하겠습니다.

제 다른 기사들도 한 번 살펴보세요.