---
title: "한정된 AI 자원의 최대 활용 Kubernetes 접근법"
description: ""
coverImage: "/assets/img/2024-06-23-MaximizingtheUtilityofScarceAIResourcesAKubernetesApproach_0.png"
date: 2024-06-23 00:52
ogImage:
  url: /assets/img/2024-06-23-MaximizingtheUtilityofScarceAIResourcesAKubernetesApproach_0.png
tag: Tech
originalTitle: "Maximizing the Utility of Scarce AI Resources: A Kubernetes Approach"
link: "https://medium.com/towards-data-science/maximizing-the-utility-of-scarce-ai-resources-a-kubernetes-approach-0230ba53965b"
---

## 한정된 AI 트레이닝 가속기의 최적 활용

![image](/assets/img/2024-06-23-MaximizingtheUtilityofScarceAIResourcesAKubernetesApproach_0.png)

AI 개발의 끊임없는 변화 속에서 헤라클리투스에게 소속된 것으로 알려진 구단속, "삶에서 유일한 상수는 변화다"라는 말보다 더 진실한 것은 없는 것 같습니다. AI 분야에서는 변화가 단연 지속적이지만, 그 변화의 속도는 영원히 증가하고 있습니다. 이 독특하고 흥미진진한 시기에서 주목할 점은 AI 팀이 지속적으로 적응하고 개발 프로세스를 조정하는 능력을 비롯한 과제입니다. 적응하지 못하는 AI 개발팀이나 적응이 더딘 팀은 빠르게 구식이 될 수 있습니다.

최근 몇 년간 AI 개발에서 가장 어려운 발전 중 하나는 AI 모델을 훈련하는 데 필요한 하드웨어를 확보하기가 점점 어려워지고 있다는 것입니다. 세계적인 공급망 위기나 AI 칩 수요의 상당한 증가 등으로 인해 AI 개발을 위해 필요한 GPU(또는 대체 트레이닝 가속기)를 구하는 것이 더 어려워졌습니다. 신규 GPU 주문에 대한 기다림 시간이 길어진 점이나 한 때 GPU 기계의 거의 무한한 용량을 제공했던 클라우드 서비스 제공업체(CSP)조차 수요를 따라가기 어려워진 점으로 확인할 수 있습니다.

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

변화하는 시대에 따라 AI 개발 팀들은 한때 AI 액셀러레이터의 무한한 용량에 의존했을 수도 있는데, 접근성이 감소하고 경우에 따라 더 높은 비용이 요구되는 세계에 적응해야 합니다. 새 GPU 머신을 언제든 생성할 수 있다는 능력을 당연하게 여겼던 개발 프로세스는 종종 여러 프로젝트 및/또는 팀에 의해 공유되는 희귀한 AI 리소스 세계의 요구를 충족시키기 위해 수정되어야 합니다. 적응하지 못하는 경우 소멸의 위험이 있습니다.

이 게시물에서는 희귀한 AI 리소스 세계에서 AI 모델 훈련 워크로드를 조율하기 위해 Kubernetes의 사용 방법을 보여드릴 것입니다. 먼저 달성하고자 하는 목표를 명시할 것이며, 그 후에 왜 Kubernetes가 이 도전에 대응하는 적절한 도구인지 설명할 것입니다. 마지막으로 Kubernetes를 사용하여 희귀한 AI 계산 리소스의 사용을 극대화하는 방법을 간단히 보여줄 것입니다. 이어지는 게시물에서는 Kubernetes 기반 솔루션을 향상시키고 클라우드 기반 훈련 환경에 적용하는 방법을 보여줄 계획입니다.

## 면책 조항

본 게시물은 Kubernetes에 대한 사전 지식을 요구하지는 않지만, 기본적인 이해가 도움이 됩니다. 이 게시물은 어떠한 방식으로도 Kubernetes 튜토리얼로 간주되어서는 안됩니다. Kubernetes에 대해 배우고 싶다면 관련 온라인 자료를 참조하시기 바랍니다. 여기서는 자원 활용의 극대화와 우선 순위 지정과 관련된 몇 가지 Kubernetes 속성만 논의할 것입니다.

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

