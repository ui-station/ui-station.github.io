---
title: "Kubernetes에서 2년간 Airflow를 운영하며 배운 교훈들"
description: ""
coverImage: "/assets/img/2024-06-23-WhatwelearnedafterrunningAirflowonKubernetesfor2years_0.png"
date: 2024-06-23 01:07
ogImage: 
  url: /assets/img/2024-06-23-WhatwelearnedafterrunningAirflowonKubernetesfor2years_0.png
tag: Tech
originalTitle: "What we learned after running Airflow on Kubernetes for 2 years"
link: "https://medium.com/apache-airflow/what-we-learned-after-running-airflow-on-kubernetes-for-2-years-0537b157acfd"
---


![이미지](/assets/img/2024-06-23-WhatwelearnedafterrunningAirflowonKubernetesfor2years_0.png)

Apache Airflow은 우리 데이터 플랫폼에서 가장 중요한 구성 요소 중 하나로, 비즈니스 내 다양한 팀들이 사용하고 있습니다. 이는 우리의 모든 데이터 변환, 사기 탐지 메커니즘, 데이터 과학 이니셔티브 및 Teya에서 실행하는 다양한 일상 및 내부 작업을 지원합니다.

전체적인 그림을 보면, 저희는 300개 이상의 운영 DAG와 평균 하루에 5,000개 이상의 작업을 수행하는 중형 규모의 Airflow 배치 시스템을 운영 중입니다. 그래서 저는 우리가 사용자에게 가치를 전달할 수 있는 꽤 규모가 있는 Airflow 배포 시스템을 보유하고 있다고 말할 수 있겠습니다. 8개월 이상 동안 Airflow에서 한 번의 인시던트나 실패 없이 운영되어 왔습니다.

본 게시물을 통해, 확장 가능하고 신뢰할 수 있는 환경을 구축하는 데에 도움이 된 저희 배포의 중요한 측면을 공유하고자 합니다. 혹시 지금 Airflow를 프로덕션 환경에서 시작하는 중이거나 다른 아이디어를 평가하고 사용 사례에 적용하고자 하는 경우 도움이 되기를 바라겠습니다.

<div class="content-ad"></div>

저희는 현재 Airflow 구현의 주요 측면에 따라 나눠볼 거예요:

- Executor 선택
- 디커플링 및 동적 DAG 생성
- 세밀한 구성 조정
- 알림, 경보 및 감시

## Executor 선택

여기서는 Kubernetes에서 모든 것을 실행합니다. 그래서 Airflow의 경우도 다를 바 없습니다. 처음에는 Executor 선택이 명백해 보였습니다: 쿠버네티스 Executor를 사용합시다! 런타임 격리, 쿠버네티스를 활용하여 원활한 작업 확장성, 그리고 관리해야 할 구성 요소가 적다는 장점들이 모두 아주 좋아 보였죠. 그렇게 우리의 여정을 시작했습니다.

<div class="content-ad"></div>

그러나 저희 스택에서 중요한 특징 하나가 있습니다: 대부분의 작업은 가벼운 DBT 증분 변환 작업이며, 아주 소수의 작업만 장기 실행되는 모델(+/- 1시간)입니다.

처음에 직면한 문제는 작업을 시작하는 데 드는 오버헤드였습니다. KubernetesExecutor는 각 작업을 별도의 Pod에서 실행하므로 때로는 Pod를 시작하는 대기 시간이 작업 자체의 실행 시간보다 더 길어지곤 했습니다. 작은 작업이 많아서 Kubernetes 노드의 확장을 기다리느라 계속해서 기다려야 했습니다.

두 번째 문제는 더 많은 고통을 야기한 문제이었는데, 일부 작업(특히 장기 실행되는 작업)이 Pod가 유배되어 예기치 않게 실패하는 일이 있었습니다. 작업이 급격히 증가하면 Pod와 클러스터 노드의 수도 증가하므로 작업이 완료되자마자 시스템이 다시 축소될 준비가되었습니다.

<img src="/assets/img/2024-06-23-WhatwelearnedafterrunningAirflowonKubernetesfor2years_1.png" />

