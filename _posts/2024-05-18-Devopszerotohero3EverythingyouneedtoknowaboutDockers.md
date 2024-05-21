---
title: "데브옵스 제로 투 히어로 3  도커에 대해 알아야 할 모든 것"
description: ""
coverImage: "/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_0.png"
date: 2024-05-18 16:42
ogImage: 
  url: /assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_0.png
tag: Tech
originalTitle: "Devops zero to hero #3 — Everything you need to know about Dockers"
link: "https://medium.com/illumination/devops-zero-to-hero-3-everything-you-need-to-know-about-dockers-7ff321b38e6b"
---


## 데브옵스 업무 흐름에서 도커 사용을 시작하는 완벽한 가이드

### 이 블로그 포스트에서 무엇을 기대해야 하는가?

### 왜 Docker를 사용해야 하는가?

20~30년 전으로 돌아가면, 하드웨어와 설치된 운영 체제(커널 및 UI)가 있었습니다. 어플리케이션을 실행하기 위해서 코드를 컴파일하고 모든 앱 의존성을 정렬해야 했습니다. 다른 어플리케이션이나 어플리케이션 작업량의 증가를 수용하기 위해 새 하드웨어를 구매하고 설치 및 구성해야 했습니다.

<div class="content-ad"></div>

가상화는 하드웨어와 운영 체제 사이에 추가 계층인 하이퍼바이저라는 것을 추가했습니다. 이를 통해 사용자들은 독립된 여러 애플리케이션을 실행하여 가상 머신을 자체 운영 체제와 함께 실행할 수 있었습니다.

그럼에도 불구하고, 우리는 모든 가상 머신에 소프트웨어를 설치하고 종속성을 설정해주어야 했습니다. 애플리케이션이 휴대 가능하지 않았습니다. 일부 기기에서는 작동하지만 다른 기기에서는 작동하지 않았습니다.

![이미지](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_0.png)

# 도커란 무엇인가요?

<div class="content-ad"></div>

도커는 마이크로서비스 기반 응용 프로그램 개발이 가능하도록 함으로써 소프트웨어 빌드 방법을 혁신했습니다.

# 도커는 어떻게 작동하나요?

도커 엔진은 호스트 운영 체제 위에서 실행됩니다. 도커 엔진에는 호스트 시스템에서 도커 컨테이너를 관리하는 서버 프로세스(dockerd)가 포함되어 있습니다. 도커 컨테이너는 응용 프로그램과 그 의존성을 격리시켜 다른 환경에서도 일관되게 실행할 수 있도록 설계되었습니다.

![이미지](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_1.png)

<div class="content-ad"></div>

도커를 사용하려면 Dockerfile, Docker 이미지, Docker 컨테이너라는 세 가지 개념을 이해해야 합니다.

## Docker 파일(Dockerfile)이란?

## Docker 이미지란 무엇인가요?

Docker 이미지에는 컨테이너 내에서 코드를 실행하는 데 필요한 모든 종속성이 포함되어 있습니다.

<div class="content-ad"></div>

## Docker 컨테이너란 무엇인가요?

# Docker로 시작하기

## 사전 요구 사항

Docker는 로컬 머신 또는 클라우드 VM에 설치되어 있어야 합니다.

<div class="content-ad"></div>

Linux:

로컬 노트북, 가상 머신 또는 클라우드 VM에서 Linux를 실행 중이라면 패키지 관리자를 사용하여 Docker를 설치할 수 있습니다. 이 블로그 포스트의 지시사항을 따라주세요.

Mac 및 Windows:

로컬 머신에서 Docker를 실행할 수 있게 해주는 Docker Desktop을 설치할 수 있습니다.

<div class="content-ad"></div>

도커를 시작하는 것은 쉬운 일이에요. 아래 명령을 실행하기만 하면 돼요

```js
도커 런 -d -t --name Thor alpine
도커 런 -d -t busybox
```

이 명령은 도커 이미지 alpine과 busybox로부터 2개의 컨테이너를 생성할 거에요. 이 둘은 도커 허브에 저장된 미니멀리스트이면서 퍼블릭인 리눅스 도커 이미지에요.

- -d 옵션은 컨테이너를 백그라운드에서 실행(detach mode)
- -t 옵션은 tty 터미널을 연결해 줘요.
- --name 옵션은 컨테이너에 이름을 부여할 수 있어요. 제공하지 않으면 컨테이너는 랜덤 이름을 받게 돼요

<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_2.png" />

참고: 위에 언급된 이미지로 처음 docker run을 실행하면 도커가 로컬 머신에서 이미지를 다운로드(풀)해야 합니다.

## 로컬 머신에서 실행 중인/중지된 컨테이너 목록

```js
docker ps # 실행 중인 컨테이너 확인
docker ps -a # 모든 컨테이너 목록
```

<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_3.png" />

## 로컬 머신에 있는 도커 이미지 목록

```js
docker image ls
```

<img src="/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_4.png" />

<div class="content-ad"></div>

Linux 기반 도커 컨테이너에 사용된 이미지 크기를 확인할 수 있어요. 우분투, 아마존 리눅스, CentOS 등과 같은 일반적인 Linux 기반 머신과 비교하면 작아요.

실행 중인 컨테이너와 상호 작용하는 방법은 명령을 전달하거나 대화형 세션을 여는 두 가지 방법이 있어요.

```js
# docker exec -it <컨테이너 ID> <셸>
```

- docker exec를 사용하면 실행 중인 도커 컨테이너 내부로 진입할 수 있어요.
- --it는 도커와 대화형 세션을 열어줘요.
- 셸은 sh, bash, zsh 등을 사용할 수 있어요.

<div class="content-ad"></div>

컨테이너 정보를 얻기 위해 몇 가지 명령을 실행해 봅시다.

```js
docker exec -t Thor ls ; ps
# -t는 tty 세션을 열기 위한 옵션입니다.
# Thor는 컨테이너 이름입니다.
# ls ; ps는 ls와 ps 두 개의 명령을 실행합니다.

docker exec -t 8ad10d1d0660 free -m 
# 8ad10d1d0660은 컨테이너 ID이며, free -m은 메모리 사용량을 확인하는 명령입니다.
```

![이미지](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_5.png)

이제 -it 옵션을 사용하여 컨테이너와 대화형 쉘 세션을 열어보겠습니다.

<div class="content-ad"></div>

```js
도커 exec -it 16fb1c59fbea sh 
# 세션을 종료하려면 "exit"을 입력하세요
```

![이미지](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_6.png)

# 컨테이너 시작, 중지, 삭제

```js
# 실행 중인 컨테이너를 중지하는 방법
docker stop <컨테이너 이름 또는 ID>

# 중지된 컨테이너를 시작하는 방법
docker start <컨테이너 이름 또는 ID>
```

<div class="content-ad"></div>


![Image](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_7.png)

```js
# 실행 중인 컨테이너를 종료한 후에 삭제하려면 다음과 같이 명령을 사용합니다.
# docker stop <컨테이너 이름 또는 ID>
# docker rm <컨테이너 이름 또는 ID>
docker stop 16fb1c59fbea
docker rm 16fb1c59fbea

# 아니면 -f 플래그를 사용하여 강제로 컨테이너를 삭제합니다.
docker rm -f Thor
```

![Image](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_8.png)

# 도커 이미지 빌드하기


<div class="content-ad"></div>

# Docker 네트워킹

도커는 다양한 종류의 네트워크를 제공합니다.

## 1. 기본 브릿지

만약 도커허브에서 nginx 이미지를 사용하여 nginx 컨테이너를 실행 중이고 웹 서버에 액세스하려고 한다면요.

<div class="content-ad"></div>

![screenshot](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_9.png)

스크린샷을 통해 Nginx 웹 서버가 80번 포트에서 실행 중임을 확인할 수 있습니다. NGINX 컨테이너에 로그인하여 curl 127.0.0.1:80을 실행하면 웹 서버에서 HTML 응답을 반환합니다.

- 127.0.0.1은 루프백 주소로 현재 장치(로컬호스트)를 항상 가리킵니다.

![screenshot](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_10.png)

<div class="content-ad"></div>

위에서 본 것처럼, 컨테이너 내부의 웹 서버가 우리가 기대한 대로 작동했습니다.

이제 호스트 머신 (랩톱/가상 머신)에서 웹 서버에 연결해 보겠습니다.

![이미지](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_11.png)

컨테이너에서 실행 중인 nginx 웹 서버에 연결할 수 없었습니다.

<div class="content-ad"></div>

네트워크 깊이 파고들기

도커 컨테이너를 검사하려면 docker inspect 명령을 실행하고 맨 아래로 스크롤하면 도커가 브릿지 네트워크를 사용하고 네트워크에 대한 자세한 내용을 볼 수 있습니다.

```js
docker inspect nginx-container
```

<img src="/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_12.png" />

<div class="content-ad"></div>

```js
도커 네트워크 목록
```

<img src="/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_13.png" />

네트워크를 검사하면 기본 브릿지 네트워크를 사용하는 컨테이너를 찾을 수 있습니다.

```js
도커 네트워크 검사 브릿지
```

<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_14.png" />

## 포트 전달

```js
도커 실행 -d -p <호스트 포트>:<컨테이너 포트> --name <컨테이너 이름> 이미지

# 포트 전달 -> 컨테이너의 포트 80을 호스트 머신의 포트 5000으로 전달
도커 실행 -t -d -p 5000:80 --name nginx-container nginx:latest
```

<img src="/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_15.png" />

<div class="content-ad"></div>

![](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_16.png)

도커는 기본 네트워크를 사용하지 말라고 권장하고 대신에 우리만의 네트워크를 생성하도록 하고 있습니다.

busybox 이미지를 사용하여 2개의 컨테이너를 생성해 봅시다. 컨테이너를 실행한 후, 컨테이너 내부에서 서로 핑을 시도해 봅시다.

2개의 컨테이너를 생성하고, 각 컨테이너의 IP 주소를 얻기 위해 docker inspect를 사용해 보세요.

<div class="content-ad"></div>

아래는 두 컨테이너의 IP 주소가 동일한 네트워크에 있음을 확인할 수 있습니다(기본 브리지 네트워크).

이름과 IP 주소로 서로 핑을 보내 봅시다.

![image1](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_17.png)

![image2](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_18.png)

<div class="content-ad"></div>

위 스크린샷을 보면 두 컨테이너 모두 서로 통신할 수 있지만 이름으로는 연결할 수 없습니다.

## 2. 사용자 정의 브리지 네트워크

네트워크 생성

```js
docker network create blog-network
```

<div class="content-ad"></div>

새로운 네트워크를 사용하여 이름이 nginx-con인 nginx 컨테이너를 만들어보세요.

```bash
docker run -itd --network blog-network --name nginx-con nginx
docker ps
```

![이미지](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_19.png)

도커 컨테이너와 네트워크를 검사해봅시다.

<div class="content-ad"></div>

```js
도커 네트워크를 검사하는 방법: 

```docker network inspect blog-network```

![사진](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_20.png)

Nginx 컨테이너를 검사하는 방법: 

```docker inspect nginx-con```

![사진](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_21.png)
```

<div class="content-ad"></div>

이제 호스트 포트를 통해 컨테이너에서 실행 중인 nginx 웹 서버에 액세스하려고 하면 작동하지 않을 것입니다. 여전히 컨테이너에서 실행 중인 웹 서버에 액세스하려면 호스트 포트로의 포트 포워딩을 수행해야 합니다.

그러나 하나가 정리되었습니다 — 이름 해결입니다. 이제 사용자 정의 브리지 네트워크에서 컨테이너를 핑하면 작동해야 합니다.

한 가지 busybox 컨테이너를 만들고 nginx-con 컨테이너를 핑해 보겠습니다.

![이미지](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_22.png)

<div class="content-ad"></div>

## 3. 호스트 네트워크

```js
docker run -td --network host --name nginx-server nginx:latest
docker ps
```

![image](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_23.png)

이렇게 하면 nginx 컨테이너가 호스트 네트워크에서 실행됩니다.

<div class="content-ad"></div>

만약 로컬 호스트에서 nginx 서버에 접근하려고 하면, 작동해야 합니다.

만약 컨테이너의 IP 주소를 확인하려면

```js
docker inspect nginx-server | grep IPAddress
```

아래 그림을 참고하세요:

![이미지](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_24.png)

<div class="content-ad"></div>

뭐라구요? 해당 IP 주소가 없나봐요. 호스트 머신의 IP 주소를 사용한 모양이에요.

다른 종류의 네트워크에 대해 더 알려드릴게요. 하지만 블로그가 너무 길어지고 있네요.

# 도커 볼륨

도커는 로컬 파일 시스템에서 컨테이너의 모든 콘텐츠, 코드 및 데이터를 분리합니다. 이는 도커 데스크탑에서 컨테이너를 삭제하면 그 안의 모든 콘텐츠가 삭제된다는 뜻이에요.

<div class="content-ad"></div>

가끔은 컨테이너가 생성한 데이터를 유지하기를 원할 수 있습니다. 이때 볼륨을 사용할 수 있습니다.

## 바인드 마운트

바인드 마운트를 사용하면 호스트 머신의 파일 또는 디렉토리가 컨테이너로 마운트됩니다.

## 도커 볼륨

<div class="content-ad"></div>

체적은 도커에서 관리하는 로컬 파일 시스템의 위치입니다.

체적은 사용 중인 컨테이너의 크기를 늘리지 않으며, 체적의 내용은 특정 컨테이너의 생명 주기 외부에 존재합니다.

# 도커에서 체적 사용하는 방법

도커에서 체적을 사용하는 방법에는 --mount 및 -v (또는 --volume ) 두 가지가 있습니다. -v 구문은 모든 옵션을 하나의 필드에 결합하며, --mount 구문은 옵션을 분리합니다.

<div class="content-ad"></div>

- -v 또는 --volume: 콜론 문자(:)로 구분된 세 개의 필드로 구성됩니다.

   - 첫 번째 필드는 바인드 마운트의 경우 호스트 머신의 파일 또는 디렉터리 경로이거나 볼륨의 이름입니다.
   - 두 번째 필드는 컨테이너 내에서 파일 또는 디렉터리가 마운트된 경로입니다.
   - 세 번째 필드는 선택 사항입니다. ro, z, Z와 같은 옵션들의 쉼표로 구분된 목록입니다.

--mount: 쉼표로 구분된 여러 개의 키-값 페어로 구성됩니다.

   - 마운트의 유형(bind, volume 또는 tmpfs).
   - 마운트의 소스.
   - 목적지는 파일 또는 디렉터리가 컨테이너 내에서 마운트된 경로로 값을 취합니다.

<div class="content-ad"></div>

# 예시: Docker 컨테이너와 바인드 마운트

우리는 호스트 머신의 동일한 디렉토리/폴더를 4개의 컨테이너에 마운트했습니다.

```js
mkdir docker-bind-mount
docker run -t -d  -v docker-bind-mount:/app/log  --name captain-america busybox
docker run -t -d  -v docker-bind-mount:/app/log  --name thor busybox
docker run -t -d  -v docker-bind-mount:/app/log  --name hulk  busybox
docker run -t -d  -v docker-bind-mount:/app/log  --name iron-man  alpine

# --mount 옵션을 사용한 명령어
docker run -t -d --mount type=bind,source=docker-bind-mount,target=/app/log \
  --name captain-america busybox
```

<img src="/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_25.png" />

<div class="content-ad"></div>


![image](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_26.png)

각 컨테이너 안에 저장된 로그를 작성해봅시다. 바인드-마운트가 연결된 경로인 /app/log에

![image](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_27.png)

자세히 살펴보면, 1번째와 2번째 컨테이너에서 생성한 파일들이 3번째 컨테이너에서 보이며, 1번째에서 3번째 컨테이너에서 생성한 파일이 4번째(Captain-America) 컨테이너에서도 보인다는 점을 알 수 있습니다.


<div class="content-ad"></div>

일반적으로 도커 데이터 파일에는 다른 컨테이너의 내용이 포함되어 있습니다.

이 예시는 우리가 어떻게 간단하게 여러 컨테이너 간에 파일/로그를 공유할 수 있는지 보여줍니다.

하지만, 한 가지 주의할 점이 있습니다. 만약 우리가 로컬 호스트로 이동하여 모든 컨테이너에 바인드 마운트한 디렉터리를 목록으로 보면 아무것도 찾을 수 없을 것입니다.

이것은 즉, 도커가 바인드 마운트한 데이터를 다른 컨테이너와 공유할 수 있었지만 데이터가 로컬 호스트에서는 보이지 않는다는 것을 의미합니다.

<div class="content-ad"></div>

이 파일들은 바인드 마운트 시 도커 컨테이너 간에 저장되고 공유됩니다.

![Image](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_28.png)

도커 컨테이너 중 하나를 검사하고 마운트 섹션으로 이동하면 마운트에 관련된 세부 정보를 볼 수 있습니다.

참고: 도커 inspect 명령은 도커 컨테이너에 대해 더 많은 정보를 제공합니다.

<div class="content-ad"></div>

```js
# 도커 컨테이너 조회 명령어: docker inspect <컨테이너 이름 또는 컨테이너 ID>
docker inspect hulk
```

![도커 컨테이너 및 볼륨에 대한 자세한 정보](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_29.png)

# 예제: 볼륨을 사용하는 Docker 컨테이너

볼륨 생성하기


<div class="content-ad"></div>

```md
# 도커 볼륨 생성하기
도커 볼륨 생성 thor-vol
도커 볼륨 생성 hulk-vol

# 도커 볼륨 목록 확인
도커 볼륨 목록 보기

# 도커 볼륨   
# 명령어:
#  create      볼륨 생성
#  inspect     한 개 이상의 볼륨에 대한 자세한 정보 표시
#  ls          볼륨 목록 보기
#  prune       사용하지 않는 로컬 볼륨 제거
#  rm          한 개 이상의 볼륨 제거
```

<img src="/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_30.png" />

볼륨 검사하기

```md
도커 볼륨 inspect thor-vol
```

<div class="content-ad"></div>


![이미지](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_31.png)

새 컨테이너를 생성하는 동안 이 볼륨을 마운트해 봅시다. 이번에는 — — mount 옵션을 사용할 거에요.

```js
docker run -d \
  --name thor-container \
  --mount type=volume,source=thor-vol,target=/app \
  nginx:latest

# 볼륨을 source로 명시할 때 type의 기본 값은 volume입니다.
docker run -d \ 
  --name hulk-container \
  --mount source=thor-vol,target=/app \
  nginx:latest
```

```js
docker inspect hulk-container
```   
  

<div class="content-ad"></div>


![Image 1](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_32.png)

Let’s create some logs in both containers and see if the files/logs are visible to both containers.

![Image 2](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_33.png)

As you can see from the above screenshot, we can share data/logs across containers.


<div class="content-ad"></div>

# 도커 볼륨 삭제

```js
# 도커 볼륨을 제거하기
# 한 번에 하나 이상의 볼륨을 삭제할 수 있습니다
docker volume rm <volume-names> 
```

