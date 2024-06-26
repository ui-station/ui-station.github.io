---
title: "알알지 탐정 웹사이트 데이터를 활용한 검색 증대형 생성"
description: ""
coverImage: "/assets/img/2024-05-23-RAGDetectiveRetrievalAugmentedGenerationwithwebsitedata_0.png"
date: 2024-05-23 18:03
ogImage:
  url: /assets/img/2024-05-23-RAGDetectiveRetrievalAugmentedGenerationwithwebsitedata_0.png
tag: Tech
originalTitle: "RAG Detective: Retrieval Augmented Generation with website data"
link: "https://medium.com/@iankelk/rag-detective-retrieval-augmented-generation-with-website-data-5a748b063040"
---

이 기사는 하버드의 AC215 가을 2023 과정의 최종 프로젝트의 일환으로 제작되었습니다.

- 프로젝트 Github 저장소
- 비디오

저자: Ian Kelk, Alyssa Lutservitz, Nitesh Kumar, Mandy Wong

![](/assets/img/2024-05-23-RAGDetectiveRetrievalAugmentedGenerationwithwebsitedata_0.png)

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

대형 언어 모델 (LLM)인 GPT-3.5는 일반적으로 알려진 주제에 대한 질문에는 능숙하게 대답할 수 있음이 증명되었습니다. 그 주제들은 모델이 풍부한 양의 훈련 데이터를 받은 것으로 예상됩니다. 그러나, 그들이 훈련을 받지 않은 데이터가 포함된 주제에 대한 질문을 받을 때, 그들은 종종 그 지식을 갖고 있지 않다고 말하거나, 더 나쁜 경우에는 그럴듯한 답변을 허구로 만들어냅니다.

LLM을 사용하여 회사의 제품 및 서비스에 대해 연구하기는 보통 불가능합니다. 제품 및 서비스를 직접 비교하기 위해서는 모델이 훈련을 받았던 것보다 최근의 데이터가 필요합니다. 우리가 해결하고자 하는 문제는 회사에 대한 최신 정보와 그들의 웹사이트 정보와 일치하는 정보를 얻는 방법을 찾는 것입니다.

또한, 이 과정에 대한 이전에 다뤄지지 않았을 것으로 예상되는 이정표를 충족시키기 위해, GPT-3.5가 답변이 금융적인 성격일 수 있다고 할 때 BERT 모델을 세밀하게 조정하여 금융 감성 분석을 수행합니다.

# 제안된 해결책

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

이 한계를 해결하는 두 가지 주요 방법이 있습니다: 세밀조정(fine-tuning)과 검색 증강 생성(RAG).

세밀조정은 모델을 학습률을 크게 줄여서 사용자의 데이터를 계속하여 훈련시키는 과정입니다. 새롭게 얻은 지식은 모델 가중치에 캡슐화됩니다. 그러나 세밀조정은 모델의 복사본 및 해당 비용을 사용하고 호스팅해야 한다는 점, 그리고 "치명적인 망각"이라는 위험(이전에 배운 정보를 잊어버리는 현상)도 있습니다.

반면 RAG는 주로 포함된 텍스트의 임베딩의 벡터 저장소를 활용한 지식 소스를 사용합니다. 쿼리의 예측된 임베딩을 벡터 저장소의 임베딩과 비교함으로써, 우리는 LLM을 위한 적절한 프롬프트를 형성할 수 있어서 그것이 그의 문맥 안에 맞고 질문에 답할 정보를 포함합니다.

# 우리의 해결책에는 세 가지 주요 구성 요소가 있습니다:

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