<div class="content-ad"></div>

문제는 Karpenter를 사용하여 k8s 클러스터의 리소스 사용량을 최적화하고 있어서 더욱 악화되었습니다. 따라서 몇 개의 Pods가 완료된 후에 노드의 스케일인이 매우 빠르게 발생했습니다. 이 동작은 해당 노드에 남아 있는 나머지 Pods를 추방하여 다른 노드로 재배포하여 총 노드 수를 줄이고 비용을 절감하는 것입니다.

## CeleryExecutor가 해결책으로 등장

이 모든 상황을 고려하여, 우리는 오래된 신뢰할 수 있는 Celery Executor로 전환하기로 결정했습니다. 이제 고정된 워커 노드를 갖게 되어, 우리의 많고 빠른 작업을 완벽히 처리할 수 있습니다. DBT 작업의 평균 실행 시간이 크게 감소했습니다. 이제는 초기화를 기다릴 필요가 없게 되었기 때문입니다.

Airflow의 공식 최신 helm 차트를 사용함으로써, KEDA 오토스케일러의 도움을 받아 필요에 따라 셀러리 워커의 수를 증가 또는 감소시킬 수 있습니다. 따라서 아이들 상태의 워커에 대해 추가 비용을 지불할 필요가 없게 됩니다. 이는 Airflow의 데이터베이스에서 실행 중이거나 대기 중인 작업의 수를 가져와서 작업자 병행성 구성에 따라 작업자 수를 조정함으로써 작동합니다.

<div class="content-ad"></div>

자원을 더 많이 필요로 하는 맞춤형 작업의 경우, KubernetesPodOperator를 사용하여 실행할 수 있는 옵션이 있습니다. 이를 통해 Airflow의 이미지에 설치하지 않고도 특정 종속성에 대한 런타임 분리를 유지하고 각 작업에 개별 자원 요청을 정의할 수 있습니다.

현재는 KubernetesCeleryExecutor의 채택을 고려 중입니다. 이는 작업을 두 개의 별도 대기열인 k8s와 Celery로 예약할 수 있게 해줍니다. 몇몇 작업이 Celery에 더 잘 맞고 다른 일부는 Kubernetes에 더 적합한 경우에 유용할 수 있습니다.

# Decoupling 및 동적 DAG 생성

데이터 엔지니어링 팀뿐만 아니라 각 팀이 자체 DAG를 작성하는 시나리오를 수용하기 위해 DAG에 대한 멀티 레포 접근 방식이 필요했습니다. 그러나 동시에 일관성을 유지하고 지침을 강제할 필요가 있습니다.

<div class="content-ad"></div>

## DAGs를 위한 다중 저장소 접근 지원

DAGs는 각각 다른 팀이 소유하는 개별 저장소에서 개발될 수 있으며 여전히 동일한 Airflow 인스턴스에 배치될 수 있습니다. 물론 DAGs를 Airflow 이미지에 포함할 필요 없이 😉

한 사람이 DAG의 한 줄을 수정할 때마다 스케줄러와 워커를 계속 다시 시작하려고 하지 않는 게 좋을 거에요.

<img src="/assets/img/2024-06-23-WhatwelearnedafterrunningAirflowonKubernetesfor2years_2.png" />

<div class="content-ad"></div>

각 DAG는 팀이 소유한 DAG에 대한 경로에 따라 동기화 프로세스에 의해 어딘가의 버킷에 끝나게 됩니다.

이 접근 방식이 작동하려면 CI/CD 가드레일을 강제로 실행해야 합니다. 각 DAG 이름은 해당 DAG를 소유한 팀으로 미리 지정되어야 하므로 DAG ID 충돌을 피할 수 있습니다. 또한 각 DAG에 대해 정적 검사가 수행되어 정확한 소유자 할당과 태그 존재 여부, 가능한 가져오기 오류 등을 확인합니다.

이를 통해 네이티브 Airflow 역할을 사용하여 액세스 제어를 강화하고 각 DAG는 커밋되기 위해 최소 거버넌스 체크리스트를 통과해야 합니다.

