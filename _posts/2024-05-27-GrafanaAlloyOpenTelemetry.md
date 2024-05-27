---
title: "Grafana Alloy OpenTelemetry"
description: ""
coverImage: "/assets/img/2024-05-27-GrafanaAlloyOpenTelemetry_0.png"
date: 2024-05-27 17:29
ogImage: 
  url: /assets/img/2024-05-27-GrafanaAlloyOpenTelemetry_0.png
tag: Tech
originalTitle: "Grafana Alloy , OpenTelemetry"
link: "https://medium.com/@magstherdev/grafana-alloy-opentelemetry-59c171d2ebfc"
---


## Alloy에 인사를 전해보세요

## 소개

Grafana 에이전트가 최근에 Alloy로 전환되었습니다. 이 게시물에서는 이에 대해 안내해 드리겠습니다. 대부분의 예시는 에이전트에서 벤더의 백엔드로 직접 텔레미터 데이터를 보내는 것을 보여줍니다. 그러나 OpenTelemetry는 Collector라는 흥미로운 구성 요소를 소개했습니다.

OpenTelemetry Collector를 사용하면 사용자는 데이터를 효과적으로 제어하여 변형, 보완 및 벤더 중립적인 대상 결정을 할 수 있습니다.

<div class="content-ad"></div>

저번 포스트인 "OpenTelemetry as a Service"를 참고해보세요. 그곳에서 중앙 OpenTelemetry 수집기(게이트웨이)를 설정하는 더 많은 정보를 얻을 수 있습니다.

## Grafana Alloy가 무엇이며, 사용해야 하는 이유는 무엇인가요?

Grafana Alloy는 OpenTelemetry (OTEL) 에이전트로 작동하여 다양한 소스에서 텔레메트리 데이터를 수집하고 모니터링 시스템으로 전송하여 분석 및 시각화하는 기능을 제공합니다.

Alloy는 OpenTelemetry 수집기나 Grafana 에이전트와 같은 데이터 수집 구성 요소를 대체합니다. 하지만 Mimir, Tempo, 또는 Loki와 같은 도구를 대체하지는 않습니다.

<div class="content-ad"></div>

예를 들어, Grafana에서 로그, 메트릭 및 추적 데이터를 소화하고 표시하려는 경우, Loki, Mimir 및 Tempo를 데이터 원본으로 설정하고 Alloy를 배포하여 데이터를 스크랩하고 전송할 수 있습니다.

이 방법을 통해 한 구성 요소로 여러 목적을 위한 소화를 관리할 수 있습니다. 이를 통해 Alloy는 다양한 모니터링 요구 사항을 충족할 수 있는 다재다능한 도구가 됩니다.

## Grafana Alloy의 Walkthrough

## Prerequisites

<div class="content-ad"></div>

시작하기 전에 Kubernetes 클러스터를 설정해 두세요. 다음 명령을 실행하여 새로운 로컬 Kubernetes 클러스터를 만들 수 있어요: kind create cluster

그리고 무료 Grafana Cloud 계정이 필요해요. Grafana Cloud는 Grafana, Mimir, Loki, Tempo와 같은 오픈 소스 프로젝트로 구축된 완전히 관리되는 클라우드 관측성 플랫폼이에요. 아직 가입하지 않았다면 무료 계정으로 가입하세요. 여기서 할 작업에 대해 충분히 제공해 줄 거에요.

필요한 저장소를 추가하세요:

```js
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

<div class="content-ad"></div>

## 환경 설정 설정

Grafana Alloy를 Grafana Cloud에서 Kubernetes 클러스터에 구성하는 두 가지 대안이 있습니다. 간단한 방법부터 시작하여 둘 다 자세히 설명하겠습니다.

## 대안 1 (가장 쉬운 방법)

Grafana Cloud에서 Kubernetes 클러스터용 Grafana Alloy를 구성하려면 다음 단계를 따라주십시오:

<div class="content-ad"></div>

- Grafana Cloud에서 인프라스트럭처 `Kubernetes` 구성으로 이동합니다.
- 활성화하려는 기능을 선택합니다.

- 메트릭: 이 옵션은 Kubernetes 클러스터 인프라 메트릭을 수집하여 Grafana Cloud Prometheus로 전송합니다.
- 비용 메트릭: 비용 메트릭을 스크랩하고 Grafana Cloud Prometheus로 전송하려면 이 기능을 활성화하세요.
- 클러스터 이벤트: Kubernetes 클러스터 이벤트를 캡처하여 Grafana Cloud Loki로 전송합니다.
- Pod 로그: Pod 로그를 캡처하고 Grafana Cloud Loki로 전송합니다.
- OTLP 수신기: Grafana Alloy를 구성하여 OTLP/gRPC 및 OTLP/HTTP를 통해 OpenTelemetry 데이터를 수신합니다.

3. 구성 프로세스를 계속합니다.

그러나 데이터를 별도의 OpenTelemetry Collector로 전송하려면 대체 2를 계속하세요.

<div class="content-ad"></div>

## 대안 2 (권장하는 방법)

이 방법은 구성 프로세스에 대한 더 많은 유연성과 제어를 제공합니다.

- 먼저 헬름 값 파일을 준비하세요.
- 요구 사항에 따라 헬름 값 파일을 구성하고, 'OTLP endpoint', 'your_endpoint', 'my_username', 'my_secret_password'를 실제 값으로 교체해야 합니다. 또한, OTLP를 HTTP를 통해 사용하는 경우 서버 프로토콜이 otlphttp로 설정되어 있는지 확인해주세요.

```js
host: <OTLP 엔드포인트>
writeEndpoint: /<your_endpoint>
protocol: otlphttp
basicAuth:
  username: <my_username>
  password: <my_secret_password>
```

<div class="content-ad"></div>

3. 쿠버네티스 클러스터에 Helm 차트를 배포하세요.

```js
helm repo add grafana https://grafana.github.io/helm-charts &&
  helm repo update &&
  helm upgrade --install --atomic --timeout 300s grafana-k8s-monitoring grafana/k8s-monitoring \
    --namespace "default" --create-namespace --values - <<EOF
```

## 배포 검증

kubectl get all -n default을 사용하여 배포가 성공적으로 이루어졌는지 확인하세요.

<div class="content-ad"></div>

```yaml
PODS

NAME                                                        READY   STATUS    RESTARTS   AGE
grafana-k8s-monitoring-alloy-0                              2/2     Running   0          7m29s
grafana-k8s-monitoring-alloy-events-6fc5d58d6f-k4fz9        2/2     Running   0          7m29s
grafana-k8s-monitoring-alloy-logs-x4chm                     2/2     Running   0          7m29s
grafana-k8s-monitoring-kube-state-metrics-f995ccbbc-7gdkh   1/1     Running   0          7m29s
grafana-k8s-monitoring-opencost-6659b44b6f-dn8ck            1/1     Running   0          7m29s
grafana-k8s-monitoring-prometheus-node-exporter-g2ddm       1/1     Running   0          7m29s

DEPLOYMENT
NAME                                        READY   UP-TO-DATE   AVAILABLE   AGE
grafana-k8s-monitoring-alloy-events         1/1     1            1           7m46s
grafana-k8s-monitoring-kube-state-metrics   1/1     1            1           7m46s
grafana-k8s-monitoring-opencost             1/1     1            1           7m46s

SERVICES
NAME                                              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                                                                               AGE
grafana-k8s-monitoring-alloy                      ClusterIP   10.96.55.19     <none>        12345/TCP,4317/TCP,4318/TCP,9999/TCP,14250/TCP,6832/TCP,6831/TCP,14268/TCP,9411/TCP   8m7s
grafana-k8s-monitoring-alloy-cluster              ClusterIP   None            <none>        12345/TCP,4317/TCP,4318/TCP,9999/TCP,14250/TCP,6832/TCP,6831/TCP,14268/TCP,9411/TCP   8m7s
grafana-k8s-monitoring-alloy-events               ClusterIP   10.96.222.205   <none>        12345/TCP                                                                             8m7s
grafana-k8s-monitoring-alloy-logs                 ClusterIP   10.96.68.158    <none>        12345/TCP                                                                             8m7s
grafana-k8s-monitoring-grafana-agent              ClusterIP   10.96.207.204   <none>        12345/TCP,4317/TCP,4318/TCP,9999/TCP,14250/TCP,6832/TCP,6831/TCP,14268/TCP,9411/TCP   8m7s
grafana-k8s-monitoring-kube-state-metrics         ClusterIP   10.96.168.190   <none>        8080/TCP                                                                              8m7s
grafana-k8s-monitoring-opencost                   ClusterIP   10.96.55.101    <none>        9003/TCP                                                                              8m7s
grafana-k8s-monitoring-prometheus-node-exporter   ClusterIP   10.96.162.70    <none>        9100/TCP

