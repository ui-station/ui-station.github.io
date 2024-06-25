---
title: "도커 대 Podman 안전한 오케스트레이션의 새 시대"
description: ""
coverImage: "/assets/img/2024-05-23-DockervsPodmanANewErainSecureOrchestration_0.png"
date: 2024-05-23 14:14
ogImage:
  url: /assets/img/2024-05-23-DockervsPodmanANewErainSecureOrchestration_0.png
tag: Tech
originalTitle: "Docker vs Podman: A New Era in Secure Orchestration"
link: "https://medium.com/gitconnected/docker-vs-podman-a-new-era-in-secure-orchestration-957ea2123098"
---

탐구하는 Root vs Rootless Orchestration: 보안 관점에서

안녕하세요, 기술 애호가 여러분! 😊 오늘은 컨테이너 오케스트레이션의 매혹적인 세계로 빠져들어보겠습니다. 이 도구들이 나오기 전에는 개발자들이 수동 배포의 고통, 표준화 부족 (내 컴퓨터에서는 동작하는)으로 인한 복잡하고 오류가 발생하기 쉬운 과정을 겪어야 했습니다. 이러한 기술을 개발한 사람들에게 이해와 감사의 마음을 전해봅시다.

이를 염두에 두고, 우리의 관심을 보안 오케스트레이션의 세계에서 뜨거운 반향을 일으키는 새로운 도구인 Podman으로 돌려봅시다. 이 도구는 특히 안전한 오케스트레이션 분야에서 Docker와 10년간 사랑받아온 기존 강자에 도전하고 있습니다 💪. 이 흥미진진한 발전에 대해 더 깊이 파헤치기 위해 기대해 주세요!

# 🚀 컨테이너 이해: 왜 필요한가부터 어떻게 하는가까지

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

컨테이너는 코드, 런타임, 라이브러리 및 시스템 설정을 포함한 응용 프로그램 실행에 필요한 모든 것이 포함된 독립 실행 가능한 패키지입니다.

이는 응용 프로그램이 어디에서 시작하더라도 동일하게 실행된다는 것을 의미합니다. 즉, 당신의 랩탑, 클라우드 서버 또는 동료의 컴퓨터 등 어디에서든 실행할 수 있습니다. 이 일관성은 '내 컴퓨터에서는 작동하는데'라는 오랜 문제를 해결합니다.

컨테이너 오케스트레이션의 핵심은 컨테이너 실행 환경이며, 컨테이너 생성, 관리 및 실행에 도움을 줍니다.

- 컨테이너가 시작되면 실행 환경은 저장소에서 지정된 컨테이너 이미지를 요청합니다. 이 이미지는 응용 프로그램 및 종속성에 대한 청사진 역할을 합니다.
- 실행 환경은 Linux 네임스페이스를 사용하여 안전하고 분리된 환경을 제공하여 시스템 리소스(CPU, 메모리, 디스크, 네트워크 등)에 대한 독특한 이해를 제공합니다.
- Linux 커널의 컨트롤 그룹(cgroups)은 리소스 공정한 분배를 보장하며 어떤 컨테이너도 리소스를 독차지하거나 시스템 성능을 저하시키지 않습니다.
- 한 번에 한 컨테이너가 격리되면 실행 환경은 해당 환경 내에서 프로그램을 실행하여 호스트 시스템과 쉽게 통신합니다.
- 데몬 프로세스로 작동하는 컨테이너 실행 환경 도구는 리눅스 커널과 상호작용하여 컨테이너를 관리하며, 관리를 위해 루트 액세스가 필요합니다. 이 상호작용은 효율적인 컨테이너 관리에 중요합니다.

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

잘 알려진 컨테이너 런타임 중에는 Docker, k8s, nerdctl 등에서 사용되는 containerd와 cri-o가 있습니다.

# 🏆 도커의 지배 속에서 Podman의 부상

<img src="/assets/img/2024-05-23-DockervsPodmanANewErainSecureOrchestration_0.png" />

컨테이너 관리 세계에서 인기가 Podman으로 변화되고 있으며, 그 이유에는 몇 가지가 있습니다:

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

