---
title: "도커에서 자체 서명 인증서를 사용한 NGINX"
description: ""
coverImage: "/assets/img/2024-06-19-NGINXwithSelf-SignedCertificateonDocker_0.png"
date: 2024-06-19 12:57
ogImage: 
  url: /assets/img/2024-06-19-NGINXwithSelf-SignedCertificateonDocker_0.png
tag: Tech
originalTitle: "NGINX with Self-Signed Certificate on Docker"
link: "https://medium.com/better-programming/nginx-self-signed-certificate-docker-f3861c0f03e"
---


<img src="/assets/img/2024-06-19-NGINXwithSelf-SignedCertificateonDocker_0.png" />

저희 코드를 작업하는 동안 HTTPS에서 작업이 잘되는지 또는 더 중요한 것은 HTTPS에서 작동하는 방식을 빠르게 확인해야할 때가 많습니다. 온라인에서는 CSR(Certificate Sign Request)를 생성하고 해당 CSR을 자체로 서명하고 웹 서버의 구성을 수동으로 수정하여 해당 인증서를 사용하도록 만드는 방법을 보여주는 가이드가 많이 있습니다.

이 기사에서는 어떤 것도 생성하거나 수동으로 편집하지 않고도 도커를 사용하여 자체 서명된 인증서가 있는 NGINX 컨테이너를 빠르게 실행하는 완전 자동화된 프로세스를 제시하겠습니다!

# 보안 주의사항과 경고

<div class="content-ad"></div>

- 자체 서명된 인증서는... 당신만 신뢰할 수 있습니다. 생산 환경에서 데이터를 제공하는 수단으로 사용할 수 없습니다. 그런 경우에는 적절한 인증서를 사용하세요.
- 이 글에서 제시된 HTTPS로 콘텐츠를 제공하게끔 NGINX 구성은 작업을 수행하기 위한 최소한의 것입니다. 본격적인 TLS가 적용된 프로덕션 NGINX를 수정하려면 공식 가이드를 참고하세요.

공개 키 암호화를 처음 시작하는 경우, 도움이 될 수 있는 소개 기사를 작성했습니다.

# 설계 디자인

빌드는 2단계 Docker 빌드로 설계되었습니다.

<div class="content-ad"></div>


<img src="/assets/img/2024-06-19-NGINXwithSelf-SignedCertificateonDocker_1.png" />

첫 번째 단계에서 Alpine Linux 이미지를 사용합니다. Alpine의 패키지 관리자인 APK를 사용하여 OpenSSL을 설치합니다. 다음 단계에서 OpenSSL을 사용하여 셀프 서명 인증서와 관련 개인 키를 생성합니다.

두 번째 단계에서 NGINX 이미지를 사용합니다. 빌드는 이전 단계에서 생성된 인증서와 개인 키를 포함하도록 이미지를 수정하고 HTTPS를 활성화하기 위한 간단한 NGINX 구성을 작성합니다.

# Dockerfile


<div class="content-ad"></div>

```dockerfile
# syntax=docker/dockerfile:1
# 이 Dockerfile을 빌드하려면 Docker BuildKit가 활성화되어 있어야 합니다.

# 사용할 Alpine 및 NGINX 버전을 정의합니다.
ARG ALPINE_VERSION=3.17.3
ARG NGINX_VERSION=1.23.4

# OpenSSL을 사용하기 위해 Alpine 기반 이미지를 준비합니다.
FROM alpine:${ALPINE_VERSION} as alpine
ARG DOMAIN_NAME=localhost
ARG DAYS_VALID=30

RUN apk add --no-cache openssl
RUN echo "${DAYS_VALID}일 동안 유효한 ${DOMAIN_NAME} 도메인을 위한 자체 서명 인증서를 생성합니다." && \
    openssl \
    req -x509 \
    -nodes \
    -subj "/CN=${DOMAIN_NAME}" \
    -addext "subjectAltName=DNS:${DOMAIN_NAME}" \
    -days ${DAYS_VALID} \
    -newkey rsa:2048 -keyout /tmp/self-signed.key \
    -out /tmp/self-signed.crt

# 위에서 생성한 인증서를 사용하여 NGINX 기반 이미지를 준비합니다.
FROM nginx:${NGINX_VERSION} as nginx
COPY --from=alpine /tmp/self-signed.key /etc/ssl/private
COPY --from=alpine /tmp/self-signed.crt /etc/ssl/certs
COPY <<EOF /etc/nginx/conf.d/default.conf
server {
    listen 80;
    listen [::]:80;
    listen 443 ssl;
    listen [::]:443 ssl;
    ssl_certificate /etc/ssl/certs/self-signed.crt;
    ssl_certificate_key /etc/ssl/private/self-signed.key;
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
EOF
```

