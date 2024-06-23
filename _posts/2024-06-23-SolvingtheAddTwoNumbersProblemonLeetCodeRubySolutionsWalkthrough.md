---
title: "리트코드 두 수 더하기 문제 해결 - 루비 솔루션 완벽 가이드"
description: ""
coverImage: "/assets/img/2024-06-23-SolvingtheAddTwoNumbersProblemonLeetCodeRubySolutionsWalkthrough_0.png"
date: 2024-06-23 20:53
ogImage: 
  url: /assets/img/2024-06-23-SolvingtheAddTwoNumbersProblemonLeetCodeRubySolutionsWalkthrough_0.png
tag: Tech
originalTitle: "Solving the ‘Add Two Numbers’ Problem on LeetCode — Ruby Solutions Walkthrough"
link: "https://medium.com/@AlexanderObregon/solving-the-add-two-numbers-problem-on-leetcode-ruby-solutions-walkthrough-46064c2f10a9"
---


<img src="/assets/img/2024-06-23-SolvingtheAddTwoNumbersProblemonLeetCodeRubySolutionsWalkthrough_0.png" />

# 소개

LeetCode의 'Add Two Numbers' 문제는 두 개의 음이 아닌 정수를 나타내는 비어 있지 않은 두 연결 리스트를 더하는 것을 포함합니다. 각 목록의 숫자는 역순으로 저장되어 있으며, 각 노드에는 한 자리 숫자가 포함되어 있습니다. 이 작업은 두 숫자를 더하고 합계를 연결 리스트로 반환하는 것입니다.

Ruby는 간결한 구문과 동적 기능으로 이 문제를 효율적으로 해결하는 여러 가지 방법을 제공합니다. 이 안내서에서는 'Add Two Numbers' 문제를 해결하기 위한 4가지 Ruby 솔루션을 살펴보겠습니다. 이 가이드의 끝에서 각 방법의 메커니즘과 효율성, 효과에 대한 이해를 얻게 될 것입니다. 코드 주석 및 각 방법에 대한 자세한 단계별 설명은 결론 이후에 제공됩니다.

<div class="content-ad"></div>

# 문제 설명

```js
# 단일 연결 리스트에 대한 정의
# class ListNode
#     attr_accessor :val, :next
#     def initialize(val = 0, _next = nil)
#         @val = val
#         @next = _next
#     end
# end
```

## 루비에서의 함수 시그니처:

```js
# @param {ListNode} l1
# @param {ListNode} l2
# @return {ListNode}
def add_two_numbers(l1, l2)
    
end
```

<div class="content-ad"></div>

## 예시 1:

<img src="/assets/img/2024-06-23-SolvingtheAddTwoNumbersProblemonLeetCodeRubySolutionsWalkthrough_1.png" />

- 입력: l1 = [2,4,3], l2 = [5,6,4]
- 출력: [7,0,8]
- 설명: 342 + 465 = 807.

## 예시 2:

<div class="content-ad"></div>


- 입력: l1 = [0], l2 = [0]
- 출력: [0]

## 예제 3:

- 입력: l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
- 출력: [8,9,9,9,0,0,0,1]

## 제약 조건


<div class="content-ad"></div>

- 각 연결 목록의 노드 수는 [1, 100] 범위 내에 있습니다.
- 0 ≤ Node.val ≤ 9
- 목록이 선행 0이 없는 숫자를 나타낸다는 것이 보장됩니다.

# 해법 1: 전달하는 접근 방법 및 캐리 처리

이 간단한 접근 방법은 두 연결 목록을 반복하며 해당하는 숫자를 더하고, 9보다 큰 합계에 대해 캐리를 처리하는 것을 포함합니다.

## 루비 코드:

<div class="content-ad"></div>

```js
def add_two_numbers(l1, l2)
    dummy = ListNode.new(0)
    current = dummy
    carry = 0

    while l1 || l2
        x = l1 ? l1.val : 0
        y = l2 ? l2.val : 0
        sum = carry + x + y
        carry = sum / 10
        current.next = ListNode.new(sum % 10)
        current = current.next
        l1 = l1.next if l1
        l2 = l2.next if l2
    end

    current.next = ListNode.new(carry) if carry > 0
    dummy.next
end
```

