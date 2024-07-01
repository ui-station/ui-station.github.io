---
title: "Microsoft DSA 면접 질문 모음"
description: ""
coverImage: "/assets/img/2024-07-01-MicrosoftDSAInterviewQuestions_0.png"
date: 2024-07-01 15:54
ogImage: 
  url: /assets/img/2024-07-01-MicrosoftDSAInterviewQuestions_0.png
tag: Tech
originalTitle: "Microsoft DSA Interview Questions"
link: "https://medium.com/gitconnected/microsoft-dsa-interview-questions-2a1a5a12d8bf"
---


# 질문 1

![이미지](/assets/img/2024-07-01-MicrosoftDSAInterviewQuestions_0.png)

두 개의 동일한 길이를 가진 정수 배열 A와 B가 주어졌을 때, 시작점 j까지 A의 요소들의 누적 합이 B의 요소들의 누적 합과 같은 경우의 수를 반환하는 solution(A, B) 함수를 작성하십시오. 이때 다음 조건들을 만족해야 합니다:

- A의 요소들의 누적 합이 시작부터 j까지 B의 요소들의 누적 합과 동일합니다.
- A의 요소들의 누적 합이 배열 A의 총 합의 절반과 동일합니다.
- B의 요소들의 누적 합이 배열 B의 총 합의 절반과 동일합니다.

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

## 방법,

- 총합 초기화: 주석은 sumA 및 sumB의 초기화 및 목적을 명확히 합니다.
- 총합 계산: 총합을 계산하는 루프에 대한 주석이 있습니다.
- 유효한 분할 지점 계산: count의 목적 및 유효한 분할 지점을 확인하는 논리에 대한 설명이 있습니다.
- 누적 합: tempA 및 tempB의 초기화 및 누적 합을 추적하는 역할을 명확히 하는 주석이 있습니다.
- 반복 및 조건 확인: 두 번째 요소부터 시작하는 루프, 유효한 분할 지점을 찾기 위해 확인된 조건 및 누적 합의 업데이트를 설명하는 주석이 있습니다.

## 실제 실행,

```js
단계별 결과

총합 계산:
sumA 진행: 0, 4, 3, 3, 6
sumB 진행: 0, -2, 3, 3, 6
최종 sumA = 6
최종 sumB = 6

누적 합 및 조건 확인:

초기화:
tempA = 0
tempB = 0

반복:

j = 1:
tempA = 4
tempB = -2
조건 미충족

j = 2:
tempA = 3
tempB = 3
조건: tempA === tempB && 2 * tempA === sumA && 2 * tempB == sumB
3 === 3 && 2 * 3 === 6 && 2 * 3 == 6 (참)
하지만, j !== 1이어야 조건이 검사됨

j = 3:
tempA = 3
tempB = 3
조건: tempA === tempB && 2 * tempA === sumA && 2 * tempB == sumB
3 === 3 && 2 * 3 === 6 && 2 * 3 == 6 (참)
조건 충족, 그러나 j !== 1이므로 조건이 이 단계에서는 확인되지 않음

j = 4:
tempA = 6
tempB = 6
필요한 길이를 초과하여 조건 확인이 안 됨

최종 결과:
조건 j !== 1 && tempA === tempB && 2 * tempA === sumA && 2 * tempB == sumB이 전혀 충족되지 않아 count는 0으로 남습니다.
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

## 구현,

```js
A: [0,4,-1,0,3]
B: [0,-2,5,0,3]

function solution(A, B) {
  // 배열 A와 B의 총합을 저장할 sumA와 sumB를 초기화합니다.
  let sumA = 0;
  let sumB = 0;

  // 배열 A와 B의 요소 총합을 계산합니다.
  for (let i = 0; i < A.length; i++) {
    sumA += A[i]; // 각 반복 후 sumA = [0, 4, 3, 3, 6]
    sumB += B[i]; // 각 반복 후 sumB = [0, -2, 3, 3, 6]
  }

  // sumA는 이제 6입니다.
  // sumB는 이제 6입니다.

  // 유효한 분할 지점의 수를 추적하는 count를 초기화합니다.
  let count = 0;

  // 현재 인덱스까지 누적 합을 저장할 tempA와 tempB를 초기화합니다.
  let tempA = A[0]; // tempA = 0
  let tempB = B[0]; // tempB = 0

  // 두 번째 요소부터 배열을 반복합니다.
  for (let j = 1; j < A.length; j++) {
    // 현재 누적 합(첫 번째 인덱스 제외)이 같고,
    // 서로의 배열 합의 절반인지 확인합니다.
    if (j !== 1 && tempA === tempB && 2 * tempA === sumA && 2 * tempB == sumB) {
      count++; // 이 경우 조건이 충족되지 않아 count는 여전히 0입니다.
    }

    // 현재 요소로 누적 합을 업데이트합니다.
    tempA += A[j]; // 각 반복 후 tempA 값: [0, 4, 3, 3, 6]
    tempB += B[j]; // 각 반복 후 tempB 값: [0, -2, 3, 3, 6]
  }

  // 유효한 분할 지점의 수를 반환합니다.
  return count; // 유효한 분할 지점을 찾지 못해 count는 0입니다.
}

