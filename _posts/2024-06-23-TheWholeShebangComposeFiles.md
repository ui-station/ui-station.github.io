---
title: "전체 구성 Compose 파일 쉽게 설정하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-TheWholeShebangComposeFiles_0.png"
date: 2024-06-23 00:30
ogImage: 
  url: /assets/img/2024-06-23-TheWholeShebangComposeFiles_0.png
tag: Tech
originalTitle: "The Whole Shebang: Compose Files"
link: "https://medium.com/better-programming/the-whole-shebang-compose-files-5b6f50dd196c"
---


<img src="/assets/img/2024-06-23-TheWholeShebangComposeFiles_0.png" />

요즘 Docker 및 컨테이너에 대해 많이 쓰고 가르치고 있습니다. 내 컨텐츠를 소비하고 피드백을 제공해 주시는 여러분들에게 매우 감사합니다. 여러분의 의견을 듣고 다음 주제는 당연히 Docker Compose입니다. 왜냐하면 이제 여러분은 자신의 컨테이너 이미지를 만들고 실행하는 방법을 알고 있으므로, 다음으로 논리적으로 남은 일은 복잡한 멀티 컨테이너 애플리케이션을 만들고 그 모든 것을 단일 소프트웨어로 관리하는 것입니다.

# Docker Compose: 그게 뭔데, 왜 필요한가요?

공식 문서에서 더 많은 정보를 확인할 수 있지만, 간단히 말하면 다음과 같습니다: Docker를 사용하여 컨테이너, 이미지, 볼륨 등을 관리합니다. docker run을 실행하면 기본적으로 단일 컨테이너를 실행하는 것입니다. 만약 첫 번째 컨테이너와 상호 작용하는 두 번째 컨테이너를 시작하려면 docker run을 다시 실행해야 하며, 볼륨 및 네트워크를 별도로 처리하여 서로를 보고 통신할 수 있도록 해야 합니다.

<div class="content-ad"></div>

도커 컴포즈는 도커 위에 래퍼를 제공하여 이 모든 것을 추상화합니다 (맞아요! 도커 컴포즈는 단순히 도커를 위한 고수준 CLI입니다). 실제로 대부분의 옵션이 docker run 하위 명령어에서 사용 가능한 옵션들과 매우 유사한 방식으로 명명됩니다.

도커 컴포즈를 사용하면 YAML 파일인 컴포즈 파일을 통해 컨테이너(도커 컴포즈 세계에서는 서비스라고 불립니다... 단지 스웜 모드에서도 컴포즈 파일을 사용할 수 있기 때문에... 다른 글에서 설명할 예정), 볼륨 및 네트워크를 포함한 모든 것을 선언적으로 정의할 수 있습니다. 이렇게 하면 멀티 컨테이너 애플리케이션을 관리해야 할 때, 이 compose 파일만 건드리고 나머지는 도커 컴포즈가 모두 처리하도록 할 수 있습니다.

# 컴포즈 파일

우선, 멀티 컨테이너 애플리케이션을 올바르게 운영하려면 컴포즈 파일을 작성하는 방법을 익혀야 하므로 도커 컴포즈 CLI에 대해서는 다른 글에서 다룰 예정입니다.

<div class="content-ad"></div>

도커의 구성 파일 관련 문서는 매우 완벽하지만, 저는 실용주의와 경험으로부터 배우는 것을 중요하게 생각하는 사람이라서 모든 Compose 파일 옵션을 손질하고 명료한 설명과 실제 예시를 담아내려고 합니다. 여기서 시작해봅시다!

참고 1: 이 기사를 작성하는 시점에서 최신 버전인 v3의 Compose 파일 명세에 대한 옵션과 스키마만 다룰 예정입니다. 그러니 걱정하지 마세요.

참고 2: 각 옵션에 대해 일반적으로 사용되는 방법에 대한 설명과 예시만 제공할 것이며, 특정 옵션 매개변수를 사용해본 적이 없는 경우에는 "일반적이지 않음"으로 간주할 것입니다. 어떤 옵션의 적은 사용 방법 및 일반적이지 않은 매개변수를 더 자세히 알고 싶다면 공식 문서를 참조해주세요.

예시에 들어가기 전에, 모든 구성 파일은 작성하는 현재의 파일 스키마를 나타내는 키워드 version으로 시작됩니다. 앞서 말한대로, 저희는 최신 (버전: "3")을 사용할 것입니다.

<div class="content-ad"></div>

## 빌드

기본적으로 Docker Compose로 무언가를 시작할 때, Docker 이미지 또는 Dockerfile을 제공할 수 있습니다. 해당 컨테이너를 시작하기 전에 Docker가 이미지를 직접 빌드하도록 하고 싶다면 수동으로 이미지를 빌드하지 않고도 이 옵션을 사용할 수 있습니다.

예를 들어:

```js
$ cat >Dockerfile <<EOF
# 이것은 Dockerfile입니다
FROM ubuntu
CMD ["echo", "Hello Medium readers"]
# Dockerfile 끝
EOF
$
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  my-test-container:
    build: .
EOF
$
$ docker-compose up 
디폴트 드라이버를 사용하여 "mediumcom_default" 네트워크 생성 중
my-test-container 빌드 중
Docker 데몬으로 빌드 컨텍스트 보내는 중  1.337MB
단계 1/2 : FROM ubuntu
 ---> ba6acccedd29
단계 2/2 : CMD ["echo", "Hello Medium readers"]
 ---> 캐시 사용 중
 ---> 6c8b22fa650c
성공적으로 빌드되었습니다 6c8b22fa650c
mediumcom_my-test-container:latest로 성공적으로 태깅되었습니다
mediumcom_my-test-container_1 생성 중 ... 완료
mediumcom_my-test-container_1에 연결 중
my-test-container_1  | Hello Medium readers
mediumcom_my-test-container_1는 코드 0으로 종료됨
$ 
```

<div class="content-ad"></div>

보시다시피, Docker Compose는 Docker에게 도커파일(컴포즈 파일과 동일한 경로에 위치)로부터 이미지를 빌드하도록 지시하고, 그 후에 자동으로 컨테이너를 시작했습니다.

참고: 일반적으로 docker build와 함께 사용하는 일부 옵션들(빌드 인자 전달, Dockerfile의 경로 및 이름 지정 등)도 여기에서 사용할 수 있습니다. 자세한 내용은 문서를 확인해주세요.

## 이미지

어플리케이션을 실행할 때마다 Docker 이미지를 빌드할 필요는 없겠죠... 그렇게 하면 불편하겠죠. 따라서 이미지를 미리 빌드하거나 간단히 컨테이너 이미지를 지정하면 됩니다.

<div class="content-ad"></div>

예를 들어, 이전 예제의 Dockerfile과 다음의 compose 파일을 살펴보세요:

```js
$ # 이전 예제 정리하기 위함
$ # 이제부터 각 예제에서 다음 명령 실행해주세요
$ docker-compose down -v --remove-orphans   
$
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    build: .
  second:
    image: hello-world
EOF
$ # Dockerfile이 변경되지 않았기 때문에 첫 번째 이미지가 다시 빌드되지 않습니다
$ docker-compose up    
네트워크 "mediumcom_default" 생성 중
mediumcom_first_1  ... 생성됨
mediumcom_second_1 ... 생성됨
mediumcom_second_1, mediumcom_first_1에 연결 중
first_1   | Hello Medium readers
second_1  | 
second_1  | Hello from Docker!
second_1  | This message shows that your installation appears to be working correctly.
second_1  | 
second_1  | To generate this message, Docker took the following steps:
second_1  |  1. The Docker client contacted the Docker daemon.
second_1  |  2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
second_1  |     (amd64)
second_1  |  3. The Docker daemon created a new container from that image which runs the
second_1  |     executable that produces the output you are currently reading.
second_1  |  4. The Docker daemon streamed that output to the Docker client, which sent it
second_1  |     to your terminal.
second_1  | 
second_1  | To try something more ambitious, you can run an Ubuntu container with:
second_1  |  $ docker run -it ubuntu bash
second_1  | 
second_1  | Share images, automate workflows, and more with a free Docker ID:
second_1  |  https://hub.docker.com/
second_1  | 
second_1  | For more examples and ideas, visit:
second_1  |  https://docs.docker.com/get-started/
second_1  | 
mediumcom_second_1이 코드 0으로 종료됨
mediumcom_first_1이 코드 0으로 종료됨
```

여기서 보듯이, 이제 하나의 Compose 파일과 하나의 docker-compose 명령을 사용하여 동일한 다중 컨테이너 애플리케이션 내에서 병렬로 2개의 컨테이너를 실행했습니다. 하나의 컨테이너는 "Hello Medium readers"라고 말하는 사용자 지정 Docker 이미지를 사용했고, 다른 하나는 Docker Hub에서 "hello-world" 이미지를 가져와 자체 Hello World 메시지를 출력했습니다.

## 명령

<div class="content-ad"></div>

이를 통해 컨테이너의 명령을 정의할 수 있습니다. 따라서 Docker 이미지에 entrypoint가 정의되어 있으면 이 명령이 해당 entrypoint의 인수가 됩니다. 예시:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: ubuntu
    command: echo "I am at your command!"
EOF
$ docker-compose up
Creating network "mediumcom_default" with the default driver
Creating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | I am at your command!
mediumcom_first_1 exited with code 0
```

아주 간단하지요?

## entrypoint

<div class="content-ad"></div>

이름에서 알 수 있듯이 해당 기능은 컨테이너의 엔트리포인트를 재정의하게 해줍니다. 예를 들어, 컨테이너가 echo 실행 파일로 실행되도록 하려면 다음과 같이 작성할 수 있어요:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: ubuntu
    entrypoint: ["echo"]
    command: "I am just an arg"
EOF
$ docker-compose up
Creating network "mediumcom_default" with the default driver
Creating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | I am just an arg
mediumcom_first_1 exited with code 0
```

간단하지요? 그리고 command 대 entrypoint 등에 대해 궁금해 한다면 당신만이 아닙니다. 이것은 매우 일반적인 혼란 포인트입니다. 다른 기사 "전부 다: Dockerfiles"에서 이에 대해 이야기했어요. 간단히 말해서, 저는 이 두 옵션을 조율할 때 필요한 치트 시트를 제공합니다: https://docs.docker.com/engine/reference/builder/#understand-how-cmd-and-entrypoint-interact

## cap_add

<div class="content-ad"></div>

만약 컨테이너에 추가 기능이 필요한 경우, 다음과 같이 추가할 수 있어요:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: ubuntu:14.04
    command: ip link add dummy0 type dummy
    cap_add:
     - NET_ADMIN
EOF
$ docker-compose up
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
mediumcom_first_1 exited with code 0
```

이제 위와 같은 컨테이너를 실행하려고 하지만 기능이 없는 경우, 에러가 발생할 거예요:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: ubuntu:14.04
    command: ip link add dummy0 type dummy
EOF
$ docker-compose up
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | RTNETLINK answers: Operation not permitted
mediumcom_first_1 exited with code 2
```

<div class="content-ad"></div>

## cap_drop

cap_add의 정 반대입니다. 컨테이너에서 기능을 제거하는 데 사용됩니다. 예를 들어 슈퍼 권한이 부여된 컨테이너가 있다고 가정해보겠습니다:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: ubuntu:14.04
    command: ip link add dummy0 type dummy
    cap_add:
     - ALL
EOF
$ docker-compose up
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
mediumcom_first_1 exited with code 0
```

그러나 호스트 네트워크 스택을 관리하려고 하지 않는다면:

<div class="content-ad"></div>

```sh
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: ubuntu:14.04
    command: ip link add dummy0 type dummy
    cap_add:
     - ALL
    cap_drop:
     - NET_ADMIN
EOF
$ docker-compose up
mediumcom_first_1 컨테이너를 다시 생성하는 중... 완료
mediumcom_first_1에 연결 중
first_1 | RTNETLINK 답변: 작업을 수행할 수 없습니다
mediumcom_first_1은 코드 2로 종료됨
```

## cgroup_parent

cgroup에 대해 처음이라면 이곳에 관한 짧은 좋은 기사가 있습니다. 간단히 말해, Linux 커널은 제어 그룹 (cgroups)을 통해 프로세스별로 시스템 리소스의 사용량을 엄격하게 제어할 수 있는 기능을 제공합니다. Docker에서는 이를 통해 컨테이너가 특정 리소스를 얼마나 사용할 수 있는지 제어할 수 있습니다. 기본적으로 컨테이너는 가능한 많은 리소스를 사용하려고 시도할 것입니다:

```sh
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: ubuntu
    command: sleep 30
EOF
$ docker-compose up -d 
기본 드라이버로 네트워크 "root_default"를 생성 중
root_first_1 컨테이너 생성 중... 완료
$ docker inspect root_first_1 --format '{.HostConfig.CgroupParent}'
$
```

<div class="content-ad"></div>

보시는 대로, 기본적으로 컨테이너에 할당된 제어 그룹이 없습니다. 하지만 우리가 할당할 수 있습니다. 심지어 우리 자신의 것을 할당할 수도 있습니다:

```js
$ cgcreate -g cpu:test-cgroup-medium
$ ls /sys/fs/cgroup/cpu
cgroup-limit           cgroup.sane_behavior  cpu.shares    cpuacct.usage         cpuacct.usage_percpu_sys   cpuacct.usage_user  release_agent  test-cgroup-medium
cgroup.clone_children  cpu.cfs_period_us     cpu.stat      cpuacct.usage_all     cpuacct.usage_percpu_user  docker              system.slice   user.slice
cgroup.procs           cpu.cfs_quota_us      cpuacct.stat  cpuacct.usage_percpu  cpuacct.usage_sys          notify_on_release   tasks
```

그리고 이 옵션을 사용함으로써 이 컨테이너에서 CPU 사용량을 엄격하게 제어할 수 있게 될 것입니다. cgroup "cpu:test-cgroup-medium"을 통해:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: ubuntu
    command: sleep 30
    cgroup_parent: "cpu:test-cgroup-medium"
EOF
$ docker-compose up -d 
Recreating root_first_1 ... done
$ docker inspect root_first_1 --format '{.HostConfig.CgroupParent}'
cpu:test-cgroup-medium
$
```

<div class="content-ad"></div>

## container_name

이 옵션에 대해 로켓 과학같은 것은 없습니다. 이 옵션은 Docker Compose 배포에서 생성된 컨테이너의 이름을 고정하는 데 사용됩니다.

이전에 언급한 바와 같이, 기본적으로 Docker Compose는 컨테이너에 접미사를 추가하며 이는 Docker Compose 프로젝트의 이름과 일치합니다 (프로젝트 이름을 명시적으로 지정하지 않는 경우 Docker Compose가 현재 경로를 기본값으로 삼습니다).

따라서 Docker Compose의 이 동작을 재정의하고 생성된 컨테이너에 자체적으로 정의한 사용자 정의 이름을 지정하려면 container_name을 사용할 수 있습니다. 예시:

<div class="content-ad"></div>

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: ubuntu
    container_name: my-own-container-name
EOF
$ docker-compose up
기본 드라이버로 "mediumcom_default" 네트워크 생성 중
my-own-container-name 생성 완료
my-own-container-name에 연결 중
my-own-container-name에서 코드 0으로 종료됨
```

P.S.: 네, 궁금해하실 수도 있지만, 이 Docker Compose 배포 내의 다른 컨테이너들은 컨테이너 이름에 의해 서로에게 도달할 수 있습니다. 예:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: sleep 5
    container_name: my-own-container-name
  second:
    image: alpine
    command: ping -c 1 my-own-container-name
EOF
$ docker-compose up
my-own-container-name을 다시 생성 중... 완료
mediumcom_second_1 생성 중... 완료
mediumcom_second_1, my-own-container-name에 연결 중
second_1  | my-own-container-name(172.18.0.3) 핑 중: 56 data bytes
second_1  | 64 bytes from 172.18.0.3: seq=0 ttl=64 time=0.743 ms
second_1  | 
second_1  | --- my-own-container-name 핑 통계 ---
second_1  | 전송된 패킷: 1, 수신된 패킷: 1, 패킷 손실: 0%
second_1  | 왕복 최소/평균/최대 시간 = 0.743/0.743/0.743 ms
mediumcom_second_1이 코드 0으로 종료됨
my-own-container-name이 코드 0으로 종료됨
```

## depends_on

<div class="content-ad"></div>

먼저 말하자면, 아니요, 이 옵션은 컨테이너 애플리케이션의 시작 순서를 제어할 수 있게 해주지 않아요! 많은 사람들이 하는 실수죠. 하지만 생각해보세요… 당신의 컨테이너는 내부에서 하나 이상의 프로세스를 실행하고 있기 때문에, Docker(컨테이너를 관리하는 주체)가 해당 컨테이너 내부에서 실행 중인 애플리케이션/프로세스의 비즈니스 로직을 완벽히 이해하고 있다고 가정하는 것은 순진한 판단일 거예요.

이것은 미묘한 차이가 있지만, 이 옵션이 하는 일은 대신에 Docker Compose에게 컨테이너를 시작하고 중지할 때의 순서를 명시하는 것뿐이에요.

예시:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    depends_on:
     - second
  second:
    image: alpine
    depends_on:
     - first
EOF
$ docker-compose up
Creating network "mediumcom_default" with the default driver
Creating mediumcom_second_1 ... done
Creating mediumcom_first_1  ... done
Attaching to mediumcom_second_1, mediumcom_first_1
mediumcom_second_1 exited with code 0
mediumcom_first_1 exited with code 0
```

<div class="content-ad"></div>

봐요! 두 번째에 의존하는 첫 번째 컨테이너가 먼저 시작되므로, 두 번째 컨테이너가 먼저 시작됩니다. 그리고 멈출 때도 마찬가지 — 먼저 의존하는 첫 번째가 있기 때문에 두 번째가 마지막으로 내려갑니다!

```js
$ docker-compose down
mediumcom_first_1  ... 삭제됨
mediumcom_second_1 ... 삭제됨
mediumcom_default 네트워크 제거됨
```

추신: 만약 혼돈을 즐긴다면, 두 번째도 첫 번째에 의존하도록 만들었다면 어떻게 될까요? 😝 걱정 마세요, Docker는 똑똑해서 다음과 같은 메시지를 보여줄 거예요:

## 볼륨

<div class="content-ad"></div>

도커 볼륨에 대해 자세히 설명하지 않을 거예요. 이 주제는 꽤 크거든요. 단지 컨테이너 애플리케이션에서 데이터를 관리할 수 있는 4가지 방법이 있다는 것만 말씀드릴게요:

- 컨테이너의 쓰기 가능한 레이어 내에 모든 데이터를 유지합니다 (볼륨 없음)
- 호스트와 컨테이너 사이에 마운트 지점을 만듭니다 (바인드 마운트)
- 컨테이너의 수명주기와 독립적으로 데이터를 호스팅하고 관리하는 전용 도커 리소스를 생성합니다 (볼륨)
- 성능을 최적화하기 위해 데이터를 영구 저장하지 않고 메모리에 유지합니다 (tmpfs).

이렇게 말씀드린 바와 같이 Compose 파일에서 이러한 타입의 볼륨을 모두 관리할 수 있어요. 지금까지 모든 예제는 컨테이너의 쓰기 가능한 레이어를 사용했지만, 나머지 3가지 데이터 관리 옵션을 다음과 같이 사용할 수 있어요:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: ls /
    volumes:
     - /:/my-hostfs
     - my-volume:/my-volume
     - type: tmpfs
       target: /my-tmpfs
volumes:
  my-volume:
EOF
$ docker-compose up
Creating network "test_default" with the default driver
Creating volume "test_my-volume" with default driver
Creating test_first_1 ... done
Attaching to test_first_1
first_1  | bin
first_1  | dev
first_1  | etc
first_1  | home
first_1  | lib
first_1  | media
first_1  | mnt
first_1  | my-hostfs
first_1  | my-tmpfs
first_1  | my-volume
first_1  | opt
first_1  | proc
first_1  | root
first_1  | run
first_1  | sbin
first_1  | srv
first_1  | sys
first_1  | tmp
first_1  | usr
first_1  | var
test_first_1 exited with code 0
```

<div class="content-ad"></div>

여기에 세 가지 다른 유형의 마운트가 한꺼번에 나왔어요. 그런데 알아두셨나요? 제가 3가지 다른 구문을 사용했어요... 선호하는 것이나 마운트에 가장 적합한 것을 선택할 수 있어요 (여기에서 더 자세히 읽을 수 있어요).

추가로, tmpfs도 전용 옵션을 통해 설정할 수 있어요... 예제를 보려면 다음 섹션을 읽어보세요.

## tmpfs

이미 이전 섹션에서 설명했지만, 알아두시면 좋아요. 이는 컨테이너를 위한 비영구적 마운트 포인트를 설정하는 또 다른 간단한 방법일 뿐이에요:

<div class="content-ad"></div>

```sh
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: ls /
    tmpfs: /my-tmpfs
EOF
$ docker-compose up
mediumcom_first_1 컨테이너 다시 생성 중... 완료
mediumcom_first_1에 첨부 중
first_1  | bin
first_1  | dev
first_1  | etc
first_1  | home
first_1  | lib
first_1  | media
first_1  | mnt
first_1  | my-tmpfs
first_1  | opt
first_1  | proc
first_1  | root
first_1  | run
first_1  | sbin
first_1  | srv
first_1  | sys
first_1  | tmp
first_1  | usr
first_1  | var
mediumcom_first_1가 코드 0으로 종료됨
```

## 디바이스

그 이름에서 알 수 있듯이, 이 옵션은 컨테이너가 시스템 디바이스를 관리하기 위한 액세스 권한을 부여합니다. 이 것이 일반적인 바인드-마운트와 어떻게 다른지 궁금할 것입니다. 단순히 디바이스를 컨테이너에 바인드-마운트할 수 있지 않을까요? 음, 그렇고 그렇지 않습니다... 컨테이너가 해당 디바이스를 어떻게 사용하는지에 따라 다릅니다. 디바이스 파일은 일반 파일 및 디렉토리와 약간 다르며 (/dev 폴더 아래에서 일반 파일처럼 보이지만), 디바이스 파일은 디바이스 드라이버 및 물리 디바이스로의 인터페이스를 제공하므로 더 세분화된 액세스 제어가 있습니다. 이는 cgroups를 통해 관리되며(여기에서 더 읽을 수 있습니다).

요약하면, 컨테이너가 특권 모드에서 실행되지 않는 경우에도 이 옵션을 통해 특정 디바이스 파일에 대한 컨테이너 액세스를 부여할 수 있습니다.


<div class="content-ad"></div>

우리 컴퓨터에 웹캠이 연결되어 있다고 가정해 봅시다.

```bash
$ lsusb -s 001:003
Bus 001 Device 003: ID 046d:0826 Logitech, Inc. HD Webcam C525
```

그리고 컨테이너 내부에서 웹캠을 사용하여 사진을 찍고 싶어합니다. 다음 시나리오를 고려해보세요:

- 먼저, 옵션을 적용하지 않고 컨테이너 내부에서 웹캠에 액세스해보겠습니다.

<div class="content-ad"></div>


```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: ccordeiro/fswebcam:armv7l
    command: fswebcam -r 640x480 --jpeg 85 -D 1 picture.jpg
EOF
$ docker-compose up
네트워크 "tmp_default"를 기본 드라이버로 생성중
tmp_first_1 를 생성중... 완료
tmp_first_1 에 연결중
first_1  | --- /dev/video0 열기 중...
first_1  | stat: 해당 파일 또는 디렉터리가 없습니다
first_1  | tmp_first_1이 코드 0으로 종료되었습니다
```

…그러나 분명히 컨테이너에는 어떤 장치도 보이지 않습니다…

- 두 번째로, 장치 파일을 바인드 마운트해 봅시다

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: ccordeiro/fswebcam:armv7l
    command: fswebcam -r 640x480 --jpeg 85 -D 1 picture.jpg
    volumes:
     - /dev/video0:/dev/video0
EOF
$ docker-compose up
tmp_first_1 재생성중... 완료
tmp_first_1 에 연결중
first_1  | --- /dev/video0 열기 중...
first_1  | v4l2 모듈 시도 중...
first_1  | 기기 열기 실패: /dev/video0
first_1  | 열기: 작업을 허용하지 않습니다
first_1  | v4l1 모듈 시도중...
first_1  | 기기 열기 실패: /dev/video0
first_1  | 열기: 작업을 허용하지 않습니다
first_1  | /dev/video0을 읽을 수 있는 소스 모듈을 찾을 수 없습니다.
first_1  | tmp_first_1이 코드 0으로 종료되었습니다
```

<div class="content-ad"></div>

…음, 컨테이너는 보이지만 접근할 수 있는 권한이 없네요.

- 마지막으로 장치를 사용해 봅시다

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: ccordeiro/fswebcam:armv7l
    command: fswebcam -r 640x480 --jpeg 85 -D 1 picture.jpg
    devices:
     - /dev/video0:/dev/video0
EOF
$ docker-compose up
Recreating tmp_first_1 ... done
Attaching to tmp_first_1
first_1  | --- Opening /dev/video0...
first_1  | Trying source module v4l2...
first_1  | /dev/video0 opened.
first_1  | No input was specified, using the first.
first_1  | Delaying 1 seconds.
first_1  | --- Capturing frame...
first_1  | Captured frame in 0.00 seconds.
first_1  | --- Processing captured image...
first_1  | Setting output format to JPEG, quality 85
first_1  | Writing JPEG image to 'picture.jpg'.
first_1  | tmp_first_1 exited with code 0
```

그리고 성공입니다!

<div class="content-ad"></div>

## network_mode

Docker의 네트워킹 시스템은 네트워킹 드라이버에 기반하고 있는 것을 알고 계실 것입니다. 이 옵션을 사용하여 컨테이너가 실행되어야 하는 네트워크 모드를 지정할 수 있습니다.

예를 들어, 기본 브릿지 네트워크에 1개의 컨테이너를, 호스트 네트워크에 1개의 컨테이너를, 첫 번째 컨테이너와 동일한 네트워크에 1개의 컨테이너를, 그리고 어떤 네트워크도 없는 상태로 1개의 컨테이너를 배포하려고 합니다. 아래는 이를 수행하는 방법입니다:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: sh -c 'ifconfig eth0 && sleep 5'
    network_mode: bridge
  second:
    image: alpine
    command: ifconfig eth0
    network_mode: host
  third:
    image: alpine
    command: ifconfig eth0
    depends_on:
     - first
    network_mode: service:first
  fourth:
    image: alpine
    command: ifconfig eth0
    network_mode: none
EOF
$ docker-compose up
Creating mediumcom_first_1  ... done
Creating mediumcom_fourth_1 ... done
Creating mediumcom_second_1 ... done
Creating mediumcom_third_1  ... done
Attaching to mediumcom_second_1, mediumcom_fourth_1, mediumcom_first_1, mediumcom_third_1
first_1   | eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:02  
first_1   |           inet addr:172.17.0.2  Bcast:172.17.255.255  Mask:255.255.0.0
...
fourth_1  | ifconfig: eth0: error fetching interface information: Device not found
...
second_1  | eth0      Link encap:Ethernet  HWaddr 02:50:00:00:00:01  
second_1  |           inet addr:192.168.65.3  Bcast:192.168.65.255  Mask:255.255.255.0
...
third_1   | eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:02  
third_1   |           inet addr:172.17.0.2  Bcast:172.17.255.255  Mask:255.255.0.0
...
mediumcom_fourth_1 exited with code 1
mediumcom_second_1 exited with code 0
mediumcom_third_1 exited with code 0
mediumcom_first_1 exited with code 0
```

<div class="content-ad"></div>

그래서, 무슨 일이 있었나요? 이를 간단히 살펴보겠습니다:

- 첫 번째 컨테이너는 브리지 모드로 들어가서 자체 eth0 개인 네트워크 인터페이스를 할당 받았습니다(IP 172.17.0.2로, Docker 브리지 서브넷의 일부입니다).
- 두 번째 컨테이너는 호스트 모드로 들어가서 호스트와 동일한 네트워크 수준에서 실행되므로 호스트의 모든 네트워킹 인터페이스를 볼 수 있습니다.
- 세 번째 컨테이너는 첫 번째 것과 동일한 네트워크를 받았습니다.
- 네 번째 컨테이너는 네트워크가 없어 eth0 인터페이스를 찾지 못하여 실패했습니다.

## 네트워크

이제 위에서 언급한 옵션을 활용하여, 컨테이너를 사용자 정의 네트워크에 연결하려면 어떻게 해야 하나요(기존 네트워크 또는 새로 생성한 것 중)? 그럴 때는 networks를 사용하세요:

<div class="content-ad"></div>

```js
$ docker network create cjdc-test
0f5e35bf6b8d396c45c19545540957fbc5fa15ba1ca0e5fbca4add3802c7db6d
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: sleep 30
    networks:
     - new-net
  second:
    image: alpine
    command: sleep 30
    networks:
     - cjdc-test
