---
title: "롱혼 - K8s를 위한 분산형 블록 스토리지"
description: ""
coverImage: "/assets/img/2024-05-18-LonghornDistributedBlockStorageforK8s_0.png"
date: 2024-05-18 16:50
ogImage:
  url: /assets/img/2024-05-18-LonghornDistributedBlockStorageforK8s_0.png
tag: Tech
originalTitle: "Longhorn — Distributed Block Storage for K8s"
link: "https://medium.com/@abdelrhmanahmed131/longhorn-distributed-block-storage-for-k8s-2ea11df400d1"
---

![Longhorn](/assets/img/2024-05-18-LonghornDistributedBlockStorageforK8s_0.png)

# Longhorn이란 무엇인가요?

## K8s 클러스터에서 영구 저장소가 필요한 이유는 무엇인가요?

컨테이너화된 응용 프로그램에서 데이터는 일반적으로 컨테이너 자체와는 별도로 저장되는 저장 시스템에 저장됩니다. 이는 컨테이너를 쉽게 파기하거나 대체할 수 있게 하면서도 중요한 데이터를 손실하지 않도록 합니다. 영구 데이터를 저장한다는 것은 데이터가 컨테이너가 중지, 다시 시작되거나 삭제되어도 손실되지 않도록 데이터를 저장하는 방식을 의미합니다. 이를 위해 클라우드 네이티브 분산 블록 저장 시스템과 같은 영구 저장소 솔루션을 사용하여 데이터를 저장함으로써 컨테이너가 필요할 때 데이터에 액세스할 수 있도록 합니다.

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

데이터는 애플리케이션 데이터, 구성 파일, 사용자 데이터, 데이터베이스 및 시간이 지날수록 유지하고 액세스해야하는 다른 유형의 데이터일 수 있습니다.

# Longhorn 작동 방식은?

Longhorn에는 세 가지 주요 구성 요소가 있습니다:

- Longhorn Manager
- Longhorn Engine
- Longhorn UI

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

## 롱혼 매니저

롱혼 매니저 Pod는 롱혼 클러스터의 각 노드에서 Kubernetes 데몬세트로 실행됩니다. Kubernetes 클러스터에서 볼륨을 생성하고 관리하며 UI 또는 Kubernetes를 위한 볼륨 플러그인의 API 호출을 처리합니다.

롱혼 매니저는 Kubernetes API 서버와 통신하여 새로운 롱혼 볼륨 CRD를 생성합니다. 그런 다음 롱혼 매니저는 API 서버 응답을 모니터링하고 Kubernetes API 서버가 새 롱혼 볼륨 CRD를 생성한 것을 감지하면 새 볼륨을 생성합니다.

## 롱혼 엔진

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

롱혼 매니저가 볼륨을 생성하도록 요청받으면, 그 볼륨이 연결된 노드에 롱혼 엔진 인스턴스를 생성하고, 각 복제가 배치될 노드에 각각 복제를 생성합니다. 복제는 최대 가용성을 보장하기 위해 별도의 호스트에 배치되어야 합니다.

롱혼은 롱혼 클러스터의 각 노드에 볼륨마다 별도의 엔진을 생성합니다.

엔진은 롱혼 볼륨의 I/O 작업을 처리하는 구성 요소입니다. 볼륨이 생성되면 롱혼은 해당 볼륨이 실행되는 각 노드에 별도의 엔진을 생성합니다. 이렇게 하면 볼륨 데이터가 해당 볼륨이 실행 중인 노드에 로컬로 저장되도록 보장합니다.

만약 볼륨을 위한 롱혼 엔진을 호스팅하는 노드가 충돌하거나 다른 이유로 이용 불가능 상태가 되면, 롱혼은 해당 장애를 감지하고 자동으로 다른 이용 가능한 노드에 엔진을 재배치합니다. 이 프로세스를 자동 노드 복구라고 합니다.

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

엔진은 Pod나 배포(Deployment)와 같은 Kubernetes 객체가 아닙니다. 대신에, 그것은 호스트 운영 체제에서 이진 실행 파일로 실행되는 Longhorn에 특화된 프로세스입니다.

