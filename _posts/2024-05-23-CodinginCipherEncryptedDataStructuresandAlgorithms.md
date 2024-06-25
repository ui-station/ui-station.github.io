---
title: "암호로 코딩하기 암호화된 데이터 구조 및 알고리즘"
description: ""
coverImage: "/assets/img/2024-05-23-CodinginCipherEncryptedDataStructuresandAlgorithms_0.png"
date: 2024-05-23 18:50
ogImage:
  url: /assets/img/2024-05-23-CodinginCipherEncryptedDataStructuresandAlgorithms_0.png
tag: Tech
originalTitle: "Coding in Cipher: Encrypted Data Structures and Algorithms"
link: "https://medium.com/towards-data-science/coding-in-cipher-encrypted-data-structures-and-algorithms-dd99e584a655"
---

![이미지](/assets/img/2024-05-23-CodinginCipherEncryptedDataStructuresandAlgorithms_0.png)

Fully Homomorphic Encryption (FHE)이라는 혁신적인 방식을 소개합니다. FHE은 암호화된 데이터를 해독하지 않고도 데이터에 대한 계산을 수행할 수 있는 방법을 제공합니다. 이는 데이터 상의 연산을 수행하면서 완전한 개인 정보 보호를 유지할 수 있음을 의미합니다. FHE은 사후 양자 암호화 방법을 활용하여 암호화된 데이터가 클라우드나 블록체인과 같은 공개 네트워크에서 안전하게 유지되도록 합니다.

이 시리즈 기사에서는 바이너리 검색 트리, 정렬 알고리즘, 그리고 동적 프로그래밍 기술과 같은 전통적인 데이터 구조 및 알고리즘을 FHE를 활용해 암호화된 영역에서 구현하는 방법을 탐구합니다. 데이터셋에서 완전히 암호화된 상태로 이진 검색을 수행하거나, 원시 형태로 보이지 않는 데이터를 정렬하면서 데이터의 개인 정보 보호와 안전성이 절대로 위협받지 않도록 보장할 수 있습니다.

FHE가 어떻게 기본 수준에서 작동하는지와 데이터 보안 및 알고리즘 설계에 미치는 영향에 대해 깊이 파고들 것입니다. 이 시리즈에서는 실제 응용 프로그램 및 사기 탐지, 지불 등을 포함한 이런 암호화된 알고리즘을 구현할 때 개발자가 직면하는 잠재적인 도전에 대해 탐구할 것입니다. 이것은 보안을 향상시키는 데 그치는 것이 아니라, 데이터와 상호 작용하는 방식을 재고하고 소프트웨어 개발에서 가능한 범위를 넓히는 데 관한 것입니다.

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

경험 많은 개발자이거나 암호화된 컴퓨팅 개념에 익숙하지 않은 분이라도, 이 기사는 고급 암호 기술을 프로그래밍 프로젝트에 통합하는 방법에 대한 통찰력을 제공할 것입니다. 함께 이 여행을 떠나서 암호로 코딩하는 잠재력을 발견해 보세요. 일상 데이터 연산을 안전하고 개인정보 보호를 위한 계산으로 변환하여 안전한 디지털 혁신의 새 시대를 열어보겠습니다.

# 완전가환암호 기본

FHE(Fully Homomorphic Encryption)에서 암호문에서 수행할 수 있는 주요 연산은 덧셈과 곱셈입니다. 이 연산들은 더 복잡한 연산의 기본 요소로 사용됩니다. 예를 들어, 두 개의 암호화된 값을 더할 수 있으며, 복호화된 결과는 원래 평문 값의 합이 될 것입니다. 이러한 기본 연산의 조합을 사용하여 복잡한 계산을 구성할 수 있으며, 이를 통해 알고리즘 및 기능을 암호화된 데이터에서 실행할 수 있습니다. 예를 들어, 두 입력 값 x와 y를 사용하여 x + x _ y를 계산하는 함수 F가 있다고 가정해 봅시다. 이 함수의 수학적 표현은 F(x, y) = x + x _ y로 작성될 수 있으며, 또한 회로로 표현할 수 있습니다. 즉, 직접 비순환 그래프입니다:

![Image](/assets/img/2024-05-23-CodinginCipherEncryptedDataStructuresandAlgorithms_1.png)

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

# 소음

FHE를 사용하면 암호화된 데이터에서 연산을 수행할 수 있지만, 암호문 내의 소음 증가라는 추가적인 어려움이 동반됩니다. 이는 적절하게 관리되지 않으면 결국 복호화 오류로 이어질 수 있습니다. FHE 스키마에서는 각 암호문에 일정량의 보안을 보장하는 소음이 포함됩니다. 이 소음은 처음에는 작지만 암호문에 수행되는 연산이 늘어날수록 증가합니다. 덧셈 연산을 수행할 때 소음은 비교적 작지만, 곱셈을 할 때는 두 암호문의 소음이 곱해져서 증가합니다. 결과적으로, 두 개의 소음 레벨 n1과 n2를 가진 암호문을 곱할 때 결과 암호문의 소음은 n1 \* n2로 근사될 수 있거나 n1 또는 n2만큼보다 훨씬 빨리 증가하는 함수가 됩니다.

![이미지](/assets/img/2024-05-23-CodinginCipherEncryptedDataStructuresandAlgorithms_2.png)

FHE 스키마의 소음을 관리하는 몇 가지 방법이 있지만, 글의 분량을 고려하여 주된 초점은 부스트래핑이라는 소음 감소 기술에 있습니다. 부스트래핑은 암호문의 소음 수준을 줄이고, 따라서 소음 예산을 회복시키고 더 많은 연산을 가능하게 합니다. 본질적으로, 부스트랩은 복호화 및 다시 암호화 알고리즘을 순환 암호화적으로 적용합니다. 이는 FHE 스키마의 전체 복호화 회로를 암호화된 함수로 평가해야 한다는 것을 필요로 합니다. 결과는 이전과 동일한 평문을 나타내지만 소음이 줄어든 새로운 암호문입니다. 부스트랩은 FHE에서 무제한으로 암호화된 데이터에 대한 연산을 허용하는 중요한 기술입니다.

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

# 이론에서 실제로

FHE를 탐색하는 첫 걸음을 내딛기 위해, fhe-studio.com에서 찾을 수 있는 오픈 소스 IDE에 있는 미리 만들어진 회로를 살펴볼 수 있습니다. 이 IDE는 Concrete FHE 라이브러리를 기반으로 합니다. Concrete의 FHE 스키마(TFHE 스키마의 변형)는 이진 기반으로, 각 비트가 개별적으로 암호화됩니다. 구현은 개발자의 예시를 사용하여 정수당 비트 수를 자동으로 선택합니다. 또한 Concrete는 자동 노이즈 관리를 허용하여 복잡성을 크게 감소시키고 초보 사용자에게 더 많은 접근성을 제공합니다. 이제 2개의 숫자를 더하는 간단한 회로를 살펴봅시다:

```js
from concrete import fhe

#1. 회로 정의
def add(x, y):
    return x + y

# 2. 회로 컴파일
compiler = fhe.Compiler(add, {"x": "encrypted", "y": "clear"})

# 정수에 사용할 비트 수를 결정하기 위한 예시
inputset = [(2, 3), (0, 0), (1, 6), (7, 7), (7, 1)]
circuit = compiler.compile(inputset)

# 3. 테스트
x = 4
y = 4

# clear evaluation (암호화되지 않음)
clear_evaluation = add(x, y)

# 데이터 암호화, 암호화된 회로 실행, 결과 복호화
homomorphic_evaluation = circuit.encrypt_run_decrypt(x, y)

print(x, "+", y, "=", clear_evaluation, "=", homomorphic_evaluation)
```

