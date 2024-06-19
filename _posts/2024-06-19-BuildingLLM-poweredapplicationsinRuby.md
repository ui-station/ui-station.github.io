---
title: "루비로 LLM-파워 어플리케이션 만들기"
description: ""
coverImage: "/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_0.png"
date: 2024-06-19 10:19
ogImage: 
  url: /assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_0.png
tag: Tech
originalTitle: "Building LLM-powered applications in Ruby"
link: "https://medium.com/@rushing_andrei/building-llm-powered-applications-in-ruby-6e16d8a17548"
---


<img src="/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_0.png" />

우선, 2024년 4월 14일에 Wroclove.rb에서 이러한 아이디어들을 발표했어요. 그 영상이 올라오면 링크를 포함하겠습니다. 그동안 제 친구 Stephen Margheim은 동반 블로그 글을 쓰도록 권유했어요.

<img src="/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_1.png" />

생성 모델 인공지능은 텍스트, 이미지, 오디오, 비디오 등 다양한 유형의 콘텐츠를 생성할 수 있는 인공지능 기술입니다. 이 블로그 글의 범위 내에서는 주로 텍스트 생성에 초점을 맞출 거에요.

<div class="content-ad"></div>

대규모 언어 모델(LLMs)은 일반적인 언어 이해 및 생성 기능을 갖춘 딥 러닝 인공 신경망(모델)으로, Transformers 아키텍처를 소개한 2017년 Attention Is All You Need 연구 논문 이후 인기를 얻으며 대폭 성장했습니다.

![이미지](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_2.png)

생성형 AI와 LLM은 인공 지능에 대한 접근을 민주화했습니다. 프로덕션 수준 시스템의 배포 및 최적화 기간은 6~7개월에서 겨우 일주일로 단축되었습니다.

![이미지](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_3.png)

<div class="content-ad"></div>

대형 언어 모델은 다음과 같은 작업에서 우수한 성과를 보입니다:

- 엔지니어들이 애플리케이션에서 의지할 수 있는 JSON과 같은 구조화된 데이터로 텍스트 변환
- 대량의 텍스트를 맥락화하고 요약 생성
- 대량의 텍스트를 주제에 따라 분류
- 언어 번역
- 다양한 형태와 크기의 콘텐츠 생성
- 질문에 대답

![이미지](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_4.png)

젠 AI 모델이 미래에는 데이터베이스, 캐시, 암호화, 큐, 람다 함수 등과 마찬가지로 각기 기술 스택의 필수 구성 요소가 될 것을 제안하고 싶습니다.

<div class="content-ad"></div>

그리고 그것이 의미하는 것은 팀이 GitHub Copilot이나 ChatGPT를 사용하여 코드를 생성할 것이라는 것만이 아닙니다. AI는 코드 자체에서 지능적인 컴퓨팅 엔진이 될 것입니다.

![이미지](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_5.png)

투자 관리 회사 Coatue는 AI를 중요한 새로운 인프라 응용 프로그램 개발자들이 상위에 놓을 핵심 부분으로 내세웁니다.

![이미지](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_6.png)

<div class="content-ad"></div>

여기 실제 적용 예시가 있습니다: 의사 결정 트리 순회입니다.

이전에는 모든 다양한 입력에서 출력까지의 조합을 나열해야 했지만, 이제 AI는 주어진 입력에 가장 적합한 옵션(출력 또는 호출할 함수)을 선택할 수 있게 되었습니다(이전에 본 적이 없는 입력도 포함).

![이미지](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_7.png)

루비 온 레일스 프레임워크가 그토록 사랑받는 이유는 개발자가 비즈니스 로직 작성에 집중할 수 있게 해주고 공학 문제 해결을 공학 문제 해결만을 위한 것으로 다루지 않기 때문입니다.

<div class="content-ad"></div>

레일즈 애플리케이션에서 대다수의 비즈니스 로직은 모델 및 서비스 객체에 있습니다. AI를 사용하면 일부 비즈니스 로직이 프롬프트에도 함께 존재하게 될 것입니다.

![이미지](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_8.png)

예를 들어, 전자 상거래 스토어에서 주문 완료 절차, 반품 처리, 시즌 할인, 고객 충성도 프로그램 등에 대한 비즈니스 로직을 프롬프트에 작성할 수 있게 될 것입니다.

AI는 비즈니스 로직을 조정하고 비즈니스가 구성된 서비스를 활용할 것입니다. 위의 슬라이드에서 AI는 왼쪽의 프롬프트를 활용하여 결제 게이트웨이, 재고 관리 시스템, 배송 서비스 및 기타 서비스를 연결합니다.

<div class="content-ad"></div>

