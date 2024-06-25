---
title: "Argo CD 리포지토리를 구조화하는 방법 Application Sets 사용하기"
description: ""
coverImage: "/assets/img/2024-05-23-HowtoStructureYourArgoCDRepositoriesUsingApplicationSets_0.png"
date: 2024-05-23 14:24
ogImage:
  url: /assets/img/2024-05-23-HowtoStructureYourArgoCDRepositoriesUsingApplicationSets_0.png
tag: Tech
originalTitle: "How to Structure Your Argo CD Repositories Using Application Sets"
link: "https://medium.com/containers-101/how-to-structure-your-argo-cd-repositories-using-application-sets-1150e75d05b3"
---

<img src="/assets/img/2024-05-23-HowtoStructureYourArgoCDRepositoriesUsingApplicationSets_0.png" />

시리즈의 이전 기사에서는 GitOps 환경을 모델링하고 응용 프로그램을 이 환경 간에 프로모션하는 방법을 설명했습니다. 그 기사는 단일 응용 프로그램과 해당 Kubernetes 리소스에 초점을 맞춰 작성되었습니다. 본 기사에서는 여러 관련 주제를 살펴보겠습니다:

- Argo CD 애플리케이션 매니페스트를 넣는 위치
- 여러 팀/클러스터/응용 프로그램과 어떻게 작업할 것인지
- 더 쉬운 관리를 위해 Application Sets를 활용하는 방법
- 단일 저장소 대신 GitOps 저장소를 분리하는 방법

항상 그렇지만, 우리의 조언은 모범 사례를 따르는 일반적인 권장 사항입니다. 조직에 대한 시작점으로 활용할 수 있지만, 본인의 경우에 더 나은 접근 방식이 있다고 믿는다면, 언제든지 우리가 언급한 패턴을 본인의 환경에 맞게 적용하시기 바랍니다. 예시 저장소는 다음에서 확인할 수 있습니다: https://github.com/kostis-codefresh/many-appsets-demo

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

앞선 기사는 매우 일반적이었고 Flux 사용자에게도 적용될 수 있지만, 여기서는 특히 Argo CD와 그 고급 기능에 초점을 맞출 것입니다.

# 매니페스트의 다양한 유형

Argo CD 애플리케이션을 조직하는 방법은 많은 자료와 블로그에서 다뤄지는 인기 있는 주제이며, 사용자들의 지속적인 질문과 토론이 있습니다. 불행히도 대부분의 기존 자료에서 다양한 매니페스트 유형을 혼합하며 응용 프로그램 세트와 그 기능에 대한 언급이 전혀 없습니다. "매니페스트"라는 용어는 Argo CD와 GitOps의 경우 오버로딩된 경우도 많습니다. 그래서 먼저 다양한 매니페스트 유형을 정의해봅시다.

![이미지](/assets/img/2024-05-23-HowtoStructureYourArgoCDRepositoriesUsingApplicationSets_1.png)

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

첫 번째 카테고리는 Argo CD와 전혀 상관없는 표준 Kubernetes 자원(배포, 서비스, 인그레스, 구성, 시크릿 등)으로, 이러한 자원들은 쿠버네티스 클러스터에서 정의됩니다. 이러한 자원은 어플리케이션이 쿠버네티스 내에서 어떻게 실행되는지를 기술하며, Argo CD가 전혀 없는 로컬 클러스터에 어플리케이션을 설치하는 데 사용될 수 있습니다. 이러한 manifest는 개발자가 새로운 릴리스를 배포하면서 자주 변경되며, 일반적으로 다음과 같은 방법으로 지속적으로 업데이트됩니다:

- 배포 manifest에서 컨테이너 이미지 버전을 업데이트하는 경우 (대부분의 경우 약 80%)
- 컨테이너 이미지와 함께 configmap이나 시크릿에 대한 구성을 업데이트하는 경우 (대부분의 경우 약 15%)
- 비즈니스 또는 기술 속성을 세부 조정하기 위해 구성만 업데이트하는 경우 (대부분의 경우 약 5%)

이러한 manifest는 어플리케이션의 상태를 설명하기 때문에 개발자에게 매우 중요하며, 조직 환경(QA/Staging/Production 등)에서 모든 어플리케이션의 상태를 설명하는 데 사용됩니다. 프로모션 기사는 특히 이러한 유형의 manifest에 대해 언급했습니다.

두 번째 카테고리는 Argo CD 어플리케이션 manifest입니다. 이들은 사실의 원천(첫 번째 유형의 manifest)과 해당 어플리케이션의 대상 및 동기화 정책을 참조하는 정책 설정입니다. Argo CD 어플리케이션은 근본적으로 Git 저장소(표준 Kubernetes manifest를 포함하는)와 대상 클러스터 간의 매우 간단한 링크입니다.

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

![2024-05-23-HowtoStructureYourArgoCDRepositoriesUsingApplicationSets_2](/assets/img/2024-05-23-HowtoStructureYourArgoCDRepositoriesUsingApplicationSets_2.png)

많은 사람들이 생각하는 것과는 달리, 개발자들은 이러한 유형의 manifest로 괴롭힘받길 원하지 않아요. 운영자들에게도 이 유형의 manifest는 한 번 설정하고 나면 그냥 잊어버릴 것입니다. 어플리케이션 세트 manifest 역시 동일한 범주에 속합니다.

