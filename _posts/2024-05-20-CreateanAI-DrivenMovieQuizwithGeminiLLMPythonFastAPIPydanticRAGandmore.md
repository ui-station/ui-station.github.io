---
title: "제목 Gemini LLM, Python, FastAPI, Pydantic, RAG 등을 활용한 AI 기반 영화 퀴즈 만들기"
description: ""
coverImage: "/assets/img/2024-05-20-CreateanAI-DrivenMovieQuizwithGeminiLLMPythonFastAPIPydanticRAGandmore_0.png"
date: 2024-05-20 18:50
ogImage:
  url: /assets/img/2024-05-20-CreateanAI-DrivenMovieQuizwithGeminiLLMPythonFastAPIPydanticRAGandmore_0.png
tag: Tech
originalTitle: "Create an AI-Driven Movie Quiz with Gemini LLM, Python, FastAPI, Pydantic, RAG and more"
link: "https://medium.com/towards-data-science/create-an-ai-driven-movie-quiz-with-gemini-llm-python-fastapi-pydantic-rag-and-more-e15322be4f66"
---

## 파이썬을 이용한 VertexAI를 통한 Gemini 기초 알아보기, FastAPI로 API 생성, Pydantic을 이용한 데이터 유효성 검사 및 Retrieval-Augmented Generation (RAG) 기초

이 글에서는 다양한 기술을 이용하여 LLM을 활용한 웹 애플리케이션을 만들기 위한 기본 사항을 공유합니다: Python, FastAPI, Pydantic, VertexAI 등을 사용합니다.

고지: 본 프로젝트에서는 The Movie Database의 데이터를 사용하고 있습니다. 이 API는 비상업적 목적으로 사용할 수 있으며, 디지털 밀레니엄 저작권법 (DMCA)을 준수합니다. TMDB 데이터 사용에 대한 자세한 정보는 공식 FAQ를 참조해주세요.

# 목차

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

- 영감
- 시스템 아키텍처
- 검색 증가 생성 (RAG) 이해
- Poetry를 사용한 Python 프로젝트
- FastAPI로 API 생성
- Pydantic을 사용한 데이터 유효성 검사 및 품질
- httpx를 사용한 TMDB 클라이언트
- VertexAI에서 Gemini LLM 클라이언트
- Jinja로 모듈식 프롬프트 생성기
- 프론트엔드
- API 예시
- 결론

이 지식을 공유하는 가장 좋은 방법은 실용적인 예제를 통해 하는 것입니다. 따라서 여러 측면을 다루기 위해 제 프로젝트 Gemini Movie Detectives를 사용할 것입니다. 이 프로젝트는 구글 AI 해커톤 2024의 일부로 만들어졌습니다. 현재 이 글을 쓰는 동안에도 해당 해커톤이 진행 중입니다.

![이미지](/assets/img/2024-05-20-CreateanAI-DrivenMovieQuizwithGeminiLLMPythonFastAPIPydanticRAGandmore_0.png)

Gemini Movie Detectives는 VertexAI를 통해 Gemini Pro 모델의 파워를 활용하여 최신 영화 데이터를 활용하여 재미있는 퀴즈 게임을 만드는 것을 목표로 한 프로젝트입니다.

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

프로젝트의 일부는 Docker를 사용하여 배포할 수 있도록하고 실시간 버전을 만드는 것이었습니다. 직접 확인해보세요: movie-detectives.com. 이것은 단순한 프로토 타입이므로 예상치 못한 문제가 발생할 수 있습니다. 또한, GCP와 VertexAI를 사용함으로써 발생할 수 있는 비용을 제어하기 위해 일부 제한을 추가해야 했습니다.

이 프로젝트는 완전히 오픈 소스로, 두 개의 별도 리포지토리로 나뉘어 있습니다:

- 🚀 백엔드용 Github 리포지토리: https://github.com/vojay-dev/gemini-movie-detectives-api
- 🖥️ 프론트엔드용 Github 리포지토리: https://github.com/vojay-dev/gemini-movie-detectives-ui

이 글의 중점은 백엔드 프로젝트와 그 배경 개념에 있습니다. 그러므로 프론트엔드 및 해당 구성 요소에 대해 간략히 설명합니다.

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

다음 동영상에서는 프로젝트와 그 구성 요소에 대한 개요를 제공합니다:

# 영감

열정적인 게이머로 자라고 이제는 데이터 엔지니어로 일하며, 게임과 데이터의 교차점에 항상 매료되어 왔습니다. 이 프로젝트에서는 내 가장 큰 열정인 게임과 데이터를 결합했습니다. 90년대에는 항상 재밌게 즐겼던 You Don’t Know Jack 비디오 게임 시리즈, 유머와 문제 해결의 멋진 결합으로 오로지 즐겁게 놀 뿐만 아니라 어떤 것을 배우기도 했었습니다. 일반적으로 게임을 교육 목적으로 활용하는 개념 또한 나를 매료시키는 것입니다.

2023년, 나는 아이들과 청소년 대상으로 게임 개발을 가르치는 워크샵을 진행했습니다. 그들은 충돌 탐지 뒤에 숨은 수학적 개념에 대해 배웠지만, 모든 것이 게임의 맥락으로 풀렸기 때문에 즐거워했습니다. 게임이 거대한 시장뿐만 아니라 지식 공유에도 큰 잠재력이 있다는 것을 깨달았습니다.

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

이 프로젝트인 영화 탐정(Movie Detectives)은 제가 Gemini 및 AI 전반의 매력을 보여주고 귀하고 유익한 퀴즈 및 교육 게임을 만들어내는데 어떻게 활용할 수 있는지, 또한 게임 디자인이 이러한 기술에서 이익을 얻는 방법을 소개합니다.

