---
title: "LLM을 사용하여 데이터베이스를 쿼리하는 동안 RAG를 사용하는 데 마주하는 주요 4가지 문제 및 해결 방법"
description: ""
coverImage: "/assets/img/2024-05-18-Top4ChallengesusingRAGwithLLMstoQueryDatabaseText-to-SQLandhowtosolveit_0.png"
date: 2024-05-18 18:18
ogImage: 
  url: /assets/img/2024-05-18-Top4ChallengesusingRAGwithLLMstoQueryDatabaseText-to-SQLandhowtosolveit_0.png
tag: Tech
originalTitle: "Top 4 Challenges using RAG with LLMs to Query Database (Text-to-SQL) and how to solve it."
link: "https://medium.com/wrenai/4-key-technical-challenges-using-rag-with-llms-to-query-database-text-to-sql-and-how-to-solve-it-5d5a3d6682e5"
---


The Advent of LLMs shows the ability of machines to comprehend natural language. These capabilities have helped engineers to do a lot of amazing things, such as writing code documentation and code reviews, and one of the most common use cases is code generation; GitHub copilot has shown the capability of AI to comprehend engineers’ intention for code generation, such as Python, Javascript, and SQL, though LLM’s comprehension AI could understand what we want to do and generate code accordingly.

# Using LLM to solve Text-to-SQL

Based on the code generation capability of LLMs, many people have started considering using LLMs to solve the long-term hurdle of using natural language to retrieve data from databases, sometimes called “Text-to-SQL.” The idea of “Text-to-SQL” is not new; after the presence of “Retrieval Augmented Generation (RAG)” and the latest LLM models breakthrough, Text-to-SQL has a new opportunity to leverage LLM comprehension with RAG techniques to understand internal data and knowledge. 

![Top 4 Challenges using RAG with LLMs to Query Database Text-to-SQL and how to solve it](/assets/img/2024-05-18-Top4ChallengesusingRAGwithLLMstoQueryDatabaseText-to-SQLandhowtosolveit_0.png)

<div class="content-ad"></div>

# RAG를 사용한 텍스트-SQL의 도전 과제

텍스트-SQL 시나리오에서 사용자는 LLM이 생성한 결과를 신뢰하기 위해 정밀도, 보안 및 안정성을 갖추어야합니다. 그러나 실행 가능하고 정확하며 보안이 제어된 텍스트-SQL 솔루션을 추구하는 것은 간단하지 않습니다. 여기에서는 자연어를 통해 데이터베이스를 쿼리하기 위해 RAG를 사용한 LLM 사용의 네 가지 주요 기술적 도전 과제를 요약해보았습니다: 컨텍스트 수집, 검색, SQL 생성 및 협업.

![이미지](/assets/img/2024-05-18-Top4ChallengesusingRAGwithLLMstoQueryDatabaseText-to-SQLandhowtosolveit_1.png)

## 도전 과제 1: 컨텍스트 수집 도전과제

<div class="content-ad"></div>

- 다양한 원본 간 상호 운용성: 다양한 소스, 메타데이터 서비스 및 API 간에 원활하게 검색 및 통합된 정보를 일반화하고 표준화하는 것이 중요합니다.
- 데이터와 메타데이터의 복잡한 링킹: 이는 데이터를 해당 문서 저장소의 메타데이터와 연결하는 것을 포함합니다. 관련성, 계산 및 집계와 같은 메타데이터, 스키마 및 컨텍스트를 저장하는 것이 포함됩니다.

## 도전 과제 2: 검색 도전과제

- 벡터 저장소의 최적화: 인덱싱 및 청킹과 같은 벡터 저장소를 최적화하기 위한 기술을 개발하고 구현하는 것은 검색 효율성과 정확도 향상에 중요합니다.
- 의미 검색의 정확도: 도전 과제는 질의 이해의 뉘앙스에 있으며 이는 결과의 정확도에 중대한 영향을 미칠 수 있습니다. 이는 일반적으로 쿼리 재작성, 다시 순위 지정 등과 같은 기술을 포함합니다.

