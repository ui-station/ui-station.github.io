---
title: "Winglang을 사용해 클라우드로 이전하는 방법 1부"
description: ""
coverImage: "/assets/img/2024-06-22-PortedtoCloudwithWinglangPartOne_0.png"
date: 2024-06-22 22:02
ogImage:
  url: /assets/img/2024-06-22-PortedtoCloudwithWinglangPartOne_0.png
tag: Tech
originalTitle: "Ported to Cloud with Winglang (Part One)"
link: "https://medium.com/itnext/ported-to-cloud-with-winglang-part-one-757d31fe5862"
---

## "Hexagonal Architecture Explained"에서 Blue Zone Application

<img src="/assets/img/2024-06-22-PortedtoCloudwithWinglangPartOne_0.png" />

소프트웨어 애플리케이션을 직접 클라우드로 이관하면 종종 비효율적이고 유지보수가 어려운 코드가 될 수 있습니다. 그러나 새로운 클라우드 지향 프로그래밍 언어 Wing과 Hexagonal Architecture를 조합하여 사용하는 것은 성공적인 방법임이 입증되었습니다. 이 접근 방식은 비용, 성능, 유연성, 보안 사이에서 적절한 균형을 유지합니다.

이 시리즈에서는 주요 프로그래밍 언어에서 Winglang로 다양한 애플리케이션을 마이그레이션하는 경험을 공유하겠습니다. Wing에서 Hexagonal Architecture를 구현한 첫 경험은 "안녕, Winglang Hexagon!"이라는 제목의 기사에 보고되었습니다. 이는 이러한 조합에 대한 신뢰를 얻기에 충분했으나 과도하게 단순화된 "안녕하세요, 세계!" 인사 서비스에 제한된 핵심 구성 요소가 부족하여 이러한 접근 방식의 확장 가능성을 입증하기에 충분하지 않았습니다.

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

제 첫 번째 파트에서는 최근 출간된 책 "헥사곤 아키텍처 설명"에 소개된 "블루 존" 애플리케이션을 자바에서 Wing으로 이식하는 데 초점을 맞추었습니다. "블루 존" 애플리케이션은 상당한 코드 베이스를 가져오는데, 복잡성이 해결하기 어려울 정도로 커지지 않아도 되지만, 많은 종류의 애플리케이션을 대표하는 것입니다. 또한, 이것이 기존의 주류 자바로 작성된 것이라는 사실은 이러한 애플리케이션의 클라우드 네이티브 변형 사례 연구를 제공합니다.

본 보고서는 2024년 4월에 안타깝게도 사망한 이 책의 공저자인 후안 마누엘 가리도 데 파스에 대한 경의의 표시로도 작용합니다.

계속 진행하기 전에, 헥사곤 아키텍처 패턴의 기초를 다시 한 번 상기해 봅시다.

# 헥사곤 아키텍처 패턴 핵심요소

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

“Hexagonal Architecture Explained” 책의 두 번째 장을 참고하여 패턴에 대한 자세하고 공식적인 설명을 보실 수 있습니다. 여기서는 제가 제 나만의 말로 이 패턴의 주된 의미를 간략히 요약해서 전달하겠습니다.

Hexagonal Architecture 패턴은 소프트웨어에서 관심사를 분리하는 간단하면서도 실용적인 접근 방법을 제안합니다. 관심사 분리가 왜 중요한가요? delivered value 앱의 경우에도 소프트웨어 코드 기반은 빠르게 성장합니다. 책임을 질 일이 많기 때문입니다. 인지적 제어를 유지하려면 그룹이나 범주에 대한 고수준 조직이 필요합니다. 이 도전에 맞서기 위해 Hexagonal Architecture 패턴은 특정 소프트웨어 애플리케이션에 참여하는 모든 요소를 다섯 가지의 구분된 범주로 분리하고 각각을 따로 다루는 것을 제안합니다:

- Application 자신. 이 범주는 잠재적인 고객 및 사용자들에게 전달되는 실제 가치를 캡슐화합니다. 이것은 소프트웨어가 처음 개발되고 사용될 이유입니다. 때로는 Core 또는 System Under Development (SuD)로 불리기도 합니다. 이 부분에 대한 또 다른 가능한 이름은 Computation일 수 있습니다 — 외부 입력이 처리되고 최종 결과가 생성되는 곳입니다. 시각적으로, 시스템의 Application 부분은 육각형의 형태로 나타납니다. 이 모양에는 특별하거나 마법 같은 것은 없습니다. "Hexagonal Architecture Explained" 책의 저자들이 설명하듯이:

2. Application과 통신하거나 통신되는 외부 주체. 이들은 인간 최종 사용자, 전자 장치 또는 다른 애플리케이션일 수 있습니다. 원래 패턴은 더 나아가 제안하듯이 Primary (또는 Driving) Actors — Application과 상호 작용을 시작하는 주체들과 Secondary (또는 Driven) Actors — Application이 통신을 시작하는 주체들로 나뉘어야 한다고 제안합니다.

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

3. 포트 — 주요 작용자가 사용할 수 있는 인터페이스의 공식 명세(드라이빙 포트라고도 함) 또는 보조 작용자가 구현해야 하는 인터페이스의 공식 명세(드리븐 포트라고도 함)를 가리킵니다. 포트는 인터페이스 동사(예: 주차 티켓 구매)의 공식 명세 외에 이러한 인터페이스를 통해 교환되는 데이터 구조에 대한 자세한 명세도 제공합니다.

4. 어댑터는 외부 작용자와 포트 사이의 간격을 메꾸는 역할을 합니다. 이름에서 알 수 있듯이, 어댑터는 의미 있는 계산을 수행하는 것이 아니라, 기본적으로 작용자가 이해하는 형식으로 데이터를 변환하거나 응용 프로그램이 이해하는 형식으로 데이터를 변환하는 역할을 합니다.

5. 구성자는 외부 작용자를 모든 포트를 통해 응용 프로그램에 연결함으로써 해당 어댑터를 사용하는 것을 통해 모든 것을 통합합니다. 적용된 아키텍처 결정과 가격/성능/유연성 요구 사항을 고려한 특정 구성은 응용 프로그램 배포 전 정적으로 생성되거나 응용 프로그램 실행 중 동적으로 생성될 수 있습니다.

일반적인 신념과는 달리, 이 패턴은 한 범주, 예를 들어 응용 프로그램이 다른 것보다 중요하다는 것을 의미하지 않으며, 다른 것들보다 크거나 작아야 한다는 결론을 내리지도 않습니다. 포트와 어댑터가 없으면 어떤 응용 프로그램도 실용적으로 사용할 수 없습니다. 상대적 크기는 확장성, 성능, 비용, 가용성 및 보안과 같은 비기능 요구 사항에 의해 종종 결정됩니다.

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

