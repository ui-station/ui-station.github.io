---
title: "도커 기본 요약 시트"
description: ""
coverImage: "/assets/img/2024-05-27-DockerBasicCheatSheet_0.png"
date: 2024-05-27 17:20
ogImage:
  url: /assets/img/2024-05-27-DockerBasicCheatSheet_0.png
tag: Tech
originalTitle: "Docker Basic CheatSheet"
link: "https://medium.com/aws-in-plain-english/docker-basic-cheatsheet-011b8ccf78fc"
---


![Docker Basic Cheat Sheet](/assets/img/2024-05-27-DockerBasicCheatSheet_0.png)

# Basic Commands:

## Container Lifecycle:

- docker run: Create and start a container.


<div class="content-ad"></div>

```docker
$ docker run -d --name my_container nginx
```

docker start/stop/restart: 컨테이너를 시작, 중지 또는 재시작합니다.

```docker
$ docker stop my_container
$ docker start my_container
$ docker restart my_container
```

docker ps: 실행 중인 컨테이너 목록을 표시합니다.

<div class="content-ad"></div>

```js
$ docker ps
```

docker ps -a 명령어를 사용하면 모든 컨테이너(중지된 것 포함)를 보여줍니다.

```js
$ docker ps -a
```

## 이미지 관리:

<div class="content-ad"></div>

도커 풀: 레지스트리에서 이미지를 다운로드합니다.

```js
$ docker pull ubuntu
```

도커 빌드: Dockerfile에서 이미지를 빌드합니다.

```js
$ docker build -t my_image .
```

<div class="content-ad"></div>

도커 이미지: 모든 로컬 이미지를 목록으로 확인할 수 있어요.

```js
$ docker images
```

도커 rmi: 이미지를 삭제할 수 있어요.

```js
$ docker rmi my_image
```

<div class="content-ad"></div>

# 컨테이너 작업:

## 컨테이너와 상호 작용하기:

도커 exec: 실행 중인 컨테이너에서 명령을 실행합니다.

```js
$ docker exec -it my_container bash
```

<div class="content-ad"></div>

도커 첨부: 실행 중인 컨테이너에 연결합니다.

```js
$ docker attach my_container
```

도커 로그: 컨테이너 로그를 확인합니다.

```js
$ docker logs my_container
```

<div class="content-ad"></div>

## 컨테이너 자원 관리:

도커 복사: 컨테이너와 호스트 간 파일 복사.

```js
$ docker cp file.txt my_container:/path/to/destination
```

도커 일시정지/재개: 실행 중인 컨테이너를 일시정지하거나 다시 시작합니다.

<div class="content-ad"></div>


$ docker pause my_container
$ docker unpause my_container


docker inspect: 디테일한 컨테이너 정보 표시


$ docker inspect my_container


# 네트워킹:

<div class="content-ad"></div>

## 네트워킹:

도커 네트워크 목록: 사용 가능한 네트워크를 나열합니다.

```js
$ docker network ls
```

도커 네트워크 생성: 새 네트워크를 생성합니다.

<div class="content-ad"></div>

```js
$ docker network create my_network
```

도커 네트워크 연결/해제: 컨테이너를 네트워크에 연결하거나 연결을 해제합니다.

```js
$ docker network connect my_network my_container
$ docker network disconnect my_network my_container
```

# 볼륨 관리:

<div class="content-ad"></div>

## 볼륨:

도커 볼륨 목록: 볼륨 목록을 표시합니다.

```js
$ docker volume ls
```

도커 볼륨 생성: 볼륨을 생성합니다.

<div class="content-ad"></div>

```js
$ docker volume create my_volume
```

도커 볼륨 삭제: 볼륨 제거하기.

```js
$ docker volume rm my_volume
```

도커 볼륨 조회: 자세한 볼륨 정보 표시하기.

<div class="content-ad"></div>

```sh
$ docker volume inspect my_volume
```

# 친절한 영어로 🚀

In Plain English 커뮤니티에 참여해주셔서 감사합니다! 떠나시기 전에:

- 작가를 박수로 응원하고 팔로우 해주세요 ️👏️️
- 팔로우해주세요: X | LinkedIn | YouTube | Discord | Newsletter
- 다른 플랫폼도 방문해주세요: Stackademic | CoFeed | Venture | Cubed
- PlainEnglish.io에서 더 많은 콘텐츠를 만나보세요