세 번째와 네 번째 범주는 첫 번째와 두 번째와 동일하지만, 이번에는 개발자들이 만드는 내부 애플리케이션 대신 인프라 애플리케이션(cert manager, nginx, coredns, prometheus 등)에 대해 이야기합니다.

이 manifest에 대해 개발자의 애플리케이션과 다른 템플릿 시스템을 사용할 수 있습니다. 예를 들어, 준비된 애플리케이션에 대해서는 Helm을 사용하고, 개발자가 만든 애플리케이션에 대해서는 Kustomize를 선택하는 매우 인기 있는 패턴이 있습니다.

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

위 4가지 종류의 manifests로 핵심 포커스는 아래 2가지 카테고리에 대한 것입니다.

- 개발자들은 인프라 manifests에 대해 신경 쓰지 않습니다.
- 이러한 manifests는 매우 자주 변경되지 않습니다. 보통 해당 구성 요소를 업그레이드하거나 매개 변수를 세밀하게 조정할 때에만 변경됩니다.

여기서 중요한 점은 이 4가지 유형의 manifests가 대상 청중과 무엇보다도 변경 빈도 등 여러 측면에서 서로 다른 요구 사항을 갖고 있다는 것입니다. "GitOps 리포지토리 구조"에 대해 이야기할 때, 어떤 카테고리의 manifests에 대해 이야기하는 지 (다수일 경우) 항상 먼저 설명해야 합니다.

# 안티 패턴 1 — 서로 다른 유형의 manifests 혼합

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

가장 좋은 방법을 설명하기 전에 장기적으로 문제를 복잡하게 만들 수 있는 몇 가지 안티 패턴에 대해 경고하는 것이 중요합니다.

매니페스트의 중요한 포인트 중 하나는 Kubernetes(카테고리 1)와 Argo CD 리소스(카테고리 2) 사이에 매우 명확한 분리를 가져야 한다는 것입니다. 편리하게, Argo CD에는 두 카테고리를 섞을 수 있게 해주는 여러 기능이 있습니다. 일부 극히 드문 경우에는 필요하지만, 다른 유형의 매니페스트를 섞는 것을 권장하지 않습니다.

간단히 예를 들어, Argo CD는 다음과 같은 구문을 지원합니다:

```js
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-helm-override
  namespace: argocd
spec:
  project: default

  source:
    repoURL: https://github.com/example-org/example-repo.git
    targetRevision: HEAD
    path: my-chart


    helm:
      # 이렇게 하지 말아주세요
      parameters:
      - name: "my-example-setting-1"
        value: my-value1
      - name: "my-example-setting-2"
        value: "my-value2"
        forceString: true # 값이 문자열로 처리되도록 보장


      # 이렇게 하지 말아주세요
      values: |
        ingress:
          enabled: true
          path: /
          hosts:
            - mydomain.example.com


      # 이렇게 하지 말아주세요
      valuesObject:
        image:
          repository: docker.io/example/my-app
          tag: 0.1
          pullPolicy: IfNotPresent
  destination:
    server: https://kubernetes.default.svc
    namespace: my-app
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

이 매니페스트는 두 가지를 동시에 수행합니다. 주 파일은 Argo 앱(카테고리 2)에 관한 내용이지만, "helm" 속성은 실제로 Kubernetes 응용 프로그램(카테고리 1)에 대한 값들을 포함하고 있습니다.

이 매니페스트는 다음과 같이 차트와 동일한 Git 저장소에 있는 값 파일에 모든 매개변수를 넣음으로써 쉽게 수정할 수 있습니다.

```js
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-helm-override
  namespace: argocd
spec:
  project: default


  source:
    repoURL: https://github.com/example-org/example-repo.git
    targetRevision: HEAD
    path: my-chart


    helm:
      ## 이렇게 하세요 (Git의 값 파일 개별로)
      valueFiles:
      - values-production.yaml
  destination:
    server: https://kubernetes.default.svc
    namespace: my-app
```

또한 Helm 어머렐라 차트를 사용하는 경우, 다른 차트를 참조하고 해당 값들을 재정의할 수 있습니다.

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

외부 차트(즉, Git에 저장되지 않은)를 사용하는 것도 가능하지만, 우리가 추천하는 방법은 아닙니다. 제3자 소스에서 외부 차트를 사용하는 것은 보안 및 안정성 측면에서 많은 도전점을 제시합니다.

이상적으로 모든 Helm 차트는 귀하의 통제 하에 있어야 하며 Git에 있어야 합니다. 이렇게 하면 GitOps의 모든 이점을 얻을 수 있습니다.

하지만 때로는 이게 불가능한 경우도 있습니다. 그래서 최후 수단으로만 외부 Helm 차트를 참조하면서 여전히 로컬에 저장된 자체 값을 사용할 수 있습니다. 이 가이드를 작성하는 시점에 이 기능이 베타 버전임을 유의하십시오.

```js
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-proper-helm-app
  namespace: argocd
spec:
  sources:
  - repoURL: 'https://my-chart/helm-charts'
    chart: my-helm-chart
    targetRevision: 3.7.1
    helm:
      valueFiles:
      - $values/my-chart-values/values-prod.yaml
    ## DO THIS (values in Git on their own)
  - repoURL: 'https://git.example.com/org/value-files.git'
    targetRevision: dev
    ref: values
  destination:
    server: https://kubernetes.default.svc
    namespace: my-app
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

마찬가지로 Argo CD는 다음을 지원합니다:

```js
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-kustomize-override
  namespace: argocd
spec:
  source:
    repoURL: https://github.com/example-org/example-repo.git
    targetRevision: HEAD
    path: my-app

    # 이렇게 하지 마세요
    kustomize:
      namePrefix: prod-
      images:
      - docker.io/example/my-app:0.2
      namespace: custom-namespace

  destination:
    server: https://kubernetes.default.svc
    namespace: my-app
```

여기서 Kustomize의 속성들(category 1)이 다시 주 응용 프로그램 매니페스트 (category 2)와 섞입니다. 이를 피하기 위해 Kustomize 값을 웹 애플리케이션 CRD에 하드 코딩하는 대신 오버레이로 저장하는 것이 좋습니다.

```js
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-proper-kustomize-app
  namespace: argocd
spec:
  source:
    repoURL: https://github.com/example-org/example-repo.git
    targetRevision: HEAD
    ## 이렇게 하세요. Kustomize 오버레이에 모든 값을 저장하세요
    path: my-app/overlays/prod

  destination:
    server: https://kubernetes.default.svc
    namespace: my-app
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

만약 여러분의 매니페스트가 올바르게 분할되었는지 이해하기 위한 리트머스 테스트는 다음 질문을 하는 것입니다:

"만약 개발자가 Kubernetes 리소스에 대해 전문가이지만 Argo CD에 대해서는 전혀 모른다면, 오직 kustomize(또는 Helm)만을 사용하여 자신의 노트북에 애플리케이션을 설치할 수 있을까요?"

만약 답이 "아니오"라면, 그렇다면 여러분은 Kubernetes 매니페스트가 Argo CD 애플리케이션과 어우러져 있는 부분을 찾아내고 강력한 결합을 제거해야 합니다.

매니페스트를 혼합하는 함정에 빠져들어가 있는 기관들을 많이 보았습니다. 대화를 나눌 때 거의 항상, 그 기관들은 이러한 접근 방식이 "필요하다"고 생각했던 것입니다. 왜냐하면 그들은 기반이 되는 도구들(Helm/Kustomize)의 능력을 제대로 이해하지 못했기 때문입니다.

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

귀하의 조직이 Helm과 작업하는 경우, Helm 값 계층이 어떻게 작동하는지, 그리고 Helm 어머니 차트가 어떻게 작동하는지 알아야 합니다. 신중하게 설계된 Helm 값 계층으로 대부분의 일반적인 시나리오를 다룰 수 있습니다. Argo CD의 다중 소스 기능을 사용해야 할 경우에만 사용하세요. 그리고 현재 베타 버전이라는 것을 기억하세요.

귀하의 조직이 Kustomize와 작업하는 경우, 구성 요소(재사용 가능한 블록)의 작동 방식과 모든 다양한 변환기/패치/대체 방법을 알아야 합니다.

나중 장에서 볼 수 있듯이 두 종류의 manifest를 구분하는 것은 좋은 실천 방법입니다. 그러나 이전 섹션에 표시된 표에서는 생명 주기가 다른 것들을 섞는 것이 항상 문제의 원인임을 명백히 알 수 있어야 합니다.

다른 종류의 manifest를 섞는 것에는 여러 가지 다른 도전과제가 있습니다:

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

- 모든 관련 당사자들이 이해하기 어렵게 만듭니다.
- 매니페스트를 사용하는 사람들(예: 개발자)과 매니페스트를 생성하는 사람들(즉, 관리자/운영자) 사이의 요구사항을 혼란스럽게 합니다.
- 매니페스트를 특정 Argo CD 기능에 결합시킵니다.
- 보안 관련 문제를 분리하기가 더 복잡해집니다.
- 더 많은 부분들을 움직이게 하고 디버깅하기 어려운 시나리오를 야기합니다.
- 개발자들에 대한 로컬 테스트를 더 어렵게 만듭니다.

참고: 사용하지 말아야 할 다른 Argo CD 기능은 파라미터 오버라이드입니다. 가장 원시적인 형태에서 심지어 GitOps 원칙을 따르지 않습니다. Git에 저장해도 Argo CD와 쿠버네티스 정보가 동일한 위치에 혼합되어 있게 됩니다(카테고리 1 및 카테고리 2 정보 혼합).

# 안티 패턴 2 — 잘못된 추상화 수준에서 작업

Argo CD 애플리케이션 CRD의 목적은 주요 쿠버네티스 매니페스트를 '래퍼' 또는 '포인터'로 작동하는 것입니다. 중요한 것은 쿠버네티스 매니페스트(카테고리 1)와 Argo CD 매니페스트(카테고리 2)가 언제나 보조 역할을 해야 한다는 것입니다.

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

이상적인 경우에는 한 번 Application manifest를 만들어서 어떤 Git 저장소가 어떤 클러스터로 이동하는지 정의하고 이 파일을 다시는 손대지 않는 것이 좋습니다 (이것이 이전 테이블에서 변경 빈도가 "거의 없음"인 이유입니다).

안타깝게도 많은 회사들이 실제 쿠버네티스 manifest 대신에 Application CRD를 주 작업 단위로 사용하는 것을 볼 수 있습니다.

전형적인 예로는 CI 프로세스를 사용하여 Application의 "targetRevision" (또는 "path") 필드를 자동으로 업데이트하는 경우가 있습니다.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  ## 이렇게 하지 마세요
  name: my-ever-changing-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/example-org/example-repo.git
    targetRevision: dev
    ## 이전에는 "targetRevision: staging"이었고, 그 이전에는 "targetRevision: 1.0.0"였으며,
    ## 그보다 전에는 "targetRevision: 1.0.0-rc" 였습니다.
    path: my-staging-app
    ## 이전에는 "path: my-qa-app"이었습니다.
  destination:
    server: https://kubernetes.default.svc
    namespace: my-app
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

어플리케이션 CRD를 계속 변경되는 가변 파일로 취급하는 것은 몇 가지 근본적인 문제와 나쁜 관행을 내포하고 있음을 의미합니다. 예를 들어, 대상 리비전 필드를 계속 다른 브랜치로 지정하는 것은 거의 항상 조직이 환경에 대해 브랜치를 사용하고 있는 것을 의미하는데 이는 강력히 권하지 않는 관례입니다.

이것은 또한 Git 리포지토리 자체를 보는 것이 원하는 상태가 무엇인지 명확하게 나타내지 않는다는 것을 의미합니다. 대신, 각 응용 프로그램은 이해하고 전체 그림을 위해 다른 Git 리포지토리와 비교해야 하는 별도의 대상 리비전을 가질 수 있습니다.

Argo CD 어플리케이션은 임의의 응용 프로그램을 실행하는 데 사용할 수 있는 재사용 가능한 상자가 아닙니다. GitOps의 핵심은 어플리케이션이 수행한 이벤트에 대한 명확한 이력을 가지는 것입니다. 하지만 어플리케이션 CRD를 완전히 다른 매니페스트를 가리키는 일반화된 작업 단위로 취급한다면 GitOps의 주요 이점 중 하나를 잃게 됩니다.

대부분의 경우에는 CRD 대신에 백그라운드에 있는 쿠버네티스 매니페스트 자체를 변경해야 합니다. 예를 들어, 어플리케이션 이미지 CRD가 가리키는 쿠버네티스 배포 리소스 위에 정방향으로 새로운 앱 이미지를 가진 브랜치로 대상 리비전 필드를 변경하는 대신에 변경해야 합니다.

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

# 안티패턴 3 — 다른/다중 레벨에서 템플릿 사용하는 것

이전 안티패턴의 친척에 해당하는 것은 Application CRD (카테고리 2)에서 템플릿 기능을 적용하는 것입니다. Helm과 Kustomize는 이미 매우 강력한 도구이며 주요 Kubernetes 매니페스트에 대한 템플릿을 처리할 수 있습니다 (카테고리 1). 그리고 사용 사례가 해당되지 않더라도 사용자 정의 구성 플러그인을 사용하거나 좋아하는 외부 도구로 매니페스트를 사전 렌더링할 수 있습니다.

문제는 사람들이 Application CRD를 템플릿화하려고 시도할 때 시작됩니다. 왜냐하면 그들은 이전 안티패턴에 의해 생성된 문제를 해결하려고 하기 때문입니다.

이곳의 전형적인 예는 일팀이 Helm 차트를 생성하고 해당 Helm 차트가 Kubernetes 매니페스트의 Helm 차트를 가리키는 Application CRD를 포함할 때 발생합니다. 이제 동시에 두 가지 다른 수준에서 Helm 템플릿을 적용하려고 하게 됩니다. 그리고 Argo CD 풋프린트가 커질수록 새로 온 사람들이 매니페스트가 어떻게 구조화되어 있는지 이해하기가 매우 어렵습니다.

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

Argo CD에는 Application CRD에 대한 강력한 템플릿 메커니즘이 포함되어 있지 않다는 사실은 이 워크플로우를 권장하지 않음을 강력히 시사해야합니다. 또한 ApplicationSets(다음 항목 참조)이 소개되면서 템플릿을 적용해야 하는 적절한 위치는 개별 Application 파일이 아니라 ApplicationSet입니다.

# 안티패턴 4 — Application Sets를 사용하지 않는 경우

우리는 Application 매니페스트 (카테고리 2)에 대해 많이 이야기했으며 무엇을 하지 말아야 하는지에 대한 내용도 많았습니다. 이 기사의 주된 포인트는 여기에 있습니다. 이상적으로, Application CRD를 전혀 생성할 필요가 없어야합니다.🙂

만약 비트 trivial한 Argo CD 설치가 있다면, 꼭 ApplicationSets가 어떻게 작동하는지 이해하고, 최소한 Git 생성기와 클러스터 생성기를 동시에 결합하는 방법을 공부해야합니다.

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

어플리케이션 세트는 모든 애플리케이션 매니페스트(카테고리 2)의 생성을 담당할 수 있어요. 예를 들어, 20개의 애플리케이션과 5개의 클러스터가 있다면, Argo CD 애플리케이션을 위한 100가지 조합을 자동으로 생성해줄 수 있는 단일 어플리케이션 세트 파일을 만들 수 있어요.

GitOps 인증에서 가져온 예제가 있어요.

```js
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: cluster-git
spec:
  generators:
    # matrix 'parent' generator
    - matrix:
        generators:
          # Git generator, 'child' #1
          - git:
              repoURL: https://github.com/codefresh-contrib/gitops-cert-level-2-examples.git
              revision: HEAD
              directories:
                - path: application-sets/example-apps/*
          # cluster generator, 'child' #2
          - clusters: {}
  template:
    metadata:
      name: '{path.basename}-{name}'
    spec:
      project: default
      source:
        repoURL: https://github.com/codefresh-contrib/gitops-cert-level-2-examples.git
        targetRevision: HEAD
        path: '{path}'
      destination:
        server: '{server}'
        namespace: '{path.basename}'
```

이 생성기는 "application-sets/example-apps" 아래의 모든 앱을 Argo CD에 정의된 모든 클러스터에 배포하라고 말해요. 현재 연결된 클러스터 수나 Git 레포지토리에 있는 애플리케이션 수가 얼마나 많든 상관없어요. 어플리케이션 세트 생성기는 가능한 모든 조합을 자동으로 생성하며 새로운 클러스터나 새로운 애플리케이션을 추가할 때도 계속해서 재배포할 거에요.

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

Argo CD Autopilot에 익숙한 분들은 이 패턴을 알아볼 수 있을 것입니다. 이는 모노레포 관점에서의 기본 설정입니다.

애플리케이션 세트가 필요한 기본 템플릿을 지원한다는 점에 유의해주세요. 이를 통해 메인 Kubernetes 매니페스트에는 여전히 Helm/Kustomize를 유지하면서 Application CRD에 대해 어느 정도 유연성을 가질 수 있습니다. 또한 애플리케이션 세트에 대한 Go 템플릿 지원을 놓치지 않도록 주의하세요.

모든 애플리케이션에 대해 하나의 ApplicationSet을 사용해야 하는 것은 아닙니다. 각 애플리케이션의 "타입"에 대해 여러 개의 세트를 사용할 수도 있습니다. 여기서 "타입"이 의미하는 바는 당신의 경우에 따라 다를 것입니다.

# 최선의 방법 — 세 단계의 구조 사용하기

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

이전 섹션에서는 권장하지 않는 몇 가지 방법과 피해야 할 몇 가지 함정을 살펴보았습니다. 이제 우리의 제안하는 해결책에 대해 이야기할 준비가 되었습니다.

시작점은 아래 이미지에 표시된 대로 3단계 구조여야 합니다.

![아이미지](/assets/img/2024-05-23-HowtoStructureYourArgoCDRepositoriesUsingApplicationSets_3.png)

가장 낮은 수준에서는 응용 프로그램이 실행되는 방식을 정의하는 Kubernetes manifest(매니페스트)가 있습니다(매니페스트의 카테고리 1). 이러한 매니페스트는 Kustomize 또는 Helm 템플릿이며 완전히 자체 포함되어 있어 Argo CD가 없어도 어떤 클러스터에서든 별도로 배포될 수 있습니다. 이 파일들의 구조에 대해서는 프로모션 블로그 게시물에서 자세히 다루었습니다.

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

이전 섹션에서 설명한 대로 한 단계 위에는 응용 프로그램 세트가 있습니다. 이들은 주요 Kubernetes 매니페스트를 Argo CD 응용 프로그램(category 2 of manifests)으로 감싸줍니다. 대부분의 경우에는 개별 응용프로그램 CRD(Application CRDs)를 만들 필요 없이 ApplicationSets만 만들면 됩니다.

마지막으로 선택적 구성 요소로, 모든 응용 프로그램 세트를 App-of-App에 그룹화하여 완전히 비어 있는 클러스터에 모든 응용 프로그램을 부트스트래핑할 수 있습니다. 만일 클러스터를 만드는 다른 방법이 있다면(예: terraform/pulumi/crossplane), 이 수준이 항상 필수적이지 않을 수 있습니다.

그게 전부에요!

이 패턴이 얼마나 간단한지에 주목하세요:

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

- 추상화 수준은 단 3개뿐이에요. 4개나 5개를 가진 회사들도 있지만, 그렇게 되면 메탈 모델이 훨씬 복잡해져요.
- 각 수준은 완전히 독립적이에요. 쿠버네티스 Manifest를 별도로 설치할 수도 있고, 특정 애플리케이션 세트를 선택할 수도 있고, 모든 것을 최상위에서 선택할 수도 있어요. 하지만 선택은 당신의 몫이에요.
- Helm과 Kustomize는 쿠버네티스 Manifest에서만 한 번 사용돼요. 이것 때문에 템플릿 시스템을 이해하기 정말 쉬워져요.

실제 예시를 살펴봐요. 아래 링크에서 예시 리포지토리를 찾을 수 있어요. https://github.com/kostis-codefresh/many-appsets-demo

![2024-05-23-HowtoStructureYourArgoCDRepositoriesUsingApplicationSets_4.png](https://example.com/assets/img/2024-05-23-HowtoStructureYourArgoCDRepositoriesUsingApplicationSets_4.png)

여기서 우리는 쿠버네티스 Manifest를 "apps" 폴더에, 애플리케이션 세트를 "appsets" 폴더에 두기로 선택했어요. 이름이 중요한 건 아니에요. 무슨 일이 일어나고 있는지 명확하게 알면 무엇이든 선택할 수 있어요.

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

"앱" 디렉터리에는 표준 Kubernetes 매니페스트가 저장됩니다. 이 예제에서는 Kustomize를 사용하고 있습니다. 각 애플리케이션마다 해당 환경에 대한 오버레이만 있습니다.

![이미지](/assets/img/2024-05-23-HowtoStructureYourArgoCDRepositoriesUsingApplicationSets_5.png)

이 구조에 대한 자세한 내용은 프로모션 블로그 게시물에 설명되어 있습니다.

전체 구조를 살펴보면 각 환경이 "apps/앱이름/envs/환경이름" 디렉터리에 배치된다는 점이 중요합니다.

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

앱세트 폴더에는 모든 애플리케이션 세트가 보관됩니다. 이 간단한 예제에서는 단순한 목록이지만 더 복잡한 예제에서는 조직을 더 잘하기 위해 여기에 폴더도 있을 수 있습니다.

![ArgoCD Repository Structure](/assets/img/2024-05-23-HowtoStructureYourArgoCDRepositoriesUsingApplicationSets_6.png)

각 애플리케이션 세트는 단순히 Kubernetes 매니페스트에서 정의된 overlays를 언급합니다. 여기에 "qa" 앱세트의 예가 있습니다.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: my-qa-appset
  namespace: argocd
spec:
  goTemplate: true
  goTemplateOptions: ["missingkey=error"]
  generators:
    - git:
      repoURL: https://github.com/kostis-codefresh/many-appsets-demo.git
      revision: HEAD
      directories:
        - path: apps/*/envs/qa
  template:
  metadata:
    name: "{index .path.segments 1}-{index .path.segments 3}"
  spec:
    # 애플리케이션이 속한 프로젝트입니다.
    project: default

    # 애플리케이션 매니페스트의 소스
    source:
      repoURL: https://github.com/kostis-codefresh/many-appsets-demo.git
      targetRevision: HEAD
      path: "{.path.path}"

    # 애플리케이션을 배포할 대상 클러스터 및 네임스페이스
    destination:
      server: https://kubernetes.default.svc
      namespace: "{index .path.segments 1}-{index .path.segments 3}"
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

