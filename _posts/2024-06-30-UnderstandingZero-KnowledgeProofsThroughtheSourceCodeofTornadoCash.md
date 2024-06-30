---
title: "Tornado Cash 소스 코드를 통해 알아보는 영지식 증명 이해하기"
description: ""
coverImage: "/assets/img/2024-06-30-UnderstandingZero-KnowledgeProofsThroughtheSourceCodeofTornadoCash_0.png"
date: 2024-06-30 22:07
ogImage: 
  url: /assets/img/2024-06-30-UnderstandingZero-KnowledgeProofsThroughtheSourceCodeofTornadoCash_0.png
tag: Tech
originalTitle: "Understanding Zero-Knowledge Proofs Through the Source Code of Tornado Cash"
link: "https://medium.com/better-programming/understanding-zero-knowledge-proofs-through-the-source-code-of-tornado-cash-41d335c5475f"
---


## 제로지식증명으로 스마트 컨트랙트 세계로 뛰어들어 보세요

![image](/assets/img/2024-06-30-UnderstandingZero-KnowledgeProofsThroughtheSourceCodeofTornadoCash_0.png)

위키피디아에 따르면, 제로지식증명(ZKP)의 정의는 다음과 같습니다:

제로지식증명(ZKP) 기술은 블록체인과 같은 공개적인 데이터베이스에서 해결하기 어려운 익명 투표 또는 익명 금전 송금과 같은 여러 다양한 분야에 널리 사용될 수 있습니다.

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

토네이도 캐시는 이더리움 트랜잭션을 익명화할 수 있는 코인 믹서입니다. 블록체인의 논리로 인해 모든 트랜잭션은 공개적이기 때문에 자신의 계정에 이더리움이 있다면 누구나 블록체인에서 트랜잭션 기록을 따라갈 수 있습니다. 토네이도 캐시와 같은 코인 믹서는 ZKP를 사용하여 출처와 목적지 주소 사이의 체인 상의 연결을 끊어 개인 정보 보호 문제를 해결할 수 있습니다.

트랜잭션 중 하나를 익명화하려면 토네이도 캐시 계약에 소량의 이더리움(또는 ERC20 토큰)을 예치해야 합니다(예: 1 ETH). 잠시 후 다른 계정으로 이 1 ETH를 인출할 수 있습니다. 핵심은 누구도 예치 계정과 인출 계정 사이에 연결을 만들 수 없다는 점입니다. 수백 개의 계정이 한 쪽에 1 ETH를 예치하고 다른 수백 개의 계정이 다른 쪽에서 1 ETH를 인출하면 자금 이동 경로를 추적할 수 없습니다. 기술적인 과제는 스마트 계약 트랜잭션도 이더리움 네트워크의 다른 모든 트랜잭션과 마찬가지로 공개적이라는 것입니다. 여기서 ZKP가 관련될 때가 됩니다.

계약에 1 ETH를 예치할 때 "커밋먼트"를 제공해야 합니다. 이 커밋먼트는 스마트 계약에 저장됩니다. 다른 쪽에서 1 ETH를 인출할 때는 "널리파이어"와 제로 지식 증명을 제공해야 합니다. 널리파이어는 커밋먼트와 관련이 있는 고유한 ID이며, ZKP는 이 연결을 증명하지만 어떤 널리파이어가 어떤 커밋먼트에 할당되어 있는지는 아무도 모릅니다(예외는 예금자/인출 계정의 소유주).

한번 더 말씀드리면: 우리는 커밋먼트가 우리의 널리파이어에 할당되어 있다는 것을 증명할 수 있습니다. 본인의 커밋먼트를 공개하지 않고요.

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

스마트 계약으로 널리파이어를 추적하므로 한 번에 예금한 ETH를 하나의 널리파이어로만 인출할 수 있습니다.

쉬워보이죠? 그렇지 않아요! :) 이제 기술의 심연으로 들어가 봅시다. 하지만 무엇보다도 또 다른 tricky한 요소, Merkle tree를 이해해야 합니다.

![Merkle tree](/assets/img/2024-06-30-UnderstandingZero-KnowledgeProofsThroughtheSourceCodeofTornadoCash_1.png)

