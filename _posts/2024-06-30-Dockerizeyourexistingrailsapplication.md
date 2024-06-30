---
title: "기존 Rails 애플리케이션을 Dockerize 하는 방법"
description: ""
coverImage: "/assets/img/2024-06-30-Dockerizeyourexistingrailsapplication_0.png"
date: 2024-06-30 19:17
ogImage: 
  url: /assets/img/2024-06-30-Dockerizeyourexistingrailsapplication_0.png
tag: Tech
originalTitle: "Dockerize your existing rails application."
link: "https://medium.com/@gauravguptaa121/dockerize-your-existing-rails-application-188659d1c1a6"
---


요즘에는 Docker가 응용 프로그램의 플랫폼 의존성 문제를 해결해 주었습니다. Docker는 응용 프로그램을 컨테이너화하여 다양한 플랫폼에서 실행할 수 있도록 도와줍니다.

![이미지](/assets/img/2024-06-30-Dockerizeyourexistingrailsapplication_0.png)

다양한 환경에서 레일즈 응용 프로그램을 실행할 때는 다른 환경 변수와 설정 파일이 필요할 수 있습니다. 이를 위해 다음과 같은 다양한 솔루션을 사용할 수 있습니다:

- 환경별 암호화되지 않은 설정 파일: 우리는 테스트, 스테이징 또는 프로덕션과 같은 다른 환경에서 다른 값들을 제공하기 위해 각각 다른 환경별 파일을 사용할 수 있습니다.
참조: https://github.com/rubyconfig/config

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

2. 환경별 암호화된 자격 증명 파일: Rails 5.2에서는 저장소 자체의 암호화된 형식으로 이러한 설정을 유지하는 데 도움이되는 암호화된 자격 증명의 개념이 추가되었습니다. 이러한 자격 증명은 마스터 키를 사용하여 서버에서 해독 할 수 있습니다.

3. 심볼릭 링크: 가장 일반적인 접근 방식은 코드 기반이 아닌 서버 또는 로컬 머신에서 별도의 구성 파일을 직접 사용할 수 있도록하는 심볼릭 링크를 사용하는 것입니다.

어플리케이션을 도커화 하는 것에는 많은 장점이 있습니다. 장점에 대해서 이야기하는 대신에 이제 도커화 과정으로 진행해 봅시다.

새로운 도커화된 레일즈 어플리케이션을 만드는 것은 다양한 환경과 설정 및 변수와 함께 실행중인 기존 어플리케이션을 이주시키는 것보다 훨씬 쉽습니다.

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

이 블로그에서는 기존 레일즈 애플리케이션의 구성 파일을 도커화하는 방법에 중점을 둘 것입니다. 도커화의 어려운 부분은 기존 애플리케이션의 구성 파일을 관리하는 것입니다.

아래는 애플리케이션을 도커화하는 일반적인 단계입니다:

1. Docker 파일 추가하기:

경로: rails_app/Dockerfile

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

```korean
FROM ruby:3.2.0
RUN apt-get update -qq && apt-get install -y cron && apt-get clean autoclean && apt-get autoremove
ARG rails_env=development
ARG bundle_ruby_gem_cred
# Send log to docker logs
ENV RAILS_LOG_TO_STDOUT="1"
# Set Root directory
ENV RAILS_ROOT /opt/apps/rails_app
ENV RAILS_ENV=$rails_env
RUN mkdir -p $RAILS_ROOT
WORKDIR $RAILS_ROOT
COPY Gemfile $RAILS_ROOT/Gemfile
COPY Gemfile.lock $RAILS_ROOT/Gemfile.lock
RUN bundle install
ADD . $RAILS_ROOT
EXPOSE 3000
# Set the entrypoint
COPY web-entrypoint.sh /usr/bin/
RUN chmod +x /usr/bin/web-entrypoint.sh
ENTRYPOINT ["web-entrypoint.sh"]
```

위 파일은 레일즈 애플리케이션을 위한 기본 도커 파일입니다. 필요한 종속성을 설치하고 환경 변수를 설정할 것입니다.
web-entrypoint.sh 엔트리포인트 스크립트는 컨테이너가 시작될 때 실행되며 일반적으로 레일즈 서버를 시작하거나 기타 초기화 작업을 수행합니다.

