---
title: "Nextjs 앱을 Dockerize하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-DockerizeaNextjsApp_0.png"
date: 2024-06-23 00:47
ogImage:
  url: /assets/img/2024-06-23-DockerizeaNextjsApp_0.png
tag: Tech
originalTitle: "Dockerize a Next.js App"
link: "https://medium.com/@itsuki.enjoy/dockerize-a-next-js-app-4b03021e084d"
---

만약 Vercel에 Next.js 앱을 배포하고 싶다면, 이 경우에는 컨테이너가 필요하지 않습니다. Next.js는 Vercel에서 만들어지고 유지보수되기 때문에 배포가 쉽게 가능합니다. 그러나 AWS, Google Cloud Run 또는 다른 클라우드 공급업체를 통해 앱을 실행하려는 경우에는 컨테이너가 필요합니다.

온라인에서 찾은 대부분의 기사들은 Node.js 앱을 도커라이즈하는 방법을 설명하지만, Next.js 앱에 초점을 많이 두지 않았습니다. 해결책들은 있지만, 그 해결책들은 몇 가지 오류를 발생시켜 몇 시간이 걸렸습니다. 그래서 제가 그것을 어떻게 했는지, 부딪힌 문제점들, 그리고 그에 대한 제 해결책을 공유하려고 합니다.

# 시작하기!

가지고 계신 Next.js 프로젝트 중 하나를 사용하거나, 공식 Vercel에서 제공하는 예제 중 하나를 클론해 시작하세요. 온라인에서 찾은 다른 해결책들은 애플리케이션을 독립 실행형 애플리케이션으로 빌드하기 위해 next.config.js에 다음 라인들을 추가하는 것이 필수적이라고 제안했지만, 그것을 추가하지 않고도 모든 것이 잘 작동하는 것을 발견했습니다.

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
const nextConfig = {
  output: "standalone", // 이 줄이 없어도 잘 작동했다.
  // ... 다른 설정
};
```

## DockerFile

루트 저장소에 동일한 이름을 가진 Dockerfile을 추가하세요. 이 파일에는 빌드할 도커 이미지에 대한 명령을 추가할 것입니다.

제가 만든 두 가지 버전의 Dockerfile을 공유하겠습니다. 하나는 개발 단계/서버 테스트용으로 확실하고 기본적인 단일 단계 버전이며, 다른 하나는 개발 및 프로덕션 모두에 사용할 수 있는 docker-compose.yml과 함께 다중 단계 버전입니다.

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

단계별 DockerFile

아래는 파일이 어떻게 생겼는지를 보여주고, 한 줄씩 내가 한 작업을 설명하겠어.

```js
FROM node:18

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD npm run dev
```

먼저, Docker에 공식 Docker Node 이미지 버전 18을 사용하도록 From node:18을 통해 설정한다. FROM 뒤에 오는 내용을 변경하여 사용하고 싶은 이미지로 변경할 수 있다. 지원되는 목록은 여기에서 확인할 수 있다.

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

WORKDIR은 이후 명령의 컨텍스트를 설정합니다. 이름은 원하는 대로 지정할 수 있어요. 저는 제 앱을 app이라고 부르겠습니다. 그리고 package.json과 package-lock.json 파일을 컨테이너로 복사한 다음 npm install을 실행하여 모든 종속성을 설치합니다.

그 다음, 프로젝트의 모든 코드(현재 루트 디렉토리)를 WORKDIR로 복사합니다. 제 경우에는 /app이에요.

Expose 3000은 컨테이너에게 앱이 3000 포트에서 실행된다는 것을 알려줍니다.

모든 설정을 끝낸 후에는 CMD npm run dev로 컨테이너에게 개발 서버를 시작하도록 요청합니다.

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

이제 Dockerfile로 작업을 마쳤습니다!!

우리가 방금 만든 Dockerfile로부터 Docker 이미지를 빌드하려면, 다음 명령어를 실행하세요. 저는 이를 my-app이라고 이름 지었지만, 원하는 대로 변경해도 상관없습니다. 마지막에 .을 꼭 입력하지 않도록 주의해주세요.

```js
docker build -t my-app .
```

이미지가 생성되면, 다음과 같이 실행할 수 있습니다.

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
도커 런 -p 3000:3000 my-app
```

3000:3000은 앱을 실행할 포트를 지정하는 것이에요. 저는 3000번 포트에서 실행할 거예요. 그리고 http://localhost:3000 에 접속하면 작동 중인 앱을 볼 수 있어요!

다중 단계 DockerFile

이제 다중 단계 Dockerfile을 만들어서 더 빠르고 효율적인 빌드를 할 수 있게 하고, 운영 환경과 개발 환경을 쉽게 전환할 수 있게 될 거예요.

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

도커 파일에 있는 내용을 모두 삭제하고 다음 내용으로 대체해주세요.

```js
FROM node:18-alpine as base
RUN apk add --no-cache g++ make py3-pip libc6-compat
WORKDIR /app
COPY package*.json ./
EXPOSE 3000

FROM base as builder
WORKDIR /app
COPY . .
RUN npm run build


FROM base as production
WORKDIR /app

ENV NODE_ENV=production
RUN npm ci

RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001
USER nextjs


COPY --from=builder --chown=nextjs:nodejs /app/.next ./.next
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./package.json
COPY --from=builder /app/public ./public

CMD npm start

FROM base as dev
ENV NODE_ENV=development
RUN npm install
COPY . .
CMD npm run dev
```

