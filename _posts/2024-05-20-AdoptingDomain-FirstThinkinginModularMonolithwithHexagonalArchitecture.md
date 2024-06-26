---
title: "도메인을 최우선으로 하는 생각을 Hexagonal Architecture를 활용한 모듈식 단일체에 도입하기"
description: ""
coverImage: "/assets/img/2024-05-20-AdoptingDomain-FirstThinkinginModularMonolithwithHexagonalArchitecture_0.png"
date: 2024-05-20 15:36
ogImage:
  url: /assets/img/2024-05-20-AdoptingDomain-FirstThinkinginModularMonolithwithHexagonalArchitecture_0.png
tag: Tech
originalTitle: "Adopting Domain-First Thinking in Modular Monolith with Hexagonal Architecture"
link: "https://medium.com/itnext/adopting-domain-first-thinking-in-modular-monolith-with-hexagonal-architecture-f9e4921ac18d"
---

![Image](/assets/img/2024-05-20-AdoptingDomain-FirstThinkinginModularMonolithwithHexagonalArchitecture_0.png)

Part 1:

Part 2:

In the first blog, we explored the concept of Modular Monolith, what is a module and how DDD strategy patterns can be used to create modular applications.

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

두 번째 블로그에서는 Spring Modulith가 모듈 모노리스 응용 프로그램에서 아키텍처 유효성 검사, 독립 모듈의 테스팅 및 이벤트 기반 통합을 어떻게 용이하게 하는지 살펴보았어요.

이 블로그에서는 단일 모듈을 자세히 살펴보고 헥사고날 아키텍처가 핵심 비즈니스 로직의 유지보수성 및 테스트를 어떻게 향상시키는지 이해할 거에요. 또한 헥사고날 아키텍처가 DDD 및 모듈 모노리스 응용 프로그램의 맥락 속에 어떻게 맞는지도 이해할 거에요.

헥사고날 아키텍처를 배우는 데 한 가지 어려움은 용어로 오인될 수 있다는 것이에요. 헥사곤 모양이나 숫자 여섯과는 아무런 상관이 없답니다. "포트"와 "어댑터"라는 용어가 과부하되어 있어서 여러분은 무엇을 의미하는지 배우기 전에 스스로의 해석을 잊어야 해요. 아키텍처의 층이 없다는 점(바깥쪽과 안쪽 이외에)과 의견이 분분하지 않은 아키텍처의 특성으로 인해 누구나 자신의 방식대로 구현할 수 있어요.

나는 헥사고날 아키텍처를 이해하고 Java와 Spring Boot 응용 프로그램에서 적용하는 방법에 대해 시도했을 때, 헥사고날을 사용하지 않고 응용 프로그램을 작성한 후 문제를 겪고 하나씩 해결하여 어떤 것이 헥사고날인지 알아냈어요. 하지만 그것은 몇 년이 걸릴 수 있어요! 이 블로그(시리즈)에서는 이 과정을 더 짧은 시간 안에 기록하고 싶어요.

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

기업 문제를 해결하기 위한 애플리케이션을 개발하는 업무를 맡았습니다. 해당 애플리케이션은 웹을 통해 접속되며, 일부 비즈니스 로직을 구현하고 데이터를 데이터베이스에 저장할 것입니다 (당연히). 애플리케이션을 설계하는 과정에서 어떤 생각을 가지게 되시나요?

- 웹 애플리케이션이어야 하므로, 최신하면서 반짝이는 자바스크립트 프레임워크(React?)를 사용하여 JSON을 제공하는 REST API 및 멋진 UI를 구축할 것입니다.
- 비즈니스 요구 사항이 NoSQL DB를 필요로 하지 않는 한 관계형 데이터베이스를 사용할 것입니다.
- 비즈니스 요구 사항을 바탕으로 여러 엔티티를 식별하고 ERD에 매핑할 것입니다.

이런 내용이 익숙하시나요?