그런 다음 컴파일러는 이 회로를 MLIR 형식으로 컴파일하며, 컴파일이 완료되면 사용자에게 표시됩니다:

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
module {
  func.func @main(%arg0: !FHE.eint<4>, %arg1: i5) -> !FHE.eint<4> {
    %0 = "FHE.add_eint_int"(%arg0, %arg1) : (!FHE.eint<4>, i5) -> !FHE.eint<4>
    return %0 : !FHE.eint<4>
  }
}
```

회로가 컴파일되면 FHE 보관고에 추가하여 다른 사람들이 동일한 암호화된 계산을 수행할 수 있는 회로를 공유할 수 있습니다.

![image](/assets/img/2024-05-23-CodinginCipherEncryptedDataStructuresandAlgorithms_3.png)

# FHE Operations

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

IDE에서 사용되는 FHE 스키마는 기본적으로 다음 작업을 지원합니다:

1. 더하기
2. 곱하기
3. 비트 추출 (모든 비트가 개별적으로 암호화되기 때문)
4. 테이블 조회

처음 세 가지는 매우 직관적이지만, 마지막은 주의가 필요합니다. 아래 예시를 살펴봅시다:

```js
table = fhe.LookupTable([2, -1, 3, 0])

@fhe.compiler({"x": "encrypted"})
def f(x):
    return table[x]
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

일반적인 테이블처럼 작동합니다. x=0 이라면 f = 2이며, 나머지도 동일합니다: f(1) = -1; f(2) = 3; f(3) = 0.

테이블 조회는 매우 유연합니다. 덧셈, 뺄셈, 비암호화 값과의 곱셈, 텐서 조작 작업을 제외한 모든 작업 및 일부 기본 작업으로 구성된 몇 가지 작업은 테이블 조회로 변환됩니다. 이러한 작업은 Concrete가 여러 작업을 지원하도록 하지만 사용량이 많기 때문에 비용이 많이 듭니다. 정확한 비용은 많은 변수(사용된 하드웨어, 오류 확률 등)에 따라 달라지지만 다른 작업에 비해 항상 훨씬 비싸지므로 가능한 한 피해야 합니다. 항상 완전히 피하는 것이 항상 가능한 것은 아니지만 일부를 다른 기본 작업과 교체하여 총 테이블 조회 수를 줄이려고 해야 합니다.

# IF Operator / Branching

IF 연산자는 FHE에 기본적으로 포함되어 있지 않으며, 산술적 방법으로 사용해야 합니다. 다음 예제를 살펴보겠습니다:

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
if a > 0:
    c = 4
else:
    c = 5
```

FHE에서는 데이터를 직접적으로 볼 수 없기 때문에 모든 분기를 주의 깊게 다뤄야 합니다. 따라서 코드는 하나는 0이고, 다른 하나는 1인 두 식의 합이 됩니다:

```js
flag = a > 0 # 1 또는 0을 나타냄
c = 4 * flag + 5 * (1 - flag)
```

FHE에서 0은 네이티브가 아님을 기억하세요. 가장 간단한 구현 방법은 look-up table을 사용하는 것입니다. 양수 변수 a가 2비트라고 가정하면, a가 0인 경우를 제외한 모든 (4) 결과에 대해 a` 0이 됩니다. a의 두 비트의 모든 결과에 대한 테이블을 구성할 수 있습니다: '0,1,1,1'. 그럼 회로는 다음과 같을 것입니다:

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
table = fhe.LookupTable([0, 1, 1, 1])

@fhe.compiler({"a": "encrypted"})
def f(a):
    flag = table[a] # a > 0, for 2bit a
    return 4 * flag + 5 * (1 - flag)
```

중요한 점은 a가 2비트 이상으로 커진다면 해당 룩업 테이블의 크기가 매우 빠르게 증가하여 회로의 평가 키 크기가 증가할 수 있다는 것입니다. Concrete FHE 구현에서 이 접근 방식은 비교 연산자의 기본 기능입니다. 예를 들어, 다음 회로를 보겠습니다:

```js
from concrete import fhe

@fhe.compiler({"x": "encrypted"})
def less_then_21(x):
    return x < 21

inputset = [1, 31]

circuit = less_then_21.compile(inputset)