## 그런데, Airflow로 DAG를 어떻게 동기화할까요?

<div class="content-ad"></div>

Airflow에 DAG가 반영되려면, 스케줄러, 워커 등을 실행하는 Pod의 로컬 파일 시스템과 버킷 내용을 동기화해야 합니다. 이를 위해 우리는 Objinsync를 사용하고 있습니다. Objinsync는 가벼운 데몬으로 원격 객체 스토어를 로컬 파일 시스템으로 점진적으로 동기화합니다.

우리는 모든 Airflow 구성 요소 Pod에 부착된 부가 컨테이너로 objinsync를 실행하여 자주 동기화 작업을 수행합니다. 그래서 DAG에 대한 새로운 업데이트를 항상 몇 분 내에 확인할 수 있습니다. 여기서 배운 교훈은 objinsync를 이니셜라이저 컨테이너로도 추가하는 것입니다. 이렇게 하면 메인 스케줄러나 워커 컨테이너가 시작되기 전에도 DAG를 동기화할 수 있습니다. 이는 특히 Celery 워커들에 대해 매우 중요했습니다. 노드 회전이나 릴리스로 인해 다시 시작된 후, 때로는 DAG를 아직 가져오지 않은 새로운 워커에 할당된 작업이 발생하여 즉시 실패하는 경우가 있습니다.

<img src="/assets/img/2024-06-23-WhatwelearnedafterrunningAirflowonKubernetesfor2years_3.png" />

이를 더 효과적으로 처리하는 이상적인 방법은 스케줄러에 부착된 부가 컨테이너로써 objinsync 프로세스를 하나만 실행하고 버킷 내용을 영구 볼륨에 복사하는 것입니다. 그래서 해당 PV가 모든 Airflow 구성 요소에 장착됩니다. 여기에는 DAG가 서로 다른 Airflow 구성 요소 사이에 동기화 문제가 발생하지 않는 장점이 있습니다.

<div class="content-ad"></div>

죄송하지만, 현재는 클러스터 노드에 대해서는 EBS 볼륨만 지원하고 있어서 이러한 해결책을 아직 적용할 수 없습니다. 서로 다른 노드에 PV를 마운트하려면 ReadWriteMany 액세스 모드가 필요합니다. 현재 이 모드는 AWS EKS에서만 EFS 볼륨 모드를 사용할 때에만 사용할 수 있습니다.

우리의 제한 사항에 대한 해결책으로는 Airflow Pods들을 동일한 노드에 스케줄링하기 위해 nodeSelector를 사용하는 것이 될 수 있습니다. 그러나 우리는 서로 다른 가용 영역의 노드를 사용하여 고가용성 Airflow 배포를 선호했습니다.

## DAG를 동적으로 생성할 때 주의해야 할 점

규모에 맞게 DAG를 생성하려면 DAG 템플릿 및 프로그래밍 방식의 생성을 활용해야 합니다. 더 이상 DAG를 수동으로 모두 작성할 필요가 없습니다 😂

<div class="content-ad"></div>

가장 간단한 방법으로 DAG를 동적으로 생성하는 방법은 단일 파일 방식을 사용하는 것입니다. 루프에서 DAG 객체를 생성하고 globals() 사전에 추가하는 하나의 파일이 있습니다.

이 방법은 처음에는 우리의 DBT 프로젝트를 기반으로 동적 DAG를 생성하기 시작했을 때 매우 직관적이었습니다 (DBT 오케스트레이션에 대한 주제는 별도의 게시물이 필요하며 나중에 이루어질 예정입니다). 그러나 DAG가 정기적으로 스케줄러에 의해 구문 분석되는 경우, 이 방법을 사용할 때 CPU 및 메모리 사용량이 증가하고 스케줄러 루프 시간이 더 오래 걸리는 것을 관측했습니다. 특히 DBT manifest.json을 구문 분석해야 했기 때문에 이 방법은 우리 프로젝트 규모에서 확장 가능하지 않았음이 빨리 드러났습니다.

