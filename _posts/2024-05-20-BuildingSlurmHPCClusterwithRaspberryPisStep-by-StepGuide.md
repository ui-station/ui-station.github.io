---
title: "Raspberry Pi로 Slurm HPC 클러스터 구축하기 단계별 안내"
description: ""
coverImage: "/assets/img/2024-05-20-BuildingSlurmHPCClusterwithRaspberryPisStep-by-StepGuide_0.png"
date: 2024-05-20 19:50
ogImage:
  url: /assets/img/2024-05-20-BuildingSlurmHPCClusterwithRaspberryPisStep-by-StepGuide_0.png
tag: Tech
originalTitle: "Building Slurm HPC Cluster with Raspberry Pi’s: Step-by-Step Guide"
link: "https://medium.com/@hghcomphys/building-slurm-hpc-cluster-with-raspberry-pis-step-by-step-guide-ae84a58692d5"
---

이 게시물에서는 Raspberry Pi를 사용하여 Slurm 하이퍼파워 컴퓨팅(HPC) 클러스터를 구축한 시도를 공유하려고 합니다. 전에 이 클러스터를 시작했을 때는 GPU 컴퓨팅도 지원하는 더 큰 HPC 클러스터를 만들기 위한 시범용으로 사용했습니다. HPC 설정의 다양한 구성 요소를 실제로 직접 다루어 보며 모두가 어떻게 맞물려 있는지 파악했습니다. SLURM 구성 요소를 설정하는 것이 실제로 그 중요한 부분 중 하나였고, 약간의 검토 후에 나만의 HPC 클러스터를 성공적으로 구축했습니다. 이 기계를 설정하는 것이 꽤 간단하기 때문에, 다양한 소프트웨어 패키지 및 라이브러리를 빨리 시험해보거나 최적의 클러스터 하드웨어를 조정하는 데 효과적이었습니다.

여기서의 목표는 여러 컴퓨팅 노드를 처리할 수 있는 HPC 클러스터를 구축하는 것입니다. 이러한 시스템을 처음부터 만드는 것은 어려운 시도입니다. 또한 Slurm 클러스터를 구성하는 데 필요한 모든 단계를 다루는 포괄적인 자습서를 찾는 것은 쉽지 않을 수 있습니다, 적어도 제 경험상으로는요. 그런 관계로 여러분이 HPC 클러스터를 구축하는 여정에서 이 단계별 가이드가 유용하게 활용되기를 바랍니다.

![Building Slurm HPC Cluster with Raspberry Pis](/assets/img/2024-05-20-BuildingSlurmHPCClusterwithRaspberryPisStep-by-StepGuide_0.png)

## 목차

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

이 게시물은 HPC에 대한 간단한 소개로 시작되어 주로 사용되는 자원 관리자 및 작업 일정 관리자인 Slurm의 중요성에 대해서 소개합니다. 그 후에 클러스터 토폴로지를 설명하며 필수 전제 조건, 하드웨어 사양 및 운영 체제 설정에 대해 논의할 것입니다. 그 후에는 스토리지 노드 설정을 안내하고, 소스에서 Slurm을 빌드하고, 설치하고, 마스터 노드에서 구성하는 방법과 추가 계산 노드를 추가하는 것에 관해 설명하겠습니다. 마지막으로 구성된 Slurm 클러스터의 상태를 보여주고 작업 제출이 어떻게 처리되는지 몇 가지 예제를 제시할 것입니다.

## 기능

결과적으로 자신만의 HPC 클러스터를 구축할 수 있게 될 것입니다.

- Slurm 워크로드 관리자
- 중앙 집중식 네트워크 저장소

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

이제까지 너무 길어지지 않도록 최소한의 기능에 집중했습니다. 목표는 먼저 핵심 기능을 갖춘 HPC 클러스터를 설정하되, 나중에 개선할 수 있는 유연성을 유지하는 것입니다.

# HPC 클러스터란?

HPC 클러스터는 다양한 분야에서 높은 속도로 대량의 데이터를 처리해 계산 문제를 해결하기 위해 설계된 상호 연결된 컴퓨터 네트워크입니다. 이러한 클러스터는 다수의 컴퓨팅 노드로 구성되어 있으며, 각 노드에는 프로세서, 메모리 및 종종 GPU와 같은 전문 가속기가 장착되어 있어 연구원과 과학자들이 계산적으로 요구되는 작업 및 시뮬레이션에 대응할 수 있습니다. Slurm은 리소스 관리를 위한 간단한 Linux 유틸리티를 의미하며, 오픈 소스 HPC 작업 스케줄러 및 리소스 관리자입니다. 이는 컴퓨팅 자원을 효율적으로 할당하고 작업 스케줄링을 관리하며 HPC 클러스터에서 병렬 계산을 조정하는 데 중요한 역할을 합니다. 쿠버네티스 클러스터에 대안으로, Slurm은 과학 연구, 시뮬레이션 및 데이터 분석 작업에서 일반적으로 발견되는 일괄 컴퓨팅 워크로드의 관리에 특화되어 있습니다. 반면 쿠버네티스는 컨테이너화된 애플리케이션 및 마이크로서비스에 더 중점을 둡니다.

