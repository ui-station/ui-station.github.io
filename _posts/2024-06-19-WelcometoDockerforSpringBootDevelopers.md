---
title: "스프링 부트 개발자를 위한 도커에 오신 것을 환영합니다"
description: ""
coverImage: "/assets/img/2024-06-19-WelcometoDockerforSpringBootDevelopers_0.png"
date: 2024-06-19 12:47
ogImage:
  url: /assets/img/2024-06-19-WelcometoDockerforSpringBootDevelopers_0.png
tag: Tech
originalTitle: "Welcome to Docker for Spring Boot Developers"
link: "https://medium.com/@zjawad333/welcome-to-docker-for-spring-boot-developers-1e610d6d843c"
---

![Docker](/assets/img/2024-06-19-WelcometoDockerforSpringBootDevelopers_0.png)

현대 소프트웨어 아키텍처에서 컨테이너는 선택사항에서 필수 요소로 전환되어 가고 있습니다. 다양한 플랫폼에서 소프트웨어를 이주하고 실행하는 민첩한 방법을 제공하여 전통적인 웹 서버 모델에 비해 속도, 이식성, 확장성 등의 혜택을 제공합니다. 이러한 작고 유연한 컨테이너는 더 큰이고 적응력이 덜한 세팅을 대체하며 특히 마이크로서비스를 향상시킵니다.

본 문서는 Docker를 소개하며, 주요 클라우드 제공업체와의 호환성을 위해 선택된 컨테이너 기술입니다. Docker 기본 사항 및 Maven을 사용한 Spring Boot에서 Docker 실행 및 이미지 작성과 같은 실용적 작업에 대해 다룹니다.

# Docker란 무엇인가요?

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

도커는 2013년 Solomon Hykes에 의해 개발된 널리 사용되는 오픈 소스 컨테이너 엔진입니다. 초기에는 편리성으로 인식되었지만, 응용 프로그램 내에서 컨테이너를 관리하고 시작하는 데 중요한 도구로 진화하여 가상 머신(VM)과 같은 하드웨어 할당 대신 물리적 머신에서 자원 공유를 가능케했습니다.

IBM, Microsoft, Google 등 주요 기업들의 지원으로 인해 도커는 소프트웨어 개발자들을 위한 필수 도구로 부상했습니다.

서버, REST API 및 명령줄 인터페이스(CLI)로 구성된 Docker Engine은 도커 시스템의 핵심을 형성하여 서버 간에 컨테이너 배포를 용이하게 합니다.

# 도커 엔진 구성 요소

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

기본 구성 요소는 다음과 같습니다:

- Docker 데몬: Docker 이미지를 생성하고 관리하는 서버(dockerd)로, REST API 및 CLI로부터 명령을 받습니다.
- Docker 클라이언트: 사용자는 Docker 클라이언트를 통해 Docker와 상호 작용하며, 이 클라이언트는 데몬에 명령을 전송합니다.
- Docker 레지스트리: Docker 이미지를 저장하는 곳으로, 공개(예: Docker 허브) 또는 비공개 레지스트리 옵션이 있습니다.
- Docker 이미지: Docker 컨테이너를 생성하는 지침을 포함하는 읽기 전용 템플릿으로, 레지스트리에서 가져오거나 Dockerfile을 사용하여 새 이미지를 만들 수 있습니다.
- Docker 컨테이너: 'docker run' 명령을 사용하여 Docker 이미지에서 만들어지며, 응용 프로그램 및 환경을 호스팅하며 Docker API 또는 CLI를 통해 관리할 수 있습니다.
- Docker 볼륨: Docker 컨테이너에서 생성되고 사용되는 데이터에 대한 우선적인 저장 메커니즘으로, Docker API 또는 CLI를 통해 관리할 수 있습니다.
- Docker 네트워크: 컨테이너가 통신을위한 여러 네트워크에 연결되도록 허용하며, 다섯 가지 네트워크 드라이버 유형이 있습니다: bridge, host, overlay, none 및 macvlan.

