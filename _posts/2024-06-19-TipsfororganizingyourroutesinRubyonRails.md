---
title: "루비 온 레일즈에서 라우트를 구성하는 팁"
description: ""
coverImage: "/assets/img/2024-06-19-TipsfororganizingyourroutesinRubyonRails_0.png"
date: 2024-06-19 22:19
ogImage:
  url: /assets/img/2024-06-19-TipsfororganizingyourroutesinRubyonRails_0.png
tag: Tech
originalTitle: "Tips for organizing your routes in Ruby on Rails"
link: "https://medium.com/unagi/tips-for-organizing-your-routes-in-ruby-on-rails-d5bb5fedbc4e"
---

![Route Organization Tips](/assets/img/2024-06-19-TipsfororganizingyourroutesinRubyonRails_0.png)

routes.rb 파일은 Ruby on Rails 개발 프로젝트의 중요한 부분입니다. 이 파일은 사실상 우리 애플리케이션의 지도 역할을 합니다. 따라서 이를 잘 구성하는 것이 중요합니다. 보통 모듈로 라우트를 구성하거나 알파벳 순으로 정리하여 시작하지만, 시간이 지남에 따라 이 파일은 종종 길들이기 어려운 정글로 변할 수 있습니다.

이는 집 안의 창고와 비슷합니다. 정리되지 않은 물건들을 보관하는 곳으로, 처음에는 무엇이 어디에 있는지 알 수 있지만 언젠가는 들어가서 무언가를 찾으려 하면, 과거에 사용한 스케이트보드와 할머니가 남긴 썩은 나무 상자 위에 오래된 사진과 첫 번째 애왔던 Tony의 목줄이 있는 전쟁터 같아집니다.

나는 그런 혼돈을 좋아하지 않기 때문에, 이 글에서는 routes.rb라는 그 창고를 잘 정리하는 데 매우 유용했던 몇 가지 실천 방법을 공유하겠습니다.

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

프로젝트에 미친 영향에 따른 팁 목록이에요. 각 팁은 독립적으로 적용할 수 있어, 모두 읽을 필요 없이 가장 관심 있는 부분으로 바로 이동해도 돼요.

- 알파벳 순으로 정리된 라우트
- Resource 및 resources
- only, not except
- 네임스페이스
- 제약 조건
- 관련 사항

# 1. 알파벳 순으로 정리된 라우트

이 팁은 우연이 아니라 제일 앞에 있어요; 저는 이것을 가장 중요하게 생각해요. 알파벳 순으로 라우트를 정리하는 것만으로가 아니라 팀 간 합의를 수립하는 중요성 때문에 최상의 결과를 가져다 주었어요.

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

개인적으로 나는 루비 온 레일즈의 가장 중요한 전제 중 하나는 Convention over Configuration이라고 생각해. 프레임워크가 제안한 규칙을 따르면 대개 좋은 결과를 얻을 수 있어. 이는 개발팀의 누구나 코드 조각을 찾거나 새로운 지시사항을 추가할 위치를 잘 알 수 있기 때문에 중요한데, 이것은 시간을 절약할 뿐만 아니라 결정을 내릴 부담을 덜어줘. 결국 스티브 잡스가 매일 같은 옷을 입은 이유가 있었을 테니까.

우리 라우트에도 동일한 원칙을 적용해야 해: 유지보수를 가능한 간단하고 깔끔하게 유지하기 위해 팀의 규칙을 확립해야 돼.

다양한 프로젝트에서 우리는 여러가지 규칙을 준수해왔어: 모듈별로 구성, 다른 파일로 분리, 알파벳 순으로 정렬 등. 의심의 여지없이 가장 간단하고 실용적인 접근 방식은 라우트 파일을 알파벳 순으로 정렬하는 것이었어. 라우트가 많다면 파일을 분리하는 것도 도움이 될 수 있지만, 절대적으로 필요하지 않은 한 그것은 피하는 편이 좋을 거야.

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

어떤 프로젝트의 비즈니스 레이어가 서로 통신하고 상호작용하는 객체로 표현되는 것과 같이, 저는 라우트를 리소스의 동작으로 보는 것을 좋아해요. 이러한 이유로, 저희 프로젝트에서 정의하는 거의 모든 라우트들은 일반적으로 특정 리소스와 연관되어 있습니다.

레일즈는 이를 처리하기 위해 다양한 메커니즘을 제공하지만, 기본적으로는 resources와 resource를 사용하는 것이 일반적입니다. 또한, 필요한 경우 리소스 라우트를 중첩시킬 수 있어서 아래와 같은 결과를 얻을 수 있습니다:

```js
resources :articles do
  resources :comments
end
```

⚠️ 중첩은 매우 유용할 수 있지만, 복잡성을 도입할 수 있으므로 조심해야 합니다. 한 단계 이상 중첩을 사용하지 않는 것을 권장합니다.

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

## 얕은 중첩

중첩의 한 가지 문제는 중첩된 리소스의 멤버 라우트가 부모 안에 포함된다는 것입니다.

이전 예제에서, 댓글에 대한 라우트는 다음과 같이 article 내에 중첩될 것입니다:

```js
GET /articles/:article_id/comments
GET /articles/:article_id/comments/new
POST /articles/:article_id/comments

GET /articles/:article_id/comments/:id <===== ⚠️
GET /articles/:article_id/comments/:id/edit <===== ⚠️
DELETE /articles/:article_id/comments/:id <===== ⚠️
PUT/PATCH /articles/:article_id/comments/:id <===== ⚠️
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

첫 세 개의 루트는 괜찮아 보이지만 특정 댓글을 포함하는 루트는 기사 안에서 어색해 보입니다.

이 문제를 해결하기 위해 우리는 다음과 같이 루트를 정의할 수 있습니다:

```js
resources :articles do
  resources :comments, only: [:index, :new, :create]