## 클러스터 토폴로지

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

HPC 클러스터를 구성하는 다양한 토폴로지가 있습니다. 각각은 특정 성능 기대치와 애플리케이션 요구 사항에 맞게 설계되어 있어요. 우리의 시나리오에서는 서브넷(10.0.0.x) 안에서 라우터와 스위치를 통해 세 대의 라즈베리 파이를 연결하는 데 초점을 맞춥니다. 라우터에 DHCP 서버를 설정하여 각 라즈베리 파이에 고유한 MAC 주소를 기반으로 고정 IP 주소를 할당했어요. 하지만 라즈베리 파이 자체에 정적 IP(192.168.0.x)를 직접 구성하여 간소화할 수도 있어요. 또한, 가정의 ISP 라우터를 통해 라즈베리 파이를 상호 연결하는 대신 Wi-Fi 액세스 포인트를 사용할 경우, 라우터와 이더넷 스위치가 필요 없어요. 빠른이나 안정적인 연결이 필요 없는 테스트 클러스터를 위한 대안으로 유용해요. 더불어 인터넷 연결은 라우터를 가정의 ISP Wi-Fi 액세스 포인트에 연결하고 장치들에게 공유함으로써 제공됩니다. 아래 다이어그램은 클러스터 네트워크의 시각적 표현을 제공해요:

![cluster_network](/assets/img/2024-05-20-BuildingSlurmHPCClusterwithRaspberryPisStep-by-StepGuide_1.png)

이 토폴로지는 휴대성의 장점을 제공하여 클러스터를 쉽게 다른 액세스 포인트에 연결할 수 있어요. 또한, 데이터 관리용으로 한 대의 노드를 사용하여 전용 네트워크 저장 서버로 활용했습니다. 나머지 두 대의 라즈베리 파이는 하나는 마스터 노드 및 계산 노드로 사용되고, 다른 하나는 추가 계산 노드로 사용됩니다.

# 준비사항

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

## 하드웨어 구성 요소

이번 프로젝트에서 다음과 같은 장비들을 사용했어요.

- Raspberry Pi 4 Model B 2GB 보드 (호스트명 rpnode01)
  이 장치는 마스터 노드 및 컴퓨팅 노드로 사용됩니다.
- Raspberry Pi 4 Model B 2GB 보드 (호스트명 rpnode02)
  이 장치는 2번째 컴퓨팅 노드로 작동합니다.
- Raspberry Pi 3+ 1GB 보드 (호스트명 filenode01)
  네트워크 저장 서버로 설정되어 있습니다.
- USB 파워 허브
  여러 Raspberry Pi의 전원 요구 사항을 동시에 지원할 수 있는 다중 포트 USB 충전기가 필요합니다 (각 Raspberry Pi 당 적어도 2A).
- 라우터 및 이더넷 스위치 (선택 사양)
  라우터는 외부 연결을 관리하고 스위치는 내부 장치 통신을 처리합니다.

![이미지](/assets/img/2024-05-20-BuildingSlurmHPCClusterwithRaspberryPisStep-by-StepGuide_2.png)

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

## 운영 체제

저는 리소스가 제한된 시스템에 적합한 경량 운영 체제인 Debian bookworm (버전 12) OS Lite 64-bit를 사용했습니다. 이 OS는 라즈베리 파이와 같은 시스템에서 유용하게 사용할 수 있습니다. 다음 조정 사항은 모든 장치에 적용되도록 예상됩니다.

- 사용자 이름이 "pi"이고 패스워드는 "testpass"인 더미 패스워드를 사용했습니다. 그러나 LDAP나 기타 인증 메커니즘을 활용하여 사용자 및 그룹 ID를 클러스터 전체에서 동기화하는 것이 가능합니다.
- 노드에서 SSH 액세스를 활성화합니다. 노드에 액세스하기 위해 SSH 키 공유를 사용하는 것이 편리합니다.
- CPU 및 메모리를 위한 제어 그룹을 활성화합니다. cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory swapaccount=1를 입력하여 /boot/firmware/cmdline.txt를 수정합니다. 이 변경 사항을 적용한 후 시스템을 다시 부팅합니다.
- /etc/hosts에 다음 호스트 이름을 추가합니다.
  10.0.0.1 rpnode01 rpnode01.home.local
  10.0.0.2 rpnode02 rpnode02.home.local
  10.0.0.3 filenode01 filenode01.home.local
- (필요한 경우) 언어 및 지역 설정을 구성합니다.

  $ sudo raspi-config