Merkle tree는 잎이 요소인 해시 트리로, 모든 노드가 자식 노드의 해시인 구조입니다. 트리의 루트는 Merkle root로, 전체 요소 집합을 나타냅니다. 트리에서 어떤 요소(잎)를 추가, 제거 또는 변경하면 Merkle root가 변경됩니다. Merkle root는 요소 집합의 고유 식별자입니다. 그럼 어떻게 사용할까요?

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


![Understanding Zero Knowledge Proofs through the Source Code of Tornado Cash](/assets/img/2024-06-30-UnderstandingZero-KnowledgeProofsThroughtheSourceCodeofTornadoCash_2.png)

다른 것으로 Merkle proof라는 것이 있어요. 만약 내게 Merkle root가 있다면, 당신은 루트로 표현된 집합에 요소가 있는 것을 증명해주는 Merkle proof를 보낼 수 있어요. 아래 그림은 어떻게 작동하는지 보여줍니다. 만약 HK가 그 집합 안에 있다는 것을 나에게 증명하고 싶다면, HL, HIJ, HMNOP, HABCDEFGH 해시를 보내주어야 해요. 이 해시들을 사용하여 나는 Merkle root를 계산할 수 있어요. 만약 루트가 내 루트와 같다면, HK가 집합 안에 있는 것이에요. 어디에 사용할 수 있을까요?

간단한 예시로 화이트리스트를 상상해봅시다. 오직 화이트리스트된 사용자만이 호출할 수 있는 메소드를 갖는 스마트 컨트랙트가 있다고 가정해봅시다. 문제는 화이트리스트에 있는 계정이 1000개나 되는 것이에요. 이를 스마트 컨트랙트에 저장하는 방법은 무엇일까요? 각 계정을 매핑에 저장하는 간단한 방법이 있지만, 매우 비싸요. 더 싼 해결책은 Merkle tree를 만들고 Merkle root만 저장하는 것이에요 (1개의 해시 대신 1000개의 해시는 아주 좋아요). 누군가가 그 메소드를 호출하고 싶으면, 그 지능적인 스마트 컨트랙트가 쉽게 유효성 검사할 수 있는 Merkle proof(이 경우에 10개 해시의 목록)를 제공해야 해요.

한번 더 말씀드리면: Merkle tree는 하나의 해시(=Merkle root)로 원소 집합을 나타내는 데 사용됩니다. Merkle proof를 통해 원소의 존재를 증명할 수 있어요.


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

다음으로 이해해야 할 것은 제로-지식 증명(ZKP) 자체입니다. ZKP를 사용하면 무언가를 알고 있다는 사실을 증명할 수 있습니다. 그러나 알고 있는 내용을 공개하지 않으면서 증명할 수 있습니다. ZKP를 생성하기 위해서는 회로가 필요합니다. 회로란 공개 입력과 출력, 그리고 비공개 입력을 가진 작은 프로그램과 같은 것입니다. 이러한 비공개 입력은 검증을 위해 공개하지 않는 정보이며, 이것이 제로-지식 증명이라 불리는 이유입니다. ZKP를 사용하면 주어진 회로와 입력에서 출력이 생성될 수 있는 것을 증명할 수 있습니다.

간단한 회로는 다음과 같습니다:

```js
pragma circom 2.0.0;

include "node_modules/circomlib/circuits/bitify.circom";
include "node_modules/circomlib/circuits/pedersen.circom";

template Main() {
    signal input nullifier;
    signal output nullifierHash;

    component nullifierHasher = Pedersen(248);
    component nullifierBits = Num2Bits(248);

    nullifierBits.in <== nullifier;
    for (var i = 0; i < 248; i++) {
        nullifierHasher.in[i] <== nullifierBits.out[i];
    }

    nullifierHash <== nullifierHasher.out[0];
}

component main = Main();
```

