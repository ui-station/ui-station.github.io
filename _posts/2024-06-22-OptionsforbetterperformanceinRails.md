---
title: "Rails 성능 향상을 위한 7가지 옵션"
description: ""
coverImage: "/assets/img/2024-06-22-OptionsforbetterperformanceinRails_0.png"
date: 2024-06-22 22:21
ogImage: 
  url: /assets/img/2024-06-22-OptionsforbetterperformanceinRails_0.png
tag: Tech
originalTitle: "Options for better performance in Rails."
link: "https://medium.com/@danielmutubait/options-for-better-performance-in-rails-3cbc9d2e8aa4"
---


# 소개: 동기부여

소프트웨어 엔지니어링에서 효율성은 빠른 실행만이 아니라 리소스를 최대화하고 사용자 경험을 향상하는 해결책을 개발하는 것을 의미합니다. 우아함으로 유명한 Ruby에서는 메소드의 선택이 성능에 깊은 영향을 미칠 수 있습니다. 이 글은 일반적인 Ruby 및 Rails 메소드를 비교하여 메모리와 실행 시간 측면에서 어떤 것이 비교적 더 나은 성능을 제공하는지 살펴봅니다.

![이미지](/assets/img/2024-06-22-OptionsforbetterperformanceinRails_0.png)

# exists? vs present? vs any?:

<div class="content-ad"></div>

ActiveRecord에서 present? 또는 any?과 같은 메서드를 사용하면 레코드를 메모리로 로드하여 일치하는 레코드가 있는지 확인할 수 있습니다. 이는 특히 대규모 데이터셋의 경우 비효율적일 수 있습니다.

```js
User.where(email: 'example@gmail.com').present?
User.where(email: 'example@gmail.com').any?
```

exists?의 효율성: exists? 메서드는 데이터를로드하지 않고 레코드의 존재 여부 만을 확인하도록 최적화되어 있어 훨씬 더 빠릅니다.

```js
User.where(email: 'example@gmail.com').exists?
```

<div class="content-ad"></div>

# update_all 대 update

업데이트의 오버헤드: 개별 레코드에 대해 업데이트 메서드를 사용하면 여러 개의 데이터베이스 쿼리가 발생합니다.

```js
users.each { |user| user.update(active: true) }
```

update_all의 효율성: update_all 메서드는 조건과 일치하는 모든 레코드를 업데이트하는 데 단일 쿼리를 수행하여 성능을 크게 향상시킵니다. 단, 여기에 주의해야 할 점은 해당 열이 인덱싱되어 있는지 확인하기 위해 신중히 WHERE 절을 사용하여 넓은 범위를 설정해야 합니다.

<div class="content-ad"></div>

```js
User.where(active: false).update_all(active: true)
```

# includes vs joins vs preload

## Eager Loading Associations

- N+1 쿼리 문제: 조인을 사용하면 각 관련 레코드가 개별적으로 쿼리될 수 있는 N+1 쿼리 문제가 발생할 수 있습니다.
- User.joins(:posts)는 여기서 posts 테이블을 로드하지 않습니다. 포스트를 가진 사용자만을로드합니다. 포스트는 .each를 수행하고 사용자의 포스트를 가져올 때 로드됩니다. 데이터베이스에 다시 쿼리를 실행하게 됩니다. 이것이 고전적인 n +1 쿼리 문제입니다.

<div class="content-ad"></div>

```js
# 비효율적
User.joins(:posts).each { |user| user.posts.each { |post| puts post.title } }
```

include의 효율성: include 메서드는 관련 레코드를 최소한의 쿼리로 가져오는 것을 목적으로한다. include는 데이터를 가져오기 위해 preload와 eager_load 두 가지 방법을 사용합니다. preload는 기본 테이블과 연관 테이블을 가져오기 위해 두 개의 쿼리가 시작됩니다. eager_load는 왼쪽 조인을 사용하여 기본 및 보조 테이블 모두를 하나의 쿼리로 가져옵니다.

```js
# 효율적
User.includes(:posts).each { |user| user.posts.each { |post| puts post.title } }
```

# count vs size vs length


<div class="content-ad"></div>

## 레코드 개수 세기