패턴은 한 번에 한 문제에 집중함으로써 복잡성과 위험을 줄이는 것을 제안하며, 다른 측면은 일시적으로 무시하는 것을 시사합니다. 또한 동일한 계산에 대한 여러 구성을 보장하는 실용적인 방법을 제안합니다. 각각이 몇 가지 특정 요구 사항을 해결하는 테스트 자동화 또는 다른 환경에서의 작업일 수 있습니다.

아래 그림은 "Hexagonal Architecture Explained" 책에서 이 패턴의 모든 주요 요소를 잘 요약하고 있습니다:

![Hexagonal Architecture](/assets/img/2024-06-22-PortedtoCloudwithWinglangPartOne_1.png)

# "블루 존" 샘플 애플리케이션

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

애플리케이션 README에서:

저는 이 애플리케이션을 선택한 이유가 두 가지 있습니다. 첫째로, "Hexagonal Architecture Explained" 책에서 권장하는 대표적인 예제로 소개되었기 때문입니다. 둘째로, 이 애플리케이션은 원래 Java로 개발되었습니다. 저는 Wing 프로그래밍 언어를 사용하여 다소 복잡한 Java 애플리케이션을 클라우드로 포팅하는 과정이 어떤 것인지 궁금했습니다.

# 어디서부터 시작하면 좋을까요?

"Hexagonal Architecture Explained" 책은 4.9장 "개발 순서는 무엇인가?"에서 합리적인 권장 사항을 제공합니다. "테스트부터 테스트"로 시작하고 더 나아가는 것이 타당합니다. 그러나 대부분의 소프트웨어 엔지니어들이 일반적으로 하는 것처럼, 저는 Java 코드를 Wing으로 번역하는 것부터 시작했습니다. 일부분의 시간을 할애하여 지금까지 외부 인터페이스를 시뮬레이션하면서 로컬에서 Wing에서 작동하는 것을 얻었습니다.

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

기술적으로는 작동했지만, 결과 코드는 응용 프로그램의 크기와 관련하여 너무 크고, 심지어 저에게도 이해하기 어려웠으며, 미적으로 매력이 없었으며, 완전히 Wing 언어와 클라우드 환경의 특성과 부합하지 않았습니다. 그 후, 나는 Wing 언어와 클라우드 환경의 특정 사항에 적합한 핵심 패턴 아이디어의 가장 의미있는 표현을 찾기 위해 두 주간의 리팩토링 주기에 착수했습니다.

다음이 나의 작업 방식과는 다릅니다. 대부분의 코드를 생성, 평가하고 폐기하는 엄청난 규모의 혼돈의 왕래 시리즈가 이어집니다. 이것은 주로 생소한 기술과 영역을 다룰 때 소프트웨어 개발에서 발생합니다.

마침내 더 구조화되고 시스템적인 과정으로 단계적으로 표준화될 수 있는 무언가를 찾았습니다. 다음에 더 괴로운 경험이 아니라 더 생산적일 것으로 기대됩니다. 그러므로, 다음에 사용될 것으로 기대되는 개념적으로 바람직한 순서대로 결과를 제시할 것이며, 실제로 일어난 방식으로 제시하지는 않을 것입니다.

# 먼저 테스트로 시작하라# Thou Shalt Start with Tests

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

더 정확하고 최적의 방법은 체계적인 수용 테스트의 연속으로 시작하는 것입니다. 시스템의 아키텍처적으로 중요한 사용 사례에 관한 5.1 장 "헥사고널 아키텍처 설명" 책은 사용 사례 모델링과 헥사고널 아키텍처 간의 깊은 관련성을 자세히 다룹니다. 주의 깊게 읽어볼 가치가 있습니다.

이전 문장조차 100% 정확하지는 않습니다. 우리는 기본 외부 작용자와 그들이 시스템과 가장 특징적인 상호 작용 방법을 식별하여 시작해야 합니다. "블루 존" 애플리케이션의 경우, 두 가지 주요 외부 작용자가 있습니다:

- 차 운전사
- 주차 담당자

차 운전사 작용자의 경우, 주요 사용 사례는 "티켓 구매"일 것이며, 주차 담당자의 경우, 주요 사용 사례는 "차량 확인"일 것입니다. 이러한 사용 사례의 구현을 통해 보조 외부 작용자와 나머지 요소를 식별할 수 있습니다.

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

이 분석에서 나온 예비 사용 사례 모델은 아래에 제시되었습니다:

![사진](/assets/img/2024-06-22-PortedtoCloudwithWinglangPartOne_2.png)

위 다이어그램에는 하나의 보조 작용자인 결제 서비스만 포함되어 있음을 주목해 주세요. 데이터베이스와 같은 내부 보조 작용자는 포함되어 있지 않습니다. 이러한 기술 요소들은 곧 Driven Ports에 의해 애플리케이션에서 격리될 예정이지만, 적어도 전통적인 Use Case 작용자 해석에서는 Use Case 외부 작용자를 대표하지 않습니다.

개발을 시작하기 전에 사용 사례 수락 기준을 명시하는 것은 내부 리팩터링을 수행하면서 시스템 안정성을 보장하는 매우 효과적인 기술입니다. "블루 존" 애플리케이션의 경우, 사용 사례 수락 테스트는 자바 프레임워크인 Cucumber를 사용하여 Gherkin 언어로 지정되었습니다.

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

현재 Wing을 위한 Cucumber 프레임워크는 당연한 이유로 존재하지 않습니다 - Wing은 매우 젊은 언어이기 때문입니다. JavaScript용 공식 Cucumber가 존재하고 TypeScript Cucumber 튜토리얼도 있지만, 이 기술에 대한 조사를 연기하고 직접 Wing에서 몇 가지 테스트를 재현해보기로 결정했습니다.

놀랍게도, 이것이 가능했고 제 목적에는 잘 작동했습니다. 다음은 Wing에서 완전히 지정된 Buy Ticket use case happy path 수용 테스트의 예시입니다:

```js
bring "../src" as src;
bring "./steps" as steps;

/*
Use Case: Buy Ticket
  AS
  a car driver
  I WANT TO
  a) obtain a list of available rates
  b) submit a "buy a ticket" request with the selected rate
  SO THAT
  I can park the car without being fined
*/
let _configurator = new src.Configurator("BuyTicketFeatureTest");
let _testFixture = _configurator.getForAdministering();
let _systemUnderTest = _configurator.getForParkingCars();
let _ = new steps.BuyTicketTestSteps(_testFixture, _systemUnderTest);

test "Buy ticket for 2 hours; no error" {
    /* Given */
        ["name",    "eurosPerHour"],
        ["Blue",    "0.80"],
        ["Green",   "0.85"],
        ["Orange",  "0.75"]
    ]);
    _.next_ticket_code_is("1234567890");
    _.current_datetime_is("2024/01/02 17:00");
    _.no_error_occurs_while_paying();
    /* When */
    _.I_do_a_get_available_rates_request();
    /* Then */
    _.I_should_obtain_these_rates([
        ["name",    "eurosPerHour"],
        ["Blue",    "0.80"],
        ["Green",   "0.85"],
        ["Orange",  "0.75"]
    ]);
    /* When */
    _.I_submit_this_buy_ticket_request([
        ["carPlate", "rateName", "euros", "card"],
        ["6989GPJ",  "Green",    "1.70",  "1234567890123456-123-062027"]
    ]);
    /* Then */
    _.this_pay_request_should_have_been_done([
        ["euros", "card"],
        ["1.70",  "1234567890123456-123-062027"]
    ]);
    /* And */
    _.this_ticket_should_be_returned([
        ["ticketCode", "carPlate", "rateName", "startingDateTime", "endingDateTime",   "price"],
        ["1234567890", "6989GPJ",  "Green",    "2024/01/02 17:00", "2024/01/02 19:00", "1.70"]
    ]);
    /* And */
    _.the_buy_ticket_response_should_be_the_ticket_stored_with_code("1234567890");
}
```

