---
title: "Kubernetes에서 애플리케이션 배포, 확장 및 장애 조치 중에 건강 검사를 사용하는 방법"
description: ""
coverImage: "/assets/img/2024-05-23-HowKubernetesuseshealthprobesduringapplicationdeploymentscalingandfailover_0.png"
date: 2024-05-23 14:28
ogImage:
  url: /assets/img/2024-05-23-HowKubernetesuseshealthprobesduringapplicationdeploymentscalingandfailover_0.png
tag: Tech
originalTitle: "How Kubernetes uses health probes during application deployment, scaling, and failover."
link: "https://medium.com/@rdalal3/how-kubernetes-uses-health-probes-during-application-deployment-scaling-and-failover-dbaa1e654fe9"
---


![Health probes](/assets/img/2024-05-23-HowKubernetesuseshealthprobesduringapplicationdeploymentscalingandfailover_0.png)

헬스 프로브는 견고한 클러스터를 유지하는 중요한 부분입니다. 프로브를 사용하면 클러스터가 응답 여부를 반복적으로 조사하여 응용 프로그램의 상태를 결정할 수 있습니다.

일련의 헬스 프로브는 클러스터가 다음 작업을 수행하는 능력에 영향을 미칩니다:

- 실패 중인 팟을 자동으로 다시 시작을 시도하여 충돌 방지
- 건강한 팟에게만 요청을 보내어 장애 극복 및 부하 분산
- 팟이 실패하는 시기와 이유를 결정하여 모니터링


<div class="content-ad"></div>

# 프로브 유형

쿠버네티스는 다음과 같은 프로브를 제공합니다: 시작, 준비 및 활성화. 어플리케이션에 따라 하나 이상의 이러한 유형을 구성할 수 있습니다.

## 준비 프로브

준비 프로브는 어플리케이션이 요청을 처리할 준비가 되어 있는지 확인합니다. 준비 프로브가 실패하면 쿠버네티스가 해당 어플리케이션에 대한 클라이언트 트래픽이 도달하는 것을 방지하기 위해 서비스 리소스에서 포드의 IP 주소를 제거합니다.

<div class="content-ad"></div>

준비성 검사는 애플리케이션에 영향을 미칠 수 있는 일시적 문제를 감지하는 데 도움을 줍니다. 예를 들어, 애플리케이션이 시작될 때 초기 네트워크 연결을 설정하거나 캐시에 파일을 로드하거나 완료하는 데 시간이 걸리는 초기 작업을 수행해야 하기 때문에 애플리케이션이 일시적으로 사용할 수 없을 수 있습니다. 애플리케이션이 가끔은 긴 일괄 작업을 실행해야 할 수도 있어 클라이언트에게 일시적으로 사용할 수 없게 만듭니다.

Kubernetes는 애플리케이션이 실패한 후에도 계속해서 검사를 실행합니다. 검사가 다시 성공하면 Kubernetes는 포드의 IP 주소를 서비스 리소스에 다시 추가하고 요청을 다시 해당 포드로 보냅니다.

이러한 경우에 준비성 검사는 일시적 문제를 해결하고 애플리케이션 가용성을 향상시킵니다.

## 생존성 검사

<div class="content-ad"></div>

준비 프로브와 마찬가지로, 라이브니스 프로브는 응용 프로그램의 수명 동안 호출됩니다. 라이브니스 프로브는 응용 프로그램 컨테이너가 건강한 상태인지를 확인합니다. 응용 프로그램이 라이브니스 프로브에 충분한 횟수로 실패하면 클러스터는 다시 시작 정책에 따라 파드를 다시 시작합니다.

시작 프로브와 달리, 라이브니스 프로브는 응용 프로그램의 초기 시작 프로세스 이후에 호출됩니다. 보통 이 조치는 파드를 다시 시작하거나 다시 만들어서 해결됩니다.

## 시작 프로브