아래 그림은 Longhorn이 작동하는 방식을 보여줍니다:

![Longhorn Architecture](/assets/img/2024-05-18-LonghornDistributedBlockStorageforK8s_1.png)

- Longhorn 볼륨은 세 개의 인스턴스가 있습니다.
- 각 볼륨에는 Longhorn 엔진이라 불리는 전용 컨트롤러가 있으며 Linux 프로세스로 실행됩니다.
- 각 Longhorn 볼륨에는 두 개의 복제본이 있고, 각각의 복제본은 Linux 프로세스입니다.
- 그림에서 화살표는 볼륨, 컨트롤러 인스턴스, 복제본 인스턴스 및 디스크 간의 읽기/쓰기 데이터 흐름을 나타냅니다.
- 볼륨마다 별도의 Longhorn 엔진을 생성하여, 하나의 컨트롤러가 실패하더라도 다른 볼륨의 기능에는 영향을 미치지 않습니다.

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

## 롱혼 UI

롱혼 UI는 롱혼 매니저에 의해 쿠버네티스 배포로 배포되며, 기본적으로 하나의 레플리카로 실행되므로 단일 노드에서 팟으로 실행됩니다.

롱혼 UI는 롱혼 API를 통해 롱혼 매니저와 상호 작용합니다. 롱혼 UI를 통해 스냅샷, 백업, 노드 및 디스크를 관리할 수 있습니다.

또한, 롱혼 UI에 의해 클러스터 워커 노드의 공간 사용량이 수집되고 시각화됩니다.

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

다음 그림은 Longhorn UI에서 보는 그림을 보여줍니다:

![Longhorn UI view](/assets/img/2024-05-18-LonghornDistributedBlockStorageforK8s_2.png)

이제 이 그림에서 각 용어의 의미를 자세히 알아보겠습니다:

- Schedulable: Longhorn 볼륨 스케줄링에 사용할 수 있는 실제 공간입니다.
- Reserved: 다른 애플리케이션과 시스템을 위해 예약된 공간입니다.
- Used: Longhorn, 시스템 및 다른 애플리케이션에서 사용된 실제 공간입니다.
- Disabled: Longhorn 볼륨을 스케줄링할 수 없는 디스크/노드의 총 공간입니다.

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

# Longhorn 설치 (전제 조건 및 요구 사항)

## 노드 수

Longhorn은 Kubernetes 클러스터에 최소한 하나의 노드로 설치할 수 있지만, 데이터 중복 및 고가용성을 위해 적어도 세 개의 노드가 권장됩니다.

## K8s 클러스터의 각 노드는 다음 요구 사항을 충족해야 합니다:

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

- Kubernetes와 호환되는 컨테이너 런타임(Docker v1.13+, containerd v1.3.7)이 필요합니다.
- Kubernetes 버전은 `v1.21` 이상이어야 합니다.
- open-iscsi가 설치되어 있어야 하며, 모든 노드에서 iscsid 데몬이 실행 중이어야 합니다.

- RWX 지원을 위해 각 노드에 NFSv4 클라이언트가 설치되어 있어야 합니다.

- 호스트 파일 시스템이 데이터를 저장하기 위한 파일 extents 기능을 지원해야 합니다. 현재 지원하는 파일 시스템은 ext4 및 XFS입니다.

- 다음 Linux 명령 줄 유틸리티가 설치되어 있어야 합니다: bash, curl, findmnt, grep, awk, blkid, lsblk

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

- 마운트 전파를 활성화해야 합니다.

- 롱혼 워크로드는 롱혼이 올바르게 배포 및 운영되기 위해 루트 사용자로 실행할 수 있어야 합니다.

# 권장 최소 하드웨어

- 3 노드
- 노드 당 4 vCPU
- 노드 당 4 GiB
- 스토리지를 위한 노드에는 SSD/NVMe 또는 유사한 성능 블록 장치(추천)
- 스토리지를 위한 노드에는 HDD/스핀 디스크 또는 유사한 성능 블록 장치(확인됨)
- 볼륨 당 최대 500/250 IOPS(1 MiB I/O)
- 볼륨 당 최대 500/250 처리량(MiB/s)

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