- 길이의 오버헤드: 길이를 사용하는 것은 모든 레코드를 메모리로 로드하기 때문에 비효율적일 수 있습니다.

```js
# 비효율적
User.all.length
```

카운트의 효율성: count 메서드는 SELECT COUNT(*) 쿼리를 수행하여 대규모 데이터셋에 효율적입니다.

<div class="content-ad"></div>

```js
User.count
```

# find vs find_each:

수천 개의 레코드를 처리할 때, find를 사용하면 처리를 시작하기 전에 모든 레코드를 로드해야하기 때문에 지나칠 정도로 비효율적입니다. 한 번에 10,000개의 레코드를 처리하는 경우 find를 사용할 때 약 500ms가 소요되고 많은 메모리가 사용되어 효율성이 떨어지는 것을 명확히 나타냅니다.

```js
User.find(1..10000).each do |user|
  # 일부 처리 작업
end
```

<div class="content-ad"></div>

# find_each

`find_each`은 내가 배고플 때 삼키는 만큼만 씹겠다고 하는 것과 비슷해요.

```ruby
User.find_each(batch_size: 1000) do |user|
  # 어떤 처리
end
```

- 메모리 사용량: 감소

<div class="content-ad"></div>

# select vs pluck

# Using select

이 문은 사용자 테이블에서 특정 열 (id, name, email)을 선택하고 ActiveRecord 객체로로드합니다. 그런 다음 each 블록이 이러한 객체를 반복 처리합니다. 특히 그 객체 각각에서 무언가를 확인하려는 경우에 유용합니다.

```js
User.select(:id, :name, :email).each { |user| ... }
```

<div class="content-ad"></div>

# pluck 사용하기

이 문은 데이터베이스에서 지정된 열만 직접 검색합니다. 반환값은 값 배열(또는 다수의 열을 pluck하는 경우 값 배열의 배열)이며, Active Record 객체가 아닙니다. 이는 메모리에 덜 영향을 미칩니다. 다만, 결과는 active record 테이블에 대해 아무것도 모르는 값 배열일 뿐입니다.

```js
User.pluck(:id, :name, :email)
```

# where vs find_by:

<div class="content-ad"></div>

## where를 사용하기

where는 Rails에서 강력한 쿼리 메서드로, 여러 레코드를 반환할 수 있습니다. 여러 결과를 처리해야 하는 애플리케이션 로직에서 이상적입니다. where로 여러 레코드를 가져 올 때, where 절에 전달된 필드가 인덱싱되어 있지 않은 경우 전체 테이블 스캔을 수행합니다. 인덱스를 사용하면 검색 범위가 제한되어 index scan을 수행합니다. 일부 상황에서는 이 메서드를 사용해야 할 수도 있지만, 최종적으로 .first를 수행한다면 이상적인 방법이 아닙니다.

```js
User.where(name: 'John').first
```

find_by 사용하기

<div class="content-ad"></div>

특정 기준과 일치하는 첫 번째 레코드만 필요할 때는 find_by를 사용하는 것이 더 빠릅니다. 일치하는 항목을 찾자마자 쿼리가 중단되어 시간과 자원을 절약합니다. 필터 열이 인덱싱되어 있는 경우 런타임이 더 향상됩니다.

```js
User.find_by(name: 'John')
```

# delete_all vs destroy_all

# delete_all

<div class="content-ad"></div>

delete_all은 데이터베이스에 직접 레코드를 로드하지 않고 빠른 삭제를 위한 메서드입니다. 빠르지만 콜백을 건너뜁니다. 빠르지만 결정적이며 콜백이 없습니다. 콜백을 실행하지 않으면 외래 키 참조가 있는 레코드에서 오류가 발생할 수 있습니다.

```js
User.where(active: false).delete_all
```

destroy_all

콜백을 실행하면서 레코드를 삭제하려면 destroy_all이 필요합니다. 데이터 무결성을 유지하지만 성능에 비용이 발생합니다. 더 많은 시간이 소요되지만 체계적이며 필요한 콜백을 실행합니다.

<div class="content-ad"></div>

```js
User.where(active: false).destroy_all
```

이 글이 도움이 되었으면 좋겠어요.

즐거운 코딩하세요!