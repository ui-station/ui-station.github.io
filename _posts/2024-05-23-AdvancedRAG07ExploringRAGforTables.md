---
title: "고급 RAG 07 테이블을 위한 RAG 탐색"
description: ""
coverImage: "/assets/img/2024-05-23-AdvancedRAG07ExploringRAGforTables_0.png"
date: 2024-05-23 18:13
ogImage:
  url: /assets/img/2024-05-23-AdvancedRAG07ExploringRAGforTables_0.png
tag: Tech
originalTitle: "Advanced RAG 07: Exploring RAG for Tables"
link: "https://medium.com/ai-in-plain-english/advanced-rag-07-exploring-rag-for-tables-5c3fc0de7af6"
---

RAG를 구현하는 것은 도전적인 과제를 제공하는데, 특히 비구조화된 문서의 테이블을 효과적으로 구문 분석하고 이해하는 부분이 그 중요한 부분입니다. 특히 스캔된 문서나 이미지 형식의 문서에서는 이 작업이 특히 어려울 수 있습니다. 이러한 도전 과제에는 적어도 다음 세 가지 측면이 있습니다:

- 스캔된 문서 또는 이미지 문서의 복잡성, 다양한 구조, 비텍스트 요소의 포함 및 필기 및 인쇄된 내용의 결합과 같은 특징들은 테이블 정보를 정확하게 자동 추출하는 데 도전을 제공합니다. 부정확한 구문 분석은 테이블 구조를 손상시킬 수 있으며, 불완전한 테이블을 포함하는 것은 테이블의 의미 정보를 포착하지 못할 뿐만 아니라 RAG 결과를 손상시킬 수 있습니다.
- 테이블 캡션을 추출하고 해당 테이블에 효과적으로 연결하는 방법.
- 테이블의 의미 정보를 효과적으로 저장하기 위한 색인 구조를 설계하는 방법.

이 기사는 RAG 내에서 테이블을 관리하는 주요 기술을 소개한 후 일부 기존 오픈 소스 솔루션을 검토한 다음 새로운 솔루션을 제안하고 구현하는 방법을 제시합니다.

# 주요 기술

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

## 테이블 구문 분석

이 모듈의 주요 기능은 정형화되지 않은 문서나 이미지에서 테이블 구조를 정확하게 추출하는 것입니다.

추가 기능: 해당하는 테이블 캡션을 추출하고, 개발자가 해당 테이블 캡션을 테이블과 관련 짓기 편리하도록 하는 것이 가장 좋습니다.

제 현재 이해에 따르면, Figure 1에 나타난 것처럼 여러 가지 방법이 있습니다:

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

![이미지](/assets/img/2024-05-23-AdvancedRAG07ExploringRAGforTables_0.png)

(a). 다중 모달 LLM(예: GPT-4V)을 활용하여 각 PDF 페이지에서 표를 식별하고 정보를 추출합니다.

- 입력: 이미지 형식의 PDF 페이지
- 출력: JSON 또는 다른 형식의 표. 다중 모달 LLM이 표 데이터를 추출하지 못하는 경우 이미지를 요약하고 요약본을 반환해야 합니다.

(b). Table Transformer와 같은 전문적인 표 감지 모델을 활용하여 표 구조를 식별합니다.

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

- 입력: 이미지로 된 PDF 페이지
- 출력: 이미지로 된 테이블

(c). 오픈 소스 프레임워크인 unstructured 등을 사용하여 객체 검출 모델을 활용하세요(unstructured의 테이블 검출 과정은 이 기사에 자세히 기재되어 있습니다). 이러한 프레임워크를 사용하면 전체 문서의 철저한 구문 분석과 구문 분석 결과로부터 테이블 관련 콘텐츠의 추출이 가능합니다.

- 입력: PDF 또는 이미지 형식의 문서
- 출력: 문서 전체의 구문 분석 결과로부터 얻은 테이블을 일반 텍스트 또는 HTML 형식으로

