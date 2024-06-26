---
title: "에지 컴퓨팅을 위한 비용 절감을 위한 2-노드 HA Kubernetes"
description: ""
coverImage: "/assets/img/2024-05-23-Two-nodeHAKubernetesforedgecomputingcostsavings_0.png"
date: 2024-05-23 14:29
ogImage:
  url: /assets/img/2024-05-23-Two-nodeHAKubernetesforedgecomputingcostsavings_0.png
tag: Tech
originalTitle: "Two-node HA Kubernetes for edge computing cost savings"
link: "https://medium.com/itnext/two-node-ha-kubernetes-for-edge-computing-cost-savings-9a009eb076ac"
---

# Edge computing은 중요합니다

현재 기업들은 머신러닝 및 실시간 애플리케이션과 같은 모든 종류의 워크로드에 대한 엣지 컴퓨팅의 혜택을 인정합니다. 이러한 시나리오에서는 데이터 처리를 로컬에서 수행해야 합니다.

관련 데이터의 양이 많고 네트워크 연결이 제한적일 수 있습니다. 중앙 데이터 센터나 클라우드 컴퓨팅 환경에서 처리 및 분석을 수행하는 동안 빠른 응답 시간을 제공하는 것은 불가능합니다.

엣지 컴퓨팅 사용 사례에서 이러한 소프트웨어 애플리케이션을 실행할 때 높은 가용성이 필수입니다. 단일 노드(단일 CPU, 디스크 등)를 의존하는 것은 완전히 위험합니다.

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

예를 들어, 번잡한 패스트푸드 음식점의 포인트 오브 세일 애플리케이션이 클러스터 고장으로 다운되어 하루 매출(이상)을 손해 볼 때를 상상해보세요.

또는 제조공장의 에지 서버가 고장나 IoT 디바이스를 운용 중이며, 생산이 중단될 때를 상상해보세요.

또는 물류 창고에서 드론을 관리하는 컴퓨터 비전 앱이 하드웨어 고장으로 인해 작업자 안전이 위험에 처할 때를 상상해보세요.

# 쿠버네티스에서의 고가용성은 비싸요!

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

에지 애플리케이션을 위한 고가용성 아키텍처를 구현하려면 일반적으로 사이트당 세 노드로 이동해야 합니다. 이것은 Kubernetes의 기본 키-값 저장소인 etcd가 일관성과 가용성을 보장하려면 최소 세 노드가 필요하기 때문입니다.

빠뜨리기 쉬운 원가

그리고 퀵 서비스 레스토랑 및 소매와 같은 섹터의 에지 배포는 종종 수만 개의 가게로 확장됩니다. 이로 인해 전사적인 세 개 노드 HA용으로 배포하는 비용은 빠르게 누적됩니다: 하드웨어, 케이블 연결, 전원, 그리고 센서 – 모두 중복됩니다!

![이미지](/assets/img/2024-05-23-Two-nodeHAKubernetesforedgecomputingcostsavings_0.png)

# 비용 지출 없는 가용성

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

만약 세 개가 아닌 두 개의 엣지 노드를 사용하여 비용 효율적이고 (대부분) 고가용성을 갖는 Kubernetes 클러스터를 제공할 수 있다면 어떨까요?

작년 가을, Spectro Cloud 팀은 이 질문에 대한 답을 찾으려 노력했고 그 과정에서 진전 사항을 블로그와 KubeCon Paris에서의 프레젠테이션을 통해 공유했습니다 (이 때 데모 신이 우리 편은 아니었죠).

오늘은 최신 2 노드 HA 아키텍처를 안내해 드릴 것이며, 이는 제대로 활용된 솔루션이며 제품 규모로 실행되는 엣지 Kubernetes 애플리케이션에 대해 약 33% 비용을 절감하는 해결책입니다!

마지막으로 주의사항이 있습니다: CAP 이론 애호가 여러분을 걱정하며, 브루어의 불가침 제약을 극복했다고 주장하는 사람은 아무도 없습니다. 명확함을 위해 끝까지 읽어주세요...

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

# 솔루션 개요

저희 2노드 HA 아키텍처는 Spectro Cloud의 기존, 검증된 엣지 솔루션을 사용하며 kairos, k3s, kube-vip, harbor 및 system-upgrader-controller를 포함한 오픈 소스 구성 요소 위에 구축됩니다.

