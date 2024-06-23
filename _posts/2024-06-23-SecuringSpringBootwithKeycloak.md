---
title: "Keycloak으로 Spring Boot 보안 설정하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-SecuringSpringBootwithKeycloak_0.png"
date: 2024-06-23 21:21
ogImage: 
  url: /assets/img/2024-06-23-SecuringSpringBootwithKeycloak_0.png
tag: Tech
originalTitle: "Securing Spring Boot with Keycloak"
link: "https://medium.com/@wahyubagus1910/securing-spring-boot-with-keycloak-b352f05575f2"
---



![사진](/assets/img/2024-06-23-SecuringSpringBootwithKeycloak_0.png)

개요
우리 애플리케이션을 안전하게 보호하는 것은 요즘 큰 걱정이자 도전입니다. 처음부터 구축하는 것은 더 어려울 수 있고 더 많은 시간이 소요될 수 있습니다. 따라서 우리는 IDP(Identity Provider) 도구인 Keycloak과 같이 애플리케이션의 인가 서버로 구현할 수 있습니다.

Keycloak이란

Keycloak은 레드햇에서 개발된 오픈 소스 식별 및 액세스 관리 도구입니다. 쉬운 설정 및 애플리케이션 통합으로 여러분이 직접 모든 것을 맞춤 설정하는 것보다 훨씬 많은 시간을 절약할 수 있습니다.


<div class="content-ad"></div>

많은 회사에서 이미 보안 구성 요소로 채택되어 있어서 도구들이 검증되었고 더욱 신뢰할 수 있는 상태로 구현되어 있습니다. 많은 회사가 Keycloak을 사용하기 때문에 학습 자료를 쉽게 얻고 인터넷에서 의논할 수 있습니다.

주요 기능을 갖고 있기 때문에 비즈니스나 보안 흐름에 대응해야 할 경우 애플리케이션을 미래에 대비하여 더 확장 가능하게 만들 수 있습니다.

구현
Keycloak를 구현하는 여러 방법이 있습니다. 예를 들어 SSO, 소셜 미디어 로그인 또는 액세스 토큰을 얻기 위해 API에 접근하는 방법이 있습니다.

우리는 필요에 따라 구현이 필요한 사용자 정의 흐름을 처리하는 독립된 인증 서비스를 사용하여 구현할 것이지만, 자격 증명 자체를 인증하는 데 Keycloak가 책임을 집니다. 그리고 서비스 레이어의 리소스 서버에서 토큰을 확인합니다.

<div class="content-ad"></div>

시스템 다이어그램

![image](/assets/img/2024-06-23-SecuringSpringBootwithKeycloak_1.png)

사용자는 자격 증명을 제출하고, 이는 인증 서비스에서 처리되어 Keycloak 측에서 유효성이 검사됩니다. 자격 증명이 유효한 경우, Keycloak은 액세스 토큰을 반환하고, 유효하지 않은 경우 응답 401 — 권한 없음을 반환합니다.

물론 애플리케이션 클라이언트(FE 또는 모바일 앱)에게 다시 원시 응답을 반환하지는 않겠습니다. 인증 서비스에서 응답을 표준화하여 처리할 것입니다.

<div class="content-ad"></div>

Keycloak 설치 및 설정
다음 yml 파일을 사용하여 도커 컴포즈로 Keycloak을 설치하고 구성하세요:

```js
version: "3"

services:
  keycloak:
    image: quay.io/keycloak/keycloak:20.0.0
    command: start-dev
    environment:
      KC_DB: postgres
      KC_DB_URL_HOST: postgres
      KC_DB_URL_DATABASE: keycloak_v1
      KC_DB_PASSWORD: root
      KC_DB_USERNAME: postgres
      KC_DB_SCHEMA: public
      KEYCLOAK_ADMIN: admin
      KEYCLOAK_ADMIN_PASSWORD: admin
      KEYCLOAK_JDBC_PARAMS: 'sslmode=require'
    ports:
      - "9090:8080"
    depends_on:
      postgres:
        condition: service_healthy
    networks:
      network_sso:

  postgres:
    image: postgres:10
    command: ["postgres", "-c", "max_connections=200", "-c", "shared_buffers=24MB", "-c", "listen_addresses=*"]
    environment:
      POSTGRES_DB: keycloak_v1
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: root
    healthcheck:
      test: "exit 0"
    ports:
      - "5436:5432"
    networks:
      network_sso:
    volumes:
      - postgres_data:/var/lib/postgresql/data
    user: postgres

  redis:
    image: redis:latest
    ports:
      - "6379:6379"
    environment:
      REDIS_PASSWORD: "root"
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:

networks:
  network_sso:
```