- 웹 사이트에서 스크랩 된 데이터를 사용하는 챗봇은 'ssitemap.xml' 파일에서 가져온 데이터를 사용합니다. 이 파일은 검색 엔진에 사이트의 모든 스크랩 가능한 링크를 안내하는 것이 목적입니다. 이는 검색 엔진보다 더 구체적이고 통찰력 있는 방법으로 사용됩니다. LLM(언어 모델)는 질문에 답변할 때 이 맥락만 사용해야 하며, 자체 훈련 데이터를 삽입하거나 답변을 가공해서는 안 됩니다. 모델이 분명히 알고 있을 것으로 예상되는 "김 카다시안은 누구인가요?"와 같은 질문으로 이를 간단히 테스트할 수 있으며, 모델이 "제공된 맥락 안에 답변되지 않았다"고 회신하도록 보장해야 합니다.
- API를 통한 비동기 호출을 통해 애플리케이션에서 웹 사이트를 실시간으로 스크랩하는 기능.
- LLM에서 중요한 결과물에 대한 금융 감성 분석. GPT-3.5의 프롬프트의 일부로, 응답이 금융적인 경우를 묻습니다. 이 경우, 세밀하게 미세 조정된 BERT 모델이 호출되어 응답을 분류하고, 확률 플롯과 적절히 귀여운, 저작권 위반이 아닌 버트 퍼펫이 표시됩니다.

# RAG 구성 요소

RAG를 이해하기 위해 단순화 된 비유를 사용해보겠습니다. 한 사람이 받을 수있는 두 가지 유형의 시험 문제가 있습니다. 첫 번째는 사실에 대한 간단한 요청입니다:

- 프랑스의 수도는 무엇인가요?
- 에베레스트 산을 처음으로 등반한 사람은 누구인가요?
- 캐나다가 영국으로부터 독립을 얻은 연도는 언제인가요?

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

이러한 질문에 답변하는 데 특별한 기술이 필요하지 않습니다. 적절한 참고 자료가 있는 사람은 올바른 답변을 찾아볼 수 있습니다.

![image](/assets/img/2024-05-23-RAGDetectiveRetrievalAugmentedGenerationwithwebsitedata_1.png)

다른 유형의 질문은 학습된 기술이 필요한 질문입니다. 해당 주제에 대한 교과서를 숨기고 있더라도 상관없는 질문이며 연습하고 공부하지 않은 경우 만족스럽게 대답할 수 없습니다. 예를 들어:

- 독일어로 시를 쓰세요.
- 첫 백만 개의 소수를 계산하는 컴퓨터 프로그램을 작성하세요.
- 베토벤 스타일의 심포니를 작곡하세요.

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

수학 교과서들로 둘러싸인 채로 있어도, 그 중에 반복해서 보던 책 안에 페르마의 마지막 정리가 있지 않다면 페르마의 마지막 정리를 증명할 수는 없을 거에요!

![이미지](/assets/img/2024-05-23-RAGDetectiveRetrievalAugmentedGenerationwithwebsitedata_2.png)

대형 언어 모델의 세계에서 RAG는 사기가 쉬운 첫 번째 유형의 문제를 해결하는 데 사용됩니다. 미세 조정은 모델이 실제로 학습해야만 문제를 해결할 수 있는 두 번째 유형의 문제를 해결하는 데 사용됩니다. RAG는 더 쉬워요—모델을 다시 교육시킬 필요가 없고, 모델의 내부 작업을 다룰 필요가 없으며, 모델이 "사기"를 할 데이터를 쉽게 조절할 수 있어요. 흥미로운 점은, 이 방법이 또한 모델이 "환각하는" 양을 크게 줄여준다는 거예요—때에 따라 충분한 훈련 데이터를 바탕으로 픽션 같지만 타당한 답변을 창조하는 대형 언어 모델의 일반적인 문제 중 하나에요. RAG의 유일한 어려운 부분은 모델에 주어질 적절한 데이터를 찾는 것이에요; 모델은 얼마나 많이 자극을 받을 수 있는지에 제한이 있고, 500페이지짜리 역사 교과서는 너무 길어요.

RAG가 작동하는 기본 개념은 아래와 같아요:

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