3번 항목에 집중해 봅시다. ERD 여부와 상관없이, 데이터 모델을 도출하기 위해 여러 엔티티를 식별하고 그것을 시간이 흘러도 발전시킬 수 있는 것은 모든 웹 백엔드 엔지니어가 해야 하는 일이며 시작점이 될 수도 있습니다! 저는 이를 여러 차례 경험해 보았습니다.

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

이 방법은 (의식적인 아키텍처가 적용되지 않을 때 기본으로 사용되는) 계층화된/N계층 아키텍처로 구성된 애플리케이션을 만들게 됩니다. 데이터 모델은 JPA 엔티티로 구성되며, Repositories를 통해 영속화되고, 비즈니스 로직은 Services에 구현됩니다.

이러한 구현의 핵심 특성 중 하나는 비즈니스 로직(Services)이 데이터 모델에서 작동하며 데이터베이스에 의존하는 것입니다. 특히 JPA/JDBC가 실제 데이터베이스 기술을 추상화하고 있기 때문에 바로 알아차리기 어렵습니다. 하지만 데이터베이스 구현(테이블 구조, 외래 키 관계, 인덱스 등)이 비즈니스 로직 구현을 주도하는 것이 아니라 그 반대로 작용하고 있습니다. 아래 그림에서 보듯이, 애플리케이션 서비스는 Spring과 JPA/Hibernate와 같은 기술과 강하게 결합됩니다.

![이미지](/assets/img/2024-05-20-AdoptingDomain-FirstThinkinginModularMonolithwithHexagonalArchitecture_1.png)

## 의존성 역전

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

자, 이제 우리는 다르게 생각하고 문제에 접근해 보겠습니다. 데이터 모델에 초점을 맞추는 대신, 비즈니스 로직과 제약 조건을 포착하는 여러 클래스를 만들어 보겠습니다. 이를 도메인 모델이라고 부르겠습니다.

이 접근 방식에서는, 서비스가 스프링 데이터 리포지토리를 직접 사용하는 대신, 의존성을 반전시키기 위해 인터페이스를 도입하여 서비스가 필요로 하는 동작을 정의하고 이것이 어떻게 구현되었는지 신경쓰지 않습니다. 실은 스프링 데이터 리포지토리는 Java 인터페이스로 정의되어 있지만, 다이내믹 프록시를 통해 구현을 결합하는 인터페이스는 아닙니다.

의존성을 반전시키기 위해 사용된 인터페이스를 포트(port)로, 인터페이스의 구현은 어댑터(adapter)로 부릅니다. 이들은 도메인 모델에 의해 주도(들어온)되므로 주도 또는 보조(secondary)로 알려져 있습니다. 이는 헥사고날 아키텍처에서 사용되는 용어입니다.

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

한편, 유스 케이스를 구현하는 데 사용되는 Application 서비스도 포트입니다. 차이점은 Java 인터페이스가 아니라 일반 클래스임을 의미합니다. 또한 해당 어댑터와 함께 사용됩니다. 이 어댑터는 기술을 접근하여 우리의 사용 사례에 연결합니다(REST API, GraphQL, 람다 등). 이러한 포트와 어댑터는 도메인 모델을 사용하므로 드라이버 또는 주요 요소로 알려져 있습니다. 이들은 우리 모델의 사용을 이끌어냅니다.

![그림 안내](/assets/img/2024-05-20-AdoptingDomain-FirstThinkinginModularMonolithwithHexagonalArchitecture_3.png)

기본 및 보조 포트와 어댑터를 결합하면 헥사고날(또는 포트 및 어댑터) 아키텍처로 이어집니다.

새로운 접근 방법을 적용하는 데 도움이 되도록 몇 가지 제약 조건을 소개해 보겠습니다. 우리는 프레임워크/구현이 우리의 생각을 이끌도록 허용하지 않겠습니다. 이는 데이터베이스 라이브러리 (JPA/JDBC), 이벤트 프레임워크 (Apache Kafka) 및 애플리케이션 프레임워크 (Spring)를 배제한다는 것을 의미합니다.

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

