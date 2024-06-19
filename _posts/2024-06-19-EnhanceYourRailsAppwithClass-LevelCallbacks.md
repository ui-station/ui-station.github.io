---
title: "레일즈 앱에 클래스 레벨 콜백을 추가해보세요"
description: ""
coverImage: "/assets/img/2024-06-19-EnhanceYourRailsAppwithClass-LevelCallbacks_0.png"
date: 2024-06-19 22:14
ogImage: 
  url: /assets/img/2024-06-19-EnhanceYourRailsAppwithClass-LevelCallbacks_0.png
tag: Tech
originalTitle: "Enhance Your Rails App with Class-Level Callbacks"
link: "https://medium.com/@abhirampai/how-to-use-callback-directly-on-the-class-3887af3d1e88"
---


<img src="/assets/img/2024-06-19-EnhanceYourRailsAppwithClass-LevelCallbacks_0.png" />

# 소개

지난 주에는 ActiveJob의 결과를 처리해야 하는 기능을 작업했습니다. 해당 작업은 젬 내에 존재했고, 호스트 애플리케이션의 클래스에서 메소드를 호출하기 위해 젬을 수정해야 했습니다. 이 상황은 클래스 인스턴스와는 독립적으로 처리되어야 했기 때문에 클래스 수준의 콜백이 필요했습니다.

# Rails에서 콜백 이해하기

<div class="content-ad"></div>

콜백은 객체의 라이프사이클 중 특정 시점에 호출되는 메서드입니다. Rails에서는 콜백이 널리 사용되어 객체의 생성, 업데이트 및 삭제 중에 코드를 자동으로 실행합니다. 콜백에 대해 더 알고 싶다면 Rails 가이드의 콜백 섹션을 참조해보세요. 

# 예시 코드

## ActiveJob의 젬

여기에는 호스트 애플리케이션의 클래스에서 메서드를 호출해야 하는 젬 내의 작업이 있습니다:

<div class="content-ad"></div>

```markdown
# ActiveJob in the gem
class MyJob < ActiveJob::Base
  def perform
    result = # perform the job and get the result
    if ModelName.respond_to?(:after_result, true)
      ModelName.after_result { result }
    end
  end
end
```

이 스니펫에서는 호스트 애플리케이션의 ModelName 클래스가 after_result 클래스 메소드를 갖고 있는지 확인합니다. 해당 메소드가 있다면 블록을 사용하여 작업 결과를 이 메소드로 전달합니다.

## Host Application Class Method

호스트 애플리케이션에서는 클래스 내에 after_result 메소드를 정의합니다.
```

<div class="content-ad"></div>

```markdown
# 호스트 응용프로그램의 클래스
Class ModelName
  def self.after_result
    result = yield if block_given?
    process(result) if result.present?
  end

  def self.process(result)
    # 결과를 처리합니다
  end
end
```

여기서 yield는 작업 결과를 after_result 메서드로 전달하며, 결과가 있는 경우 처리합니다.

## 인스턴스 메서드 콜백

인스턴스 메서드와 Rails 콜백을 사용한다면 조금 다르게 보일 것입니다. define_callbacks와 set_callback을 사용하여 클래스 인스턴스에서 콜백을 정의할 수 있습니다.
```

<div class="content-ad"></div>

```ruby
# 호스트 응용 프로그램의 클래스
class ModelName
  define_callbacks :result
  set_callback :result, :after, :after_result, if: -> { respond_to?(:after_result, true) }

  def after_result
    result = yield if block_given?
    process(result) if result.present?
  end

  def process(result)
    # 결과 처리
  end
end
```

이 예에서 after_result는 인스턴스 메서드입니다. :result 콜백을 정의하고, :result 콜백이 트리거된 후에 after_result 메서드가 실행되어야 함을 지정합니다.

## 젬 내 수정된 ActiveJob

인스턴스 메서드 콜백을 사용하려면 작업을 다음과 같이 수정하십시오:```

<div class="content-ad"></div>

```js
# ActiveJob in the gem
class MyJob < ActiveJob::Base
  def perform(instance)
    result = # perform the job and get the result
    if instance.respond_to?(:after_result, true)
      instance.run_callbacks(:result) { result }
    end
  end
end
```

여기서 instance는 호스트 애플리케이션의 ModelName의 인스턴스입니다. after_result에 응답하는지 확인하고 :result 콜백을 실행합니다.

# 결론

클래스 메서드를 콜백으로 사용하는 방법과 인스턴스 메서드에 대해 Rails의 set_callback을 활용하는 방법을 다루었습니다. 사용 사례에 따라 클래스 수준 또는 인스턴스 수준 메서드가 필요한 경우 적절한 콜백 메커니즘을 구현할 수 있습니다.