# 운영 체제

- Ubuntu: 버전 (20.04, 22.04)
- SLES: 버전 (15 SP4)
- SLE Micro: 버전 (5.3)
- CentOS: 버전 (8.4)
- RHEL: 버전 (8.6)
- Oracle Linux: 버전 (8.6)
- Rocky Linux: 버전 (8.6)

# Longhorn 기능, 제한 및 단점

## 기능

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

- 고가용성: Longhorn은 클러스터 내 여러 노드에 볼륨을 복제하여 데이터의 고가용성을 제공하기 위해 설계되었습니다.
- 스냅샷 및 백업: Longhorn은 복제본이 다운됐을 때마다 (시스템) 스냅샷을 자동으로 찍고 다른 노드에서 다시 복구 시작합니다.
- 재해 복구: Longhorn은 재해로부터 회복하기 위해 볼륨을 다시 구축하고 백업 데이터를 복원하는 기능을 제공합니다.
- 복제: Longhorn은 서로 다른 클러스터 간에 볼륨을 복제하여 여러 위치에 걸쳐 재해 복구 솔루션을 만들 수 있도록합니다.
- 동적 할당: Longhorn은 Kubernetes에서 볼륨의 동적 할당을 지원하여 Kubernetes API를 사용하여 볼륨을 생성하고 관리할 수 있게 합니다.
- 볼륨 확장: Longhorn은 볼륨의 온라인 확장을 지원하여 볼륨 크기를 증가시키고 다운타임 없이 볼륨을 확장할 수 있습니다.
- 모니터링 및 메트릭: Longhorn은 볼륨의 상태 및 성능에 대한 상세한 메트릭 및 모니터링 정보를 제공하여 스토리지 인프라의 문제 해결 및 최적화를 쉽게 할 수 있습니다.
- 웹 기반 UI: Longhorn은 웹 기반 사용자 인터페이스를 제공하여 볼륨 및 스토리지 인프라를 손쉽게 관리하고 모니터링할 수 있습니다.

## 제한 사항 및 단점

- 버전 간 업그레이드는 복잡한 과정입니다.
- Longhorn은 높은 버전에서 낮은 버전으로의 다운그레이드를 지원하지 않습니다.
- Longhorn 블록 스토리지 속도는 로컬 디스크 스토리지 속도보다 약간 느립니다.

# Longhorn 설치

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

롱혼은 Helm, kubectl 및 Rancher Apps & Marketplace로 설치할 수 있어요.

롱혼은 100% 오픈 소스 소프트웨어에요. 프로젝트 소스 코드는 여러 저장소에 분산돼 있어요:

- Longhorn Engine — 코어 컨트롤러/레플리카 로직.
- Longhorn Instance Manager — 컨트롤러/레플리카 인스턴스 라이프사이클 관리.
- Longhorn Share Manager — NFS 프로비저너로 롱혼 볼륨을 ReadWriteMany 볼륨으로 노출시켜요.
- Backing Image Manager — 백킹 이미지 파일 라이프사이클 관리.
- Longhorn Manager — 롱혼 오케스트레이션, Kubernetes용 CSI(Container Storage Interface) 드라이버를 포함하고 있어요.
- Longhorn UI — 대시보드.

## Helm으로 설치하기

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

요구 사항

- 작업 스테이션에는 Helm v2.0+가 설치되어 있어야 합니다.

Longhorn 헬름 차트를 여기에서 확인할 수 있습니다.

헬름 차트의 내용:

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

템플릿: 쿠버네티스 매니페스트를 포함하고 있는 폴더로, 차트에 의해 생성되는 리소스를 정의합니다.

values.yaml: 차트에서 사용되는 구성 매개변수의 기본 값을 정의하는 파일입니다.

Chart.yaml: 차트에 관한 메타데이터를 포함하고 있는 파일로, 이름, 버전, 설명, 유지보수자 등을 포함합니다.

설치

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

Longhorn 차트 저장소를 추가해보세요:

```js
$ helm repo add longhorn https://charts.longhorn.io
```

