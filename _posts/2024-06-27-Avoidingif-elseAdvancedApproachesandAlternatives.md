---
title: "제어문을 더 스마트하게 if-else를 피하는 고급 기법과 대안 "
description: ""
coverImage: "/assets/img/2024-06-27-Avoidingif-elseAdvancedApproachesandAlternatives_0.png"
date: 2024-06-27 19:16
ogImage: 
  url: /assets/img/2024-06-27-Avoidingif-elseAdvancedApproachesandAlternatives_0.png
tag: Tech
originalTitle: "Avoiding if-else: Advanced Approaches and Alternatives"
link: "https://medium.com/@ia_taras/avoiding-if-else-advanced-approaches-and-alternatives-698f30f65445"
---


<img src="/assets/img/2024-06-27-Avoidingif-elseAdvancedApproachesandAlternatives_0.png" />

대부분의 개발자들은 다양한 상황에 대비하기 위해 if-else 문을 사용합니다. 그럼에도 불구하고, 추가적인 비즈니스 요구 사항을 이러한 연쇄에 넣으면 코드를 필요 이상으로 복잡하게 만들 수도 있고 오류가 발생할 수 있습니다. 우리는 시스템의 견고성을 보장할 뿐만 아니라 예상치 못한 상황에서도 쉽게 업데이트할 수 있도록 솔루션을 만들겠다는 것이 좋습니다. 이렇게 하면 우리의 코드는 앞으로 우리를 위해 적합하고 쉽게 적응할 수 있을 것입니다.

이 글에서는 Java를 사용하여 간단한 계산기에서 연산을 처리하는 다양한 방법을 탐색할 것입니다. 우리의 코드에서 수학 연산(덧셈, 뺄셈, 곱셈, 나눗셈)의 처리를 개선하는 것이 목표입니다. 운영 유형과 두 피연산자를 포함하는 요청을 받는 계산기 예제를 사용하여 if-else, switch, 전략 패턴과 같은 여러 접근 방식을 분석하고 구현해볼 것입니다. 각 방법의 개념과 이점을 설명하는 데 중점을 둘 것입니다.

## 요청 구조

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

먼저, 서로 다른 접근 방식을 보여주기 위해 사용될 요청 구조를 정의해 봅시다.

```java
public class CalculationRequest {
 private final Operation operation;
 private final int first;
 private final int second;

 // 생성자, 게터, 세터
}

enum Operation{
  ADD,
  SUBTRACTION,
  DIVISION,
  MULTIPLICATION;  
}
```

# If-Else 문