- 마지막으로 시스템 패키지를 업데이트하고 업그레이드합니다.

  $ sudo apt update && sudo apt upgrade

![Raspberry Pi 클러스터 구축 스텝바이스텝 가이드](/assets/img/2024-05-20-BuildingSlurmHPCClusterwithRaspberryPisStep-by-StepGuide_3.png)

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

# 저장 노드

HPC 클러스터에서 연산 노드는 상태를 유지하지 않는(stateless) 설계로 되어 있습니다. 이는 연산 노드가 지속적인 데이터나 상태를 저장하지 않음을 의미합니다. 대신, 모든 응용 프로그램 소프트웨어와 사용자 데이터는 중앙 공유 저장소에 저장됩니다. 이 아키텍처는 여러 이점을 제공합니다. 먼저, 새로운 연산 노드를 간편하게 추가할 수 있어 확장성을 향상시킵니다. 데이터를 여러 기계에 복제할 필요 없이 간단히 새로운 연산 노드를 추가할 수 있습니다. 둘째, 이는 사용자들이 클러스터 내의 모든 연산 노드에서 응용프로그램과 데이터에 액세스할 수 있도록 유연성을 제공합니다. 셋째, 중앙 저장 노드에 모든 데이터를 저장하는 것은 데이터 무결성과 일관성을 보장하여 개별 연산 노드에 로컬로 데이터를 저장할 때 발생할 수 있는 불일치에 대한 우려를 제거합니다. 마지막으로, 상태를 유지하지 않는 연산 노드 아키텍처는 소프트웨어 업데이트, 하드웨어 교체, 문제 해결과 같은 유지 보수 작업을 간단하게 만들어줍니다. 개별 연산 노드에 로컬로 저장된 데이터를 전송하거나 백업할 필요가 없기 때문입니다.

## NFS 서버

전용 노드 filenode01에 NFS(Network File System) 서버를 설정했습니다. 이곳에서 네트워크 저장소를 사용할 수 있습니다. HPC 클러스터의 맥락에서 연산 노드를 상태를 유지하지 않는 것으로 언급할 때, 해당 연산 노드 자체가 지속적인 데이터를 저장하지 않음을 의미합니다. 사용자 홈 디렉토리 데이터와 응용프로그램 소프트웨어는 중앙 저장 노드에 저장됩니다.

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

해당 작업을 위해 NFS 서버를 apt 패키지 관리자에 설치했습니다. 이 작업은 터미널에서 다음 명령을 실행하여 수행할 수 있습니다:

```js
$ sudo apt install nfs-kernel-server
```

그런 다음 /etc/exports 파일을 구성하여 NFS를 통해 공유하려는 디렉터리를 정의했습니다. 이 파일은 유닉스류 운영 체제에서 NFS 서버에 의해 사용되는 구성 파일입니다. 이 파일은 서버에서 NFS 클라이언트와 공유되는 디렉터리 및 해당 디렉터리에 대한 액세스 권한을 정의합니다. 이 디렉터리가 존재하는지 확인하고 다음 명령을 사용하여 항목을 추가했습니다:

```js
$ sudo mkdir -p /home /nfs
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
$ sudo bash -c "cat >> /etc/exports << EOF
/home  *(rw,sync,no_root_squash,no_subtree_check)
/nfs  *(rw,sync,no_root_squash,no_subtree_check)
EOF"
```

여기서 \*을 사용하여 모든 노드에서 액세스를 허용하고 필요한 옵션을 지정했습니다. 예를 들어, rw는 읽기-쓰기 액세스를 나타냅니다. exports 파일을 편집한 후에는 변경 사항을 적용하려면 다음을 실행하세요:

```js
$ sudo exportfs -ra
```

노드에 방화벽이 활성화되어 있는 경우 NFS 포트를 열어야 할 수 있습니다. NFSv4는 TCP 및 UDP 포트 2049를 사용하며, NFSv3는 추가 포트를 사용합니다. 방화벽 구성에 따라 ufw 또는 iptables을 사용하여 이러한 포트를 열 수 있습니다.

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

NFS 공유가 제대로 설정되었는지 확인할 수 있습니다. showmount 명령어를 사용하면 해당 노드에서 내보낸 디렉토리 목록을 표시할 수 있습니다.

```js
$ sudo showmount -e
Export list for filenode01:
/home *
/nfs *
```

## NFS 클라이언트

NFS 클라이언트 노드인 rpnode01 및 rpnode02에 네트워크 저장소 액세스를 활성화하려면 /etc/fstab 파일을 수정하여 NFS 마운트 지점을 통합할 수 있습니다. 이 파일은 시스템 부팅 중 파일 시스템을 자동으로 마운트할 수 있게 하는 시스템 파일입니다.

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

클라이언트 측에 /home 및 /nfs 디렉토리가 존재하는지 확인한 후에 이 파일을 수정하기 전에 아래 명령어를 실행해 주세요:

```js
$ sudo mkdir -p /home /nfs
```

```js
$ sudo bash -c "cat >> /etc/fstab << EOF
filenode01:/home /home nfs defaults 0 0
filenode01:/nfs /nfs nfs defaults 0 0
EOF"
```

/etc/fstab에 추가된 각 행은 고유한 NFS 마운트 지점을 정의합니다. 이는 시스템에게 NFS 서버의 호스트 이름이 filenode01인 /home (/nfs) 디렉토리를 로컬 /home (/nfs) 디렉토리에 마운트하도록 지시합니다. 이전에 언급했듯이, /home은 사용자 데이터로 지정되고, /nfs 디렉토리는 공유 소프트웨어 스택을 위해 의도되었습니다. 노드를 다시 부팅하세요.

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

업데이트된 /etc/fstab 파일의 정확성을 확인하기 위해 아래 명령인 mount -a를 사용합니다.

```bash
$ sudo mount -av
/nfs    : 성공적으로 마운트됨
/home    : 성공적으로 마운트됨
```

시스템이 시작될 때 /etc/fstab을 읽어 아직 마운트되지 않은 파일 시스템을 마운트합니다.

# 마스터 노드

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

## Slurm 빌드 및 설치

저는 항상 Slurm을 빠돌린 패키지 대신 소스 코드에서 컴파일하는 것을 선호합니다. 이렇게 하면 사용자 정의가 가능해지며, 최신 기능 및 수정 사항에 액세스할 수 있으며 교육적 가치를 제공합니다. 이곳에서 설명된 대로 Slurm 패키지를 소스 코드에서 빌드하고 설치 가능하도록 만드는 지침은 거의 동일하지만 약간의 변경을 적용했는데, 이어지는 내용에서 설명하겠습니다.

- 필수 라이브러리

Slurm 구성을 진행하기 전에 다음 라이브러리 또는 헤더 파일이 설치되어 있는지 확인하십시오. 이를 apt 패키지 관리자를 통해 쉽게 설치할 수 있습니다.

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
$ sudo apt install libpmix-dev libpam-dev libmariadb-dev \
  libmunge-dev libdbus-1-dev munge
```

2. Making from source

이제 소스 코드로부터 빌드해 보겠습니다.

SchedMD Github 저장소에서 이 게시물 작성 시점인 가장 최근 버전인 Slurm 버전 23.11을 다운로드하겠습니다. 우리는 x86_64 대신 aarch64 아키텍처용으로 빌드할 것입니다.

```js
$ sudo mkdir /opt/slurm && cd /opt/slurm
$ sudo wget https://github.com/SchedMD/slurm/archive/refs/tags/slurm-23-11-6-1.tar.gz
$ sudo tar -xf slurm-23-11-6-1.tar.gz
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

Slurm을 컴파일하는 데는 모든 것을 처음부터 만들기 때문에 몇 분 정도의 시간이 필요합니다.

```js
$ cd slurm-slurm-23-11-6-1
$ sudo ./configure \
  --prefix=/opt/slurm/build \
  --sysconfdir=/etc/slurm \
  --enable-pam \
  --with-pam_dir=/lib/aarch64-linux-gnu/security/ \
  --without-shared-libslurm \
  --with-pmix
$ sudo make
$ sudo make contrib
$ sudo make install
```

--prefix 옵션은 컴파일된 코드를 설치할 베이스 디렉토리를 지정합니다. /usr에 직접 설치하는 대신에 /opt/slurm/build로 설정했습니다. 이유는 Slurm을 추가 컴퓨팅 노드에 설치할 때 설치 가능한 패키지를 생성하는 데 활용하려는 것입니다.

3. Debian 패키지 빌드하기.

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

fpmtool을 사용하여 컴파일된 코드의 Debian 패키지를 만들었습니다. 이 작업을 위해서 추가 패키지를 설치해야 합니다.

```js
$ sudo apt ruby-dev
$ sudo gem install fpm
```

이 도구는 slurm-23.11_1.0_arm64.deb 라는 패키지 파일을 생성할 것입니다. Slurm 23.11.0부터는 Debian 패키지를 빌드하는 데 필요한 파일이 Slurm에 포함되어 있음을 언급해 두겠습니다.