(d). Nougat, Donut 등의 end-to-end 모델을 사용하여 전체 문서를 구문 분석하고 테이블 관련 콘텐츠를 추출하세요. 이 접근 방식은 OCR 모델을 필요로하지 않습니다.

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

- 입력: PDF 또는 이미지 형식의 문서
- 출력: 전체 문서의 구문 분석 결과를 통해 얻은 LaTeX 또는 JSON 형식의 표

언급할 가치가 있는 것은 표 정보를 추출하는 방법에 관계없이 표 캡션을 포함해야 합니다. 대부분의 경우 표 캡션은 문서나 논문 작성자가 표에 대해 간단히 설명한 것으로, 전체 표를 크게 요약할 수 있습니다.

위에서 언급한 네 가지 방법 중 (d) 방법은 표 캡션을 쉽게 검색할 수 있습니다. 개발자에게는 이 방법이 유용한데, 표 캡션을 표와 연관시킬 수 있도록 해주기 때문입니다. 이 내용은 다음 실험에서 자세히 설명될 것입니다.

## 색인 구조

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

색인 구조에 따라 해결책은 대략 다음 카테고리로 나뉩니다:

(e). 이미지 형식의 색인 표만 있는 경우.

(f). 일반 텍스트 또는 JSON 형식의 색인 표만 있는 경우.

(g). LaTeX 형식의 색인 표만 있는 경우.

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

### (h). 테이블 요약만 색인화합니다.

### (i). 작은 부터 큰 또는 문서 요약 색인 구조, Figure 2에 나와 있는 것처럼.

- 작은 청크의 내용은 테이블의 각 행 정보 또는 테이블 요약일 수 있습니다.
- 큰 청크의 내용은 이미지 형식, 일반 텍스트 형식 또는 LaTeX 형식의 테이블일 수 있습니다.

![Figure 2](/assets/img/2024-05-23-AdvancedRAG07ExploringRAGforTables_1.png)

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

위에서 논의한 대로 Table summary는 일반적으로 LLM을 사용하여 생성됩니다:

- 입력: 이미지 형식, 텍스트 형식 또는 LaTeX 형식의 테이블
- 출력: 테이블 요약

## Table Parsing, Indexing 또는 RAG가 필요하지 않은 algorithms

일부 알고리즘은 테이블 파싱이 필요하지 않습니다.

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

(j). 관련 이미지(PDF 페이지)와 사용자 쿼리를 VQA 모델(예: DAN 등)이나 멀티모달 LLM에 보내고 답변을 받습니다.

- 색인할 내용: 이미지 형식의 문서
- VQA 모델이나 멀티모달 LLM에 전송되는 내용: 쿼리 + 해당 이미지 페이지

(k). 관련 텍스트 형식의 PDF 페이지와 사용자 쿼리를 LLM에 보내고, 그런 다음 답변을 받습니다.

- 색인할 내용: 텍스트 형식의 문서
- LLM에 전송되는 내용: 쿼리 + 해당 텍스트 형식의 페이지

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

(l) 사용자의 쿼리와 관련 이미지(PDF 페이지), 텍스트 청크를 다중 모달 LLM(예: GPT-4V 등)에 보내고 답변을 직접 반환합니다.

- 색인할 콘텐츠: 이미지 형식의 문서 및 텍스트 형식의 문서 청크
- 다중 모달 LLM에 보내는 콘텐츠: 쿼리 + 문서의 이미지 형식 + 해당하는 텍스트 청크

또한, 다음은 색인이 필요하지 않은 몇 가지 방법입니다. 그림 3과 그림 4에서 보듯이:

![image](/assets/img/2024-05-23-AdvancedRAG07ExploringRAGforTables_2.png)

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

(m). 먼저, 문서의 모든 표를 이미지 형태로 변환하기 위해 (a)부터 (d) 범주 중 하나의 방법을 적용하세요. 그런 다음 모든 표 이미지와 사용자 질의를 멀티모달 LLM(예: GPT-4V 등)에 직접 전송하여 답변을 받아보세요.

