---
title: "Rails 7 폼 제출과 Turbo Stream  폼 제출 실패시 에러 표시"
description: ""
coverImage: "/assets/img/2024-06-19-Rails7FormsubmissionwithTurboStreamShowingerroronfailureofformsubmission_0.png"
date: 2024-06-19 22:11
ogImage: 
  url: /assets/img/2024-06-19-Rails7FormsubmissionwithTurboStreamShowingerroronfailureofformsubmission_0.png
tag: Tech
originalTitle: "Rails 7 Form submission with Turbo Stream || Showing error on failure of form submission"
link: "https://medium.com/@kanhu.dubey9/rails-7-form-submission-with-turbo-stream-showing-error-on-failure-of-form-submission-cd93e3f5168a"
---


# 소개

Hotwire 스위트의 일부인 Turbo Stream은 Rails 애플리케이션에서 실시간 업데이트를 처리하는 우아한 방법을 제공합니다. Rails 7에서 Turbo Stream을 사용하여 양식 제출을 관리하는 것은 더욱 강력하고 간소화되었습니다. 이 블로그 포스트에서는 양식 제출이 실패할 때 오류를 우아하게 처리하여 유연한 사용자 경험을 제공하는 방법에 대해 살펴보겠습니다.

# 전제 조건

시작하기 전에 다음 사항을 확인하세요:

<div class="content-ad"></div>

- Rails 7 애플리케이션을 설정했어요.
- Turbo와 Stimulus 라이브러리가 프로젝트에 포함되어 있어요. (이들은 Rails 7 앱에서 기본으로 제공돼요)

# 단계 1: 모델 및 컨트롤러 설정하기

간단한 모델과 컨트롤러를 설정해볼게요. 데모를 위해 간단한 제목과 본문을 가진 Post 모델을 만들고, PostsController에서 폼 제출을 처리할 거예요.

## 모델 및 컨트롤러 생성하기

<div class="content-ad"></div>

## 유효성 정의

Post 모델 (app/models/post.rb) 에 몇 가지 유효성을 추가해보세요:

```rb
class Post < ApplicationRecord
 validates :title, presence: true
 validates :body, presence: true
end
```

# 단계 2: 폼 생성하기

<div class="content-ad"></div>

다음으로, 새로운 게시물을 제출하기 위한 양식을 만들어보겠습니다. PostsController에서 new 및 create 액션을 정의하세요:

```js
class PostsController < ApplicationController
  def new
    @post = Post.new
  end

  def create
    @post = Post.new(post_params)
    if @post.save
      redirect_to @post, notice: '게시물이 성공적으로 생성되었습니다.'
    else
      render :new, status: :unprocessable_entity
    end
  end

  private

  def post_params
    params.require(:post).permit(:title, :body)
  end
end
```

# 단계 3: 뷰 작성

## 양식 부분

<div class="content-ad"></div>

폼 부분을 생성하려면 다음 파일을 만드세요 (app/views/posts/_form.html.erb):

```ruby
<%= form_for post, html: { class: 'row' } do |f| %>
  <div class="col-auto">
    <%= f.label :title, class: 'form-label' %>
    <%= f.text_field :title, class: 'form-control' %>
  </div>
  <div class="col-auto">
    <%= f.label :body, class: 'form-label' %>
    <%= f.text_area :body, class: 'form-control' %>
  </div>
  <div class="col-auto mt-4">
    <%= f.submit class: 'btn btn-primary' %>
  </div>
<% end %>
```

폼 부분을 생성하려면 다음 파일을 만드세요 (app/views/posts/new.html.erb):

```ruby
<h2>새 글 작성하기</h2>
<%= render 'form', post: @post %>
```

<div class="content-ad"></div>

# 이제 레일즈 7에서 오류가 발생합니다.
새로운 포스트를 생성하기 위해 폼을 제출할 때.

```js
turbo.es2017-esm.js:2115 Error: Form responses must redirect to another location
    at FormSubmission.requestSucceededWithResponse (turbo.es2017-esm.js:679:27)
    at FetchRequest.receive (turbo.es2017-esm.js:450:27)
    at FetchRequest.perform (turbo.es2017-esm.js:431:31)
```

이것은 모든 링크 클릭과 폼 제출이 이제 레일즈 7에서 TURBO_STREAM 요청이 되었기 때문에 발생합니다.
더 빠른 응답을 얻기 위해서이며, TURBO_STREAM 요청을 만들기 위한 명시적 코드를 작성할 필요가 없습니다.