남은 것은 순수한 자바 (또는 응용 프로그램을 개발하는 언어)와 해당 비즈니스 로직 구현의 단위 테스트입니다.

잠시 후에 기존 응용 프로그램을 위의 과정을 따르도록 리팩터링할 것입니다. 하지만, 이것을 왜 신경 써야 할까요? 어떤 이점이 있을까요?

## 핵심 비즈니스 로직은 구현 세부 정보에서 자유롭습니다

비즈니스 로직을 반영하는 도메인 모델(도메인 또는 내부라고 부르겠습니다)은 구현 세부 정보(인프라스트럭처 또는 외부라고 부르겠습니다)에 영향을 받지 않습니다. 새로운 기능 요청을 구축해야 하나요? 도메인 모델에서 구현하고 단위 테스트로 유효성을 검사하세요. Spring Boot나 Hibernate를 업데이트해야 하나요? 걱정 마세요, 도메인 모델은 영향을 받지 않습니다.

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

## 도메인 모델이 구현을 주도합니다

도메인 모델은 아키텍처의 핵심에 있습니다. 다른 모든 것은 도메인 모델에서 출발합니다. 비즈니스 로직의 구현을 주도합니다. 이전에 구현을 주도했던 데이터 모델은 이제 영속성에만 관련된 사소한 세부사항입니다. REST API가 필요하다면, 스케줄러가 필요하다면 밤에 실행되는 확인 작업을 주도해야 한다면, AWS 람다를 트리거해야 한다면 — 이 모든 것은 도메인 모델과는 상관이 없습니다.

## 지연된 결정

어떤 사용 사례에 관계형 데이터베이스를 사용해야 할지 아직 확실하지 않나요? 아키텍트가 REST 대 GraphQL 사용에 대한 궁금증에 대답하지 않았나요? 그런 결정들이 비즈니스 로직의 구현을 막아선 안됩니다.

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

좋아요, 이제 새로운 사고 방식으로 비즈니스 로직을 작성하여 도서관 사용 사례를 구현하는 방법을 살펴볼까요?

## 대여 Bounded Context (헥사고날 아키텍처로 구현)

아래의 패키지 구조로 대여 모듈을 구성할 것입니다. 응용 프로그램 및 도메인 패키지는 헥사곤의 "내부"를 나타내고 인프라스트럭처 패키지는 "외부"를 나타냅니다.

```js
src/main/java
└── example
    ├── borrow
    │   ├── application
    │   │   └── ...
    │   ├── domain
    │   │   └── ...
    │   └── infrastructure
    │       └── ...
    └── LibraryApplication
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

아래 규칙을 패키지에 대해 따를 것입니다 (모두에게 해당되는 보편적인 규칙이 아니고, 우리가 직접 정한 것입니다). 이 구현의 복잡성과 아키텍처 원칙을 엄격하게 준수하는 현실적인 접근 사이의 적절한 균형을 유지하고 있다고 생각합니다.

- 도메인 패키지에는 도메인 모델(Aggregates, Entities, Value Objects) 및 인프라스트럭처가 구현할 인터페이스를 제공하는 Secondary / Driven 포트가 포함됩니다. 우리는 표준 자바, Lombok(많은 코드를 줄이기 위한) 및 JMolecules(스테레오타입을 문서화하기 위해) 라이브러리만 사용할 것입니다.
- 애플리케이션 패키지에는 애플리케이션의 유스케이스를 구현하는 Primary / Driver 포트가 포함됩니다. 우리는 Spring의 @Transactional 및 Modulith의 @ApplicationModuleListener 주석을 사용할 수 있습니다.
- 인프라스트럭처 패키지에는 REST API 및 JPA 어댑터를 빌드하는 코드가 포함됩니다. 이것은 야생 서부입니다. 제한 사항이 없으며, 모든 라이브러리를 자유롭게 사용할 수 있습니다.

책을 대출하는 사용 사례를 살펴보겠습니다. 사용자는 이용 가능한 책에 예약을 할 수 있습니다. 아래는 사용 사례를 구현하는 주요 포트의 Java 예시입니다. @PrimaryPort 주석이 어디서 온 것인지 궁금하다면 계속 읽어보세요!

```java
@PrimaryPort
public class CirculationDesk {

