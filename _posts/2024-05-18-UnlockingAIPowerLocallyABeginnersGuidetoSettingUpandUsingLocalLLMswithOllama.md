---
title: "로컬 LLMs를 활용하는 초보자를 위한 설정 및 사용 가이드 Ollama와 함께 AI 파워 지역적으로 해제하기"
description: ""
coverImage: "/assets/img/2024-05-18-UnlockingAIPowerLocallyABeginnersGuidetoSettingUpandUsingLocalLLMswithOllama_0.png"
date: 2024-05-18 21:06
ogImage:
  url: /assets/img/2024-05-18-UnlockingAIPowerLocallyABeginnersGuidetoSettingUpandUsingLocalLLMswithOllama_0.png
tag: Tech
originalTitle: "Unlocking AI Power Locally: A Beginner’s Guide to Setting Up and Using Local LLMs with Ollama"
link: "https://medium.com/@jasuca/unlocking-ai-power-locally-a-beginners-guide-to-setting-up-and-using-local-llms-with-ollama-f26f70a742d1"
---

<img src="/assets/img/2024-05-18-UnlockingAIPowerLocallyABeginnersGuidetoSettingUpandUsingLocalLLMswithOllama_0.png" />

이 가이드는 Ollama와 같은 지역 대형 언어 모델 (LocalLLMs)의 혁신적인 세계를 소개합니다. 이들이 AI의 개인 정보 보호, 맞춤화 및 접근성을 어떻게 재정의하는지 깊이 파헤치며, 초보자든 기술 애호가든 사용자 친화적인 방식과 현실적인 통찰력을 통해 기기에서 LLM의 힘을 활용하는 방법을 배웁니다. AI가 더 개인적이고 안전하며 당신의 필요에 맞게 맞춤화된 미래로 뛰어들어보세요. 프로그래머가 아닌 사람들을 위해 빠르게 시작할 수 있는 AnythingLLM 튜토리얼도 확인해보세요.

# 목차

- 소개
- 지역 대형 언어 모델 (LocalLLMs)이란?
  – 정의 및 설명
  – 지역에서 LLM 실행의 장점
- Ollama 탐구: 지역 추론의 필수품
  – 지역 추론을 위한 Ollama의 다른 프레임워크들과의 차이
  – 대안 프레임워크 및 독특한 특징
  – 컴퓨터에 Ollama 설정하기: 단계별 안내서
- 모델에 대한 심층 탐구
  – Llama2
  – Llama2-비격식
  – Code Llama
  – Mistral
  – LLaVA
- 과정: 문서부터 답변까지
  – 자세한 단계: 문서 불러오기부터 응답 검색까지
- 지역 대형 언어 모델과 상호작용하기 위한 프론트엔드 옵션
  – 1. AnythingLLM: 기술을 잘 모르는 사람을 위해
  – 2. 터미널: 기본 명령어에 익숙한 사람을 위해
  – 3. 코드 (Python + LangChain + Streamlit): 기술 애호가를 위해
- 결론
- 추가 자료 및 참고 문헌

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

# 소개

인공지능(AI)의 빠르게 변화하는 환경에서 대규모 언어 모델(Large Language Models, LLMs)은 혁신의 중심 엔진으로 등장했습니다. 이 모델들은 자연어 처리, 콘텐츠 생성 및 기타 영역을 혁신적으로 변화시키고 있습니다. 기존에는 LLMs의 활용이 주로 클라우드 기반 서비스에 의존했지만, 이는 강력한 계산 능력을 갖추었지만 개인 정보 보호 문제, 제한된 제어권, 사용자 정의의 부재 등과 같은 단점을 가지고 있습니다. 이러한 배경은 사용자의 개인 장치나 개인 서버에서 실행할 수 있는 이러한 강력한 모델의 변형인 로컬 LLMs로 크게 전환되고 있음을 보여줍니다. 이러한 변화는 단순히 개인 정보 보호 및 데이터 보안을 강화하기 위한 것뿐만 아니라, 소유 시스템 내에서 전례없는 사용자 정의 및 통합 능력을 열어줍니다. 결과적으로, LLMs가 더 접근 가능하고 유연하며 다재다능해지는 환경이 조성됩니다. 이는 그들의 유틸리티와 응용 프로그램을 근본적으로 변경합니다.

오픈 소스 LLMs로 가는 움직임은 이러한 장점을 더욱 강화시키고, 사실상 AI 기술의 정점에 접근할 수 있도록 접근성을 민주화하고 있습니다. 메타의 Llama와 Mistral AI의 Mistral과 같은 오픈 소스 모델이 투명성, 유연성 및 커뮤니티 중심의 지원의 중요성을 강조하며, 이 혁신적인 파도를 주도하고 있습니다. 닫힌 시스템과는 달리 이러한 오픈 소스 LLMs는 기업과 개발자들에게 고객 서비스 경험 개선이나 코드 생성을 최적화하는 등에 AI 도구를 자사 요구 사항에 맞게 맞춤화할 수 있는 기회를 제공합니다. 이 사용자 정의 능력은 혁신을 촉진하고 다양한 분야에서 효율성을 높입니다.

이 변화 속에서 데이터 보안에 대한 쉬운 설정, 적응성 및 강력한 제어력으로 뛰어난 프레임워크인 Ollama가 돋보이고 있습니다. 이 글은 더 넓은 AI 개발 환경에서 로컬 LLMs의 중요성을 밝히고 오픈 소스 모델을 수용하는 현실적인 이점을 강조하는 데 초점을 맞추었습니다. Ollama를 효과적으로 설정하고 배포하는 방법에 대해 상세히 설명하여, 사용자들이 다양한 응용 프로그램에서 로컬 LLMs의 능력을 완전히 활용할 수 있도록 돕고자 합니다.

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

로컬에서 LLM을 실행하는 가치를 강조하는 Reddit 토론이, 개인 및 기관이 LocalLLMs로의 이동을 선택하는 다양한 이유를 적절하게 강조합니다. 데이터 개인 정보 보호가 강화되고 AI 능력이 특정 요구에 맞게 맞춤화되는 등 다양한 이유가 그중에 있습니다. 이러한 전환은 보다 포괄적인 산업의 오픈 소스 솔루션으로 향하는 흐름과 일치하여, AI 개발 및 배포에 대한 더 포괄적이고 유연하며 안전한 접근 방식을 제공합니다.

시작하기 전에 강조하는 것은, 글쓴이의 로컬LLMs에 대한 탐구가 OpenAI, Anthropic, 또는 Gemini과 같은 클라우드 기반 LLM 서비스에 반대로 비롯된 것이 아니라는 것입니다. 이러한 플랫폼들은 의심할 여지 없이 놀라운 도구들이며, 여러 측면에서는 맞춤화되고 정교한 수준의 능력을 제공합니다. 이 글의 목적은 그 가치를 감소시키거나 LocalLLMs가 모든 사용 차원에서 우수하다고 제안하는 것이 아닙니다. 대신, LocalLLMs에 초점을 맞춘 것은 특히 개인 정보 보호, 맞춤화, 그리고 데이터 보안과 같은 독특한 장점과 기회를 강조하고자 하는 욕구에서 비롯된 것입니다. 여기서 논의된 많은 개념과 기술은 클라우드 LLM을 활용할 때에도 동등하게 적용되며 막대한 혜택을 제공합니다. 로컬과 클라우드 기반 모델 중에서 선택하는 것은 최종적으로 각 사용자 또는 기관의 특정한 요구사항, 제약 조건, 그리고 목표에 따라 다릅니다. 두 접근 방식은 가치 있으며, 많은 경우에 넓은 AI 도구 및 기술 생태계의 보완적 구성 요소입니다.

# LocalLLMs란 무엇인가요?

LLM(Large Language Models)은 인공 지능에서 가능한 범위를 재정의하며, 자연어의 이해, 생성, 상호작용 등에서 이전에 없던 능력을 제공합니다. 이러한 모델은 일반적으로 클라우드 기반 플랫폼을 통해 접근되어 왔지만 새로운 패러다임이 주목받고 있습니다: Local Large Language Models 또는 LocalLLMs입니다. 이러한 LLM 버전은 개인 컴퓨터, 개인 서버 또는 엣지 디바이스와 같은 로컬 하드웨어에 설치되고 실행할 수 있습니다. 로컬 배포로의 이동은 LLM 기술의 접근성과 적용 가능성에 있어서 중요한 진화를 나타냅니다.

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

## 정의 및 설명

LocalLLMs는 최신 기술로 주목받고 있는 인공지능 모델로, 기사 작성, 코드 생성, 질문 답변 등 다양한 작업을 수행할 수 있는 능력으로 유명합니다. 그러나 이 모델들의 핵심적인 차이점은 운영 프레임워크에 있습니다. LocalLLMs는 인터넷을 통해 액세스하는 원격 서버에 의존하는 대신, 사용자의 하드웨어에서 직접 작동합니다. 이는 모든 데이터 처리 및 모델 추론 작업이 로컬에서 수행되므로, 지속적으로 인터넷 연결이나 외부 서버로 데이터 전송이 필요하지 않다는 것을 의미합니다.

## LocalLLMs를 로컬 환경에서 실행하는 장점

LocalLLMs로의 전환은 기술적인 새로움뿐만 아니라 디지털 시대의 다양한 요구사항과 우려에 대한 대응입니다. 개인 정보 보호와 데이터 보안이 가장 중요한 장점으로 부각됩니다. LocalLLMs를 사용하면 기밀 데이터가 사내에 머묾하면서도 데이터 유출이나 불법적인 액세스와 같은 위험을 감소시킬 수 있습니다. 게다가 LocalLLMs를 로컬에서 실행하면 모델이 언제, 어떻게 업데이트 또는 사용자 정의할지를 포함하여 사용자가 모델을 완전히 제어할 수 있습니다. 이러한 제어는 성능 최적화에도 확장됩니다. 사용자는 모델 매개변수를 자신의 하드웨어와 응용 프로그램 요구 사항에 맞게 조정할 수 있습니다. 더불어 지연 시간을 크게 줄일 수 있는데, 인터넷을 통해 데이터를 클라우드 서버로 전송하고 다시 받아오지 않아도 되기 때문에 비상 상황에서 빠른 응답 시간을 보장할 수 있습니다.

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

