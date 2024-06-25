---
title: "자바 함수형 인터페이스를 사용하여 의존성을 역전하는 방법"
description: ""
coverImage: "/assets/img/2024-05-23-How-toUseJavaFunctionalInterfacestoInvertDependencies_0.png"
date: 2024-05-23 12:43
ogImage:
  url: /assets/img/2024-05-23-How-toUseJavaFunctionalInterfacestoInvertDependencies_0.png
tag: Tech
originalTitle: "How-to Use Java Functional Interfaces to Invert Dependencies"
link: "https://medium.com/java-content-hub/how-to-use-java-functional-interfaces-to-invert-dependencies-0a0ef2ca8483"
---

자바 프로젝트 내에서 의존성을 역전시키기 위해 자바 함수형 인터페이스를 사용해 본 적이 있나요? 이 기사에서는 Supplier, Consumer 및 Function 세 가지 주요 인터페이스를 활용하여 이를 수행하는 방법을 살펴볼 것입니다.

![이미지](/assets/img/2024-05-23-How-toUseJavaFunctionalInterfacestoInvertDependencies_0.png)

## Supplier

Supplier 인터페이스는 입력 매개변수가 필요하지 않은 객체를 제공해야 할 때 사용됩니다. 다음은 Supplier 인터페이스입니다:

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
public interface Supplier<T> {
   T get();
}
```

이 인터페이스를 활용하는 필요성을 더 잘 이해하기 위해 코드 몇 줄을 확인해 봅시다.

```java
public class Logger {
   public void log(String message) {
      if (isLogEnabled()) {
         write(message);
      }
   }
}

// Logger 클래스 사용 예시
public class Controller {
   @Inject Logger logger;

   public void execute() {
      logger.log(generateLogMessage());
   }
}
```

위 코드에서는 로깅이 활성화되어 있을 때 로그 메시지를 작성하는 Logger 클래스가 있습니다. Controller 클래스는 generateLogMessage 메서드의 결과를 전달하여 로거를 호출합니다. 지금까지는 모든 것이 잘 보입니다. 그러나 만약 generateLogMessage 메서드가 많은 처리를 필요로 하거나 상당한 리소스를 소비하며 로깅이 비활성화된 경우를 상상해보세요. 이러한 경우 유용한 리소스가 낭비되며 생성된 로그 메시지가 활용되지 않을 것입니다.

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

이 문제의 해결책은 Logger 클래스에 Supplier를 전달하여 요청 시 메시지를 반환하고, 로거가 로그가 활성화된 경우에만 메서드를 호출하도록 하는 것입니다. 아래와 같이 구현할 수 있습니다:

```js
public class Logger{
   public void log(Supplier<String> messageSupplier){
      if(isLogEnabled()){
        write(messageSupplier.get());
      }
   }
}

// Logger 클래스 사용 예제
public class Controller{
   @Inject Logger logger;

   public void execute(){
      logger.log(() -> generateLogMessage());
   }
}
```

이제 generateLogMessage 메서드는 Supplier의 get 메서드가 호출될 때에만 실행되며, 로그가 비활성화된 경우 자원을 절약할 수 있습니다. 또한 Supplier를 사용한 이러한 솔루션은 로깅을 위한 매우 복잡한 로직을 구현할 유연성을 제공하며 필요할 때에만 호출됨을 보장합니다.

# 기능

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

Function 인터페이스를 사용하면 매개변수를 받아 결과를 생성하는 함수를 정의할 수 있습니다. 아래는 Function 인터페이스입니다 (일부 기본 메서드는 생략됨):

```js
public interface Function<T, R>{
   R apply(T t);
}
```

Function 인터페이스를 탐색하기 시작하려면, 판매 주문에서 품목의 가격을 계산하는 책임을 지는 클래스를 살펴보겠습니다. 이 클래스는 제품, 수량 및 할인 (0에서 100까지 범위)과 같은 입력을 가져옵니다:

```js
public class PriceCalculator{
   public BigDecimal calculatePrice(Product product,
                                    Integer quantity,
                                    BigDecimal discount){
     var grossPrice = product.getUnitPrice()
                             .multiply(BigDecimal.valueOf(quantity));
     var discountAmount = grossPrice.multiply(discount)
                                    .divide(BigDecimal.valueOf(100));
     return grossPrice.minus(discountAmount);
   }
}

// 사용 예시
var result = priceCalculator(product, 10, BigDecimal.value(10));
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

