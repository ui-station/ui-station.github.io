---
title: "라즈베리 파이로 쿠버네티스 클러스터 구축 가이드"
description: ""
coverImage: "/assets/img/2024-05-18-AGuidetoBuildingaKubernetesClusterwithRaspberryPis_0.png"
date: 2024-05-18 19:08
ogImage: 
  url: /assets/img/2024-05-18-AGuidetoBuildingaKubernetesClusterwithRaspberryPis_0.png
tag: Tech
originalTitle: "A Guide to Building a Kubernetes Cluster with Raspberry Pi’s"
link: "https://medium.com/@alexsniffin/a-guide-to-building-a-kubernetes-cluster-with-raspberry-pis-23fa4938d420"
---


몇 년 전에 라즈베리 파이에서 Kubernetes 클러스터를 세팅했었어요. 당시 라즈베리 파이의 ARM 아키텍처는 몇 가지 어려움을 야기했죠. ARM을 지원하는 애플리케이션을 찾는 건 어려운 과제였는데, 그래서 필요한 애플리케이션과 컨테이너를 직접 빌드해야 했던 적이 많았어요.

그런데 그 이후로 상황이 크게 개선되었어요! 새로운 64비트 라즈베리 파이 OS의 등장과 ARM의 저렴함으로 클라우드 배포에 많이 사용되는 산업에서의 인기 상승으로, 라즈베리 파이 클러스터 구축이 훨씬 간단해졌어요. 저는 클러스터를 다시 구축하기로 결정했고, 64비트 OS 및 최신 버전의 Kubernetes와 Docker로 업데이트했어요.

여러분이 자체 라즈베리 파이 Kubernetes 클러스터를 설정하는 방법에 대한 가이드를 작성했어요. 집에서 클러스터를 구축하는 여정에 유용하길 바랍니다! 🚀

# 요구 사항

<div class="content-ad"></div>

클러스터를 설정하기 위해서는 하드웨어가 필요합니다. 필요한 것들은 다음과 같아요:

- 라즈베리 파이(저는 4 모델 B를 사용했어요)
- SD 카드 1장 / 라즈베리 파이
- 이더넷 케이블 1개 / 라즈베리 파이
- 라우터 및/또는 네트워크 스위치
- USB 허브
- (선택 사항) 케이스

이 안내서는 Kubernetes 1.26.6, Docker 24.0.2 및 라즈베리 파이 Lite(64비트) 불자이에 맞춰 작성되었어요.

# OS 설정

<div class="content-ad"></div>

첫 번째 단계는 모든 Raspberry Pi에 OS를 설정해야 합니다. 그렇지 않으면 Raspberry Pi는 기본적으로 부팅할 시스템이 없습니다.

Raspberry Pi Imager를 다운로드하십시오. 이 편리한 애플리케이션은 Raspberry Pi의 다운로드와 플래싱에 사용됩니다. 이 가이드에서는 Raspberry Pi OS (Debian의 파생 버전)의 64비트 헤드리스 버전을 사용할 것입니다.

최신 Raspberry Pi와 호환되는지 확인한 후에 SD 카드를 플래싱해야 합니다. 

![Raspberry Pi Imager](/assets/img/2024-05-18-AGuidetoBuildingaKubernetesClusterwithRaspberryPis_0.png)

<div class="content-ad"></div>

SD 카드를 선택하고 OS를 플래싱하기 시작하세요. 모든 SD 카드에 대해 이 작업을 완료할 때까지 반복해주세요.

## SSH 활성화 및 기본 사용자 생성

각 Pi를 원격으로 구성할 수 있게끔 SSH를 설정해야 합니다.

SSH를 활성화하려면 SD 카드의 부트 파티션에 확장자 없이 ssh라는 빈 파일을 생성하세요.

<div class="content-ad"></div>

로그인 사용자를 설정하기 위해, SD 카드의 부팅 파티션에 userconf라는 파일을 생성하세요. 이 파일은 'name':'encrypted-password'로 구성된 텍스트 한 줄을 포함해야 합니다. 로그인 사용자로 노드를 사용했지만 원하는 대로 사용하셔도 됩니다.

encrypted-password를 생성하려면 다음 명령을 OpenSSL과 함께 실행하세요:

```js
echo '{password}' | openssl passwd -6 -stdin
```

파일을 저장하고 SD 카드를 제거하세요. 그리고 라즈베리 파이에 SD 카드를 삽입하고 전원을 켜세요. 개인 네트워크의 라우터나 네트워크 스위치에 연결되어 있는지 확인하세요.

<div class="content-ad"></div>

# 첫 번째 부팅 및 초기 구성

라즈베리 파이의 IP를 얻어야 합니다. 이를 위해 라우터를 확인할 수 있습니다. 제 경우, OpenWrt를 사용하며 DHCP 설정에서 기억하기 쉬운 정적 IP를 만듭니다.

![image](/assets/img/2024-05-18-AGuidetoBuildingaKubernetesClusterwithRaspberryPis_1.png)

첫 번째 노드에 SSH로 연결합니다. 이 노드는 클러스터의 제어 평면을 실행하는 마스터 노드가 됩니다. 라즈베리 파이로 터널링한 후에 설정을 시작할 수 있습니다!

<div class="content-ad"></div>

다음 명령어를 사용하여 사용자를 sudo 그룹에 추가해주세요.

```js
sudo usermod -aG sudo node
```

이제 rasp-config를 업데이트하여 node 사용자로 자동 부팅하도록 설정해봅시다.

```js
sudo raspi-config
```

<div class="content-ad"></div>