Gemini LLM에 정확하고 최신의 영화 메타데이터를 제공함으로써 Gemini의 질문의 정확성을 보장할 수 있었습니다. 이는 실시간 메타데이터를 활용하여 쿼리를 풍부하게 하는 Retrieval-Augmented Generation (RAG) 방법론이 없으면 잘못된 정보가 전파될 위험이 있기 때문에 중요한 측면입니다. AI를 이용할 때 일반적으로 치명적인 함정 중 하나입니다.

또 다른 혁신은 Jinja 템플릿을 사용하여 만든 모듈식 프롬프트 생성 프레임워크에 있습니다. 이는 게임 디자인을 위한 스위스 아미 나이프를 소유하고 있는 것과 같습니다. 이를 통해 쇼 마스터의 개성을 손쉽게 바꾸어 게임 경험을 맞춤 설정할 수 있습니다. 그리고 언어 모듈을 통해 퀴즈를 다양한 언어로 번역하는 것은 간단하며 비용이 드는 번역 프로세스를 제거할 수 있습니다.

비즈니스적인 관점에서 이를 고려하면, 고가의 번역 프로세스가 필요 없이 더 많은 고객에게 도달할 수 있습니다.

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

비즈니스적인 측면에서, 이 모듈화는 어렵지 않게 언어 장벽을 초월하며 더 넓은 고객층에 문을 열어줍니다. 또한 저는 이 모듈들의 변혁적인 힘을 직접 체험해 보았습니다. 기본 퀴즈 마스터에서 아재 농담 퀴즈 마스터로 변경하는 것은 정말 재미있는 경험이었어요. 이 프로젝트의 다재다능성에 대한 입증이자, You Don’t Know Jack의 전성기에 대한 향수로운 인사입니다.

# 시스템 아키텍처

자세한 내용에 앞서, 어떻게 애플리케이션이 구축되었는지에 대한 개요를 살펴봐요.

기술 스택: 🚀 백엔드

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

- Python 3.12 + FastAPI API 개발
- TMDB 통합을 위한 httpx
- 모듈식 프롬프트 생성을 위한 Jinja 템플릿
- 데이터 모델링 및 유효성 검사를 위한 Pydantic
- 의존성 관리를 위한 Poetry
- 배포를 위한 Docker
- 영화 데이터를 위한 TMDB API
- 퀴즈 질문 생성 및 답변 평가를 위한 VertexAI 및 Gemini
- 코드 포매터 및 린터로서 Ruff와 함께 pre-commit 훅
- Github Actions를 사용하여 모든 푸시마다 테스트 및 린터 자동 실행

기술 스택: 🖥️ 프론트엔드

- 프론트엔드 프레임워크로 VueJS 3.4 사용
- 프론트엔드 툴링을 위해 Vite 사용

이 어플리케이션은 외부 API (TMDB)에서 최신 영화 메타데이터를 가져온 후 다양한 모듈 (성격, 언어, ...)로 기반을 둔 프롬프트를 구축하고, 이 메타데이터로 프롬프트를 보강하여 사용자가 올바른 제목을 추측해야 하는 영화 퀴즈를 이니셜라이징하도록 Gemini를 사용합니다.

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

백엔드 인프라는 FastAPI와 Python으로 구축되었으며, 실시간 메타데이터를 활용하여 쿼리를 보강하는 Retrieval-Augmented Generation (RAG) 방법론을 적용했습니다. 백엔드는 Jinja 템플릿을 활용하여 프롬프트 생성을 기본, 개성 및 데이터 향상 템플릿으로 모듈화하며, 정확하고 매력적인 퀴즈 질문을 생성할 수 있도록 합니다.

프론트엔드는 Vue 3와 Vite로 구동되며, 효율적인 프론트엔드 개발을 위해 daisyUI와 Tailwind CSS를 활용합니다. 이러한 도구들은 사용자에게 매끄럽고 현대적인 인터페이스를 제공하여 백엔드와의 원활한 상호작용을 지원합니다.

영화 탐정들에서는 퀴즈 답변이 언어 모델 (LLM)에 의해 해석되어 동적 점수 매기기와 맞춤형 응답이 가능해집니다. 이는 게임 디자인 및 개발에서 LLM과 RAG를 통합하는 잠재력을 선보이며, 진정한 개별화된 게임 경험을 제공합니다. 뿐만 아니라, LLM을 활용하여 매력적인 퀴즈 트리비아 또는 교육 게임을 만드는 잠재력을 보여줍니다. 또한, 퍼스널리티나 언어를 추가하거나 변경하는 것은 더 많은 Jinja 템플릿 모듈을 추가하는 것만큼 쉽습니다. 개발자에게 노력을 줄이고 게임 경험을 변경하는 데 큰 도움이 됩니다.

<img src="/assets/img/2024-05-20-CreateanAI-DrivenMovieQuizwithGeminiLLMPythonFastAPIPydanticRAGandmore_1.png" />

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

개요에서 보듯이, Retrieval-Augmented Generation (RAG)은 백엔드의 중요한 아이디어 중 하나입니다. 이 특별한 패러다임을 더 자세히 살펴보겠습니다.

# Retrieval-Augmented Generation (RAG) 이해하기

대형 언어 모델 (LLM) 및 AI 영역에서 점점 인기를 얻고 있는 패러다임 중 하나가 Retrieval-Augmented Generation (RAG)입니다. 그러나 RAG가 무엇을 함유하고 있으며, AI 개발 영역에 어떤 영향을 미치는지 알아볼까요?

