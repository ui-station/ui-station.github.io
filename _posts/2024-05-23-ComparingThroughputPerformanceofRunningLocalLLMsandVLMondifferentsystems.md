---
title: "로컬 LLM 및 다양한 시스템에서 VLM 실행 시 처리량 성능 비교"
description: ""
coverImage: "/assets/img/2024-05-23-ComparingThroughputPerformanceofRunningLocalLLMsandVLMondifferentsystems_0.png"
date: 2024-05-23 16:57
ogImage: 
  url: /assets/img/2024-05-23-ComparingThroughputPerformanceofRunningLocalLLMsandVLMondifferentsystems_0.png
tag: Tech
originalTitle: "Comparing Throughput Performance of Running Local LLMs and VLM on different systems"
link: "https://medium.com/aidatatools/comparing-throughput-performance-of-running-local-llms-and-vlm-on-different-systems-ca4ca82c8edc"
---


데이터 엔지니어로서, 저는 몇 가지 생성적 AI 모델을 시험해 보고 로컬에서 모델을 설치/실행하는 것에 매혹을 느낍니다. Large Language Model (LLM)과 Vision-Language Model (VLM)은 가장 흥미로운 모델입니다. OpenAI는 ChatGPT 웹사이트와 모바일 앱을 제공합니다. Microsoft는 Windows 11 Copilot을 우리가 사용할 수 있도록 만들었습니다. 그러나 우리는 어떤 데이터가 인터넷으로 전송되고 그들의 데이터베이스에 저장되는지를 제어할 수 없습니다. 그들의 시스템은 오픈 소스가 아니며, 마치 신비로운 검은 상자와 같습니다.

일부 관대한 회사(Meta 및 Mistral AI 같은) 또는 개인들이 자신들의 모델을 오픈 소스로 공개하고, 적극적인 커뮤니티가 도구를 단계별로 구축하여 우리가 집 컴퓨터에서 LLM과 VLM을 쉽게 실행할 수 있도록 도와줍니다. Raspberry Pi 5와 8GB RAM은 이 기사에서 시험되었습니다 (Raspberry Pi에서 로컬 LLM 및 VLM 실행). 이는 신용 카드 크기의 소형 Single Board Computer (SBC)입니다. 나는 더 저렴한 컴퓨터 장비/솔루션이나 가상 머신을 찾아 성능을 시험해보고, 돈을 지불하는 대가에 좋은 가치를 제공하거나 심지어 일반 대중에게도 제공하도록 하고 싶습니다. 고려해야 할 사항은 텍스트 출력 속도, 텍스트 출력 품질 및 비용입니다.

생성된 내용을 평가하는 작업은 다른 연구 기관에서 수행됩니다. 예를 들어, 이 기사에서 mistral-7b가 llama2-13b보다 지식, 추론 및 이해력에서 능가한다고 언급됩니다. https://mistral.ai/news/announcing-mistral-7b/. 그래서 나는 LLM 테스트에 mistral과 llama2를 포함했습니다.

Ollama는 현재 macOS, Linux 및 Windows의 WSL2에서 실행할 수 있습니다. WSL2에서 메모리 사용량 및 CPU 사용량을 제어하기 어렵기 때문에 WSL2의 테스트를 제외하였습니다. 생태계에서는 여러 LLM과 VLM 모델을 다운로드할 수 있습니다. 그래서 여러 시스템에서 다양한 AI 모델을 테스트하기 위해 Ollama를 벤치마킹 테스트 베드로 사용합니다. 설치는 매우 간단합니다. 터미널에서 다음을 실행하세요:

<div class="content-ad"></div>

```markdown
```js
curl https://ollama.ai/install.sh | sh
```

저는 Ollama LLMs에서 생성된 토큰/초의 처리량을 테스트하는 도구를 개발했습니다. 이 코드(ollama-benchmark)는 Python3로 작성되었으며 MIT 라이선스 하에 오픈 소스로 공개되어 있습니다. 추가해야 할 기능이 더 있거나 수정해야 할 버그가 있다면 알려주세요. 텍스트 출력 품질을 측정하기 어려울 수 있으므로 이 실험에서는 텍스트 출력 속도에 초점을 맞춥니다. (더 높은 토큰/초가 좋음)

테스트에 사용된 기계 또는 VM의 기술 사양

- 8GB RAM이 장착된 Raspberry Pi 5 (Ubuntu 23.10 64비트 운영 체제) 쿼드코어 64비트 Arm CPU
- Windows 11 랩탑 호스트에 설치된 VMware Player 17.5를 통해 4코어 프로세서와 8GB RAM이 장착된 Ubuntu 23.10 64비트 운영 체제
- Windows 11 데스크톱 호스트에 설치된 VMware Player 17.5를 통해 8코어 프로세서와 16GB RAM이 장착된 Ubuntu 23.10 64비트 운영 체제
- Apple Mac mini (Apple M1 칩) (macOS Sonoma 14.2.1 운영 체제) 8코어 CPU(성능 코어 4개, 효율성 코어 4개), 8코어 GPU, 16GB RAM
- NVIDIA T4 GPU (Ubuntu 23.10 64비트 운영 체제), 8 vCPU, 16GB RAM
```