이 경우 node:18-alpine를 사용하겠습니다. 이것은 기본 이미지보다 훨씬 작습니다. -alpine를 사용하려면 모든 다른 것보다 먼저 파이썬을 설치해야 합니다. 그래서 그 추가 명령(RUN apk add — no-cache g++ make py3-pip libc6-compat)이 있는 것입니다. 공통 설정을 base 단계에 넣어서 나중에 다른 단계에서 재사용할 수 있도록 했습니다. 원하는 경우 단계 이름을 변경하여 as 다음에 오는 내용을 변경할 수 있습니다.

빌더 단계는 사실상 npm run build를 담당합니다. 이 단계는 production 단계에서 이미 존재하지 않은 경우 COPY — from=builder를 시도할 때 호출됩니다. 보세요? 다중 단계 Dockerfile이 유용한 점이 여기에 있습니다. 빌더 단계는 필요할 때만 호출됩니다.

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

저희는 프로덕션 단계에서 NODE_ENV를 production으로 설정하고, 이렇게 하면 성능이 세 배 향상된다고 합니다. 그 다음으로, npm ci를 실행합니다. 이것은 npm install 대신에 지속적인 통합을 위해 사용됩니다.

그 후, 보안상의 이유로 앱을 실행할 비루트 사용자를 추가합니다. 제가 게으른 터라 사용자를 nextjs로 그룹을 nodejs로 설정했습니다.

그 후에, 빌더 단계에서 필요한 에셋을 COPY — from=builder를 통해 복사합니다.

마지막으로, npm start를 호출하여 어플리케이션을 시작합니다.

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

개발 스테이지에서는 싱글 스테이지 Dockerfile에서 한 것과 기본적으로 똑같은 작업을 하고 있기 때문에 넘어가도록 할게요.

docker-compose.yml을 생성하기 전에 Dockerfile이 실제로 빌드되는지 확인하고 싶다면 docker build -t my-app . 및 docker run -p 3000:3000 my-app를 실행할 수 있어요. 테스트하고 싶은 스테이지를 주석 처리하는 것을 잊지 마세요. 예를 들어, 프로덕션 스테이지가 성공적으로 빌드되고 실행되는지 확인하려면 FROM base로 시작하는 부분 이후에 오는 모든 것을 주석 처리해주세요.

## Docker Compose

Docker Compose를 사용하면 긴 명령어를 기억할 필요가 없어요. docker-compose build 및 docker-compose up 명령어를 간편하게 사용할 수 있어요.

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

루트 디렉토리에 다음 내용을 가진 docker-compose.yml 파일을 추가해주세요.

```js
version: '3.8'
services:
  app:
    image: openai-demo-app
    build:
      context: ./
      target: dev
      dockerfile: Dockerfile
    volumes:
        - .:/app
        - /app/node_modules
        - /app/.next
    ports:
      - "3000:3000"
```

여기서 `3.8` 버전은 사용할 Docker Compose 버전을 지정합니다. 이 경우에는 한 개의 서비스인 app을 가지고 있지만, 필요에 따라 더 추가할 수 있습니다.

Build context는 현재 디렉토리를 지정하며, target은 Docker 이미지를 빌드할 단계를 지정합니다. 프로덕션에서 실행하려면 단순히 target:production으로 설정하시면 됩니다.

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

Volume은 호스트의 ./ 로컬 디렉토리의 내용을 Docker 컨테이너의 /app 디렉토리로 복사하도록 지시합니다.

마지막으로, 호스트 머신의 포트 3000을 컨테이너의 포트 3000으로 매핑합니다. 우리는 컨테이너를 빌드할 때 포트 3000을 노출했으며, 우리의 앱도 3000 포트에서 실행될 것입니다.

## Docker Compose로 테스트하기

마침내 Docker 이미지를 빌드하는 과정에 도달했습니다. 더 빠른 빌드를 위해 Docker의 BuildKit 기능을 사용할 것입니다.

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
COMPOSE_DOCKER_CLI_BUILD=1 DOCKER_BUILDKIT=1 docker-compose build
```

빌드가 완료되면 이미지를 실행하여 앱을 시작할 수 있어요.

```js
docker-compose up
```

그 다음에 브라우저에서 http://localhost:3000 로 이동하면 앱이 실행 중인 것을 볼 수 있어요!!!

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

그게 다야!

아래에는 몇 가지 문제가 있었어요. 내 Docker 컨테이너가 작동하지 않거나 문제의 원인을 찾고 싶다면 고생 중일 수도 있으니 빠르게 공유할게요.

# 내가 겪은 문제들

- Python이 명령줄이나 npm 구성에서 설정되지 않았습니다: (맥 사용자로서) python을 경로에 추가하거나 python을 python3로 별칭 지정, python 삭제 및 다시 설치 등 많은 시도를 해보았지만 어떤 것도 제겐 도움이 되지 않았어요. 그래서 제가 찾아낸 두 가지 해결책을 여기에 소개할게요. (1) node:18(또는 기호하는 다른 버전) 대신 node:18-alpine을 사용하세요. (2) 패키지 설치 전에 RUN apk add --no-cache g++ make py3-pip libc6-compat 를 추가하세요.
- Docker-compose up을 사용할 때 '/app/.next' 디렉토리에서 프로덕션 빌드를 찾을 수 없음: docker run을 통해 이미지를 실행했을 때는 모든 것이 예상대로 작동했습니다. 해결 방법은 Dockerfile에서 CMD npm start 앞에 CMD ["npm","run","build"]를 추가하는 것을 제안하는 솔루션이 있지만, 이렇게 하면 두 개의 CMD를 허용하지 않는 오류가 발생했어요. 제 해결책은 Docker-compose.yml의 volumes에 — /app/.next를 추가하는 것이었습니다.

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

오늘은 여기까지입니다!

읽어 주셔서 감사합니다! 이 팁들이 도움이 되길 바랍니다!