- 데이터 정리: 위의 만화 속 작은 사람처럼 교과서로 둘러싸여 있다고 상상해보세요. 우리는 이 책들을 각각 작은 조각으로 나누어—하나는 양자 물리학에 대한 것이고, 또 다른 하나는 우주 탐사에 관한 것일 수 있습니다. 이러한 각 조각 또는 문서는 벡터를 만들기 위해 처리됩니다. 이는 마치 도서관에서 그 정보 덩어리를 바로 가리키는 주소와 같은 것입니다.
- 벡터 생성: 이러한 조각들은 임베딩 모델을 통해 전달됩니다. 이는 정보의 의미를 포착하는 수백 개 또는 수천 개의 숫자로 이루어진 벡터 표현을 만드는 모델의 한 유형입니다. 이 모델은 각 조각에 고유한 벡터를 할당합니다—컴퓨터가 이해할 수 있는 고유한 인덱스를 만든 것과 같습니다.
- 질의: LLM에게 대답할 수 없는 질문을 하고 싶을 때, 먼젓거로 "AI 규제 분야의 최신 개발은 무엇인가요?"와 같은 프롬프트를 제공하여 시작합니다.
- 검색: 이 프롬프트는 임베딩 모델을 통해 통과되어 벡터로 변환됩니다. 이는 자신의 의미를 기반으로 고유한 검색어를 얻는 것이며, 그 키워드의 정확한 일치뿐만 아니라 의미에 기반한 검색어를 얻는 것입니다. 시스템은 이 검색어를 사용하여 질문과 관련된 가장 관련성 있는 조각을 찾기 위해 벡터 데이터베이스를 검색합니다.
- 컨텍스트 추가: 가장 관련성 있는 조각은 컨텍스트로 제공됩니다. 이는 질문하기 전에 참조 자료를 제공하는 것과 비슷한데, 우리는 LLM에게 조언을 제공합니다: "이 정보를 사용하여 다음 질문에 대답하십시오." LLM에게 제공되는 프롬프트는이 배경 정보로 확장되지만 사용자가 이를 볼 수는 없습니다. 이 복잡성은 뒷면에서 처리됩니다.
- 답변 생성: 마침내, 이 새로운 정보를 갖춘 LLM은 방금 검색한 데이터를 품은 응답을 생성하여 질문에 대답합니다. 이러한 방식으로 답변하는 것은 마치 이미 그 답을 알고 있던 것처럼 느껴집니다.

우리는 이 목표를 달성하기 위해 "Weaviate"라는 벡터 저장소 위에 애플리케이션을 구축했습니다. 우리는 파이썬에서 웹 스크래퍼를 구축하여 주어진 웹 사이트의 sitemap.xml을 크롤하도록하였습니다. 이는 검색 엔진이 사이트를 크롤할 수 있도록 사용되는 페이지 목록입니다. 인터넷 웹사이트의 끝없는 다양성 때문에 약간의 도전이 되었다는 것이 밝혀졌습니다.

# 스크래퍼 구성 요소

우리의 웹 스크래퍼는 현대 웹 아키텍처의 많은 도전을 처리하도록 구축되었으며, 전통적인 스크래핑 방법으로 종종 놓치는 데이터를 캡처하고, 스크래핑 활동을 실시간으로 웹 애플리케이션으로 스트리밍합니다. 이 엔드포인트는 웹 데이터를 가져 오고, 여러 단계의 저장 및 처리로 전환하고, 결과적으로 벡터 저장소에 즉각 색인화하기 위해 설계된 솔루션의 중요한 데이터 수집 단계를 나타냅니다.

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