진정한 읽기 쉬운 텍스트는 아니지만 충분히 가깝고 이해하기 어렵지 않습니다. 여기서 해독할 사항이 꽤 많이 있습니다. 하나씩 진행해 봅시다.

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

## 테스트 구조

위의 테스트는 특정 프로젝트 폴더 구조를 가정하고 있으며 Wing 모듈 및 가져오기 규칙을 반영하고 있습니다.

테스트 소스의 처음 두 줄로부터 우리는 프로젝트에는 src 폴더(모든 소스 코드가 위치하는 곳)와 test 폴더(모든 테스트가 위치하는 곳) 두 가지 주요 폴더가 있다는 결론을 내릴 수 있습니다. 더 나아가 test\steps 하위 폴더에는 개별 테스트 단계 구현이 보관되어 있습니다.

테스트 소스의 다음 세 줄에서는 preflight Configurator 객체를 할당하고 이를 통해 두 개의 포인터를 추출합니다.

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

- \_testFixture는 테스트 설정을 담당하는 사전 점검 클래스를 가리킵니다.
- \_systemUnderTest는 운전자를 위한 주 포트 인터페이스를 가리킵니다.

다음으로, 우리는 개별 단계를 구현하는 inflight BuyTicketTestSteps 객체를 할당합니다. 이 객체는 대부분 보이지 않는 언더스코어 이름을 갖게 되며, 이는 전체 테스트 가독성을 향상시킵니다. 이는 일반 목적의 호스트 언어에 내장된 도메인 특화 언어(DSL)를 개발하는 일반적인 기술입니다.

중요한 점은, 제 경우에는 그렇지 않았지만, 단순한 src 및 test\steps 폴더 구조와 다른 구조적 결정을 이끌 수 있는 간단한 테스트 설정으로 프로젝트를 시작하는 것이 전혀 상상 가능하다는 것입니다.

물론, 단계가 구현되지 않았다면 테스트는 컴파일에도 통과하지 않을 것입니다. 진행하기 위해, 우리는 BuyTicketTestSteps 클래스 내부를 살펴봐야 합니다.

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

## 테스트 단계 클래스

"티켓 구매" 유스 케이스를 위한 테스트 단계 클래스가 아래와 같이 나와 있습니다:

```js
bring expect;
bring "./Parser.w" as parse;
bring "./TestStepsBase.w" as base;
bring "../../src/application/ports" as ports;


pub class BuyTicketTestSteps extends base.TestStepsBase {
    _systemUnderTest: ports.ForParkingCars;
    inflight var _currentAvailableRates: Set<ports.Rate>;
    inflight var _currentBoughtTicket: ports.Ticket?;

    new(
        testFixture: ports.ForAdministering,
        systemUnderTest: ports.ForParkingCars
    ) {
        super(testFixture);
        this._systemUnderTest = systemUnderTest;
    }

    inflight new() {
        this._currentBoughtTicket = nil;
        this._currentAvailableRates = Set<ports.Rate>[];
    }

    pub inflight the_existing_rates_in_the_repository_are(
        sRates: Array<Array<str>>
    ): void {
        this.testFixture.initializeRates(parse.Rates(sRates).toArray());
    }

    pub inflight next_ticket_code_is(ticketCode: str): void {
        this.testFixture.changeNextTicketCode(ticketCode);
    }

    pub inflight no_error_occurs_while_paying(): void {
        this.testFixture.setPaymentError(ports.PaymentError.NONE);
    }

    pub inflight I_do_a_get_available_rates_request(): void {
        this._currentAvailableRates = this._systemUnderTest.getAvailableRates();
    }

    pub inflight I_should_obtain_these_rates(sRates: Array<Array<str>>): void {
        let expected = parse.Rates(sRates);
        expect.equal(this._currentAvailableRates, expected);
    }

    pub inflight I_submit_this_buy_ticket_request(sRequest: Array<Array<str>>): void {
        let request = parse.BuyRequest(sRequest);
        this.setCurrentThrownException(nil);
        this._currentBoughtTicket = nil;
        try {
            this._currentBoughtTicket = this._systemUnderTest.buyTicket(request);
        } catch err {
            this.setCurrentThrownException(err);
        }
    }

    pub inflight this_ticket_should_be_returned(sTicket: Array<Array<str>>): void {
        let sTicketFull = Array<Array<str>>[
            sTicket.at(0).concat(["paymentId"]),
            sTicket.at(1).concat([this.testFixture.getLastPayResponse()])
        ];
        let expected = parse.Ticket(sTicketFull);
        expect.equal(this._currentBoughtTicket, expected);
    }

    pub inflight this_pay_request_should_have_been_done(sRequest: Array<Array<str>>): void {
        let expected = parse.PayRequest(sRequest);
        let actual = this.testFixture.getLastPayRequest();
        expect.equal(actual, expected);
    }

    pub inflight the_buy_ticket_response_should_be_the_ticket_stored_with_code(code: str): void {
        let actual = this.testFixture.getStoredTicket(code);
        expect.equal(actual, this._currentBoughtTicket);
    }

    pub inflight an_error_occurs_while_paying(error: str): void {
        this.testFixture.setPaymentError(parse.PaymentError(error));
    }

    pub inflight a_PayErrorException_with_the_error_code_that_occurred_should_have_been_thrown(code: str): void {
        //TODO: make it more specific
        let err = this.getCurrentThrownException()!;
        log(err);
        expect.ok(err.contains(code));
    }

    pub inflight no_ticket_with_code_should_have_been_stored(code: str): void {
        try {
            this.testFixture.getStoredTicket(code);
            expect.ok(false, "Should never get there");
        } catch err {
            expect.ok(err.contains("KeyError"));
        }
    }
}
```

이 클래스는 데이터를 파싱하여 구체적인 애플리케이션 데이터 구조로 통일하고, 테스트Fixture 또는\_systemUnderTest 객체로 데이터를 보내고, 중간 결과를 유지하며, 적절한 경우 예상 대비 실제 결과를 비교합니다.

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