시작 프로브는 응용 프로그램의 시작이 완료된 시점을 결정합니다. 라이브니스 프로브와 달리, 시작 프로브는 성공한 후에도 호출되지 않습니다. 설정 가능한 시간 초과 후에 시작 프로브가 성공하지 않으면, 해당 파드는 다시 시작 정책 값에 기반하여 다시 시작됩니다.

<div class="content-ad"></div>

앱 시작 시간이 긴 애플리케이션에 스타트업 프로브를 추가하는 것을 고려해보세요. 스타트업 프로브를 사용하면 라이브니스 프로브를 짧고 빠르게 유지할 수 있습니다.

# 테스트 유형

프로브를 정의할 때 수행할 테스트 유형을 다음 중 하나로 지정해야 합니다:

HTTP GET

<div class="content-ad"></div>

각 번 업된 때는 물고기가 클러스터에게 지정된 HTTP 엔드포인트로 요청을 보냅니다. 요청에 대한 응답이 200에서 399 사이의 HTTP 응답 코드로 오면 테스트는 성공한 것으로 간주됩니다. 다른 응답은 테스트를 실패하게 만듭니다.

컨테이너 명령어

각 번 업된 때는 물고기가 컨테이너에서 지정된 명령어를 실행합니다. 명령이 0의 상태 코드로 종료되면 테스트가 성공합니다. 다른 상태 코드는 테스트에 실패하게 만듭니다.

TCP 소켓

<div class="content-ad"></div>

프로브가 실행될 때마다 클러스터는 컨테이너에 소켓을 열려고 시도합니다. 연결이 수립되어야만 테스트가 성공합니다.

# 시간과 임계값

모든 유형의 프로브에는 시간 변수가 포함되어 있습니다. 주기 초 변수는 프로브가 실행되는 빈도를 정의합니다. 실패 임계값은 프로브 자체가 실패하기 전에 필요한 실패 시도 횟수를 정의합니다.

예를 들어, 실패 임계값이 3이고 주기 초가 5인 프로브는 전체 프로브가 실패하기 전에 최대 세 번 실패할 수 있습니다. 이 프로브 구성을 사용하면 문제가 해결되기 전 10초 동안 문제가 발생할 수 있습니다. 그러나 너무 자주 프로브를 실행하면 리소스를 낭비할 수 있습니다. 프로브를 설정할 때 이러한 값을 고려해주세요.

<div class="content-ad"></div>

예시 1

```yaml
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
    - name: liveness
      image: registry.k8s.io/busybox
      args:
        - /bin/sh
        - -c
        - touch /tmp/healthy; sleep 30; rm -f /tmp/healthy; sleep 600
      livenessProbe:
        exec:
          command:
            - cat
            - /tmp/healthy
        initialDelaySeconds: 5
        periodSeconds: 5
```

예시 2

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-app-liveness-pod
spec:
  containers:
    - name: hello-app-container
      image: gcr.io/google-samples/hello-app:1.0 # 애플리케이션 이미지로 교체하세요
      ports:
        - containerPort: 8080 # 애플리케이션의 수신 포트와 일치해야 합니다
      livenessProbe:
        httpGet:
          path: /
          port: 8080
initialDelaySeconds: 15 # 컨테이너가 시작된 후 생존성 프로브가 시작되기까지의 시간(초)
periodSeconds: 10 # 프로브를 수행하는 주기(초)
timeoutSeconds: 1 # 응답을 기다리는 시간(초)
failureThreshold: 3 # 재시작하기 전에 허용되는 실패 횟수
```

<div class="content-ad"></div>

오픈쉬프트에서 oc set probe 명령은 배포에 프로브를 추가하거나 수정합니다. 예를 들어, 다음 명령은 front-end라는 배포에 준비 상태 프로브를 추가합니다:

```js
oc set probe deployment/front-end \
--readiness \
--failure-threshold 6 \
--period-seconds 10 \
--get-url http://:8080/healthz
```

set probe 명령은 RHOCP와 oc에만 존재합니다.
