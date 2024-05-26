---
title: "데이터브릭의 DBRX를 사용하면 실시간으로 학습할 수 있어요, 미세 조정 필요 없어요"
description: ""
coverImage: "/assets/img/2024-05-23-DatabricksDBRXforreal-timewithoutfine-tuning_0.png"
date: 2024-05-23 14:02
ogImage:
  url: /assets/img/2024-05-23-DatabricksDBRXforreal-timewithoutfine-tuning_0.png
tag: Tech
originalTitle: "Databrick’s DBRX for real-time, without fine-tuning"
link: "https://medium.com/@rharshith387/databricks-dbrx-for-real-time-without-fine-tuning-91196085b128"
---

![DBRX](/assets/img/2024-05-23-DatabricksDBRXforreal-timewithoutfine-tuning_0.png)

DBRX이란 무엇인가요?

DBRX는 Databricks의 최신 언어 모델 중 하나입니다. 언어 모델이란 충분한 예제를 학습하여 인간의 언어나 다른 유형의 복잡한 데이터를 인식하고 해석할 수 있는 컴퓨터 프로그램입니다. 많은 언어 모델들은 수십억 또는 수백만 기가바이트에 달하는 인터넷에서 수집한 데이터를 기반으로 학습됩니다.

DBRX는 Databricks에서 개발한 오픈, 일반 목적의 언어 모델입니다. 다양한 표준 벤치마크를 통해 DBRX는 이미 알려진 오픈 언어 모델들에 대한 최신 결과를 보여주고 있습니다.

<div class="content-ad"></div>

DBRX는 고밀도 전문가 혼합(MoE) 아키텍처 덕분에 오픈 모델 중 효율성 면에서 최첨단 기술을 선도합니다. LLaMA2-70B보다 추론이 최대 2배 빠르고, 전체 및 활성 매개변수 개수 측면에서 Grok-1의 약 40% 크기입니다.

DBRX는 총 1320억 개의 매개변수 중 360억 개가 어떤 입력에 대해서도 활성인 미세 구조 MoE 아키텍처를 사용하는 대형 언어 모델(LLM)입니다. 디코더 전용이며, 12조 개의 텍스트 및 코드 데이터 토큰을 포함하는 대규모 데이터 세트에서 다음 토큰 예측을 사용하여 교육되었습니다. Mixtral 및 Grok-1과 같은 유사한 모델과 달리, DBRX는 세부 접근 방식을 채용하며, 16개 전문가를 활용하고 그 중 4개를 선택합니다. 반면 다른 모델들은 8개 전문가를 가지고 2개를 선택합니다.

전통적인 언어 모델은 최근 이벤트나 트레이닝 데이터 외의 정보를 예측하는 능력에 제한이 있어서 현재 주제에 대한 쿼리에 대해 효과적이지 않을 수 있습니다. 이 제한으로 인해 언어 모델의 생성 능력을 보완하기 위해 검색 증강 생성(RAG)이 필요해졌습니다. 외부 소스를 통합함으로써 RAG는 특히 모델 훈련 데이터를 넘어서는 최근 이벤트나 주제에 대한 질의에 대한 정확도와 시기적절성을 향상시킵니다.

DBRX를 선택하는 이유는 무엇일까요?

<div class="content-ad"></div>

- 오픈 소스 모델 우위: 데이터브릭스의 DBRX 모델은 LLaMA2-70B, Mixtral 및 Grok-1과 같은 주요 오픈 소스 모델과 비교하여 우수한 성능을 보여줍니다. 이는 데이터브릭스가 언어 이해, 프로그래밍, 수학 및 논리와 같은 여러 영역에서 오픈 소스 모델 품질 향상에 기여하겠다는 의지를 나타냅니다. 이는 데이터브릭스가 지원하기를 자랑스럽게 생각하는 트렌드입니다.

![이미지 1](/assets/img/2024-05-23-DatabricksDBRXforreal-timewithoutfine-tuning_1.png)

