---
title: "디커Docker가 죽었나요"
description: ""
coverImage: "/assets/img/2024-05-18-Isdockerdead_0.png"
date: 2024-05-18 16:34
ogImage:
  url: /assets/img/2024-05-18-Isdockerdead_0.png
tag: Tech
originalTitle: "Is docker dead?"
link: "https://medium.com/@kloudino/is-docker-dead-27cf8d88a1d9"
---

간단한 답변은 "예"이며, 그 이유는 여기에 있습니다:

최근에 나는 도커가 더 이상 사용되지 않는다고 한 인스타그램 스토리를 올렸어.

일부 사람들은 이 아이디어에 동의하지 않았어;

그래서 나는 자세히 이야기하려고 해.

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

먼저 가상화 구성 요소 (가상 머신 및 컨테이너)에 대해 이야기해 보겠습니다.

가상화는 자원을 공유하는 것이 중요하죠. 그래서 구성 요소를 두 가지 주요 카테고리로 나눌 건데요;

1. 메인 구성 요소

2. 관리 구성 요소

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

<img src="/assets/img/2024-05-18-Isdockerdead_0.png" />

그래서 우리가 볼 수 있는 것은 CPU와 RAM과 같은 컴퓨팅 자원을 공유하는 하나, 저장 공간을 공유하는 하나, 네트워크를 공유하는 하나로 주요 구성 요소가 3개 있다는 것입니다.

이는 가상 머신과 컨테이너 개념 모두에서 작동합니다.

이제 관리 구성 요소를 살펴보겠습니다:

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

![](/assets/img/2024-05-18-Isdockerdead_1.png)

이 3가지 주요 구성요소 외에도 이미지를 관리하는 데 필요한 하나와 API(GUI 또는 CLI)도 필요합니다.

요약하면, 가상화 세계에서 작동할 수 있도록 5개 구성 요소가 필요합니다.

컨테이너의 세계에서 docker는 모든 이러한 구성 요소를 제공하기 때문에 이미 docker가 모든 솔루션을 갖춘 것처럼 좋다고 생각할 수 있습니다. 그러나 조금만 기다려보세요. 더 설명해 드릴게요:

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

하지만 실제로 제품 환경에서 모든 구성 요소가 필요한지 정말로 필요할까요? 제 생각에는 아니죠. 제품 환경에서 이미지를 빌드할 필요는 없습니다. 제품 환경에서는 Kubernetes와 같은 다른 Orchestration 도구를 사용해야 할 수 있습니다. 따라서 발생하는 상황은 꼭 필요하지 않은 많은 도구와 레이어가 설치되어 있는 제품 환경입니다.

하지만 Docker와 같은 올인원 솔루션이 개발 환경에서는 괜찮아 보입니다. 저는 개인적으로 선호하지는 않습니다.

원하시는 대로 개별적으로 필요한 도구들을 설치하고 사용할 수 있습니다.

여기에는 필요에 따라 설치하고 사용할 수 있는 이러한 구성 요소(도구)에 대한 간단한 소개가 있습니다.

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

Podman: 컨테이너 생성 및 삭제에는 레드햇과 오픈 소스 커뮤니티에서 적극 유지보수 중인 Podman을 사용할 수 있습니다.

Buildah: 레드햇에서도 지원하는 이미지 생성용 빌더입니다.

Kaniko: 구글이 개발한 이미지 생성 도구로, 구글은 지원하지 않지만 오픈 소스 커뮤니티에서 활발히 유지보수 중입니다.

네트워킹 부분에서는 Podman을 사용하면 Netavark라는 네트워킹 백엔드가 함께 제공되지만, 다른 CNI로 변경할 수도 있습니다.

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

CNI은 컨테이너 네트워킹 인터페이스의 약자로, 컨테이너(팟)의 네트워킹을 관리하는 데 사용됩니다.

weave, flannel, calico 및 cilium과 같은 많은 CNI가 있습니다.