```js
$ sudo fpm -s dir -t deb -v 1.0 -n slurm-23.11 --prefix=/usr -C /opt/slurm/build .
Created package {:path=>"slurm-23.11_1.0_arm64.deb"}
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

4. Debian 패키지 설치

다음으로, dpkg 명령어를 사용하여 이 패키지를 설치합니다:

```js
$ sudo dpkg -i slurm-23.11_1.0_arm64.deb
slurm-23.11_1.0_arm64.deb를 풀기 위한 준비를 합니다...
slurm-23.11(1.0)을(를) (1.0) 위에 덮어씁니다...
slurm-23.11(1.0)을(를) 설정합니다...
man-db(2.11.2-2)에 대한 트리거를 처리 중...
```

또한, slurm 시스템 사용자를 생성하고 필요한 디렉토리를 올바른 액세스 권한으로 초기화해야 합니다. userslurm 사용자가 존재하고 클러스터 전체에서 사용자 ID가 동기화되어 있는지 확인해주세요. Slurm 컨트롤러가 사용하는 파일 및 디렉토리는 userslurm에 의해 읽거나 쓸 수 있어야 합니다. 또한, 로그 파일 디렉토리 /var/log/slurm과 상태 저장 디렉토리 /var/spool/slurm은 쓰기 권한이 있어야 합니다.

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

여기요, 사용자 slurm과 해당 그룹 ID를 151로 수정했습니다.

```js
$ sudo adduser --system --group --uid 151 slurm
```

또한 아래 명령어를 실행하여 예상 권한으로 필요한 디렉토리를 생성했습니다:

```js
$ sudo mkdir -p /etc/slurm /var/spool/slurm/ctld /var/spool/slurm/d /var/log/slurm
$ sudo chown slurm: /var/spool/slurm/ctld /var/spool/slurm/d /var/log/slurm
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

## Slurm 구성

지금까지 모든 것이 순조롭게 진행되고 있어요. 지금까지 Slurm 패키지를 빌드하고 설치했습니다. 다음 단계는 각 요소를 구성하고 서비스로 실행하는 것입니다.

- Slurm 데이터베이스 데몬

우리는 Slurm 데이터베이스 데몬 (slurmdbd)을 설정하여 각 작업에 대한 상세한 회계 정보를 수집할 것입니다. 모든 회계 데이터는 데이터베이스에 저장됩니다. 먼저 마스터 노드에 데이터베이스 서버를 만들어야하는데, 이상적으로는 별도의 노드에 있어야 합니다. 저는 MySQL과 호환되는 오픈 소스 데이터베이스인 MariaDB를 선택했습니다. 다음 지침에 따라 데이터베이스 서버를 배포할 수 있어요.

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

```bash
$ sudo apt install mariadb-server
$ sudo mysql -u root
create database slurm_acct_db;
create user 'slurm'@'localhost';
set password for 'slurm'@'localhost' = password('slurmdbpass');
grant usage on *.* to 'slurm'@'localhost';
grant all privileges on slurm_acct_db.* to 'slurm'@'localhost';
flush privileges;
exit
```

그 후에는 /etc/slurm/slurmdbd.conf를 만들고 인증, 데이터베이스 서버 호스트 이름, 로깅 등을 지정하는 등 필요한 구성을 추가해야 합니다. 아래 명령을 실행하여 구성 파일을 추가하세요.

```bash
$ sudo bash -c "cat > /etc/slurm/slurmdbd.conf << EOF
# 인증 정보
AuthType=auth/munge

# slurmDBD 정보
DbdAddr=localhost
DbdHost=localhost
SlurmUser=slurm
DebugLevel=3
LogFile=/var/log/slurm/slurmdbd.log
PidFile=/run/slurmdbd.pid
PluginDir=/usr/lib/slurm

# 데이터베이스 정보
StorageType=accounting_storage/mysql
StorageUser=slurm
StoragePass=slurmdbpass
StorageLoc=slurm_acct_db
EOF"
```

이 파일은 Slurm 데이터베이스 데몬 구성 정보를 설명합니다. slurmdbd가 실행되는 컴퓨터에만 존재해야 하며, slurm 사용자만 읽을 수 있어야 합니다.

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

다음으로, slurmdbd를 systemd 서비스로 설정해야 합니다. 이를 위해서는 /etc/systemd/system/slurmdbd.service 파일을 생성해야 합니다.

```bash
$ sudo bash -c "cat > /etc/systemd/system/slurmdbd.service << EOF
[Unit]
Description=Slurm DBD accounting daemon
After=network.target munge.service
ConditionPathExists=/etc/slurm/slurmdbd.conf

[Service]
Type=forking
EnvironmentFile=-/etc/sysconfig/slurmdbd
ExecStart=/usr/sbin/slurmdbd $SLURMDBD_OPTIONS
ExecReload=/bin/kill -HUP $MAINPID
PIDFile=/run/slurmdbd.pid

[Install]
WantedBy=multi-user.target
EOF"
```

이제 slurmdbd.service를 활성화하고 시작할 수 있습니다.

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
$ sudo systemctl enable slurmdbd.service
$ sudo systemctl start slurmdbd.service
```

```js
$ sudo systemctl | grep slurmdbd
  slurmdbd.service      loaded active running   Slurm DBD accounting daemon
