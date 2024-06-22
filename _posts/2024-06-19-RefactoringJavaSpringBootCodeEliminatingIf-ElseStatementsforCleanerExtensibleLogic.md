---
title: "Java Spring Boot 코드 리팩터링 더 깔끔하고 확장 가능한 로직을 위해 If-Else 문 제거하기"
description: ""
coverImage: "/assets/img/2024-06-19-RefactoringJavaSpringBootCodeEliminatingIf-ElseStatementsforCleanerExtensibleLogic_0.png"
date: 2024-06-19 09:59
ogImage: 
  url: /assets/img/2024-06-19-RefactoringJavaSpringBootCodeEliminatingIf-ElseStatementsforCleanerExtensibleLogic_0.png
tag: Tech
originalTitle: "Refactoring Java Spring Boot Code: Eliminating If-Else Statements for Cleaner, Extensible Logic"
link: "https://medium.com/@akintopbas96/refactoring-java-spring-boot-code-eliminating-if-else-statements-for-cleaner-extensible-logic-f1314cf9724e"
---


if-else 문은 널리 사용되지만 과용하면 복잡하고 유지보수가 어려운 코드를 작성하게 될 수 있습니다. 이 기사에서는 Java Spring Boot 프로젝트에서 if-else 구조의 사용을 줄이는 다양한 전략을 탐색하며 코드를 모듈화하고 유지보수 가능하며 가독성 있게 만드는 데 초점을 맞춥니다.

![image](/assets/img/2024-06-19-RefactoringJavaSpringBootCodeEliminatingIf-ElseStatementsforCleanerExtensibleLogic_0.png)

## if-else 문 줄이는 전략

- 전략 패턴
- Enum 사용
- 다형성
- 람다 표현식 및 함수형 인터페이스
- 명령 패턴
- 가드 절(recipes)

<div class="content-ad"></div>

각 전략에 대해 예제와 함께 자세히 파헤쳐 봅시다.

### 1. 전략 패턴

전략 패턴은 알고리즘의 집합을 정의하고, 각각을 캡슐화하며, 서로 교환할 수 있게 만드는 패턴입니다. 이 패턴은 특정 작업을 수행하는 여러 방법이 있는 경우 유용합니다.

### 예제: 결제 처리 시스템

<div class="content-ad"></div>

먼저 PaymentStrategy 인터페이스를 정의합니다:

```java
public interface PaymentStrategy {
    void pay(double amount);
}
```

다음으로 다양한 결제 전략을 구현합니다:

```java
@Component
public class CreditCardPayment implements PaymentStrategy {
    @Override
    public void pay(double amount) {
        // 신용카드 결제 처리 로직
        System.out.println("신용카드를 사용하여 " + amount + " 결제되었습니다.");
    }
}

@Component
public class PaypalPayment implements PaymentStrategy {
    @Override
    public void pay(double amount) {
        // PayPal 결제 처리 로직
        System.out.println("PayPal을 사용하여 " + amount + " 결제되었습니다.");
    }
}
```

<div class="content-ad"></div>

테이블 태그를 마크다운 형식으로 변경하십시오.


| 구분 | 설명 |
|---|---|
| 1 | 첫 번째 |
| 2 | 두 번째 |


<div class="content-ad"></div>

## 예시: 주문 상태 관리

다양한 동작을 가진 OrderStatus enum을 정의합니다:

```js
public enum OrderStatus {
    NEW {
        @Override
        public void handle() {
            System.out.println("신규 주문 처리 중.");
        }
    },
    SHIPPED {
        @Override
        public void handle() {
            System.out.println("주문 발송됨.");
        }
    },
    DELIVERED {
        @Override
        public void handle() {
            System.out.println("주문 배송 완료됨.");
        }
    };

    public abstract void handle();
}
```

이 enum을 서비스에서 사용하세요:

<div class="content-ad"></div>

```java
@Service
public class OrderService {
    public void processOrder(OrderStatus status) {
        status.handle();
    }
}
```

## 3. Polymorphism

다형성은 객체를 실제 클래스가 아닌 부모 클래스의 인스턴스로 취급할 수 있게 합니다. 이를 통해 부모 클래스의 참조를 통해 파생 클래스의 재정의된 메소드를 호출할 수 있습니다.

## Example: Notification System


<div class="content-ad"></div>

```java
// `Notification` 인터페이스와 그 구현 클래스 정의:

public interface Notification {
    void send(String message);
}

public class EmailNotification implements Notification {
    @Override
    public void send(String message) {
        // 이메일 전송 로직
        System.out.println("이메일 전송 중: " + message);
    }
}

public class SmsNotification implements Notification {
    @Override
    public void send(String message) {
        // SMS 전송 로직
        System.out.println("SMS 전송 중: " + message);
    }
}

// 다형성을 사용하는 서비스 생성:

@Service
public class NotificationService {
    private final List<Notification> notifications;

    public NotificationService(List<Notification> notifications) {
        this.notifications = notifications;
    }

    public void notifyAll(String message) {
        for (Notification notification : notifications) {
            notification.send(message);
        }
    }
}
```

