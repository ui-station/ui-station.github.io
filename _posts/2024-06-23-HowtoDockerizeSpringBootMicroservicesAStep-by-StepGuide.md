---
title: "스프링 부트 마이크로서비스 도커라이징 방법 단계별 가이드"
description: ""
coverImage: "/assets/img/2024-06-23-HowtoDockerizeSpringBootMicroservicesAStep-by-StepGuide_0.png"
date: 2024-06-23 00:44
ogImage: 
  url: /assets/img/2024-06-23-HowtoDockerizeSpringBootMicroservicesAStep-by-StepGuide_0.png
tag: Tech
originalTitle: "How to Dockerize Spring Boot Microservices: A Step-by-Step Guide"
link: "https://medium.com/@amitu2016/how-to-dockerize-spring-boot-microservices-a-comprehensive-guide-f585fe016789"
---



![이미지](/assets/img/2024-06-23-HowtoDockerizeSpringBootMicroservicesAStep-by-StepGuide_0.png)

현대 소프트웨어 개발 환경에서, 마이크로서비스 아키텍처는 확장성, 유연성 및 유지 보수성 때문에 상당한 인기를 얻었습니다. 선도적인 컨테이너화 플랫폼인 Docker는 각 서비스를 위한 격리된 환경을 제공하여 지속적인 통합 및 배포 (CI/CD)를 용이하게 하고, 서로 다른 환경에서 일관된 동작을 보장함으로써 이 아키텍처를 보완합니다. 이 글에서는 마이크로서비스의 도커화 과정을 안내하며, 주요 개념, 모범 사례 및 실용적인 단계를 강조할 것입니다.

# 마이크로서비스란?

마이크로서비스 아키텍처는 거대한 단일 응용 프로그램을 작은, 독립적인 서비스로 분해하여 개발, 배포 및 확장이 독립적으로 가능한 아키텍처입니다. 각 마이크로서비스는 특정 기능을 담당하고 다른 서비스와 API를 통해 통신합니다.


<div class="content-ad"></div>

# Microservices에 Docker를 사용하는 이유?

Docker 컨테이너는 Microservices에 다음과 같은 많은 이점을 제공합니다:

- 격리성: 각 Microservice는 자체 컨테이너에서 실행되어 프로세스와 리소스를 격리합니다.
- 일관성: Docker 컨테이너는 개발, 테스트 및 프로덕션 환경에서 응용 프로그램이 동일하게 작동함을 보장합니다.
- 이식성: Docker 이미지는 Docker를 지원하는 모든 플랫폼에서 실행할 수 있습니다.
- 확장성: 컨테이너는 수요에 따라 쉽게 확장하거나 축소할 수 있습니다.
- 간편한 CI/CD: Docker는 CI/CD 파이프라인과 잘 통합되어 자동 빌드, 테스트 및 배포를 가능하게 합니다.

# 준비 사항

<div class="content-ad"></div>

- Docker 및 Docker Compose에 대한 기본적인 이해도
- 로컬에서 설정된 Spring Boot 마이크로서비스 프로젝트
- 공식 Docker 웹사이트에서 다운로드할 수 있는 본인의 컴퓨터에 Docker가 설치되어 있어야 합니다.

# Spring Boot 마이크로서비스를 Docker화하는 단계

- 각 마이크로서비스에 대해 Dockerfile 작성

각 Spring Boot 마이크로서비스에 대해 프로젝트의 루트 디렉토리에 Dockerfile을 생성해야 합니다. 다음은 Spring Boot 애플리케이션을 위한 Dockerfile 예시입니다:

<div class="content-ad"></div>

쿠폰 서비스용:

```js
# 공식 OpenJDK 런타임을 부모 이미지로 사용
FROM openjdk:17-alpine

# JAR 파일을 컨테이너로 복사
ADD target/couponservice-0.0.1-SNAPSHOT.jar couponservice-0.0.1-SNAPSHOT.jar

# 애플리케이션 실행
ENTRYPOINT [ "java","-jar","couponservice-0.0.1-SNAPSHOT.jar" ]
```

상품 서비스용:

```js
# 공식 OpenJDK 런타임을 부모 이미지로 사용
FROM openjdk:17-alpine

# JAR 파일을 컨테이너로 복사
ADD target/productservice-0.0.1-SNAPSHOT.jar productservice-0.0.1-SNAPSHOT.jar

# 애플리케이션 실행
ENTRYPOINT [ "java","-jar","productservice-0.0.1-SNAPSHOT.jar" ]
```

<div class="content-ad"></div>

2. 다음 명령을 사용하여 메이븐을 통해 두 개의 마이크로서비스를 빌드하세요:

```js
mvn clean package -DskipTests
```

3. 도커 이미지 빌드하기

도커 파일을 포함하는 디렉토리로 이동한 후 도커 이미지를 빌드하세요:

<div class="content-ad"></div>

```js
도커 build -f Dockerfile -t coupon_app .
```

```js
도커 build -f Dockerfile -t product_app .
```

4. 데이터베이스 연결을 위한 MySQL 컨테이너 설정합니다.

```js
도커 run -d -p 6666:3306 --name=docker-mysql --env="MYSQL_ROOT_PASSWORD=test1234" --env="MYSQL_DATABASE=mydb" mysql
```

<div class="content-ad"></div>

5. 다음 명령어를 사용하여 Docker 이미지를 실행하십시오:

- MySQL 이미지를 실행하고 단일 명령어로 쿼리를 실행하십시오.

```js
docker exec -i docker-mysql mysql -uroot -ptest1234 mydb <tables.sql
```

2. 다음 명령어를 사용하여 두 마이크로서비스를 실행하십시오.

<div class="content-ad"></div>

```js
도커 실행 -t --name=coupon-app --link docker-mysql:mysql -p 10555:9091 coupon_app
```

```js
도커 실행 -t --link docker-mysql:mysql --link coupon-app:coupon_app -p 10666:9090 product_app
```

6. Postman을 사용하여 애플리케이션 테스트하기.

엔드포인트를 호출하여 Postman을 사용하여 애플리케이션을 테스트할 수 있습니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-23-HowtoDockerizeSpringBootMicroservicesAStep-by-StepGuide_1.png" />

<img src="/assets/img/2024-06-23-HowtoDockerizeSpringBootMicroservicesAStep-by-StepGuide_2.png" />

데모에 사용된 마이크로서비스의 소스 코드는 제 GitHub 저장소에서 확인하실 수 있습니다.

# 결론

<div class="content-ad"></div>

Spring Boot 마이크로서비스를 도커화하면 개발, 배포 및 확장 프로세스를 간편하게 만들 수 있어요. 각 마이크로서비스를 위한 도커 파일을 만들고 도커의 환경 일관성 및 격리 기능을 활용함으로써 견고하고 확장 가능한 마이크로서비스 아키텍처를 구축할 수 있어요. 이 안내서에 나온 단계에 따라 Spring Boot 마이크로서비스를 도커화하고 컨테이너화의 모든 잠재력을 활용해 보세요.