---
title: "Ruby on Rails 응용 프로그램을 PostgreSQL을 사용하여 VPS에 Kamal을 이용하여 배포하는 단계별 안내"
description: ""
coverImage: "/assets/img/2024-06-19-Step-by-StepGuidetoDeployingaRubyonRailsApplicationwithPostgreSQLonVPSusingKamal_0.png"
date: 2024-06-19 22:12
ogImage: 
  url: /assets/img/2024-06-19-Step-by-StepGuidetoDeployingaRubyonRailsApplicationwithPostgreSQLonVPSusingKamal_0.png
tag: Tech
originalTitle: "Step-by-Step Guide to Deploying a Ruby on Rails Application with PostgreSQL on VPS using Kamal"
link: "https://medium.com/@talha.97.mahmood/step-by-step-guide-to-deploying-a-ruby-on-rails-application-with-postgresql-on-vps-using-kamal-6a0f651b7a56"
---


![image](/assets/img/2024-06-19-Step-by-StepGuidetoDeployingaRubyonRailsApplicationwithPostgreSQLonVPSusingKamal_0.png)

이 튜토리얼에서는 DigitalOcean에서 제공하는 Virtual Private Server (VPS)에 Ruby on Rails 애플리케이션을 PostgreSQL 데이터베이스와 함께 배포하는 과정을 안내하겠습니다. Docker 기반 애플리케이션을 자동화하는 배포 도구 인 Kamal을 활용하여 배포 프로세스를 자동화할 것입니다.

# 전제 조건

시작하기 전에 다음 사항을 갖추었는지 확인하세요:

<div class="content-ad"></div>

- PostgreSQL이 구성된 Ruby on Rails 애플리케이션.
- VPS (droplet)가 프로비저닝되어 SSH 액세스가 설정된 DigitalOcean 계정. 원하는 제공업체를 사용해도 됩니다.

# 단계 1: Kamal 설치

로컬 머신에 Kamal이 설치되어 있는지 확인하세요. RubyGems를 통해 설치할 수 있습니다:

```js
gem install kamal
```

<div class="content-ad"></div>

# 단계 2: 배포 설정 구성

프로젝트 디렉토리로 이동한 다음 다음을 실행하세요:

```js
kamal init
```

이 명령은 deploy.yml 설정 파일을 생성하고 다른 몇 가지 파일을 함께 생성합니다. deploy.yml 구성 파일의 여러 섹션을 이해해 봅시다.

<div class="content-ad"></div>

```js
# 애플리케이션의 이름. 고유하게 컨테이너를 구성하는 데 사용됩니다.
service: mynewapp
```

애플리케이션의 이름을 지정합니다. 이는 애플리케이션과 관련된 컨테이너를 고유하게 구성하는 데 사용됩니다.

```js
# 컨테이너 이미지의 이름.
image: talha/mynewapp
```

애플리케이션에 대한 Docker 컨테이너 이미지의 이름을 지정합니다. talha/mynewapp를 Docker Hub 저장소의 이름으로 교체하세요.


<div class="content-ad"></div>

```js
# 이러한 서버에 배포할 수 있습니다.
서버:
  웹:
    호스트:
      - 123.123.45.678
    라벨:
      traefik.http.routers.mynewapp.rule: Host(`mynewapp.com`)
      traefik.http.routers.mynewapp.entrypoints: websecure
      traefik.http.routers.mynewapp_secure.rule: Host(`mynewapp.com`)
      traefik.http.routers.mynewapp_secure.tls: true
      traefik.http.routers.mynewapp_secure.tls.certresolver: letsencrypt
    옵션:
      네트워크: "private"
```

어플리케이션이 배포될 서버를 정의하고, HTTP 요청을 앱에 라우팅하고 TLS/SSL 암호화를 활성화하기 위한 Traefik 설정을 구성합니다.

```js
# 이미지 호스트에 대한 자격 증명.
레지스트리:
  사용자 이름:
    - KAMAL_REGISTRY_USERNAME
  비밀번호:
    - KAMAL_REGISTRY_PASSWORD
```