DAEMONSET
NAME                                              DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
grafana-k8s-monitoring-alloy-logs                 1         1         1       1            1           kubernetes.io/os=linux   8m52s
grafana-k8s-monitoring-prometheus-node-exporter   1         1         1       1            1           kubernetes.io/os=linux   8m52s

CONFIGMAPS
NAME                                  DATA   AGE
grafana-k8s-monitoring-alloy          1      9m4s
grafana-k8s-monitoring-alloy-events   1      9m4s
grafana-k8s-monitoring-alloy-logs     1      9m4s
kube-root-ca.crt                      1      121m
kubernetes-monitoring-telemetry       1      9m4s
```

## Inspecting Grafana Alloy

To inspect the Grafana Alloy configuration, run:

```yaml
kubectl get cm grafana-k8s-monitoring-alloy -o yaml
```

<div class="content-ad"></div>

여기에서 OpenTelemetry Collector 내에서 사용되는 수신기, 프로세서, 커넥터 및 익스포터 구성 세부 정보를 살펴볼 수 있습니다.

## 발견

Grafana Alloy가 먼저 다양한 Kubernetes 리소스에 대한 Kubernetes 발견 설정을 설정합니다. 노드, 서비스, 엔드포인트 및 파드와 같은 Kubernetes 리소스를 시각화, 모니터링 및 분석에 활용할 수 있습니다.

```js
     discovery.kubernetes "nodes" {
       role = "node"
     }

     discovery.kubernetes "services" {
       role = "service"
     }

     discovery.kubernetes "endpoints" {
       role = "endpoints"
     }

     discovery.kubernetes "pods" {
       role = "pod"
     }
```

<div class="content-ad"></div>

## OTLP 수신기

수신기는 gRPC 및 HTTP를 통해 OpenTelemetry 프로토콜 (OTLP)을 사용하여 텔레미터 데이터를 수집하도록 설정됩니다.

```js
     // OTLP Receivers
     otelcol.receiver.otlp "receiver" {
       debug_metrics {
         disable_high_cardinality_metrics = true
       }

       grpc {
         endpoint = "0.0.0.0:4317"
       }

       http {
         endpoint = "0.0.0.0:4318"
       }
       output {
         metrics = [otelcol.processor.resourcedetection.default.input]
         logs = [otelcol.processor.resourcedetection.default.input]
         traces = [otelcol.processor.resourcedetection.default.input]
       }
     }
