---
title: "코드에서 컨테이너로 Maven으로 Spring Boot 앱을 Dockerize하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-FromCodetoContainerDockerizingSpringBootAppswithMaven_0.png"
date: 2024-06-23 22:54
ogImage:
  url: /assets/img/2024-06-23-FromCodetoContainerDockerizingSpringBootAppswithMaven_0.png
tag: Tech
originalTitle: "From Code to Container: Dockerizing Spring Boot Apps with Maven"
link: "https://medium.com/@tauseefameen/from-code-to-container-dockerizing-spring-boot-apps-with-maven-a7e4321703a7"
---

![이미지](/assets/img/2024-06-23-FromCodetoContainerDockerizingSpringBootAppswithMaven_0.png)

컨테이너화는 애플리케이션을 배포하는 방식을 혁신적으로 변화시켰습니다. 환경별 일관성을 제공하고 배포 프로세스를 간소화합니다. 이 가이드에서는 Maven을 사용하여 스프링 부트 애플리케이션을 원활하게 도커화하는 방법을 살펴보겠습니다. 개발부터 프로덕션까지 스무스하고 효율적인 워크플로우를 보장합니다.

필수 사항

1. Spring Boot 및 Maven의 기본 지식
2. 시스템에 Docker가 설치되어 있어야 함

단계 1: 스프링 부트 애플리케이션 설정하기

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

간단한 Spring Boot 애플리케이션을 만들려면 spring initializer (https://start.spring.io/)를 사용하세요. Spring web, Spring Boot dev tools와 같은 기본 종속성을 포함하세요.

![spring initializer](/assets/img/2024-06-23-FromCodetoContainerDockerizingSpringBootAppswithMaven_1.png)

단계 2: Maven의 pom.xml에 도커 Maven 플러그인을 추가하세요.

io.fabric8 플러그인은 Maven을 위한 도커 관련 활동의 공식 플러그인입니다.
이제 새로운 "containerize"라는 Maven 프로필을 만들고 해당 프로필에서 도커-Maven 플러그인을 사용할 것입니다. 코드 스니펫을 추가했고, 코드를 설명하기 위해 주석을 사용했습니다.
이 플러그인의 문서는 여기서 확인할 수 있습니다: https://dmp.fabric8.io/

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
<profile>
  <id>containerize</id>
  <build>
   <plugins>
    <plugin>
     <groupId>io.fabric8</groupId>
     <artifactId>docker-maven-plugin</artifactId>
     <version>0.44</version>
     <configuration>
      <images>
       <image>
        <!--플레이스홀더의 명명법입니다.
          %g=매이븐 그룹 이름의 마지막 부분은 여기에서 중요합니다.
          %v=프로젝트 버전. ${project.version}과 동의어입니다.
           ....................................... -->
        <name>%g/docker-containerize:%v</name>
        <!-- ....................................... -->
         <!-- 이미지 생성을위한 빌드 구성 -->
        <!-- ....................................... -->
        <build>
         <dockerFile>Dockerfile</dockerFile>
         <assembly>
          <!--매븐 어셈블리 플러그인에 대한 자세한 내용은 다음에서 찾을 수 있습니다:
          https://maven.apache.org/plugins/maven-assembly-plugin/assembly.html-->
          <basedir>/</basedir>
          <inline xmlns="http://maven.apache.org/ASSEMBLY/2.2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://maven.apache.org/ASSEMBLY/2.2.0 https://maven.apache.org/xsd/assembly-2.2.0.xsd">
           <fileSets>
            <fileSet>
             <directory>${project.basedir}/src/main/docker</directory>
             <outputDirectory>/</outputDirectory>
             <includes>
              <include>docker-entrypoint.sh</include>
             </includes>
             <fileMode>755</fileMode>
            </fileSet>
            <fileSet>
             <directory>${project.basedir}/target</directory>
             <outputDirectory>/</outputDirectory>
             <includes>
              <include>containerize-*.jar</include>
             </includes>
             <fileMode>755</fileMode>
            </fileSet>
           </fileSets>
          </inline>
         </assembly>
         <tags>
          <tag>${project.version}</tag>
          <tag>latest</tag>
         </tags>
        </build>
       </image>
      </images>
     </configuration>
     <!-- 라이프사이클에 훅 달기 -->
     <executions>
      <execution>
       <id>build</id>
       <goals>
        <goal>build</goal>
       </goals>
      </execution>
     </executions>
    </plugin>
   </plugins>
  </build>
 </profile>
```

```js
위 구성에 대한 중요 사항:

DockerFile -> 이 플러그인은 src/main/docker 디렉토리에 배치된 도커 파일을 자동으로 가져올 것입니다.
도커 파일의 이름만 전달해주면 됩니다. 여기서는 <dockerFile>Dockerfile</dockerFile>을 전달했습니다.

Assembly plugin -> <build> 내부의 <assembly> 요소는 XML 구조를 가지며, 빌드 아티팩트 및 기타 파일이
도커 이미지로 진입하는 방식을 정의합니다. 여러 <assembly> 요소를 추가하여 명시할 수 있습니다.
어셈블리 플러그인에 대한 자세한 내용은 여기에서 확인할 수 있습니다.

outputDirectory -> 이 플러그인에서 사용할 기본 출력 디렉터리입니다. 기본 값은 target/docker의
매이븐 디렉터리입니다. 사용자는 이 디렉터리를 변경할 수 있는 옵션을 가지고 있습니다.

FileSets -> List<FileSet> 유형의 파일 설정으로, 포함된 각 모듈에서 어떤 그룹의 파일을 어셈블리에 포함할지 지정합니다.
한 개 이상의 <fileSet> 하위 요소를 제공하여 fileSet을 지정합니다.
여기서는 도커 파일에 필요한 어플리케이션 jar 및 docker-entrypoint.sh를 포함했습니다.

tags -> 빌드 후 이미지를 태깅할 추가 태그 요소 목록입니다. 여기서는 tag를 'latest'로 추가했으므로,
이 플러그인은 이미지를 생성하고 해당 이미지에 'latest' 태그를 지정합니다.
I이로 인해, 이 구성으로 2개의 이미지가 생성될 것입니다.
```

Step 3: Docker 파일 추가

자바 17을 기본 이미지로 사용하는 Docker 파일을 작성합시다.

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
###########################################################################
# Dockerfile - Spring Boot Application Runner
###########################################################################

# 기본 이미지를 java17로 설정합니다.

FROM openjdk:17-oracle

# 파일 작성자 / 유지 관리자
LABEL org.medium.image.authors="tauseef"

# 기본 환경 변수 정의
ENV APP_HOME=/opt/medium/containerize
# 실행 가능한 artifact의 이름
ENV ARTIFACT_NAME=containerize-*.jar
# Java 디버그 포트
ENV DEBUG_PORT=8000

# 모든 artifact를 홈 디렉토리로 복사합니다
# maven/에서 <assembly> 섹션에 지정된 파일은 수동으로 추가해야 합니다
COPY /maven/docker-entrypoint.sh /
COPY /maven/$ARTIFACT_NAME ${APP_HOME}/

# 권한 부여
USER root
RUN chmod 755 /docker-entrypoint.sh

# 디렉토리에 쓰기 액세스를 위해 777 권한을 설정합니다
RUN chmod -R 777 /opt/medium

# 작업 디렉토리 설정
WORKDIR /opt/medium

# 주요 명령어
USER 185
ENTRYPOINT ["/docker-entrypoint.sh"]
```

이 도커 파일에서 ENTRYPOINT를 사용하고 있으며 이는 docker-entrypoint.sh를 가리킵니다. 파일은 다음과 같습니다.

```js
#!/bin/sh
set -e

echo 'Starting containerize Spring Boot App'

if [ "$DEBUG" = true ]; then
  printf "Running the application in debug mode\n"
  JAVA_OPTS="$JAVA_OPTS -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:$DEBUG_PORT"
fi

# 애플리케이션이 PID 1을 취하고 Docker stop 명령에 의해 보내진 SIGTERM을 수신할 수 있도록 합니다.
# 여기를 참조하세요: https://docs.docker.com/engine/reference/builder/#/entrypoint
exec java $JAVA_OPTS \
       -Djava.security.egd=file:/dev/./urandom -jar \
       ${APP_HOME}/$ARTIFACT_NAME

# 인터럽트가 발생할 때까지 컨테이너를 계속 실행합니다
sleep infinity
```

Step 4: 메이븐 프로필을 사용하여 이미지를 빌드합니다

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

모든 설정이 완료되었고, 이제 우리 애플리케이션의 이미지를 생성할 준비가 되었어요. 다음 명령어를 실행해봐요.

```js
mvn clean install -Pcontainerize
```

이 명령어는 docker-maven 플러그인을 실행시키고 이미지를 생성할 거에요. 빌드에 성공하면 터미널에 다음 로그 라인을 볼 수 있을 거에요.

```js
[INFO] DOCKER> [medium/docker-containerize:0.0.1-SNAPSHOT]: Created docker-build.tar in 318 milliseconds
[INFO] DOCKER> [medium/docker-containerize:0.0.1-SNAPSHOT]: Built image sha256:e091d
[INFO] DOCKER> medium/docker-containerize:0.0.1-SNAPSHOT: Removed dangling image sha256:ea18f
[INFO] DOCKER> [medium/docker-containerize:0.0.1-SNAPSHOT]: Tag with 0.0.1-SNAPSHOT,latest
[INFO] DOCKER> Tagging image medium/docker-containerize:0.0.1-SNAPSHOT successful!
[INFO] DOCKER> Tagging image medium/docker-containerize:latest successful!
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

아래 명령어를 실행하여 확인해주세요

![image1](/assets/img/2024-06-23-FromCodetoContainerDockerizingSpringBootAppswithMaven_2.png)

이제 다음 이미지를 실행해주세요

![image2](/assets/img/2024-06-23-FromCodetoContainerDockerizingSpringBootAppswithMaven_3.png)

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

따라서 컨테이너가 성공적으로 시작되었습니다.

단계 5: 도커 메이븐 플러그인을 사용한 준비된 조립품

도커 메이븐 플러그인은 사용자가 자세한 내용에 들어가서 조립품을 작성할 필요가 없도록 준비된 조립품을 지원합니다. 이는 descriptor-ref를 통해 수행할 수 있습니다. 조립품에서 지원되는 아티팩트 목록은 여기에서 찾을 수 있습니다.
이 설명서를 따라, 우리는 pom 파일의 구성을 다음과 같이 수정하기만 하면 됩니다.

```xml
<profile>
   <id>containerize2</id>
   <build>
    <plugins>
     <plugin>
      <groupId>io.fabric8</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <configuration>
       <images>
        <image>
         <!-- 플레이스홀더의 명명법
          %g= Maven 그룹 이름의 마지막 부분은 여기서 미디엄에 해당합니다
          %v= 프로젝트 버전. ${project.version}의 동의어
          ....................................... -->
         <name>%g/docker-containerize2:%v</name>
         <!-- ....................................... -->
         <!-- 이미지 생성을 위한 빌드 구성 -->
         <!-- ....................................... -->
         <build>
          <dockerFile>Dockerfile2</dockerFile>
          <assemblies>
           <assembly>
            <descriptorRef>artifact</descriptorRef>
           </assembly>
          </assemblies>
          <tags>
           <tag>${project.version}</tag>
           <tag>latest</tag>
          </tags>
         </build>
        </image>
       </images>
      </configuration>
      <!-- 라이프사이클에 훅하기 -->
      <executions>
       <execution>
        <id>docker-build</id>
        <goals>
         <goal>build</goal>
        </goals>
       </execution>
      </executions>
     </plugin>
    </plugins>
   </build>
  </profile>
```

```xml
만약 주목했다면 다음과 같이 전달하기만 하면 됩니다.
            <assembly>
            <descriptorRef>artifact</descriptorRef>
           </assembly>
그리고 이것은 jar 파일을 target/docker의 maven 디렉토리로 복사하고
거기서 Docker 파일이 jar 파일을 선택할 것입니다.
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

결론
Spring Boot 애플리케이션을 컨테이너화하는 것은 배포 프로세스를 효율적으로 만들 뿐만 아니라 다양한 환경에서의 확장성과 일관성을 향상시킵니다. 오늘부터 Spring Boot 프로젝트를 컨테이너화하여 현대적인 애플리케이션 배포의 혜택을 직접 경험해보세요.