본질적으로, RAG는 외부 데이터를 통합하여 LLM 시스템을 강화하여 예측을 풍부하게 합니다. 이는 LLM에 관련 컨텍스트를 전달해서 프롬프트의 추가 요소로 사용한다는 것을 의미합니다. 하지만 어떻게 관련 컨텍스트를 찾을까요? 일반적으로, 해당 데이터는 벡터 검색이나 전용 벡터 데이터베이스를 통해 자동으로 검색될 수 있습니다. 벡터 데이터베이스는 유사한 데이터에 대한 쿼리를 빠르게 수행할 수 있도록 데이터를 저장하는 방식으로 특히 유용합니다. 그런 다음 LLM은 쿼리와 검색된 문서를 기반으로 출력을 생성합니다.

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

상상해 보세요: 주어진 프롬프트에 따라 텍스트를 생성할 수 있는 LLM이 있는 상황을 생각해보세요. RAG는 최신 영화 데이터와 같은 외부 소스에서 추가적인 컨텍스트를 주입함으로써 생성된 텍스트의 관련성과 정확도를 높이는 한 걸음 더 나아간 기술입니다.

RAG의 주요 구성 요소를 살펴보죠:

- LLMs: LLMs는 RAG 워크플로우의 중추 역할을 합니다. 이 모델들은 방대한 양의 텍스트 데이터로 훈련되어 있어 인간과 유사한 텍스트를 이해하고 생성할 수 있는 능력을 갖추고 있죠.
- 컨텍스트 풍부화를 위한 벡터 인덱스: RAG의 중요한 측면 중 하나는 벡터 인덱스의 사용입니다. 이 인덱스는 LLM이 이해할 수 있는 형식으로 텍스트 데이터의 임베딩을 저장하며, 생성 프로세스 중 관련 정보를 효율적으로 검색할 수 있게 해줍니다. 프로젝트의 맥락에서는 영화 메타데이터베이스일 것입니다.
- 검색 프로세스: RAG는 주어진 컨텍스트나 프롬프트를 기반으로 관련 문서나 정보를 검색하는 과정을 포함합니다. 이러한 검색된 데이터는 LLM에 대한 추가적인 입력으로 작용하여 이해력을 보충하고 생성된 응답의 품질을 향상시킵니다. 이는 특정 영화에 관련된 모든 관련 정보를 알아내고 연결하는 것을 의미할 수 있습니다.
- 생성된 출력물: LLM과 검색된 컨텍스트로부터 획득한 결합된 지식을 기반으로 시스템이 응답을 생성합니다. 이를 통해 생성된 텍스트는 일관성을 유지할 뿐만 아니라 문맥적으로 관련이 있어 강화된 데이터 덕분에 생성됩니다.

젬니미 영화 탐정 프로젝트에서는 프롬프트가 The Movie Database의 외부 API 데이터로 보강되지만, RAG는 일반적으로 이러한 프로세스를 단순화하기 위해 벡터 인덱스의 사용을 포함합니다. 보다 복잡한 문서와 향상된 데이터 양을 사용한다는 점이 다릅니다. 따라서 이러한 인덱스는 시스템을 빠르게 관련 외부 소스로 안내하는 안내표 같은 역할을 합니다.

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

이 프로젝트에서는 LLM 능력을 증진시키는 외부 데이터의 강점을 증명하며, 기본 아이디어를 보여줌으로써 RAG의 미니 버전을 소개합니다.

보다 일반적으로 RAG는 퀴즈나 교육 게임을 만들 때 Gemini와 같은 LLM을 사용할 때 매우 중요한 개념입니다. 이 개념은 잘못된 질문을 하거나 사용자의 답변을 잘못 해석하는 위험을 피할 수 있습니다.

다음은 프로젝트 중 하나에 RAG를 적용할 때 도움이 될 수 있는 오픈 소스 프로젝트 목록입니다:

- txtai: 시맨틱 검색, LLM 조작 및 언어 모델 워크플로우를 위한 All-in-one 오픈 소스 임베딩 데이터베이스.
- LangChain: LangChain은 대형 언어 모델 (LLMs)을 활용한 애플리케이션을 개발하기 위한 프레임워크입니다.
- Qdrant: 다음 세대 AI 애플리케이션을 위한 벡터 검색 엔진.
- Weaviate: 강력하고 빠르며 확장 가능한 클라우드 원본 벡터 데이터베이스 인 Weaviate.

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

물론, LLM 기반 응용 프로그램에 대한이 접근 방식의 잠재적 가치가 있으므로 오픈 소스 및 클로즈 소스 대체품이 더 많이 있지만, 이러한 것들을 사용하면 주제에 대한 연구를 시작할 수있을 것입니다.

# Poetry 이용한 Python 프로젝트

주요 개념이 명확해지면 프로젝트가 어떻게 생성되었고 일반적으로 종속성이 관리되는 방법을 조금 더 자세히 살펴 보겠습니다.

Poetry가 도와줄 수있는 세 가지 주요 작업은 빌드, 게시 및 추적입니다. 종속성을 관리하기 위한 결정론적인 방법을 가지고, 프로젝트를 공유하고 종속성 상태를 추적하는 것이 아이디어입니다.

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

![Create an AI-Driven Movie Quiz with GeminiLL, MPython, FastAPI, Pydantic, RAG, and more](/assets/img/2024-05-20-CreateanAI-DrivenMovieQuizwithGeminiLLMPythonFastAPIPydanticRAGandmore_2.png)

시를 사용하면 가상 환경을 쉽게 생성할 수 있어요. 기본적으로 시는 시스템 내의 중앙 폴더에 가상 환경을 만들어요. 그러나 저와 같이 프로젝트 폴더 내에서 가상 환경을 사용하길 원한다면 간단한 설정 변경이 필요해요:

```js
poetry config virtualenvs.in-project true
```