- 색인할 내용: 없음
- 멀티모달 LLM에 전송될 내용: 질의 + 모든 변환된 표(이미지 형태)

![표 이미지](/assets/img/2024-05-23-AdvancedRAG07ExploringRAGforTables_3.png)

(n). (m)에서 추출된 이미지 형식의 표를 사용하여 OCR 모델을 이용해 표 안의 모든 텍스트를 인식한 후, 표 안의 모든 텍스트와 사용자 질의를 LLM에 직접 전송하여 답변을 받아보세요.

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

- 색인할 콘텐츠: 없음
- LLM에 보내지는 콘텐츠: 사용자 쿼리 + 모든 테이블 내용(텍스트 형식)

익명의 사실을 공유하자면, 일부 방법은 RAG 프로세스에 의존하지 않습니다:

- 첫 번째 방법은 LLM을 사용하지 않으며, 특정 데이터세트에서 학습하며 모델(예: BERT와 유사한 트랜스포머)이 TAPAS와 같은 테이블 이해 작업을 더 잘 지원하도록 합니다.
- 두 번째 방법은 LLM을 사용하며, 사전 학습, 파인 튜닝 방법 또는 프롬프트를 사용하여 LLM이 GPT4Table과 같은 테이블 이해 작업을 수행할 수 있도록 합니다.

# 기존 오픈 소스 솔루션

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

이전 섹션에서는 RAG의 테이블에 대한 주요 기술을 요약하고 분류했습니다. 이 글이 구현하는 해결책을 제안하기 전에 오픈 소스 솔루션 중 일부를 탐색해보겠습니다.

LlamaIndex는 네 가지 방법을 제안했는데, 처음 세 가지는 다중 모달 모델을 사용합니다.

- 관련 이미지(PDF 페이지)를 검색하여 이를 GPT-4V에 보내 쿼리에 대답하도록 합니다.
- 각 PDF 페이지를 이미지로 간주하고, 각 페이지에 대해 이미지 추론을 수행하도록 GPT-4V에게 맡깁니다. 이미지 추론을 위한 텍스트 벡터 저장소 인덱스를 작성합니다. 이미지 추론 벡터 저장소에 대한 답변을 쿼리로 가져옵니다.
- 테이블 트랜스포머를 사용하여 검색된 이미지에서 테이블 정보를 잘라내고, 이러한 잘린 이미지를 GPT-4V에 보내 쿼리 응답을 받습니다.
- 잘려진 테이블 이미지에 OCR을 적용하고 데이터를 GPT4/GPT-3.5로 보내어 쿼리에 답변을 받습니다.

이 글의 분류에 따르면:

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

- 이 기사에서의 (j) 항목과 유사한 첫 번째 방법은 테이블 구문 분석이 필요하지 않습니다. 그러나 결과는 이미지에 정답이 있더라도 올바른 답을 내놓지 못하는 것을 보여줍니다.
- 두 번째 방법은 테이블 구문 분석을 포함하며 (a) 항목과 일치합니다. 색인된 콘텐츠는 GPT-4V의 결과에 따라 테이블 콘텐츠 또는 요약이며, 이는 (f) 또는 (h)에 해당할 수 있습니다. 이 방법의 단점은 GPT-4V가 이미지에서 테이블을 식별하고 내용을 추출하는 능력이 불안정하다는 것이며, PDF 형식에서 발생하는 테이블, 텍스트 및 다른 이미지가 혼합된 경우에 특히 해당됩니다.
- 세 번째 방법은 (m) 항목과 유사하며 색인이 필요하지 않습니다.
- 네 번째 방법은 (n) 항목과 유사하며 또한 색인이 필요하지 않습니다. 결과는 이미지에서 테이블 정보를 추출하는 능력이 없어 잘못된 답변이 생성된다고 나타냅니다.

