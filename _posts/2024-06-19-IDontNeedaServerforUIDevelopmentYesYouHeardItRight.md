---
title: "UI 개발을 위해 서버가 필요하지 않아요 - 네, 정말 그렇게요"
description: ""
coverImage: "/assets/img/2024-06-19-IDontNeedaServerforUIDevelopmentYesYouHeardItRight_0.png"
date: 2024-06-19 11:13
ogImage: 
  url: /assets/img/2024-06-19-IDontNeedaServerforUIDevelopmentYesYouHeardItRight_0.png
tag: Tech
originalTitle: "I Don’t Need a Server for UI Development — Yes, You Heard It Right!"
link: "https://medium.com/@boobalaninfo/i-dont-need-a-server-for-ui-development-yes-you-heard-it-right-a6334b31a210"
---


<!--
/assets/img/2024-06-19-IDontNeedaServerforUIDevelopmentYesYouHeardItRight_0.png

UI 개발 세계에서 많은 사람들이 지속적인 서버 개발, 배포 문제, 서버 이용 불가 등과 같은 다양한 서버 관련 장애물을 직면합니다. 이러한 도전에 부딪치면 개발 프로세스가 느려질 수 있습니다. 이러한 장애물을 극복하기 위해 나는 개발자들이 HTTP 서버를 모의하여 예측 가능하고 테스트 가능하며 독립적인 UI 환경을 만들 수 있도록 해주는 강력한 툴인 WireMock을 사용하기로 결정했습니다.

이 글에서는 WireMock를 설정하고 다양한 시나리오를 구성하며 엔드포인트를 유효성 검사하는 과정을 안내해드리겠습니다. 초기 WireMock 템플릿은 이 

참조 동영상은 여기에서 확인할 수 있습니다.
-->

<div class="content-ad"></div>

# WireMock을 사용하는 이유

WireMock은 개발 중에 서버 의존성을 제거하는 데 도움이 됩니다. 서버 응답을 시뮬레이션함으로써 실제 서버가 준비되거나 이용 가능할 때까지 기다리지 않고 UI를 개발하고 테스트할 수 있습니다. 이 접근 방식은 통합하기 전에 모든 가능한 엣지 케이스를 다룰 수 있도록 보장하여 응용 프로그램의 신뢰성과 견고성을 향상시킵니다.

# WireMock 설정하기

로컬 머신에서 WireMock을 설정하려면 다음 단계를 따르세요:

<div class="content-ad"></div>

리포지토리를 복제하세요:

git clone https://github.com/piappstudio/WireMock.git

WireMock 실행: 터미널을 열고 로컬 서버를 설정하기 위해 다음 명령을 실행하세요:

```js
java -jar wiremock-standalone-3.6.0.jar --port 9191 --verbose --global-response-templating --jetty-header-buffer-size 16384
```

<div class="content-ad"></div>

이 명령은 WireMock 서버를 9191 포트에서 시작하며 자세한 로깅과 전역 응답 템플릿 사용이 활성화됩니다.

## 폴더 구조

- __file
  - __files 폴더는 응답 일부로 제공될 수 있는 정적 파일을 저장하는 데 사용됩니다. 이러한 파일에는 JSON, XML 또는 기타 반환하려는 콘텐츠가 포함될 수 있습니다.
- mappings
  - __files 폴더는 모의 요청에 대한 응답의 일부로 제공될 수 있는 정적 파일을 저장하는 데 사용됩니다. 이 파일에는 JSON, XML, HTML 또는 기타 반환하려는 콘텐츠가 포함될 수 있습니다.

![이미지](/assets/img/2024-06-19-IDontNeedaServerforUIDevelopmentYesYouHeardItRight_1.png)

<div class="content-ad"></div>

특정 JSON 응답을 반환하는 스텁을 만들고 싶다면 다음과 같이 작성할 수 있습니다. GET 요청이 /v1/biggoss/shows로 들어오는 경우를 가정하겠습니다.

```js
{
  "request": {
    "method": "GET",
    "url" : "/v1/biggboss/shows"
  },
  "response": {
    "status": 200,
    "headers": {
      "Content-Type": "application/json"
    },
    "bodyFileName": "biggboss-shows-api/default.json"
  }
}
```

