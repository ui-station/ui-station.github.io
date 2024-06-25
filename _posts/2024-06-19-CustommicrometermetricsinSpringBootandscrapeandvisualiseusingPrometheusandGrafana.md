---
title: "Spring Boot에서 사용자 정의 micrometer 메트릭 및 스크레이핑, Prometheus 및 Grafana를 사용하여 시각화하기"
description: ""
coverImage: "/assets/img/2024-06-19-CustommicrometermetricsinSpringBootandscrapeandvisualiseusingPrometheusandGrafana_0.png"
date: 2024-06-19 22:09
ogImage:
  url: /assets/img/2024-06-19-CustommicrometermetricsinSpringBootandscrapeandvisualiseusingPrometheusandGrafana_0.png
tag: Tech
originalTitle: "Custom micrometer metrics in Spring Boot and scrape, and visualise using Prometheus and Grafana"
link: "https://medium.com/@yogendra.209/custom-micrometer-metrics-in-spring-boot-and-scrape-and-visualize-using-prometheus-and-grafana-66d020a6c90f"
---

# 개요

비즈니스에 중요한 응용 프로그램에서 관측 가능성은 중요합니다. 이는 응용 프로그램의 내부 상태를 외부에서 관측하는 능력입니다. 자주 언급되는 것처럼 로그, 지표 및 추적은 관측 가능성의 세 축입니다.

- 로그는 이벤트의 연대 기록입니다. 로그는 주로 일반 텍스트로 기록되지만(이진 및 구조화된 형식도 가능함) 디버깅 및 시스템 이벤트 및 실패 이해에 중요합니다.
- 지표는 집계된 데이터 또는 이벤트 또는 응용 프로그램 성능의 현재 상태입니다. 지표는 CPU 사용률, 평균 HTTP 응답 시간 또는 총 HTTP 요청과 같은 실시간 통찰력을 제공할 수 있습니다.
- 추적은 시작부터 끝까지의 엔드 투 엔드 흐름에 대한 상세한 정보를 제공합니다. 요청이 응용 프로그램의 각 구성 요소 또는 서비스를 통과하는 것을 추적합니다. 추적은 로그 및 지표보다 복잡합니다.

이 블로그에서는 지표에 초점을 맞출 것입니다. Spring Boot는 Micrometer와 통합하기 위한 자동 구성을 제공합니다. 이는 SLF4J와 유사하지만 관측 가능성을 위한 것입니다.

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

# 단계

우리는 사용자 정의 지표와 헬스 인디케이터를 생성하고 아래 단계를 따를 것입니다:

- 액추에이터와 마이크로미터 레지스트리를 사용하여 프로메테우스 엔드포인트를 활성화합니다.
- JaMon을 사용하여 REST API 응답 시간을 모니터링하는 Aspect를 생성합니다.
- HTTP 응답 시간과 요청 횟수에 대한 사용자 정의 지표를 생성합니다.
- 사용자 정의 헬스 인디케이터를 생성합니다.
- 프로메테우스를 설정하여 정기적으로 지표를 수집합니다.
- 그라파나 대시보드를 설정하여 거의 실시간으로 지표를 시각화합니다.

# 구현

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

1. 액추에이터와 micrometer 레지스트리를 사용하여 프로메테우스 엔드포인트를 활성화합니다.

다음 의존성을 pom.xml에 추가하세요.

```js
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
```

엔드포인트는 HTTP 또는 JMX를 통해 활성화 또는 노출시킬 수 있습니다. 우리는 HTTP에만 초점을 맞출 것입니다.

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

기본적으로 모든 엔드포인트가 활성화되어 있습니다. 그러나 종료 점은 활성화되지 않습니다.

기본적으로 헬스 엔드포인트만 노출됩니다.

다른 엔드포인트도 노출해 봅시다. 아래 라인을 애플리케이션 속성에 추가해 주세요:

```js
management.endpoints.web.exposure.include=*
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

(\*) 모든 끝점을 노출시킬 것이다 (권장되지 않음). 선택된 끝점을 지정하는 데 쉼표로 구분된 값들을 사용할 수 있습니다. 이제 /actuator/metrics로 이동하면 모든 메트릭 목록을 볼 수 있습니다. 이제 /actuator/prometheus로 이동하면 특정 형식의 유사한 메트릭 및 그 값들을 볼 수 있습니다. 이것이 프로메테우스를 구성할 때 사용할 내용입니다.

2 JaMon을 사용하여 REST API 응답 시간을 모니터링하는 Aspect를 생성하세요.

Around 어드바이스를 생성하세요. 이를 위해 어노테이션을 만들었습니다. AOP에 대한 전체 기사를 작성했습니다. 한 번 시도해보세요. JaMon은 개발자가 애플리케이션을 쉽게 모니터링할 수 있게 하는 Java 애플리케이션 모니터링 API입니다. JaMon 의존성을 주입하세요:

<dependency>
   <groupId>com.jamonapi</groupId>
   <artifactId>jamon</artifactId>
   <version>2.82</version>
</dependency>

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

지금은 JaMon을 위해 빈을 생성하고 구성할 것입니다:

```js
@Configuration
@ComponentScan(basePackages = "com.yogendra.aspect")
@EnableAspectJAutoProxy
public class MonitorConfig {
    @Bean
    MonitorFactoryInterface monitorFactory() {
        return MonitorFactory.getFactory();
    }
}

@Component
@Aspect
public class MonitorAspect {

    private final MonitorFactoryInterface monitorFactoryInterface;

    public MonitorAspect(MonitorFactoryInterface monitorFactoryInterface) {
        this.monitorFactoryInterface = monitorFactoryInterface;
    }


    @Around("@annotation(com.yogendra.annotation.MethodMonitor)")
    public Object monitorMethodExecution(ProceedingJoinPoint joinPoint) throws Throwable {
        MethodSignature methodSignature = (MethodSignature) joinPoint.getSignature();
        Method method = methodSignature.getMethod();
        MethodMonitor methodMonitor = method.getAnnotation(MethodMonitor.class);
        Monitor monitor = monitorFactoryInterface.start(methodMonitor.name());
        try {
            return joinPoint.proceed();
        } finally {
            monitor.stop();
        }
    }
}

@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface MethodMonitor {
    String name() default "";

    String uri() default "";

    String method() default "GET";
}
```

이제 MethodMonitor로 우리의 메서드 중 어떤 것이든 연결하면 JaMon이 모니터링을 시작합니다. AOP가 얼마나 강력한지 보여주는 것이죠.

3. HTTP 응답 시간 및 요청 횟수에 대한 사용자 정의 메트릭 생성

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

요즘 Micrometer에 대해 조금 이야기해보자. Micrometer는 JVM 기반 애플리케이션을 위한 메트릭 인스트루먼테이션 라이브러리야. Micrometer에서 가장 중요한 인터페이스는 Meter야. 미터는 MeterRegistry에 생성되고 보관돼. Step 1에서 추가한 종속성은 PrometheusMeterRegistry를 자동으로 구성할 거야. Micrometer는 Counter, Gauge, Timer, DistributionSummary 등 다양한 타입의 미터를 제공해. 이 예제에서는 Counter와 Gauge를 구현할 거야. http.requests.count와 http.response.time 두 가지 지표를 생성할 거야.

```js
Counter.builder("http.requests.count")
  .tag("uri", methodMonitor.uri())
  .tag("method", methodMonitor.method())
  .register(meterRegistry)
  .increment();

Gauge.builder("http.response.time", monitor::getLastValue)
  .tag("uri", methodMonitor.uri())
  .tag("method", methodMonitor.method())
  .register(meterRegistry);
```

첫 번째 코드는 URI별로 HTTP 메소드(GET, PUT 등) 당 수행된 HTTP 요청 수를 셈. 두 번째 코드는 URI별로 HTTP 메소드(GET, PUT 등)의 API 실행 시간을 측정해. 이전 단계에서 생성한 MonitorAspect에 위 지표들을 추가할 거야. 우리 업데이트된 어드바이스는 이렇게 생겼어:

```js
@Component
@Aspect
public class MonitorAspect {

    private final MonitorFactoryInterface monitorFactoryInterface;
    private final MeterRegistry meterRegistry;

    public MonitorAspect(MonitorFactoryInterface monitorFactoryInterface, MeterRegistry meterRegistry) {
        this.monitorFactoryInterface = monitorFactoryInterface;
        this.meterRegistry = meterRegistry;
    }


