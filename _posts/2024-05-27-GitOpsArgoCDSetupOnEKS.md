---
title: "EKS에 GitOps Argo CD 설정하기"
description: ""
coverImage: "/assets/img/2024-05-27-GitOpsArgoCDSetupOnEKS_0.png"
date: 2024-05-27 17:23
ogImage:
  url: /assets/img/2024-05-27-GitOpsArgoCDSetupOnEKS_0.png
tag: Tech
originalTitle: "GitOps Argo CD Setup On EKS"
link: "https://medium.com/@vikash06.india/gitops-argo-cd-install-on-eks-0a3111c677c7"
---

Argo CD는 쿠버네티스를 위한 GitOps 지속적 전달 도구로, 응용 프로그램 상태를 Git 저장소와 자동 동기화하고 롤백, 헬스 체크, RBAC 통합, 다중 환경 지원 및 CI/CD 시스템과의 원활한 통합을 가능케 합니다. 우리의 구현에 사용할 Argo CD에 대한 몇 가지 포인트는 다음과 같습니다:

- Git Ops 에이전트 — Argo CD는 Git 저장소에서 업데이트된 코드를 가져와 쿠버네티스 리소스로 직접 배포하는 역할을 합니다. 인프라 구성 및 애플리케이션 업데이트를 한 시스템에서 관리할 수 있습니다.
- 자동 배포: Argo CD는 응용 프로그램을 지정된 목표 환경에 자동으로 배포합니다. 응용 프로그램 상태를 선언적으로 정의하고, Argo CD가 배포 프로세스를 처리합니다.
- 다중 클러스터 관리: Argo CD를 사용하면 여러 쿠버네티스 클러스터에서 응용 프로그램을 관리하고 배포할 수 있습니다.
- 템플릿 지원: Argo CD는 helm과 Kustomize를 사용하여 템플릿 및 구성 관리를 지원합니다.
- 롤백: Git 저장소에 커밋된 모든 애플리케이션 구성을 어디서든지 롤백할 수 있습니다.

Argo CD에는 많은 기능이 있습니다. 위에서 설명한 기능 중 일부를 우리의 CI/CD 파이프라인에 사용할 것입니다. Argo CD에 대한 자세한 내용은 Argo CD — Declarative GitOps CD for Kubernetes (argo-cd.readthedocs.io)에서 확인할 수 있습니다.

# Argo CD 설치하기:

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

아르고 CD는 다음 두 가지 방법을 사용하여 EKS에 설치할 수 있습니다:

## 쿠버네티스 매니페스트를 사용하는 방법:

1. 아르고 CD Git 저장소를 복제하세요:

```js
C: />git clone https:/ / github.com / argoproj / argo - cd.git;
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

귀하의 기계에 git을 설치해야 하며, git CLI가 있어야 합니다.

b. 매니페스트를 귀하의 Kubernetes 클러스터에 적용하세요:

```js
C:/argo-cd> kubectl apply -n argocd -f manifests/install.yaml
```

## Helm 사용 (권장되는 최상의 방법)

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

a. 아르고 CD 헬름 리포지토리 추가:

```js
  helm repo add argo https://argoproj.github.io/argo-helm
```

b. Helm 업데이트

```js
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

c. 설치

helm install argocd argo/argo-cd -n argocd

# Argo CD CLI:

Argo CD는 여러 클러스터 추가, 사용자 추가, Argo CD CLI 명령 실행 등 여러 기능 위한 중요한 도구입니다. Argo CD Command Line Interface (CLI)를 설치하는 방법은 운영 체제에 따라 옵션이 조금 다를 수 있습니다. 다음은 CLI를 설치하는 방법입니다:

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

Linux/macOS

```bash
brew install argocd
```

Windows:

관리자 권한으로 PowerShell을 열어주세요. 아래 명령어를 입력하여 choco를 설치해주세요. 자세한 단계는 https://medium.com/@vikash06.india/part-1-multi-environment-instance-deployment-helm-template-argo-cd-eks-initial-setup-5ce4519184fc에서 확인하실 수 있습니다.

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
C:/> choco install argocd-cli
```

```js
C:/> argocd version
argocd: v2.9.3+6eba5be
  BuildDate: 2023-12-01T23:24:09Z
  GitCommit: 6eba5be864b7e031871ed7698f5233336dfe75c7
  GitTreeState: clean
  GoVersion: go1.21.4
  Compiler: gc
  Platform: windows/amd64
