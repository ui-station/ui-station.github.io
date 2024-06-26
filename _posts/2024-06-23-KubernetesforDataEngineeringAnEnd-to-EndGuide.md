---
title: "데이터 엔지니어링을 위한 Kubernetes 처음부터 끝까지 완벽 가이드"
description: ""
coverImage: "/assets/img/2024-06-23-KubernetesforDataEngineeringAnEnd-to-EndGuide_0.png"
date: 2024-06-23 23:03
ogImage:
  url: /assets/img/2024-06-23-KubernetesforDataEngineeringAnEnd-to-EndGuide_0.png
tag: Tech
originalTitle: "Kubernetes for Data Engineering: An End-to-End Guide"
link: "https://medium.com/towards-data-engineering/kubernetes-for-data-engineering-an-end-to-end-guide-26c741a8c013"
---

최근 몇 년간 데이터 엔지니어링이 크게 발전하였는데, Kubernetes가 이 분야에서 중요한 기술로 부상하였습니다. Kubernetes는 확장 가능하고 효율적인 애플리케이션 배포를 포함한 데이터 파이프라인 및 워크플로우의 효율적인 구축과 관리를 지원하는 오픈 소스 컨테이너 오케스트레이션 플랫폼입니다. 이 글에서는 Docker에서 Kubernetes 설정, kubectl로 클러스터 관리, Kubernetes 대시보드 배포, 그리고 Helm 차트를 사용해 Apache Airflow를 실행하는 방법에 대해 알아보겠습니다.

![KubernetesforDataEngineeringAnEnd-to-EndGuide_0](/assets/img/2024-06-23-KubernetesforDataEngineeringAnEnd-to-EndGuide_0.png)

# Kubernetes란 무엇인가요?

Kubernetes는 K8s로 약칭되며, 애플리케이션 컨테이너를 자동으로 배포, 확장 및 운영하기 위해 설계된 오픈 소스 플랫폼입니다. 원래 구글에서 개발되었으며 현재는 Cloud Native Computing Foundation에서 유지보수하고 있습니다. Kubernetes는 견고한 기능과 광범위한 커뮤니티 지원으로 컨테이너 오케스트레이션의 표준으로 자리매깁니다.

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

# 쿠버네티스의 핵심 개념

쿠버네티스를 이해하기 위해 그 핵심 개념을 살펴보겠습니다:

- 컨테이너: 쿠버네티스는 컨테이너화를 기반으로 구축되어 있습니다. 컨테이너는 응용 프로그램을 모든 종속성과 실행 환경과 함께 패키징합니다. 이는 다양한 개발, 테스트 및 프로덕션 환경에서 일관성을 유지합니다.
- 파드: 쿠버네티스에서 가장 작은 배포 가능한 단위입니다. 파드는 하나 이상의 컨테이너를 포함할 수 있으며, 이 컨테이너들은 저장소, 네트워크 및 실행 방법에 대한 사양을 공유합니다. 파드는 순간적이고 임시적입니다.
- 노드: 쿠버네티스에서의 워커 머신으로서, 물리적 또는 가상 머신이 될 수 있습니다. 각 노드는 파드를 실행하고, 마스터 노드에 의해 관리됩니다. 노드에는 파드를 실행하기 위한 필요한 서비스가 포함되어 있으며, 컨트롤 플레인에 의해 관리됩니다.
- 컨트롤 플레인: 쿠버네티스 노드를 제어하는 프로세스들의 모음입니다. 모든 작업 할당은 여기에서 시작됩니다. 컨트롤 플레인의 구성 요소들은 클러스터에 대한 전역 결정(예: 스케줄링)을 내리고, 클러스터 이벤트(예: 배포의 레플리카 필드가 충족되지 않았을 때 새로운 파드 시작)를 감지하고 대응합니다.
- 서비스: 쿠버네티스 서비스는 논리적인 일련의 파드와 그에 대한 액세스 정책을 정의하는 추상화 계층입니다. 이는 일부 파드에 네트워크 액세스를 제공하는 데 자주 사용됩니다.
- 배포: 파드 및 레플리카셋에 대한 선언적 업데이트를 관리하는 고수준 개념입니다. 배포는 파드에 대한 템플릿을 사용하고 파드 수, 롤링 업데이트 전략 및 원하는 상태에 대한 제어 매개변수를 확장합니다.