이 응용 프로그램 설정은 다음과 같이 말합니다:

"앱 폴더로 이동하십시오. 앱을 포함하는 모든 폴더를 검색하고 하위 폴더 envs/qa가 있는 경우 Argo CD 앱을 생성하십시오." 이 앱세트는 "qa" 오버레이가 있는 QA 클러스터 앱에만 배포됩니다. 이 오버레이가 없는 앱은 배포되지 않습니다. 앱 폴더를 살펴보면 모든 환경에 모든 앱이 배포되지 않음을 알 수 있습니다. 예를 들어 "billing"은 프로덕션에만 배포되고 "fake-invoices"는 QA에만 있습니다.

또한 별도의 app-of-apps 매니페스트가 있으며 이것은 단순히 모든 앱 세트를 그룹화하는 데 도움이 됩니다. 이것은 엄격히 필수는 아니지만 빈 클러스터를 처음부터 모두 구성하는 데 도움이 됩니다.

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

예시에서 우리는 딱 한 개의 매니페스트만으로 12개의 애플리케이션을 배포했어요 (이는 애플리케이션 세트가 만드는 조합의 수입니다) 한 번에요.

이 인위적인 데모 저장소는 모든 "환경"에 대해 단일 클러스터를 사용합니다. 프로덕션 설정에서는 모든 애플리케이션을 각각의 클러스터(qa/prod/staging)로 분리하기 위해 클러스터 생성기도 사용할 수 있어요.

