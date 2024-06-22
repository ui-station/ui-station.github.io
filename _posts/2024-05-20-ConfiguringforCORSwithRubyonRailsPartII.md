---
title: "루비 온 레일즈로 CORS 구성하기 - 2부"
description: ""
coverImage: "/assets/img/2024-05-20-ConfiguringforCORSwithRubyonRailsPartII_0.png"
date: 2024-05-20 15:49
ogImage: 
  url: /assets/img/2024-05-20-ConfiguringforCORSwithRubyonRailsPartII_0.png
tag: Tech
originalTitle: "Configuring for CORS with Ruby on Rails — Part II"
link: "https://medium.com/@chris.lty07/configuring-for-cors-with-ruby-on-rails-part-ii-fb5399a80d64"
---


여러 달 전에, 프록시를 사용하여 CORS 오류를 해결하는 방법에 대해 게시물을 게시했었어요. 배포할 때까지는 잘 되지만, 프록시를 설정하는 것이 최선의 방법이 아니라는 것을 빨리 깨달았어요. 이전 포스트에서 놓친 요소 중 하나는 Ruby on Rails API를 빌드할 때 사용자 인증을 통합하는 경우에 Rack-CORS를 구성하는 것이었어요. 이 포스트는 개인 프로젝트에서 개발 및 프로덕션에서 CORS 오류를 디버깅하면서 배운 내용을 요약한 것이에요.

처음에 제가 채택한 접근 방식은 순수한 Ruby를 사용하는 것이었어요. 이 방법은 분명 더 복잡했지만, 사전 플라이트 HTTP 요청 주변의 세부 사항을 이해하고 요청/응답 헤더가 동작하는 실제 내용을 이해하는 데 도움이 되었어요. 그래서 이 방법을 간소화하기 전에 CORS 오류를 해결하는 첫 번째 방법으로 제시하고 있어요.

## CORS 사전 체크 — HTTP OPTIONS 요청

<div class="content-ad"></div>


![이미지](/assets/img/2024-05-20-ConfiguringforCORSwithRubyonRailsPartII_1.png)

가장 먼저 이해해야 할 것은 프런트엔드가 백엔드에 보내는 HTTP 요청의 유형에 따라 해당 요청이 처리되기 전에 완료되는 CORS 사전검사입니다. 이는 보내려는 HTTP 요청이 안전한지 여부를 결정합니다. 사전검사는 HTTP OPTIONS 메서드를 통해 수행됩니다. 개발 도구의 네트워크 탭에서 사전검사 및 fetch 요청을 확인할 수 있습니다:

![이미지](/assets/img/2024-05-20-ConfiguringforCORSwithRubyonRailsPartII_2.png)

OPTIONS 요청에는 다음과 같은 요청 헤더가 포함될 수 있습니다:


<div class="content-ad"></div>

- Origin: 요청이 온 곳을 나타냅니다. 예를 들어, 개발 중이라면 "http://localhost:3000"이거나 배포된 앱이라면 "http://127.0.0.1:5173" 또는 "http://yourwebsite.production.app"입니다.
- Access-Control-Request-Method: 사용 가능한 HTTP 메소드를 나타냅니다. 예를 들어, GET, POST, PUT, PATCH, DELETE, OPTIONS가 있습니다.
- Access-Control-Request-Headers: 실제 요청을 보낼 때 프론트엔드 애플리케이션이 보낼 수 있는 HTTP 헤더를 지정합니다.
- Access-Control-Request-Credentials: 요청이 인증을 요구하는지를 결정합니다. 요구되면 true로 설정하고, 그렇지 않으면 생략됩니다. 이 헤더의 유효한 값은 true뿐입니다.
- Access-Control-Max-Age: 프리플라이트 요청의 결과를 캐싱할 수 있는 시간(초)을 결정하는 선택적인 헤더입니다. MDN 문서에 따르면, 기본값은 5초이며 최대 24시간(86400초)입니다.

만약 서버가 프리플라이트 확인에 적절한 응답 헤더를 제공한다면, 그때에만 프론트엔드가 실제 HTTP 요청을 계속할 수 있습니다. 프리플라이트 확인이 실패하면 적절한 헤더가 제공되지 않았기 때문에 아래와 같이 콘솔에서 볼 수 있는 오류의 예시가 있습니다:

<img src="/assets/img/2024-05-20-ConfiguringforCORSwithRubyonRailsPartII_3.png" />

## 루비 온 레일즈를 이용한 CORS 구성

<div class="content-ad"></div>

