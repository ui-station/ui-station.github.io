---
title: "쿠버네티스를 시작할 때 알았더라면 좋았을 것 같은 점"
description: ""
coverImage: "/assets/img/2024-05-27-WhatIWishIKnewWhenIGotStartedwithKubernetes_0.png"
date: 2024-05-27 17:33
ogImage: 
  url: /assets/img/2024-05-27-WhatIWishIKnewWhenIGotStartedwithKubernetes_0.png
tag: Tech
originalTitle: "What I Wish I Knew When I Got Started with Kubernetes"
link: "https://medium.com/aws-in-plain-english/what-i-wish-i-knew-when-i-got-started-with-kubernetes-177cf717f5ef"
---


Kubernetes를 처음 봤을 때는 복잡해 보일 수 있지만, 이해해야 할 수많은 핵심 개념과 용어가 있습니다. 더욱 사실적인 것은 Kubernetes에 대해 깊이 파고들기 위해서는 많은 실전 실습이 필요하다는 점입니다.

그래서 이 블로그 포스트에서는 Kubernetes를 처음 시작할 때 알았으면 하는 내용을 공유하고자 합니다. 함께 실전학습을 위한 예시 로컬 클러스터를 설정해봅시다.

![이미지](/assets/img/2024-05-27-WhatIWishIKnewWhenIGotStartedwithKubernetes_0.png)

# Kubernetes를 배워야 하는 이유?

<div class="content-ad"></div>

OpenAI가 쿠버네티스에 배포되어 7,500개의 워커 노드로 확장되고 있다는 걸 알고 계셨나요? 그리고 Apple과 같은 기술 거물들은 2022년에 아파치 메소스에서 쿠버네티스로 전환하여 쿠버네티스를 주요 컨테이너화 플랫폼으로 채택했어요.

쿠버네티스가 마이크로서비스 아키텍처에 어떻게 적합한지 이야기할 때, 느슨하게 결합된 단위의 단일 개념을 컨테이너에 넣거나 서버리스 함수를 활용하는 것은 모두 타당한 해결책입니다.

컨테이너는 응용 프로그램과 해당 종속성을 쉽게 한 컴퓨팅 환경에서 다른 환경으로 쉽게 이동할 수 있도록 패키지화하고 격리하는 방법입니다. 더불어 애플리케이션을 일관되고 신뢰할 수 있게 배포하는 데 도움이 되며, 사용자의 컴퓨터 설정과 무관하게 작동합니다.

2024년에 올바른 방법으로 쿠버네티스를 배우는 데 도움이 되는 미디움 포스트를 작성했어요. 그리고 이 분야에서 경력을 시작하기 위해 학습을 확장하고 싶다면, 쿠버네티스 인증을 취득하는 것이 좋은 길일 거예요. 아래 책이 도움이 될 거예요.

<div class="content-ad"></div>

# 관리 Kubernetes 플레이그라운드 활용하기

시작하기 전에, 시장에는 많은 관리 Kubernetes 플레이그라운드가 있음을 감안해야 합니다.

- 관리 Kubernetes 플레이그라운드는 인프라를 설정하고 관리하는 수고를 들이지 않고 Kubernetes 클러스터를 실험할 수 있는 기회를 제공합니다. 보통 미리 구성된 Kubernetes 환경을 제공하여 사용자가 웹 인터페이스나 명령줄 인터페이스를 통해 액세스할 수 있습니다.
- 이러한 플랫폼은 교육 자료와 대화형 튜토리얼도 제공하는데, 새로운 Kubernetes 기능들을 번거롭지 않은 환경에서 사용해보는 좋은 방법입니다.

## Killercoda 플레이그라운드

<div class="content-ad"></div>

Killercoda는 로컬 설정이나 자원 소모가 많은 브라우저의 귀찮음 없이 완전히 기능적인 Linux 또는 Kubernetes 환경에 즉시 브라우저 액세스를 제공하는 가상 놀이터입니다. 원격 유지 보수는 로컬에서 액세스했을 때 원활한 사용성을 보장합니다. 최신 상태를 유지하기 위한 계속된 헌신으로, Killercoda는 지난릴리즈 후 몇 주만에 최신 Kubeadm Kubernetes 버전을 지속적으로 업데이트합니다.

이 게시물 작성 시점을 기준으로 사용자들은 두 개의 2GB 노드로 구성된 초기 빈 Kubeadm 클러스터에 액세스를 즐길 수 있습니다:

![Kubernetes Image](/assets/img/2024-05-27-WhatIWishIKnewWhenIGotStartedwithKubernetes_1.png)

## 킬러 셸 플레이그라운드

<div class="content-ad"></div>