```

output 블록은 메트릭, 로그 및 추적이 otelcol.processor.resourcedetection.default.input이라는 프로세서로 전송되어야 함을 나타냅니다.

<div class="content-ad"></div>

## 처리기

처리기는 텔레메트리 데이터를 변환하고 향상시키기 위해 구성되어 있으며, 모니터링 인프라의 가시성을 향상시키는 데 사용할 수 있습니다. 처리기는 OpenTelemetry Collector 내에서 텔레메트리 데이터를 변환, 감지, 추출 및 필터링하는 데 중요한 역할을 합니다.

예를 들어, 사용되고 있는 처리기는 다음과 같습니다.

```js
otelcol.processor.transform         # 속성을 추가하여 메트릭 데이터를 변환합니다.
otelcol.processor.resourcedetection # 환경에서 리소스를 감지합니다.
otelcol.processor.k8sattributes     # Kubernetes 관련 메타데이터를 추출합니다.
otelcol.connector.host_info         # 호스트 정보를 수집합니다.
otelcol.processor.batch             # 데이터 처리를 일괄로 처리합니다.
otelcol.processor.filter            # 데이터를 필터링합니다.
```

<div class="content-ad"></div>

## Exporters

수출기는 외부 시스템으로 텔레메트리 데이터를 내보내기 위해 정의됩니다. 예를 들어, 메트릭을 프로메테우스로 내보내기, 로그를 로키로 내보내기, 그리고 Tempo로 추적을 내보내는 방식입니다.

## 주석 자동 탐지

주석을 기반으로 동적으로 포드 및 서비스에서 메트릭을 발견하고 스크랩하기 위해 규칙이 정의되어 있습니다.

<div class="content-ad"></div>

## 스크래핑 구성

구성은 Kubernetes, cAdvisor, Kubelet 및 Node Exporter와 같은 다양한 구성 요소에서 메트릭을 스크랩하는 데 설정됩니다.

## 라벨 재설정

규칙은 메트릭 및 로그에 부착된 레이블을 수정하기 위해 정의됩니다. 예를 들어 레이블 이름 바꾸기, 불필요한 레이블 삭제 및 특정 메트릭 필터링 등이 포함됩니다.

<div class="content-ad"></div>

## 인증

기본 인증은 수출 업체 및 필요한 다른 구성 요소에 구성되어 있습니다.

구성 파일을 이해하기 위해 구성 맵을 확인하는 것을 권장합니다. 이는 완전한 세부 정보와 구성을 제공합니다.

요구 사항에 맞게 Grafana Alloy 설정을 검토하고 구성한 후 UI로 돌아가서 모든 것이 예상대로 작동하는지 확인하세요.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-27-GrafanaAlloyOpenTelemetry_0.png" />

## 모니터링 컴포넌트 탐색

## 클러스터 탐색

Grafana에서 제공하는 기본 클러스터 탐색 기능을 활용하여 Kubernetes 클러스터에 대한 유용한 통찰을 얻어보세요.

<div class="content-ad"></div>

```markdown
![GrafanaAlloyOpenTelemetry_1](/assets/img/2024-05-27-GrafanaAlloyOpenTelemetry_1.png)
![GrafanaAlloyOpenTelemetry_2](/assets/img/2024-05-27-GrafanaAlloyOpenTelemetry_2.png)
![GrafanaAlloyOpenTelemetry_3](/assets/img/2024-05-27-GrafanaAlloyOpenTelemetry_3.png)
![GrafanaAlloyOpenTelemetry_4](/assets/img/2024-05-27-GrafanaAlloyOpenTelemetry_4.png)
```

<div class="content-ad"></div>

## 비용 모니터링

Grafana의 비용 개요 페이지를 활용하여 클라우드 비용을 모니터링하세요. 효율적인 자원 관리에 중요합니다.

## 알림

중요한 이벤트에 대한 예방적인 모니터링과 즉각적인 대응을 위해 경보 규칙 및 기록 규칙을 설정하세요.

<div class="content-ad"></div>

## Cardinality

이 게시물은 카디널리티에 집중한 것은 아니지만, 중요한 부분이기 때문에 언급해야 합니다. 카디널리티를 모니터링하면 데이터의 고유성을 이해할 수 있어 성능 및 자원 이용을 최적화하는 데 중요합니다. 고카디널리티는 많은 고유값을 가진 데이터 세트를 가리킵니다.

Grafana Cloud의 적응형 메트릭을 사용하여 증가한 카디널리티 문제를 해결할 수 있습니다.

![이미지](/assets/img/2024-05-27-GrafanaAlloyOpenTelemetry_5.png)

<div class="content-ad"></div>

## 애플리케이션 기기 설정

이제 우리는 우리 애플리케이션들에게 Grafana Alloy로 텔레미터 데이터를 보내도록 지시해야 합니다. http://grafana-k8s-monitoring-grafana-agent.default.svc.cluster.local:4318

맞아요, 이 로컬 OpenTelemetry 에이전트는 클러스터와 애플리케이션으로부터 데이터를 수신하는 중개자 역할을 합니다.

## 메트릭과 로그

<div class="content-ad"></div>

이제 우리의 메트릭과 로그가 Kubernetes 클러스터에서 Grafana Alloy로 흘러들어가고, 중앙 OpenTelemetry Collector를 거쳐 Grafana Cloud에서 시각화됩니다.

이 데이터에 액세스하는 것은 쉽습니다. 클러스터 내비게이션을 통해 이동하거나 Explore 도구를 사용할 수 있습니다. 제 개인적으로는 다양한 소스에서 텔레메트리 데이터를 탐색할 수 있는 Explore 도구를 선호합니다. 이 강력한 도구를 사용하면 쿼리를 작성하고 필터 및 변환을 적용하며 가치 있는 통찰을 효율적으로 추출할 수 있습니다.

## 추적

그렇다면 추적은 어떨까요? 추적은 사용자 요청이 응용 프로그램을 통해 이동하는 과정에 대한 중요한 통찰을 제공합니다. 추적을 활용하려면 데이터를 Grafana Alloy로 보내도록 설정된 계기가 설치된 응용 프로그램이 필요합니다. 이를 위해 응용 프로그램이 다음 주소 중 하나로 데이터를 보내도록 설정해야 합니다:

<div class="content-ad"></div>

- OTLP/gRPC 엔드포인트: http://grafana-k8s-monitoring-grafana-agent.default.svc.cluster.local:4317
- OTLP/HTTP 엔드포인트: http://grafana-k8s-monitoring-grafana-agent.default.svc.cluster.local:4318

Spring Boot 애플리케이션에서는 다음 주소를 OTEL_EXPORTER_OTLP_ENDPOINT 환경 변수로 설정할 수 있습니다:

```js
- name: OTEL_EXPORTER_OTLP_ENDPOINT
  value: http://grafana-k8s-monitoring-grafana-agent.default.svc.cluster.local:4317