networks:
  new-net:
  cjdc-test:
    external: true
    name: cjdc-test
EOF
$ docker-compose up -d
Creating network "mediumcom_new-net" with the default driver
Creating mediumcom_second_1 ... done
Creating mediumcom_first_1  ... done
$ docker inspect --format '{.NetworkSettings.Networks}' mediumcom_first_1
map[mediumcom_new-net:0xc0004d8f00]
$ docker inspect --format '{.NetworkSettings.Networks}' mediumcom_second_1
map[cjdc-test:0xc0003e8f00]
```

## dns

It basically lets you define a custom DNS server for your container. Example:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: cat /etc/resolv.conf
    dns: 1.2.3.4
    network_mode: bridge
EOF
$ docker-compose up
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | nameserver 1.2.3.4
mediumcom_first_1 exited with code 0
```

<div class="content-ad"></div>

주의: 사용자 정의 네트워크에서는 DNS 옵션이 기본적으로 동작하지 않으므로 브릿지 네트워크가 필요합니다.

## dns_search

여기에서 사용자 맞춤 DNS 검색 도메인을 설정할 수 있습니다.

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: cat /etc/resolv.conf
    dns_search: my.custom.dns
    network_mode: bridge
EOF
$ docker-compose up 
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | search my.custom.dns
first_1  | nameserver 192.168.65.5
mediumcom_first_1 exited with code 0
```  

<div class="content-ad"></div>

## env_file

일반적으로 컨테이너 환경을 하나씩 설정하는 데 익숙할 수 있지만, 한 번에 이 작업을 수행하도록 대신 파일을 사용할 수 있습니다:

```js
$ cat >my-env <<EOF
VAR1=value
MY_VAR_FROM_FILE=1
FOO=BAR
EOF
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: env
    env_file:
     - my-env
EOF
$ docker-compose up 
mediumcom_first_1 다시 만들기 ... 완료
mediumcom_first_1에 연결
first_1  | PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
first_1  | HOSTNAME=157e34e4fc64
first_1  | VAR1=value
first_1  | MY_VAR_FROM_FILE=1
first_1  | FOO=BAR
first_1  | HOME=/root
mediumcom_first_1 코드 0으로 종료
```

## environment

<div class="content-ad"></div>

이를 통해 컴포즈 파일에서 컨테이너의 환경 변수를 하나씩 직접 설정할 수 있습니다.

이 방법을 사용하여 env_file 옵션을 통해 로드되는 변수를 재정의할 수도 있습니다.

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: env
EOF
$ docker-compose up 
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
first_1  | HOSTNAME=68e909013198
first_1  | NEW_VAR=yes
first_1  | HOME=/root
mediumcom_first_1 exited with code 0
``` 

## 노출

<div class="content-ad"></div>

도커파일과 유사하게, 이 옵션은 컨테이너의 포트를 표시하고 다른 연결된 컨테이너에서 사용할 수 있도록 합니다.

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: sleep 20
    expose:
     - 9876
EOF
$ docker-compose up -d
Recreating mediumcom_first_1 ... done
$ docker ps
CONTAINER ID   IMAGE     COMMAND      CREATED          STATUS          PORTS      NAMES
3be377f4bf6c   alpine    "sleep 20"   18 seconds ago   Up 16 seconds   9876/tcp   mediumcom_first_1
```

## external_links

옵션을 건너뛰어야 합니다. 왜냐하면 1) 이것은 레거시 옵션이기 때문에, 그리고 2) 동일한 결과를 얻기 위해 네트워크를 사용할 수 있습니다(즉, 컨테이너를 외부 네트워크에 포함되어 있지 않은 다른 컨테이너에 연결할 수 있습니다).

<div class="content-ad"></div>

## extra_hosts

이 기능은 컨테이너의 /etc/hosts 파일에 새 항목을 추가할 수 있게 해줍니다. 예를 들어, 컨테이너가 "localhost"가 아닌 다른 이름으로 스스로를 호출하도록 하고 싶다면 다음과 같이 할 수 있습니다:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: sh -c 'cat /etc/hosts; ping me -c 1'
    extra_hosts:
     - "myself:127.0.0.1"
     - "me:127.0.0.1"
EOF
$ docker-compose up 
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | 127.0.0.1 localhost
first_1  | ::1 localhost ip6-localhost ip6-loopback
first_1  | fe00::0 ip6-localnet
first_1  | ff00::0 ip6-mcastprefix
first_1  | ff02::1 ip6-allnodes
first_1  | ff02::2 ip6-allrouters
first_1  | 127.0.0.1 myself
first_1  | 127.0.0.1 me
first_1  | 172.22.0.2 01d566c4380a
first_1  | PING me (127.0.0.1): 56 data bytes
first_1  | 64 bytes from 127.0.0.1: seq=0 ttl=64 time=1.192 ms
first_1  | 
first_1  | --- me ping statistics ---
first_1  | 1 packets transmitted, 1 packets received, 0% packet loss
first_1  | round-trip min/avg/max = 1.192/1.192/1.192 ms
mediumcom_first_1 exited with code 0
```

## healthcheck

<div class="content-ad"></div>

당연히, 이것은 도커 파일 옵션과 매우 유사하게 작동합니다. 도커가 컨테이너가 "healthy(건강한)"인지 아닌지 평가하는 데 사용할 수 있는 사용자 정의 확인을 만들 수 있습니다.

그러니까, /tmp에 medium.txt라는 파일이 있을 때만 우리 컨테이너가 건강한 것으로 간주됩니다.

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: sh -c 'touch /tmp/NOT_MY_FILE; sleep 10'
    healthcheck:
      test: "[ -f /tmp/medium.txt ]"
      # run asap
      interval: 1s
      timeout: 5s
      retries: 1
      start_period: 0s
  second:
    image: alpine
    command: sh -c 'touch /tmp/medium.txt; sleep 10'
    healthcheck:
      test: "[ -f /tmp/medium.txt ]"
      # run asap
      interval: 1s
      timeout: 5s
      retries: 1
      start_period: 0s
EOF
$ docker-compose up -d
Recreating mediumcom_first_1 ... done
Creating mediumcom_second_1  ... done
$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS                     PORTS     NAMES
b8804cbb440d   alpine    "sh -c 'touch /tmp/N…"   4 seconds ago   Up 2 seconds (unhealthy)             mediumcom_first_1
604a6880f3c6   alpine    "sh -c 'touch /tmp/m…"   4 seconds ago   Up 2 seconds (healthy)               mediumcom_second_1
```

그런데 여기서 볼 수 있듯이, 처음 것은 건강하지 않지만 두 번째는 건강하다고 나옵니다. 필요한 파일이 올바른 위치에 존재하기 때문입니다.

<div class="content-ad"></div>

## 초기화

이를 통해 Docker에게 컨테이너에 대한 init를 사용할지 여부를 알릴 수 있습니다. 참일 경우, Docker는 tini 기반의 docker-init이라는 init을 사용할 것입니다. 차이를 살펴봅시다:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: ps -a
    init: false
EOF
$ docker-compose up 
mediumcom_first_1 다시 생성 중... 완료
mediumcom_first_1에 참여 중
first_1  | PID   USER     TIME  COMMAND
first_1  |     1 root      0:00 ps -a
mediumcom_first_1은 코드 0으로 종료됨
```