// 사용 예시
const A = [0, 4, -1, 0, 3];
const B = [0, -2, 5, 0, 3];
console.log(solution(A, B)); // 출력: 0
```

# 두 번째 질문

<img src="/assets/img/2024-07-01-MicrosoftDSAInterviewQuestions_1.png" />

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

## 단계별 접근 방법:

초기화:

- 각 요소가 이전 요소와 다음 요소보다 큰지를 추적하는 배열 isGreaterThanPrev 및 isGreaterThanNext를 초기화합니다.
- 이전 요소나 다음 요소와 비교할 요소가 없는 첫 번째 요소의 isGreaterThanPrev와 마지막 요소의 isGreaterThanNext를 -1로 초기화합니다.

비교 배열 채우기:

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

- 배열 Y를 순회하여 각 요소가 다음 요소보다 큰지 나타내는 isGreaterThanNext를 채웁니다.
- 배열 Y를 순회하여 각 요소가 이전 요소보다 큰지 나타내는 isGreaterThanPrev를 채웁니다.

쌍(Pairs) 생성 및 정렬:

- 각 Y의 요소에 대해 [값, 인덱스] 쌍을 포함하는 배열 v를 생성합니다.
- 배열 v를 요소의 값에 기반하여 내림차순으로 정렬합니다.

변수 초기화:

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

- cur와 maxLines라는 변수를 초기화하여 현재 라인 수와 관찰된 최대 라인 수를 추적합니다.

정렬된 요소 처리:

- 동일한 값으로 처리할 요소를 처리하기 위해 정렬된 배열 v를 반복합니다.
- 각 요소에 대해 다음을 계산합니다:
  - down: 시작 부분에서 이전 요소보다 큰 요소 수 또는 끝 부분에서 다음 요소보다 큰 요소 수.
  - up: 시작 부분에서 다음 요소보다 크지 않은 요소 수 또는 끝 부분에서 이전 요소보다 크지 않은 요소 수.
  - peaks: 다음과 이전 요소보다 큰 요소 수.
  - valleys: 다음과 이전 요소보다 작은 요소 수.
  - peaks를 추가하고 valleys를 빼고 down/up 값을 통해 현재 라인 수 cur를 조정합니다.
  - 현재 라인 수가 이전 최대값을 초과하면 최대 라인 수 maxLines를 업데이트합니다.

결과 반환:

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

- 배열에서 각각의 peak(봉우리)와 valley(골짜기) 전환을 나타내는 비중복 라인(줄)의 최대 수인 maxLines를 반환합니다.

## 실행 과정,

```js
중간 결과와 함께 설명:
초기화:

이전 요소보다 큰지, 이후 요소보다 큰지를 추적할 isGreaterThanPrev 및 isGreaterThanNext 배열이 생성됩니다.
isGreaterThanNext = [false, true, true, false, false, true, -1]
isGreaterThanPrev = [-1, true, false, false, true, true, false]
쌍 생성 및 정렬:

Y의 각 요소에 대해 [값, 인덱스] 쌍 생성합니다.
정렬 전 v: [[2, 0], [3, 1], [2, 2], [1, 3], [5, 4], [6, 5], [5, 6]]
값을 기준으로 v를 내림차순으로 정렬합니다.
정렬 후 v: [[6, 5], [5, 4], [5, 6], [3, 1], [2, 0], [2, 2], [1, 3]]
정렬된 요소 처리:

요소 6 (인덱스 5):
down = 1, 이전 요소보다 크고, 끝에 위치하므로
up = 0, 조건 충족 없음.
cur = 1, maxLines = 1
요소 5 (인덱스 4와 6):
인덱스 4:
down = 0, up = 0
인덱스 6:
down = 0, up = 1, 이전 요소보다 크지 않고, 끝에 위치하므로
봉우리 = 0, 골짜기 = 0
cur = 1 - 1 = 0, maxLines = 1
요소 3 (인덱스 1):
down = 0, up = 0
봉우리 = 1, 이전과 이후 요소보다 크기 때문.
cur = 0 + 1 - 0 = 1, maxLines = 1
요소 2 (인덱스 0와 2):
인덱스 0:
down = 0, up = 1, 이후 요소보다 크지 않고, 시작에 위치하므로
인덱스 2:
down = 0, up = 0
봉우리 = 0, 골짜기 = 1, 이전과 이후 요소보다 작기 때문.
cur = 1 - 1 - 0 = 0, maxLines = 1
요소 1 (인덱스 3):
down = 0, up = 0
봉우리 = 0, 골짜기 = 0
cur = 0 + 0 - 0 = 0, maxLines = 1
최종 결과:

관측된 최대 라인 수는 1이고, 따라서 maxLines = 1입니다.
```

## 구현,

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
function solution(Y) {
  let n = Y.length;

  // 다음 요소보다 크다는지 이전 요소보다 크다는지 추적하는 배열
  let isGreaterThanPrev = new Array(n);
  let isGreaterThanNext = new Array(n);
  isGreaterThanNext[n - 1] = -1; // 마지막 요소는 다음 요소가 없음
  isGreaterThanPrev[0] = -1; // 첫 요소는 이전 요소가 없음

  // isGreaterThanNext 배열 작성
  for (let i = 0; i < n - 1; i++) {
    isGreaterThanNext[i] = Y[i] > Y[i + 1];
  }

  // isGreaterThanNext = [false, true, true, false, false, true, -1]

  // isGreaterThanPrev 배열 작성
  for (let i = 1; i < n; i++) {
    isGreaterThanPrev[i] = Y[i] > Y[i - 1];
  }

  // isGreaterThanPrev = [-1, true, false, false, true, true, false]

  // [값, 인덱스] 쌍의 배열 생성
  let v = [];
  for (let i = 0; i < n; i++) {
    v.push([Y[i], i]);
  }

  // v = [[2, 0], [3, 1], [2, 2], [1, 3], [5, 4], [6, 5], [5, 6]]

  // 값 기준으로 내림차순 정렬
  v.sort((a, b) => b[0] - a[0]);

  // v = [[6, 5], [5, 4], [5, 6], [3, 1], [2, 0], [2, 2], [1, 3]]

  let cur = 0,
    maxLines = 0;
  for (let i = 0; i < v.length; i++) {
    let y = v[i][0];

    let peaks = 0,
      valleys = 0,
      down = 0,
      up = 0;

    // 동일한 값 요소 처리
    while (i < v.length && v[i][0] === y) {
      let idx = v[i][1];

      // 아래로 향하는 지점인지 확인
      if (
        (idx === 0 && isGreaterThanNext[idx]) ||
        (idx === n - 1 && isGreaterThanPrev[idx])
      ) {
        down++;
      }

      // 올라가는 골짜기인지 확인
      if (
        (idx === 0 && !isGreaterThanNext[idx]) ||
        (idx === n - 1 && !isGreaterThanPrev[idx])
      ) {
        up++;
      }

      let isNotEnd = idx > 0 && idx < n - 1;

      // 산인지 확인
      if (isNotEnd && isGreaterThanNext[idx] && isGreaterThanPrev[idx]) {
        peaks++;
      } 
      // 계곡인지 확인
      else if (isNotEnd && !isGreaterThanNext[idx] && !isGreaterThanPrev[idx]) {
        valleys++;
      }
      i++;
    }

    i--;

    // 현재 라인 수 계산
    cur = cur + peaks - valleys + down;
    maxLines = Math.max(maxLines, cur);

    cur = cur + peaks - valleys - up;
    maxLines = Math.max(maxLines, cur);
  }

  return maxLines;
}

// 사용 예시
console.log(solution([2, 3, 2, 1, 5, 6, 5])); // 출력: 2
```

# 읽어 주셔서 감사합니다

- 👏 이야기에 박수를 보내주시고 팔로우 부탁드립니다 👉
- 📰 자바스크립트, 자료 구조 및 알고리즘, 리액트, 인터뷰 준비 등 다양한 콘텐츠 확인해보세요
- 🔔 팔로우 링크: LinkedIn!

늘 개선할 점이 있을텐데요. 의견을 자유롭게 공유해주세요. 😊