# Argo CD 애플리케이션의 두 번째 날 운영

그렇다면 이 구조가 최적일까요? 이전 섹션의 데모 저장소를 사용하여 일반적인 시나리오를 살펴보고 얼마나 간단한지 확인해보겠습니다.

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

이전 장에서는 쿠버네티스 매니페스트(카테고리 1)와 Argo CD 매니페스트(카테고리 2)를 분리해야 한다는 주장을 했습니다. 이 결정의 주요 목표는 개발자들의 삶을 쉽게 만들고 공통 시나리오를 돕는 데 있습니다. 개발자를 위해 몇 가지 예시를 살펴보겠습니다:

시나리오 1 — 개발자가 "invoices" 앱의 "qa" 구성을 검사하고 싶어 합니다.

해결책 1:

```js
cd apps/invoices
kustomize build envs/qa
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

해당 문제는 한 줄의 명령어로 해결할 수 있으며 Argo CD 설치가 필요하지 않습니다.

시나리오 2 — 개발자가 "billing" 앱의 미국과 유럽 간에 어떤 설정이 다른지 이해하려고 합니다.

해결책 2:

```js
cd apps/billing
kustomize build envs/prod-eu/> /tmp/eu.yml
kustomize build envs/prod-us/ > /tmp/us.yml
vimdiff /tmp/eu.yml /tmp/us.yml
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