TURBO_STREAM 요청이 하는 일은 무엇인가요?

<div class="content-ad"></div>

일반적으로 전체 페이지를 다시로드하지 않고 페이지에 터보 프레임을 업데이트합니다.

해결하는 방법은

이 문제를 해결하려면

이렇게 컨트롤러에서 TURBO_STREAM 요청을 처리해야 합니다.

```js
  def create
    @post = Post.new(post_params)
    if @post.save
      redirect_to @post, notice: '게시물이 성공적으로 생성되었습니다.'    
    else
      respond_to do |format|
        format.turbo_stream { render turbo_stream: turbo_stream.replace(@post, partial: 'posts/form', locals: { post: @post }) }
        format.html { render :new }
      end
    end
  end
```

<div class="content-ad"></div>

터보 스트림.replace 메소드는 레일즈의 Turbo Streams 라이브러리의 일부입니다. 이 메소드는 전체 페이지 새로고침 없이 페이지의 일부를 교체하는 Turbo Stream 액션을 생성합니다.

다음은 이 메소드와 해당 속성에 대한 설명입니다:

- format.turbo_stream: 이는 다음 블록이 Turbo Stream 요청에 응답하는 데 사용되어야 함을 지정합니다. Turbo Stream은 Hotwire 프레임워크의 일부로서 WebSocket을 통해 페이지의 특정 부분에 업데이트를 보낼 수 있게 합니다.
- `render turbo_stream: turbo_stream.replace(@post, partial: 'posts/form', locals: { post: @post })`: 이는 Turbo Stream 요청에 대해 실행되는 블록입니다. 응답을 보내기 위해 render 메소드를 사용합니다.
- turbo_stream.replace(@post, partial: `posts/form`, locals: { post: @post }): 이는 페이지의 일부를 교체하는 Turbo Stream 액션입니다. 새로운 콘텐츠로 Turbo Frame이나 Turbo Stream 요소를 교체하는 replace 메소드를 사용합니다.
- @post: 이는 교체 액션의 대상입니다. 페이지에서 Turbo Frame이나 Turbo Stream 요소의 ID와 일치해야 합니다.
- 지금은 대상이 form ID인 new_post인 경우가 있습니다. 첫 번째 인자에 @post를 전달했기 때문에 replace 메소드가 자동으로 form ID를 대상으로 설정합니다.
- partial: 'posts/form': 이는 대상을 렌더링하고 교체할 부분을 지정합니다.
- locals: { post: @post }: 이는 부분에 로컬 변수를 전달합니다. 이 경우 @post 인스턴스 변수를 post라는 로컬 변수로 전달합니다.

또한, 폼 제출 실패를 처리하기 위해 뷰에 오류를 추가할 예정입니다.

<div class="content-ad"></div>


## 게시물 생성하기

```ruby
<%= form_for post, html: { class: 'row' } do |f| %>
  <% if post.errors.any? %>
    <div class="col-12">
      <div class="alert alert-danger">
        <ul>
          <% post.errors.full_messages.each do |message| %>
            <li><%= message %></li>
          <% end %>
        </ul>
      </div>
    </div>
  <% end %>

  <div class="col-auto">
    <%= f.label :title, class: 'form-label' %>
    <%= f.text_field :title, class: 'form-control' %>
  </div>
  <div class="col-auto">
    <%= f.label :body, class: 'form-label' %>
    <%= f.text_area :body, class: 'form-control' %>
  </div>
  <div class="col-auto mt-4">
    <%= f.submit class: 'btn btn-primary' %>
  </div>
<% end %>
```

이렇게 수정하면 우리는 Rails 7에서 양식 제출 실패를 처리하는 문제를 해결할 수 있습니다.

# 결론: 

Turbo는 Basecamp에 의해 소개된 Hotwire 프레임워크의 일부입니다. 최소한의 JavaScript를 사용하여 HTML을 통해 전송함으로써 현대적인 웹 애플리케이션을 구축하는 방법을 제공하도록 설계되었습니다. Turbo에는 세 가지 주요 부분이 있습니다: Turbo Drive, Turbo Frames 및 Turbo Streams.


<div class="content-ad"></div>

위 단계를 따라 하셨다면 Rails 7 애플리케이션에서 Turbo Stream을 성공적으로 구현하셨습니다. 이 방법을 통해 사용자 경험을 향상시켜 전체 페이지 새로고침 없이 즉각적인 피드백을 제공하고 Hotwire의 Turbo 라이브러리의 능력을 활용할 수 있습니다.