```

만약 모든 것이 잘 진행된다면, slurmdbd 서비스가 실행 중인 것을 확인할 수 있어요. 그렇지 않다면, /var/log/slurm/slurmdbd.log 파일이나 systemd 상태를 확인해주세요.

2. Slurm 컨트롤러 데몬

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

슬러름 컨트롤러 데몬 (slurmctl)은 슬러름 활동을 조정하며 슬러름의 중앙 관리 데몬입니다. 다른 모든 슬러름 데몬 및 리소스를 모니터링하고 작업을 수락하며 해당 작업에 리소스를 할당합니다. 우리는 /etc/slurm/slurmd.conf 파일을 생성해야 합니다. 이 구성 파일은 슬러름이 리소스와 상호 작용하고 작업을 관리하며 다른 구성 요소와 통신하는 방식을 정의합니다. 이 파일에는 다양한 매개변수가 포함되어 있으며 클러스터의 각 노드에서 일관되게 사용 가능해야 합니다. 필요한 구성을 만들고 추가하려면 터미널에서 다음 명령어를 사용하세요.

```js
$ sudo bash -c "cat > /etc/slurm/slurm.conf << EOF
ClusterName=raspi-hpc-cluster
ControlMachine=rpnode01
SlurmUser=slurm
AuthType=auth/munge
StateSaveLocation=/var/spool/slurm/ctld
SlurmdSpoolDir=/var/spool/slurm/d
SwitchType=switch/none
MpiDefault=pmi2
SlurmctldPidFile=/run/slurmctld.pid
SlurmdPidFile=/run/slurmd.pid
ProctrackType=proctrack/cgroup
PluginDir=/usr/lib/slurm
ReturnToService=1
TaskPlugin=task/cgroup

# 스케줄링
SchedulerType=sched/backfill
SelectTypeParameters=CR_Core_Memory,CR_CORE_DEFAULT_DIST_BLOCK,CR_ONE_TASK_PER_CORE

# 로깅
SlurmctldDebug=3
SlurmctldLogFile=/var/log/slurm/slurmctld.log
SlurmdDebug=3
SlurmdLogFile=/var/log/slurm/slurmd.log
JobCompType=jobcomp/none

# 회계
JobAcctGatherType=jobacct_gather/cgroup
AccountingStorageTRES=gres/gpu
DebugFlags=CPU_Bind,gres
AccountingStorageType=accounting_storage/slurmdbd
AccountingStorageHost=localhost
AccountingStoragePass=/run/munge/munge.socket.2
AccountingStorageUser=slurm
AccountingStorageEnforce=limits

# 컴퓨팅 노드
NodeName=rpnode01 CPUs=4 Sockets=1 CoresPerSocket=4 ThreadsPerCore=1 RealMemory=1800 State=idle
NodeName=rpnode02 CPUs=4 Sockets=1 CoresPerSocket=4 ThreadsPerCore=1 RealMemory=1800 State=idle

# 파티션
PartitionName=batch Nodes=rpnode[01-02] Default=YES State=UP DefaultTime=1-00:00:00 DefMemPerCPU=200 MaxTime=30-00:00:00 DefCpuPerGPU=1
EOF"
```

그리고 /etc/system/systemd/slurmctld.service를 시스템 데몬으로 실행하도록 만들어야 합니다. 다음 명령어를 실행하여 다음 내용을 추가할 수 있습니다.

```js
$ sudo bash -c "cat > /etc/systemd/system/slurmctld.service << EOF
[Unit]
Description=Slurm controller daemon
After=network.target munge.service
ConditionPathExists=/etc/slurm/slurm.conf

[Service]
Type=forking
EnvironmentFile=-/etc/sysconfig/slurmctld
ExecStart=/usr/sbin/slurmctld $SLURMCTLD_OPTIONS
ExecReload=/bin/kill -HUP $MAINPID
PIDFile=/run/slurmctld.pid

[Install]
WantedBy=multi-user.target
EOF"
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

이제 slurmctld.service를 활성화하고 시작할 준비가 되었습니다:

```bash
$ sudo systemctl enable slurmctld.service
$ sudo systemctl start slurmctld.service
```

```bash
$ sudo systemctl | grep slurmctld
slurmctld.service       loaded active running   Slurm controller daemon
```

3. Slurm 노드 데몬

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

마스터 노드를 컴퓨트 노드로도 사용하려면 Slurm의 컴퓨트 노드 데몬인 slurmd를 설정해야 합니다. slurmd 데몬은 모든 컴퓨트 노드에서 실행되어야 합니다. 이는 컴퓨트 노드에서 실행 중인 모든 작업을 모니터링하고 작업을 수락하며 실행 및 중지 요청 시 실행 중인 작업을 중지합니다. 이 데몬은 slurmd.conf와 함께 cgroup.conf 및 cgroup_allowd_devices_file.conf 두 가지 추가 파일을 함께 읽습니다. 다음 명령어를 사용하여 두 개의 필요한 제어 그룹 (cgroup) 파일을 만드세요:

```js
$ sudo bash -c "cat > /etc/slurm/cgroup.conf << EOF
ConstrainCores=yes
ConstrainDevices=yes
ConstrainRAMSpace=yes
EOF"
```