# 결과는 5비트 정수
x = 19
homomorphic_evaluation = circuit.simulate(x)
print(f"homomorphic_evaluation = {homomorphic_evaluation}")
```

컴파일하고 MLIR(컴파일된 회로)를 검사하면 생성된 룩업 테이블을 관찰할 수 있습니다.

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
module {
  func.func @main(%arg0: !FHE.eint<5>) -> !FHE.eint<1> {
    %c21_i6 = arith.constant 21 : i6
    %cst = arith.constant dense<[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]> : tensor<32xi64>
    %0 = "FHE.apply_lookup_table"(%arg0, %cst) : (!FHE.eint<5>, tensor<32xi64>) -> !FHE.eint<1>
    return %0 : !FHE.eint<1>
  }
}
```

# 덧셈을 사용하여 두 숫자 비교

FHE를 사용하여 두 이진 숫자를 비교하는 방법은 간단한 산술을 사용하여 효율적으로 수행할 수 있습니다. 뺄셈을 사용하여 두 이진 수를 비교하는 방법은 이진 산술의 특성을 활용합니다. 핵심 아이디어는 두 숫자를 빼면 결과 및 작업 중에 설정된 특정 플래그(프로세서의 carry 플래그와 같은)을 기반으로 상대적인 크기에 대한 정보가 드러난다는 것입니다.

이진 뺄셈에서 A가 B보다 크거나 같으면 결과가 양수입니다. B가 더 크면 결과가 음수이며 carry 플래그가 1이됩니다.

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

<img src="/assets/img/2024-05-23-CodinginCipherEncryptedDataStructuresandAlgorithms_4.png" />

여기서 의미하는 것은 A가 B라면 carry=1이고 그렇지 않으면 0이라는 것입니다. 우리는 오른쪽에서 왼쪽으로 캐리 비트를 계산해야 하며 마지막 케리가 최종 결과가 됩니다. FHE 계산을 가속화하기 위해 각 비트에 대해 1+ A - B를 계산하여 이를 양수로 만들 수 있습니다. 이 예제는 잔여값을 저장하기 위해 단지 2개의 비트만 필요합니다. 그런 다음 (``)으로 캐리 비트를 2칸 왼쪽으로 이동시키고 잔여값을 더합니다. 모든 결과의 총 수는 8개가 되며, 우리는 이를 룩업 테이블과 함께 사용하여 다음 캐리 비트를 출력할 수 있습니다. 이 회로에서와 같이요.

```js
# 두 숫자는 비트 배열로 표시되어야 합니다
# ---------------------------
# 0 0000 -> 1 개 더 적습니다 (1+0-1), 캐리 비트 설정
# 1 0001 -> 0, 동일 (1+1-1) 또는 (1+0-0)
# 2 0010 -> 0, 크거나 같습니다 (1+1-0)
# 3 0100 -> 0 (존재하지 않음)
# 캐리 비트 설정
# 5 1000 -> 1
# 6 1100 -> 1
# 7 1010 -> 1
# 8 1010 -> 1

from concrete import fhe

table = fhe.LookupTable([1,0,0,0,1,1,1,1])

# 결과는 작으면 1, 그렇지 않으면 0
@fhe.compiler({"x": "encrypted", "y": "encrypted"})
def fast_comparision(x, y):
    carry = 0

    # 모든 비트에 대해
    for i in range(4):
        s = 1 + x[i] - y[i]
        # 2칸 왼쪽으로 이동 (carry << 4)
        carry4 = carry*4 + s
        carry = table[carry4]

    return curry

inputset = [([0,1, 1, 1], [1,0, 1,1])]

circuit = fast_comparision.compile(inputset)

homomorphic_evaluation = circuit.simulate([1,0,1, 0], [1,0,0,0])
print("homomorphic_evaluation =", homomorphic_evaluation)
```

이 방법은 이전 예제와 같이 룩업 테이블을 사용하는 것보다 계산적으로 훨씬 더 비쌉니다. 그러나 여기서는 메모리복잡성이 낮습니다. 왜냐하면 룩업 테이블은 단지 8개의 값을 가지고 있기 때문에 평가 키가 작아집니다. 그리고 예상대로, 아무것도 완벽하지 않습니다. 메모리 사용량 대 CPU 사용량 및 선택한 방법에 따라 키 크기 사이의 교환이 있습니다.

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