스크레이퍼는 먼저 PythonBeautifulSoup 라이브러리를 사용하여 HTML 및 CSS 내용을 걸러냅니다. 그러나 일부 현대 웹사이트는 동적 콘텐츠 생성을 위해 JavaScript에 의존하므로, 순전히 HTTP 요청에 의존하는 표준 스크레이핑 방법에 장애물을 제공합니다. 스크레이핑 시스템은 Selenium WebDriver를 활용하여 이 문제를 해결합니다. 이 도구는 '헤드리스'(화면 없이 실행되는) 브라우저인 Google Chrome을 통해 웹 페이지와의 실제 사용자 상호작용을 모방하는 기능을 제공합니다. 이를 통해 동적 콘텐츠 로딩을 완전히 지원합니다. 스크레이퍼가 직접 HTTP 요청을 통해 데이터를 추출하는 초기 노력이 무효화되었거나 일정 임계값 이하의 데이터를 가져오는 경우 Selenium이 사용됩니다. 이 기술은 스크레이퍼가 정적 페이지 로드를 통해 사용할 수 없는 콘텐츠에 성공적으로 액세스할 수 있도록 보장합니다.

스크레이퍼는 불필요한 요소인 이미지와 같은 것들을 우회하여 오버헤드를 줄이고 빠른 처리를 가능케 합니다. 데이터를 수집한 후에는, 제어를 돌려 받아 데이터를 필터링하고 HTML에서 텍스트를 추출하는 BeautifulSoup로 돌아갑니다.

데이터 추출 후에, 데이터는 직렬화되어 Google Cloud Storage에 CSV 파일로 저장되어 데이터의 백업으로 기능합니다. 추출된 데이터는 이후 조감도 라이브러리인 LlamaIndex를 통해 Weaviate에 쪼개져서 삽입되며, 여기서 벡터 저장소 인덱스에 추가됩니다.

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

# 오케스트레이션

데이터가 수집된 후에 어떤 일이 발생하는지 좀 더 자세히 살펴봅시다. 이 데이터를 벡터 스토어에 삽입하기 위해 수행해야 할 작업이 있습니다. 이러한 단계들은 위에서 요약 수준으로 언급되었지만, 구체적으로는 다음과 같은 단계들이 필요합니다.

- 각 수집된 웹사이트를 가져와서 청크(chunk)로 분리합니다. 이 청크의 길이는 검색 프로세스가 얼마나 잘 작동하는지에 큰 영향을 미칩니다.
- 각 청크에 대해 OpenAI의 텍스트 임베딩-ada-002 모델을 사용하여 임베딩 벡터를 생성합니다. Weaviate와 LlamaIndex는 이 모델을 기본적으로 통합하고 있습니다.
- 이 임베딩 벡터와 텍스트 청크를 Weaviate 벡터 스토어에 삽입합니다.

모델에 프롬프트할 때 검색 단계:

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

- 텍스트 임베딩-ada-002을 통해 프롬프트를 가져와 임베딩을 얻습니다.
- 그 임베딩을 사용하여 프롬프트에 대답해야 할 청크를 찾은 후, 그 청크를 쿼리에 선행하고 GPT-3.5로 보냅니다.

문서의 청킹은 어느 정도 예술미가 있습니다. GPT-3.5는 최대 문맥 길이가 4,096토큰 또는 약 3,000단어입니다. 이는 모델이 처리할 수 있는 총 양을 나타냅니다 - 3,000단어 길이의 문맥을 갖는 프롬프트를 만들면, 모델은 응답을 생성할 충분한 공간이 없을 것입니다. 현실적으로, GPT-3.5에 약 2,000단어 이상으로 프롬프트를 지정해서는 안됩니다. 이는 데이터에 따라 청크 크기를 위한 트레이드 오프가 있다는 것을 의미합니다.

더 작은 chunk_size 값으로 설정할수록, 반환된 텍스트는 보다 상세한 텍스트 청크를 생성하지만, 텍스트에서 멀리 떨어져 있는 정보를 놓칠 위험이 있습니다. 반면, 더 큰 chunk_size 값은 상단 청크에 필요한 모든 정보를 포함할 가능성이 높아 더 나은 응답 품질을 보장하지만, 정보가 텍스트 전체에 분산되어 있는 경우 중요한 섹션을 놓칠 수 있습니다.

이 트레이드 오프가 어떻게 작동하는지 설명하기 위해 최근 테슬라 사이버트럭 출시 이벤트를 사용한 몇 가지 예를 살펴보겠습니다. 트럭의 일부 모델은 2024년에 출시될 예정입니다. 그러나 RWD만 탑재된 최저가 모델은 2025년까지 출시되지 않을 것입니다. RAG에 사용된 텍스트의 형식과 청킹에 따라 모델의 응답이 이 사실을 만나지 못할 수도 있습니다!

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

위의 이미지에서 파란색은 일치 항목이 발견되어 청크가 반환된 위치를 나타내며, 회색 상자는 청크가 검색되지 않았음을 나타냅니다. 또한 빨간색 텍스트는 관련 텍스트가 존재하지만 검색되지 않았음을 나타냅니다. 짧은 청크가 성공적으로 작동하는 예시를 살펴보겠습니다:

위의 이미지에서 왼쪽에 있는 텍스트는 RWD가 2025년에 출시될 것이라는 내용이 단락으로 구분되어 있지만 질의와 일치하는 관련 텍스트를 가지고 있습니다. 두 개의 짧은 청크를 검색하는 방법이 더 잘 작동하는 이유는 모든 정보를 포착하기 때문입니다. 오른쪽에서은 검색기가 단일 청크만 검색하기 때문에 추가 정보를 반환할 공간이 없어 모델이 잘못된 정보를 제공합니다.

그러나 항상 그런 것은 아닙니다. 때로는 질문에 대한 진정한 답변을 포함하는 텍스트와 강력한 일치하지 않을 때 더 긴 청크가 더 잘 작동합니다. 더 긴 청크가 성공하는 예시를 살펴보겠습니다:

몇 번의 실험 끝에, 우리는 1,000 토큰 길이의 청크를 사용하고 GPT-3.5를 촉발시키기 위해 두 개의 청크를 검색하기로 결정했습니다. GPT-3.5는 4,096 컨텍스트 길이를 처리할 수 있기 때문에 적절한 응답을 위한 충분한 공간을 남겨둘 것으로 예상됩니다.

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

프로젝트를 처음 시작할 때는 chunking, indexing, 그리고 데이터 검색을 직접 수행하여 매우 잘 작동되었습니다. Weaviate에서 쿼리 언어로 사용하는 GraphQL을 배우는 데는 학습 곡선이 있었습니다.

![이미지](/assets/img/2024-05-23-RAGDetectiveRetrievalAugmentedGenerationwithwebsitedata_4.png)

LlamaIndex와 같은 라이브러리를 사용하는 장점은 이러한 조율을 추상화하여 다른 벡터 스토어로 교체할 수 있는 기회를 제공한다는 것입니다 (Weaviate는 Milvus, Qdrant, Pinecone과 같은 많은 경쟁사가 있으며, 계속해서 새로운 경쟁사들이 등장하고 있습니다). LlamaIndex를 사용하면 향후 보다 복잡한 RAG 구현(예: 트리 구조 데이터 및 재귀적 프롬프팅)을 실험할 수도 있습니다. 그러나 이러한 새로운 라이브러리 사용은 적절한 문서의 부재와 같은 도전을 안겨주었습니다. 그들의 도움 자료 대부분은 예제로 이루어져 있었는데, 이 예제들이 우리의 사용 사례에 부합하지 않으면 Discord에서 개발자들에게 질문하거나 소스 코드를 직접 읽는 것 외에는 다른 선택지가 없었습니다.

# BERT 구성요소

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

모델 호스팅 요구 사항을 충족하기 위해, BERT 모델을 세밀 조정하고 훈련한 후 Google Vertex에서 파이프라인을 통해 호스팅했습니다. 이 모델을 응용 프로그램에 추가하면 실제로 GPT-3.5로 활용하여 답변과 함께 플래그를 반환하고 해당 답변이 금융적인 것으로 판단되는지 여부를 알 수 있습니다. 그럴 때마다, 우리는 적절히 유머러스하면서도 저작권을 침해하지 않는 버트 퍼펫과 모델이 반환한 확률 플롯을 표시할 수 있습니다.

BERT 모델의 세밀 조정 과정은 데이터뿐만 아니라 기술적인 구성에 관한 것이었습니다. 이 모델에게 텍스트 내에서 금융적 감정을 구별하는 기술을 가르치는 것이 목표였는데, 이는 복잡한 금융 뉴스에서 통찰력을 필요로 하는 사람들에게 유용한 기술입니다. 이러한 기사에서의 감정은 상냥하고 즉각적으로 드러나지 않을 수 있기 때문에, 특별히 세밀하게 조정된 모델을 활용하여 비전문가에게 밑바탕에 깔린 감정의 분명한 지표를 제공하는 것이 중요합니다.

우리가 BERT 모델을 훈련시킬 때는 금융 문구은행 데이터셋을 사용했습니다. 이 데이터셋은 금융에 능통한 사람들이 감정으로 레이블이 지정된 문장들로 구성되어 있습니다. 그러나 이러한 데이터셋에서 흥미로운 문제가 발생합니다: 어노테이터 간의 합의 수준의 변동에 따른 차이점은 모델의 학습과 그에 따른 예측에 암묵적으로 영향을 줄 수 있습니다.

이러한 데이터셋을 사용하여 BERT 모델을 세밀하게 조정했을 때 (어노테이터 간 100%, 75%, 66%, 50% 합의를 대표하는 데이터셋 포함), 어노테이터들이 동의하는 정도가 더 높을수록 훈련된 모델이 더 좋아 보였습니다.

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

수상한 거 아니에요? 모델이 더 높은 합의를 보일수록 실제로 더 나은 결과를 내놓는다는 것이 아니라, 높은 합의 수준은 금융 보고서가 간단히 분류하기 쉬운 것을 의미한다는 것입니다.

높은 합의가 모델을 명확한 감정만 인식하도록 편향시킬 수 있고, 때로는 복잡한 금융 텍스트에서 현실인 미묘한 감정을 놓쳐버릴 수도 있기 때문에 상상하기 어렵지 않습니다.

이 문제를 해결하기 위해, 우리는 데이터의 편향을 해소하기로 결심했습니다. 우리는 모든 감정의 명료성 수준이 공정하게 대표되도록 훈련 및 테스트 분할을 구성하는 것을 목표로 삼았습니다. 이를 위해 데이터 분할 과정을 주의 깊게 프로그래밍하고, 무작위 섞기, 계층 샘플링, 그리고 균형 잡힌 분할을 위한 검증 및 테스트 세트 생성과 같은 추가 단계를 거쳤습니다. 이렇게 함으로써, 우리는 모델이 단순히 분명한 감정 데이터에서만 잘 작동하는 위험을 완화하고, 대신 금융 텍스트의 현실적인 혼합을 처리할 수 있도록 보장했습니다.

편향 해소 후, 보다 균형있는 접근 방식을 사용하여 모델을 평가하자, 흥미로운 추세가 나타났습니다. 75% 주석 작성자 합의로 교육된 모델이 가장 높은 F1 점수를 보였는데, 초기 결과에서 벗어나는 것으로 나타났습니다. 우리가 의심했던 대로, 최상의 데이터셋은 실제로 완전한 주석자 합의와 논란을 유발하는 좀 더 복잡한 금융 보고서 사이에서의 타협이었던 것 같습니다.

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

# 모든 것을 결합하기

