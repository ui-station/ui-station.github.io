---
title: "GKE  Gemma  Ollama 유연한 LLM 배포를 위한 파워 트리오 "
description: ""
coverImage: "/assets/img/2024-05-18-GKEGemmaOllamaThePowerTrioforFlexibleLLMDeployment_0.png"
date: 2024-05-18 16:52
ogImage:
  url: /assets/img/2024-05-18-GKEGemmaOllamaThePowerTrioforFlexibleLLMDeployment_0.png
tag: Tech
originalTitle: "GKE + Gemma + Ollama: The Power Trio for Flexible LLM Deployment 🚀"
link: "https://medium.com/google-cloud/gke-gemma-ollama-the-power-trio-for-flexible-llm-deployment-5f1fa9223477"
---

오늘은 구글 젬마를 중점으로 다양한 LLM 배포의 복잡성을 탐색하겠습니다. 선택한 플랫폼은 Ollama 프레임워크로부터 가치 있는 지원을 받는 GKE일 것입니다. 이 이정표를 달성하기 위한 여정은 Open WebUI에 의해 용이하게 이끌어질 것입니다. 이는 원래 OpenAI ChatGPT 프롬프트 인터페이스와 놀라운 유사성을 가지고 있어 사용자 경험을 원활하고 직관적으로 만들어줍니다.

세부적인 내용에 대해 들어가기 전에, 가장 중요한 부분에 대해 이야기해 봅시다. 왜 먼저 이 방향으로 나아가는 것일까요? 제게는 이 이유가 명백하게 보이며, 여러 가지 강력한 요소로 요약될 수 있습니다:

- 비용 효율성: 공개 클라우드 인프라에서 LLM을 운영할 경우, 특히 예산 제약으로 집중적인 작은 조직이나 연구 기관에게 더 경제적인 솔루션을 제공할 수 있습니다. 그러나 Vertex AI Studio 및 OpenAI Developer Platform과 같은 플랫폼이 이미 경제적이고 완전히 플래시된 관리 서비스를 제공한다는 조건부 혜택을 강조하는 것이 중요합니다. 또한 Vertex AI는 모델의 수명 주기와 가시성을 관리할 것입니다. 이를 염두에 두세요.
- 맞춤화와 유연성: Ollama는 맞춤화, 유연성 및 오픈 소스 원칙을 핵심으로 만들어졌습니다. 클라우드 제공 업체의 모델 레지스트리를 통해 사용 가능한 포괄적인 모델 옵션에도 불구하고, 원하는 특정 모델이 즉시 사용 가능하지 않은 시나리오가 발생할 수 있습니다. 이때 Ollama가 해결책을 제공합니다.
- 환경 간 이식성: Ollama의 디자인은 클라우드 및 플랫폼에 중립적이며, Docker를 수용하는 모든 공개 또는 개인 플랫폼에 배포할 자유를 제공합니다. 이는 Vertex AI 및 SageMaker와 같은 강력한 솔루션과 대조적입니다. 이 솔루션들은 그들 각자의 클라우드 환경과 결합되어 있습니다. Docker와 Kubernetes가 시장 전체를 섭렵한 이유가 있습니다. 이것은 x86에 대해서도 마찬가지입니다.
- 개인 정보 보호 및 데이터 제어: 완전히 오픈 소스 모델인 LLaVA 및 젬마 같은 것을 활용하려는 사람들을 위해 완전히 개인적인 프레임워크 내에서 데이터 개인 정보 보호와 배포 환경에 대한 완전한 제어를 보장할 수 있는 최적의 방법을 제공합니다.

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

## GKE 플랫폼

이 실험에서 나의 GKE 플랫폼 설정은 효율성과 성능을 우선시했습니다:

- GKE 1.27 (정기 채널): 호환성 및 최신 구글 쿠버네티스 엔진 기능에 대한 액세스를 보장합니다.
- Container-Optimized OS: 보다 빠른 워크로드 배포를 위해 노드 시작 시간을 줄입니다 (이전 글에서 자세히 읽을 수 있습니다).
- g2-standard-4 노드 풀 (NVIDIA L4 GPU): GPU 및 CPU 리소스의 강력한 조합으로, 머신 러닝 작업에 이상적입니다. 벤치마크 결과가 장점을 보여줄 것입니다.
- 관리되는 NVIDIA GPU 드라이버: GKE에 드라이버를 직접 통합하여 설정 프로세스를 간소화하며, 클러스터가 준비되면 즉시 사용할 수 있도록 합니다.

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

NVIDIA의 L4 GPU는 원시 사양과 결과에서 강력한 처리 능력을 발휘하며, 계산 집중적인 ML 작업에 대한 강력한 처리 능력을 제공합니다:

- 7680 Shader Processors, 240 TMUs, 80 ROPs, 60 RT cores, 240 Tensor Cores.
- 24GB GDDR6 메모리, 300GB/s 대역폭.
- 485 teraFLOPs (FP8 처리량).

G2 Machine Series는 Intel Cascade Lake 기반의 플랫폼으로, GPU를 보완하고 그것에 충분한 처리를 제공하기 위한 탁월한 솔루션을 제공합니다.

G2는 Spot VM을 지원합니다: 중지를 허용할 수 있는 ML 작업에 대해 대규모 비용 절감(약 67% 할인)을 제공합니다.

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

## 올라마 및 오픈 웹UI 배포하기 (이전 올라마 웹UI)

K8s 생태계의 성숙함으로 인해 배포 프로세스가 간소화되었으며, 이제는 주로 helm install 및 kubectl apply 명령을 실행하는 문제입니다. 올라마의 경우, 배포는 GitHub에서 제공되는 커뮤니티 주도의 Helm Chart를 활용하며, 설정을 안내하는 표준 values.yaml 파일이 제공됩니다:

```js
ollama:
  gpu:
    enabled: true
    type: 'nvidia'
    number: 1
  models:
    - gemma:7b
    - llava:13b
    - llama2:7b
persistentVolume:
  enabled: true
  size: 100Gi
  storageClass: "premium-rwo"
```

한편, Open WebUI를 배포하는 경우에는 공식 차트와 커뮤니티에서 제공하는 Kustomize 템플릿을 사용하여 이 구현에 더 적합한 접근방식을 제공합니다:

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

Open WebUI은 Ollama 배포를 위한 매니페스트를 제공하지만, 저는 Helm Chart의 기능 풍부함을 선호했습니다. 배포 후에는 GCP 로드 밸런서의 IP 주소와 포트 8080으로 이동하여 Open WebUI 로그인 화면에 액세스할 수 있어야합니다.

![이미지](/assets/img/2024-05-18-GKEGemmaOllamaThePowerTrioforFlexibleLLMDeployment_1.png)

ollama 네임스페이스에서 간단한 확인 작업을 수행하면 모든 시스템이 운영 중인지 확인할 수 있습니다.

![이미지](/assets/img/2024-05-18-GKEGemmaOllamaThePowerTrioforFlexibleLLMDeployment_2.png)

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

한 번 경험해보자! 왜 하늘은 파란색일까요?

