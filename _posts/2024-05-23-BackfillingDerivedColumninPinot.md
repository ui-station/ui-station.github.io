---
title: "핀오프에서 파생 열backfilling derived column 채우기"
description: ""
coverImage: "/assets/img/2024-05-23-BackfillingDerivedColumninPinot_0.png"
date: 2024-05-23 15:51
ogImage: 
  url: /assets/img/2024-05-23-BackfillingDerivedColumninPinot_0.png
tag: Tech
originalTitle: "Backfilling Derived Column in Pinot"
link: "https://medium.com/@shruti1810/backfilling-derived-column-in-pinot-fce24829cb3e"
---


데이터 엔지니어링 세계에서 백필링은 일반적인 시나리오입니다. 최근에 Pinot의 실시간 테이블에서 작업하던 중에 다른 JSON 기반 열에 존재하는 값을 추출하고있는데, 해당 속성이 깊은 중첩 안에 숨겨져 있어서 꽤 어려운 상황에 빠졌었죠.

<img src="/assets/img/2024-05-23-BackfillingDerivedColumninPinot_0.png" />

이제 매번 이 테이블을 쿼리하고 원하는 속성을 필요로 할 때마다 해당 속성의 값을 추출하는 옵션은 항상 존재했습니다. 그러나 우리가 쿼리를 발전시키고자 했던 방식은 이 새로운 속성을 `where` 절에서도 사용하는 것이었습니다. 따라서, 이 속성에 대한 새로운 열을 만들어 이 열에 인덱스를 넣는 것이 좋은 지연 경험을 얻을 수 있는 가장 좋은 옵션이었습니다.

이 새로운 열을 도입하는 것은 원활할 수 있지만, 각 들어오는 항목마다 이 새로운 열을 채우기 시작할 수 있으며, 또한 이미 테이블에 존재하는 레코드에 대해서도 이 열을 백필하는 작업이 필요했습니다. 이는 실시간 테이블이었기 때문에 다소 어려웠고, 누군가가 이를 이전에 수행한 문서나 블로그가 없었습니다. 그래서 저는 이 여정에 착수하여 Pinot에서 파생 열을 위한 백필을 달성하는 데 관련된 모든 세부 사항을 파악했습니다. 실시간 테이블에 대해 이 문제를 해결했지만, 과정은 배치 테이블에 대해서도 유사할 것입니다.

<div class="content-ad"></div>

# 문제 설명

다음과 같은 `orders` 테이블이 있다고 가정해 봅시다:

![테이블](/assets/img/2024-05-23-BackfillingDerivedColumninPinot_1.png)

이제 우리는 `productDetails` 열에서 `brand` 속성을 유도하고, 이를 고유한 열로 만들고 싶습니다. 기존 레코드는 모두 원하는 값을 `productDetails` 열에서 추출하여 backfilling해야 합니다.

<div class="content-ad"></div>

브랜드 값이 없는 제품도 있을 수 있으니 해당 경우 파생 값을 null로 처리해야 합니다.

최종 테이블은 다음과 같이 보일 것입니다:

![테이블](/assets/img/2024-05-23-BackfillingDerivedColumninPinot_2.png)

# 스키마에 새 열 추가하기

<div class="content-ad"></div>

첫 번째로 할 일이네요! `orders` 테이블 스키마에 이 새로운 열을 추가하세요.
예를 들어, 기존 스키마가 다음과 같다면:

```js
{
   "schemaName": "orders",
   "enableColumnBasedNullHandling": false,
   "dimensionFieldSpecs": [
     {
       "name": "orderId",
       "dataType": "INT",
       "notNull": false
     },
     {
       "name": "productId",
       "dataType": "INT",
       "notNull": false
     },
     {
       "name": "amount",
       "dataType": "LONG",
       "notNull": false
     },
     {
       "name": "productDetails",
       "dataType": "JSON",
       "notNull": false
     }
   ],
   "dateTimeFieldSpecs": [
     {
       "name": "createdAt",
       "dataType": "TIMESTAMP",
       "notNull": false,
       "format": "1:MILLISECONDS:EPOCH",
       "granularity": "1:MILLISECONDS"
     }
   ]
}
```

"브랜드"라는 새로운 열은 "UI에서 스키마 편집" 옵션을 사용하거나 해당 REST API를 사용하여 스키마에 추가할 수 있습니다. 이 열의 `notNull` 값을 false로 설정해야 합니다. 새로운 스키마는 다음과 같이 보일 것입니다:

```js
{
   "schemaName": "orders",
   "enableColumnBasedNullHandling": false,
   "dimensionFieldSpecs": [
     {
       "name": "orderId",
       "dataType": "INT",
       "notNull": false
     },
     {
       "name": "productId",
       "dataType": "INT",
       "notNull": false
     },
     {
       "name": "amount",
       "dataType": "LONG",
       "notNull": false
     },
     {
       "name": "brand",
       "dataType": "STRING",
       "notNull": false
     },
     {
       "name": "productDetails",
       "dataType": "JSON",
       "notNull": false
     }
   ],
   "dateTimeFieldSpecs": [
     {
       "name": "createdAt",
       "dataType": "TIMESTAMP",
       "notNull": false,
       "format": "1:MILLISECONDS:EPOCH",
       "granularity": "1:MILLISECONDS"
     }
   ]
}
```

<div class="content-ad"></div>

# 변환 구성 설정하기

이것은 프로세스에서 가장 중요한 단계 중 하나입니다. 새 열에 필요한 변환 구성을 찾아야 합니다. 이 경우 다음 변환 구성을 사용할 수 있습니다:

```js
{
   "columnName": "brand",
   "transformFunction": "jsonPathString(json_format(productDetails), '$.details.brand', 'null')"
}
```

이 변환을 통해 우리는 `productDetails` 열에서 `brand` 속성을 추출할 것입니다. 이 속성이 없는 경우 `brand` 열에 문자열 `null`을 넣을 것입니다.

<div class="content-ad"></div>

상기 언급된 변환 구성은 테이블 구성의 "ingestionConfig" - "transformationConfigs" 섹션에 추가할 수 있습니다.

# 파생 열 역추적

이것은 역추적이 발생하는 실제 단계입니다. 역추적이 이루어지려면 모든 세그먼트를 다시로드해야 합니다. 이 작업은 테이블 페이지의 "모든 세그먼트 다시로드" 버튼을 클릭하거나 REST API(POST) `/segments/'tableName'/reload`을 호출하여 수행할 수 있습니다.

일반적으로 이 작업은 1분 미만이 소요되지만, 매우 큰 테이블의 경우 조금 더 오랜 시간이 걸릴 수 있습니다. 이 작업의 상태를 확인하려면 테이블 페이지에서 "다시로드 상태" 버튼을 클릭할 수 있습니다. 다시로드 상태를 확인하는 해당 API는 `/segments/segmentReloadStatus/'jobId'`입니다.

<div class="content-ad"></div>

작업이 완료되면 모든 레코드에 값이 포함된 파생 컬럼을 볼 수 있어야 합니다.

# 주의 사항

다음은 프로세스의 오류 포인트 중 일부입니다(제가 고생하며 배웠습니다). 리로드 세그먼트 작업은 새로 추가된 컬럼에만 영향을 미칩니다. 한 번 리로드 세그먼트 작업을 실행하고 변환 구성에 버그가 있는 것을 발견하면 해당 테이블에 무용지물 컬럼이 남게 됩니다. 이제 이 컬럼의 변환 구성을 수정하고 다시 리로드 세그먼트를 트리거하면 해당 컬럼에 값이 없는 것을 확인할 수 있습니다. 리로드 세그먼트 작업은 모든 세그먼트에 대해 성공했다는 상태를 표시할지라도 실제로는 아무 작업도 수행하지 않을 것입니다. Pinot이 역호환성 문제에 대해 불평하고 해당 컬럼을 삭제할 수 없도록 막을 것입니다.

이를 해결하기 위해 테이블의 사본을 만들고 해당 테이블 사본에 백필을 수행하여 변환 구성이 올바른지 확인하는 것을 강력히 권장합니다. 변환 구성에 만족하면 해당 테이블에 필요한 단계를 수행하십시오.

<div class="content-ad"></div>

# 결론

전반적으로 Pinot은 이 블로그에서 설명된 것처럼 파생 열을 백필링하는 등 여러 기능을 지원하는 강력한 OLAP 데이터 스토어입니다. 그러나 위대한 능력에는 큰 책임이 따릅니다! 새롭게 추가된 열에 대해 백필링을 수행할 때 매우 주의해야 합니다. 새로 추가된 열에 대해 리로드를 한 번만 실행할 수 있습니다.