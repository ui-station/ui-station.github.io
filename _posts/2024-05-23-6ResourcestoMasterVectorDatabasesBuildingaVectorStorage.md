---
title: "벡터 데이터베이스를 마스터하기 위한 6가지 자료, 벡터 저장소 구축"
description: ""
coverImage: "/assets/img/2024-05-23-6ResourcestoMasterVectorDatabasesBuildingaVectorStorage_0.png"
date: 2024-05-23 18:26
ogImage:
  url: /assets/img/2024-05-23-6ResourcestoMasterVectorDatabasesBuildingaVectorStorage_0.png
tag: Tech
originalTitle: "6 Resources to Master Vector Databases , Building a Vector Storage"
link: "https://medium.com/gitconnected/6-resources-to-master-vector-databases-building-a-vector-storage-8d94ca1e3897"
---

벡터 데이터베이스는 자연어 처리와 이미지 인식과 같은 다양한 분야에서 중요한 구성 요소로, 정보를 효율적으로 조직화하고 검색하는 데 중추적인 도구로 작용합니다.

벡터 데이터베이스를 이해하는 것은 의미 검색, 검색 증강 생성 (RAG), 추천 시스템과 같은 고급 AI 응용 프로그램을 가능하게 함에 있어서 중요합니다.

본 기사는 벡터 데이터베이스를 마스터하고 벡터 저장 솔루션을 구축하기 위한 리소스에 대한 포괄적인 개요를 제공합니다. 기본 개념, 실용적 응용, 그리고 벡터 데이터베이스 작업에 필수적인 다양한 도구와 라이브러리에 대해 다루고 있습니다.

튜토리얼을 통해, 블로그 추천을 통해, 그리고 LangChain과 Sentence Transformers 라이브러리와 같은 도구를 활용하여, 독자들은 AI 프로젝트에서 벡터 데이터베이스를 효과적으로 활용하는 방법에 대한 통찰과 실습 경험을 얻게 됩니다. 게다가, 이 기사는 신기술을 최신 상태로 유지하는 중요성을 강조하며 추가 학습 및 커뮤니티 참여를 위한 방법을 소개합니다.

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

![이미지](/assets/img/2024-05-23-6ResourcestoMasterVectorDatabasesBuildingaVectorStorage_0.png)

# 목차:

- Vector Databases: from Embeddings to Applications
- Building Applications with Vector Databases
- The Top 5 Vector Database Blog
- LangChain — Text splitters
- Sentence Transformers library
- MTEB Leaderboard

# 1. Vector Databases: from Embeddings to Applications

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

![image](/assets/img/2024-05-23-6ResourcestoMasterVectorDatabasesBuildingaVectorStorage_1.png)

벡터 데이터베이스는 자연어 처리, 이미지 인식, 추천 시스템 및 시맨틱 검색과 같은 다양한 분야에서 중요한 역할을 하며, 대규모 언어 모델의 점점 증가하는 채택으로 더 많은 중요성을 얻고 있습니다.

이러한 데이터베이스는 LLM(Large Language Models)에 실시간 독점 데이터에 액세스할 수 있게 함으로써 검색 증강 생성(Retreival Augmented Generation, RAG) 애플리케이션의 개발을 가능케 하는 데에 매우 가치가 있습니다.

핵심적으로, 벡터 데이터베이스는 임베딩의 사용에 의존하여 데이터의 의미를 포착하고 다른 벡터 쌍 사이의 유사성을 측정하며 방대한 데이터 세트를 살피면서 가장 유사한 벡터를 식별합니다.

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

이 과정은 벡터 데이터베이스를 애플리케이션에 적용할 때 언제 결정을 내릴지에 대한 지식을 습득하는 데 도움이 될 것입니다. 다음을 살펴볼 것입니다:

- 벡터 데이터베이스와 LLM을 사용하여 데이터에 대해 더 깊은 통찰을 얻는 방법.
- 임베딩을 형성하고 유사한 임베딩을 찾는 데 몇 가지 검색 기술을 사용하는 빌드 랩의 방법.
- 방대한 데이터 세트를 빠르게 검색하기 위한 알고리즘 탐색 및 RAG에서 다국어 검색까지 다양한 애플리케이션 구축.

## 2. 벡터 데이터베이스로 애플리케이션 빌딩

![이미지](/assets/img/2024-05-23-6ResourcestoMasterVectorDatabasesBuildingaVectorStorage_2.png)

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

벡터 데이터베이스는 데이터의 의미를 포착하기 위해 임베딩을 사용하며, 서로 다른 벡터 쌍 간의 유사성을 측정하고 가장 유사한 벡터를 식별하기 위해 대규모 데이터 세트를 탐색합니다.

대형 언어 모델의 맥락에서 벡터 데이터베이스의 주요 용도는 검색이 강화된 생성 (RAG)이며, 여기서 텍스트 임베딩이 저장되고 특정 쿼리에 대해 검색됩니다.

그러나 벡터 데이터베이스의 다양성은 RAG를 넘어 확장되어 최소한의 코딩으로 빠르게 다양한 응용 프로그램을 구축할 수 있게 합니다.

이 강좌에서는 벡터 데이터베이스를 사용하여 육 가지 응용 프로그램을 구현하는 방법을 탐험하게 됩니다:

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