이 클래스는 초기에 총 가격을 계산하고 할인을 적용한 후 총 가격에서 빼는 작업을 합니다. 이제 새로운 요구 사항을 고려해봅시다: 가격에 통화 변환을 수행해야 합니다.

하나의 접근 방식은 이 클래스에 직접 통화 변환 로직을 추가하는 것일 수 있지만, 이는 버그를 도입할 수 있습니다. 더 견고한 해결책은 통화 변환을 처리하는 함수 매개변수를 추가하는 것입니다.

```js
public class PriceCalculator{
   public BigDecimal calculatePrice(
                        Product product,
                        Integer quantity,
                        BigDecimal discount,
                        Function<BigDecimal,BigDecimal> converterFunction){
     var grossPrice = product.getUnitPrice()
                             .multiply(BigDecimal.valueOf(quantity));
     var discountAmount = grossPrice.multiply(discount)
                                    .divide(BigDecimal.valueOf(100));
     var netPrice = grossPrice.minus(discountAmount);
     return converterFunction.apply(netPrice);
   }
}

// Usage example
var result = priceCalculator(product,
                             10,
                             BigDecimal.value(10),
                             netPrice -> netPrice.multiply(CURRENCY_RATE));
```

새로운 요구 사항의 추가로 인해 최소한의 영향을 받았고, 의존성을 성공적으로 역전시켰습니다. PriceCalculator 클래스는 더 이상 통화 변환을 처리할 필요가 없으며, 대신 제공된 함수를 호출하고 최종 결과를 반환합니다. 이 설계를 통해 PriceCalculator 클래스를 수정하지 않고도 어떤 통화로든 변환할 수 있게 되었습니다.

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

가격 계산기 클래스를 변경하지 않고 이 요구 사항을 해결하는 다양한 방법이 있습니다. PriceCalculator를 호출하는 퍼사드로 작동하는 또 다른 클래스를 만들어 화폐 변환을 수행할 수 있습니다. 일반적으로 어떤 솔루션을 따를지는 프로젝트 결정입니다.

# 소비자

Consumer 인터페이스를 통해 매개변수를 받아 특정 작업을 수행하고 값을 반환하지 않는 함수를 정의할 수 있습니다. 다음은 Consumer 인터페이스입니다 (일부 기본 메서드는 생략됨):

```js
public interface Consumer<T> {
    void accept(T t);
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

소비자 인터페이스의 예제를 살펴보기 위해 이 클래스를 살펴봅시다. 여기에는 엔티티에 일부 정보를 설정한 후 데이터베이스에 저장하는 작업이 포함되어 있습니다:

```java
public class EntitySaver{
   public void create(Entity entity){
      entity.setCreationDate(new Date());
      database.insert(entity);
   }
}

// 사용 예시
entitySaver.create(entity);
```

이제 엔티티가 생성될 때 다른 클래스에 알림을 보내어야 하는 경우를 가정해 봅시다. 그러나 create 메서드 인터페이스를 수정할 수 없는 경우, 소비자 인터페이스를 사용하여 발행-구독 패턴을 구현할 수 있습니다. 다음은 이를 어떻게 달성할 수 있는지에 대한 예시입니다:

```java
public class EntitySaver{
   private List<Consumer<Entity>> consumerList = new ArrayList<>();

   public void register(Consumer<Entity> consumer){
      consumerList.add(consumer);
   }

   public void create(Entity entity){
      entity.setCreationDate(new Date());
      database.insert(entity);
      consumerList.forEach(consumer -> consumer.accept(entity));
   }
}

// 사용 예시
entitySaver.register(entity -> log.info(entity));
entitySaver.register(entity -> mailerService.notifyUser(entity));
entitySaver.create(entity);
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

이 발행-구독 패턴의 구현에서는 Consumer 인터페이스를 활용합니다. EntitySaver 클래스는 이제 Consumer 리스트를 유지하고 등록 메서드를 포함하여 소비자를이 목록에 추가합니다. create 메서드의 인터페이스는 변경되지 않았지만, 만들어진 엔티티를 '소비'하기위한 한 줄의 코드를 도입했습니다.

# 결론

Java 기능 인터페이스는 많은 년 전에 소개되었으며, Java에서 개발을하던 방식에 큰 영향을 미쳤습니다. 우리는 이를 람다 함수로 사용할 수 있지만 의존성을 뒤집고 코드를 더 깔끔하게 만드는 데 사용할 수도 있습니다.