만일 전체 비디오에 관심이 있으시다면, [여기](#)를 클릭해서 보실 수 있습니다.

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

# 데이터 엔지니어링에서 Kubernetes 이해하기

실제 측면에 들어가기 전에, 데이터 엔지니어링에서 Kubernetes가 왜 게임 체인저인지 이해하는 것이 중요합니다:

- 확장성: Kubernetes는 수요에 따라 데이터 처리 워크로드를 자동으로 확장할 수 있습니다.
- 내구성: 실패를 효과적으로 관리하여 데이터 파이프라인의 높은 가용성을 보장합니다.
- 자원 최적화: Kubernetes는 기본 리소스의 사용을 최적화하여 비용을 절감합니다.
- 이식성과 일관성: 서로 다른 배포 플랫폼에서도 일관된 환경을 제공합니다.

# 연결 기술은 어떨까요?

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

우리 Kubernetes 클러스터를 관리하고 제어하며 데이터 엔지니어링 프로세스를 용이하게 하기 위해 Kubernetes와 함께 사용될 다른 기술들에 대해 이야기해보려고 해요. 이 기술들은 Docker, Kubectl, Helm 등을 포함합니다.

## Docker

Docker는 컨테이너 내에서 애플리케이션을 개발, 배포 및 실행하는 플랫폼입니다. 컨테이너화는 애플리케이션을 해당 애플리케이션만을 위한 자체 운영 환경과 함께 컨테이너에 묶는 완전한 가상화의 가벼운 대안입니다.

- 컨테이너: 애플리케이션의 코드, 구성 및 종속성을 하나의 객체로 패키징하는 표준 방법을 제공합니다.
- 이미지: 코드, 런타임, 라이브러리, 환경 변수 및 구성 파일 등 애플리케이션을 실행하는 데 필요한 모든 것이 포함된 실행 가능한 패키지입니다.

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

# 쿠버네티스 클러스터 관리

쿠버네티스 클러스터를 관리하는 것은 각각이 클러스터의 라이프사이클 및 운영에 특정 목적을 제공하는 다양한 도구와 기술을 포함합니다. 저는 쿠버네티스 클러스터를 제어하고 관리하는 데 널리 사용되는 세 가지 주요 기술을 강조하기로 결정했습니다.

## 1. kubectl

- 목적: kubectl은 쿠버네티스 API와 상호 작용하는 명령줄 도구입니다. 이는 쿠버네티스 클러스터를 관리하는 주요 인터페이스입니다.
- 기능: 애플리케이션을 배포하고 클러스터 리소스를 검사하고 관리하며 로그를 보고 팟에서 명령을 실행하는 등의 기능을 제공합니다.
- 사용법: kubectl 명령은 간단합니다. 예를 들어, kubectl get pods는 현재 네임스페이스의 모든 팟을 나열하거나 kubectl apply -f deployment.yaml은 파일에서 구성을 적용하는 것과 같은 명령입니다.

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

## 2. 미니큐브

- 목적: 미니큐브는 쿠버네티스를 로컬에서 실행할 수 있게 해주는 도구입니다. 로컬 머신에서 단일 노드 쿠버네티스 클러스터를 생성합니다.
- 사용 사례: 개발 및 테스트 목적으로 이상적입니다. 개발자들이 자신의 머신에서 쿠버네티스 환경에서 애플리케이션을 테스트할 수 있도록 합니다.
- 특징: DNS, 노드 포트, 구성 맵 및 시크릿, 대시보드, 컨테이너 런타임 등 다양한 쿠버네티스 기능을 지원합니다.

## 3. 쿠버엠톰

- 목적: 쿠버엠톰은 쿠버네티스 클러스터를 생성하고 관리하기 위해 kubeadm init 및 kubeadm join을 제공하는 도구입니다.
- 기능: 쿠버네티스 클러스터의 부트스트래핑, 제어 플레인 설정, 토큰 관리, kubeadm 설정 등을 처리합니다.
- 사용법: 일반 클러스터 설정 과정을 간소화하여, 쿠버네티스에 처음 입문하는 사람들도 접근하기 쉽게 만듭니다.

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

이 문서에서는 kubectl을 사용할 것입니다. 귀하의 운영 체제 및 버전을 선택하여 기기에 설치할 수 있습니다.

만약 minikube 또는 kubeadm을 사용하여 Kubernetes 클러스터를 관리하고 싶다면, 각각 여기와 여기의 빠른 시작 가이드를 따를 수 있습니다.

설치가 완료된 후에는 다음을 실행하여 설치를 확인할 수 있습니다.

```js
kubectl version
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

제 경우에는 kubectl 버전 1.29.1을 실행 중입니다. 당신이 프로세스를 실행하는 시기에 따라 더 높을 수 있습니다.

![이미지](/assets/img/2024-06-23-KubernetesforDataEngineeringAnEnd-to-EndGuide_1.png)

# Docker에서 Kubernetes 설정하기

Docker에서 Kubernetes를 설정하는 것은 Docker가 컨테이너화된 애플리케이션을 실행하는 로컬 Kubernetes 클러스터를 생성하는 과정을 포함합니다. 이 설정은 개발 및 테스트 목적으로 특히 유용합니다. Docker에서 Kubernetes를 설정하는 단계별 가이드는 다음과 같습니다:

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

# 준비 사항

- 도커: 시스템에 도커가 설치되어 있는지 확인해주세요. Windows 및 Mac용 도커 데스크톱은 쿠버네티스 지원이 기본 내장되어 있습니다.
- 하드웨어 요구 사항: 여러 컨테이너를 실행하기 위한 충분한 CPU, 메모리 및 저장 공간이 필요합니다.

# 설정 단계

## 1. 도커 데스크톱 설치

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

- 다운로드 및 설치: 공식 Docker 웹사이트에서 Docker Desktop을 다운로드하세요. 제공된 지침을 따라 컴퓨터에 설치해주세요.
- Kubernetes 활성화: Docker Desktop에는 로컬 컴퓨터에서 실행되는 독립형 Kubernetes 서버가 포함되어 있습니다. 이를 활성화하기 전에 초기 기본 구성에서 리소스를 약간 늘려주어야 합니다.

![이미지](/assets/img/2024-06-23-KubernetesforDataEngineeringAnEnd-to-EndGuide_2.png)

Docker Desktop에서 Kubernetes 활성화 방법:

- Docker Desktop 설정을 엽니다.
- Kubernetes 섹션을 찾습니다.
- “Kubernetes 활성화”란에 체크합니다.
- 변경사항을 저장하려면 “적용 및 다시 시작”을 클릭합니다.

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

아래는 Markdown 형식으로 변경하겠습니다.

![이미지](/assets/img/2024-06-23-KubernetesforDataEngineeringAnEnd-to-EndGuide_3.png)

## 2. 설치 확인

- 도커 확인: 터미널이나 명령 프롬프트를 열고 docker --version을 실행하여 도커가 올바르게 설치되었는지 확인합니다.

## 3. 쿠버네티스 컨텍스트 구성

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

- 쿠버네티스는 서로 다른 클러스터에 액세스하기 위해 컨텍스트를 사용합니다. 도커 데스크탑은 docker-desktop이라는 컨텍스트를 설정합니다.
- 이 컨텍스트로 전환하려면 kubectl config use-context docker-desktop을 사용하십시오.

# PC에 헬름 차트 설치하는 방법

## macOS용:

- Homebrew: Homebrew를 설치한 경우, 간단히 다음을 실행할 수 있습니다:

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
brew install helm
```

## Windows 사용자분들을 위해:

- Chocolatey를 사용하는 경우, 다음 명령어를 실행하여 Helm을 설치할 수 있습니다:

```js
choco install kubernetes-helm
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

## 리눅스용:

- 스크립트: Helm은 리눅스 사용자를 위한 자동화된 스크립트를 제공합니다. 다음을 실행하세요:

```js
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

만약 다른 버전의 운영 체제, 패키지 관리자를 원하시거나 소스에서 직접 빌드하고 싶다면 공식 가이드의 지침을 따를 수 있습니다.

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

헬름이 컴퓨터에 설치되면 다음을 실행하여 설치를 확인할 수 있습니다:

```js
helm version
```

아래 스크린샷과 유사한 내용이 표시됩니다. 저의 경우에는 헬름 3.14.0을 실행하고 있습니다.

<img src="/assets/img/2024-06-23-KubernetesforDataEngineeringAnEnd-to-EndGuide_4.png" />

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

# Kubernetes 대시보드 배포하기

Kubernetes 대시보드는 Kubernetes 클러스터를 관리하는 사용자 친화적인 웹 기반 인터페이스를 제공합니다. 클러스터 리소스와 애플리케이션을 보고 관리할 수 있으며 기본적인 문제 해결 기능도 제공합니다. Kubernetes 대시보드를 배포하는 방법은 다음과 같습니다:

## 단계 1: 대시보드 배포하기

- 배포 명령 실행: Kubernetes 대시보드를 배포하려면 kubectl을 사용하여 yaml 구성을 배포하세요.

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
kubectl apply -f https://raw.githubusercontent.com/airscholar/Kubernetes-For-DataEngineering/main/k8s/recommended-dashboard.yaml
```

이 명령은 Kubernetes 대시보드의 GitHub 저장소에서 권장 배포 구성을 다운로드하고 적용합니다. 보게 될 내용은 다음과 같아야 합니다.

# 단계 2: 대시보드에 액세스하기

- 프록시 시작: Kubernetes 대시보드는 프록시 서버를 통해 액세스됩니다. 다음 몤령을 사용하여 프록시를 시작합니다.

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
kubectl proxy
```

이 명령어를 실행하면 대시보드를 로컬 머신에서 URL을 통해 접속할 수 있습니다.

![image](/assets/img/2024-06-23-KubernetesforDataEngineeringAnEnd-to-EndGuide_5.png)

2. URL에 접속하세요: 웹 브라우저를 열고 다음 URL로 이동하세요:

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
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

이 URL은 Kubernetes 대시보드 인터페이스로 이동합니다.

# 단계 3: 대시보드 인증하기

- Bearer 토큰 받기: 대시보드에 로그인하려면 Bearer 토큰을 생성해야 합니다. 다음 단계를 따라 서비스 계정을 생성하고 토큰을 받을 수 있습니다:

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

- 다음 내용으로 dashboard-adminuser.yaml이라는 파일을 생성하세요:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```

- 다음 내용으로 dashboard-clusterrole.yaml이라는 파일을 생성하세요:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: admin-user
    namespace: kubernetes-dashboard
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

- 그리고 마지막으로 아래 내용을 가진 dashboard-secret.yaml 파일을 생성해주세요.

```js
apiVersion: v1
kind: Secret
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
  annotations:
    kubernetes.io/service-account.name: "admin-user"
type: kubernetes.io/service-account-token
```

이 구성을 적용하려면 다음 명령을 실행해야합니다.

```js
kubectl apply -f dashboard-adminuser.yaml
kubectl apply -f dashboard-clusterrole.yaml
kubectl apply -f dashboard-secret.yaml
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

아래는 대시보드에 액세스할 때 사용할 토큰을 생성하는 방법입니다:

```js
kubectl get secret admin-user -n kubernetes-dashboard -o jsonpath={".data.token"} | base64 -d
```

만약 CLI를 사용하는 것을 선호한다면, 대신에 다음 명령어를 실행할 수도 있습니다.

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

- kube-system 네임스페이스에 서비스 계정을 생성해 주세요:

```js
kubectl create serviceaccount admin-user -n kubernetes-dashboard
```

- 서비스 계정을 클러스터 관리자 역할에 바인딩해 주세요:

```js
kubectl create clusterrolebinding admin-user --clusterrole=cluster-admin --serviceaccount=kubernetes-dashboard:admin-user
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

- 다음 명령을 사용하여 비밀을 가져오세요:

```js
kubectl get secret $(kubectl get serviceaccount admin-user -n kubernetes-dashboard -o jsonpath="{.secrets[0].name}") -n kubernetes-dashboard -o jsonpath="{.data.token}" | base64 --decode
```

그런 다음 대시보드에 로그인하려면 토큰을 사용하세요:

## 단계 4: 대시보드 사용하기

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

- 대시보드 탐색하기: 로그인하면 배포, 서비스 및 팟과 같은 Kubernetes 자원을 보거나 관리할 수 있습니다.
- 자원 생성 및 수정: 대시보드 UI를 통해 직접 새로운 자원을 생성하거나 기존 자원을 수정할 수 있습니다.
- 클러스터 및 애플리케이션 성능 모니터링: 대시보드를 통해 클러스터 전체 및 CPU 및 메모리 사용량을 포함한 개요를 제공합니다.

모든 소셜 미디어 플랫폼에서 팔로우를 눌러주시고 지지를 보여주기 위해 박수를 보내고 댓글을 달아주세요.

- Github: airscholar
- Twitter: @YusufOGaniyu
- LinkedIn: Yusuf Ganiyu
- Youtube: CodeWithYu

이제 Kubernetes 대시보드를 성공적으로 배포했으므로, 차례로 Kubernetes 클러스터에 Apache Airflow를 배포할 준비를 해야 합니다.

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

# 쿠버네티스 클러스터에 Apache Airflow 배포하기

쿠버네티스 클러스터에 Apache Airflow를 배포하는 것은 강력한 스케줄링 및 워크플로우 관리 기능을 확장 가능한 환경에서 활용하는 훌륭한 방법입니다. Apache Airflow를 쿠버네티스에 배포하는 가장 효율적인 방법은 Helm을 사용하는 것입니다. Helm은 쿠버네티스 응용 프로그램의 설치 및 관리를 간소화하는 패키지 매니저입니다. 이제 함께 Apache Airflow를 쿠버네티스에 배포하는 과정을 살펴보겠습니다.

## 요구 사항

다음 요구 사항은 문제 없이 진행하기 위해 필요합니다. 이전 부분을 건너 뛰었다면, 계속 진행하기 전에 모든 것이 잘 작동하는지 다시 확인하는 것이 좋습니다.

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

- 실행 중인 Kubernetes 클러스터가 필요합니다.
- 로컬 머신에 Helm이 설치되어 있어야 합니다 (필요한 경우 Helm 설치 방법은 이전 지침을 참조하십시오).
- Kubernetes 클러스터와 통신할 수 있도록 kubectl이 구성되어 있어야 합니다.

## 단계 1: Airflow Helm Chart 저장소 추가

먼저, Apache Airflow 공식 Helm 차트 저장소를 Helm 설치에 추가해야 합니다:

```sh
helm repo add apache-airflow https://airflow.apache.org
helm repo update
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

이 명령어는 Airflow 리포지토리를 추가하고 로컬 Helm 차트 리포지토리 인덱스를 업데이트합니다.

# 단계 2: Apache Airflow 설치

다음 명령어로 Apache Airflow를 Helm 차트를 사용하여 설치할 수 있습니다:

```js
helm install airflow apache-airflow/airflow --namespace airflow --create-namespace --debug
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

- airflow은 릴리스 이름으로, 원하는대로 변경할 수 있습니다.
- --namespace airflow는 Airflow를 설치할 Kubernetes 네임스페이스를 지정합니다. 네임스페이스가 존재하지 않는 경우, --create-namespace를 사용하여 생성하고 --debug를 사용하면 배포 단계를 볼 수 있습니다.

이 명령은 웹 서버, 스케줄러 등 필요한 모든 구성 요소와 함께 Airflow를 배포합니다. 디버그 플래그가 있으면 Apache Airflow 릴리스를 배포하는 데 사용된 세부 정보, 구성 및 해당 값들을 볼 수 있습니다.

배포 중에는 대기 중인 작업 및 해당 상태를 쿠버네티스 클러스터에서 볼 수 있습니다. 프로세스가 완료되면 작업은 대기열에서 제거되어 더 이상 보이지 않게 됩니다.

재미있게도 Apache Airflow 릴리스는 완료되었지만 보통 localhost:8080으로 접근해도 여전히 접속할 수 없습니다. 이를 가능하게 하고 Apache Airflow 파드에 대한 연결을 처리하려면 아래 명령을 실행한 다음 UI에서 Apache Airflow 배포에 액세스해야 합니다:

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
kubectl port-forward svc/airflow-webserver 8080:8080 --namespace airflow
```

또한, 쿠버네티스 클러스터에서 Airflow 배포의 건강 상태를 확인하고 모니터링하는 것이 중요합니다. 모든 것이 잘 되고 준비되어 있는지 확인하기 위해 위의 네임스페이스를 airflow로 변경해주시면 워크로드 세부 정보를 볼 수 있습니다.

이제 관리자 사용자 이름과 관리자 비밀번호로 Airflow UI에 액세스할 수 있습니다.

화면 상단에 동적 웹서버 비밀 키에 관한 경고가 있는 경우 정적 웹서버 비밀 키를 사용하는 것이 좋습니다.

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

## 동적 키 대신 정적 키 사용하는 이유

개발 환경에서는 동적 웹 서버 비밀 키를 사용해도 상관없지만, 제품 환경에서는 정적 웹 서버 비밀 키를 사용하는 것이 강력히 권장됩니다. 그 이유는 다음과 같습니다:

- 응용 프로그램 인스턴스 간의 일관성: 특히 쿠버네티스로 관리되는 제품 환경에서는 응용 프로그램이 여러 인스턴스로 확장될 수 있습니다. 각 인스턴스가 자체 동적 비밀 키를 생성하는 경우 세션, 쿠키 또는 기타 암호화된 데이터 처리에 일관성이 무너질 수 있습니다. 정적 키는 응용 프로그램의 모든 인스턴스가 데이터를 일관적으로 읽고 쓸 수 있도록 보장합니다.
- 다시 시작 시 지속성: 웹 서버가 다시 시작될 때 새로운 동적 비밀 키를 생성하면 이전 키로 암호화된 모든 세션 및 쿠키가 무효화됩니다. 이는 갑자기 로그아웃되거나 세션 데이터를 잃어버린 사용자에게 혼란을 줄 수 있습니다. 정적 키는 다시 시작할 때 동일하게 유지되어 세션의 연속성을 유지합니다.
- 보안 최상의 사례: 역설적으로 보일 수 있지만, 안전하게 저장된 정적 비밀 키를 사용하는 것이 동적으로 생성하는 것보다 보안상 더 안전할 수 있습니다. 완전히 무작위이고 안전한 키를 생성하는 것은 쉽지 않습니다. 잘못 생성된 동적 키가 선정된 정적 키보다 덜 안전할 수 있습니다.
- 구성 관리: 정적 키는 안전한 구성 관리 관행을 통해 관리할 수 있습니다. 이는 HashiCorp Vault, AWS Secrets Manager 또는 Kubernetes Secrets와 같은 비밀 관리 시스템에 키를 저장하는 것을 포함합니다. 이렇게 하면 키가 코드나 안전하지 않은 구성에서 노출되지 않고 액세스를 엄격하게 제어할 수 있습니다.
- 감사 및 규정 준수: 많은 규정 준수 환경에서는 비밀 키에 대한 감사 및 액세스 제어가 필요합니다. 정적 키를 사용하면 이러한 제어를 구현하고 누가 액세스 권한을 가지고 있는지 추적하는 것이 더 쉬워집니다.

이를 해결하기 위해, Apache Airflow 릴리스에 웹 서버 비밀 키를 포함하여 재구성하겠습니다. 이를 위해 원하는 구성을 덮어쓸 values.yaml 파일을 생성할 수 있습니다.

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

키를 생성하는 다양한 방법이 있지만 가장 인기 있는 두 가지 방법을 강조하겠습니다:

- 파이썬 암호화 라이브러리를 사용하여 키를 생성하는 간단한 코드를 작성할 수 있습니다.

- 먼저, cryptography 라이브러리가 설치되어 있는지 확인하십시오:

```js
pip install cryptography
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

- 그럼 당신의 Python 환경에서 다음 한 줄을 사용할 수 있어요:

```js
python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"
```

2. airflow 페르네 키 사용:

```js
 echo Fernet Key: $(kubectl get secret --namespace airflow
 airflow-fernet-key -o jsonpath="{.data.fernet-key}" | base64 --decode)
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

한번 생성되면 다음과 같이 보일 것입니다. 이 값들로 values.yaml 파일을 업데이트할 수 있어요.

```js
fernetKey: aERBZE5MN3E0TjRjU2xzQWxCdTNIUks0WGFTZThoWXc=
webserverSecretKey: aERBZE5MN3E0TjRjU2xzQWxCdTNIUks0WGFTZThoWXc=
```

그런 다음 helm 차트 배포를 다시 업데이트하세요:

```js
helm upgrade --install airflow apache-airflow/airflow --namespace airflow --create-namespace -f values.yaml
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

UI에서 변경 사항을 미리보기하면 경고 메시지가 사라진 것을 확인할 수 있습니다.

# 쿠버네티스에서 Airflow의 DAG 연결

쿠버네티스에 Apache Airflow를 성공적으로 배포한 후에 해야 할 다음 단계는 DAG를 해당 Airflow에 연결하는 것입니다.

우리의 경우, GitHub 저장소에서 DAG를 Airflow에 연결하게 됩니다. 거기에 Airflow DAG 코드를 작성하고 커밋한 후에 해당 커밋으로부터 URL을 얻어 Kubernetes와 동기화할 것입니다. 아래에 제시된 대로 진행됩니다.

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

Here is the translation into Korean:

```python
from airflow import DAG
from airflow.operators.bash_operator import BashOperator
from datetime import datetime, timedelta

default_args = {
    'owner': 'datamasterylab.com',
    'start_date': datetime(2024, 1, 25),
    'catchup': False
}

dag = DAG(
    'hello_world',
    default_args=default_args,
    schedule_interval=timedelta(days=1)
)

t1 = BashOperator(
    task_id='hello_world',
    bash_command='echo "Hello World"',
    dag=dag
)

t2 = BashOperator(
    task_id='hello_dml',
    bash_command='echo "Hello Data Mastery Lab"',
    dag=dag
)

t1 >> t2

```

```python
from datetime import datetime, timedelta

from airflow import DAG
from airflow.operators.python_operator import PythonOperator


def get_data(**kwargs):
    import requests
    import pandas as pd

    url = 'https://raw.githubusercontent.com/airscholar/ApacheFlink-SalesAnalytics/main/output/new-output.csv'
    response = requests.get(url)

    if response.status_code == 200:
        df = pd.read_csv(url, header=None, names=['Category', 'Price', 'Quantity'])

        # 데이터프레임을 JSON 문자열로 변환하여 xcom으로 전달
        json_data = df.to_json(orient='records')

        kwargs['ti'].xcom_push(key='data', value=json_data)
    else:
        raise Exception(f'데이터를 가져오는 데 실패했습니다. HTTP 상태 코드: {response.status_code}')


def preview_data(**kwargs):
    import pandas as pd
    import json

    output_data = kwargs['ti'].xcom_pull(key='data', task_ids='get_data')
    print(output_data)
    if output_data:
        output_data = json.loads(output_data)
    else:
        raise ValueError('XCom으로부터 데이터를 받지 못했습니다.')

    # JSON 데이터에서 데이터프레임 생성
    df = pd.DataFrame(output_data)

    # 총 판매량 계산
    df['Total'] = df['Price'] * df['Quantity']

    df = df.groupby('Category', as_index=False).agg({'Quantity': 'sum', 'Total': 'sum'})

    # 총 판매량을 기준으로 정렬
    df = df.sort_values(by='Total', ascending=False)

    print(df[['Category', 'Total']].head(20))


default_args = {
    'owner': 'datamasterylab.com',
    'start_date': datetime(2024, 1, 25),
    'catchup': False
}

dag = DAG(
    'fetch_and_preview',
    default_args=default_args,
    schedule_interval=timedelta(days=1)
)

get_data_from_url = PythonOperator(
    task_id='get_data',
    python_callable=get_data,
    dag=dag
)

preview_data_from_url = PythonOperator(
    task_id='preview_data',
    python_callable=preview_data,
    dag=dag
)

get_data_from_url >> preview_data_from_url

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

위에서 commited된 저장소는 다음과 같습니다:

```js
https://github.com/airscholar/Kubernetes-For-DataEngineering.git
```

이제 우리의 values.yaml 파일로 돌아가서 저장소와 동기화할 값을 업데이트해봅시다.

```js
fernetKey: aERBZE5MN3E0TjRjU2xzQWxCdTNIUks0WGFTZThoWXc=
webserverSecretKey: aERBZE5MN3E0TjRjU2xzQWxCdTNIUks0WGFTZThoWXc=

dags:
  gitSync:
    enabled: true
    repo: https://github.com/airscholar/Kubernetes-For-DataEngineering.git
    branch: main
    rev: HEAD
    depth: 1
    maxFailures: 0
    subPath: "dags"
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

하루 마무리로 우리는 구성을 다시 적용하고 helm 차트로 릴리스를 업데이트할 거에요!

```js
helm upgrade --install airflow apache-airflow/airflow --namespace airflow --create-namespace -f values.yaml
```

UI에서 그 모습을 확인해보죠.

마지막으로, 이 DAG들을 트리거하고 해당 로그에서 출력을 확인해봅시다.

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

# 이제 마무리입니다!

아래 주제 중에 관심이 있는 분은:

- Python
- 데이터 엔지니어링
- 데이터 분석
- 데이터 과학
- SQL
- 클라우드 플랫폼 (AWS/GCP/Azure)
- 머신러닝
- 인공지능

제 모든 플랫폼을 좋아요와 팔로우 해주세요:

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

- Github: airscholar
- Twitter: @YusufOGaniyu
- LinkedIn: Yusuf Ganiyu
- Youtube: CodeWithYu
- Medium: Yusuf Ganiyu

나는 LinkedIn, X, Medium 및 YouTube에서 매일 콘텐츠를 공유합니다.

datamasterylab.com에서 더 많은 코스를 확인하실 수 있습니다.

# 자료들

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

깃허브 풀 코드

유튜브 비디오

엔드 투 엔드 데이터 엔지니어링 재생 목록

# 스택데믹

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

끝까지 읽어 주셔서 감사합니다. 떠나시기 전에:

- 축소 버튼을 클릭해 주시고 작가를 팔로우해 주세요! 👏
- X사의 팔로우 | LinkedIn | YouTube | Discord
- 다른 플랫폼도 방문해 보세요: In Plain English | CoFeed | Venture