![image](https://miro.medium.com/v2/resize:fit:1400/1*XG-oT3YeryzngA-Xv3JY9w.gif)

“System Options” → “Boot / Auto Login” 으로 이동하여 “Console Autologin”을 선택해주세요.

# Docker & Kubernetes 초기 설정

기본적으로 cgroup 메모리 옵션이 비활성화되어 있으므로 Docker가 메모리 사용량을 제한할 수 있도록 업데이트해야 합니다. /boot/cmdline.txt를 열고 cgroup_enable=memory cgroup_memory=1을 추가해주세요.


<div class="content-ad"></div>

이제 우리의 apt 저장소를 업데이트하고 Kubernetes 저장소를 포함시킬 차례입니다.

```js
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt update && sudo apt upgrade -y
```

Docker 설치:

```js
curl -sSL https://get.docker.com | sh
sudo usermod -aG docker node
```

<div class="content-ad"></div>

Kubernetes 1.20부터는 dockershim이 폐기되고 있습니다. Mirantis에서 제공하는 cri-dockerd라는 클러스터용 오픈 소스 CRI를 사용할 수 있습니다. cri-dockerd를 설치하고 서비스를 설정하려면 다음 명령을 실행하세요:

```js
wget https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.4/cri-dockerd-0.3.4.arm64.tgz
tar -xvzf cri-dockerd-0.3.4.arm64.tgz
sudo mv cri-dockerd/cri-dockerd /usr/bin/cri-dockerd
sudo chmod +x /usr/bin/cri-dockerd
wget https://raw.githubusercontent.com/Mirantis/cri-dockerd/master/packaging/systemd/cri-docker.service
wget https://raw.githubusercontent.com/Mirantis/cri-dockerd/master/packaging/systemd/cri-docker.socket
sudo mv cri-docker.service /etc/systemd/system/
sudo mv cri-docker.socket /etc/systemd/system/
sudo systemctl enable cri-docker.service
sudo systemctl enable cri-docker.socket
sudo systemctl start cri-docker.service
sudo systemctl start cri-docker.socket
```

Kubernetes 스케줄러를 위해 노드에서 swap을 비활성화하는 것이 권장됩니다.

```js
sudo apt-get update && sudo apt-get install dphys-swapfile && sudo dphys-swapfile swapoff && sudo dphys-swapfile uninstall && sudo systemctl disable dphys-swapfile
```

<div class="content-ad"></div>

만약 cri-dockerd 설정에 문제가 발생하면, 이 안내서를 확인해보세요. 처음에 작성했을 때와 달라진 사항이 있을 수 있어요.

마지막으로, Kubernetes를 설치해봅시다!

```js
sudo apt install -y kubelet=1.26.6-00 kubeadm=1.26.6-00 kubectl=1.26.6-00
sudo apt-mark hold kubelet kubeadm kubectl
```

이 가이드에서는 모든 것이 1.26.6에서 작동하는지 테스트했어요. 1.24 이전 버전은 정상적으로 작동하지 않을 거예요. 이러한 패키지를 업데이트되지 않도록 표시할 거에요.

<div class="content-ad"></div>

차선으로, 랜처 랩스에서 만든 k3s는 가벼운 옵션으로 좋은 선택일 것입니다. 그 중 일부 장점은 작은 실행 파일 크기, 매우 낮은 자원 요구 사항 및 ARM용으로 최적화되어 있다는 것입니다. 이 가이드에서는 이를 테스트해보지 않았지만, 이후에 비슷한 설정이 될 것으로 생각합니다.

이제 클러스터를 초기화할 시간입니다. 이를 위해 InitConfiguration 및 ClusterConfiguration 설정이 포함된 파일을 만들겠습니다.

```js
apiVersion: kubeadm.k8s.io/v1beta3
bootstrapTokens:
- groups:
  - system:bootstrappers:kubeadm:default-node-token
  token: {token}
  usages:
  - signing
  - authentication
kind: InitConfiguration
localAPIEndpoint:
  advertiseAddress: 10.0.0.100
  bindPort: 6443
nodeRegistration:
  criSocket: unix:///var/run/cri-dockerd.sock
  imagePullPolicy: IfNotPresent
  name: node-0
---
apiVersion: kubeadm.k8s.io/v1beta3
kind: ClusterConfiguration
networking:
  podSubnet: "10.244.0.0/16" # --pod-network-cidr
```

이 파일에는 마스터 노드의 설정이 포함되어 있습니다. criSocket이 cri-dockerd를 사용하고, 나중에 네트워크 CIDR을 설정해두었음을 주목하십시오.

<div class="content-ad"></div>

이 노드에서 제어 평면을 초기화하려면 다음을 실행하세요.

```js
sudo kubeadm init --config kubeadm-config.yaml
```

이 명령은 새 노드를 클러스터에 추가하는 설정 및 kube-config를 설정하는 방법을 보여줍니다.

명령에서 지시하는 방법에 따라 kube-config를 설정하고, 워크스테이션에 kube-config와 가입 명령을 복사하고 저장하세요. 나중에 필요할 것이니까요!

<div class="content-ad"></div>

# 클러스터 네트워킹

이제 클러스터에서 네트워킹을 설정해야 합니다. Pod들이 노드 간에 서로 통신할 수 있도록 하려면 네트워크 플러그인 (CNI 또는 컨테이너 네트워크 인터페이스로도 불림)이 필요합니다.

네트워크 플러그인은 IP 주소 할당, DNS 해결 및 네트워크 격리와 같은 기능을 Pod에 제공합니다.

우리는 이를 위해 Flannel을 사용할 것입니다.

<div class="content-ad"></div>

마스터 노드에서 다음을 실행해 주세요.

```js
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

그게 다에요! 이제 마스터 노드가 완료되었으니, 클러스터에 새 노드를 추가하기 시작할 수 있어요. 이전에 출력된 조인 명령을 기억하고 있나요? 이제 그것이 필요할 거에요.

# 클러스터에 새로운 노드 추가하기

<div class="content-ad"></div>

클러스터에 새 노드를 추가하는 것은 꽤 간단합니다. 많은 노드를 추가하는 경우에는 tmux와 같은 도구를 사용하여 세션 명령을 다중화하는 것이 좋습니다.

"첫 번째 부팅 및 초기 설정"을 완료하고 "도커 및 쿠버네티스 초기 설정"을 진행하세요. 서로 다른 Kubernetes 구성 요소를 설치하는 단계 이후에 작업을 중지하세요. 이 시점에서 이전에 실행한 kubeadm join 명령을 실행해야 합니다. cri-socket 및 node-name 옵션을 포함하여 실행해 주세요. 

```js
sudo kubeadm join 10.0.0.100:6443 --token {token} --discovery-token-ca-cert-hash {hash} --cri-socket unix:///var/run/cri-dockerd.sock --node-name {name}
```

이제 마스터 노드에서 클러스터를 모니터링하고 모든 노드가 클러스터에 가입하는지 확인하세요.

<div class="content-ad"></div>

```js
> kubectl get nodes를 watch합니다.
이제 귀하의 클러스터가 사용할 준비가 되었습니다! 그러나 SSH를 통해가 아닌 워크스테이션에서 액세스하고 싶을 것입니다. 컴퓨터에서 이전에 설정한 kube-config를 설정할 수 있습니다.

기본 kube-config는 관리자 권한을 부여하며 다른 사람과 공유해서는 안됩니다.

먼저 프로필에 구성을 내보냅니다.
```

<div class="content-ad"></div>

```js
export KUBECONFIG=~/.kube/config
```

컨텍스트 설정:

```js
kubectl config use-context kubernetes-admin@kubernetes
```

이제 원격으로 클러스터에 액세스할 수 있어야 합니다.

<div class="content-ad"></div>

```js
> kubectl cluster-info
쿠버네티스 제어 평면이 https://10.0.0.100:6443 에서 실행 중입니다.
CoreDNS이 https://10.0.0.100:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy 에서 실행 중입니다.

더 많은 디버깅 및 진단을 위해 'kubectl cluster-info dump'를 사용하세요.
```

# 도구 설정

이제 밴자이라 클러스터를 일반에서 멋지게 업그레이드해 봅시다. 새 응용 프로그램을 쉽게 배포하고 클러스터를 모니터링할 수 있는 몇 가지 널리 사용되는 도구를 설정해 보겠습니다. 여기서 ArgoCD, Prometheus 및 Grafana 설치 방법을 안내하겠습니다! 이 세 가지 오픈소스 프로젝트가 우리의 클러스터를 다음 수준으로 끌어올립니다.

계속하기 전에, 이러한 도구들에 대한 모든 설정 변경 사항을 추적하기 위한 원격 git 저장소를 만들어 보시기를 권장합니다. 특히 ArgoCD를 사용할 때, 각 도구나 추가 응용 프로그램을 배포할 때마다 거기를 통해 추가합니다.```

<div class="content-ad"></div>

# ArgoCD

각 도구에 대해 Helm을 리소스 템플릿팅 도구로 사용할 것입니다. 최신 버전(또는 적어도 Helm v3)을 설치하고 ArgoCD 저장소를 추가해 봅시다.

```js
helm repo add argo https://argoproj.github.io/argo-helm
```

Values 파일을 생성하세요.

<div class="content-ad"></div>

```yaml
server:
  serviceType: NodePort
  httpNodePort: 30080
  httpsNodePort: 30443
```

이 파일은 차트의 설정 중 하나를 재정의하는 데 사용할 수 있습니다. 이 경우에는 서비스를 ClusterIP 대신 NodePort로 실행하도록 변경하고 있습니다. 이렇게 하면 클러스터에서 지정한 포트를 외부에서 엑세스할 수 있도록 하여 리버스 프록시를 사용하지 않고도 개인 네트워크에서 해당 서비스에 액세스할 수 있습니다.

서비스를 설치하십시오.

```js
helm install argocd -n argocd -f values.yaml argo/argocd
```

<div class="content-ad"></div>

그럼 관리자 사용자의 기본 암호를 가져와야 합니다.

```js
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

OpenWrt를 사용하고 있기 때문에 클러스터에 호스트 이름 항목을 설정하고 https://cluster.home:30443에서 로그인 페이지에 액세스할 수 있습니다.

![그림](/assets/img/2024-05-18-AGuidetoBuildingaKubernetesClusterwithRaspberryPis_2.png)

<div class="content-ad"></div>

ArgoCD에 로그인하고, 곧 돌아올게요.

![image](/assets/img/2024-05-18-AGuidetoBuildingaKubernetesClusterwithRaspberryPis_3.png)

# Prometheus

우리는 클러스터에 대한 정보 수집을 위해 타임 시리즈 메트릭 서버로 Prometheus를 사용할 거에요.

<div class="content-ad"></div>

설치하기 전에 Prometheus가 쿼리 데이터를 저장할 지속적인 볼륨을 설정해야 합니다. 집 클러스터에서는 예비 USB 드라이브를 사용하기로 결정했지만 원하는 것을 연결하여 사용할 수 있습니다.

마스터 노드에서 볼륨을 설정한 단계는 다음과 같습니다. 우리의 볼륨을 위한 경로를 만들고 실수를 막기 위해 변경 사항을 반영해야 할 fstab의 백업을 만듭니다.

```js
sudo mkdir /mnt/usb
sudo cp /etc/fstab /etc/fstab.bak
```

장치를 연결한 다음 fstab을 변경 내용과 함께 수정합니다.

<div class="content-ad"></div>

```md
/dev/sda1 /mnt/usb vfat defaults,uid=youruid,gid=yourgid,dmask=002,fmask=113 0 0
```

이제 우리 노드 사용자의 사용자 및 그룹 설정으로 장치를 마운트합니다.

```md
sudo mount -o uid=youruid,gid=yourgid,dmask=002,fmask=113 /dev/sdX1 /mnt/usb
```

이제 우리는 PersistentVolume과 PersistentVolumeChain을 가진 Kubernetes 자원을 생성하려고 합니다.
 

<div class="content-ad"></div>

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: prometheus-usb-pv
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: {device의 크기}Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: "/mnt/usb"
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: prometheus-usb-pvc
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: {device의 크기}Gi
```

만약 git 리포지토리를 사용 중이라면, 이 파일들을 template 디렉토리 안에 새로운 Helm Chart에 위치시켜주세요. 다음 단계를 따라 계속 진행해봐요.

이제 프로메테우스와 함께 차트를 설정해봅시다.

```bash
helm create prometheus
```

<div class="content-ad"></div>

Chart.yaml에 Prometheus subchart를 종속성으로 추가해주세요.

```yaml
dependencies:
  - name: prometheus
    version: 22.7.0
    repository: https://prometheus-community.github.io/helm-charts
```

이제 새 PV 및 PVC를 사용하도록 구성을 설정하고, 일부 권한을 수정하고 서버를 마스터 노드에만 배포하도록 확인할 수 있습니다.

```yaml
prometheus:
  alertmanager:
    enabled: false
  prometheus-pushgateway:
    enabled: false
  configmapReload:
    prometheus:
      enabled: false
  server:
    nodeSelector:
      kubernetes.io/hostname: {master node}
    securityContext:
      runAsUser: {userid}
      runAsNonRoot: true
      runAsGroup: {groupid}
      fsGroup: {fsid}
    persistentVolume:
      enabled: true
      existingClaim: "prometheus-usb-pvc"
      volumeName: "prometheus-usb-pv"
```

<div class="content-ad"></div>

일부 추가 서비스를 비활성화합니다. 예를 들어 alertmanager, pushgateway, 그리고 configmapreload가 이에 해당합니다. 필요한 경우 다른 시간에 이를 활성화할 수 있습니다. 비정상적으로 행동하는 경우 알림을 받을 수 있는 유용한 도구인 Alert Manager입니다.

이제 ArgoCD로 돌아가 "새 앱"을 만들어보겠습니다. Prometheus라는 이름의 앱을 만들고 깃 레포지토리를 소스로 추가하고 경로를 선택하세요. Grafana도 나중에 이 작업을 해야하므로 서로 다른 경로에 유지하세요.


![Image](/assets/img/2024-05-18-AGuidetoBuildingaKubernetesClusterwithRaspberryPis_4.png)


생성한 사용자 정의 설정을 설정할 값 파일을 선택한 다음 앱을 생성하세요. 수동으로 동기화하도록 지정한 경우 동기화가 필요할 때 이를 수행해야 합니다. 이것은 업그레이드할 때 사용하거나 수동으로 릴리스하고자 할 때 유용합니다. 그 외에는 홈 프로젝트에 가장 적합한 CD용 자동 동기화 방법이 유용합니다.

<div class="content-ad"></div>

이미지 태그를 Markdown 형식으로 변경해주세요.

![Grafana](https://miro.medium.com/v2/resize:fit:1400/1*Ice0ZJGARkN6BdzAl1nGDQ.gif)

마찬가지로 Prometheus와 비슷하게, git 레포지토리에서 새로운 Helm 차트를 생성하는 것부터 시작해보세요.

```js
helm create grafana
```

<div class="content-ad"></div>

헬름 리포지토리를 추가해주세요.

```js
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

리포지토리를 통해 차트를 업데이트하세요.

```js
dependencies:
  - name: grafana
    version: 6.57.4
    repository: https://grafana.github.io/helm-charts
```

<div class="content-ad"></div>

values.yaml 파일을 추가해주세요.

```js
grafana:
  service:
    enabled: true
    type: NodePort
    nodePort: 30180
```

그런 다음 이전과 같이 ArgoCD를 통해 Grafana를 추가해주세요. 동기화를 진행하고 이제 두 개가 모두 실행 중이어야 합니다.

<img src="/assets/img/2024-05-18-AGuidetoBuildingaKubernetesClusterwithRaspberryPis_5.png" />


<div class="content-ad"></div>

Grafana를 사용하려면 관리자 비밀번호를 먼저 얻어야 합니다.

```js
kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

출력에서 나온 관리자 사용자 이름과 비밀번호로 로그인해주세요.

![이미지](/assets/img/2024-05-18-AGuidetoBuildingaKubernetesClusterwithRaspberryPis_6.png)

<div class="content-ad"></div>

이제 Prometheus 데이터 원본을 추가해 보겠습니다. Prometheus 서비스 URL은 모니터링을 설정한 네임스페이스인 클러스터에서 http://prometheus-server.monitoring.svc.cluster.local로 접근할 수 있습니다. "Administration" → "Data sources" → "Add new data source" 아래로 이동한 다음 URL을 추가하고 "Save & Test"를 클릭하여 확인할 수 있습니다.

![image](https://miro.medium.com/v2/resize:fit:1400/1*VphOqjzSh30E4dKrnf2Q2A.gif)

만약 우리 클러스터의 상태를 간단히 확인하고 싶다면 Grafana Labs에서 제공하는 대시보드를 사용할 수 있습니다. 이를 통해 우리 클러스터에서 사용되는 리소스에 대한 간단한 뷰를 확인할 수 있을 것입니다.

![image](https://miro.medium.com/v2/resize:fit:1400/1*AIw7esBCtaz5koK_uJTU-w.gif)

<div class="content-ad"></div>

# 다음 단계

## 사용자 정의 Docker 이미지

당신의 클러스터를 운영하는 중요한 단계로, 공개 Docker.io 레지스트리에 없는 컨테이너나 사용자 정의 컨테이너를 배포할 수 있게 됩니다. 클러스터에 많은 컨테이너를 배포할 계획이라면, 무료 티어 Docker Hub의 제한을 피하기 위해 개인 컨테이너 레지스트리를 설정하는 것을 권장합니다. 이는 GCP의 Artifacts Repository와 같은 클라우드 공급업체나 Harbor와 같은 오픈 소스 docker 저장소로 구현할 수 있습니다.

## 클러스터 자동화

<div class="content-ad"></div>

이 안내서는 교육 목적이나 소규모 개인 클러스터를 관리할 때 이상적인 Kubernetes 클러스터 설정에 대한 수동 방법을 제공합니다. 그러나 프로덕션 클러스터를 배포하거나 이 안내서의 범위를 벗어나는 작업을 수행할 경우, Ansible과 같은 자동화 도구를 활용하는 것을 권장합니다. 이렇게하면 더 효율적이고 확장 가능하며 관리하기 쉬운 배포가 가능합니다.

# 결론

Kubernetes 클러스터를 설정하는 것은 쉽지 않을 수 있지만 한 번 완료되면 일반적인 독립형 서버를 뛰어넘는 확장 가능한 환경을 제공하는 장점이 있습니다.

Raspberry Pi는 비용이 저렴하고 전력 소비가 낮은 옵션이지만, 더 큰 응용 프로그램에 대한 확장성이 여전히 제한되어 있습니다. Kubernetes 클러스터의 장점은 동일한 하드웨어만 실행하는 것에 제한받지 않는다는 것입니다. 새로운 노드를 추가함으로써 다양한 하드웨어를 혼합하여 필요에 맞게 Raspberry Pi나 서버와 같은 다양한 하드웨어를 조합할 수 있습니다!

<div class="content-ad"></div>

그것이 좋은 시작점이 되었기를 바랍니다. 읽어 주셔서 감사합니다!