저희는 변경불가능하고 A/B 파티션으로 나눈 부팅 가능한 OS 이미지를 통해 솔루션을 배포합니다 (고마워요, Kairos). 저희 이미지에는 Kubernetes 배포 (주로 K3s), 독점적인 Go 에이전트 및 필요에 따라 개별적으로 추가하는 사용자 정의 소프트웨어가 포함됩니다. 모든 구성은 클라우드-컨피그 구문으로 선언적으로 지정되며 클라우드-이닛에 의해 실행됩니다.

엣지 디바이스들은 초기에 등록 모드로 프로비저닝됩니다. 그 후 Spectro Cloud의 Palette 플랫폼이나 로컬 관리 GUI를 통해 클러스터에 추가되어 원하는 부팅 가능한 OS 이미지를 사용하여 설치 모드로 재부팅됩니다. Kubernetes가 온라인으로 되면 Go 에이전트에 의해 애드온이 설치되고 조정됩니다.

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

Immutable upgrades are performed by streaming a new image to the node (or via USB for air-gapped use cases), writing it to disk, and rebooting from the B partition. If anything goes wrong, we automatically fail back to the A partition with known-stable configuration.

For two node support, we layered kine and postgresql into our existing edge solution and introduced a handful of additional mechanisms for lifecycle management, including liveness checks, a liveness client and server, and a finite state machine to ensure correctness of the kine endpoint configuration.

As always, a picture is worth a thousand words:

![Image Description](/assets/img/2024-05-23-Two-nodeHAKubernetesforedgecomputingcostsavings_1.png)

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

두 가지 엣지 호스트가 프로비저닝되었습니다: 하나는 리더이고 다른 하나는 팔로워입니다. 양쪽 호스트에는 systemd 서비스로 구성된 Go 에이전트가 포함되어 있으며, 설정 가능한 주기로 지속적으로 살아있음 조정을 실행합니다. 살아있음 조정 루프는 각 호스트의 역할을 수정할 여부와 방법을 결정하는 유한 상태 기계(FSM)입니다.

위 다이어그램은 명확성을 위해 개별적인 Kubernetes 구성 요소를 나타냈지만 실제로는 제어 플레인, kubelet 및 kube-proxy가 각 호스트의 단일 K3s 서버 프로세스에 포함되어 있습니다. 두 K3s 서버 모두 로컬 kine 프로세스의 데이터 저장소 엔드포인트로 http://localhost:2379를 사용하도록 구성되어 있습니다.

Kine 및 postgres는 각 호스트에서 systemd를 통해 구성되지만, 리더의 kine 엔드포인트는 로컬호스트의 postgres 프로세스를 가리키고 팔로워의 kine 엔드포인트는 리더의 postgres 프로세스를 가리킵니다.

마지막으로, 두 postgres 프로세스 간에 단방향 논리 복제가 구성되어 있어서 각 호스트의 kine 테이블 내용을 동기화합니다. 두 kine 프로세스가 API 서버 트래픽을 리더로 인바운드하므로 모든 쓰기는 먼저 리더 데이터베이스에 기록된 다음 팔로워로 일관된 방식으로 복제됩니다. 각 호스트에서 부팅 시에 kine 및 k3s를 시작하기 전에 실행되는 한 번 실행되는 systemd 유닛인 kine 엔드포인트 조정기가 논리 복제 구성을 조율하고 kine 프로세스가 올바른 엔드포인트로 구성되어 있는지 보장합니다.

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

## 초기화: 엣지 호스트 메타데이터

시스템이 처음 프로비저닝될 때, 각 호스트는 호스트의 역할 및 주소, 컨트롤 플레인 엔드포인트 및 마지막 수정된 타임스탬프를 포함한 메타데이터 파일(이하 호스트메타로 지칭함)을 초기화합니다. 호스트메타 콘텐츠는 로컬 장치 구성(네트워크 인터페이스) 및 엣지 클러스터 구성의 조합에서 유추됩니다. 이 구성은 Spectro Cloud의 Palette 플랫폼에서 가져오거나 공기차단된 익스포트로부터 로드될 수 있습니다.

```js
// 호스트메타 예시
localHost:
  name: host-1
  address: 10.10.10.1
  uid: host-1-uid
altHost:
  name: host-2
  address: 10.10.10.2
  uid: host-2-uid
leader:
  name: host-1
  address: 10.10.10.1
  uid: host-1-uid
follower:
  name: host-2
  address: 10.10.10.2
  uid: host-2-uid
controlPlaneEndpoint: 10.10.10.0
lastModified: "Mon Apr 29 18:54:47 2024"
```