- Rootless Architecture: 도커와는 달리 루트 액세스가 있는 데몬 프로세스로 작동하는 Podman과 달리, Podman은 루트리스 접근 방식을 채택합니다. 이 기본적인 차이가 Podman의 인기 증진에 상당한 기여를 합니다.
- 보안 취약점: Docker의 루트 액세스는 파일을 읽고 프로그램을 설치하며 애플리케이션을 편집하는 등 컨테이너를 관리할 수 있게 합니다. 그러나 이는 시스템에 보안 취약점을 도입하여 헤커들로 하여금 유혹적인 대상이 되게 합니다.
- 해커들의 타깃: 해커가 데몬을 compromise하는 데 성공하면 민감한 데이터에 접근하거나 악성 코드를 실행하거나 컨테이너 구성을 변경하거나 시스템 전체를 다운시킬 가능성이 있습니다.
- SELinux로 보강된 보안: Docker와는 다르게 Podman은 각 컨테이너를 Security-Enhanced Linux (SELinux) 레이블과 함께 시작하여 보안을 강화합니다.
- 다른 도구에 의존: 루트리스 접근 방식으로 Podman은 컨테이너를 직접 관리하지 않습니다. 대신 이 아래서 설명하는 다른 도구들을 사용하여 컨테이너 관리를 수행합니다.
- Buildah: OCI (Open Container Initiative) 호환 컨테이너를 빌드하는 데 사용되는 오픈 소스 리눅스 기반 도구입니다. Buildah는 전체 컨테이너 런타임이나 데몬을 설치하지 않고도 컨테이너를 생성하고 관리할 수 있습니다.
- Skopeo: 컨테이너 이미지 및 이미지 레지스트리를 사용하여 다양한 작업을 수행하기 위한 명령줄 유틸리티입니다. 전체 이미지를 다운로드하지 않고 원격 레지스트리의 이미지를 검사할 수 있어 컨테이너 작업에 대한 가벼운 솔루션입니다.
- Systemd: Podman은 설정된 컨테이너 런타임을 호출하여 실행 중인 컨테이너를 생성합니다. 그렇지만 전용 데몬이 없는 Podman은 시스템 및 서비스 관리자인 systemd를 사용하여 업데이트를 수행하고 컨테이너를 백그라운드에서 유지합니다.

# 🔒 Podman의 보안에 대한 행동: Docker에 대한 안전한 대체품

<img src="/assets/img/2024-05-23-DockervsPodmanANewErainSecureOrchestration_1.png" />

- 주어진 시나리오에서 세 명의 리눅스 사용자인 Bob, Dawg, BadBoy가 생성되었습니다. Bob과 Dawg는 Podman을 사용하여 컨테이너를 생성하며, 이러한 컨테이너들은 각 사용자 네임스페이스 내의 리소스에만 액세스할 수 있습니다. 이러한 설정은 각 컨테이너의 액세스를 해당하는 네임스페이스로 제한하여 보안을 강화합니다.
- BadBoy는 Docker를 사용하며 루트 액세스를 가지고 있어 호스트 시스템의 모든 리소스에 대한 가시성을 허용합니다. 네임스페이스 밖에 있는 리소스까지도 볼 수 있어 시스템에 잠재적인 공격 가능성을 노출시킵니다. 이에 반해 루트리스 아키텍처인 Podman은 사용자 개별 네임스페이스에만 액세스 권한을 제한하여 보안을 강화합니다.

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

# Podman 설정

Podman을 설정하는 실용적인 예제에 대해 알아보겠습니다. macOS에서 설정을 진행할 것이지만 필요에 따라 다른 환경에서 설정하는 방법에 대한 해당 문서를 참조할 수 있습니다.

- Podman 설치: Homebrew를 이용하여 Podman을 설치하려면 brew install podman을 실행하세요.
- Podman Machine 초기화: Podman 머신을 초기화하려면 podman machine init을 사용하세요.
- Podman-Compose 설치: Docker Compose를 Podman으로 실행하는 스크립트인 Podman-Compose를 설치하려면 brew install podman-compose을 사용하세요.
- Podman-Desktop 설치: Podman에 대한 Docker Desktop과 유사한 경험을 제공하는 Podman-Desktop을 설치하려면 brew install podman-desktop을 사용하세요.
- Podman 세부 정보 확인: 마지막으로, podman info를 사용하여 Podman의 설치 및 구성 세부사항을 확인할 수 있습니다. 아래는 중요한 몇 가지 필드가 강조된 예시입니다.