## 도전 과제 3: SQL 생성 도전과제

<div class="content-ad"></div>

- SQL 쿼리의 정확성 및 실행 가능성: 정확하고 실행 가능한 SQL 쿼리를 생성하는 것은 상당한 도전입니다. 이를 위해서는 LLM이 SQL 구문, 데이터베이스 스키마, 그리고 다양한 데이터베이스 시스템의 특정 방언에 대한 깊은 이해가 필요합니다.
- 쿼리 엔진 방언 적응: 데이터베이스는 종종 SQL 구현에서 고유한 방언과 뉘앙스를 가집니다. 이러한 차이에 적응하고 다양한 시스템 간에 호환되는 쿼리를 생성할 수 있는 LLM을 설계하는 것은 도전의 복잡도를 더 높이는 요소입니다.

## 도전 4: 협업 도전

- 집단 지식 축적: 도전은 다양한 사용자 그룹으로부터 수집된 집단적인 통찰과 피드백을 효과적으로 수집, 통합, 그리고 활용하여 LLM이 검색하는 데이터의 정확성과 관련성을 향상하는 메커니즘을 만드는 데에 있습니다.
- 접근 제어: 데이터를 검색하는 것에 대한 다음으로 중요한 도전은 존재하는 조직 데이터 접근 정책 및 개인정보 보호 규정이 새로운 LLM 및 RAG 아키텍처에도 적용되도록 보장하는 것입니다.

더 많은 정보를 원하시나요? 각 도전에 대해 미래 게시물에서 자세히 공유할 계획입니다. 알림을 받으려면 Medium에서 팔로우해주세요!

<div class="content-ad"></div>

# 어떻게 문제를 해결할 수 있을까요? LLM을 위한 의미론적 레이어.

위의 과제들을 해결하기 위해서, 우리는 LLM과 데이터 소스 사이에 레이어가 필요합니다. 이 레이어를 통해 LLM이 비즈니스 의미론과 메타데이터를 데이터 소스로부터 학습할 수 있게 되며, 이 레이어는 종종 "의미론적 레이어"라고 불리는 것이 필요합니다. 의미론적 레이어는 의미론과 데이터 구조 간의 연결을 해결하고, 액세스 제어와 식별 관리를 조정하여 정확한 사용자만이 정확한 데이터에 액세스하도록 보장해야 합니다.

LLM을 위한 의미론적 레이어에는 무엇이 포함되어야 할까요? 여기서 몇 가지 측면으로 일반화해봅시다.

## 데이터 해석 및 표현

<div class="content-ad"></div>

- 비즈니스 용어 및 개념: 시맨틱 레이어는 비즈니스 용어와 개념의 정의를 포함합니다. 예를 들어, "수익"과 같은 용어는 시맨틱 레이어에 정의되어 있어서 비즈니스 사용자가 BI 도구에서 "수익"을 조회할 때 시스템이 어떤 데이터를 검색하고 어떻게 계산할지 정확히 알고 있습니다.

- 데이터 관계: 이것은 서로 다른 데이터 엔티티 간의 관계를 정의합니다. 예를 들어, 고객 데이터가 판매 데이터와 어떻게 관련되는지 또는 제품 데이터가 재고 데이터와 연결되는 방법 등이 있습니다. 이러한 관계는 복잡한 분석을 수행하고 통찰을 얻는 데 중요합니다.

- 계산 및 집계: 시맨틱 레이어에는 종종 미리 정의된 계산 및 집계 규칙이 포함됩니다. 이는 사용자가 예를 들어 금년 매출을 계산하기 위해 복잡한 수식을 작성하는 방법을 알 필요가 없다는 것을 의미합니다. 시맨틱 레이어는 내부 데이터 원본을 기반으로 이러한 작업을 정의 및 규칙에 따라 처리합니다.

## 데이터 액세스 및 보안

