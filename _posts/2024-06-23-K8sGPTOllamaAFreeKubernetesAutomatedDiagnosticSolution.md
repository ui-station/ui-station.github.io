---
title: "K8sGPT  Ollama 무료 Kubernetes 자동 진단 솔루션 사용법"
description: ""
coverImage: "/assets/img/2024-06-23-K8sGPTOllamaAFreeKubernetesAutomatedDiagnosticSolution_0.png"
date: 2024-06-23 23:00
ogImage: 
  url: /assets/img/2024-06-23-K8sGPTOllamaAFreeKubernetesAutomatedDiagnosticSolution_0.png
tag: Tech
originalTitle: "K8sGPT + Ollama: A Free Kubernetes Automated Diagnostic Solution"
link: "https://medium.com/@addozhang/k8sgpt-ollama-a-free-kubernetes-automated-diagnostic-solution-d453b63f112f"
---


![Kubernetes Automated Diagnosis Tool: k8sgpt-operator](/assets/img/2024-06-23-K8sGPTOllamaAFreeKubernetesAutomatedDiagnosticSolution_0.png)

주말에 블로그 초고를 확인했더니 이 글이 있었어요. 한 해 전 'Kubernetes Automated Diagnosis Tool: k8sgpt-operator'를 쓸 때의 기억이 떠오르네요. 처음에는 K8sGPT + LocalAI를 써보려 했지만, Ollama로 시도해보니 더 사용하기 편리했어요. 게다가 Ollama는 OpenAI API를 지원하기도 해서 Ollama로 바꾸기로 결정했죠.

k8sgpt-operator를 소개하는 글을 게시한 후 몇몇 독자들이 OpenAI를 사용하기 위한 높은 진입 장벽을 언급했어요. 이 문제는 정말 어려운 문제이지만 극복할 수 있는 문제에요. 하지만 이 글은 그 문제를 해결하는 게 아니라 OpenAI 대안인 Ollama를 소개하기 위한 글이에요. 작년 말에 k8sgpt는 CNCF Sandbox에 들어갔어요.

# 1. Ollama 설치하기

<div class="content-ad"></div>


![Ollama](/assets/img/2024-06-23-K8sGPTOllamaAFreeKubernetesAutomatedDiagnosticSolution_1.png)

Ollama은 로컬이나 클라우드에서 쉽게 설치하고 실행할 수 있는 여러 대형 모델을 지원하는 오픈 소스 대형 모델 도구입니다. 매우 사용하기 편리하며 간단한 명령어로 실행할 수 있습니다. macOS에서는 homebrew를 사용하여 다음 명령어로 쉽게 설치할 수 있습니다:

```js
brew install ollama
```

최신 버전은 0.1.44입니다.


<div class="content-ad"></div>

```js
ollama -v
경고: 실행 중인 Ollama 인스턴스에 연결할 수 없습니다
경고: 클라이언트 버전은 0.1.44입니다
```

리눅스에서는 공식 스크립트로도 설치할 수 있습니다.

```js
curl -sSL https://ollama.com/install.sh | sh
```

Ollama를 시작하고 컨테이너나 K8s 클러스터에서 접근할 수 있도록 환경 변수를 통해 수신 주소를 0.0.0.0으로 설정하세요.

<div class="content-ad"></div>

```js
OLLAMA_HOST=0.0.0.0 ollama start
```

```js
...
time=2024-06-16T07:54:57.329+08:00 level=INFO source=routes.go:1057 msg="127.0.0.1:11434에서 수신 대기 중 (버전 0.1.44)"
time=2024-06-16T07:54:57.329+08:00 level=INFO source=payload.go:30 msg="임베디드 파일 추출 중" dir=/var/folders/9p/2tp6g0896715zst_bfkynff00000gn/T/ollama1722873865/runners
time=2024-06-16T07:54:57.346+08:00 level=INFO source=payload.go:44 msg="동적 LLM 라이브러리 [metal]"
time=2024-06-16T07:54:57.385+08:00 level=INFO source=types.go:71 msg="추론 계산 중" id=0 library=metal compute="" driver=0.0 name="" total="21.3 GiB" available="21.3 GiB"
```

# 2. 큰 모델 다운로드 및 실행하기

4월에 Meta에서 오픈 소스로 공개된 인기 있는 큰 모델 중 하나인 Llama3가 있습니다. Llama3에는 8B와 70B 두 가지 버전이 있습니다.

<div class="content-ad"></div>

맥OS에서 실행 중이고, 8B 버전을 선택했어요. 8B 버전은 4.7GB이며, 빠른 인터넷 연결로 다운로드하면 3-4분이 소요돼요.

```js
ollama run llama3
```

제 M1 Pro에서 32GB 메모리를 사용하고 있는데, 실행하는 데 약 12초 정도 걸려요.

