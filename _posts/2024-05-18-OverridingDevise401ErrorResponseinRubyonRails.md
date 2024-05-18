---
title: "루비 온 레일즈에서 Devise 401 오류 응답 덮어쓰기"
description: ""
coverImage: "/assets/img/2024-05-18-OverridingDevise401ErrorResponseinRubyonRails_0.png"
date: 2024-05-18 15:16
ogImage: 
  url: /assets/img/2024-05-18-OverridingDevise401ErrorResponseinRubyonRails_0.png
tag: Tech
originalTitle: "Overriding Devise 401 Error Response in Ruby on Rails"
link: "https://medium.com/passgage-tech/overriding-devise-401-error-response-in-ruby-on-rails-35e060d492c8"
---


```markdown
![Image](/assets/img/2024-05-18-OverridingDevise401ErrorResponseinRubyonRails_0.png)

안녕하세요, 여러분! 오늘은 루비 온 레일즈 앱에서 뷰와 엔드포인트를 동시에 가지고 있을 때 Devise 401 에러 응답을 어떻게 오버라이딩하는지 이야기하려고 해요. 우선, 제 문제를 소개하고 해결책에 대해 논의하려고 해요. 다른 해결책이 있다면 제게 공유해주세요.

제 User 모델에 Devise 메서드가 있어요:

```ruby
devise :registerable,
  :recoverable, :rememberable, :lockable, :trackable,
  :omniauthable

def active_for_authentication?
  super and is_active?
end
```

<div class="content-ad"></div>

해당 메서드는 사용자가 활성화되어 있는지 여부를 확인합니다. 사용자가 활성화되어 있지 않으면 Devise가 세션을 생성하는 것을 허용하지 않습니다.

세션 컨트롤러가 있습니다:

```js
class Api::V2::SessionsController < Devise::SessionsController
  respond_to :json

  def create
    # 여러분의 코드
  end
end
```

우리는 Devise::SessionsController에서 Api::V2::SessionsController의 create 메서드를 오버라이드합니다. 하지만 active_for_authentication? 메서드의 응답 키나 값을 오버라이드할 수 없습니다. 일반적으로 응답은 아래와 같습니다:

<div class="content-ad"></div>

```js
{
    "error": "Your account is not activated yet."
}
```

위 응답은 원하는 형식이 아닙니다. 아래처럼 덮어쓰기해야 합니다:

```js
{
    "success": false,
    "status": 401,
    "message": "Your account is not activated yet.",
    "errors": [
        {
            "field_name": "inactive_user",
            "messages": [
                "Your account is not activated yet."
            ]
        }
    ]
}
```

## 이를 수정하는 데는 2단계가 필요합니다.```

<div class="content-ad"></div>

- 새로운 관심사를 만들기

```js
class CustomFailureApp < Devise::FailureApp
  def http_auth_body
    if request.controller_class == Api::V2::SessionsController && request.format == :json
      json_error_response
    else
      super
    end
  end

  def json_error_response
    self.status = 401
    self.content_type = "application/json"
    self.response_body =
      {
        success: false,
        status: 401,
        message: I18n.t('devise.failure.inactive'),
        "errors": [
          {
            "field_name": "inactive_user",
            "messages": [
              I18n.t('devise.failure.inactive')
            ]
          }
        ]
      }.to_json
  end
end
```

2. Devise 구성 추가

```js
 config.warden do |manager|
    manager.failure_app = CustomAuthFailure
  end 
```

<div class="content-ad"></div>

The Links:

- [https://gist.github.com/emilsoman/5604254#file-custom_auth_failure_app-rb](https://gist.github.com/emilsoman/5604254#file-custom_auth_failure_app-rb)
- [https://stackoverflow.com/questions/7297183/custom-devise-401-unauthorized-response/35299936#35299936](https://stackoverflow.com/questions/7297183/custom-devise-401-unauthorized-response/35299936#35299936)
- [https://github.com/heartcombo/devise/blob/main/app/controllers/devise/sessions_controller.rb](https://github.com/heartcombo/devise/blob/main/app/controllers/devise/sessions_controller.rb)
- [https://github.com/heartcombo/devise/blob/main/lib/devise/failure_app.rb#L197](https://github.com/heartcombo/devise/blob/main/lib/devise/failure_app.rb#L197)