이제 poetry new를 사용하여 새 Python 프로젝트를 생성할 수 있어요. 시는 시스템의 기본 Python과 연결된 가상 환경을 만들어 줄 거예요. 이를 pyenv와 결합하면 특정 버전을 사용하여 프로젝트를 만드는 유연한 방법을 얻을 수 있어요. 또는 Poetry에 직접 사용할 Python 버전을 알려줄 수도 있어요: poetry env use /full/path/to/python.

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

새 프로젝트가 있다면, poetry add를 사용하여 해당 프로젝트에 종속성을 추가할 수 있어요.

이를 통해, Gemini 영화 탐정 프로젝트를 위해 프로젝트를 만들었어요:

```js
poetry config virtualenvs.in-project true
poetry new gemini-movie-detectives-api

cd gemini-movie-detectives-api

poetry add 'uvicorn[standard]'
poetry add fastapi
poetry add pydantic-settings
poetry add httpx
poetry add 'google-cloud-aiplatform>=1.38'
poetry add jinja2
```

프로젝트에 대한 메타데이터와 해당 버전과 함께의 종속성은 poetry.toml 및 poetry.lock 파일에 저장돼요. 제가 추가로 종속성을 추가한 후에는, 프로젝트를 위한 다음과 같은 poetry.toml이 생성되었어요:

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

[tool.poetry]
name = "gemini-movie-detectives-api"
version = "0.1.0"
description = "Use Gemini Pro LLM via VertexAI to create an engaging quiz game incorporating TMDB API data"
authors = ["Volker Janz <volker@janz.sh>"]
readme = "README.md"

[tool.poetry.dependencies]
python = "^3.12"
fastapi = "^0.110.1"
uvicorn = {extras = ["standard"], version = "^0.29.0"}
python-dotenv = "^1.0.1"
httpx = "^0.27.0"
pydantic-settings = "^2.2.1"
google-cloud-aiplatform = ">=1.38"
jinja2 = "^3.1.3"
ruff = "^0.3.5"
pre-commit = "^3.7.0"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"

# FastAPI로 API 만들기

FastAPI는 빠른 API 개발을 가능하게 하는 Python 프레임워크입니다. 오픈 표준을 바탕으로 구축되어 새로운 구문을 익힐 필요 없이 매끄러운 경험을 제공합니다. 자동 문서 생성, 견고한 유효성 검사, 통합된 보안을 통해 FastAPI는 개발을 간소화하고 동시에 우수한 성능을 보장합니다.

<img src="/assets/img/2024-05-20-CreateanAI-DrivenMovieQuizwithGeminiLLMPythonFastAPIPydanticRAGandmore_3.png" />

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

젬니니 무비 디텍티브 프로젝트용 API를 구현하는 중에, 저는 단순히 Hello World 응용 프로그램에서 시작하여 그것을 확장했어요. 이렇게 시작할 수 있어요:

```js
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"Hello": "World"}
```

프로젝트 폴더 내의 .venv/ 가상 환경을 유지하고 uvicorn을 사용한다고 가정하면, 코드 변경을 테스트하기 위해 다시 시작할 필요 없이 리로드 기능이 활성화된 API를 시작하는 방법은 다음과 같아요:

```js
source .venv/bin/activate
uvicorn gemini_movie_detectives_api.main:app --reload
curl -s localhost:8000 | jq .
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

만약 아직 jq를 설치하지 않았다면 지금 설치하는 것을 적극 추천합니다. 나중에 나는 이 멋진 JSON 스위스 아미 나이프에 대해 다룰 수도 있을 것입니다. 여기에 응답이 어떻게 보이는지 알아봅시다:

![이미지](/assets/img/2024-05-20-CreateanAI-DrivenMovieQuizwithGeminiLLMPythonFastAPIPydanticRAGandmore_4.png)

여기서 필요에 따라 API 엔드포인트를 개발할 수 있습니다. 예를 들어 Gemini Movie Detectives에서 영화 퀴즈를 시작하는 API 엔드포인트 구현은 다음과 같습니다:

```js
@app.post('/quiz')
@rate_limit
@retry(max_retries=settings.quiz_max_retries)
def start_quiz(quiz_config: QuizConfig = QuizConfig()):
    movie = tmdb_client.get_random_movie(
        page_min=_get_page_min(quiz_config.popularity),
        page_max=_get_page_max(quiz_config.popularity),
        vote_avg_min=quiz_config.vote_avg_min,
        vote_count_min=quiz_config.vote_count_min
    )

    if not movie:
        logger.info('could not find movie with quiz config: %s', quiz_config.dict())
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail='No movie found with given criteria')

    try:
        genres = [genre['name'] for genre in movie['genres']]

        prompt = prompt_generator.generate_question_prompt(
            movie_title=movie['title'],
            language=get_language_by_name(quiz_config.language),
            personality=get_personality_by_name(quiz_config.personality),
            tagline=movie['tagline'],
            overview=movie['overview'],
            genres=', '.join(genres),
            budget=movie['budget'],
            revenue=movie['revenue'],
            average_rating=movie['vote_average'],
            rating_count=movie['vote_count'],
            release_date=movie['release_date'],
            runtime=movie['runtime']
        )

        chat = gemini_client.start_chat()

        logger.debug('starting quiz with generated prompt: %s', prompt)
        gemini_reply = gemini_client.get_chat_response(chat, prompt)
        gemini_question = gemini_client.parse_gemini_question(gemini_reply)

        quiz_id = str(uuid.uuid4())
        session_cache[quiz_id] = SessionData(
            quiz_id=quiz_id,
            chat=chat,
            question=gemini_question,
            movie=movie,
            started_at=datetime.now()
        )

        return StartQuizResponse(quiz_id=quiz_id, question=gemini_question, movie=movie)
    except GoogleAPIError as e:
        raise HTTPException(status_code=status.HTTP_500_INTERNAL_SERVER_ERROR, detail=f'Google API error: {e}')
    except Exception as e:
        raise HTTPException(status_code=status.HTTP_500_INTERNAL_SERVER_ERROR, detail=f'Internal server error: {e}')
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