해결책은 3개의 명령어이며 Argo CD 설치가 필요하지 않습니다.

시나리오 3 — 개발자가 "orders" 애플리케이션의 "qa" 환경설정을 로컬 클러스터에 설치하려고 합니다.

해결책 3:

```js
cd apps/orders
kubectl apply -k -f envs/qa
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

다시 한 번, Argo CD 설치가 필요하지 않습니다.

만약 다양한 유형의 manifest를 혼합한다면, 개발자들도 Argo CD를 다뤄야 할 수도 있습니다. Argo CD로 로컬 테스트를 진행하는 것은 Kubernetes 클러스터를 사용하는 것보다 훨씬 복잡합니다.

관리자/운영자들에게도 매우 간단한 일입니다. 대부분의 작업은 클러스터와 어플리케이션의 수에 관계없이 단일 파일/폴더에서 단일 변경만으로 이루어집니다.

예를 들어, 관리자가 "payments" 어플리케이션을 QA 환경에 배포하려고 한다고 가정해보겠습니다 (현재는 프로드 환경에서만 실행 중인 상태입니다).

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
cd apps/payment/env
mkdir qa
<qa를 위한 k8s manifest 생성>
<Argo CD 동기화를 기다립니다>
```

관리자가 원하는 모든 작업은 간단한 Git 작업에 해당합니다.