## 시간 및 공간 복잡도

- 시간 복잡도: O(n). 이 알고리즘은 연결 리스트 l1과 l2를 정확히 한 번씩 훑습니다. 각 반복에서 각 목록에서 하나의 노드를 처리하고 고정량의 작업(덧셈, carry 처리 및 노드 생성)을 수행합니다. 따라서 시간 복잡도는 긴 목록의 길이에 대해 선형입니다.
- 공간 복잡도: O(n). 공간 복잡도는 결과 연결 리스트를 저장하는 데 필요한 공간에 의해 결정됩니다. 합의 각 자릿수에 대해 새로운 노드가 생성되므로 공간 복잡도는 결과 목록의 길이에 비례하며 O(n)입니다. carry, dummy 및 current와 같은 변수에 사용하는 보조 공간은 상수(O(1))입니다. 그러나 새로운 연결 리스트가 전체 O(n) 공간 복잡도에 기여합니다.

# Solution 2: 재귀적 접근

<div class="content-ad"></div>

이 접근 방식은 재귀를 사용하여 숫자와 올림을 처리하며, 더 작은 재귀 호출로 반복 논리를 단순화합니다.

## 루비 코드:

```js
def add_two_numbers(l1, l2, carry = 0)
    return ListNode.new(carry) if l1.nil? && l2.nil? && carry > 0
    return nil if l1.nil? && l2.nil?

    sum = carry
    sum += l1.val if l1
    sum += l2.val if l2

    result = ListNode.new(sum % 10)
    result.next = add_two_numbers(l1 ? l1.next : nil, l2 ? l2.next : nil, sum / 10)
    result
end
```

## 시간 및 공간 복잡도

<div class="content-ad"></div>

- 시간 복잡도: O(n). 반복적인 방법과 유사하게, 재귀적인 해결책은 입력 리스트의 각 노드를 한 번씩 처리합니다. 각 재귀 호출은 하나의 숫자 더하기를 처리하고 l1 및 l2의 다음 노드로 진행하며, 시간 복잡도는 더 긴 리스트의 길이에 비례하여 선형적입니다.
- 공간 복잡도: O(n). 공간 복잡도에는 새 연결 리스트와 재귀 호출 스택에 필요한 공간이 모두 포함됩니다. 각 재귀 호출은 스택 공간을 소비하므로 재귀 깊이가 더 긴 리스트의 길이와 동일하기 때문에 재귀 깊이가 O(n)이 되어 O(n) 공간 복잡도가 발생합니다. 또한 결과를 저장하기 위해 생성된 새 연결 리스트가 O(n) 공간 복잡도에 기여합니다. 따라서, 호출 스택과 새 연결 리스트에 대한 총 공간 복잡도는 O(n)입니다.

# Solution 3: Using Enumerators for Clean Iteration

이 접근 방식은 루비의 enumerators를 활용하여 연결된 리스트를 반복하는 코드를 더 깔끔하고 관용적으로 만듭니다.

## 루비 코드:

<div class="content-ad"></div>

```js
def add_two_numbers(l1, l2)
    dummy = ListNode.new(0)
    current = dummy
    carry = 0

    e1 = to_enum(l1)
    e2 = to_enum(l2)

    loop do
        begin
            x = e1.next
        rescue StopIteration
            x = ListNode.new(0)
        end

        begin
            y = e2.next
        rescue StopIteration
            y = ListNode.new(0)
        end

        sum = carry + x.val + y.val
        carry = sum / 10
        current.next = ListNode.new(sum % 10)
        current = current.next

        break if x.next.nil? && y.next.nil? && carry.zero?
    end

    current.next = ListNode.new(carry) if carry > 0
    dummy.next
end

def to_enum(node)
    Enumerator.new do |yielder|
        while node
            yielder << node
            node = node.next
        end
    end
end
```

## 시간 복잡도와 공간 복잡도

