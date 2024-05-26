---
title: " 쿠버네티스에서 Vault 사용 방법 안내 "
description: ""
coverImage: "/assets/img/2024-05-23-AHand-OnGuidetoVaultinKubernetes_0.png"
date: 2024-05-23 14:19
ogImage:
  url: /assets/img/2024-05-23-AHand-OnGuidetoVaultinKubernetes_0.png
tag: Tech
originalTitle: "⎈ A Hand-On Guide to Vault in Kubernetes ⚙️"
link: "https://medium.com/@muppedaanvesh/a-hand-on-guide-to-vault-in-kubernetes-%EF%B8%8F-1daf73f331bd"
---

## ⇢ 실용적인 예제로 HashiCorp Vault를 사용하여 k8s Secrets 관리하기

![image](/assets/img/2024-05-23-AHand-OnGuidetoVaultinKubernetes_0.png)

쿠버네티스 세계에서 API 키, 비밀번호 및 기타 중요 정보와 같은 보안 정보를 관리하는 것은 매우 중요한 작업입니다. 쿠버네티스에는 내장된 보안 정보 관리 메커니즘이 있지만, 모든 조직의 보안 요구 사항을 충족시키지 못할 수도 있는 특정 제한 사항이 있습니다. 예를 들어, 쿠버네티스 보안 정보는 etcd에 저장되며, 이는 휴식 중 암호화되어 있지만, 매우 중요한 정보에 필요한 보안 수준과 접근 제어를 제공하지 못할 수 있습니다.

이때 HashiCorp Vault가 등장합니다. Vault는 중요한 정보를 안전하게 저장하고 관리하기 위해 설계된 도구입니다. 동적 보안 정보, 서비스로의 암호화 및 접근 제어를 위한 견고한 메커니즘을 제공하여, 쿠버네티스 환경에서 보안 정보를 관리하기에 이상적인 솔루션입니다.

<div class="content-ad"></div>

이 튜토리얼에서는 Helm을 사용하여 쿠버네티스 클러스터에 Vault를 설치하고 구성하는 단계를 안내합니다. 그리고 Pod를 배포하여 Vault에서 비밀을 액세스할 수 있도록합니다. 이 안내서를 마치면 쿠버네티스 클러스터에 작동하는 Vault 설정이 완료되어 응용 프로그램 비밀을 안전하게 관리할 수 있습니다.

# 전제 조건

시작하기 전에 다음 사항을 확인하세요:

- 실행 중인 쿠버네티스 클러스터가 있어야 합니다.
- 클러스터와 상호 작용하도록 구성된 kubectl이 있어야 합니다.
- 로컬 머신에 Helm이 설치되어 있어야 합니다.

<div class="content-ad"></div>

<img src="https://miro.medium.com/v2/resize:fit:1400/1*jmt8bsoEGeVHv5ZUP7XY_Q.gif" />

# 보르트(Namespace) 네임스페이스 생성하기

우선, 보르트(Vault)를 위한 별도의 네임스페이스를 생성해야 합니다. 이렇게 하면 보르트에 특화된 리소스를 독립적으로 관리할 수 있습니다.

```js
$ kubectl create ns vault
```

<div class="content-ad"></div>

# Vault 설치

우리는 HashiCorp가 제공하는 Helm 차트를 사용하여 Vault의 최신 버전을 설치할 것입니다. 이 작업을 수행하는 두 가지 방법이 있습니다: 1. HashiCorp Helm 리포지토리를 사용하여 직접 Helm 설치 명령어를 실행하거나 2. Helm 차트를 다운로드하여 로컬로 설치하는 방법이 있습니다.

## 1. HashiCorp Helm 리포지토리 추가

HashiCorp Helm 리포지토리를 Helm 구성에 추가해보세요.

<div class="content-ad"></div>

```js
helm repo add hashicorp https://helm.releases.hashicorp.com
```

## 2. 설치 방법

1. 직접 Helm 설치 실행하기

다음 명령어를 사용하여 HashiCorp 저장소에서 Helm 차트를 사용하여 Vault를 직접 설치할 수 있습니다:

<div class="content-ad"></div>

```js
helm install vault hashicorp/vault \
       --set='server.dev.enabled=true' \
       --set='ui.enabled=true' \
       --set='ui.serviceType=LoadBalancer' \
       --namespace vault
```

2. Helm 차트 다운로드 및 설치

대안으로 Helm 차트를 다운로드하고 로컬로 설치할 수 있습니다:

```js
# Helm 차트 다운로드
helm pull hashicorp/vault --untar

# 다운로드한 차트를 사용하여 Vault 설치
helm install vault \
       --set='server.dev.enabled=true' \
       --set='ui.enabled=true' \
       --set='ui.serviceType=LoadBalancer' \
       --namespace vault \
       ./vault-chart
```

<div class="content-ad"></div>

이 설정을 사용하여 UI가 활성화된 Vault를 개발 모드로 설치하고 외부에서 액세스하기 위해 LoadBalancer 서비스를 통해 노출합니다. 이 설정은 테스트 및 개발 목적으로 이상적입니다.

결과:

```js
$ kubectl get all -n vault
NAME                                        READY   STATUS    RESTARTS   AGE
pod/vault-0                                 1/1     Running   0          2m39s
pod/vault-agent-injector-8497dd4457-8jgcm   1/1     Running   0          2m39s

NAME                               TYPE           CLUSTER-IP       EXTERNAL-IP    PORT(S)             AGE
service/vault                      ClusterIP      10.245.225.169   <none>         8200/TCP,8201/TCP   2m40s
service/vault-agent-injector-svc   ClusterIP      10.245.32.56     <none>         443/TCP             2m40s
service/vault-internal             ClusterIP      None             <none>         8200/TCP,8201/TCP   2m40s
service/vault-ui                   LoadBalancer   10.245.103.246   24.132.59.59   8200:31764/TCP      2m40s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/vault-agent-injector   1/1     1            1           2m40s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/vault-agent-injector-8497dd4457   1         1         1       2m40s

NAME                     READY   AGE
statefulset.apps/vault   1/1     2m40s
```

# Vault 구성

<div class="content-ad"></div>

이번 단계에서는 Kubernetes 클러스터 내에서 안전하게 비밀을 관리하고 액세스하기 위해 Vault 정책과 인증 방법을 설정할 것입니다. 이 구성은 인증된 애플리케이션만 Vault에서 민감한 데이터를 검색할 수 있도록 보장합니다.

## 1. Vault Pod에 연결하기

설치가 완료된 후 Vault pod에 연결하여 초기 구성을 수행하세요:

```js
kubectl exec -it vault-0 -- /bin/sh
```

<div class="content-ad"></div>

## 2. 정책 생성 및 적용하기

이제 비밀을 읽을 수 있는 정책을 생성하겠습니다. 이 정책은 역할에 첨부되어 특정 Kubernetes 서비스 계정에 액세스 권한을 부여하는 데 사용될 수 있습니다.

정책 파일을 생성하세요:

```js
cat <<EOF > /home/vault/read-policy.hcl
path "secret*" {
  capabilities = ["read"]
}
EOF
```

<div class="content-ad"></div>

아래와 같이 정책을 적용해주세요:

```js
# 문법
$ vault policy write <정책명> /정책/경로/여기에.hcl

# 예시
$ vault policy write read-policy /home/vault/read-policy.hcl
```

## 3. Kubernetes 인증 활성화

Vault에서 Kubernetes 인증 방법을 활성화하세요.

<div class="content-ad"></div>

```js
vault auth enable kubernetes
```

## 4. Kubernetes 인증 설정

Vault가 Kubernetes API 서버와 통신하도록 구성합니다:

```js
vault write auth/kubernetes/config \
   token_reviewer_jwt="$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" \
   kubernetes_host=https://${KUBERNETES_PORT_443_TCP_ADDR}:443 \
   kubernetes_ca_cert=@/var/run/secrets/kubernetes.io/serviceaccount/ca.crt
```

<div class="content-ad"></div>

## 5. 역할 생성

특정 네임스페이스에 있는 쿠버네티스 서비스 계정(vault-serviceaccount)에 위에서 만든 정책을 바인딩하는 역할(vault-role)을 생성합니다. 이를 통해 서비스 계정이 Vault에 저장된 시크릿에 액세스할 수 있게 됩니다:

```js
vault write auth/kubernetes/role/vault-role \
   bound_service_account_names=vault-serviceaccount \
   bound_service_account_namespaces=vault \
   policies=read-policy \
   ttl=1h
```

여기서 여러 개의 서비스 계정과 네임스페이스를 전달할 수 있습니다:

<div class="content-ad"></div>

```bash
vault write auth/kubernetes/role/<my-role> \
   bound_service_account_names=sa1, sa2 \
   bound_service_account_namespaces=namespace1, namespace2 \
   policies=<policy-name> \
   ttl=1h
```

# 보안 정보 만들기