여기서 제시한 방법에 대체 도구 및 기술이 많이 있습니다. 각각 장단점이 있습니다. 저희가 이 게시물에서 하고 있는 것은 순전히 교육적인 목적입니다. 우리가 하는 선택 중 어떤 것도 지지한다고 보지 마십시오.

마지막으로, Kubernetes 플랫폼은 지속적인 개발 중에 있으며, AI 개발 분야의 많은 프레임워크 및 도구도 그렇습니다. 이 게시물에서의 몇 가지 설명, 예제 또는 외부 링크가 읽을 때 더 이상 유효하지 않을 수도 있음을 고려하고, 자신의 설계 결정 전에 가장 최신 솔루션을 고려해주시기 바랍니다.

# 희소한 AI 연산 자원에 적응하기

우리의 토론을 간단히 하기 위해, 우리는 모델을 훈련하기 위해 한 대의 워커 노드를 사용할 수 있다고 가정해 봅시다. 이것은 GPU가 장착된 로컬 머신이거나 AWS의 p5.48xlarge 인스턴스나 GCP의 TPU 노드와 같은 클라우드에서 예약된 계산가속 인스턴스일 수 있습니다. 아래의 예시에서는 이 노드를 "내 소중한 자원"이라고 지칭할 것입니다. 일반적으로 우리는 이 기계에 많은 돈을 투자해 왔을 것입니다. 또한 여러 훈련 작업이 단일 연산 자원을 경쟁하며 각 작업이 몇 분에서 며칠까지 소요될 수 있다고 가정할 것입니다. 당연히, 연산 자원의 활용을 극대화하고 중요한 작업을 우선순위로 지정하려고 할 것입니다. 필요한 것은 어떤 형태의 우선순위 큐와 이에 대한 기반 우선순위 기반 스케줄링 알고리즘입니다. 원하는 동작에 대해 조금 더 구체적으로 설명해 보겠습니다.

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

## 일정 요구 사항

- 활용 최대화: 리소스가 지속적으로 사용되도록 하고 싶습니다. 구체적으로 말하자면, 작업을 완료하는 즉시 새로운 작업에 자동으로 시작하도록 합니다.
- 대기 중인 작업 큐: 고유한 리소스에서 처리 대기 중인 훈련 작업 큐의 존재가 필요합니다. 또한 큐에 새 작업을 생성하고 제출하며, 큐의 상태를 모니터링하고 관리하기 위한 관련 API도 필요합니다.
- 우선순위 지원: 각 훈련 작업이 연관된 우선순위를 가지도록 하여 더 높은 우선순위를 갖는 작업이 낮은 우선순위의 작업보다 먼저 실행되도록 합니다.
- 선점: 더 높은 우선순위의 긴급 작업이 제출되었을 때 우리의 리소스가 낮은 우선순위 작업을 처리 중이라면, 실행 중인 작업이 중단되고 긴급 작업으로 교체되기를 원합니다. 중단된 작업은 큐로 반환되어야 합니다.

이러한 요구 사항을 충족하는 솔루션을 개발하기 위한 한 가지 접근 방식은 이미 존재하는 API를 훈련 리소스에 작업을 제출하는 데 사용하고, 원하는 속성이 있는 우선순위 큐의 사용자 정의 구현으로 래핑하는 것입니다. 최소한이 접근 방식에는 대기 중인 작업 목록을 저장하기 위한 데이터 구조, 큐에서 훈련 리소스로 작업을 선택하고 제출하는 전용 프로세스, 작업이 완료되고 리소스가 사용 가능해졌을 때 이를 식별하는 메커니즘이 필요합니다.

