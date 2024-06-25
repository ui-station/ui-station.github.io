---
title: "Rails 7에서 Sidekiq Gem을 사용한 백그라운드 작업 처리 Part-I"
description: ""
coverImage: "/assets/img/2024-06-23-ProcessingBackgroundJobsUsingSidekiqGeminRails7Part-I_0.png"
date: 2024-06-23 20:52
ogImage:
  url: /assets/img/2024-06-23-ProcessingBackgroundJobsUsingSidekiqGeminRails7Part-I_0.png
tag: Tech
originalTitle: "Processing Background Jobs Using Sidekiq Gem in Rails 7 (Part-I)"
link: "https://medium.com/@maffan/processing-background-jobs-using-sidekiq-gem-in-rails-7-part-i-5c71574ac479"
---

<img src="/assets/img/2024-06-23-ProcessingBackgroundJobsUsingSidekiqGeminRails7Part-I_0.png" />

레일즈 개발자로서, 백엔드 개발자들이 거의 모든 시간에 수행하는 일로 비동기적으로 백그라운드에서 실행되는 코드를 작성하는 것은 매우 흔한 작업입니다.

예를 들어, 새로운 고객에게 환영 이메일을 보내거나 수천 건의 레코드를 통해 복잡한 계산을 위해 백그라운드 처리를 사용합니다.

다행히도, 모든 무거운 작업을 처리하고 간단한 인터페이스를 제공하여 우리의 시간을 아낄 수 있는 도구들이 있습니다. 그 중 하나인 Sidekiq를 살펴보겠습니다!

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

# Sidekiq이란 무엇인가요?

Sidekiq은 루비 온 레일즈(Ruby on Rails) 애플리케이션을 위한 인기 있는 백그라운드 작업 처리 라이브러리입니다. Sidekiq은 주요 요청-응답 주기(main request-response cycle)에서 시간이 많이 소요되는 작업들을 간편하고 효율적으로 처리할 수 있는 방법을 제공합니다.

Sidekiq이 의존하는 세 가지 주요 구성 요소가 있습니다:

- Redis

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

Sidekiq은 작업자에 의해 처리되는 작업을 저장하는 데 메모리 데이터 저장소인 Redis를 사용합니다.

클라이언트

백그라운드에서 처리되는 작업을 생성하는 Ruby 또는 Rails 프로세스입니다.

서버

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

이 작업은 Redis 대기열에서 작업을 가져와 실행하는 데 책임이 있어요.

너무 많은 텍스트인가요? 지금 무언가를 만들어보죠!

## 전제 조건

언급했듯이 Sidekiq는 백그라운드 작업을 대기열에 넣기 위해 Redis에 의존합니다. 따라서 로컬에 Redis 서버가 실행 중인지 확인해주세요.

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

# 단계 1

새 프로젝트를 만들거나 이미 설정된 프로젝트가 있다면이 단계를 건너 뛰세요.

```js
$ rails new sidekiq_tutorial
```

# 단계 2

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

이 프로젝트 안으로 들어오신 것을 환영합니다:

```js
$ bundle add sidekiq
```

# 단계 3

이제, 새 작업을 생성하고 Sidekiq 워커로 푸시할 준비가 되었습니다. 새 작업을 생성해봅시다.

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
$ rails generate sidekiq:job my_first_job
```

여기에 원하는 내용을 입력하고 app/sidekiq/my_first_job.rb과 테스트 파일이 생성됩니다. 작업 파일의 내용을 다음과 같이 편집해 봅시다:

```js
class MyFirstJob
  include Sidekiq::Job

  def perform(name,age)
    puts "나는 #{name}이고, 나이가 #{age}살인 첫 작업을 실행 중입니다."
    #여기에는 다른 유효한 루비/레일스 코드를 넣을 수 있어요!
  end
end
```

여기서는 콘솔에 텍스트를 로깅하긴 하지만, 여기에서는 거의 모든 유효한 코드를 실행할 수 있습니다.

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

이 작업을 실행하기 전에 Redis가 올바르게 구성되어 있는지 확인해야 합니다.

## Redis 구성

이 작업을 실행하기 전에 Redis가 올바르게 구성되어 있는지 확인해야 합니다.

두 가지 방법이 있습니다. 하나는 REDIS_URL 환경 변수(`dotenv-rails gem`을 사용하여)를 사용하는 방법이며, 더 복잡한 설정이 필요한 경우에는 config/initializers에 있는 sidekiq.rb 파일을 만들 수 있습니다.

### .env 파일 사용하기

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
REDIS_URL=redis://redis.example.com:7372/0
```

## config/initializers/sidekiq.rb 파일을 사용하기

```js
Sidekiq.configure_server do |config|
  config.redis = { url: 'redis://redis.example.com:7372/0' }
end

Sidekiq.configure_client do |config|
  config.redis = { url: 'redis://redis.example.com:7372/0' }
end
```

참고: Redis와 연결하려면 위의 설정 중 하나를 사용해야 합니다.

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

만약 당신이 Docker에 능숙하다면, 간단한 Redis 컨테이너를 사용하여 Redis를 실행하고 Rails 앱과 연결할 수 있습니다.

# 작업을 대기열에 넣기

Rails 콘솔에서 작업을 실행해 봅시다:

```js
$ rails c

3.0.0 :001 > MyFirstJob.perform_async "Affan", 31
=> "5a4c435ddd2295a6104c8fcb"
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

작업이 성공적으로 생성되었습니다.

# 작업 실행하기

이제 작업이 대기열에 들어 있으므로 Sidekiq 워커를 실행하여 작업을 실행해야 합니다. 다른 터미널 창을 열고 다음을 실행하세요:

```js
$ bundle exec sidekiq
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

모든 것이 잘 작동되면 다음과 같은 내용을 볼 수 있을 거예요.

![image](/assets/img/2024-06-23-ProcessingBackgroundJobsUsingSidekiqGeminRails7Part-I_1.png)

작업을 마치면 ctrl+c를 사용하여 워커를 종료할 수 있어요.

# 결론

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

이 튜토리얼의 첫 번째 부분에서는 Sidekiq에 대해 어떻게 백그라운드 작업을 처리하는지, 새로운 Rails 프로젝트에서 간단한 작업을 만드는 방법에 대해 설명했습니다. 또한 Sidekiq를 Rails 앱에 연결하는 구성 옵션에 대해 알아보았습니다. 다음 이야기의 2부에서는 이 주제와 관련된 고급 내용에 대해 논의할 예정입니다 :)
