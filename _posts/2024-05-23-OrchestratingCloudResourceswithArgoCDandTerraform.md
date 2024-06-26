---
title: "ArgoCD와 Terraform으로 클라우드 리소스 조정하기"
description: ""
coverImage: "/assets/img/2024-05-23-OrchestratingCloudResourceswithArgoCDandTerraform_0.png"
date: 2024-05-23 14:33
ogImage:
  url: /assets/img/2024-05-23-OrchestratingCloudResourceswithArgoCDandTerraform_0.png
tag: Tech
originalTitle: "Orchestrating Cloud Resources with ArgoCD and Terraform"
link: "https://medium.com/gitconnected/orchestrating-cloud-resources-with-argocd-and-terraform-0e8a16ee24c7"
---

## Terraform을 사용하여 ArgoCD 자동화하기

최근 몇 년간 GitOps가 핫한 주제가 되었고, ArgoCD가 이 대화의 선두에 서 있습니다. GitOps를 많이 좋아하지만, 일상적인 작업에서는 GPC, AWS 및 온프레미스에서 클라우드 인프라를 관리해야 하며, 클라우드 리소스를 조정할 때 Terraform이 제 가장 좋아하는 도구입니다.

![이미지](/assets/img/2024-05-23-OrchestratingCloudResourceswithArgoCDandTerraform_0.png)

최근 RKE2 Kubernetes 클러스터를 사용하여 Cilium을 기본 네트워킹 솔루션으로 활용하는 프로젝트에 착수했습니다. 여러 테넌트 및 다양한 프로젝트를 효율적으로 처리하기 위해 애플리케이션 오케스트레이션을 ArgoCD로 선택했습니다. 이는 인기 있는 선택이지만, GitLab, GitHub 및 Bitbucket과 같은 다양한 버전 컨트롤 시스템에서 설정을 산재시킬 수 있는 선언적 애플리케이션 설정 방식을 가지고 있습니다. 전체적인 오케스트레이션 시스템을 구축하는 것은 많은 구성 요소를 고려해야 합니다. 이 게시물에서는 ArgoCD와 Terraform의 통합에 초점을 맞추어 이러한 도구들이 효과적인 애플리케이션 오케스트레이션을 위해 어떻게 원활하게 함께 작동하는지 강조하겠습니다. 이 접근 방식을 다양한 프로젝트에 성공적으로 통합하여 이해하기 쉽고 원활한 관리를 나타내었습니다.

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

# ArgoCD

아르고 CD는 Kubernetes 애플리케이션의 선언적 지속적 전달을 위해 고안된 혁신적이고 오픈 소스 도구입니다. GitOps 지속적 전달 도구로서, ArgoCD는 Kubernetes 클러스터에서 애플리케이션의 배포와 관리를 자동화하고 최적화하는 데 도움을 줍니다. ArgoCD는 Git 저장소에서 애플리케이션의 원하는 상태를 정의하는 원칙에 따라 작동하며, 클러스터 내의 애플리케이션의 실제 상태가 선언된 상태로 수렴하도록 보장합니다.

아르고 CD 인프라를 살펴보면, 여러 동적 구성 요소로 이루어진 모듈식으로 설계되었음을 알 수 있습니다. 한쪽에는 사용자 또는 CI 파이프라인이 새로운 애플리케이션의 배포를 시작하고, 다른 한쪽에는 실제 배포가 대상이 되는 Kubernetes 클러스터가 있습니다. ArgoCD는 선호하는 버전 관리 시스템(VCS)과 함께 중간 역할을 수행하여 설정을 사용자의 기호에 따라 구성할 수 있는 유연성을 제공합니다.

이 유연성을 통해 회사 내 다른 팀의 액세스 수준을 자동화하고 제어하는 파트를 결정할 수 있습니다. 우리가 탐구할 설정은 생산 환경에서 효과적임이 입증되었으며, 이 프로젝트에 나중에 참여한 개발자들에게도 잘 받아들여졌습니다.

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

이제 설계 선택 사항을 다루었으니, 기술적 세부 정보로 들어가 봅시다.

# 요구 사항

이 안내서는 기능적인 Kubernetes 클러스터가 있고 다음 전제 조건이 이미 설치되어 있다고 가정합니다:

- Bitnami의 Sealed Secrets
- Helm CLI 도구

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

특정 쿠버네티스 배포 버전은 중요한 요소가 아닙니다. 배포 애플리케이션에 필요한 액세스만 있으면 됩니다. 저는 Rancher Kubernetes Engine 2 (RKE2)를 선택했는데, 이는 가벼우면서 다재다능한 Kubernetes 배포로 알려져 있어 간편하고 사용하기 쉽다는 장점이 있습니다. 또한 최신 보안 기준을 준수합니다. RKE2에 대한 더 깊은 통찰력을 얻으려면 이전 기사를 살펴보시고, Cilium의 기능과 어떻게 매끄럽게 통합되는지 알아보세요:

Sealed Secrets를 사용하기로 한 결정은 온프레미스 배포로 인해 GCP, AWS 또는 Azure에서 제공하는 관리형 솔루션과 다릅니다. 클라우드 제공업체를 활용하는 경우에는 SOPS가 배포 관련 비밀을 암호화하고 안전하게 보관하는 데 주목할만한 대안으로 제시됩니다. 하지만 우리의 온프레미스 시나리오에서는 Kubernetes에 더 네이티브한 솔루션을 채용했습니다:

마지막으로, 이론적으로 OpenTofu 또는 Ansible이 유사한 결과를 달성할 수 있겠지만, HashiCorp BSL 라이선스 변경에 문제가 없어 Terraform을 계속 사용하게 되었습니다. Terraform과 Ansible 사이를 선택하는 실용적인 지침이 필요하다면 이 리소스를 확인하세요:

# Terraform

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

테이블 태그를 마크다운 형식으로 변경해드리겠습니다.

| 번호 | 항목 | 설명        |
| ---- | ---- | ----------- |
| 1    | 이름 | 샘플 사용자 |
| 2    | 나이 | 30세        |
| 3    | 성별 | 여성        |

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

- ArgoCD 및 Sealed-Secrets Helm 차트 설치하기
- ArgoCD 구성이 저장된 저장소를 참조 및 연결하기
- ArgoCD 구성 저장소에서 구성 적용하기

이 작업에서의 합리성은 ArgoCD 구성의 잠재적 복잡성에 있습니다. 별도의 저장소에 이를 구성함으로써 구조화된 접근 방식을 취할 수 있게 되며, 이에 대해 다음 섹션에서 보다 자세히 다루겠습니다. 또한 Terraform 구성을 포함하는 저장소가 ArgoCD 뿐만 아니라 다양한 인프라 관련 배포를 포함할 수 있다는 점을 고려할 때, 명확성과 관리 용이성을 위해 구분된 ArgoCD 구성 저장소를 선택하는 것이 도움이 됩니다. 예를 들어, 본 문서에서 철저히 논의한 Terraform을 사용한 Teleport 배포를 고려해보세요:

## 비밀 관리

Terraform 스크립트에서 강조되는 중요한 부분은 SSH 비밀 키를 암호화하기 위해 Sealed-secrets를 설치하고 활용하는 것입니다. 이 키는 ArgoCD가 특정 GitHub 저장소에서 응용 프로그램 구성을 검색하고 연결하는 데 필수적입니다. 이를 구성하는 다양한 방법이 있지만, 우리는 Kubernetes Secret 개체에 argocd.argoproj.io/secret-type: repo-creds 라벨을 사용하기로 선택했습니다. 이 라벨은 특정 접두사로 시작하는 저장소, 예를 들어 git@github.com:`your-github-username`,는 본인 인증을 위해 이 시크릿(우리의 경우 SSH 비밀 키)을 사용할 수 있음을 ArgoCD에 알려줍니다. 보다 명확한 이해를 위해, 이 파일 ssh-secret.yaml의 완전한 구성을 확인해보세요.

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
kind: Secret
metadata:
  name: argoproj-ssh-creds
  namespace: argocd
  labels:
    argocd.argoproj.io/secret-type: repo-creds
stringData:
  url: git@github.com:<your-github-username>
  type: git
data:
  sshPrivateKey: <your-super-secret-ssh-key>