테스트 결과, 세 번째 방법이 전반적으로 가장 효과적인 것으로 나타났습니다. 그러나 제 테스트에 따르면 세 번째 방법은 테이블을 감지하는 데 어려움을 겪고, 특히 테이블 제목을 올바르게 병합하는 것조차 어렵다는 것을 보여줍니다.

Langchain은 일부 솔루션을 제안했습니다. Semi-structured RAG의 주요 기술은 다음과 같습니다:

- 테이블 구문 분석은 비구조적을 사용하며, 이는 (c) 항목에 해당합니다.
- 색인 방법은 문서 요약 색인이며, 이는 (i) 항목에 해당합니다. 작은 청크 콘텐츠: 테이블 요약, 큰 청크 콘텐츠: 원시 테이블 콘텐츠(텍스트 형식).

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

아래 그림 5에 나타난 대로:

![Figure 5](/assets/img/2024-05-23-AdvancedRAG07ExploringRAGforTables_4.png)

반구조화 및 멀티 모달 RAG는 세 가지 해결책을 제안하며, 아키텍처는 아래 그림 6에 나와 있습니다.

![Figure 6](/assets/img/2024-05-23-AdvancedRAG07ExploringRAGforTables_5.png)

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

옵션 1은 이 기사의 (l) 범주와 유사합니다. 멀티모달 임베딩(예: CLIP)을 사용하여 이미지와 텍스트를 임베드하고 유사성 검색을 통해 둘 다 검색하며, 생 이미지 및 청크를 멀티모달 LLM에게 전달하여 답변 합성을 수행합니다.

옵션 2은 이미지로부터 텍스트 요약을 생성하는 멀티모달 LLM(예: GPT-4V, LLaVA, 또는 FUYU-8b)을 활용합니다. 그런 다음 텍스트를 임베드하고 검색하여 텍스트 청크를 LLM에게 전달하여 답변 합성을 합니다.

- 테이블 파싱은 구조화되지 않은 것을 사용합니다. 이는 범주 (d)입니다.
- 색인 구조는 문서 요약 인덱스(범주 (i))이며, 작은 청크 내용: 테이블 요약, 큰 청크 내용: 텍스트 형식의 테이블

옵션 3은 이미지로부터 텍스트 요약을 생성하기 위해 멀티모달 LLM(예: GPT-4V, LLaVA, 또는 FUYU-8b)를 사용합니다. 그런 다음 이미지 요약을 임베드하고 검색하여 원본 이미지에 대한 이미지 요약과 함께 반환하고, 원본 이미지 및 텍스트 청크를 멀티모달 LLM에게 전달하여 답변 합성을 수행합니다.

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

# 제안된 솔루션

이 글은 주요 기술 및 기존 솔루션을 요약, 분류 및 논의하였습니다. 이를 기반으로 다음과 같은 솔루션을 제안합니다. 간단히 말해서 Re-ranking 및 query rewriting과 같은 RAG 모듈은 생략되었습니다.

![Figure 7](/assets/img/2024-05-23-AdvancedRAG07ExploringRAGforTables_6.png)

- 테이블 파싱: Nougat(catogery (d))를 사용합니다. 제 테스트에 따르면, 이는 테이블 감지가 unstructured(catogery (c))보다 더 효과적입니다. 게다가 Nougat은 테이블 캡션을 잘 추출하여 테이블과 연결하는 데 매우 편리합니다.
- 문서 요약 인덱스 구조(catogery (i)): 작은 청크의 내용에는 테이블 요약이, 큰 청크의 내용에는 LaTeX 형식의 해당 테이블과 텍스트 형식의 테이블 캡션이 포함됩니다. 이를 다중 벡터 검색기를 사용하여 구현합니다.
- 테이블 요약 획득 방법: 테이블과 테이블 캡션을 LLM에 보내 요약을 받습니다.

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