👉 위의 Dockerfile을 시도하기 전에 Docker BuildKit가 활성화되어 있는지 확인해주세요. BuildKit는 레거시 빌더를 대체하는 개선된 백엔드로, Docker 데스크톱 및 Docker Engine 버전 23.0부터 사용자들에게 기본 빌더로 제공됩니다.

## Stage 1: 인증서 생성

인증서 및 개인 키를 생성하기 위해 OpenSSL을 사용하며 필요한 모든 정보를 인수로 전달하여 대화형 모드가 아닌 모드에서 명령을 실행합니다. 다음과 같은 Docker ARG를 지정하여 이 단계를 자신의 요구에 맞게 조정할 수 있습니다.

<div class="content-ad"></div>

- DOMAIN_NAME: 이는 인증서가 유효한 도메인입니다. 이는 자체 서명된 인증서이므로 여기에 지정한 도메인 이름은 중요한 역할을 하지 않지만, 해당 인증서를 사용해야 하는 응용 프로그램이 있는 경우, 액세스할 리소스의 도메인 이름과 일치하도록 변경해야 할 수 있습니다. 빌드에 사용된 기본 도메인은 localhost입니다.
- DAYS_VALID: 인증서가 유효한 일수입니다. 테스트를 완료할 수 있도록 충분히 큰 숫자를 사용하십시오. 빌드에 사용된 기본 유효 기간은 30일입니다.

## 단계 2: 수정된 NGINX 이미지 생성

이 단계에서는 이전 단계에서 생성된 인증서와 개인 키를 가져와 새로 생성된 이미지로 복사합니다. 또한 HTTPS를 활성화하기 위해 간단한 NGINX 구성을 생성하기 위해 heredoc를 사용합니다.

직접 이미지 상에 다른 구성을 사용하려는 경우, heredoc를 자체 콘텐츠로 대체하거나 결과 이미지를 확장하여 자체 이미지에 추가할 수 있습니다. 이미지를 자체 구성 파일로 확장하는 경우, 해당 파일은 다음 위치에 배치해야 합니다.
/etc/nginx/conf.d/default.conf.

<div class="content-ad"></div>

# 이미지 빌드하기

알파인 이미지와 NGINX 이미지는 매우 작기 때문에 빌드 속도가 정말 빠릅니다. 다음으로 빌드를 시작하세요:

```bash
docker build . -t nginx-self-signed
```

![이미지](/assets/img/2024-06-19-NGINXwithSelf-SignedCertificateonDocker_2.png)

<div class="content-ad"></div>

# 이미지 실행하기

이미지를 실행할 때, Docker Engine이 실행 중인 컴퓨터에서 80번 포트를 HTTP용, 그리고 443번 포트를 HTTPS용으로 사용할 수 있도록 해 주세요. 아래 명령어를 사용하여 컨테이너를 시작할 수 있습니다:


docker run -p 80:80 -p 443:443 nginx-self-signed


![NGINXwithSelf-SignedCertificateonDocker_3](/assets/img/2024-06-19-NGINXwithSelf-SignedCertificateonDocker_3.png)

<div class="content-ad"></div>

# 이미지 테스트 중

신뢰할 수 있는 curl을 사용해서 몇 가지 테스트를 해봅시다. 만약 도커 엔진이 로컬 호스트에서 실행되지 않는다면 localhost를 적절한 주소로 바꿔주셔야 합니다.

## HTTP 접근

curl localhost

<div class="content-ad"></div>


![Screenshot](/assets/img/2024-06-19-NGINXwithSelf-SignedCertificateonDocker_4.png)

여기에 볼 건 별거 없어요. HTTP 접근은 예상대로 작동합니다.

## HTTPS 접근

curl https://localhost


<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-NGINXwithSelf-SignedCertificateonDocker_5.png" />

여기서 NGINX가 HTTPS 요청에 응답했지만 curl은 다음과 같은 오류로 처리를 거부했습니다:

curl: (60) SSL certificate problem: self signed certificate

이것은 HTTPS를 위한 기본 TLS를 설정하는 데 사용된 자체 서명된 인증서가 컴퓨터에 의해 신뢰되지 않기 때문입니다. 그렇다면 어떻게 해야 할까요? 여기에는 몇 가지 다른 옵션이 있습니다:

<div class="content-ad"></div>