특정 시나리오나 시험 시뮬레이션을 찾고 있는 분들은 CKS, CKA, CKAD, LFCS, 그리고 LFCT 시험을 위해 디자인된 Killer Shell을 살펴보세요. Killer Shell은 실제 시뮬레이터와 인터랙티브 학습 환경을 제공하여 사용자가 시나리오를 철저히 탐구하고 편리하게 학습할 수 있도록 돕습니다. 위 시험 중 하나를 패스하고 싶다면, 공식 CKA 시험 시뮬레이터인 Killer Shell에서 제공되는 샘플 시나리오를 살펴보는 것을 강력히 추천합니다.

또한, 실전 중심 시험에서의 시간 관리가 매우 중요합니다. 공식 문서의 특정 페이지를 북마크해두는 것은 당신의 준비에 매우 도움이 될 것입니다.

또한, Kubernetes를 배우기 시작하는 플레이리스트를 준비했습니다. 관심이 있다면 확인해보세요.

## Kubernetes Playground에서 Kubernetes와 놀아보세요

<div class="content-ad"></div>

Play with Kubernetes는 도커가 제공하는 랩 사이트로, K8s 플레이그라운드를 제공합니다. 사용자는 빠르게 K8s 클러스터를 시작하고 브라우저에서 직접 무료 Alpine Linux VM을 경험할 수 있습니다. Docker-in-Docker (DinD)를 사용하여 여러 VM/PC를 시뮬레이션하여 원활한 경험을 제공합니다. 그들의 안내에 따라 GitHub 저장소에서 최신 정보를 확인하세요.

플레이그라운드를 이용하는 것은 Kubernetes를 배우고 배운 내용을 실험하면서 시간을 절약하는 좋은 방법입니다.

# Kubernetes 노드 아키텍처

Kubernetes는 단일 노드 및 다중 노드 아키텍처를 모두 지원하며, 각각의 장점과 사용 사례가 있습니다. Kubernetes 클러스터 아키텍처에 대해 더 자세히 알고 싶다면 이 포스트를 확인해보세요.

<div class="content-ad"></div>

여기서 각각의 것을 살펴보겠습니다 :

## 단일 노드 아키텍처

단일 노드 아키텍처에서는 모든 Kubernetes 구성 요소가 한 대의 기계에서 실행됩니다. 이 설정은 단순성이 확장성과 고가용성보다 우선이 되는 로컬 개발 환경이나 테스트 환경에 적합합니다.

단일 노드 배포를 지원하는 Kubernetes 배포 중 하나는 Minikube입니다. Minikube는 사용자의 워크스테이션에서 로컬로 실행되는 가벼운 Kubernetes 클러스터를 제공하여 개발자가 Kubernetes 기능과 응용 프로그램을 쉽게 실험할 수 있도록 하며 완전한 규모의 클러스터가 필요하지 않습니다.

<div class="content-ad"></div>

다른 예로는 MicroK8s가 있습니다. MicroK8s는 엣지 컴퓨팅 및 사물인터넷(IoT) 디바이스를 위한 최소한의 단일 노드 Kubernetes 배포를 제공합니다. 이러한 Kubernetes 배포는 단일 노드 클러스터의 설정과 관리를 간소화하여 개발자들이 Kubernetes 개발 및 테스트를 시작하기 편리하게 만듭니다.

![Image](/assets/img/2024-05-27-WhatIWishIKnewWhenIGotStartedwithKubernetes_2.png)

## 다중 노드 아키텍처

그러나 대부분의 엔터프라이즈 환경은 주로 고유노드 클러스터를 만족시키기 위해 단일 노드 클러스터보다 많은 노드를 필요로 합니다. 이러한 환경은 주로 다중 노드 설정입니다.

<div class="content-ad"></div>

Kubernetes에서 다중 노드 아키텍처는 Kubernetes 구성 요소를 여러 대의 머신에 분산하여 중복성과 확장성을 제공합니다. 이는 고가용성 및 오류 허용성을 가능하게 하며, 프로덕션 환경 및 대규모 배포에 이상적입니다.

다중 노드 클러스터를 구성하고 관리하는 것은 단일 노드보다 더 복잡하며 최적의 성능과 자원 활용을 보장하기 위해 신중한 계획이 필요합니다.

최종적으로, 단일 노드 및 다중 노드 아키텍처 사이의 선택은 Kubernetes 배포의 구체적인 요구 사항과 목표에 따라 다릅니다.

<div class="content-ad"></div>

# 첫 번째 로컬 쿠버네티스 클러스터 설정하기

minikube를 사용하여 Kubernetes 클러스터를 만드는 것은 로컬 Kubernetes 클러스터를 생성하는 가장 쉬운 방법이며 몇 분만에 완료할 수 있습니다. 다음은 해야 할 일입니다:

## minikube 설치

