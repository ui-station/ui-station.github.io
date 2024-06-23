---
title: "Docker Compose를 사용한 로컬 MongoDB Replica Set 설정 가이드 완벽한 방법"
description: ""
coverImage: "/assets/img/2024-06-23-TheonlylocalMongoDBreplicasetwithDockerComposeguideyoulleverneed_0.png"
date: 2024-06-23 22:55
ogImage: 
  url: /assets/img/2024-06-23-TheonlylocalMongoDBreplicasetwithDockerComposeguideyoulleverneed_0.png
tag: Tech
originalTitle: "The only local MongoDB replica set with Docker Compose guide you’ll ever need!"
link: "https://medium.com/workleap/the-only-local-mongodb-replica-set-with-docker-compose-guide-youll-ever-need-2f0b74dd8384"
---


![이미지](/assets/img/2024-06-23-TheonlylocalMongoDBreplicasetwithDockerComposeguideyoulleverneed_0.png)

이 블로그 포스트에서는 MongoDB 레플리카 세트를 로컬에서 실행할 수 있는 다양한 Docker Compose 설정을 탐색해보려고 합니다. 레플리카 세트는 MongoDB의 강력한 기능인 트랜잭션, 변경 스트림 또는 oplog에 액세스하는 것과 같은 것들을 활용하려는 사람들에게 필수적입니다. 로컬에서 MongoDB 레플리카 세트를 실행하면 이러한 기능에 액세스할 뿐만 아니라 복제 메커니즘 및 일반적인 오류 허용성을 실험할 수 있는 일회용 샌드박스로도 작용합니다. 더 이상 기다리지 말고 시작해 봅시다!

# 단일 노드 레플리카 세트 설정

첫 번째 설정은 몇 초만에 MongoDB 단일 노드 레플리카 세트를 실행할 수 있는 준비된 Docker Compose 파일입니다. 클라우드 환경에서는 고가용성과 오류 허용성을 보장하기 위해 복수 노드가 필요할 것입니다. 그러나 로컬 개발에는 단일 노드 레플리카 세트가 충분하며 트랜잭션 및 변경 스트림에 액세스할 수 있습니다. 이는 로컬에서 MongoDB 인스턴스를 실행하는 데 필요한 CPU 및 메모리 리소스를 줄여 Google Chrome을 더 행복하게 만들어줍니다. rs0라는 이름의 단일 노드 레플리카 세트를 실행하는 docker-compose.yml 파일은 다음과 같습니다:

<div class="content-ad"></div>

```js
version: "3.8"

services:
  mongo1:
    image: mongo:7.0
    command: ["--replSet", "rs0", "--bind_ip_all", "--port", "27017"]
    ports:
      - 27017:27017
    extra_hosts:
      - "host.docker.internal:host-gateway"
    healthcheck:
      test: echo "try { rs.status() } catch (err) { rs.initiate({_id:'rs0',members:[{_id:0,host:'host.docker.internal:27017'}]}) }" | mongosh --port 27017 --quiet
      interval: 5s
      timeout: 30s
      start_period: 0s
      start_interval: 1s
      retries: 30
    volumes:
      - "mongo1_data:/data/db"
      - "mongo1_config:/data/configdb"

volumes:
  mongo1_data:
  mongo1_config:
```

여기에서 무슨 일이 벌어지고 있는지 이해해보도록 합시다. 먼저, 우리는 이 글 작성 시 최신 MongoDB Community Edition 이미지인 mongo:7.0을 사용하고 있습니다. rs0라는 레플리카 셋 이름을 지정하기 위해 --replSet 플래그를 사용하고 있습니다. --bind_ip_all 플래그는 MongoDB 인스턴스를 모든 IPv4 주소에 바인딩하기 위해 사용되었으며, --port 플래그는 MongoDB 인스턴스가 수신 대기할 포트를 지정하기 위해 사용되었습니다. 27017은 MongoDB의 기본 포트입니다. 컨테이너 포트 27017을 호스트 포트 27017로 매핑하여 호스트 머신에서 MongoDB 인스턴스에 연결할 수 있도록 하고 있습니다. extra_hosts 섹션은 host.docker.internal 호스트 이름을 호스트 머신의 IP 주소에 매핑하는 데 사용되고 있습니다.

