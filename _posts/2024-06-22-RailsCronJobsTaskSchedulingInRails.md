---
title: "Rails 크론 잡 - Rails에서 작업 스케줄링 하는 방법"
description: ""
coverImage: "/assets/img/2024-06-22-RailsCronJobsTaskSchedulingInRails_0.png"
date: 2024-06-22 22:26
ogImage: 
  url: /assets/img/2024-06-22-RailsCronJobsTaskSchedulingInRails_0.png
tag: Tech
originalTitle: "Rails Cron Jobs — Task Scheduling In Rails"
link: "https://medium.com/@hasnatraza.dev/rails-cron-jobs-task-scheduling-in-rails-f7662106feaa"
---


![image](/assets/img/2024-06-22-RailsCronJobsTaskSchedulingInRails_0.png)

언젠가 자동으로 작업을 예약해야 하는 상황에 처했던 적이 있나요? 매일 이메일을 보내거나 외부 API와 정기적으로 데이터를 동기화하는 것과 같은 작업들이 될 수 있습니다. 여기서 Cron 작업이 유용하게 사용됩니다. 이를 통해 이러한 작업들을 쉽게 자동화할 수 있습니다. 이 블로그에서는 예약된 작업을 실행하는 데 사용되는 Unix/Linux 시스템에 통합된 기본 소프트웨어인 Cron을 소개할 것입니다. 또한 Ruby Whenever Gem을 사용하여 특히 Rails 애플리케이션에서 Cron 작업을 쉽게 배포하는 빠른 가이드도 제공할 예정입니다.

# Cron 작업이란?

Whenever Gem을 사용한 Rails에서 Cron 작업을 사용하는 방법에 대해 설명하기 전에, cron 작업이 무엇인지 간단히 설명해 드리겠습니다. (이 주제에 대해 자세한 기사를 곧 쓸 것입니다)

<div class="content-ad"></div>

간단히 말해서, Cron은 특정 시간에 반복적으로 작업을 예약하고 실행할 수 있게 해주는 명령줄 도구입니다. 이러한 작업들을 'cron jobs'라고 합니다. Cron은 Linux/Unix 시스템에 내장되어 있기 때문에 추가적인 종속성을 설치할 필요가 없습니다. 일반적으로 cron jobs는 'crontab'이라는 파일에 작성됩니다. 'crontab'은 Cron Table의 약자입니다. crontab 작업/cron job의 구문은 다섯 개의 숫자로 구성되어 있으며 각각은 작업을 실행해야 하는 시간을 나타내며 그 뒤에 실행할 스크립트가 따릅니다. 구문은 다음과 같아야 합니다:

```js
# ┌───────────── 분 (0 - 59)
# │ ┌───────────── 시간 (0 - 23)
# │ │ ┌───────────── 일 (1 - 31)
# │ │ │ ┌───────────── 월 (1 - 12)
# │ │ │ │ ┌───────────── 요일 (0 - 6) (일요일부터 토요일까지;
# │ │ │ │ │ 7는 일요일일 수도 있음)
# │ │ │ │ │
# │ │ │ │ │
# * * * * * 실행할 명령어
```

아래 다이어그램은 구문을 잘 설명하고 있습니다:

<img src="/assets/img/2024-06-22-RailsCronJobsTaskSchedulingInRails_1.png" />

<div class="content-ad"></div>

레일즈 작업을 예약하는 데는 crontab을 사용할 수 있지만, 낡고 복잡하게 느껴질 수 있습니다. 이 프로세스는 다음 명령어를 사용하는 것을 포함합니다:

```js
0 * * * * 'cd path/to/project && bundle exec some_task'
```

# Whenever 젬

이제 레일즈에서 cron 작업을 다루는 더 나은 방법에 대해 설명해 드리겠습니다. 전통적이고 다소 난잡한 crontab을 다루는 대신, 일정을 잡는 것이 훨씬 간편하고 우아한 루비 젬이 있습니다. 이 용도로 가장 많이 사용되는 젬은 Whenever입니다.

<div class="content-ad"></div>

'Whenever'은 크론 작업을 설정하는 과정을 간소화하는 젬입니다. 크론탭을 수동으로 편집하는 대신 Whenever을 사용하여 루비 코드로 크론 작업을 작성할 수 있습니다. 이 젬은 크론탭을 자동으로 업데이트해줍니다.

# Whenever 젬 설정하기

이제 어플리케이션에 해당 젬을 설정하고 사용해봅시다. 먼저 젬을 설치해야 합니다. 아래와 같이 Gemfile에 젬을 포함시키고 bundle install을 실행하세요.

```js
gem 'whenever', require: false
```

<div class="content-ad"></div>

이제 젬을 초기화하기 위해 아래 명령을 실행해보세요.

```js
bundle exec .wheneverize
```

`.wheneverize` 명령을 실행하면 config 디렉토리, 구체적으로는 config/schedule.rb에 schedule.rb라는 이름의 파일이 생성됩니다. 이 파일은 작업 실행 시간을 정의하고 실행할 Rake 작업을 지정하는 역할을 합니다.

이제 젬을 설정한 후 작업을 실제로 예약해 보겠습니다. 이를 위해서 Rake 작업을 만들어 시작하면 됩니다.

<div class="content-ad"></div>

```js
레일즈 g task batch send_messages
```

레일즈의 rake 태스크를 생성했다면, 해당 태스크 파일은 lib/tasks/sample.rb에 있을 거에요. 이제 이곳에 이메일을 보내는 기능을 추가해 보겠습니다.

```js
# lib/tasks/send_messages.rake

namespace :messages do
  desc "사용자에게 메시지 보내기"
  task send: :environment do
    User.all.each do |user|
      MessageMailer.send_message(user.email).deliver_now
      puts "#{user.email}님에게 메시지를 보냈습니다."
    end
  end
end
```

Rake 태스크에 로직을 추가했다면, schedular.rb 파일에 등록해 보세요. 여기서는 예시로 1분 간격으로 설정하겠습니다.


<div class="content-ad"></div>

```js
매 분마다
    rake 'messages:send'
end
```

그런 다음 crontab을 업데이트하고 해당 작업을 추가할 것입니다. 아래 명령을 실행하여 작업을 추가하실 수 있습니다.

```js
whenever --update-crontab
```

Cron Job을 성공적으로 설정했습니다. 특정 시간에 실행되도록 예약되었습니다. 또한 whenever --clear-crontab 명령을 사용하여 crontab을 지울 수 있고, crontab을 보려면 crontab -l을 사용할 수 있습니다.

<div class="content-ad"></div>

# Sidekiq-Cron Gem

Sidekiq-cron은 작업 처리 라이브러리인 Sidekiq의 애드온입니다. Sidekiq-cron을 사용하면 특정 시간이나 간격에 작업을 예약할 수 있습니다. Sidekiq-cron은 Sidekiq에서 제공하는 공식 젬이 아님을 유의해야 합니다.

Sidekiq에서 작업을 예약하는 것은 일반적으로 엔터프라이즈 수준의 기능으로 간주되어 유료 라이선스가 필요할 수 있습니다. 그러나 엔터프라이즈 버전에 돈을 쓰고 싶지 않다면 Sidekiq-cron은 여러분에게 적합한 대안입니다.

간단히 말하면, Sidekiq-cron은 공식 엔터프라이즈 버전을 지불하지 않고도 Sidekiq 내에서 작업을 예약할 수 있게 해주는 도구입니다. 비슷한 예약 기능을 제공하여 작업이 언제와 얼마나 자주 실행되어야 하는지 지정할 수 있습니다.

<div class="content-ad"></div>

# Sidekiq-Cron Gem 설정하기

이제 우리의 애플리케이션에 Sidekiq-Cron 젬을 설정해 봅시다. 먼저 젬을 설치해야 합니다. Gemfile에 아래와 같이 젬을 추가하고 bundle install을 실행해주세요.

```js
gem 'sidekiq-cron'
```

그런 다음 config/ 디렉토리에 schedule.yml 파일을 생성하세요. Whenever 젬의 경우처럼 파일을 초기화하는 기능은 제공되지 않지만, 다행히 Sidekiq-Cron은 파일을 초기화하는 기능을 제공하지 않습니다. 또한 config/initializers 디렉토리에 sidekiq.rb라는 이름의 파일을 생성하여 아래 내용을 추가해 주세요.

<div class="content-ad"></div>

```js
schedule_file = "config/schedule.yml"
if File.exist?(schedule_file) && Sidekiq.server?
   Sidekiq::Cron::Job.load_from_hash YAML.load_file(schedule_file)
end
```

우리의 초기 설정이 완료되었습니다. 이제 실제 작업을 생성할 차례입니다. 터미널로 이동하여 다음 명령을 입력하세요.

```js
rails generate job send_bulk_emails
```

그리고 이 코드에 일부 기능을 추가하세요.

<div class="content-ad"></div>

참고: 레일즈 백그라운드 작업/워커를 설정, 생성 및 사용하는 방법에 대한 자세한 튜토리얼이 필요하시다면 이 기사를 참조해주세요.

```js
class SendBulkEmailJob < ApplicationJob
  queue_as :default

def perform(emails)
    begin
      BulkEmailService.send_emails(emails)
    rescue StandardError => e
      Rails.logger.error "Error sending bulk emails: #{e.message}"
      raise e
    end
  end
end
```

작업을 만든 후, 터미널에 sidekiq을 입력하여 실행하고 다음과 같이 스케줄러 파일에 작업을 추가하세요:

```js
email_job:
   cron: "*/5 * * * *"
   class: "SendBulkEmailJob"
```

<div class="content-ad"></div>

워커를 실행하려면 터미널에서 새 탭을 열어주세요. 앱이 활성화된 상태에서도 워커가 계속 실행되어야 하는 점을 주의해 주세요. 레일즈 서버를 시작하거나 기타 작업을 실행해야 하는 경우 다른 탭으로 전환해야 합니다.

모두입니다! 첫 번째 Sidekiq-cron 작업을 생성한 것을 축하드립니다!

# 결론

요약하면, 크론 작업은 Unix/Linux 시스템에서 반복적인 작업을 자동화하는 데 필수적입니다. Whenever 젬은 루비 코드로 작성하고 크론 작업을 예약하는 레일즈 애플리케이션에서 간단하게 사용할 수 있도록 하며 crontab을 자동으로 업데이트합니다. Sidekiq-Cron은 Sidekiq에서 작업을 예약하는 데 유용한 젬입니다. 두 젬은 크론 작업을 관리하고 실행하는 과정을 간소화하여 애플리케이션의 자동화와 효율성을 향상시킵니다.

<div class="content-ad"></div>

앞으로의 기사에서는 Rails가 어떻게 작동하는지에 대해 더 알아볼 것입니다. 그러니 계속해서 주의 깊게 지켜보고 배우세요!