```js
time=2024-06-17T09:30:25.070+08:00 level=INFO source=server.go:572 msg="llama runner started in 12.58 seconds"
```

<div class="content-ad"></div>

각 쿼리마다 약 14초가 소요됩니다.

```js
curl http://localhost:11434/api/generate -d '{
  "model": "llama3",
  "prompt": "Why is the sky blue?",
  "stream": false
}'
```

```js
....
"total_duration":14064009500,"load_duration":1605750,"prompt_eval_duration":166998000,"eval_count":419,"eval_duration":13894579000}
```

# 3. K8sGPT CLI 백엔드 구성하기

<div class="content-ad"></div>

만약 k8sgpt-operator를 테스트하려면 이 단계를 건너뛸 수 있어요.

k8sgpt의 백엔드로 Ollama REST API를 사용할 거에요. 이 API는 추론 제공자로 기능하며, backend 유형은 localai로 선택했어요. LocalAI는 OpenAI API와 호환되며, 실제 제공자는 여전히 Llama를 실행하는 Ollama일 거예요.

```js
k8sgpt auth add --backend localai --model llama3 --baseurl http://localhost:11434/v1
```

이를 기본 제공자로 설정하세요.

<div class="content-ad"></div>

```js
k8sgpt auth default --provider localai
localai로 기본 제공자가 설정되었습니다.

테스트 중:

image-not-exist 이미지를 사용하여 k8s 내에서 Pod를 생성합니다.

kubectl get po k8sgpt-test
이름          준비     상태         다시 시작     나이
k8sgpt-test   0/1     ErrImagePull   0          6초

<div class="content-ad"></div>

에러를 분석하려면 k8sgpt를 사용해보세요.

k8sgpt analyze --explain --filter=Pod --namespace=default --output=json

{
  "provider": "localai",
  "errors": null,
  "status": "ProblemDetected",
  "problems": 1,
  "results": [
    {
      "kind": "Pod",
      "name": "default/k8sgpt-test",
      "error": [
        {
          "Text": "Back-off pulling image \"image-not-exist\"",
          "KubernetesDoc": "",
          "Sensitive": []
        }
      ],
      "details": "Error: Back-off pulling image \"image-not-exist\"\n\nSolution: \n1. Check if the image exists on Docker Hub or your local registry.\n2. If not, create the image using a Dockerfile and build it.\n3. If the image exists, check the spelling and try again.\n4. Verify the image repository URL in your Kubernetes configuration file (e.g., deployment.yaml).",
      "parentObject": ""
    }
  ]
}

# 4. k8sgpt-operator 배포 및 설정하기

<div class="content-ad"></div>

k8sgpt-operator은 클러스터 내에서 k8sgpt를 자동화할 수 있습니다. Helm을 사용하여 쉽게 설치할 수 있어요.

```
helm repo add k8sgpt https://charts.k8sgpt.ai/
helm repo update
helm install release k8sgpt/k8sgpt-operator -n k8sgpt --create-namespace


k8sgpt-operator는 K8sGPT를 구성하고 분석 결과를 출력하는 Result를 위한 두 가지 CRD를 제공합니다.


kubectl api-resources  | grep -i gpt
k8sgpts                                        core.k8sgpt.ai/v1alpha1                true         K8sGPT
results                                        core.k8sgpt.ai/v1alpha1                true         Result


<div class="content-ad"></div>

Ollama의 IP 주소를 baseUrl로 사용하여 K8sGPT를 구성하세요.

```js
kubectl apply -n k8sgpt -f - << EOF
apiVersion: core.k8sgpt.ai/v1alpha1
kind: K8sGPT
metadata:
  name: k8sgpt-ollama
spec:
  ai:
    enabled: true
    model: llama3
    backend: localai
    baseUrl: http://198.19.249.3:11434/v1
  noCache: false
  filters: ["Pod"]
  repository: ghcr.io/k8sgpt-ai/k8sgpt
  version: v0.3.8
EOF
```

K8sGPT CR을 생성한 후, 연산자(operator)가 이를 위한 파드를 자동으로 만듭니다. result CR을 확인하면 동일한 결과가 표시됩니다.

```js
kubectl get result -n k8sgpt -o jsonpath='{.items[].spec}' | jq .
{
  "backend": "localai",
  "details": "Error: Kubernetes is unable to pull the image \"image-not-exist\" due to it not existing.\n\nSolution: \n1. Check if the image actually exists.\n2. If not, create the image or use an alternative one.\n3. If the image does exist, ensure that the Docker daemon and registry are properly configured.",
  "error": [
    {
      "text": "Back-off pulling image \"image-not-exist\""
    }
  ],
  "kind": "Pod",
  "name": "default/k8sgpt-test",
  "parentObject": ""
}
```