<div class="content-ad"></div>

## 4. 람다 표현식과 함수형 인터페이스

람다 표현식은 코드를 간단하게 만들어 줄 수 있어, 특히 작고 단일 메서드 인터페이스를 다룰 때 유용합니다.

## 예시: 할인 서비스

람다 표현식을 사용하는 할인 서비스를 정의하세요:

<div class="content-ad"></div>

```java
import java.util.HashMap;
import java.util.Map;
import java.util.function.Function;

public class DiscountService {
    private Map<String, Function<Double, Double>> discountStrategies = new HashMap<>();

    public DiscountService() {
        discountStrategies.put("SUMMER_SALE", price -> price * 0.9);
        discountStrategies.put("WINTER_SALE", price -> price * 0.8);
    }

    public double applyDiscount(String discountCode, double price) {
        return discountStrategies.getOrDefault(discountCode, Function.identity()).apply(price);
    }
}
```

## 5. Command Pattern

The Command Pattern encapsulates a request as an object, thereby allowing you to parameterize clients with queues, requests, and operations.

## Example: File Operations

<div class="content-ad"></div>

아래는 Command 인터페이스와 구체적인 명령어를 정의한 코드입니다:

```js
public interface Command {
    void execute();
}

public class OpenFileCommand implements Command {
    private FileSystemReceiver fileSystem;

    public OpenFileCommand(FileSystemReceiver fs) {
        this.fileSystem = fs;
    }

    @Override
    public void execute() {
        this.fileSystem.openFile();
    }
}

public class CloseFileCommand implements Command {
    private FileSystemReceiver fileSystem;

    public CloseFileCommand(FileSystemReceiver fs) {
        this.fileSystem = fs;
    }

    @Override
    public void execute() {
        this.fileSystem.closeFile();
    }
}
```

아래는 FileSystemReceiver와 Invoker를 정의한 코드입니다:

```js
public interface FileSystemReceiver {
    void openFile();
    void closeFile();
}

public class UnixFileSystemReceiver implements FileSystemReceiver {
    @Override
    public void openFile() {
        System.out.println("Unix 운영체제에서 파일을 엽니다.");
    }

    @Override
    public void closeFile() {
        System.out.println("Unix 운영체제에서 파일을 닫습니다.");
    }
}

public class FileInvoker {
    private Command command;

    public FileInvoker(Command cmd) {
        this.command = cmd;
    }

    public void execute() {
        this.command.execute();
    }
}
```

<div class="content-ad"></div>

## 6. 가드 구문

가드 구문은 조건을 조기에 처리하여 중첩 구조를 줄여 코드를 더 읽기 쉽게 만드는 방법을 제공합니다.

## 예시: 사용자 유효성 검사

사용자 입력을 유효성 검사하기 위해 if-else 문을 중첩하는 대신, 가드 구문을 사용하여 잘못된 경우를 미리 처리하세요.

<div class="content-ad"></div>

```java
public class UserService {
    public void registerUser(User user) {
        if (user == null) {
            throw new IllegalArgumentException("User cannot be null");
        }
        if (user.getName() == null || user.getName().isEmpty()) {
            throw new IllegalArgumentException("User name cannot be empty");
        }
        if (user.getEmail() == null || user.getEmail().isEmpty()) {
            throw new IllegalArgumentException("User email cannot be empty");
        }
        // Proceed with registration
        System.out.println("Registering user: " + user.getName());
    }
}
```

이 접근 방식을 통해 잘못된 조건을 조기에 처리하고 주요 로직을 보다 깔끔하고 이해하기 쉽게 유지할 수 있습니다.

# 결론

이러한 전략을 적용함으로써 Java Spring Boot 프로젝트에서 if-else 문의 사용을 크게 줄일 수 있습니다. 이는 코드를 더 읽기 쉽게 만들 뿐만 아니라 유지보수성과 확장성을 향상시킵니다. 이러한 패턴과 관행을 받아들여 더 깨끗하고 효율적인 코드를 작성해 보세요.


<div class="content-ad"></div>

# 참고 자료

- Gamma, E., Helm, R., Johnson, R., & Vlissides, J. (1994). Design Patterns: Elements of Reusable Object-Oriented Software. Addison-Wesley.
- Bloch, J. (2018). Effective Java. Addison-Wesley.
- Fowler, M. (2019). Refactoring: Improving the Design of Existing Code. Addison-Wesley.
- Freeman, E., & Robson, E. (2020). Head First Design Patterns: Building Extensible and Maintainable Object-Oriented Software. O’Reilly Media.
- Beck, K. (2003). Test Driven Development: By Example. Addison-Wesley.

코딩을 즐기세요! 👨‍💻👩‍💻