해결책은 다중 파일 방식을 채택하는 것이었습니다. 이 방식에서는 동적으로 생성하려는 DAG마다 .py 파일을 생성합니다. 이를 통해 DAG 생성 프로세스를 우리의 DBT 프로젝트 저장소로 통합했습니다. 프로젝트는 이제 더 많은 DAG를 생성하는 프로듀서로 변화하여 동적으로 생성된 파일을 DAG 버킷으로 푸시합니다.

Astronomer에는 단일 파일 방법과 다중 파일 방법에 대한 훌륭한 기사가 있습니다.

<div class="content-ad"></div>

# 세부 구성 조정

CeleryExecutor로 이동한 순간, 우리가 한 문제를 해결했지만 새로운 문제가 발생하기 시작했습니다. 몇 일 후(또는 몇 시간 후)에 에어플로우를 실행하는 동안 일부 Celery 워커가 OOM(메모리 부족) 문제로 죽기 시작했습니다. Pod에 충분한 메모리 자원을 제공했기 때문에 무언가 이상한 것이 있었습니다.

조사 결과, 우리가 본 Celery 워커 자원 사용 차트는 다음과 같습니다.

![차트](/assets/img/2024-06-23-WhatwelearnedafterrunningAirflowonKubernetesfor2years_4.png)

<div class="content-ad"></div>

저희 업무는 주로 셀러리 워커에 의해 실행되는 DBT 작업으로 구성되어 있습니다. 이 때 거의 지속적으로 증가하는 메모리 사용량은 우리를 혼란스럽게 했었죠. 우리는 작업 간에 메모리 누수가 있는 것으로 의심하기 시작했습니다.

메모리 누수를 방지하고 작업의 메모리 사용량을 제어하기 위해 두 가지 중요한 셀러리 구성을 세밀하게 조정해야 했습니다: worker_max_tasks_per_child와 worker_max_memory_per_child.

첫 번째 구성은 워커 프로세스가 새 프로세스로 교체되기 전에 실행할 수있는 최대 작업 수를 제어합니다. 먼저 셀러리 워커와 워커 프로세스의 차이를 이해해야 합니다. 한 워커 노드는 여러 워커 프로세스를 생성할 수 있으며 이는 동시성 설정에 의해 제어됩니다. 예를 들어, 동시성을 12로 설정하고 셀러리 워커가 2개 있다면 총 24개의 워커 프로세스가 생깁니다.

따라서 동일한 워커 프로세스 내 작업 간 메모리 누수를 방지하기 위해 때때로 해당 프로세스를 재시작하는 것이 좋습니다. 이 구성이 설정되지 않으면 기본적으로 해당 워커 프로세스는 재시작하지 않습니다.

<div class="content-ad"></div>

두 번째 설정인 worker_max_memory_per_child은, 단일 워커 프로세스가 실행되기 전에 교체되는 최대 주거 중인 메모리 양을 제어합니다. 이는 기본적으로 메모리 사용량을 제어합니다. 기본 설정은 제한이 없으므로 항상 설정하는 것이 좋습니다.

이 두 구성을 조정함으로써 우리는 메모리 사용량을 제어하고, 두 가지 경우에 워커 프로세스를 재활용하게 됩니다: 만약 최대 작업 수에 도달하거나 최대 주거 중인 메모리양에 도달했을 때입니다. 이러한 구성은 프리포크 풀을 사용하는 경우에만 작동한다는 점을 주의해야 합니다. 자세한 내용은 공식 문서에서 확인할 수 있습니다.

Airflow에서 이를 설정하는 것은 매우 간단합니다. Airflow의 config_templates 폴더에 있는 기본 Celery 구성을 업데이트해야 합니다. 아래와 같이 해주세요:

```js
# config_templates/custom_celery.py

from airflow.config_templates.default_celery import DEFAULT_CELERY_CONFIG

CUSTOM_CELERY_CONFIG = DEFAULT_CELERY_CONFIG.copy()
CUSTOM_CELERY_CONFIG.update(
    {
        "worker_max_tasks_per_child": <int>,
        "worker_max_memory_per_child": <int>,
    }
)
```

<div class="content-ad"></div>

그리고 values.yaml에서 이 사용자 정의 구성물을 가리키도록 합니다.