유의해야 할 유일한 구체적인 사항은 프리플라이트 및 인플라이트 정의를 올바르게 처리하는 것입니다. 이를 올바르게 만들도록 도와준 Cristian Pallares에게 감사드립니다.

우리는 명확히 구분된 책임을 갖는 세 가지 추가 요소가 있습니다:

- 파서 — 일관된 문자열 입력 배열을 애플리케이션 특정 데이터 구조로 변환하는 데 책임이 있습니다.
- 테스트 픽스처 — 전제 조건 설정 및 사후 상태 확인을 위해 시스템과의 비공식적인 통신을 담당합니다.
- 테스트 대상 시스템 — 애플리케이션 로직을 구현하는 데 책임이 있습니다.

각각을 좀 더 자세히 살펴봅시다.

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

## 파서

Parser 모듈의 소스 코드는 아래에 제시되어 있습니다:

```js
bring structx;
bring datetimex;
bring "../../src/application/ports" as ports;


pub class Util {
    pub inflight static Rates(sRates: Array<Array<str>>): Set<ports.Rate> {
        return unsafeCast(
            structx.fromFieldArray(
                sRates,
                ports.Rate.schema()
            )
        );
    }

    pub inflight static BuyRequest(
     sRequest: Array<Array<str>>
   ): ports.BuyTicketRequest {
        let requestSet: Set<ports.BuyTicketRequest> = unsafeCast(
            structx.fromFieldArray(
                sRequest,
                ports.BuyTicketRequest.schema()
            )
        );
        return requestSet.toArray().at(0);
    }

    pub inflight static Tickets(
     sTickets: Array<Array<str>>
   ): Set<ports.Ticket> {
        return unsafeCast(
            structx.fromFieldArray(
                sTickets,
                ports.Ticket.schema(),
                datetimex.DatetimeFormat.YYYYMMDD_HHMM
            )
        );
    }

    pub inflight static Ticket(sTicket: Array<Array<str>>): ports.Ticket {
        return Util.Tickets(sTicket).toArray().at(0);
    }

    pub inflight static PayRequest(
     sRequest: Array<Array<str>>
   ): ports.PayRequest {
        let requestSet: Set<ports.PayRequest> = unsafeCast(
            structx.fromFieldArray(
                sRequest,
                ports.PayRequest.schema()
            )
        );
        return requestSet.toArray().at(0);
    }

    pub inflight static CheckCarRequest(
     sRequest: Array<Array<str>>
   ): ports.CheckCarRequest {
        let requestSet: Set<ports.CheckCarRequest> = unsafeCast(
            structx.fromFieldArray(
                sRequest,
                ports.CheckCarRequest.schema()
            )
        );
        return requestSet.toArray().at(0);
    }

    pub inflight static CheckCarResult(
     sResult: Array<Array<str>>
   ): ports.CheckCarResult {
        let resultSet: Set<ports.CheckCarResult> = unsafeCast(
            structx.fromFieldArray(
                sResult, ports.CheckCarResult.schema()
            )
        );
        return resultSet.toArray().at(0);
    }

    pub inflight static DateTime(dateTime: str): std.Datetime {
        return datetimex.parse(
            dateTime,
            datetimex.DatetimeFormat.YYYYMMDD_HHMM
        );
    }

    pub inflight static PaymentError(error: str): ports.PaymentError {
        return Map<ports.PaymentError>{
            "NONE" => ports.PaymentError.NONE,
            "GENERIC_ERROR" => ports.PaymentError.GENERIC_ERROR,
            "CARD_DECLINED" => ports.PaymentError.CARD_DECLINED
        }.get(error);
    }
}
```

이 클래스는 알고리즘적인 관점에서는 정교하지 않지만, 아주 중요한 아키텍처 결정을 반영하고 있습니다.

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

먼저, 시스템 Ports에 대한 의존성을 src\\application\ports 폴더에 위치한 것으로 발표합니다. "헥사고널 아키텍처 설명" 책 4.8장인 "내 파일을 어디에 넣어야 하나요?"는 명확한 문장을 제시합니다.

그러나 "패턴의 의도와 일치하지 않는 폴더 구조는 손해를 입히는 결과를 초래한다"고 경고합니다. Java와 같은 강력한 타입 언어의 경우, 주행 및 주행되는 포트의 명세를 별도의 폴더에 유지하는 것을 권장합니다.

저는 이러한 구조로 시작했지만, 매우 빨리 코드의 크기를 증가시키고 Wing 모듈과 import 규칙을 완전히 활용하지 못하게 만든다는 것을 깨달았습니다. 이를 고려하여 모든 포트를 하나의 전용 폴더에 유지하기로 결정했습니다. 현재 애플리케이션의 규모를 고려할 때, 이 결정은 정당화된 것으로 보입니다.

둘째, 클래스 이름이 Util인 클래스의 모든 public static inflight 메서드를 고객 모듈이 직접 액세스할 수 있는 특정 Wing 모듈 및 import 기능을 활용하여 코드 가독성을 향상시킵니다.

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

Third, it uses two Wing Standard Library extensions, datetimex, and structx, which were developed to compensate for some features I needed. These extensions were part of my “In Search for Winglang Middleware” project endor.w, which I reported about here, here, and here.

Justification for these extensions will be clarified when we look at the core architectural decision about representing the Port Interfaces and Data.

# Representing Port Interfaces and Data