```js
$ sudo bash -c "cat > /etc/slurm/cgroup_allowed_devices_file.conf << EOF
/dev/null
/dev/urandom
/dev/zero
/dev/sda*
/dev/cpu/*/*
/dev/pts/*
/dev/nvidia*
EOF"
```

그런 다음, systemd 서비스로서 slurmd를 실행하기 위해 systemd.service 파일을 다시 만들어야 합니다.

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

```bash
$ sudo bash -c "cat > /etc/systemd/system/slurmd.service << EOF
[Unit]
Description=Slurm 노드 데몬
After=network.target munge.service
ConditionPathExists=/etc/slurm/slurm.conf

[Service]
Type=forking
EnvironmentFile=-/etc/sysconfig/slurmd
ExecStart=/usr/sbin/slurmd -d /usr/sbin/slurmstepd $SLURMD_OPTIONS
ExecReload=/bin/kill -HUP $MAINPID
PIDFile=/run/slurmd.pid
KillMode=process
LimitNOFILE=51200
LimitMEMLOCK=infinity
LimitSTACK=infinity
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target
EOF"
```

마지막으로 아래 명령어를 사용하여 slurmd 서비스를 활성화하고 시작합니다:

```bash
$ sudo systemctl enable slurmd.service
$ sudo systemctl start slurmd.service
```

```bash
$ sudo systemctl | grep slurmd
  slurmd.service       loaded active running   Slurm 노드 데몬
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

이제 모든 설정이 완료되었으므로 Slurmsinfo 명령어를 사용하여 Slurm 노드 및 파티션에 대한 정보를 확인할 수 있습니다:

```js
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
batch*       up 30-00:00:0      1   idle rpnode01
```

이 출력을 보면 Slurm이 성공적으로 설치되고 구성된 것입니다. 수고하셨습니다!

# Slurm 회계

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

Slurm은 실행된 모든 작업과 작업 단계에 대한 회계 정보를 수집합니다. 또한 데이터베이스로 직접 회계 기록을 지원합니다. 테스트 목적으로, Slurm 데이터베이스에서 클러스터 "raspi-hpc-cluster"와 계정 "compute"를 다음과 같이 정의합니다.

```js
$ sudo sacctmgr add cluster raspi-hpc-cluster
$ sudo sacctmgr add account compute description="Compute account" Organization=home
```

```js
$ sudo sacctmgr show account
   Account                Descr                  Org
---------- -------------------- --------------------
   compute      Compute account                home
      root default root account                root
```

그리고 사용자 pi를 일반 Slurm 계정으로 연결합니다.

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

```bash
$ sudo sacctmgr add user pi account=compute
$ sudo sacctmgr modify user pi set GrpTRES=cpu=4,mem=1gb
$ sudo sacctmgr show user
      User   Def Acct     Admin
---------- ---------- ---------
        pi    compute      None
      root       root Administ+

```

모든 것이 순조롭게 진행되었다면, 단일 노드 Slurm 클러스터가 작업 제출을 위해 준비된 상태여야 합니다. 이제 사용 가능한 노드의 현재 상태에 대한 정보를 먼저 표시해보겠습니다.

```bash
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
batch*       up 30-00:00:0      1   idle rpnode[01]
```

이제 간단한 Slurm srun 명령어를 실행하고 출력을 확인해봅시다.

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

$ srun hostname
rpnode01

이것은 우리의 작업이 Slurm 작업으로 성공적으로 실행되고 컴퓨팅 노드의 호스트 이름인 rpnode01을 반환했음을 나타냅니다.

# 컴퓨팅 노드

추가 컴퓨팅 노드를 포함하는 Slurm 클러스터를 확장하는 것은 다음의 주요 단계를 포함합니다:

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

- 필요한 라이브러리와 헤더 파일 설치하기
- 마스터 노드에서 /etc/munge/munge.key를 컴퓨트 노드로 복사하고, 소유자를 munge 사용자로 변경한 후 munge.service를 다시 시작하기
- slurm-23.11_1.0_arm64.deb를 설치하기
- slurm 사용자 및 필요한 Slurm 디렉토리 만들기
- slurm.conf, cgroup.conf 및 cgroup_allowed_devices_file.conf 파일을 /etc/slurm/으로 복사하기
- slurmd.service를 활성화하고 시작하기

테스트

새로운 노드 newpnode02의 상태를 idle로 업데이트하려면 먼저 다음을 실행해보세요

```js
$ scontrol update nodename=rpnode02 state=idle
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
$ sinfo
| PARTITION | AVAIL | TIMELIMIT  | NODES | STATE | NODELIST |
|-----------|-------|------------|-------|-------|----------|
| batch*    | up    | 30-00:00:00| 2     | idle  | rpnode[01-02] |
```

And again run the hostname job on the new node using

```js
$ srun -w rpnode02 hostname
rpnode02
```

As you can see, this job was executed on the second compute node, so it returns the hostname rpnode02 this time.

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

# 예시

## 클러스터 정보

Slurm에서의 파티션은 클러스터를 논리적 노드 그룹으로 나누는 방법으로, 자원을 효율적으로 관리하고 할당할 수 있도록 도와줍니다. 여기서는 Slurm에서 구성된 배치 파티션에 대한 상세 정보를 표시합니다.

```js
$ scontrol show partition
PartitionName=batch
   AllowGroups=ALL AllowAccounts=ALL AllowQos=ALL
   AllocNodes=ALL Default=YES QoS=N/A
   DefaultTime=1-00:00:00 DisableRootJobs=NO ExclusiveUser=NO GraceTime=0 Hidden=NO
   MaxNodes=UNLIMITED MaxTime=30-00:00:00 MinNodes=0 LLN=NO MaxCPUsPerNode=UNLIMITED MaxCPUsPerSocket=UNLIMITED
   Nodes=rpnode[01-02]
   PriorityJobFactor=1 PriorityTier=1 RootOnly=NO ReqResv=NO OverSubscribe=NO
   OverTimeLimit=NONE PreemptMode=OFF
   State=UP TotalCPUs=8 TotalNodes=2 SelectTypeParameters=NONE
   JobDefaults=DefCpuPerGPU=1
   DefMemPerCPU=200 MaxMemPerNode=UNLIMITED
   TRES=cpu=8,mem=3600M,node=2,billing=8
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