end
resources :comments, only: [:show, :edit, :update, :destroy]
```

이렇게 정의하면 다음과 같은 루트가 생성되는데, 내 의견으로는 훨씬 더 의미가 있다고 생각합니다:

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
GET /articles/:article_id/comments
GET /articles/:article_id/comments/new
POST /articles/:article_id/comments

GET /comments/:id <===== ✅
GET /comments/:id/edit <===== ✅
DELETE /comments/:id <===== ✅
PUT/PATCH /comments/:id <===== ✅
```

이 같은 결과를 얻으려면 shallow 매개변수를 사용할 수 있습니다:

```js
resources :articles do
 resources :comments, shallow: true
end
```

## 리소스와 관련이 없는 라우트는 어떻게 처리해야 합니까?

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

이건 좀 이상한 냄새가 난다고 봐요. 이런 경로 뒤에는 숨겨진 자원이 있을 가능성이 높습니다.

하지만 여전히 routes.rb에는 독립적인 경로가 있습니다. 예를 들어 로그인, 로그아웃 그리고 아마도 헬스체크 등이 떠오를 거예요.

```js
post 'login' => 'sessions#login', as: :login
delete 'logout' => 'sessions#logout', as: :logout
get 'up' => 'rails/health#show', as: :rails_health_check
```

# 3. Only, not except

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

저는 개발자로서 "암시하는 것보다 명확히 표현하는 것이 항상 낫다"는 규칙을 따릅니다. 몇 마디나 한 줄의 코드, 심지어 주석을 절약하려는 우리의 시도에서는 종종 응용 프로그램에서 문제를 발생시키거나 미래 개발자가 우리의 작업을 상속할 때 어렵게 만들 수 있습니다. (네, 제가 주석을 선호합니다.)

저는 라우트에도 같은 규칙을 적용하며 only를 except 대신 선호합니다. except를 사용하는 것이 편리할 수 있지만, 사용하지 않을 라우트를 생성할 가능성이 있습니다.

그래서 라우트에서 리소스를 생성할 때 처음으로 하는 일은 only 매개변수를 추가하는 것입니다.

```js
resources :products, only: %i[index new create show]
resources :users, only: %i[index new create destroy]
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

# 4. 네임스페이스

네임스페이스를 통해 라우트를 컨트롤러 그룹으로 구분하여 로직을 모듈화하는 데 도움이 됩니다.

다음과 같은 상황에서 특히 유용합니다:

1. 명확히 분리된 하위 시스템이 있는 경우. 예를 들어 백오피스와 프론트엔드.

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

2. 어플리케이션이 발전하면서 복잡도가 증가하고, 컨트롤러를 그룹/모듈로 분리하고 싶을 때 아래와 같이 코드를 작성할 수 있습니다.

```js
namespace :admin do
  resources :payments
  resources :users
end

namespace :ads do
  resource :report, only: %i[show]
end

namespace :finance do
  resource :report, only: %i[show]
```

이렇게 하면 다음과 같은 컨트롤러가 생기게 됩니다:

```js
Admin::PaymentsController # app/controllers/admin/payments_controller.rb
Admin::UsersController # app/controllers/admin/users_controller.rb
Ads::ReportController # app/controllers/ads/report_controller.rb
Finance::ReportController # app/controllers/finance/report_controller.rb
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

# 5. 제약 조건

제약 조건은 경로에 할당할 수 있는 제한 사항입니다. 제약 조건을 정의할 때는 요청 객체에서 지정된 메서드가 호출되고 반환된 값이 매개변수 값과 비교됩니다.

대표적인 예로 하위 도메인 제약 조건이 있습니다. 예를 들어 어드민을 위한 경로를 위한 제약 조건을 정의할 수 있습니다.

```js
namespace :admin do
  constraints subdomain: 'admin' do
    resources :users
  end
end
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

# 6. 고려 사항

또 다른 유용한 도구는 concerns 입니다. 이를 사용하면 일반적인 경로를 정의하고 이후 서로 다른 리소스에 사용할 수 있습니다.

예를 들어 최근 프로젝트에서 사용한 하나의 concern을 공유하겠습니다. 애플리케이션에는 다양한 리소스가 있었고, 이러한 리소스는 목록 내에서 순서를 변경할 수 있었 즉, 위치를 변경할 수 있었습니다. 이 위치를 변경하는 라우트는 코드베이스 전체에서 반복되어 사용되었기 때문에 이를 concern으로 추출했습니다:

```js
concern :positionable do
  patch :update_position, on: :member
end

resources :categories, only: %i[index new create], concerns: %i[positionable]
resources :category_groups, concerns: %i[positionable]
resources :sections, only: %i[index new create destroy], concerns: %i[positionable]
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

⚠️ 주의점은 코드에서 간접성을 소개합니다. 공통 경로를 추출할 가치가 있는지 여쭤보는 것이 중요합니다. 때로는 간접성을 소개하는 것보다 코드를 반복하는 것이 나을 수 있습니다.

저희가 루트 파일을 작성하는 데 따르는 몇 가지 관례입니다. 팀원과 동일한 규칙을 따를 필요는 없습니다. 이는 우리에게 효과가 있었던 사례입니다.

가장 중요한 것은 팀과 공통 규칙에 동의하여 루트 파일이 가장 깔끔하고 유지 관리가 가능하도록 하는 것입니다.

Unagi는 Ruby on Rails에서 12년 이상 선택한 스택을 사용하는 소프트웨어 부티크입니다. 자세한 내용은 소셜 미디어를 확인해보세요.
