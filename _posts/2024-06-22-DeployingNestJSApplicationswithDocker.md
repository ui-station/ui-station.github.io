---
title: "Docker로 NestJS 애플리케이션 배포하는 방법"
description: ""
coverImage: "/assets/img/2024-06-22-DeployingNestJSApplicationswithDocker_0.png"
date: 2024-06-22 22:54
ogImage: 
  url: /assets/img/2024-06-22-DeployingNestJSApplicationswithDocker_0.png
tag: Tech
originalTitle: "Deploying NestJS Applications with Docker."
link: "https://medium.com/@preetam19cs051/deploying-nestjs-applications-with-docker-1040cf534ff9"
---


![이미지](/assets/img/2024-06-22-DeployingNestJSApplicationswithDocker_0.png)

안녕하세요 여러분 👋, 여러분들이 잘 지내고 있기를 바라요! 오늘은 도커의 세계로 빠져보고, 응용 프로그램 개발과 배포에 미치는 변화를 이해해보려 합니다. 도커는 응용 프로그램과 종속성을 단일 컨테이너로 패키징하여 빌드, 배포 및 실행하는 방식을 혁신적으로 변화시켰습니다. 이는 다양한 환경 간에 일관성을 보장합니다. 이 블로그 포스트에서는 NestJS 응용 프로그램을 도커를 사용해 컨테이너화하고 원활하게 배포하는 과정을 이해해보겠습니다. 시작해봅시다!

## NestJS 프로젝트 설정 🛠️:

먼저 간단한 NestJS 애플리케이션을 만들어봅시다. NestJS CLI가 설치되어 있지 않다면 다음 명령어로 설치할 수 있습니다.

<div class="content-ad"></div>

```js
npm install -g @nestjs/cli
// 새로운 nestjs 앱 생성하기
nest new my-nest-app
```

## Dockerfile 작성:

Dockerfile을 만들기 전에 Docker 이미지와 컨테이너에 대한 단단한 기초적인 이해가 필요합니다. 다음과 같이 설명해보겠습니다:

Docker 이미지: Docker 이미지는 코드, 런타임, 라이브러리 및 환경 변수를 포함하여 소프트웨어 조각을 실행하는 데 필요한 모든 것이 포함된 가벼운 실행 가능한 것입니다. 


<div class="content-ad"></div>

도커 컨테이너: 도커 컨테이너는 코드와 모든 종속성을 포장하여 응용 프로그램이 다양한 컴퓨팅 환경에서 빠르고 신뢰성 있게 실행될 수 있도록 하는 경량, 휴대용, 자체 완비형 환경입니다.

이 컨테이너를 어디로든 이동하고 어떤 컴퓨터에서든 실행할 수 있으므로 소프트웨어 작업 여부를 걱정할 필요가 없습니다. 필요한 모든 것이 이미 안에 들어 있기 때문이죠.

도커 파일: Dockerfile은 응용 프로그램을 위한 도커 이미지를 구축하는 방법에 대한 일련의 명령을 포함하는 스크립트입니다. 프로젝트의 루트에 Dockerfile이라는 파일을 만들고 다음 내용을 추가하세요.

```js
# 공식 Node.js 16 이미지를 기본 이미지로 사용합니다
FROM node:16-alpine

# 작업 디렉토리 설정
WORKDIR /app

# package.json 및 package-lock.json 파일 복사
COPY package*.json ./

# 종속성 설치
RUN npm install

# 나머지 응용 프로그램 코드 복사
COPY . .

# NestJS 응용 프로그램 빌드
RUN npm run build

# 응용 프로그램 포트 노출
EXPOSE 3000

# 응용 프로그램 시작
CMD ["node", "dist/main"]
```

<div class="content-ad"></div>

## .dockerignore 파일 만들기:

.dockerignore 파일은 .gitignore 파일과 비슷하게 작동합니다. 이미지를 빌드할 때 Docker에게 무시해야 할 파일과 디렉토리를 알려줍니다. 프로젝트의 루트에 .dockerignore 파일을 만들고 다음 내용을 추가해주세요.

```js
node_modules
dist
.git
.gitignore
Dockerfile
```

## Docker 이미지 빌드 및 실행하기:

<div class="content-ad"></div>

이제 도커파일과 .dockerignore 파일이 있으니 도커 이미지를 빌드하고 실행해 봅시다.

```js
//도커에서 이미지를 빌드하는 명령어
docker build -t <이미지-이름>:태그

//예시: my-nest-app 이미지를 빌드하는 방법
docker build -t my-nest-app:latest .
```

태그(Tag): 태그는 Docker 이미지의 특정 버전을 식별하는 라벨입니다. 동일한 이미지의 여러 버전을 관리하고 구분하는 데 사용됩니다.

## 도커에서 이미지가 제대로 빌드되었는지 확인하는 방법:

<div class="content-ad"></div>