```js
host:
  arch: amd64
  buildahVersion: 1.32.0
  cgroupControllers:
  - cpu
  - io
  - memory
  - pids
  cgroupManager: systemd
  cgroupVersion: v2
  ociRuntime:
    name: crun
    package: crun-1.12-1.fc39.x86_64
    path: /usr/bin/crun
    version: |-
      crun version 1.12
      commit: ce429cb2e277d001c2179df1ac66a470f00802ae
      rundir: /run/user/501/crun
      spec: 1.0.0
      +SYSTEMD +SELINUX +APPARMOR +CAP +SECCOMP +EBPF +CRIU +LIBKRUN +WASM:wasmedge +YAJL
  os: linux
  security:
    apparmorEnabled: false
    capabilities: CAP_CHOWN,CAP_DAC_OVERRIDE,CAP_FOWNER,CAP_FSETID,CAP_KILL,CAP_NET_BIND_SERVICE,CAP_SETFCAP,CAP_SETGID,CAP_SETPCAP,CAP_SETUID,CAP_SYS_CHROOT
    rootless: true
    seccompEnabled: true
    seccompProfilePath: /usr/share/containers/seccomp.json
    selinuxEnabled: true
  serviceIsRemote: true
registries:
  search:
  - docker.io
version:
  APIVersion: 4.7.2
  Built: 1698762721
  BuiltTime: Tue Oct 31 20:02:01 2023
  GitCommit: ""
  GoVersion: go1.21.1
  Os: linux
  OsArch: linux/amd64
  Version: 4.7.2
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

- Podman의 세부 정보를 확인하고 이미지를 검사하며 실행 중인 컨테이너를 관리하는 데 Podman CLI도 사용할 수 있습니다.

![이미지](/assets/img/2024-05-23-DockervsPodmanANewErainSecureOrchestration_2.png)

- 아래 제공된 Docker Compose 파일을 사용하여 컨테이너를 시작하려면 다음 명령을 실행하세요: podman compose up -d

```yaml
services:
  postgres:
    image: postgres
    restart: always
    environment:
      POSTGRES_DB: podman-psql
      POSTGRES_USER: podman-psql-user
      POSTGRES_PASSWORD: podman-pass
    ports:
      - "5432:5432"

  redis:
    image: "redis:6.0.14"
    restart: always
    command: redis-server
    ports:
      - "6379:6379"
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

- 다음은 Podman 데스크톱 내에서 실행 중인 컨테이너와 이미지를 검사할 수 있습니다.

![image1](/assets/img/2024-05-23-DockervsPodmanANewErainSecureOrchestration_3.png)

![image2](/assets/img/2024-05-23-DockervsPodmanANewErainSecureOrchestration_4.png)

# 마지막으로

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

디지턈 시대에는 보안이 매우 중요합니다. 침입이 발생하면 심각한 결과를 가져올 수 있습니다. Docker와 Podman은 각각 강점과 약점을 가지고 있습니다. Podman은 안전한 오케스트레이션의 기초를 바탕으로 만들어졌지만 Docker와 같은 기능(예: Docker Swarm)이 부족할 수 있습니다. 반면 Docker는 사용 편의성을 강조하지만 보안 측면에서는 미흡하다고 여겨집니다.

이 토론이 유익했고 안전한 오케스트레이션에 대한 이해력을 높일 수 있었기를 바랍니다. 이 정보가 유용했다면 더 많은 글을 읽고 싶다면 저를 팔로우해주세요. 즐거운 학습되세요! 🚀

# 참고 자료

- Podman이란? (redhat.com)
- Podman 설치 | Podman
- Docker를 대체할 도구 및 그 이유 | mkdev의 프로그래밍 글
- Alfresco와 함께 Podman 사용하기 — Alfresco Hub
