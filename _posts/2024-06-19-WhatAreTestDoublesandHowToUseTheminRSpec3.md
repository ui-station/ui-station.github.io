---
title: "테스트 더블이란 무엇이며 RSpec 3에서 어떻게 사용하는 지"
description: ""
coverImage: "/assets/img/2024-06-19-WhatAreTestDoublesandHowToUseTheminRSpec3_0.png"
date: 2024-06-19 22:21
ogImage: 
  url: /assets/img/2024-06-19-WhatAreTestDoublesandHowToUseTheminRSpec3_0.png
tag: Tech
originalTitle: "What Are Test Doubles and How To Use Them in RSpec 3"
link: "https://medium.com/@patrykrogedu/what-are-test-doubles-and-how-to-use-them-in-rspec-3-d990f1b91a36"
---



![Test Doubles](/assets/img/2024-06-19-WhatAreTestDoublesandHowToUseTheminRSpec3_0.png)

# 소프트웨어 개발에서의 테스트 더블 이해

소프트웨어에 대한 테스트를 작성할 때, 종종 실제 객체나 의존성의 동작을 시뮬레이션하기 위해 "테스트 더블" 또는 "모의 객체"를 만들어야 합니다. 각각 특정 목적을 위해 사용되는 다양한 종류의 테스트 더블이 있습니다.

# 테스트 더블의 종류


<div class="content-ad"></div>

테스트 더블은 사용 모드와 원본에 따라 분류할 수 있어요.

## 사용 모드

Stub: Stub은 사이드 이펙트를 발생시키지 않고 값을 반환하는 쿼리 메소드를 시뮬레이트하는 데 사용돼요. 미리 정의된 캐너드 응답을 반환합니다.

```js
# LMS 코스 모델을 위한 Stub
stub_course = double('Course', name: 'Ruby 입문', description: '루비 기초 학습')
```

<div class="content-ad"></div>

모의 객체: 모의 객체는 값을 반환하는 것보다 부작용을 수행하는 명령 메서드를 테스트할 때 유용합니다. 특정 메시지가 수신되었는지 확인하고, 예상한 메시지가 수신되지 않으면 오류를 발생시킵니다.

```js
# LMS 등록 서비스용 모의 객체
mock_enrollment_service = double('EnrollmentService')
expect(mock_enrollment_service).to receive(:enroll).with(user, course)
```

널 객체: 널 객체는 어떤 메시지에 대해 자신을 반환하는 친화적인 테스트 대역입니다. 여러 협력자가 있는 객체를 테스트할 때 유용합니다.

```js
# LMS 사용자용 널 객체
null_user = double('User').as_null_object
null_user.enroll_in_course(course)
```

<div class="content-ad"></div>

스파이: 스파이들은 받은 메시지를 기록하여 특정 메시지가 올바른 매개변수로 호출되었음을 확인할 수 있게 해줍니다.

```js
# LMS 알림 서비스용 스파이
spy_notification_service = spy('NotificationService')
spy_notification_service.send_notification(user, '환영합니다!')
expect(spy_notification_service).to have_received(:send_notification).with(user, '환영합니다!')
```

## 원점

테스트 더블의 사용 모드를 이해하는 것 외에도, 그 원점과 유형을 알아야 합니다. 테스트 더블은 순수한(pure), 부분적인(partial), 또는 확인(verify)할 수 있는 것으로, 각각 다른 목적을 제공합니다.

<div class="content-ad"></div>

순수 더블: 순수 더블은 RSpec과 같은 테스트 프레임워크에 의해 목적에 맞게 생성되며 해당 행동이 완전히 추가된 것으로 구성됩니다. 의존성을 전달할 수 있는 코드를 테스트하기 위해 유연하고 사용하기 쉽습니다.

```js
# LMS 강좌를 위한 순수 더블
pure_course = double('Course')
allow(pure_course).to receive(:name).and_return('루비 입문')
```

부분 더블: 때로는 테스트 중인 코드가 간단한 의존성 주입을 허용하지 않을 수 있습니다. 이러한 경우 기존 Ruby 객체에 모의(Mocking) 및 스텁(Stubbing) 행동을 추가하는 부분 더블을 사용할 수 있습니다.

```js
# 내장된 Ruby Time 클래스의 부분 더블
allow(Time).to receive(:now).and_return(Time.new(2023, 6, 1))
```

<div class="content-ad"></div>

더블 검증: 더블 검증은 테스트 더블과 실제 의존성이 동기화되지 않을 때 문제를 찾는 데 도움이 됩니다. 더블의 인터페이스를 실제 클래스나 객체에 기반하여 제한하여 메서드 변경을 감지합니다.

```js
# LMS 사용자 클래스의 더블 검증
user = instance_double('User')
allow(user).to receive(:enroll_in_course)
```

오버로드된 상수: 테스트 더블은 루비 상수를 바꾸거나 제거하여 테스트 기간 동안 환경을 제어할 수 있습니다.

```js
# 기본 등록 기간을 위한 상수 스텁
stub_const('LMS::DEFAULT_ENROLLMENT_PERIOD', 7)
```

<div class="content-ad"></div>

테스트 더블은 사용 모드와 원본을 모두 결합할 수 있어요. 예를 들어, 순수한 더블이 스텁으로 작동하거나 검증 더블이 스파이로 작동하는 경우가 있을 수 있어요.