    @Around("@annotation(com.yogendra.annotation.MethodMonitor)")
    public Object monitorMethodExecution(ProceedingJoinPoint joinPoint) throws Throwable {
        MethodSignature methodSignature = (MethodSignature) joinPoint.getSignature();
        Method method = methodSignature.getMethod();
        MethodMonitor methodMonitor = method.getAnnotation(MethodMonitor.class);
        Monitor monitor = monitorFactoryInterface.start(methodMonitor.name());
        try {
            return joinPoint.proceed();
        } finally {
            monitor.stop();
            Counter.builder("http.requests.count")
                    .tag("uri", methodMonitor.uri())
                    .tag("method", methodMonitor.method())
                    .register(meterRegistry).increment();
            Gauge
                    .builder("http.response.time", monitor::getLastValue)
                    .tag("uri", methodMonitor.uri())
                    .tag("method", methodMonitor.method())
                    .register(meterRegistry);
        }
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

필요한 지표를 구성했습니다. 이제 서버를 재시작하고, API 엔드포인트를 호출한 후 브라우저에서 /actuator/prometheus를 열어보세요. 여기서 http_requests_count와 http_response_time이라는 이름의 두 가지 지표를 찾을 수 있습니다. Spring Boot는 자동 구성된 HTTP 지표를 제공하며, 이를 비활성화하려면 application.properties에 다음을 추가해주세요.

```js
management.metrics.enable.http.server.requests = false;
```

4. 사용자 정의 헬스 지표를 만드세요.

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

Spring Boot은 DOWN, OUT_OF_SERVICE, UP, UNKNOWN과 같은 네 가지 다른 상태 코드를 제공합니다. 이 상태 코드는 중요도 순으로 정렬되어 있어요. 클래스 패스에서 발견된 종속성에 따라 Spring Boot는 JmsHealthIndicator, DataSourceHealthIndicator, RedisHealthIndicator 등과 같은 여러 건강 지시자를 자동으로 구성할 수 있습니다. 이를 비활성화할 수도 있어요. 비즈니스 요구에 따라 특정 데이터베이스 테이블의 행 수나 대기열에 있는 메시지 수와 같은 것을 기반으로 사용자 정의 상태를 추가하고 싶을 수 있어요.

건강 지시자를 생성하기 위해 HealthIndicator를 구현하거나 AbstractHealthIndicator를 확장할 수 있습니다:

```java
public class AccountHealthIndicator implements HealthIndicator {

    public final JdbcTemplate jdbcTemplate;

    public AccountHealthIndicator(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    @Override
    public Health health() {
        Long count = jdbcTemplate.queryForObject("select count(*) from account", Long.class);

        if (count >= 1) {
            return Health.up().withDetail("accounts", count).build();
        } else {
            return Health.status("NO_ACCOUNT").withDetail("accounts", count).build();
        }
    }
}
```

여기서는 사용자 정의 상태인 NO_ACCOUNT를 생성합니다. 이를 적용하려면 해당 심각도 수준을 정의해야 합니다.

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
(management.endpoint.health.status.order = NO_ACCOUNT),
  DOWN,
  OUT_OF_SERVICE,
  UP,
  UNKNOWN;
```

이제 새로 만든 Health Indicator를 위해 HealthMetrics를 생성해 보겠습니다. 이를 위해 Gauge 미터를 사용할 것입니다:

```js
public class HealthMetrics {

    private final AccountHealthIndicator accountHealthIndicator;
    private final MeterRegistry meterRegistry;

    public HealthMetrics(AccountHealthIndicator accountHealthIndicator, MeterRegistry meterRegistry) {
        this.accountHealthIndicator = accountHealthIndicator;
        this.meterRegistry = meterRegistry;
    }


    @Scheduled(fixedRate = 15000, initialDelay = 0)
    public void reportHealth() {
        Gauge
                .builder("application.health",
                        () -> getStatus(accountHealthIndicator.getHealth(true).getStatus())
                )
                .register(meterRegistry);
    }

    private int getStatus(Status status) {
       return switch (status.getCode()) {
            case "NO_ACCOUNT" -> 0;
            case "DOWN" -> 1;
            case "OUT_OF_SERVICE" -> 2;
            case "UP" -> 3;
            default -> -1;
        };
    }
}
```

이제 애플리케이션을 다시 시작하면 새로 생성된 메트릭인 application_health를 볼 수 있습니다.

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

프로메테우스를 자주 간격으로 메트릭을 스크래핑하도록 설정하기

프로메테우스는 모니터링 및 경보 기술로 오픈 소스입니다. 프로메테우스를 설치하기 위해 다음 단계를 따를 수 있습니다. 기본적으로 프로메테우스는 prometheus.yml을 기본 구성 파일로 사용합니다. 아래와 같이 파일을 편집하세요:

![이미지](/assets/img/2024-06-19-CustommicrometermetricsinSpringBootandscrapeandvisualiseusingPrometheusandGrafana_0.png)

여기에서 대부분의 것을 기본값으로 유지했습니다. 스크래핑 구성 아래에서 작업 이름과 대상을 업데이트했습니다. 대상은 우리의 스프링 부트 어플리케이션입니다. 프로메테우스 서버를 시작하려면 아래 명령을 사용할 수 있습니다:

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
./prometheus --web.listen-address=호스트:포트
```

상태로 이동 - `targets`. 활성화된 모든 엔드포인트를 확인할 수 있습니다:

![2024-06-19-CustommicrometermetricsinSpringBootandscrapeandvisualiseusingPrometheusandGrafana_1.png](/assets/img/2024-06-19-CustommicrometermetricsinSpringBootandscrapeandvisualiseusingPrometheusandGrafana_1.png)

실시간으로 메트릭을 시각화하는 Grafana 대시보드를 설정합니다.

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

로컬 시스템에 Grafana를 설치하려면 이 링크를 사용하십시오. Grafana가 작동 중이면 데이터 소스를 구성합니다. 홈 `연결` `데이터 소스` `새 데이터 소스 추가로 이동합니다. Prometheus를 선택하고 이전 단계에서 설정한 Prometheus 서버 URL을 입력하십시오. 이제 홈 `대시보드` `새 대시보드`로 이동하여 시각화를 추가해보세요. 대시보드에 추가할 시각화를 위해 다음 PromQL 쿼리를 공유할게요:

(1) 상태 (Stat) - `application_health'application="YOGENDRA"`을 추가하고 아래 값 매핑을 추가합니다.

![이미지](/assets/img/2024-06-19-CustommicrometermetricsinSpringBootandscrapeandvisualiseusingPrometheusandGrafana_2.png)

(2) 시작 시간 (Stat) - `process_start_time_seconds'application="YOGENDRA"'\*1000

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

(3) Uptime (Stat) - `process_uptime_seconds' application="YOGENDRA"

(4) Heap utilization (Gauge) - `sum(jvm_memory_used_bytes' application="YOGENDRA", area="heap")\*100 / sum(jvm_memory_max_bytes' application="YOGENDRA", area="heap")'

(5) CPU utilization (Time series)

- A - `system_cpu_usage' application="YOGENDRA"
- B - `process_cpu_usage' application="YOGENDRA"

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

(6) HTTP 응답 시간 (시계열) - `http_response_time 'application=”YOGENDRA”, uri!=”_/actuator/_”' 여기서는 HTTP 호출이 발생한 지점만 보여줄 수 있도록 데이터를 변환합니다. 따라서 데이터 변환으로 이동하고 Group by를 선택합니다. 시간에서 Calculate를 선택하고 Stat First을 선택합니다\*:

![이미지](/assets/img/2024-06-19-CustommicrometermetricsinSpringBootandscrapeandvisualiseusingPrometheusandGrafana_3.png)

(7) HTTP 요청 횟수 (시계열) - `http_requests_count_total'application=”YOGENDRA”, uri!=”_/actuator/_”' 여기서도 reduce 함수를 사용하여 데이터를 변환합니다. 변환은 다음과 같이 보입니다:

![이미지](/assets/img/2024-06-19-CustommicrometermetricsinSpringBootandscrapeandvisualiseusingPrometheusandGrafana_4.png)

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

애플리케이션 코드 링크를 [여기](https://github.com/your-repository)에서 찾을 수 있어요.