# 정렬

버블 정렬을 살펴보겠습니다. 이는 간단한 비교 기반 정렬 알고리즘이며, 정렬할 목록을 반복적으로 확인하면서 인접한 항목 쌍을 비교하고 순서가 잘못된 경우에는 교환합니다. 이 알고리즘은 작은 요소들이 각 반복에서 목록의 맨 위(배열의 시작)로 올라가고, 큰 요소들이 각 반복에서 목록의 맨 아래(배열의 끝)로 가라앉는다는 특징 때문에 이름이 지어졌습니다.

```js
from concrete import fhe
import numpy as np

@fhe.compiler({"in_array": "encrypted"})
def bubble_sort(in_array):
    for i in range(len(in_array)):
        for j in range(len(in_array)-1):
            a = in_array[j]
            b = in_array[j+1]
            flag = a > b
            # if a > b then swap the values
            in_array[j] = flag * b + (1-flag) * a
            in_array[j+1] = flag * a + (1-flag) * b

    return in_array

inputset = [[3,0,0,0]]
circuit = bubble_sort.compile(inputset)

test = [3,2,0,1]
test_clear = test.copy()
test_fhe = test.copy()

clear_evaluation = bubble_sort(test_clear)

#homomorphic_evaluation = circuit.encrypt_run_decrypt(test_fhe)
homomorphic_evaluation = circuit.simulate(test_fhe)

print(test, "=> ", clear_evaluation, "=>", homomorphic_evaluation)
```

버블 정렬은 속도가 꽤 느리지만 [O(n²)] 매우 메모리 효율적입니다 [O(1)]. 더 CPU 효율적인 알고리즘을 원한다면 병합 정렬을 사용할 수 있습니다. 이는 목록을 더 작고 더 관리하기 쉬운 부분(이상적으로는 개별 요소까지)으로 분할하고, 이러한 부분들을 정렬한 다음 올바른 순서대로 다시 병합하는 원리에 기반합니다.

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

![Coding in Cipher: Encrypted Data Structures and Algorithms](/assets/img/2024-05-23-CodinginCipherEncryptedDataStructureandAlgorithms_5.png)

머지 소트는 O(n log n)의 복잡도를 가지며, 대규모 데이터 세트에 대해 가장 효율적인 정렬 알고리즘 중 하나입니다. 그러나 공간 복잡도는 O(n)으로, 임시 병합 과정에 대한 배열 크기에 비례하는 추가 공간이 필요합니다.

# 동적 프로그래밍

동적 프로그래밍은 복잡한 문제를 더 간단한 하위 문제로 분해하여 각 하위 문제를 한 번만 해결하여 해결하는 방법입니다. 아이디어는 더 작은 하위 문제를 효율적으로 해결할 수 있다면 이러한 해결책을 사용하여 더 큰 문제에 대처할 수 있다는 것입니다. 피보나치 수열을 예로 들어보겠습니다.

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

피보나치 수열은 각 수가 이전 두 수의 합인 수열로, 일반적으로 0과 1로 시작합니다. 수열은 보통 0, 1, 1, 2, 3, 5, 8, 13과 같이 이어집니다. 동적 프로그래밍을 사용하여 n번째 피보나치 수를 구할 때, 무분별한 재귀 접근보다 중복 계산을 피하므로 효율적일 수 있습니다.

![이미지](/assets/img/2024-05-23-CodinginCipherEncryptedDataStructuresandAlgorithms_6.png)

위 그림을 보면 F(6)를 구하기 위해 F(5)와 F(4) 두 개의 하위 문제를 재귀적으로 해결해야 합니다. 해결 방법이 겹치는 것을 알 수 있으므로 F(4)의 계산이 나무의 왼쪽과 오른쪽 모두에서 발생합니다. 당연히 각 고유 결과를 캐시하고 한 번만 계산해야 합니다. 그러면 나무가 매우 단순해질 것입니다. 이 접근 방식을 메모이제이션이라고 합니다.

