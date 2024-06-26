---
title: "깊은 학습 모델이 GPU에서 더 빨리 실행되는 이유 CUDA 프로그래밍 간단 소개"
description: ""
coverImage: "/assets/img/2024-05-23-WhyDeepLearningModelsRunFasteronGPUsABriefIntroductiontoCUDAProgramming_0.png"
date: 2024-05-23 17:17
ogImage:
  url: /assets/img/2024-05-23-WhyDeepLearningModelsRunFasteronGPUsABriefIntroductiontoCUDAProgramming_0.png
tag: Tech
originalTitle: "Why Deep Learning Models Run Faster on GPUs: A Brief Introduction to CUDA Programming"
link: "https://medium.com/towards-data-science/why-deep-learning-models-run-faster-on-gpus-a-brief-introduction-to-cuda-programming-035272906d66"
---

## .to("cuda") 가 무엇을 하는지 이해하고 싶은 분들을 위해

![이미지](/assets/img/2024-05-23-WhyDeepLearningModelsRunFasteronGPUsABriefIntroductiontoCUDAProgramming_0.png)

요즘에는 딥 러닝을 이야기할 때 성능을 향상시키기 위해 GPU를 활용한다고 연관 지어지는 것이 매우 일반적입니다.

GPU(그래픽 처리 장치)는 원래 이미지, 2D 및 3D 그래픽의 렌더링을 가속화하기 위해 설계되었습니다. 그러나 다수의 병렬 작업을 수행할 수 있는 능력으로 인해 그 유용성은 그 이상으로 확장되어 딥 러닝과 같은 응용 프로그램에까지 이어집니다.

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

깊은 학습 모델에 GPU를 사용한 것은 2000년대 중후반 경에 시작되었으며 2012년에 AlexNet이 등장하면서 매우 인기를 끌었습니다. AlexNet은 Alex Krizhevsky, Ilya Sutskever 및 Geoffrey Hinton이 디자인한 합성곱 신경망으로, 2012년 ImageNet 대규모 시각 인식 챌린지(ILSVRC)에서 우승했습니다. 이 승리는 깊은 신경망을 통한 이미지 분류의 효과성 및 대형 모델 학습에 GPU 사용을 보여주어 중요한 이정표가 되었습니다.

이후 이 기술적 발전을 통해 깊은 학습 모델에 GPU를 사용하는 것이 점점 인기를 얻으며, PyTorch나 TensorFlow와 같은 프레임워크 개발에 이바지했습니다.

요즘에는 PyTorch에서 데이터를 GPU로 전송하려면 .to("cuda")만 작성하면 학습이 가속화되는 것으로 예상됩니다. 하지만 실제로 어떻게 GPU 컴퓨팅 성능을 활용하는지 알아볼까요?

신경망, CNNs, RNNs 및 트랜스포머와 같은 깊은 학습 아키텍처는 기본적으로 행렬 덧셈, 행렬 곱셈 및 행렬에 함수를 적용하는 수학 연산을 사용하여 구축됩니다. 따라서 이러한 연산을 최적화하는 방법을 찾으면 깊은 학습 모델의 성능을 개선할 수 있습니다.

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

그러니까, 간단하게 시작해 봅시다. 두 벡터 C = A + B를 추가하고 싶다고 상상해 보세요.

![이미지](/assets/img/2024-05-23-WhyDeepLearningModelsRunFasteronGPUsABriefIntroductiontoCUDAProgramming_1.png)

이를 C에서 간단히 구현하는 방법은 다음과 같습니다:

```js
void AddTwoVectors(float A[], float B[], float C[]) {
    for (int i = 0; i < N; i++) {
        C[i] = A[i] + B[i];
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

위에서 볼 수 있듯이 컴퓨터는 벡터를 반복해서 각 쌍의 요소를 순차적으로 더해야 합니다. 그러나 이러한 작업은 서로 독립적입니다. i번째 쌍의 요소를 더하는 것은 다른 쌍에 의존하지 않습니다. 그래서 만약 이러한 작업들을 병렬로 실행할 수 있다면 어떨까요?

간단한 접근 방식은 CPU 멀티스레딩을 사용하여 모든 계산을 병렬로 실행하는 것일 것입니다. 그러나 딥러닝 모델에서는 수백만 개 요소를 가진 대규모 벡터를 다루게 됩니다. 일반적인 CPU는 동시에 약 10여 개의 스레드만 처리할 수 있습니다. 이때 GPU가 필요한 것입니다! 현대의 GPU는 수백만 개의 스레드를 동시에 실행할 수 있어 이러한 대규모 벡터에 대한 수학적 연산의 성능을 향상시킵니다.

# GPU 대 CPU 비교

CPU 연산이 GPU보다 단일 작업에서 빠를 수 있지만 GPUs의 장점은 병렬화 능력에 있습니다. 이것의 이유는 그들이 서로 다른 목표로 설계되었기 때문입니다. CPU는 가능한 한 빠르게 연산 순서(스레드)를 실행하는 데 설계되었지만(동시에 약 10여 개의 스레드만 실행할 수 있음) GPU는 수백만 개의 연산을 병렬로 실행하는 데 설계되었습니다(개별 스레드의 속도를 희생하면서).

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

아래의 비디오를 확인해보세요:

예를 들어, CPU가 페라리와 같다고 상상해보세요. GPU는 버스입니다. 한 사람을 옮기는 작업이라면, 페라리(CPU)가 더 나은 선택일 것입니다. 그러나 여러 사람을 이동시키는 경우에는, 페라리(CPU)가 한 번에 더 빠르지만, 버스(GPU)는 한 번에 모두를 옮겨 더 빠르게 목적지에 도착하게 됩니다. CPU는 연속적인 작업을 처리하는 데 뛰어나지만 GPU는 병렬 작업에 적합하게 설계되어 있습니다.

![이미지](/assets/img/2024-05-23-WhyDeepLearningModelsRunFasteronGPUsABriefIntroductiontoCUDAProgramming_2.png)

더 높은 병렬 기능을 제공하기 위해 GPU 설계는 데이터 캐싱 및 흐름 제어 보다는 데이터 처리에 더 많은 트랜지스터를 할당합니다. 이는 CPU와는 달리, CPU가 단일 스레드 성능 및 복잡한 명령 실행을 최적화하기 위해 상당 부분의 트랜지스터를 할당하는데 사용하는 것과 대조적입니다.

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

아래 그림은 CPU와 GPU의 칩 자원 분포를 보여줍니다.

![CPU vs GPU Resources](/assets/img/2024-05-23-WhyDeepLearningModelsRunFasteronGPUsABriefIntroductiontoCUDAProgramming_3.png)

CPU는 강력한 코어와 더 복잡한 캐시 메모리 아키텍처(이를 위해 상당한 양의 트랜지스터를 할당)를 갖고 있습니다. 이 설계는 순차 작업의 신속한 처리를 가능하게 합니다. 반면, GPU는 고수준의 병렬성을 달성하기 위해 많은 코어를 갖는 것을 우선시합니다.

이러한 기본 개념을 이해했으니, 실전에서 이러한 병렬 처리 능력을 어떻게 활용할 수 있을까요?

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

# CUDA 소개

딥러닝 모델을 실행할 때, 아마도 PyTorch나 TensorFlow와 같은 인기있는 Python 라이브러리를 사용하게 될 것입니다. 그러나 이러한 라이브러리의 핵심이 C/C++ 코드로 구동된다는 것은 잘 알려져 있습니다. 또한 앞에서 언급했듯이, 처리 속도를 높이기 위해 GPU를 사용할 수 있습니다. 여기서 CUDA가 등장합니다! CUDA는 NVIDIA가 개발한 일반 목적의 처리를 위한 플랫폼으로, Compute Unified Architecture의 약자입니다. 그러므로 게임 엔진에서 그래픽 계산을 처리하는 데 DirectX가 사용되는 것과 달리, CUDA는 개발자가 NVIDIA의 GPU 연산 능력을 그래픽 렌더링에만 한정되지 않고 일반 목적의 소프트웨어 응용프로그램에 통합할 수 있도록 합니다.

이를 구현하기 위해 CUDA는 GPU의 가상 명령어 집합과 특정 작업(예: CPU와 GPU 간의 데이터 이동)에 액세스를 제공하는 간단한 C/C++ 기반 인터페이스인 CUDA C/C++을 제공합니다.

더 나아가기 전에, CUDA 프로그래밍 개념과 용어를 몇 가지 이해해 보겠습니다:

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

- 호스트: CPU 및 해당 메모리를 가리킵니다.
- 장치: GPU 및 해당 메모리를 가리킵니다.
- 커널: 장치(GPU)에서 실행되는 함수를 가리킵니다.

그래서 CUDA를 사용하여 작성된 기본 코드에서 프로그램은 호스트(CPU)에서 실행되며, 데이터를 장치(GPU)에 전송한 후 장치(GPU)에서 실행될 커널(함수)을 시작합니다. 이러한 커널은 병렬로 여러 스레드에 의해 실행됩니다. 실행이 완료되면 결과는 장치(GPU)에서 호스트(CPU)로 다시 전송됩니다.

그러니까 두 벡터를 더하는 문제로 돌아가 봅시다:

```js
#include <stdio.h>