- 기존 애플리케이션을 새 환경에 배포 - Kustomize overlay를 생성합니다. Argo CD 변경은 필요하지 않습니다.
- 환경에서 애플리케이션 제거 - 해당 Kustomize overlay를 삭제합니다. Argo CD 변경은 필요하지 않습니다.
- 새로운 애플리케이션 생성 - "apps" 폴더 아래 새 폴더에 K8s manifest를 커밋합니다. Argo CD 변경은 필요하지 않습니다.
- "integration"이라는 새로운 환경 생성 - qa를 기반으로 "integration" 애플리케이션 세트를 새 파일로 복사/수정합니다. 다음 동기화에서 Argo CD는 "integration" 오버레이를 가진 모든 애플리케이션에 대해 새로운 조합을 생성합니다.
- 새 클러스터 추가 - 클러스터를 Argo CD에 연결하면 해당 클러스터를 참조하는 모든 applicationset이 자동으로 애플리케이션을 해당 클러스터에 배포합니다.
- 클러스터를 다른 환경으로 이동 - 해당하는 애플리케이션 세트에 새 라벨을 추가/편집하세요.

이곳의 핵심은 명료한 manifest의 분리를 유지하고 있습니다. 개발자들은 Argo CD가 어떤 모델링을 하고 있는지 알 필요 없이 일반 쿠버네티스 리소스와 작업할 수 있으며, 관리자들은 applicationset과 폴더를 사용하여 쉽게 애플리케이션을 환경 간에 이동할 수 있습니다.

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

기존 환경에 대한 새로운 클러스터를 빠르게 설정하는 것은 정말 쉽습니다. 해당 클러스터 생성기에 추가하기만 하면 됩니다.

# Monorepo, Monorepo, Monorepo

이전 섹션에서 공유한 예제 저장소는 모든 애플리케이션이 어떤 식으로든 관련이 있다고 가정합니다. 아마도 그것들이 더 큰 애플리케이션의 일부이거나 동일한 팀에서 처리하는 것일지도 모릅니다.

큰 조직에서는 여러 애플리케이션이 있고 완전히 다른 요구 사항과 제약 조건을 가진 여러 팀이 있습니다. 많은 팀이 여러 Git 저장소를 사용할지 또는 모든 애플리케이션에 대해 단일 저장소(monorepo)를 사용할지에 대한 선택에 고민합니다. 물론, 여기에서도 우리만의 권장 사항이 있습니다.

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

"우선 'Monorepo'가 무엇을 의미하는지를 우리가 정의하는 것이 매우 중요합니다. 왜냐하면 많은 관리자와 개발자와의 토론을 통해 백그라운드에 따라 단어의 의미가 다른 것을 명확하게 알 수 있기 때문입니다.

기본적으로 우리는 사람들이 'monorepo'를 특정 Git 조직 구조로 부르는 3가지 다른 영역을 발견했습니다.

개발자들은 주로 소스 코드를 구성할 때 'monorepo'를 언급합니다. 애플리케이션마다 하나의 저장소를 갖는 대신, 일부 팀은 조직 내 모든 애플리케이션의 소스 코드를 그룹화하는 단일 저장소를 선택합니다. 이 기술은 구글에서 지난 수십 년 동안 인기를 얻었으며 'monorepo'에 대한 대부분의 온라인 자료들은 주로 이 기술에 대해 이야기합니다.

여기서 중요한 점은 이 정의가 소스 코드에만 해당한다는 것입니다. Argo CD는 소스 코드를 다루지 않으므로 이러한 방식에 대해 장단점을 언급하는 자료들은 실제로 Argo CD와 관련이 없습니다. 유감스럽게도, Argo CD를 채택할 때 이러한 유형의 기사를 참고하는 관리자들이 많이 보이며 이들은 Argo CD가 쿠버네티스 매니페스트를 관리하는 맥락에서 이러한 소스 코드 기술이 알맞지 않다는 것을 이해하지 못합니다."

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

"모노 리포"에 대한 두 번째 정의는 소스 코드와 쿠버네티스 매니페스트 및 Argo CD 매니페스트를 동시에 포함하는 Git 저장소를 사람들이 "모노 리포"라고 설명하는 경우입니다. 이 기술은 Argo CD와 관련이 있지만, 여러 번 소스 코드와 매니페스트를 분리하는 것을 권장해왔습니다. 이 정의를 전체성을 위해 언급하는 것뿐이며, 몇몇 팀이 "Argo CD 애플리케이션에 모노 리포를 사용한다"고 말하는 경우가 있으며, 실제로는 Git 저장소가 매니페스트와 소스 코드를 모두 포함한다는 것을 의미하는 것입니다 (필수적으로 조직 전체에 대한 단일 Git 저장소를 사용하는 것은 아닙니다).

"모노 리포"의 세 번째 정의이자 본 문서에서 중요하게 다루는 것은 조직이 모든 Argo CD 애플리케이션을 위한 단일 Git 저장소를 갖는 경우입니다. 이 저장소에는 소스 코드가 없지만, 중요한 점은 완전히 관련이 없는 경우에도 모든 배포된 애플리케이션을 포함한다는 것입니다. 그렇다면 이것이 좋은 방법인가요 아닌가요?

# 최상의 방법 - 팀 당 Git 저장소 사용