healthcheck 기능은 우리의 설정에서 레플리카 셋을 초기화하기 위해 재사용되었습니다. 레플리카 셋은 rs.initiate() 명령을 사용하여 초기화되어야 하며(이것은 replSetInitiate 데이터베이스 명령의 동일한 것입니다), 이 작업은 MongoDB 인스턴스가 시작되는 동안 실패할 수 있으므로 healthcheck 기능을 사용하여 작업이 성공할 때까지 재시도하고 있습니다. Docker의 healthcheck은 시작 단계에서 조금 더 공격적일 수 있도록 허용해줍니다. 이것이 start_interval이 1초로만 설정되어 있는 이유입니다. 유감스럽게도 Docker Compose에서 start_interval이 아직 지원되지 않고 있지만, 이것은 사양의 일부입니다. 이 기능에 대한 진행 상황은 해당 GitHub 이슈에서 확인할 수 있습니다. 그 동안 우리는 5초로 일반 interval 값을 설정할 수 있으며, 과도하게 공격적이거나 너무 오랫동안 기다리는 중간 지점입니다. 그러나 start_interval이 구현되면 interval 값을 몇 분 동안 올릴 수 있을 것입니다.

여기서 rs.status()를 사용한 이유는 레플리카 셋이 초기화되지 않았을 때 예외를 throw하기 때문에, 레플리카 셋이 초기화될 때까지 rs.initiate()를 호출하기에 편리합니다. 그 후에는 rs.status()를 주기적으로 호출하는 것은 부담이 되지 않습니다. 또한 여기서 healthcheck가 의도한 대로 작동하고 있다는 점에 유의하십시오. 왜냐하면 우리는 bash 명령이 처음으로 레플리카 셋을 초기화하거나 레플리카 셋이 이미 초기화되어 있는 경우에만 성공적인 종료 코드를 리턴할 것으로 기대하고 있기 때문입니다.


<div class="content-ad"></div>

마지막으로, 우리는 mongo1_data라는 Docker 볼륨에 데이터를 영속화합니다. 이것은 컨테이너가 중지될 때 데이터가 손실되지 않도록 하는 최선의 방법입니다. 또 다른 볼륨인 mongo1_config은 복제 세트 구성을 영속화하는 데 사용됩니다.