사실 이 실험을 진행하고 결과를 여기에 문서화했습니다: [https://github.com/patterns-ai-core/ecommerce-ai-assistant-demo](https://github.com/patterns-ai-core/ecommerce-ai-assistant-demo).

![이미지](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_9.png)

이것은 AI 에이전트의 개념으로 이어지는데, 이는 자율 주행형 LLM 프로그램입니다.

![이미지](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_10.png)

<div class="content-ad"></div>

AI 에이전트는 비교적 신뢰할 수 없다고 알려져 있어요. AI 에이전트가 맡는 작업이 많을수록 더 믿을 수 없는 경향이 있어요. AI 에이전트가 작동하는 결정 트리의 가지와 잎이 적을수록 더 신뢰할 수 있어요.

![이미지](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_11.png)

저의 주요 관심사와 작업은 오늘날 비즈니스 프로세스와 워크플로우 자동화를 위해 구축 중인 신뢰할 수 있는 AI 에이전트들이에요.

![이미지](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_12.png)

<div class="content-ad"></div>

현재 이러한 시스템을 프로덕션 환경에 배포하는 데 망설이고 계신다면 — 외롭지 않습니다! Gen AI의 기업 채택이 예상한 것보다 훨씬 더 느리게 진행되고 있습니다.

그 이유는 다음과 같습니다:

- 현재 빠르게 변화하는 분야에 대한 투자는 계속해서 움직이는 목표를 추구하게 될 수 있습니다.
- AI로 생성된 작품의 지적재산권 및 저작권 소유권에 대한 모호함.
- EU 및 US의 새롭고 떠오르는 규제 환경에 대한 법적 준수에 대한 불확실성.
- 부족한 툴링
- AI 시스템의 블랙박스와 유사한 행동이 추가적인 리스크를 가져옵니다.

![이미지](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_13.png)

<div class="content-ad"></div>

LLM은 환각 및 오래된 데이터(모델은 특정 시점의 데이터로 훈련되었습니다)와 같은 몇 가지 결함을 보여줍니다. 또한 특정 사용 사례에 대한 관련/동일 정보를 활용하지 않는다는 점도 그중 하나입니다.

![이미지](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_14.png)

이러한 결함 중 일부는 "Prompt Engineering"을 통해 완화할 수 있지만, 특정 프롬프트가 더 나은(또는 나쁜) 결과를 가져오는 이유를 설명하지 못해 "프롬프트 연금술"이라고 부르고 있습니다.

"훈련 데이터에 있다!"라고 말합니다.

<div class="content-ad"></div>

세부 사항을 조금 바꿔보는 것, 예를 들어 "여기서 한 걸음 씩 생각해 봅시다"나 "깊게 숨을 들이마시고 이 문제를 해결해 봅시다"와 같이 친근한 어조로 지시사항을 바꾸면, 큰 언어 모델이 최적화된 결과를 제되는 것으로 밝혀졌어요.

![이미지 1](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_15.png)

모델은 다양한 기법을 사용해 의도하지 않은 작업이나 발언을 하도록 강제할 수 있기도 해요. 예를 들면, 앤소픽에서 보고한 Many-shot 탈옥 기술과 같은 기법이 있어요.

![이미지 2](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_16.png)

<div class="content-ad"></div>

외부 사용자의 자유 형식 입력을 모델에 프롬프트로 전달하는 것은 무한한 문제의 가능성을 열어둘 수 있습니다. 이러한 취약점의 99번째 백분위를 어떻게 다루어야 할지 배우기 전까지는 미래에는 AI 에이전트가 시스템 간 상호작용을 담당하고 고객과 시스템 간 상호작용을 담당하지 않게 될 것입니다.

![Image](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_17.png)

RAG(검색증대생성)가 등장했습니다. 이 기법은 외부 소스에서 가져온 사실로 LLM 모델의 정확성과 신뢰성을 높이는 기술입니다.

현재 일반적으로 사용되는 사례는 AI 챗봇을 구축하여 독점적인 기업 데이터 위에 질문에 답할 수 있는 기능을 가지게 하는 것입니다. 물론, 상자 밖의 AI는 귀하의 조직의 독점적 데이터에 대해 훈련받지 않았습니다.

<div class="content-ad"></div>

아래는 Markdown 형식입니다.

```markdown
![이미지](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_18.png)

다음은 RAG 컴포넌트 각각에 대해 자세히 살펴볼 것입니다:
- 벡터 임베딩
- 유사도 검색
- RAG 프롬프트

![이미지](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_19.png)
```

<div class="content-ad"></div>

아래는 Markdown 형식입니다.

```markdown
![BuildingLLM-poweredapplicationsinRuby_20](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_20.png)

벡터 임베딩은 데이터의 의미론적인 의미를 벡터 공간에서 나타냅니다. 이를 "잠재 공간"이라고도 합니다.

![BuildingLLM-poweredapplicationsinRuby_21](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_21.png)

LLM에 전달할 관련 컨텍스트를 찾기 위해 의미론적으로 유사한 문서들을 찾습니다. 보통 벡터 검색 데이터베이스에 저장된 벡터 임베딩 중에서 전달된 쿼리와의 거리를 계산하는 과정이 유사성 검색입니다.
```

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_22.png" />

순진한 RAG 구현의 마지막 단계는 컨텍스트와 원래 질문을 연결하여 RAG 프롬프트를 구성하는 것입니다. 선택적으로— 특정 행동이나 응답 스타일을 조절하기 위해 모델 지침을 지정할 수도 있습니다.

<img src="/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_23.png" />

모두 함께 넣어 보면— 이것이 순진한 RAG 구현을 위해 얻게 되는 스키마입니다. 고급 RAG 전략은 현재 활발히 연구되고 있는 핫한 주제입니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_24.png" />

이러한 고급 RAG 전략 중 하나의 예는 2 벡터 검색 데이터베이스 인덱스를 활용하는 것입니다: 하나는 소스 문서 요약의 임베딩을 저장하고, 다른 하나는 문서 청크 임베딩을 저장합니다.

이 경우에는 다음과 같이 2개의 유사성 검색 단계를 실행합니다:

- 관련 요약 찾기
- 스텝 #1에서 찾은 문서 요약과 연결된 관련 문서 청크 찾기

<div class="content-ad"></div>

![image](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_25.png)

루비 소프트웨어 엔지니어가 LLM을 활용하여 애플리케이션을 구축하는 데 도움이 되는 루비 라이브러리를 작업해 왔고, 이 발표에서 논의된 많은 아이디어를 구현했습니다.

![image](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_26.png)

저희가 취하는 방식은 최고의 루비 온 레일즈 패션 — 배터리 포함 및 모범 사례를 포함한 방식입니다.

<div class="content-ad"></div>

![BuildingLLM-poweredapplicationsinRuby_27](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_27.png)

내가 우리가 가장 인기 있는 벡터 검색 데이터베이스 중 하나와 함께 어떤 LLM 플레이버를 선택하고 사용할 수 있도록 유연성을 제공한다.

![BuildingLLM-poweredapplicationsinRuby_28](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_28.png)

루비는 인공 지능/기계 학습 또는 어떤 종류의 데이터 과학 작업에 관련이 있을 때 특히 역설적인 기술로 선택되어 왔다.

<div class="content-ad"></div>

AI 시대에는 특정 언어 선택보다는 전에는 그랬던 것보다 훨씬 덜 중요한 것 같아요. 저는 Ruby를 사용한 이유는 Ruby로 아이디어를 가장 효과적으로 전달할 수 있기 때문이에요.

곧 AI가 대규모 코드베이스를 몇 시간이나 몇 분 안에 새롭고 트렌디한 프로그래밍 언어로 재작성할 수 있도록 해줄 거예요. AI가 인간 언어 번역을 다루는 방식으로 코드를 번역하면 되죠.

![이미지](/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_29.png)

인기 있는 오픈소스 프로젝트를 운영하면서 경험한 것을 통해, 다른 오픈소스 소프트웨어 유지보수자들에게 깊은 존경과 감사의 마음을 갖게 되었어요. 그들은 대부분 매우 친절하고 수면 부족한 사람들로, 낯선 사람들을 도와주기를 즐기면서 아무것도 기대하지 않고 도와줘요.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-BuildingLLM-poweredapplicationsinRuby_30.png" />

저희 프로젝트에 기여해준 모든 훌륭한 분들께 깊은 감사의 말씀을 전합니다.

Wroclove.rb 주최자분들께 감사의 인사를 전합니다. 지난 18개월 동안 AI 여정에서 만난 모든 분들께 큰 찬사를 보냅니다! 우리 프로젝트를 확인하고, Ruby AI 및 Langchain.rb 디스코드 서버에 참여해 주세요.

## 참고문헌

<div class="content-ad"></div>

- Advanced RAG 기술: 그림으로 설명한 개요
- LLM에서는 이제 임베딩 작업에 대한 도구를 제공합니다
- 많은 샷으로 감옥 탈출
- Optimizer로서 대형 언어 모델
- RAGAS 프레임워크
- Andrew Ng: AI 분야의 기회 - 2023
- Adams가 홍보한 NYC AI 챗봇이 기업들에게 법을 어기라고 조언
- 챗봇의 환청에 대해 Air Canada가 책임을 져
- GM 딜러 챗봇이 2024 Chevy Tahoe를 1달러에 판매하는 것에 동의