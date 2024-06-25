---
title: "루비 온 레일즈에서의 유효성 검사를 위한 모범 사례들"
description: ""
coverImage: "/assets/img/2024-05-20-BestPracticesforValidationsinRubyonRails_0.png"
date: 2024-05-20 15:52
ogImage:
  url: /assets/img/2024-05-20-BestPracticesforValidationsinRubyonRails_0.png
tag: Tech
originalTitle: "Best Practices for Validations in Ruby on Rails"
link: "https://medium.com/@patrickkarsh/best-practices-for-validations-in-ruby-on-rails-b7bf3f1e15cc"
---

![Validation](/assets/img/2024-05-20-BestPracticesforValidationsinRubyonRails_0.png)

유효성 검사는 웹 응용 프로그램 개발의 중요한 측면으로, 사용자가 입력한 데이터가 처리되거나 저장되기 전에 특정 기준을 충족시키도록 하는 것을 보장합니다. 루비 온 레일즈(Ruby on Rails)에서는 ActiveRecord의 강력한 내장 유효성 검사 메서드 덕분에 유효성 검사를 구현하기가 간단합니다. 이 기사에서는 레일즈 애플리케이션에서 유효성 검사를 구현하는 최선의 방법에 대해 안내해 드리겠습니다.

# 유효성 검사의 중요성

유효성 검사는 응용 프로그램의 데이터 무결성과 일관성을 보장합니다. 데이터베이스에 잘못된 데이터가 저장되는 것을 방지하여 응용 프로그램 오류, 보안 취약점 및 기타 문제가 발생하는 것을 방지합니다. 모델 수준에서 데이터를 유효성 검사함으로써 데이터 무결성 논리를 집중시킴으로써, 응용 프로그램을 유지 보수 가능하고 안전하게 만듭니다.

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

# 일반적인 유효성 검증

루비 온 레일즈는 모델을 유효성 검증하는 데 사용할 수 있는 다양한 내장 유효성 검증 도우미를 제공합니다. 여기에는 일반적으로 사용되는 몇 가지가 있습니다:

1. **Presence(존재)**: 필드가 비어있지 않은지 확인합니다.

```js
validates :name, presence: true
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

독특성: 값이 테이블 전체에서 고유한지 확인합니다.

```js
validates :email, uniqueness: true
```

형식: 정규 표현식을 사용하여 필드가 특정 형식에 일치하는지 확인합니다.

```js
validates :email, format: { with: URI::MailTo::EMAIL_REGEXP }
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

숫자 정합성: 필드가 숫자임을 보장하고 숫자 제약 조건을 유효성 검사할 수도 있습니다.

```js
validates :age, numericality: { only_integer: true, greater_than: 0 }
```

# 유효성 검사 구현을 위한 모범 사례

## 내장된 유효성 검사기 사용

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

가능한 한 Rails의 내장 유효성 검사기를 활용하세요. 이들은 테스트를 거친 상태이며 다양한 일반적인 유효성 검사 요구사항을 다룹니다. 내장 유효성 검사기를 사용하면 코드가 더 읽기 쉽고 유지보수하기 쉬워집니다.

```js
class User < ApplicationRecord
  validates :email, presence: true, uniqueness: true, format: { with: URI::MailTo::EMAIL_REGEXP }
end
```

## 사용자 정의 유효성 검사

내장 도우미로 다루기 어려운 복잡한 유효성 검사 로직이 필요한 경우, 사용자 정의 유효성 검사 메서드를 작성할 수 있습니다.

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
class User < ApplicationRecord
  validate :password_complexity

  def password_complexity
    return if password.blank? || password =~ /(?=.*\d)(?=.*[a-z])(?=.*[A-Z])/

    errors.add :password, 'must include at least one lowercase letter, one uppercase letter, and one digit'
  end
end
```

## 조건부 유효성 검사

가끔 필드를 특정 조건에서만 유효성을 검사해야 할 때가 있습니다. Rails에서는 :if와 :unless 옵션을 통해 이를 쉽게 할 수 있습니다.

```ruby
class User < ApplicationRecord
  validates :ssn, presence: true, if: :adult?

  def adult?
    age >= 18
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

## 유효성 검사 및 데이터베이스 제약조건

ActiveRecord 유효성 검사는 강력하지만 애플리케이션 수준에서만 실행됩니다. 중요한 유효성 검사의 경우, 데이터베이스 수준에서도 제약조건을 강제하세요. 이렇게 하면 두 겹의 보호막이 제공됩니다.

```js
# 마이그레이션
add_index :users, :email, unique: true
```

## 지나치게 유효성 검사 피하기

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

너무 많은 유효성 검사를 하지 않도록 주의해주세요. 지나치게 엄격한 유효성 검사는 사용자에게 답답한 경험을 줄 수 있습니다. 필요한 유효성 검사를 실행하고 사용자에게 의미 있는 피드백을 제공해 주세요.

## 오류 메시지

사용자 친화적인 오류 메시지를 표시해 주세요. 기본 오류 메시지는 최종 사용자에게는 너무 기술적일 수 있습니다.

```js
class User < ApplicationRecord
  validates :username, presence: { message: "빈 칸일 수 없습니다" }
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

## 유효성 검사 테스트

언제나 유효성 검사에 대한 테스트를 작성하세요. 이렇게 함으로써 해당 기능이 예상대로 작동하는지 확인할 수 있으며, 미래의 회귀 사항을 방지할 수 있습니다.

```ruby
# spec/models/user_spec.rb
require 'rails_helper'

RSpec.describe User, type: :model do
  it '유효한 이메일로 유효성이 유효해야 합니다' do
    user = User.new(email: 'user@example.com')
    expect(user).to be_valid
  end

  it '이메일이 없으면 유효하지 않아야 합니다' do
    user = User.new(email: nil)
    user.valid?
    expect(user.errors[:email]).to include("can't be blank")
  end

  it '중복된 이메일로는 유효하지 않아야 합니다' do
    User.create!(email: 'user@example.com')
    user = User.new(email: 'user@example.com')
    user.valid?
    expect(user.errors[:email]).to include('has already been taken')
  end
end
```

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

테이블 태그를 Markdown 형식으로 변경하세요.