이제 Vault에 일부 보안 정보를 만들어 보겠습니다:

우리는 두 가지 방법으로 보안 정보를 만들 수 있어요:

<div class="content-ad"></div>

- Vault CLI를 사용하기

## 1. Vault CLI 사용하기

아래 명령어를 사용하여 시크릿을 생성하세요

```js
$ vault kv put secret/login pattoken=ytbuytbytbf765rb65u56rv
```

<div class="content-ad"></div>

아래 명령어를 사용하여 시크릿을 나열하여 비밀을 확인할 수 있습니다:

```js
$ vault kv list secret
Keys
----
login
```

## 2. Vault UI 사용 방법

Vault 네임스페이스에서 서비스를 나열하여 로드 밸런서의 외부 IP를 얻을 수 있습니다.

<div class="content-ad"></div>

```js
$ kubectl get svc -n vault
NAME                       TYPE           CLUSTER-IP       EXTERNAL-IP    PORT(S)             AGE
vault                      ClusterIP      10.245.139.117   <none>         8200/TCP,8201/TCP   28h
vault-agent-injector-svc   ClusterIP      10.245.58.140    <none>         443/TCP             28h
vault-internal             ClusterIP      None             <none>         8200/TCP,8201/TCP   28h
vault-ui                   LoadBalancer   10.245.11.13     24.123.49.59   8200:32273/TCP      26h
```

위의 로드밸런서의 외부 IP를 사용하여 Vault UI에 액세스하실 수 있습니다.

예: `external-ip`:8200

제 경우: 24.123.49.59:8200



<div class="content-ad"></div>



<img src="/assets/img/2024-05-23-AHand-OnGuidetoVaultinKubernetes_1.png" />

이제 토큰 방법을 사용하여 Vault에 로그인할 수 있습니다. 초기에는 Token=root를 사용하여 로그인하십시오.

이제 Vault UI에서 시크릿 대시보드를 사용하여 시크릿을 생성할 수 있습니다.

시크릿 엔진으로 이동하세요 '` 시크릿`


<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-AHand-OnGuidetoVaultinKubernetes_2.png" />

그런 다음 오른쪽 상단의 "비밀 생성(Create Secret)"을 클릭하세요.

<img src="/assets/img/2024-05-23-AHand-OnGuidetoVaultinKubernetes_3.png" />

이제 비밀을 만들기 위해 원하는 필드를 입력하세요.

<div class="content-ad"></div>

아래 명령어를 사용하여 Vault CLI에서 위의 비밀을 액세스할 수도 있습니다:

<div class="content-ad"></div>

```js
$ vault kv list secret
Keys
----
login
my-first-secret
```

쿠버네티스 클러스터에 Vault를 성공적으로 설치하고 구성했습니다. 이제 Vault를 사용하여 쿠버네티스에서 실행 중인 응용 프로그램의 비밀을 관리할 수 있습니다.

# 쿠버네티스 Pod에서 비밀 액세스

위 단계를 사용하여 Vault를 설치하고 Vault 역할(vault-role)을 구성하여 서비스 계정(vault-serviceaccount)이 Vault에 저장된 비밀에 액세스할 수 있도록 했습니다.```

<div class="content-ad"></div>

또한, login과 my-first-secret이라는 키-값 쌍을 가진 두 개의 시크릿을 생성했습니다. 이제 간단한 쿠버네티스 배포를 생성하고 이러한 시크릿에 액세스해 보겠습니다.

먼저, vault 네임스페이스에 vault-serviceaccount라는 서비스 계정을 생성합니다. 이 서비스 계정은 위에서 정의한 "Role 생성" 단계에서 정의된 Vault 역할에 대한 권한이 부여됩니다.

아래 매니페스트 파일을 vault-sa.yaml로 저장합니다.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: vault-serviceaccount
  labels:
    app: read-vault-secret
```

<div class="content-ad"></div>

위 명령을 사용하여 위에 제공된 매니페스트를 적용하세요.

```js
kubectl apply -f vault-sa.yaml
```

이제 아래 매니페스트 파일을 사용하여 간단한 배포(vault-secret-test-deploy.yaml)를 생성해 봅시다.

이 배포 매니페스트는 Vault에서 시크릿을 안전하게 가져오도록 구성된 Nginx 파드의 단일 레플리카를 생성합니다. Vault 에이전트는 지정된 템플릿에 따라 시크릿인 login 및 my-first-secret을 파드에 주입합니다. 시크릿은 파드 파일 시스템에 저장되어 컨테이너에서 실행 중인 응용 프로그램에서 액세스할 수 있습니다. Vault와 인증하기 위해 필요한 권한을 갖고 있는 vault-serviceaccount 서비스 어카운트가 사용됩니다.