- Request: 수신된 요청의 구조를 정의합니다.
- Response: 위 요청에 대한 응답의 구조를 정의합니다.

# API 유효성 검사

<div class="content-ad"></div>

/v1/biggboss/shows 엔드포인트를 유효성 검사하려면 다음 단계를 따라 주세요:

- Postman 열기: Postman에 WireMock-BiggBoss.postman_collection.json 파일을 가져오세요.
- 요청 호출: “Bigg Boss Shows” 엔드포인트로 요청을 보내 응답을 받으세요.

```js
{
  "shows": [
    {
      "title": "BiggBoss Tamil Season 7",
      "id": "bbtamil7",
      "host": "Kamalahaasan",
      "start_date": "2023-10-01",
      "end_date": "2024-01-14",
      "winner": "Archana",
      "runner": "Manichandra",
      "cash": "Poornima (16L)",
      "logo": "https://raw.githubusercontent.com/piappstudio/resources/main/biggboss/images/shows/tamil/session7/logo.png",
      "more_info": "https://raw.githubusercontent.com/piappstudio/resources/main/biggboss/json/shows/tamil_session_7.json",
      "trends": "https://raw.githubusercontent.com/piappstudio/resources/main/biggboss/json/shows/tamil_session_7_trends.json",
      "votes": "https://raw.githubusercontent.com/piappstudio/resources/main/biggboss/json/shows/tamil/season7/votes.json"
    }
  ],
  "reviewers": [
    {
      "channel_name": "SBI Electroal Bond - 1 (Google Drive)",
      "description": "Google Drive에서 더 나은 분석을 위해 엑셀 형식의 선거채권 파트-1 다운로드",
      "url": "https://docs.google.com/spreadsheets/d/17DJrN1orB93OTIYF6QMiFj1aZ9F2WZrA_D8zfjH56FE",
      "image": "https://i.ytimg.com/vi/z_OY38rDj9k/hq720.jpg"
    },
    {
      "channel_name": "SBI Electroal Bond - 2 (Google Drive)",
      "description": "Google Drive에서 더 나은 분석을 위해 엑셀 형식의 선거채권 파트-2 다운로드",
      "url": "https://docs.google.com/spreadsheets/d/1GQBoGkyqNd3HOWDaJc2ueOmDQpmYob74",
      "image": "https://i.ytimg.com/vi/z_OY38rDj9k/hq720.jpg"
    },
    {
      "channel_name": "Bigg Boss Tamizh Review",
      "description": "BiggBoss 리뷰, 비평가, 트롤 및 심층 분석",
      "url": "https://www.youtube.com/@tamizhreview/videos",
      "image": "https://raw.githubusercontent.com/piappstudio/resources/main/biggboss/images/shows/tamil/session7/channels/tamizhreview.png"
    },
    {
      "channel_name": "Pi App Studio",
      "description": "타밀어로 무료 iOS 및 안드로이드 개발 강좌",
      "url": "https://www.youtube.com/@piappstudio/videos",
      "image": "https://raw.githubusercontent.com/piappstudio/resources/main/biggboss/images/shows/tamil/session7/channels/piappstudio.png"
    }
  ]
}
```

이제 시나리오 기반 구성으로 넘어가 봅시다.

<div class="content-ad"></div>

# WireMock의 기본 키워드 이해하기

## Url/UrlPathPattern

WireMock에서 처리할 URL을 정의합니다.

## Priority

<div class="content-ad"></div>

환경 설정의 우선순위를 설정합니다. 여러 개의 설정이 존재하는 경우, 우선 순위가 가장 높은 것(숫자가 가장 작은 것)이 선택됩니다. 기본값은 5이며, 여기서 1이 가장 높은 우선 순위이고 Integer.MAX_VALUE(Java)가 가장 낮은 우선 순위입니다.

## 시나리오명

- 정의: 동일 시나리오에 속하는 관련된 스텁 그룹을 나타내는 고유 식별자입니다.
- 목적: 여러 요청과 응답을 연결하여 상태를 유지하는 동작을 가능케 합니다.
- 예시: "scenarioName": "biggboss-show"

## newScenarioState