애플리케이션 아키텍처는 FastAPI 서비스와 Nginx를 통해 연결된 프론트엔드 컨테이너로 구성됩니다. FastAPI 서비스는 특정 작업에 대한 스트리밍 응답을 처리하는 여러 엔드포인트를 호스팅하며, 스트리밍 응답을 사용하여 쿼리를 관리하고 웹 사이트 주소를 나열하고 특정 웹 사이트의 타임스탬프를 검색하고 주어진 쿼리에 대한 URL 및 금융 플래그를 가져오며, Vertex AI의 Prediction API를 활용하여 감성 분석에 사용하고 효과적인 사이트맵 스크래핑을 위해 입력을 처리합니다.

프론트엔드는 HTML과 JavaScript로 구축되어 동기식 및 비동기식 함수를 사용하는 단일 페이지 애플리케이션으로 만들어졌습니다. CSS로 스타일이 지정되어 있으며, 로딩 인디케이터 및 슬라이딩 패널용 여러 가지 효과를 사용하고, Google의 Material Design 라이브러리를 사용하여 현대적인 텍스트 입력란과 버튼을 만듭니다.

우리는 OpenAI의 DALL-E 3를 사용하여 30개의 서로 다른 저작권 침해가 없는 Bert 이미지를 생성했습니다. 이 중에는 긍정적, 부정적, 중립적 감정 각각 10개씩이 포함되어 있습니다. 프론트엔드는 발견된 감정에 따라 해당 감정 이미지 중 하나를 무작위로 선택하여 표시하고 클래스와 함께 표시합니다.

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

진행 및 각 단계 완료 상황의 실시간 업데이트가 사이트맵 스크래핑 프로세스 중에 클라이언트에게 제공됩니다. Nginx는 게이트웨이로 작동하여 적절한 엔드포인트로 요청을 효율적으로 라우팅하여 일관된 반응성 있는 사용자 경험을 보장합니다.

# 배포

RAG Detective 앱은 Ansible을 사용하여 자동화 및 재현성에 따라 배포됩니다. 배포 프로세스에는 필요한 Google Cloud Platform (GCP) API 활성화, GCP 서비스 계정 설정, 필요한 소프트웨어가 포함된 Docker 컨테이너 생성이 포함됩니다. Ansible playbook은 Docker 컨테이너를 빌드하고 GCR에 푸시하여 Google Cloud Registry (GCR)에 컨테이너를 만들고 푸시하며, GCP에서 컴퓨트 인스턴스 (VM) 서버를 생성하고 해당 구성으로 인스턴스를 구성하고 인스턴스 내에서 Docker 컨테이너를 설정하는 데 사용됩니다. 이 프로세스는 컴퓨트 인스턴스에 웹 서버로 Nginx를 구성함으로써 Docker 컨테이너에 대한 올바른 설정 및 액세스를 보장합니다.

스케일링에는 Kubernetes를 사용합니다. Ansible playbook은 Kubernetes 클러스터를 생성하고 배포합니다. 이 단계에는 GCR에 Docker 컨테이너 빌드 및 푸시, Kubernetes 클러스터 생성이 포함됩니다. 증가된 수요 발생 시 스케일링 프로세스에는 GKE가 클러스터에 더 많은 노드를 자동으로 추가하는 노드 스케일링 및 쿠버네티스 수평 팟 자동스케일러가 관여하며 자원 활용률에 따라 팟 복제 수를 수정합니다. 로드 밸런싱은 Kubernetes Services 및 GKE가 Google Cloud Load Balancer와 통합되어 들어오는 트래픽을 고르게 분배하여 달성됩니다.

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

