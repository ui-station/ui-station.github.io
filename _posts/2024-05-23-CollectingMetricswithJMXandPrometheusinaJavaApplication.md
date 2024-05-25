---
title: "자바 애플리케이션에서 JMX와 프로메테우스로 메트릭 수집하기"
description: ""
coverImage: "/assets/img/2024-05-23-CollectingMetricswithJMXandPrometheusinaJavaApplication_0.png"
date: 2024-05-23 12:38
ogImage: 
  url: /assets/img/2024-05-23-CollectingMetricswithJMXandPrometheusinaJavaApplication_0.png
tag: Tech
originalTitle: "Collecting Metrics with JMX and Prometheus in a Java Application"
link: "https://medium.com/@estevaosaleme/collecting-metrics-with-jmx-and-prometheus-in-a-java-application-f4364b459692"
---


저희 응용 프로그램의 내부 작업, 성능 특성 및 비즈니스 지표를 이해하는 것은 사용자 경험과 비즈니스 상태를 구분짓고, 비즈니스가 어떻게 진행되고 있는지를 나타낼 수 있습니다. 높은 CPU 또는 메모리 사용량, 느린 응답 시간 또는 높은 지연과 같은 성능 문제는 비효율성이나 자원 경합을 지적할 수 있습니다.

하지만 이러한 수치가 사용자에게 영향을 주기 전에 어떻게 감지, 추적 및 관찰할 수 있을까요?

# 소개

우리는 지표를 수집하고 분석함으로써 응용 프로그램의 상태, 동작 및 건강 상태에 대한 통찰력을 얻을 수 있습니다. JMX 및 프로메테우스와 같은 도구를 활용하여 간단한 방식으로 자바 응용 프로그램 지표에 대한 심도있는 가시성을 제공하는 강력한 모니터링 인프라를 구축할 수 있습니다.

<div class="content-ad"></div>

이 글에서는 다음을 배울 것입니다:

- MBeans를 사용한 JMX 계측의 기본 사항.
- jconsole을 사용하여 JMX에 노출된 메트릭을 시각화하는 방법.
- Prometheus가 무엇이며 JMX와 통합하여 대시보드에 메트릭을 노출하는 방법.
- Java Prometheus 에이전트를 구성하고 Prometheus 서버를 실행하는 방법.
- Prometheus를 사용하여 시계열 데이터를 시각화하는 방법.

![이미지](/assets/img/2024-05-23-CollectingMetricswithJMXandPrometheusinaJavaApplication_0.png)

# JMX란 무엇인가요?

<div class="content-ad"></div>

JMX (Java Management Extensions)은 애플리케이션, 시스템 객체, 장치 및 서비스 중심 네트워크를 관리하고 모니터링하는 도구를 제공하는 네이티브 Java 기술입니다. 이러한 자원들은 MBeans(또는 관리되는 빈)이라고 불리는 객체로 나타낼 수 있습니다.

작동 방식은 다음과 같습니다: 하나 이상의 MBeans가 리소스에 의해 계측(instrumented)되며, MBeans는 표준(관리 인터페이스가 메서드 이름으로 설명됨) 또는 동적(런타임에 관리 인터페이스를 노출하여 최대 유연성을 제공) 중 하나일 수 있습니다. 그런 다음 MBeans는 MBean 서버로 알려진 코어-관리되는 객체 서버에 등록되어 레지스트리 역할을 하는데, 각 MBean은 ObjectName에 의해 식별된 MBeanServer 내에서 고유한 식별자를 갖습니다.

이것들은 JMX의 기본 사항이지만 이 기사를 따라갈 정도로 충분합니다. 자세히 살펴보고 싶다면, 오라클의 "Getting Started with Java Management Extensions (JMX): Developing Management and Monitoring Solutions"라는 오래되었지만 상당히 유용한 기술 포스트를 읽어보시기를 권장합니다.

## Mbeans 구현하기

