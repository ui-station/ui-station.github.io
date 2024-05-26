---
title: "루비 필터를 Logstash에서 테스트하는 방법"
description: ""
coverImage: "/assets/img/2024-05-23-HowtotestaRubyfilterinLogstash_0.png"
date: 2024-05-23 12:49
ogImage: 
  url: /assets/img/2024-05-23-HowtotestaRubyfilterinLogstash_0.png
tag: Tech
originalTitle: "How to test a Ruby filter in Logstash"
link: "https://medium.com/@ingrid.jardillier/how-to-test-a-ruby-filter-in-logstash-804551b4b5e9"
---


이전 기사에서는 Logstash에서 코드를 공유하고 루비 필터에서 모듈을 만드는 방법을 보았습니다. 이 기사에서는 결과 이벤트가 예상대로인지 확인하기 위해 필터를 테스트하는 방법을 보여드릴 것입니다.

# 이전 코드에 대해

기억을 새기기 위해, 코드는 다음과 같았습니다:

```js
require './script/denormalized_by_prizes_utils.rb'

# `params`의 값은 로그스태시 구성에서 `script_params`에 전달된 해시의 값입니다.
def register(params)
    @keep_original_event = params["keep_original_event"]
end

# 필터 메소드는 이벤트를 수신하고 이벤트 목록을 반환해야 합니다.
# 이벤트를 삭제하면 반환 배열에 포함되지 않음을 의미합니다.
# 새 이벤트를 만드는 것은 반환된 배열에 LogStash::Event의 새 인스턴스만 추가하면 됩니다.
def filter(event)
    
    items = Array.new

    # 원래 이벤트를 유지하려면
    originalEvent = LogStash::Util::DenormalizationByPrizesHelper::getOriginalEvent(event, @keep_original_event);
    if not originalEvent.nil?
        items.push originalEvent
    end

    # 상품 항목을 가져옵니다 (정규화)
    prizes = LogStash::Util::DenormalizationByPrizesHelper::getPrizes(event);
    if prizes.nil?
        return items
    end
   
    # 복제 기본 이벤트 생성
    eventBase = LogStash::Util::DenormalizationByPrizesHelper::getEventBase(event);

    # 필요한 수정으로 상품 항목별 이벤트 생성
    prizes.each { |prize| 
        items.push LogStash::Util::DenormalizationByPrizesHelper::createEventForPrize(eventBase, prize);
    }

    return items;
end
```

<div class="content-ad"></div>

위 코드는 denormalized_by_prizes_utils.rb 파일 전체 내용입니다.

```rb
module LogStash::Util::DenormalizationByPrizesHelper
    include LogStash::Util::Loggable

    # 원본 이벤트 유지 여부 확인
    def self.getOriginalEvent(event, keepOriginalEvent)
        logger.debug('keepOriginalEvent is :' + keepOriginalEvent.to_s)
        if keepOriginalEvent.to_s == 'true'
            event.set('[@metadata][_index]', 'prizes-original');
            return event;
        end
        return nil;
    end

    # 상금 아이템 얻기 (정규화)
    def self.getPrizes(event)
        prizes = event.get("prize");
        if prizes.nil?
            logger.warn("이벤트에 상금이 없습니다: " + event.to_s)
        end
        return prizes;
    end

    # 클론 기본 이벤트 생성
    def self.getEventBase(event)
        eventBase = event.clone();
        eventBase.set('[@metadata][_index]', 'prizes-denormalized');
        eventBase.remove("prize");
        return eventBase;
    end

    # 필요한 수정과 함께 현재 상금 아이템을 위한 이벤트 클론 생성
    def self.createEventForPrize(eventBase, prize)
        eventPrize = eventBase.clone();
        # 각 상금 아이템 값을 상금 객체로 복사
        prize.each { |key,value|
            eventPrize.set("[prize][" + key + "]", value)
        }
        return eventPrize;
    end
end
```

## 일반 구문

이 섹션에서는 예상된 이벤트가 실제로 예상한 대로 생성되는지 확인하는 기능 테스트를 작성하는 방법을 보여줍니다.

<div class="content-ad"></div>

하나 이상의 테스트 케이스를 작성할 수 있으며 각 테스트 케이스마다 필요한 만큼의 테스트를 수행할 수 있습니다. 이러한 테스트는 루비 필터 파일 끝에 작성되어야 합니다. 즉, register/filter 함수를 포함하는 주 파일에 작성되어야 합니다.

필터 테스트는 다음과 같은 구문을 따라야 합니다:

```js
test "테스트 케이스 이름" do

    parameters do
    { 
        # 필터에 전달할 매개변수
    }
    end
    
    in_event { 
        # 필터 프로세스에 도착하는 이벤트
    }

    # expect 메서드를 사용한 테스트

end
```

# 저희 루비 필터에 테스트를 구현합니다.

<div class="content-ad"></div>

예제에서는 역정규화를 구현했기 때문에 테스트에서 원본 이벤트를 잘 역정규화했는지 여러 경우(원본 이벤트 유지 여부, 상금 목록에 상금 하나 또는 둘의 예)를 확인할 것입니다.

## 테스트 케이스

따라서, 아래에 제시된 네 가지 테스트 케이스가 필요합니다:

```js
test "Case 1: 이벤트에 상금 하나 / 원본 이벤트 유지하지 않음" do

    parameters do
    { 
        "keep_original_event" => false
    }
    end

    in_event { 
        { 
            "id"        => 1, 
            "firstname" => "Pierre", 
            "surname"   => "Curie",
            "gender"    => "male",
            "prize"     => [
                {
                    "year" => 1903,
                    "category" => "physics"
                }
            ]
        } 
    }

    # expect 메소드를 사용한 테스트

end

test "Case 2: 이벤트에 상금 하나 / 원본 이벤트 유지" do

    parameters do
    { 
        "keep_original_event" => true
    }
    end

    in_event { 
        { 
            "id"        => 1, 
            "firstname" => "Pierre", 
            "surname"   => "Curie",
            "gender"    => "male",
            "prize"     => [
                {
                    "year" => 1903,
                    "category" => "physics"
                }
            ]
        } 
    }

    # expect 메소드를 사용한 테스트

end

test "Case 3: 이벤트에 상금 둘 / 원본 이벤트 유지하지 않음" do

    parameters do
    { 
        "keep_original_event" => false
    }
    end

    in_event { 
        { 
            "id"        => 2, 
            "firstname" => "Marie", 
            "surname"   => "Curie",
            "gender"    => "female",
            "prize"     => [
                {
                    "year" => 1903,
                    "category" => "physics"
                },
                {
                    "year" => 1911,
                    "category" => "chemistry"
                }
            ]
        } 
    }

    # expect 메소드를 사용한 테스트

end

test "Case 4: 이벤트에 상금 둘 / 원본 이벤트 유지" do

    parameters do
    { 
        "keep_original_event" => true
    }
    end

    in_event { 
        { 
            "id"        => 2, 
            "firstname" => "Marie", 
            "surname"   => "Curie",
            "gender"    => "female",
            "prize"     => [
                {
                    "year" => 1903,
                    "category" => "physics"
                },
                {
                    "year" => 1911,
                    "category" => "chemistry"
                }
            ]
        } 
    }

    # expect 메소드를 사용한 테스트

end
```

<div class="content-ad"></div>

## 기능 테스트 구현

이 글에서는 더 복잡한 테스트 케이스(마지막)만 구현할 것입니다. 다른 것들에 대해서도 원리는 동일하지만 서로 다른 테스트 케이스를 테스트하므로 예상 결과는 같지 않을 것입니다.

그래서 마지막 테스트 케이스에서 다음을 확인할 것입니다:

- 원본이 변경 없이 출력에 포함되어 있는지 확인하기
- "prize" 배열의 각 항목마다 문서를 생성하므로 두 항목은 두 개의 문서를 생성해야 합니다
- 생성된 각 항목이 올바른 공통 필드와 올바른 상금 필드를 포함하고 있는지 확인하기
- 결과적으로 출력에는 3개의 이벤트가 있어야 하며, 각 이벤트는 별도의 인덱스에 있어야 합니다

<div class="content-ad"></div>

저희의 테스트는 다음과 같이 작성될 수 있어요:

```js
test "Case 4: two prizes in event / keep original event" do

    parameters do
    { 
        "keep_original_event" => true
    }
    end

    in_event { 
        { 
            "id"        => 2, 
            "firstname" => "Marie", 
            "surname"   => "Curie",
            "gender"    => "female",
            "prize"     => [
                {
                    "year" => 1903,
                    "category" => "physics"
                },
                {
                    "year" => 1911,
                    "category" => "chemistry"
                }
            ]
        } 
    }

    expect("Count of events") do |events|
        events.length == 3
    end

    expect("Each event has same shared fields") do |events|
        result = true
        events.each { |event|
            result &= event.get("[id]") == 2
            result &= event.get("[firstname]") == "Marie"
            result &= event.get("[surname]") == "Curie"
            result &= event.get("[gender]") == "female"
        }
        result
    end

    expect("Each event has good _index") do |events|  
        result = true
        result &= events[0].get("[@metadata][_index]") == "prizes-original"
        result &= events[1].get("[@metadata][_index]") == "prizes-denormalized"
        result &= events[2].get("[@metadata][_index]") == "prizes-denormalized"
        result
    end

    expect("Each event has good prize fields") do |events| 
        result = true 
        result &= events[0].get("[prize][0][year]") == 1903
        result &= events[0].get("[prize][0][category]") == "physics"
        result &= events[0].get("[prize][1][year]") == 1911
        result &= events[0].get("[prize][1][category]") == "chemistry"
        result &= events[1].get("[prize][year]") == 1903
        result &= events[1].get("[prize][category]") == "physics"
        result &= events[2].get("[prize][year]") == 1911
        result &= events[2].get("[prize][category]") == "chemistry"
        result
    end

end
```

여러 개의 어설션을 갖는 `expect` 메서드를 사용할 때는, `&&` 또는 `&=` 연산자를 사용하여 어설션 결과를 결합하는데 문법에 주의하세요.

우리의 테스트 케이스 구현이 준비되었어요. 모든 테스트 케이스는 Logstash 시작 시 실행되며 해당 파이프라인이 생성될 때 실행돼요. 실제로, Logstash는 Ruby 필터에 작성된 모든 테스트를 찾을 수 있어요. 그리고 Logstash 로그에서 모든 테스트 결과를 볼 수 있을 거예요.

<div class="content-ad"></div>

아래는 Markdown 형식으로 변경한 내용입니다.


![이미지1](/assets/img/2024-05-23-HowtotestaRubyfilterinLogstash_0.png)

![이미지2](/assets/img/2024-05-23-HowtotestaRubyfilterinLogstash_1.png)

테스트가 실패한 경우, 테스트 케이스 이름 및 필요한 모든 정보(매개변수, 입력 이벤트, 결과)를 명확히 확인할 수 있습니다. 최소한 하나의 테스트가 실패하면 연결된 파이프라인은 시작되지 않습니다.
