---
title: "재밌는 주제 프로덕션 환경에서 신뢰할 수 있는 딥러닝 스택 구축하는 방법 이에 대해 알아보도록 하죠"
description: ""
coverImage: "/assets/img/2024-05-23-Howtoensureyourdeeplearningstackisfail-safeinproduction_0.png"
date: 2024-05-23 17:14
ogImage: 
  url: /assets/img/2024-05-23-Howtoensureyourdeeplearningstackisfail-safeinproduction_0.png
tag: Tech
originalTitle: "How to ensure your deep learning stack is fail-safe in production?"
link: "https://medium.com/decodingml/how-to-ensure-your-deep-learning-stack-is-fail-safe-in-production-2673c0e3b03d"
---


Prometheus, Triton, 그리고 Grafana를 사용하여 엔드 투 엔드 모니터링 대시보드를 구축해보세요.

ML 시스템을 배포하기 전에, 엔지니어들은 로컬 및 대규모에서 어떻게 성능을 발휘할지 정확한 통찰력이 필요합니다. 병목 현상을 식별하고 예상치 못한 동작을 파악하기 위해.

클래식 ML 추론 파이프라인과 비교하면, 딥 러닝 시스템은 자원 소비, 복잡성 및 확장 가능성의 도전에 따라 낮은 지연 시간, 높은 처리량에 중점을 둔다는 것 때문에 보다 "중요한" 및 자세한 모니터링이 필요합니다. 특히, 컴퓨터 비전과 같은 자원 집중적인 응용 프로그램에서 딥 러닝 배포를 위해 ML 엔지니어들은 모니터링에 우선순위를 두어야 합니다.

이 글에서는 배포 설정 및 모니터링의 워크플로우에 대해 다루겠습니다.

<div class="content-ad"></div>

# 목차

1. 성능 모니터링 파이프라인 설정 방법

- 컨테이너
- 설정 파일
- 도커 컴포즈

2. 메트릭 스크랩 구성

<div class="content-ad"></div>

프로메테우스 타겟을 추가하기
- 그라파나 데이터소스 추가하기
- 헬스체크 대상 스크랩

3. 대시보드 생성

- GPU 메트릭을 위한 패널
- CPU/RAM 메트릭을 위한 패널

4. 시각화

<div class="content-ad"></div>

다음으로 넘어가기 전에 사용할 도구들을 살펴봅시다:

- 도커는 가벼우고 휴대용한 컨테이너 내에서 애플리케이션을 개발, 배포, 실행할 수 있는 플랫폼입니다 — ML 엔지니어에게 꼭 필요한 도구입니다.
- 도커 컴포즈는 멀티 컨테이너 애플리케이션을 정의하고 구성하는 도구입니다.
- cAdvisor는 구글에서 개발한 리소스 사용량 및 컨테이너 성능 지표를 제공해주는 오픈소스 도구입니다.
- 프로메테우스는 메트릭을 수집하고 저장하는 모니터링 및 경보 시스템으로, 프로메테우스에 대한 전문 지식은 ML/MLOps 엔지니어에게 큰 장점입니다.
- 그라파나는 모니터링 및 가시성 플랫폼으로, 배포된 시스템의 메트릭을 생성, 시각화, 경보 및 이해할 수 있게 해줍니다. 모니터링 대시보드를 관리하는 것은 MLOps 엔지니어에게 중요한 기술입니다.
- Triton Inference Server는 NVIDIA에서 개발한 인기 있는 모델 서빙 프레임워크로, 복잡한 ML 모델을 프로덕션 환경에 배포하는 데 중요한 역할을 합니다. Triton에 대한 전문 지식은 MLOps 엔지니어에게 필수적인 기술입니다.

# 1. 도커 컴포즈 설정

각 서비스가 무엇을 하는지 설명하고, 이러한 서비스를 캡슐화하고 실행할 도커 컴포즈를 준비하는 것부터 시작해봅시다.

<div class="content-ad"></div>

다음과 같은 내용이 있습니다:

![이미지](/assets/img/2024-05-23-Howtoensureyourdeeplearningstackisfail-safeinproduction_0.png)

도커 컴포즈 모니터링 yaml 파일을 살펴보겠습니다.

