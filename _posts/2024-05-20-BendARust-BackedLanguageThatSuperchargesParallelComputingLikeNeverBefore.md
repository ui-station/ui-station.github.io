---
title: "번드 - 전례 없이 병렬 컴퓨팅을 강력하게 돕는 러스트 기반 언어"
description: ""
coverImage: "/assets/img/2024-05-20-BendARust-BackedLanguageThatSuperchargesParallelComputingLikeNeverBefore_0.png"
date: 2024-05-20 20:52
ogImage:
  url: /assets/img/2024-05-20-BendARust-BackedLanguageThatSuperchargesParallelComputingLikeNeverBefore_0.png
tag: Tech
originalTitle: "Bend — A Rust-Backed Language That Supercharges Parallel Computing Like Never Before"
link: "https://medium.com/gitconnected/bend-a-rust-backed-language-that-supercharges-parallel-computing-like-never-before-3e603f5eed9c"
---

안녕하세요! Bend에 대해 들어보셨나요?

아직 안 들어보셨나요? 이 이야기를 읽고 있다면 아직 늦지 않으십니다.

몇 시간 전에 Bend라는 GitHub 저장소를 발견했어요. 'Bend — 대규모 병렬 처리를 지원하는 고수준 프로그래밍 언어'라는 제목이 붙어 있었죠.

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

Bend는 병렬 계산을 수행하는 방식을 영원히 변경하려는 고수준 프로그래밍 언어입니다.

세계를 바꾼다고 약속한 언어가 많았다는 건 알고 있어요. 하지만 Bend는 실제로 그럴 수도 있어요!

Bend가 약속하는 좋은 부분을 알아보기 전에 병렬 계산에 대해 조금 배워볼까요?

# 병렬 계산 - 최소의 시간으로 최대의 일을 하는 기술

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

컴퓨터 프로그램은 기본적으로 작업을 하나씩 차례대로 실행합니다.

이는 프로세서 코어에 의해 한 번에 한 가지 명령을 실행하는 단일 스레드에 의존하기 때문입니다.

이 간단한 방식은 순차 계산이라고 합니다.

이로 인해 프로그램 내부의 명령 흐름이 예측 가능하고 디버깅하기 쉬워집니다.

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

그러나, 이것은 또한 모든 명령어가 실행을 시작하기 전에 이전 명령어가 끝날 때까지 기다려야 한다는 것을 의미합니다.

현대 컴퓨팅 시대에 있어서 대부분의 프로세서가 여러 코어로 구성되어 있기 때문에, 병렬 연산이라는 다른 접근 방식을 사용하여 지수적으로 빠르게 만들 수 있습니다.

프로그램 내의 많은 명령어들이 여러 스레드를 사용하여 동시에 실행됨으로써 프로그램 전체의 실행 시간을 줄일 수 있습니다.

# 하지만, 병렬 연산을 올바르게 수행하는 것은 어려울 수 있습니다

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

병렬 계산을 다룬 적이 있는 분이라면, 그것을 올바르게 처리하는 것이 정말 악몽이라고 동의할 것입니다.

동시에 공유 리소스에 접근하는 여러 스레드는 경합 상태를 유발해 완전히 예상치 못한 결과로 이어질 수 있습니다.

또는, 좋지 않은 날이면 두 개 이상의 스레드가 서로가 차지한 리소스를 대기하고 끝없이 기다리는 데드락에 갇힐 수도 있습니다.

이러한 문제는 Python 라이브러리인 threading과 multiprocessing이나 Go의 WaitGroup, Mutex, RWMutex와 같은 동기화 기본 요소를 이용한 Goroutines 등으로 해결되었지만, 이들의 구현은 숙련하기 어렵습니다.

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

CUDA 및 Metal은 각각 NVIDIA 및 Apple GPU에서 병렬 계산을 활성화하는 몇 가지 다른 프로그래밍 모델입니다. 그러나 이를 다루는 대부분의 사람들은 (정직하다면) 그것을 완벽히 이해하는 데 얼마나 어려운 작업인지 말해 줄 것입니다.

그럼에도 불구하고 남아 있는 의문은 —

병렬 계산을 쉽게 할 수 있는 새로운 사람들에게 좋은 대안이 없는 걸까요?

# Bend가 구해줄게요

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

단순히 Python처럼 쉽게 느껴지도록 작성된 언어인 Bend는 아마도 우리를 이 비통으로부터 구출해 줄 수 있는 사랑스러운 언어일지도 모르겠어요.

그 철학은 웃기게 간단합니다 —

Bend는 기본적으로 코드를 병렬로 실행하기 때문에, 다중 코어 CPU 또는 GPU와 작업하기 위해 CUDA를 배우는 데 수년을 보내야 할 필요가 없어요.

## 하지만, Bend는 어떻게 이것이 가능하게 만드는 걸까요?

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

Bend은 매우 병렬 실행을 위해 설계된 강력한 계산 프레임워크인 HVM2 또는 Higher-order Virtual Machine 2에 의해 구동됩니다.

Python과 같은 고수준 언어로 작성된 프로그램을 HVM2로 컴파일하면 GPU에서 빠르게 실행할 수 있습니다!

HVM2는 1997년 Yves Lafont가 고안한 병렬 컴퓨테이션 모델인 Interaction Combinators를 기반으로 하며, 그래프 기반 구조를 사용합니다.

이 모델에서 각 계산은 그래프로 시각화됩니다.

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

이 그래프들에서 각 노드는 조합자(Combinator)입니다.

조합자는 이 모델의 기본 구성 요소로 작용하는 고차 함수입니다.

각 조합자마다 다른 조합자와의 상호 작용 방식을 결정하는 간단한 규칙 세트가 있습니다. 예를 들어 —