저희 Keycloak 애플리케이션에서 사용자 저장소로 postgres 데이터베이스를 사용하세요. “docker-compose up”을 실행한 후 http://localhost:9090에 액세스하고 다음 자격 증명으로 로그인하세요:

계층을 만들고 원하는 대로 계층 이름 폼을 작성하세요.

<div class="content-ad"></div>

![이미지](/assets/img/2024-06-23-SecuringSpringBootwithKeycloak_2.png)

이 구성대로 클라이언트를 계속 생성하세요.

![이미지](/assets/img/2024-06-23-SecuringSpringBootwithKeycloak_3.png)

![이미지](/assets/img/2024-06-23-SecuringSpringBootwithKeycloak_4.png)

<div class="content-ad"></div>

클라이언트를 성공적으로 생성한 후, 클라이언트 비밀키를 복사하려면 클라이언트 자격 증명 탭으로 이동하세요.

사용자를 생성하고 사용자 자격 증명 또는 암호를 설정한 후, 임시 암호 토글을 비활성화해야 합니다. userdemo와 admindemo 두 명의 사용자를 생성하세요.

![이미지](/assets/img/2024-06-23-SecuringSpringBootwithKeycloak_5.png)

역할 할당

<div class="content-ad"></div>

키클로크에는 렘 역할과 리소스 클라이언트 역할이 있어요. 둘 다 서로 다른 책임과 동작을 가지고 있어요.

렘 역할을 만들어 보세요, "admin"과 "user"와 같이요.

![image](/assets/img/2024-06-23-SecuringSpringBootwithKeycloak_6.png)

리소스 클라이언트 역할도 만들어 보세요, "write"와 "read" 같은 역할을 만들어 보세요.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-23-SecuringSpringBootwithKeycloak_7.png" />

사용자 메뉴로 이동해서 사용자 'admindemo'에게 'admin' 및 'write' 역할을 할당하고, 'userdemo'에게 'user' 및 'read' 역할을 할당하세요.

<img src="/assets/img/2024-06-23-SecuringSpringBootwithKeycloak_8.png" />

코드 구현
Spring 프로젝트를 초기화하고, pom.xml에 다음 종속성을 추가하세요.

<div class="content-ad"></div>

```js
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
 <modelVersion>4.0.0</modelVersion>
 <parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>3.2.2</version>
 </parent>
 <artifactId>auth-service</artifactId>
 <version>0.0.1-SNAPSHOT</version>
 <name>auth-service</name>
 <description>Demo project for Spring Boot</description>
 <properties>
  <java.version>17</java.version>
 </properties>
 <dependencies>
  <dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  <dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-security</artifactId>
  </dependency>
  <dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-oauth2-client</artifactId>
  </dependency>
  <dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-oauth2-resource-server</artifactId>
  </dependency>
  <dependency>
   <groupId>org.apache.commons</groupId>
   <artifactId>commons-collections4</artifactId>
   <version>4.4</version>
  </dependency>
  <dependency>
   <groupId>org.projectlombok</groupId>
   <artifactId>lombok</artifactId>
   <version>1.18.30</version>
   <scope>provided</scope>
  </dependency>
 </dependencies>

 <build>
  <plugins>
   <plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
   </plugin>
   <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>3.2.2</version>
   </plugin>
  </plugins>
 </build>

</project>
```

KeycloakJwtRolesResolver.java 파일은 키클로캣 액세스 토큰에서 로그인한 사용자 역할을 추출하여 GrantedAuthority 컬렉션에 저장하는 것입니다 (여기서 토큰을 디버그할 수 있습니다). 이 코드에서는 렘 역할을 구성하고 리소스 역할에 연결한 다음 대문자로 변환합니다. 결과는 ADMIN_WRITE 또는 USER_READ여야 합니다.

```js
@Slf4j
public class KeycloakJwtRolesConverter implements Converter<Jwt, Collection<GrantedAuthority>> {

    private static final String CLAIM_REALM_ACCESS = "realm_access";
    private static final String CLAIM_RESOURCE_ACCESS = "resource_access";
    private static final String CLAIM_ROLES = "roles";

    private final String kcClientId;

    public KeycloakJwtRolesConverter(String kcClientId) {
        this.kcClientId = kcClientId;
    }

    @Override
    public Collection<GrantedAuthority> convert(Jwt jwt) {
        Map<String, Collection<String>> realmAccess = jwt.getClaim(CLAIM_REALM_ACCESS);
        Map<String, Map<String, Collection<String>>> resourceAccess = jwt.getClaim(CLAIM_RESOURCE_ACCESS);

        Collection<GrantedAuthority> grantedAuthorities = new ArrayList<>();

        if (realmAccess != null && !realmAccess.isEmpty()) {
            Collection<String> realmRole = realmAccess.get(CLAIM_ROLES);
            if (realmRole != null && !realmRole.isEmpty()) {
                realmRole.forEach(r -> {
                    if (resourceAccess != null && !resourceAccess.isEmpty() && resourceAccess.containsKey(kcClientId)) {
                        resourceAccess.get(kcClientId).get(CLAIM_ROLES).forEach(resourceRole -> {
                            String role = String.format("%s_%s", r, resourceRole).toUpperCase(Locale.ROOT);
                            grantedAuthorities.add(new SimpleGrantedAuthority(role));
                        });
                    } else {
                        grantedAuthorities.add(new SimpleGrantedAuthority(r));
                    }
                });
            }
        }

        return grantedAuthorities;
    }
}
```

CustomAuthenticationEntryPoint.java 파일은 요청을 인증하는 동안 예외를 처리하는 클래스이며 JSON을 반환합니다.

<div class="content-ad"></div>

```java
@Component
public class CustomAuthenticationEntryPoint implements AuthenticationEntryPoint {

    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException) throws IOException, ServletException {
        response.setStatus(HttpStatus.UNAUTHORIZED.value());
        response.setContentType("application/json");

        BaseResponseDto errorResponse = BaseResponseDto.builder()
                .status("UNAUTHORIZED")
                .build();

        ObjectMapper mapper = new ObjectMapper();
        mapper.writeValue(response.getWriter(), errorResponse);
    }
}
```

CustomAccessDenied.java은 예외 처리를 위한 클래스입니다. 로그인한 사용자가 일부 엔드포인트에서 유효한 역할이나 권한이 없는 경우 JSON을 반환합니다.

```java
@Component
public class CustomAccessDenied implements AccessDeniedHandler {
    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException accessDeniedException) throws IOException, ServletException {
        response.setStatus(HttpStatus.FORBIDDEN.value());
        response.setContentType(MediaType.APPLICATION_JSON_VALUE);

        BaseResponseDto errorResponse = BaseResponseDto.builder()
                .status("ACCESS_DENIED")
                .build();

        ObjectMapper mapper = new ObjectMapper();
        mapper.writeValue(response.getWriter(), errorResponse);
    }
}
```

WebSecurityConfiguration.java에서는 보안 구성을 정의하고, 일부 엔드포인트를 역할 확인으로 보호하며, 보안 시스템 내에서 사용자 정의 예외 처리를 어떻게 구현하는지 설명합니다.

<div class="content-ad"></div>

jwtDecoder() 함수는 요청에서 받은 JSON 웹 토큰(JWT)을 유효성 검사하고 해독하는 역할을 담당합니다. 이 함수는 토큰을 생성하고 서명하는 인증 서버로 가리키는 발급자를 가리킵니다.

grantedAuthorityDefaults() 함수는 우리 스프링 애플리케이션에서 "ROLE_" 접두사를 제거하는 역할을 합니다.

