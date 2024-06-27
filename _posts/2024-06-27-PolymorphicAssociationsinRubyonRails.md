---
title: "Ruby on Rails에서 다형성 연관 관계를 사용하는 방법"
description: ""
coverImage: "/assets/img/2024-06-27-PolymorphicAssociationsinRubyonRails_0.png"
date: 2024-06-27 19:17
ogImage: 
  url: /assets/img/2024-06-27-PolymorphicAssociationsinRubyonRails_0.png
tag: Tech
originalTitle: "Polymorphic Associations in Ruby on Rails"
link: "https://medium.com/@irfan.azhar.dev/polymorphic-associations-in-ruby-on-rails-b764fb85d580"
---


폴리모픽 연관은 루비 온 레일즈의 강력한 기능 중 하나로, 한 모델이 하나 이상의 다른 모델에 단일 연관을 사용하여 속할 수 있게 합니다. 이는 단일 모델이 다른 여러 모델과 폴리모픽 인터페이스를 통해 연관될 수 있음을 의미합니다. 이 기능은 특히 한 모델이 여러 유형의 엔터티에 속할 수 있는 경우에 유용합니다.

## 폴리모픽 연관이란 무엇인가요?

폴리모픽 연관은 단일 연관을 사용하여 모델이 여러 다른 모델을 참조할 수 있게 합니다. 예를 들어, Comment 모델이 Post 모델 또는 Photo 모델에 속할 수 있는 시나리오를 고려해보세요. 각 모델에 대해 별도의 연관을 생성하는 대신, 폴리모픽 연관을 사용하여 이를 달성할 수 있습니다.

다음은 루비 온 레일즈에서 폴리모픽 연관을 정의하는 방법입니다:

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

- 모델 생성하기

먼저 필요한 모델을 생성하세요. 이 예시에서는 Post, Photo, 그리고 Comment 모델을 만들겠습니다.

```js
rails generate model Post title:string body:text
rails generate model Photo title:string url:string
rails generate model Comment body:text commentable:references{polymorphic}
```

2. 관계 정의하기

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

다음에는 모델에서 연관 관계를 정의하세요. Comment 모델에서는 commentable이라는 다형 엔티티에 속한다고 지정하십시오.

```ruby
# app/models/comment.rb
class Comment < ApplicationRecord
  belongs_to :commentable, polymorphic: true
end
```

Post 및 Photo 모델에서 다형 관계를 통해 많은 comments를 가지고 있다고 지정하세요.

```ruby
# app/models/post.rb
class Post < ApplicationRecord
  has_many :comments, as: :commentable
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

```ruby
# app/models/photo.rb
class Photo < ApplicationRecord
  has_many :comments, as: :commentable
end
```

3. 데이터베이스 마이그레이션

데이터베이스 스키마를 업데이트하려면 마이그레이션을 실행하세요.

```shell
rails db:migrate
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

다음은 다형성 연관을 사용하는 방법을 안내합니다. 이제 이를 애플리케이션에서 사용할 수 있습니다. 예시를 살펴보겠습니다:

- 포스트에 댓글 생성하기:

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
post = Post.create(title: '내 글', body: '이것이 글의 내용입니다.')
comment = post.comments.create(body: '이것은 글에 대한 댓글입니다.')
```

- 사진에 댓글 작성하기:

```js
photo = Photo.create(title: '내 사진', url: 'http://example.com/photo.jpg')
comment = photo.comments.create(body: '이것은 사진에 대한 댓글입니다.')
```

- 댓글 가져오기:

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

특정 게시물이나 사진에 대한 댓글을 가져올 수 있어요:

```js
post.comments # 게시물에 대한 모든 댓글을 반환합니다
photo.comments # 사진에 대한 모든 댓글을 반환합니다
```

- 댓글 가능한 객체에 접근하기:

댓글에서 부모 객체(게시물이나 사진)에도 접근할 수 있어요.

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
comment = Comment.first
comment.commentable # 댓글과 연관된 게시물 또는 사진을 반환합니다
```

## 다형성 연관의 장점

- 유연성: 다형성 연관은 단일 모델이 여러 다른 모델에 속할 수 있도록하여 유연성을 제공합니다. 이로 인해 여러 연관이 필요한 것을 줄이고 코드베이스를 간단하게 만듭니다.
- 중복 감소: 단일 연관을 사용함으로써 중복되는 코드와 데이터베이스 열을 피할 수 있어서 더 깔끔하고 유지보수가 쉬운 코드베이스를 얻을 수 있습니다.
- 유지보수가 쉬움: 관리해야 할 연관이 적어지면 코드베이스를 유지보수하고 업데이트하는 일이 더 쉬워집니다.

## 결론

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

루비 온 레일스의 다형성 연관은 코드베이스를 단순화하고 하나 이상의 유형의 엔티티에 속할 수 있는 모델 처리 시 더 큰 유연성을 제공할 수 있는 강력한 기능입니다. 이 글에서 안내된 단계를 따라가면 레일스 애플리케이션에서 다형성 연관을 효과적으로 구현하고 이점을 활용할 수 있습니다.