```yaml
# cat docker-compose-monitoring.yaml
version: '3.4'
```

<div class="content-ad"></div>

```yaml
services:
  prometheus:
    image: prom/prometheus:latest
    ports:
      - "${PROMETHEUS_PORT}:${PROMETHEUS_PORT}"
    container_name: prometheus
    restart: always
    volumes:
      - "${MONITORING_CONFIGURATIONS}/prometheus.monitoring.yml:/etc/prometheus/prometheus.monitoring.yml"
      - /var/run/docker.sock:/var/run/docker.sock:ro
    command:
      - "--config.file=/etc/prometheus/prometheus.monitoring.yml"
      - "--enable-feature=expand-external-labels"
    depends_on:
      - cadvisor
    networks:
      monitor-net:
        ipv4_address: ${PROM_IP}
  grafana:
    image: grafana/grafana-enterprise:8.2.0
    container_name: grafana
    ports:
      - "${GRAFANA_PORT}:${GRAFANA_PORT}"
    volumes:
      - ${MONITORING_CONFIGURATIONS}/datasources:/etc/grafana/provisioning/datasources
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=${GRAFANA_PWD}
      - GF_SECURITY_ADMIN_USER=${GRAFANA_USER}
    networks:
      monitor-net:
        ipv4_address: ${GRAFANA_IP}
  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
    container_name: cadvisor
    restart: always
    ports:
      - "${CADVISOR_PORT}:${CADVISOR_PORT}"
    volumes:
      - "/etc/localtime:/etc/localtime:ro"
      - "/etc/timezone:/etc/timezone:ro"
      - "/:/rootfs:ro"
      - "/var/run:/var/run:rw"
      - "/sys:/sys:ro"
      - "/var/lib/docker:/var/lib/docker:ro"
    networks:
      monitor-net:
        ipv4_address: ${CADVISOR_IP}
  triton_server:
    container_name: tis2109
    image: nvcr.io/nvidia/tritonserver:21.09-py3
    privileged: true
    ports:
      - "8002:8002"
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              capabilities: [gpu]
    volumes:
      - ${TRITON_MODELS_REPOSITORY}:/models
    command: ["tritonserver","--model-repository=/models", "--strict-model-config=false"]
    networks:
      monitor-net:
        ipv4_address: ${TRITON_IP}
networks:
  monitor-net:
    driver: bridge
    internal: false
    ipam:
        driver: default
        config:
            - subnet: ${SUBNET}
              gateway: ${GATEWAY}
```

보시다시피, .yaml 설정에 일부 마스킹된 $'변수'가 있습니다. 이들은 .env 파일 내부에서 자동으로 상속되어 로컬 개발 및 CI/CD 파이프라인에서 최상의 관행을 따르는 흐름을 가지고 있습니다.

이제 .env 파일에 어떤 것이 있는지 살펴봅시다:

```yaml
# == 모니터링 변수 ==
PROMETHEUS_PORT=9090
GRAFANA_PORT=3000
CADVISOR_PORT=8080
MONITORING_CONFIGURATIONS=<your_configuration_files에 대한_경로>
```

<div class="content-ad"></div>

```js
# == 자격 증명 ==
GRAFANA_PWD=admin
GRAFANA_USER=admin
# == TIS 변수 == 
TRITON_MODELS_REPOSITORY=<당신의_triton_모델_저장소_경로>
# == 기본 네트워크 ==
SUBNET=172.17.0.0/16
GATEWAY=172.17.0.1
# == 서브넷 IP ==
TRITON_IP=172.17.0.3
CADVISOR_IP=172.17.0.4
PROM_IP=172.17.0.5
GRAFANA_IP=172.72.0.6
```

대부분의 변수가 설정되었지만, 여기서 살펴봐야 할 주요한 2가지 변수는 다음과 같습니다:

- MONITORING_CONFIGURATIONS
이것은 이러한 구조가 있는 폴더를 가리켜야 합니다

```js
.__ monitoring
|  |_ datasources
|  | |_ datasources.yml
|  |_ prometheus.monitoring.yml
```

<div class="content-ad"></div>

- TRITON_MODEL_REPOSITORY
모델 저장소의 구조는 다음과 같아야 합니다:

```js
model_repository
└── prod_client1_encoder
    └── 1
        └──resnet50.engine
    └── config.pbtxt
```

프로메테우스 모니터링 파일인 prometheus.monitoring.yml에는 메트릭을 가져올 대상(컨테이너)을 추가할 것입니다.
데이터 소스 파일인 datasources.yml에는 그라파나 대시보드의 소스로 프로메테우스를 추가할 것입니다. 그렇게 하면 그라파나 UI를 열 때 나타날 것입니다.

# 2. 프로메테우스 스크래핑 구성 정의

<div class="content-ad"></div>

그럼 Prometheus 대상을 구성해 보겠습니다. `prometheus.monitoring.yml` 파일에 작성하겠습니다.

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s
```

```yaml
scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['172.17.0.5:9090']
  - job_name: 'triton-server'
    static_configs:
      - targets: ['172.72.0.3:8002']
  
  - job_name: 'cadvisor'
    static_configs:
      - targets: ['172.72.0.4:8080']
```

3개의 대상이 있습니다.

<div class="content-ad"></div>

- 프로메테우스 — 모니터링 프로메테우스 자체를 유지하는 것이 건강한 모니터링을 위한 모범 사례로 작용합니다. 수천 개의 메트릭을 처리하면 병목 현상이 발생할 수 있으며 프로메테우스 자체의 자원 사용량을 알고 있는 것이 유용합니다.
- Triton Server — 이것은 이 딥러닝 스택의 핵심에 있어 중요합니다. ML 모델을 제공하고 관리하기 때문입니다.
Triton은 인퍼런스 프로세스 전반에 걸쳐 다양한 메트릭을 제공하는 포트 8002의 내장된 프로메테우스 엔드포인트가 있습니다.
- cAdvisor — 이 배포에서 컨테이너 전반의 CPU/RAM 사용량 정보를 얻기 위해 사용됩니다.

모두 구성한 뒤, 컴포저를 시작하고 문제가 있는지 검사할 수 있습니다.
컨테이너를 시작해 봅시다.

```js
docker compose -f docker-compose-monitoring.yaml up -d
```

프로메테우스 대상을 검사해 봅시다.

<div class="content-ad"></div>

- 웹 브라우저로 가서 전체 Prometheus URL(IP:9090)을 입력해주세요.
- 상태 → 대상(Targets)으로 이동해주세요.
- 스크래핑 구성에서 각 대상이 정상인지 확인해주세요(녹색).

이러한 사항을 확인한 후에는 Grafana에서 대시보드를 만들어 진행할 수 있습니다.

# #3 대시보드 생성하기

Grafana WebUI 대시보드에 액세스하려면 브라우저를 열고 `localhost:3000`으로 이동해주세요. 여기서 3000은 Grafana 컨테이너를 실행하는 포트입니다.

<div class="content-ad"></div>

로그인 페이지로 이동하면 사용자 이름/암호 필드에 `admin/admin`을 사용하십시오. 더 높은 보안이 권장되지만, 이는 이 기사의 범위에 포함되지 않습니다.

Grafana 웹을 열었으면 다음을 수행해야 합니다:

- 데이터 소스를 우리의 Prometheus 메트릭 스크래퍼 엔드포인트로 지정합니다.
- 새 대시보드 생성
- 관심 있는 메트릭을 집계/시각화하기 위해 차트 추가

#3.1 Prometheus 데이터 소스

<div class="content-ad"></div>

왼쪽 패널에서 기어 아이콘(설정)을 클릭하고 DataSources를 선택하세요.
다음과 같은 뷰가 나타날 것입니다:

"Add data source"를 클릭한 후 Time Series Databases 아래에서 `Prometheus`를 선택하세요. Grafana는 여러 종류의 메트릭 스크랩을 지원하고 있습니다. 여기서는 Prometheus를 사용할 것입니다. 이런 뷰가 나타날 것입니다:

<img src="/assets/img/2024-05-23-Howtoensureyourdeeplearningstackisfail-safeinproduction_1.png" />

여기에는 Prometheus 엔드포인트의 URL을 추가해야 합니다. 우리의 도커 컴포즈 배포에서는 `http://prometheus:9090`을 사용할 것입니다. 이 템플릿을 따르면 `http://container_name:container_port`가 됩니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-Howtoensureyourdeeplearningstackisfail-safeinproduction_2.png" />