이 코드 안에는 이미 백엔드의 주요 구성 요소 세 가지가 포함되어 있습니다:

- tmdb_client: 내가 구현한 클라이언트로, The Movie Database (TMDB)에서 데이터를 가져 오는 데 사용됩니다.
- prompt_generator: Jinja 템플릿을 기반으로 모듈식 프롬프트를 생성하는 데 도움이 되는 클래스입니다.
- gemini_client: Google Cloud의 VertexAI를 통해 Gemini LLM과 상호 작용하는 클라이언트입니다.

나중에 이러한 구성 요소를 자세히 살펴보겠지만, 먼저 FastAPI 사용에 대한 더 많은 유용한 통찰력을 살펴보겠습니다.

FastAPI는 HTTP 메서드와 데이터를 백엔드로 전달하는 것을 정의하는 것이 정말 쉽습니다. 특정 함수의 경우 POST 요청이 예상되므로 새 퀴즈를 생성합니다. 이를 post 데코레이터를 사용하여 수행할 수 있습니다:

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

```python
@app.post('/quiz')
```

또한, 요청에 몇 가지 데이터가 포함되어 JSON 형식으로 전송될 것으로 예상합니다. 이 경우, JSON으로 QuizConfig의 인스턴스를 기대하고 있습니다. 나는 단순히 Pydantic의 BaseModel 클래스를 서브클래스로 정의한 QuizConfig를 API 함수에 전달하면 FastAPI가 나머지를 처리해줄 것이다.

```python
class QuizConfig(BaseModel):
    vote_avg_min: float = Field(5.0, ge=0.0, le=9.0)
    vote_count_min: float = Field(1000.0, ge=0.0)
    popularity: int = Field(1, ge=1, le=3)
    personality: str = Personality.DEFAULT.name
    language: str = Language.DEFAULT.name
# ...
def start_quiz(quiz_config: QuizConfig = QuizConfig()):
```

또한, 두 개의 사용자 정의 데코레이터를 알아챌 수 있을 것입니다.

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

@rate_limit
@retry(max_retries=settings.quiz_max_retries)

이것들은 중복 코드를 줄이기 위해 구현한 것입니다. API 함수를 래핑하여 오류가 발생한 경우 함수를 재시도하고 하루에 시작할 수 있는 영화 퀴즈의 전역 속도 제한을 소개합니다.

또 내가 개인적으로 좋아한 것은 FastAPI와 함께 하는 오류 처리입니다. 간단히 HTTPException을 발생시키고 원하는 상태 코드를 지정하면 사용자가 원하는 조건에 맞는 영화를 찾을 수 없는 경우와 같이 적절한 응답을 받게 됩니다:

```js
raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail='No movie found with given criteria')
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

여기서, 지니 마블 디텍티브와 FastAPI를 사용하여 API를 만드는 개요를 갖게 될 것입니다. 주의할 점은 모든 코드가 오픈 소스이므로 Github의 API 저장소를 자유롭게 살펴볼 수 있습니다.

# Pydantic을 사용한 데이터 유효성 검사 및 품질

오늘날 AI/ML 프로젝트의 주요 도전 과제 중 하나는 데이터 품질입니다. 그러나 이것은 모델 훈련이나 예측에 사용되는 데이터셋을 준비하는 ETL/ELT 파이프라인에만 해당되는 것은 아닙니다. AI/ML 애플리케이션 자체에도 적용됩니다. 예를 들어 Python을 사용하면 대부분 동적 타입이지만 데이터 유효성을 검사하는 기본적인 방법으로 사용할 때 Python은 데이터 유효성 검사에 태만한 면이 있습니다.

그래서 이 프로젝트에서는 FastAPI와 Python의 강력한 데이터 유효성 검사 라이브러리인 Pydantic을 결합했습니다. 목표는 API를 가볍고 데이터 품질 및 유효성 검사 측면에서 엄격하고 강력하게 만드는 것이었습니다. 예를 들어, 평범한 사전 대신 영화 디텍티브 API는 Pydantic에서 제공하는 BaseModel에서 상속된 사용자 정의 클래스를 엄격하게 사용합니다. 이겢은 예시로 퀴즈를 위한 구성입니다:

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
class QuizConfig(BaseModel):
    vote_avg_min: float = Field(5.0, ge=0.0, le=9.0)
    vote_count_min: float = Field(1000.0, ge=0.0)
    popularity: int = Field(1, ge=1, le=3)
    personality: str = Personality.DEFAULT.name
    language: str = Language.DEFAULT.name
```

이 예제는 올바른 유형뿐만 아니라 실제 값에 대한 추가 유효성 검사도 보장됩니다.

더욱이 최신 Python 기능, 예를 들어 StrEnum을 사용하여 특정 유형(예: 성격)을 구별합니다:

```js
class Personality(StrEnum):
    DEFAULT = 'default.jinja'
    CHRISTMAS = 'christmas.jinja'
    SCIENTIST = 'scientist.jinja'
    DAD = 'dad.jinja'
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

또한, 사용자 지정 데코레이터를 정의하여 중복 코드를 피합니다. 예를 들어, 다음 데코레이터는 GCP 비용을 관리하기 위해 오늘의 퀴즈 세션 수를 제한합니다:

```js
call_count = 0
last_reset_time = datetime.now()

def rate_limit(func: callable) -> callable:
    @wraps(func)
    def wrapper(*args, **kwargs) -> callable:
        global call_count
        global last_reset_time

        # 날짜가 변경되었을 경우 호출 횟수를 초기화합니다.
        if datetime.now().date() > last_reset_time.date():
            call_count = 0
            last_reset_time = datetime.now()

        if call_count >= settings.quiz_rate_limit:
            raise HTTPException(status_code=status.HTTP_400_BAD_REQUEST, detail='일일 한도 도달')

        call_count += 1
        return func(*args, **kwargs)

    return wrapper