<div class="content-ad"></div>

- **정의:** 요청이 일치되고 응담이 제공된 후 시나리오가 전환하는 상태입니다.
- **목적:** 시나리오가 다른 상태로 이동하여 순차적인 상태 기반 상호작용이 가능하도록 합니다.
- **예시:** "newScenarioState": "shows-only"

## 요구 시나리오 상태

- **정의:** 특정 스텁이 요청과 일치하려면 시나리오가 있어야 하는 상태를 지정합니다.
- **목적:** 시나리오가 특정 상태에 있을 때만 스텁이 일치하도록 보장합니다.
- **예시:** "requiredScenarioState": "shows-only"

# 빈 시나리오 구성

<div class="content-ad"></div>

새로운 엔드포인트/시나리오/shows/empty를 만들어서 "shows-empty" 상태를 트리거하는게 좋을 거예요.

```js
{
  "scenarioName": "biggboss-show",
  "newScenarioState": "shows-empty",
  "request": {
    "method": "POST",
    "urlPathPattern" : "/scenario/shows/empty"
  },
  "response": {
    "status": 200,
    "body": "Empty show scenario stated"
  }
}
```

이 구성은 "Bigg Boss" 쇼를 관리하는 애플리케이션의 동작을 시뮬레이션하고 싶은 경우 유용합니다. /scenario/shows/empty 엔드포인트로 포스트를 보내면 시나리오의 상태가 "shows-empty"로 전환되어 후속 요청 처리에 영향을 줄 수 있습니다.

이제 이 상태를 감지하고 /v1/biggboss/shows에 대한 응답을 구성해볼까요?

<div class="content-ad"></div>

```js
{
  "scenarioName": "biggboss-show",
  "requiredScenarioState": "shows-empty",
  "request": {
    "method": "GET",
    "url" : "/v1/biggboss/shows"
  },
  "response": {
    "status": 200,
    "headers": {
      "Content-Type": "application/json"
    },
    "bodyFileName": "biggboss-shows-api/empty_shows.json"
  }
}
```

empty_shows.json

```js
{}
```

여기서 /v1/biggboss/shows API에 대해 shows-empty가 요구되는 경우 empty_shows.json 파일을 설정하고 있습니다.```

<div class="content-ad"></div>

## 실행 단계

- Mock 서버 시작
- “shows-empty” 시나리오를 트리거하기 위해 /scenario/shows/empty를 호출
- 빈 JSON을 받기 위해 클라이언트 API /v1/biggboss/shows 호출

# 리뷰어 전용 시나리오 구성

shows-reviewers-only 시나리오를 트리거하기 위한 새로운 엔드포인트/scenario/shows/reviewers 생성

<div class="content-ad"></div>

```json
{
  "scenarioName": "biggboss-show",
  "newScenarioState": "shows-reviewers-only",
  "request": {
    "method": "POST",
    "urlPathPattern" : "/scenario/shows/reviewers"
  },
  "response": {
    "status": 200,
    "body": "Only reviewers scenario stated"
  }
}
```

shows-reviewers-only시나리오와 일치하도록 스텁 구성

```json
{
  "scenarioName": "biggboss-show",
  "requiredScenarioState": "shows-reviewers-only",
  "request": {
    "method": "GET",
    "url" : "/v1/biggboss/shows"
  },
  "response": {
    "status": 200,
    "headers": {
      "Content-Type": "application/json"
    },
    "bodyFileName": "biggboss-shows-api/only_reviewers.json"
  }
}
```

only_reviewers.json```

<div class="content-ad"></div>

