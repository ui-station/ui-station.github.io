---
title: "M 시리즈 맥에서 Multipass로 로컬 클러스터 쉽게 만들기"
description: ""
coverImage: "/assets/img/2024-06-23-LocalClusterMadeEasywithMultipassonMacMchips_0.png"
date: 2024-06-23 22:57
ogImage:
  url: /assets/img/2024-06-23-LocalClusterMadeEasywithMultipassonMacMchips_0.png
tag: Tech
originalTitle: "Local Cluster Made Easy with Multipass on Mac M chips"
link: "https://medium.com/@ibrahimmohelsayed/local-cluster-made-easy-with-mutipass-on-mac-m-chips-82af3d34b9b7"
---

<img src="/assets/img/2024-06-23-LocalClusterMadeEasywithMultipassonMacMchips_0.png" />

이 기사는 여러분의 기계에 k3s Kubernetes 환경을 설정하여 여러분의 POC를 테스트하고 CNCF 랜드스케이프의 더 많은 도구들을 탐색하는 방법을 보여줍니다.

여러분의 Mac에서 K3S/K8S를 직접 실행할 수 없기 때문에 여러분은 Mac 위에 Linux 레이어를 설정해야 합니다. Mac M1에서 Linux VM을 설정하는 쉬운 방법은 Multipass를 사용하는 것입니다.

왜 Multipass를 사용해야 하는지요?

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

한 번의 명령어로 즉시 Ubuntu VM을 얻을 수 있어요.

먼저, Multipass를 설치해야 해요.

```js
brew install --cask multipass
```

설치되면 메모리 및 디스크 공간을 지정하여 새 VM을 생성해보세요.

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
multipass launch --name k3s --memory 4G --disk 40G
```

우리는 심지어 VM에서 Mac 디렉터리를 마운트할 수도 있어요.

```js
mkdir ~/test/k8s
multipass mount ~/test/k8s k3s:~/k8s
```

호스트 디렉토리에서 변경 사항을 만들고 VM 내부의 클러스터에 변경 사항을 적용하려고 할 때 유용할 거예요.

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

VM 내부에서 설치 스크립트를 실행하여 k3s를 설치할 수 있어요.

```js
multipass shell k3s

ubuntu@k3s:~$ curl -sfL https://get.k3s.io | K3S_KUBECONFIG_MODE="644" sh -
```

VM이 시작되면 VM 세부 정보를 확인할 수 있어요.

```js
multipass info k3s

Name:           k3s
State:          Running
Snapshots:      0
IPv4:           192.168.64.7
                10.42.0.0
                10.42.0.1
Release:        Ubuntu 24.04 LTS
Image hash:     8263b4713896 (Ubuntu 24.04 LTS)
CPU(s):         1
Load:           0.29 0.22 0.13
Disk usage:     2.8GiB out of 38.7GiB
Memory usage:   814.2MiB out of 3.8GiB
Mounts:         /Users/ibrahimmohamed/test/k8s => ~/k8s
                    UID map: 501:default
                    GID map: 20:default
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

```shell
K3S_IP=$(multipass info k3s | grep IPv4 | awk '{print $2}')

echo $K3S_IP

192.168.64.7# kubeconfig 다운로드

multipass exec k3s cp /etc/rancher/k3s/k3s.yaml /home/ubuntu/k8s/

cd ~/test/k8s

sed -i '' "s/127.0.0.1/${K3S_IP}/" k3s.yaml

export KUBECONFIG=${PWD}/k3s.yaml
```

이제 kubeconfig이 있습니다:

머신에 kubectl을 설치하세요:

```shell
brew install kubectl
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

```sh
kubectl get nodes -o wide

NAME   STATUS   ROLES                  AGE   VERSION        INTERNAL-IP    EXTERNAL-IP   OS-IMAGE           KERNEL-VERSION     CONTAINER-RUNTIME
k3s    Ready    control-plane,master   12m   v1.29.5+k3s1   192.168.64.7   <none>        Ubuntu 24.04 LTS   6.8.0-35-generic   containerd://1.7.15-k3s1


kubectl get pods -A

NAMESPACE     NAME                                      READY   STATUS      RESTARTS   AGE
kube-system   coredns-6799fbcd5-dc8nd                   1/1     Running     0          41m
kube-system   local-path-provisioner-6c86858495-9q524   1/1     Running     0          41m
kube-system   helm-install-traefik-crd-p4xhh            0/1     Completed   0          41m
kube-system   metrics-server-54fd9b65b-vmhvc            1/1     Running     0          41m
kube-system   helm-install-traefik-5snzg                0/1     Completed   1          41m
kube-system   svclb-traefik-ae8c3cf6-hntgn              2/2     Running     0          40m
kube-system   traefik-7d5f6474df-48vsc                  1/1     Running     0          40m
```

Now let's view it through Lens

![Lens](/assets/img/2024-06-23-LocalClusterMadeEasywithMultipassonMacMchips_1.png)

Now you are ready to run any POC on your local machine.

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

k3s 실험이 끝나면 VM을 삭제할 수 있습니다.

```js
multipass delete k3s
multipass purge
```

화이팅!