    private final BookRepository books;
    private final HoldRepository holds;
    private final HoldEventPublisher eventPublisher;

    @Transactional
    public HoldDto placeHold(PlaceHold command) {
        books.findAvailableBook(command.inventoryNumber())
                .orElseThrow(() -> new IllegalArgumentException("Book not found"));

        return Hold.placeHold(command)
                .then(holds::save)
                .then(eventPublisher::holdPlaced)
                .to(HoldDto::from);
    }

    record PlaceHold(Barcode inventoryNumber,
                     String patronId,
                     LocalDate dateOfHold) {
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

의존성을 역전시키기 위해, 모든 집합체를 위한 Repository 인터페이스를 도입했습니다 (Spring Data와는 혼동하지 마세요).

```js
@SecondaryPort
인터페이스 BookRepository {

    Optional<Book> findAvailableBook(Book.Barcode inventoryNumber);

    Optional<Book> findOnHoldBook(Book.Barcode inventoryNumber);

    Book save(Book book);

    Optional<Book> findByBarcode(String barcode);
}

@SecondaryPort
인터페이스 HoldRepository {

    Hold save(Hold hold);

    Optional<Hold> findById(Hold.HoldId id);

    List<Hold> activeHolds();
}
```

CirculationDesk 응용 프로그램 서비스는 위의 리포지터리 인터페이스를 사용합니다. 이를 통해 구현 기술(Spring Data, JPA, JDBC 등)과 독립시켰습니다.

## 집합체로부터 도메인 이벤트 트리거하기

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

하나의 사용 사례 결과는 Aggregate의 업데이트된 상태를 지속시키는 것입니다. 또 다른 결과는 도메인 이벤트를 발생시켜 모노리스의 다른 모듈 (또는 동일 모듈 내의 다른 Aggregate)에서 대응할 수 있도록 하는 것일 수 있습니다.

Aggregate를 지속시키는 것 (Repository 사용)과 이벤트를 발생시키는 것 (Event Publisher 사용)은 헥사고날 아키텍처에서 이차/드라이븐 포트입니다. 구현은 비즈니스 로직과 관련이 없습니다.

사용 사례 실행 후에는 BookPlacedOnHold 이벤트가 발생합니다. 이 이벤트는 Book Aggregate에서 도서 상태를 데이터베이스에서 업데이트하기 위해 사용됩니다. 이벤트 처리는 비즈니스 로직의 중요한 부분이므로 주/드라이버 포트의 책임입니다.

```java
// 실제로 이벤트가 발행되는 방법에 대한 구현에 대한 이해가 없는
// 이벤트 퍼블리셔 인터페이스
@SecondaryPort
public interface HoldEventPublisher {

    void holdPlaced(BookPlacedOnHold event);

    default Hold holdPlaced(Hold hold) {
        BookPlacedOnHold event = new BookPlacedOnHold(hold.getId().id(), hold.getOnBook().barcode(), hold.getDateOfHold());
        this.holdPlaced(event);
        return hold;
    }
}

// 이벤트 핸들러는 Spring Modulith 리스너를 사용하여
// 이벤트에 신뢰성있게 대응합니다.
@PrimaryPort
class CirculationDeskEventHandler {

    private final BookRepository books;
    private final HoldEventPublisher eventPublisher;

    @ApplicationModuleListener
    public void handle(BookPlacedOnHold event) {
        books.findAvailableBook(new Book.Barcode(event.inventoryNumber()))
                .map(Book::markOnHold)
                .map(books::save)
                .orElseThrow(() -> new IllegalArgumentException("중복 예약?"));
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

## 육각형 구조의 "내부" (비즈니스 로직) 단위 테스트

![이미지](/assets/img/2024-05-20-AdoptingDomain-FirstThinkinginModularMonolithwithHexagonalArchitecture_4.png)

프레임워크 종속성 없이 비즈니스 로직을 구현하는 큰 장점은 통합 테스트가 필요하지 않고 코드를 단위 테스트할 수 있다는 것입니다. Repository 포트의 인메모리 구현을 사용하여 placeHold 유스케이스를 유효성 검사하는 단위 테스트의 예시가 여기 있습니다(CirculationDeskTest.java 참조).

```java
class CirculationDeskTest {

    CirculationDesk circulationDesk; // 애플리케이션 서비스

    BookRepository bookRepository; // 보조 포트

    HoldRepository holdRepository; // 보조 포트

    @BeforeEach
    void setUp() {
        bookRepository = new InMemoryBooks();
        holdRepository = new InMemoryHolds();
        circulationDesk = new CirculationDesk(bookRepository, holdRepository, new InMemoryHoldsEventPublisher());
    }

    @Test
    void patronCanPlaceHold() {
        var command = new Hold.PlaceHold(new Book.Barcode("12345"), LocalDate.now());
        var holdDto = circulationDesk.placeHold(command);
        assertThat(holdDto.getBookBarcode()).isEqualTo("12345");
        assertThat(holdDto.getDateOfHold()).isNotNull();
    }

    @Test
    void bookStatusUpdatedWhenPlacedOnHold() {
        var command = new Hold.PlaceHold(new Book.Barcode("12345"), LocalDate.now());
        var hold = Hold.placeHold(command);
        circulationDesk.handle(new BookPlacedOnHold(hold.getId().id(), hold.getOnBook().barcode(), hold.getDateOfHold()));
        //noinspection OptionalGetWithoutIsPresent
        var book = bookRepository.findByBarcode("12345").get();
        assertThat(book.getStatus()).isEqualTo(ON_HOLD);
    }
}

// 테스트 픽처 (보조 포트를 구현하는 보조 어댑터 역할)
class InMemoryBooks implements BookRepository {

    private final Map<String, Book> books = new HashMap<>();

    public InMemoryBooks() {
        var booksToAdd = List.of(
                Book.addBook(new Book.AddBook(new Book.Barcode("12345"), "A famous book", "92972947199")),
                Book.addBook(new Book.AddBook(new Book.Barcode("98765"), "Another famous book", "98137674132"))
        );
        booksToAdd.forEach(book -> books.put(book.getInventoryNumber().barcode(), book));
    }

    ...
}

// 테스트 픽처 (보조 포트를 구현하는 보조 어댑터 역할)
class InMemoryHolds implements HoldRepository {
  // InMemoryBooks와 유사한 구현
  ...
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

도메인 모델이 집계를 지속하거나 이벤트를 트리거하기 위해 인터페이스에 의존하고 있는 점을 감안할 때, 유닛 테스트는 이 인터페이스 메서드를 실행할 수 있도록 테스트 픽스처가 필요합니다. 유당 테스트가 작동하려면 모든 보조/드라이븐 포트에 대한 구현이 필요합니다.

전체 코드는 아래 GitHub 저장소에서 확인할 수 있습니다.

## 아키텍처 규칙 테스트하기

Spring Modulith 및 jMolecules 라이브러리는 ArchUnit과 함께 사용되어 우리의 솔루션에서 적용된 아키텍처와 Modular Monolith 애플리케이션의 모듈 간 상호 작용을 테스트할 수 있게 합니다. 이는 새로운 개발자들이 작업을 시작함에 따라 우리의 애플리케이션이 선택한 아키텍처에서 벗어나지 않도록 하는 데 중요합니다.

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

저희 모듈형 모놀리스 응용 프로그램에는 이제 Catalog 및 Borrow 두 개의 모듈이 있습니다. Catalog 모듈은 Layered/N-tier 아키텍처를 사용하고 Borrow 모듈은 Hexagonal 아키텍처를 사용하고 있어요. 다음과 같이 테스트할 수 있어요 (BorrowJMoleculesTests 및 CatalogJMoleculesTests 참고).

```js
@AnalyzeClasses(packages = "example.catalog")
public class CatalogJMoleculesTests {

    @ArchTest
    ArchRule dddRules = JMoleculesDddRules.all();

    @ArchTest
    ArchRule layering = JMoleculesArchitectureRules.ensureLayering();
}

@AnalyzeClasses(packages = "example.borrow")
public class BorrowJMoleculesTests {

    @ArchTest
    ArchRule dddRules = JMoleculesDddRules.all();

    @ArchTest
    ArchRule hexagonal = JMoleculesArchitectureRules.ensureHexagonal();
}
```

@AnalyzeClasses 주석을 사용하여 테스트할 모듈을 선택할 수 있어요.

## Hexagonal 아키텍처의 "외부"를 통합 테스트하기

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

기술이 도메인 모델과 상호 작용하는 외부 부분은 동작을 확인하기 위해 통합 테스트로 테스트해야 합니다. 여기에 예제가 있습니다 (영속성인 CirculationDeskIT.java 및 REST인 CirculationDeskControllerIT.java):

```js
// from CirculationDeskIT
@Test
void patronCanPlaceHold(Scenario scenario) {
    var command = new Hold.PlaceHold(new Book.Barcode("13268510"), LocalDate.now());
    scenario.stimulate(() -> circulationDesk.placeHold(command))
            .andWaitForEventOfType(BookPlacedOnHold.class)
            .toArriveAndVerify((event, dto) -> {
                assertThat(event.inventoryNumber()).isEqualTo("13268510");
            });
}

// from CirculationDeskControllerIT
@Test
void placeHoldRestCall() throws Exception {
    mockMvc.perform(post("/borrow/holds")
                    .contentType(MediaType.APPLICATION_JSON)
                    .content("""
                            {
                              "barcode": "64321704",
                              "patronId": 5
                            }
                            """))
            .andExpect(status().isOk());
}
```

## jMolecules로 클래스 스테레오타입 시각화

이전 블로그 시리즈에서 언급했듯이 jMolecules Intellij 플러그인을 사용하여 다양한 스테레오타입을 시각화할 수 있습니다. 그러나 jMolecules 라이브러리가 헥사고날 아키텍처에 대한 주석을 지원하더라도 플러그인은 해당 주석을 지원하지 않습니다. 저는 관련 주석을 추가하여 플러그인을 수정했습니다. 플러그인을 활성화하고 관련 주석을 추가한 후 대여 모듈 내부 클래스가 어떻게 보이는지 확인하세요.

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

![](/assets/img/2024-05-20-AdoptingDomain-FirstThinkinginModularMonolithwithHexagonalArchitecture_5.png)

주요 포트 및 어댑터 클래스를 쉽게 식별할 수 있습니다.

## 헥사고날 아키텍처에 헌신해야 할까요?

모듈형 단일체 애플리케이션을 구축할 때, 모든 모듈에 대해 헥사고날 아키텍처를 사용해야 할지에 대해 고려해야 합니다. 만약 모듈이 자체 비즈니스 규칙을 갖고 있거나 (또는 나중에 갖게 될 것으로 예상된다면), 헥사고날 아키텍처가 좋은 아이디어일 수 있습니다.

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

하지만 해결책에 더 많은 클래스와 장황성을 추가합니다. Layered 아키텍처에는 보조 포트와 어댑터가 없습니다. Spring Data 리포지토리 인터페이스는 Service 클래스에 직접 의존성을 추가하는 대가로 보조 포트와 어댑터의 역할을 취합니다.

결국, 헥사고날 아키텍처를 채택하는 이점이 비용을 크게 상회합니다. 최소한 코드베이스에서 불필요한 결합을 줄이기 위해 의존성 역전의 원칙을 적용할 수 있는지 항상 확인해야 합니다.

## 헥사고날 아키텍처를 구현하는 데 DDD가 필요한가요?

아니요. 도메인 주도 설계(DDD)는 도메인이 모델링되는 방식에만 관련이 있습니다. 아키텍처와는 관련이 없습니다(헥사고날이든 그 외). DDD를 사용해야 하는가요? 클래스와 필드를 명명할 때 항상 모든 지식의 언어를 사용하는 것을 추천합니다. 도메인 전문가가 사용하는 용어와 동일한 용어를 사용하는 코드는 항상 이해하기 쉽습니다.

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

DDD의 Aggregate 및 기타 전략적 패턴을 사용하는 것은 애플리케이션의 필요성과 복잡성에 따라 다릅니다. 정당화되지 않은 경우에는 트랜잭션 스크립트나 액티브 레코드를 대신 사용할 수 있습니다.

## 헥사고날 아키텍처의 순수 구현 회피

헥사곤의 내부는 핵심 비즈니스 로직의 구현이며, 헥사곤 외부에서 사용된 프레임워크와 기술로부터 자유롭게 유지해야 합니다. 몇몇 구현이 이 규칙을 신앙적으로 따라 코드를 복잡하게 만드는 것을 본 적이 있습니다.

예를 들어, 애플리케이션 서비스 코드는 트랜잭션 내에서 실행되어야 합니다. Spring의 @Transactional을 사용하면 쉽게 달성할 수 있습니다. 그러나 이를 피하려면 애플리케이션 서비스를 Spring으로부터 자유롭게 유지하기 위해 사용자 정의 어노테이션과 AOP를 도입해야 합니다.

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

헥사곤 안의 내부는 Application과 Domain으로 구성되어 있습니다. 도메인은 이상적으로는 프레임워크 없이 유지되어야 하지만, Application은 Spring을 사용할 수 있습니다. 이 질문을 Vaughn Vernon에게 던졌고, 그는 좋은 설명을 제공했습니다.

![이미지](/assets/img/2024-05-20-AdoptingDomain-FirstThinkinginModularMonolithwithHexagonalArchitecture_6.png)

## 도메인 모델로부터 만들어진 분리된 JPA 엔티티를 병합하기

아래 코드 스니펫을 확인하여 Hold를 배치하세요.

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
public HoldDto placeHold(Hold.PlaceHold command) {
    ...

    Hold.placeHold(command) // Hold 도메인 모델 객체 생성
            .then(holds::save) // JPA 엔티티로 변환하여 데이터베이스에 저장
            .then(eventPublisher::holdPlaced) // HoldPlaced 이벤트를 트리거

    ...
  );
}
```

holds::save는 Secondary 포트 인터페이스에서의 호출로, Secondary 어댑터 구현을 트리거합니다:

```js
public Hold save(Hold hold) {
    holds.save(HoldEntity.fromDomain(hold));
    return hold;
}

// HoldEntity 클래스에서
public static HoldEntity fromDomain(Hold hold) {
    var entity = new HoldEntity();
    entity.id = hold.getId().id(); // 도메인 모델 식별자와 JPA 엔티티 PK가 동일합니다!
    entity.book = hold.getOnBook();
    entity.dateOfHold = hold.getDateOfHold();
    entity.status = HoldStatus.HOLDING;
    entity.version = 0L;
    return entity;
}
```

Hold 도메인 모델은 영속화되기 전에 분리된 HoldEntity JPA 엔티티로 변환되어야 합니다. 엔티티 생성에는 기본 키 ID를 알아야 합니다. 도메인 모델이 PK를 따로 저장하거나 모델 식별자로 사용해야 합니다. 저의 예시에서는 후자를 선택했지만, 이 문제를 어떻게 해결할지 궁금합니다.

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

## 결론

이 블로그에서는 계층 구조 대신 헥사고날 아키텍처를 사용하여 대출 모듈을 다시 구현했습니다. 이 과정에서 동일한 단일 코드 베이스의 일부임에도 불구하고 카탈로그 모듈에는 절대적으로 변경이 필요하지 않았음을 보여주었습니다. 이는 이벤트 기반 통합 덕분에 느슨한 결합이 활성화되었음을 확인했습니다.

헥사고날에 대한 여러분의 경험은 어떠한가요? 댓글에서 공유해 주시면 감사하겠습니다.

아래에서 업데이트된 코드를 확인할 수 있습니다.
