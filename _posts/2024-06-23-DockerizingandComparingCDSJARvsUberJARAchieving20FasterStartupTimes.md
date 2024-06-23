---
title: "CDS JAR vs Uber JAR 도커화 및 비교 20 빠른 시작 시간 달성하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-DockerizingandComparingCDSJARvsUberJARAchieving20FasterStartupTimes_0.png"
date: 2024-06-23 22:45
ogImage: 
  url: /assets/img/2024-06-23-DockerizingandComparingCDSJARvsUberJARAchieving20FasterStartupTimes_0.png
tag: Tech
originalTitle: "Dockerizing and Comparing CDS JAR vs. Uber JAR: Achieving 20% Faster Startup Times"
link: "https://medium.com/itnext/dockerizing-and-comparing-cds-jar-vs-uber-jar-achieving-20-faster-startup-times-31756adef99b"
---


## JAVA | JVM | CDS | DOCKER

![Dockerizing and Comparing CDS JAR vs Uber JAR Achieving 20% Faster Startup Times](/assets/img/2024-06-23-DockerizingandComparingCDSJARvsUberJARAchieving20FasterStartupTimes_0.png)

이 기사에서는 Java Spring Boot 애플리케이션 greetings-app을 Uber JAR 및 CDS JAR을 사용하여 두 가지 방법으로 도커화할 것입니다. 나중에 이들의 시작 시간을 비교할 것입니다.

아래 연결된 기사에서 greetings-app의 전체 코드 및 구현을 찾을 수 있습니다. 기사에서 설명된 단계를 따라 진행하고 시작하세요.

<div class="content-ad"></div>

그러면 시작해봅시다!

## 준비물

이 튜토리얼을 따라하려면 컴퓨터에 Java 17+와 Docker가 설치되어 있어야 합니다.

## Docker 이미지 빌드하기

<div class="content-ad"></div>

## Uber JAR을 사용하여 도커 이미지 생성하기

인사 앱 루트 폴더에서 Dockerfile-Uber-JAR라는 파일을 다음 내용으로 생성해 봅시다:

```js
FROM amazoncorretto:17.0.10

COPY target/greetings-app-0.0.1-SNAPSHOT.jar greetings-app.jar

ENTRYPOINT ["java", "-jar", "greetings-app.jar"]
```

다음으로, 터미널을 열고 인사 앱 루트 폴더 내에서 아래 명령어를 실행하여 greetings-app-uber-jar 도커 이미지를 생성해 봅시다:

<div class="content-ad"></div>