<div class="content-ad"></div>

비교를 더 효과적으로 하기 위해 Raspberry Pi 5에 우분투 23.10 64비트 운영체제가 설치되었습니다. 아래 비디오에서 운영체제 설치 단계를 따를 수 있습니다.

Ollama 웹사이트 llama2 모델 페이지에는 다음과 같은 내용이 언급되어 있습니다.

## 메모리 요구 사항

- 7b 파라미터 모델은 일반적으로 적어도 8GB의 RAM이 필요합니다.
- 13b 파라미터 모델은 일반적으로 적어도 16GB의 RAM이 필요합니다.

<div class="content-ad"></div>

## 테스트할 모델

- mistral:7b (LLM)
- llama2:7b (LLM), llama2:13b (LLM)
- llava:7b, llava:13b (이미지에서 텍스트로, 이미지로부터 질의응답) (VLM)

메모리 제약 사항을 고려하여, 서로 다른 기기에서 성능을 테스트하고 싶은 모델입니다.

샘플 프롬프트 예시는 benchmark.yml에 저장되어 있습니다.

<div class="content-ad"></div>

```yaml
version: 1.0
modeltypes:
  - type: instruct
    models:
      - model: mistral:7b
    prompts:
      - prompt: 달걀빵을 처음부터 굽는 방법을 단계별 가이드를 작성해주세요.
        keywords: 요리, 레시피
      - prompt: 다음 문제를 해결하는 파이썬 함수를 개발하세요. 수도쿠 게임
        keywords: 파이썬, 수도쿠
      - prompt: 경제 위기에 관한 대화를 나누는 두 캐릭터의 대화를 만들어주세요.
        keywords: 대화
      - prompt: 숲에 용갈퀴가 살고 있습니다. 이야기를 계속해주세요.
        keywords: 문장 완성
      - prompt: 미국 시애틀로 4명을 위한 항공편을 예약하고 싶습니다.
        keywords: 항공편 예약
```

각 라운드마다 5가지 다른 프롬프트가 사용되어 출력 토큰 수를 평가합니다. 이 5가지 수의 평균이 기록됩니다. 저는 처음으로 라즈베리 파이 5를 실행했고, 여기에 기록된 동영상이 있습니다.

다양한 모델의 시스템별 토큰 속도에 대한 벤치마크 요약

## AI 모델 (LLMs 및 VLM) 추론 처리량 성능 결과에 대한 생각

<div class="content-ad"></div>

- 위의 동영상에서 확인할 수 있듯이, 계산 활용은 주로 GPU 코어 및 GPU VRAM에서 발생합니다.
- 추론을 더 빨리 실행하려면 강력한 GPU를 선택하세요.
- 사람과 AI 모델 간의 편안한 상호작용은 초당 7토큰의 처리량이 필요합니다 (예시는 비디오 5에서 제공됨). 대부분의 사람이 따라갈 수 없는 13토큰의 속도는 비디오 6에서 보여주었습니다.
- 미래 OS 중 Copilot이라는 AI 지원이 내장된 OS는 최소 16GB RAM이 필요할 것입니다. AI의 출력은 의미가 있는 것이며 신뢰성이 있어야 하며, 너무 빠르지도, 너무 느리지도 않아야 합니다. 이 부분은 Microsoft가 발표한 소식과 일치합니다: (Microsoft, AI PC용 RAM으로 16GB를 기본 설정 — 해당 기기에는 40 TOPS의 AI 계산 능력이 필요하다고 보도).

## 결론

LLM을 로컬에서 실행함으로써 데이터 보안과 개인 정보 보호를 강화할 수 있을 뿐만 아니라, 전문가, 개발자 및 열정가들을 위한 무한한 가능성을 열어줍니다. 이 처리량 성능 기준에 따라, Raspberry Pi 5를 LLM 추론 기계로 사용하지 않겠습니다. 왜냐하면 너무 느리기 때문입니다. LLM과 VLM을 Apple Mac mini M1 (16GB RAM)에서 실행하는 것이 충분합니다. LLM 추론을 더 빠르게 실행하려면 강력한 기계가 필요하다면 GPU가 탑재된 클라우드 VM을 임대하세요.

책임 성명: 저는 Ollama나 Raspberry Pi, Apple, Google과 관련이 없습니다. 모든 의견과 견해는 제 개인적인 것이며 어떤 조직도 대표하지 않습니다.