- 시간 복잡도: O(n). 이 알고리즘은 입력 연결 리스트의 각 노드를 정확히 한 번씩 처리합니다. 각 노드마다 상수 시간의 작업(덧셈, carry 처리 및 노드 생성)을 수행합니다. Enumerators의 사용은 각 노드가 더 긴 목록의 길이에 비례하는 선형 시간 내에 처리된다는 사실을 변경하지 않습니다.
- 공간 복잡도: O(n). 공간 복잡도는 결과 연결 리스트를 저장하는 데 필요한 공간에 의해 결정됩니다. 합의 각 자릿수마다 새로운 노드가 생성되므로 공간 복잡도는 결과 목록의 길이에 비례하며 O(n)입니다. carry, dummy 및 current와 같은 변수에 사용된 보조 공간은 상수(O(1))이지만 새로운 연결 리스트가 전체적인 O(n) 공간 복잡도를 차지합니다. Enumerators의 사용은 최소한의 오버헤드를 추가하며 공간 복잡도에 중대한 영향을 미치지 않습니다.

# 솔루션 4: 입력 리스트의 위치 수정을 통한 해결 방법


<div class="content-ad"></div>

긴 입력 목록의 노드를 변경하여 결과 목록을 만드는 추가 노드를 생성하지 않고 재사용하는 방식으로 처리합니다.

## 루비 코드:

```js
def add_two_numbers(l1, l2)
    carry = 0
    head = l1
    prev = nil

    while l1 && l2
        sum = l1.val + l2.val + carry
        carry = sum / 10
        l1.val = sum % 10
        prev = l1
        l1 = l1.next
        l2 = l2.next
    end

    if l2
        prev.next = l2
        l1 = l2
    end

    while l1
        sum = l1.val + carry
        carry = sum / 10
        l1.val = sum % 10
        prev = l1
        l1 = l1.next
    end

    if carry > 0
        prev.next = ListNode.new(carry)
    end

    head
end
```

## 시간 및 공간 복잡도

<div class="content-ad"></div>

- 시간 복잡도: O(n). 이 알고리즘은 입력 연결 리스트의 각 노드를 정확히 한 번 처리하므로, 긴 리스트의 길이에 비례하여 선형 시간 복잡도를 갖습니다.
- 공간 복잡도: O(1). 알고리즘은 결과를 위해 추가 공간을 할당하지 않고 기존 노드를 그대로 수정하므로, 공간 복잡도는 상수입니다. 사용되는 추가 공간은 몇 가지 보조 변수(carry, prev, head) 뿐이며 입력 크기에 따라 달라지지 않습니다.

# 결론 및 비교

분석 결과, 네 가지 루비 메서드의 시간 복잡도는 모두 유사하며, 각각 입력 연결 리스트를 한 번만 순회하는 작업이 포함되어 있습니다. 결과 연결 리스트 및 carry를 처리하는 전략에 따라 솔루션 간 공간 복잡도가 달라집니다.

## 솔루션 1: Carry 처리를 포함한 반복적 방법

<div class="content-ad"></div>

- 장점: 직관적이고 이해하기 쉽습니다. 두 숫자의 덧셈을 처리하는 데 효율적으로 작동하며 리스트를 반복하고 루프 내에서 캐리를 처리합니다. 논리가 명확하고 간단하여 구현 및 디버깅이 쉽습니다.
- 단점: 결과를 저장하기 위해 새 연결 리스트를 생성해야 하므로 O(n) 공간 복잡성을 가집니다. 이 방법은 인플레이스 수정 솔루션보다 공간을 덜 효율적으로 사용합니다.

## 솔루션 2: 재귀적 접근

- 장점: 명확하고 간결한 해결책을 제공합니다. 반복 논리를 더 작은 조각으로 나누어 처리하는 재귀 호출을 통해 단순화합니다. 코드가 깨끗하고 이해하기 쉬워 재귀를 선호하는 사람들에게 좋은 선택입니다.
- 단점: 재귀 스택으로 인해 공간 복잡성이 높아져 O(n)의 공간을 사용할 수 있습니다. 이는 매우 큰 입력 크기에 대한 제약으로, 스택 오버플로우 문제를 일으킬 수 있습니다.