```

그런 다음 관련 API 함수에 간단히 적용됩니다:

```js
@app.post('/quiz')
@rate_limit
@retry(max_retries=settings.quiz_max_retries)
def start_quiz(quiz_config: QuizConfig = QuizConfig()):
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

최신 Python 기능과 FastAPI, Pydantic 또는 Ruff와 같은 라이브러리의 조합은 백엔드를 더 간결하게 만들어주지만 여전히 매우 안정적이며 특정 데이터 품질을 보장하여 LLM(언어 모델) 출력이 예상대로 나오도록 합니다.

# httpx를 사용한 TMDB 클라이언트

TMDB 클라이언트 클래스는 httpx를 사용하여 TMDB API에 대한 요청을 수행합니다.

httpx는 Python 라이브러리 세계에서 주목받는 존재입니다. 오랫동안 HTTP 요청을 수행하는 데 사용되었던 requests에 대안으로 httpx가 제공됩니다. 그 중요한 강점 중 하나는 비동기 기능입니다. httpx를 사용하면 동시에 여러 요청을 처리할 수 있는 코드를 작성할 수 있어, HTTP 상호작용이 많은 응용 프로그램에서 상당한 성능 향상을 기대할 수 있습니다. 게다가 httpx는 requests와의 넓은 호환성을 지향하여 개발자가 더 쉽게 익힐 수 있게 합니다.

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

젬니미 무비 디텍티브 서비스에서는 주로 두 가지 요청이 있어요:

- get_movies: 특정 설정(평균 투표 수 등)에 기반하여 랜덤 영화 목록을 가져옵니다.
- get_movie_details: 퀴즈에 사용할 특정 영화의 세부 정보를 가져옵니다.

외부 요청 양을 줄이기 위해 뒷 부분의 함수는 lru_cache 데코레이터를 사용합니다. 이는 "Least Recently Used cache"의 약자로, 함수 호출의 결과를 캐시하여 같은 입력이 다시 발생하면 함수가 결과를 다시 계산할 필요가 없게 합니다. 대신, 캐시된 결과를 반환하며, 특히 계산 비용이 많이 드는 함수의 경우 프로그램 성능을 크게 향상시킬 수 있습니다. 이 경우에는 1024개의 영화 세부 정보를 캐시하므로, 두 플레이어가 동일한 영화를 받을 경우 요청을 다시 보낼 필요가 없어요:

```js
@lru_cache(maxsize=1024)
def get_movie_details(self, movie_id: int):
    response = httpx.get(f'https://api.themoviedb.org/3/movie/{movie_id}', headers={
        'Authorization': f'Bearer {self.tmdb_api_key}'
    }, params={
        'language': 'en-US'
    })

    movie = response.json()
    movie['poster_url'] = self.get_poster_url(movie['poster_path'])

    return movie
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

TMDB(The Movie Database)의 데이터에 액세스하는 것은 비상업적 용도로 무료입니다. API 키를 생성하고 요청을 시작할 수 있습니다.

# Gemini LLM 클라이언트와 VertexAI

Gemini를 통해 VertexAI를 사용하기 위해서는 VertexAI가 활성화된 Google Cloud 프로젝트와 충분한 액세스 권한을 가진 서비스 계정 및 해당 JSON 키 파일이 필요합니다.

![이미지](/assets/img/2024-05-20-CreateanAI-DrivenMovieQuizwithGeminiLLMPythonFastAPIPydanticRAGandmore_5.png)

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

신규 프로젝트를 생성한 후 APIs 및 서비스로 이동하십시오. API 및 서비스 활성화 - VertexAI API를 검색하여 활성화합니다.

![이미지](/assets/img/2024-05-20-CreateanAI-DrivenMovieQuizwithGeminiLLMPythonFastAPIPydanticRAGandmore_6.png)

서비스 계정을 생성하려면 IAM 및 관리자로 이동하십시오. 서비스 계정 - 서비스 계정 생성을 선택합니다. 적절한 이름을 선택한 다음 다음 단계로 이동합니다.

![이미지](/assets/img/2024-05-20-CreateanAI-DrivenMovieQuizwithGeminiLLMPythonFastAPIPydanticRAGandmore_7.png)

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

이제 계정에 미리 정의된 역할 Vertex AI 사용자를 할당해주세요.

![image](/assets/img/2024-05-20-CreateanAI-DrivenMovieQuizwithGeminiLLMPythonFastAPIPydanticRAGandmore_8.png)

마지막으로, '새 사용자 - 키 - 키 추가 - 새 키 생성 - JSON'을 클릭하여 JSON 키 파일을 생성하고 다운로드할 수 있습니다. 이 파일이 있으면 준비 완료입니다.

![image](/assets/img/2024-05-20-CreateanAI-DrivenMovieQuizwithGeminiLLMPythonFastAPIPydanticRAGandmore_9.png)

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

Google의 Gemini와 Python을 사용하여 VertexAI를 통해 작업을 시작하려면 다음과 같이 프로젝트에 필요한 종속성을 추가해야 합니다:

```js
poetry add 'google-cloud-aiplatform>=1.38'
```

이제 JSON 키 파일을 사용하여 vertexai를 가져와 초기화할 수 있습니다. 또한 새로 출시된 Gemini 1.5 Pro 모델과 같은 모델을 로드하고 다음과 같이 채팅 세션을 시작할 수 있습니다:

```js
import vertexai
from google.oauth2.service_account import Credentials
from vertexai.generative_models import GenerativeModel

project_id = "my-project-id"
location = "us-central1"