이 방법의 장점은 테이블을 효율적으로 구문 분석하면서 테이블 요약과 테이블 간의 관계를 포괄적으로 고려한다는 것입니다. 또한, 멀티모달 LLM이 필요하지 않아 비용을 절감할 수 있습니다.

## Nougat의 원칙

Nougat은 도넛 아키텍처를 바탕으로 개발되었습니다. Figure 8에서 보여지듯이 OCR 관련 입력이나 모듈이 필요하지 않고 네트워크를 통해 텍스트를 인식합니다.

![Nougat Principle](/assets/img/2024-05-23-AdvancedRAG07ExploringRAGforTables_7.png)

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

누가(Nougat)가 수식을 분석하는 능력이 인상적이에요. 테이블 분석에도 능숙해요. 더불어, 테이블 캡션을 연결하여 보여줄 수 있어 편하지요. 그림 9에서 보여졌듯이요:

![Figure 9](/assets/img/2024-05-23-AdvancedRAG07ExploringRAGforTables_8.png)

수많은 논문을 테스트한 결과, 테이블 캡션이 항상 테이블 다음 줄에 고정되어 있는 것을 발견했어요. 이 일관성은 우연이 아님을 시사하며, 그래서 누가(Nougat)가 이 효과를 달성하는 방법에 관심이 있어요.

중간 결과가 없는 엔드 투 엔드 모델이므로, 훈련 데이터에 많이 의존할 것으로 예상됩니다.

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

테이블 태그를 Markdown 형식으로 변경하세요.

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

- 누가(Nougat)는 이전 파싱 도구에서 어려웠던 부분인 수식 및 표와 같은 부분을 정확하게 LaTeX 소스 코드로 파싱할 수 있습니다.
- 누가(Nougat)의 파싱 결과는 마크다운과 유사한 반구조화된 문서입니다.
- 쉽게 표 제목을 얻고 해당 표와 편리하게 연결할 수 있습니다.

단점:

- 누가(Nougat)의 파싱 속도가 느리기 때문에 대규모 배포에 도전이 될 수 있습니다.
- 누가(Nougat)는 과학 논문을 기반으로 학습되었기 때문에 비슷한 구조의 문서에서 뛰어난 성능을 발휘합니다. 그러나 비라틴 문자 텍스트 문서에서는 성능이 떨어집니다.
- 누가(Nougat) 모델은 한 번에 한 페이지의 과학 논문만을 학습하며, 다른 페이지에 대한 지식이 부족합니다. 이로 인해 파싱된 콘텐츠에 일관성이 없을 수 있습니다. 따라서, 인식 효과가 좋지 않다면 PDF를 개별 페이지로 나누고 하나씩 파싱하는 것을 고려해야 합니다.
- 이중 칼럼 논문에서의 표 파싱은 단일 칼럼 논문과 같이 효과적이지 않을 수 있습니다.

## 코드 구현

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

먼저 관련 Python 패키지를 설치해주세요.

```js
pip install langchain
pip install chromadb
pip install nougat-ocr
```

설치를 완료한 후, Python 패키지의 버전을 확인할 수 있습니다.

```js
langchain                                0.1.12
langchain-community                      0.0.28
langchain-core                           0.1.31
langchain-openai                         0.0.8
langchain-text-splitters                 0.0.1

chroma-hnswlib                           0.7.3
chromadb                                 0.4.24

nougat-ocr                               0.1.17
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

환경을 설정하고 라이브러리를 가져와주세요:

```js
import os
os.environ["OPENAI_API_KEY"] = "YOUR_OPEN_AI_KEY"

import subprocess
import uuid