## 솔루션 3: 깔끔한 반복을 위한 열거자 사용

<div class="content-ad"></div>

- 장점: Ruby의 enumerators를 활용하여 더 깔끔하고 관용적인 접근을 제공합니다. 명시적인 확인이 줄어들고 반복을 우아하게 처리합니다. 이 해결책은 Ruby의 고수준 구조를 이해하는 사람들에게 적합합니다.
- 단점: 첫 번째 해결책에 비해 약간 더 복잡한 논리를 요구하며 Ruby의 enumerators에 대한 숙련도가 필요합니다. 공간 복잡도는 새로운 연결 리스트를 만들어야 하기 때문에 O(n)으로 유지됩니다.

## 솔루션 4: 입력 리스트의 자리수 수정

- 장점: 이 해결책은 입력 리스트의 노드를 그 자리에서 수정하여 공간을 가장 효율적으로 활용합니다. 기존 노드를 재사용하고 추가적인 노드를 만들지 않음으로써 O(1)의 고정된 공간 복잡도를 달성합니다. 메모리 소비를 과도하게 막으면서 대량의 입력 크기를 처리하기에 이상적입니다.
- 단점: 자리수 수정을 위한 논리는 더 복잡하며 노드 포인터와 값에 대한 주의 깊은 처리가 필요합니다. 다른 해결책들에 비해 오류 발생 가능성이 높고 디버깅하기 어려울 수 있습니다.

## 요약

<div class="content-ad"></div>

- 해결책 1: 캐리 처리를 포함한 반복적 접근은 간단함과 효율성 사이의 균형을 제공하여 문제를 이해하는 데 좋은 시작점이 될 수 있습니다.
- 해결책 2: 재귀적 접근은 명확하고 간결한 해결책을 제공하지만 큰 입력에 대해 더 높은 공간 복잡성을 가집니다.
- 해결책 3: 깨끗한 반복을 위한 Enumerators 사용은 Ruby의 고수준 구조를 활용하여 더 깔끔한 접근을 제공하지만 반복적인 접근과 동일한 공간 복잡성을 가집니다.
- 해결책 4: 입력 리스트의 내부 수정은 가장 공간을 효율적으로 활용할 수 있으며 큰 입력 크기에 적합하지만 더 신중한 구현이 필요합니다.

네 개의 해결책 모두 장단점이 있지만, 공간 효율성 면에서 해결책 4가 최적의 선택으로 돋보입니다. 입력 리스트를 그대로 수정함으로써 메모리 사용량을 최소화하고, 대규모 데이터셋을 다룰 때 특히 유리합니다. 그러나 더 세심한 구현 방식이 필요합니다. 포인터 관리와 내부 수정에 익숙한 사람들에게는 자원을 가장 효율적으로 사용할 수 있는 해결책 4를 추천합니다.

- LeetCode — 두 수 더하기
- Ruby 문서

읽어 주셔서 감사합니다! 이 안내서가 도움이 되었다면, 하이라이트를 주거나 박수를 치거나 답글을 달거나 Twitter/X를 통해 연결해 주시면 정말 감사하겠습니다. 이는 매우 감사히 받아들이며, 이와 같은 콘텐츠를 계속 무료로 제공하는 데 도움이 됩니다!

<div class="content-ad"></div>

제 다른 Leetcode Walkthrough 목록도 확인해보세요:

- Two Sum 문제
- Palindrome Number 문제

# 주석이 달린 코드 및 단계별 설명

## 솔루션 1: 캐리 처리를 포함한 반복적 접근

<div class="content-ad"></div>

이 방법은 두 연결 리스트를 반복하면서 해당하는 숫자를 합쳐서 9보다 큰 경우를 위해 캐리를 관리합니다. 결과를 저장하기 위해 새로운 연결 리스트를 만듭니다. 이 방법은 구현하고 이해하기 쉽기 때문에 직관적입니다. 두 리스트를 동시에 탐색하면 모든 숫자가 정확하게 처리되며 존재하는 경우 최종 캐리도 처리합니다.