- 전용 모델보다 우위: DBRX는 다양한 벤치마크에서 GPT-3.5를 능가하여, 데이터브릭스의 다양한 고객 기반 중에서 전용 모델 대신 오픈 소스 모델을 선호함에 대한 주목할만한 변화와 조화를 이룹니다. 데이터브릭스는 고객이 오픈 소스 모델을 사용자 지정하여 특정 요구 사항에 맞게 맞춤화하여 더 나은 품질과 속도를 달성할 수 있다는 능력을 강조합니다. 이는 기업과 조직에서 오픈 소스 모델의 도입을 가속화시킬 수 있는 가능성을 제시합니다.

![이미지 2](/assets/img/2024-05-23-DatabricksDBRXforreal-timewithoutfine-tuning_2.png)

<div class="content-ad"></div>

- DBRX의 MoE 아키텍처로 향상된 효율성과 확장성: MegaBlocks 연구 및 오픈소스 프로젝트에서 개발된 DBRX의 Mixture-of-Experts(MoE) 모델 아키텍처는 초당 처리된 토큰 수에 대해 높은 속도를 제공합니다. Databricks는 이 혁신이 미래 오픈소스 모델이 MoE 구조를 채택하도록 이끌어내며, 대규모 모델의 훈련을 유지하면서 빠른 처리량을 유지할 수 있도록 할 것으로 기대합니다. DBRX는 총 1320억 개의 매개변수 중 언제든지 360억 개의 매개변수만을 활용하며, 속도와 성능 사이의 균형을 제공하여 사용자에게 효율적인 솔루션을 제공합니다.

![이미지](/assets/img/2024-05-23-DatabricksDBRXforreal-timewithoutfine-tuning_3.png)

언어 모델을 위한 지식 창고!!!

LLM을 위한 지식 창고는 외부 소스의 실시간 정보를 추가함으로써 언어 모델을 더 스마트하게 만듭니다. 이는 다양한 주제에 걸쳐보다 정확하고 의미 있는 응답을 제공하는 데 도움을 줍니다. RAG, 또는 검색 증강 생성,은 이의 주요 구성 요소로서 정확한 정보를 찾아 활용하여 응답을 개선하는 데 도움을 줍니다.

<div class="content-ad"></div>

![그림](/assets/img/2024-05-23-DatabricksDBRXforreal-timewithoutfine-tuning_4.png)

RAG(검색 보강 생성)이 무엇인가요?

전통적인 언어 모델은 최근 사건이나 학습 데이터 외의 정보를 예측하는 능력이 제한되어 있어서 현재 주제에 대한 질의에 덜 효과적입니다. 이 한계로 인해 검색 보강 생성이 필요해졌습니다.

RAG 또는 검색 보강 생성은 언어를 이해하고 생성하는 새로운 방법입니다. 이 방법은 두 가지 종류의 모델을 결합합니다. 먼저, 관련 정보를 검색하고, 그 정보를 기반으로 텍스트를 생성합니다. 이 두 가지를 함께 사용함으로써 RAG는 놀라운 성과를 거두었습니다. 각 모델의 강점이 서로의 약점을 보완하기 때문에, RAG는 자연어 처리의 혁신적인 방법으로 각광을 받고 있습니다.

<div class="content-ad"></div>

아래는 Markdown 형식으로 표를 변환한 내용입니다.


![이미지](/assets/img/2024-05-23-DatabricksDBRXforreal-timewithoutfine-tuning_5.png)

벡터 저장소(Vector Store)는 무엇인가요?

벡터 저장소와 벡터 검색은 현대 정보 검색 시스템의 필수 구성 요소입니다.

- **벡터 저장소(Vector Store)**: 각 정보를 벡터로 나타내어 데이터를 저장하는 데이터베이스와 같은 역할입니다. 이러한 방식을 사용하면 각 정보를 다차원 공간에서 수학적으로 표현한 벡터로 저장할 수 있습니다. 이는 기존 인덱싱 방법이 아닌 유사성 측정 기준에 따라 데이터를 효율적으로 저장하고 검색할 수 있도록 합니다.