<div class="content-ad"></div>

JMX에 메트릭을 노출하는 MBean 규칙은 다음과 같습니다:

- MBean 접미사를 가진 인터페이스를 정의합니다.
- 이 인터페이스를 클래스에서 구현합니다.
- 구현을 Platform MBean 서버에 ObjectName을 사용하여 등록합니다.

세 가지 작업(step 1)이 있는 간단한 DemoJmxMBean 인터페이스를 정의해봅시다:

```js
public interface DemoJmxMBean {
    void increaseVolume();
    void decreaseVolume();
    int getVolume();
}
```

<div class="content-ad"></div>

다음으로 이 인터페이스를 구현해야 합니다 (단계 2). 다음과 같이 DemoJmx 클래스에서 작업해 봅시다:

```js
public class DemoJmx implements DemoJmxMBean {
    private int volume = 0;
    @Override
    public void increaseVolume() {
        volume++;
    }
    @Override
    public void decreaseVolume() {
        volume--;
    }
    @Override
    public int getVolume() {
        return volume;
    }
...
```

이후, MBean 서버를 초기화하고 사용자 정의 MBean (DemoJmx)의 인스턴스를 생성한 다음, main 메소드 내에서 고유한 ObjectName을 사용하여 MBean 서버에 등록해야 합니다 (단계 3). 이 설정을 통해 JMX를 통해 DemoJmx 인스턴스를 관리하고 모니터링할 수 있게 됩니다. 이 경우, 볼륨을 관리하고 수집할 수 있습니다.

```js
...
// 모든 MBean을 위한 레지스트리인 플랫폼 MBeanServer를 검색합니다
MBeanServer mbs = ManagementFactory.getPlatformMBeanServer();
// MBean 구현의 인스턴스를 생성합니다
DemoJmx demoService = new DemoJmx();
ObjectName name;
try {
    // 특정 네이밍 패턴을 사용하여 새 ObjectName 인스턴스를 생성합니다
    name = new ObjectName("com.example.DemoJmx:type=DemoJmxMBean,name=DemoJmxMetrics");
    // ObjectName을 사용하여 MBean 인스턴스 (demoService)를 MBeanServer에 등록합니다
    // 이렇게 하면 JMX를 통해 MBean을 관리하고 모니터링할 수 있습니다
    mbs.registerMBean(demoService, name);
...

<div class="content-ad"></div>

시간을 절약하기 위해 전체 코드는 제 Github에서 확인할 수 있어요. 원하는 IDE를 선택하고 이 기사를 따라해보세요.

## 노출된 MBean 시각화

한 번 MBean이 등록되면, 우리는 JMX 클라이언트(예: JConsole)를 사용하여 메트릭에 액세스할 수 있어요. 이 클라이언트는 JVM에 연결되어 등록된 MBeans를 탐색하고 상호 작용할 수 있게 해줍니다 (그림 1).

<img src="/assets/img/2024-05-23-CollectingMetricswithJMXandPrometheusinaJavaApplication_1.png" />

<div class="content-ad"></div>

만약 나처럼 JConsole을 사용 중이라면 MBeans 탭으로 이동하면 DemoJmxMetrics라는 우리의 MBeans가 있을 것입니다 (그림 2).

![DemoJmxMetrics](/assets/img/2024-05-23-CollectingMetricswithJMXandPrometheusinaJavaApplication_2.png)

JConsole UI에 노출된 volume 속성을 확인할 수 있습니다. 그러나 이는 해당 속성의 현재 상태입니다. 만약 우리가 이 속성의 볼륨을 시계열 그래프를 통해 보고 싶다면 어떻게 할까요? 이 임무를 위해 우리는 다른 도구 — Prometheus가 필요합니다. 다음에 소개됩니다.

# Prometheus란 무엇인가요?

<div class="content-ad"></div>

프로메테우스는 신뢰성과 확장성을 위해 설계된 오픈 소스 시스템 모니터링 및 경고 도구입니다. 특히 동적 클라우드 환경, 컨테이너화된 응용프로그램 및 마이크로서비스 아키텍처의 모니터링에 적합합니다. 프로메테우스는 Cloud Native Computing Foundation (CNCF)의 일부입니다. 이 어떻게 우리를 도울까요?

프로메테우스는 JMX 메트릭을 수집하고 http 엔드포인트를 통해 노출시킵니다. 이 작업을 위해 일반적으로 프로메테우스 JMX 내보내기 도구를 사용합니다. 이 도구는 JMX 메트릭과 프로메테우스 사이의 다리 역할을 하며, 프로메테우스가 다른 메트릭 엔드포인트와 마찬가지로 JMX 메트릭을 수집할 수 있도록 합니다.

이제 프로메테우스가 무엇이며 JMX 속성(또는 메트릭)을 내보내기하는 데 사용할 수 있는 것을 알았으니, 다시 손을 더럽히기 시작해봅시다.

## 프로메테우스 JMX 내보내기로 JMX 메트릭 노출하기

<div class="content-ad"></div>

단순해요. 먼저 GitHub 릴리스 페이지에서 최신 jmx_prometheus_javaagent-`버전`.jar 파일을 다운로드하세요.

그런 다음 config.yaml(다른 이름일 수도 있음)이라는 설정 파일을 생성하여 JMX Exporter를 구성하세요. 이 파일은 수집할 JMX 메트릭과 Prometheus 메트릭으로의 매핑을 정의할 것입니다.

다음은 promiscuous(넓은 범위) 설정 파일 예시입니다 (수집하려는 항목만 남기도록 필터링할 수 있습니다):

```js
rules:
- pattern: ".*"
```

<div class="content-ad"></div>

프로메테우스에 메트릭 유형을 지정하려면 config.yaml에서해야 합니다. 이것은 JMX 익스포터 설명서에 설명되어 있습니다.

다음으로, IntelliJ를 사용 중이라면 Figure 3처럼 Java 애플리케이션의 시작 매개변수로 JMX 익스포터를 Java 에이전트로 추가해야 합니다.

![Figure 3](/assets/img/2024-05-23-CollectingMetricswithJMXandPrometheusinaJavaApplication_3.png)

위와 동일합니다.

<div class="content-ad"></div>

```markdown
java -javaagent:./../jmx_prometheus_javaagent-0.20.0.jar=8080:./../config.yaml -classpath mbeans-example/target/classes com.example.DemoJmx
```

이전에 다운로드한 파일의 경로(jmx_prometheus_javaagent-`버전`.jar 및 config.yaml)를 사용함을 기억해주세요.

이제 우리는 프로메테우스 에이전트가 게시한 메트릭에 /metrics 엔드포인트(http://localhost:8080/metrics)를 통해 액세스할 수 있어야 합니다. 도표 4에서 볼 수 있듯이, 자세히 살펴보면 DemoJmxMetrics 객체와 속성 volume을 확인할 수 있습니다.

![이미지](/assets/img/2024-05-23-CollectingMetricswithJMXandPrometheusinaJavaApplication_4.png)
```

<div class="content-ad"></div>

이제 데이터가 캡처되는 것을 확인했지만, 이는 확실히 시각화하기에 최적의 방법은 아닙니다. 다음에는 이에 대해 처리해 봅시다.

## 그래프를 통한 데이터 시각화

먼저, JMX 익스포터에서 메트릭을 수집하기 위해 프로메테우스 설정 파일 (prometheus.yml)을 가져옵니다. 그림 5는 이 시점까지의 디렉토리 구조를 보여줌으로써 우리가 길을 잃지 않도록 도와줍니다.

![Figure 5](/assets/img/2024-05-23-CollectingMetricswithJMXandPrometheusinaJavaApplication_5.png)

<div class="content-ad"></div>