<div class="content-ad"></div>

어노테이션 섹션을 자세히 살펴보면 그 목적과 기능을 이해할 수 있습니다.

```js
annotations:
        vault.hashicorp.com/agent-inject: "true"
        vault.hashicorp.com/agent-inject-status: "update"
        vault.hashicorp.com/agent-inject-secret-login: "secret/login"
        vault.hashicorp.com/agent-inject-template-login: |
          {- with secret "secret/login" -}
          pattoken={ .Data.data.pattoken }
          {- end }
        vault.hashicorp.com/agent-inject-secret-my-first-secret: "secret/my-first-secret"
        vault.hashicorp.com/agent-inject-template-my-first-secret: |
          {- with secret "secret/my-first-secret" -}
          username={ .Data.data.username }
          password={ .Data.data.password }
          {- end }
        vault.hashicorp.com/role: "vault-role"
```

이러한 어노테이션은 Vault 에이전트를 구성하여 시크릿을 파드 볼륨에 주입하는 데 사용됩니다.

- vault.hashicorp.com/agent-inject: “true”: 이 파드에 대한 Vault 에이전트 주입을 활성화합니다.
- vault.hashicorp.com/agent-inject-status: “update”: 시크릿 주입 상태가 업데이트되도록 보장합니다.
- vault.hashicorp.com/agent-inject-secret-login: “secret/login”: Vault에 저장된 secret/login의 시크릿을 주입해야 함을 지정합니다.
- vault.hashicorp.com/agent-inject-template-login: 주입된 로그인 시크릿의 템플릿을 정의하여 시크릿이 기록될 형식을 지정합니다.
- vault.hashicorp.com/agent-inject-secret-my-first-secret: “secret/my-first-secret”: Vault에 저장된 secret/my-first-secret의 시크릿을 주입해야 함을 지정합니다.
- vault.hashicorp.com/agent-inject-template-my-first-secret: 주입된 my-first-secret에 대한 템플릿을 정의하여 시크릿이 기록될 형식을 지정합니다.
- vault.hashicorp.com/role: “vault-role”: 인증에 사용될 Vault 역할을 지정합니다.

<div class="content-ad"></div>

아래 명령어를 사용하여 pod 볼륨에서 Vault 시크릿을 확인할 수 있습니다.

<div class="content-ad"></div>

```js
$ kubectl exec -it vault-test-84d9dc9986-gcxfv -- sh -c "cat /vault/secrets/login && cat /vault/secrets/my-first-secret" -n vault
```

```js
$ kubectl exec -it vault-test-84d9dc9986-gcxfv -- sh -c "cat /vault/secrets/login && cat /vault/secrets/my-first-secret" -n vault

Defaulted container "nginx" out of: nginx, vault-agent, vault-agent-init (init)
pattoken=ytbuytbytbf765rb65u56rv
username=anvesh
password=anveshpassword
```

완료되었습니다! Vault에 시크릿을 성공적으로 생성하고 해당 시크릿을 팟 내에서 활용했습니다.

# 소스 코드



<div class="content-ad"></div>

위의 텍스트를 한국어로 번역하였습니다.

친구야! 당신을 우리의 GitHub 저장소로 초대합니다. 거기에는 Kubernetes용 소스 코드의 포괄적인 컬렉션이 저장되어 있어요.

또한, 여러분의 피드백과 제안을 환영합니다! 문제가 발생하거나 개선 아이디어가 있다면, 저희의 GitHub 저장소에서 issue를 열어주세요. 🚀

<img src="/assets/img/2024-05-23-AHand-OnGuidetoVaultinKubernetes_6.png" />

# Connect With Me

<div class="content-ad"></div>

이 블로그를 유익하게 찾으셨고 AWS, 클라우드 전략, Kubernetes 또는 관련된 모든 주제에 대해 더 깊이 알고 싶다면, LinkedIn에서 연결할 기회를 갖게 되어 기쁩니다. 의미 있는 대화를 나누고 통찰을 공유하며 함께 클라우드 컴퓨팅의 광활한 영역을 탐색해 봅시다.

언제든지 연락 주시거나 생각을 공유하거나 질문을 할 자유가 있습니다. 동적인 분야에서 연결하고 함께 성장하기를 기대합니다!

행복한 배포 되세요! 🚀

행복한 쿠버네팅 되세요! ⎈