```

# Argo CD UI에 액세스하기:

Argo CD UI에는 다음과 같은 방법으로 액세스할 수 있습니다:

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

- 포트 포워딩
- 인그레스

## 포트 포워딩:

포트 포워딩은 주로 로컬 머신에서 Argo CD UI를 사용하고자 할 때 사용됩니다

```js
kubectl port-forward svc/argocd-server -n argocd 8080:443
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

그럼, 브라우저에서 https://localhost:8080을 열어 Argo CD UI에 액세스할 수 있어요.

## ALB Ingress:

EKS에 설치하고 Argo CD를 로드 밸런서로 노출하려면 ALB Ingress가 최적의 옵션입니다(다른 로드 밸런서도 있습니다)

```js
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: argocd-ingress
  namespace: argocd
  annotations:
    alb.ingress.kubernetes.io/actions.ssl-redirect: '{"Type": "redirect", "RedirectConfig":{ "Protocol": "HTTPS", "Port": "443", "StatusCode": "HTTP_301"}'
    alb.ingress.kubernetes.io/backend-protocol: HTTPS
    alb.ingress.kubernetes.io/certificate-arn: arn:aws:acm:us-east-1:<account-id>:certificate/<certificate-id>
    alb.ingress.kubernetes.io/group.name:  prodalb
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTP": 80}, {"HTTPS": 443}]'
    alb.ingress.kubernetes.io/load-balancer-name: shared-load-balacer-name
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/tags: Environment=dev,Team=Cool Product, name=ALB
      Dev , UsedIN=EKS
    alb.ingress.kubernetes.io/target-type: ip

  finalizers:
   - ingress.k8s.aws/resources

spec:
    ingressClassName: alb
    rules:
    - host: argocd.example.net
      http:
        paths:
        - path: /
          backend:
            service:
              name: argocd-server
              port:
                number: 443
          pathType: Prefix
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

위 예제에서 `account-id`를 귀하의 AWS 계정 ID로, 인증서 ID를 AWS Certificate Manager에서 가져온 인증서 ID로 바꾸세요. 본질적으로 그것은 인증서 ARN입니다. 아직 인증서를 보유하고 있지 않다면 AWS Certificate Manager에서 인증서를 생성하세요. 매우 간단한 과정이며 발급 완료까지 약 5-10분이 소요됩니다. 이 링크를 참조하세요: [링크](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-public.html)

또한, `alb.ingress.kubernetes.io/group.name`은 여러 네임스페이스에서 동일한 로드 밸런서를 사용하는 경우에 사용됩니다.

```js
C:> kubectl apply -f argocd-ingress.yaml
```

위 명령을 실행하면 ALB Ingress 로드 밸런서가 생성됩니다.

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

argocd.example.io는 브라우저에서 접속 가능합니다. (문제가 발생하면 서브 도메인의 Route 53 A 레코드를 확인해보세요.)

argocd.example.io 또는 localhost:8080(포트 포워드)로 접속하면 아래 페이지가 나타납니다:

![Argo CD 설치 페이지](/assets/img/2024-05-27-GitOpsArgoCDSetupOnEKS_0.png)

Argo CD에 처음으로 로그인하기.

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

Argo CD와 인그레스 설정을 설치한 후에는 Argo CD의 기본 비밀번호를 가져와야 합니다. 아래 명령어를 사용할 것입니다.

```js
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

위 명령은 Argo CD의 비밀번호를 가져옵니다. 기본적으로 k8s에 저장된 모든 비밀번호는 Secret으로 암호화되어 base64로 저장됩니다. 윈도우 사용자의 경우 때로는 위 명령이 작동하지 않을 수 있으니 PowerShell을 사용하거나 'kubectl -n argocd get secret argocd-initial-admin-secret -o' 명령어로 비밀번호를 가져온 후에 Base64로 복호화해주세요.

argo cd.example.io 또는 localhost:8080(포트 포워드)로 접속할 때, 사용자를 admin으로 설정하고 위 단계에서 가져온 'password'로 로그인해주세요.

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

로그인 후에는 사용자 정보 화면에서 비밀번호를 업데이트할 수 있습니다.

![User Info Screen](/assets/img/2024-05-27-GitOpsArgoCDSetupOnEKS_1.png)

# 사용자 및 RBAC Argo CD 생성

Argo CD 설치 시 기본적으로 관리자 사용자가 생성됩니다. 때로는 사용자를 생성하고 역할을 관리해야 할 때도 있습니다.

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

Argo CD를 통해 설치된 인증 및 역할 관리용 Configmaps이 있습니다. 클러스터에서 역할 및 권한 부여를 관리하기 위해 아래 ConfigMap을 찾을 수 있습니다. 아래 명령을 실행하면 K8s의 모든 ConfigMaps이 표시됩니다.