이 방법을 사용하면 추가적인 젬이나 종속성을 설치할 필요가 없습니다. 첫 번째 단계는 요청을 CORS 컨트롤러로 라우팅할 config/routes.rb에 OPTIONS 라우트를 추가하는 것입니다. 아래 예시에서는 ' /login', ' /signup', ' /logout', '그리고 ' /users' 네 개의 라우트를 추가했습니다.

```js
# config/routes.rb에 추가

Rails.application.routes.draw do

  match '/login', controller: 'cors', action: 'cors_preflight_check', via: [:options]
  match '/signup', controller: 'cors', action: 'cors_preflight_check', via: [:options]
  match '/logout', controller: 'cors', action: 'cors_preflight_check', via: [:options]
  match '/users', controller: 'cors', action: 'cors_preflight_check', via: [:options]
  # ... 다른 라우트들은 여기에 추가

end
```

구성하려는 특정 엔드포인트들이 있으면 아래와 같이 개별적으로 추가하여 `path`와 `컨트롤러의 이름`을 대체할 수 있습니다:

```js
match '<path>', controller: "<컨트롤러의 이름>", action: 'cors_preflight_check', via: [:options]
```

<div class="content-ad"></div>

내 애플리케이션은 약 20개의 엔드포인트로 끝났어요. OPTIONS 요청을 경로하는 DRY한 방법은 아래와 같습니다. 모든 OPTIONS 요청을 와일드카드 "*" 경로를 사용하여 한 컨트롤러와 메소드에 경로하려면 이 방법이 작동합니다.

```js
# config/routes.rb에 추가

Rails.application.routes.draw do

  options '*path', to: 'application#cors_preflight_check'
  # ... 다른 루트

end
```

그 다음으로 app/controllers에 cors_controller.rb 파일을 생성하고 다음 메소드를 추가하세요. HTTP 메소드가 OPTIONS인 경우 cors_preflight_check 메소드가 cors_set_access_control_headers 메소드를 실행합니다.

```js
# app/controllers/cors_controller.rb에 추가:

class CorsController < ApplicationController

    def cors_preflight_check
        if request.method == 'OPTIONS'
          cors_set_access_control_headers
          render text: '', content_type: 'text/plain'
        end
      end

    protected

    def cors_set_access_control_headers
        response.headers['Access-Control-Allow-Origin'] = "http://localhost:4000"
        response.headers['Access-Control-Allow-Credentials'] = "true"
        response.headers['Access-Control-Allow-Methods'] = 'POST, GET, PUT, PATCH, DELETE, OPTIONS'
        response.headers['Access-Control-Allow-Headers'] = 'Origin, Content-Type, Accept, Authorization, Token, Auth-Token, Email, X-User-Token, X-User-Email'
        response.headers['Access-Control-Max-Age'] = '86400'
    end

end
```

<div class="content-ad"></div>

cors_set_access_control_headers 메서드는 적절한 응답 헤더를 설정합니다. Access-Control-Allow-Methods, Access-Control-Allow-Headers 및 Access-Control-Max-Age에 대해 각각 모든 HTTP 메소드, 허용되는 모든 헤더 및 최대 캐시 기간을 나열했습니다.

Origin 및 Credentials에 초점을 맞추고 싶습니다. 인증된 요청이 필요하지 않다면 Origin 헤더에 와일드카드 "*"를 사용하여 서버가 모든 원본에서 요청을 수락하도록 할 수 있습니다. 그러나 로그인, 가입 등과 같은 인증이 필요한 HTTP 요청의 경우, 원본으로 와일드카드 "*"를 사용할 수 없습니다. 시도하면 다음과 같은 오류가 발생합니다.

<img src="/assets/img/2024-05-20-ConfiguringforCORSwithRubyonRailsPartII_4.png" />

따라서 인증이 필요한 경우 프론트엔드 애플리케이션의 원본을 명시해야 합니다. API에 액세스를 허용하고 싶은 여러 원본이 있는 경우, response.headers['Access-Control-Allow-Origin']을 다음과 같이 업데이트할 수 있습니다:

<div class="content-ad"></div>

```js
# in app/controllers/cors_controller.rb:
def cors_set_access_control_headers
        response.headers['Access-Control-Allow-Origin'] = check_origin
        response.headers['Access-Control-Allow-Credentials'] = "true"
        response.headers['Access-Control-Allow-Methods'] = 'POST, GET, PUT, PATCH, DELETE, OPTIONS'
        response.headers['Access-Control-Allow-Headers'] = 'Origin, Content-Type, Accept, Authorization, Token, Auth-Token, Email, X-User-Token, X-User-Email'
        response.headers['Access-Control-Max-Age'] = '86400'
end

def check_origin
        permitted_origins = Set[
            "http://localhost:4000", 
            "http://127.0.0.1:4000",
            "http://yourwebsite.production.app",
            /\Ahttps:\/\/deploy-preview-\d{1,4}--yourwebsite\.domain\.app\z/
        ]

        origin = request.origin

        if permitted_origins.include?(origin)
            origin
        else
            render json: { error: "Origin not permitted" }
        end
end
```