대체 접근 방식 및 이 게시물에서 채택하는 방식은 요구 사항을 충족하는 우선순위 기반 일정 관리의 기존 솔루션을 활용하고 교육 개발 워크플로우를 해당 사용에 맞추는 것입니다. Kubernetes와 함께 제공되는 기본 스케줄러가 이러한 솔루션 중 하나의 예입니다. 다음 섹션에서는 희소한 AI 훈련 리소스의 활용을 최적화하는 문제를 해결하는 데 사용할 수 있는 방법을 보여드리겠습니다.

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

# 쿠버네티스를 활용한 ML 오케스트레이션

이 섹션에서는 ML 훈련 워크로드의 오케스트레이션에 쿠버네티스를 적용하는 철학적인 면을 다루겠습니다. 만약 이런 논의에 참여할 여유가 없고 실제 예시로 바로 넘어가고 싶다면 다음 섹션으로 건너뛰어도 괜찮아요.

쿠버네티스는 많은 개발자들 사이에서 강한 반응을 불러일으키는 소프트웨어/기술적인 솔루션 중 하나입니다. 일부는 쿠버네티스를 강력히 지지하고 적극 활용하는 반면, 다른 일부는 무거우며 서투르고 불필요하다고 생각하기도 합니다. 많은 논의와 마찬가지로 저자의 의견은 중간 어딘가에 진실이 있다고 생각합니다. 쿠버네티스는 생산성을 크게 높여줄 수 있는 이상적인 프레임워크를 제공하는 상황과 개발 프로페셔널에게 모욕이 되는 수준까지 갈 수 있는 상황이 모두 존재합니다. 핵심 질문은, ML 개발이 어느 위치에 속하는지인데요. 쿠버네티스가 ML 모델 훈련에 적합한 프레임워크일까요? 온라인 검색을 통해 간단히 얻을 수 있는 정보에서는 거의 대다수가 강력한 "예"라고 결론 내리는 것처럼 보일 수 있지만, 우리는 그것이 항상 옳은 선택인지에 대한 일부 주장을 제시할 것입니다. 그런데 먼저, 쿠버네티스를 사용한 "쿠버네티스를 활용한 ML 훈련 오케스트레이션"이라는 용어가 무엇을 의미하는지 명확히 해야 합니다.

쿠버네티스를 사용한 ML에 대한 다양한 온라인 자료가 있지만, 사용 방식이 항상 일치하는 것은 아니라는 점을 명심해야 합니다. 일부 자료는 오로지 클러스터를 배포하기 위해 쿠버네티스를 사용합니다. 클러스터가 가동되면 쿠버네티스 외부에서 훈련 작업을 시작합니다. 다른 자료는 전용 모듈을 사용해 훈련 작업(및 관련 리소스)을 완전히 다른 시스템에서 시작하는 파이프라인을 정의하기 위해 쿠버네티스를 사용합니다. 위 두 예시와는 대조적으로, 다른 많은 자료들은 훈련 워크로드를 쿠버네티스 노드에서 실행되는 쿠버네티스 Job 아티팩트로 정의합니다. 그러나 각각이 주로 초점을 맞추는 특성들이 상당히 다릅니다. 일부는 오토 스케일링 속성을 강조하고 다른 일부는 다중 인스턴스 GPU(MIG) 지원을 강조합니다. 또한 ElasticJob, TrainingWorkload, JobSet, VolcanoJob 등의 훈련 작업을 나타내는 정확한 아티팩트(Job 확장)에 대한 구현 세부사항도 크게 달라집니다. 이 게시물의 맥락에서도 우리는 훈련 워크로드를 쿠버네티스 Job으로 정의한다고 가정할 것입니다. 그러나 토론을 간단히 하기 위해 쿠버네티스의 핵심 객체만 다루고 ML을 위한 쿠버네티스 확장에 대한 토론은 추후 게시물에서 다루도록 하겠습니다.

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

## 케이버네티스를 머신러닝에 사용하는 것에 반대하는 주장들