# Ollama 탐험: 로컬 인퍼런스를 위한 당신의 선택지

Ollama는 로컬 머신에서 언어 모델을 구축하고 실행하기 위한 매우 인기 있는 가벼운 확장 가능한 프레임워크입니다. 백만 회 이상의 Docker pull을 기록하면서, 대용량 언어 모델(LLM)과 상호작용하려는 사람들에게 개인 데이터를 제 3자 서비스로 전송하지 않으면서 이를 수행하기 위한 해결책으로 자리매김했습니다. Ollama를 특히 매력적으로 만드는 주요 기능은 사용 편의성, 확장 가능성 및 강력한 모델 기능입니다. 텍스트 생성, 언어 번역, 창의적인 콘텐츠 작성, 유익한 질의응답을 지원합니다. Ollama는 연구자와 개발자를 위한 가치 있는 도구이며, 챗봇, 요약 도구 및 창의적인 글쓰기 어시스턴트와 같은 언어 기반 응용 프로그램을 구축하기 위한 훌륭한 플랫폼입니다.

## Ollama가 로컬 인퍼런스를 위한 다른 프레임워크들 사이에서 빛나는 이유

![이미지](/assets/img/2024-05-18-UnlockingAIPowerLocallyABeginnersGuidetoSettingUpandUsingLocalLLMswithOllama_1.png)

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

Ollama는 몇 가지 독특한 장점을 통해 자신을 구별합니다:

- 사용 편의성: Ollama는 사용자 친화적으로 설계되어 간단한 설치 및 사용 프로세스로 이전 언어 모델 경험이 없는 사람들에게도 접근할 수 있습니다.
- 확장성: 사용자는 사용자 지정 모델을 만들거나 다른 소스에서 모델을 가져와 도구를 특정 요구 사항에 맞게 조정할 수 있는 막강한 유연성을 제공합니다.
- 강력하고 다재다능: Ollama의 모델은 다재다능하며 뛰어난 성능을 제공하여 다양한 자연어 처리 작업을 수행할 수 있습니다.
- 커뮤니티 및 생태계: 활발한 Discord 커뮤니티(3만명 이상의 회원)와 다양한 커뮤니티 통합을 통해 Ollama는 로컬 추론 주변의 활기찬 생태계를 육성합니다.

## 대안 프레임워크 및 그들의 독특한 기능

Ollama는 로컬 추론을 위한 폭넓은 기능 세트를 제공하지만, 각각 고유한 강점을 가진 다른 프레임워크도 언급할 가치가 있습니다:

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

- llama.cpp: 고도로 최적화된 성능으로 알려진 llama.cpp는 ROCm으로 인해 상당한 크기를 차지하지만 지원되는 GPU에서 특히 성능 향상을 제공하는 별도의 이미지입니다.
- GPT4All: 제공된 소스에서 명시적으로 언급되지는 않았지만, 일반적으로 GPT 모델의 접근성과 배포의 용이성에 중점을 둔 것으로 알려져 있습니다.
- Mozilla-Ocho의 llamafile: 제공된 소스에서는 이 프레임워크에 대해 자세히 설명하지 않았지만, Mozilla의 오픈 소스 프로젝트에 대한 기여는 주로 개인 정보 보호, 보안 및 커뮤니티 협업을 강조합니다.

Ollama의 사용 편의성, 확장성, 강력한 기능은 서버의 대형 언어 모델에 대한 로컬 추론을 이용하려는 모든 사용자에게 뛰어난 선택지가 됩니다. llama.cpp와 같은 대안 프레임워크가 최적화된 성능을 제공하는 반면, Ollama의 넓은 인기 요인은 해당 프레임워크의 접근성, 활기찬 커뮤니티, 및 지원하는 통합 환경에 있습니다.

# 컴퓨터에 Ollama 설정하기: 단계별 가이드

Ollama는 LocalLLM 세계에서 쉬운 설치와 사용으로 두각을 나타냅니다. macOS와 Windows에서 추론을 실행하는 간편한 방법을 제공합니다. 이 안내서를 통해 두 운영 체제에 Ollama를 설정하는 방법을 안내해 드릴 것이며, 여러분의 프로젝트에 로컬 LLM의 성능을 활용할 준비를 할 수 있을 것입니다.

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

## macOS 사용자를 위한 안내

옵션 1: Homebrew 사용하기

- 터미널 열기: 유틸리티 폴더에서 터미널 애플리케이션을 찾거나 Spotlight를 사용하여 검색하세요.
- Homebrew 설치: Homebrew가 이미 설치되어 있지 않은 경우, 다음 명령을 터미널에 붙여넣고 Enter 키를 눌러 Homebrew를 설치할 수 있습니다. 이 명령은 Homebrew 웹사이트에서도 사용할 수 있습니다.

