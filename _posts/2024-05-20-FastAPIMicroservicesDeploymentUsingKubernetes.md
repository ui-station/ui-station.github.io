---
title: "파이썬 FastAPI를 사용한 Kubernetes로의 Microservices 배포 방법"
description: ""
coverImage: "/assets/img/2024-05-20-FastAPIMicroservicesDeploymentUsingKubernetes_0.png"
date: 2024-05-20 17:11
ogImage: 
  url: /assets/img/2024-05-20-FastAPIMicroservicesDeploymentUsingKubernetes_0.png
tag: Tech
originalTitle: "FastAPI Microservices Deployment Using Kubernetes"
link: "https://medium.com/@sumangaire52/fastapi-microservices-deployment-using-kubernetes-35e6b8c4b52d"
---


![FastAPI 및 Kubernetes를 사용한 미니큐브에 두 마이크로서비스 배포하기](/assets/img/2024-05-20-FastAPIMicroservicesDeploymentUsingKubernetes_0.png)

이 기사에서는 FastAPI로 2개의 마이크로서비스를 만들고 이를 미니큐브에 배포할 것입니다. 우리는 쿠버네티스 인그레스를 사용하여 요청을 각각의 마이크로서비스로 라우팅할 것입니다.

먼저, 이 기사에서 사용할 쿠버네티스 기능을 살펴보겠습니다.

- **Kubernetes Deployment**: 쿠버네티스 배포는 애플리케이션 업데이트와 스케일링의 자동화를 담당합니다. 이는 애플리케이션의 원하는 상태를 정의하며, 레플리카 수, 사용할 컨테이너 이미지 및 업데이트 전략을 포함합니다. 배포 컨트롤러는 필요에 따라 파드를 작성하고 업데이트하여 애플리케이션의 실제 상태가 원하는 상태와 일치하도록 합니다. 배포는 롤링 업데이트, 롤백 기능 및 셀프 힐링을 지원하여 변경 사항 중에 애플리케이션 가용성과 안정성을 유지하기 쉽게 합니다. 이 추상화는 쿠버네티스 클러스터에서 스테이트리스 애플리케이션을 배포, 확장 및 관리를 간소화합니다. 자세한 정보는 [링크](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)를 참조하세요.

<div class="content-ad"></div>

**Kubernetes Service:** 쿠버네티스 서비스는 논리적인 포드 집합과 이에 접근하기 위한 정책을 정의하는 추상화입니다. 일반적으로 안정적인 IP 주소와 DNS 이름을 통해 접근합니다. 서비스를 통해 응용 프로그램의 다른 부분 간에 통신할 수 있으며 포드의 IP 주소를 알 필요가 없어집니다. 왜냐하면 그 주소는 변경될 수 있기 때문입니다.

[자세히 알아보기](https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/)

**Kubernetes Ingress:** 쿠버네티스 인그레스는 쿠버네티스 클러스터 내의 서비스에 대한 외부 액세스를 관리합니다. 저희 어플리케이션에서는 해당 서비스(예: 마이크로서비스 1 또는 마이크로서비스 2)로 트래픽을 라우팅하는 데 사용할 것입니다. 호스트 이름 및 경로에 따라 트래픽을 특정 서비스로 라우팅하는 규칙을 정의합니다. NGINX 또는 Traefik과 같은 인그레스 컨트롤러는 이러한 규칙을 실행하여 중앙 집중형 관리, 로드 밸런싱, SSL 종료 및 경로 기반 라우팅을 제공합니다. 인그레스 리소스를 효과적으로 사용하려면 인그레스 컨트롤러를 배포해야 합니다.

