---
title: "아마존 EKS 업그레이드 여정 129에서 130으로 - 귀여운 우베르네티스를 만나보세요"
description: ""
coverImage: "/assets/img/2024-05-27-AmazonEKSUpgradeJourneyFrom129to130-sayhellotocuteUwubernetes_0.png"
date: 2024-05-27 17:41
ogImage:
  url: /assets/img/2024-05-27-AmazonEKSUpgradeJourneyFrom129to130-sayhellotocuteUwubernetes_0.png
tag: Tech
originalTitle: "Amazon EKS Upgrade Journey From 1.29 to 1.30- say hello to cute “Uwubernetes”"
link: "https://medium.com/@marcincuber/amazon-eks-upgrade-journey-from-1-29-to-1-30-say-hello-to-cute-uwubernetes-eba082199cc4"
---

"Uwubernetes" 릴리스를 환영합니다. EKS 제어 평면을 버전 1.30으로 업그레이드하는 과정 및 주의 사항.

![이미지](/assets/img/2024-05-27-AmazonEKSUpgradeJourneyFrom129to130-sayhellotocuteUwubernetes_0.png)

# 개요

AWS EKS 1.30에 오신 것을 환영합니다. 이것은 Kubernetes v1.30 릴리스로, 당신의 클러스터를 더 귀여워 지게 만듭니다!

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

Kubernetes는 전 세계의 수천 명의 사람들이 만들고 공개하는데, 그들은 모든 삶의 영역에서 온 사람들입니다. 대부분의 기여자들은 이를 위해 돈을 받지 않지만, 재미로 만들거나 문제를 해결하거나, 무언가를 배우거나, 커뮤니티를 사랑하기 때문에 만듭니다. 우리 중 많은 사람들이 여기서 집, 친구, 그리고 직업을 찾았습니다. 릴리스 팀은 Kubernetes의 지속적인 성장에 참여하여 영광으로 생각합니다.

한번 더 커뮤니티에 대해 감사 인사를 전하고자 합니다. 많은 회사들에서는 전문가들이 가치를 인정받지 못하고 간과되는 경우가 많지만, 이는 옳지 않습니다. 그래서 이를 대중에게 알리고 싶습니다.

Kubernetes v1.30: Uwubernetes, 이제까지 가장 귀여운 릴리스입니다. 이 이름은 "kubernetes"와 "UwU"라는 행복이나 귀여움을 나타내는 이모티콘의 합성어입니다.

# 이전 이야기와 업그레이드

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

만약 당신이 다음을 찾고 있다면

- EKS를 1.28에서 1.29로 업그레이드하려면 이 이야기를 확인해보세요
- EKS를 1.27에서 1.28로 업그레이드하려면 이 이야기를 확인해보세요
- EKS를 1.26에서 1.27로 업그레이드하려면 이 이야기를 확인해보세요
- EKS를 1.25에서 1.26으로 업그레이드하려면 이 이야기를 확인해보세요
- EKS를 1.24에서 1.25로 업그레이드하려면 이 이야기를 확인해보세요
- EKS를 1.23에서 1.24로 업그레이드하려면 이 이야기를 확인해보세요