경로: rails_app/web-entrypoint.sh


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
#!/bin/bash
# 데이터베이스 마이그레이션
bundle exec rake db:migrate
# 웹 서버 실행
rm -f tmp/pids/server.pid
bundle exec rails server -b 0.0.0.0 -p 3000
```

참고: 필요한 경우 cron, sidekiq 및 기타 서비스를 여기에서 처리할 수 있습니다.

애플리케이션이 도커화되었지만, 다음 도전 과제는 database.yml, redis.yml 등의 설정 파일을 처리하는 것입니다.

다음과 같은 방법으로 구성 파일을 처리할 수 있습니다:

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

## 1. 환경별 자격 증명 파일 사용하기:

이것은 가장 권장되고 안전한 방법입니다. 도커 이미지는 불변하기 때문에 이미지 내의 모든 내용을 유지해야 합니다.

환경별 자격 증명 파일에 모든 설정 변수와 자격 증명을 커밋할 수 있으며 이미지 빌드 중에 해당 마스터 키를 제공할 수 있습니다.

다음은 구성 파일 조각 중 하나입니다:

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

경로: rails_app/config/database.yml

```yaml
default: &default
  adapter: postgresql
  encoding: unicode
  pool: 100
  port: 5432
  host: <%= Rails.application.credentials.database.host %>
  database: <%= Rails.application.credentials.database.name %>
  username: <%= Rails.application.credentials.database.username %>
  password: <%= Rails.application.credentials.database.password %>

development:
  <<: *default

test:
  <<: *default

staging:
  <<: *default

production:
  <<: *defaultadapter: postgresql
```

모든 환경에 대해 위와 같은 테이블을 Markdown 형식으로 바꿀 수 있어요.

위 환경에 대한 서로 다른 자격 증명 파일을 생성하는 방법은 아래 명령어를 사용하세요:

EDITOR=vim rails credentials:edit -e development|test|production|staging

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

이 명령어는 다음 디렉터리에 파일을 생성합니다:

경로: rails_app/config/credentials

```js
test.yml.enc
test.key

development.yml.enc
development.key

prodution.yml.enc
prodution.key

staging.yml.enc
staging.key
```

우리는 yml.enc 파일을 레포지토리에 커밋할 수 있고, 빌드 프로세스 중에 key는 github 자격증명을 사용하여 제공할 수 있습니다.

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
echo -n ${ secrets.RAILS_APP_PRODUCTION_KEY } > ‘config/credentials/production.key’
```

RAILS_APP_PRODUCTION_KEY은 github 시크릿에서 유지되는 키의 실제 값을 포함합니다.

## 장점:

a. 어떠한 원격 서버에도 어플리케이션을 머신 의존성 없이 배포할 수 있습니다.

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

b. 키를 제외한 모든 것이 저장소 자체에 있습니다.

## 단점:

a. 여러 설정 파일이 있는 경우 설정 변수를 자격 증명 파일 참조로 대체하려면 redis.yml에 언급된 대로 변경을 애플리케이션 전체에 적용해야 합니다.

## 2. 심볼릭 링크를 사용:

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

![이미지](/assets/img/2024-06-30-Dockerizeyourexistingrailsapplication_1.png)

도커 컨테이너의 설정 파일에 대한 심볼릭 링크를 호스트 머신에 생성하여, 호스트 머신에서 설정 파일을 변경하면 도커 컨테이너에 반영될 수 있습니다.

database.yml 파일을 예시로 들어보겠습니다:

2.1. 호스트 머신의 특정 디렉토리에 database.yml 파일의 실제 내용을 넣어주세요:

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

경로: /opt/rails_app_configs/database.yml

```js
  qa:
    adapter: postgresql
    encoding: unicode
    pool: 100
    port: 5432
    host: abc
    database: abc
    username: abc
    password: abc
```

2.2. 이제 database.yml 파일을 아래 컨테이너 경로에 심볼릭 링크로 커밋하세요:

파일: database.yml

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
/opt/configs/database.yml
```

2.3. 배포 시 호스트 머신에서 실제 파일을 database.yml에 지정된 컨테이너의 특정 경로로 복사하세요.

```js
docker container run -d — rm — name hello-rails-app -e RAILS_ENV=qa -v /opt/rails_app_configs:/opt/configs -p 3000:3000 rails_app:latest
```

이렇게 하면 호스트 머신에서 파일이 컨테이너로 복사되어 응용 프로그램이 구성 파일에 액세스할 수 있게 됩니다.

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

## 장점:

a. 호스트 머신의 구성 파일을 변경하면 컨테이너에 반영됩니다.

b. 구성 파일이 변경되어도 새 이미지를 빌드할 필요가 없습니다.

c. 설정 변수를 자격 증명 파일 참조로 대체하려면 애플리케이션 코드를 변경할 필요가 없습니다.

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

## Cons:

a. 호스트 머신에서 설정 파일을 유지하고 있어야 합니다.

b. 설정 파일은 호스트 머신에서 읽을 수 있습니다.

c. 새로운 머신에서 컨테이너를 실행할 때마다 파일을 호스트 머신에 복사해야 합니다. 각 머신마다 파일을 복사하는 추가 노력을 피하기 위해 nfs 서버를 사용하여 호스트 머신에 설정 파일을 마운트하고 모든 머신에서 사용할 수 있습니다.

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

위 내용이 기존 레일즈 애플리케이션을 도커화하는 데 도움이 되길 바랍니다. 궁금한 사항이나 제안 사항이 있으시면 아래에 자유롭게 댓글을 남겨주세요.

저는 동일한 주제로 GitHub 액션 및 배포용 워크플로우를 이어서 진행할 예정입니다.

읽어주셔서 감사합니다.