from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI
from langchain.retrievers.multi_vector import MultiVectorRetriever
from langchain.storage import InMemoryStore
from langchain_community.vectorstores import Chroma
from langchain_core.documents import Document
from langchain_openai import OpenAIEmbeddings
from langchain_core.runnables import RunnablePassthrough
```

Attention Is All You Need 논문을 YOUR_PDF_PATH로 다운로드하고, nougat을 실행하여 PDF 파일을 구문 분석하고 해당 결과로부터 LaTex 형식의 표 및 텍스트 형식의 표 캡션을 얻어주세요. 첫 실행 시 필요한 모델 파일이 다운로드됩니다.

```js
def june_run_nougat(file_path, output_dir):
    # nougat을 실행하고 결과를 Mathpix Markdown으로 저장합니다.
    cmd = ["nougat", file_path, "-o", output_dir, "-m", "0.1.0-base", "--no-skipping"]
    res = subprocess.run(cmd)
    if res.returncode != 0:
        print("nougat 실행 중 오류가 발생했습니다.")
        return res.returncode
    else:
        print("작업 완료!")
        return 0

def june_get_tables_from_mmd(mmd_path):
    f = open(mmd_path)
    lines = f.readlines()
    res = []
    tmp = []
    flag = ""
    for line in lines:
        if line == "\\begin{table}\n":
            flag = "BEGINTABLE"
        elif line == "\\end{table}\n":
            flag = "ENDTABLE"

        if flag == "BEGINTABLE":
            tmp.append(line)
        elif flag == "ENDTABLE":
            tmp.append(line)
            flag = "CAPTION"
        elif flag == "CAPTION":
            tmp.append(line)
            flag = "MARKDOWN"
            print('-' * 100)
            print(''.join(tmp))
            res.append(''.join(tmp))
            tmp = []

    return res

file_path = "YOUR_PDF_PATH"
output_dir = "YOUR_OUTPUT_DIR_PATH"

if june_run_nougat(file_path, output_dir) == 1:
    import sys
    sys.exit(1)

mmd_path = output_dir + '/' + os.path.splitext(file_path)[0].split('/')[-1] + ".mmd"
tables = june_get_tables_from_mmd(mmd_path)
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

함수 june_get_tables_from_mmd은 Figure 10에 표시된 mmd 파일에서 'table'부터 'table'까지의 모든 내용 및 'table' 다음 줄을 추출하는 데 사용됩니다.

![이미지](/assets/img/2024-05-23-AdvancedRAG07ExploringRAGforTables_9.png)

표 캡션을 표 아래에 배치해야 하거나 표가 'table'로 시작하고 'end'로 끝나야 한다는 것을 명시하는 공식 문서가 발견되지 않았다는 점을 유의하십시오. 따라서 june_get_tables_from_mmd는 휴리스틱입니다.

PDF에서 표를 구문 분석한 결과는 다음과 같습니다:

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

표 1: 다양한 레이어 유형에 대한 최대 경로 길이, 레이어당 복잡성 및 최소 순차 작업 수가 표시됩니다.

표 2: Transformer가 영어-독일어 및 영어-프랑스어 newstest2014 테스트에서 이전 최신 모델보다 더 우수한 BLEU 점수를 달성하며 훈련 비용이 줄어드는 것을 보여줍니다.

표 3: Transformer 아키텍처의 변형이 나열되어 있으며, 기본 모델 정보와 영어-독일어 번역 개발 세트 newstest2013에서의 모든 메트릭스가 제공됩니다.

표 4: Transformer가 영어 구성 구문 분석에 잘 일반화되며, WSJ 23 섹션의 결과가 제시됩니다.

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

<img src="/assets/img/2024-05-23-AdvancedRAG07ExploringRAGforTables_10.png" />

다중 벡터 리트리버를 사용하여 문서 요약 색인 구조를 구축해보세요.

```js
# 자식 청크를 색인화하는 데 사용할 벡터스토어
vectorstore = Chroma(collection_name = "summaries", embedding_function = OpenAIEmbeddings())

# 상위 문서를 위한 저장소 레이어
store = InMemoryStore()
id_key = "doc_id"

# 리트리버 (시작할 때는 빈 상태)
retriever = MultiVectorRetriever(
    vectorstore = vectorstore,
    docstore = store,
    id_key = id_key,
    search_kwargs={"k": 1} # 요청된 결과의 수가 색인 요소보다 큰 4이므로, n_results = 1로 업데이트하겠습니다
)

# 테이블 추가
table_ids = [str(uuid.uuid4()) for _ in tables]
summary_tables = [
    Document(page_content = s, metadata = {id_key: table_ids[i]})
    for i, s in enumerate(table_summaries)
]
retriever.vectorstore.add_documents(summary_tables)
retriever.docstore.mset(list(zip(table_ids, tables)))
```