void AddTwoVectors(float A[], float B[], float C[]) {
    for (int i = 0; i < N; i++) {
        C[i] = A[i] + B[i];
    }
}

int main() {
    ...
    AddTwoVectors(A, B, C);
    ...
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

CUDA C/C++에서 프로그래머는 CUDA 스레드에 의해 병렬로 N번 실행되는 C/C++ 함수 인 켤널이라고 불리는 함수를 정의할 수 있습니다.

켤널을 정의하려면 **global** 선언 지정자를 사용하고, 이 켤널을 실행하는 CUDA 스레드의 수는 ... 표기법을 사용하여 지정할 수 있습니다:

```js
#include <stdio.h>

// 켤널 정의
__global__ void AddTwoVectors(float A[], float B[], float C[]) {
    int i = threadIdx.x;
    C[i] = A[i] + B[i];
}

int main() {
    ...
    // N개의 스레드로 켤널 호출
    AddTwoVectors<<<1, N>>>(A, B, C);
    ...
}
```

각 스레드는 켤널을 실행하고 내장 변수를 통해 켤널 내에서 액세스할 수있는 고유한 스레드 ID threadIdx가 제공됩니다. 위의 코드는 크기가 N 인 두 벡터 A와 B를 더하여 결과를 벡터 C에 저장합니다. 순차적으로 각 쌍-wise 추가를 실행하는 루프 대신, CUDA는 우리에게 모든 이러한 작업을 N 개의 스레드를 사용하여 동시에 수행할 수 있도록 허용합니다.

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

하지만 이 코드를 실행하기 전에 다른 수정 작업을 해야 합니다. 커널 함수는 장치(GPU) 내에서 실행되기 때문에 모든 데이터는 장치 메모리에 저장되어야 합니다. 다음 CUDA 내장 함수를 사용하여 이 작업을 수행할 수 있습니다:

```js
#include <stdio.h>

// 커널 정의
__global__ void AddTwoVectors(float A[], float B[], float C[]) {
    int i = threadIdx.x;
    C[i] = A[i] + B[i];
}

int main() {

    int N = 1000; // 벡터의 크기
    float A[N], B[N], C[N]; // 벡터 A, B, C를 위한 배열

    ...

    float *d_A, *d_B, *d_C; // 벡터 A, B, C에 대한 장치 포인터

    // 벡터 A, B, C에 대한 장치 메모리 할당
    cudaMalloc((void **)&d_A, N * sizeof(float));
    cudaMalloc((void **)&d_B, N * sizeof(float));
    cudaMalloc((void **)&d_C, N * sizeof(float));

    // 호스트에서 디바이스로 벡터 A와 B 복사
    cudaMemcpy(d_A, A, N * sizeof(float), cudaMemcpyHostToDevice);
    cudaMemcpy(d_B, B, N * sizeof(float), cudaMemcpyHostToDevice);

    // N 개의 스레드로 커널 호출
    AddTwoVectors<<<1, N>>>(d_A, d_B, d_C);

    // 디바이스에서 호스트로 벡터 C 복사
    cudaMemcpy(C, d_C, N * sizeof(float), cudaMemcpyDeviceToHost);

}
```

커널에 변수 A, B, C를 직접 전달하는 대신 포인터를 사용해야 합니다. CUDA 프로그래밍에서는 커널 런치 내에서 호스트 배열(예: 예제의 A, B, C)을 직접 사용할 수 없습니다. CUDA 커널은 장치 메모리에서 작동하므로 커널이 작동하도록 장치 포인터(d_A, d_B, d_C)를 전달해야 합니다.

이를 넘어서 cudaMalloc을 사용하여 장치에 메모리를 할당하고, cudaMemcpy를 사용하여 호스트와 장치 간에 데이터를 복사해야 합니다.

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

이제 벡터 A와 B의 초기화를 추가하고 코드 끝에 cuda 메모리를 새로고침할 수 있습니다.

```js
#include <stdio.h>

// Kernel 정의
__global__ void AddTwoVectors(float A[], float B[], float C[]) {
    int i = threadIdx.x;
    C[i] = A[i] + B[i];
}

int main() {

    int N = 1000; // 벡터의 크기
    float A[N], B[N], C[N]; // 벡터 A, B, C에 대한 배열

    // 벡터 A와 B 초기화
    for (int i = 0; i < N; ++i) {
        A[i] = 1;
        B[i] = 3;
    }

    float *d_A, *d_B, *d_C; // 벡터 A, B, C에 대한 장치 포인터

    // 벡터 A, B, C에 대한 메모리 장치에 할당
    cudaMalloc((void **)&d_A, N * sizeof(float));
    cudaMalloc((void **)&d_B, N * sizeof(float));
    cudaMalloc((void **)&d_C, N * sizeof(float));

    // 호스트에서 장치로 벡터 A 및 B 복사
    cudaMemcpy(d_A, A, N * sizeof(float), cudaMemcpyHostToDevice);
    cudaMemcpy(d_B, B, N * sizeof(float), cudaMemcpyHostToDevice);

    // N 스레드를 사용하여 커널 호출
    AddTwoVectors<<<1, N>>>(d_A, d_B, d_C);

    cudaDeviceSynchronize(); // 커널 호출 뒤 cudaDeviceSynchronize() 추가

    // 장치에서 호스트로 벡터 C 복사
    cudaMemcpy(C, d_C, N * sizeof(float), cudaMemcpyDeviceToHost);

    // 장치 메모리 해제
    cudaFree(d_A);
    cudaFree(d_B);
    cudaFree(d_C);
}
```

또한, 커널 호출 후에 cudaDeviceSynchronize();를 추가해야 합니다. 이 함수는 호스트 스레드를 장치와 동기화하는 데 사용됩니다. 이 함수가 호출되면 호스트 스레드는 계속 실행하기 전에 이전에 발행된 모든 CUDA 명령이 장치에서 완료될 때까지 기다립니다.

또한 GPU에서 버그를 식별할 수 있도록 일부 CUDA 오류 확인을 추가하는 것이 중요합니다. 이 확인을 추가하지 않으면 코드가 계속해서 호스트 스레드(CPU)를 실행하고 CUDA 관련 오류를 식별하는 것이 어려울 수 있습니다.

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

아래에 두 기술의 구현이 있습니다.

```js
#include <stdio.h>

// 커널 정의
__global__ void AddTwoVectors(float A[], float B[], float C[]) {
    int i = threadIdx.x;
    C[i] = A[i] + B[i];
}

int main() {

    int N = 1000; // 벡터의 크기
    float A[N], B[N], C[N]; // 벡터 A, B 및 C를 위한 배열

    // 벡터 A와 B를 초기화
    for (int i = 0; i < N; ++i) {
        A[i] = 1;
        B[i] = 3;
    }

    float *d_A, *d_B, *d_C; // 벡터 A, B 및 C의 장치 포인터

    // 장치에서 벡터 A, B 및 C에 대한 메모리 할당
    cudaMalloc((void **)&d_A, N * sizeof(float));
    cudaMalloc((void **)&d_B, N * sizeof(float));
    cudaMalloc((void **)&d_C, N * sizeof(float));

    // 호스트에서 장치로 벡터 A와 B 복사
    cudaMemcpy(d_A, A, N * sizeof(float), cudaMemcpyHostToDevice);
    cudaMemcpy(d_B, B, N * sizeof(float), cudaMemcpyHostToDevice);

    // N개의 스레드로 커널 호출
    AddTwoVectors<<<1, N>>>(d_A, d_B, d_C);

    // 오류 확인
    cudaError_t error = cudaGetLastError();
    if(error != cudaSuccess) {
        printf("CUDA 오류: %s\n", cudaGetErrorString(error));
        exit(-1);
    }

    // 모든 CUDA 스레드가 실행될 때까지 대기
    cudaDeviceSynchronize();

    // 장치에서 호스트로 벡터 C 복사
    cudaMemcpy(C, d_C, N * sizeof(float), cudaMemcpyDeviceToHost);

    // 장치 메모리 해제
    cudaFree(d_A);
    cudaFree(d_B);
    cudaFree(d_C);
}
```

CUDA 코드를 컴파일하고 실행하려면 시스템에 CUDA 툴킷이 설치되어 있는지 확인해야 합니다. 그런 다음 NVIDIA CUDA 컴파일러인 nvcc를 사용하여 코드를 컴파일할 수 있습니다. 만약 컴퓨터에 GPU가 없다면 Google Colab을 사용할 수 있습니다. Runtime → 노트 설정에서 GPU를 선택한 후 code.cu 파일에 코드를 저장하고 다음과 같이 실행하면 됩니다:

```js
%%shell
nvcc example.cu -o compiled_example # 컴파일
./compiled_example # 실행

# 버그 감지 산소화 도구로 코드 실행도 가능합니다
compute-sanitizer --tool memcheck ./compiled_example
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

그러나, 우리의 코드는 아직 완벽하게 최적화되지 않았습니다. 위의 예시에서는 크기가 N = 1000인 벡터를 사용했습니다. 그러나 이는 GPU의 병렬화 능력을 완전히 보여주지 못하는 작은 숫자입니다. 또한, 딥러닝 문제를 다룰 때는 종종 수백만 개의 매개변수를 가진 대규모 벡터를 다루게 됩니다. 그러나, 예를 들어 N = 500000으로 설정하고 위의 예시와 같이 1, 500000로 커널을 실행하면 오류가 발생할 것입니다. 이러한 작업을 개선하고 수행하기 위해서는 먼저 CUDA 프로그래밍의 중요한 개념인 Thread 계층 구조를 이해해야 합니다.

# Thread 계층 구조

커널 함수를 호출할 때는 블록의*개수, 블록당*쓰레드\_개수 표기법을 사용합니다. 따라서, 위의 예시에서는 1개의 블록을 N개의 CUDA 쓰레드로 실행했습니다. 그러나, 각 블록은 지원할 수 있는 쓰레드 개수에 제한이 있습니다. 이는 블록 내의 모든 쓰레드가 동일한 스트리밍 멀티프로세서 코어에 있어야 하고 해당 코어의 메모리 자원을 공유해야 하기 때문에 발생합니다.

다음 코드 스니펫을 사용하여 이 제한을 확인할 수 있습니다:

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
int device;
cudaDeviceProp props;
cudaGetDevice(&device);
cudaGetDeviceProperties(&props, device);
printf("블록당 최대 스레드 수: %d\n", props.maxThreadsPerBlock);
```

현재 코랩 GPU에서 스레드 블록 당 최대 1024개의 스레드가 포함될 수 있습니다. 그래서 우리는 예제에서 대량의 벡터를 처리하기 위해 훨씬 더 많은 스레드를 실행하기 위해 더 많은 블록이 필요합니다. 또한, 아래 그림에서 보여지는 대로 블록은 그리드로 구성됩니다.

![CUDA Programming](/assets/img/2024-05-23-WhyDeepLearningModelsRunFasteronGPUsABriefIntroductiontoCUDAProgramming_4.png)

이제 스레드 ID는 다음과 같이 액세스할 수 있습니다:

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

```c
__global__ void AddTwoVectors(float A[], float B[], float C[], int N) {
    int i = blockIdx.x * blockDim.x + threadIdx.x;
    if (i < N) // 배열 범위를 초과하지 않도록
        C[i] = A[i] + B[i];
}
```

그래서 우리의 스크립트는 다음과 같아졌습니다:

```c
#include <stdio.h>

// 커널 정의
__global__ void AddTwoVectors(float A[], float B[], float C[], int N) {
    int i = blockIdx.x * blockDim.x + threadIdx.x;
    if (i < N) // 배열 범위를 초과하지 않도록
        C[i] = A[i] + B[i];
}

int main() {
    int N = 500000; // 벡터 크기
    int threads_per_block;
    int device;
    cudaDeviceProp props;
    cudaGetDevice(&device);
    cudaGetDeviceProperties(&props, device);
    threads_per_block = props.maxThreadsPerBlock;
    printf("블록 당 최대 쓰레드 수: %d\n", threads_per_block); // 1024

    float A[N], B[N], C[N]; // 벡터 A, B 및 C를 위한 배열

    // 벡터 A와 B 초기화
    for (int i = 0; i < N; ++i) {
        A[i] = 1;
        B[i] = 3;
    }

    float *d_A, *d_B, *d_C; // 벡터 A, B 및 C를 위한 장치 포인터

    // 벡터 A, B 및 C를 위한 장치에 메모리 할당
    cudaMalloc((void **)&d_A, N * sizeof(float));
    cudaMalloc((void **)&d_B, N * sizeof(float));
    cudaMalloc((void **)&d_C, N * sizeof(float));

    // 벡터 A와 B를 호스트에서 장치로 복사
    cudaMemcpy(d_A, A, N * sizeof(float), cudaMemcpyHostToDevice);
    cudaMemcpy(d_B, B, N * sizeof(float), cudaMemcpyHostToDevice);

    // 여러 블록과 블록 당 쓰레드 수로 커널 호출
    int number_of_blocks = (N + threads_per_block - 1) / threads_per_block;
    AddTwoVectors<<<number_of_blocks, threads_per_block>>>(d_A, d_B, d_C, N);

    // 오류 확인
    cudaError_t error = cudaGetLastError();
    if (error != cudaSuccess) {
        printf("CUDA 오류: %s\n", cudaGetErrorString(error));
        exit(-1);
    }

    // 모든 CUDA 쓰레드가 실행될 때까지 대기
    cudaDeviceSynchronize();

    // 벡터 C를 장치에서 호스트로 복사
    cudaMemcpy(C, d_C, N * sizeof(float), cudaMemcpyDeviceToHost);

    // 장치 메모리 해제
    cudaFree(d_A);
    cudaFree(d_B);
    cudaFree(d_C);
}
```

# 성능 비교

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

다음은 다른 벡터 크기에 대해 CPU 및 GPU 연산을 비교한 것입니다.

![Comparison of CPU and GPU computation](/assets/img/2024-05-23-WhyDeepLearningModelsRunFasteronGPUsABriefIntroductiontoCUDAProgramming_5.png)

어떤 경우에는 대규모 벡터 크기 N에 대해 GPU 처리의 이점이 뚜렷해집니다. 또한, 시간 비교는 커널/함수의 실행만을 고려한 것임을 기억해주세요. 호스트와 장치 간 데이터 복사에 소요되는 시간은 고려되지 않았는데, 일반적으로 큰 문제가 아닐 수 있지만, 우리의 경우에는 단순 덧셈 연산만 수행하기 때문에 상당히 중요합니다. 따라서, GPU 계산은 고도로 계산 집약적이고 또한 고도로 병렬화된 계산을 다룰 때만 그 이점을 나타냄을 기억하는 것이 중요합니다.

# 다차원 스레드

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

좋아요, 이제 간단한 배열 작업의 성능을 향상시키는 방법을 알게 되었습니다. 그러나 딥 러닝 모델을 다룰 때는 행렬 및 텐서 작업을 처리해야 합니다. 이전 예제에서는 N 스레드를 사용하여 1차원 블록만 사용했습니다. 그러나 최대 3차원까지 다차원 스레드 블록을 실행할 수도 있습니다. 행렬 작업을 실행해야 할 경우 편리하게 NxM 스레드의 스레드 블록을 실행할 수 있습니다. 이 경우에는 행 = threadIdx.x, 열 = threadIdx.y와 같이 행렬 행 및 열 인덱스를 얻을 수 있습니다. 또한 편리하게 number_of_blocks 및 threads_per_block을 정의하는 데 dim3 변수 유형을 사용할 수 있습니다.

아래 예제는 두 행렬을 더하는 방법을 보여줍니다.

```js
#include <stdio.h>

// Kernel definition
__global__ void AddTwoMatrices(float A[N][N], float B[N][N], float C[N][N]) {
    int i = threadIdx.x;
    int j = threadIdx.y;
    C[i][j] = A[i][j] + B[i][j];
}

int main() {
    ...
    // 1개의 NxN 스레드 블록을 사용하여 커널 호출
    dim3 threads_per_block(N, N);
    AddTwoMatrices<<<1, threads_per_block>>>(A, B, C);
    ...
}
```

이 예제를 여러 블록을 처리할 수 있도록 확장할 수도 있습니다:

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
#include <stdio.h>

// Kernel definition
__global__ void AddTwoMatrices(float A[N][N], float B[N][N], float C[N][N]) {
    int i = blockIdx.x * blockDim.x + threadIdx.x;
    int j = blockIdx.y * blockDim.y + threadIdx.y;
    if (i < N && j < N) {
        C[i][j] = A[i][j] + B[i][j];
    }
}

int main() {
    ...
    // Kernel invocation with 1 block of NxN threads
    dim3 threads_per_block(32, 32);
    dim3 number_of_blocks((N + threads_per_block.x - 1) ∕ threads_per_block.x, (N + threads_per_block.y - 1) ∕ threads_per_block.y);
    AddTwoMatrices<<<number_of_blocks, threads_per_block>>>(A, B, C);
    ...
}
```

이런 멀티 차원 데이터를 다루는 법을 알게 되어서 좋습니다. 또한, 커널 내에서 함수를 호출하는 방법을 알아보겠습니다. 기본적으로 이는 **device** 선언 지정자를 사용하여 간단히 수행할 수 있습니다. 이는 기기(GPU)에서 직접 호출할 수 있는 함수를 정의합니다. 따라서 이러한 함수들은 오직 **global** 또는 다른 **device** 함수에서만 호출할 수 있습니다. 아래 예시는 시그모이드 연산을 벡터에 적용하는 방법을 보여줍니다.

```js
#include <math.h>

// Sigmoid function
__device__ float sigmoid(float x) {
    return 1 / (1 + expf(-x));
}

// Kernel definition for applying sigmoid function to a vector
__global__ void sigmoidActivation(float input[], float output[]) {
    int i = threadIdx.x;
    output[i] = sigmoid(input[i]);

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

그래서, 이제 CUDA 프로그래밍의 기본적인 중요한 개념을 알았으니 CUDA 커널을 만들기 시작할 수 있어요. 딥 러닝 모델의 경우, 그들은 기본적으로 합, 곱셈, 컨볼루션, 정규화 등과 같은 매트릭스 및 텐서 연산들의 집합입니다. 예를 들어, 단순한 행렬 곱셈 알고리즘은 다음과 같이 병렬화될 수 있어요:

![Image](/assets/img/2024-05-23-WhyDeepLearningModelsRunFasteronGPUsABriefIntroductiontoCUDAProgramming_6.png)

```js
// GPU 버전

__global__ void matMul(float A[M][N], float B[N][P], float C[M][P]) {
    int row = blockIdx.x * blockDim.x + threadIdx.x;
    int col = blockIdx.y * blockDim.y + threadIdx.y;

    if (row < M && col < P) {
        float C_value = 0;
        for (int i = 0; i < N; i++) {
            C_value += A[row][i] * B[i][col];
        }
        C[row][col] = C_value;
    }
}
```

이제 이것을 아래의 두 행렬 곱셈의 일반 CPU 구현과 비교해보세요:

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
// CPU 버전

void matMul(float A[M][N], float B[N][P], float C[M][P]) {
    for (int row = 0; row < M; row++) {
        for (int col = 0; col < P; col++) {
            float C_value = 0;
            for (int i = 0; i < N; i++) {
                C_value += A[row][i] * B[i][col];
            }
            C[row][col] = C_value;
        }
    }
}
```

GPU 버전에는 더 적은 루프가 있어서 작업이 빨라진다는 것을 알 수 있습니다. 아래는 NxN 행렬 곱셈의 CPU와 GPU 성능 비교입니다:

![performance-comparison](/assets/img/2024-05-23-WhyDeepLearningModelsRunFasteronGPUsABriefIntroductiontoCUDAProgramming_7.png)

행렬의 크기가 커질수록 GPU 처리의 성능 향상이 더 큰 것을 관찰할 수 있습니다.

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

이제 기본 신경망을 고려해 보세요. 주로 y = σ(Wx + b) 작업을 포함하는데, 아래 그림과 같이 구성됩니다:

![Neural Network Operations](/assets/img/2024-05-23-WhyDeepLearningModelsRunFasteronGPUsABriefIntroductiontoCUDAProgramming_8.png)

이러한 작업은 주로 행렬 곱셈, 행렬 덧셈, 배열에 함수를 적용하는 것으로 이루어져 있습니다. 병렬화 기술에 익숙하신 분들이라면 이미 이들을 알고 계실 것입니다. 따라서 이제 GPU에서 실행되는 자체 신경망을 처음부터 구현할 수 있는 능력이 생겼습니다!

# 결론

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

이 게시물에서는 GPU 처리에 대한 입문적인 개념을 다루어 딥러닝 모델의 성능을 향상시키는 방법에 대해 알아보았어요. 그러나 본 포스트에서 다룬 내용은 기초적인 것들뿐이며, 더 많은 것을 배울 수 있습니다. PyTorch와 Tensorflow와 같은 라이브러리는 최적화 기술을 구현하고 있으며, 최적화된 메모리 액세스, 배치 연산 등과 같은 보다 복잡한 개념들을 활용합니다 (이들은 cuBLAS 및 cuDNN과 같은 CUDA 기반 라이브러리 위에서 구축된 라이브러리를 활용합니다). 그러나 "cuda"로 지정하고 GPU에서 딥러닝 모델을 실행할 때 무슨 일이 벌어지는지에 대한 배경 정보를 이해하는 데 이 게시물이 도움이 되기를 바랍니다.

향후 게시물에서는 CUDA 프로그래밍에 관련된 더 복잡한 개념들을 소개할 예정이에요. 의견을 주시거나 다음에 대해 무엇을 쓰기를 원하시는지 알려 주시면 감사하겠어요! 읽어주셔서 너무 감사해요! 😊

# 추가 자료

NVIDIA CUDA 프로그래밍 문서 — NVIDIA CUDA 프로그래밍 가이드.

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

CUDA 문서 — NVIDIA의 완전한 CUDA 문서입니다.

CUDA 신경망 훈련 구현 — 순수 CUDA C++로 구현된 신경망 훈련입니다.

CUDA LLM 훈련 구현 — 순수 CUDA C로 구현된 LLM의 훈련입니다.
