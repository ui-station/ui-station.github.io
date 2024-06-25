---
title: "웹 애플리케이션을 위한 변경 불가능한 인프라 구축하기 단계별 가이드"
description: ""
coverImage: "/assets/img/2024-05-23-BuildinganImmutableInfrastructureforYourWebApplicationAStep-by-StepGuide_0.png"
date: 2024-05-23 14:32
ogImage:
  url: /assets/img/2024-05-23-BuildinganImmutableInfrastructureforYourWebApplicationAStep-by-StepGuide_0.png
tag: Tech
originalTitle: "Building an Immutable Infrastructure for Your Web Application: A Step-by-Step Guide"
link: "https://medium.com/@kalimitalha8/building-an-immutable-infrastructure-for-your-web-application-a-step-by-step-guide-ffd7906f95de"
---

# 소개

소프트웨어 개발의 빠르게 변화하는 세계에서 일관성, 확장성 및 신뢰성을 보장하는 것이 중요합니다. 데브옵스에서 핵심 개념인 불변 인프라는 인프라 구성요소를 불변하게 만들어 이러한 요구 사항을 해결합니다. 이 블로그에서는 Docker, Kubernetes 및 Terraform을 사용하여 간단한 웹 애플리케이션을 위한 불변 인프라를 생성하는 방법을 안내하겠습니다.

www.linkedin.com/in/mohammedtalhakalimi

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

# 불변 인프라란?

불변 인프라는 서버(또는 다른 인프라 구성 요소)를 배포한 후에는 결코 수정하지 않는 방식을 말합니다. 업데이트나 변경이 필요한 경우 새로운 서버를 빌드하고 배포하며 이전 서버는 해제됩니다. 이 접근 방식은 일관성과 반복성을 보장하며 구성 드리프트를 줄이고 의도하지 않은 변경의 위험을 최소화합니다.

www.linkedin.com/in/mohammedtalhakalimi

# 프로젝트 개요

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

이 프로젝트에서는 Node.js 웹 애플리케이션을 위한 불변 인프라를 만들 것입니다. 이 프로젝트에는 다음이 포함됩니다:

- Docker를 사용하여 애플리케이션을 컨테이너화하기
- Kubernetes를 사용하여 배포를 조정하기
- Terraform을 사용하여 인프라 프로비저닝 및 관리하기
- CI/CD 파이프라인을 통해 빌드, 테스트 및 배포 프로세스 자동화하기

www.linkedin.com/in/mohammedtalhakalimi

# 단계 1: Docker를 사용하여 애플리케이션을 컨테이너화하기

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

먼저 Docker를 사용하여 웹 애플리케이션을 컨테이너화해야 합니다. 프로젝트 디렉토리에 Dockerfile을 만들어주세요:

```js
Dockerfile;
```

```js
# 부모 이미지로 공식 Node.js 런타임 사용
FROM node:14
```

```js
# 작업 디렉토리 설정
WORKDIR /usr/src/app
# package.json 복사 및 종속성 설치
COPY package*.json ./
RUN npm install
# 나머지 애플리케이션 코드 복사
COPY . .
# 애플리케이션 포트 노출
EXPOSE 3000
# 앱 실행 명령 정의
CMD ["node", "app.js"]
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

도커 이미지를 빌드하고 테스트해보세요:

```bash
docker build -t my-web-app .
docker run -p 3000:3000 my-web-app
```

# 단계 2: 쿠버네티스로 오케스트레이션하기

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

다음으로, 도커 컨테이너를 쿠버네티스 클러스터에 배포할 것입니다. deployment.yaml 및 service.yaml 파일을 생성해주세요:

```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
        - name: web-app
          image: my-web-app:latest
          ports:
            - containerPort: 3000
```

```yaml
# service.yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app-service
spec:
  selector:
    app: web-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
  type: LoadBalancer
```

쿠버네티스 구성을 적용해주세요:

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

```yaml
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

www.linkedin.com/in/mohammedtalhakalimi

# Step 3: Terraform으로 프로비저닝하기

테라폼을 사용하여 쿠버네티스 클러스터를 프로비저닝할 것입니다. main.tf 파일을 작성해주세요.

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
# main.tf
provider "aws" {
  region = "us-west-2"
}
```

```js
module "eks" {
  source          = "terraform-aws-modules/eks/aws"
  cluster_name    = "my-cluster"
  cluster_version = "1.20"
  subnets         = ["subnet-0123456789abcdef0", "subnet-0123456789abcdef1"]
  vpc_id          = "vpc-0123456789abcdef0"
  node_groups = {
    my-node-group = {
      desired_capacity = 2
      max_capacity     = 3
      min_capacity     = 1
      instance_type = "t3.medium"
    }
  }
}
output "cluster_endpoint" {
  value = module.eks.cluster_endpoint
}
```

테라폼 구성을 초기화하고 적용하십시오:

```js
terraform init
terraform apply
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

새 클러스터를 사용하도록 kubectl을 구성하세요:

```bash
aws eks --region us-west-2 update-kubeconfig --name my-cluster
```

LinkedIn 프로필: www.linkedin.com/in/mohammedtalhakalimi

# 단계 4: CI/CD로 자동화

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

마지막으로, GitHub Actions를 사용하여 CI/CD 파이프라인을 설정할 것입니다. .github/workflows/ci-cd-pipeline.yaml 파일을 만들어주세요:

```yaml
# .github/workflows/ci-cd-pipeline.yaml
name: CI/CD Pipeline
```

```yaml
on:
  push:
    branches:
      - main
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1
      - name: Login to Docker Hub
        uses: docker/login-action@v1
        with:
          username: ${secrets.DOCKER_USERNAME}
          password: ${secrets.DOCKER_PASSWORD}
      - name: Build and push Docker image
        run: |
          docker build -t my-web-app:latest .
          docker tag my-web-app:latest ${secrets.DOCKER_USERNAME}/my-web-app:latest
          docker push ${secrets.DOCKER_USERNAME}/my-web-app:latest
  deploy:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Set up kubectl
        uses: azure/setup-kubectl@v1
        with:
          version: "v1.20.0"
      - name: Deploy to Kubernetes
        run: |
          kubectl apply -f deployment.yaml
          kubectl apply -f service.yaml
```

www.linkedin.com/in/mohammedtalhakalimi

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

# 결론

위 단계를 따라 웹 애플리케이션을 위한 불변의 인프라를 구축했습니다. 이 방식을 통해 배포가 일관적이고 확장 가능하며 신뢰할 수 있음을 보장할 수 있습니다. 불변의 인프라 관행을 준수함으로써 응용 프로그램의 안정성과 관리 용이성을 크게 향상시킬 수 있습니다.

의견이나 경험을 댓글로 공유해 주세요. 즐거운 코딩 되세요!

www.linkedin.com/in/mohammedtalhakalimi
