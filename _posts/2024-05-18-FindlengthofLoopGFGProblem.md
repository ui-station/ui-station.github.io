---
title: "루프의 길이 찾기 GFG 문제"
description: ""
coverImage: "/assets/img/2024-05-18-FindlengthofLoopGFGProblem_0.png"
date: 2024-05-18 15:13
ogImage:
  url: /assets/img/2024-05-18-FindlengthofLoopGFGProblem_0.png
tag: Tech
originalTitle: "Find length of Loop (GFG Problem)"
link: "https://medium.com/@Mohd_Aamir_17/find-length-of-loop-gfg-problem-34860348575a"
---

## 소개

연결 리스트는 컴퓨터 과학에서 중요한 데이터 구조입니다. 노드로 구성되어 있으며 각 노드는 데이터와 순서상 다음 노드를 가리키는 참조(또는 링크)를 포함합니다. 연결 리스트의 일반적인 문제 중 하나는 루프를 감지하고 해당 루프의 노드 수를 계산하는 것입니다. 이 기사에서는 연결 리스트 내의 루프에 있는 노드 수를 세는 간단하면서 효과적인 알고리즘에 대해 살펴보겠습니다.

## 문제

연결 리스트의 루프는 노드의 다음 포인터가 리스트 내 이전 노드 중 하나를 가리킬 때 발생하여 순환이 생성됩니다. 이러한 루프를 감지하는 것은 탐색 중 무한 루프를 방지하기 위해 중요합니다. 루프를 감지한 후에는 루프 내 노드 수를 세는 것이 구조를 이해하거나 추가 처리를 위한 여러 목적에 유용할 수 있습니다.

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

## 알고리즘

루프 내 노드 수를 세려면 먼저 루프를 감지해야 합니다. 이를 위해 Floyd의 순환 찾기 알고리즘(토끼와 거북이 알고리즘으로도 알려짐)을 사용할 수 있습니다. 루프가 감지되면 다음 단계를 통해 루프 내 노드 수를 세는 방법을 사용할 수 있습니다.

다음은 루프 내 노드를 세는 코드 스니펫입니다:

```js
int count = 1;
Node current = loopNode;

while (current.next != loopNode) {
    current = current.next;
    count++;
}
return count;
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

아래는 해당 알고리즘을 자세히 살펴보겠습니다:

- 초기화:

  - 루프노드 자체가 루프의 일부이기 때문에 count 카운터를 1로 초기화합니다.
  - current 포인터를 루프노드로 설정합니다.

- 순회 및 카운팅:

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

- 현재 포인터를 사용하여 루프를 트래버스(순회)합니다.
- 각 노드마다 다음 노드로 이동하고 카운터를 증가시킵니다.
- 이 과정을 반복하여 루프 노드에 다시 도달할 때까지 진행하여 사이클을 완료합니다.

- 카운트 반환:

- 루프가 완전히 트래버스(순회)된 후, 카운트에는 루프의 노드 수가 저장되며 이를 반환합니다.

## 예시 해설

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

다음과 같이 루프를 가진 링크드 리스트를 고려해 봅시다:

![image](/assets/img/2024-05-18-FindlengthofLoopGFGProblem_0.png)

- 여기서 노드 5는 노드 3을 가리키므로 루프가 생성됩니다.
- loopNode이 노드 3으로 감지되었다고 가정합니다.
- 우리의 알고리즘은 current를 노드 3으로 초기화하고 카운팅을 시작합니다.
- 탐색은 다음과 같이 진행됩니다: 3 - 4 - 5 - 6 - 7 - 8 - 3.
- 각 단계마다 count가 증가하여 최종 count는 6이 됩니다.

Markdown 형식으로 테이블 태그를 변경합니다.

```js
static int countNodesinLoop(ListNode head) {
    if (head == null) {
        return 0;
    }

    ListNode slow = head;
    ListNode fast = head;

    int count = 1 ;

    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;

        if (slow == fast) {
            return countLoopLength(slow);
        }
    }

    return 0;
}

private static int countLoopLength(ListNode loopNode) {
    int count = 1;
    ListNode current = loopNode;
    while (current.next != loopNode) {
        current = current.next;
        count++;
    }
    return count;
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

## 실용적인 고려 사항

- 루프 감지: 위 코드는 루프 감지가 이미 완료되었고 loopNode가 제공되었다고 가정합니다. Floyd의 순환 찾기 알고리즘을 사용하여 이를 달성할 수 있습니다.
- 예외 경우: 루프가 없거나 루프가 하나의 노드로만 구성된 경우를 고려해야 합니다. 알고리즘은 이러한 사례를 민첩하게 처리해야 합니다.
- 복잡성: 이 루프 카운팅의 시간 복잡도는 O(n)입니다. 여기서 n은 루프 내 노드의 수입니다. 공간 복잡도는 추가적인 공간을 상수로 사용하므로 O(1)입니다.

## 결론

연결 리스트 내의 루프에서 노드를 계산하는 것은 직관적인 순회 방법을 사용하여 쉽게 수행할 수 있는 기본 작업입니다. 이 알고리즘을 이해하고 구현함으로써 연결 리스트 조작에 대한 이해를 높이고 데이터 구조에서 더 복잡한 문제를 대비할 수 있습니다. 이 지식을 바탕으로 연결 리스트의 루프를 효율적으로 처리하여 알고리즘을 견고하고 신뢰성 있게 만들 수 있습니다.