원하는 것 중 하나는 Weaviate 인스턴스가 다른 컨테이너와 함께 확장되지 않으며, 데이터베이스 확장 구현은 이 코스의 범위를 벗어나고 별도의 과학이 필요하다는 점입니다. Weaviate를 확장하기 위해 수평 확장이 주요 방법으로, 클러스터에 더 많은 노드를 추가하여 작업 부하를 균등하게 분산하는 것이 중요합니다. 이에 더하여 부하 분산을 위해 로드 밸런싱이 있으며, 이는 들어오는 요청을 이용 가능한 노드에 골고루 분배하는 데 도움이 됩니다. 데이터 샤딩도 가능하며, 데이터가 여러 노드에 파티션으로 분할되어 쿼리를 효율적으로 처리할 수 있습니다. 쿠버네티스를 사용하여 Weaviate 인스턴스의 확장 및 관리를 자동화할 수도 있어 시스템이 성장하여 증가하는 요구 사항을 충족하는 동시에 견고하고 효율적으로 유지될 수 있도록 할 수 있습니다.

# 배운 점

시스템 및 운영에 대해 몇 가지 중요한 교훈을 얻었습니다:

- 인증은 큰 어려움 요소가 될 수 있습니다. 처음으로 무언가를 배포할 때마다 Google Cloud, Google Vertex 또는 OpenAI가 제대로 인증되지 않았거나 잘못된 권한을 가지고 있는 문제가 발생했습니다. 인증 및 시크릿 관리에 대한 숙달은 AI 제품의 운영에 숙련될 수 있는 주요 구성 요소입니다. (dotenv와 같은 라이브러리 사용이 유용하다는 것을 발견했습니다.)
- GPT 모델은 선구자적입니다. LLM 답변에 대해 자신의 BERT 모델을 호출할지 결정하기 위해, 답변이 금융 관련인지 나타내는 플래그를 반환하도록 요청했습니다. 초기에는 플래그를 생성한 다음 답변을 생성하도록 했지만, GPT 모델은 텍스트를 생성하는 과정 중이어도 텍스트를 생성하기 전에는 분류할 수 없다는 사실을 깨달았습니다. 모델의 스트리밍 응답 끝에 플래그를 이동하는 것이 다소 까다로웠지만 더 잘 작동합니다. 한 가지 알아둬야 할 점은 GPT-3.5가 GPT-4보다 추론에서 훨씬 떨어지며, GPT-4가 하지 않는 오류를 자주 범한다는 것입니다.
- 새로운 라이브러리를 사용할 때는 자신의 책임 하에 사용해야 합니다. Weaviate만 사용하여 작동하는 프로토 타입이 있음에도 불구하고, 보다 고급 RAG 아키텍처를 사용하기 위해 LlamaIndex를 사용했습니다. 적절한 라이브러리 참조 부재로 문서는 예제 코드로 가득하지만, 소스 코드를 자세히 살펴보아야만 할 일이 여러 차례 발생했습니다. 이 프로젝트를 계속해서 복잡한 RAG 방법을 추가해 나갈 수 있으며, 이는 LlamaIndex의 사용을 정당화할 뿐만 아니라 쿼리에 더 나은 응답을 제공할 것입니다.
- 새로운 라이브러리를 사용하려면 Discord 챗을 확인해야 합니다. 보통 라이브러리 저자들은 문서가 떨어지거나 제대로 되어 있지 않을 때 Discord에서 활동적이며, 고민할 때 직접 안내를 제공해 줄 수 있습니다.
- 데이터셋은 평범하게 숨겨진 편향을 담고 있을 수 있습니다. 우리는 financial_phrasebank 데이터셋을 사용했는데, 앞서 말한 바와 같이, 보다 큰 주석자 합의는 더 좋은 모델로 이어진 이유가 숨겨져 있었습니다. 데이터를 통해 보이는 듯한 단순한 경로를 선택할 때 항상 주의해야 합니다!

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

아래는 해당 프로젝트에 대한 이미지입니다.

이 프로젝트는 매우 현대적인 문제에 대한 매우 현대적인 솔루션을 활용한 재미있는 프로젝트였습니다. 클라우드에서 모델을 훈련 및 배포하거나 자체 RAG 아키텍처를 만드는 경험은 수업 시간을 보내는 훌륭한 방법이었습니다.