그리고 rpndoe01 상태를 보여주는 중입니다

```js
$ scontrol show nodes
NodeName=rpnode01 Arch=aarch64 CoresPerSocket=4
   CPUAlloc=0 CPUEfctv=4 CPUTot=4 CPULoad=0.01
   AvailableFeatures=(null)
   ActiveFeatures=(null)
   Gres=(null)
   NodeAddr=rpnode01 NodeHostName=rpnode01 Version=23.11.6
   OS=Linux 6.6.28+rpt-rpi-v8 #1 SMP PREEMPT Debian 1:6.6.28-1+rpt1 (2024-04-22)
   RealMemory=1800 AllocMem=0 FreeMem=297 Sockets=1 Boards=1
   State=IDLE ThreadsPerCore=1 TmpDisk=0 Weight=1 Owner=N/A MCS_label=N/A
   Partitions=batch
   BootTime=2024-05-19T13:58:15 SlurmdStartTime=2024-05-19T14:20:03
   LastBusyTime=2024-05-19T14:26:11 ResumeAfterTime=None
   CfgTRES=cpu=4,mem=1800M,billing=4
   AllocTRES=
   CapWatts=n/a
   CurrentWatts=0 AveWatts=0
   ExtSensorsJoules=n/a ExtSensorsWatts=0 ExtSensorsTemp=n/a
```

## 작업 제출

홈 디렉토리에 간단한 Slurm 배치 파일을 만들어봅시다.

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
$ cat > ~/submit.sh << EOF
#!/usr/bin/sh

#SBATCH --job-name=testjob
#SBATCH --mem=10mb
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=2
#SBATCH --time=00:01:00

srun sleep 10
EOF
```

이제 우리는 이 작업을 제출할 수 있어요

```js
cd ~/
$ sbatch submit.sh
제출된 작업 8
```

제출된 작업의 상태를 기본 배치 대기열에서 확인할 수 있어요

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
$ squeue -al
Sun May 19 14:26:03 2024
JOBID PARTITION     NAME     USER    STATE       TIME TIME_LIMI  NODES NODELIST(REASON)
    8     batch  testjob       pi  RUNNING       0:03      1:00      1 rpnode01
```

이 작업은 rpnode01에서 실행되고 1분의 시간 제한이 있습니다.

# 다음 단계

이전 글에서 언급했듯이, 이 HPC 클러스터는 테스트 환경으로 사용됩니다. 현재 기본 Slurm 및 중앙 저장 기능을 갖추고 있지만, 향후 확장 및 향상이 가능합니다. 사용자 계정, 디스크 할당량 관리, 환경 모듈 및 Conda 패키지 매니저를 활용한 소프트웨어 스택 설정, MPI 구현, Jupyterhub 서비스 설정 등을 다루는 후속 글을 쓸 계획이 있습니다.

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

제가 몇 년 전에 작성한 HPC 클러스터 설정 안내서를 위해 만든 GitHub 저장소를 자유롭게 확인해보세요. 이 저장소에서는 언급된 HPC 기능의 설정 프로세스를 다루고 있습니다. 정보가 다소 오래되었을 수 있고 현재 버전과 호환되도록 몇 가지 조정이 필요할 수 있습니다.

이 게시물이 좋은 시작점이 되었으면 좋겠습니다. 읽어 주셔서 감사합니다!