Traditional strongly typed Object-Oriented languages like Java advocate encapsulating all domain elements as objects. If I followed this advice, the Ticket object would look something like this:

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
pub inflight class Ticket {
  pub ticketCode: str;
  pub carPlate: str;
  pub rateName: str;
  pub startingDateTime: std.Datetime;
  pub endingDateTime: std.Datetime;
  pub price: num;
  pub paymentId: str;

  new (ticketCode: str, ...) {
    this.ticketCode = ticketCode;
    ...
  }
  pub toJson(): Json {
    return Json {
       ticketCode: this.ticketCode,
       ...
  }
  pub static fromJson(data: Json): Ticket {
    return new Ticket(
      data.get("ticketCode").asStr(),
      ...
   );
  }
  pub toFieldArray(): Array<str> {
    return [
      this.ticketCode,
      ...
    ];
  }
  pub static fromFieldArray(records: Array<Array<str>>): Set<Ticket> {
    let result = new MutSet<Ticket>[];
    for record in records {
      result.add(new Ticket(
        record.at(0),
        ...
      );
    }
    return result.copy();
 }
}
```

이러한 방식으로 데이터 필드 당 초기화 및 변환을 위해 코드 줄을 6줄 추가하는 것 외에도 일정한 메서드 정의의 오버헤드가 생깁니다. 이는 상당한 보일러플레이트 오버헤드를 만듭니다.

자바 및 파이썬과 같은 대중적 언어들은 데코레이터, 추상 기본 클래스 또는 메타 클래스와 같은 다양한 메타 프로그래밍 자동화 도구로 이러한 문제를 완화하려고 노력합니다.

Wing에서는 Wing 표준 라이브러리에 소규모 조정을 가하여, 이러한 모든 접근 방식이 비효율적이고 불필요하다는 것이 입증되었습니다.

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

티켓 데이터 구조는 다음과 같이 정의될 수 있습니다:

```js
pub struct Ticket {                 //객체 데이터를 나타내는 데이터 구조
                                    //주차 티켓의 데이터 포함:
    ticketCode: str;                //티켓의 고유 식별자;
                                    //필요한 경우 선행 0이 포함된 10자리 숫자
    carPlate: str;                  //주차된 차량의 번호판
    rateName: str;                  //주차된 위치의 요금 이름
    startingDateTime: std.Datetime; //주차 기간이 시작되는 시간
    endingDateTime: std.Datetime;   //주차 기간이 종료되는 시간
    price: num;                     //티켓을 위해 지불한 유로 액수
    paymentId: str;                 //티켓을 얻기 위해 한 결제의 고유 식별자.
}
```

## 주차차량을 위한 ForParkingCars Port

"Hexagonal Architecture Explained" 책의 권장에 따라 포트 네이밍은 `ActorName`에 대해 For`ActorName` 규칙을 따릅니다. 여기 주차 차량 외부 엑터를 위해 정의된 내용입니다:

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

```rust
pub struct BuyTicketRequest {  // 차 티켓을 구매하는 데 필요한 입력 데이터:
  carPlate: str;    // 주차된 차량의 차량 번호판
    rateName: str;  // 주차된 지역의 요금 이름
    euros: num;     // 지불할 유로 액수
    card: str;      // 지불에 사용되는 카드, 'n-c-mmyyyy' 형식으로
                    // 'n'은 카드 번호(16자리)
                    // 'c'는 확인 코드(3자리),
                    // 'mmyyyy'는 만료 월 및 연도(6자리)
}


/**
 * DRIVING PORT (제공된 인터페이스)
 */
pub inflight interface ForParkingCars {
 /**
  * 도시의 규제된 지역에서 차를 주차할 수 있는 요금 목록을 가져옵니다.
  * 요금이 없는 경우 빈 세트가 반환됩니다.
  */
 getAvailableRates(): Set<rate.Rate>;

 /**
  * 요금을 지불하여 요금이 부과된 지역에 차를 주차하고
  * 티켓을 저장합니다. 티켓의 유효 기간은 현재 일시에서 시작하며,
  * 유로 지불 금액을 기반으로 요금을 적용하여 분 단위로 계산됩니다.
  * @param request 차 티켓 구매에 필요한 입력 데이터.
  *      @see BuyTicketRequest
  * @return 요금이 부과된 지역에서 차를 주차하기 위한 유효한 티켓,
  *    카드를 사용하여 유로 금액을 지불합니다.
  *    티켓은 지불의 식별자에 대한 참조를 유지합니다.
  * @throws BuyTicketRequestException
  *    요청의 입력 데이터가 유효하지 않은 경우.
  * @throws PayErrorException
  *    지불 중 오류가 발생한 경우.
  */
 buyTicket (request: BuyTicketRequest): ticket.Ticket;
}
```

티켓 및 요금 객체와 마찬가지로 BuyTicketRequest 객체는 자동 변환 인프라에 의존하는 일반 Wing 구조체로 정의됩니다.

ForParkingCars는 Wing 인터페이스로 정의됩니다. 원래의 "Blue Zone" 구현과 달리 이 구현은 포트 사양에 BuyTicketRequest 유효성 검사를 포함하지 않습니다. 이것은 일부러 이루어졌습니다.

강력한 객체 캡슐화는 validate() 메소드를 BuyTicketRequest 클래스에 포함시키는 것을 권장하지만, 여기서 채택된 개방형 불변 데이터 구조와 같은 상황에서는 관련 use case 구현에 해당 로직을 포함시키는 것이 좋습니다. 그 반면, 포트 사양에 요청 유효성 검사 로직을 포함하면 너무 많은 구현 세부 사항이 너무 일찍 들어가게 됩니다.

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

## 관리 포트 사용법

이 기능은 testFixture 기능을 제공하기 위해 사용되며 길지만 완전히 직관적입니다:

```js
bring "./Rate.w" as rate;
bring "./Ticket.w" as ticket;
bring "./ForPaying.w" as forPaying;


/**
 * 운전 포트 (제공되는 인터페이스)
 * 초기화, 저장소에 데이터 로드, 앱에서 사용하는 서비스 구성 등과 같은 관리 작업 수행에 사용됩니다.
 * 일반적으로 사용되는 곳:
 *      - 테스트 (운전 액터)에서 테스트 픽처(구동 액터)를 설정하는 데
 *      - 앱 초기화를 시작하는 데
 */
 pub inflight interface ForAdministering {

    /**
    * 주어진 요율을 데이터 저장소에 로드하고,
    * 이전에 있는 요율은 삭제합니다.
    */
    initializeRates(newRates: Array<rate.Rate>): void;

    /**
    * 주어진 티켓을 데이터 저장소에 로드하고,
    * 이전에 있는 티켓은 삭제합니다.
    */
    initializeTickets(newTickets: Array<ticket.Ticket>): void;

    /**
    * 주어진 티켓 코드를 요청 시 반환될 다음 코드로 지정합니다.
    */
    changeNextTicketCode(newNextTicketCode: str): void;

    /**
    * 주어진 코드로 저장소에 있는 티켓을 반환합니다.
    */
    getStoredTicket(ticketCode: str): ticket.Ticket;

    /**
    * "pay" 메서드에 마지막으로 수행한 요청을 반환합니다.
    */
    getLastPayRequest(): forPaying.PayRequest;

    /**
    * "pay" 메서드에서 반환된 마지막 응답을 반환합니다.
    * 이것은 결제된 식별자입니다.
    */
    getLastPayResponse(): str;

    /**
    * 결제 오류의 확률을 매개변수로 제공된 "백분율"로 설정합니다.
    */
    setPaymentError(errorCode: forPaying.PaymentError): void;

    /**
    * "pay" 메서드 실행 중에 발생한 오류 코드를 반환합니다.
    */
    getPaymentError(): forPaying.PaymentError;

    /**
    * 주어진 일시를 현재 일시로 설정합니다.
    */
    changeCurrentDateTime(newCurrentDateTime: std.Datetime): void;

}
```

이제 우리는 한 단계 더 깊이 파고들어 애플리케이션 로직 구현을 살펴봐야 합니다.

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

# 구현 세부 내용

## 주차 차량용 백엔드

```js
import "../../application/ports" as ports;
import "../../application/usecases" as usecases;

public class ForParkingCarsBackend implements ports.ForParkingCars {
    private final usecases.BuyTicket buyTicket;
    private final usecases.GetAvailableRates getAvailableRates;

    public ForParkingCarsBackend(
        ports.ForStoringData dataRepository,
        ports.ForPaying paymentService,
        ports.ForObtainingDateTime dateTimeService
    ) {
        this.buyTicket = new usecases.BuyTicket(
            dataRepository, paymentService, dateTimeService);
        this.getAvailableRates = new usecases.GetAvailableRates(
            dataRepository);
    }

    public Set<ports.Rate> getAvailableRates() {
        return getAvailableRates.apply();
    }

    public ports.Ticket buyTicket(ports.BuyTicketRequest request) {
        return buyTicket.apply(request);
    }
}
```

이 클래스는 src/outside/backend 폴더에 위치해 있으며 직접적인 함수 호출에 적합한 ports.ForParkingCars 인터페이스의 구현을 제공합니다. 두 가지 추가 보조 포트인 ports.ForStoringData 및 ports.ForObtainingTime을 전제로하며 실제 구현은 두 Use Case 구현에 위임됩니다: BuyTicket 및 GetAvailableRates. BuyTicket Use Case 구현에 핵심 시스템 로직이 포함되어 있으므로 해당 부분을 살펴보겠습니다.

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

## 티켓 구매 사용 사례

```js
수학 가져오기;
datetimex 가져오기;
예외 가져오기;
"../ports"를 ports로 가져오기;
"./Verifier.w"를 validate로 가져오기;

pub 클래스 BuyTicket {
    _dataRepository: ports.ForStoringData;
    _paymentService: ports.ForPaying;
    _dateTimeService: ports.ForObtainingDateTime;

    새로운(
        dataRepository: ports.ForStoringData,
        paymentService: ports.ForPaying,
        dateTimeService: ports.ForObtainingDateTime
    ) {
        this._dataRepository = dataRepository;
        this._paymentService = paymentService;
        this._dateTimeService = dateTimeService;
    }

    pub inflight apply(request: ports.BuyTicketRequest): ports.Ticket {
        let currentDateTime = this._dateTimeService.getCurrentDateTime();
        this._validateRequest(request, currentDateTime);
        let paymentId = this._paymentService.pay(
            euros: request.euros,
            card: request.card
        );
        let ticket = this._buildTicket(request, paymentId, currentDateTime);
        this._dataRepository.saveTicket(ticket);
        return ticket;
    }

    inflight _validateRequest(request: ports.BuyTicketRequest, currentDateTime: std.Datetime): void {
        let requestErrors = validate.BuyTicketRequest(request, currentDateTime);
        if requestErrors.length > 0 {
            throw exception.ValueError(
                "티켓 구매 요청이 유효하지 않습니다",
                requestErrors
            );
        }
    }

    inflight _buildTicket(
        request: ports.BuyTicketRequest,
        paymentId: str,
        currentDateTime: std.Datetime
    ): ports.Ticket {
        let ticketCode = this._dataRepository.nextTicketCode();
        let rate = this._dataRepository.getRateByName(request.rateName);
        let endingDateTime = BuyTicket._calculateEndingDateTime(
            currentDateTime,
            request.euros,
            rate.eurosPerHour
        );
        return ports.Ticket {
            ticketCode: ticketCode,
            carPlate: request.carPlate,
            rateName: request.rateName,
            startingDateTime: currentDateTime,
            endingDateTime: endingDateTime,
            price: request.euros,
            paymentId: paymentId
        };
    }

    /**
     * minutes = (euros * minutesPerHour) / eurosPerHour
     * endingDateTime = startingDateTime + minutes
     */
     static inflight _calculateEndingDateTime(
        startingDateTime: std.Datetime,
        euros: num,
        eurosPerHour: num
    ): std.Datetime {
        let MINUTES_PER_HOUR = 60;
        let minutes = math.round((MINUTES_PER_HOUR * euros) / eurosPerHour);
        return datetimex.plus(startingDateTime, duration.fromMinutes(minutes));
    }
}
```

"Buy Ticket" 사용 사례 구현 클래스는 src/application/usecases 폴더에 있습니다. 이것은 사용 사례 로직을 실행하는 inflight 함수를 반환합니다:

- 요청 유효성 검사
- 새 티켓 결제
- 티켓 레코드 생성
- 데이터베이스에 티켓 레코드 저장

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

Implementing Use Cases as inflight functions is essential because all Wing event handlers are inflight functions. While direct function calls are handy for local testing, they will mostly be HTTP REST or GraphQL API calls in a real deployment.

The validation of the BuyTicketRequest is actually handled by a utility class within the Verifier.w module. This approach is taken because individual field validation could be very detailed and involve numerous low-level specifics, which add little to the overall understanding of the use case logic.

## Bringing Everything Together

In line with suggestions from the "Hexagonal Architecture Explained" book, this is accomplished within a Configurator class as shown below:

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
import util;
import endor;
import outside from "./outside";
import ports from "./application/ports";


enum ApiType {
    DIRECT_CALL,
    HTTP_REST
}

enum ProgramType {
    UNKNOWN,
    TEST,
    SERVICE
}

class Configurator extends outside.BlueZoneApiFactory {
    _apiFactory;

    constructor(name) {
        let mockService = new outside.mock.MockDataRepository();
        let programType = this._getProgramType(name);
        let mode = this._getMode(programType);
        let apiType = this._getApiType(programType, mode);
        this._apiFactory = this._getApiFactory(
            name,
            mode,
            apiType,
            mockService,
            mockService,
            mockService
        );
    }

    _getProgramType(name) { //TODO: migrate to endor??
        if (name.endsWith("Test")) {
            return ProgramType.TEST;
        } else if (name.endsWith("Service") || name.endsWith("Application")) {
            return ProgramType.SERVICE;
        } else if (std.Node.of(this).app.isTestEnvironment) {
            return ProgramType.TEST;
        }
        return ProgramType.UNKNOWN;
    }

    _getMode(programType) {
        if (let mode = util.tryEnv("MODE")) {
            return {
                "DEV": endor.Mode.DEV,
                "TEST": endor.Mode.TEST,
                "STAGE": endor.Mode.STAGE,
                "PROD": endor.Mode.PROD
            }[mode];
        } else if (programType === ProgramType.TEST) {
            return endor.Mode.TEST;
        } else if (programType === ProgramType.SERVICE) {
            return endor.Mode.STAGE;
        }
        return endor.Mode.DEV;
    }

    _getApiType(programType, mode) {
        if (let apiType = util.tryEnv("API_TYPE")) {
            return {
                "DIRECT_CALL": ApiType.DIRECT_CALL,
                "HTTP_REST": ApiType.HTTP_REST
            }[apiType];
        } else if (programType === ProgramType.SERVICE) {
            return ApiType.HTTP_REST;
        }
        let target = util.env("WING_TARGET");
        if (target.includes("sim")) {
            return ApiType.DIRECT_CALL;
        }
        return ApiType.HTTP_REST;
    }

    _getApiFactory(name, mode, apiType, dataService, paymentService, dateTimeService) {
        let directCall = new outside.DirectCallApiFactory(
            dataService,
            paymentService,
            dateTimeService
        );
        if (apiType === ApiType.DIRECT_CALL) {
            return directCall;
        } else if (apiType === ApiType.HTTP_REST) {
            return new outside.HttpRestApiFactory(
                name,
                mode,
                directCall
            );
        }
    }

    getForAdministering() {
        return this._apiFactory.getForAdministering();
    }

    getForParkingCars() {
        return this._apiFactory.getForParkingCars();
    }

    getForIssuingFines() {
        return this._apiFactory.getForIssuingFines();
    }

}
```

This is an experimental, still not final, implementation, but it could be extended to address the production deployment needs. It adopts a static system configuration by exploiting the Wing preflight machinery.

In this implementation, a special MockDataStore object implements all three Secondary Ports: data service, paying service, and date-time service. It does not have to be this way and was created to save time during the scaffolding development.

The main responsibility of the Configurator class is to determine which type of API should be used:

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

- 직접 호출
- 로컬 HTTP REST
- 원격 HTTP REST
- 로컬 HTTP REST 및 HTML
- 원격 HTTP REST 및 HTML

실제 API 생성은 해당 ApiFactory 클래스에 위임됩니다.

이러한 구현의 주목할 만한 점은 실제 HTML 기반 UI 모드를 제외한 모든 구성에서 동일한 테스트 스위트를 사용한다는 것입니다. 후자도 가능하지만 Selenium과 같은 HTML 테스트 드라이버가 필요합니다.

이렇게 코드 재사용 수준을 달성한 것은 이번이 처음입니다. 결과적으로 코드 구조 리팩토링을 수행할 때 대부분 로컬 직접 호출 구성을 실행하며, 변경 없이 원격 테스트 및 프로덕션 환경에서 실행될 것을 확신합니다. 이는 Wing 클라우드 지향 프로그래밍 언어와 헥사고날 아키텍처가 정말로 우수한 조합임을 증명합니다.

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

# 큰 그림

각 모듈의 전체 소스 코드를 모두 포함하면 이 문서의 크기가 너무 커질 것입니다. 이 프로젝트의 GitHub 저장소에 대한 액세스는 요청시 제공됩니다.

그 대신 전체 폴더 구조, 두 개의 UML 클래스 다이어그램, 그리고 주요 프로그램 요소와 관계를 반영하는 클라우드 리소스 다이어그램을 제시할 것입니다.

```js
├── src
│   ├── application
│   │   ├── ports
│   │   │   ├── ForAdministering.w
│   │   │   ├── ForIssuingFines.w
│   │   │   ├── ForObtainingDateTime.w
│   │   │   ├── ForParkingCars.w
│   │   │   ├── ForPaying.w
│   │   │   ├── ForStoringData.w
│   │   │   ├── Rate.w
│   │   │   └── Ticket.w
│   │   ├── usecases
│   │   │   ├── BuyTicket.w
│   │   │   ├── CheckCar.w
│   │   │   ├── GetAvailableRates.w
│   │   │   └── Veryfier.w
│   ├── outside
│   │   ├── backend
│   │   │   ├── ForAdministeringBackend.w
│   │   │   ├── ForIssuingFinesBackend.w
│   │   │   └── ForParkingCarsBackend.w
│   │   ├── http
│   │   │   ├── html
│   │   │   │    ├── _htmlForParkingCarsFormatter.ts
│   │   │   │    └── htmlForParkingCarsFormatter.w
│   │   │   ├── json
│   │   │   │    ├── jsonForIssuingFinesFormatter.w
│   │   │   │    └── jsonForParkingCarsFormatter.w
│   │   │   ├── ForIssuingFinesClient.w
│   │   │   ├── ForIssuingFinesController.w
│   │   │   ├── ForParkingCarsClient.w
│   │   │   ├── ForParkingCarsController.w
│   │   │   └── middleware.w
│   │   ├── mock
│   │   │   └── MockDataRepository.w
│   │   ├── ApiFactory.w
│   │   ├── BlueZoneAplication.main.w
│   │   ├── DirectCallApiFactory.w
│   │   └── HttpRestApiFactory.w
│   └── Configurator.w
├── test
│   ├── steps
│   │   ├── BuyTicketTestSteps.w
│   │   ├── CheckCarTestSteps.w
│   │   ├── Parser.w
│   │   └── TestStepsBase.w
│   ├── usecase.BuyTicketTest.w
│   └── usecase.CheckCarTest.w
├── .gitignore
├── LICENSE
├── Makefile
├── README.md
├── package-lock.json
├── package.json
└── tsconfig.json
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

프로젝트의 응용 프로그램 논리적으로는 작지만 이미 구조를 제어하는 데 충분한 도전 과제를 제기합니다. 현재 버전은 다음과 같은 여러 기준 사이의 합리적인 균형을 이루려고 노력했습니다:

- 파일 구조의 깊이.
- 복잡성 및 가져오기 문장의 양.
- 의도한 가치를 전달하는 코드와 그것을 구성, 테스트하고 전달하는 데 필요한 코드 간의 비율.

모든 원하는 메트릭 세트를 계산하는 것은 이 출판물의 범위를 벗어난 일이지만, 여기서 지금 직접 손으로 백업한 계산을 수행해볼 수 있습니다: 응용 프로그램과 외부 폴더 아래의 파일(여기에 "가치"라고 부르기로 하겠습니다)의 백분율과 총 파일 및 폴더 수(이를 "물건"이라고 부르기로 하겠습니다). 현재 버전에서의 숫자는 다음과 같습니다:

총계: 55
src/application: 16
src/: 41
파일: 43
엄격한 가치 대 물건 비율: 16*100/55 = 29.09%
확장된 가치 대 물건 비율: (15+19)*100/42 = 74.55%

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

크게 할지 작게 할지? 좋은 것일지 나쁜 것일지? 지금 당장은 확실히 말하기 어렵습니다. 초기 인상은 숫자가 건강하다는 것입니다. 더 근거 있는 결론을 내기 위해서는 추가적인 연구와 실험이 필요합니다. 실제 제품 시스템은 상당히 많은 테스트가 필요할 것입니다.

인지 부담 관점에서, 43개의 파일은 인간의 커뮤니케이션 채널과 단기 기억의 유명한 7 ± 2 제한을 초과하는 많은 숫자입니다. 조직이 필요합니다. 현재 버전에서 한 수준에서의 최대 파일 수는 8개로 제한 내에 있습니다.

제시된 계층 다이어그램은 실제 그래프 이미지를 부분적으로만 반영합니다. bring 문에 의한 파일 간 종속성이 보이지 않습니다. 또한 결과적인 패키지 크기에 영향을 미치는 외부 종속성을 반영하는 **node_files** 폴더도 빠졌습니다.

간단히 말하면, 도구 및 측정 방법론에 대한 추가 투자 없이는 그림이 부분적일 뿐입니다.

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

우리는 여전히 원하는 방향을 몇 가지 제시할 수 있습니다: 가능한 한 직접 가치를 창출하는 자산들과 이를 작동하게 만드는 데 필요한 지원 요소는 최소한으로 다루고 싶습니다. 이상적으로, 건강한 가치 대 지원물 비율은 언어 및 라이브러리 지원에서 나올 것입니다. 자동 코드 생성, 생성적 AI에 의해 수행되는 것을 포함하여 타이핑을 줄이지만 전반적인 인지 부담은 줄일 것입니다.

# 클래스 다이어그램

단일 UML 클래스 다이어그램에 모든 "Blue Zone" 애플리케이션 요소를 묘사하는 것은 현실적이지 않을 것입니다. UML은 선행 요소와 기체 내 요소를 직접적으로 분리된 표현으로 지원하지 않습니다. 시스템의 가장 중요한 부분을 별도로 시각화할 수 있습니다. 예를 들어, 다음은 애플리케이션 부분용 UML 클래스 다이어그램입니다:

![클래스 다이어그램](/assets/img/2024-06-22-PortedtoCloudwithWinglangPartOne_3.png)

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

이용자들과 주차 감독관 주요 캐릭터와 BuyTicket, CheckCar 사용 케이스와는 이름이 다르다는 것을 주목해주세요. 이것은 실수가 아닙니다. 주요 포트 인터페이스의 이름은 특정 사용 케이스에서 주요 캐릭터 역할을 반영해야 합니다. 이러한 명명에 대한 자동적인 규칙은 없습니다. 선택된 이름이 직관적이기를 희망합니다.

또한, 주요 인터페이스가 응용 프로그램 모듈 내에서 직접 구현되지 않고 이러한 인터페이스와 사용 케이스 구현 사이에 불일치가 있는 것을 주목해주세요.

또한, 이것 또한 실수가 아닙니다. 주요 인터페이스와 해당 사용 케이스 구현 사이의 구체적인 연결은 설정에 따라 다르며 아래 UML 클래스 다이어그램에 반영됩니다:
![다이어그램](/assets/img/2024-06-22-PortedtoCloudwithWinglangPartOne_4.png)

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

위의 클래스 다이어그램에 따르면 구성자(Configurator)가 사용할 IBlueZoneApiFactory 구현체를 결정합니다. DirectApiCallFactory는 로컬 테스트용이며 HttpRestApiFactory는 HTTP를 통한 로컬 및 원격 테스트 및 프로덕션 배포용입니다.

## 클라우드 리소스

![이미지](/assets/img/2024-06-22-PortedtoCloudwithWinglangPartOne_5.png)

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

위의 클라우드 리소스 다이어그램은 Wing 컴파일레이션 결과를 AWS 대상 플랫폼으로 반영한 것입니다. UML 클래스 다이어그램과는 매우 다르며, 다양한 종류의 다이어그램이 함께 동작하여 서로 보완하는 역할을 한다는 결론을 내리게 되었습니다. 클라우드 리소스 다이어그램은 비용, 성능, 신뢰성, 복구력, 보안과 같은 시스템의 운영 측면을 이해하고 제어하는 데 중요합니다.

과거의 다이어그램과 마찬가지로 주요 도전 과제는 규모입니다. 더 많은 클라우드 리소스가 추가되면 다이어그램에 너무 많은 세부 사항이 포함되어 혼란스러울 수 있습니다.

현재 모든 다이어그램의 버전은 공식 청사진보다는 유용한 일러스트레이션에 가깝습니다. 정확성과 이해도 사이의 적절한 균형을 유지하는 것은 앞으로의 연구 주제입니다. 이 문제를 이전에 나의 초기 출간 논문 중 하나에서 다루었습니다. 아마도, 이 연구 주제로 다시 돌아가야 할 때가 온 것 같습니다.

# 결론

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

"최근 출간된 "Hexagonal Architecture Explained" 책에 소개된 "Blue Zone" 애플리케이션을 Java에서 Wing으로 이식하는 경험은 다음과 같은 임시 결론을 이끌어 냈습니다.

- 소프트웨어 애플리케이션을 직접 클라우드로 이식하는 것은 종종 비효율적이고 유지보수하기 어려운 코드를 낳습니다.
- 각 프로그래밍 언어는 디자인 결정을 표현하는 고유한 방식을 갖고 있으며, 하나에서 다른 것으로의 맹목적인 번역은 작동하지 않습니다.
- 새로운 클라우드 중심 프로그래밍 언어 Wing에서 Hexagonal Architecture 패턴을 구현하는 것이 성공적인 조합임이 입증되었습니다. 이 접근 방식은 비용, 성능, 유연성 및 보안 사이의 적절한 균형을 이룹니다.
- 기능 면에서 조금이라도 큰 애플리케이션의 코드베이스 크기는 빠르게 증가합니다. 복잡성을 효과적으로 관리하려면 방법론과 지침이 필요합니다.
- 애플리케이션 논리와 클라우드 리소스의 그래픽 표현은 설명에 유용합니다. 이를 형식적인 청사진으로 변환하려면 추가 연구가 필요합니다.

# 감사의 글

이 출판물을 준비하는 동안 초안을 향상시키고 품질을 보장하기 위해 몇 가지 핵심 도구를 활용하였습니다."

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

초안은 노션의 무료 구독을 통해 조직화할 수 있는 기능을 활용하여 아이디어를 구조화하고 발전시했습니다.

문법 및 철자 검토를 위해 Grammarly의 무료 버전이 기본 오류를 식별하고 수정하여 텍스트의 가독성을 보장하는 데 유용했습니다.

스타일 표현의 향상과 이야기 일관성을 검토하기 위해 ChatGPT 4o의 유료 버전을 사용했습니다. ChatGPT 4o 도구는 Trusted Wing Libraries의 중요한 부분인 datetimex와 TypeScript의 struct를 개발하는 데도 사용되었습니다.

UML 클래스 다이어그램은 PlantText UML 온라인 도구의 무료 버전을 사용하여 생성되었습니다.

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

테스트 단계 클래스(TestSteps)에서 사전 및 비행 정의를 올바르게 처리하는 데 Cristian Pallares의 도움이 중요했습니다.

이 출판물 초안에 중요한 코멘트를 해 주신 Elad Ben-Israel에게 많은 감사를 드립니다.

"블루 존" 애플리케이션의 자바 버전은 책의 공동 저자인 Juan Manuel Garrido de Paz에 의해 개발되었습니다. 안타깝게도 Juan Manuel Garrido de Paz는 2024년 4월에 별세하셨습니다. 그의 추억이 기억되고 이 보고서가 그에게 바치는 축복이 되기를 바랍니다.

모든 고급 도구와 자원이 준비 과정에 상당한 기여를 했지만, 이 문서에 제시된 개념, 해결책 및 최종 결정은 전적으로 제 자신의 것이며, 그에 대한 책임은 전적으로 제게 있습니다.