여기까지 오셨군요. 이제 데이터 원본 추가 섹션이 완료되었습니다.
이제 대시보드를 만들어보겠습니다.

### 3.2 Grafana 대시보드 만들기

왼쪽 패널에서 “+” 표시를 클릭하고 `Dashboard`를 선택하세요. 이렇게 하면 미리 정의된 패널 그룹이 있는 새 대시보드 페이지로 이동합니다. 우리는 모든 것을 처음부터 만들고 있으므로 `Empty Panels`만 사용하여 주요 지표를 표시할 것입니다.

<div class="content-ad"></div>

다음은 한 예제에 대한 따라할 프로세스입니다:

- 새 쿼리를 추가하고 `promql` (Prometheus 쿼리 언어)를 정의합니다.
- 시각화 유형, 그래프 스타일, 범례를 구성합니다.

다음은 비어 있는 패널의 모습입니다:

이제, 트리튼 추론 서버 모델 서빙 플랫폼을 모니터링하기 위해 몇 가지 사용자 정의 쿼리를 추가할 것입니다. 하지만 먼저 다음 참고 사항을 유념해야 합니다:

<div class="content-ad"></div>

첫 번째 쿼리를 설정해보겠습니다. 이 쿼리는 성공적인 요청의 수를 고려하여 모델이 하나의 추론 요청을 수행하는 데 걸리는 시간(밀리초)을 측정할 것입니다. 우리는 시간이 지남에 따라 진행 상황을 보고 싶기 때문에 이 차트는 `시계열(time-series)`이 될 것입니다.
다음은 해당 지표를 작성하는 쿼리입니다:

```js
(irate(nv_inference_compute_infer_duration_us{job="triton-server"}[$__rate_interval]) / 1000) / irate(nv_inference_request_success{job="triton-server"}[$__rate_interval])
```

쿼리를 해석해 봅시다:

아래에서 쿼리의 모습을 확인할 수 있습니다.

<div class="content-ad"></div>

이 새 차트의 설정을 구성할 때, 오른쪽에 다음을 지정할 수 있습니다:

- 차트 유형 — 직선 / 곡선 / T-스텝 라인 중 선택
- 메트릭 범위 — 메트릭 선택(예: 밀리초(ms))하고 low_range(예: 0)와 high_range(예: 100ms) 정의
- 사용자 지정 텍스트 — 범례 또는 다른 필드에 표시될 내용

#3.3 시각화 완료

위의 흐름에 따라, 나머지 차트를 생성할 수 있습니다.
전체 성능 모니터링 차트를 컴파일하려면 나머지 패널을 추가하십시오. 다음 각각에 대해 새 패널을 만들고 해당 세부 정보로 채워넣습니다:

<div class="content-ad"></div>

- GPU 사용된 바이트 - VRAM 사용량의 백분율

```js
쿼리: nv_gpu_memory_used_bytes{job="triton-server"}/nv_gpu_memory_total_bytes{job="triton-server"}
차트 유형: 파이
범례: {인스턴스}
```

2. GPU 활용도 - 전체 GPU 활용도

```js
쿼리: nv_gpu_utilization{job="triton-server"}
차트 유형: 시계열
범례: NULL
```

<div class="content-ad"></div>

3. 입력 시간/요청 — 클라이언트가 입력 데이터를 Triton 서버로 보내는 데 걸린 시간.

```js
Query: (irate(nv_inference_compute_input_duration_us{job="triton-server"}[$__rate_interval]) / 1000) / irate(nv_inference_request_success{job="triton-server"}[$__rate_interval])
Chart Type: 시계열
Legend: {model}-{version}
```

4. 출력 시간/요청 — 서버가 클라이언트에게 출력을 보내는 데 걸린 시간.

```js
Query: (irate(nv_inference_compute_output_duration_us{job="triton-server"}[$__rate_interval]) / 1000)/ irate(nv_inference_request_success{job="triton-server"}[$__rate_interval])
Chart Type: 시계열
Legend: {model}-{version}
```

