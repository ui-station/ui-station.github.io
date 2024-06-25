---
title: "Azure IAM을 활용하여 Kubernetes 리소스를 최적화해보세요 관리형 vs 워크로드 ID"
description: ""
coverImage: "/assets/img/2024-05-18-OptimizeYourKubernetesResourceswithAzureIAMManagedvsWorkloadIdentity_0.png"
date: 2024-05-18 16:47
ogImage:
  url: /assets/img/2024-05-18-OptimizeYourKubernetesResourceswithAzureIAMManagedvsWorkloadIdentity_0.png
tag: Tech
originalTitle: "Optimize Your Kubernetes Resources with Azure IAM: Managed vs. Workload Identity"
link: "https://medium.com/itnext/simplify-secure-your-azure-resources-managed-identity-vs-workload-identity-fe49d133fc03"
---

## Azure Kubernetes Service (AKS) 배포를 위한 최고의 Identity 솔루션 공개

![image](https://miro.medium.com/v2/resize:fit:1400/1*cZynTTP_7qDHrix67vOYyQ.gif)

Azure의 Identity 옵션에 혼란스러워 하고 계신가요? 걱정하지 마세요! 이 블로그는 그 이유들을 명확히 설명하고, 여러분이 완벽한 솔루션을 선택할 수 있도록 도와줍니다!

# 소개

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

Microsoft의 관리 ID 개념을 자세히 살펴보기 전에 이전에 어떻게 작동했는지와 Microsoft가 이 혁신으로 해결하려고 했던 도전 과제를 포함한 다른 측면을 먼저 살펴봅시다. 엔지니어의 관점에서 보면 일반적으로 Azure Kubernetes Services(AKS) 클러스터에 배포된 작업 부하는 Microsoft Entra 애플리케이션 자격 증명이나 관리 ID를 사용하여 Azure Key Vault 및 Microsoft Graph와 같은 Microsoft Entra 보호된 리소스에 액세스해야 합니다. Microsoft Entra Workload ID는 외부 ID 제공자와의 연합을 위해 쿠버네티스의 기본 기능과 통합됩니다.

다음은 다른 개념 간의 간단한 차이점입니다:

- 서비스 주체(Service Principal): 기본적으로 특정 Azure AD 테넌트 또는 다른 Microsoft 서비스 내의 리소스에 액세스하기 위해 응용 프로그램, 서비스 또는 자동화 도구용으로 만든 ID입니다. Azure 리소스에 한정되지 않으며 다양한 Microsoft 서비스에 인증 및 액세스에 사용할 수 있습니다.
- Pod-Identity (폐기 예정): Microsoft Entra 포드 관리 ID는 Azure 리소스 및 Microsoft Entra ID의 관리 ID를 포드와 연결하기 위해 쿠버네티스 원시 개념을 사용합니다. 관리자는 포드가 Microsoft Entra ID를 ID 제공자로 사용하는 Azure 리소스에 액세스할 수 있도록 쿠버네티스 원시로 ID와 바인딩을 생성합니다.
- 관리 ID(Managed Identity): Microsoft Entra ID (이전 Azure AD) 테넌트 내의 서비스 또는 리소스에 대한 ID를 제공하는 Azure의 기능입니다. Azure에서 실행되는 애플리케이션 및 서비스에서 사용하는 자격 증명 관리를 간소화하기 위해 설계되었습니다.
- 연합 ID 자격 증명을 사용한 작업 ID(Workload Identity with Federation Identity Credentials): Microsoft Entra Workload ID는 Service Account Token Volume Projection을 사용하여 포드가 쿠버네티스 ID(즉, 서비스 계정)를 사용할 수 있도록 합니다. 쿠버네티스 토큰이 발급되고 OIDC 연합을 통해 쿠버네티스 애플리케이션은 주석이 달린 서비스 계정을 기반으로 Microsoft Entra ID를 통해 Azure 리소스에 안전하게 액세스할 수 있습니다.

요약하면, 관리 ID는 Azure 리소스를 위해 설계된 특정 유형의 ID로 Azure 서비스에 대한 인증 및 액세스를 보다 안전하게 관리하기 쉽게합니다. 반면에 서비스 주체는 Azure를 포함한 Microsoft 서비스 전반에 사용할 수 있는 더 일반적인 ID로, Azure를 포함한 다양한 목적을 위해 명시적으로 생성하고 구성할 수 있습니다.

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

Azure Kubernetes Service (AKS)에서 어떻게 활용될 수 있는지 살펴봅시다. 간단한 소개부터 시작해서 실제 예제까지 살펴보겠습니다.

# Azure Kubernetes Service 및 관리 ID

Azure Kubernetes Service(AKS)는 응용 프로그램을 보호하기 위한 두 가지 주요 식별 옵션을 제공합니다:

## 관리 ID:

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

- AKS 클러스터 또는 더 구체적으로 Virtual Machine Scale Sets (VMSS)에 대한 Azure 리소스용 관리 ID.
- 자격 증명이 필요하지 않은 다른 Azure 리소스에 대한 Azure RBAC 액세스 제공.

두 가지 유형:

- 시스템 할당: 리소스 수명 주기에 묶임.
- 사용자 지정: 여러 리소스에 할당할 수 있음.

## 연합 자격 증명을 활용한 워크로드 ID:

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

- AKS 팟에서 실행되는 작업 부하를 위한 신원을 제공합니다.
- 팟 내에 비밀을 저장하지 않고 Azure 리소스에 액세스할 수 있습니다.
- Microsoft Entra Workload ID는 서비스 어카운트 토큰 볼륨 프로젝션을 사용하여 팟이 쿠버네티스 신원(즉, 서비스 어카운트)을 사용할 수 있게 합니다. 쿠버네티스 토큰이 발급되며 OIDC 연합을 통해 주석이 달린 서비스 어카운트를 기반으로 Azure 리소스에 안전하게 액세스할 수 있습니다.

## 예시:

Azure Key Vault에 액세스해야 하는 애플리케이션이 있는 AKS 클러스터를 고려해보겠습니다.

Managed Identity를 사용하는 경우:

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

클러스터를 생성할 때는 --enable-managed-identity 플래그를 설정하여 관리 식별 증명을 생성할 수 있습니다. 이 작업은 Microsoft.ManagedIdentity/userAssignedIdentities의 백그라운드 생성을 트리거합니다.

이 작업의 명령 구문은 다음과 같습니다:

```js
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

생성된 식별 증명은 그 후 Azure Key Vault 또는 DNS Zones와 같은 리소스에 액세스할 수 있도록 권한을 부여받을 수 있습니다. 이 식별 증명은 VMSS(Virtual Machine Scale Sets)에 할당됩니다. 각 노드 또는 VM은 kubelet을 실행하며, 이를 통해 External-DNS와 같은 팟이 관리 식별 증명을 통해 리소스에 액세스합니다. 아래 다이어그램은 두 개의 관리 식별 증명이 있는 두 노드 풀을 더 명확하고 안전한 시스템 및 응용 프로그램 팟 간에 시스템적으로 접근을 세밀하게 제어할 수 있는 구조를 제공합니다.

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

<img src="https://miro.medium.com/v2/resize:fit:1400/1*9VftZEvhy_k_eskwir2eKw.gif" />

다음은 프로세스의 간결한 산문 요약입니다:

1. AKS 클러스터에 사용자 지정 관리 ID 할당: 이 단계는 Azure Kubernetes Service (AKS) 클러스터에 사용자 지정 관리 ID를 구성하는 것을 포함합니다. 이렇게 함으로써 AKS 클러스터 내에서 실행되는 각 응용 프로그램이나 서비스가 각각의 자격 증명을 요구하지 않고 Azure 서비스 인증을 위해이 ID를 활용할 수 있습니다.

2. 관리 ID에 Azure Key Vault 액세스 권한 부여: 관리 ID가 AKS 클러스터에 할당된 후 다음 단계는 이 ID가 Azure Key Vault에 액세스 할 필요 권한을 부여하는 것입니다.이 권한 설정은 Key Vault에 저장된 비밀, 키 및 인증서와 같은 민감한 정보에 안전하게 액세스하기 위해 중요합니다.

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

3. 관리 식별자를 할당한 AKS 클러스터가 Azure Key Vault에 액세스하는 애플리케이션: AKS 클러스터에 할당된 관리 식별자가 Azure Key Vault에 액세스 권한을 부여받으면 AKS 클러스터 내에서 실행되는 애플리케이션은 이제 Azure Key Vault에 안전하게 액세스할 수 있습니다. 이는 명시적 자격 증명이 필요하지 않기 때문에 최소 권한 원칙을 준수하고 응용 프로그램의 보안 기본을 강화합니다.

이 원활한 통합을 통해 AKS에 배포된 애플리케이션의 비밀 관리 및 액세스 제어 프로세스가 간소화되며, Azure의 관리 식별자 및 Key Vault 서비스를 활용하여 견고하고 안전한 클라우드 네이티브 환경을 제공합니다.

하지만 이제 더 잘 작동하는 방법을 살펴보겠습니다.

페더레이티드 식별자 자격 증명과 함께 Workload Identity 사용하기

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

Azure Kubernetes Service (AKS) 클러스터에 워크로드 ID를 설정할 때는 --enable-managed-identity 및 --enable-oidc-issuer 플래그를 모두 지정해야 합니다. 예를 들어 다음과 같이 실행합니다:

```js
az aks create -g myResourceGroup -n myManagedCluster --enable-oidc-issuer --enable-workload-identity
```

- --enable-oidc-issuer: 이 옵션은 AKS 클러스터에 대한 OpenID Connect (OIDC) 기능을 활성화합니다. OIDC를 사용하면 애플리케이션이 Azure Active Directory (Azure AD)를 사용하여 인증할 수 있습니다.
- --enable-workload-identity: 이 옵션은 AKS 클러스터에 대한 Azure Workload Identity를 활성화합니다. Workload Identity를 사용하면 클러스터의 팟이 명시적인 자격 증명을 저장하지 않고도 Azure 리소스에 액세스할 수 있습니다.

다음 figure 3은 과정을 추상적으로 보여줍니다.

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

![Image](https://miro.medium.com/v2/resize:fit:1400/1*cZynTTP_7qDHrix67vOYyQ.gif)

Figure 2와는 반대로, 여기서는 가상 머신 규모 집합(VMSS)의 kubelet ID 대신 AKS 클러스터가 발행자 역할 (--enable-oidc-issuer)로 작동하여 해당 워크로드에 대한 토큰을 생성합니다. 이는 관리 신원이 생성되고, 그런 다음 하나 이상의 Azure 리소스에 액세스 할 수 있도록 권한이 부여됩니다. 이 권한은 자격 증명의 연맹을 통해 ServiceAccount에 매핑되며, 이 예에서 우리는 AKS 클러스터를 발행자로 사용하여 액세스 토큰을 발급합니다.

하지만 내부 작동 원리는 무엇인가요?

기본 보안 모델에서 AKS 클러스터는 토큰의 발급자 역할로 작동합니다. OpenID Connect를 사용하여, OAuth 2.0 위에 구축된 프로토콜을 통해 Microsoft Entra ID는 서비스 계정 토큰의 진위를 확인하는 데 필수적인 공개 서명 키를 발견합니다. 이 확인 프로세스는 Microsoft Entra 토큰으로 교환되기 전에 토큰의 정당성을 확인하는 데 중요합니다. 서비스 계정 토큰을 Microsoft Entra 토큰으로 교환하는 과정은 Azure Identity 클라이언트 라이브러리나 Microsoft Authentication Library를 통해 가능하며, 이를 통해 워크로드는 권한을 담언하는 토큰을 활용하여 Azure 리소스에 안전하게 액세스할 수 있습니다. 이 시스템은 Microsoft Entra ID에 의해 확인된 토큰만이 리소스에 액세스하는 데 사용될 수 있도록 하여 최소 권한의 원칙을 유지함으로써 보안성을 강화합니다.

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

표 4는 작업 흐름을 보여줍니다:
![workflow](/assets/img/2024-05-18-OptimizeYourKubernetesResourceswithAzureIAMManagedvsWorkloadIdentity_0.png)

알겠어요! Azure는 어떻게 특정 파드만 Workload Identity를 받아야 한다는 것을 알 수 있을까요?

Azure는 레이블 및 주석을 사용하여 어떤 파드가 Workload Identity를 받아야 하는지 제어하여 올바른 레이블이 있는 경우에만 해당 Azure 리소스에 ServiceAccount를 통해 액세스할 수 있도록 합니다. 이 설정은 Kubernetes 배포 내에서 구성됩니다.

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

배포 후에는 다음과 같이 나타납니다:

![이미지 1](/assets/img/2024-05-18-OptimizeYourKubernetesResourceswithAzureIAMManagedvsWorkloadIdentity_1.png)

Azure에서는 다음과 같이 나타납니다:

![이미지 2](/assets/img/2024-05-18-OptimizeYourKubernetesResourceswithAzureIAMManagedvsWorkloadIdentity_2.png)

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

이 경우에는 애플리케이션이 Azure 키 저장소에 액세스하여 비밀을 가져오고 데이터베이스에 액세스할 수 있으며 역할 할당을 확인할 수 있습니다:

![이미지](/assets/img/2024-05-18-OptimizeYourKubernetesResourceswithAzureIAMManagedvsWorkloadIdentity_3.png)

다중 매핑은 가능한가요?

네, 마이크로 소프트 엔트라 워크로드 ID에서는 서비스 계정과 관련된 여러 매핑이 가능합니다. 서비스 계정을 마이크로 소프트 엔트라 객체와 연결하는 다양한 구성을 지원합니다:

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

- 일대일 매핑은 단일 서비스 계정이 하나의 Microsoft Entra 개체를 참조할 수 있도록 합니다.
- 다대일 매핑은 여러 서비스 계정이 동일한 Microsoft Entra 개체를 참조할 수 있도록 허용합니다.
- 일대다 매핑은 단일 서비스 계정이 클라이언트 ID 주석을 변경하여 여러 Microsoft Entra 개체를 참조할 수 있도록 합니다.

어떤 제한 사항이 있을까요?

- 관리되는 ID 당 페더레이티드 ID 자격 증명을 최대 20개까지만 보유할 수 있습니다.
- 페더레이티드 ID 자격 증명을 처음 추가한 후에도 전파되기까지 몇 초가 소요됩니다.
- 오픈 소스 프로젝트인 Virtual Kubelet을 기반으로 하는 가상 노드 추가 기능은 지원되지 않습니다.
- 특정 지역에서는 사용자 지정 관리 ID에서 페더레이티드 ID 자격 증명을 생성하는 것이 지원되지 않습니다.

# 정리

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

마무리하며, Azure와 Kubernetes 환경에서 적절한 신원 모델을 선택하는 미묘한 차이를 이해하는 것이 중요합니다.

관리 신원은 Azure 리소스가 다른 Azure 서비스와 상호 작용해야 하는 시나리오에서 빛을 발합니다. Azure 내에서의 간편함과 통합성으로 인해 액세스 관리에 대한 복잡성을 줄이며, 수동으로 자격 증명을 처리하는 것보다 이상적인 선택지가 됩니다.

반면, 피더레이션 신원 자격 증명을 사용하는 워크로드 신원은 동적이고 단기적인 신원이 필요한 환경에서 빛을 발합니다. 이 모델은 특히 Kubernetes 애플리케이션에 적합하며, Azure 리소스로의 액세스 관리를 안전하고 간소화된 방식으로 제공합니다. 액세스 권한을 세밀하게 제어하여 더 맞춤화된 보안 포지션을 제공합니다.

Managed Identities를 사용하는 주요 이점 중 하나는 Managed Identity(MI)의 kubelet 신원에 의존하는 대신, 제공하는 액세스 제어의 세분성입니다. MI를 사용한 kubelet 신원은 VMSS에서 실행되는 모든 Pod에 대해 Azure 리소스에 대한 넓은 액세스 수준을 제공하는 반면, 워크로드 신원은 애플리케이션 또는 서비스 당 액세스 권한을 정의하여 더 정확한 제어가 가능합니다.

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

또한, Workload Identity는 ServiceAccounts에 대한 매핑을 용이하게 해주어 하나 이상의 애플리케이션이 필요한 Azure 리소스에 대한 접근을 얻을 수 있도록 도와줍니다. 이 유연성은 더 구체적인 액세스 권한을 제공하여 애플리케이션의 요구에 따라 더욱 보안 프레임워크를 강화합니다.

Otterize와 같은 도구를 사용하면 토큰이 워크로드가 필요한 만큼만 살아 있도록 보장하여 잠재적인 공격 표면을 최소화함으로써 보안을 강화할 수 있습니다.

마지막으로, Managed Identity는 리소스 그룹 내에 존재하는 Azure 리소스임을 명심해야 합니다. 이는 미국 Entra ID 테넌트와 구별되며 전역적인 존재를 갖습니다. 이 차이점은 애플리케이션에서 사용하는 ID의 범위와 수명주기를 이해하는 데 중요합니다.

Managed Identity와 Workload Identity, 그리고 연합 ID 자격 증명을 사용하여 신중한 선택을 통해 보안과 기능성에 균형을 맞출 수 있습니다. 애플리케이션이 필요로 하는 액세스를 제공하되 보안 원칙을 저해하지 않도록 보장합니다.

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

# 연락처 정보

질문이 있거나 친근하게 이야기를 나누고 싶거나 놓치지 말아야 할 주제가 있다면, 중간의 코멘트 기능을 사용하지 말고 자유롭게 나의 LinkedIn 네트워크에 추가해 주세요!

# 참고 자료

- https://kubernetes.io/docs/reference/access-authn-authz/authentication/#openid-connect-tokens
- https://learn.microsoft.com/en-us/azure/aks/workload-identity-overview?
- Pod Identity (Deprecated) — https://learn.microsoft.com/en-us/azure/aks/use-azure-ad-pod-identity
- Microsoft 엔트라 Workload ID
- OIDC 연합
- https://learn.microsoft.com/en-us/azure/aks/workload-identity-overview?tabs=dotnet
- 꼭 놓치지 마세요 — ` https://medium.com/itnext/kubernetes-automate-workload-iam-on-azure-with-otterize-860faa221eac