[자세히 알아보기](https://kubernetes.io/docs/concepts/services-networking/ingress/#what-is-ingress)

<div class="content-ad"></div>

# 마이크로서비스 1

마이크로서비스 1은 매우 간단하게 될 것입니다. "You requested microservice 1"을 반환하는 하나의 엔드포인트만을 가질 것입니다.

microservice_1이라는 폴더를 만들고 main.py라는 파일을 추가하세요.

```python
# main.py (마이크로서비스 1)

from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def root():
    return {"message": "You requested microservice 1"}
```  

<div class="content-ad"></div>

요구사항.txt 파일을 추가하세요

```js
fastapi==0.111.0
```

Dockerfile을 생성하세요

```js
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt requirements.txt

RUN pip install -r requirements.txt

COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80"]
```

<div class="content-ad"></div>

도커 이미지를 빌드하고 도커허브에 푸시하세요.

이미지를 username/image_name:version 형식으로 태그해야 합니다. 그렇지 않을 경우 쿠버네티스가 이미지를 인식하지 못할 수 있습니다. 이 명령어는 마이크로서비스 1의 Dockerfile이 있는 디렉토리에서 실행되어야 합니다.

```js
$ docker build . -t sumangaire96/microservice1:v1
$ docker push sumangaire96/microservice1:v1
```

이와 같은 메시지가 나타날 것입니다. 만약 나타나지 않는다면, 도커에 로그인되어 있는지 확인하세요.

<div class="content-ad"></div>

![이미지](/assets/img/2024-05-20-FastAPIMicroservicesDeploymentUsingKubernetes_1.png)

# 마이크로서비스 2

마이크로서비스 2도 엔드포인트가 하나만 있어서 "You requested microservice 2"를 반환할 것입니다.

microservice_1이라는 폴더를 만들고 main.py라는 파일을 추가하세요.

<div class="content-ad"></div>


# main.py (microservice 2)

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def root():
    return {"message": "You requested microservice 2"}


Add requirements.txt


fastapi==0.111.0


Add Dockerfile


<div class="content-ad"></div>

```Dockerfile
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt requirements.txt

RUN pip install -r requirements.txt

COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80"]
```

도커 이미지를 빌드하고 도커허브에 푸시하세요.

```bash
$ docker build . -t sumangaire96/microservice2:v1
$ docker push sumangaire96/microservice2:v1
```

# Minikube


<div class="content-ad"></div>

미니큐브가 설치되어 있는지 확인하세요. 설치되어 있지 않다면 다음 링크를 따르세요: https://minikube.sigs.k8s.io/docs/start/. 터미널에서 미니큐브 클러스터와 상호 작용하기 위해 kubectl이 설치되어 있는지 확인하세요. 설치되어 있지 않다면 다음 링크를 따르세요: https://kubernetes.io/docs/tasks/tools/.

쿠버네티스 클러스터를 생성하세요.

```js
$ minikube start
```

미니큐브에서 인그레스를 활성화하세요.

<div class="content-ad"></div>

```bash
$ minikube addons enable ingress
```

**1. Create kubernetes manifest file for microservice 1.**

```yaml
# kubernetes/microservice_1.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: microservice1-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: microservice1
  template:
    metadata:
      labels:
        app: microservice1
    spec:
      containers:
      - name: microservice1
        image: sumangaire96/microservice1:v1
        ports:
        - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: microservice1-service
spec:
  selector:
    app: microservice1
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

**2. Create kubernetes manifest file for microservice 2.**

<div class="content-ad"></div>

```yaml
# kubernetes/microservice_2.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: microservice2-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: microservice2
  template:
    metadata:
      labels:
        app: microservice2
    spec:
      containers:
      - name: microservice2
        image: sumangaire96/microservice2:v1
        ports:
        - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: microservice2-service
spec:
  selector:
    app: microservice2
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

쿠버네티스 인그레스 매니페스트 파일을 생성합니다.

```yaml
# kubernetes/ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: microservice1.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: microservice1-service
            port:
              number: 80
  - host: microservice2.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: microservice2-service
            port:
              number: 80
```

매니페스트를 적용하세요.


<div class="content-ad"></div>

```md
$ kubectl apply -f microservice_1.yaml
$ kubectl apply -f microservice_2.yaml
$ kubectl apply -f ingress.yaml
```

호스트 이름을 Minikube IP에 매핑하기 위해 호스트 파일을 업데이트하세요.

```md
$ echo "$(minikube ip) microservice1.local" | sudo tee -a /etc/hosts
$ echo "$(minikube ip) microservice2.local" | sudo tee -a /etc/hosts
```

이제 microservice1.local 및 microservice2.local로 이동하면 다음과 같은 출력이 나타납니다:
  

<div class="content-ad"></div>

아래는 Markdown 형식으로 표로 변환한 내용입니다.


| 이미지 | 
|---|
| ![FastAPI와 Kubernetes를 사용한 2024-05-20 날짜의 마이크로서비스 배포 이미지](/assets/img/2024-05-20-FastAPIMicroservicesDeploymentUsingKubernetes_2.png) |
| ![FastAPI와 Kubernetes를 사용한 2024-05-20 날짜의 또 다른 마이크로서비스 배포 이미지](/assets/img/2024-05-20-FastAPIMicroservicesDeploymentUsingKubernetes_3.png) |

축하합니다! FastAPI로 2개의 마이크로서비스를 만들고 Kubernetes를 사용하여 성공적으로 배포했습니다. 앞으로 나올 문서를 기대해 주세요.