로컬이나 클라우드 기반 Linux VM에서 minikube 이진 파일을 검색하기 위해 curl 명령을 사용하고, 다음과 같이 /usr/local/bin/minikube에 설치하십시오:

<div class="content-ad"></div>

```js
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

다음 단계로 넘어가기 전에 minikube 이진 파일이 설치되었는지 확인하려면 /usr/local/bin/minikube로 이동하십시오. 또한 터미널에 다음을 입력하여 Minikube를 사용할 수 있는지 확인할 수도 있습니다 :

```js
minikube –-help
```

## 단일 노드 Kubernetes 클러스터 프로비저닝

<div class="content-ad"></div>

Minikube를 사용하여 단일 노드 Kubernetes 클러스터를 프로비저닝하려면 단순히 다음 명령을 사용할 수 있어요 :

```js
minikube start
```

만약 minikube 클러스터를 시작할 때 CPU 코어와 메모리를 설정하고 싶다면, 메모리 및 CPU 플래그를 추가하여 다음과 같이 실행하세요 :

```js
minikube start --memory 8192 --cpus 4
```

<div class="content-ad"></div>

명령이 실행된 후, 당신의 minikube 클러스터는 프로비저닝 프로세스에 있습니다. 마지막에는 minikube Kubernetes 클러스터를 사용할 준비가 되었다는 메시지가 표시될 것입니다.

## 설치 확인

Minikube 클러스터는 단일 노드로 구성되어 있으며, 통제 평면 및 워커 노드 역할을 수행합니다. 이 설정을 통해 구성된지 한 뒤에는 로컬 Kubernetes 클러스터 내에서 작업 로드를 예약하기 시작할 수 있습니다. 노드가 사용 준비되었는지 확인하려면 다음 명령을 사용할 수 있습니다:

```js
Kubectl get node
```

<div class="content-ad"></div>

이 명령어의 단축키를 사용해보세요:

```js
alias k=kubectl 
k get no
```

출력 결과를 통해 다음 정보를 확인할 수 있습니다:

- 노드의 상태(사용 가능 여부)
- 해당 노드의 역할
- Kubernetes 버전
- 초기 배포 이후의 노드 연령

<div class="content-ad"></div>

## Minikube 클러스터 구성

만약 새로운 클러스터를 다시 제공하지 않고 minikube 클러스터를 구성하고 싶다면, minikube 클러스터를 중지해야 합니다.

Bash

```js
minikube stop
```

<div class="content-ad"></div>

minikube config set 명령어를 사용하면 원하는 설정을 minikube 클러스터에 적용할 수 있어요.

Minikube 클러스터 구성을 마치면 클러스터를 시작해야 해요. 그 후에는 새 구성으로 클러스터에서 작업할 거예요.

더 많은 메모리와 CPU를 사용하여 minikube 클러스터를 구성해볼까요:

```js
minikube stop

minikube config set memory 8192
minikube config set cpus 4
minikube start
```

<div class="content-ad"></div>

이제 미니큐브 클러스터를 사용할 준비가 되었어요.

## 미니큐브 클러스터 삭제

로컬 Kubernetes 클러스터와 모든 프로파일을 삭제합니다 :

```js
minikube delete --all
```

<div class="content-ad"></div>

# 실습이 최고입니다!

다음 비디오에서 몇 분 만에 Minikube 클러스터를 쉽게 설정하는 방법을 보여드립니다. Apple 실리콘 (ARM 기반)인 M1/M2 맥북과 같은 환경을 설정합니다:

여기 릴리스 노트에 기능이 특징화된 최신 Kubernetes 릴리스를 빠르게 테스트하기 위해 이 섹션에서 배운 내용을 복제할 수 있습니다.

# 기대가 됩니다!

<div class="content-ad"></div>

로컬 Kubernetes를 쉽게 설정할 수 있는 다양한 옵션이 많이 있다는 것을 기쁘게 생각해요. 대부분의 플레이그라운드는 무료로 제공되니 현명한 선택을 해서 다양한 방법을 시도해보세요. 이 블로그가 2024년 Kubernetes를 시작하려는 분들에 도움이 되길 진심으로 바랍니다! 더 알고 싶다면 제 미디엄 팔로우나 YouTube 채널을 구독해주시고, 댓글로 의견을 공유해주세요.

# 간단하게 설명하자면 🚀

In Plain English 커뮤니티에 함께해주셔서 감사합니다! 떠나시기 전에:

- 꼬옥 박수를 치시고 작가를 팔로우해주세요 ️👏️️
- 팔로우하기: X | LinkedIn | YouTube | Discord | Newsletter
- 다른 플랫폼 방문: Stackademic | CoFeed | Venture | Cubed
- PlainEnglish.io에서 더 많은 콘텐츠를 만나보세요