```js
/bin/bash -c " $(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh) "
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

화면에 나오는 안내에 따라 설치를 완료해 주세요.

3. Ollama 설치: Homebrew를 설치한 후에는 다음 명령어를 터미널에서 실행하여 Ollama를 설치할 수 있습니다:

```js
brew install ollama
```

이 명령은 Homebrew 저장소에서 Ollama 패키지를 가져와 설치합니다.

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

옵션 2: 설치 프로그램 사용하기

- 설치 프로그램 다운로드: Ollama 웹사이트를 방문하여 macOS 설치 프로그램을 다운로드할 수 있습니다. 컴퓨터로 설치 프로그램을 다운로드하세요.
- 설치 프로그램 실행: 다운로드한 파일을 찾아서(일반적으로 다운로드 폴더에 위치함) 더블 클릭하여 열어주세요. 시스템에 Ollama를 설치하려면 설치 안내에 따라 진행하세요.

## Windows 사용자를 위한 안내

설치 프로그램 사용하기

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

- 설치 프로그램 다운로드: 올라마 웹사이트로 이동해서 다운로드 섹션을 찾아서 윈도우용 설치 프로그램을 선택하세요. 파일을 컴퓨터로 다운로드합니다.
- 설치하기: 다운로드한 설치 프로그램을 저장된 위치(일반적으로 다운로드 폴더)로 이동해서 두 번 클릭하세요. 안내에 따라 설치 단계를 진행하면서 시스템에 올라마를 설정할 수 있습니다.

## 올라마 서버 시작 및 모델 실행

![Ollama 이미지](/assets/img/2024-05-18-UnlockingAIPowerLocallyABeginnersGuidetoSettingUpandUsingLocalLLMswithOllama_2.png)

올라마가 설치되면 서버를 시작하고 모델을 실행할 수 있습니다.

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

- Ollama Server 시작: 터미널(macOS)이나 명령 프롬프트(Windows)를 열고 다음 명령을 입력하세요:

```js
ollama serve
```

이 명령을 입력하면 Ollama 서버가 시작되어 요청을 처리할 준비가 됩니다.

- 모델 실행: 특정 모델을 실행하려면 아래 명령을 사용하고, `[LLM]`을 사용하려는 모델의 이름으로 바꿔주세요:

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

```bash
ollama run [LLM]
```

예를 들어, Llama2 모델을 실행하려면 다음을 입력하십시오:

```bash
ollama run llama2
```

## 모델 라이브러리 탐색하기

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

Ollama는 다양한 요구 사항과 응용 프로그램에 적합한 모델 라이브러리를 제공합니다. 사용 가능한 모델을 살펴보려면 Ollama 웹사이트를 방문하거나 다음 명령을 사용하여 터미널이나 명령 프롬프트에서 모델 목록을 직접 확인할 수 있습니다:

```js
ollama list-models
```

이 명령을 통해 Ollama와 실행할 수 있는 모델의 개요를 제공받아 프로젝트 요구 사항에 가장 잘 맞는 모델을 선택할 수 있습니다.

## 추가 자료

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

Ollama 튜토리얼을 참조하여 더 자세한 지침, 문제 해결 및 고급 기능을 확인해보세요. 이 자료는 macOS 및 Windows에서 Ollama의 기능을 최대한 활용할 수 있도록 종합적인 지침을 제공합니다.

컴퓨터에 Ollama를 설정하는 것은 간단하며, 개인 또는 전문적인 용도로 LocalLLMs의 방대한 잠재력을 발휘할 수 있도록 합니다. AI 모델을 실험하거나 애플리케이션을 개발하거나 연구를 수행하든, Ollama는 여러분의 노력을 지원하기 위한 강력하고 유연한 플랫폼을 제공합니다.

# 모델의 심층 탐구

로컬 대형 언어 모델(Local Large Language Models, LocalLLMs)의 영역은 자연어 처리 및 이를 넘어 다양한 필요에 부응하는 각종 모델들을 보여줍니다. 최신 세부 정보를 기반으로 여러분이 관심 있는 모델들에 대한 업데이트된 개요는 다음과 같습니다:

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

## 라마2

2023년에 Meta AI에 의해 출시된 라마2는 사전 학습 및 미세 조정된 다양한 크기의 LLM 패밀리입니다. 이 모델들은 70억(7B), 130억(13B), 700억(70B) 등 다양한 크기로 제공되며 텍스트 생성 및 프로그래밍 코드와 같은 다양한 자연어 처리 작업에 능숙합니다. 특히 대화 시나리오에 최적화된 라마2 모델은 Google 변환기 아키텍처를 기반으로 구축되었으며 다양한 공개 데이터를 통해 훈련되었습니다. 그들의 효율성과 공개적인 이용 가능성은 더 작은 조직 및 연구자들에게 특히 유용하며, 상당한 컴퓨팅 자원이 없어도 지역 인스턴스를 배포할 수 있게 합니다.

## 라마2-무자비

라마2-무자비는 라마2 모델 계통에서 나온 것이지만, 보편적으로 LLM에서 내재되어 있는 콘텐츠 중재 및 안전 메커니즘을 피해 특징을 갖추고 있습니다. George Sung과 Jarrad Hope가 제작했으며, Eric Hartford가 제시한 방법론을 따릅니다. 이 변형본은 더 넓은 범위의 필터되지 않은 응답을 제공하며, 콘텐츠 생성의 자유도를 높여 주지만, 불안전하거나 부적절한 결과물을 생산할 가능성도 높입니다. 창의적인 자유와 콘텐츠 안전성 사이에서 트레이드 오프를 강조합니다.

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

## 코드 람마

코드 람마 가족의 정상에 위치한 Code Llama 70B는 코딩 작업을 위해 명시적으로 설계되어 있으며, 산업 기준을 제시하는 코드 생성 및 이해 능력을 자랑합니다. 7B에서 70B 매개변수로 확장되는 모델은 코드 완성, 채움, 코딩과 관련된 자연어 지시사항 해석에서 뛰어난 성과를 거두고 있습니다. 프로그래밍 언어의 다양한 배열을 지원하며, 연구 및 상업적 노력에 접근 가능하며, 개발자와 프로그래머들에게 상당한 자산을 제공합니다.

## 미스트랄

Younes Belkada와 Arthur Zucker가 개발한 Mistral은 Hugging Face를 통해 제공되며, 지시에 맞게 조정된 모델링의 진보를 대표합니다. 이전 모델을 향상한 Mistral-7B-Instruct-v0.2는 인과적 언어 모델링 작업을 위해 맞춤화되어 있으며, 보다 간략한 버전인 Mistral-tiny에 대한 적응이 가능하며, 다양한 컴퓨팅 자원에 사용할 수 있습니다. 이 모델은 강력한 LLM(Large Language Model)을 보다 쉽게 사용할 수 있도록 한 발전을 증명하고 있습니다.

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

## LLaVA

2023년 NeurIPS에서 소개된 LLaVA (Large Language and Vision Assistant)는 언어와 시각 처리 능력을 융합한 협업 프로젝트입니다. LLaVA 모델의 구체적인 세부 사항은 상세히 문서화되어 있지 않지만, 그 기초는 LLaMA 팀의 기여에 기반합니다. 이는 Alpaca와 Vicuna와 같은 다른 오픈 소스 프로젝트와 통합됩니다. LLaVA의 창시는 크리에이티브 커먼즈 저작자표시-동일조건변경허락 4.0 국제 라이센스 하에 이루어졌으며, AI 분야에서의 오픈 소스 협업 및 혁신에 대한 약속을 강조합니다.

이러한 LocalLLMs는 각각의 능력과 전문성을 갖춘 구별된 모델로, 자연 언어 및 코딩 애플리케이션에서의 AI 혁신의 광범위한 영역을 엿볼 수 있습니다. Llama2, Llama2-Uncensored, Code Llama 70B, Mistral 및 LLaVA와 같은 모델이 각각 독특한 강점을 가지고 있지만, 그것들은 더 크고 빠르게 진화하는 AI 분야의 일부입니다. 간략히 설명하기 위해 이러한 모델만을 강조했지만, 분야가 다양한 두드러진 모델로 가득 차 있으며, 각각이 장단점을 제공합니다.

LocalLLMs의 세계는 새로운 모델과 진보가 규칙적으로 등장하며, 이러한 모델의 잠재력에 흥미를 느끼고 이 흥미진진한 분야를 더 깊이 파고들기를 원하는 분들을 위해 Hugging Face와 같은 플랫폼 방문을 권장합니다. 여기서는 여기서 언급된 것 외에도 상세한 문서와 커뮤니티 통찰을 제공하는 다양한 모델을 탐색할 수 있습니다. Hugging Face는 AI 연구 및 응용 분야에 대한 활기찬 허브로, 사용자들은 다양한 모델뿐만 아니라 최신 개발 및 AI 커뮤니티 내의 창의적 노력에 대한 다양한 게시물 및 토론에 참여할 수 있습니다.

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

이 탐험에 초대합니다. 오늘 사용 가능한 도구를 발견하는 것뿐만 아니라 AI의 미래를 형성하는 커뮤니티에 참여하는 것에 관한 것입니다. 개발자, 연구자 또는 AI 애호가이든지, 다양한 지식과 자원을 통해 당신이 필요로 하는 완벽한 모델을 찾을 수 있도록 돕거나 이 커다란 분야에 기여하도록 영감을 줄 수 있습니다. 그래서 LocalLLMs의 세계로 뛰어들어 Hugging Face와 그 이상을 탐험하고 싶은 호기심 많은 분들을 기다리고 있습니다.

# 과정: 문서부터 답변까지

원시 문서부터 통찰력 있는 답변까지의 여정은 Local Large Language Models (LocalLLMs)을 통해 일련의 핵심 구성 요소를 거칩니다: 원본 문서, 임베딩 및 벡터 데이터베이스(Vector DB). 이러한 요소들이 상호 작용하는 방법을 이해하면 LLMs의 능력이 빛을 발하게 됩니다. 정보를 처리하는 능력 뿐만 아니라 가치 있는 통찰력으로 변화시키는 것도 가능합니다. 이 과정을 단계별로 살펴보면, 그 간단함과 효율성을 보여줄 것입니다.

![image](/assets/img/2024-05-18-UnlockingAIPowerLocallyABeginnersGuidetoSettingUpandUsingLocalLLMswithOllama_3.png)

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

- 문서: 이들은 프로세스에 사용되는 원자료입니다. 문서는 간단한 텍스트 파일이나 기사부터 레포트나 이메일과 같은 보다 구조화된 데이터까지 다양할 수 있습니다. 이 문서들의 내용을 분석하거나 검색하거나 응답 생성하는 데 사용할 것입니다.
- 임베딩: 문서 내용을 LLM이 이해하고 사용할 수 있도록 하기 위해 이를 임베딩으로 변환합니다. 임베딩은 텍스트의 의미적 의미와 관계를 캡처하는 고차원 숫자 표현으로, LLM이 처리할 수 있는 형식입니다.
- 벡터 데이터베이스 (벡터 DB): 이 데이터베이스에는 유사성 검색을 지원하는 방식으로 임베딩이 저장됩니다. 사용자 쿼리에서 생성된 임베딩으로 벡터 DB를 쿼리함으로써 가장 관련성 있는 문서 임베딩 및 따라서 문서 자체를 찾을 수 있습니다. 저는 현재 Chroma를 선호하지만, 개발과 빠른 설정을 위한 몇 가지 기본 기능이 있습니다. 더 많은 정보는 이 문서에서 얻을 수 있습니다.

## 자세한 단계: 문서로드부터 응답 검색까지

- 문서 로드 (PDF): 프로세스는 원문서로 시작하며, PDF 파일로 표시됩니다. 이 문서에는 작업할 텍스트가 포함되어 있습니다. 임베딩의 종류를 변경하여 항상 다른 유형의 문서를 추가할 수 있습니다.
- 텍스트 청크 (문서): 다음으로, PDF에서 텍스트가 추출되어 관리 가능한 청크로 분할됩니다. 이 분할은 텍스트를 작은, 더 집중된 세그먼트로 처리하는 데 중요합니다.
- 임베딩: 각 텍스트 청크는 그런 다음 임베딩으로 변환됩니다. 이러한 숫자 표현은 텍스트에서 의미 정보를 인코딩하여 기계 학습 모델에서 해석할 수 있게 합니다. 데이터 유형에 따라 다른 임베딩 유형이 더 잘 작동합니다.
- 벡터 데이터베이스: 생성된 임베딩은 벡터 데이터베이스에 저장됩니다. 이 데이터베이스는 임베딩의 빠른 검색 및 비교를 지원하도록 설계되었습니다.
- 질문 임베딩: 질문이 제시되면 해당 질문이 임베딩으로 변환되어 질문 임베딩이라고 알려진다. 이를 통해 시스템은 저장된 문서 임베딩과 질문의 의도를 의미적으로 비교할 수 있습니다.
- 의미적 검색: 질문 임베딩을 사용하여 시스템은 벡터 스토어 내에서 의미적 검색을 수행하여 질문 의도와 가장 일치하는 문서 임베딩을 찾습니다.
- 순위 매긴 결과: 의미적 검색은 순위 매긴 결과를 제공하며, 질문 임베딩과 가장 일치하는 최상위 임베딩이 연관된 문서 청크를 보여줍니다. 이러한 결과는 쿼리와의 관련성에 따라 순위가 매겨집니다.
- LLM (답변): 최상위 임베딩과 관련된 문서 청크는 대형 언어 모델(LLM)에 입력됩니다. LLM은 이 문맥을 사용하여 쿼리에 대한 일관된 상세한 답변을 생성합니다. 이 답변은 사용자에게 제공되어 문서로드에서 통찰력있는 응답을 검색하는 프로세스를 완료합니다.

여기에 제공된 개요는 로컬 대규모 언어 모델(Local Large Language Models, LocalLLMs)의 광범위한 능력과 복잡한 프로세스를 간략히 소개한 것입니다. 중요한 구성 요소와 고수준 워크플로우에 대해 다루었지만, 해당 분야는 다양한 개념과 기술이 들어가는 넓은 지식 영역이며 더 많은 개념 및 기술이 사용됩니다.

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

이 기사는 미필의 세부사항을 탐구하거나 철저한 논고를 제시하는 것을 목표로 하지 않습니다. 대신에 어떤 복잡한 프로세스들을 간단하게 정리해줄만한 개요를 제공하고자 합니다. 때로는 설명이 기술적 세부사항을 완전히 포착하지 못하거나 그러한 정교한 시스템에 내재된 미묘한 부분을 생략할 수도 있습니다.

임베딩, 벡터 데이터베이스 및 LLM에 대한 깊은 이해를 원하는 독자들은 다양한 자원과 기술 논문을 찾을 수 있습니다.

# 로컬 LLM과 상호작용하기 위한 프론트엔드 옵션

Ollama와 대규모 언어 모델의 능력을 활용하는 것이 어려운 과제일 필요는 없습니다. 코딩 경험이 전혀 없는 경우든 Python과 같은 스크립팅 언어에 익숙한 경우든, 여러분에게 알맞은 방법이 있습니다. 아래에서는 Ollama와 상호작용하는 세 가지 사용자 친화적인 방법을 소개하고 있으며, 각각 다른 기술 전문성 수준을 고려하고 있습니다.

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

## 1. AnythingLLM: 비 기술자를 위한

![이미지](/assets/img/2024-05-18-UnlockingAIPowerLocallyABeginnersGuidetoSettingUpandUsingLocalLLMswithOllama_4.png)

AnythingLLM은 몇 번의 클릭으로 자신의 Ollama 서버를 실행하거나 기존 서비스에 연결할 수 있는 그래픽 사용자 인터페이스(GUI)를 제공합니다. 코딩은 필요하지 않습니다. 시작하는 방법은 다음과 같습니다:

- 설정: AnythingLLM을 설치하고 Ollama 서버에 연결하도록 구성합니다.
- 사용법: 직관적인 GUI를 사용하여 쿼리를 입력하고 응답을 받습니다.
- 자원: AnythingLLM 자습서를 참조하여 서비스 설정 및 사용에 대한 단계별 안내를 확인하세요.

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

<img src="/assets/img/2024-05-18-UnlockingAIPowerLocallyABeginnersGuidetoSettingUpandUsingLocalLLMswithOllama_5.png" />

AnythingLLM은 로컬 LocalLLM과 손쉽게 상호 작용할 수 있도록 사용자 친화적인 인터페이스를 제공하는 플랫폼입니다. 여기 Ollama를 사용하여 AnythingLLM을 시작하는 빠른 안내서가 있습니다:

- Ollama 선택: AnythingLLM을 열고 나면 LLM 제공자로 Ollama를 선택하십시오. 이렇게 하면 인터페이스가 로컬 Ollama 서버에 연결됩니다.
- Ollama 기본 URL: Ollama 기본 URL로 http://127.0.0.1:11434를 입력하십시오. 이는 AnythingLLM이 Ollama 서버에서 요청을 수신하는 로컬 주소를 가리킵니다.
- 채팅 모델 선택: 모델 선택 드롭다운에서 mistral:latest 또는 Ollama 서버에 설치된 다른 모델을 선택하십시오. 이렇게 하면 AnythingLLM이 응답 생성에 사용할 특정 LLM을 알 수 있습니다.
- 토큰 컨텍스트 윈도우: Mistral 모델의 경우 토큰 컨텍스트 윈도우를 4096로 설정하여 컨텍스트 크기가 모델 용량과 일치하도록 합니다. 서로 다른 모델은 서로 다른 컨텍스트 크기를 요구할 수 있음을 유의하십시오.

임베딩을 위해:

| 병합 테스트용 | 테이블  | 변환    |
| ------------- | ------- | ------- |
| 열1           | 열2     | 열3     |
| 아이템1       | 아이템2 | 아이템3 |

여기까지입니다! 문제가 발생하면 언제든지 물어보세요. 😉

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

- 최대 임베딩 청크 길이: 임베딩의 최대 청크 길이를 8192로 설정하세요. 이는 모델이 임베딩 생성을 위해 처리하는 텍스트 세그먼트의 크기를 결정합니다.

벡터 데이터베이스를 위해:

- LanceDB: 벡터 데이터베이스로 AnythingLLM 내에 포함된 LanceDB를 사용하세요. LanceDB는 추가 설정이 필요하지 않으며 즉시 사용할 수 있습니다.

이 튜토리얼은 기본에 초점을 맞추어 전사 모델을 건너뛰며 진행됩니다. 이 간단한 단계를 따르면 코드 한 줄을 작성하지 않고도 AnythingLLM을 통해 Ollama 서버와 상호 작용할 수 있습니다. 다가오는 안내서는 시각적인 자세한 지시사항과 추가 자원을 제공하여 프로세스를 쉽게 따를 수 있게 해줄 것입니다.

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

## 2. 터미널: 기본 명령어에 익숙한 분들을 위한

터미널을 사용하면 간단한 명령어를 사용하여 Ollama 서버와 직접 상호작용할 수 있어서, 노력형 접근 방식을 선호하는 분들에게 적합합니다.

- 설정: Ollama가 설치되어 있고 기계에서 실행 중인지 확인하세요 (ollama serve).
- 사용법: 쿼리를 직접 입력하고 터미널 창에서 응답을 확인하세요 (ollama run mistral).
- 자료: Ollama 명령줄 튜토리얼을 통해 LLM과 상호작용하는 데 필요한 기본 명령어가 안내됩니다.

![이미지](/assets/img/2024-05-18-UnlockingAIPowerLocallyABeginnersGuidetoSettingUpandUsingLocalLLMswithOllama_6.png)

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

## 3. 코드 (Python + LangChain + Streamlit): 기술 열정가를 위해

코딩에 도전하고 싶다면, Python과 LangChain 그리고 Streamlit을 사용하여 대화형 웹 애플리케이션을 만들 수 있습니다.

기본적으로, 선택한 LocalLLM으로 Ollama를 사용하고, LangChain(언어모델 프레임워크)과 Streamlit(웹 프레임워크)을 사용할 것입니다.

![image](/assets/img/2024-05-18-UnlockingAIPowerLocallyABeginnersGuidetoSettingUpandUsingLocalLLMswithOllama_7.png)

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

- 설치: Python, LangChain 및 Streamlit을 설치한 후 Ollama에 연결하는 기본 스크립트를 설정합니다.
- 사용법: Ollama에 쿼리를 보내고 결과를 표시하는 사용자 정의 프론트엔드 응용 프로그램을 구축합니다.
- 자료: 자신의 AI 기반 앱을 만드는 방법에 대한 지침은 Streamlit 및 LangChain 튜토리얼을 참고하십시오.

# 결론

로컬 대형 언어 모델(Local Large Language Models, LocalLLMs)의 세분화를 통해 Ollama가 제공하는 편리함과 접근성을 통해, 인공 지능의 세계는 이전보다 더 접근하기 쉬운 것으로 나타났습니다. 이러한 고급 모델을 로컬 서버에서 실행할 수 있는 능력은 데이터 개인 정보 보호 및 보안을 극대화하며, AI 도구를 우리 고유한 요구에 맞게 조정할 수 있는 유연성을 제공합니다. 사용자 친화적인 설정과 다재다능성을 갖춘 Ollama는 LocalLLMs의 세계로 진입하는 사람들에게 특별한 존재로 빛을 발하고 있습니다.

Ollama의 간소화된 과정을 통해 원본 텍스트에서 실용적인 통찰력을 얻는 이 여정은, 저희가 사용할 수 있는 잠재력과 역량을 강조합니다. 제시된 단계와 방법론을 따라가며, 여러분도 이 잠재력에 참여하시기를 초대합니다. 여러분이 AI의 세계에 처음 발을 딛거나 보다 고급 AI 기능을 자신의 작업에 통합하려 한다면 더 나은 방향으로 나아가실 수 있을 것입니다.

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

이 글 전체를 통해 제공되는 자원과 링크를 탐색할 것을 장려합니다. 이들은 LocalLLM의 깊은 이해와 실용적인 적용을 위한 여정의 시작점 역할을 합니다. 튜토리얼을 살펴보고, 다양한 프론트엔드 옵션을 실험해보며, LocalLLM이 데이터와 기술과의 상호 작용을 어떻게 변화시킬 수 있는지 직접 경험해보세요.

AI 분야가 계속 발전함에 따라, 토론의 내용도 변화할 것입니다. Qe will delve into the intricate world of vector databases and explore alternative LocalLLM frameworks. These topics will open new doors and reveal further possibilities in AI.

개발자이든, 비즈니스 리더이든, AI 애호가이든, 미래는 기회로 넘쳐납니다. 이 도구들을 받아들이고 혁신가들의 커뮤니티에 참여하여 AI 발전의 다음 단계를 함께 모색합시다. AI로의 여정은 계속 됩니다. 각 단계는 발견과 잠재력을 약속합니다. 계속해서 함께 배우고, 창조하고, 혁신해봅시다.

# 추가 자원과 참고 자료

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

 <img src="/assets/img/2024-05-18-UnlockingAIPowerLocallyABeginnersGuidetoSettingUpandUsingLocalLLMswithOllama_8.png" />

LocalLLM 활용 및 커뮤니티 통찰:

- Reddit: Local LLM의 목적에 대한 토론
- Ollama Discord 채널

LocalLLMs에 대한 추가 정보 및 지식 확장을 위해 아래 자료들을 참고하시기 바랍니다!

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

## 오픈 소스 LLMs 개요:

- IBM 블로그: 오픈 소스 LLMs의 장단점, 위험 및 종류
- Substack: 설명하는 오픈 소스 LLMs의 역사

로컬 LLMs 설정 안내:

- The New Stack: Ollama와 Llama 2로 로컬 LLM 설정 및 실행 방법
- LangChain 문서: 로컬 LLMs를위한 안내서

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

벡터 데이터베이스 통찰:

- 뉴 스택: AI 프로젝트용 상위 5개 벡터 데이터베이스 솔루션

이러한 리소스들은 현재의 환경을 포괄적으로 이해할 수 있는 지침, 설정 및 사용을 위한 실용적인 안내서, 역사적 맥락, LocalLLMs 및 관련 기술에 대한 기술적 깊이를 제공하기 위해 선정되었습니다. 시작하려는 초보자든지 이미 숙련된 개발자로서 LocalLLMs 사용을 최적화하고 싶은 분들이라도, 이들 참고 자료는 정보의 보물창고입니다.

LocalLLMs와 함께 계속 탐험하며 개발하는 동안 이 리소스들을 책갈피에 추가해 두세요. 이들은 넓은 실무 커뮤니티로 이어지는 여정의 발판이자 AI 도구 상자의 자산입니다.