그럼, 새로운 작업을 추가하여 prometheus.yml 구성 파일을 업데이트합니다:

```js
...
- job_name: 'jmx-exporter'
  static_configs:
    - targets: ['<당신의 IP 주소>:8080']
```

`your IP address`는 우리의 Java 어플리케이션이 실행 중인 네트워크 인터페이스의 IP로 대체되어야 합니다.

마지막으로, prometheus.yml 파일을 위한 볼륨을 만들어 컨테이너 이미지를 사용하여 Prometheus 서버를 실행합니다:

<div class="content-ad"></div>

```markdown
docker run \
    -p 9090:9090 \
    -v ./prometheus.yml:/etc/prometheus/prometheus.yml \
    prom/prometheus
```

모든 것이 잘 되면 콘솔에 다음 메시지가 표시될 것입니다: "서버는 웹 요청을 받을 준비가 되었습니다."

그런 다음, Prometheus 서버 UI 엔드포인트인 http://localhost:9090 으로 이동합니다 (그림 6). 우리의 애플리케이션에서 메트릭이 스크랩되고 있는지 확인하기 위해 상태 메뉴로 이동한 다음 타겟을 선택합니다.

![이미지](/assets/img/2024-05-23-CollectingMetricswithJMXandPrometheusinaJavaApplication_6.png)
```

<div class="content-ad"></div>

볼륨 측정치의 변화를 시각화하기 위해 JConsole 인터페이스를 사용하여 볼륨을 증가하거나 감소시킬 수 있습니다 (그림 7).

![Figure 7](/assets/img/2024-05-23-CollectingMetricswithJMXandPrometheusinaJavaApplication_7.png)

increaseVolume() 또는 decreaseVolume()을 클릭한 후에는 볼륨 속성이 그에 맞게 변경되며 이러한 값은 Prometheus에 업데이트될 것입니다.

그래프를 시각화하기 위해 Graph 메뉴로 이동하여 원하는 메트릭을 검색할 수 있습니다. 그림 8에서는 볼륨이라는 데모용으로 생성한 메트릭을 찾고 있습니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-CollectingMetricswithJMXandPrometheusinaJavaApplication_8.png" />

마지막이에요! 프로메테우스 서버가 응용 프로그램을 스크래핑하는 시간 주기를 구성할 수 있고, 다른 여러 옵션들을 파라미터화할 수도 있지만 이 부분은 당신의 요구에 따라 결정됩니다. 학습 목적으로는 충분할 것입니다.

# 최종 고려 사항

이 기사에서는 MBean 규약을 따르는 방법을 배웠습니다. 또한, 시계열 방식으로 이러한 메트릭을 시각화하기 위해 프로메테우스 서버를 설정했습니다. 프로메테우스가 노출하는 데이터를 시각화하기 위해 Grafana와 같은 다른 도구를 사용할 수 있음에 유의하십시오. Grafana는 대시보드를 생성, 탐색 및 공유하는 데 풍부한 기능 세트를 제공하는 도구입니다.

<div class="content-ad"></div>

기타 유의할 점은 Kafka, RabbitMQ, ActiveMQ 등을 포함한 많은 인기있는 Java 기반 라이브러리들이 기본적으로 JMX(metrics)를 제공한다는 것입니다. 이러한 메트릭들은 여기에서 배운 방법과 마찬가지로 노출될 수 있습니다. 따라서 이러한 MBeans를 활용하여 사용 가능한 메트릭을 찾고 응용 프로그램에서 간편하게 모니터링할 수 있습니다.

마지막으로, 응용 프로그램 내에서 비즈니스 메트릭을 생성하고 수집하는 데도 이 같은 패턴을 적용할 수 있다는 점을 강조해야 합니다. 이 접근 방식은 제품 팀과 다른 이해관계자들을 위한 매우 관련성 높은 대시보드를 만들어주어 가치 있는 통찰력을 제공하고 정보에 근거한 의사 결정을 도울 수 있습니다.