```js
kubectl get configmaps -n argoccd
```

결과:

<img src="/assets/img/2024-05-27-GitOpsArgoCDSetupOnEKS_2.png" />

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

다음으로 우리는 사용자를 만들기 위해 argocd-cm을 사용할 것이고 역할 관리를 위해 argocd-rbac-cm을 사용할 것입니다.

## 사용자 생성:

사용자 관점에서 Argo CD에는 다음과 같은 옵션이 있습니다:

- OIDC 공급자 — 이미 사용 중인 OIDC 공급자(예: Okta, OneLogin, Auth0, Microsoft, Keycloak, Google (G Suite))가 있으면 이 옵션을 사용하세요. 여기서 사용자, 그룹 및 멤버십을 관리합니다.
- 번들된 Dex OIDC 공급자 — 현재 공급자가 OIDC를 지원하지 않는 경우(예: SAML, LDAP) 또는 Dex의 커넥터 기능(예: GitHub 조직 및 팀을 OIDC 그룹 클레임에 매핑하는 기능)을 활용하려는 경우 이 옵션을 사용하세요.
- 소규모 팀을 위한 사용자 생성:

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

```plaintext
C:\Users\vikas>kubectl edit configmap/argocd-cm -n argocd
```

아래와 같이 출력이되며, 사용자를 "accounts" 섹션에 추가하고 후행 앱앤더로 사용자를 추가하세요:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cm
  namespace: argocd
  labels:
    app.kubernetes.io/name: argocd-cm
    app.kubernetes.io/part-of: argocd
data:
  # add an additional local user with apiKey and login capabilities
  #   apiKey - allows generating API keys
  #   login - allows to login using UI
  accounts.demouser: apiKey, login
  # disables user. User is enabled by default
  accounts.demouser.enabled: "false"
```

"accounts.demouser.enabled"은 비밀번호를 활성화하거나 비활성화하는 데 사용됩니다.

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

새로 추가된 사용자의 비밀번호를 업데이트하려면 Argo CD CLI가 필요합니다. 이미 로컬에 Argo CD CLI를 설치해 두었으며 해당 명령을 실행하기 위해 EKS Argo CD에 연결해야 합니다. Windows 기기의 경우 Environment 변수 ARGOCD_SERVER를 설정해야 하며 URL은 http(ingress 또는 port forward)을 제외한 값을 사용해야 합니다. 다른 방법은 login 명령을 사용하여 Argo CD CLI에 로그인하는 것입니다.

```js
argocd login <ARGOCD_SERVER> --username <USERNAME> --password <PASSWORD>
```

새로 생성된 사용자를 확인합니다.

```js
argocd account list
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

사용자 목록이 표시됩니다. 이제 사용자 암호를 업데이트해야 합니다:

```js
# 만일 당신이 어드민 사용자로서 사용자를 관리 중이라면, <current-user-password>에는 현재 어드민 암호를 입력해주세요.
argocd account update-password --account <name> --current-password <current-user-password> --new-password <new-user-password>
```

위에서 사용자 이름은 구성 맵에 정의되어 있는 demouser이고, current-password는 어드민 사용자의 암호입니다.

https://argo-cd.readthedocs.io/en/stable/operator-manual/user-management/#:~:text=Once%20installed%20Argo%20CD%20has,users%20or%20configure%20SSO%20integration.

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

# RBAC:

위에서 사용자를 만든 후에는 권한 역할을 할당해야 합니다. RBAC는 Argo CD의 일부로 이미 설치된 ConfigMap입니다. 역할 및 인증을 정의하는 데 사용됩니다. 클러스터에서 역할과 권한을 관리하는 ConfigMap을 아래에서 찾을 수 있습니다. 애플리케이션 배포를 변경하고 권한 및 액세스와 관련된 문제가 발생할 때는 항상 아래 ConfigMap을 참고해야 합니다.

아래 명령어를 실행하면 모든 RBAC 세부 정보가 표시됩니다:

```js
C:\Users\vikas>kubectl describe configmap/argocd-rbac-cm -n argocd
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

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd
data:
  policy.default: role:readonly
  policy.csv: |
    p, role:org-admin, applications, *, */*, allow
    p, role:org-admin, clusters, get, *, allow
```

아래 명령어로 구성 맵을 수정할 수 있어요

```bash
C:\Users\vikas>kubectl edit configmap/argocd-rbac-cm -n argocd
```

이 링크에서 각 정책 파일의 각 속성에 대한 자세한 내용을 찾을 수 있어요 RBAC Configuration — Argo CD — Declarative GitOps CD for Kubernetes (argo-cd.readthedocs.io)

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

## 결론:
