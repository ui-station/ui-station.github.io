---
title: "도커 도컴포즈를 사용하여 PostgreSQL을 설치하는 방법"
description: ""
coverImage: "/assets/img/2024-05-20-DockerHowtoInstallPostgreSQLusingDockerCompose_0.png"
date: 2024-05-20 17:06
ogImage: 
  url: /assets/img/2024-05-20-DockerHowtoInstallPostgreSQLusingDockerCompose_0.png
tag: Tech
originalTitle: "Docker : How to Install PostgreSQL using Docker Compose"
link: "https://medium.com/@agusmahari/docker-how-to-install-postgresql-using-docker-compose-d646c793f216"
---


PostgreSQL은 개발 커뮤니티에서 널리 사용되는 인기 있는 관계형 데이터베이스 관리 시스템입니다.

![이미지](/assets/img/2024-05-20-DockerHowtoInstallPostgreSQLusingDockerCompose_0.png)

Docker Compose는 여러 컨테이너로 구성된 Docker 응용 프로그램을 정의하고 실행할 수 있는 강력한 도구입니다. 여러 컨테이너를 쉽게 관리하기 위해 구성을 단일 YAML 파일에 정의할 수 있어요.

PostgreSQL은 개발 커뮤니티에서 널리 사용되는 인기 있는 관계형 데이터베이스 관리 시스템입니다. Docker Compose를 사용하면 PostgreSQL 인스턴스를 컨테이너에서 쉽게 설정하고 실행할 수 있어요. 이는 개발, 테스트 및 배포 목적으로 훌륭한 솔루션일 수 있어요.

<div class="content-ad"></div>

이 블로그 포스트에서는 Docker Compose를 사용하여 PostgreSQL을 설치하는 단계를 안내해 드리겠습니다.

단계 1: Docker 설치

Docker Compose를 설치하고 사용하기 전에 시스템에 Docker가 설치되어 있는지 확인해야 합니다. Docker는 Windows, macOS 및 Linux 운영 체제용으로 제공됩니다. 공식 Docker 웹사이트에서 적절한 버전의 Docker를 다운로드할 수 있습니다.

단계 2: Docker Compose 설치

<div class="content-ad"></div>

도커를 시스템에 설치한 후에는 공식 도커 컴포즈 설치 안내에 따라 도커 컴포즈를 설치할 수 있어요. 운영 체제에 따라 설치 단계가 조금씩 다를 수 있지만, 일반적으로 직관적입니다.

Step 3: 도커 컴포즈 파일 생성하기

도커 컴포즈를 사용하여 PostgreSQL 컨테이너를 생성하기 위해 구성을 도커 컴포즈 파일에 정의해야 해요. 원하는 디렉토리에 docker-compose.yml이라는 새 파일을 만들고 다음 코드를 붙여넣어 주세요:

```js
version: '3.9'

services:
  postgres:
    image: postgres:14-alpine
    ports:
      - 5432:5432
    volumes:
      - ~/apps/postgres:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=S3cret
      - POSTGRES_USER=citizix_user
      - POSTGRES_DB=citizix_db
```

<div class="content-ad"></div>

위와 같은 명령어입니다:

- up은 컨테이너를 구동합니다.
- -d는 백그라운드 모드로 실행합니다.

이 파일에서는 official PostgreSQL 이미지를 사용하는 db라는 단일 서비스를 정의합니다. 또한 default postgres 사용자의 암호를 지정하기 위해 POSTGRES_PASSWORD 환경 변수를 설정합니다. 마지막으로 컨테이너 내의 /var/lib/postgresql/data 디렉토리에 로컬 ./data 디렉토리를 장착하여 PostgreSQL 데이터를 저장하는 위치를 지정합니다.

단계 4: PostgreSQL 컨테이너 시작

<div class="content-ad"></div>

PostgreSQL 컨테이너를 시작하려면 터미널에서 docker-compose.yml 파일이 있는 디렉토리로 이동하고 다음 명령을 실행하세요:

```js
docker-compose up -d
```

```js
➜ docker-compose up -d
Creating network "pg_default" with the default driver
Creating pg_postgres_1 ... done
```

위 명령을 사용하세요.

<div class="content-ad"></div>

- 'up' 명령어를 사용하면 컨테이너를 올릴 수 있습니다.
- '-d' 옵션은 detached 모드로 실행합니다.

이 명령어는 컨테이너를 detached 모드로 시작합니다. 이는 백그라운드에서 실행됨을 의미합니다. 컨테이너가 실행 중인지 확인하려면 다음 명령어를 실행하세요:

```js
docker ps
```

```js
➜ docker-compose ps
    Name                   Command              State                    Ports
------------------------------------------------------------------------------------------------
pg_postgres_1   docker-entrypoint.sh postgres   Up      0.0.0.0:5432-
```

<div class="content-ad"></div>

스텝 5: PostgreSQL 컨테이너 중지 및 제거

PostgreSQL 컨테이너를 중지하고 제거하려면 터미널에서 docker-compose.yml 파일이 있는 디렉토리로 이동한 후 다음 명령을 실행하세요:

```js
docker-compose down
```

이렇게 하면 컨테이너가 중지되고 제거됩니다.

<div class="content-ad"></div>

도커 컴포즈를 사용하여 PostgreSQL을 설치하는 것은 컨테이너 내에서 빠르게 PostgreSQL 인스턴스를 설정할 수 있는 간단한 과정입니다. 도커 컴포즈 파일에서 컨테이너의 구성을 정의하여 여러 컨테이너를 쉽게 관리하고 확장할 수 있습니다.

도커 컴포즈를 사용하여 PostgreSQL을 설치하려면 먼저 시스템에 도커와 도커 컴포즈를 설치해야 합니다. 그 후 도커 컴폏 파일을 만들고 PostgreSQL 컨테이너의 구성을 정의할 수 있습니다. 그런 다음 컨테이너를 시작하고 psql과 같은 PostgreSQL 클라이언트를 사용하여 연결할 수 있습니다.

도커 컴포즈를 사용하면 PostgreSQL 컨테이너를 쉽게 관리하고 확장할 수 있으며 개발 및 테스트 환경을 설정하는 간단하고 반복 가능한 방법을 제공합니다. 전반적으로 도커 컴포즈는 복잡한 애플리케이션을 관리하고 배포하는 프로세스를 간소화할 수 있는 강력한 도구입니다.