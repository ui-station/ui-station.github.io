---
title: "세 개의 타워 뭐가 다른가"
description: ""
coverImage: "/assets/img/2024-06-30-TheThreeTowers_0.png"
date: 2024-06-30 21:55
ogImage: 
  url: /assets/img/2024-06-30-TheThreeTowers_0.png
tag: Tech
originalTitle: "The Three Towers"
link: "https://medium.com/@thakuria.mayukh/the-three-towers-e4466330dc5c"
---


![이미지](/assets/img/2024-06-30-TheThreeTowers_0.png)

하노이 탑은 프로그래밍에서 고전이자 도전적인 문제입니다. 이 문제의 해결책을 찾는 것은 어렵지 않지만, 해결책 뒤에 숨겨진 직관을 이해하는 데는 약간의 시간이 걸립니다. 이 문제를 살펴보고 이 문제의 직관을 이해하고 어떻게 코드로 작성할지 알아봅시다.

# 기원

하노이 탑 문제는 1883년 프랑스 수학자 에두아르 루카가 발명했습니다.

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

루카스는 이 문제를 더 넓은 대중에게 호소하기 위해 함께 이야기도 추가했습니다. 전설에 따르면 수세기동안 하인두 사원에서 사제들이 세 기둥 사이에서 금판을 옮겨다 놓았다고 합니다. 그들은 하노이의 탑 규칙을 따라야 합니다. 하루에 한 디스크씩만 움직일 수 있습니다. 이 퍼즐을 완성하면 세상이 끝날 것이라고 전해집니다.

# 문제 설명

세 개의 타워(기둥)가 있습니다. 하나의 타워에는 크기가 증가하는 n개의 디스크가 있고, 다른 두 타워는 비어 있습니다. 모든 디스크를 한 타워에서 다른 타워로 옮겨야 합니다.

두 가지 규칙이 있습니다. 더 큰 디스크를 작은 디스크 위에 놓을 수 없으며 한 번에 한 개의 디스크만 이동시킬 수 있습니다.

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

# 직관

엣지 케이스를 살펴보죠. 즉, 우리가 디스크가 하나뿐인 경우의 처리 방법은 무엇일까요?

이 경우의 답은 꽤 간단합니다. 필요한 기둥으로 그냥 이동하면 됩니다.

<img src="/assets/img/2024-06-30-TheThreeTowers_1.png" />

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

이제 2개의 디스크를 이동해 봅시다.

작은 디스크를 큰 디스크 위에 놓을 수 없기 때문에 먼저 작은 디스크를 중간으로 옮겨야 합니다.

![이미지](/assets/img/2024-06-30-TheThreeTowers_2.png)

그런 다음 큰 디스크를 끝으로 옮기게 될 거에요.

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


![이미지](/assets/img/2024-06-30-TheThreeTowers_3.png)

작은 디스크를 마지막 위치로 이동시키면 됩니다.

![이미지](/assets/img/2024-06-30-TheThreeTowers_4.png)

문제가 해결되었습니다.


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

이제 우리가 가장 중요한 테스트 케이스, 즉 3개의 디스크에 대한 것을 시도해 봅시다. 이것을 주의 깊게 이해하려고 노력해주세요.

![이미지](/assets/img/2024-06-30-TheThreeTowers_5.png)

하나의 해결책은 백트래킹을 시도하여 가능성을 찾는 것입니다(수도 퍼즐과 같이). 그러나 더 나은 접근 방식은 문제를 분해하는 것입니다.

가장 큰 디스크를 무시해 봅시다. 우리는 한 막대에서 다른 막대로 두 개의 디스크를 옮기는 방법을 알고 있습니다. 그러나 만약 그들을 직접 최종 막대로 옮긴다면(2개 디스크 문제에서처럼) 가장 큰 디스크를 어떻게 옮길 것인가요?

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


![image1](/assets/img/2024-06-30-TheThreeTowers_6.png)

이 방법은 문제가 발생할 수 있으므로 상단의 두 디스크를 중간 막대로 이동해 보겠습니다. 그렇게 하면 가장 큰 디스크를 마지막 막대로 바로 이동할 수 있습니다.

![image2](/assets/img/2024-06-30-TheThreeTowers_7.png)

이제 동일한 방법을 따라서 (2개의 디스크를 이동할 때 사용한 것과 같은 방법으로) 두 개의 작은 디스크를 더 큰 디스크 위로 이동할 수 있습니다.


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


