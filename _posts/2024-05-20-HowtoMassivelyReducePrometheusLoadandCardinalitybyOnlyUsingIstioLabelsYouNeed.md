---
title: "프로메테우스 부하와 카디널리티 대폭 줄이는 방법 필요한 이스티오 레이블만 사용하기"
description: ""
coverImage: "/assets/img/2024-05-20-HowtoMassivelyReducePrometheusLoadandCardinalitybyOnlyUsingIstioLabelsYouNeed_0.png"
date: 2024-05-20 17:24
ogImage: 
  url: /assets/img/2024-05-20-HowtoMassivelyReducePrometheusLoadandCardinalitybyOnlyUsingIstioLabelsYouNeed_0.png
tag: Tech
originalTitle: "How to Massively Reduce Prometheus Load and Cardinality by Only Using Istio Labels You Need"
link: "https://medium.com/itnext/how-to-massively-reduce-prometheus-load-and-cardinality-by-only-using-istio-labels-you-need-75bcf41ff5d3"
---


![image](/assets/img/2024-05-20-HowtoMassivelyReducePrometheusLoadandCardinalitybyOnlyUsingIstioLabelsYouNeed_0.png)

만약 프로메테우스 작업에 노출되어 있다면, 카디널리티 관리가 중요하다는 것을 들어봤을 것입니다. 이것은 관측 구성의 가장 중요한 측면 중 하나이며 시스템 부하를 크게 증가시킬 수 있습니다. Robust Perception에는 왜 이것이 중요한지에 대해 좋은 설명이 있으므로, 왜 이것이 중요한지에 대해 자세히 다루지는 않겠지만, 가장 비용이 많이 드는 메트릭 중 일부를 다룰 수 있도록 메트릭을 조정하는 한 가지 방법을 설명하겠습니다.

구체적으로 이스티오에 대해 이야기할 것입니다. 현재 우리는 v1.18을 사용 중이므로, 참고하셔야 할 필드 중 일부는 이전 버전에서 (또는 신규한 버전에서) 다르게 보일 수 있음을 주의해 주세요. 이 버전에서 예제를 정확히 찾는 데 어려움을 겪어 예제가 유용하게 될 것을 희망합니다.

# 재미있는 부분으로 넘어가보겠습니다.

<div class="content-ad"></div>

우선, 우리가 이야기하는 것은 Istio (그리고 간접적으로 Kubernetes)입니다. 우리가 조정하고 있는 구체적인 구성 요소는 Istio Operator입니다. 
익숙하지 않다면, Istio Operator는 istioOperator 사용자 지정 리소스를 관리하는 Kubernetes 컨트롤러입니다. 
그 결과로 Istio Operator는 클러스터에서 Istio 리소스를 만들고 지속적으로 조정합니다. 
이에는 Gateway 리소스, Envoy Filters, Pilot (istiod) 등이 포함됩니다.

Istio 문서에는 우리가 작업하는 기능을 참조하는데, 사용 방법에 대한 예시를 충분히 제공하지 않는 단 한 줄이 있습니다.

그래서 우리는 설정해야 할 istioOperator 매니페스트가 있습니다. 그리고 metric 라벨을 덮어쓰기 위해 tags_to_remove 옵션을 사용한다는 것을 알고 있습니다.

## 참고: Istio 구성을 업데이트할 때는 항상 실제 운영 환경이 아닌 환경에서 테스트해야 합니다. 
메트릭 라벨에 대해 이야기하고 있더라도 의도치 않은 영향이 발생할 수 있습니다.
예를 들어, 사용자 지정 메트릭을 사용하여 스케일링 동작을 정의하는 경우 전체 클러스터의 스케일링 동작을 망가뜨릴 수 있습니다.
이것은 한 가지 예시일 뿐이지만, 이것을 염두에 두세요.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-20-HowtoMassivelyReducePrometheusLoadandCardinalitybyOnlyUsingIstioLabelsYouNeed_1.png" />

이제, 실제로 이 구성 변경이 어떻게 보이는지는 명확하지 않습니다. 그래서 여기에 있습니다. 원하는 구성은 inboundSidecar 및 outboundSidecar 속성 모두에 대해 설정됩니다. istiooperator 매니페스트에서 전체 경로는 다음과 같습니다:

spec.telemetry.v2.prometheus.configOverride.inboundSidecar.metrics

spec.telemetry.v2.prometheus.configOverride.outboundSidecar.metrics

<div class="content-ad"></div>

아래와 같이 더 완전한 예시를 살펴보세요 (이 이야기에서 필요한 구성만을 간략화하여 포함하였음을 유의해주세요):

```js
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: ${istio_namespace}
  name: ${istio_name}
spec:
  values:
    telemetry:
      enabled: true
      v2:
        prometheus:
          wasmEnabled: true
          configOverride:
            inboundSidecar:
              metrics:
              - name: requests_total
                tags_to_remove:
                - connection_security_policy
                - destination_cluster
                - destination_canonical_revision
                - destination_canonical_service
                - destination_principal
                - destination_version
                - destination_service_name
                - destination_service_namespace
                - destination_workload_namespace
                - source_canonical_service
                - source_canonical_revision
                - source_workload_namespace
                - source_version
                - source_cluster
                - source_principal
              - name: request_bytes
                tags_to_remove:
                - destination_cluster
                - destination_canonical_revision
                - destination_canonical_service
                - destination_principal
                - destination_version
                - destination_service_name
                - destination_service_namespace
                - destination_workload_namespace
                - source_canonical_service
                - source_canonical_revision
                - source_workload_namespace
                - source_version
                - source_cluster
                - source_principal
                - service
                - pod
```

# 이제 무엇을 해야 할까요?

Istio Operator 구성이 업데이트되었습니다. 멋져요! 하지만… 이제 무엇을 해야 할까요?

<div class="content-ad"></div>

아마도 별도의 운영 환경에 액세스할 수 있고 이 업데이트된 매니페스트를 해당 Kubernetes 클러스터에 적용할 수 있을 것으로 예상됩니다. Istio 오퍼레이터 파드의 로그를 추적하여 클러스터 리소스를 동기화하는 것을 확인할 수 있습니다. 구체적으로, Envoy 필터를 업데이트해야 합니다.

만약 구문 오류가 없다면 로그에 다음과 같은 내용이 표시될 것입니다:

```js
2024-03-18T17:54:21.303088Z info installer 생성된 매니페스트와 캐시 사이에 다음 오브젝트가 다릅니다:
 - ConfigMap:istio-system:istio
 - EnvoyFilter:istio-system:stats-filter-1.16
 - EnvoyFilter:istio-system:tcp-stats-filter-1.16
 - EnvoyFilter:istio-system:stats-filter-1.17
 - EnvoyFilter:istio-system:tcp-stats-filter-1.17
 - EnvoyFilter:istio-system:stats-filter-1.18
 - EnvoyFilter:istio-system:tcp-stats-filter-1.18
2024-03-18T17:54:21.303181Z info installer 서버 측 적용을 사용하여 오브젝트를 업데이트 중: EnvoyFilter/istio-system/stats-filter-1.16
- 제거된 리소스를 가지치기 중
2024-03-18T17:54:21.333325Z info installer 서버 측 적용을 사용하여 오브젝트를 업데이트 중: EnvoyFilter/istio-system/stats-filter-1.17
2024-03-18T17:54:21.352880Z info installer 서버 측 적용을 사용하여 오브젝트를 업데이트 중: EnvoyFilter/istio-system/stats-filter-1.18
2024-03-18T17:54:21.372182Z info installer 서버 측 적용을 사용하여 오브젝트를 업데이트 중: EnvoyFilter/istio-system/tcp-stats-filter-1.16
2024-03-18T17:54:21.392004Z info installer 서버 측 적용을 사용하여 오브젝트를 업데이트 중: EnvoyFilter/istio-system/tcp-stats-filter-1.17
2024-03-18T17:54:21.409610Z info installer 서버 측 적용을 사용하여 오브젝트를 업데이트 중: EnvoyFilter/istio-system/tcp-stats-filter-1.18
2024-03-18T17:54:21.433381Z info installer 서버 측 적용을 사용하여 오브젝트를 업데이트 중: ConfigMap/istio-system/istio
```

전체적으로 너무 조심스러운 면이 있다고 생각되어 istio-system 네임스페이스에 있는 모든 것을 재시작하는 기회를 가졌습니다. 이는 모든 게이트웨이 및 istiod를 포함합니다. 이것이 완전히 필요한지는 모르지만, 제가 한 작업입니다. 변경 사항을 적용한 모든 클러스터에서 트래픽을 제거한 다음 변경을 적용하고 다시 시작했습니다.

<div class="content-ad"></div>

변경 사항을 적용하는 데 한 가지 더: 제 네임 스페이스 중 일부에서 메트릭 레이블 업데이트가 즉시 적용되지 않는 것을 발견했습니다. 이스티오 프록시 컨테이너가 실제로 Envoy 필터에 변경 사항을 적용하기 전에 다시 시작해야 한다는 이유로 이해됩니다. 100% 사실인지는 모르지만, 다시 말하지만 — 제 네임 스페이스 중 일부에서 제 관측 결과였습니다. 이것이 이례적으로 느껴지지는 않지만, 변경 사항을 할 때 주의하고 확인하는 것이 좋습니다.

# 작별 인사와 예시

Kubernetes 클러스터가 서비스로의 매우 많은 요청을 허용한다면, 카디널리티를 줄이는 데 얻게 될 이득은 상당할 것입니다. 레이블을 제대로 검토하는 데 시간이 걸리지만(prometheusRules, 대시보드 등을 확인), 이 작업은 귀중합니다.

고 카디널리티 레이블을 다음과 같이 줄일 때:

<div class="content-ad"></div>


```js
{
    "container":"istio-proxy",
    "destination_canonical_revision":"<dstUID>",
    "destination_canonical_service":"<dstApp>",
    "destination_cluster":"Kubernetes",
    "destination_service":"<dstApp>.<dstApp>-production.svc.cluster.local",
    "destination_service_name":"PassthroughCluster",
    "destination_service_namespace":"<dstApp>-production",
    "destination_version":"<dstUID>",
    "destination_workload":"<dstUID>",
    "destination_workload_namespace":"<dstApp>-production",
    "instance":"<someIP>",
    "job":"<appName>",
    "le":"0.5",
    "namespace":"<appName>-production",
    "pod":"<srcUID>-kkd77",
    "reporter":"source",
    "request_protocol":"http",
    "response_code":"200",
    "response_flags":"-",
    "service":"<appName>",
    "source_app":"<appName>",
    "source_canonical_revision":"<srcUID>",
    "source_canonical_service":"<appName}",
    "source_cluster":"Kubernetes",
    "tag":"<srcUID>"
}
```

위를 아래의 형식으로 변경해주세요.

```js
{
    "reporter": "destination",
    "source_workload": "<srcUID>",
    "source_app": "<srcApp>",
    "destination_workload": "<dstUID>",
    "destination_app": "<dstApp>",
    "destination_service": "<dstApp>.<dstApp>-production.svc.cluster.local",
    "request_protocol": "http",
    "job":"<appName>",
    "response_code": "200",
    "grpc_response_status": "",
    "response_flags": "-",
    "le": "1"
}
```

S3 저장 비용이 크게 절감되었고, 단 몇 일 만에 압도적인 성능 향상을 볼 수 있었습니다. 또한 Prometheus 인스턴스에서 리소스 사용량이 줄어들었습니다. 다른 말로 — 요청 당 메트릭에서 불필요한 레이블을 식별하고 제거하는 것은 절대적으로 가치 있는 작업입니다.

<div class="content-ad"></div>

제가 작성한 워크스루가 몇몇 프로메테우스 운영자분들께서 관측성 스택을 최적화하는데 도움이 되길 바라요!