어플리케이션 이미지가 호스팅된 Docker 이미지 레지스트리에 액세스하는 자격 증명을 지정합니다. KAMAL_REGISTRY_USERNAME 및 KAMAL_REGISTRY_PASSWORD를 Docker 허브 자격 증명으로 교체하세요.

<div class="content-ad"></div>

```yaml
# 컨테이너에 환경 변수 주입 (시크릿은 .env에서 온다).
# 변경 후 `kamal env push`를 실행하는 것을 잊지 마세요!
env:
  clear:
    RAILS_ENV: production
    RACK_ENV: production
    RAILS_LOG_TO_STDOUT: true
    RAILS_SERVE_STATIC_FILES: true
  secret:
    - RAILS_MASTER_KEY
    - SMTP_PASSWORD
    - SMTP_SERVER
    - SMTP_LOGIN
    - DB_HOST
    - POSTGRES_USER
    - POSTGRES_PASSWORD
```

컨테이너의 환경 변수를 지정합니다. clear 변수는 공개적으로 접근 가능하며, 시크릿 변수는 비공개로 유지됩니다. 이러한 변수는 .env 파일에서 가져옵니다.

```yaml
# root가 아닌 다른 ssh 사용자 사용
ssh:
  user: deploy
```

- 서버에 액세스하는 데 사용할 SSH 사용자를 지정합니다. 이 경우 사용자는 deploy입니다.

<div class="content-ad"></div>

```yaml
# 빌더 설정 구성.
빌더:
  원격:
    아키텍처: amd64
```

빌더 설정을 구성하여 응용 프로그램을 빌드할 원격 서버의 아키텍처를 지정합니다. 위의 구성은 Apple Silicon에서 개발하고 있지만 amd64 도커 이미지만 빌드하려는 경우 유용합니다.

```yaml
# 부가 서비스 사용 (비밀은 .env에서 제공됨).
부가서비스:
  db:
    이미지: postgres:16.0
    호스트: 123.123.45.678
    환경:
      클리어:
        POSTGRES_USER: "mynewapp"
        POSTGRES_DB: 'mynewapp_production'
      시크릿:
        - POSTGRES_PASSWORD
        - POSTGRES_USER
    파일:
      - config/init.sql:/docker-entrypoint-initdb.d/setup.sql
    디렉토리:
      - data:/var/lib/postgresql/data
    옵션:
      네트워크: "private"
```

응용 프로그램에서 필요한 추가 서비스를 정의합니다. 이 경우 지정된 버전과 환경 변수를 갖는 PostgreSQL 데이터베이스 서비스를 구성합니다. 파일 지시문을 사용하여 자체 엔트리포인트를 제공할 수 있습니다. config/init.sql은 config/database.yml 구성에서 예상한대로 데이터베이스를 생성해야 합니다.

<div class="content-ad"></div>

```js
CREATE DATABASE mynewapp_production;
```

위 명령어를 사용하여 새로운 파일 config/init.sql을 만들어주세요.

```js
production:
  <<: *default
  database: mynewapp_production
  username: <%= ENV["POSTGRES_USER"] %>
  password: <%= ENV["POSTGRES_PASSWORD"] %>
  host: <%= ENV["DB_HOST"] %>
```

또한, 위와 같이 database.yml 파일을 업데이트하여 프로덕션 DB 구성을 업데이트해주세요.

<div class="content-ad"></div>

```yaml
traefik:
  options:
    publish:
      - "443:443"
    volume:
      - "/letsencrypt/acme.json:/letsencrypt/acme.json"
    network: "private"
  args:
    entryPoints.web.address: ":80"
    entryPoints.websecure.address: ":443"
    entryPoints.web.http.redirections.entryPoint.to: websecure
    entryPoints.web.http.redirections.entryPoint.scheme: https
    entryPoints.web.http.redirections.entrypoint.permanent: true
    certificatesResolvers.letsencrypt.acme.email: "info@mynewapp.com"
    certificatesResolvers.letsencrypt.acme.storage: "/letsencrypt/acme.json"
    certificatesResolvers.letsencrypt.acme.httpchallenge: true
    certificatesResolvers.letsencrypt.acme.httpchallenge.entrypoint: web
```