<div class="content-ad"></div>

![이미지](/assets/img/2024-05-23-DatabricksDBRXforreal-timewithoutfine-tuning_6.png)

벡터 검색: 벡터 간 유사성을 비교하여 관련 정보를 찾는 과정입니다. 키워드 매칭이나 기타 전통적인 검색 기술 대신 벡터 검색은 벡터 저장소에서 유사한 벡터를 식별하여 정확한 쿼리 용어를 포함하지 않더라도 의미론적으로 관련된 결과를 반환합니다.

![이미지](/assets/img/2024-05-23-DatabricksDBRXforreal-timewithoutfine-tuning_7.png)

Hands-on RAG 데모:

<div class="content-ad"></div>

제가 Databricks를 플랫폼으로 선택한 이유는 DBRX라는 기본 모델과 엔드 투 엔드 기계 학습 및 딥 러닝 워크플로에 맞춘 도구 및 라이브러리들을 제공하기 때문입니다.

코드 개요:

- Databricks Foundational Models에서 Chat 모델(DBRX)과 임베딩 모델을 가져오기

![image](/assets/img/2024-05-23-DatabricksDBRXforreal-timewithoutfine-tuning_8.png)

<div class="content-ad"></div>

2. Hugging Face에서 GPT Tokenizer를 가져오기

![image](/assets/img/2024-05-23-DatabricksDBRXforreal-timewithoutfine-tuning_9.png)

3. 챗 어시스턴트를 위한 클래스 생성: 이 클래스는 Tokenizer, Embedding, Docs 및 LLM을 입력으로 사용하여 클래스 객체를 인스턴스화합니다.

- get_pdf_text(): 제공된 문서에서 텍스트를 추출합니다.
- Chunk_return(): 텍스트를 입력으로 받아 토큰화하고 거대한 텍스트를 청크로 분할합니다.

<div class="content-ad"></div>


![image](/assets/img/2024-05-23-DatabricksDBRXforreal-timewithoutfine-tuning_10.png)

- get_vector_text(): 청크를 삽입하고 FAISS 인덱스를 사용하여 내장된 콘텐츠를 색인화하여 Vector Library를 반환합니다.

![image](/assets/img/2024-05-23-DatabricksDBRXforreal-timewithoutfine-tuning_11.png)

- get_conve_chain(): 대화형 Q&A 체인을 가져오는 메서드이며, DBRX의 프롬프트 템플릿 및 언어 모델과 함께 반환합니다.
- query(): 이 메서드는 LLM 체인과 Vector Library를 호출하여 언어 모델 (DBRX)에 공급하는 데 사용됩니다.


<div class="content-ad"></div>

예시:

- RAG에 대해 DBRX에 쿼리하는 방법

![이미지](/assets/img/2024-05-23-DatabricksDBRXforreal-timewithoutfine-tuning_12.png)

- 지식 창과 함께 DBRX에 쿼리하기

<div class="content-ad"></div>


![DBRX](/assets/img/2024-05-23-DatabricksDBRXforreal-timewithoutfine-tuning_13.png)

결론:

Databricks의 최고 수준 언어 모델인 DBRX는 언어 및 코드를 이해하는 데 뛰어나지만 최근 업데이트에 대한 처리가 약간 어려울 수 있습니다. 따라서 우리는 이를 지원하기 위해 검색 증강 생성(Retrieval-Augmented Generation, RAG) 기술을 사용합니다. 이 기술은 정보를 찾아 새로운 텍스트를 생성하는 방식을 결합하여 DBRX를 더욱 똑똑하게 만듭니다. Databricks에서 구현된 RAG 및 DBRX는 머신 러닝을 보다 쉽고 효과적으로 만들어줍니다.