- 시멘틱 검색: 사용자 Q/A 데이터 세트에서 이해 측면에 중점을 둔 콘텐츠의 의미를 가리키며, 단어 일치 이상의 검색 도구를 만듭니다.
- RAG: 모델이 훈련되지 않은 소스에서 내용을 통합하여 LLM 응용 프로그램을 향상시킵니다. Wikipedia 데이터 세트를 사용하여 질문에 대답합니다.
- 추천 시스템: 시멘틱 검색과 RAG를 결합하여 주제를 추천하고 뉴스 기사 데이터 세트로 시연하는 시스템을 개발합니다.
- 혼합 검색: 이미지와 설명 텍스트를 모두 사용하여 항목을 찾는 응용 프로그램을 작성하며, 이를 위해 전자 상거래 데이터 세트를 사용합니다.
- 얼굴 유사성: 공인 인물 데이터베이스를 사용하여 얼굴 특징을 비교하는 앱을 만듭니다.
- 이상 감지: 네트워크 통신 로그에서 비정상적인 패턴을 식별하는 이상 감지 앱을 만드는 방법을 배웁니다.

이 과정을 마치면 새로운 아이디어로 어떤 벡터 데이터베이스 애플리케이션을 개발할 수 있을 것입니다.

# 3. 최고의 5가지 벡터 데이터베이스 블로그

![image](/assets/img/2024-05-23-6ResourcestoMasterVectorDatabasesBuildingaVectorStorage_3.png)

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

이 블로그는 Moez Ali에 의해 작성된 것으로, 최고의 벡터 데이터베이스에 대한 포괄적인 가이드입니다. 고차원 데이터 저장, 비구조적 정보 해독, 그리고 AI 응용 프로그램을 위한 벡터 임베딩을 활용하는 방법을 마스터하는 데 도움이 될 것입니다.

이 블로그는 다음을 다룹니다:

- 벡터 데이터베이스란 무엇인가?
- 벡터 데이터베이스는 어떻게 작동하는가?
- 벡터 데이터베이스의 예시
- 우수한 벡터 데이터베이스의 특징
- 2023년 최고의 5개 벡터 데이터베이스
- AI의 부상과 벡터 데이터베이스의 영향

# 4. LangChain — 텍스트 분할기

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

![LangChain](/assets/img/2024-05-23-6ResourcestoMasterVectorDatabasesBuildingaVectorStorage_4.png)

LangChain — 텍스트 분리기는 LangChain에서 구현된 다양한 텍스트 분리기 목록입니다. 문서를 로드한 후에는 종종 애플리케이션에 더 적합하도록 변환하고 싶을 것입니다.

가장 간단한 예는 긴 문서를 모델 컨텍스트 창에 맞게 더 작은 조각으로 나누고 싶을 수 있다는 것입니다. LangChain에는 문서를 쉽게 분할, 결합, 필터링 및 기타 조작할 수 있게 해주는 내장 문서 변환기가 여러 개 있습니다.

텍스트의 긴 부분을 처리하려면 해당 텍스트를 조각으로 나누는 것이 필요합니다. 이것이 얼마나 단순한 것처럼 들리더라도 여기에는 많은 잠재적인 복잡성이 있습니다. 이상적으로는 의미론적으로 관련된 텍스트 조각을 함께 유지하고 싶을 것입니다. "의미론적으로 관련된"이란 무슨 의미인지는 텍스트 유형에 따라 달라질 수 있습니다. 이 노트북에서는 이를 수행하는 여러 방법을 소개합니다.

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

# 5. Sentence Transformers 라이브러리

![이미지](/assets/img/2024-05-23-6ResourcestoMasterVectorDatabasesBuildingaVectorStorage_5.png)

Sentence Transformers 라이브러리는 최신의 문장, 텍스트 및 이미지 임베딩을 위한 인기 있는 Python 프레임워크입니다. 초기 작업은 저희 논문인 'Sentence-BERT: Siamese BERT-Networks를 사용한 문장 임베딩'에서 설명되었습니다.

이 프레임워크를 사용하여 100개 이상의 언어에 대한 문장/텍스트 임베딩을 계산할 수 있습니다. 이러한 임베딩은 코사인 유사도와 같이 사용하여 의미가 유사한 문장을 찾는 데 사용할 수 있습니다. 의미론적 텍스트 유사성, 의미론적 검색 또는 패러프레이즈 마이닝에 유용할 수 있습니다.

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

해당 프레임워크는 PyTorch와 Transformers를 기반으로 하며, 다양한 작업에 튜닝된 미리 학습된 모델의 큰 컬렉션을 제공합니다. 또한, 모델을 쉽게 세밀 조정할 수 있습니다.

## 6. MTEB 리더보드

![이미지](/assets/img/2024-05-23-6ResourcestoMasterVectorDatabasesBuildingaVectorStorage_6.png)

MTEB 리더보드는 임베딩 모델을위한 리더보드이며, 다양한 임베딩 모델을 비교하여 사용할 수 있습니다.

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

## 만약 이 글을 좋아하시고 저를 지원하고 싶으시면 다음을 확인해주세요:

- 👏 이야기에 박수를 보내주세요 (50번 박수) - 그러면 이 기사를 주목할 수 있습니다
- To Data & Beyond 뉴스레터를 구독해주세요
- 제 Medium 페이지를 팔로우해주세요
- 📰 제 Medium 프로필에서 더 많은 콘텐츠를 확인해주세요
- 🔔 저를 팔로우해주세요: LinkedIn | Youtube | GitHub | Twitter

## To Data & Beyond 뉴스레터를 구독하여 제 글을 미리 보고 전체로 읽어보세요:

## 데이터 과학과 AI 분야에서 새로운 경력을 시작하고 방법을 모르는가요? 데이터 과학 멘토링 세션과 장기적인 경력 멘토링을 제공하고 있습니다:

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

- 멘토링 세션: [링크](https://lnkd.in/dXeg3KPW)
- 장기 멘토링: [링크](https://lnkd.in/dtdUYBrM)

![이미지](/assets/img/2024-05-23-6ResourcestoMasterVectorDatabasesBuildingaVectorStorage_7.png)