credentials = Credentials.from_service_account_file("credentials.json")
model = "gemini-1.0-pro"

vertexai.init(project=project_id, location=location, credentials=credentials)
model = GenerativeModel(model)

chat_session = model.start_chat()
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

이제 chat.send_message()를 사용하여 모델에 프롬프트를 전송할 수 있습니다. 그러나 데이터 조각들로 응답을 받게 되므로, 간단한 도우미 함수를 사용하는 것을 추천합니다. 이렇게 하면 단순히 전체 응답을 하나의 문자열로 받게 됩니다:

```js
def get_chat_response(chat: ChatSession, prompt: str) -> str:
    text_response = []
    responses = chat.send_message(prompt, stream=True)
    for chunk in responses:
        text_response.append(chunk.text)
    return ''.join(text_response)
```

이제 전체 예제는 이렇게 보일 수 있습니다:

```js
import vertexai
from google.oauth2.service_account import Credentials
from vertexai.generative_models import GenerativeModel, ChatSession

project_id = "my-project-id"
location = "us-central1"

credentials = Credentials.from_service_account_file("credentials.json")
model = "gemini-1.0-pro"

vertexai.init(project=project_id, location=location, credentials=credentials)
model = GenerativeModel(model)

chat_session = model.start_chat()


def get_chat_response(chat: ChatSession, prompt: str) -> str:
    text_response = []
    responses = chat.send_message(prompt, stream=True)
    for chunk in responses:
        text_response.append(chunk.text)
    return ''.join(text_response)


response = get_chat_response(
    chat_session,
    "How to say 'you are awesome' in Spanish?"
)
print(response)
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

젠티미니가 다음과 같은 응답을 제공했습니다:

![Gemini Response](/assets/img/2024-05-20-CreateanAI-DrivenMovieQuizwithGeminiLLMPythonFastAPIPydanticRAGandmore_10.png)

저도 젠티미니의 의견에 동의합니다:

이를 사용할 때 또 다른 팁: send_message 함수의 generation_config 매개변수를 통해 생성 구성을 설정할 수도 있습니다. 예를 들어:

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
generation_config = {
  temperature: 0.5,
};

responses = chat.send_message(
  prompt,
  (generation_config = generation_config),
  (stream = True)
);
```

저는 Gemini Movie Detectives에서 온도를 0.5로 설정하기 위해 이것을 사용하고 있습니다. 이는 최상의 결과를 제공했습니다. 이 문맥에서 온도란 무엇이죠? 바로 Gemini가 생성하는 응답이 얼마나 창의적인지를 나타냅니다. 이 값은 0.0과 1.0 사이여야 하며, 1.0에 가까울수록 더 많은 창의성을 의미합니다.

프롬프트를 보내고 Gemini에서 받은 응답을 분석하여 관련 정보를 추출하는 것 이외에 중요한 도전 과제 중 하나입니다.

프로젝트에서 배운 점 중 하나는:

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

예를 들어, Gemini의 질문 프롬프트에는 다음 지침이 포함되어 있습니다:

```js
귀하의 답변은 반드시 세 줄로만 이루어져야 합니다! 다음 템플릿을 엄격히 준수하여 세 줄로만 회신해야 합니다:
질문: <귀하의 질문>
힌트 1: <참가자를 돕기 위한 첫 번째 힌트>
힌트 2: <보다 쉽게 제목을 얻기 위한 두 번째 힌트>
```

쓸데없는 방식은, Question:으로 시작하는 줄을 찾아 응답을 구문 분석하는 것입니다. 그러나 독일어와 같은 다른 언어를 사용하면, 응답은 Antwort:으로 시작합니다.

대신, 구조와 주요 기호에 집중하세요. 이 답변은 다음과 같이 읽어주세요:

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

- 테이블 태그를 Markdown 형식으로 변경하실수 있나요?

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

그 외에도, 저는 Gemini 클라이언트를 구성 가능한 클래스로 래핑했습니다. 전체 구현은 Github에서 오픈 소스로 확인하실 수 있어요.

# Jinja를 활용한 모듈식 프롬프트 생성기

프롬프트 생성기는 Jinja2 템플릿 파일을 결합하고 렌더링하여 모듈식 프롬프트를 생성하는 클래스입니다.

질문을 생성하는 하나의 기본 템플릿과 답변을 평가하는 하나의 기본 템플릿이 있습니다. 그 외에도 최신 영화 데이터로 프롬프트를 보강하는 메타데이터 템플릿이 있습니다. 더 나아가, 언어 및 성격 템플릿이 있으며, 각 옵션에 대한 템플릿 파일이 있는 별도의 폴더로 구성되어 있습니다.

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

<img src="/assets/img/2024-05-20-CreateanAI-DrivenMovieQuizwithGeminiLLMPythonFastAPIPydanticRAGandmore_11.png" />

Jinja2를 사용하면 템플릿 상속과 같은 고급 기능을 사용할 수 있습니다. 이는 메타데이터에 사용됩니다.

이를 통해 이 구성 요소를 더 많은 인물과 언어 옵션으로 확장하고 더 이상의 오픈 소스 프로젝트로 추출하여 다른 Gemini 프로젝트에서도 사용할 수 있습니다.

# 프론트엔드

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

젬니니 무비 디텍티브 프론트엔드는 네 가지 주요 구성 요소로 나뉘어 있으며, vue-router를 사용하여 이들 간에 이동합니다.

홈 컴포넌트는 환영 메시지를 단순히 표시합니다.