# Dockerfiles

Dockerfile은 Docker 이미지를 자동으로 생성하고 구성하는 지침을 포함하는 텍스트 파일입니다. Linux와 유사한 명령을 사용하여 이미지 생성을 간소화하고 가독성을 향상시킵니다.

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

Dockerfile을 생성한 후에는 Docker 이미지를 빌드하기 위해 docker build 명령을 실행합니다. 그런 다음, Docker 이미지가 준비되면 실행 명령을 사용하여 컨테이너를 생성합니다.

가장 일반적인 Dockerfile 명령어:

- FROM: 빌드 프로세스를 시작하는 기본 이미지를 지정합니다.
- LABEL: 이미지에 메타데이터를 키-값 쌍 형식으로 추가합니다.
- ARG: 사용자가 docker build 명령을 사용하여 빌더로 전달할 수 있는 변수를 정의합니다.
- COPY: 호스트 시스템에서 파일이나 디렉토리를 Docker 이미지로 복사합니다.
- ADD: COPY와 비슷하지만 원격 URL을 가져오고 tar 파일의 자동 추출과 같은 추가 기능을 지원합니다.
- VOLUME: 볼륨 마운트를 생성합니다.
- RUN: 이미지 빌드 중에 명령을 실행합니다.
- CMD: 컨테이너가 시작될 때 실행할 기본 명령을 지정합니다.
- ENTRYPOINT: 컨테이너가 실행 가능으로 설정됩니다.
- ENV: 컨테이너 내에서 환경 변수를 설정합니다.

# Docker Compose

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

Docker Compose은 여러 컨테이너 애플리케이션을 관리하기 위한 도구입니다. 단일 YAML 파일을 사용하여 서비스를 정의하고 제어하며, 간단한 명령을 사용하여 전체 애플리케이션 스택을 시작하고 중지하고 관리할 수 있습니다.

Docker Compose 사용 방법:

- docker-compose.yml이라는 YAML 구성 파일을 생성합니다.
- docker-compose config로 파일을 유효성 검사합니다.
- docker-compose up을 사용하여 서비스를 시작합니다.

샘플 docker-compose.yml 파일은 데이터베이스와 애플리케이션과 같은 서비스를 정의합니다. 명령어는 다음과 같습니다.

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

- 이미지를 빌드하고 서비스를 시작하려면 `docker-compose up -d`를 사용하세요.
- 배포 정보를 확인하려면 `docker-compose logs`를 사용하세요.
- 컨테이너 목록을 보려면 `docker-compose ps`를 사용하세요.
- 서비스를 중지하려면 `docker-compose stop`을 사용하세요.
- 모든 컨테이너를 중지하고 제거하려면 `docker-compose down`을 사용하세요.

다음은 MySQL 및 Redis와 함께 Spring Boot 애플리케이션을 위한 Docker Compose YAML 파일(docker-compose.yml)의 예시입니다:

```yaml
version: "3.8"

services:
  spring-boot-app:
    image: spring-boot-image:latest
    ports:
      - "8080:8080"
    depends_on:
      - mysql-db
      - redis-cache
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://mysql-db:3306/database_name
      SPRING_DATASOURCE_USERNAME: $MYSQLDB_USERNAME
      SPRING_DATASOURCE_PASSWORD: $MYSQLDB_PASSWORD
      SPRING_REDIS_HOST: redis-cache

  mysql-db:
    image: mysql:latest
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: mysql_root_password
      MYSQL_DATABASE: database_name
      MYSQL_USERNAME: $MYSQLDB_USERNAME
      MYSQL_PASSWORD: $MYSQLDB_PASSWORD

  redis-cache:
    image: redis:latest
    ports:
      - "6379:6379"
```

이 파일에서:

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