여기 케이버네티스를 통해 머신러닝 모델을 학습하는 데에 반대할 수 있는 몇 가지 주장들이 있습니다.

- 복잡성: 케이버네티스는 어렵다는 것은 그 사용자들조차 인정해야 합니다. 케이버네티스를 효과적으로 사용하려면 높은 전문 지식이 요구되며, 가파른 학습 곡선이 존재하고, 현실적으로 말하자면 전문 개발팀이 필요합니다. 케이버네티스를 기반으로 한 학습 솔루션을 설계하는 것은 전문가에 대한 의존성을 증가시키고, 결과적으로 문제가 발생할 수 있는 위험을 증가시키며, 개발이 지연될 수 있다는 위험을 증가시킵니다. 많은 대체 머신러닝 학습 솔루션은 개발자 독립성과 자유를 보장하며 개발 과정에서 버그의 위험을 줄일 수 있습니다.
- 고정된 자원 요구 사항: 케이버네티스의 가장 큰 장점 중 하나인 확장성 — 작업의 수, 클라이언트 수(서비스 응용 프로그램의 경우), 자원 용량 등에 따라 자동으로 컴퓨팅 자원 풀을 확장하고 축소할 수 있는 능력에 대해 가장 주목받는 특성 중 하나입니다. 그러나 머신러닝 학습 작업의 경우 필요한 자원의 수가 일반적으로 학습 중에 고정된다는 점을 고려할 수 있습니다.
- 고정된 인스턴스 유형: 케이버네티스는 컨테이너화된 응용 프로그램을 오케스트레이션하기 때문에 노드 풀 내의 머신 유형에 대해 매우 유연성을 제공합니다. 그러나 머신러닝의 경우 주로 GPU와 같은 전용 가속기를 갖춘 매우 구체적인 기계가 필요합니다. 게다가, 우리의 작업은 종종 매우 특정 인스턴스 유형에서 최적으로 실행될 수 있습니다.
- 단일 애플리케이션 아키텍처: 현대 애플리케이션 개발에서 애플리케이션을 미니 서비스라고 불리는 작은 구성 요소로 분해하는 것이 일반적입니다. 케이버네티스는 이 설계의 핵심 구성 요소로 자주 볼 수 있습니다. 머신러닝 학습 응용 프로그램은 설계상 매우 단일식이며, 이는 마이크로 서비스 아키텍처에 자연스럽게 합치기 어려울 수 있다는 주장이 있습니다.
- 자원 오버헤드: 케이버네티스를 실행하는 데 필요한 전용 프로세스는 각 노드에 일부 시스템 자원을 필요로 합니다. 따라서 이는 학습 작업에 일정한 성능 저하를 초래할 수 있습니다. 학습에 필요한 자원의 비용을 감안할 때, 이를 피하길 선호할 수 있습니다.

물론, 우리는 케이버네티스를 머신러닝을 위한 프레임워크로 선택하는 데 필요한 아주 좋은 이유가 있어야 한다는 한면적인 견해를 가졌습니다. 위에서 제시된 주장에만 근거하여 보면, 케이버네티스를 선택하기 위한 뚜렷한 이유가 필요할 것으로 보입니다. 본 포스트에서 제시된 도전에는 희귀한 AI 컴퓨팅 자원의 유틸리티를 극대화하려는 열망이 바로 위의 주장에도 불구하고 케이버네티스를 사용하게끔 타당하게 만드는 유형의 정당화라고 생각합니다. 저희는 내장된 기본 스케줄러와 우선 순위와 선점을 지원하는 케이버네티스가 이러한 요구 사항을 충족시키는 데 선두주자임을 입증하겠습니다.

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

# 장난감 예시