- Identity Combinator는 인수를 변경하지 않고 반환합니다.
- Constant Combinator는 두 개의 인수를 사용하고 첫 번째 것을 반환합니다(두 번째 것을 무시), 등등.

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

그래프에서 각 엣지는 이 결합자들 간의 연결을 나타냅니다.

이러한 결합자들이 상호작용할 때(엣지에서 시사하는 대로), 그들은 각자의 규칙에 따라 변환되어 결과를 돌려줍니다.

상호작용 결합자 모델에서 가장 훌륭한 부분은 이들의 그래프 구조가 프로그램 내의 다른 계산을 서로 다른 코어(즉, 병렬로)에서 처리할 수 있게 한다는 것입니다.

그리고 이것이 이 모델을 강력하게 만드는 요소입니다!

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

HVM2은 고수준 언어로 작성된 프로그램을 대상으로 하는 저수준 컴파일러이며 직접 사용하기 위한 것은 아닙니다.

재귀 합을 구현하려면 다음과 같이 읽을 수 있습니다.

```js
@main = a
  & @sum ~ (28 (0 a))

@sum = (?(((a a) @sum__C0) b) b)

@sum__C0 = ({c a} ({$([*2] $([+1] d)) $([*2] $([+0] b))} f))
  &! @sum ~ (a (b $(:[+] $(e f))))
  &! @sum ~ (c (d e))
```

하지만 걱정하지 마세요. Bend는 우리의 삶을 쉽게 만들기 위해 이 암호화된 HVM2와 인터페이스를 맺기 위해 작성된 사람이 읽을 수 있는 언어입니다. 함께 작업을 병렬로 처리할 수 있습니다.

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

## Bend 사용 방법

Bend는 러스트 프로그래밍 언어로 작성되었으므로, 첫 번째 단계는 Rust nightly를 설치하고, 그 다음으로 HVM2와 Bend를 함께 설치하는 것입니다.

```js
cargo +nightly install hvm
cargo +nightly install bend-lang
```

안타깝게도, 현재 Windows에서는 작동하지 않기 때문에 (나처럼) WSL2를 해결책으로 사용해야 합니다.

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

다음은 Bend 코드를 작성합니다.

```js
# sorter.bend

# Sorting Network = just rotate trees!
def sort(d, s, tree):
  switch d:
    case 0:
      return tree
    case _:
      (x,y) = tree
      lft   = sort(d-1, 0, x)
      rgt   = sort(d-1, 1, y)
      return rots(d, s, lft, rgt)

# Rotates sub-trees (Blue/Green Box)
def rots(d, s, tree):
  switch d:
    case 0:
      return tree
    case _:
      (x,y) = tree
      return down(d, s, warp(d-1, s, x, y))

# Swaps distant values (Red Box)
def warp(d, s, a, b):
  switch d:
    case 0:
      return swap(s + (a > b), a, b)
    case _:
      (a.a, a.b) = a
      (b.a, b.b) = b
      (A.a, A.b) = warp(d-1, s, a.a, b.a)
      (B.a, B.b) = warp(d-1, s, a.b, b.b)
      return ((A.a,B.a),(A.b,B.b))

# Propagates downwards
def down(d,s,t):
  switch d:
    case 0:
      return t
    case _:
      (t.a, t.b) = t
      return (rots(d-1, s, t.a), rots(d-1, s, t.b))

# Swaps a single pair
def swap(s, a, b):
  switch s:
    case 0:
      return (a,b)
    case _:
      return (b,a)

# Testing
# -------

# Generates a big tree
def gen(d, x):
  switch d:
    case 0:
      return x
    case _:
      return (gen(d-1, x * 2 + 1), gen(d-1, x * 2))

# Sums a big tree
def sum(d, t):
  switch d:
    case 0:
      return t
    case _:
      (t.a, t.b) = t
      return sum(d-1, t.a) + sum(d-1, t.b)

# Sorts a big tree
def main:
  return sum(18, sort(18, 0, gen(18, 0)))
```

이것은 공식 저장소에서 가져온 예시로, 병렬 정렬 알고리즘인 Bitonic Merge Sort를 구현한 것입니다.

이 알고리즘은 병렬로 실행할 수 있는 분할 정복 방식을 사용하므로 Bend는 병렬로 실행할 것입니다 (Bend의 철학을 기억하시나요?)

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

다음으로, 이 명령어를 사용하여 sorter.bend 프로그램을 실행합니다 —

```js
bend run sorter.bend # 러스트 해석기(순차 실행) 사용
bend run-c sorter.bend # C 해석기(병렬 실행) 사용
bend run-cu sorter.bend # CUDA 해석기(대규모 병렬 실행) 사용
```

마지막 명령어는 기기의 GPU를 자동으로 사용하며, 세부 사항을 자세히 다룰 필요가 없습니다.

또한 최대 성능을 위해 Bend 파일을 독립적인 C/CUDA 파일로 컴파일할 수 있지만, 이러한 명령어는 아직 성숙하지 않을 수 있으며 오류를 발생시킬 수 있습니다.

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

Bend는 아직 초기 단계에 있지만 매우 유망해보입니다.

다음 섹션의 자원들은 더 많이 배우고 개발에 참여하는 데 도움을 줄 것입니다. 한번 보세요!

즐거운 병렬 처리!

# 더 많이 알아보기

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

- Bend의 GitHub 저장소
- Bend를 처음부터 배우기
- Higher-order Virtual Machine 2 (HVM2)의 GitHub 저장소
- HVM 작동 방식 블로그 포스트
- HVM 작동 방식 비디오 설명
- Y. Lafont에 의한 상호 작용 결합 연구 논문
- Blend의 모기업인 Higher Order Company 웹 사이트

저의 작업과 계속 연락하고 싶다면 이메일 목록 링크를 확인하세요 —