```js
@Slf4j
@Configuration
@EnableWebSecurity
public class WebSecurityConfiguration {

    @Value("${keycloak.client-id}")
    private String kcClientId;

    @Value("${keycloak.issuer-url}")
    private String tokenIssuerUrl;

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http, CustomAuthenticationEntryPoint entryPoint,
                                                   CustomAccessDenied accessDenied) throws Exception {

        DelegatingJwtGrantedAuthoritiesConverter authoritiesConverter = new DelegatingJwtGrantedAuthoritiesConverter(
                        new JwtGrantedAuthoritiesConverter(),
                        new KeycloakJwtRolesConverter(kcClientId));

        http.authorizeHttpRequests(authorizeRequests ->
                authorizeRequests
                    .requestMatchers("/home/admin/**")
                        .hasRole("ADMIN_WRITE")
                    .requestMatchers("/home/public/**")
                        .hasRole("USER_READ")
                    .requestMatchers("/auth/**").permitAll()
                    .anyRequest().authenticated()
            )
            .httpBasic()
            .and()
            .exceptionHandling()
                .authenticationEntryPoint(entryPoint)
                .accessDeniedHandler(accessDenied)
            .and()
            .csrf().disable()
                .oauth2ResourceServer()
                .jwt()
                .jwtAuthenticationConverter(
                        jwt -> new JwtAuthenticationToken(jwt, authoritiesConverter.convert(jwt))
                );
        return http.build();
    }

    @Bean
    public JwtDecoder jwtDecoder() {
        return JwtDecoders.fromIssuerLocation(tokenIssuerUrl);
    }

    @Bean
    GrantedAuthorityDefaults grantedAuthorityDefaults() {
        return new GrantedAuthorityDefaults("");
    }
}
```

AuthService.java는 Keycloak의 엔드포인트에 요청을 보내 인증할 자격 증명을 제공하는 서비스 클래스입니다. client_id 및 client_secret 자격 증명은 Keycloak에 방금 등록한 클라이언트에서 얻었습니다.

<div class="content-ad"></div>

서비스에는 액세스 토큰을 가져오고 토큰을 새로 고침하는 두 가지 메서드가 있습니다. 액세스 및 새로 고침 토큰을 받았으므로 새로 고침 토큰을 Redis에 REFRESH_TOKEN 상수 키로 저장한 다음 디바이스 ID와 연결합니다. 토큰을 새로 고칠 때 Redis에서 저장할 때와 동일한 키로 가져옵니다.

```js
@Slf4j
@Service
public class AuthService {

    @Autowired
    private RestTemplate restTemplate;

    @Autowired
    private SessionStorage sessionStorage;

    @Value("${keycloak.client-id}")
    private String kcClientId;

    @Value("${keycloak.client-secret}")
    private String kcClientSecret;

    @Value("${keycloak.get-token-url}")
    private String kcGetTokenUrl;

    private static final String GRANT_TYPE_PASSWORD = "password";
    private static final String GRANT_TYPE_REFRESH_TOKEN = "refresh_token";

    private static final String ACCESS_TOKEN = "Access-Token";
    private static final String REFRESH_TOKEN = "Refresh-Token";
    private static final String EXPIRES_IN = "Expires-In";
    private static final String DEVICE_ID = "Device-Id";

    public ResponseEntity<Object> login(LoginRequestDto request, HttpServletRequest servletRequest, HttpServletResponse servletResponse) {
        log.info("액세스 토큰을 가져오기 시작");

        String deviceId = servletRequest.getHeader(DEVICE_ID);

        TokenDto tokenDto = this.getAccessToken(request);

        servletResponse.addHeader(ACCESS_TOKEN, tokenDto.getAccessToken());
        servletResponse.addHeader(EXPIRES_IN, String.valueOf(tokenDto.getExpiresIn()));

        sessionStorage.putCache(REFRESH_TOKEN, deviceId, tokenDto.getRefreshToken(), 1800);

        return ResponseEntity.ok().body(BaseResponseDto.builder()
                .status("SUCCESS")
                .build());
    }

    public ResponseEntity<Object> refreshToken(HttpServletRequest servletRequest, HttpServletResponse servletResponse) {
        log.info("액세스 토큰을 새로 고침하기 시작");

        String deviceId = servletRequest.getHeader(DEVICE_ID);
        String refreshToken = (String) sessionStorage.getCache(REFRESH_TOKEN, deviceId);

        TokenDto tokenDto = this.getRefreshToken(refreshToken);

        servletResponse.addHeader(ACCESS_TOKEN, tokenDto.getAccessToken());
        servletResponse.addHeader(EXPIRES_IN, String.valueOf(tokenDto.getExpiresIn()));

        sessionStorage.putCache(REFRESH_TOKEN, deviceId, tokenDto.getRefreshToken(), tokenDto.getRefreshExpiresIn());

        return ResponseEntity.ok().body(BaseResponseDto.builder()
                .status("SUCCESS")
                .build());
    }

    private TokenDto getAccessToken(LoginRequestDto request) {
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_FORM_URLENCODED);

        MultiValueMap<String, String> requestBody = new LinkedMultiValueMap<>();
        requestBody.add("grant_type", GRANT_TYPE_PASSWORD);
        requestBody.add("client_id", kcClientId);
        requestBody.add("client_secret", kcClientSecret);
        requestBody.add("username", request.getUsername());
        requestBody.add("password", request.getPassword());

        ResponseEntity<TokenDto> response = restTemplate.postForEntity(kcGetTokenUrl,
                new HttpEntity<>(requestBody, headers), TokenDto.class);

        return response.getBody();
    }

    private TokenDto getRefreshToken(String refreshToken) {
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_FORM_URLENCODED);

        MultiValueMap<String, String> requestBody = new LinkedMultiValueMap<>();
        requestBody.add("grant_type", GRANT_TYPE_REFRESH_TOKEN);
        requestBody.add("refresh_token", refreshToken);
        requestBody.add("client_id", kcClientId);
        requestBody.add("client_secret", kcClientSecret);

        ResponseEntity<TokenDto> response = restTemplate.postForEntity(kcGetTokenUrl,
                new HttpEntity<>(requestBody, headers), TokenDto.class);

        return response.getBody();
    }
}
```