```js
def add_two_numbers(l1, l2)
    dummy = ListNode.new(0)  # 결과 리스트의 헤드 역할을 할 더미 노드 생성
    current = dummy  # 결과 리스트를 구축하기 위한 포인터 초기화
    carry = 0  # 캐리 초기화

    while l1 || l2  # 두 리스트가 모두 완전히 처리될 때까지 순회
        x = l1 ? l1.val : 0  # l1의 현재 노드 값 또는 l1이 nil인 경우 0 반환
        y = l2 ? l2.val : 0  # l2의 현재 노드 값 또는 l2가 nil인 경우 0 반환
        sum = carry + x + y  # 현재 값과 캐리의 합 계산
        carry = sum / 10  # 캐리 업데이트
        current.next = ListNode.new(sum % 10)  # 숫자 값으로 새 노드 생성하고 결과 리스트에 연결
        current = current.next  # 결과 리스트의 다음 노드로 이동
        l1 = l1.next if l1  # l1이 있으면 l1의 다음 노드로 이동
        l2 = l2.next if l2  # l2가 있으면 l2의 다음 노드로 이동
    end

    current.next = ListNode.new(carry) if carry > 0  # 나머지 캐리가 있는 경우, 해당 값을 갖는 새 노드 생성
    dummy.next  # 결과 리스트의 헤드 반환
end
```

단계별 설명

- 단계 1: 결과 리스트의 헤드로 사용할 더미 노드와 결과 리스트를 구축하기 위한 현재 포인터를 초기화합니다. 캐리를 0으로 초기화합니다.
- 단계 2: l1 및 l2를 동시에 탐색합니다.
- 단계 3: 현재 노드의 값(x 및 y)을 추출합니다. 리스트가 완전히 순회된 경우, 값으로 0을 사용합니다.
- 단계 4: x, y 및 캐리의 합을 계산합니다.
- 단계 5: 합을 10으로 나누어 캐리를 업데이트합니다.
- 단계 6: 값이 sum % 10인 새 노드를 만들고 결과 리스트에 연결합니다.
- 단계 7: 현재 포인터를 새 노드로 이동합니다.
- 단계 8: l1 및 l2 포인터를 다음 노드로 이동합니다(존재하는 경우).
- 단계 9: 반복문을 종료한 후, 남은 캐리가 있는지 확인합니다. 있다면 해당 값을 갖는 새 노드를 만들고 결과 리스트에 연결합니다.
- 단계 10: 결과 리스트의 헤드로 더미 노드의 다음을 반환합니다.

<div class="content-ad"></div>

## 해결 방법 2: 재귀 접근 방식

이 방법은 숫자 및 캐리의 덧셈을 처리하기 위해 재귀를 사용하여 반복 논리를 작은 재귀 호출로 분해하여 단순화합니다. 재귀를 사용하면 덧셈 과정을 효율적으로 처리하므로 코드가 간결하고 명확해집니다. 이 함수는 각 재귀 호출에서 각 입력 목록의 하나의 숫자를 처리하고 이들을 캐리와 함께 더한 다음 다음 노드로 진행합니다. 이 방법은 상태를 관리하기 위해 호출 스택을 효과적으로 활용하여 재귀를 선호하는 사람들에게 좋은 선택입니다.

```js
def add_two_numbers(l1, l2, carry = 0)
    return ListNode.new(carry) if l1.nil? && l2.nil? && carry > 0  # 기본 사례: 모두 nil이면서 캐리 값이 있는 경우 빈 노드를 반환
    return nil if l1.nil? && l2.nil?  # 기본 사례: 모두 nil이면서 캐리 값이 없는 경우 빈 노드를 반환

    sum = carry  # 캐리 값으로 시작
    sum += l1.val if l1  # l1의 현재 노드 값 추가(현재 노드가 있는 경우)
    sum += l2.val if l2  # l2의 현재 노드 값 추가(현재 노드가 있는 경우)

    result = ListNode.new(sum % 10)  # 숫자 값으로 새 노드 생성
    result.next = add_two_numbers(l1 ? l1.next : nil, l2 ? l2.next : nil, sum / 10)  # 다음 노드 및 캐리로 재귀 호출
    result  # 결과 노드 반환
end
```