<div class="content-ad"></div>

5. DB 비율 (#요청/#실행) — 성공적인 요청의 전체 요청 대비 비율

```js
쿼리: sum by (모델,버전) (rate(nv_inference_request_success{job="triton-server"}[$__rate_interval])/rate(nv_inference_exec_count{job="triton-server"}[$__rate_interval]) )
차트 유형: 시계열
범례: {모델}-{버전}
```

6. 대기 시간/요청 — 요청이 처리되기 전에 대기하는 시간

```js
쿼리: sum by (모델,버전) ((irate(nv_inference_queue_duration_us{job="triton-server"}[$__rate_interval]) / 1000) / irate(nv_inference_request_success{job="triton-server"}[$__rate_interval]))
차트 유형: 시계열
범례: {모델}-{버전}
```

<div class="content-ad"></div>

7. 집계된 입력/추론/출력 - 입력 출력 및 추론을 한 차트에 표시합니다.

```js
질의:
A: rate(nv_inference_compute_input_duration_us{job="triton-server"}[$__interval]) / 1000
B: rate(nv_inference_compute_infer_duration_us{job="triton-server"}[$__interval]) / 1000
C: rate(nv_inference_compute_output_duration_us{job="triton-server"}[$__interval]) / 1000

차트 유형: 시계열
범례: {모델}-{버전}
```

우리가 만든 완벽한 대시보드는 다음을 보여줍니다:

- GPU VRAM 이용률
- 클라이언트에서 서버로의 입력 송신 시간
- 서버 추론 요청 시간
- 서버에서 클라이언트로의 출력 송신 시간
- 성공 요청/전체 요청 비율

<div class="content-ad"></div>

이런 종류의 대시보드는 배포된 스택의 성능 및 스트레스 테스트 속에서의 동작을 모니터링하기 위한 시작점을 제시합니다. 

이는 배포의 실패 및 위험 지점을 연구하는 구체적인 방법을 제공하며 SLI(서비스 수준 지표)를 모니터링하는 데 도움이 됩니다.

서비스 수준 지표는 SLA(서비스 수준 계약)를 준수하고 SLO(서비스 수준 목표)를 달성하기 위해 모니터링되는 메트릭입니다. 우리가 만든 대시보드는 제공되는 서비스 수준 계약을 준수하기 위한 목표에 도달하기 위한 가치 있는 통찰을 제공할 수 있습니다. 

또한 이는 다중 복제본을 추가하거나 추론 서빙 프레임워크를 실행하는 여러 기계로의 확장 전략을 계획하는 데 도움이 됩니다.

<div class="content-ad"></div>

# 결론

본 문서에서는 Prometheus와 Grafana를 사용하여 자사의 ML 애플리케이션 및 모델 서빙 프레임워크를 위한 성능 모니터링 스택을 설정하고 구축하는 방법을 소개했습니다.

도커 컴포즈 파일을 준비하는 것부터 시작하여 워크플로우의 각 단계를 설명하고 스택을 배포하고 데이터 원본을 구성하며 대시보드 패널을 생성하고 지표를 집계하는 과정을 마무리했습니다.

모니터링은 MLOps 시스템의 중요한 부분입니다!
이 튜토리얼을 따라 하면 테스트 환경에서 단일 배포로 ML 애플리케이션을 위한 모니터링 파이프라인을 구조화하고 배포하거나, 클라우드 시나리오 설정에서 여러 입력 소스를 결합하고 전체 스택 배포를 모니터링하는 단일 대시보드 소비자 지점을 갖도록 구성할 수 있습니다.

<div class="content-ad"></div>

# 더 많은 내용을 원하신다면!

저는 미디엄에 새롭게 등장했습니다. 만약 이 글을 즐겨보셨다면 박수를 보내주시고 제 계정을 팔로우해주세요 - 정말로 감사하겠습니다! 🚀

![How to ensure your deep learning stack is fail-safe in production](/assets/img/2024-05-23-Howtoensureyourdeeplearningstackisfail-safeinproduction_3.png)

# 더 많은 글 보기

<div class="content-ad"></div>

이 게시물과 관련성에 따라 정렬되었습니다.