![이미지](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_34.png)

# 도커 이미지 빌드

<div class="content-ad"></div>

이제 기본적인 도커 개념을 살펴 보았으니, 실습 예제를 해 봅시다.

## 여기서 무엇을 할 건가요?

도커 이미지를 만들어 기본적인 Flask 애플리케이션을 내장시킬 거에요. 그리고 만든 도커 이미지를 도커 허브에 푸시할 거에요.

한 폴더를 만들고 app.py, requirements.txt, 그리고 Dockerfile 이라는 3개의 파일을 생성해 볼까요?

<div class="content-ad"></div>

```js
Dockerfile, app.py, requirements.txt 파일들을 만들었습니다.

<img src="/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_35.png" />

각 파일에 아래 코드를 붙여 넣어주세요.

app.py
```

<div class="content-ad"></div>

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_docker():
    return 'Hello, Docker!'

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
```

requirements.txt

```python
Flask
```

이제 Docker 이미지를 빌드하기 위한 Dockerfile을 생성할 것입니다.```

<div class="content-ad"></div>

Dockerfile

```js
# 부모 이미지로 공식 Python 실행 환경 사용
FROM python:3.11

# 작업 디렉토리를 /app으로 설정
WORKDIR /app

# Python 종속성 파일을 컨테이너의 /app 디렉토리로 복사
COPY requirements.txt /app

# requirements.txt에 명시된 필요한 패키지 설치
RUN pip install --no-cache-dir -r requirements.txt

# flask 앱 파일을 컨테이너의 /app 디렉토리로 복사
COPY app.py /app

# 포트 5000을 이 컨테이너 외부에 노출
EXPOSE 5000

# 컨테이너가 시작될 때 app.py 실행
CMD ["python", "app.py"]
```

도커 이미지 생성

```js
# docker build -t <이미지-이름> <Dockerfile 경로>
docker build -t flask-image .
```

<div class="content-ad"></div>

아래는 Markdown 형식으로 변경되었습니다:


![Docker Image](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_36.png)

Docker Hub repository를 만들어보겠습니다. 아직 계정이 없다면 만드세요 - livingdevopswithakhilesh라는 이름으로 만들었어요.

![Image 태깅](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_37.png)


<div class="content-ad"></div>

```md
# docker tag <local image> <docker hub username>/<repository name>:<tag>
docker tag flask-image livingdevopswithakhilesh/docker-demo-docker:1.0
```

![Image](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_38.png)

도커 허브에 이미지를 푸시하세요. 이미지를 푸시하기 전에 docker login을 실행해주세요.

```sh
docker login
# docker push <tagged-image>
docker push livingdevopswithakhilesh/docker-demo-docker:1.0
```

<div class="content-ad"></div>

![Image](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_39.png)

로컬 이미지를 삭제하고 Docker Hub에서 이미지를 가져와서 해당 이미지를 사용하여 컨테이너를 실행합니다.

```js
docker pull livingdevopswithakhilesh/docker-demo-docker:1.0
```

![Image](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_40.png)

<div class="content-ad"></div>

```js
docker run -td -p 8080:5000 --name flask livingdevopswithakhilesh/docker-demo-docker:1.0
```

![Image](/assets/img/2024-05-18-Devopszerotohero3EverythingyouneedtoknowaboutDockers_41.png)

이 블로그는 여기까지입니다. 다음 블로그에서 뵙겠습니다. 저와 함께 더 유용한 콘텐츠를 확인하려면 팔로우해주세요.

Linkedin에서 연락하기: [https://www.linkedin.com/in/akhilesh-mishra-0ab886124/](https://www.linkedin.com/in/akhilesh-mishra-0ab886124/)

<div class="content-ad"></div>

시리즈 Devops Zero to Hero의 세 번째 블로그였습니다. 첫 두 개 블로그를 확인해보세요 —