![이미지](https://miro.medium.com/v2/resize:fit:836/0*gWRKqyTE1gOT5wiD.gif)

# 업그레이드를 위한 필수 조건

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

Amazon EKS의 Kubernetes v1.30로 업그레이드하기 전에 완료해야 할 중요한 작업이 몇 가지 있습니다. 이 작업들은 "업그레이드 인사이트"에서 쉽게 확인할 수 있습니다. 제 경우에는 항상 클러스터를 최신 상태로 유지하고 있어서 완료해야 할 작업이 없었습니다.

# Kubernetes 1.30- 이 릴리스에서 변경 사항

항상 Kubernetes 버전 1.30에서의 변경 사항과 업데이트의 완전한 목록을 찾으려면 Kubernetes 변경 로그를 확인하십시오. 아래에서 v1.30 릴리스에 중요한 개선 사항 몇 가지를 찾을 수 있습니다. 전체 목록은 여기를 확인해주세요.

## Kubernetes v1.30에서 안정 버전으로 졸업한 개선 사항

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

- Robust VolumeManager 재구성 후 kubelet 재시작. 이것은 kubelet이 시작될 때 기존 볼륨이 어떻게 마운트되는지에 대한 추가 정보를 제공하도록 볼륨 관리자를 재구성하는 것입니다. 사용자나 클러스터 관리자에게는 어떤 변화도 가져오지 않습니다. 이것은 이전 동작으로 되돌아갈 수 있도록 feature process와 feature gate NewVolumeManagerReconstruction을 사용합니다.
- 볼륨 복원 중 무단으로 볼륨 모드 변환 방지. 이 릴리스에서, 볼륨을 PersistentVolume로 복원할 때 컨트롤 플레인은 항상 무단으로 볼륨 모드를 변경하는 것을 방지합니다. 클러스터 관리자로서, 복원 시 그러한 변경을 허용하려면 적절한 신원 원칙(ServiceAccounts representing a storage integration과 같은)에 권한을 부여해야 합니다.
- Pod 스케줄링 준비 상태. 지금 안정적인 상태인 이 기능은 클러스터가 실제로 그 Pod를 노드에 바인딩할 수 있도록 필요한 리소스가 아직 제공되지 않을 때 Kubernetes가 해당 Pod를 스케줄링하려는 시도를 피하도록 합니다. 또한 Pod가 스케줄링될 수 있는지 여부에 대한 사용자 정의 제어를 제공하고 할당량 메커니즘, 보안 제어 등을 구현할 수 있도록 합니다. k8s v1.30에서 .spec.schedulingGates를 지정하여 Pod가 스케줄링에 고려될 준비가 되었는지를 제어할 수 있습니다.
- PodTopologySpread의 최소 도메인. 이번 릴리스에서 PodTopologySpread 제약 조건의 minDomains 매개변수가 안정적으로 졸업했으며 최소 도메인 수를 정의할 수 있게 되었습니다. 이 기능은 Cluster Autoscaler와 함께 사용하도록 설계되었습니다. 저는 개인적으로 Karpenter를 사용하고 있기 때문에 제 상황에서 큰 변화를 가져다주지는 않을 것입니다.

여기에 안정화된 17가지 개선 사항의 전체 목록이 있습니다:

- Container Resource를 기반으로 하는 Pod Autoscaling
- KCCM의 서비스 컨트롤러에서 일시적인 노드 예측 삭제
- k/k를 위한 Go 작업 영역
- Secret 기반 서비스 계정 토큰의 감소
- Admission Control을 위한 CEL
- CEL 기반의 admission webhook match 조건
- Pod 스케줄링 준비 상태
- PodTopologySpread의 최소 도메인
- 볼륨 복원 중 무단으로 볼륨 모드 변환 방지
- API 서버 추적
- 클라우드 듀얼 스택 - 노드 IP 처리
- AppArmor 지원
- kubelet 재시작 후 강력한 VolumeManager 재구성
- kubectl delete: 상호작용(-i) 플래그 추가
- 메트릭 카디널리티 강제 실행
- Pod에 status.hostIPs 필드 추가
- 집합 된 발견

# 테라폼을 사용하여 EKS를 업그레이드하세요

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

내 모든 업그레이드와 마찬가지로, 나는 Terraform을 사용합니다. 이는 빠르고 효율적이며 내 삶을 간편하게 만들어줍니다. 이번 업그레이드에 사용한 프로바이더는 다음과 같습니다:

![이미지1](/assets/img/2024-05-27-AmazonEKSUpgradeJourneyFrom129to130-sayhellotocuteUwernetes_1.png)

![이미지2](/assets/img/2024-05-27-AmazonEKSUpgradeJourneyFrom129to130-sayhellotocuteUwernetes_2.png)

이번에는 컨트롤 플레인의 업그레이드에 약 8분 정도 소요되었습니다. 이것은 정말 빠르다고 생각해요. 업그레이드 후에는 어떠한 문제도 경험하지 않았습니다. 이전 업그레이드에서 있었던 API 서버 자체의 일시적인 사용 불가 상황조차도 전혀 인지하지 못했을 정도입니다. AWS는 EKS 컨트롤 플레인 업그레이드에 소요되는 시간을 줄이는 데 훌륭한 일을 하고 있습니다. EKS 1.29 업그레이드는 8분 24초가 걸렸지만, EKS 1.30은 참고로 4초 더 소요되었습니다.

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

![이미지](/assets/img/2024-05-27-AmazonEKSUpgradeJourneyFrom129to130-sayhellotocuteUwubernetes_3.png)

저는 즉시 워커 노드를 업그레이드하여 업그레이드된 EKS 클러스터에 가입하는 데 약 14분이 걸렸어요. 이 시간은 워커 노드 수와 이전 노드에서 배출해야 하는 포드 수에 따라 다를 수 있어요.

일반적으로 제어 플레인 + 워커 노드의 전체 업그레이드 과정은 약 22분이 걸렸어요. 정말 좋은 시간이라고 말씀드릴 수 있습니다.

저는 개인적으로 Terraform을 사용하여 EKS 클러스터를 배포하고 업그레이드합니다. 여기 EKS 클러스터 리소스의 예시가 있습니다.

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
resource "aws_eks_cluster" "cluster" {
  enabled_cluster_log_types = ["audit"]
  name                      = local.name_prefix
  role_arn                  = aws_iam_role.cluster.arn
  version                   = "1.30"

  vpc_config {
    subnet_ids              = flatten([module.vpc.public_subnets, module.vpc.private_subnets])
    security_group_ids      = []
    endpoint_private_access = "true"
    endpoint_public_access  = "true"
  }

  encryption_config {
    resources = ["secrets"]
    provider {
      key_arn = module.kms-eks.key_arn
    }
  }

  access_config {
    authentication_mode                         = "API_AND_CONFIG_MAP"
    bootstrap_cluster_creator_admin_permissions = false
  }

  tags = var.tags
}
```

워커 노드에는 AMI로 공식 AMI인 아이디 ami-0e6a4f108467d0c54를 사용했습니다. 이 AMI는 유럽 런던 지역인 eu-west-2와 관련이 있을 수 있습니다. 모든 노드를 회전한 후 문제가 없는 것으로 보입니다. 현재 노드들은 다음 버전을 실행 중입니다: v1.30.0-eks-036c24b.

따라서 초기 EKS 1.30 버전 릴리스는 최신 버전 1.30.1이 아닌 첫 번째 1.30.0을 사용 중입니다.

제가 Terraform을 사용하여 EKS 클러스터를 생성하는 데 사용하는 템플릿은 Github 저장소인 https://github.com/marcincuber/eks에서 확인할 수 있습니다.

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

# 관리형 EKS 애드온 업그레이드

이 경우 변경 사항은 사소하며 잘 작동합니다. 애드온의 버전을 간단히 업데이트하면 됩니다. 제 경우 이번 릴리스에서 kube-proxy, coreDNS 및 ebs-csi-driver를 활용했습니다.

# 애드온을 위한 Terraform 자원

```js
resource "aws_eks_addon" "kube_proxy" {
  cluster_name      = aws_eks_cluster.cluster[0].name
  addon_name        = "kube-proxy"
  addon_version     = "v1.30.0-eksbuild.3"
  resolve_conflicts = "OVERWRITE"
}
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

```js
리소스 "aws_eks_addon" "core_dns" {
  cluster_name      = aws_eks_cluster.cluster[0].name
  addon_name        = "coredns"
  addon_version     = "v1.11.1-eksbuild.9"
  resolve_conflicts = "OVERWRITE"
}
```

```js
리소스 "aws_eks_addon" "aws_ebs_csi_driver" {
  cluster_name      = aws_eks_cluster.cluster[0].name
  addon_name        = "aws-ebs-csi-driver"
  addon_version     = "v1.31.1-eksbuild.1"
  resolve_conflicts = "OVERWRITE"
}
```

# EKS 컨트롤 플레인 업그레이드 후

EKS 1.30을 위해 권장되는 핵심 배포 및 데몬 세트를 업그레이드하는 것을 잊지 마세요.

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

- CoreDNS — v1.11.1-eksbuild.9
- Kube-proxy — 1.30.0-eksbuild.3
- VPC CNI — 1.18.1-eksbuild.3
- aws-ebs-csi-driver- v1.31.1-eksbuild.1

위는 AWS의 권장 사항입니다. 모든 구성 요소를 1.30 Kubernetes 버전과 일치하도록 업그레이드하는 것을 고려해보세요. 업그레이드 대상에는 다음이 포함될 수 있습니다:

- 로드 밸런서 컨트롤러
- calico-node
- 클러스터 오토스케일러 또는 Karpenter
- External Secrets Operator
- Kube State Metrics
- Metrics Server
- csi-secrets-store
- calico-typha 및 calico-typha-horizontal-autoscaler
- Reloader
- Keda (이벤트 기반 오토스케일러)
- nvidia 장치 플러그인 (GPU 사용 시에 사용)

## 최종 결과

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
> $ kubectl version                                                                                                                                                                                           [±d1fbdc7c ✓(✹)]
Client Version: v1.30.1
Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3
Server Version: v1.30.0-eks-036c24b
```

제 CLI와 Kubernetes 클러스터 버전을 일치시키기 위해 kubectl를 업그레이드해야 해요.

# 요약 및 결론

이전보다 더 빠르게 EKS 클러스터를 업그레이드했어요. 8분 만에 제어플레인 업그레이드 작업이 완료되었어요. 클러스터 및 노드 업그레이드를 실행하기 위해 Terraform을 사용하고, GitHub Actions 파이프라인을 통해 쉽고 편리하게 작업을 처리할 수 있어요.

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

한국어 번역:

한번 더 중요한 문제가 없었어요. 당신이 수행할 작업도 쉬울 거에요. 모든 작업이 잘 작동했어요. 정말 아무것도 수정할 필요가 없었죠.

EKS에 대한 전체 Terraform 설정에 관심이 있다면, GitHub에서 찾아볼 수 있어요 - [https://github.com/marcincuber/eks](https://github.com/marcincuber/eks).

이 기사가 EKS를 버전 1.30으로 업그레이드하는 중요한 정보들을 모두 모아놓아 잘 정리되었으면 하며, 사람들이 자신의 작업을 빠르게 진행할 수 있도록 도움이 되길 바라요.

요약하면, 쿠버네티스를 싫어하거나/또는 사랑해도 그래도 여전히 사용하고 있어요 ;).

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

아래 표를 Markdown 형식으로 변경해 주십시오.

Enjoy Kubernetes!!!

# Sponsor Me

저의 모든 노트는 공식 AWS와 Kubernetes 소스를 기반으로 합니다.

Medium에 있는 다른 이야기와 마찬가지로, 문서화된 작업을 수행했습니다. 이것은 제 개인적인 연구이며 직면한 문제들에 대한 것입니다.

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

모두 읽어 주셔서 감사합니다. Marcin Cuber