![이미지](https://miro.medium.com/v2/resize:fit:1388/1*0CM7W3rKWJbuMEGsAGkC5Q.gif)

이것은 실시간 영상입니다 — NVIDIA L4의 Gemma 7B가 빠른 속도로 결과를 제공합니다! 직접 시도해 보고 싶나요? Ollama에서 모델을 배포하는 것은 아주 간단합니다. 단순히 ollama run gemma:7b를 사용하면 됩니다.

## GPU 대 CPU — 속도의 문제

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

이제 플랫폼이 준비되어 있는데, 난 좋은 벤치마크 세션을 참여할 수 없다는 걸 알고 있지 😉. 다른 모델들에 걸쳐 두 종류의 벤치마크를 실행했어:

- 고전적인 왜 하늘은 파랗게 보일까? 질문: Gemma 2B 및 7B, 그리고 LLaMA v1.6 7B와 13B에게 질문을 했지. 멀티모달과 유니모달 LLM을 테스트해 봐야 하거든!
- 사진 속에 뭔가 있니? LLaMA v1.6 7B와 13B를 대상으로: 여기서 이미지 분석에 초점을 맞췄어.

걱정 마, 내가 완전한 LLM 격렬한 대결을 벌일 생각은 없어 — 그건 전혀 다른 문제고, 내 이해 범위를 넘어서지. 내 목표는 다른 머신 유형이 속도와 반응성에 미치는 영향을 추적하는 거였어.

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

GPU 가속없이는 추론 성능이 주로 원시 CPU 성능과 메모리 대역폭에 의존했습니다. 당연히, 저는 CPU나 메모리 제한 없이 Ollama를 배포했고, CPU 활용률을 확인했습니다. 그러나 추론 작업은 종종 메모리 대역폭 가용성과 메모리 아키텍처에 의해 병목 현상을 겪을 수 있습니다.

첫 번째 그래프는 몇 가지 주요 지표를 보여줍니다:

- 총 소요 시간: 모델이 입력을 처리하고 응답을 생성하는 데 걸리는 시간입니다.
- 응답 토큰/초: 모델이 출력을 생산하는 속도를 측정한 지표입니다.
- 월간 비용: 선택한 구성을 한 달 동안 실행하는 데의 재정적 영향을 보여줍니다.

![그래프 이미지](/assets/img/2024-05-18-GKEGemmaOllamaThePowerTrioforFlexibleLLMDeployment_4.png)

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

여기에는 많은 것이 풀어야 할 문제가 있지만, 경고로 시작하고 싶습니다: 여러분이 지금 볼 성능 숫자는 특정 시나리오에 대한 것으로만 나타나는 것에 주목하세요. LLM의 세계는 매우 넓고 빠르기 때문에, 현재 이 그림조차 며칠 안에 완전히 관련이 없어질 수도 있습니다. 약간 다른 시나리오에서도 그렇습니다.

GPU 우세성:

- GPU는 CPU보다 대폭으로 낮은 대기 시간을 제공합니다(초당 더 많은 토큰). 심지어 $12,000/월에 180개의 전용 CPU 코어도 경쟁할 수 없습니다.
- NVIDIA L4는 이전 T4보다 15% 더 빠른 속도를 제공하지만, 비용은 78% 증가했습니다. 지속 사용 할인이 반영되었습니다.
- A100은 번개처럼 빠르지만, L4보다 약 세 배 더 빠르며, 높은 가격과 교육에 중점을 둔 점 때문에 대부분의 추론 작업에는 지나친 것으로 보입니다. 그럼에도 불구하고 3.6초 미만에 답변하는 데 성공했습니다 🤯.

CPU 고민:

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

- 작은 CPU는 명백히 느리고 놀랍도록 비싸다.
- 비용이 비슷한 CPU들도 (c3-highcpu-22 / c3d-highcpu-16) 처리량 면에서 L4 및 T4보다 뒤처진다.
- 가장 큰 CPU들 (c3-standard-176 / c3d-standard-360)은 그들의 엄청난 비용에 비해 성능이 나쁘다.
- C3는 나쁘게 확장되는데, 이것은 ollama/llama.cpp, 내 설정 또는 C3 인스턴스와 그들의 vNUMA 토폴로지 부족과 관련된 잠재적인 문제일 수 있다. 하지만, 가격 때문에 그것은 의미가 없어진다.

이제 이미지 인식 프롬프트를 살펴보자. 이번에 선택한 모델은 13B 파라미터를 가진 LLaVA v1.6이다.

![이미지](/assets/img/2024-05-18-GKEGemmaOllamaThePowerTrioforFlexibleLLMDeployment_5.png)

GPU의 성능 우위는 여기서도 유효하며, CPU는 이 도메인에서 단순히 경쟁할 수 없음을 보여준다. 흥미로운 점은 c3-standard-176이 마침내 c3-highcpu-22를 능가했는데, 이것은 C3나 내 설정에 버그가 있음을 의심하는 것을 해소시켰다.

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

전통에 따라 모든 결과는 다음 Google 시트에서 공개적으로 이용 가능합니다:

Ollama에 대해 몇 가지 포인트를 논의하기 전에, 이 환경에서 사용된 정확한 SHA와 태그를 공유하고 싶습니다. AI 세계가 빠르게 변화하고 있어, 제 작업을 재현하려는 누군가는 길을 나가면 얼마든지 다른 환경을 발견할 수 있습니다:

- ollama v0.1.29;
- Gemma 2B SHA b50d6c999e59
- Gemma 7B SHA 430ed3535049
- LLaVA v1.6 7B SHA 8dd30f6b0cb1
- LLaVA v1.6 13B SHA 0d0eb4d7f485

그리고 벤치마크가 실행된 방식에 대해:

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
curl http://localhost:8080/api/generate -d \
'{
  "model": "gemma:7b",
  "prompt": "Why is the sky blue?",
  "stream": false,
  "options": {"seed": 100}
}'
```

```js
curl http://localhost:8080/api/generate -d \
'{
  "model": "llava:13b",
  "prompt":"What is in this picture?",
  "images": ["iVBORw0KGgoAAAANSUhEUgAAAG0AAABmCAYAAADBPx+VAAAACXBIWXMAAAsTAAALEwEAmpwYAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAA3VSURBVHgB7Z27r0zdG8fX743i1bi1ikMoFMQloXRpKFFIqI7LH4BEQ+NWIkjQuSWCRIEoULk0gsK1kCBI0IhrQVT7tz/7zZo888yz1r7MnDl7z5xvsjkzs2fP3uu71nNfa7lkAsm7d++Sffv2JbNmzUqcc8m0adOSzZs3Z+/XES4ZckAWJEGWPiCxjsQNLWmQsWjRIpMseaxcuTKpG/7HP27I8P79e7dq1ars/yL4/v27S0ejqwv+cUOGEGGpKHR37tzJCEpHV9tnT58+dXXCJDdECBE2Ojrqjh071hpNECjx4cMH...
```

결과를 기록하는 동안 사용된 설정은 다음과 같습니다:

- Ollama API와의 직접 통신
- 스트리밍 비활성화
- 모든 프롬프트에 동일한 시드값 사용함.

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

## 올라마의 현재 한계: 더 깊게 파헤쳐보기

올라마 프로젝트가 빠르게 발전 중임을 기억하는 것이 중요하지만, 전문 사용자가 알아야 할 중요한 제한 사항을 조사하는 것이 유용합니다:

- 저장소 병목 현상: registry.ollama.ai에 갇히면 혁신과 실험을 억눌게 됩니다. 도커가 Quay.io를 넘어서지 않았다면 어땠을까 상상해보세요! 해결책이 있을 수 있지만, 다양한 모델 소스에 대한 네이티브 솔루션이 큰 발전을 이룰 것이며, 커뮤니티에서 이미 제안을 했습니다.
- 병렬성을 통한 기회의 놓침: 올라마의 순차적 요청 처리가 현실 성능을 제한합니다. 사용자가 괴롭힘을 겪는 높은 트래픽 시나리오를 상상해보세요. 좋은 소식은 병렬 디코딩이 llama.cpp에 합병되었으며 v0.1.30 주기 중에 풀 리퀘스트된 것입니다. 이 문제 #358을 주시해야 합니다.
- AVX512의 실망과 새로운 선택지: AVX512 최적화가 기대한 성능 향상을 제공하지 못하는 것은 실망스럽습니다. 실제로 더 좋게 만들려는 시도를 해봤지만 현실을 직면하게 되었습니다: AVX512는 별로다, AVX2보다 느리다 😭 (물론 코어 클록은 절반 이상), 그리고 "AVX512가 고통스러운 죽음을 맞기를 희망합니다". Intel AMX는 더 밝은 전망을 제시합니다. 경쟁력 있는 가격, 초기 벤치마크 결과, 특정 작업 부하에서 GPU를 앞서갈 수 있는 잠재력 등이 흥미로운 대안으로 나타납니다. 이 주제에 대해, The Next Platform의 AI 추론이 주로 CPU에서 이루어질 이유에 대한 깊은 의견을 강력히 권장합니다.

## 주요 결론

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

GKE에서 Ollama와 함께 LLM을 배포하는 것은 맞춤화, 유연성, 잠재적인 비용 절감 및 LLM 솔루션 내에서의 개인 정보 보호를 우선시하는 사용자들에게 매력적인 옵션을 제공합니다. 이 접근 방식은 상업 플랫폼에서 사용할 수 없는 모델을 활용할 수 있게 해주며 배포 환경에 대한 완전한 제어권을 제공합니다. 중요한 점은 GPU 가속이 최적의 LLM 성능을 위해 필수적이며, 강력한 CPU 기반 인스턴스조차 크게 앞서갑니다. 그러나 Ollama의 현재 제약 사항, 예를 들어 레지스트리 의존성 및 순차적 요청 처리와 같은 것에 주의를 기울이는 것이 중요합니다. 이러한 제약 사항은 Ollama가 계속 성장함에 따라 해결될 가능성이 크며, 그 가능성을 더욱 높일 것입니다.

즐거운 시간 보내셨길 바라며, 저에게 궁금한 점이 있으시면 언제든지 댓글을 남겨주세요.