![이미지](/assets/img/2024-06-30-TheThreeTowers_8.png)

패턴을 보셨나요?

n개의 디스크를 시도해보세요.

![이미지](/assets/img/2024-06-30-TheThreeTowers_9.png)


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

3개의 디스크가 있는 문제와 비교해보세요. 먼저, 작은 디스크를 가운데로 옮깁니다.

![image](/assets/img/2024-06-30-TheThreeTowers_10.png)

그런 다음 가장 큰 디스크를 최종 막대로 옮길 수 있습니다.

![image](/assets/img/2024-06-30-TheThreeTowers_11.png)

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

이제 첫 번째 단계를 반복하고 n-1 개의 디스크를 더 큰 디스크 위로 이동해야 합니다.

![image](/assets/img/2024-06-30-TheThreeTowers_12.png)

따라서 우리의 문제는 해결되었습니다!

# 재귀 논리

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

이제 주요 부분으로 넘어갑시다. 재귀 논리를 이해하는 방법은 무엇인가요?

- 먼저 n-1개의 디스크를 보조 막대로 이동
- n번째 디스크를 최종 막대로 이동
- n-1개의 디스크를 최종 막대로 이동
- 만약 n = 1이면, 바로 최종 막대로 이동시킵니다

# 소스 코드

이제 솔루션을 코딩해봅시다.

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
var towerOfHanoi = function(n, start, auxiliary, end){}
```

먼저 우리는 에지 케이스를 작성할 것입니다.

```js
var towerOfHanoi = function(n, start, auxiliary, end) {
  if (n == 1) {
    console.log(`디스크 1을 ${start}에서 ${end}로 옮깁니다`);
    return 0;
  }
}
```

우리는 코드가 n-1에 대해 작동할 것으로 가정하고, n-1을 보조로 옮깁니다.

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
var towerOfHanoi = function(n, start, auxiliary, end) {
  if (n == 1) {
    console.log(`디스크 1을 ${start}에서 ${end}로 이동합니다`);
    return 0;
  }
  towerOfHanoi(n - 1, start, end, auxiliary);
}
```

그리고 가장 큰 디스크를 끝으로 이동합니다.

```js
var towerOfHanoi = function(n, start, auxiliary, end) {
  if (n == 1) {
    console.log(`디스크 1을 ${start}에서 ${end}로 이동합니다`);
    return 0;
  }
  towerOfHanoi(n - 1, start, end, auxiliary);
  console.log(`디스크 ${n}을 ${start}에서 ${end}로 이동합니다`);
}
```

마지막으로, 보조 기둥에 있는 n-1을 끝으로 옮깁니다.

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
var towerOfHanoi = function(n, start, auxiliary, end) {
  if (n == 1) {
    console.log(`move the disk 1 from ${start} to ${end}`);
    return 0;
  }
  towerOfHanoi(n - 1, start, end, auxiliary);
  console.log(`move the disk ${n} from ${start} to ${end}`);
  towerOfHanoi(n - 1, auxiliary, start, end);
}
```

이제 우리의 코드가 완성되었습니다!!!

# 시간 복잡도

이 알고리즘의 시간 복잡도를 찾아봅시다


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

가정해볼게요. n개의 디스크가 있다고 가정해봅시다. 시간은 T(n)이라고 부르겠습니다.

따라서, T(n) = 2(T(n-1)) +1

= T(n) = 2(2(T(n-2)) + 1) +1

= T(n)= 2²T(n-2) + 2 + 1

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


=T(n)= 2³T(n-3) + 2² + 2 + 1

= T(n) = (2^k) *T(n-k) +(2^k) +2^(k-1) + …. + 2² + 1

( T(1) = 1 and T(0) = 0)

= T(n) = 2⁰ + 2¹ + 2² + …. + 2^(n-1)


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

이것은 등비 수열이기 때문에 합계를 구할 수 있습니다.

` T(n) = (2^n) -1 `

따라서, 시간복잡도는 O((2^n)-1) 입니다.

# 공간 복잡도

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

재귀를 사용할 때 함수가 다른 함수를 호출하면 외부 함수가 재귀 호출 스택에 저장됩니다.

함수는 1에 도달할 때까지 계속 호출되므로,

SC = O(N)

이 글이 마음에 들었기를 바랍니다. 불일치나 의문 사항이 있으면 언제든지 연락 주세요. 즐거운 학습되세요!!!