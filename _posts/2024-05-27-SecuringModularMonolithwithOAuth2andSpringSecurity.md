---
title: "모듈화된 단일 모놀리스의 보안을 OAuth2와 Spring Security로 확보하기"
description: ""
coverImage: "/assets/img/2024-05-27-SecuringModularMonolithwithOAuth2andSpringSecurity_0.png"
date: 2024-05-27 15:50
ogImage:
  url: /assets/img/2024-05-27-SecuringModularMonolithwithOAuth2andSpringSecurity_0.png
tag: Tech
originalTitle: "Securing Modular Monolith with OAuth2 and Spring Security"
link: "https://medium.com/itnext/securing-modular-monolith-with-oauth2-and-spring-security-43f2504c4e2e"
---

<img src="/assets/img/2024-05-27-SecuringModularMonolithwithOAuth2andSpringSecurity_0.png" />

제1부: Spring Boot 및 도메인 주도 설계를 사용하여 모듈식 단일체 응용 프로그램 구축

제2부: Spring Modulith를 사용하여 모듈식 단일체 응용 프로그램 개선

제3부: Hexagonal Architecture를 활용한 모듈식 단일체에서 도메인 중심 사고 채택

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

모듈화된 모노리틱 코드베이스에서는 각 기능이 다른 모듈과 순환 종속성이 없는 모듈에 구현되어야 합니다. 그러나 보안과 같은 교차 관심사가 적용되어야 할 때, 보안 관련 코드는 어디에 배치해야 할까요? 각 모듈에 있어야 할까요? 아니면 별도의 모듈이어야 할까요? 함께 알아보겠습니다.

이전 블로그에서 우리는 도서관 애플리케이션을 개발했습니다. 이 애플리케이션에서 사용자는 책을 대출할 수 있습니다. 그러나 보안이 없으면 어플리케이션을 사용하는 대여자를 식별할 방법이 없고 다른 사람이 대출한 책을 대출하는지 확신할 수 없습니다. 올바른 동작을 보장하기 위해 인증 및 권한 부여 규칙을 추가하려고 합니다. 우리는 애플리케이션을 보호하기 위해 OAuth2 프로토콜을 사용할 것입니다.

## OAuth2 / OIDC 프로토콜

코드 구조와 모듈 조직에 대한 토론에 앞서, OAuth2에 대한 간단한 개요부터 살펴보겠습니다. 이미 알고 계시다면 이 섹션을 건너뛰어도 됩니다.

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

OAuth2를 이해할 수 있는 많은 훌륭한 리소스들이 있기 때문에 여기에서는 세부적인 내용을 다루지 않겠습니다. 대신 OAuth2를 활용한 솔루션을 설계하는 방법에 대해 이야기하려고 합니다.

OAuth2로 해야 할 세 가지 중요한 사항은 "인가 서버(Authorization Server)", "리소스 서버(Resource Server)", 그리고 "클라이언트(Client)"입니다. 클라이언트는 일반적으로 웹 애플리케이션의 UI로 사용되지만 Terraform 제공자나 자동화를 위한 CI/CD 파이프라인, 그리고 테스트를 위한 Postman이나 Insomnia일 수도 있습니다. 리소스 서버는 리소스를 제공하는 백엔드 API를 나타냅니다. 우리의 경우에는 이를 "도서관 어플리케이션"으로 정의할 수 있습니다. 중요한 점은 리소스 서버가 사용자의 인증 및 관리 책임이 없다는 것입니다. 이 역할을 하는 것은 인가 서버입니다.

OAuth2 솔루션을 구축할 때 가장 중요한 구성 요소는 인가 서버입니다. 사용자 관리 및 사용자 자격 증명을 보관하는 역할을 합니다. 인가 서버를 선택하는 것은 중요한 결정입니다. SaaS를 구축 중이라면 Auth0나 Okta와 같은 SaaS 솔루션을 선택할 수 있습니다. 자체 호스팅(또는 온프레미스)해야 하는 경우 Keycloak을 선택할 수 있습니다. 또는 Spring Authorization Server를 사용하여 직접 구축할 수도 있지만, 거의 항상 좋은 아이디어는 아닙니다. 우리의 구현에는 Keycloak을 선택할 것입니다.