```js
airflow:
   config:
     celery:
       worker_concurrency: <int>
       celery_config_options: config_templates.custom_celery.CUSTOM_CELERY_CONFIG
```

위 구성에 사용하는 구체적인 값은 워커 노드 구성, 메모리 요청/제한 양, 동시성 수준 및 작업이 메모리 집약적인지에 따라 다를 수 있습니다. 따라서 귀하의 특정 설정에 맞게 세밀하게 조정해야합니다.

## 노드 회전을 대비하세요

<div class="content-ad"></div>

k8s 노드는 고장이 발생하거나 쿠버네티스 클러스터를 관리하는 인프라 팀에 의해 예정된 노드 회전으로 인해 회전할 수 있습니다. 또한, 워커 노드(Pods)는 릴리스 이벤트 발생 시, 구성 변경(예: 환경 변수)이나 베이스 이미지 변경 시 회전합니다. 노드 회전은 당연히 Pods가 종료되는 결과를 가져옵니다.

이러한 사건에 대비하여 우리의 작업이 단순히 종료된 Pod로 인해 실패하지 않도록 준비되어 있어야 합니다. 특히 장시간 실행되는 작업의 경우 특히 아플 수 있습니다. 예를 들어, 2~3시간 동안 작업을 실행 중에 예정된 노드 회전으로 인해 실패한다면 상상할 수 있습니다.

이를 방지하기 위해 개별적인 요구 사항에 따라 Worker Termination Grace Period 구성을 설정하는 것이 중요합니다. 이 구성은 쉘러리 워커가 릴리스 프로세스나 노드 회전에 의해 종료되기 전에 해당 시간(초)까지 기다리도록 만들 것입니다. 이것은 Airflow의 차트 값(values.yaml)에서 쉽게 설정할 수도 있습니다.


airflow:
   workers:
      terminationGracePeriodSeconds: <int>


<div class="content-ad"></div>

가장 오래 걸리는 작업 완료 시간의 1.5배로 설정하는 것이 좋습니다. 이렇게 하면 모든 작업이 그 기간 내에 완료될 것이므로, 작업자를 원할하게 종료할 수 있습니다.

# 알림, 경보 및 관측

## 회사 알림 통합

Airflow의 가장 일반적인 사용 사례 중 하나는 특정 작업 이벤트, 예를 들어 파일 처리, 클린업 작업 또는 작업 실패 후에 사용자 지정 알림을 보내는 것입니다. Airflow를 사용하는 여러 팀이 있는 환경에서 작업 알림 메커니즘을 통합하는 것이 좋습니다.

<div class="content-ad"></div>

테이블 태그를 마크다운 형식으로 변경해주세요.

<div class="content-ad"></div>

커스텀 알림은 템플릿화되어 있어서 팀들은 표준 형식으로 Slack에서 정보 메시지를 만드는 데 사용할 수 있습니다. 또 다른 장점은 이 접근 방식을 사용하는 개별 팀이 개별 알림 대상의 비밀을 관리할 필요가 없다는 것입니다.

## 실패에 대해 가장 먼저 알아보세요

고가용성의 최상의 실천 방법과 패턴을 구현하더라도 Airflow는 다양한 이유로 실패할 수 있습니다. 이것이 인프라 수준에서의 관측 가능성, 메트릭 및 경보가 매우 중요한 이유입니다.

Kubernetes에서 실행할 때 관심 있는 모든 이벤트에 대해 PrometheusRule을 설정하여 수행할 수 있습니다. 예를 들어 스케줄러 노드의 상태, 사용 가능한 워커 노드 수 또는 스케줄러 루프 시간과 같은 특정 Airflow 메트릭을 모니터링할 수 있습니다. 또한 AlertManager를 실행하면 다양한 대상 (Slack, PagerDuty, Opsgenie 등)으로 경보를 발생시킬 수 있습니다.

<div class="content-ad"></div>

