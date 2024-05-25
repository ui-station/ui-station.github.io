---
title: "Logstash에서 Ruby 코드를 공유하는 방법"
description: ""
coverImage: "/assets/img/2024-05-23-HowtoshareRubycodeinLogstash_0.png"
date: 2024-05-23 12:44
ogImage: 
  url: /assets/img/2024-05-23-HowtoshareRubycodeinLogstash_0.png
tag: Tech
originalTitle: "How to share Ruby code in Logstash"
link: "https://medium.com/@ingrid.jardillier/how-to-share-ruby-code-in-logstash-8d772ee42569"
---


이전 기사에서는 루비 필터를 사용하여 문서를 비정규화하는 방법을 살펴보았습니다. 이 기사에서는 코드를 개선하고 필터간에 코드를 공유하는 방법을 보여드리겠습니다.

## 이전 코드에 대해

기억하시나요? 이전 코드는 다음과 같았습니다:

```js
# 'params'의 값은 로그스태시 구성에서 'script_params'로 전달된 해시의 값입니다.
def register(params)
    @keep_original_event = params["keep_original_event"]
end

# 필터 메서드는 이벤트를 받아들이고 이벤트 목록을 반환하여야 합니다.
# 이벤트를 삭제하면 반환 배열에 포함되지 않습니다.
# 새로운 이벤트를 만들려면 반환된 배열에 LogStash::Event의 새 인스턴스를 추가하기만 하면 됩니다.
def filter(event)

    items = Array.new

    # 원본 이벤트를 유지하도록 요청되었는지 확인
    logger.debug('keep_original_event is :' + @keep_original_event.to_s)

    if @keep_original_event.to_s == 'true'
        event.set('[@metadata][_index]', 'prizes-original');
        items.push event
    end

    # 상금 아이템 가져오기 (비정규화)
    prizes = event.get("prize");
    if prizes.nil?
        logger.warn("No prizes for event " + event.to_s)
        return items
    end

    # 복제된 기본 이벤트 생성
    eventBase = event.clone();
    eventBase.set('[@metadata][_index]', 'prizes-denormalized');
    eventBase.remove("prize");

    # 각 상금 아이템별로 필요한 수정과 함께 이벤트 생성
    prizes.each { |prize|
        eventPrize = eventBase.clone();

        # 각 상금 아이템 값을 상금 객체로 복사
        prize.each { |key,value|
            eventPrize.set("[prize][" + key + "]", value)
        }

        items.push eventPrize
    }

    return items
end
```

<div class="content-ad"></div>

보시다시피, 우리에게는 매개변수를 설명하는 register 함수와 필터 기능을 구현하는 다른 함수가 2개뿐입니다. 그러나 기능 전체를 한 방법에 구현하는 것은 가독성, 유지 관리 가능성, 테스트 가능성 등 여러 가지 이유로 최선의 선택이 아닙니다.

# 루비 코드 공유

코드를 공유하는 첫 번째 방법은 다른 루비 파일에 일부 함수를 외부화하고 이러한 함수를 우리의 루비 필터에서 호출하는 것입니다.

예를 들어, 우리는 일부 코드 조각을 간단한 함수로 외부화할 수 있습니다:

<div class="content-ad"></div>

- 원본 이벤트를 얻기 위한 함수(원하면 현재 이벤트를 유지하려면)
- 이벤트로부터 상품 배열을 얻기 위한 함수
- 각 상품을 위해 복제되는 이벤트 베이스를 구성하는 함수
- 각 상품마다 이벤트를 생성하는 함수

```js
# 필요 시 원본 이벤트 유지
def getOriginalEvent(event)
    logger.debug('keep_original_event is :' + @keep_original_event.to_s)
    if @keep_original_event.to_s == 'true'
        event.set('[@metadata][_index]', 'prizes-original');
        return event;
    end
    return nil;
end

# 상품 항목 가져오기 (정규화)
def getPrizes(event)
    prizes = event.get("prize");
    if prizes.nil?
        logger.warn("No prizes for event " + event.to_s)
    end
    return prizes;
end

# 복제된 베이스 이벤트 생성
def getEventBase(event)
    eventBase = event.clone();
    eventBase.set('[@metadata][_index]', 'prizes-denormalized');
    eventBase.remove("prize");
    return eventBase;
end

# 필요한 수정을 수행한 현재 상품 항목 복제 이벤트 생성
def createEventForPrize(eventBase, prize)
    eventPrize = eventBase.clone();
    # 각 상품 항목 값을 상품 객체로 복사
    prize.each { |key,value|
        eventPrize.set("[prize][" + key + "]", value)
    }
    return eventPrize;
end
```

위 코드는 denormalized_by_prizes_utils.rb라는 이름의 파일에 작성되어 있습니다.

그 이후에 필터의 주요 코드는 다음과 같습니다:

<div class="content-ad"></div>