```

또한 springboot-service에 OTEL_SERVICE_NAME 변수를 추가했습니다.

<div class="content-ad"></div>

```yaml
- name: OTEL_SERVICE_NAME
  value: springboot-service
```

애플리케이션이 배포되고 데이터를 생성하면 Explore 도구에서 추적을 분석할 수 있습니다.

## Tempo에서 추적 탐색

Tempo로 추적을 탐색해 봅시다. Tempo는 오픈 소스 분산 추적 시스템으로, 분산 시스템 간 요청 흐름에 대한 가시성을 제공합니다.

<div class="content-ad"></div>

분산 시스템에서 추적(trace)은 단일 사용자 요청이 다양한 서비스를 거쳐 이동하는 여정을 나타내는 데이터 집합입니다. 이 여정의 각 단계를 스팬(span)이라고 하며, 서비스 이름, 수행된 작업, 소요된 시간 등의 정보가 포함됩니다. 추적 및 스팬을 통해 개발자는 복잡하고 연결된 환경에서 애플리케이션의 성능 및 동작을 이해하는 데 도움을 받습니다.

Explore 도구에서 grafanacloud-`name`-traces 데이터 소스를 선택합니다. 서비스 이름 드롭다운 목록에서 서비스를 선택하고(예: springboot-service), 추적을 찾아봅니다. 자세한 통찰을 얻으려면 어떤 추적이든 클릭하세요.

<img src="/assets/img/2024-05-27-GrafanaAlloyOpenTelemetry_6.png" />

자세한 통찰을 얻으려면 어떤 추적이든 클릭하세요.

<div class="content-ad"></div>

![이미지](/assets/img/2024-05-27-GrafanaAlloyOpenTelemetry_7.png)

저희는 서비스, 응용 프로그램 및 인프라에 대한 탁월한 관측성을 위해 스스로를 성공적으로 설정했습니다. 하지만 더 많은 가능성이 있습니다! 다음 포스트에서 모든 관측성 요구 사항을 충족시키는 또 다른 Grafana 제품인 애플리케이션 관측성을 알아보겠습니다. 계속 주시해 주세요! 😊

## 결론

이 단계를 따르면 오픈텔레미트리의 강력함을 활용하여 Kubernetes 클러스터에 Grafana Alloy를 성공적으로 배포할 수 있을 것입니다.

<div class="content-ad"></div>

만약 이 내용이 도움이 되었다면 👏 버튼을 클릭하시고 제 프로필을 팔로우해주세요. 더 많은 기사를 확인할 수 있습니다!