AuthController.java

```js
@RestController
@RequestMapping(value = "/auth")
public class AuthController {

    @Autowired
    private AuthService authService;

    @PostMapping(value = "/login")
    public ResponseEntity<Object> login(@RequestBody LoginRequestDto request, HttpServletRequest servletRequest,
                                        HttpServletResponse servletResponse) {
        return authService.login(request, servletRequest, servletResponse);
    }

    @PostMapping(value = "/refresh-token")
    public ResponseEntity<Object> refreshToken(HttpServletRequest servletRequest, HttpServletResponse servletResponse) {
        return authService.refreshToken(servletRequest, servletResponse);
    }
}
```

<div class="content-ad"></div>

HomeController.java에는 USER 역할로 로그인한 사용자를 위한 공개 엔드포인트 및 ADMIN 역할을 위한 /admin을 처리하는 컨트롤러가 있습니다.

```java
@RestController
@RequestMapping(value = "/home")
public class HomeController {

    @GetMapping(value = "/public")
    public ResponseEntity<Object> home() {
        return new ResponseEntity<>("PAGE_PUBLIC", HttpStatus.OK);
    }

    @GetMapping(value = "/admin")
    public ResponseEntity<Object> homeAdmin() {
        return new ResponseEntity<>("PAGE_ADMIN", HttpStatus.OK);
    }
}
```

테스트

새로 등록한 사용자 자격 증명으로 /api/auth/login 엔드 포인트에 로그인하세요.

<div class="content-ad"></div>


curl을 사용하여 테이블로 admin 엔드포인트로 이동 가능합니다.

```js
curl --location 'http://localhost:8085/api/auth/login' \
--header 'Device-Id: example-my-device-id' \
--header 'Content-Type: application/json' \
--data '{
    "username": "userdemo",
    "password": "useruser123"
}'
```

admin 엔드포인트로 이동하는 curl입니다.

```js
curl --location 'http://localhost:8085/api/home/admin' \
--header 'Authorization: Bearer $access_token'

# 페이지_ADMIN을 반환해야 함
```

public 엔드포인트로 이동하는 curl입니다.


<div class="content-ad"></div>

```js
curl --location 'http://localhost:8085/api/home/public' \
--header 'Authorization: Bearer $access_token'

# 페이지_PUBLIC이 반환되어야합니다
```

사용자가 역할에 맞지 않는 엔드포인트에 액세스하려고하면 403 — 금지로 반환되어야 합니다.

새로 고침 토큰 엔드포인트를 호출하여 리프레시 토큰을 갱신하고 액세스 토큰을 받아옵니다.

```js
curl --location --request POST 'http://localhost:8085/api/auth/refresh-token' \
--header 'Device-Id: example-my-device-id'
```

<div class="content-ad"></div>

업그레이드
동일한 보안 구성을 적용하는 여러 서비스가 있다면, KeycloakJwtRolesResolver.java를 복제하고 중복 코드를 만드는 것은 좋지 않습니다. 더 나은 접근 방식은 해당 클래스를 독립된 스프링 프로젝트로 분리하는 것입니다. 이 프로젝트를 모듈-공통이라고 부르고, 다른 서비스에 공통으로 사용되는 클래스나 기능을 수용하도록 합니다.