```javascript
require './script/denormalized_by_prizes_utils.rb'

// `params`의 값은 로그스태시 구성에서 `script_params`로 전달된 해시 값입니다.
function register(params) {
    @keep_original_event = params["keep_original_event"];
}

// 필터 메서드는 이벤트를 수신하고 이벤트 목록을 반환해야 합니다.
// 이벤트를 삭제하면 반환 배열에 포함되지 않습니다.
// 새 이벤트를 생성하려면 반환된 배열에 LogStash::Event 인스턴스를 추가하면 됩니다.
function filter(event) {

    var items = [];

    // 필요하다면 원본 이벤트를 유지합니다.
    var originalEvent = getOriginalEvent(event);
    if (!originalEvent) {
        items.push(originalEvent);
    }

    // 상금 아이템(정규화) 가져오기
    var prizes = getPrizes(event);
    if (!prizes) {
        return items;
    }
   
    // 클론 기본 이벤트 생성
    var eventBase = getEventBase(event);

    // 필요한 수정을 가진 상금 항목별 하나의 이벤트 생성
    prizes.forEach(function(prize) {
        items.push(createEventForPrize(eventBase, prize));
    });

    return items;
}
```

기존 코드보다 훨씬 읽기 쉽고 필터 기능의 다른 단계를 직접 확인할 수 있습니다. 작은 함수로 잘 세분화되고 이해하기 쉽게 작성하여 유지보수성이 향상될 것입니다.

하지만 경우에 따라 코드를 공유하는 여러 파일과 여러 파일이 필요한 필터를 갖고 있는 경우, 충돌이 발생하거나 유지보수성이 일부 저하될 수 있습니다.

# 모듈 생성하기
```

<div class="content-ad"></div>

다른 방법으로 코드를 공유하는 방법은 모듈을 만드는 것입니다. 이 모듈은 같은 기능적 범위의 코드 조각을 그룹화할 것입니다. 우리는 공유 함수를 사용하기 전에 모듈 이름을 지정해야 하기 때문에 충돌이 발생하지 않을 것입니다.

이전에 공유된 함수는 다음과 같이 될 것입니다:

```js
module LogStash::Util::DenormalizationByPrizesHelper
    include LogStash::Util::Loggable

    # 원래 이벤트 유지
    def self.getOriginalEvent(event, keepOriginalEvent)
        logger.debug('keepOriginalEvent is :' + keepOriginalEvent.to_s)
        if keepOriginalEvent.to_s == 'true'
            event.set('[@metadata][_index]', 'prizes-original');
            return event;
        end
        return nil;
    end

    # 상품 항목 가져오기 (정규화 해제)
    def self.getPrizes(event)
        prizes = event.get("prize");
        if prizes.nil?
            logger.warn("No prizes for event " + event.to_s)
        end
        return prizes;
    end

    # 복제 기본 이벤트 생성
    def self.getEventBase(event)
        eventBase = event.clone();
        eventBase.set('[@metadata][_index]', 'prizes-denormalized');
        eventBase.remove("prize");
        return eventBase;
    end

    # 필요한 수정으로 현재 상품 항목을 위한 복제 이벤트 생성
    def self.createEventForPrize(eventBase, prize)
        eventPrize = eventBase.clone();
        # 각 상품 항목 값을 상품 객체로 복사
        prize.each { |key,value|
            eventPrize.set("[prize][" + key + "]", value)
        }
        return eventPrize;
    end

end
```

logger 인스턴스를 사용할 수 있도록 Loggable Util 모듈을 포함해야 합니다.

<div class="content-ad"></div>

주요 코드는 다음과 같습니다:

```js
require './script/denormalized_by_prizes_utils.rb'

# `params`의 값은 Logstash 구성에서 `script_params`에 전달된 해시의 값입니다.
def register(params)
    @keep_original_event = params["keep_original_event"]
end

# 필터 메서드는 이벤트를 받아서 이벤트 목록을 반환해야 합니다.
# 이벤트를 삭제하는 것은 반환 배열에 포함시키지 않는 것을 의미합니다.
# 새 이벤트를 생성하는 것은 반환 배열에 LogStash::Event의 인스턴스를 추가하는 것만 필요합니다.
def filter(event)

    items = Array.new

    # 요청이 있을 경우 원본 이벤트 보존
    originalEvent = LogStash::Util::DenormalizationByPrizesHelper::getOriginalEvent(event, @keep_original_event);
    if not originalEvent.nil?
        items.push originalEvent
    end

    # 상품 항목 가져오기 (정규화)
    prizes = LogStash::Util::DenormalizationByPrizesHelper::getPrizes(event);
    if prizes.nil?
        return items
    end
   
    # 복제된 기본 이벤트 생성
    eventBase = LogStash::Util::DenormalizationByPrizesHelper::getEventBase(event);

    # 필요한 수정을 가한 상품 항목별로 이벤트 생성
    prizes.each { |prize| 
        items.push LogStash::Util::DenormalizationByPrizesHelper::createEventForPrize(eventBase, prize);
    }

    return items;
end
```

주요 코드를 수정할 필요가 많지 않습니다. 함수 호출을 모듈 이름과 함께 접두사로 붙이면 됩니다. 따라서 필터 기능에 통합된 다른 모듈에 있는 여러 `getEventBase` 함수와 같은 함수들이 있을 경우 충돌 없이 사용할 수 있습니다. 명시적으로 각 경우에 사용할 모듈을 설정하고 가독성을 향상시키기 때문에 좋습니다.

다음 글에서는 필터 코드를 테스트하는 방법에 대해 이야기할 것입니다...