check_origin 메소드 내에서, 허용된 origin을 Set으로 정의합니다. 여기에 Rail API가 허용할 origin을 추가합니다: 포트 번호가 포함된 localhost 및 프론트엔드 응용 프로그램이 배포된 경우의 프로덕션 URL 등이 포함됩니다.

[선택사항] 내 응용 프로그램에서 프론트엔드를 배포하기 위해 Netlify를 사용하고 있었으며, Netlify의 CI/CD의 일부로 "https://deploy-preview-123--yourwebsite.netlify.app"와 같은 형식의 배포 미리보기가 생성됩니다. 여기서 "123"은 길이가 3 이상인 정수 문자열일 수 있습니다. 이것을 해결하기 위해 유연성을 제공하고 Netlify의 미리보기 모드로 배포된 앱을 계속 테스트할 수 있도록 하기 위해 정규식을 추가했습니다.

[주의] Origin을 정의할 때 슬래시(/)가 앞뒤로 제대로 있는지에 주의하십시오. 이를 제대로 맞추기 위해 백엔드를 5번 재배포해야 할 것 같았습니다..

<div class="content-ad"></div>

그게 다입니다! Rails 서버를 다시 시작하면 CORS 오류가 더 이상 표시되지 않아야 합니다.

## Postman에 대한 참고 사항

API를 테스트할 때 Postman을 사용할 때, Postman에 대한 origin이 설정되어 있지 않아도 CORS가 발생하지 않는 것을 알았습니다. 이는 Postman의 기본 origin이 “nil”로 설정되어 있기 때문입니다. 따라서 Postman은 API를 테스트하는 데 훌륭하지만, 프론트엔드와 백엔드 응용 프로그램을 연결하고 HTTP 요청이 다른 도메인에서 전송되는 경우에만이 문제가 발생한다는 점을 기억해 주세요. 저는 개발 중에, 배포 중에, 그리고 CI/CD에서 여러 번 이 문제를 겪었습니다 (즐겁게요...)!

## Rack-CORS Gem 사용하기

<div class="content-ad"></div>

이제 Vanilla Ruby를 사용하여 상세 구현을 살펴 보았습니다. Rack-CORS gem을 사용하여 이를 간단하게 만들어 봅시다.

먼저 Gemfile에 rack-cors gem을 추가하세요. 이미 추가되어 있을 수 있으므로 주석 처리만 해제해야 할 수도 있습니다.

```js
# Gemfile에 추가
gem 'rack-cors'
```

그런 다음 다음 명령어로 설치하세요:

<div class="content-ad"></div>

번들 설치

cors.rb를 구성하세요. 일반적인 Ruby 케이스에서 위와 같은 구성을 달성하려면 config/initializers/cors.rb로 이동하여 미들웨어 구성 코드 블록 주석 처리를 해제하고 다음과 같이 수정하세요:

```js
# in config/initializers/cors.rb

Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins "http://localhost:4000"
            "http://127.0.0.1:4000",
            "http://yourwebsite.production.app",
            /\Ahttps:\/\/deploy-preview-\d{1,4}--yourwebsite\.domain\.app\z/

    resource "*",
      headers: :any,
      methods: [:get, :post, :put, :patch, :delete, :options, :head],
      credentials: true,
      max_age: 86400
  end
end
```

그런 다음 서버를 재시작하면 문제 없이 작동해야 합니다!


<div class="content-ad"></div>

Rack-CORS를 사용하면 훨씬 쉬워지고 초반에 이를 어떻게 구성해야 할지 이해했더라면 훨씬 많은 시간을 절약할 수 있었을 텐데요. 하지만 HTTP 요청과 CORS 사전 확인에 대해 깊이 파헤친 것은 가치 있는 학습 경험이었습니다. 이를 통해 이러한 원칙을 다른 언어와 프레임워크로 번역할 수 있었죠. 예를 들어, CORS와 사전 확인 요청/응답 헤더를 이해하고 구성하는 것이 파이썬/Django로 CORS를 이해하고 구성하는 데 도움이 되었습니다.

읽어 주셔서 감사합니다.