차트 저장소에서 로컬 Longhorn 차트 정보를 업데이트해보세요:

```js
$ helm repo update
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

Longhorn 차트 설치:

Helm 2를 이용해 다음 명령어를 실행하면 longhorn-system 네임스페이스를 생성하고 Longhorn 차트를 함께 설치합니다:

```js
$ helm install longhorn longhorn/longhorn --namespace longhorn-system
```

Helm 3를 이용해 다음 명령어를 실행하면 먼저 longhorn-system 네임스페이스를 생성하고 Longhorn 차트를 설치합니다:

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
$ kubectl create namespace longhorn-system
$ helm install longhorn longhorn/longhorn --namespace longhorn-system
```

설치가 성공적으로 완료되었는지 확인하려면 설치 후 실행 중인 파드를 확인하여 다음 명령을 실행하면 Longhorn 파드를 볼 수 있어요:

```bash
$ kubectl get pods --namespace longhorn-system
```

# Longhorn 볼륨 생성하기

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

Step_1: 롱혼 StorageClass 생성하기

다음은 storageclass.yaml이 어떻게 생겼는지입니다:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: longhorn
provisioner: driver.longhorn.io
allowVolumeExpansion: true
```

```bash
$ kubectl apply -f storageclass.yaml
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

Step_2: Longhorn StorageClass를 참조하는 PersistentVolumeClaim (PVC)를 만듭니다.

다음은 longhorn-pvc.yaml 파일의 내용입니다.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: longhorn-vol-pvc
spec:
  storageClassName: longhorn
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
```

```bash
$ kubectl apply -f longhorn-pvc.yaml
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

Step 3: Longhorn 볼륨을 사용하는 Pod 생성하기

아래는 pod-with-longhorn.yaml 파일의 내용입니다:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: longhorn-test
spec:
  containers:
    - name: longhorn-test-container
      image: busybox
      command: ["/bin/sh"]
      args:
        [
          "-c",
          "while true; do echo $(date) >> /mnt/data/date.txt; sleep 1; done",
        ]
      volumeMounts:
        - mountPath: /mnt/data
          name: longhorn-vol
  volumes:
    - name: longhorn-vol
      persistentVolumeClaim:
        claimName: longhorn-vol-pvc
```

이후, longhorn-test 라는 이름의 Pod가 시작되며, Longhorn StorageClass를 참조하는 longhorn-vol-pvc라는 PersistentVolumeClaim도 생성됩니다.

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

영구 볼륨 클레임이 Pod에 볼륨으로 마운트됩니다.

# 제거

Helm 2를 사용하여 Longhorn을 제거하려면:

```js
# 삭제 확인을 위해 Longhorn 설정을 패치합니다
$ kubectl -n longhorn-system patch -p '{"value": "true"}' --type=merge lhs deleting-confirmation-flag

# Longhorn을 제거합니다
$ helm delete longhorn --purge
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

Helm 3으로 Longhorn을 제거하는 방법은 다음과 같습니다:

```js
# 삭제 확인을 위해 Longhorn 설정을 패치
kubectl -n longhorn-system patch -p '{"value": "true"}' --type=merge lhs deleting-confirmation-flag

# Longhorn 제거
helm uninstall longhorn -n longhorn-system

# Longhorn 네임스페이스 삭제
kubectl delete namespace longhorn-system
```

# 결론

요약하자면, Longhorn은 Kubernetes에서 영속적인 저장소를 생성하고 관리하기 위한 솔루션이다. 관리자, 컨트롤러, 레플리카 인스턴스 등의 컴포넌트를 특징으로 하는 아키텍처는 데이터의 내구성과 고가용성을 보장한다. Longhorn의 주요 기능으로는 분산 블록 저장소, 스냅샷, 백업, 볼륨 복제가 있어 상태를 유지해야 하는 응용프로그램을 실행하는 데 이상적이다. Helm 2 또는 3을 사용한 쉬운 설치로, Longhorn은 저장소 프로비저닝과 구성을 단순화하여 Kubernetes 환경에서 개발자와 시스템 관리자에게 가치 있는 도구가 된다.
