---
title: "MLOps 작업을 위한 GPU와 함께 Kubernetes 사용 방법"
description: ""
coverImage: "/assets/img/2024-06-23-KuberneteswithGPUforMLOpsWorkloads_0.png"
date: 2024-06-23 00:51
ogImage: 
  url: /assets/img/2024-06-23-KuberneteswithGPUforMLOpsWorkloads_0.png
tag: Tech
originalTitle: "Kubernetes with GPU for MLOps Workloads"
link: "https://medium.com/@sivanaikk0903/kubernetes-with-gpu-for-mlops-workloads-c684f8c8d41c"
---


이 기사에서는 GPU가 쿠버네티스와 통합되어 기계 학습 워크로드를 실행하는 방법에 대해 논의하고자 합니다.

저는 주변에 있는 NVIDIA GeForce RTX 3050을 가지고 아이디어를 얻었어요 💡... ` Kubernetes

빠르게 쿠버네티스와 GPU를 통합하여 딥 러닝 훈련 배포를 실행해봅시다!

0. Docker가 설치되어 있는지 확인하세요. 아직 설치되지 않았다면 여기에서 Docker를 다운로드하세요.

<div class="content-ad"></div>

# NVIDIA Container Toolkit for Docker

- NVIDIA 드라이버를 업데이트해주세요

```js
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-5
```

- 공식 웹사이트에서 해당 OS에 맞는 NVIDIA 컨테이너 툴킷을 설치해주세요.
- 제가 WSL을 사용하고 있기 때문에 apt 명령어를 통해 설치하겠습니다.

<div class="content-ad"></div>

```js
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

- 저장소에서 패키지 목록을 업데이트합니다:

```js
sudo apt-get update
```

- NVIDIA Container Toolkit 패키지를 설치합니다:


<div class="content-ad"></div>

```js
sudo apt-get install -y nvidia-container-toolkit
```

![KuberneteswithGPUforMLOpsWorkloads](/assets/img/2024-06-23-KuberneteswithGPUforMLOpsWorkloads_0.png)

- 도커를 실행할 수 있도록 런타임 구성하기

```js
sudo nvidia-ctk runtime configure --runtime=docker
```

<div class="content-ad"></div>

아래는 테이블 태그를 Markdown 형식으로 변경한 코드입니다.


<img src="/assets/img/2024-06-23-KuberneteswithGPUforMLOpsWorkloads_1.png" />

- restart docker

```js
sudo systemctl restart docker
```

- Docker Desktop


<div class="content-ad"></div>

만일 Docker Desktop을 사용 중이라면, 설정을 다르게 구성하고 daemon.json을 수정해야 합니다.

- 왼쪽 상단의 설정 ⚙️ 로 이동하여 Docker Engine으로 이동한 다음 다음 구성을 추가하십시오. (,를 잊지 말고 json 구문을 확인해주세요 😅)

```js
"runtimes": {
  "nvidia": {
    "args": [],
    "path": "nvidia-container-runtime"
  }
}
```

![이미지](/assets/img/2024-06-23-KuberneteswithGPUforMLOpsWorkloads_2.png)

<div class="content-ad"></div>

2. Apply 및 다시 시작을 클릭하고 GPU 확인

다음 명령어로 도커가 런타임으로 GPU를 사용하는지 확인하세요.

```shell
sudo docker run --rm --runtime=nvidia --gpus all ubuntu nvidia-smi
```

![이미지](/assets/img/2024-06-23-KuberneteswithGPUforMLOpsWorkloads_3.png)

<div class="content-ad"></div>

# Minikube

로컬에서 Kubernetes 클러스터를 프로비저닝하는 데 Minikube를 사용할 것입니다.

- [여기](링크)에서 운영 체제에 맞게 Minikube를 다운로드하세요.
- Minikube 이진 파일을 다운로드한 후 다음 명령어로 Minikube를 시작하세요. Minikube를 시작하기 전에 Docker가 실행 중인지 확인하세요.

```js
minikube start --gpus all --driver=docker --addons=ingress
```

<div class="content-ad"></div>

Minikube의 nvidia-gpu-device-plugin 애드온은 Kubernetes 클러스터에서 GPU 지원을 활성화하는 데 설계되었습니다. 이 애드온을 사용하면 Kubernetes가 GPU가 필요한 작업로드를 인식하고 예약하여 클러스터에서 GPU 리소스를 사용할 수 있게 됩니다.

NVIDIA GPU 장치 플러그인이란 무엇인가요?

NVIDIA GPU 장치 플러그인은 Kubernetes 클러스터에서 NVIDIA GPU를 사용할 수 있게 하는 Kubernetes 장치 플러그인입니다. 이 플러그인을 사용하면 Kubernetes가 GPU 리소스를 컨테이너에 할당하고 예약하여 응용 프로그램이 GPU 가속을 사용할 수 있도록 합니다.

주요 기능

<div class="content-ad"></div>

- GPU 발견:

    - 노드에서 NVIDIA GPU를 자동으로 발견하여 Kubernetes에서 스케줄 가능한 리소스로 이용할 수 있습니다.

- GPU 리소스 관리:

    - GPU 리소스를 컨테이너에 할당하는 작업을 관리합니다. Pod에 요청된 GPU 수를 확인하고 GPU 리소스를 스케줄링하는 복잡성을 처리합니다.

<div class="content-ad"></div>

3. 격리:

- GPU 리소스의 격리를 제공하여 GPU 워크로드가 서로 간섭하지 않도록 보장합니다.

4. 메트릭 및 모니터링:

- GPU 활용에 관한 메트릭을 노출하여 GPU 워크로드의 모니터링과 스케일링에 활용할 수 있습니다.

<div class="content-ad"></div>

# 쿠버네티스에서 GPU 가용성 확인하기

GPU 노드가 사용 가능한지 노드 세부정보를 확인하여 확인하세요

```js
kubectl get nodes -o "custom-columns=NAME:.metadata.name,STATUS:.status.conditions[-1].type,CAPACITY:.status.capacity"
```

- 작업으로 확인하고 매니페스트 파일을 적용하세요

<div class="content-ad"></div>

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: gpu-job
spec:
  template:
    spec:
      containers:
      - name: gpu-container
        image: nvidia/cuda:12.5.0-base-ubuntu22.04
        resources:
          limits:
            nvidia.com/gpu: 1 # Request 1 GPU
        command: ["nvidia-smi"]
      restartPolicy: Never
```

```bash
kubectl apply -f gpu-verify.yaml
```

![Kubernetes with GPU for MLOps Workloads](/assets/img/2024-06-23-KuberneteswithGPUforMLOpsWorkloads_4.png)

- 파드 가져오기


<div class="content-ad"></div>

```js
kubectl get po 
```

![이미지](/assets/img/2024-06-23-KuberneteswithGPUforMLOpsWorkloads_5.png)

- GPU 로그 확인

```js
kubectl logs <pod>
```

<div class="content-ad"></div>


![이미지](/assets/img/2024-06-23-KuberneteswithGPUforMLOpsWorkloads_6.png)

그리고 시작합니다..!

GPU를 보실 수 있습니다. 업데이트를 기다려주세요. 몇 가지 워크로드를 실행하고 여기에 공유할 예정입니다.

이제 쿠버네티스에서 머신러닝 모델을 훈련할 수 있습니다.


<div class="content-ad"></div>

읽어 주셔서 감사합니다!

[LinkedIn 프로필](https://www.linkedin.com/in/sivanaik/)

[x.com에서의 프로필](https://x.com/sivanaikk)