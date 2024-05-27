---
title: "널 객체 패턴 - 루비 스타일"
description: ""
coverImage: "/assets/img/2024-05-27-TheNullObjectPatternTheRubyway_0.png"
date: 2024-05-27 16:07
ogImage: 
  url: /assets/img/2024-05-27-TheNullObjectPatternTheRubyway_0.png
tag: Tech
originalTitle: "The Null Object Pattern — The Ruby way"
link: "https://medium.com/kode-art/the-null-object-pattern-the-ruby-way-0749ce7cb7c2"
---


```markdown
![image](/assets/img/2024-05-27-TheNullObjectPatternTheRubyway_0.png)

# 소개

당신은 아마도 위의 기사 이미지에서 보듯 유사한 오류를 만나본 적이 있을 것입니다. 뭔가 다음과 같은 내용이 포함되어 있죠: "undefined method 'username' for nil:NilClass".

이 문구는 그저 'username' 메서드를 누락된 객체에 적용할 수 없다는 것을 의미합니다. 즉, 당신이 메서드 이름을 잘못 입력했거나 데이터베이스에서 제공된 ID로 찾는 객체가 삭제되었을 수 있습니다. 따라서, 이곳이 바로 Null 객체 패턴을 사용하여 이러한 공포스러운 화면을 피할 수 있는 정확한 장소입니다.
```

<div class="content-ad"></div>

널 객체 패턴은 코드에서 널 체크를 제거하는 데 도움이 되는 행동 디자인 패턴입니다. 이는 Nil 예외의 피해를 최소화하고 코드를 더 선언적이고 DRY하게 만들어주는 매우 유용한 도구입니다.

간단히 말해서, 이것은 ActiveRecord 객체에 대해 끝없이 'present?' 또는 'try?'를 호출하는 것을 막아주는 훌륭한 해킹입니다.

널 객체는 실제 객체의 부재 상황에서 실제 객체를 대신하여 나타낼 수 있으므로, 소비 코드가 실제 객체의 부재를 명시적으로 처리하고 널 체크를 수행할 필요가 없습니다.

그럼 시작해 볼까요...

<div class="content-ad"></div>

# 루비에서 널 객체 패턴 구현

널 객체 패턴을 소개하는 간단하면서도 유익한 방법은 일반적인 시나리오를 고려해보는 것입니다. 사용자의 사용자 이름, 이메일 또는 이름을 출력하는 경우를 생각해보겠습니다. 제공된 ID를 가진 사용자가 이미 데이터베이스에서 삭제되었을 때의 상황을 가정해봅시다.

```js
# 케이스 1
@user_1 = User.find_by(email: "deleted.user@mymail.com")
@user_1.first_name

# 케이스 2
@user_2 = User.find(34)
@user_2.username
```

그리고 이러한 상황에서 여러분이 받게 되는 것은 이 오류 메시지뿐입니다:

<div class="content-ad"></div>

```js
# Case 1
nil:NilClass에 대한 nil을 위한 'first_name' 메소드가 정의되지 않았습니다.

# Case 2
nil:NilClass에 대한 nil을 위한 'username' 메소드가 정의되지 않았습니다.
```

물론, 세상이 끝나는 것은 아닙니다. 오류를 피하는 몇 가지 방법이 있습니다. 여기에 있습니다:

```js
# 해결책 1 / 'try' 메소드 사용.
@user.try(:first_name)


# 해결책 2 / 조건 연산자 사용.
if @user.exists?
 @user.first_name
end
```

이러한 종류의 확인 사항은 쉽게 여러분의 코드를 확인 사항과 조건 연산자의 혼란으로 바꿀 수 있습니다. 우리가 한 것과 대조적으로 한 번만 하고 모든 곳에서 사용할 수 있는 더 나은 방법이 있습니다. 이는 코드를 보기 좋게 만들어줍니다.```

<div class="content-ad"></div>

데이터베이스에 없는 사용자 객체에 대체될 'DeletedUser' 클래스를 정의해 봅시다. 이 클래스는 실제 사용자 객체를 흉내 내도록 동일한 일반 속성/메서드를 갖게 될 것입니다.

```js
class DeletedUser
  def first_name
   "알 수 없는 이름"
  end

  def last_name
    "알 수 없는 성"
  end

  def username
    "알 수 없는 사용자 이름"
  end

  def email
    "이미 삭제된 사용자입니다. 이메일: already.deleted.user@mail.com"
  end
end
```

이전 코드에서 사용자로부터 기대한 것을 얻지 못했던 것을 기억하나요? 사용자가 우리의 요청을 따르지 않았고, 이제 그것을 다스리는 방법을 찾았습니다. 사용자를 찾지 못했다면, 'DeletedUser' 객체의 인스턴스로 ' @user_1` 및 '@user_2' 변수를 초기화합니다.

```js
# 케이스 1
@user_1 = User.find_by(email: "deleted@user.com") || DeletedUser.new
@user_1.first_name #=> "알 수 없는 이름"
@user_1.email #=> "이미 삭제된 사용자입니다. 이메일: already.deleted.user@mail.com"

# 케이스 2
@user_2 = User.find(34) || DeletedUser.new
@user_2.username #=> "알 수 없는 사용자 이름"
```

<div class="content-ad"></div>

이렇게 하면 객체가 항상 예상한 메서드에 응답할 것이라는 점을 확신할 수 있어요.

# 장단점

Null 객체 패턴은 다음 시나리오에서 특히 유용해요:

- 기본 값: 누락된 객체에 대한 기본 값이나 동작을 제공하고 싶을 때.

<div class="content-ad"></div>

조심하세요, 때로는 이 패턴이 다음과 같습니다:

- 속일 수 있음: 오류/버그를 일반적인 프로그램 실행으로 나타낼 수 있음.

# 결론

널 객체 패턴은 누락된 객체를 깨끗하고 유지보수 가능한 방식으로 처리하기 위한 강력한 도구입니다. 실제 객체의 동작을 흉내 내는 널 객체를 생성함으로써 코드를 단순화하고 테스트를 쉽게 할 수 있습니다. 필요한 객체가 없을 때 우아하게 처리해야 할 필요가 있을 때 이 패턴을 고려할 수 있습니다.

<div class="content-ad"></div>

널 객체 패턴은 데이터베이스 쿼리에서 데이터를 반환하지 않는 경우 외에도 유용하게 활용할 수 있습니다.

그럼, 널 객체 패턴에 대한 내용이었습니다. 여러분의 코드를 더 예쁘고 의미있게 만들기 위해 어디선가 활용해보세요.

안전하고 건강하게 지내세요!

![이미지](/assets/img/2024-05-27-TheNullObjectPatternTheRubyway_1.png)