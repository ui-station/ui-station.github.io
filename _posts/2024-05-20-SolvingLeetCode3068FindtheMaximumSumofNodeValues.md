---
title: "LeetCode 3068번 문제 해결하기 노드 값의 최대 합 찾기"
description: ""
coverImage: "/assets/img/2024-05-20-SolvingLeetCode3068FindtheMaximumSumofNodeValues_0.png"
date: 2024-05-20 15:34
ogImage: 
  url: /assets/img/2024-05-20-SolvingLeetCode3068FindtheMaximumSumofNodeValues_0.png
tag: Tech
originalTitle: "Solving LeetCode 3068. Find the Maximum Sum of Node Values"
link: "https://medium.com/@yash-soni/solving-leetcode-3068-find-the-maximum-sum-of-node-values-4817bed75282"
---


# 문제 설명:

0부터 n - 1까지 번호가 매겨진 노드로 구성된 무방향 트리가 있습니다. 길이가 n - 1인 0을 기준으로 한 2차원 정수 배열 edges가 주어지는데, edges[i] = [ui, vi]는 트리의 노드 ui와 vi 사이에 간선이 있음을 나타냅니다. 또한 양의 정수 k와 길이가 n인 0을 기준으로 한 음이 아닌 정수 배열 nums가 주어집니다. 여기서 nums[i]는 i번째로 번호가 매겨진 노드의 값을 나타냅니다.

앨리스는 트리 노드의 값의 합을 최대로 하려고 합니다. 이를 위해 앨리스는 다음 작업을 트리에서 원하는 횟수만큼 (포함하여) 수행할 수 있습니다:

- 노드 u와 v를 연결하는 [u, v] 간선을 선택하고, 그들의 값들을 다음과 같이 업데이트합니다:
- nums[u] = nums[u] XOR k
- nums[v] = nums[v] XOR k

<div class="content-ad"></div>

Alice가 작업을 여러 번 수행하여 얻을 수 있는 값의 최대 합을 반환합니다.

예시 1:

![image](/assets/img/2024-05-20-SolvingLeetCode3068FindtheMaximumSumofNodeValues_0.png)

```js
Input: nums = [1,2,1], k = 3, edges = [[0,1],[0,2]]
Output: 6
Explanation: Alice가 다음을 수행하여 최대 합 6을 얻을 수 있습니다:
- [0,2] 엣지를 선택합니다. nums[0] 및 nums[2]는 다음과 같이 변경됩니다: 1 XOR 3 = 2이고, 배열 nums는 [1,2,1] -> [2,2,2] 로 변합니다.
값의 총 합은 2 + 2 + 2 = 6 입니다.
가능한 최대 값 합은 6임을 보여줄 수 있습니다.
```

<div class="content-ad"></div>

예제 2:

![그림](/assets/img/2024-05-20-SolvingLeetCode3068FindtheMaximumSumofNodeValues_1.png)

```js
Input: nums = [2,3], k = 7, edges = [[0,1]]
Output: 9
Explanation: Alice can achieve the maximum sum of 9 using a single operation:
- Choose the edge [0,1]. nums[0] becomes: 2 XOR 7 = 5 and nums[1] become: 3 XOR 7 = 4, and the array nums becomes: [2,3] -> [5,4].
The total sum of values is 5 + 4 = 9.
It can be shown that 9 is the maximum achievable sum of values.
```

예제 3:

<div class="content-ad"></div>

```js
Input: nums = [7,7,7,7,7,7], k = 3, edges = [[0,1],[0,2],[0,3],[0,4],[0,5]]
Output: 42
Explanation: 최대 가능한 합은 42로, Alice가 작업을 수행하지 않고 이를 달성할 수 있습니다.
```

제한사항:

- 2 `= n == nums.length `= 2 * 104
- 1 `= k `= 109
- 0 `= nums[i] `= 109
- edges.length == n - 1
- edges[i].length == 2
- 0 `= edges[i][0], edges[i][1] `= n - 1
- 입력은 edges가 유효한 트리를 나타내도록 생성됩니다.

<div class="content-ad"></div>

# 방법: 탐욕법 (정렬 기반 접근)

## 직관

노드 U에 대해 실행된 작업이 있다면, 노드의 새로운 값은 nums[U] XOR k가 됩니다. 각 노드에 대해, 작업을 수행한 후 값의 순 변화는 netChange[U] = nums[U] XOR k - nums[U]로 주어집니다.

만약 이 순 변화가 양수라면, 모든 노드 값의 총합은 증가합니다. 그렇지 않으면 감소합니다.

<div class="content-ad"></div>

"가정해보겠습니다. 노드 쌍에서 노드 합에 가장 큰 증가를 제공하는" 효율적인 작업 "을 수행하려고 한다고 가정해봅시다. 가장 큰 양수 netChange 값을 가진 노드를 선택하면 노드 합에 가장 큰 증가를 제공할 것입니다.

모든 노드에 대해 위에서 논의한 공식을 사용하여 net change 값을 계산할 수 있습니다. 이 값들을 내림차순으로 정렬한 후에는 positive sum을 가진 pair를 정렬 된 netChange 배열의 시작부터 선택할 수 있습니다.

쌍의 합이 양수이면이 쌍에 대한 작업을 수행할 때 총 노드 합의 값을 증가시킵니다.

## 알고리즘"

<div class="content-ad"></div>

1. nums의 크기인 n의 netChange 배열과 현재 nums의 합을 저장하는 정수 nodeSum을 초기화합니다.

2. nums 배열을 반복합니다 (0부터 n-1까지):

- 각 인덱스마다 intuition에서 논의한 아이디어를 사용하여 netChange의 값을 저장합니다.

3. netChange 배열을 내림차순으로 정렬합니다.

<div class="content-ad"></div>

4. netChange 배열을 반복합니다 (0부터 n-1까지, 단계 크기 = 2):

- 인접한 요소의 쌍을 만들 수 없는 경우, 반복을 중단합니다.
- 인접한 요소의 쌍의 합이 양수인 경우 이 합을 nodeSum에 추가합니다.

5. 모든 netChange 요소를 반복한 후, 수행한 작업 이후 노드의 최대 가능한 합인 nodeSum을 반환합니다.

## 구현

<div class="content-ad"></div>

```java
class Solution {
    public long maximumValueSum(int[] nums, int k, int[][] edges) {
        int n = nums.length;
        int[] netChange = new int[n];
        long nodeSum = 0;

        for (int i = 0; i < n; i++) {
            netChange[i] = (nums[i] ^ k) - nums[i];
            nodeSum += nums[i];
        }

        Arrays.sort(netChange);

        for (int i = n-1; i >= 1; i -= 2) {
            // If netChange contains odd number of elements break the loop
            if (i - 1 == -1) {
                break;
            }
            long pairSum = netChange[i] + netChange[i - 1];
            // Include in nodeSum if pairSum is positive
            if (pairSum > 0) {
                nodeSum += pairSum;
            } else {
                return nodeSum;
            }
        }
        return nodeSum;
    }
}
```

## 복잡도 분석

노드 값 목록에 포함된 요소 수를 n이라고 합시다.

- 시간 복잡도: O(n⋅logn)
  - 정렬을 제외하고 목록에 대해 단순 선형 작업을 수행하기 때문에 런타임은 정렬의 O(n⋅logn) 복잡성에 지배됩니다.
- 공간 복잡도: O(n)
  - 새로운 크기 n의 netChange 배열을 만들고 정렬하기 때문에 추가 공간은 netChange 배열에 대한 O(n)이고 정렬에 대해 O(logn) 또는 O(n)이므로 순 공간 복잡도는 O(n)입니다.```