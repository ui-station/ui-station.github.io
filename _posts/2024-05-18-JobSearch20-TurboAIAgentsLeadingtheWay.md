---
title: "구직 20-터보 AI 에이전트가 선두를 달리다"
description: ""
coverImage: "/assets/img/2024-05-18-JobSearch20-TurboAIAgentsLeadingtheWay_0.png"
date: 2024-05-18 19:59
ogImage: 
  url: /assets/img/2024-05-18-JobSearch20-TurboAIAgentsLeadingtheWay_0.png
tag: Tech
originalTitle: "Job Search 2.0-Turbo: AI Agents Leading the Way"
link: "https://medium.com/towards-data-science/job-search-2-0-turbo-579e1bdb5177"
---


```
![이미지](/assets/img/2024-05-18-JobSearch20-TurboAIAgentsLeadingtheWay_0.png)

# 목차

## 소개

- 모든 구직자에 영향을 미치는 일반적인 도전 과제 (왜..)
- 취업 과정 최적화를 위한 AI 에이전트의 중요한 역할 (무엇..)
- 과정에 AI 에이전트 기능 소개 (어떻게..)
```

<div class="content-ad"></div>

## 직접 해보기: AI 기반 취업 검색 엔진 구현

- 프로젝트 구조
- 프레임워크
- 도구
- 데이터
- 작업
- 에이전트
- LLM
- 출력 모델
- 모든 것을 함께 모아보기: 크루
- 결과 분석

소스 코드
요약
잠재적 개선 사항
참고 자료

취업 검색은 어렵고 시간이 많이 소요될 수 있습니다. 취업을 위해 구인 공고를 탐색하는 데는 몇 주에서 몇 달이 걸릴 수 있습니다.

<div class="content-ad"></div>

![Job Search](/assets/img/2024-05-18-JobSearch20-TurboAIAgentsLeadingtheWay_1.png)

시간 소요량은 전문성, 수요 역할, 시장 등 여러 요소에 따라 다를 수 있지만, 미국 노동 통계국(US Bureau of Labor Statistics)의 실업률 데이터(수동적으로 정찰 중인 사람들 포함)에 따르면 2024년 3월의 중앙값 기간은 21.6주 ~ 5개월이었습니다.

인간 연령 규모에서, 평균 구직자가 새로운 직업을 찾는 데 소요되는 시간을 고려하면, 수백 건의 지원서를 검토하는 데 소비되는 훌륭한 시간이네요.

취업을 위한 성공 요소 중 하나는 제출된 지원서의 수입니다. 한 연구 결과에 따르면, 적절한 직책을 찾기 위해 필요한 평균 지원서 수는 100~200건 이상이며, 시장, 경제 상황 및 지원자의 전문성에 따라 이상치가 발생할 수 있습니다. 취업을 위해 지원서를 작성하고 노력하는 데 드는 양이 많기 때문에, 효율적이고 확장 가능한 해결책을 개발하여 수천 시간을 절약해 주는 동기가 있습니다.

<div class="content-ad"></div>

# 모든 구직자가 직면하는 공통적인 도전과제 (왜 그럴까요..)

![JobSearch Image](/assets/img/2024-05-18-JobSearch20-TurboAIAgentsLeadingtheWay_2.png)

시장이 구인 공고로 넘쳐나는 상황에서 자신의 기술과 요구 사항에 가장 적합한 역할을 찾는 것은 어려운 일입니다. 이러한 상황에 처해 본 적이 있다면, 수백 개의 구인 공고를 살펴보고 기술, 급여 수준, 선결 조건 등을 맞추려고 노력하는 과정이 얼마나 스트레스 받고 힘들 수 있는지 알 것입니다. 프로세스가 늦어질수록 동기 부여가 낮아지고 구직을 포기하고 덜 나은 선택을 하는 위험이 높아집니다.

적절한 역할을 찾는 데 걸리는 시간은 개인의 경험, 지원 시기, 그리고 구직 시장에서 기술이 얼마나 요구되는지에 따라 다릅니다. 그리고 적용하는 산업 및 기업에 영향을 미치는 등의 제어할 수 없는 요소들이 있습니다.

<div class="content-ad"></div>

이 기사에서는 AI 기반의 취업 검색 엔진을 구축하는 과정에서 다루는 주요 과제들은 주로 취업 검색 및 매칭 과정을 최적화하는 데 초점을 맞추고 있습니다. 따라서 아래와 같은 주요 과제를 해결하고 있습니다:

중요한 과제들

- 취업 광고 검색: 전통적으로, 취업을 위한 스크리닝 작업은 작업이 광고되는 올바른 소스 플랫폼을 선택하는 것으로 시작됩니다. 특정 기준으로 작업을 필터링하고 모든 콘텐츠를 소화하여 최종 결과물을 평가합니다. 더 많은 사람들에게 도달하기 위해 많은 사람이 한 가지 이상의 플랫폼에서 작업을 한다고 합니다. 여러 소스 사이를 오가며 어디에 무엇이 있는지 추적해야 하는 문제가 발생하기 시작합니다.
- 취업 광고 평가: 구인 광고가 내 이전 경험과 기술 세트에 부합하는가? 필요한 경력은 있는가? 올바른 위치에 있는가? 어떤 언어를 구사해야 하는가? 급여 범위가 내 요구 사항을 만족하는가? 역할 시작일은 언제인가? 요구 사항에 맞는 구인 광고를 평가할 때 발생하는 몇 가지 질문들입니다.
- 조직 평가: 취업 스크리닝 중 일반적인 단계는 조직에 대한 연구입니다. 조직을 평가하는 데 일반적인 기준은 직원 리뷰, 시장 성과, 평판 등입니다. 조직이 운영하는 시장에 따라 더 많은 기준이나 평가 기준이 소개될 수 있습니다. 교차 시장 기회를 찾는 경우 이 단계는 고려해야 할 모든 요소를 고려할 때 매우 많은 시간이 소요될 수 있습니다.
- 취업자 추리: 수십 개에서 수백 개의 구인 광고를 스크리닝한 후 결정을 내리기 전에 최종적인 결정에 도달하기 위해 통과한 몇 가지 작업을 추립니다. 취하는 방식에 관계없이, 리스트 정렬을 위한 어떤 기준을 기반으로 평가 전략을 취할 것입니다. 이는 취향과 우선순위에 따라 주관적인 평가이며, 이 작업을 완료하기 위해 필요한 구인 광고 사이의 혼합 및 일치하는 양을 상상해 볼 수 있습니다.

열거된 과제들은 반복적인 작업을 포함하며, 강력한 추론 능력과 명확한 실행 가능한 목록에 도달하기 위해 소화해야 할 대량의 콘텐츠를 요구합니다.

<div class="content-ad"></div>

이러한 작업은 LLM 파워에 기반을 둔 에이전트를 채용하는 주요 특성을 완벽하게 충족시킵니다.

# AI 에이전트가 취업 프로세스를 최적화하는 데 중요한 역할 (무엇..)

![이미지](/assets/img/2024-05-18-JobSearch20-TurboAIAgentsLeadingtheWay_3.png)

수백 명의 구직자가 구인 광고를 검토하고 광고 회사와 그들의 채용 공고를 심층적으로 평가하며, 마지막으로 귀하의 요구에 가장 적합한 권장 사항을 맞춤 제작하는 것의 최종 결과를 상상해보세요. 온라인 구인 광고를 검토하는 데 소요되는 시간을 크게 줄이는 자동화 및 추론에 기초한 완전한 엔드 투 엔드 프로세스입니다.

<div class="content-ad"></div>

AI 에이전트의 능력을 분석해 보겠습니다. 일자리 검색과 추천을 자동화하고 개인화하는 데에서의 능력에 초점을 맞춥니다. 다음 섹션에서 엔진을 구축하는 것에 대해 더 자세히 살펴볼 것입니다. 현재는 AI 에이전트가 갖고 있는 장점에 초점을 맞춥니다.

1. 에이전트는 도구를 사용할 수 있습니다.

에이전트는 자신의 작업을 수행하는 데 정의된 도구를 활용할 수 있습니다. 이는 인터넷 검색 및 사전 정의된 API를 사용하여 데이터를 요청하는 것과 같은 더 나은 연구 능력을 제공하는 도구를 활용하는 것을 포함합니다. 이는 에이전트가 훈련된 데이터를 넘어서 다양한 도구를 활용하여 사용자가 선택한 툴킷을 사용하여 에이전트가 특정 작업을 어떻게 해결할지를 지시할 수 있는 능력을 제공합니다.

2. 에이전트는 상당량의 정보를 소화할 수 있습니다.

<div class="content-ad"></div>

AI 모델의 콘텍스트 창 크기가 점점 커지면서(1밀리초 이상), 때로는 무한한 콘텍스트 창에 이를 정도로, 에이전트들이 소화하고 논할 수 있는 정보량은 계속해서 증가하고 있습니다. 제대로 분배된다면, 에이전트들은 무한한 양의 정보를 분석하고 사용자에게 최종 요약된 버전을 제공할 수 있습니다.

3. 에이전트가 논리를 할 수 있습니다

AI 에이전트의 추론 능력에는 제한이 있을 수 있지만, 점점 개선되고 최적화된 새로운 모델들이 점점 더 자주 공개되면서 이 갭이 좁혀지고 있습니다. 구인 광고를 분석하고 사용자 쿼리를 일부 매개 변수에 기반해 비교하는 작업을 고려할 때, 이 작업은 매우 훈련된 AI 에이전트에게는 직관적인 분석 작업으로 간주될 수 있습니다.

4. 에이전트가 결과를 요약하고 구조화할 수 있습니다

<div class="content-ad"></div>

대량의 정보를 처리할 수 있는 도구는 가치가 있지만, 그 정보를 효율적이고 구조화된 방식으로 전달하지 못하는 경우 가치가 상실됩니다. AI 에이전트는 환각에 취약할 수 있으며, 이는 정보를 요약하고 보고하는 방식에 영향을 미칠 수 있습니다. 잘 설계된 프롬프트 및 추가적인 유효성 검사 단계를 제공하여 에이전트가 올바르고 신뢰할 수 있는 결과로 작업을 수행하도록 보장합니다.

5. 에이전트는 협업할 수 있습니다.

에이전트가 작업할 때 배경, 역할 및 과제의 차이는 다양성의 층을 제공하며, 이는 다양한 관점에서 문제 해결에 접근하는 데 도움이 됩니다. 에이전트 간의 협력은 사용자에게 전달되는 최종 결과를 최적화하는 데 이 능력을 활용합니다.

6. 에이전트는 확장 가능합니다.

<div class="content-ad"></div>

대규모 요소들을 병렬 실행 작업으로 동적 할당하는 것은 매우 강력합니다. 이 방식은 문제를 작은 조각으로 나누고 별도의 요소들이 작업에 참여할 수 있는 경우 빛을 발합니다. 다수의 요소들이 일괄 작업에 할당될 수 있으며, 각 요소는 과정의 한 단계를 처리하고 결과를 다음 단계에 넘기는 역할을 맡습니다. 마지막으로, 요소들은 결과를 모아 최종 요약 및 구조화를 수행할 수 있습니다. 이러한 방식은 다중 채널이나 원본을 통해 구인 광고를 빠르고 확장 가능하게 처리할 수 있는 방법 중 하나입니다.

우리의 사용 사례에 대해 모든 이러한 기능을 어떻게 결합할 수 있을까요?

# 프로세스에 AI 요소 기능 소개 (어떻게..)

모든 AI 기능을 활용하는 솔루션을 보장하기 위해, 프로세스의 각 단계에 요소를 도입합니다.

<div class="content-ad"></div>

- 에이전트들은 구인 광고에 관한 정보를 검색하기 위해 도구를 사용할 것입니다. 초기 구인 공고는 사용자 쿼리를 기반으로 검색됩니다.
- 에이전트들은 사용자의 이력서와 쿼리를 기반으로 구인 공고에 대한 등급 및 그에 대한 이유를 제공할 것입니다.
- 에이전트들은 인터넷 접근 도구를 사용하여 구인 광고의 소스 조직에 대한 정보를 연구하고, 그 정보를 기반으로 조직에 대한 등급 점수를 제공할 것입니다.
- 에이전트들은 최종적으로 결과를 요약하고 미리 정의된 모델에 따라 결과를 구조화할 것입니다.

다음 섹션에서는 나열된 AI 에이전트들의 능력을 활용하여 구인 검색 엔진을 구축하는 방법에 대해 살펴보겠습니다.

# 실습 안내: AI-기반 구인 검색 엔진 구축하기

이번 섹션에서는 완벽한 구현 단계별로 안내하며 최종 결과를 분석하고 잠재적 개선 사항을 살펴볼 것입니다.

<div class="content-ad"></div>

# 프로젝트 구조

프로젝트 구조는 기능과 컴포넌트 간의 명확한 분리를 보장하여 코드 수정 및 적응을 쉽게 할 수 있도록 합니다. 다음과 같은 구성요소로 구성됩니다:

- configs 디렉토리: 에이전트(역할, 배경, 배경 이야기 설정) 및 작업(설명 및 기대 출력 설정)을 구성하는 데 필요한 모든 구성 및 매개변수를 포함
- data 디렉토리: 작업 검색 엔진을 테스트하는 데 필요한 모든 데이터를 포함
- models 디렉토리: 기대 출력 스키마를 정의하는 모델을 포함
- utils 디렉토리: 필요한 지원 함수를 포함
- agents_factory.py 및 tasks_factory.py: 구성에 기반하여 에이전트 및 작업의 인스턴스를 동적으로 생성하는 데 사용됩니다.

```markdown
project/
├── configs
│ └── agents.yml # 에이전트 구성
│ └── tasks.yml # 작업 구성
│
├── data
│ ├── sample_jobs.json # 작업 목록을 포함하는 JSON 파일
│ └── sample_resume.txt # 이력서를 포함하는 텍스트 파일
│
├── models
│ └── models.py # ORM 모델
│
├── utils
│ └── utils.py # 유틸리티 함수 및 도우미
│
├── .env # 필요한 모든 환경 변수 포함
│
├── agents_factory.py # 에이전트 인스턴스 생성을 위한 팩토리 클래스
├── tasks_factory.py # 작업 인스턴스 생성을 위한 팩토리 클래스
│
└── main.py # 메인
```

<div class="content-ad"></div>

# 프레임워크

프로젝트는 crewAI 프레임워크를 활용하여 엔드 투 엔드 응용 프로그램을 구축할 것입니다. 이는 할당된 작업과 특정 도구를 사용하여 AI 에이전트를 구축하기 위한 직관적인 인터페이스를 제공합니다. 다른 사용 가능한 AI 프레임워크와 매우 잘 통합되며, 이는 이 글의 범위에 완벽하게 맞습니다.

먼저 Python 환경(제 경우에는 python-3.11.9 사용)이 있는지 확인하고 필요한 모든 도구와 함께 프레임워크를 설치하십시오.

```js
pip install 'crewai[tools]'
```  

<div class="content-ad"></div>

`crewai[tools]` 패키지 설치는 애플리케이션을 실행하는 데 필요한 모든 패키지를 포함해야 합니다.

# 도구들

![이미지](/assets/img/2024-05-18-JobSearch20-TurboAIAgentsLeadingtheWay_4.png)

에이전트는 할당된 작업을 수행하기 위해 일련의 도구가 필요합니다. 도구를 에이전트에 할당하는 것은 요청 흐름 아키텍처를 어떻게 정의하고 정보가 한 에이전트에서 다른 에이전트로 전송되는지에 따라 달라집니다.

<div class="content-ad"></div>

crewAI 내에서 이미 사용 가능한 두 가지 도구를 사용하여 우리의 사용 사례를 테스트해야 합니다.

**FileReadTool**

생산 규모에서는 신뢰할 수있는 여러 API를 확보하여 작업 데이터를 제공하고 관리할 수 있으며, 이러한 경우 주요 에이전트 도구는 여러 공급 업체와 인터페이스 할 수 있고 선택한 매개변수를 기반으로 작업 정보를 가져올 수 있는 도구입니다.

우리 애플리케이션의 범위 내에서는 이미 검색된 JSON 응답 샘플 sample_jobs.json을 사용하여 작업 세부 정보 목록을 갖고 솔루션을 집중적으로 보여주도록 할 것입니다.

<div class="content-ad"></div>

생성된 이력서 샘플 **sample_resume.txt**은 에이전트가 제공하는 평점을 테스트하기 위해 사용할 수 있습니다.

**SerperDevTool**

에이전트들은 조직에 관한 정보를 수집하고 사용자에게 평점 피드백을 제공하는 것이 그들의 임무입니다. 이 도구는 에이전트들이 인터넷을 검색할 수 있도록 지원하며, serper.dev에 계정을 생성하여 필요한 API 키를 획들한 후 환경에 로드하여 이 도구를 사용할 수 있습니다.

API 키가 **.env** 파일에 정의되어 있는지 확인하세요.

<div class="content-ad"></div>

```js
SERPER_API_KEY=<>
```

툴을 직접 가져와서 사용할 수 있습니다

```js
from crewai_tools import FileReadTool, SerperDevTool
```