또 다른 현명한 일은 Airflow 메트릭을 활용하여 환경의 가시성을 향상시키는 것입니다. 현재 Airflow는 StatsD와 OpenTelemetry로 메트릭을 전송하는 것을 지원합니다. 후자가 더 우선되는데, 왜냐하면 OpenTelemetry가 Logs와 Traces를 지원하는 더 완전한 프레임워크이기 때문입니다. 그러나 현재 Airflow는 아직 OTEL을 통한 로그 및 추적을 지원하지 않습니다. (하지만 미래에 지원할 예정입니다!)

사용하고 싶다면, Kubernetes에서 OTEL Collector 배포를 관리해야 합니다 (공식 헬름 차트는 여기에 있습니다). 공식 Airflow 차트는 statsd와 달리 OTEL Collector를 제공하지 않습니다.

표준 메트릭으로 경보 기능을 크게 향상시킬 수 있습니다. 예를 들어, 대기 중인 작업의 총 수를 사용하여 대기열이 특정 시간 동안 너무 많이 증가할 때 트리거할 경고를 설정할 수 있습니다. 예를 들어 SLA 시간보다 더 긴 대기열은 원하지 않으실 겁니다.

❗️경고: 이 이미지는 미리보기용이므로 이미지가 표시되지 않을 수 있습니다.

<div class="content-ad"></div>

다른 유용한 지표는 DAG 구문 분석 시간과 스케줄러 루프 시간입니다. 이를 통해 Airflow에 영향을 미치는 문제를 빠르게 식별하고 전체 애플리케이션을 속도조절하는 데 도움을 줄 수 있습니다.

## Airflow 메타데이터 관리에 주의

메타데이터 데이터베이스는 성공적인 Airflow 구현의 중요한 부분이며, 성능을 저하시키거나 Airflow를 다운시킬 수 있습니다.

Airflow 노드 및 위에서 언급한 성능 지표를 모니터링하는 것 외에도 데이터베이스의 건강 지표를 모니터링하는 것이 중요합니다. PostgreSQL 또는 MySQL을 사용하는지에 따라 다르지만, CPU 사용량, 사용 가능한 스토리지, 열린 연결 수 등이 가장 일반적인 지표입니다.😂

<div class="content-ad"></div>

테이블 태그를 마크다운 형식으로 변경하면 더 좋은 방법입니다.

오래되거나 사용하지 않는 메타데이터를 정기적으로 정리하는 것도 좋은 실천 방법 중 하나입니다. job, dag_run, task_instance, log, xcom, sla_miss, dags, task_reschedule, task_fail 등과 같은 테이블이 여기에 포함될 수 있습니다. 이 모든 메타데이터는 Airflow 내에서 계속 쌓이며, 예를 들어 작업 상태를 가져오는 평균 쿼리가 필요 이상으로 오래 걸릴 수 있습니다. 또한 Airflow가 로드되고 탐색하는 것이 너무 느리게 느껴지는 적이 있나요? 메타데이터가 쌓이는 것이 그 원인일 수 있습니다.

다행히도, Airflow에는 그에 대한 네이티브 명령어가 제공됩니다. airflow db clean — 이 명령어는 동작을 구성할 수 있는 선택적 플래그와 함께 제공됩니다. 자세한 내용은 여기에서 확인할 수 있습니다.

Kubernetes를 사용하는 경우에는 Airflow 차트에 추가 리소스로 CronJob을 설정하여 지정한 플래그와 함께 주기적으로 airflow db clean 명령어를 실행하도록 하는 것이 좋은 접근 방법입니다. 구현 규모에 따라 이 작업을 매일 또는 매주 실행해야 할 수도 있습니다.

# 결론

<div class="content-ad"></div>

제가 작성한 텍스트가 Airflow on Kubernetes를 사용하는 여러 팀들이 함께 사용하는 협업 환경에서 여정을 시작하는 데 도움이 될 것을 희망합니다.

여기에 언급되지 않은 많은 구성 요소와 세부 정보들이 성공적인 구현에 기여하고 있습니다. 아직 개선할 부분이 많고 갈 길이 멀지만, 여러분도 경험을 공유하거나 질문이 있으시면 언제든지 연락해주세요. 함께 이야기 나누면 더 좋겠죠 😄