단계별 설명

<div class="content-ad"></div>

- 단계 1: 기본 경우: l1과 l2가 모두 nil이고 캐리가 없는 경우 nil을 반환합니다.
- 단계 2: 현재 노드(l1 및 l2)와 캐리의 값들을 더합니다.
- 단계 3: 합과 새로운 캐리를 계산합니다.
- 단계 4: 합 % 10 값을 갖는 새로운 노드를 생성합니다.
- 단계 5: 새로운 캐리를 전달하여 l1과 l2의 다음 노드로 재귀적으로 함수를 호출합니다.
- 단계 6: 재귀 호출 결과를 새로운 노드의 다음에 연결합니다.
- 단계 7: 새롭게 생성된 노드를 반환합니다.

## 솔루션 3: 깨끗한 반복을 위한 Enumerators 사용

이 접근 방식은 Ruby의 Enumerators를 활용하여 링크드 리스트를 반복하면서 코드를 더 깔끔하고 Ruby다운 방식으로 만듭니다. Enumerators는 명시적인 루프를 피하고 코드를 더 읽기 쉽게 만들어주는 Ruby스러운 반복 처리 방식을 제공합니다. Enumerators를 사용함으로써이 솔루션은 각 숫자가 올바르게 합산되고 처리되도록 하고 불필요하게 새 노드를 생성하지 않고 리스트를 탐색하고 캐리를 관리하는 과정을 간소화합니다.

```js
def add_two_numbers(l1, l2)
    dummy = ListNode.new(0)  # 결과 리스트의 헤드로 사용할 더미 노드를 만듭니다
    current = dummy  # 결과 리스트를 구성하는 포인터를 초기화합니다
    carry = 0  # 캐리를 초기화합니다

    e1 = to_enum(l1)  # l1에 대한 이너레이터를 생성합니다
    e2 = to_enum(l2)  # l2에 대한 이너레이터를 생성합니다

    loop do
        begin
            x = e1.next  # e1에서 다음 값 가져오기
        rescue StopIteration
            x = ListNode.new(0)  # e1이 소진된 경우 0 사용
        end

        begin
            y = e2.next  # e2에서 다음 값 가져오기
        rescue StopIteration
            y = ListNode.new(0)  # e2가 소진된 경우 0 사용
        end

        sum = carry + x.val + y.val  # 현재 값 및 캐리의 합 구하기
        carry = sum / 10  # 캐리 업데이트
        current.next = ListNode.new(sum % 10)  # 숫자 값으로 새 노드 생성하고 결과 리스트에 연결
        current = current.next  # 결과 리스트의 다음 노드로 이동

        break if x.next.nil? && y.next.nil? && carry.zero?  # 두 리스트가 모두 소진되고 캐리가 남아있지 않은 경우 반복문 종료

    end

    current.next = ListNode.new(carry) if carry > 0  # 남아 있는 캐리가 있으면 이를 위한 새 노드 생성
    dummy.next  # 결과 리스트의 헤드 반환
end

def to_enum(node)
    Enumerator.new do |yielder|
        while node
            yielder << node  # 각 노드를 반환합니다
            node = node.next  # 다음 노드로 이동합니다
        end
    end
end
```

<div class="content-ad"></div>

단계별 설명

- 단계 1: 연결 리스트를 열거자(enumerator)로 변환하는 helper method인 to_enum을 생성합니다.
- 단계 2: 결과 리스트의 머리 역할을 하는 더미(dummy) 노드와 결과 리스트를 구성할 현재 포인터를 초기화합니다. 그리고 carry를 0으로 초기화합니다.
- 단계 3: 열거자 e1과 e2를 사용하여 l1과 l2를 반복(iterate)합니다.
- 단계 4: e1과 e2에서 다음 값을 가져오려 시도합니다. 열거자가 소진된 경우 해당 값을 0으로 취급합니다.
- 단계 5: 현재 값과 carry의 합을 계산합니다.
- 단계 6: 합을 10으로 나누어 carry를 업데이트합니다.
- 단계 7: sum % 10의 값으로 새 노드를 생성하고 이를 결과 리스트에 연결합니다.
- 단계 8: 현재 포인터를 새 노드로 이동합니다.
- 단계 9: 두 열거자가 모두 소진되고 carry가 남아있지 않은 경우 루프를 탈출합니다.
- 단계 10: 루프를 탈출한 후 남은 carry가 있는지 확인합니다. 있다면 carry 값을 가진 새 노드를 만들고 결과 리스트에 연결합니다.
- 단계 11: 결과 리스트의 머리를 나타내는 더미 노드의 다음을 반환합니다.