```js
도커 이미지를 CDS JAR로 빌드하려면

greetings-app 루트 폴더에 Dockerfile-CDS-JAR이라는 파일을 다음 내용으로 생성해보세요:

FROM amazoncorretto:17.0.10

COPY greetings-app-0.0.1-SNAPSHOT/greetings-app-0.0.1-SNAPSHOT.jar greetings-app.jar
COPY greetings-app-0.0.1-SNAPSHOT/lib/ lib/

ENTRYPOINT ["java", "-jar", "greetings-app.jar"]

<div class="content-ad"></div>

다음으로, 터미널에서 greetings-app 루트 폴더 안으로 이동한 다음 아래 명령어를 실행하여 greetings-app-cds-jar 도커 이미지를 생성해 봅시다:

docker build -f Dockerfile-CDS-JAR -t greetings-app-cds-jar .

## 도커 이미지 크기

도커 이미지의 크기를 확인해 봅시다. 터미널에서 다음 명령어를 실행해 주세요:

<div class="content-ad"></div>

도커 이미지 | grep greetings-app

이렇게 나와야 해요:

greetings-app-cds-jar                                latest                   50613179d4fa   3 minutes ago   486MB
greetings-app-uber-jar                               latest                   67c71f76b553   3 minutes ago   486MB

둘 다 크기가 486MB로 동일해요.

<div class="content-ad"></div>

# 시작 및 중지 시간 5번

## Uber JAR로 Docker 컨테이너 시작 및 중지

터미널에서 다음 명령을 실행하여 애플리케이션의 Docker 컨테이너를 시작합니다. 중지하려면 Ctrl+C를 누르고, 이 프로세스를 다섯 번 반복하세요:

docker run --rm -m 1024M -p 8080:8080 --name greetings-app greetings-app-uber-jar

<div class="content-ad"></div>

아래는 우리가 진행한 5회 실행의 결과입니다 (일부 로그 라인은 간략하게 생략되었습니다):

➜  greetings-app docker run --rm -m 1024M -p 8080:8080 --name greetings-app greetings-app-uber-jar

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/

 :: Spring Boot ::                (v3.3.1)

...
2024-06-28 오후 06:52:23.982Z  INFO 1 --- [greetings-app] [           main] c.e.g.GreetingsAppApplication            : GreetingsAppApplication이 2.767초 안에 시작되었습니다 (프로세스 실행 시간 3.48초).
2024-06-28 오후 06:52:23.988Z  INFO 1 --- [greetings-app] [           main] c.e.greetingsapp.MemoryUsageLogger       : 시작 시 메모리 풋프린트: 14MB
^C%
➜  greetings-app docker run --rm -m 1024M -p 8080:8080 --name greetings-app greetings-app-uber-jar

...

이를 반복하여 총 다섯 번 실행하시면 됩니다.

## CDS JAR로 Docker 컨테이너 시작 및 정지

터미널에서 다음 명령을 실행하여 애플리케이션의 Docker 컨테이너를 시작하세요. 중지하려면 Ctrl+C를 누르고, 이 작업을 총 다섯 번 반복하세요.

<div class="content-ad"></div>

아래는 우리가 실시한 5회 실행 결과입니다 (간결함을 위해 일부 로그 라인이 생략되었습니다):

➜  greetings-app docker run --rm -m 1024M -p 8080:8080 --name greetings-app greetings-app-cds-jar

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/

 :: Spring Boot ::                (v3.3.1)

...
2024-06-23T06:52:53.646Z  INFO 1 --- [greetings-app] [           main] c.e.g.GreetingsAppApplication            : Started GreetingsAppApplication in 2.285 seconds (process running for 2.673)
2024-06-23T06:52:53.651Z  INFO 1 --- [greetings-app] [           main] c.e.greetingsapp.MemoryUsageLogger       : Memory footprint at startup: 15 MB
^C%
➜  greetings-app docker run --rm -m 1024M -p 8080:8080 --name greetings-app greetings-app-cds-jar

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/

 :: Spring Boot ::                (v3.3.1)

...
2024-06-23T06:52:58.858Z  INFO 1 --- [greetings-app] [           main] c.e.g.GreetingsAppApplication            : Started GreetingsAppApplication in 2.422 seconds (process running for 2.814)
2024-06-23T06:52:58.864Z  INFO 1 --- [greetings-app] [           main] c.e.greetingsapp.MemoryUsageLogger       : Memory footprint at startup: 16 MB
^C%
➜  greetings-app docker run --rm -m 1024M -p 8080:8080 --name greetings-app greetings-app-cds-jar

...

# 비교결과

<div class="content-ad"></div>

## 스타트업 시간

아래 표는 위 실행에서 얻은 스타트업 시간과 평균을 보여줍니다:

```
| Docker Image | 스타트업 시간(초) | 평균 스타트업 시간(초) |
|--------------|-------------------|-----------------------|
| uber-jar     | 2.767             |                       |
| uber-jar     | 2.841             |                       |
| uber-jar     | 2.864             |                       |
| uber-jar     | 2.916             |                       |
| uber-jar     | 2.860             | 2.849                 |
| cds-jar      | 2.285             |                       |
| cds-jar      | 2.422             |                       |
| cds-jar      | 2.318             |                       |
| cds-jar      | 2.335             |                       |
| cds-jar      | 2.319             | 2.335                 |


평균 표를 통해 CDS JAR가 있는 Docker 컨테이너의 스타트업 시간이 Uber JAR가 있는 것보다 약 18.03% 빠르다는 것을 알 수 있습니다.

<div class="content-ad"></div>

## 성능

성능을 분석하기 위해 API에 대한 부하 테스트를 실행하는 oha 도구를 사용할 것입니다. 도커 컨테이너를 시작하고 동시성이 2인 상태에서 10,000개의 요청을 제출할 것입니다.

다음은 oha 명령어입니다:

```js
oha -n 10000 -c 2 --latency-correction --disable-keepalive http://localhost:8080/greetings
```

<div class="content-ad"></div>

여기 결과가 있어요:

```js
Docker Container with Uber JAR | Docker Container with CDS JAR
------------------------------ | -----------------------------
Summary:                       | Summary:
  Success rate: 100.00%        |   Success rate: 100.00%
  Total: 13.2974 secs          |   Total: 13.3007 secs
  Slowest: 0.2332 secs         |   Slowest: 0.2062 secs
  Fastest: 0.0012 secs         |   Fastest: 0.0012 secs
  Average: 0.0027 secs         |   Average: 0.0027 secs
  Requests/sec: 752.0248       |   Requests/sec: 751.8427
  Total data: 117.19 KiB       |   Total data: 117.19 KiB
  Size/request: 12 B           |   Size/request: 12 B
  Size/sec: 8.81 KiB           |   Size/sec: 8.81 KiB
```

Uber JAR를 사용한 Docker 컨테이너가 CDS JAR를 사용한 것보다 약간 더 나은 성능을 보였어요. Uber JAR를 이용한 컨테이너는 초당 752.02개의 요청을 처리하는 반면, CDS JAR를 이용한 컨테이너는 초당 751.84개의 요청을 처리했어요.

## 메모리 사용량

<div class="content-ad"></div>

메모리 사용량을 비교하기 위해 Google의 cAdvisor 도구를 사용했어요.

다음은 결과입니다:

Uber JAR를 사용한 Docker 컨테이너

![이미지](/assets/img/2024-06-23-DockerizingandComparingCDSJARvsUberJARAchieving20FasterStartupTimes_1.png)

<div class="content-ad"></div>

위와 같이 볼 때, Uber JAR를 사용한 Docker 컨테이너는 시작할 때 140MB에 이르렀고 부하 테스트 중에 최대 182MB까지 증가했습니다.

CDS JAR를 사용한 Docker 컨테이너

![이미지](/assets/img/2024-06-23-DockerizingandComparingCDSJARvsUberJARAchieving20FasterStartupTimes_2.png)

CDS JAR를 사용한 Docker 컨테이너는 시작할 때 127MB에 이르렀고 부하 테스트 중에 최대 174MB까지 증가했습니다.

<div class="content-ad"></div>

사, 시작 시 메모리 사용량을 9.3% 절약했고 로드 테스트 중에는 4.4%를 절약했다고 결론지을 수 있습니다.

# 정리

이 기사에서 생성된 도커 이미지를 삭제하려면 다음 명령을 실행하세요:

```js
docker rmi greetings-app-cds-jar:latest greetings-app-uber-jar:latest
```

<div class="content-ad"></div>

# 결론

이 글에서는 Java Spring Boot 애플리케이션 greetings-app을 Uber JAR 및 CDS JAR을 사용하여 두 가지 방법으로 Docker화했습니다. 그리고 이들 Docker 컨테이너를 다섯 번 실행 및 중지시키고 시작 시간을 기록했습니다. 결과는 CDS JAR이 적용된 Docker 컨테이너가 시작 시간을 약 20% 개선시켰다는 것을 보여주었습니다. 또한 성능을 확인했을 때, Uber JAR이 적용된 Docker 컨테이너가 CDS JAR 적용된 것보다 약간 더 우수했습니다. 마지막으로 메모리 사용량을 살펴보면, 시작 시에 CDS JAR이 적용된 Docker 컨테이너가 메모리 사용량을 9.3% 줄이고 부하 테스트 중에는 4.4% 줄였음을 확인할 수 있었습니다.

# 지원과 참여

만약 이 글을 즐겁게 읽으셨고 지원을 보여주고 싶다면, 아래의 조치를 고려해 주시기 바랍니다:

<div class="content-ad"></div>

- 👏 제 이야기에 박수를 치거나 강조하고 답글을 남겨주셔요. 궁금한 점이 있다면 언제든지 물어봐주세요.
- 🌐 제 이야기를 소셜 미디어에 공유해주세요.
- 🔔 Medium | LinkedIn | Twitter | GitHub에서 저를 팔로우해주세요.
- ✉️ 제 뉴스레터를 구독하여 최신 포스트를 놓치지 마세요.