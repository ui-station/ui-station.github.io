---
title: "2024년 최신 가이드 Rails 프로젝트에 Swagger UI 설정하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-HowtoSetUpSwaggerUIforRailsProject2024Edition_0.png"
date: 2024-06-23 20:47
ogImage:
  url: /assets/img/2024-06-23-HowtoSetUpSwaggerUIforRailsProject2024Edition_0.png
tag: Tech
originalTitle: "How to Set Up Swagger UI for Rails Project — 2024 Edition"
link: "https://medium.com/@MaxDiffere39428/how-to-set-up-swagger-ui-for-rails-project-2024-edition-c387737c83c3"
---

![이미지](/assets/img/2024-06-23-HowtoSetUpSwaggerUIforRailsProject2024Edition_0.png)

명확하고 상호작용이 가능한 API 문서 작성은 유지보수 가능하고 접근성 있는 웹 서비스를 개발하는 중요한 부분입니다. Swagger UI는 개발자를 위한 동적 문서 인터페이스로서, Ruby on Rails 프로젝트에 통합하면 개발자 경험을 크게 향상시킬 수 있습니다. 이 글에서는 Grape API를 위한 인기 있는 gem인 grape-swagger와 RSpec API 문서 작성을 위한 gem인 rswag를 사용하여 Swagger UI를 설정하는 과정을 자세히 설명합니다.

---

# 해결책 1: 일반적인 Rails 프로젝트용

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

Cordyline은 Progressive Documentation 아이디어를 가진 새로운 프로젝트에요.

## 단계 1: Cordyline 설치

Cordyline 공식 웹사이트를 방문해서 미리 만들어진 gem 앱을 다운로드하세요.

## 단계 2: 통합

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

루비 온 레일즈 프로젝트를 선택하고 번들 설치를 실행해주세요.

(base) 컨트롤러에 도우미 모듈을 추가해주세요.

```js
class ApplicationController < ActionController::API
 include Cordyline::DocGen
end
```

## 단계 3: 첫 번째 문서 작성

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

컨트롤러의 모든 동작에서는 docto 메서드를 사용하여 문서 주석을 작성하세요.

```js
class WelcomeController
  doc "인사 엔드포인트, 아무 동작 없음" do
    detail "일부 세부 정보"
  end

  def index
  end
end
```

## 단계 4: Swagger 문서 호스팅

아래 라인을 routes.rb 파일에 추가하세요.

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

```ruby
require 'cordyline/web'

TestApp::Application.routes.draw do
  mount Cordyline::Web => '/team_doc' # 또는 다른 경로
end
```

이제 팀은 배포된 웹사이트에서 /team_doc/index.html 경로로 Swagger 문서에 액세스할 수 있습니다.

# 해결책 2: Grape API에 대한 설명

Grape는 루비용 REST와 유사한 API 마이크로 프레임워크로, Rack에서 실행하거나 Rails와 같은 기존 웹 애플리케이션 프레임워크와 함께 사용할 수 있도록 설계되었습니다. Grape-swagger는 Grape API에 대한 자동 생성된 문서를 제공합니다.

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

## 단계 1: grape-swagger 설치하기

Gemfile에 grape-swagger를 추가하고 프로젝트에 추가하려면 번들 설치를 실행하세요:

```js
gem 'grape-swagger'
```

그리고 실행하세요:

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
번들 설치
```

## 단계 2: grape-swagger 구성

grape-swagger를 위한 초기 설정을 해보세요:

```js
# config/initializers/swagger.rb
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

```js
if defined?(Grape)
  GrapeSwaggerRails.options.url      = "/api/swagger_doc"
  GrapeSwaggerRails.options.app_name = "MyApp"
  GrapeSwaggerRails.options.app_url  = "/"
end
```

## 단계 3: API 엔티티 및 문서 정의

grape-swagger가 문서화할 수 있도록 API 엔티티를 정의해야 합니다:

```js
# app/api/entities/my_entity.rb
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

```js
모듈 API
  모듈 엔티티
    클래스 MyEntity < Grape::Entity
      expose :id, documentation: { type: 'Integer', desc: '엔티티의 ID' }
      # ... 추가 코드 ...
    end
  end
end
```

## 단계 4: 스웨거 엔드포인트 추가

API 베이스 클래스에서 스웨거 설명 라우트를 추가하여 스웨거 JSON을 제공할 수 있도록 합니다:

```js
# app/api/base.rb
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

```ruby
class API::Base < Grape::API
  add_swagger_documentation
end
```

## 단계 5: Swagger 문서 보기

이제 http://localhost:3000/swagger로 이동하여 Swagger UI에 액세스할 수 있습니다.

# 해결 방법 3: RSpec 사용하기

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

rswag은 API 테스트에 기반하여 자동으로 Swagger 호환 API 문서를 생성하는 데 RSpec과 통합됩니다.

## 단계 1: rswag-specs 설치

Gemfile에 rswag-specs를 추가하고 설치하세요:

```js
gem 'rswag-specs'
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

그리고 다음을 실행해 주세요:

```js
bundle install
```

## 단계 2: 구성 및 파일 생성

RSwag 생성기를 실행하여 API 사양을 위한 초기 구성 및 디렉터리를 생성하세요.

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
rails g rswag:install
```

## 단계 3: Swagger DSL을 사용하여 요청 스펙 작성하기

스펙 파일을 생성하고 RSwag의 DSL을 사용하여 엔드포인트를 테스트하고 문서화하세요:

```js
# spec/integration/my_api_spec.rb
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

```ruby
require 'swagger_helper'
describe 'My API' do
  path '/my_api/endpoint' do
    ...
    get 'Retrieves something' do
      produces 'application/json'
      response '200', 'successful' do
        schema type: :object,
               properties: {
                 id: { type: :integer }
               },
               required: ['id']
        # Your test code goes here
      end
    end
  end
end
```

## 단계 4: RSwag UI로 API 문서에 액세스하기

RSwag는 http://localhost:3000/api-docs에서 액세스할 수 있는 Swagger UI를 제공합니다. 이 UI는 직접 작성한 사양을 반영합니다.