그러나 초기화를 활성화하면 이와 같은 프로세스 래퍼를 얻게 되며, 이는 PID 1로 추가 기능이 있는 경우 (이 경우에는 docker-init)입니다:

<div class="content-ad"></div>


$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: ps -a
    init: true
EOF
$ docker-compose up
Starting mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | PID   USER     TIME  COMMAND
first_1  |     1 root      0:00 /sbin/docker-init -- ps -a
first_1  |     8 root      0:00 ps -a
mediumcom_first_1 exited with code 0


## 격리

리눅스의 경우, 이 옵션은 항상 기본값으로 설정됩니다. 윈도우의 경우 더 많은 옵션이 있지만, 아쉽게도 여러분을 위한 예제를 실행할 윈도우 머신이 없습니다 😛 윈도우 사용자분들 죄송해요!

## 레이블


<div class="content-ad"></div>

콘테이너에 레이블을 설정할 수 있는 이 옵션의 이름처럼:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: sleep 10
    labels:
     - "foo=bar"
EOF
$ docker-compose up -d
Recreating mediumcom_first_1 ... done
$ docker ps --filter 'label=foo=bar'
CONTAINER ID   IMAGE     COMMAND      CREATED          STATUS         PORTS     NAMES
94e844176d76   alpine    "sleep 10"   30 seconds ago   Up 7 seconds             mediumcom_first_1
```

## 링크

외부 링크에 대한 내용은 과거의 옵션을 건너뛰겠습니다. 컨테이너 간 통신은 사용자 정의 네트워크로 쉽게 설정할 수 있습니다.

<div class="content-ad"></div>

## 로깅

실제로 이 옵션이 매우 중요하다고 생각해요. 대부분의 사용자들은 보통 컨테이너 애플리케이션 로그의 크기가 계속해서 커지고 있다는 사실에 충분한 주의를 기울이지 않습니다. 호스트 공간이 시간이 지남에 따라 가득 차지 않기를 원한다면, 다른 애플리케이션과 마찬가지로, 애플리케이션이 로그를 기록하는 방식도 제어해야 합니다. Docker를 통해 이 로깅 옵션을 사용하여 컨테이너 애플리케이션의 stdout 로그를 제어할 수 있습니다. 로깅 종류 뿐만 아니라 로깅 크기 제한 및 로테이션 매개변수도 제어할 수 있습니다:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: sh -c 'while true; do echo `date`; done'
    logging:
      options:
        max-size: "50k"
        max-file: "10"
EOF
$ docker-compose up -d
tmp_first_1을 다시 생성 중... 완료됨
$ docker ps -f 'name=tmp_first_1'
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
2ea63dd32607   alpine    "sh -c 'while true; …"   2 minutes ago   Up 2 minutes             tmp_first_1
$ 
$ du -h /var/lib/docker/containers/2ea63dd32607*/*log*
20K /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log
52K /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.1
52K /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.2
52K /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.3
52K /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.4
52K /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.5
52K /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.6
52K /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.7
52K /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.8
52K /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.9
```

그러니까 이 예제에서 볼 수 있듯이, Docker는 최대 10개의 로그 파일만 유지하고, 그 크기가 우리가 원하는 50 Kibibytes(= 52K)를 초과하지 않는 것을 확인할 수 있어요.

<div class="content-ad"></div>

## PID

요청 시, 컨테이너가 호스트와 PID 주소 공간을 공유할 수 있도록 설정할 수 있습니다. 이를 가능하게 하는 옵션은 다음과 같습니다:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: ps -a
    pid: "host"
  second:
    image: alpine
    command: ps -a
EOF
$ docker-compose up 
Creating network "mediumcom_default" with the default driver
Creating mediumcom_second_1 ... done
Creating mediumcom_first_1  ... done
Attaching to mediumcom_second_1, mediumcom_first_1
second_1  | PID   USER     TIME  COMMAND
second_1  |     1 root      0:00 ps -a
first_1   | PID   USER     TIME  COMMAND
first_1   |     1 root      0:07 /sbin/init
first_1   |     2 root      0:00 [kthreadd]
first_1   |     3 root      0:00 [rcu_gp]
first_1   |     4 root      0:00 [rcu_par_gp]
first_1   |     6 root      0:00 [kworker/0:0H-ev]
first_1   |     8 root      0:00 [mm_percpu_wq]
first_1   |     9 root      0:00 [rcu_tasks_rude_]
first_1   |    10 root      0:00 [rcu_tasks_trace]
first_1   |    11 root      0:22 [ksoftirqd/0]
first_1   |    12 root      0:10 [rcu_sched]
first_1   |    13 root      0:07 [migration/0]
first_1   |    15 root      0:00 [cpuhp/0]
first_1   |    16 root      0:00 [cpuhp/1]
...
```

이것은 컨테이너 내에서 호스트 프로세스를 관리하고(포함하여 종료)할 수 있다는 의미이므로 이 옵션을 사용할 때 주의해야 합니다.

<div class="content-ad"></div>

## 포트

가장 인기 있는 옵션 중 하나인 이 기능을 사용하면 컨테이너 내부의 포트를 호스트로 게시할 수 있습니다 (컨테이너가 이미 "호스트" 모드에서 실행 중이지 않은 경우).

예를 들어, 다음과 같이 게시하려는 경우를 생각해 봅시다:
- 포트 3000을 임의의 포트로
- 포트 범위 3005부터 3010까지
- 로컬호스트의 포트 80을 포트 80으로만
- 모든 호스트 인터페이스의 포트 443을 포트 443으로
- 호스트의 포트 3011을 UDP만을 사용하여 포트 9876로

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: sleep 10
    ports:
     - 3000
     - 3005-3010
     - 127.0.0.1:80:80
     - 443:443
     - 9876:3011/udp
EOF
$ docker-compose up -d
중복 컨테이너 "mediumcom_second_1"를 제거하는 중
mediumcom_first_1이 시작되었습니다. ... 완료
$ docker ps --format '{.Ports}'
127.0.0.1:80->80/tcp, 0.0.0.0:443->443/tcp, 0.0.0.0:61290->3000/tcp, 0.0.0.0:61292->3005/tcp, 0.0.0.0:61293->3006/tcp, 0.0.0.0:61294->3007/tcp, 0.0.0.0:61295->3008/tcp, 0.0.0.0:61296->3009/tcp, 0.0.0.0:61291->3010/tcp, 0.0.0.0:9876->3011/udp
```

<div class="content-ad"></div>

## 프로필

"프로필"은 컴포즈 파일 내에서 역할을 분리할 수 있는 멋진 제어 메커니즘입니다. 우리가 컴포즈 파일 내에서 일부 컨테이너가 매우 특정한 설정하에서만 실행되거나 필요할 때만 실행되도록 원한다고 가정해 봅시다 (즉, "프로필"이라고도 함). 이것이 할 수 있는 것입니다:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: nginx
  second:
    image: alpine
    command: wget http://first
    profiles: ["debug"]
EOF
$ docker-compose up -d
네트워크 "mediumcom_default"를 기본 드라이버로 생성 중
mediumcom_first_1 생성 완료
$ # 보세요? nginx 컨테이너만 시작되었습니다. 이제 "debug"를 활성화해 봅시다
$ docker-compose --profile debug up 
mediumcom_first_1 is up-to-date
mediumcom_second_1 생성 완료
mediumcom_first_1, mediumcom_second_1에 첨부
second_1  | first(172.26.0.2:80)에 연결 중
second_1  | 'index.html' 저장 중
second_1  | index.html           100% |********************************|   615  0:00:00 남음
second_1  | 'index.html' 저장됨
first_1   | /docker-entrypoint.sh: /docker-entrypoint.d/가 비어 있지 않으므로 구성을 시도할 것입니다
(first_이하 생략)
mediumcom_second_1이 코드 0으로 종료됨
```

## 재시작

<div class="content-ad"></div>

컨테이너의 재시작 정책을 정의하는 방법입니다 (no, always, unless-stopped, on-failure 중 하나를 선택).

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    restart: on-failure
    command: sleep 4
  second:
    image: alpine
    command: echo Hi `date`
    restart: always
EOF
$ docker-compose up 
Creating network "mediumcom_default" with the default driver
Creating mediumcom_first_1  ... done
Creating mediumcom_second_1 ... done
Attaching to mediumcom_first_1, mediumcom_second_1
second_1  | Hi Tue Feb 1 17:21:11 CET 2022
mediumcom_second_1 exited with code 0
second_1  | Hi Tue Feb 1 17:21:11 CET 2022
second_1  | Hi Tue Feb 1 17:21:11 CET 2022
mediumcom_second_1 exited with code 0
second_1  | Hi Tue Feb 1 17:21:11 CET 2022
second_1  | Hi Tue Feb 1 17:21:11 CET 2022
second_1  | Hi Tue Feb 1 17:21:11 CET 2022
mediumcom_second_1 exited with code 0
second_1  | Hi Tue Feb 1 17:21:11 CET 2022
second_1  | Hi Tue Feb 1 17:21:11 CET 2022
second_1  | Hi Tue Feb 1 17:21:11 CET 2022
second_1  | Hi Tue Feb 1 17:21:11 CET 2022
mediumcom_first_1 exited with code 0
```

## 보안 설정

이 옵션을 사용하면 컨테이너의 SELinux 레이블을 제어할 수 있습니다.

<div class="content-ad"></div>

```bash
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: sleep 30
    security_opt:
     - label:user:USER
  second:
    image: alpine
    command: sleep 30
EOF
$ docker inspect mediumcom_first_1 --format '{ .Id }: SecurityOpt={ .HostConfig.SecurityOpt }'
2808e4b2ac6a6870b372ecf06da5ce0217efdd6d14df16cef4b332e1d4fb9b6a: SecurityOpt=[label:user:USER]
$ 
$ docker inspect mediumcom_second_1 --format '{ .Id }: SecurityOpt={ .HostConfig.SecurityOpt }'
ed4c417b27257cc9f793d5d3dad94672074859dba90effddd0cde49f12dd4115: SecurityOpt=<no value>
```

## stop_grace_period

기본적으로 Docker는 SIGTERM을 수신한 후 10초 내에 컨테이너가 자연스럽게 중지되지 않으면 컨테이너를 강제로 종료합니다 (예: docker stop으로 컨테이너를 의도적으로 중지시킨 후). 이 시간 간격을 stop grace period라고 하며 구성할 수 있습니다.

```bash
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: sh -c 'while true; do sleep 5 & done'
  second:
    image: alpine
    command: sh -c 'while true; do sleep 5 & done'
    stop_grace_period: 1s
EOF
$ docker-compose up -d
Recreating mediumcom_first_1  ... done
Recreating mediumcom_second_1 ... done
$ time docker stop mediumcom_first_1
mediumcom_first_1
real 0m10.847s
user 0m0.205s
sys 0m0.092s
$ time docker stop mediumcom_second_1
mediumcom_second_1
real 0m1.883s
user 0m0.192s
sys 0m0.105s
```

<div class="content-ad"></div>

## stop_signal

앞서 언급한 옵션에서 SIGTERM이 도커 컨테이너를 중지하는 기본 신호임을 이미 알았을 것입니다. 그러나 여러분은 `docker stop`이 컨테이너 응용 프로그램으로 원하는 사용자 정의 신호를 전송하도록 변경할 수 있습니다:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: sleep 60
    stop_signal: SIGUSR1
EOF
$ docker-compose up -d
Creating network "mediumcom_default" with the default driver
Creating mediumcom_first_1 ... done
$ docker inspect mediumcom_first_1 --format '{.Config.StopSignal}'
SIGUSR1
```

## sysctls

<div class="content-ad"></div>

이 옵션은 컨테이너의 네임스페이스된 커널 매개변수를 런타임에서 구성할 수 있게 합니다.

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: sleep 60
    sysctls:
      net.core.somaxconn: 1024
      net.ipv4.tcp_syncookies: 0
EOF
$ docker-compose up -d
네트워크 "mediumcom_default" 생성 중, 기본 드라이버로 설정
mediumcom_first_1 생성 중... 완료
$ docker inspect mediumcom_first_1 --format '{json .HostConfig.Sysctls}'
{"net.core.somaxconn": "1024", "net.ipv4.tcp_syncookies": "0"}
```

## ulimits

컨테이너의 기본 ulimits를 재정의할 수 있게 합니다.

<div class="content-ad"></div>

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: sleep 60
    ulimits:
      nproc: 65535
      nofile:
        soft: 20000
        hard: 40000
EOF
$ docker-compose up -d
Recreating mediumcom_first_1 ... done
$ docker inspect mediumcom_first_1 --format '{json .HostConfig.Ulimits}'
[{"Name":"nproc","Hard":65535,"Soft":65535},{"Name":"nofile","Hard":40000,"Soft":20000}]
```

## userns_mode

너무 깊게 파고들지 않고 Docker 데몬에서 사용자 네임스페이스를 활성화한 경우, 단일 컨테이너에서 사용자 네임스페이스를 비활성화하게 하는 옵션입니다. `userns_mode: "host"`를 사용하면 됩니다.

## user


<div class="content-ad"></div>

컨테이너 응용프로그램이 특정 사용자로 실행되도록 하려면 어떻게 하시겠습니까?

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: whoami
    user: nobody
EOF
$ docker-compose up 
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | nobody
mediumcom_first_1 exited with code 0
```

## working_dir

도커 파일에서 본 적이 있을 것입니다. 이를 사용하면 컨테이너의 랜딩 경로(PWD)를 재정의할 수 있습니다. 이를 통해 엔트리포인트와 명령이 절대 경로를 사용하지 않는 경우에도 동작을 변경할 수 있습니다.

<div class="content-ad"></div>

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: pwd
    working_dir: /my/custom/medium/dir
EOF
$ docker-compose up 
mediumcom_first_1 컨테이너 다시 생성 중... 완료
mediumcom_first_1에 연결 중
first_1  | /my/custom/medium/dir
mediumcom_first_1 코드 0으로 종료됨
```

## 도메인 이름

당신의 컨테이너는 호스트명과 도메인명을 가지고 있다는 것을 아시죠. 호스트 모드가 아닐 때, 호스트명은 컨테이너의 ID와 동일하며, 도메인명은 예상대로 없을 것입니다. 도메인명을 설정하려면 다음과 같이 하세요:

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: hostname -f
    domainname: cristovaocordeiro.medium.com
EOF
$ docker-compose up
mediumcom_first_1 컨테이너 다시 생성 중... 완료
mediumcom_first_1에 연결 중
first_1  | 6e900c697939.cristovaocordeiro.medium.com
mediumcom_first_1 코드 0으로 종료됨
```

<div class="content-ad"></div>

## 호스트이름

당연히 도메인 이름을 설정할 수 있다면 컨테이너의 호스트 이름도 설정할 수 있습니다. 이렇게 하면 기본적으로 제공되는 "충격적인" ID 형식 대신 호스트 이름을 사용할 수 있습니다 😜

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: hostname -f
    hostname: test-container
EOF
$ docker-compose up 
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | test-container
mediumcom_first_1 exited with code 0
```

## ipc

<div class="content-ad"></div>

컨테이너의 프로세스 간 통신을 관리할 수 있습니다(성능 조정 목적). 이 옵션을 통해 컨테이너의 IPC 네임스페이스를 설정할 수 있습니다.

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: sleep 30
    ipc: shareable
EOF
$ docker inspect mediumcom_first_1 --format '{json .HostConfig.IpcMode}'
"shareable"
```

## mac_address

원하는 컨테이너에 사용자 정의 MAC 주소를 설정할 수도 있습니다.

<div class="content-ad"></div>

```js
$ cat >docker-compose.yml <<EOF
버전: "3"
서비스:
  first:
    이미지: alpine
    명령: ifconfig eth0
    mac_address: 00:ab:cd:12:34:56
EOF
$ docker-compose up 
mediumcom_first_1 컨테이너 다시 생성 중... 완료
mediumcom_first_1에 연결 중
first_1  | eth0 Link encap:Ethernet  HWaddr 00:AB:CD:12:34:56  
first_1  | inet addr:172.29.0.2 Bcast:172.29.255.255 Mask:255.255.0.0
first_1  | UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1
first_1  | RX packets:2 errors:0 dropped:0 overruns:0 frame:0
first_1  | TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
first_1  | collisions:0 txqueuelen:0 
first_1  | RX bytes:200 (200.0 B)  TX bytes:0 (0.0 B)
first_1  | 
mediumcom_first_1가 코드 0으로 종료됨
```

## 특권적

위에서 "특권적" 컨테이너 상태를 몇 번 다뤘습니다. 간단히 말하면, (!!) 확실히 확신이 있고 컨테이너에 가능한 모든 기능을 부여하고 싶다면 (따라서 호스트 장치 및 기타 자원에 액세스 할 수 있음), 이 옵션을 사용할 수 있습니다.

```js
$ cat >docker-compose.yml <<EOF
버전: "3"
서비스:
  first:
    이미지: alpine
    명령: sh -c 'ls /dev/ | wc -l'
  second:
    이미지: alpine
    명령: sh -c 'ls /dev/ | wc -l'
    특권적: true
EOF
$ docker-compose up 
mediumcom_first_1 컨테이너 다시 생성 중... 완료
mediumcom_second_1 컨테이너 다시 생성 중... 완료
mediumcom_first_1에 연결 중, mediumcom_second_1에 연결 중
first_1  | 15
second_1  | 150
mediumcom_first_1가 코드 0으로 종료됨
mediumcom_second_1가 코드 0으로 종료됨
```

<div class="content-ad"></div>

## 읽기 전용

컨테이너를 실행할 때 읽기 전용 이미지 레이어 위에 쓰기 가능한 레이어가 생성된다는 사실을 알고 계실 겁니다. 그러나 이 쓰기 가능한 레이어를 읽기 전용으로 바꿀 수도 있습니다.

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: touch /tmp/test
  second:
    image: alpine
    command: touch /tmp/test
    read_only: true
EOF
$ docker-compose up 
mediumcom_first_1  ... done
mediumcom_second_1 ... done
mediumcom_first_1, mediumcom_second_1에 연결 중
second_1  | touch: /tmp/test: 읽기 전용 파일 시스템
mediumcom_first_1 코드 0으로 종료
mediumcom_second_1 코드 1로 종료
```

## shm_size

<div class="content-ad"></div>

컨테이너가 사용할 수 있는 공유 메모리 양을 지정할 수 있습니다.

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    command: df -h /dev/shm
    shm_size: 123M
EOF
$ docker-compose up 
mediumcom_first_1 컨테이너를 다시 생성 중... 완료
mediumcom_first_1에 연결 중
first_1  | 파일 시스템                크기      사용함 사용 가능함 사용률 마운트된 위치
first_1  | shm                     123.0M         0    123.0M   0% /dev/shm
mediumcom_first_1은 코드 0으로 종료됨
```

## stdin_open

이는 docker run의 -i에 해당합니다. 이를 사용하여 호스트와 컨테이너 간 상호작용이 가능한 인터랙티브 세션을 만들 수 있습니다.

<div class="content-ad"></div>

```bash
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    stdin_open: true
EOF
$ docker-compose up -d
Recreating mediumcom_first_1 ... done
$ docker attach mediumcom_first_1 
echo Hello, I am sending commands to the container
Hello, I am sending commands to the container
exit
```

위와 같이 컨테이너와 상호 작용하고 있지만 호스트와 컨테이너 사이에 실제로 터미널 세션이 없습니다... 간단히 말해서 파이프일 뿐입니다.

## tty

이것은 도커 실행에서의 -t와 동등합니다. 컨테이너에 가상 tty를 할당하고 stdin_open과 결합할 때 유용합니다.


<div class="content-ad"></div>

```js
$ cat >docker-compose.yml <<EOF
version: "3"
services:
  first:
    image: alpine
    stdin_open: true
    tty: true
EOF
$ docker-compose up -d
mediumcom_first_1 컨테이너를 다시 생성합니다... 완료
$ docker attach mediumcom_first_1 
/ # echo now this is a real terminal
now this is a real terminal
/ # sleep 5
^C
/ # echo $?
130
/ # exit
```

이제 우리는 실제 터미널과 상호 작용하는 세션을 가지고 있습니다. 컨테이너 안에서 원하는 일을 할 수 있어요.

우와... 정말 긴 기사였죠. 죄송합니다. 밝은 면에서 말하면, 여러 기사를 넘나들며 다양한 컴포즈 파일 옵션이 무엇을 하는지 찾아보지 않아도 되겠죠?

스웜 모드에서 서비스에 특정한 옵션(변수 치환 및 확장 필드 같은 고유한 구조적 노하우)을 일부 뺐습니다. 이것들은 선택 사항이며, 다른 기사에서 다룰 수 있어요. 


<div class="content-ad"></div>

이 테이블 태그를 마크다운 형식으로 변경해드렸습니다.