이 섹션에서는 Kubernetes에 내장된 우선 순위 스케줄링 지원을 보여주는 간단한 예시를 공유하겠습니다. 저희의 데모를 위해 Minikube (버전 v1.32.0)를 사용할 것입니다. Minikube는 로컬 환경에서 Kubernetes 클러스터를 실행할 수 있는 도구로, Kubernetes를 실험하기에 이상적인 플레이그라운드입니다. Minikube의 설치 및 시작하는 공식 문서를 참조해주세요.

## 클러스터 생성

Minikube start 명령을 사용하여 두 노드 클러스터를 생성해보겠습니다.

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

minikube start --nodes 2

로컬 Kubernetes 클러스터를 시작하면 마스터(“control-plane”) 노드인 minikube와 하나의 워커 노드인 minikube-m02가 만들어집니다. 이 노드는 단일 AI 리소스를 시뮬레이트합니다. 이를 고유한 리소스 유형으로 식별하기 위해 my-precious 라벨을 적용해보세요:

kubectl label nodes minikube-m02 node-type=my-precious

Minikube 대시보드를 사용하여 결과를 시각화할 수 있습니다. 다음 명령을 실행하여 새로운 셸에서 생성된 브라우저 링크를 열어보세요.

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
minikube 대시보드
```

왼쪽 창에서 노드 탭을 누르면 클러스터 노드의 요약을 볼 수 있습니다:

<img src="/assets/img/2024-06-23-MaximizingtheUtilityofScarceAIResourcesAKubernetesApproach_1.png" />

## PriorityClass 정의

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

다음으로, 아래에 표시된 priorities.yaml 파일에 low-priority와 high-priority 두 가지 PriorityClasses를 정의합니다. 새 작업은 기본적으로 low-priority 할당을 받게 됩니다.

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: low-priority
value: 0
globalDefault: true

---
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000000
globalDefault: false
```

새 클래스를 클러스터에 적용하기 위해 다음 명령을 실행합니다:

```bash
kubectl apply -f priorities.yaml
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

## 작업 생성

아래 코드 블록에 표시된 job.yaml 파일을 사용하여 간단한 작업을 정의합니다. 우리의 데모를 위해, 100초 동안 아무것도 하지 않는 Kubernetes Job을 정의합니다. Docker 이미지로는 busybox를 사용합니다. 실제로는 이를 학습 스크립트와 적절한 ML Docker 이미지로 대체해야 합니다. 이 작업을 특수 인스턴스인 my-precious에서 실행하도록 nodeSelector 필드를 사용하여 리소스 요구 사항을 지정하여 해당 인스턴스에서 한 번에 하나의 작업 인스턴스만 실행되도록 합니다. 작업의 우선순위는 위에서 정의한 대로 저우선순위로 설정됩니다.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: test
spec:
  template:
    spec:
      containers:
        - name: test
          image: busybox
          command: # 간단한 sleep 명령
            - sleep
            - "100"
          resources: # 모든 가능한 리소스 요구
            limits:
              cpu: "2"
            requests:
              cpu: "2"
      nodeSelector: # 우리의 고유한 리소스 지정
        node-type: my-precious
      restartPolicy: Never
```

다음 명령어로 작업을 제출합니다:

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
kubectl apply -f job.yaml
```

## 작업 대기열 만들기

Kubernetes가 작업을 처리하기 위해 대기열에 넣는 방식을 보여주기 위해 위에서 정의한 작업의 세 개의 동일한 복사본인 test1, test2 및 test3을 만듭니다. 이 세 작업을 jobs.yaml 파일에 그룹화하여 제출합니다:

```js
kubectl apply -f jobs.yaml
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

아래 이미지는 제출 직후 Minikube 대시보드에서 클러스터의 Workload 상태를 캡처한 것입니다. my-precious가 test1을 처리하기 시작했음을 확인할 수 있습니다. 다른 작업들은 차례를 기다리며 대기 중입니다.

![Workload Status](/assets/img/2024-06-23-MaximizingtheUtilityofScarceAIResourcesAKubernetesApproach_2.png)

test1 처리가 완료되면 test2 처리가 시작됩니다:

![Workload Status](/assets/img/2024-06-23-MaximizingtheUtilityofScarceAIResourcesAKubernetesApproach_3.png)

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

만약 우선순위가 높은 다른 작업이 제출되지 않는 한, 우리의 작업은 모두 완료될 때까지 한 번에 하나씩 처리될 것입니다.

## 작업 선점

이제 쿠버네티스가 제공하는 작업 선점 기능을 소개하기 위해 우선순위 설정이 높은 네 번째 작업을 제출할 때 어떻게 작동하는지 보여드리겠습니다:

```js
apiVersion: batch/v1
kind: Job
metadata:
  name: test-p1
spec:
  template:
    spec:
      containers:
        - name: test-p1
          image: busybox
          command:
            - sleep
            - '100'
          resources:
            limits:
              cpu: "2"
            requests:
              cpu: "2"
      restartPolicy: Never
      priorityClassName: high-priority # 높은 우선순위 작업
      nodeSelector:
          node-type: my-precious
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

아래 이미지에서 작업 부하 상태의 영향이 표시됩니다:

![Workload Status](/assets/img/2024-06-23-MaximizingtheUtilityofScarceAIResourcesAKubernetesApproach_4.png)

test2 작업이 중단되었습니다. 더 높은 우선순위의 test-p1 작업을 처리하기 위해 중단되었고, 대신에 my-precious가 처리를 시작했습니다. test-p1이 완료되면 낮은 우선순위 작업의 처리가 재개될 것입니다. (중단된 작업이 ML 훈련 작업인 경우, 최근 저장된 모델 체크포인트로부터 재개되도록 프로그램할 것입니다).

아래 이미지는 모든 작업이 완료된 후의 작업 부하 상태를 보여줍니다.

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

![image](/assets/img/2024-06-23-MaximizingtheUtilityofScarceAIResourcesAKubernetesApproach_5.png)

# 쿠버네티스 확장

우리가 우선순위 기반 스케줄링 및 선점을 위해 시연한 솔루션은 쿠버네티스의 핵심 구성 요소에만 의존했습니다. 실제로는, Kueue와 같은 확장 프로그램이 소개한 기본 기능을 향상시키거나, Run:AI나 Volcano와 같은 쿠버네티스 기반 플랫폼에서 제공되는 전용 ML 특징을 활용할 수 있습니다. 하지만, AI 컴퓨팅 자원의 활용도를 극대화하기 위한 기본 요구사항을 충족하기 위해서는 핵심 쿠버네티스만 있으면 됨을 기억해 주세요.

# 요약

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

AI 실리콘의 가용성이 감소함에 따라 ML 팀은 개발 프로세스를 조정해야 했습니다. 이전과 달리 개발자들이 필요에 따라 AI 리소스를 자유롭게 사용할 수 있던 과거와는 달리, 이제는 AI 컴퓨트 용량에 제약이 생겼습니다. 이는 전용 장치를 구매하거나 클라우드 인스턴스를 예약하는 등의 방법으로 AI 인스턴스를 확보해야 한다는 필요성을 의미합니다. 게다가, 개발자들은 이러한 자원을 다른 사용자 및 프로젝트와 공유해야 할 가능성을 인지해야 합니다. 희소한 AI 컴퓨트 파워가 최대한 활용되도록하기 위해, 유휴 시간을 최소화하고 핵심 워크로드에 우선 순위를 둔 일정 알고리즘을 정의해야 합니다. 본 기사에서는 쿠버네티스 스케줄러를 활용하여 이러한 목표를 달성하는 방법을 보여주었습니다. 앞서 강조했듯이, 이는 희소한 AI 자원의 유틸리티를 극대화하는 도전에 대처하기 위한 다양한 접근 방식 중 하나에 불과합니다. 당연히 선택하는 방식 및 구현 세부사항은 여러분의 AI 개발의 특정 요구에 따라 다를 것입니다.
