---
title: "쿠버네티스 명령어 명령형 vs 선언형 비교"
description: ""
coverImage: "/assets/img/2024-06-23-ImperativevsDeclarativeKubernetesCommands_0.png"
date: 2024-06-23 23:01
ogImage: 
  url: /assets/img/2024-06-23-ImperativevsDeclarativeKubernetesCommands_0.png
tag: Tech
originalTitle: "Imperative vs Declarative Kubernetes Commands"
link: "https://medium.com/@thebackendgrip/imperative-vs-declarative-kubernetes-commands-d92db5cc2c25"
---


쿠버네티스 명령줄 도구 인 kubectl을 사용하면 쿠버네티스 클러스터에 대해 명령을 실행할 수 있습니다. kubectl을 사용하여 응용 프로그램을 배포하고 클러스터 리소스를 검사하고 관리하며 로그를 볼 수 있습니다.

kubectl 도구는 세 가지 유형의 객체 관리를 지원합니다.

- 명령형 명령
- 명령형 객체 구성
- 선언적 객체 구성

![이미지](/assets/img/2024-06-23-ImperativevsDeclarativeKubernetesCommands_0.png)

<div class="content-ad"></div>

## 명령문

명령문은 클러스터의 객체에 직접적으로 작용하며, 이러한 명령문은 객체의 상태를 즉시 변경합니다. 

이러한 명령문의 예시:

- **kubectl create** — 이 명령어는 객체(e.g. 배포(Deployment), 레플리카셋(ReplicaSet) 등)를 생성하는 데 사용됩니다.
- **kubectl run** — 이 명령어는 포드(Pod)를 생성하는 데 사용됩니다.
- **kubectl expose** — 이 명령어는 배포(Deployment) 또는 레플리카셋(ReplicaSet)에 대한 서비스를 생성하는 데 사용됩니다.
- **kubectl scale** — 이 몤령어는 배포(Deployment) 또는 레플리카셋(ReplicaSet)에서 레플리카의 수를 확장하거나 축소하는 데 사용됩니다.
- **kubectl delete** — 이 명령어는 객체(e.g. 배포(Deployment), 포드(Pod) 등)를 삭제하는 데 사용됩니다.

<div class="content-ad"></div>

예시

첫 번째 명령은 nginx 컨테이너를 실행하는 Pod 객체를 생성합니다. 두 번째 명령

nginx 컨테이너를 실행하는 Pod 객체를 생성합니다:

```js
kubectl run nginx --image=nginx
```

<div class="content-ad"></div>

nginx 컨테이너를 실행하는 ReplicaSets가 있는 Deployment 객체를 만들어보세요:

```js
kubectl create deployment nginx --image nginx
```

Imperative 명령어들은 보통 사용하기 쉽습니다. 학습이나 테스트 프로젝트에는 훌륭한데, Git과 같은 버전 관리 시스템에서 시스템 변경사항을 추적할 수 없기 때문에 프로덕션 시스템에서는 일반적으로 피해야합니다.

## 명령어를 사용한 객체 구성

<div class="content-ad"></div>

명령형 객체 구성에서는 kubectl 명령어를 사용하여 작업(생성, 대체 등), 선택적 플래그 및 하나 이상의 파일 이름을 지정합니다.

예시

구성 파일에 정의된 객체를 생성합니다:

```js
kubectl create -f config.yaml
```

<div class="content-ad"></div>

두 구성 파일에서 정의된 객체를 삭제합니다:

```js
kubectl delete -f config1.yaml -f config2.yaml
```

## 선언적 객체 구성

명령형 명령어와는 달리 객체에 대한 작업을 수행하기 위해 정확한 단계를 올바른 순서대로 수행해야 하는 절차적 방법과 달리, 선언적 접근 방식은 선언적 매니페스트 파일에서 객체의 원하는 상태를 선언하고 Kubernetes가 kubectl applycommand를 사용하여 객체의 원하는 상태를 달성하도록 관리합니다.

<div class="content-ad"></div>

예시

configs 디렉토리에 있는 모든 객체 구성 파일을 처리하고 라이브 객체를 생성하거나 패치합니다. 무엇이 변경될 것인지 먼저 확인하고 적용할 수 있습니다:

```js
kubectl diff -f configs/
kubectl apply -f configs/
```

디렉토리를 재귀적으로 처리합니다.

<div class="content-ad"></div>

```js
kubectl diff -R -f configs/
kubectl apply -R -f configs/
```

딱지 복제 파일은 YAML 또는 JSON으로 작성되며 Kubernetes 객체의 원하는 상태를 정의합니다. 선언적 매니페스트가 클러스터에 적용되면 Kubernetes는 객체의 현재 상태와 원하는 상태를 비교하여 원하는 상태를 달성하기 위해 필요한 변경을 수행합니다.

다음은 선언적 매니페스트 예시입니다 — configs/nginx-deployment.yaml

```js
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx-container
          image: nginx:latest
          ports:
            - containerPort: 8080
```

<div class="content-ad"></div>

이 manifest는 nginx:latest Docker 이미지를 기반으로 하는 컨테이너의 복제본을 두 개 실행해야 한다는 배포를 설명합니다.

이렇게 하면, 우리는 많은 선언적 manifest 파일들을 직접적으로 구성에 추가하고, 모두를 하나의 kubectl apply -f configs/ 명령어로 적용할 수 있습니다.

일반적으로 선언적 접근 방식은 변경 사항을 버전 관리 시스템에서 추적할 수 있게 하며, 코드 리뷰를 가능하게 하고 변경 사항을 CI/CD 파이프라인에서 자동으로 적용하는 것을 가능하게 합니다.

이 글이 마음에 드셨다면 팔로우 버튼을 눌러 주세요. 더 이상의 이와 유사한 글을 읽고 싶으시다면요