모든 준비가 되었습니다. 간단한 RAG 파이프라인을 구축하고 쿼리를 수행해보세요:

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
# 프롬프트 템플릿
템플릿 = """다음 콘텍스트를 기반으로 질문에 답하세요. 이는 텍스트와 테이블을 포함할 수 있으며 LaTeX 형식의 테이블과 일반 텍스트 형식의 테이블 캡션을 포함합니다:
{context}
질문: {question}
"""
프롬프트 = ChatPromptTemplate.from_template(템플릿)

# LLM
모델 = ChatOpenAI(temperature = 0, model = "gpt-3.5-turbo")


# 간단한 RAG 파이프라인
체인 = (
    {"context": retriever, "question": RunnablePassthrough()}
    | 프롬프트
    | 모델
    | StrOutputParser()
)


print(체인.invoke("when layer type is Self-Attention, what is the Complexity per Layer?"))  # 테이블 1에 관한 쿼리

print(체인.invoke("Which parser performs worst for BLEU EN-DE"))  # 테이블 2에 관한 쿼리

print(체인.invoke("Which parser performs best for WSJ 23 F1"))  # 테이블 4에 관한 쿼리
```

아래는 실행 결과입니다. 여러 질문이 정확히 답변되었음을 보여주는데, 이는 도표 12에 나와 있습니다:

<img src="/assets/img/2024-05-23-AdvancedRAG07ExploringRAGforTables_11.png" />

전체 코드는 아래와 같습니다:

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
import os
os.environ["OPENAI_API_KEY"] = "YOUR_OPEN_AI_KEY"

import subprocess
import uuid

from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI
from langchain.retrievers.multi_vector import MultiVectorRetriever
from langchain.storage import InMemoryStore
from langchain_community.vectorstores import Chroma
from langchain_core.documents import Document
from langchain_openai import OpenAIEmbeddings
from langchain_core.runnables import RunnablePassthrough


def june_run_nougat(file_path, output_dir):
    # Run Nougat and store results as Mathpix Markdown
    cmd = ["nougat", file_path, "-o", output_dir, "-m", "0.1.0-base", "--no-skipping"]
    res = subprocess.run(cmd)
    if res.returncode != 0:
        print("Error when running nougat.")
        return res.returncode
    else:
        print("Operation Completed!")
        return 0

def june_get_tables_from_mmd(mmd_path):
    f = open(mmd_path)
    lines = f.readlines()
    res = []
    tmp = []
    flag = ""
    for line in lines:
        if line == "\\begin{table}\n":
            flag = "BEGINTABLE"
        elif line == "\\end{table}\n":
            flag = "ENDTABLE"

        if flag == "BEGINTABLE":
            tmp.append(line)
        elif flag == "ENDTABLE":
            tmp.append(line)
            flag = "CAPTION"
        elif flag == "CAPTION":
            tmp.append(line)
            flag = "MARKDOWN"
            print('-' * 100)
            print(''.join(tmp))
            res.append(''.join(tmp))
            tmp = []

    return res

file_path = "YOUR_PDF_PATH"
output_dir = "YOUR_OUTPUT_DIR_PATH"

if june_run_nougat(file_path, output_dir) == 1:
    import sys
    sys.exit(1)

mmd_path = output_dir + '/' + os.path.splitext(file_path)[0].split('/')[-1] + ".mmd"
tables = june_get_tables_from_mmd(mmd_path)


# Prompt
prompt_text = """You are an assistant tasked with summarizing tables and text. \
Give a concise summary of the table or text. The table is formatted in LaTeX, and its caption is in plain text format: {element}  """
prompt = ChatPromptTemplate.from_template(prompt_text)

# Summary chain
model = ChatOpenAI(temperature = 0, model = "gpt-3.5-turbo")
summarize_chain = {"element": lambda x: x} | prompt | model | StrOutputParser()
# Get table summaries
table_summaries = summarize_chain.batch(tables, {"max_concurrency": 5})
print(table_summaries)

# The vectorstore to use to index the child chunks
vectorstore = Chroma(collection_name = "summaries", embedding_function = OpenAIEmbeddings())

# The storage layer for the parent documents
store = InMemoryStore()
id_key = "doc_id"

# The retriever (empty to start)
retriever = MultiVectorRetriever(
    vectorstore = vectorstore,
    docstore = store,
    id_key = id_key,
    search_kwargs={"k": 1} # Solving Number of requested results 4 is greater than number of elements in index..., updating n_results = 1
)

# Add tables
table_ids = [str(uuid.uuid4()) for _ in tables]
summary_tables = [
    Document(page_content = s, metadata = {id_key: table_ids[i]})
    for i, s in enumerate(table_summaries)
]
retriever.vectorstore.add_documents(summary_tables)
retriever.docstore.mset(list(zip(table_ids, tables)))


# Prompt template
template = """Answer the question based only on the following context, which can include text and tables, there is a table in LaTeX format and a table caption in plain text format:
{context}
Question: {question}
"""
prompt = ChatPromptTemplate.from_template(template)

# LLM
model = ChatOpenAI(temperature = 0, model = "gpt-3.5-turbo")

# Simple RAG pipeline
chain = (
    {"context": retriever, "question": RunnablePassthrough()}
    | prompt
    | model
    | StrOutputParser()
)

print(chain.invoke("when layer type is Self-Attention, what is the Complexity per Layer?"))  # Query about table 1

print(chain.invoke("Which parser performs worst for BLEU EN-DE"))  # Query about table 2

print(chain.invoke("Which parser performs best for WSJ 23 F1"))  # Query about table 4
```