이 회로를 사용하면 주어진 해시의 원본을 알고 있다는 것을 증명할 수 있습니다. 이 회로에는 하나의 입력(널리파이어)과 하나의 출력(널리파이어 해시)가 있습니다. 입력의 기본 접근성은 비공개이며, 출력은 항상 공개입니다. 이 회로는 Circomlib에서 2개의 라이브러리를 사용합니다. Circomlib은 유용한 회로들의 집합입니다. 첫 번째 라이브러리는 비트 조작 방법을 포함하는 bitlify이며, 두 번째는 페더슨 해시를 포함하는 pedersen입니다. 페더슨 해싱은 제로-지식 증명 회로에서 효율적으로 실행할 수 있는 해싱 방법입니다. Main 템플릿의 내용에서 해시를 채우고 계산합니다. (circom 언어에 대한 자세한 정보는 circom 문서를 참조해주세요)

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

영접하십시오. 제로지식 증명을 생성하려면 증명 키가 필요합니다. 이는 ZKP의 가장 민감한 부분인데, 증명 키를 생성하는 데 사용된 소스 데이터를 사용하면 누구든지 가짜 증명을 생성할 수 있습니다. 이 소스 데이터를 "유독 폐기물"이라고 하며, 이를 폐기해야 합니다. 이러한 이유로 증명 키 생성을 위해 "의식"이 있습니다. 의식에는 많은 구성원이 참여하며 각 구성원이 증명 키에 기여합니다. 악의가 없는 하나의 구성원만 있으면 유효한 증명 키를 생성할 수 있습니다. 개인 입력, 공개 입력 및 증명 키를 사용하여 ZKP 시스템은 회로를 실행하고 증명 및 출력을 생성할 수 있습니다.

![이미지](/assets/img/2024-06-30-UnderstandingZero-KnowledgeProofsThroughtheSourceCodeofTornadoCash_3.png)

증명 키에 대한 유효성 검사용 검증 키가 있습니다. 검증 시스템은 공개 입력, 출력 및 검증 키를 사용하여 증명을 검증할 수 있습니다.

![이미지](/assets/img/2024-06-30-UnderstandingZero-KnowledgeProofsThroughtheSourceCodeofTornadoCash_4.png)

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

스낵제이에스는 세레모니를 통해 증명 키 및 검증 키를 생성하고 증명을 생성하며 해당 증명을 확인하는 기능을 제공하는 풀 피처드 도구입니다. 또한 검증을 위한 스마트 계약을 생성할 수 있어 다른 계약에서 제로 지식 증명을 확인하는 데 사용할 수 있습니다. 더 자세한 정보는 스낵제이에스 문서를 확인해 주세요.

지금, 토네이도 캐시(TC)가 어떻게 작동하는지 이해할 수 있는 모든 것이 준비되었습니다. TC 계약에 1이더를 예금할 때 커밋먼트 해시를 제공해야 합니다. 이 커밋먼트 해시는 Merkle 트리에 저장됩니다. 다른 계정으로 이 1이더를 인출할 때는 2개의 제로 지식 증명을 제공해야 합니다. 첫 번째는 Merkle 트리에 당신의 커밋먼트가 포함됨을 증명하는 것입니다. 이 증명은 Merkle 증명의 제로 지식 증명입니다. 하지만 이것만으로 충분하지 않습니다. 당신은 이 1이더를 한 번만 인출할 수 있어야 합니다. 그래서 당신은 커밋먼트에 대한 유일한 널리파를 제공해야 합니다. 계약은 이 널리파를 저장함으로써 당신이 예금한 돈을 두 번 이상 인출하지 못하도록 보장합니다.

널리파의 고유성은 커밋먼트 생성 방법에 의해 보장됩니다. 커밋먼트는 널리파와 비밀을 해싱하여 생성됩니다. 널리파를 변경하면 커밋먼트도 변경되기 때문에 하나의 널리파는 하나의 커밋먼트에만 사용할 수 있습니다. 해싱의 단방향성으로 인해 커밋먼트와 널리파를 연결할 수는 없지만 이에 대한 제로 지식 증명을 생성할 수 있습니다.

![이미지](/assets/img/2024-06-30-UnderstandingZero-KnowledgeProofsThroughtheSourceCodeofTornadoCash_5.png)

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

이론을 이해한 후에는 TC의 인출 회로가 어떻게 생겼는지 살펴보겠습니다:

```js
include "../node_modules/circomlib/circuits/bitify.circom";
include "../node_modules/circomlib/circuits/pedersen.circom";
include "merkleTree.circom";
// Pedersen(nullifier + secret)를 계산합니다.
template CommitmentHasher() {
    signal input nullifier;
    signal input secret;
    signal output commitment;
    signal output nullifierHash;
    component commitmentHasher = Pedersen(496);
    component nullifierHasher = Pedersen(248);
    component nullifierBits = Num2Bits(248);
    component secretBits = Num2Bits(248);
    nullifierBits.in <== nullifier;
    secretBits.in <== secret;
    for (var i = 0; i < 248; i++) {
        nullifierHasher.in[i] <== nullifierBits.out[i];
        commitmentHasher.in[i] <== nullifierBits.out[i];
        commitmentHasher.in[i + 248] <== secretBits.out[i];
    }
    commitment <== commitmentHasher.out[0];
    nullifierHash <== nullifierHasher.out[0];
}
// 주어진 비밀과 널리파에 해당하는 묵사가 예금의 Merkle tree에 포함되어 있는지 확인합니다.
template Withdraw(levels) {
    signal input root;
    signal input nullifierHash;
    signal private input nullifier;
    signal private input secret;
    signal private input pathElements[levels];
    signal private input pathIndices[levels];
    component hasher = CommitmentHasher();
    hasher.nullifier <== nullifier;
    hasher.secret <== secret;
    hasher.nullifierHash === nullifierHash;
    component tree = MerkleTreeChecker(levels);
    tree.leaf <== hasher.commitment;
    tree.root <== root;
    for (var i = 0; i < levels; i++) {
        tree.pathElements[i] <== pathElements[i];
        tree.pathIndices[i] <== pathIndices[i];
    }
}
component main = Withdraw(20);
```

첫 번째 템플릿은 CommitmentHasher입니다. 널리파 와 시크릿이라고하는 두 개의 랜덤한 248비트 숫자를 입력으로 사용합니다. 템플릿은 널리 파해시 및 커밋먼트 해시를 계산합니다. 이는 널리파와 시크릿의 해시입니다.

두 번째 템플릿은 Withdraw 자체입니다. 해당 템플릿에는 Merkle 루트와 널리 파 해시와 같은 2개의 공개 입력이 있습니다. Merkle root는 Merkle proof를 확인하기 위해 필요하며, 널리파 해시는 스마트 계약에서 저장해야 합니다. 개인 입력 매개 변수는 널리파, 시크릿 및 Merkle proof의 pathElements 및 pathIndices입니다. 회로는 널리파를 검사하여 해당 스마트 계약에서 커밋먼트를 생성하고 지정된 Merkle proof를 검사합니다. 모든 것이 정상이면 TC 스마트 계약에서 확인할 수 있는 제로 지식 증명이 생성됩니다.

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

저장소의 contracts 폴더에서 스마트 계약을 찾을 수 있어요. Verifier는 회로에서 생성돼요. Tornado 계약에서는 주어진 널리파 해시 및 Merkle 루트의 ZKP를 확인하는 데 사용돼요.

계약을 사용하는 가장 쉬운 방법은 명령줄 인터페이스를 사용하는 거예요. 이 인터페이스는 JavaScript로 작성되었으며 소스 코드가 비교적 간단해요. 여기에서 매개변수와 ZKP가 생성되고 스마트 계약을 호출하는 데 사용되는 곳을 쉽게 찾을 수 있어요.

제로지식증명은 암호화 세상에서 비교적 새로운 기술이에요. 그 뒤에 숨어있는 수학은 정말 복잡하고 이해하기 어려운데, snarkjs와 circom과 같은 도구를 사용하면 쉽게 사용할 수 있어요. 이 기술에 대해 이 기사가 도움이 되었으면 좋겠어요. 다음 프로젝트에서 ZKP를 사용할 수 있게 되길 바라요.

즐거운 코딩 하세요...

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

업데이트: 해당 주제에 대한 새로운 기사가 있어요:

그리고 Tornado Cash의 소스 코드를 기반으로 한 익명 투표를 위한 JavaScript 라이브러리를 만드는 방법에 대한 또 다른 기사가 있습니다. circom, Solidity 및 JavaScript 코드를 사용한 단계별 자습서입니다:

그리고 이를 기반으로한 투표 시스템을 어떻게 만들었는지 설명하고 있어요: