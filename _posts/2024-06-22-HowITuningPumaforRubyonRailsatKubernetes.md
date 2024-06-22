---
title: "Kubernetes 환경에서 Ruby on Rails의 Puma 튜닝 방법"
description: ""
coverImage: "/assets/img/2024-06-22-HowITuningPumaforRubyonRailsatKubernetes_0.png"
date: 2024-06-22 22:29
ogImage: 
  url: /assets/img/2024-06-22-HowITuningPumaforRubyonRailsatKubernetes_0.png
tag: Tech
originalTitle: "How I Tuning Puma for Ruby on Rails at Kubernetes"
link: "https://medium.com/williamdesk/how-i-tuning-puma-for-ruby-on-rails-c09d79f6ec34"
---


## 쿠버네티스에 맞게 Puma 튜닝하기

Puma는 Ruby on Rails용 인기 있는 웹 서버 패키지이며, Puma 구성을 잘 튜닝하면 서비스 효율성이 더욱 향상됩니다.

Puma 젬: [https://rubygems.org/gems/puma](https://rubygems.org/gems/puma)

![이미지](/assets/img/2024-06-22-HowITuningPumaforRubyonRailsatKubernetes_0.png)

<div class="content-ad"></div>

# 왜 리팩토링 해야 하나요?

가장 중요한 이유는 도커에서 쿠버네티스 아키텍처로 마이그레이션할 때, Puma 구성을 새 아키텍처에 맞게 업데이트해야 할 수도 있다는 것입니다. 따라서, 저는 많은 기사와 소스 저장소를 재조사하여 이를 다시 작성하는 데 도움을 받았어요.

# Puma 구성 튜닝을 하기 전, 확인해야 할 사항

- 처음에는 가장 중요한 서비스를 사용하지 마세요. 그 대신 부수적인 서비스를 사용하세요. 
예를 들어, 먼저 테스트할 때 회원 OAuth 서비스를 사용하지 마세요. 이 예에서, 저는 테스트용으로 메일 센터를 사용했어요. (대부분의 요청이 내부 호출이며, 요청이 실패하면 다시 시도합니다.)
- 서비스의 온라인 상태를 확인하기 위해 모니터링 서비스 중 하나가 반드시 필요해요. 저는 서비스와 쿠버네티스 상태를 확인하기 위해 Datadog를 사용하고 있어요.
- 각 구성 설정과 단계별 튜닝을 위해 왜 그리고 어떻게 하는지 알아야 해요.

<div class="content-ad"></div>

# Puma 구성 리팩터링

## 우리의 초기 Puma 구성

첫 번째 구성은 다음과 같습니다.

```js
#!/usr/bin/env puma

environment ENV.fetch("RAILS_ENV") { "development" }

if ENV['RAILS_ENV'].nil? || ENV['RAILS_ENV'] == 'development'
  threads_count = ENV.fetch("RAILS_MAX_THREADS") { 5 }
  threads threads_count, threads_count

  port ENV.fetch("PORT") { 3000 }

  plugin :tmp_restart
else
  directory './'
  rackup "./config.ru"

  pidfile "./tmp/pids/puma.pid"
  state_path "./tmp/pids/puma.state"

  threads 0,16

  port ENV.fetch("PORT") { 3000 }

  workers 2

  prune_bundler

  on_restart do
    puts 'Refreshing Gemfile'
    ENV["BUNDLE_GEMFILE"] = "./Gemfile"
  end
end
```

<div class="content-ad"></div>

## 리팩터링 키포인트 1: 스레드 용량 감소

일부 참조 링크를 조사했는데, 먼저 그것들을 확인해보세요.

- [문서] 쿠버네티스에 적합한 Puma 튜닝 방법
- 최대 효율을 위한 Puma, Unicorn 및 Passenger 구성
- Pod 당 워커 및 기타 구성 문제

마지막 문제 토론에 따르면, 너무 많은 스레드를 사용하는 것은 더 많은 요청을 수용하기에 적합하지 않으며, 리소스가 Global VM 락에 직면할 수 있습니다.

<div class="content-ad"></div>

우리 설정에서는 각 워커에 너무 많은 스레드를 설정했어요. 그래서 이것이 제 첫 번째 반응 대상이 됐어요.

Puma 문서의 기사에 따르면, 쿠버네티스 팟 * Puma 워커 * 스레드가 최종 총 용량이라는 걸 알 수 있어요.

이전에 우리의 용량은 64였어요. 2 (팟) * 2 (워커) * 16 (최대 스레드)인거죠. 큰 감소가 심각한 문제를 일으킬 것을 두려워해서, 처음에는 스레드를 8로 순차적으로 감소시켰고, 마지막에 5로 줄였어요 (총 용량은 20이 될 거예요).

```js
# puma.rb
# Puma에게 1에서 5 범위 내에서 스레드를 자동 조정하도록 설정합니다.
# 각 스레드가 더 많은 CPU / 메모리 리소스를 갖으므로 16에서 5로 줄였어요.
threads 0, 5
```

<div class="content-ad"></div>

## Refactor Key 2: CPU 코어의 수를 신뢰성 있게 감지할 수 있어야 합니다.

초기 설정에서는 이전 도커 인프라에서 각 서비스가 사용할 수 있는 2개의 vCPU를 가지고 있기 때문에, 종업원을 두 명으로 설정했습니다.

첫 번째 시도에서, 새로운 Rails 버전의 Puma 구성 설정을 참조하여 CPU를 감지하였습니다.

```js
# Specifies that the worker count should equal the number of processors in production.
if ENV["RAILS_ENV"] == "production"
  worker_count = Integer(ENV.fetch("WEB_CONCURRENCY") { Concurrent.physical_processor_count })
  workers worker_count if worker_count > 1
end
```

<div class="content-ad"></div>

루비 메소드를 사용하여 CPU 프로세서 카운트를 감지하는 것은 좋은 아이디어에요. "물리 프로세서 수" 또는 "OS에서 보이는 프로세서 및 프로세스 스케줄링에 사용되는 프로세서"를 기준으로 결정할 수 있어요.

```js
# Module: Concurrent

require "concurrent-ruby"

# 현재 시스템의 물리 프로세서 코어 수.
# 성능상의 이유로 처음 호출될 때 계산된 값은 메모이즈될 거에요.
Concurrent.physical_processor_count

# OS에서 보이는 프로세서 및 프로세스 스케줄링에 사용되는 프로세서 수.
# 성능상의 이유로 처음 호출될 때 계산된 값은 메모이즈될 거에요.
Concurrent.processor_count
```

만약 Kubernetes 베이스 클러스터 노드에 CPU가 4개 있고, 서비스를 1000(µs) CPU 리소스로 제한한다면, Coucurrnet 메소드를 사용하면 항상 4를 반환할 거에요 (호스트 수준 리소스가 보이기 때문에 팟 수준 리소스가 아니에요).

그래서 Puma가 Kubernetes 설정을 직접 읽을 수 있도록 해야 해요. 다음과 같이 시도해 봤어요:

<div class="content-ad"></div>


# K8S CPU 제한을 설정하는 방법을 확인하세요.
## cpu.cfs_quota_us는 그룹이 해당 창기간 동안 사용할 수 있는 최대 CPU 시간(마이크로초 단위)을 지정합니다.
## cpu.cfs_period_us는 CPU 액세스를 위한 시간 창문의 길이(마이크로초 단위)를 나타냅니다.

## quota가 -1이면 CPU 자원 사용에 대한 제한이 없는 것을 의미합니다.

"concurrent-ruby"을 요구합니다.
quota_file = '/sys/fs/cgroup/cpu/cpu.cfs_quota_us'
period_file = '/sys/fs/cgroup/cpu/cpu.cfs_period_us'
quota = File.read(quota_file).strip.to_i
period = File.read(period_file).strip.to_i

if quota != -1
  processors_count = (quota.to_f / period.to_f).ceil
else
  processors_count = Integer(ENV.fetch("WEB_CONCURRENCY") { Concurrent.physical_processor_count })
end


CPU 자원이 제한되지 않는 경우에는 서비스가 노드의 CPU 최대 범위를 사용할 수 있으므로 물리적 프로세서 개수를 기반으로 설계되었습니다. 제한이 있는 경우 계산하세요!

## 리팩터링 핵심 3: 항상 서비스에 시간 제한을 설정하세요.

Sidekiq 작성자인 마이크의 글을 예전에 읽었는데도 기억이 싱싱합니다. 모든 네트워크 요청에는 타임아웃을 설정해야 한다는 교훈을 주었습니다.

<div class="content-ad"></div>

제가 참고한 GitHub은 puma 타임아웃 설정을 위해 the-ultimate-guide-to-ruby-timeouts입니다. 매우 유용합니다.

![이미지](/assets/img/2024-06-22-HowITuningPumaforRubyonRailsatKubernetes_1.png)

## 리팩터링 핵심 4: 에러 처리 설정하기.

Puma에는 기본 에러 처리 구성이 내장되어 있습니다. 또한 오류를 캡처하는 Sentry 시스템도 있으므로 에러가 발생할 때 Sentry 캡처를 추가하는 것이 좋아 보입니다!

<div class="content-ad"></div>

```js
# 참조: https://github.com/puma/puma?tab=readme-ov-file#error-handling

lowlevel_error_handler do |e|
  Sentry.capture_exception(e)
  [500, {}, ["오류가 발생했습니다"]]
end
```

## 최종 Puma 설정

```js
# 이 구성 파일은 Puma에 의해 평가될 것입니다. 여기에서 호출되는 최상위 메서드들은
# Puma의 구성 DSL의 일부입니다. DSL에서 제공하는 메서드에 대한 자세한 정보는 https://puma.io/puma/Puma/DSL.html에서 확인하세요.

# Puma는 설정 가능한 일정 수의 프로세스(작업자)를 시작하고 각 프로세스는 내부 스레드 풀에서 스레드로 각 요청을 처리합니다.
#
# 각 작업자당 이상적인 스레드 수는 응용 프로그램이 IO 작업을 기다리는 시간과
# 처리량을 지연 시간보다 우선시할지에 따라 다릅니다.
#
# 일반적으로 스레드 수를 늘리면 특정 프로세스가 처리할 수 있는 트래픽 양(처리량)이 늘어납니다.
# 그러나 CRuby의 Global VM Lock (GVL)로 인해 반응 시간(지연 시간)이 악화될 수 있고
# 수응용 프로그램의 경우 감소하기 때문에 이 형벌이 선뜻 좋은 결정이라고 할 수 없습니다.
#
# 평균적인 Rails 응용 프로그램에 대한 처리량과 지연 시간 사이의 괜찮은 절충으로 간주되는 3개의 스레드로 설정됩니다.
#
# 연결 풀이나 다른 리소스 풀을 사용하는 모든 라이브러리는
# 스레드 수와 동일하거나 그 이상의 연결을 제공하도록 구성되어야 합니다.
# 이에는 `database.yml`의 Active Record의 `pool` 매개변수가 포함됩니다.
default_threads_count = ENV.fetch("RAILS_MAX_THREADS") { 3 }
threads default_threads_count, default_threads_count

# 프로덕션 및 준비 단계에서 작업자 수를 프로세서 수와 동일하게 설정
if rails_env == "production" || rails_env == "preparing"
  # 프로세스 당 1개 이상의 스레드를 실행중인 경우 작업자 수
  # 기본적으로 프로세서(컴퓨터 코어)의 수와 동일하게 설정해야합니다.
  #
  # 이것은 신뢰할 수 없te, CPU 코어의 수를 신뢰할 수없습니다.
  # `WEB_CONCURRENCY` 환경 변수를 프로세서 수와 일치하도록 설정하는지 확인하세요.
  require "concurrent-ruby"

  # config 파일로부터 K8S CPU 한정 메모리 확인
  ## cpu.cfs_quota_us는 그 그룹이 그 창에서 사용할 수 있는 최대 CPU 시간(밀리 초 단위)을 지정합니다.
  ## cpu.cfs_period_us는 CPU 액세스 시간 창의 길이를 지정합니다.
  quota_file = '/sys/fs/cgroup/cpu/cpu.cfs_quota_us'
  period_file = '/sys/fs/cgroup/cpu/cpu.cfs_period_us'
  quota = File.read(quota_file).strip.to_i
  period = File.read(period_file).strip.to_i

  if quota != -1
    processors_count = (quota.to_f / period.to_f).ceil
  else
    processors_count = Integer(ENV.fetch("WEB_CONCURRENCY") { Concurrent.physical_processor_count })
  end

  # 작업자 및 스레드 설정
  if processors_count > 1
    workers processors_count
    threads 0, 5

    on_worker_boot do
      ActiveRecord::Base.establish_connection if defined?(ActiveRecord)
    end
  else
    preload_app!
  end

  worker_timeout 15
  worker_shutdown_timeout 8
end

# Puma가 요청을 수신하기 위해 청취하는 `포트`를 지정; 기본값은 3000입니다.
port ENV.fetch("PORT") { 3000 }

# Puma가 실행할 `환경`을 지정합니다.
environment rails_env

# `bin/rails restart` 명령으로 Puma를 다시 시작할 수 있도록 허용
plugin :tmp_restart
pidfile ENV["PIDFILE"] if ENV["PIDFILE"]

if rails_env == "development"
  # 디버거에 의해 일시 중단되었을 때 Puma에 의해 작업자가 종료되지 않도록 매우 넉넉한 `worker_timeout`를 지정합니다.
  worker_timeout 3600
end

# 응용 프로그램의 범위를 벗어난 오류가 발생하면 Puma가 500 및 간단한 텍스트 오류 메시지와 함께 응답
lowlevel_error_handler do |e|
  Sentry.capture_exception(e)
  [500, {}, ["오류가 발생했습니다"]]
end
```

이전에 언급하지 않았던 몇 가지 작은 변경 사항이 있지만 참고하실 수 있습니다.

<div class="content-ad"></div>

- **on_worker_boot** 메소드를 사용해보세요.
작업자가 부팅될 때 항상 ActiveRecord가 데이터베이스에 연결되어 있는지 확인하세요 (필요하다면 Redis를 추가할 수도 있습니다).

- 여러 작업자를 사용하는 경우 **preload_app!**는 기본적으로 켜져 있습니다.
Rails에서는 새로운 Puma 템플릿이 작업자 수가 1인 경우에도 preload_app를 사용합니다.
https://github.com/rails/rails/blob/main/railties/lib/rails/generators/rails/app/templates/config/puma.rb.tt

# 모든 것을 모니터링하세요

만든 변경사항은 항상 모니터링되어야 함을 기억하세요.

새로운 환경 설정을 프로덕션에 배포할 때 문제가 없어야 합니다. (물론, 우리는 준비된 환경이 있고 서비스가 작동하는지 확인하기 위해 몇 가지 스트레스 테스트를 수행하였지만, 요청량은 프로덕션 환경과 비교할 수 없을 정도로 상이합니다.)

<div class="content-ad"></div>

요청 용량을 64에서 20으로 줄인 후에도 서비스는 잘 작동했어요. CPU 및 메모리 사용량이 크게 개선되지는 않았지만, 스레드 용량을 더 적합한 상황으로 줄여 GVL을 피하려고 성공적으로 조정했습니다.

![그림 1](/assets/img/2024-06-22-HowITuningPumaforRubyonRailsatKubernetes_2.png)

![그림 2](/assets/img/2024-06-22-HowITuningPumaforRubyonRailsatKubernetes_3.png)

# 결론

<div class="content-ad"></div>

- 멀티스레드인 경우, Puma 워커 당 1개의 CPU를 할당하세요.
참고: https://github.com/puma/puma/issues/2645#issuecomment-867629826
- 대부분의 Puma는 워커 당 약 512MB ~ 1GB의 메모리를 사용하며, 마스터 프로세스에 대략 1GB를 사용합니다.
참고: https://github.com/puma/puma/issues/2645#issuecomment-867629826
- 대부분의 Puma는 각 쓰레드 당 약 300MB ~ 500MB의 메모리를 사용합니다.
이는 웹 서비스의 유형과 기능에 따라 다를 수 있으며, Ruby 3 및 Rails 6 응용 프로그램을 실험하면서 각 프로세스가 약 200MB ~ 400MB 정도 사용한다는 기사를 참조했습니다.
참고: https://www.speedshop.co/2017/10/12/appserver.html
- 각 Puma 워커를 3 ~ 5개의 쓰레드로 설정하는 것이 일반 목적에 가장 적합합니다.
참고 1: https://github.com/rails/rails/issues/50450
참고 2: https://devcenter.heroku.com/articles/deploying-rails-applications-with-the-puma-web-server#recommended-default-puma-process-and-thread-configuration
- 자원에 따라 구성이 동적으로 조정되도록 하세요.
- 웹 서비스에 대한 시간 초과 및 오류 처리 메커니즘을 항상 설정하세요.
참고 1: https://www.mikeperham.com/2015/05/08/timeout-rubys-most-dangerous-api/
참고 2: https://github.com/ankane/the-ultimate-guide-to-ruby-timeouts
- 생산 환경에 배포하기 전에 중요하지 않은 서비스부터 사용하고, 배포 전에 더 많은 테스트를 수행하세요.
- 생산 환경에 배포할 때 모니터링 서비스를 사용하여 모든 변경 사항을 처리하고 기록하는 데 도움을 받으세요.

# 참고 자료

- https://devcenter.heroku.com/articles/deploying-rails-applications-with-the-puma-web-server
- https://github.com/ankane/the-ultimate-guide-to-ruby-timeouts?tab=readme-ov-file#puma
- https://github.com/puma/puma/blob/master/docs/kubernetes.md#workers-per-pod-and-other-config-issues
- https://github.com/puma/puma?tab=readme-ov-file#clustered-mode
- https://github.com/puma/puma?tab=readme-ov-file#error-handling
- https://github.com/puma/puma/issues/2645
- https://github.com/rails/rails/blob/main/activestorage/test/dummy/config/puma.rb
- https://github.com/rails/rails/blob/main/railties/lib/rails/generators/rails/app/templates/config/puma.rb.tt
- https://github.com/rails/rails/issues/50450
- https://puma.io/puma/Puma/DSL.html
- https://ruby-concurrency.github.io/concurrent-ruby/master/Concurrent.html#physical_processor_count-class_method
- https://www.speedshop.co/2017/10/12/appserver.html
- https://www.mikeperham.com/2015/05/08/timeout-rubys-most-dangerous-api/