```json
{
  "reviewers": [
    {
      "channel_name": "SBI Electroal Bond - 1 (Google Drive)",
      "description": "Google 드라이브에서 더 나은 분석을 위해 엑셀 형식의 선거 채권 파트 1 다운로드",
      "url": "https://docs.google.com/spreadsheets/d/17DJrN1orB93OTIYF6QMiFj1aZ9F2WZrA_D8zfjH56FE",
      "image": "https://i.ytimg.com/vi/z_OY38rDj9k/hq720.jpg"
    },
    {
      "channel_name": "SBI Electroal Bond - 2 (Google Drive)",
      "description": "Google 드라이브에서 더 나은 분석을 위해 엑셀 형식의 선거 채권 파트 2 다운로드",
      "url": "https://docs.google.com/spreadsheets/d/1GQBoGkyqNd3HOWDaJc2ueOmDQpmYob74",
      "image": "https://i.ytimg.com/vi/z_OY38rDj9k/hq720.jpg"
    },
    {
      "channel_name": "Bigg Boss Tamizh Review",
      "description": "BiggBoss 리뷰, 비평가, 트롤 및 심층 분석",
      "url": "https://www.youtube.com/@tamizhreview/videos",
      "image": "https://raw.githubusercontent.com/piappstudio/resources/main/biggboss/images/shows/tamil/session7/channels/tamizhreview.png"
    },
    {
      "channel_name": "Pi App Studio",
      "description": "타밀어로 무료 iOS 및 안드로이드 개발 코스",
      "url": "https://www.youtube.com/@piappstudio/videos",
      "image": "https://raw.githubusercontent.com/piappstudio/resources/main/biggboss/images/shows/tamil/session7/channels/piappstudio.png"
    }
  ]
}
```

## 실행 단계

- 목 서버 시작
- /scenario/shows/reviewers를 호출하여 “shows-reviewers-only” 시나리오를 트리거
- 리뷰어만 내보내는 JSON 응답을 받기 위해 클라이언트 API /v1/biggboss/shows 호출

동일한 방식으로 “N”개의 시나리오와 해당 클라이언트 스텁을 구성할 수 있습니다.```

<div class="content-ad"></div>

## 모든 API에 대한 전역 오류 처리

500, 503 및 404와 같은 전역 오류는 다른 endpoint에 대한 시나리오와 유사한 방식으로 WireMock 시나리오를 사용하여 처리할 수 있습니다. 이러한 오류를 구성하는 방법은 다음과 같습니다:

- 오류 시나리오 생성:

- 500 내부 서버 오류: server-down-error-started
- 503 서비스를 사용할 수 없음: server-maintenance-error-started
- 404 찾을 수 없음: server-api-not-found-error-started

<div class="content-ad"></div>

그러나 전역 오류에 대한 클라이언트 스텁을 구성하는 데 몇 가지 특별한 고려 사항이 있습니다:

- 우선 순위: "priority"를 1로 설정하세요. 기본적으로 스텁 우선 순위는 5이지만, 1로 설정하면 요청이 발생할 때 이 스텁이 가장 높은 우선 순위를 갖게 됩니다. WireMock는 모든 요청을 가로채고 requiredScenarioState를 확인한 후 시스템 상태가 일치하는 경우 적절한 오류 응답을 반환합니다.
- urlPathPattern: "/(.*)"을 사용하세요. 이 와일드카드 정규식은 모든 엔드포인트와 일치하여 오류 응답이 전역적으로 적용되도록 합니다.

```js
{
  "priority": 1,
  "scenarioName": "general-server-error",
  "requiredScenarioState": "server-down-error-started",
  "request": {
    "urlPathPattern" : "/(.*)"
  },
  "response": {
    "status": 500,
    "body": "Server is Down"
  }
}
```

## 실행 단계

<div class="content-ad"></div>

- 목 서버를 시작하세요.
- /scenario/trigger-server-down-error를 호출하여 "500 에러" 시나리오를 시작하세요.
- /v1/biggboss/shows 클라이언트 API를 호출하여 500 에러 응답을 받으세요. 실제로 시스템은 모든 API 호출에 대해 이 500 에러를 반환합니다.
- 모든 시나리오를 재설정하려면 __admin/scenarios/reset을 호출하세요.

# 결론

WireMock를 사용하여 UI 개발을 하는 것은 게임 체인저입니다. 서버 가용성과 독립적으로 작업할 수 있어 개발 프로세스가 효율적이고 중단되지 않음을 보장합니다. 다양한 시나리오를 설정하고 엔드포인트를 확인함으로써 더 신뢰할 수 있고 견고한 응용프로그램을 구축할 수 있습니다.

GitHub 저장소에서 자세한 내용과 템플릿을 확인하고 시작하는 데 도움이 되는 구독 부탁드립니다. 즐거운 코딩하세요!