- 자체 서명된 인증서를 OS의 신뢰/인증서 저장소에 가져올 수 있습니다.
- curl에 보안 인증서를 수락하도록 지시할 수 있습니다.

보안 인증서를 수락하도록 curl에 요청해 봅시다:

curl https://localhost --insecure

![이미지](/assets/img/2024-06-19-NGINXwithSelf-SignedCertificateonDocker_6.png)

<div class="content-ad"></div>

curl은 이제 기초 리소스의 내용을 즐겁게 출력합니다.

마찬가지로, HTTPS URL을 인터넷 브라우저로 열어보려고 하면 보안 경고가 표시됩니다. 실제 경고 메시지와 진행 방법은 각각 다른 인터넷 브라우저마다 다를 수 있습니다. Chrome에서는 다음과 같이 표시됩니다:

![Chrome에서의 보안 경고](/assets/img/2024-06-19-NGINXwithSelf-SignedCertificateonDocker_7.png)

Chrome에서 '고급' 버튼을 클릭한 다음 'localhost로 진행(안전하지 않음)' 옵션을 클릭할 수 있습니다:

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-NGINXwithSelf-SignedCertificateonDocker_8.png" />

# 추가 콘텐츠

가기 전에, 여기서 생성한 이미지를 사용하여 여러분 자신의 콘텐츠와 함께 사용할 수 있는 몇 가지 추가 팁이 있습니다.

## 1. 여러분 자신의 콘텐츠 제공

<div class="content-ad"></div>

기본 NGINX 환영 페이지를 제공하는 것은 누구에게나 큰 가치가 없을 것 같아요. 자신의 콘텐츠로 사용자 정의 NGINX 이미지를 가리킬 수 있도록 로컬 폴더를 컨테이너에 마운트하면 됩니다:

```js
docker run \
  -p 80:80 -p 443:443 \
  -v {YOUR-PATH}:/usr/share/nginx/html \
nginx-self-signed
```

## 2. 자신의 콘텐츠에 대한 역방향 프록시 구성

컨테이너 내부에 콘텐츠를 마운트하고 싶지 않을 경우, NGINX를 구성하여 이미 실행 중인 다른 서버에 대한 역방향 프록시로 사용할 수 있습니다. 다음 코드 조각은 이러한 구성을 설정하는 데 도움이 되나요. 그러나 역방향 프록시는 아래의 간단한 예제보다 더 복잡한데, 실제로 프록시하는 내용에 따라 다양한 문제가 발생할 수 있습니다:

<div class="content-ad"></div>

NGINX 설정 파일의 상단에 upstream 서버를 정의해주세요:

```js
upstream api-gateway {
  server http://some-server:80;
}
```

NGINX 설정 파일의 server 블록을 다음과 같이 업그레이드해주세요:

```js
location /api/ {
    proxy_pass                http://api-gateway;
    proxy_redirect            off;
    proxy_set_header          X-Real-IP $remote_addr;
    proxy_set_header          X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header          X-NginX-Proxy true;
    proxy_ssl_session_reuse   off;
    proxy_set_header Host     $http_host;
    proxy_cache_bypass        $http_upgrade;
}
```

<div class="content-ad"></div>

# 결론

이 글에서는 자체 서명 인증서를 사용하는 NGINX 도커 컨테이너를 빠르게 설정하는 방법을 소개했습니다. 컴퓨터에 OpenSSL을 설치할 필요가 없으며 인증서를 생성하기 위해 openssl 명령을 실행할 필요가 없습니다. 모든 작업이 Docker 빌드의 일부로 실행됩니다.

또한 결과물인 NGINX 이미지에 자신의 콘텐츠를 통합하는 두 가지 예제를 제공했습니다. 컨테이너 내에서 콘텐츠를 마운트하거나 이미 실행 중인 다른 서버로 역방향 프록시하는 방법 등이 있습니다.

이 글을 읽어주셔서 감사합니다. 다음 글에서 다시 뵙기를 기대합니다.

<div class="content-ad"></div>

🔔 새 이야기를 발행할 때마다 알림을 받고 싶으세요? 제 콘텐츠는 항상 제가 본 것이나 일했던 것을 바탕으로 한 실용적인 기술 팁과 소프트웨어 엔지니어링 조언을 제공합니다:
https://nmichas.medium.com/subscribe

🚀 아직 Medium 회원이 아니신가요? 커피 한 잔 가격으로 매월 제 이야기에 액세스할 수 있습니다 (그리고 Medium의 수천 명의 다른 작가들의 이야기에도):
https://medium.com/@nmichas/membership