호스트가 등록 모드에 있거나 특정 센티넬 파일인 /oem/.two-node-pause가 존재하는 경우, 라이브니스 조정은 무시됩니다.

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

다른 센티넬 파일, /oem/.two-node-initialized,은 호스트 메타가 성공적으로 초기화된 후에 생성됩니다. 그 이후에는 엣지 클러스터 구성이 아닌 호스트 메타 파일만을 참조하여 역할 할당 및 상태 전이를 위해 사용됩니다. 이것은 클러스터가 완전히 연결이 끊겨 자체적으로 작동한다는 것을 의미합니다.

## Kine 엔드포인트 조정기

Kine 엔드포인트 조정기는 먼저 /oem/.two-node-initialized 파일의 존재 여부를 확인합니다. 이 파일이 존재하지 않는 경우 미리 지정된 로컬 호스트 메타 파일을 가져와 /oem/.two-node-initialized 파일을 생성합니다.

다음으로, 해당 호스트가 팔로워인 경우, 리더에서 발행 및 복제 슬롯 작업을 확인하거나 구성하기 위해 PostgreSQL 작업들의 일련을 수행합니다. 이 작업들은 리더에서 발행 및 팔로워에서 리더의 발행에 대한 구독을 포함합니다.

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

시스템 상태에 따라 kine-endpoint-reconciler가 엣지 호스트 교체 작업을 수행할 수 있습니다.

마지막으로, 로컬 및 원격 hostMeta 파일이 조정됩니다. 이 파일들이 일치하지 않으면 호스트의 역할이 변경될 수 있습니다. 즉, 승격 또는 강등될 수 있습니다. 이 단계에서 강등이 발생하면 로컬 Kubernetes 데이터베이스를 삭제하고 복제를 다시 구성하는 일련의 PostgreSQL 작업이 실행됩니다. 이는 로컬 Kubernetes 데이터베이스의 전체 동기화를 트리거합니다.

kine-endpoint-reconciler가 완료되면 시스템은 정상 작동 모드로 들어가며 제어가 라이브니스 서비스로 전환됩니다.

![이미지](/assets/img/2024-05-23-Two-nodeHAKubernetesforedgecomputingcostsavings_2.png)

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

키네 엔드포인트 조정기가 완료되면, 시스템은 정상 운영 모드로 전환되어 liveness 서비스로 제어가 전환됩니다.

## Liveness reconciliation

각각의 liveness 주기마다, 각 Go 에이전트는 두 개의 hostMeta 파일을 획득합니다. 하나는 로컬 파일 시스템에서 가져오고, 다른 하나는 대체 호스트의 liveness 서버에서 가져옵니다 (대체 호스트가 이용 가능하고 건강한 경우에만 일시적으로 검색됨). 이후 일련의 건강 검사가 실행되고, 결과에 따라 상태 전이가 발생할 수 있습니다:

- 제어 플레인 엔드포인트로의 TCP 연결
- 대체 호스트의 쿠버네티스 API 서버로의 TCP 연결
- 대체 호스트에 대한 ICMP 핑
- 건강 검사 스크립트 (선택사항) — 디스크 이용률, 메모리 압박 등을 확인하는 임의의 쉘 스크립트

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

각 실패한 확인에 대해 카운터가 증가됩니다. 다음 다이어그램은 liveness-service의 조화 흐름을 보여줍니다:

![Image](/assets/img/2024-05-23-Two-nodeHAKubernetesforedgecomputingcostsavings_3.png)

## 승급

리더가 다운되고 팔로워의 라이브니스 서비스가 네 번의 건강 확인 실패를 감지하면 승급이 시작됩니다:

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

![Image](/assets/img/2024-05-23-Two-nodeHAKubernetesforedgecomputingcostsavings_4.png)

프로모션 기간 중 팔로워 호스트에서 다음 이벤트 시퀀스가 발생합니다:

- 다음 파일은 역할 변경을 반영하도록 업데이트됩니다:
  - 로컬 호스트 메타 파일
  - 다양한 클라우드 초기화 구성 파일
  - kine 엔드포인트가 localhost를 가리키도록 다시 구성됨
  - 리더에 대한 논리 복제 구독이 해제됨
  - k3s 및 kine가 중지됨
  - 다음 "등록 해제" SQL이 실행됨:

```js
DELETE FROM kine
WHERE name LIKE '/registry/masterleases%'
OR name LIKE '/registry/leases/%'
OR name = '/registry/services/endpoints/default/kubernetes';
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

디폴트 네임스페이스에서 쿠버네티스 서비스의 엔드포인트를 삭제하면 k3s 서버가 손상된 호스트와의 웹소켓 터널을 끊게 됩니다.

게다가, k3s를 다시 시작하면 모든 로컬 컨트롤러의 리소스 리소소르를 빠르게 확보하기 위해 kine 테이블에서 모든 k8s 리스를 삭제합니다.

5. 다음의 "시퀀스 복구" SQL이 실행됩니다:

```js
SELECT setval('kine_id_seq', (SELECT MAX(id) FROM KINE), true);
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

이 작업은 논리 복제 중에 시퀀스 데이터를 복제하지 않기 때문에 필수적입니다. 이 단계를 수행하지 않으면 kine 테이블에 prev_revision ` id로 새로운 행이 삽입되어 kine가 가정하는 키 불변성을 위반하고 데이터베이스 손상을 일으킬 수 있습니다.

6. k3s 및 kine을 다시 시작합니다.

![이미지](/assets/img/2024-05-23-Two-nodeHAKubernetesforedgecomputingcostsavings_5.png)

## Demotion

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

원래 리더였던 호스트가 일시적으로 오프라인 상태가 되면, 팔로워가 리더의 부재를 보완하여 자체를 승격합니다. 그 후 원래 리더가 온라인 상태로 돌아오면, 그의 kine-endpoint-reconciler은 불일치를 감지하고 강등을 시작합니다.

강등 중에는 원래 리더(현재는 팔로워로 지칭함) 호스트에서 다음 일련의 이벤트가 발생합니다:

- 리더(이후 팔로워로 지칭)는 두 호스트의 역할 변경을 반영하도록 다음 파일을 업데이트합니다:
  - 로컬 호스트 메타
  - 다양한 클라우드 초기화 구성 파일
  - kine 엔드포인트를 localhost가 아닌 새 리더를 가리키도록 다시 구성합니다.
  - 현재 리더의 데이터베이스에 게시물이 생성됩니다(이미 존재하지 않는 경우).
  - 팔로워는 kine 테이블의 내용을 삭제합니다.
  - 팔로워는 copy_data = true로 리더의 게시물을 구독하여 전체 kine 테이블을 다시 동기화합니다.
  - 비활성 복제 슬롯이 이미 있는 경우 사용하고, 그렇지 않으면 리더에 새로운 복제 슬롯이 생성됩니다.
  - k3s 및 kine이 다시 시작됩니다.

## Edge 호스트 교체

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

만약 두 호스트 중 하나가 영구적으로 손상된 경우(예: 하드 드라이브 고장, NIC 고장 등), 대체 장치가 엣지 사이트로 발송됩니다. 새 장치 부팅 후 저하된 팔로워를 원활하게 대체하며, 그 후 해당 장치를 폐기할 수 있습니다.

![이미지](/assets/img/2024-05-23-Two-nodeHAKubernetesforedgecomputingcostsavings_6.png)

대체 호스트가 부팅되며 클러스터 구성에 액세스하면 현재 리더의 주소 및 현재 팔로워임을 표시합니다. 연결된 환경에서는 클러스터 구성이 팔레트 플랫폼으로부터 가져옵니다. 공기가락된 환경에서는 새 대체 호스트가 "클러스터 구성 내보내기"로 미리 초기화됩니다.

대체 호스트의 kine-endpoint-reconciler가 리더의 hostMeta를 요청하고 리더에 의해 팔로워로 간주되지 않음을 확인하고, 대체 API를 통해 리더에게 자신의 존재를 알리게 됩니다. 이로써 리더는 자신의 hostMeta를 업데이트하여 새 호스트를 팔로워로 등록합니다.

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

마지막으로, 대체 호스트는 강등에서 설명한 것과 동일한 방식으로 리더를 구독하며, 이는 리더의 kine 테이블 전체를 복제하게 됩니다.

## 업그레이드

업그레이드 중에는 승급 또는 강등이 없습니다(호스트는 원래의 역할을 유지), 그러나 리더가 다시 부팅하는 동안 필수적인 API 서버 다운 타임이 발생합니다. 다음 표는 업그레이드 중 발생하는 이벤트 순서를 보여줍니다:

![Sequential Events during Upgrade](/assets/img/2024-05-23-Two-nodeHAKubernetesforedgecomputingcostsavings_7.png)

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

두 개의 호스트는 시스템 업그레이드 컨트롤러 계획을 통해 모든 제어 평면 노드를 무작위 순서로 대상으로 업데이트됩니다. 업그레이드를 시작하기 전에 Go 에이전트가 각 호스트의 라이브니스 서비스를 일시 중지시킵니다. 이로써, 리더가 먼저 업그레이드되는 경우에 팔로워가 자신을 승격하지 못하도록합니다. 두 호스트가 모두 업그레이드된 후에는 그들의 라이브니스 서비스가 다시 시작됩니다.

## 대체 업그레이드 흐름

대체 업그레이드 흐름이 가능합니다. 여기서는 먼저 팔로워가 업그레이드되고, 그 후 호스트의 역할이 교환되어(팔로워 승격, 리더 강등) 원래의 리더가 업그레이드되는 흐름이 있습니다. 그러나 이러한 흐름은 매우 복잡하며 오류가 발생할 가능성이 있습니다. 첫째, 강등으로 인해 데이터베이스가 삭제되고 다시 동기화되어야 하므로, 위의 흐름을 선택했습니다.

참고: 더 복잡한 흐름에서는 파드가 다시 균형을 이루는 데 걸리는 시간으로 인해 여전히 다운 타임이 있습니다. 아래 K8s 구성 옵션은 건강하지 않은 노드를 감지하고 해당 노드에서 파드가 추방될 때까지 걸리는 시간을 결정합니다:

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

```json
kube-controller-manager-arg:
- node-monitor-period=5s
- node-monitor-grace-period=20s
```

# HA 및 CAP 이론: 우리가 얼마나 잘 했을까요?

소개에서는 CAP 이론과 솔루션의 (대부분) 고가용성에 대해 더 상세히 설명할 것을 약속했습니다. 제가 A\*P라고 부르는 것입니다:

- 일관성이 최종적으로 일치하기 때문에 일관성을 나타내는 C는 제외합니다.
- 가용성을 나타내는 A에 별표가 붙는 이유는 프로모션, 강등 및 업그레이드 중에 일부 API 서버 다운타임이 발생하기 때문입니다 (벤치마크에 따르면 평균 4.5분). 그러나 팔로워에 배포된 어떠한 애플리케이션도 완전히 사용 가능할 것입니다. 이 아키텍처로 고가용성을 달성하기 위한 핵심은 모든 애플리케이션이 데몬셋이거나 적어도 하나의 레플리카가 각 노드에서 실행되도록 탑톨로지 분산 제약 조건을 가져야 한다는 것입니다. 롱혼은 상태 있는 애플리케이션을 위해 충돌 일관된 블록 스토리지를 제공하기 위해 쉽게 설치될 수 있습니다.
- 파티션 허용도를 나타내는 P는 hostMeta의 lastModified 타임스탬프를 사용하여 처리됩니다. 네트워크 분할이 발생한 경우 두 호스트가 자체 프로모션합니다. 분할이 해소되면 가장 최근에 업데이트된 호스트가 "승리"합니다. 패배한 호스트는 자신을 강등시키고 승리자의 데이터베이스 내용을 복사합니다.

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

의심의 여지없이, 3 노드 Kubernetes 클러스터는 아마도 덜한 아키텍처 복잡성으로 더 강력한 보장을 제공하지만, 규모에 따라 상당한 자본 투자가 필요합니다. 자체 상자 비용 뿐만 아니라 케이블링, 배송, 소프트웨어, 전력 소비 등의 비용도 발생합니다. 비용을 최적화하거나 엣지 컴퓨팅 사용 사례를 고려 중이라면, 2 노드 솔루션이 즉시 비용을 절감하고 중요한 절감을 실현할 수 있을 것입니다.

마지막으로, K3s, kine 및 NATS를 사용한 2 노드 솔루션을 추구하며 한계를 느낀 Synadia 팀으로부터 영감을 받았다는 점을 언급하지 않을 수 없습니다. 그들의 작품은 우리 자신의 계획을 영감주었습니다.

이렇게 멀리까지 읽어주셔서 감사합니다. 궁금한 점이 있으면 직접 tyler@spectrocloud.com 으로 문의하거나 Spectro Cloud 커뮤니티 Slack을 확인해주시기 바랍니다.

그리고 쿠버네티스를 활용한 엣지 컴퓨팅 프로젝트를 진행 중이라면, 당사의 Palette Edge를 살펴보시고 도움이 필요한 부분이 있는지 확인해주시기 바랍니다.