Traefik을 설정하여 HTTPS 요청을 처리하고 Let's Encrypt를 사용하여 SSL 인증서를 자동으로 관리하도록 구성합니다.

아래에 완전한 deploy.yml 파일을 찾을 수 있어요:

```yaml
# 애플리케이션의 이름. 컨테이너를 고유하게 설정하는 데 사용됩니다.
service: mynewapp

# 컨테이너 이미지의 이름입니다.
image: talha/mynewapp

# 이 서버로 배포합니다.
servers:
  web:
    hosts:
      - 123.123.45.678
    labels:
      traefik.http.routers.mynewapp.rule: Host(`mynewapp.com`)
      traefik.http.routers.mynewapp.entrypoints: websecure
      traefik.http.routers.mynewapp_secure.rule: Host(`mynewapp.com`)
      traefik.http.routers.mynewapp_secure.tls: true
      traefik.http.routers.mynewapp_secure.tls.certresolver: letsencrypt
    options:
      network: "private"

# 이미지 호스트의 자격 증명입니다.
registry:
  username:
    - KAMAL_REGISTRY_USERNAME
  password:
    - KAMAL_REGISTRY_PASSWORD

# 컨테이너로 ENV 변수를 주입합니다(비밀은 .env에서 가져옵니다).
# 변경 후 `kamal env push`를 실행하는 것을 잊지 마세요!
env:
  clear:
    RAILS_ENV: production
    RACK_ENV: production
    RAILS_LOG_TO_STDOUT: true
    RAILS_SERVE_STATIC_FILES: true
  secret:
    - RAILS_MASTER_KEY
    - SMTP_PASSWORD
    - SMTP_SERVER
    - SMTP_LOGIN
    - DB_HOST
    - POSTGRES_USER
    - POSTGRES_PASSWORD

# root가 아닌 다른 ssh 사용자를 사용합니다.
ssh:
  user: deploy

# 빌더 설정을 구성합니다.
builder:
  remote:
    arch: amd64

# 보조 서비스를 사용합니다(비밀은 .env에서 가져옵니다).
accessories:
  db:
    image: postgres:16.0
    host: 123.123.45.678
    env:
      clear:
        POSTGRES_USER: "mynewapp"
        POSTGRES_DB: 'mynewapp_production'
      secret:
        - POSTGRES_PASSWORD
        - POSTGRES_USER
    files:
      - config/init.sql:/docker-entrypoint-initdb.d/setup.sql
    directories:
      - data:/var/lib/postgresql/data
    options:
      network: "private"

traefik:
  options:
    publish:
      - "443:443"
    volume:
      - "/letsencrypt/acme.json:/letsencrypt/acme.json"
    network: "private"
  args:
    entryPoints.web.address: ":80"
    entryPoints.websecure.address: ":443"
    entryPoints.web.http.redirections.entryPoint.to: websecure
    entryPoints.web.http.redirections.entryPoint.scheme: https
    entryPoints.web.http.redirections.entrypoint.permanent: true
    certificatesResolvers.letsencrypt.acme.email: "info@mynewapp.com"
    certificatesResolvers.letsencrypt.acme.storage: "/letsencrypt/acme.json"
    certificatesResolvers.letsencrypt.acme.httpchallenge: true
    certificatesResolvers.letsencrypt.acme.httpchallenge.entrypoint: web

asset_path: /rails/public/assets
``` 


<div class="content-ad"></div>

위의 예시를 참고하여 실제 애플리케이션 이름, 도메인 및 서버 IP 주소로 mynewapp, mynewapp.com 및 123.123.45.678와 같은 자리 표시자를 교체해주세요.

# 단계 3: 배포 사용자 생성

VPS에 SSH로 접속하고 새로운 사용자 deploy를 만들어보세요. 아래 명령어를 실행하세요.

로컬 머신에서 터미널을 열고 SSH를 사용하여 VPS에 연결합니다. 실제 VPS의 IP 주소로 your_server_ip를 교체해주세요.

<div class="content-ad"></div>

```sh
ssh root@your_server_ip
```

다음 명령어를 실행하여 새로운 사용자 deploy를 생성하세요:

```sh
adduser deploy
```