If-else 문은 프로그래밍에서 조건을 처리하는 가장 간단하고 널리 사용되는 구성 중 하나입니다. 이들은 특정 조건이 충족되었는지에 따라 특정 코드 블록을 실행할 수 있도록 합니다. 계산기의 맥락에서, if-else 문은 덧셈, 뺄셈, 곱셈 및 나눗셈과 같은 다양한 작업을 처리하는 데 사용될 수 있습니다. 다음 예제는 이러한 작업을 수행하기 위해 if-else 문을 사용하는 방법을 보여줍니다.

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
public static Integer calculate(CalculationRequest request) {
 var first = request.getFirst();
 var second = request.getSecond();
 var operation = request.getOperation();
 
if (Operation.ADD.equals(operation)) {
   return first + second;
 } else if (Operation.SUBTRACTION.equals(operation)) {
   return first - second;
 } else if (Operation.DIVISION.equals(operation)) {
   if (second == 0) {
     throw new IllegalArgumentException("Can't be zero");
   }
   return first / second;
 } else if (Operation.MULTIPLICATION.equals(operation)) {
   return first * second;
 } else {
   throw new IllegalStateException("Operation not found");
 }
}
```

장점:

- 단숨함: 이해하고 구현하기 쉽습니다.
- 명확함: 각 조건에서 무슨 일이 벌어지는지 명확히 보여줍니다.

단점:

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

- 유지보수: 로직을 변경하려면 여러 곳에서 수정해야 하므로 오류 발생 가능성이 높아집니다. 예를 들어, 새로운 작업이 추가된다면 if-else 체인에 다른 조건을 추가해야 하므로 조건을 놓칠 가능성이 커집니다. 다양한 곳에서의 변경 사항은 코드의 디버깅과 테스트를 복잡하게 만듭니다.
- 확장성: 새로운 작업을 추가하려면 기존 코드를 수정해야 하므로 SOLID의 개방/폐쇄 원칙(OCP)을 위반합니다. 각각의 새로운 조건은 기존 코드를 수정해야 하므로 유연성이 줄어들고 변경에 견고하지 못해집니다. 이는 장기적으로 기술적 부채가 증가하고 코드 품질이 저하될 수 있습니다.

# Switch 문

일부 경우에는 if-else 체인 대비 switch 문이 더 가독성이 좋고 편리할 수 있습니다. 코드를 더 잘 구조화할 수 있으며 긴 조건 체인을 피할 수 있습니다. switch 문을 사용하는 것을 고려해 봅시다.

```js
public static Integer calculate(CalculationRequest request) {
 var first = request.getFirst();
 var second = request.getSecond();
 var operation = request.getOperation();

return switch (operation) {
   case ADD -> first + second;
   case SUBTRACTION -> first - second;
   case DIVISION -> {
       if (second == 0) {
       throw new IllegalArgumentException("Can't be zero");
     }
     yield first / second;
   }
   case MULTIPLICATION -> first * second;
 };
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

장점:

- 가독성: 긴 if-else 체인과 비교하여 구조화된 형태로 코드가 되어 더 조밀하고 쉽게 읽을 수 있습니다.
- 간소화: 서로 다른 경우들을 명확히 분리하여 코드를 더 깔끔하게 만듭니다.

단점:

- 확장성: if-else와 마찬가지로 새로운 작업을 추가하려면 기존 코드를 변경해야 하므로 SOLID 원칙 중 개방/폐쇄 원칙을 위반합니다.
- 유연성: switch 문은 다른 방법보다 유연성이 떨어질 수 있습니다. 예를 들어 복잡한 논리나 상태를 쉽게 통합하지 못할 수 있습니다. 이는 더 복잡한 처리가 필요한 고급 사용 사례에 적합하지 않게 만듭니다.

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

# 전략 패턴

전략 패턴은 알고리즘 패밀리를 정의하고 각각을 캡슐화하여 교체가 가능하도록 하는 것을 허용합니다. 이를 통해 클라이언트는 코드를 변경하지 않고 다른 알고리즘을 사용할 수 있습니다. 계산기의 경우, 각 연산(덧셈, 뺄셈, 곱셈, 나눗셈)은 별도의 전략으로 표현될 수 있습니다. 이는 새로운 연산을 기존 코드를 변경하지 않고 추가할 수 있어 확장성과 유지보수성을 향상시킵니다.

장점:

- 확장성: 기존 코드를 변경하지 않고 새로운 전략을 쉽게 추가할 수 있습니다. 이는 미래에 지원이나 추가되어야 하는 새 기능을 처리해야 하는 상황에서 특히 유용합니다.
- SOLID 지원: 이 패턴은 단일 책임 원칙(SRP)을 지원합니다. 각 전략은 특정 연산에 대해 책임이 있습니다. 또한 개방/폐쇄 원칙(OCP)을 지원하며 새 전략을 추가할 때 기존 클래스를 변경하지 않아도 됩니다.
- 유연성: 알고리즘은 적절한 전략을 대체하여 런타임에 쉽게 변경할 수 있습니다. 이로써 시스템이 더 유연하고 변화하는 요구 사항에 적응할 수 있습니다.

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

단점:
- 복잡성: 여러 전략을 구현할 때 코드에 추가적인 복잡성을 더할 수 있습니다. 클래스 수가 증가하면 프로젝트 관리가 어려워질 수 있습니다.

다양한 구현 옵션을 살펴보겠습니다:

## Enum

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

이 예시에서는 추상 apply 메소드를 가진 Operation enum을 만듭니다. 각 enum 요소는 이 메소드의 구현에 대응합니다. 이를 통해 각 작업의 로직을 별도의 열거 요소로 캡슐화하여 코드를 보다 구조화되고 유지보수 가능하게 만듭니다.

```java
public enum Operation {
    ADD {
        @Override
        Integer apply(int first, int second) {
            return first + second;
        }
    },
    SUBTRACTION {
        @Override
        Integer apply(int first, int second) {
            return first - second;
        }
    },
    DIVISION {
        @Override
        Integer apply(int first, int second) {
            if (second == 0) {
                throw new IllegalArgumentException("Can't be zero");
            }
            return first / second;
        }
    },
    MULTIPLICATION {
        @Override
        Integer apply(int first, int second) {
            return first * second;
        }
    };

  abstract Integer apply(int first, int second);
}
```

사용법:

```java
public static Integer calculate(CalculationRequest request) {
    var first = request.getFirst();
    var second = request.getSecond();
    var operation = request.getOperation();

    return operation.apply(first, second);
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

## 객체 지도

OperationStrategy 인터페이스는 각 작업에 구현되어야 하는 apply 메서드를 정의하여 모든 작업에 대한 단일 계약을 생성하고, 새로운 전략을 간편하게 추가할 수 있도록 합니다.

```js
public interface OperationStrategy { 
 Integer apply(int first, int second); 
}
```

각 작업은 OperationStrategy 인터페이스를 구현하는 별도의 클래스로 구현됩니다. 각 클래스는 해당 작업을 수행하기 위해 apply 메서드를 구현합니다.

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

```java
class AddOperationStrategy implements OperationStrategy {
    @Override
    public Integer apply(int first, int second) {
        return first + second;
    }
}

class SubtractionOperationStrategy implements OperationStrategy {
    @Override
    public Integer apply(int first, int second) {
        return first - second;
    }
}

class DivisionOperationStrategy implements OperationStrategy {
    @Override
    public Integer apply(int first, int second) {
        if (second == 0) {
            throw new IllegalArgumentException("Can't be zero");
        }
        return first / second;
    }
}

class MultiplicationOperationStrategy implements OperationStrategy {
    @Override
    public Integer apply(int first, int second) {
        return first * second;
    }
}
```

STRATEGY_OBJECT_MAP이라는 맵을 만들어 Operation 열거형의 값들을 키로, 대응하는 OperationStrategy 구현체들을 값으로 가지게 했습니다. 이를 통해 각 연산에 필요한 전략을 빠르게 찾아서 사용할 수 있습니다.

```java
public static final Map<Operation, OperationStrategy> STRATEGY_OBJECT_MAP =
            Map.ofEntries(
                    Map.entry(Operation.ADD, new AddOperationStrategy()),
                    Map.entry(Operation.SUBTRACTION, new SubtractionOperationStrategy()),
                    Map.entry(Operation.DIVISION, new DivisionOperationStrategy()),
                    Map.entry(Operation.MULTIPLICATION, new MultiplicationOperationStrategy())
            );
```

이 메소드는 맵으로부터 필요한 전략을 찾아 apply 메소드를 호출하여 연산을 수행합니다.

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
public static Integer calculate(CalculationRequest request) {
    var first = request.first();
    var second = request.second();
    var operation = request.operation();

    return STRATEGY_OBJECT_MAP.get(operation).apply(first, second);
}
```

## 함수 맵

이 방법은 각 연산을 위한 함수형 인터페이스를 사용하고 키는 연산이고 값은 함수인 맵을 생성합니다. 이를 통해 각 전략에 대해 별도의 클래스를 생성할 필요없이 코드를 더 간단하고 조밀하게 만들 수 있습니다.

```js
public static final Map<Operation, BiFunction<Integer, Integer, Integer>> STRATEGY_FUNCTION_MAP;

static {
    STRATEGY_FUNCTION_MAP = Map.ofEntries(
        Map.entry(Operation.ADD, (first, second) -> first + second),
        Map.entry(Operation.SUBTRACTION, (first, second) -> first - second),
        Map.entry(Operation.DIVISION, (first, second) -> {
            if (second == 0) {
                throw new IllegalArgumentException("Can't be zero");
            }
            return first / second;
        }),
        Map.entry(Operation.MULTIPLICATION, (first, second) -> first * second)
    );
}
public static Integer calculate(CalculationRequest request) {
    var first = request.getFirst();
    var second = request.getSecond();
    var operation = request.getOperation();
    
    return STRATEGY_FUNCTION_MAP.get(operation).apply(first, second);
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

함수 맵을 사용하는 것은 각각 별도의 클래스를 만들지 않고 일련의 작업을 빠르고 쉽게 구현해야 할 때 좋은 선택입니다. 그러나 더 복잡한 시나리오의 경우 객체 전략이 더 적합합니다.

# 결론

각 접근 방식에는 장단점이 있습니다. if-else 및 switch 문은 간단하고 사용하기 쉽지만 조건이 증가함에 따라 관리하기 어려워집니다. 새로운 작업을 추가할 때 기존 코드를 변경하지 않고 처리해 주는 모든 작업이 어렵습니다.

반면에 전략 패턴은 작업을 처리하는 더 효율적이고 유연한 방식을 제공합니다. SOLID 원칙을 따르므로 기존 코드를 방해하지 않고 새로운 작업을 손쉽게 도입할 수 있습니다. 이는 유지보수 및 확장이 용이한 코드로 이어지며 새로운 비즈니스 요구에 쉽게 적응할 수 있습니다. 처음에는 약간 복잡해 보일 수 있지만, 강력하고 유연하며 확장 가능한 코드 기반에서 얻는 이점을 발견하게 되어 그가 그것이 그만한 가치가 있다는 것을 알게 될 것입니다.