UI가 없는 경우, 이 흐름을 사용하여 API를 테스트할 수 있습니다: 클라이언트(Postman이나 Insomnia)는 사용자를 인증하고 액세스 토큰을 획득하기 위해 인가 서버(Keycloak)를 호출합니다. 이 액세스 토큰을 사용하면 클라이언트가 리소스 서버(도서관 어플리케이션)의 데이터에 액세스할 수 있습니다. 이는 Authorization Code Flow로도 알려져 있습니다.

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

## 스프링 시큐리티를 사용한 OAuth2

이제 우리 애플리케이션이 OAuth2 플로우에서 리소스 서버로 작동한다는 것을 이해했으니, pom.xml에 관련 의존성을 포함해야 합니다.

```js
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>

<dependency>
  <groupId>org.springframework.security</groupId>
  <artifactId>spring-security-oauth2-resource-server</artifactId>
</dependency>

<!-- JWT를 다루기 위한 의존성 -->
<dependency>
  <groupId>org.springframework.security</groupId>
  <artifactId>spring-security-oauth2-jose</artifactId>
</dependency>
```

그 다음으로, OAuth2 리소스 서버를 활성화 하기 위해 스프링 시큐리티를 구성합니다.

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
@Configuration
@EnableMethodSecurity
public class LibraryWebSecurityConfiguration {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity security) throws Exception {

        return security
                .authorizeHttpRequests(http -> http
                        .requestMatchers("/swagger-ui/**", "/v3/api-docs/**").permitAll()
                        .anyRequest().authenticated())
                .oauth2ResourceServer(oauth2 ->
                        oauth2.jwt(jwtConfigurer ->
                                jwtConfigurer.jwtAuthenticationConverter(new KeycloakJwtAuthenticationConverter())
                        )
                ).build();
    }
}
```

`oauth2.jwt()` 함수는 액세스 토큰에 대한 Json Web Tokens (JWT) 사용을 나타냅니다. 이를 통해 사용자의 역할을 액세스 토큰에서 Spring Security의 내부 Granted Authorities로 추출하는 사용자 정의 컨버터를 사용합니다.

또한 우리는 리소스 서버가 JWT 토큰을 특정 인가 서버로 유효성 검사하도록 설정해야 합니다. 이는 application.yaml 파일에서 구성할 수 있습니다.

```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: http://localhost:8083/realms/library
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

## 보안을 위한 새로운 모듈

<img src="/assets/img/2024-05-27-SecuringModularMonolithwithOAuth2andSpringSecurity_1.png" />

보안과 관련된 클래스를 관리하기 위해 User Account라는 새 모듈을 소개하려고 합니다. 이 모듈에는 UserAccount라는 인증된 사용자를 나타내는 도메인 모델이 포함될 것입니다. 다른 모듈들은 UserAccount를 참조하여 자신만의 내부 표현에 매핑할 수 있습니다. Borrow 모듈에는 Patron이, Catalog 모듈에는 Staff가 될 것입니다.

```js
.
└── example/
    ├── borrow
    ├── catalog
    ├── useraccount/
    │   ├── web/
    │   │   ├── Authenticated.java
    │   │   └── AuthenticatedUserArgumentResolver.java
    │   ├── KeycloakJwtAuthenticationConverter.java
    │   └── UserAccount.java
    └── LibraryWebSecurityConfiguration.java
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

대출 및 카탈로그 모듈은 현재 인증된 사용자의 세부 정보를 가져와 관련 도메인 모델과 매핑하는 데 사용자 계정 모듈을 사용합니다. 새로운 기능을 위한 미래 모듈도 해당 모듈을 사용할 가능성이 높습니다. 이는 사용자 계정 모듈을 공유 모듈로 만들어줍니다. Spring Modulith는 모듈을 공유로 지정할 수 있는 방법을 제공합니다.

Markdown 형식의 테이블로 변환하면 다음과 같습니다.

| 모듈     | 설명                                                                     |
| -------- | ------------------------------------------------------------------------ |
| 대출     | 사용자 계정 모듈을 사용하여 현재 승인된 사용자의 세부 정보를 가져옵니다. |
| 카탈로그 | 사용자 계정 모듈을 사용하여 현재 승인된 사용자의 세부 정보를 가져옵니다. |

@SpringBoot 애플리케이션 클래스에 @Modulithic 어노테이션을 설정하고 공유 모듈을 정의할 수 있습니다. 이렇게 하면 Spring Modulith가 항상 사용자 계정 모듈을 부트스트랩하도록 할 수 있습니다.

## 모듈에서 인증된 사용자 식별하기

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

인증된 사용자는 UserAccount 레코드로 모델링됩니다. 사용자 세부 정보를 영속화할 필요가 없기 때문에 이를 집계로 정의하지 않습니다. 사용자, 역할 및 자격 증명의 참 소스는 Keycloak입니다.

```js
/**
 * 사용자 계정을 나타내는 모델입니다.
 * 아직 필요가 없기 때문에 집계로 이동되지 않았습니다.
 */
