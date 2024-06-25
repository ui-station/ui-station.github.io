---
title: "맵핑 대 이터러블 맵핑 간단히 해석하기"
description: ""
coverImage: "/assets/img/2024-05-27-MappingsvsIterableMappingsSimplified_0.png"
date: 2024-05-27 16:20
ogImage:
  url: /assets/img/2024-05-27-MappingsvsIterableMappingsSimplified_0.png
tag: Tech
originalTitle: "Mappings vs. Iterable Mappings: Simplified"
link: "https://medium.com/@imethvinnath/mappings-vs-iterable-mappings-simplified-eb4e588400a8"
---

안녕하세요! 모두들! 저는 모든 것을 간단하게 만들어 설명해드릴게요. 데이터 구조가 어떻게 작동하는지 설명하는 글이 정말 많죠. 하지만 여기서는 조금 더 쉽게 설명할 거에요. 이 글을 끝까지 읽어보시면 제대로 이해하실 거에요. 확실해요💯.

![이미지](/assets/img/2024-05-27-MappingsvsIterableMappingsSimplified_0.png)

# 표준 Mapping

Mappings이 뭔가요? 하하, 제가 어이가 없네요! Solidity에서의 Mappings은 간단한 전화번호부와 같아요. 사람의 이름을 통해 전화번호를 찾을 수 있지만, 전화번호를 통해 이름을 찾을 수는 없어요. (Mapping이라고 말할 때는 표준 Mapping을 의미합니다 😉) 지금쯤 완전 쉽다고 생각하셨을 거예요. 그래요, 이렇게 말해볼게요:

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

매핑은 Solidity에서 키-값 데이터 구조입니다. 고유한 키를 기반으로 값을 저장하고 검색할 수 있습니다. 주로 고유한 이더리움 주소와 다양한 값 유형을 연결하는 데 사용되어, 스마트 계약에서 데이터를 관리하는 데 필수적인 도구입니다.

# 매핑의 종류

간단한 매핑:

간단한 매핑의 예제를 살펴봅시다. 이곳에서는 이더리움 주소와 해당 잔액 간의 연결을 만들고 있습니다. 이를 "잔액(balances)"이라는 매핑으로 부르겠습니다.

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

```js
매핑 :address => uint) public balances;
```

<img src="/assets/img/2024-05-27-MappingsvsIterableMappingsSimplified_1.png" />

컨트랙트인 SimpleMapping을 살펴보겠습니다.

```js
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.25;

contract SimpleMapping {
    mapping(address => uint) public balances;

    function setBalance(address _user, uint _balance) public {
        balances[_user] = _balance;
    }

    function getBalance(address _user) public view returns (uint) {
        return balances[_user];
    }
}
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

아래는 배포된 계약의 스크린샷입니다. 'setBalance' 함수를 호출할 때 이더리움 주소와 해당 잔고를 제공했습니다. 'getBalance'에서는 그 이더리움 주소의 잔고를 검색했습니다.

![contract-screenshot](/assets/img/2024-05-27-MappingsvsIterableMappingsSimplified_2.png)

Solidity에서 매핑을 선언할 때 주의할 점:

- 사실상 두 값 사이의 연결을 만드는 것입니다.
- 매핑에서 키를 사용하여 해당 값을 찾을 수 있지만, 역은 찾을 수 없습니다. 즉, 값을 사용하여 키를 찾을 수는 없습니다.

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

중첩 매핑:

이제 중첩 매핑에 대해 알아보겠습니다. 두 개의 이더리움 주소와 불리언 값 사이의 링크를 생성 중입니다. 불리언 값이 true이면, 두 주소는 관련이 있습니다.

```js
mapping(address => mapping(address => bool)) public isRelated;
```

<img src="/assets/img/2024-05-27-MappingsvsIterableMappingsSimplified_3.png" />

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

안녕하세요! 아래는 'NestedMapping' 계약에 대한 정보입니다.

```js
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.25;

contract NestedMapping {
    mapping(address => mapping(address => bool)) public isRelated;

    function setRelationship(address _user1, address _user2, bool _isRelated) public {
        isRelated[_user1][_user2] = _isRelated;
    }

    function checkRelationship(address _user1, address _user2) public view returns (bool) {
        return isRelated[_user1][_user2];
    }
}
```

배포된 계약의 스크린샷을 아래에서 확인할 수 있습니다. 'setRelationship' 함수를 사용하여 두 이더리움 주소 및 false 값을 제공하였습니다. 'checkRelationship' 함수에서 해당 중첩 매핑의 부울 값을 검색하였습니다.

![매핑 이미지](/assets/img/2024-05-27-MappingsvsIterableMappingsSimplified_4.png)

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

또한 중첩된 매핑의 경우:

- 순서가 중요합니다.
- \_user1에 값 설정 후 \_user2에 값 설정하는 것은 자동으로 \_user2에 값 설정 후 \_user1에 값 설정하는 것을 의미하지 않습니다.

# 반복 가능한 매핑

반복 가능한 매핑이란 무엇인가요?

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

반복 가능한 매핑은 모든 이름과 전화번호를 나열할 수 있는 향상된 전화번호부와 같습니다. 이름으로 전화번호를 찾을 수는 있지만 모든 항목을 하나씩 차례대로 확인할 수도 있습니다.

주요 포인트:

- 양방향 상호작용: 키로 값을 찾거나 모든 키를 나열할 수 있습니다.
- 더 많은 제어: 보다 복잡한 작업을 수행할 수 있습니다. 예를 들어 보고서 생성이나 모든 항목에 대한 작업 수행 등이 가능합니다.

예시:

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

```js
// SPDX-License-Identifier: MIT
pragma solidity 0.8.25;

contract IterableMapping {

    mapping(address => uint) private balances;

    address[] private keys;

    mapping(address => bool) private isKey;

    function setBalance(address _user, uint _balance) public {
        if (!isKey[_user]) {
            keys.push(_user);
            isKey[_user] = true;
        }
        balances[_user] = _balance;
    }

    function getBalance(address _user) public view returns (uint) {
        return balances[_user];
    }

    function getAllKeys() public view returns (address[] memory) {
        return keys;
    }
}
```

배포된 스마트 계약의 스크린샷이 아래에 있습니다. ‘getAllKeys’ 함수를 호출했습니다. 현재 ‘keys’ 배열에 저장된 모든 키를 반환해야 합니다. 이는 Iterable Mapping을 사용하여 가능한 내용 중 하나에 불과합니다… 모두 나열했습니다.

<img src="/assets/img/2024-05-27-MappingsvsIterableMappingsSimplified_5.png" />

# 왜 Iterable Mappings를 사용해야 하는가?

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

- 모든 것 나열하기: 모든 항목을 보고 싶을 때 반복 가능한 매핑을 사용할 수 있어요. 예를 들어, 모든 토큰 소지자를 나열하는 경우입니다.
- 복잡한 작업: 모든 항목에 작업을 수행할 수 있도록해주어, 모든 사용자에게 보상을 분배하는 것과 같은 작업을 수행할 수 있어요.

각각을 사용하는 시점:

- 표준 매핑: 간단하고 빠른 조회가 필요하며 모든 항목을 나열할 필요가 없는 경우에 사용하세요. 예를 들어, 사용자의 잔고를 확인하는 경우입니다.
- 반복 가능한 매핑: 모든 항목을 나열하거나 관리해야 할 때 사용하세요. 예를 들어, 모든 사용자의 잔액을 처리하는 경우입니다.

# 비교 요약

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

<table> 태그를 Markdown 형식으로 변경해주세요.

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

테이블 태그를 마크다운 형식으로 변경해 주세요!