```js
//도커 이미지 목록을 제공합니다. 
도커 이미지 목록 확인

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
my-nest-app         latest              1d4f1e3f5b7e        2 hours ago         540MB
another-app         v1.0                3e23f1c3e6a4        3 days ago          350MB
```

## 도커 컨테이너 실행하기 :

아래는 도커 컨테이너를 실행하는 명령어입니다. host_port는 사용하려는 기기의 포트를 나타내고, container_port는 컨테이너 내부에서 애플리케이션이 수신 대기하는 포트를 나타냅니다.

```js
docker run -p [host_port]:[container_port] [image_name]:[tag]
```

<div class="content-ad"></div>

## 도커에 있는 컨테이너 목록 :

```js
// 모든 실행 중인 컨테이너 목록 표시
docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
f3b1c25a3ac8        my-nest-app:latest  "docker-entrypoint.s…"   2 hours ago         Up 2 hours          0.0.0.0:3000->3000/tcp   my-nest-app
a4d5f2b5c7a3        another-app:v1.0    "python app.py"          3 days ago          Up 3 days           0.0.0.0:5000->5000/tcp   another-app
```

이제 http://localhost:3000 에서 접속할 수 있어요 🚀.

# 도커 컴포즈 :

<div class="content-ad"></div>

도커 컴포즈는 여러 개의 컨테이너로 구성되는 도커 응용 프로그램을 정의하고 실행하는 도구입니다. NestJS 애플리케이션을 여러 개 실행하는 데 사용할 수 있습니다. 예를 들어, 마이크로서비스 또는 다른 서비스를 함께 실행하는 것이 가능합니다.

## NestJS 애플리케이션 설정:

app1 및 app2라는 두 개의 애플리케이션을 생성할 것이며, NestJS 프로젝트를 만드는 방법은 동일합니다.

```js
// 첫 번째 애플리케이션 생성
nest new app1

// 두 번째 애플리케이션 생성
nest new app2
```

<div class="content-ad"></div>

## 각 응용 프로그램을 위한 도커 파일 작성:

각 애플리케이션은 이미지를 빌드하기 위한 도커 파일이 필요합니다. 아래는 두 개의 NestJS 응용 프로그램에 동일한 도커 파일이 될 것입니다.

```js
# 공식 Node.js 이미지 사용
FROM node:16-alpine

# 작업 디렉토리 생성 및 변경
WORKDIR /app

# 응용 프로그램 종속성 파일을 이미지에 복사
COPY package*.json ./

# 종속성 설치
RUN npm install

# 로컬 코드를 이미지에 복사
COPY . .

# 응용 프로그램 빌드
RUN npm run build

# 응용 프로그램 시작
CMD [ "node", "dist/main.js" ]
```

## 도커 컴포즈 파일 작성:

<div class="content-ad"></div>

루트 디렉토리에 docker-compose.yml 파일을 만들어서 두 애플리케이션을 위한 서비스를 정의해야 해요.

```js
//도커  compose 파일의 버전 컨트롤러를 지정.
version: '3.8'
services:
  app1:
    build:
      context: ./app1
      dockerfile: Dockerfile
    //컨테이너 내부의 포트 3000을 호스트 머신의 포트 3001로 매핑합니다.
    ports:
      - "3001:3000"
    volumes:
      - ./app1:/app
    command: ["npm", "run", "start:prod"]

  app2:
    build:
      context: ./app2
      dockerfile: Dockerfile
    //컨테이너 내부의 포트 3000을 호스트 머신의 포트 3002로 매핑합니다.
    ports:
      - "3002:3000"
    volumes:
      - ./app2:/app
    command: ["npm", "run", "start:prod"]
```

## 파일 디렉토리 구조 :

```js
root/
├── app1/
│   ├── Dockerfile
│   ├── package.json
│   ├── ...
├── app2/
│   ├── Dockerfile
│   ├── package.json
│   ├── ...
└── docker-compose.yml
```

<div class="content-ad"></div>

## Docker Compose를 사용하여 응용 프로그램 빌드 및 실행하기:

docker-compose build 명령어는 docker-compose.yml에 지정된 도커 이미지를 빌드하는 데 사용됩니다.

docker-compose up 명령어는 서비스를 실행하는 데 사용됩니다.

## 컨테이너 중지 또는 제거하기:

<div class="content-ad"></div>

이 명령어는 docker-compose up 에 의해 생성된 네트워크를 포함하여 컨테이너 실행을 중지하고 그것들을 제거할 것입니다.

```js
docker-compose down 

// 컨테이너를 중지하지만 제거하지 않고 싶은 경우
docker-compose stop
```

## 결론 :

이 블로그 포스트에서는 Docker를 사용하여 NestJS 애플리케이션을 컨테이너화하는 기본 사항을 다뤘습니다. Dockerfile을 작성하고 .dockerignore 파일을 만들고 Docker Compose를 사용하여 컨테이너 실행을 간소화했습니다. Docker를 사용하면 응용 프로그램이 다른 환경에서 일관되게 실행되도록 보장할 수 있어 배포 및 확장을 쉽게 할 수 있습니다.