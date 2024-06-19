---
title: "Redis를 S3 Express로 재구축하기"
description: ""
coverImage: "/assets/img/2024-06-19-RebuildingRedisonS3Express_0.png"
date: 2024-06-19 12:10
ogImage: 
  url: /assets/img/2024-06-19-RebuildingRedisonS3Express_0.png
tag: Tech
originalTitle: "Rebuilding Redis on S3 Express"
link: "https://medium.com/data-engineer-things/rebuilding-redis-on-s3-express-e2701c5dcc29"
---


## 느린, 저렴하고 확장 가능한 키-값 저장소

## 느린-저렴 대 빠른-비싼

다년간 "최고의 데이터베이스"로 선정된 Redis는 빠르고 신뢰성 있으며 고처리량의 키-값 저장소로 탄생했습니다 (훨씬 더 발전하기 전). 오늘날에도 가장 많이 사용되는 용도는 캐싱입니다 — 사용자를 위해 작고 자주 액세스하는 데이터를 빠르게 저장합니다 (예: 몇 줄의 파이썬 코드로):

```js
>>> import redis
>>> r = redis.Redis(host='myhost', port=6379, db=0)
>>> r.set('userId:10', '1701743088')
True
```

<div class="content-ad"></div>

그리고 키 기반 쿼리로 나중에 다시 검색하세요:

```js
>>> r.get('userId:10')
1701743088
```

쉽고 빠르죠. Redis의 가치와 비밀은 데이터 전문가들에게 분명히 비밀이 아니며, 비용도 마찬가지입니다. 메모리는 비싸며, 캐시는 클라우드 요금에 빠르게 영향을 줄 수 있습니다. AWS ElasticCache의 25GB(cache.m7g.2xlarge)는 약 500달러 / 월입니다.

다른 한편으로, S3와 같은 객체 저장소는 다른 가격 / 성능 트레이드오프를 약속했습니다. 25GB 저장 비용은 약 0.5달러 / 월이지만, S3 지연 시간(평균 및 테일)은 당신의 응용 프로그램에 심각한 영향을 미칠 수 있습니다. 만약 일관적이고 빠른 작업에 의존하고 있다면요:

<div class="content-ad"></div>

요즘까지는 아니었지만, S3 익스프레스의 공개로 파레토 프런티어에 또 다른 포인트가 추가되었습니다. "익스프레스 저장소 클래스는 최대 10배 더 나은 성능을 제공하기 위해 설계되었으며 (...), 가장 자주 액세스하는 데이터에 탁월한 적합성을 갖추고 있습니다."

이러한 새로운 기능에 매료되어, 우리는 AWS의 신제품을 테스트하기 위해 Redis와 유사한 키-값 워크로드를 완전히 S3로 백업하는 새로운 인터페이스를 재구성해 보았습니다(적절하게 "redis3"라고 합니다). 우리는 이를 프로토타입화하고 테스트하며 오픈소스로 공유했는데, 우리가 배운 것은 이렇습니다: 저장소를 복제하고 스타를 추가하며 함께하세요!

![이미지](/assets/img/2024-06-19-RebuildingRedisonS3Express_0.png)

## Redis3: 개발자 시각

<div class="content-ad"></div>

요즘 데이터 랜드의 모든 사람들이 개발자 경험에 집착하고 있기 때문에, 우리는 redis3를 최종 사용자인 개발자에게 어떻게 보이는지부터 살펴보겠습니다. GET/SET 연산이 실제로 Redis의 핵심 기능이므로, redis-py 명령어를 redis3와 비교하여 시작합니다.

```js
>>> from redis3 import redis3Client
>>> r = redis3Client(cache_name='mytestcache', db=0)
>>> r.set('userId:10', '1701743088')
True
>>> r.get('userId:10')
1701743088
```

알 수 있는 독자들은 이미 알고 계실 것이지만, 위 명령어는 기본적으로 동일합니다: URL 및 포트는 클라이언트 초기화에 없지만 (이유는 아래에서 자세히 설명되어 있습니다. 심지어 GET 및 SET에 대해 동일한 간결한 구문과 의미론을 가지고 있습니다.

Redis3에서는 데이터베이스의 모든 키를 나열하거나 Redis에서의 MSET 및 MGET을 모방할 수 있습니다.```

<div class="content-ad"></div>

```js
key_list = ['playground_{}'.format(i) for i in range(5)]
val_list = ['bar_{}'.format(i) for i in range(5)]
r = my_client.mset(key_list, val_list)
val_list_back = my_client.mget(key_list)
assert val_list == val_list_back
```

그리고 key를 삭제할 수도 있어요 (DEL):

```js
r = my_client.delete('userId:10')
```

마지막으로, db 매개변수를 사용하여 키를 자동으로 네임스페이스 할 수 있어요 (물론 16에 제한되지 않아요):```

<div class="content-ad"></div>

```js
r = redis3Client(cache_name='mytestcache', db=133)
```

## Redis3: 아키텍처

자세히 살펴보면, redis3Client는 boto3 작업을 매우 얇게 래핑한 것입니다. 특히 S3 Express 버킷에 대한 작업을 합니다. 다음과 같이 클라이언트를 초기화합니다:

```js
r = redis3Client(cache_name='mytestcache', db=0)
```

<div class="content-ad"></div>

버킷 redis3-mytestcache -- use1-az5 -- x-s3 를 생성하는 시도를 합니다. 즉, use1-az5 가용 영역에서 명명 규칙을 따르는 익스프레스 버킷을 생성합니다. 그런 다음 db 매개변수가 AWS 폴더 내에 키를 이름 공간으로 사용합니다:

```js
redis3-mytestcache -- use1-az5 -- x-s3
    0/
        my_key_1
        my_key_2
    1/
        my_key_1
    ...
```

이 설계에는 일반 캐싱 서버와 비교할 때 몇 가지 장단점이 있습니다. (가격-성능 외에, 이는 다음에 다룹니다):

- 배포, 유지 관리, 이해하는 데 추가 인프라가 필요하지 않습니다.
- 보안, 연결 및 권한 부여를 IAM 세분성으로 처리할 수 있습니다. 사용자는 예를 들어 새 캐시를 생성할 수 없지만 기존 캐시에서 키를 가져올 수 있는 권한을 가질 수 있습니다. redis3Client는 스크립트 실행 시 적용되는 AWS 권한을 상속하고 존중합니다 (마치 boto3.client('s3') 와 같이);
- redis3Client는 연결 풀이 필요하지 않으며 공간이 부족해지지 않고 대량 처리를 지원합니다 (결국 S3 이기 때문에!). 최근에는 이 용어가 남발되고 있지만, 여기에는 분명히 "서버리스 경험"이 있습니다.

<div class="content-ad"></div>

그러나 Redis의 동시성 모델은 이해하기 매우 쉽습니다. 병행성이 많지 않기 때문에 원자 연산 지원이 시스템의 정확성을 보장하는 데 중요합니다. 안타깝게도 S3는 그러한 보장을 제공하지 않습니다. S3는 강력한 쓰기 후 읽기 일관성을 제공하지만(예를 들어 SET 직후에 DB 내용을 나열할 때 유용함), 여전히 INCR과 같은 것을 흉내 내는 방법은 없습니다. 이로 인해 누군가가 이전 my_value를 읽고 있는 동안에도 코드가 해당 값을 증가시키고 다시 저장하는 것을 피할 방법이 없습니다:

```js
v = GET my_value
v = v + 1
SET my_value v
```

이에 대해 어떤 사람들은 redis3Client MSET 및 MGET 명령어가 잘못된 이름을 가졌으며, Redis 파이프라인으로 보내는 키 목록과 관련된 것보다 원래의 원자적 명령어와 더 유사하다고 주장할 수 있습니다 (사실 이름 짓기와 캐싱을 한다는 것이 컴퓨터 과학에서 가장 어려운 두 가지일이라는 것이 밝혀졌습니다!).

## 빠르고 비교적 저렴한 방법

<div class="content-ad"></div>

우리의 파이썬 메소드가 얼마나 좋은지는 중요하지 않아요. 캐시는 빠르고 신뢰할 수 있어야 해요. 저희는 EC2에서 테스트 스크립트를 실행하고, 같은 가용 영역에 위치한 redis3 캐시와 표준 S3 버킷, Redis 인스턴스(그냥 편하게 Redis Labs에서 호스팅해요! ) 결과(초 단위)를 비교해요:

![image](/assets/img/2024-06-19-RebuildingRedisonS3Express_1.png)

Redis보다는 여전히 느리지만, redis3는 실제로 새로운 가격 성능 옵션으로, "미숙한 S3 캐시"보다 평균적으로 훨씬 더 빠를 뿐만 아니라 신뢰할 수 있음을 입증했어요(95백분위에서 5배).

공식 가격표를 빠르게 살펴보면, AWS Redis 인스턴스와 비교했을 때 더 분명하게 알 수 있어요:

<div class="content-ad"></div>

![image](/assets/img/2024-06-19-RebuildingRedisonS3Express_2.png)

1GB의 redis3 캐시는 메모리 지원 캐시보다 훨씬 저렴하게 구입할 수 있습니다 (추가 인프라 및 유지보수를 고려하지 않음).

## 수괴카우보이, 다음에 봐요

S3를 캐시로 사용하는 것은 미친 것처럼 보일 수 있지만, 새로운 익스프레스 버킷을 사용하면 그렇게 보이지 않습니다: Redis가 어디론가 사라지지 않는 한, 객체 스토리지와 데이터 애플리케이션의 상호 진화는 계속해서 새로운 트레이드 오프를 활용하는 흥미로운 패턴을 만들어냅니다. 이 실험을 좋아하셨다면, redis3 클래스를 직접 사용해보고 어떻게 생각하는지 알려주세요!

<div class="content-ad"></div>

마지막으로, EC2를 S3 위에 올려 데이터 작업을 수행할 때 무엇이 발생하는지 신경 쓰신다면, Bauplan에 대한 최신 정보를 따르는 것을 잊지 마세요.