![이미지](/assets/img/2024-05-23-CodinginCipherEncryptedDataStructuresandAlgorithms_7.png)

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

Fully Homomorphic Encryption (FHE)의 맥락에서는 기본 특성과 보안 제약으로 인해 일반적으로 메모이제이션을 사용할 수 없습니다. FHE는 암호화된 데이터에서 작업을 수행할 수 있도록 허용하기 때문에 실제 데이터 값은 계산 중에 숨겨진 상태로 유지됩니다.

동적 프로그래밍의 다른 접근 방식은 타뷸레이션이라고 불립니다. 타뷸레이션은 작은 하위 문제부터 해결하고 그 해결책을 사용하여 더 큰 문제의 해결책을 구축하는 하향식 접근 방식입니다. 이 방법은 FHE에 특히 효과적인데, 이는 비재귀적인 특성을 가지고 있기 때문입니다. 타뷸레이션은 각 단계에서 현재 값을 업데이트하는 테이블을 사용합니다. 이 예시에서는 첫 번째 요소가 0이 되어야하고 두 번째 요소가 1이 되어야하는 기본 조건을 만족하는 6개 요소의 테이블을 초기화합니다. 그런 다음 나머지 요소는 0으로 초기화됩니다: [0,1,0,0,0,0]. 그런 다음, 왼쪽에서 오른쪽으로 진행합니다.

![image](/assets/img/2024-05-23-CodinginCipherEncryptedDataStructuresandAlgorithms_8.png)

이 글은 암호화 데이터 구조 및 알고리즘에 관한 시리즈의 시작을 알립니다. 다음에는 Fully Homomorphic Encryption (FHE) 영역에서 그래프 및 트리, 머신 러닝 및 인공 지능 사용에 대해 탐구할 것입니다. 그 후에는 금융 산업 내에서의 실제 응용 프로그램을 살펴볼 것입니다.

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

# 코딩을 암호화로 변환하시겠어요?

FHE-Studio.com에서 오픈 소스 IDE를 활용하여 암호화된 데이터 구조 및 알고리즘의 세계를 더 깊이 탐험해 보세요. 프로젝트를 최고 수준의 보안 프로토콜로 강화하거나 소프트웨어 개발에서의 데이터 프라이버시 다음 세대에 대해 궁금하다면, FHE Studio는 FHE 세계로의 무료이며 오픈 소스 게이트웨이입니다. 회로를 개발하고 시험하고 공유하며 동료로부터 피드백을 받아 보세요!

전문적인 전문 지식을 찾고 계신가요? FHE Studio의 팀은 완전 홈모픽 암호화를 기존 프로젝트에 통합하거나 귀하의 요구에 맞게 맞춤형 새로운 암호화된 솔루션을 개발하는 데 도와드릴 수 있습니다.

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

# 우리를 지원해주세요

만약 우리 프로젝트에서 가치를 발견했다면, 우리를 지원하는 것을 고려해보세요. 우리는 FHE-Studio를 열려 있고 접근 가능하게 유지하기로 맹세했으며, 모든 기여가 프로젝트를 확장하는 데 도움이 됩니다.

# 참고 자료

- FHE-STUDIO.COM, 오픈 소스 FHE IDE

2. FHE Studio 문서 및 소스, https://github.com/artifirm
3. Concrete FHE 컴파일러: https://docs.zama.ai/concrete
4. Concrete ML은 Fully Homomorphic Encryption (FHE)를 기반으로 하는 오픈 소스 개인 정보 보호 기계 학습 프레임워크입니다. https://docs.zama.ai/concrete-ml
5. Microsoft SEAL, 오픈 소스 FHE 라이브러리 https://www.microsoft.com/en-us/research/project/microsoft-seal/
6. HELib, FHE 라이브러리 https://github.com/homenc/HElib

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

저자의 이미지가 아닌 경우를 제외하고, 모든 이미지는 저자에 의해 제공되었습니다.
