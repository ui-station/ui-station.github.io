---
title: "다트와 플러터에서 객체지향 프로그래밍OOP 심층 탐구"
description: ""
coverImage: "/assets/img/2024-05-27-ADeepDiveObject-OrientedProgrammingOOPinDartandFlutter_0.png"
date: 2024-05-27 16:30
ogImage:
  url: /assets/img/2024-05-27-ADeepDiveObject-OrientedProgrammingOOPinDartandFlutter_0.png
tag: Tech
originalTitle: "A Deep Dive Object-Oriented Programming (OOP) in Dart and Flutter"
link: "https://medium.com/@shahzebnaqvi/a-deep-dive-object-oriented-programming-oop-in-dart-and-flutter-70e2ef6fd2ac"
---

객체지향 프로그래밍(OOP)은 소프트웨어 디자인을 객체와 그 상호작용을 중심으로 구성하는 패러다임입니다. 플러터 뒤에 있는 언어인 Dart는 OOP 원칙을 완벽히 지원하여 견고하고 확장 가능한 애플리케이션을 구축하는 데 탁월한 선택지입니다. 이 포괄적인 가이드에서는 Dart와 Flutter의 맥락에서 캡슐화, 상속, 다형성, 추상화와 같은 OOP의 네 가지 기둥을 자세히 살펴보며 메서드 오버라이딩과 오버로딩과 같은 개념도 살펴볼 것입니다.

# OOP의 네 가지 기둥 이해하기

## 1. 캡슐화

캡슐화는 데이터와 데이터를 조작하는 메서드를 하나의 클래스라는 단위 내에 묶는 것을 말합니다. 이는 객체의 내부 상태를 외부 세계로부터 숨기고 필요한 기능을 명확히 정의된 인터페이스를 통해 노출하는 것입니다.

<div class="content-ad"></div>

Dart에서 캡슐화는 public, private 및 protected와 같은 접근 한정자를 통해 달성됩니다. 기본적으로 멤버는 public이지만 밑줄(\_)을 사용하여 private로 표시할 수도 있습니다.

예제:

```js
class BankAccount {
  double _balance = 0; // Private property

  void deposit(double amount) {
    _balance += amount;
  }

  void withdraw(double amount) {
    if (_balance >= amount) {
      _balance -= amount;
    } else {
      print('Insufficient funds.');
    }
  }

  double getBalance() {
    return _balance;
  }
}

void main() {
  var account = BankAccount();
  account.deposit(1000);
  print('Current balance: ${account.getBalance()}'); // Output: Current balance: 1000
  account.withdraw(500);
  print('Remaining balance: ${account.getBalance()}'); // Output: Remaining balance: 500
}
```

## 2. 상속

<div class="content-ad"></div>

상속은 하위 클래스(subclass)가 다른 클래스(슈퍼클래스)로부터 속성과 메소드를 상속받을 수 있는 메커니즘입니다. 이는 코드 재사용을 촉진하고 클래스 간에 계층적인 관계를 정립합니다.

예시:

```js
class Animal {
  void speak() {
    print('동물이 말합니다.');
  }
}

class Dog extends Animal {
  @override
  void speak() {
    print('개가 짖습니다.');
  }
}

void main() {
  var dog = Dog();
  dog.speak(); // 출력: 개가 짖습니다.
}
```

## 3. 다형성

<div class="content-ad"></div>

다형성은 서로 다른 클래스의 객체를 공통 부모 클래스의 객체로 다룰 수 있게 합니다. 이는 코드의 유연성과 확장성을 가능하게 합니다.

예시:

```js
class Shape {
  void draw() {
    print('도형을 그립니다.');
  }
}

class Circle extends Shape {
  @override
  void draw() {
    print('원을 그립니다.');
  }
}

class Rectangle extends Shape {
  @override
  void draw() {
    print('사각형을 그립니다.');
  }
}

void main() {
  Shape circle = Circle();
  Shape rectangle = Rectangle();

  circle.draw(); // 결과: 원을 그립니다.
  rectangle.draw(); // 결과: 사각형을 그립니다.
}
```

## 4. 추상화

<div class="content-ad"></div>

추상화는 복잡한 구현 세부사항을 숨기고 객체의 필수적인 특성만을 보여주는 과정입니다. 이는 프로그래밍 복잡성을 줄이고 대규모 코드베이스를 효과적으로 관리하는 데 도움이 됩니다.

예시:

```js
abstract class Animal {
  void speak();
}

class Dog extends Animal {
  @override
  void speak() {
    print('Dog barks.');
  }
}

void main() {
  var dog = Dog();
  dog.speak(); // 출력: Dog barks.
}
```

# 메서드 재정의와 오버로딩

<div class="content-ad"></div>

## 메소드 오버라이딩

메소드 오버라이딩은 서브클래스가 슈퍼클래스에서 이미 정의된 메소드의 특정 구현을 제공할 때 발생합니다. 이를 통해 서브클래스는 상속된 메소드의 동작을 자신의 필요에 맞게 수정할 수 있습니다.

예시:

```js
class Animal {
  void speak() {
    print('Animal speaks.');
  }
}

class Dog extends Animal {
  @override
  void speak() {
    print('Dog barks.');
  }
}

void main() {
  var dog = Dog();
  dog.speak(); // 출력: Dog barks.
}
```

<div class="content-ad"></div>

## 메소드 오버로딩

메소드 오버로딩은 같은 이름을 가진 다른 매개변수를 갖는 여러 메소드를 정의할 수 있는 능력을 가리킵니다. Dart는 메소드 오버로딩을 직접 지원하지 않지만 선택적 매개변수 또는 이름이 지정된 매개변수를 사용하여 유사한 기능을 구현할 수 있습니다.

예시:

```js
class Calculator {
  int add(int a, int b) {
    return a + b;
  }

  double add(double a, double b) {
    return a + b;
  }
}

void main() {
  var calc = Calculator();
  print(calc.add(2, 3)); // 결과: 5
  print(calc.add(2.5, 3.5)); // 결과: 6.0
}
```

<div class="content-ad"></div>

# 상속 이해

상속은 기존 클래스(슈퍼 클래스)에서 새 클래스(서브 클래스)를 생성하여 속성과 메소드를 상속받는 메커니즘입니다. 이는 코드 재사용을 촉진하고 클래스 간에 계층적인 관계를 설정합니다.

# 상속의 종류

## 1. 단일 상속

<div class="content-ad"></div>

한 개의 수퍼클래스로부터 속성과 메소드를 상속받는 것을 단일 상속이라고 합니다. Dart에서 클래스는 하나의 수퍼클래스만 확장할 수 있습니다.

예시:

```js
class Animal {
  void eat() {
    print('동물이 먹고 있습니다.');
  }
}

class Dog extends Animal {
  void bark() {
    print('개가 짖고 있습니다.');
  }
}

void main() {
  var dog = Dog();
  dog.eat();  // 출력: 동물이 먹고 있습니다.
  dog.bark(); // 출력: 개가 짖고 있습니다.
}
```

이 예시에서 Dog 클래스는 Animal 클래스로부터 eat() 메소드를 상속받습니다.

<div class="content-ad"></div>

## 2. 다중 상속

다중 상속은 하위 클래스가 다른 클래스의 슈퍼 클래스가 되는 상속 체인에 관련된 개념입니다. 이는 다수의 상속 레벨을 가진 계층적 관계를 설정합니다.

예시:

```dart
class Animal {
  void eat() {
    print('Animal is eating.');
  }
}

class Dog extends Animal {
  void bark() {
    print('Dog is barking.');
  }
}

class Labrador extends Dog {
  void swim() {
    print('Labrador is swimming.');
  }
}

void main() {
  var labrador = Labrador();
  labrador.eat();  // 출력: Animal is eating.
  labrador.bark();  // 출력: Dog is barking.
  labrador.swim();  // 출력: Labrador is swimming.
}
```

<div class="content-ad"></div>

랩라도 클래스는 Dog 클래스를 상속하고, Dog 클래스는 Animal 클래스를 상속합니다.

## 3. 계층 상속

계층적 상속은 여러 하위 클래스가 단일 상위 클래스에서 상속받는 것을 의미합니다. 각 하위 클래스는 상위 클래스에서 공통 특성을 공유하지만 각자 고유한 특성을 가질 수 있습니다.

예시:

<div class="content-ad"></div>

```dart
class Animal {
  void eat() {
    print('Animal is eating.');
  }
}

class Dog extends Animal {
  void bark() {
    print('Dog is barking.');
  }
}

class Cat extends Animal {
  void meow() {
    print('Cat is meowing.');
  }
}

void main() {
  var dog = Dog();
  dog.eat();  // 결과: Animal is eating.
  dog.bark(); // 결과: Dog is barking.

  var cat = Cat();
  cat.eat();  // 결과: Animal is eating.
  cat.meow(); // 결과: Cat is meowing.
}
```

이 예제에서 Dog 및 Cat 클래스는 Animal 클래스를 상속받아 eat() 메서드를 공유합니다.

# 캡슐화 이해하기

캡슐화는 객체 지향 프로그래밍의 핵심 원칙 중 하나로, 데이터(변수) 및 해당 데이터를 조작하는 메서드(함수)를 하나로 묶은 클래스라는 단위로 묶는 것을 목표로 합니다. 이 접근 방식은 개체의 일부 구성 요소에 대한 직접적인 액세스를 제한하여 의도하지 않은 간섭 및 남용을 방지하는 데 필수적입니다. Dart 및 Flutter에서 캡슐화는 멤버 변수 캡슐화, 함수 캡슐화 및 클래스 캡슐화의 세 가지 주요 방법으로 달성됩니다. 각 유형을 구체적인 예를 통해 자세히 살펴보겠습니다.



<div class="content-ad"></div>

## 1. 멤버 변수 캡슐화

멤버 변수 캡슐화는 객체의 내부 상태를 보호하기 위해 변수를 private로 만들고 public 메서드를 통해 제어된 접근을 제공하는 것을 의미합니다. 이를 통해 객체의 데이터가 정의된 방법으로만 수정될 수 있도록 보장할 수 있습니다.

예시:

```js
class Person {
  String _name; // Private 변수

  Person(this._name);

  // name의 Getter
  String get name => _name;

  // name의 Setter
  set name(String name) {
    if (name.isNotEmpty) {
      _name = name;
    }
  }
}

void main() {
  var person = Person('John');
  print(person.name); // 결과: John
  person.name = 'Doe';
  print(person.name); // 결과: Doe
}
```

<div class="content-ad"></div>

이 예제에서 \_name 변수는 private이며 getter 및 setter 메서드를 통해서만 액세스하거나 수정할 수 있습니다. 이 캡슐화는 \_name에 대한 모든 수정이 확인되도록 보장합니다.

## 2. 함수 캡슐화

함수 캡슐화는 일부 메서드를 private으로 만들어 객체의 데이터가 내부적으로 어떻게 조작되는지 제어하는 것을 의미합니다. 필요한 메서드만 공개되고 내부 구현은 숨겨집니다.

예시:

<div class="content-ad"></div>

```js
class Calculator {
  // Public method
  int add(int a, int b) {
    return _performAddition(a, b);
  }

  // Private helper method
  int _performAddition(int a, int b) {
    return a + b;
  }
}

void main() {
  var calc = Calculator();
  print(calc.add(2, 3)); // Output: 5
}
```

여기서 \_performAddition 메서드는 private이며 Calculator 클래스 내에서만 접근할 수 있습니다. public add 메서드는 기능을 노출하면서 내부 작업은 숨겨둡니다.

## 3. Class Encapsulation

클래스 캡슐화는 관련된 변수와 메서드를 하나의 클래스로 묶는 것을 의미합니다. 이는 모듈성과 재사용성을 촉진하여 코드를 보다 조직적이고 관리하기 쉽도록 만듭니다.

<div class="content-ad"></div>

예시:

```dart
class Car {
  String _model;
  int _year;

  Car(this._model, this._year);

  // 자동차 정보를 표시하는 공개 메서드
  void displayInfo() {
    print('Model: $_model, Year: $_year');
  }
}

void main() {
  var myCar = Car('Tesla', 2021);
  myCar.displayInfo(); // 출력: Model: Tesla, Year: 2021
}
```

이 예시에서 Car 클래스는 \_model과 \_year 변수 및 displayInfo 메서드를 캡슐화합니다. 이 구조는 자동차에 관련된 모든 정보와 동작이 함께 그룹화되도록 보장합니다.

# 추상화 이해하기

<div class="content-ad"></div>

추상화는 객체 지향 프로그래밍(OOP)에서 중요한 원칙으로, 문제에 적합한 클래스를 모델링하여 복잡한 시스템을 간단하게 만들어줍니다. 이는 구현 세부 정보를 숨기고 사용자에게 기능만 표시함으로써 도움이 됩니다. Dart와 Flutter에서 추상화는 깔끔하고 유지보수 가능하며 모듈식 코드를 작성하는 데 다양한 형태로 적용될 수 있습니다. 이 블로그에서는 OOP에서 다양한 종류의 추상화인 데이터 추상화, 프로세스 추상화, 공개 지정자를 사용한 추상화, 그리고 비공개 분류자를 사용한 추상화에 대해 다룰 것입니다. 함께 알아보겠습니다!

## 1. 데이터 추상화

데이터 추상화는 필수적인 기능만 노출하고 불필요한 세부 정보를 숨기는 데 초점을 맞춥니다. Dart에서는 데이터 추상화를 주로 추상 클래스와 인터페이스를 사용하여 달성합니다.

예시:

<div class="content-ad"></div>


추상 클래스인 Animal은 사운드()라는 추상 메서드를 정의합니다. 이 메서드는 Dog 클래스에서 구현됩니다. Animal 클래스의 사용자로부터 사운드()의 구현 세부사항을 숨기고 필요한 기능만 노출시킵니다.

## 2. 프로세스 추상화

프로세스 추상화는 복잡한 프로세스나 작업을 간단하고 관리하기 쉬운 메서드로 추상화하는 것을 의미합니다. 이를 통해 복잡한 기능을 작은 재사용 가능한 메서드로 분해하는 데 도움이 됩니다.


<div class="content-ad"></div>

예시:

```dart
class MathOperations {
  double calculateArea(double radius) {
    return _calculateCircleArea(radius);
  }

  // 자세한 계산을 위한 비공개 메서드
  double _calculateCircleArea(double radius) {
    return 3.14 * radius * radius;
  }
}

void main() {
  var math = MathOperations();
  print(math.calculateArea(5)); // 출력: 78.5
}
```

여기서 MathOperations 클래스는 원의 넓이를 계산하는 과정을 \_calculateCircleArea 비공개 메서드로 추상화합니다. 사용자는 내부 계산을 알 필요 없이 calculateArea 메서드와 상호작용할 수 있습니다.

## 3. 공개 지정자를 사용한 추상화

<div class="content-ad"></div>

Dart에서의 공개 지정자는 클래스의 특정 부분을 노출시키면서 다른 부분을 숨길 때 사용됩니다. 이는 클래스의 명확한 인터페이스를 정의하는 데 중요하며 내부 상태를 보호하는 데 도움이 됩니다.

예시:

```dart
class BankAccount {
  String _accountNumber;
  double _balance;

  BankAccount(this._accountNumber, this._balance);

  // 계좌 잔액을 얻기 위한 공개 메서드
  double get balance => _balance;

  // 돈을 입금하는 공개 메서드
  void deposit(double amount) {
    if (amount > 0) {
      _balance += amount;
    }
  }
}

void main() {
  var account = BankAccount('123456789', 1000.0);
  account.deposit(500);
  print(account.balance); // 출력: 1500.0
}
```

이 예시에서는 \_accountNumber 및 \_balance 변수가 비공개이지만 balance getter 및 deposit 메서드는 공개되어 있습니다. 이를 통해 잔액에 제한된 액세스권을 부여하면서 계좌 번호를 숨길 수 있습니다.

<div class="content-ad"></div>

## 4. 비공개 분류자를 사용한 추상화

다트에서의 비공개 분류자는 클래스 내의 세부 사항을 캡슐화하여 클래스 외부에서 특정 속성 및 메서드에 액세스를 제한합니다.

예시:

```dart
class Employee {
  String _name;
  double _salary;

  Employee(this._name, this._salary);

  // 직원 세부 정보를 얻는 공용 메서드
  String getDetails() {
    return 'Name: $_name, Salary: $_salary';
  }

  // 보너스 계산을 위한 비공용 메서드
  double _calculateBonus() {
    return _salary * 0.1;
  }

  // 보너스를 얻기 위한 공용 메서드
  double get bonus => _calculateBonus();
}

void main() {
  var employee = Employee('Alice', 50000);
  print(employee.getDetails()); // 출력: Name: Alice, Salary: 50000
  print(employee.bonus); // 출력: 5000.0
}
```

<div class="content-ad"></div>

이 예에서 \_calculateBonus 메서드는 private이며 Employee 클래스 내에서만 액세스할 수 있습니다. 보너스 getter는 실제 계산 프로세스를 숨기고 계산된 보너스를 노출합니다.

# 결론

Dart 및 Flutter 애플리케이션에서 깨끗하고 유지보수가능하며 확장 가능한 코드를 작성하는 데 OOP의 네 가지 기둥인 캡슐화, 상속, 다형성 및 추상화를 이해하는 것이 중요합니다. 또한 메서드 재정의와 오버로딩과 같은 개념을 이해하면 더 강력한 소프트웨어 솔루션을 설계하고 구현하는 능력이 향상됩니다. 이러한 개념을 숙달하여 OOP의 전체 능력을 활용하여 복잡하고 확장 가능한 애플리케이션을 구축할 수 있습니다.

또한 Dart와 Flutter에서 다양한 유형의 상속을 이해하는 것은 효율적이고 유지보수 가능한 코드베이스를 설계하는 데 중요합니다. 상속을 효과적으로 활용하면 코드 재사용을 촉진하고 계층적 관계를 수립하고 쉽게 확장 가능한 애플리케이션을 구축할 수 있습니다. 간단한 클래스 계층 구조를 작성하거나 복잡한 객체 구조를 설계하든 상속은 Dart 및 Flutter 프로젝트의 아키텍처를 형성하는 데 중요한 역할을 합니다.

<div class="content-ad"></div>

아래 댓글에 Dart와 Flutter에서의 OOP에 대한 생각과 경험을 자유롭게 공유해주세요!