![이미지](https://miro.medium.com/v2/resize:fit:1400/1*4FJZGrr5m7VuvOk-SmYxcg.gif)

이 단일 노드 MongoDB 복제 세트에 액세스하기 위한 연결 문자열은 다음과 같습니다:

```js
mongodb://127.0.0.1:27017/?replicaSet=rs0
```

<div class="content-ad"></div>

# 쓰림 노드 복제본 설정

이전에 말했듯이, 단일 노드 복제본은 로컬 개발에 충분합니다. 그러나 장애 허용성 및 고 가용성을 실험하려면 여러 노드가 필요합니다. 프로덕션 용도로 사용할 경우, MongoDB 문서에서는 적어도 세 개의 노드를 갖는 것을 권장합니다. 첫 번째 컨테이너는 기본 노드가 되고, 나머지 두 개의 컨테이너는 보조 노드가 됩니다. 다음은 rs0라는 세 개의 노드로 구성된 쓰림 복제본을 실행하기 위한 docker-compose.yml 파일입니다:

```js
version: "3.8"

services:
  mongo1:
    image: mongo:7.0
    command: ["--replSet", "rs0", "--bind_ip_all", "--port", "27017"]
    ports:
      - 27017:27017
    extra_hosts:
      - "host.docker.internal:host-gateway"
    healthcheck:
      test: echo "try { rs.status() } catch (err) { rs.initiate({_id:'rs0',members:[{_id:0,host:'host.docker.internal:27017',priority:1},{_id:1,host:'host.docker.internal:27018',priority:0.5},{_id:2,host:'host.docker.internal:27019',priority:0.5}]}) }" | mongosh --port 27017 --quiet
      interval: 5s
      timeout: 30s
      start_period: 0s
      start_interval: 1s
      retries: 30
    volumes:
      - "mongo1_data:/data/db"
      - "mongo1_config:/data/configdb"

  mongo2:
    image: mongo:7.0
    command: ["--replSet", "rs0", "--bind_ip_all", "--port", "27018"]
    ports:
      - 27018:27018
    extra_hosts:
      - "host.docker.internal:host-gateway"
    volumes:
      - "mongo2_data:/data/db"
      - "mongo2_config:/data/configdb"

  mongo3:
    image: mongo:7.0
    command: ["--replSet", "rs0", "--bind_ip_all", "--port", "27019"]
    ports:
      - 27019:27019
    extra_hosts:
      - "host.docker.internal:host-gateway"
    volumes:
      - "mongo3_data:/data/db"
      - "mongo3_config:/data/configdb"

volumes:
  mongo1_data:
  mongo2_data:
  mongo3_data:
  mongo1_config:
  mongo2_config:
  mongo3_config:
```

이 구성에서 기본 노드를 중지하고 보조 노드가 새로운 기본 노드를 선택하는 방식을 확인할 수 있습니다. 이 설정에서 mongo1 컨테이너에는 다른 두 컨테이너보다 약간 더 높은 우선순위가 부여됩니다. 이는 복제본 세트가 완전히 기능할 때 mongo1 컨테이너가 기본 노드로 선출되도록 하는 것입니다.

<div class="content-ad"></div>

보조 노드 중 하나를 중지하고 레플리카 세트가 계속 작동하는지 확인해 볼 수도 있어요. 모든 노드를 중지해 보고 레플리카 세트가 작동을 멈추는지도 확인할 수 있어요. 이는 내결함 허용성과 고가용성을 실험하는 좋은 방법이에요. 레플리카 세트 상태를 쿼리하고 어느 노드가 주 노드인지 확인하려면 rs.status() 몽고 쉘 명령어를 사용하세요.

![image](https://miro.medium.com/v2/resize:fit:1400/1*w9Oxx6FtrIJ4SMj2ySOApA.gif)

3개 노드 레플리카 세트 연결 문자열은:

```js
mongodb://127.0.0.1:27017,127.0.0.1:27018,127.0.0.1:27019/?replicaSet=rs0
```

<div class="content-ad"></div>

# 연결 문제 해결하기

![이미지](/assets/img/2024-06-23-TheonlylocalMongoDBreplicasetwithDockerComposeguideyoulleverneed_1.png)

만약 MongoDB 복제본 세트에 연결하는 데 문제가 있다면 Docker가 실행 중인지 확인해주세요. 또한 host.docker.internal 호스트명이 호스트 머신의 IP 주소로 해석될 수 있도록도 확인해주세요.

Windows에서는 호스트 파일에 *.docker.internal 호스트명을 자동으로 추가하는 설정이 있습니다.

<div class="content-ad"></div>

Linux에서 host.docker.internal을 해결할 수 없는 경우, host.docker.internal을 IP 주소 127.17.0.1로 매핑하기 위해 /etc/hosts 파일에 한 줄을 추가해야 합니다.

# healthcheck에 대한 추가 사항

여기에서 healthcheck을 사용하여 복제 세트를 초기화하는 장점은 docker-compose.yml 파일이 자체 포함되어 있습니다. 수동으로 복제 세트를 초기화하는 것을 선호하는 경우, healthcheck 섹션을 제거하고 rs.initiate() mongosh 명령을 사용하여 복제 세트를 초기화할 수 있습니다.

```js
docker compose exec mongo1 mongosh --port 27017 --quiet --eval "rs.initiate({...})" --json relaxed
```

<div class="content-ad"></div>

그러나 docker-compose.yml 파일을 사용할 모든 개발자 여러분께서는 적어도 한 번은 이 작업을 기억해야 합니다.