# 결론

이 글에서는 RAG 프로세스 중 표 처리를 위한 주요 기술과 기존 솔루션을 논의하고 구현과 함께 해결책을 제안합니다.

이 문서에서는 표를 파싱하는 데 nougat을 사용합니다. 그러나 더 빠르고 효과적인 파싱 도구가 있다면 nougat을 대체 고려할 것입니다. 우리의 도구에 대한 태도는 먼저 올바른 아이디어를 가지고, 그런 다음 도구를 찾아 실현하는 것에 있으며, 특정 도구에 의존하는 대신입니다.

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

이 기사에서는 모든 테이블 콘텐츠를 LLM에 입력합니다. 그러나 실제 시나리오에서는 표가 LLM 콘텍스트 길이를 초과하는 경우를 고려해야 합니다. 이 문제를 효과적인 청킹 방법을 사용하여 해결할 수 있습니다.

RAG 기술에 관심이 있다면, 다른 기사들도 확인해보세요.

그리고 최신 AI 관련 콘텐츠는 제 뉴스레터에서 찾을 수 있습니다.

마지막으로, 이 기사에 오류나 누락된 내용이 있다면, 또는 궁금한 점이 있으면 댓글 섹션에서 언급해 주세요.

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

# 친근한 어조로 번역한 내용 🚀

In Plain English 커뮤니티에 참여해 주셔서 감사합니다! 그 전에:

- 저희 작가를 박수로 응원하고 팔로우해 주세요️👏️
- 저희를 팔로우하세요: X | LinkedIn | YouTube | Discord | Newsletter
- 다른 플랫폼도 방문해 보세요: Stackademic | CoFeed | Venture | Cubed
- 더 많은 콘텐츠를 만나실 수 있습니다: PlainEnglish.io