## 솔루션 4: 입력 리스트의 장소를 변경하여 수정

이 솔루션은 입력 리스트 중 긴 쪽의 노드를 그대로 수정하여 노드를 재사용하고 결과 리스트의 추가 노드를 생성하는 것을 피합니다. 기존 노드를 직접 업데이트함으로써 공간 사용을 최소화하고 O(1)의 상수 공간 복잡도를 달성합니다. 이 방법은 노드 포인터와 값들을 정확하게 처리하여 올바른 결과를 보장하기 위해 주의 깊은 처리가 필요합니다. 메모리 소비를 줄이면서 효율성을 유지하므로 대량의 입력 크기를 다루는 데 특히 유리합니다.

<div class="content-ad"></div>

```js
def add_two_numbers(l1, l2)
    carry = 0  # 캐리를 0으로 초기화합니다.
    head = l1   # l1의 헤드부터 시작합니다.
    prev = nil  # 이전 포인터를 초기화합니다.

    while l1 && l2  # 두 리스트를 모두 탐색합니다.
        sum = l1.val + l2.val + carry  # 현재 값과 캐리의 합을 계산합니다.
        carry = sum / 10  # 캐리를 업데이트합니다.
        l1.val = sum % 10  # l1의 현재 노드 값을 업데이트합니다.
        prev = l1  # 이전 포인터를 이동합니다.
        l1 = l1.next  # l1의 다음 노드로 이동합니다.
        l2 = l2.next  # l2의 다음 노드로 이동합니다.
    end

    if l2  # l2가 더 긴 경우, 나머지 l2를 l1에 추가합니다.
        prev.next = l2
        l1 = l2
    end

    while l1  # 더 긴 리스트의 나머지 부분을 처리합니다.
        sum = l1.val + carry  # 현재 값과 캐리의 합을 계산합니다.
        carry = sum / 10  # 캐리를 업데이트합니다.
        l1.val = sum % 10  # 현재 노드 값을 업데이트합니다.
        prev = l1  # 이전 포인터를 이동합니다.
        l1 = l1.next  # 다음 노드로 이동합니다.
    end

    if carry > 0  # 남은 캐리가 있는 경우, 끝에 새 노드를 추가합니다.
        prev.next = ListNode.new(carry)
    end

    head  # 수정된 리스트의 헤드를 반환합니다.
end
```

단계별 설명

- 단계 1: 캐리를 0으로 초기화하고 l1의 시작 부분을 head로 설정합니다. 이전 노드를 추적하기 위해 prev를 사용합니다.
- 단계 2: l1과 l2를 반복하여 각 노드 쌍에 대해:
    - 노드 값 및 캐리의 합을 계산합니다.
    - l1의 노드 값을 sum % 10으로 업데이트합니다.
    - carry를 sum / 10으로 업데이트합니다.
    - prev를 l1으로 이동한 다음 l1과 l2를 다음 노드로 이동합니다.


<div class="content-ad"></div>

- 단계 3: l2가 더 긴 경우, l2의 나머지 부분을 l1에 추가하고 l1을 계속 처리합니다.
- 단계 4: 더 긴 목록에 남아있는 노드를 계속 처리하고, 노드 값을 및 캐리를 업데이트합니다.
- 단계 5: 모든 노드를 처리한 후에도 캐리가 남아있는 경우, 캐리 값으로 새 노드를 끝에 추가합니다.
- 단계 6: 수정된 목록의 헤드를 반환합니다.