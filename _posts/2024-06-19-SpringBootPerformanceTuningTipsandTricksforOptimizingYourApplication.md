---
title: "Spring Boot 성능 튜닝 애플리케이션 최적화를 위한 팁과 트릭"
description: ""
coverImage: "/assets/img/2024-06-19-SpringBootPerformanceTuningTipsandTricksforOptimizingYourApplication_0.png"
date: 2024-06-19 10:11
ogImage: 
  url: /assets/img/2024-06-19-SpringBootPerformanceTuningTipsandTricksforOptimizingYourApplication_0.png
tag: Tech
originalTitle: "Spring Boot Performance Tuning: Tips and Tricks for Optimizing Your Application"
link: "https://medium.com/@dev.ahsanul/spring-boot-performance-tuning-tips-and-tricks-for-optimizing-your-application-caf1f91e75aa"
---


Spring Boot은 사용의 용이성과 빠른 개발 능력으로 유명하지만, 애플리케이션이 커짐에 따라 성능 문제가 발생할 수 있습니다. Spring Boot 애플리케이션의 기능을 향상시키면 응답 속도가 향상되고 리소스를 효율적으로 사용하며 전반적으로 강력한 프로그램이 될 수 있습니다. 이 블로그 글에서는 캐싱, 프로파일링 및 효과적인 데이터베이스 연결에 중점을 두고 Spring Boot 앱의 속도를 최적화하기 위한 중요한 지침과 전략을 살펴볼 것입니다.

![Spring Boot Performance Tuning Tips and Tricks for Optimizing Your Application](/assets/img/2024-06-19-SpringBootPerformanceTuningTipsandTricksforOptimizingYourApplication_0.png)

- Spring Boot Actuator 사용하기:

최적화를 시작하기 전에 병목 현상이 어디에 있는지 이해해야 합니다. 프로파일링 도구를 사용하여 애플리케이션의 성능 문제를 식별할 수 있습니다.
Spring Boot Actuator가 제공하는 운영 준비 기능을 활용하여 애플리케이션을 모니터링하고 제어할 수 있습니다. 이 기능은 어플리케이션이 얼마나 잘 수행되고 있는지에 대한 정보를 제공할 수 있는 여러 엔드포인트를 갖추고 있습니다.

<div class="content-ad"></div>


<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>


아래는 application.properties 파일에서 필요한 엔드포인트를 활성화하는 방법입니다:


management.endpoints.web.exposure.include=health,info,metrics,threaddump,env


성능 메트릭


<div class="content-ad"></div>

Actuator의 /metrics 엔드포인트를 이용하면 애플리케이션의 메모리 사용량, 가비지 수집 및 스레드 활동과 같은 메트릭을 수집할 수 있습니다. 이를 통해 최적화가 필요한 영역을 파악하는 데 도움이 됩니다.

2. 캐싱:

캐싱을 사용하면 데이터로드를 줄이고 응답 시간을 단축할 수 있습니다. Spring은 최소한의 설치로 훌륭한 지원을 제공합니다.

```java
@SpringBootApplication
@EnableCaching
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

<div class="content-ad"></div>

Spring Boot은 EhCache, Hazelcast, 그리고 Redis를 포함한 여러 캐시 제공자를 지원합니다. 예를 들어, EhCache를 사용하려면 다음 종속성을 추가하십시오:

```js
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
<dependency>
    <groupId>net.sf.ehcache</groupId>
    <artifactId>ehcache</artifactId>
</dependency>
```

캐싱 어노테이션을 사용하여 캐싱 동작을 지정할 수 있습니다:

- @Cacheable: 메소드 결과를 캐싱할 수 있음을 나타냅니다.
- @CachePut: 메소드 결과로 캐시를 업데이트합니다.
- @CacheEvict: 캐시에서 항목을 제거합니다.

<div class="content-ad"></div>

예시:

```java
@Cacheable("books")
public Book findBookById(Long id) {
    return bookRepository.findById(id).orElse(null);
}
```

3. 좋은 데이터베이스 상호작용
데이터베이스 상호작용은 종종 응용프로그램의 병목 현상이 될 수 있습니다. 데이터베이스 성능을 최적화하기 위한 몇 가지 팁을 소개합니다. 아래는 HikariCP를 사용한 예시 설정입니다:

쿼리 최적화

<div class="content-ad"></div>

색인 만들기: 데이터베이스 테이블이 올바르게 색인화되었는지 확인하세요.

- 일괄 처리: 대량 삽입 및 업데이트에는 일괄 처리를 사용하세요.
- 지연 로딩: 엔티티 관계에 대해 지연 로딩을 활용하여 불필요한 데이터 로딩을 피하세요.

JPA와 Hibernate 팁

- 검색 유형: 컬렉션에 대해 FetchType.LAZY를 사용하여 불필요한 데이터로딩을 피하세요.
- 두 번째 레벨 캐시: Hibernate의 두 번째 레벨 캐시를 활성화하여 데이터베이스 조회를 줄이세요.

<div class="content-ad"></div>

Query Optimization: JPQL이나 네이티브 쿼리를 사용하여 최적화된 쿼리를 작성하세요.

## 지연 로딩의 예시

```js
@Entity
public class Author {
    @OneToMany(fetch = FetchType.LAZY, mappedBy = "author")
    private Set<Book> books;
}
```

## 데이터베이스 연결 풀링

<div class="content-ad"></div>

데이터베이스 연결을 효율적으로 활용하기 위해 연결 풀을 구성하세요:

```js
spring.datasource.hikari.maximum-pool-size=15
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.idle-timeout=30000
spring.datasource.hikari.connection-timeout=20000
spring.datasource.hikari.max-lifetime=1800000
```

## 모니터링 및 분석

데이터베이스 성능을 모니터링하고 느린 쿼리 또는 연결을 식별하기 위해 /metrics 및 /trace와 같은 액추에이터 엔드포인트를 사용하세요.

<div class="content-ad"></div>

Spring Boot 애플리케이션의 성능을 최적화하려면 프로파일링, 캐싱 및 효율적인 데이터베이스 상호작용의 조합이 필요합니다. 애플리케이션의 성능 특성을 이해하고 이러한 모베스트 프랙티스를 적용하여 높은 성능과 확장 가능성을 갖춘 애플리케이션을 개발할 수 있습니다.