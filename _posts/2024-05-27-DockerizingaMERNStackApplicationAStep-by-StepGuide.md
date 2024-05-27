---
title: "MERN 스택 애플리케이션 도커화 단계별 가이드"
description: ""
coverImage: "/assets/img/2024-05-27-DockerizingaMERNStackApplicationAStep-by-StepGuide_0.png"
date: 2024-05-27 17:17
ogImage: 
  url: /assets/img/2024-05-27-DockerizingaMERNStackApplicationAStep-by-StepGuide_0.png
tag: Tech
originalTitle: "Dockerizing a MERN Stack Application: A Step-by-Step Guide"
link: "https://medium.com/gitconnected/dockerizing-a-mern-stack-application-a-step-by-step-guide-1c109d5a2cf9"
---


MERN 스택 애플리케이션을 구축하는 것은 도커화 및 여러 환경 관리와 관련해 도전적일 수 있습니다. 도커를 사용하면 애플리케이션을 컨테이너로 패키징하여 다양한 환경 간에 쉽게 이동할 수 있도록 도와줄 수 있습니다.

이 블로그 포스트에서는 Docker와 Docker Compose를 사용하여 MERN 스택 애플리케이션을 컨테이너화하는 과정을 안내해 드리겠습니다. Docker와 Docker Compose는 함께 작동하여 컨테이너화된 애플리케이션의 개발, 배포 및 관리를 간편화하는 데 도움이 되는 두 가지 강력한 도구입니다.

Docker는 애플리케이션과 그 종속성을 표준화된 단위인 컨테이너로 패키징할 수 있는 플랫폼입니다. 이러한 컨테이너는 가볍고 이식성이 있으며, Docker가 설치된 시스템의 기반이 되는 운영 체제에 관계없이 일관되게 실행될 수 있습니다.

Docker Compose는 쉽게 다중 컨테이너 애플리케이션을 정의하고 실행하기 위한 도구입니다. YAML 파일(일반적으로 docker-compose.yml로 명명됨)을 사용하여 애플리케이션의 서비스(컨테이너)와 그들 간의 관계를 구성할 수 있습니다.

<div class="content-ad"></div>

시작하기 전에 시스템에 다음 항목이 설치되어 있는지 확인하세요:

- Docker
- Node.js

그리고 도커와 관련된 기본적인 이해와 명령어가 있는 것으로 가정합니다.

## 단계 1: MERN 애플리케이션 설정하기

<div class="content-ad"></div>

가정하에 기본적인 MERN 애플리케이션이 다음과 같이 구성되어 있다고 가정하고, 다음과 같은 Dockerfile 및 docker-compose 파일을 만들어야 합니다.

```markdown
my-mern-app/
├── backend/
│   ├── Dockerfile
│   ├── package.json
│   ├── server.js
├── frontend/
│   ├── Dockerfile
│   ├── package.json
│   ├── public/
│   ├── src/
├── docker-compose.yml
```

## 단계 2: 백엔드와 프론트엔드 도커 파일 설정

백엔드 설정
```

<div class="content-ad"></div>

아래 지시사항을 백엔드 도커 파일에 포함해야 합니다.

```js
# backend/Dockerfile

# 공식 Node.js Alpine 기반 이미지 사용
FROM node:20.11.1-alpine

# 작업 디렉토리 설정
WORKDIR /app

# package.json 및 package-lock.json 복사
COPY package*.json ./

# 의존성 설치
RUN npm install

# 나머지 애플리케이션 코드 복사
COPY . .

# 실행 중인 앱의 포트 노출
EXPOSE 5000

# 애플리케이션 실행
CMD ["npm", "start"]
```

컨테이너의 기본 이미지로는 Alpine 리눅스 배포판을 기반으로 한 Node.js 런타임 버전 20.11.1을 사용하고 있습니다. Alpine 이미지는 일반적으로 더 작고 다운로드 속도가 빠릅니다. WORKDIR /app은 컨테이너 내부의 작업 디렉토리를 /app으로 설정합니다. 이후의 모든 명령은 이 디렉토리에서 실행됩니다. COPY package*.json ./는 로컬 머신에서 컨테이너로 package.json과 package-lock.json(있는 경우)을 복사합니다. 이 파일들은 종속성을 설치하는 데 사용됩니다.

이는 npm install을 컨테이너 내에서 실행하여 package.json에 지정된 모든 종속성을 설치합니다. COPY . .는 나머지 애플리케이션 코드를 컨테이너의 작업 디렉토리로 복사합니다. EXPOSE 5000은 컨테이너가 실행 중인 포트 5000에서 수신하는 것을 Docker에 알립니다. 이는 내부 포트를 호스트 머신의 외부 포트에 매핑하는 데 유용합니다. CMD ["npm", "start"]는 컨테이너 시작 시 실행할 명령을 지정합니다. 일반적으로 package.json에 정의된 start 스크립트를 사용하여 서버를 시작하는 npm start를 실행합니다.

<div class="content-ad"></div>

프론트엔드 설정

프론트엔드 도커 파일에 아래 지침을 포함해야 합니다.

```js
# frontend/Dockerfile

# 공식 Node.js Alpine 기반 이미지 사용
FROM node:20.11.1-alpine

# 작업 디렉토리 설정
WORKDIR /app

# package.json 및 package-lock.json 복사
COPY package*.json ./

# npm이 더 긴 타임아웃을 가지고 캐시를 사용하도록 설정
RUN npm config set cache /app/.npm-cache --global
RUN npm config set fetch-retries 10
RUN npm config set fetch-retry-mintimeout 40000
RUN npm config set fetch-retry-maxtimeout 220000

# 종속성 설치
RUN npm install

# 나머지 애플리케이션 코드 복사
COPY . .

# 애플리케이션이 실행되는 포트 노출
EXPOSE 3000

# 애플리케이션 실행
CMD ["npm","start"]
```

프론트엔드 및 백엔드 애플리케이션용 도커 파일을 설정할 때, 대부분의 지시사항이 매우 유사하다는 것을 알게 될 것입니다. 주요 차이점은 타임아웃 및 캐시 구성에 있습니다. 특정 요구 사항에 따라 추가 단계가 필요할 수도 있고 아닐 수도 있습니다.

<div class="content-ad"></div>

- 컨테이너 내의 캐시 디렉토리를 사용하세요 (/app/.npm-cache).
- 패키지를 가져올 때 재시도 횟수를 늘리세요 (fetch-retries).
- 패키지를 가져오는 데 걸리는 최소 및 최대 시간을 늘리세요 (fetch-retry-mintimeout 및 fetch-retry-maxtimeout).

이러한 설정은 불안정한 네트워크 환경에서 종속성을 다운로드할 때 신뢰성을 향상시킬 수 있습니다. 인터넷 연결이 제대로 되지 않을 때 도움이 될 수 있어요 :(.

## 단계 3: 도커 컴포즈 설정

루트 도커 컴포즈 파일에 아래 구성을 포함해야 합니다.

<div class="content-ad"></div>

```md
# docker-compose.yml

version: '3.8'
services:
  backend:
    build: ./backend
    ports:
      - '5000:5000'
  frontend:
    build: ./frontend
    ports:
      - '3000:3000'
```

`backend`: 백엔드 서비스를 정의합니다. 백엔드 디렉토리에서 Docker 이미지를 빌드하고 포트 5000으로 매핑합니다. `frontend`: 프론트엔드 서비스를 정의합니다. 프론트엔드 디렉토리에서 Docker 이미지를 빌드하고 포트 3000으로 매핑합니다.

만약 몽고 DB와 같은 추가 서비스를 추가해야 한다면, 다음과 비슷한 추가 서비스를 backend에 의존하도록 추가하면 됩니다.

```md
version: '3.8'
services:
  backend:
    build: ./backend
    ports:
      - '5000:5000'
    depends_on:
      - mongo
  mongo:
    image: mongo:latest
    ports:
      - '27017:27017'
```

<div class="content-ad"></div>

## 단계 4: 애플리케이션 빌드 및 실행

프로젝트 루트에서 다음 명령을 실행하여 애플리케이션을 빌드하고 시작합니다:

```js
docker-compose up — build
```

Docker Compose가 이미지를 빌드하고 컨테이너를 시작합니다. 프론트엔드는 http://localhost:3000에서, 백엔드는 http://localhost:5000에서 접속할 수 있습니다. 참조 코드는 여기에서 확인할 수 있습니다.

<div class="content-ad"></div>

이제 Docker Compose를 사용하여 MERN 스택 애플리케이션을 성공적으로 Docker화했습니다. 이 설정은 각 구성 요소에 대해 격리된 환경을 제공하여 애플리케이션을 관리하고 배포하기 쉽게합니다. 설정을 더 맞춤화하여 개발 및 프로덕션 요구 사항에 맞게 사용할 수 있습니다.

환영합니다...!