---
title: "Docker로 ReactJS 애플리케이션 컨테이너화하는 방법"
description: ""
coverImage: "/assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_0.png"
date: 2024-07-01 16:15
ogImage: 
  url: /assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_0.png
tag: Tech
originalTitle: "Containerization of React JS Application using Docker"
link: "https://medium.com/@jaydeepvpatil225/containerization-of-react-js-application-using-docker-d96519588cfe"
---


<img src="/assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_0.png" />

안녕하세요

이 글에서는 샘플 React JS 애플리케이션을 생성하고 Docker를 활용하여 컨테이너화하는 방법을 배워보겠습니다.

일정

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

- React JS 애플리케이션 샘플
- Docker 파일 생성
- 애플리케이션 컨테이너화

필수 요구사항

- NPM
- React JS
- Docker Engine

React JS 애플리케이션 샘플

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

단계 1:

아래 명령어를 사용하여 새 React JS 애플리케이션을 만듭니다.

참고: 머신에 NPM 및 React JS가 이미 설치되어 있는지 확인하세요.


npx create-react-app reactjs-app-docker


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


![이미지](/assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_1.png)

단계 2:

애플리케이션 디렉토리로 이동하여 애플리케이션을 실행합니다.

npm start


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

아래는 Markdown 형식으로 변경된 테이블입니다.


![이미지 1](/assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_2.png)

![이미지 2](/assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_3.png)

Docker 파일 생성

```js
# 공식 Node.js 베이스 이미지 사용
FROM node:18

# 작업 디렉토리 설정
WORKDIR /app

# package.json 및 package-lock.json 파일 복사
COPY package*.json ./

# 종속 항목 설치
RUN npm install

# 나머지 애플리케이션 코드 복사
COPY . .

# React 앱 빌드
RUN npm run build

# 빌드 폴더를 제공하는 serve를 전역으로 설치
RUN npm install -g serve

# 앱이 실행되는 포트 노출
EXPOSE 3000

# React 앱 시작
CMD ["serve", "-s", "build"]
```


안내해 주셔서 감사합니다. 기타 요청이 있으시면 언제든지 말씀해주세요!

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

아래는 Docker 파일의 각 단계를 설명합니다:

![이미지](/assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_4.png)

이 줄은 Docker 이미지의 기본 이미지를 지정합니다. 우리는 Node.js의 공식 이미지를 버전 18으로 사용합니다. 이 이미지에는 Node.js 애플리케이션을 실행하는 데 필요한 모든 것이 포함되어 있습니다.

![이미지](/assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_5.png)

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

위 줄은 Docker 컨테이너 내의 작업 디렉토리를 /app으로 설정합니다. 이후의 모든 명령은 /app 디렉토리에서 실행됩니다. 이는 컨테이너 내에서 파일 시스템을 조직화하는 데 도움이 됩니다.

![이미지](/assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_6.png)

호스트 머신에서 package.json 및 package-lock.json을 Docker 컨테이너의 현재 작업 앱 디렉토리로 복사합니다. 이 파일들은 애플리케이션의 의존성을 설치하는 데 필요합니다.

![이미지](/assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_7.png)

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

이 단계는 package.json에 정의된 종속성을 설치하기 위해 npm install을 실행합니다. 이 단계를 통해 모든 필요한 Node.js 패키지가 컨테이너에 설치됩니다.

![image](/assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_8.png)

다음으로, 호스트 머신의 현재 디렉토리에서 Docker 컨테이너의 현재 작업 디렉토리로 모든 파일과 디렉토리를 복사합니다. 이는 소스 코드와 애플리케이션을 빌드하고 실행하는 데 필요한 모든 파일을 포함합니다.

![image](/assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_9.png)

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

아래 명령은 React 애플리케이션을 프로덕션을 위해 빌드하는 npm run build를 실행합니다. 빌드 프로세스는 React 코드를 컴파일합니다.

![이미지](/assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_10.png)

Docker 컨테이너 내부에 serve 패키지를 글로벌로 설치합니다. serve는 빌드된 React 애플리케이션을 제공하는 데 사용할 간단한 정적 파일 서버입니다.

![이미지](/assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_11.png)

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

이 줄은 Docker에게 컨테이너가 실행 중일 때 포트 3000에서 수신 대기함을 알려줍니다. 이 포트는 React 애플리케이션이 제공될 포트입니다.

![이미지](/assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_12.png)

다음으로, 컨테이너가 시작될 때 실행할 명령을 지정합니다. 여기서 serve -s build는 serve 정적 파일 서버를 시작하고 React 빌드 프로세스에서 컴파일된 정적 파일이 포함된 build 디렉토리의 내용을 제공합니다.

애플리케이션의 컨테이너화

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

안녕하세요! 위 아래로 팔을 펴고 팔다리를 흔들어보세요. 근육들을 풀어주는 운동이죠! 함께 힘내봐요! 🏋🏻‍♂️🚀

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

아래와 같이 Markdown 형식으로 변경하면 됩니다.


![Containerization of ReactJS Application using Docker](/assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_13.png)

Step 2:

Run the docker image.

docker run -p 3000:3000 reactjs-app-docker


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


![이미지](/assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_14.png)

![이미지](/assets/img/2024-07-01-ContainerizationofReactJSApplicationusingDocker_15.png)

Github:

https://github.com/Jaydeep-007/reactjs-app-docker


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

이 글에서는 샘플 React JS 애플리케이션을 만들었습니다. 이후 Docker 파일을 생성하고 각 단계와 목적을 이해했습니다. 마지막으로 Docker 명령어를 사용하여 Docker 이미지 파일을 빌드하고 컨테이너화했습니다.