```

그러나 이것을 직접 버전 컨트롤 시스템 (VCS)에 푸시하는 것은 SSH 개인 키가 모두에게 노출되어 보안 위험이 발생한다는 것을 의미합니다. 이 우려를 해소하기 위해 Sealed-secrets를 사용하여 암호화하는 예방 조치를 취합니다. 이 조치로 sealed-ssh-secret.yaml이 생성됩니다. 이 암호화된 파일은 실제로 이전에 언급한 Terraform 스크립트에 의해 적용되며 ssh-secret.yaml을 삭제할 수 있습니다. Sealed Secrets가 작동하는 방식의 복잡성에 대해 자세히 알아가는 것은 이 글의 범위를 벗어납니다만, 자세한 탐구를 위해 다음 글을 참조할 수 있습니다:

이러한 단계로 Terraform의 중요한 작업이 완료되었습니다. 그러나 이 스크립트가 참조하는 ArgoCD 구성 리포지토리 내의 컨텐츠를 이해하는 데 필요한 최종 퍼즐 조각에는 여전히 주목해야 합니다.

# ArgoCD Configuration Repository

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

ArgoCD의 선언적 구성을 사용하면 재배포 가능성이 권장됩니다. 이 저장소는 Argo의 구성의 기본 소스 역할을 합니다. 특히, UI를 통해 앱을 만들 경우, ArgoCD를 재배포하는 동안 자동으로 다시 생성되지 않음을 염두에 두세요.

이 저장소는 다음과 같은 구조를 따릅니다. 이는 주관적 선택이며 개인적으로 매력적이고 명확하다고 생각합니다:

```js
├── apps
│   ├── demo
│   │   ├── config.yaml
├── config
│   ├── add-config.yaml
├── projects
│   ├── demo.yaml
├── repositories
│   ├── demo-app-config.yaml
├── argo-config.yaml
├── argo-projects.yaml
└── argo-repositories.yaml
```

각 파일의 합리적인 근거와 이 구조를 권장하는 이유를 명확히 하기 위해 각 파일의 내용을 살펴보겠습니다:

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

- ./apps: 이 디렉토리는 ArgoCD Application 객체의 구성을 관리하고 응용 프로그램 구성이 저장된 외부 저장소를 가리킵니다. /apps/demo/config.yaml 파일의 예는 다음과 같습니다:

```js
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: demo
spec:
  destination:
    name: 'in-cluster'
    namespace: <app-namespace>
  source:
    repoURL: '<GitHub-저장소-URL>'
    targetRevision: <대상-브랜치>
    path: './'
  # 이 응용 프로그램이 속한 ArgoCD 프로젝트
  project: demo
```

- ./config: 이 디렉토리는 ArgoCD 자체에 대한 추가 구성을 지정하는 데 사용됩니다. 예를 들어 회사 Slack 채널을 연결하여 응용 프로그램 관련 문제에 대해 개발자에게 알림을 보내는 알림 정책 구성이 포함됩니다.
- ./projects: 이 디렉토리는 ArgoCD 프로젝트의 생성을 담당하며 다양한 응용 프로그램이 속할 수 있는 공동 공간 역할을 합니다.
- ./repositories: 이 디렉토리는 응용 프로그램 구성을 가져올 수 있는 저장소를 whitelist로 지정하는 역할을 합니다.
- ./argo-config.yaml, ./argo-projects.yaml 및 ./argo-repositories.yaml: Terraform으로 실행되는 ArgoCD Application 객체들입니다. 이들은 앞서 설명한 디렉토리에 정의된 ArgoCD 객체를 호출합니다. 다음은 argo-config.yaml의 예시이지만 다른 구성은 다른 파일들에도 적용됩니다:

```js
# Argo의 기본 구성. 이것은 helm/terraform 배포에 의해 설치된 앱입니다.
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: argo-config
  namespace: argocd
spec:
  destination:
    namespace: argocd
    server: https://kubernetes.default.svc
  project: default
  # 각 저장소의 ./config 디렉토리에 대한 적용
  source:
    path: ./config
    repoURL: <argocd-구성-저장소-URL>
    targetRevision: <대상-브랜치>
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

데모 애플리케이션을 위한 구성 파일이 이러한 폴더에 추가되었습니다:

- projects/demo.yaml:

```js
apiVersion: argoproj.io/v1alpha1
kind: AppProject
metadata:
  name: demo
  namespace: argocd
  # 해당 프로젝트가 어떠한 애플리케이션에도 참조되지 않을 때까지 삭제되지 않도록 하는 파이널 라이저
  finalizers:
    - resources-finalizer.argocd.argoproj.io
spec:
  description: 데모 애플리케이션
  # 어떠한 Git 저장소에서도 배포할 수 있는 매니페스트 허용
  sourceRepos:
    - '*'
  # 모든 애플리케이션이 모든 사용 가능한 클러스터의 모든 네임스페이스에 배포할 수 있도록 허용
  destinations:
    - namespace: '*'
      server: '*'
  # 앱에 의해 배포될 수 있는 쿠버네티스 오브젝트를 결정하는 정책
  clusterResourceWhitelist:
    - group: ''
      kind: Namespace
    - group: ''
      kind: Deployment
    - group: ''
      kind: Service
---

apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: demo-apps
  namespace: argocd
spec:
  destination:
    namespace: argocd
    server: https://kubernetes.default.svc
  project: default
  sources:
    - path: ./apps/demo
      repoURL: <application-repository-url>
      targetRevision: <target-branch>
```

- repositories/demo-app-config.yaml:

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
kind: Secret
metadata:
  labels:
    argocd.argoproj.io/secret-type: repository
  name: repo-kubernetes-config
  namespace: argocd
stringData:
  name: "demo-app-config"
  type: "git"
  url: <application-repository-url>
  # No need to specify SSH Private Key since
  # this is done by repo-creds in Terraform and applicable to all
```

이 설명은 이 리포지토리 구조를 선택한 이유와 왜 Terraform 리포지토리와 분리되었는지에 대해 밝혀줍니다. 비교적 간단한 예시라도 이 구조를 선택한 이유를 알 수 있게 해줍니다.

애플리케이션 소스 코드와 분리된 별도의 Kubernetes 매니페스트용 Git 리포지토리를 활용하는 것이 여러 가지 이유로 매우 권장됩니다:

- 깔끔한 분리: 응용 프로그램 코드와 구성을 명확히 구분하여 매니페스트를 격리된 수정 없이 불필요한 CI 빌드를 발생시키지 않고 수정할 수 있게 합니다.
- 감사 로그의 명확성: 구성 전용 리포지토리는 감사 목적을 위한 더 깨끗한 Git 히스토리를 보장하여 정기 개발 활동으로 인한 잡음을 제거합니다.
- 마이크로서비스 배포: 단일 단위로 배포되는 여러 리포지토리의 서비스로 구성된 응용 프로그램의 경우 중앙 구성 리포지토리에 매니페스트를 저장함으로써 다양한 버전 관리 체계와 릴리스 주기를 수용할 수 있습니다.
- 접근 제어: 소스 코드와 구성 리포지토리를 분리하면 개발 중인 응용 프로그램에 작업하는 개발자가 프로덕션 환경에 직접 액세스할 수 없도록 구분된 액세스 제어를 제공합니다.
- CI 파이프라인의 안정성: 무한한 작업 루프와 Git 커밋 트리거를 피하기 위해 매니페스트 변경을 별도 리포지토리에 푸시하여 CI 파이프라인의 안정성을 유지합니다.

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

그게 전체 설정이에요 — 우리가 인프라 자원으로 흔히 참조하는 것들을 위한 한 저장소, 각 인프라 구성요소에 특화된 구성을 위한 별도의 저장소, 그리고 애플리케이션 매니페스트를 위한 세 번째 저장소가 있어요. 진짜 멋지죠?

# 결론

클라우드 인프라 관리 분야에서는 ArgoCD가 주도하는 GitOps 접근 방식이 상당한 인기를 얻었습니다. 클라우드 자원을 조정하는 이 여정을 통해 Terraform은 다양한 자원을 처리하는 중심 요소로 나타납니다. RKE2 Kubernetes 클러스터를 활용한 실제 프로젝트를 살펴보면, ArgoCD에 중점을 두어 애플리케이션 조정을 간소화하며, 이를 위해 Terraform과의 통합을 통해 선언적인 설정을 위한 원활한 설정을 탐구합니다. 이 게시물은 설계 선택의 이성, 깨끗한 저장소 구조의 필요성, 그리고 조율 환경에서 ArgoCD와 Terraform 사이의 조화로운 상호작용에 대한 실용적 가이드를 제공합니다.