퀴즈 컴포넌트는 퀴즈 자체를 표시하고 fetch를 통해 API와 통신합니다. 퀴즈를 생성하려면, 원하는 설정으로 api/quiz로 POST 요청을 보내고, 백엔드는 사용자 설정을 기반으로 무작위 영화를 선택하고 모듈식 프롬프트 생성기를 사용하여 프롬프트를 만들고, 젬니를 사용하여 질문과 힌트를 생성하여 최종적으로 모든 것을 컴포넌트로 다시 반환하여 퀴즈를 렌더링할 수 있도록 합니다.

게다가 각 퀴즈는 백엔드에서 세션 ID가 할당되고 제한된 LRU 캐시에 저장됩니다.

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

디버깅 목적으로이 구성 요소는 api/sessions 엔드포인트에서 데이터를 가져옵니다. 이는 캐시에서 모든 활성 세션을 반환합니다.

이 구성 요소는 서비스에 대한 통계를 표시합니다. 그러나 지금까지 표시된 데이터 카테고리는 퀴즈 한도뿐입니다. VertexAI 및 GCP 사용 비용을 제한하기 위해 퀴즈 세션의 일일 한도가 있으며, 다음 날 첫 퀴즈로 재설정됩니다. 데이터는 api/limit 엔드포인트에서 검색됩니다.

![이미지](/assets/img/2024-05-20-CreateanAI-DrivenMovieQuizwithGeminiLLMPythonFastAPIPydanticRAGandmore_12.png)

# API 예제

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

당연히 프론트엔드를 사용하여 애플리케이션과 상호작용하는 것이 좋지만, API만 사용하여도 가능합니다.

다음 예제는 Santa Claus / Christmas 성격을 이용해 API를 통해 퀴즈를 시작하는 방법을 보여줍니다:

```js
curl -s -X POST https://movie-detectives.com/api/quiz \
  -H 'Content-Type: application/json' \
  -d '{"vote_avg_min": 5.0, "vote_count_min": 1000.0, "popularity": 3, "personality": "christmas"}' | jq .
```

```js
{
  "quiz_id": "e1d298c3-fcb0-4ebe-8836-a22a51f87dc6",
  "question": {
    "question": "Ho ho ho, this movie takes place in a world of dreams, just like the dreams children have on Christmas Eve after seeing Santa Claus! It's about a team who enters people's dreams to steal their secrets. Can you guess the movie? Merry Christmas!",
    "hint1": "The main character is like a skilled elf, sneaking into people's minds instead of houses. ",
    "hint2": "I_c_p_i_n "
  },
  "movie": {...}
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

이 예시는 퀴즈 언어를 변경하는 방법을 보여줍니다:

```js
curl -s -X POST https://movie-detectives.com/api/quiz \
  -H 'Content-Type: application/json' \
  -d '{"vote_avg_min": 5.0, "vote_count_min": 1000.0, "popularity": 3, "language": "german"}' | jq .
```

```js
{
  "quiz_id": "7f5f8cf5-4ded-42d3-a6f0-976e4f096c0e",
  "question": {
    "question": "Stellt euch vor, es gäbe riesige Monster, die auf der Erde herumtrampeln, als wäre es ein Spielplatz! Einer ist ein echtes Urviech, eine Art wandelnde Riesenechse mit einem Atem, der so heiß ist, dass er euer Toastbrot in Sekundenschnelle rösten könnte. Der andere ist ein gigantischer Affe, der so stark ist, dass er Bäume ausreißt wie Gänseblümchen. Und jetzt ratet mal, was passiert? Die beiden geraten aneinander, wie zwei Kinder, die sich um das letzte Stück Kuchen streiten! Wer wird wohl gewinnen, die Riesenechse oder der Superaffe? Das ist die Frage, die sich die ganze Welt stellt! ",
    "hint1": "Der Film spielt in einer Zeit, in der Monster auf der Erde wandeln.",
    "hint2": "G_dz_ll_ vs. K_ng "
  },
  "movie": {...}
}
```

그리고 API 호출을 통해 퀴즈에 대답하는 방법입니다:

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
curl -s -X POST https://movie-detectives.com/api/quiz/84c19425-c179-4198-9773-a8a1b71c9605/answer \
  -H 'Content-Type: application/json' \
  -d '{"answer": "Greenland"}' | jq .
```

```js
{
  "quiz_id": "84c19425-c179-4198-9773-a8a1b71c9605",
  "question": {...},
  "movie": {...},
  "user_answer": "Greenland",
  "result": {
    "points": "3",
    "answer": "Congratulations! You got it! Greenland is the movie we were looking for. You're like a human GPS, always finding the right way!"
  }
}
```

# 결론

기본 프로젝트를 완료한 후에 모듈형 프롬프트 접근 방식으로 더 많은 퍼스널리티와 언어를 추가하는 것이 쉬웠습니다. 이것은 게임 디자인 및 개발을 위한 가능성을 열어준 것에 감명받았습니다. 다른 퍼스널리티를 추가함으로써 순수한 영화 교육 게임에서 코미디 퀴즈 "You Don't Know Jack"과 같은 게임으로 한 분 내에 게임을 변경할 수 있었습니다.

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

그리고 여기서 마무리입니다! 이제 여러분은 자신만의 LLM을 활용한 웹 애플리케이션을 만들 준비가 되었어요.

영감을 받았지만 시작점이 필요하다면 어떻게 시작할지 궁금하신가요? Gemini Movie Detectives 프로젝트의 오픈 소스 코드를 확인해보세요:

- 🚀 백엔드의 Github 저장소: https://github.com/vojay-dev/gemini-movie-detectives-api
- 🖥️ 프론트엔드의 Github 저장소: https://github.com/vojay-dev/gemini-movie-detectives-ui

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

AI 기반 애플리케이션의 미래는 밝으며, 당신이 페인트붓을 쥐고 있습니다! 함께 놀라운 것을 만들어봅시다. 그리고 쉬는 시간이 필요하다면 https://movie-detectives.com/을 자유롭게 이용해보세요!
