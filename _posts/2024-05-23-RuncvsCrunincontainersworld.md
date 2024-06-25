---
title: "컨테이너 세계에서 Runc 대 Crun"
description: ""
coverImage: "/assets/img/2024-05-23-RuncvsCrunincontainersworld_0.png"
date: 2024-05-23 14:15
ogImage:
  url: /assets/img/2024-05-23-RuncvsCrunincontainersworld_0.png
tag: Tech
originalTitle: "Runc vs Crun in containers world:"
link: "https://medium.com/@kloudino/runc-vs-crun-in-containers-world-62b8143fd9d3"
---

만약 컨테이너화 기술에 흥미가 있다면, runc에 대해 들어봤을 수도 있어요.

자세한 내용을 설명해 들어가기 전에 컨테이너가 정확히 무엇인지 알려드릴게요.

주로 두 가지 리눅스 커널 모듈, 네임스페이스와 cgroup으로 만들어진 환경 안에서 가상 감옥 또는 격리된 환경을 만들어낸답니다.

- 네임스페이스: 볼 수 있거나 접근할 수 있는 것을 제어합니다.

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

Cgroup: 리소스(예: RAM 및 CPU) 얼마나 사용하는지 확인하는 데 사용됩니다.

![Image](/assets/img/2024-05-23-RuncvsCrunincontainersworld_0.png)

컨테이너 런타임이 무엇인가요?

컨테이너 런타임은 이미 이야기한 컨테이너라고 불리는 격리된 환경을 관리하는 데 도움을 주는 소프트웨어입니다.

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

Runc과 Crun은 현재 사용되는 두 가지 주요 컨테이너 런타임입니다.

그렇다면 containerd 또는 cri-o란 무엇인가요?

이 둘은 모두 runc 위에서 작동하는 컨테이너 런타임의 추상화된 레이어입니다. 이것들은 컨테이너를 관리하기 위한 프론트 엔드로, 즉 컨테이너를 생성, 제거, 시작 및 중지하는 역할을 담당합니다.

![이미지](/assets/img/2024-05-23-RuncvsCrunincontainersworld_1.png)

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

크리올(Containerd)과 크라이오(CRI-O)는 둘 다 컨테이너 런타임으로 runc를 사용합니다.

지금까지는 runc와 crun이 무엇인지 알아보았습니다; 이제 차이를 살펴봅시다.

둘 다 컨테이너 런타임이며 컨테이너를 처리하는 데 동일한 작업을 수행합니다.

하지만 차이점은 다음과 같습니다:

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

1. runc은 Go로 작성되었고 crun은 C로 작성되어서 Linux 커널과 더 호환성이 높아요.

2. runc는 도커에 의해 개발되었고 crun은 RedHat에 의해 개발되었어요.

3. crun은 더 가벼워서 메모리 소비가 낮아요. crun은 300K이고 runc는 15M이에요.

Podman은 어떠세요?

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

포드맨은 컨테이너를 관리하는 데 책임이 있는 crun의 frontend 역할을 하는 도커와 비슷한 유틸리티입니다.

## 요약

![RuncvsCrunincontainersworld_2](/assets/img/2024-05-23-RuncvsCrunincontainersworld_2.png)

결론

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

어떤 것을 사용해야 할까요?

개발 및 프로덕션 환경 모두에서 추가 구성 요소 없이 가볍게 사용할 컨테이너 엔진이 필요하다면 Podman을 선택하세요.

특히 이미지를 생성할 필요가 없는 프로덕션 환경에서는 Podman을 선택하는 것이 좋습니다. 이미지 빌드 구성 요소는 불필요하고 자원을 소비하는 소프트웨어로 간주될 수 있습니다.

그리고 이것이 Docker를 프로덕션 환경에서 사용하지 않아야 하는 또 다른 이유입니다.

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

하지만 쿠버네티스를 사용 중이라면 cri-o를 선택하세요. 그리고 runc에 만족스럽지 않다면 C 언어로 된 가벼운 런타임을 원한다면 crun으로 런타임을 쉽게 전환할 수 있습니다.

kloudino.com (메디 탈레가니)가 작성함
