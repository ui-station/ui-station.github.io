---
title: "백엔드 면접 질문  믹스인이란 무엇인가요"
description: ""
coverImage: "/assets/img/2024-06-22-BackendInterviewQuestionWhatIsAMixin_0.png"
date: 2024-06-22 22:53
ogImage: 
  url: /assets/img/2024-06-22-BackendInterviewQuestionWhatIsAMixin_0.png
tag: Tech
originalTitle: "Backend Interview Question — What Is A Mixin?"
link: "https://medium.com/gitconnected/backend-interview-question-what-is-a-mixin-3e9922838635"
---


Interviewer가 데이터 구조 및 알고리즘 질문에 대한 면접을 마치면 임의로 짧은 기술 질문을 할 수 있습니다. 

예를 들어, 믹스인(Mixin)이 무엇이며 왜 사용하는지 설명해보세요.

그리고 그 외에도 많은 다른 질문들이 있습니다. 이 질문들의 상당수에 대해 괜찮은 답변을 준비하는 것이 중요합니다.

<div class="content-ad"></div>

# Mixin이란 무엇인가요?

- 기본적으로, Mixin은 간단한 클래스입니다.
- 사용자 정의 클래스에 기능을 제공하는 클래스입니다.

```js
class BarkMixin:
    def bark(self):
        print('woof')

class Animal:
    ... 

class Dog(Animal, BarkMixin):
    ... 
```

^ 여기서 BarkMixin은 해당 클래스에서 상속 받은 클래스에 'bark' 메소드를 제공합니다.

<div class="content-ad"></div>

BarkMixin이 Dog의 부모 클래스로 사용되는 것이 아니라, 단순히 Dog에게 짖는 메서드를 제공하도록 설계되었습니다.

# 믹스인은 섞어서 사용할 수 있습니다

```js
class BarkMixin:
    def bark(self):
        print('woof')

class MeowMixin:
    def meow(self):
        print('meow')

class SqueakMixin:
    def squeak(self):
        print('squeak')
```

^ 각 믹스인에는 일반적으로 소수의 함수가 포함되어 있습니다. 이렇게 하면 조합하여 더 편리하게 사용할 수 있습니다.

<div class="content-ad"></div>

```javascript
class Monster1(BarkMixin, MeowMixin):
    pass

m = Monster1()

m.bark()    # woo
m.meow()    # 야옹
```

가령 Monster1이 짖고 야옹할 수 있다고 합시다. 내부에 짖고 야옹하는 메서드를 정의하는 대신, Monster1을 BarkMixin과 MeowMixin에서 상속받도록 만들면 됩니다.

```javascript
class Monster2(BarkMixin, MeowMixin, SqueakMixin):
    pass

m = Monster2()

m.bark()    # woo
m.meow()    # 야옹
m.squeak()  # squeak
```

그리고 Monster2가 짖고 야옹하며 삑삑 소리를 낼 수 있게 하려면 BarkMixin, MeowMixin, SqueakMixin에서 상속받으면 됩니다.

<div class="content-ad"></div>

# 그런데, 클래스 내에서 이들을 정의하는 것이 어떨까요???

```js
class 짖기포함:
    def 짖다(self):
        print('왈왈')

class 고양이울음포함:
    def 고양이울음(self):
        print('야옹')

class 삑삑포함:
    def 삑삑(self):
        print('삐익')
```

다음과 같은 몬스터를 만들고 싶다고 가정해봅시다:

- 몬스터1은 짖을 수 있고 야옹거릴 수 있습니다.
- 몬스터2는 짖고 야옹하며 삑삑을 할 수 있습니다.
- 몬스터3은 야옹하며 삑삑을 할 수 있습니다.
- 몬스터4는 삑삑만 할 수 있습니다.
- 몬스터5는 짖거나 삑삑을 할 수 있습니다.

<div class="content-ad"></div>

천천히 하나씩 mixins을 사용해보죠.

```js
class Monster1:
    def bark(self):
        print('woof')

    def meow(self): 
        print('meow')

class Monster2:
    def bark(self):
        print('woof')

    def meow(self):
        print('meow')

    def squeak(self):
        print('squeak')

class Monster3:
    def meow(self):
        print('meow')

    def squeak(self):
        print('squeak')

class Monster4:
    def squeak(self):
        print('squeak')

class Monster5:
    def bark(self):
        print('woof')

    def squeak(self):
        print('squeak')
```

우와, 말하게 해서 쓰기도 귀찮고, 코드가 중복되어서 짜증났죠! 이제 mixins을 사용해봅시다:

```js
class BarkMixin:
    def bark(self):
        print('woof')

class MeowMixin:
    def meow(self):
        print('meow')

class SqueakMixin:
    def squeak(self):
        print('squeak')

class Monster1(BarkMixin, MeowMixin): pass

class Monster2(BarkMixin, MeowMixin, SqueakMixin): pass

class Monster3(MeowMixin, SqueakMixin): pass

class Monster4(SqueakMixin): pass

class Monster5(BarkMixin, SqueakMixin): pass
```

<div class="content-ad"></div>

훨씬 깔끔하고, 코드 반복이 훨씬 적으며, 읽기도 훨씬 쉬워요

# 결론

만약 당신이 다음과 같은 기능이 있다면:

- 작고 모듈식이고
- 상당 수의 클래스들이 필요로 하는
- 혼합 및 매칭이 필요한

<div class="content-ad"></div>

테이블 태그를 Markdown 형식으로 변경해 보시는 것을 권장해 드립니다. 이 설명이 명확하고 이해하기 쉬웠으면 좋겠네요. 

# 만약 제가 창작자로서 지원받길 원하신다면

- 이 이야기에 대해 50번 박수를 보내주세요.
- 여러분의 생각을 말씀해 주는 댓글을 남겨주세요.
- 이야기에서 가장 좋아하는 부분을 강조해 주세요.

감사합니다! 이런 작은 조치들이 큰 도움이 되고 정말 감사히 받아들입니다!

<div class="content-ad"></div>

YouTube: https://www.youtube.com/@zlliu246

LinkedIn: https://www.linkedin.com/in/zlliu/

My Ebooks: https://zlliu.co/ebooks