새로운 기능을 빠르고 간단하게 애플리케이션 범위를 확장하기 위해 툴을 추가하고 에이전트에 할당하는 것이 중요합니다.```

<div class="content-ad"></div>

# 데이터

샘플_jobs.json은 사용 사례의 테스트 데이터로 합성으로 생성된 샘플 구인 광고 목록의 JSON 응답을 포함하고 있습니다.

기사에 제시된 구인 데이터는 JSON 형식으로 표시되지만, 에이전트들이 구문 분석할 수 있는 다른 형식으로 쉽게 변환될 수 있습니다. 예를 들어 PDF 또는 Excel 파일로 저장된 간단한 텍스트, 직접 수집한 구인 설명 데이터 문서 등이 있습니다.

검색 프로세스를 완전히 활용하려면 에이전트들이 작업 플랫폼 API와 상호 작용하는 도구를 사용하여 데이터 수집 프로세스를 자동화하고 확장해야 합니다. 이러한 공식적인 구인 검색 API 제공업체의 예로는 Glassdoor나 Jooble이 있습니다.

<div class="content-ad"></div>

```js
{
  "jobs": [
    {
      "id": "VyxlLGIsICxELGUsdixlLGwsbyxwLGUscixELGUsbSxhLG4sdCxTLHkscixhLGMsdSxzLGUsLCwgLE4=",
      "title": "Web Developer",
      "company": "Apple",
      "description": "As a Web Developer at CQ Partners, you will be a leader in the structuring, maintaining, and facilitating of websites and web-based applications...",
      "image": "<https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQAkPEjEwMeJizfsnGN-qUAEw8pmPdk357KIzsi&s=0>",
      "location": "Syracuse, NY",
      "employmentType": "Full-time",
      "datePosted": "17 hours ago",
      "salaryRange": "",
      "jobProvider": "LinkedIn",
      "url": "<https://www.linkedin.com/jobs/view/web-developer-at-demant-3904417702?utm_campaign=google_jobs_apply&utm_source=google_jobs_apply&utm_medium=organic>"
    },
    {
      "id": "VyxlLGIsICxELGUsdixlLGwsbyxwLGUsciwsLCAsVSxYLC8sVSxJLCwsICxCLHIsYSxuLGQsaSxuLGc=",
      "title": "Web Developer, UX/UI, Branding, Graphics",
      "company": "Adobe",
      "description": "Degree required: Bachelor’s degree in relevant field...",
      "image": "<https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSV7-v1EkEhWtAh8W8WaqPD6vMQG2uBi0GOOOmb&s=0>",
      "location": "Columbia, MD",
      "employmentType": "Full-time",
      "datePosted": "1 day ago",
      "salaryRange": "",
      "jobProvider": "LinkedIn",
      "url": "<https://www.linkedin.com/jobs/view/web-developer-ux-ui-branding-graphics-at-adg-creative-3903771314?utm_campaign=google_jobs_apply&utm_source=google_jobs_apply&utm_medium=organic>"
    },
    ......
```

또한 경험 많은 데이터 과학자를 위한 생성된 이력서가 포함된 sample_resume.txt 파일이 있습니다.

Github에서 파일의 전체 내용을 확인할 수 있습니다.

# 작업

<div class="content-ad"></div>

작업은 해당 작업을 완료할 수 있는 적절한 도구와 기술을 갖춘 에이전트에게 배정됩니다.

각 작업은 설명, 예상 출력 및 작업을 완료할 책임이 있는 에이전트로 정의됩니다.

```js
# configs/tasks.yml 

job_search:
  description: |
    다음 요구 사항을 충족하는 작업 목록을 찾습니다: {query}
  expected_output: 모든 정보가 포함된 작업 목록의 유효한 json 형식의 구조화된 출력입니다. 필드 이름이 동일한지 확인하세요.

job_rating:
  description: |
    이력서 파일 정보를 찾는 데 도구를 사용합니다.
    받은 작업에 대해 이력서 정보에 따라 추가 등급을 제공합니다.
    등급은 1에서 10까지이며 10이 가장 적합합니다.
    모든 작업에는 등급이 있어야 합니다.
    추가로 등급 설명 필드를 추가하여 등급에 대한 이유를 1~2문장으로 설명합니다.
    모든 작업에 대한 모든 정보도 출력에 유지되도록 합니다.
  expected_output: 작업 목록과 각각의 등급을 유효한 json 형식으로 구조화된 출력입니다. 필드 이름이 동일한지 확인하세요.

evaluate_company:
  description: |
    작업 회사에 대한 정보를 찾기 위해 도구를 사용합니다.
    정보에는 회사 문화 평가, 회사 재무 보고서 및 주가 성과가 포함될 수 있습니다.
    회사에 대한 추가 등급을 나타내는 company_rating 필드를 제공합니다.
    등급은 1에서 10까지이며 10이 가장 좋은 등급입니다.
    모든 작업에는 등급이 있어야 합니다.
    추가로 company_rating_description 필드를 추가하여 등급에 대한 이유를 1~2문장으로 설명합니다.
    모든 작업에 대한 모든 정보도 출력에 유지되도록 합니다.
  expected_output: 작업 목록과 각각의 등급을 유효한 json 형식으로 구조화된 출력입니다. 모든 정보를 이 모델 {output_schema}에 따라 구조화하도록 확인하세요.

structure_results:
  description: |
    최종 보고서에 필요한대로 모든 컨텍스트를 사용하여 출력을 구조화합니다.
  expected_output: 작업 목록과 각각의 등급을 유효한 json 형식으로 구조화된 출력입니다. 제공하는 최종 출력이 스키마 {output_schema}를 준수하는 유효한 json임을 확인하세요.
```

TasksFactory 클래스를 사용하여 모든 필요한 작업을 생성합니다.

<div class="content-ad"></div>

```js
# project/tasks_factory.py 

from textwrap import dedent
from typing import Optional

from crewai import Agent, Task

from utils.utils import load_config # YAML 파일 불러오기

class TasksFactory:
    def __init__(self, config_path):
        self.config = load_config(config_path)

    def create_task(
        self,
        task_type: str,
        agent: Agent,
        query: Optional[str] = None,
        output_schema: Optional[str] = None,
    ):
        task_config = self.config.get(task_type)
        if not task_config:
            raise ValueError(f"{task_type}에 대한 구성을 찾을 수 없습니다.")

        description = task_config["description"]
        if "{query}" in description and query is not None:
            description = description.format(query=query)

        expected_output = task_config["expected_output"]
        if "{output_schema}" in expected_output and output_schema is not None:
            expected_output = expected_output.format(output_schema=output_schema)

        return Task(
            description=dedent(description),
            expected_output=dedent(expected_output),
            agent=agent,
        )
```

# 에이전트

에이전트는 선택된 작업을 가장 잘 완료하기 위해 역할, 목표 및 소개가 정의됩니다.

```js
# configs/tasks.yml 

job_search_expert:
  role: 최고의 취업 전문가
  goal: 최고의 구인 공고와 광고를 찾아 모든 요청자를 감명시키기
  backstory:  취업 시장의 전문가로서 많은 경험을 가진 취업 전문가

job_rating_expert:
  role: 최고의 취업평가 전문가
  goal: 이력서 정보에 가장 적합한 취업을 찾아 정확한 평가 제공으로 모든 요청자를 감동시키기
  backstory: 취업 매칭 및 평가에 대한 전문 성이 뛰어난 취업평가 전문가

company_rating_expert:
  role: 최고의 기업 평가자
  goal: 취업 적합성을 평가하기 위해 기업에 대한 모든 중요한 정보를 찾기
  backstory: 기업 정보 수집자 및 평가 전문가로서 회사 평가 및 심층적 연구에 대한 많은 전문성을 가진 피부록

summarization_expert:
  role: 최고의 출력 유효성 검사 및 요약가
  goal: 작업에 필요한 최종 결과물이 작업과 일치하는지 확인하기
  backstory: 필요한 대로 출력물에 보고하는 방법과 올바른 구조를 알고 있는 최고의 보고 전문가
```

<div class="content-ad"></div>

태스크 팩토리와 유사하게, 에이전트 팩토리 클래스는 설정에 기반하여 필요한 모든 AI 에이전트를 생성하는 데 사용됩니다.

```python
# project/agents_factory.py 

from typing import Any, List, Optional

from crewai import Agent

from utils.utils import load_config # YAML 파일로드

class AgentsFactory:
    def __init__(self, config_path):
        self.config = load_config(config_path)

    def create_agent(
        self,
        agent_type: str,
        llm: Any,
        tools: Optional[List] = None,
        verbose: bool = True,
        allow_delegation: bool = False,
    ) -> Agent:
        agent_config = self.config.get(agent_type)
        if not agent_config:
            raise ValueError(f"{agent_type}에 대한 구성을 찾을 수 없습니다.")

        if tools is None:
            tools = []

        return Agent(
            role=agent_config["role"],
            goal=agent_config["goal"],
            backstory=agent_config["backstory"],
            verbose=verbose,
            tools=tools,
            llm=llm,
            allow_delegation=allow_delegation,
        )
```

## LLM

LLM 선택에 관한 옵션 범위는 계속 확장되어 매주 새로운 모델이 출시됩니다. 이 설정에서는 특히 gpt-4–32k 버전 0613의 Azure Open AI 모델과 작업하게 될 것입니다. 그러나 선호하는 LLM으로 코드를 조정하고 테스트해보세요.```

<div class="content-ad"></div>

Azure OpenAI 모델을 사용하려면 API KEY와 ENDPOINT가 필요합니다. Azure는 이러한 모델을 설정하고 배포하기 위한 훌륭한 문서를 제공합니다.

우리는 `.env` 파일에 필요한 환경 변수를 추가합니다.

```js
OPENAI_API_VERSION = <>
AZURE_OPENAI_KEY= <>
AZURE_OPENAI_ENDPOINT = <>
```

아래 코드를 사용하여 선택한 모델을 가져와 사용할 수 있습니다.

<div class="content-ad"></div>

```js
from langchain_openai import AzureChatOpenAI
import os

azure_llm = AzureChatOpenAI(
            azure_endpoint=os.environ.get("AZURE_OPENAI_ENDPOINT"),
            api_key=os.environ.get("AZURE_OPENAI_KEY"),
            deployment_name="gpt4",
            streaming=True,
            temperature=0 # 더 일관적이고 정확한 출력을 위해 이 값을 0으로 설정했습니다
        )
```

# 출력 모델

에이전트가 최종적으로 일관된 출력을 보여주기 위해 원하는 출력 모델을 정의합니다.

```js
# models/models.py

from typing import List, Optional
from pydantic import BaseModel

class Job(BaseModel):
    id: Optional[str]
    location: Optional[str]
    title: Optional[str] 
    company: Optional[str]
    description: Optional[str]
    jobProvider: Optional[str]
    url: Optional[str]
    rating: Optional[int] 
    rating_description: Optional[str] 
    company_rating: Optional[int] 
    company_rating_description: Optional[str]  
    
class JobResults(BaseModel): 
    jobs: Optional[List[Job]]
```

<div class="content-ad"></div>

# 모든 것을 함께 넣어보자: 승무원

이전 다이어그램들은 에이전트가 원하는 결과물을 얻기 위해 작업할 일련의 이벤트를 보여줍니다. 한 작업의 완료는 다음 에이전트에게 전달되며 특정 역할로 최적화된 에이전트가 작업을 처리합니다. 따라서 순차적 프로세스를 구성하여 목적을 달성합니다. 에이전트 협력 및 병렬 처리를 통해 작업에 대한 다른 전략을 선택하고 설계를 재구성할 수 있습니다.

마지막으로, 우리는 main.py 내에서 서치 승무원을 생성하여 모든 AI 에이전트를 생성, 할당 및 실행합니다.

```js
import json
import os
from textwrap import dedent

from crewai import Crew, Process
from crewai_tools import FileReadTool, SerperDevTool
from dotenv import load_dotenv
from langchain_openai import AzureChatOpenAI
from pydantic import ValidationError

from agents_factory import AgentsFactory
from models.models import JobResults
from tasks_factory import TasksFactory

load_dotenv()


class JobSearchCrew:
    def __init__(self, query: str):
        self.query = query

    def run(self):
        # 에이전트가 활용할 LLM AI 정의
        azure_llm = AzureChatOpenAI(
            azure_endpoint=os.environ.get("AZURE_OPENAI_ENDPOINT"),
            api_key=os.environ.get("AZURE_OPENAI_KEY"),
            deployment_name="gpt4",
            streaming=True,
            temperature=0,
        )

        # 필요한 모든 도구 초기화
        resume_file_read_tool = FileReadTool(file_path="data/sample_resume.txt")
        jobs_file_read_tool = FileReadTool(file_path="data/sample_jobs.json")
        search_tool = SerperDevTool(n_results=5)

        # 에이전트 생성
        agent_factory = AgentsFactory("configs/agents.yml")
        job_search_expert_agent = agent_factory.create_agent(
            "job_search_expert", tools=[jobs_file_read_tool], llm=azure_llm
        )
        job_rating_expert_agent = agent_factory.create_agent(
            "job_rating_expert", tools=[resume_file_read_tool], llm=azure_llm
        )
        company_rating_expert_agent = agent_factory.create_agent(
            "company_rating_expert", tools=[search_tool], llm=azure_llm
        )
        summarization_expert_agent = agent_factory.create_agent(
            "summarization_expert", tools=None, llm=azure_llm
        )

        # 응답 모델 스키마
        response_schema = json.dumps(JobResults.model_json_schema(), indent=2)

        # 작업 생성
        tasks_factory = TasksFactory("configs/tasks.yml")
        job_search_task = tasks_factory.create_task(
            "job_search", job_search_expert_agent, query=self.query
        )
        job_rating_task = tasks_factory.create_task(
            "job_rating", job_rating_expert_agent
        )
        evaluate_company_task = tasks_factory.create_task(
            "evaluate_company",
            company_rating_expert_agent,
            output_schema=response_schema,
        )
        structure_results_task = tasks_factory.create_task(
            "structure_results",
            summarization_expert_agent,
            output_schema=response_schema,
        )

        # 승무원 조립
        crew = Crew(
            agents=[
                job_search_expert_agent,
                job_rating_expert_agent,
                company_rating_expert_agent,
                summarization_expert_agent,
            ],
            tasks=[
                job_search_task,
                job_rating_task,
                evaluate_company_task,
                structure_results_task,
            ],
            verbose=1,
            process=Process.sequential,
        )

        result = crew.kickoff()
        return result


if __name__ == "__main__":
    print("## 직업 검색 승무원에 오신 것을 환영합니다")
    print("-------------------------------")
    query = input(
        dedent("""
      찾고 있는 직업의 특성 목록을 제공하세요: 
    """)
    )

    crew = JobSearchCrew(query)
    result = crew.run()

    print("최종 결과를 확인 중..")
    try:
        validated_result = JobResults.model_validate_json(result)
    except ValidationError as e:
        print(e.json())
        print("데이터 출력 유효성 검사 오류, 다시 시도 중...")

    print("\n\n########################")
    print("## 결과 ")
    print("########################\n")
    print(result)
```

<div class="content-ad"></div>

작업 실행 후에는 JSON 출력이 정의된 모델 스키마로 유효성이 검사되도록 검증 확인을 실행합니다. 또한, 에이전트가 필요한 구조를 반환하는 것을 보장하기 위해 추가적인 유효성 검사 루프를 추가할 수도 있습니다. 이 완전한 프로세스를 구현하는 세부 정보에 대해 다루지는 않겠지만, 관심이 있다면 이 주제에 대한 훌륭한 글을 확인해보세요.

# 결과 분석

우리는 간단히 아래의 코드를 실행합니다.

```js
python main.py
```

<div class="content-ad"></div>

우리의 초기 에이전트 쿼리에는 다음을 입력했습니다.

```js
찾고 있는 직업의 특성 목록을 제공하십시오.

미국에서 연봉 범위가 $100K- $170K인 기계 학습 및 데이터 과학 직업
```

쿼리는 추가적으로 위치, 시작 날짜, 산업 등과 같은 많은 다른 매개변수를 필터링하고 취업 정보를 찾기에 적합하게 확장될 수 있습니다.

Output:

<div class="content-ad"></div>

터미널 결과를 보면 첫 번째 에이전트가 성공적으로 실행되어 도구를 사용하여 작업 목록 콘텐츠를 검색했습니다.

![이미지1](/assets/img/2024-05-18-JobSearch20-TurboAIAgentsLeadingtheWay_5.png)

에이전트는 그런 다음 목록을 필터링하고 입력 쿼리를 기반으로 올바른 작업을 찾는 데 성공했습니다.

![이미지2](/assets/img/2024-05-18-JobSearch20-TurboAIAgentsLeadingtheWay_6.png)

<div class="content-ad"></div>


개발자님의 진행 상황을 보여주는 상세한 디버깅 정보를 건너뛰고 최종 결과로 넘어가겠습니다.

결과에는 구성된 작업 내에서 요청된 모든 필드뿐만 아니라 유효한 최종 JSON 결과 구조도 포함되어 있습니다. 또한 우리는 지시된 대로 제공된 평가 및 해당 평가에 대한 이유도 확인합니다.

이제 해당 평가가 실제로 사용자 이력서와 일치하는 과정을 반영하는지 더 테스트해 보려면 쿼리를 다음과 같이 조정해보겠습니다

```js
찾고 있는 직업의 특성 목록을 제공해 주세요: 

미국의 웹 개발자 직업
```

<div class="content-ad"></div>

웹 개발자 역할을 요청하면서 기계 학습 및 데이터 과학 포지션에 최적화된 이력서를 갖고 계시다가 평가가 실제로 낮아졌다는 것을 관찰했습니다.

# 소스 코드

프로젝트의 전체 소스 코드는 GitHub에서 확인할 수 있습니다.

# 요약

<div class="content-ad"></div>

이 기사는 전통적으로 시간이 많이 소요되는 구직 과정에 AI 에이전트를 도입하는 것이 어떤 엄청난 영향을 미치는지에 대해 논의했습니다. 특히, 온라인 구인 광고와 요구 사항을 평가하고 매칭하는 단계에 초점을 맞췄습니다. 우리는 구직 과정의 주요 도전과제를 살펴보고, 인간이 완료하는 데 주로 몇 주, 아니면 몇 달이 걸릴 작업들을 AI 에이전트가 몇 초만에 수행할 수 있는 위치에 AI 에이전트를 위치시켰습니다. 마지막으로, 사용자에 대한 적합한 직업을 찾도록 작업하는 AI 에이전트를 자동화하는 데 필요한 코드를 구현했으며, 여러 도구를 활용하여 작업을 완료했습니다.

다가오는 수개월과 수년 동안, AI가 결국 구직 엔진과 플랫폼에 통합될 것으로 예상되며, 결과적으로 빠르고 극도로 개인화된 취업 매칭 과정이 이루어질 것입니다. 그러나 자신의 AI 기반 구직 엔진의 설계와 아키텍처를 통제할 수 있는 능력은 구직 시에 유연성과 더 큰 장점을 가져다 줄 것입니다.

# 잠재적 개선 사항

본 문서에서 논의된 사용 사례는 AI 기반 구직 엔진에 추가될 수 있는 잠재적인 기능 중 일부에 불과합니다. 나는 구직 플랫폼이 결국 이러한 도구들을 통합하고 거대한 고객 기반을 활용할 것이라고 믿습니다. 이를 새로운 수준으로 끌어올릴 수 있는 것은 자신의 요구 사항에 맞게 맞춤형으로 제작된 크로스 플랫폼 솔루션을 직접 구축하는 것입니다. 선택한 모델을 통합하고 에이전트와 작업을 특정 요구 사항에 맞게 조정하는 유연성을 가질 수 있는것입니다.

<div class="content-ad"></div>

아래 목록에 다시 그림카드를 설치할 것이 좋습니다.

- 다양한 작업 검색 플랫폼 API와 상호 작용하는 대규모 에이전트 및 도구를 통합하여 확장
- 채용공고에 기반하여 이력서 및 자기소개서를 개선하고 적응시키기 위해 과제를 개인화하는 자동화된 응용 프로그램 과정
- 채용 조건에 따라 잠재적 인터뷰 문항 목록을 생성하고 에이전트와 모의 인터뷰를 실행하여 더 나은 준비 상태
- 포지션 및 광고된 기술을 위해 광범위한 시장 조사를 통해 더 나은 급여 협상
- 실시간 처리 및 평가로 광고를 지속적으로 모니터링한 후 더 나은 기회 제공
- ….

- 새로운 이야기를 게시할 때 알림을 받으려면 구독하세요.
- LinkedIn에서 저에게 연락을 주세요.

더 많은 유사한 기사에 관심이 있으시다면 아래 목록을 확인해보세요.

<div class="content-ad"></div>

# 참고 자료

미국 노동통계국. (2024, 4월 5일). Table A-12. 2024년 Q01 결과에 따른 실업자의 실업 기간별 인원 수. U.S. Bureau of Labor Statistics. https://www.bls.gov/news.release/empsit.t12.htm

Tao, Zhengwei 등. 대형 언어 모델의 이벤트 추론에 대한 포괄적 평가. arXiv:2404.17513, arXiv, 2024년 4월 26일. arXiv.org, https://doi.org/10.48550/arXiv.2404.17513.

일자리를 얻기 위해 몇 번의 지원이 필요할까요? — movement to work. Movement to Work — Movement to Work는 영국 기업이 청소년 실업 문제를 해결하기 위해 고품질 직업 훈련과 청소년을 위한 직무 경험 기회를 제공을 통해 자발적으로 협력하는 단체입니다. (2022, 7월 15일). https://movementtowork.com/how-many-applications-does-it-take-to-get-a-job/

<div class="content-ad"></div>

크루에이아이 - 다중 AI 에이전트 시스템을 위한 플랫폼