- spring-boot-app은 Spring Boot 애플리케이션을 위한 서비스입니다.
- mysql-db는 MySQL 데이터베이스를 위한 서비스입니다.
- redis-cache는 Redis 캐시를 위한 서비스입니다.

각 서비스를 위한 이미지(image)를 정의하고 필요한 포트(ports)를 노출시키며, 데이터베이스 연결 세부 정보를 위한 환경 변수(environment)를 설정합니다.

Spring Boot 앱은 MySQL과 Redis에 의존하며(dependes_on), Spring Boot 앱보다 먼저 시작되도록 합니다.

# Spring Boot으로 Docker 이미지 만들기

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

Spring Boot 2.3에서는 Spring Boot 애플리케이션용 Docker 이미지를 만드는 간단하고 효율적인 방법을 소개했습니다. 기존에는 Docker 이미지를 Dockerfile 및 docker build 명령을 사용하여 만들었지만, 이 방법은 몇 가지 단점이 있었습니다.
첫째로 Spring Boot이 생성한 fat jar에 의존했기 때문에, 특히 컨테이너 환경에서 부팅 시간에 영향을 줄 수 있습니다. 이를 해결하기 위해 jar 파일의 폭발된 내용을 포함할 수 있습니다.
둘째로 Docker 이미지는 계층으로 구성됩니다. Spring Boot fat jar을 사용하면 모든 애플리케이션 코드와 타사 라이브러리가 하나의 계층으로 묶입니다. 이는 작은 코드 변경이라도 전체 계층을 다시 빌드해야 한다는 것을 의미합니다.
이미지를 빌드하기 전에 jar 파일을 폭파시키면 애플리케이션 코드와 타사 라이브러리가 별도 계층으로 구성됩니다. 이를 통해 Docker의 캐싱 메커니즘을 활용할 수 있습니다. 결과적으로 코드 변경이 발생하면 해당 계층만 재빌드하면 되어 효율성이 크게 향상되고 빌드 시간이 줄어듭니다.

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

Spring Boot 2.3에서 소개된 두 가지 새로운 기능은 이미지 작성 기술을 개선하는 데 도움이 되었습니다: Buildpacks와 Layered jars support.

## Buildpacks

Buildpacks는 Docker 이미지 작성을 간편화하여 필요한 프레임워크 및 응용 프로그램 종속성을 자동으로 제공하여 Dockerfile이 필요하지 않도록합니다. Spring Boot에서는 Maven과 Gradle이 buildpacks를 지원하여 간편한 이미지 생성을 가능하게합니다.

예를 들어, Maven을 사용하여 pom.xml 파일을 변경하지 않고 다음을 실행합니다:

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
./mvnw spring-boot:build-image -Dspring-boot.build-image.imageName=myorg/app
```

Gradle를 사용하는 경우:

```js
./gradlew bootBuildImage --imageName=myorg/app
```

첫 번째 빌드는 컨테이너 이미지 및 JDK를 다운로드해야 하기 때문에 시간이 오래 걸릴 수 있지만, 그 이후의 빌드는 빠릅니다.

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

이 명령어는 먼저 표준 Fat Jar를 빌드한 다음 Docker 이미지 생성을 시작합니다. Packeto 빌더를 사용하여 클라우드 네이티브 빌드팩을 구현합니다. 이는 프로젝트를 분석하고 필요한 프레임워크 및 라이브러리(예: Spring Boot)를 식별하여 이미지에 추가합니다.

이후의 빌드는 Docker의 레이어링을 활용하여, 애플리케이션 코드만 변경될 때에는 빌드 시간을 단축할 수 있습니다. 이 효율성은 빌드팩을 통해 도커 이미지를 생성하는 데 간편하고 빠른 옵션으로 만들어줍니다.

## 계층화된 JARS

Spring Boot은 jar에 레이어 인덱스 파일을 추가하는 것을 지원합니다. 이 인덱스는 레이어 목록과 해당 레이어에 포함되어야 하는 jar의 부분을 제공합니다. 인덱스의 레이어 목록은 Docker/OCI 이미지에 추가할 순서로 정렬됩니다. 기본적으로 다음 레이어가 지원됩니다:

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

- 종속성
- spring-boot-loader
- snapshot-dependencies
- application

다음은 layers.idx 파일의 예시를 보여줍니다:

```js
- "dependencies":
  - BOOT-INF/lib/library1.jar
  - BOOT-INF/lib/library2.jar
- "spring-boot-loader":
  - org/springframework/boot/loader/launch/JarLauncher.class
  - ... <other classes>
- "snapshot-dependencies":
  - BOOT-INF/lib/library3-SNAPSHOT.jar
- "application":
  - META-INF/MANIFEST.MF
  - BOOT-INF/classes/a/b/C.class
```

이 레이어링은 응용 프로그램 빌드 간에 얼마나 자주 변경될 수 있는지에 따라 코드를 분리하는 데 사용됩니다. 라이브러리 코드는 빌드 간에 변경될 가능성이 낮기 때문에 캐시로부터 레이어를 재사용할 수 있도록 자체 레이어에 배치됩니다. 응용 프로그램 코드는 빌드 간에 변경될 가능성이 더 높기 때문에 별도의 레이어에 격리됩니다.

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

Docker 이미지를 만드는 데 이것을 어떻게 활용할 수 있는지 살펴보겠습니다.

- 계층화된 JAR 생성: 먼저 pom.xml 파일의 Spring Boot Maven 플러그인에 계층 구성을 추가하세요. 아래는 예시 스니펫입니다:

```js
<plugin>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-maven-plugin</artifactId>
  <configuration>
    <layers>
      <enabled>true</enabled>
    </layers>
  </configuration>
</plugin>
```

- JAR 재빌드: pom.xml을 구성한 후에는 mvn clean package를 사용하여 Spring Boot JAR을 재빌드하여 새로운 계층화된 JAR을 생성하세요.

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

애플리케이션의 루트 디렉토리로 이동한 다음 다음 명령어를 실행하여 레이어와 그 순서를 표시하세요:

```js
java -Djarmode=layertools -jar target/app.jar list
```

- 레이어 도구(jar 모드)를 사용하여 jar 파일을 실행하세요:

```js
$ java -Djarmode=layertools -jar app.jar
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

- Dockerfile 생성하기: Docker 이미지에 이러한 레이어들을 포함하는 가장 쉬운 방법은 Dockerfile을 사용하는 것입니다:

```js
FROM eclipse-temurin:17-jre as builder

WORKDIR application
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} application.jar
RUN java -Djarmode=layertools -jar application.jar extract

FROM eclipse-temurin:17-jre
WORKDIR application

COPY --from=build application/dependencies/ ./
COPY --from=build application/spring-boot-loader/ ./
COPY --from=build application/snapshot-dependencies/ ./
COPY --from=build application/application/ ./

ENTRYPOINT ["java", "org.springframework.boot.loader.JarLauncher"]
```

이 Dockerfile에서는 우리의 fat jar를 별도의 레이어로 분리하고 각 레이어를 Docker 이미지에 추가하는 방법을 제시했습니다. COPY 명령을 사용할 때마다 Docker 이미지에 새로운 레이어가 생성됩니다.

- Docker 이미지 빌드하기: 위의 Dockerfile이 현재 디렉토리에 있다고 가정하면, 도커 이미지는 docker build . 명령을 사용하여 빌드할 수 있습니다. 또는 다음 예시와 같이 애플리케이션 jar 파일의 경로를 지정할 수도 있습니다:

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
$ docker build --build-arg JAR_FILE=path/to/app.jar .
```

# Jib을 사용하여 Java 컨테이너 빌드하기

Jib은 구글이 개발한 Java 도구로 Java 앱을 위한 Docker 이미지를 쉽게 빌드할 수 있습니다. Dockerfile을 작성할 필요도 없고 시스템에 Docker를 설치할 필요도 없어요.

Jib은 의존성, 리소스, 클래스와 같은 애플리케이션을 구별하는 레이어로 구성하고 Docker 이미지 레이어 캐싱을 이용하여 변경 사항만 다시 빌드하는 방식으로 빌드를 빠르게 유지합니다. Jib의 레이어 구조와 작은 베이스 이미지는 전체 이미지 크기를 작게 유지하여 성능과 이식성을 향상시킵니다.

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

배포하기 전에 Docker 저장소에 대한 로컬 인증 설정을 하는 것이 매우 중요합니다. 예를 들어 DockerHub 자격 증명을 .m2/settings.xml 파일에 포함할 수 있습니다. 그러나 이 파일에 암호를 평문으로 저장하는 것은 권장되지 않습니다.

```js
<settings>
  ...
  <servers>
    ...
    <server>
      <id>registry.hub.docker.com</id>
      <username>내_사용자명</username>
      <password>{내_시크릿}</password>
    </server>
  </servers>
</settings>
```

id 필드가 이 자격 증명이 지정된 레지스트리 서버와 일치하는지 확인하세요.

Jib를 사용하여 Docker Hub에 배포하려면 jib-maven-plugin이나 그와 동등한 Gradle을 사용할 수 있습니다. 간단히 다음과 같은 명령을 실행하면 됩니다:

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
mvn compile com.google.cloud.tools:jib-maven-plugin:3.1.1:build -Dimage=$IMAGE_PATH
```

예를 들어, 이미지를 DockerHub에 업로드하려면 $IMAGE_PATH를 registry.hub.docker.com/my-repo/my-app과 같은 값으로 설정하세요.

이 명령은 앱의 Docker 이미지를 빌드하고 DockerHub로 푸시합니다. Google Container Registry나 Amazon Elastic Container Registry와 같은 다른 레지스트리에도 비슷한 방식으로 이미지를 업로드할 수 있습니다.

## 추가 커스터마이징

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

Jib 빌드를 Dockerfile과 비슷한 방식으로 사용자 정의할 수 있습니다. 이는 환경 변수를 추가하는 등의 방식입니다. 아래는 그 방법입니다:

```js
<project>
  ...
  <build>
    <plugins>
      ...
      <plugin>
        <groupId>com.google.cloud.tools</groupId>
        <artifactId>jib-maven-plugin</artifactId>
        <version>3.1.1</version>
        <configuration>
          <to>
            <image>${IMAGE_PATH}</image>
          </to>
          <container>
            <environment>
              <ENV_VAR>VALUE</ENV_VAR>
            </environment>
          </container>
        </configuration>
      </plugin>
      ...
    </plugins>
  </build>
  ...
</project>
```

이 변경으로 Maven 명령을 간소화할 수 있습니다:

```js
mvn compile jib:build
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

옵션으로 Docker를 설치한 경우, 로컬 Docker 설치로 빌드할 수 있습니다. 이렇게 하면 이미지를 다른 로컬 컨테이너처럼 검사하거나 실행할 수 있습니다:

```js
mvn compile jib:dockerBuild
```

# 결론

Docker는 현대 소프트웨어 개발에 있어 필수 요소가 되었습니다. 민첩성과 확장성을 제공합니다. 본 문서에서는 Docker의 기초를 소개하고 Maven을 사용하여 Spring Boot 앱용 이미지를 생성하는 방법을 알아보았습니다. Spring Boot 2.3은 효율적인 이미지 생성을 위해 Buildpacks와 Layered Jars를 도입하였습니다. Google의 Jib는 Java 앱을 위한 Docker 이미지 생성을 간단하게 해줍니다. 이러한 도구들을 활용하면 배포를 간소화하고 생산성을 향상시킬 수 있습니다.