public record UserAccount(String firstName,
                          String lastName,
                          String email,
                          List<String> roles) {}
```

대출 모듈에서 홀드를 배치할 때, 인증된 사용자를 알아야 하며, 컨트롤러의 Patron 모델과 매핑해야 합니다. 이 작업은 사용자 정의 어노테이션 @Authenticated로 수행됩니다. 사용자 지정 HandlerMethodArgumentResolver를 사용하여 컨트롤러 메서드에 자동으로 UserAccount를 주입할 수 있습니다.

```js
@PostMapping("/borrow/holds")
ResponseEntity<HoldDto> holdBook(@RequestBody HoldRequest request, @Authenticated UserAccount userAccount) {
    var command = new Hold.PlaceHold(new Book.Barcode(request.barcode()), LocalDate.now(), new PatronId(userAccount.email()));
    var holdDto = circulationDesk.placeHold(command);
    return ResponseEntity.ok(holdDto);
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

사용자 계정은 새로운 PatronId(userAccount.email())와 매핑되어 있습니다. CirculationDesk 서비스는 인증 방식에 대한 지식 없이 특정 Patron만을 다룹니다.

## 권한 부여 대 비즈니스 규칙

새로운 두 가지 비즈니스 요구 사항이 있습니다:

- 도서관 직원만이 카달로그에 새 책을 추가할 수 있습니다.
- 다른 회원이 대출한 책을 대출할 수 없습니다.

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

이 요구 사항을 도메인 모델에 매핑해 봅시다. 현재 Library Staff 구성원에 대한 모델이 없습니다. 이를 ROLE_STAFF 역할을 갖는 인증된 사용자로 정의할 수 있습니다. 두 번째 요구 사항은 이미 있는 도메인 모델인 Patron으로 모델에 변경이 필요하지 않습니다.

첫 번째 요구 사항은 RBAC(Role-Based Access Control)의 간단한 경우입니다. Spring Security의 Method Security @PreAuthorize 주석을 사용하여 구현할 수 있습니다.

```java
@PreAuthorize("hasRole('STAFF')")
@PostMapping("/catalog/books")
ResponseEntity<BookDto> addBookToInventory(@RequestBody AddBookRequest request) {
    var bookDto = books.addToCatalog(request.title(), new Barcode(request.catalogNumber()), request.isbn(), request.author());
    return ResponseEntity.ok(bookDto);
}
```

사용자의 역할은 JWT 토큰에서 식별됩니다. 역할이 어느 클레임에서 사용 가능할지는 인가 서버가 결정합니다. 저희의 경우 Keycloak에서는 realm*access.roles[] 클레임에서 역할을 사용할 수 있습니다. Spring Security가 역할로 인식하려면 역할에는 기본적으로 ROLE* 접두사가 있어야 하지만, GrantedAuthorityDefaults를 사용하여 변경할 수도 있습니다. 토큰 처리 중에 각 역할을 GrantedAuthority로 변환합니다. Authorization 결정을 내릴 때 AccessDecisionManager에 의해 이러한 권한이 읽힙니다.

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

두 번째 요구 사항이 더 흥미롭습니다. 권한 규칙인지 비즈니스 규칙인지 어떻게 판단해야 할까요? 리소스에 대한 액세스를 설명하고 있기 때문에 권한 규칙으로 보이지만, 반대로 도메인의 기능을 설명하고 있기 때문에 비즈니스 규칙으로도 볼 수 있습니다. 그렇다면 이를 어떻게 구현해야 할까요?

권한 규칙으로써 요구 사항을 구현할 수 없는 이유는 해당 도메인 모델의 속성을 확인해야하기 때문입니다. 이 경우, Hold 객체가 올바른 Patron에 의해 보유되어 있는지 확인해야 합니다. 따라서 RBAC(Role-Based Access Control) 대신 ABAC(Attributed-Based Access Control)이 필요합니다. Spring Security에는 ABAC을 내장 지원하지 않으므로 직접 구현해야 합니다.

반면, 이 요구 사항을 도메인의 비즈니스 제약 조건으로 처리한다면 도메인 모델이나 서비스에서 확인할 수 있습니다. ABAC보다 간단한 해결책을 택하는 경우, 비즈니스 규칙 확인을 선택하겠습니다.

```js
public CheckoutDto checkout(Hold.Checkout command) {

    var hold = holds.findById(command.holdId())
            .orElseThrow(() -> new IllegalArgumentException("Hold not found!"));

    if (!hold.isHeldBy(command.patronId())) {
        throw new IllegalArgumentException("Hold belongs to a different patron");
    }

    return CheckoutDto.from(
            hold.checkout(command)
                    .then(holds.save)
                    .then(eventPublisher.bookCheckedOut)
    );
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

인증 규칙이 복잡해지면 비즈니스 규칙과 중첩될 수 있습니다. 일반적으로 인가 규칙은 "누가 무엇에 액세스할 수 있는지"에 관심이 있으며, 비즈니스 규칙은 "시스템 및 도메인의 동작"에 관심이 있습니다. 인가 주변에 복잡한 규칙이 많다면, 모든 정책을 한 곳에 모아 쉽게 관리할 수 있는 ABAC 솔루션을 구축하는 것이 좋은 아이디어일 수 있습니다.

## 인증 플로우 트리거

UI가 없으므로 Insomnia(REST 클라이언트)를 사용하여 라이브러리 애플리케이션 및 OAuth2로의 인증을 테스트할 것입니다. 그러나 먼저 애플리케이션을 시작해야 합니다. 이 작업은 docker compose를 사용하여 쉽게 할 수 있습니다.

애플리케이션의 최신 코드는 여기에서 확인할 수 있습니다: https://github.com/xsreality/spring-modulith-with-ddd/tree/part-4-authentication. 로컬에서 코드를 확인하고 mvn spring-boot:build-image를 실행하여 애플리케이션의 로컬 도커 이미지를 빌드해보세요.

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

도커 컴포즈를 실행하여 Keycloak과 애플리케이션을 시작하세요. 이 Keycloak은 realm이라는 이름의 렘을 미리 구성했으며 ROLE_STAFF 역할 및 두 사용자인 john.wick@continental.com 및 winston@continental.com을 정의했습니다. Winston은 도서관 직원입니다. 두 계정의 암호는 "password"입니다.

우리의 realm 라이브러리에 대한 인가 서버 메타데이터는 http://localhost:8083/realms/library/.well-known/openid-configuration에서 확인할 수 있습니다. authorization_endpoint 및 token_endpoint와 같은 두 엔드포인트가 필요한 Authorization Code 플로우가 있습니다.

![이미지](/assets/img/2024-05-27-SecuringModularMonolithwithOAuth2andSpringSecurity_2.png)

다음으로 Insomnia를 설정하여 OAuth2 플로우를 트리거합니다. 아래 스크린샷에 따라 설정을 선택하고 토큰을 가져옵니다.

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

<img src="/assets/img/2024-05-27-SecuringModularMonolithwithOAuth2andSpringSecurity_3.png" />

인증 코드 플로우로 인해 Insomnia가 Keycloak의 로그인 페이지로 리디렉트됩니다. 자격 증명을 입력하고 코드를 엑세스 토큰으로 교환하려면 로그인하세요. 이 토큰은 그런 다음 자원 서버를 Bearer 토큰으로 호출하는 데 사용됩니다. Spring Security는 토큰을 디코딩하고 Keycloak에서 서명을 유효성 검사하고 iss 클레임을 유효성 검사하며 역할을 권한으로 매핑하는 데 신경 씁니다.

## OAuth2의 통합 테스트

Spring Security OAuth2 플로우를 JWT로 통합 테스트하는 아이디어는 전체 OAuth 플로우를 건너뛰고(결국 복잡하기 때문에 서명을 유효성 검사하는 것이 테스트의 중점이 아님) 대신 목킷 JWT를 사용하여 인증 후의 비즈니스 로직을 테스트하는 것입니다. 권한 관점에서는 유효한 인증된 사용자가 나타나므로 권한 규칙을 테스트하기가 쉽습니다.

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

첫 번째 요구 사항을 테스트해 보겠습니다. 카탈로그에 책을 추가하는 테스트를 하려면 역할 ROLE_STAFF가 포함된 JWT 토큰이 필요합니다.

먼저 mockMvc 객체를 Spring Security를 사용하도록 구성해야 합니다.

```js
@BeforeEach
void setUp() {
    this.mockMvc = MockMvcBuilders.webAppContextSetup(context)
            .apply(springSecurity())
            .build();
}
```

역할 ROLE_STAFF를 가진 인증된 사용자를 시뮬레이션하기 위해 권한을 포함한 가짜 JWT 토큰을 생성합니다.

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

@Test
void addBookToCatalogSucceedsWithStaff() throws Exception {
mockMvc.perform(post("/catalog/books")
.with(jwt().authorities(new SimpleGrantedAuthority("ROLE_STAFF")))
.contentType(MediaType.APPLICATION_JSON)
.content("""
{
"title": "Sapiens",
"catalogNumber": "12345",
"isbn": "9780062316097",
"author": "Yuval Noah Harari"
}
"""))
.andExpect(status().isOk())
.andExpect(jsonPath("$.id").exists())
            .andExpect(jsonPath("$.catalogNumber.barcode", equalTo("12345")))
.andExpect(jsonPath("$.isbn", equalTo("9780062316097"))
            .andExpect(jsonPath("$.author.name", equalTo("Yuval Noah Harari"));

만약 역할이 없는 경우 403 Access Denied 응답이 반환되며, 헤더에는 WWW-Authenticate:"Bearer error="insufficient_scope", error_description="The request requires higher privileges than provided by the access token.", error_uri="https://tools.ietf.org/html/rfc6750#section-3.1""가 포함됩니다.

두 번째 요구 사항에서는 대출 모듈은 인증된 사용자의 이메일 주소에서 Patron을 식별합니다. 따라서 가짜 JWT 토큰에는 이메일 클레임이 필요합니다. 이 작업은 쉽게 할 수 있습니다.

@Test
void checkoutBookRestCall() throws Exception {
mockMvc.perform(post("/borrow/holds/018dc74a-4830-75cf-a194-5e9815727b02/checkout")
.with(jwt().jwt(jwt -> jwt.claim("email", "john.wick@continental.com"))))
.andExpect(jsonPath("$.holdId", equalTo("018dc74a-4830-75cf-a194-5e9815727b02"))
            .andExpect(jsonPath("$.patronId", equalTo("john.wick@continental.com"))
.andExpect(jsonPath("$.dateOfCheckout").exists());
}

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

## 발급자 URI 대 JWK Set URI

OAuth2 사양의 주요 요구 사항 중 하나는 권한 부여 서버의 발급자 URI(Authorization Server Metadata 응답의 발급자)와 토큰의 발급자 클레임(iss 클레임)이 일치해야 한다는 것입니다. application.yaml에 발급자 URI가 구성되어 있는 경우, Spring Security가 Authorization Server Metadata를 호출하고 JWK Set URI를 포함한 여러 URL을 찾습니다. 이 URI는 토큰의 서명을 확인하기 위한 공개 서명 키를 반환합니다.

이 흐름의 문제는 리소스 서버가 외부 사용자용 URL에서 권한 부여 서버에 연락해야 한다는 것입니다. 이러한 서버가 API 게이트웨이 또는 역방향 프록시 뒤에 있으면 요청이 게이트웨이에 전송되고 다시 돌아와야 하므로 비효율적입니다.

이 문제를 해결하는 한 가지 방법은 application.yaml 대신 JWK Set URI를 직접 구성하는 것입니다.

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

```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          jwk-set-uri: http://keycloak:8080/realms/library/protocol/openid-connect/certs
```

이 설정은 Spring Security가 인증 서버 메타데이터를 호출하는 것을 건너뛰고 토큰의 서명을 확인하기 위해 JWK Set URI를 직접 호출하도록 만듭니다. API 게이트웨이 뒤에서 JWK Set URI는 통신용으로만 사용되기 때문에 내부 URL일 수 있습니다.

## 결론

Spring Security에 대해 더 말할 수 있는 부분이 많이 있습니다. 그러나 이 블로그에서는 Spring Security 관련 코드가 모듈식 모놀리스 코드베이스에 어떻게 들어 맞는지 살펴보았습니다. 또한 Spring Security를 사용하여 OAuth2 플로우를 구현하고 설정을 통합 테스트했습니다.

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

만약 코드를 확인하고 실행해보고 싶다면 아래를 확인해보세요.