지난 섹션의 마지막 정의에 따라 정의된 모노 리포는 Argo CD 애플리케이션이 증가함에 따라 발생하는 여러 가지 확장성 및 성능 문제가 있습니다. Argo CD는 이미 모노 리포를 다루기 위한 여러 메커니즘을 가지고 있지만, 더 많은 팀이 Argo CD를 채택함에 따라 문제를 해결하지 말고, 미리 예방하는 것이 좋습니다."

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

단일 모노리포는 Argo CD 여정을 시작할 때 취할 수 있는 매우 논리적인 결정입니다. 단일 저장소를 갖는 것은 유지 보수와 관측성 측면에서 일을 쉽게 만듭니다. 그러나 장기적으로는 Argo CD가 커밋을 감지하는 방식 뿐만 아니라 Git 저장소가 다양한 워크플로와 충돌 및 재시도를 처리하는 방식에도 제약 사항이 몇 가지 있습니다.

저희의 권장사항은 여러 개의 Git 저장소를 사용하는 것입니다. 이상적으로는 각 팀 또는 각 부서에 하나씩 있어야 합니다. 다시 말해 항상 스스로 질문해야 할 기본 질문은 Git 저장소에 포함된 응용 프로그램이 어떤 방식으로 관련되어 있는지입니다. 그것들이 마이크로 서비스이며 더 큰 응용 프로그램의 일부이거나 단일 팀에서 사용하는 해제된 구성 요소일 수 있습니다.

![이미지](/assets/img/2024-05-23-HowtoStructureYourArgoCDRepositoriesUsingApplicationSets_7.png)

여러 개의 Git 저장소의 장점은 성능과 사용성 측면 모두에서 명백합니다. 특히 개발자들에게는, 다수의 Git 저장소가 선호됩니다. 그들은 자신의 팀에 속하지 않는 응용 프로그램들을 다루지 않고 자신들의 Kubernetes 매니페스트에 집중할 수 있기 때문입니다.

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

# 인프라 어플리케이션은 어떻게 할까요?

우리는 Kubernetes manifest(분류 1) 및 Argo CD 리소스(분류 2)에 대해 많이 이야기했습니다. 인프라 어플리케이션(분류 3 및 4)은 어떨까요? 어디에 배치해야 할까요?

인프라 매니페스트를 개발자 어플리케이션과 같은 방식으로 저장할 수 있습니다. 이전 섹션에서 언급한 3단계 구조를 사용하세요. 그러나 반드시 기억해야 할 점은 이 매니페스트를 개발자가 필요한 매니페스트와 혼합해서는 안 된다는 것입니다. 그리고 이들을 분리하는 가장 좋은 방법은 다른 Git 저장소를 갖는 것입니다.

인프라 어플리케이션과 개발자 어플리케이션을 동일한 Git 리포지토리에 혼합하지 마세요. 다시 말하지만, 이것은 Argo CD 성능에 도움이 되는 것뿐만 아니라 개발자를 돕는 좋은 기술입니다.

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

이전 이미지를 업데이트하고 인프라 애플리케이션을 처리하는 팀을 위한 또 다른 Git 저장소를 포함할 수 있습니다.

![이미지](/assets/img/2024-05-23-HowtoStructureYourArgoCDRepositoriesUsingApplicationSets_8.png)

이러한 방식으로 팀 내 개발자로서 나는 내가 관심 있는 것만 포함한 Git 저장소를 체크아웃할 수 있습니다. 해당 Argo CD 애플리케이션은 빠르며 이 Git 저장소에 영향을 미치는 CI 시스템 역시 빠릅니다. Git 충돌이 최소화되며 추가적인 보안 제약을 적용하려면 Git 공급 업체에서 제공하는 기존 Git 메커니즘을 사용할 수 있습니다.

많은 애플리케이션과 팀이 있을 경우 어떤 공통 애플리케이션을 공유해야 할 필요성을 느낄 수 있습니다. 이를 처리하는 방법은 여러 가지가 있습니다. 빠른 방법 중 하나는 다른 팀의 매니페스트를 참조하는 Application Set를 사용하는 것입니다. 또 다른 방법은 모든 팀에서 필요로 하는 애플리케이션을 위해 "공통" Git 저장소를 사용하는 것입니다.

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

앞으로의 글에서는 Git 서브모듈을 사용하여 두 가지 유형의 매니페스트에 대한 고급 시나리오와 공유 기술을 더 많이 다룰 예정입니다.

# 요약

Argo CD 애플리케이션을 구성하고 관리하기 위해 Application Sets를 활용하는 방법에 대한 포괄적인 안내서가 끝났습니다.

다룬 내용은 다음과 같습니다:

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

- 쿠버네티스와 Argo CD manifest의 다른 종류는 무엇인가요?
- 이러한 manifest의 서로 다른 라이프사이클을 이해하는 것이 합리적한 이유는 무엇인가요?
- Argo CD 애플리케이션 세트가 어떻게 작동하는지
- 상호 연결된 애플리케이션 세트의 간단한 3단계 구조를 적용하는 방법은 무엇인가요?
- 피해야 할 일반적인 함정은 무엇일까요?
- 인프라 애플리케이션을 다루는 방법은 무엇인가요?
- 서로 다른 팀을 위해 GitOps 리포지토리를 분할하는 방법은 무엇인가요?

GitOps 리포지토리를 조직하는 방법에 대해 잘 이해하셨길 바라요. Argo CD에 오신 것을 환영합니다 🙂

(Unsplash의 Breno Assis 사진 참고)