새 사용자에 대한 암호를 설정하고 추가 정보를 입력하라는 프롬프트가 표시됩니다. 선택 사항을 건너뛰려면 Enter 키를 누르세요.

<div class="content-ad"></div>

(선택사항) 배포 사용자에게 sudo 권한을 부여하고 싶다면 다음 명령을 실행하여 sudo 그룹에 추가할 수 있습니다:

```js
usermod -aG sudo deploy
```

# 단계 4: VPS에 Docker 설치하기:

Docker를 설치하기 전에 패키지 인덱스를 업데이트하는 것이 좋은 실천법입니다:

<div class="content-ad"></div>

```js
apt update
```

HTTPS를 통해 저장소를 사용할 수 있도록 패키지를 설치하고 Docker에 필요한 몇 가지 패키지를 설치하세요:

```js
apt install -y apt-transport-https ca-certificates curl software-properties-common
```

시스템에 Docker의 공식 GPG 키를 추가하세요.


<div class="content-ad"></div>

```js
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Docker 리포지토리를 APT 원본에 추가하세요:

```js
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

한번 더 패키지 인덱스를 업데이트하세요:

<div class="content-ad"></div>

```bash
apt update
```

최신 버전의 Docker CE (Community Edition)와 containerd를 함께 설치하려면:

```bash
apt install -y docker-ce docker-ce-cli containerd.io 
```

Docker 서비스를 시작하고 부팅 시 자동으로 시작되도록 설정하세요.

<div class="content-ad"></div>

```js
systemctl start docker
systemctl enable docker
```

다음 명령어를 실행하여 Docker가 올바르게 설치되었는지 확인하세요. 이 명령은 Docker 버전 정보를 출력해야 합니다:

```js
docker --version
```

마지막으로, 프라이빗 Docker 네트워크를 생성하세요.

<div class="content-ad"></div>

```js
도커 네트워크 생성 -d bridge private
```

# 단계 5: LetsEncrypt 설치하기:

로컬 머신에서 터미널을 열고 SSH를 사용하여 VPS에 연결합니다. 실제 VPS의 IP 주소로 your_server_ip를 대체하십시오.

```js
ssh root@your_server_ip
```

<div class="content-ad"></div>

다음 명령을 사용하여 Let's Encrypt 디렉토리를 설정하세요:

```js
mkdir -p /letsencrypt && touch /letsencrypt/acme.json && chmod 600 /letsencrypt/acme.json
```

# 단계 6: 애플리케이션 배포

처음부터 모든 단계를 거쳐 config/deploy.yml 파일을 작성했고 배포할 준비가 되었다면, 다음을 실행하세요:

<div class="content-ad"></div>


```js
kamal setup
```

# Step 7: 일일 업무 흐름

카말의 명령어를 사용하여 매일 배포 작업을 수행하세요:

앱의 새 버전을 배포하려면:


<div class="content-ad"></div>

```js
kamal deploy
```

환경 변수를 업데이트하려면:

```js
kamal env push
```

새 컨테이너에서 bash 세션을 시작하려면:

<div class="content-ad"></div>

```js
kamal app exec -i bash
```

새로운 컨테이너에서 Rails 콘솔을 시작하려면:

```js
kamal app exec -i ‘bin/rails console’
```

로그 보기:

<div class="content-ad"></div>

```js
kamal 앱 로그
```

# 결론

축하합니다! 카말(Kamal)을 사용하여 디지턈오션(DigitalOcean) VPS에서 PostgreSQL과 함께 루비 온 레일즈(Ruby on Rails) 애플리케이션을 성공적으로 배포했습니다. 이제 구성된 도메인 이름으로 애플리케이션에 액세스할 수 있습니다. 🚀

이 포스트가 마음에 들었다면 좋아요를 눌러 주시고 미디엄(Medium)과 트위터(Twitter)에서도 저를 팔로우해주세요(https://twitter.com/royalty568).

<div class="content-ad"></div>

시간을 절약하고 앱을 즉시 구축하고자 한다면 Rails 보일러플레이트를 찾고 계신 것 같네요. https://talha345.gumroad.com/l/rails7-bootstrap5-devise-fa-psql-boldo-boilerplate 를 확인해보세요!