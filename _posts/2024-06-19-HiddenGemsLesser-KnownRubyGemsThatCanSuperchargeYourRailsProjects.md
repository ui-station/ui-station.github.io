---
title: "숨겨진 보석 레일즈 프로젝트에 에너지를 불어넣어 줄 수 있는 잘 알려지지 않은 루비 젬들"
description: ""
coverImage: "/assets/img/2024-06-19-HiddenGemsLesser-KnownRubyGemsThatCanSuperchargeYourRailsProjects_0.png"
date: 2024-06-19 22:24
ogImage: 
  url: /assets/img/2024-06-19-HiddenGemsLesser-KnownRubyGemsThatCanSuperchargeYourRailsProjects_0.png
tag: Tech
originalTitle: "Hidden Gems: Lesser-Known Ruby Gems That Can Supercharge Your Rails Projects"
link: "https://medium.com/devops-dev/hidden-gems-lesser-known-ruby-gems-that-can-supercharge-your-rails-projects-69f4e2ffe704"
---


```markdown
![Hidden Gems: Lesser-Known Ruby Gems That Can Supercharge Your Rails Projects](/assets/img/2024-06-19-HiddenGemsLesser-KnownRubyGemsThatCanSuperchargeYourRailsProjects_0.png)

루비온레일즈는 강력한 프레임워크이며, 우리는 모두 Devise, RSpec, 그리고 Sidekiq과 같은 인기있는 젬들에 대해 알고 있지만, 몇 가지 잘 알려지지 않은 젬들이 있습니다. 이러한 숨겨진 보석들을 살펴보고 개발 경험을 높일 수 있는 방법을 탐구해 봅시다.

# 1. Dalli

Dalli은 Memcached 캐싱 시스템과 시원찮게 통합되는 강력한 루비 젬입니다. 이는 빠르고 빈번하게 접근되는 데이터를 캐싱함으로써 응용 프로그램의 성능을 크게 향상시킬 수 있습니다. 세션 데이터, 조각 캐싱 또는 전체 페이지 캐싱을 다루고 있다면, Dalli가 모두 대처할 것입니다. 이렇게 사용할 수 있습니다:
```

<div class="content-ad"></div>

```js
# Gemfile
gem 'dalli'

# config/environments/production.rb
config.cache_store = :dalli_store, 'localhost:11211', { expires_in: 1.day, compress: true }
```

# 2. Redis-Rails

Redis-Rails는 캐싱 및 백그라운드 작업을 위해 Redis의 강력함을 활용하는 또 다른 젬입니다. 그것은 Rails와 원활하게 통합되어 Redis를 기본 캐시 저장소로 사용하거나 세션 데이터 저장소로 사용할 수 있게 합니다. 아래는 간단한 예시입니다:

```js
# Gemfile
gem 'redis-rails'

# config/environments/production.rb
config.cache_store = :redis_store, 'redis://localhost:6379/0/cache', { expires_in: 1.day }
```

<div class="content-ad"></div>

## 3. ActiveSupport::Cache

알려지지 않은 편은 아니지만 ActiveSupport::Cache는 더 많은 주목을 받을 가치가 있습니다. 이는 MemoryStore, FileStore 및 NullStore를 포함한 다양한 캐싱 스토어에 대한 통일된 인터페이스를 제공합니다. 응용 프로그램의 요구에 따라 이러한 스토어 간을 쉽게 전환할 수 있습니다.

## 4. Rack::Cache

Rack::Cache는 Rails 애플리케이션과 통합되어 HTTP 캐싱을 제공하는 미들웨어입니다. 브라우저 캐싱을 처리하고 서버 부하를 줄이는 데 특히 유용합니다. config/application.rb에 다음을 추가하세요:

<div class="content-ad"></div>

```js
config.middleware.use Rack::Cache,
  verbose: true,
  metastore: 'file:/var/cache/rack/meta',
  entitystore: 'file:/var/cache/rack/body'
```

## 5. 캐시 머니

캐시 머니는 ActiveRecord 모델의 캐싱을 간편하게 해주는 젬입니다. 모델 인스턴스를 자동으로 캐시하고, 레코드가 업데이트될 때 캐시 만료를 처리합니다. 데이터베이스 쿼리의 속도를 높이고 데이터베이스 서버 부하를 줄이는 데 사용하세요.

## 6. IdentityCache
```

<div class="content-ad"></div>

IdentityCache는 ActiveRecord 연관을 캐싱하는 간결한 솔루션이에요. 캐시로부터 연관 레코드를 지능적으로 가져와서 데이터베이스 쿼리 횟수를 줄여줘요. 성능을 중시하는 애플리케이션에 꼭 필요한 기능이죠.

## 7. Cashier

Cashier는 뷰에서 fragment 캐싱을 지원하는 젬이에요. 가벼우며 사용하기 쉬워요. 뷰 코드를 캐시 블록으로 감싸기만 하면 Cashier가 나머지를 처리해줄 거에요.

## 8. Readthis

<div class="content-ad"></div>

Readthis는 Redis를 백엔드로 사용하는 고성능 캐싱 젬입니다. 속도와 효율성을 고려하여 디자인되어 있어, 레일즈 애플리케이션에서 빠른 캐싱이 필요한 경우에 우수한 선택지입니다.

## 9. Http::Cache

Http::Cache는 레일즈 애플리케이션에 대한 HTTP 캐싱 헤더를 제공합니다. 브라우저와 프록시가 자산을 효율적으로 캐시하도록 보장하여 서버로의 불필요한 요청을 줄입니다.

## 10. Rails Cache Digests

<div class="content-ad"></div>

레일즈 캐시 다이제스트는 프래그먼트 캐싱을 위해 캐시 키를 최적화합니다. 템플릿과 관련된 레코드를 기반으로 자동으로 캐시 키를 생성합니다. 캐시 무효화로 인한 머리 아픔을 피하려면 사용하세요.

이 젬들은 잘 알려지지 않았을 수 있지만, 레일즈 프로젝트를 크게 향상시킬 수 있습니다. 그러니 자세히 살펴보고, 개발 여정을 더욱 강력하게 만들어보세요! 🚀

이 글이 도움이 되었나요? 아래 댓글에 의견을 공유해주세요!