- 보안 및 액세스 제어: 이것은 누가 어떤 데이터에 액세스할 수 있는지를 관리할 수도 있습니다. 사용자가 액세스 권한을 부여받은 데이터만 볼 수 있고 분석할 수 있도록 보장하여 데이터 프라이버시를 유지하고 규정을 준수하는 데 중요합니다.

## 데이터 구조 및 조직

<div class="content-ad"></div>

- 데이터 소스 매핑: 시맨틱 레이어는 비즈니스 용어와 개념을 실제 데이터 소스에 매핑합니다. 이는 각 비즈니스 용어에 해당하는 데이터베이스 테이블과 열을 지정하고, BI 도구가 올바른 데이터를 검색할 수 있도록 합니다.
- 다차원 모델: 일부 BI 시스템에서 시맨틱 레이어에는 다차원 모델(예: OLAP 큐브)이 포함되어 복잡한 분석과 데이터 슬라이싱/다이싱이 가능합니다. 이러한 모델은 사용자가 쉽게 탐색하고 분석할 수 있는 차원과 측정 값을 구성합니다.

## 메타데이터

- 메타데이터 관리: 메타데이터를 관리합니다. 이는 데이터에 대한 데이터로서, 데이터 원본, 변환, 데이터 계보 등 데이터를 이해하는 데 도움이 되는 모든 정보가 포함됩니다.

# WrenAI 소개

<div class="content-ad"></div>

![이미지](/assets/img/2024-05-18-Top4ChallengesusingRAGwithLLMstoQueryDatabaseText-to-SQLandhowtosolveit_2.png)

WrenAI는 오픈 소스입니다. 데이터, LLM API 및 환경 어디에서든 WrenAI를 배포할 수 있습니다. 직관적인 온보딩 및 사용자 인터페이스가 함께 제공되어 몇 분 안에 데이터소스에서 데이터 모델을 연결하고 구축할 수 있습니다.

WrenAI의 하부에는 이전 섹션에서 언급한 LLM을 위한 "Wren Engine"이라는 프레임워크를 개발했습니다. Wren Engine은 GitHub에서도 오픈 소스로 제공됩니다. Wren Engine에 관심이 있다면 댓글을 남겨주시기 바랍니다. 앞으로 나올 글에서 아키텍처와 디자인에 대해 더 자세히 공유할 계획입니다.

## WrenAI에서의 모델링

<div class="content-ad"></div>

데이터 소스와 연결이 완료되면 자동으로 모든 메타데이터를 수집하며 WrenAI UI를 통해 비즈니스 의미론과 관계를 추가할 수 있습니다. 미래의 의미론적 검색을 위해 자동으로 벡터 저장소를 업데이트할 것입니다.

![이미지](/assets/img/2024-05-18-Top4ChallengesusingRAGwithLLMstoQueryDatabaseText-to-SQLandhowtosolveit_3.png)

## 질문하고 따라가기

모델링을 마치고 나면 비즈니스 질문을 시작할 수 있습니다. WrenAI는 가장 관련성 높은 결과 3개를 찾아 제공할 것입니다. 옵션 중 하나를 선택하면 해당 데이터의 출처 및 요약을 단계별 설명으로 제공해 드립니다. 이를 통해 WrenAI가 제안하는 결과를 더 자신 있게 사용할 수 있습니다.

<div class="content-ad"></div>

WrenAI로부터 결과를 받으면 반환된 결과를 기반으로 깊은 통찰이나 분석을 위한 후속 질문을 할 수 있습니다.

![image](/assets/img/2024-05-18-Top4ChallengesusingRAGwithLLMstoQueryDatabaseText-to-SQLandhowtosolveit_4.png)

## 지금 GitHub에서 WrenAI를 사용해보고 커뮤니티에 참여해보세요!

👉 GitHub: https://github.com/Canner/WrenAI

<div class="content-ad"></div>

👉 디스코드: https://discord.gg/5DvshJqG8Z