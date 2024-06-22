---
title: "자바스크립트 파일 분석으로 버그 찾기"
description: ""
coverImage: "/assets/img/2024-05-20-AnalyzingJavaScriptFilesToFindBugs_0.png"
date: 2024-05-20 21:26
ogImage: 
  url: /assets/img/2024-05-20-AnalyzingJavaScriptFilesToFindBugs_0.png
tag: Tech
originalTitle: "Analyzing JavaScript Files To Find Bugs"
link: "https://medium.com/@rajput623929/analyzing-javascript-files-to-find-bugs-2b7d67a52c4e"
---


안녕하세요, 해커 여러분, 저는 Horbio입니다.

## 모든 독자들에게 중요합니다. 기사를 읽은 후 웹 사이트를 방문해주시기 바랍니다. 기사의 끝 부분에 사용 가능한 링크가 있습니다.

자바스크립트는 웹 개발의 중요한 구성 요소이며, 자바스크립트 파일은 웹 애플리케이션의 기능에서 중요한 역할을 합니다. 웹에서 자바스크립트 파일이 왜 중요한지에 대한 몇 가지 주요 이유는 다음과 같습니다:

상호 작용: 자바스크립트를 사용하면 개발자들이 웹 페이지에 상호 작용성과 반응성을 추가하여 더 매력적이고 사용자 친화적인 환경을 만들 수 있습니다.

<div class="content-ad"></div>

동적 콘텐츠: 자바스크립트는 전체 페이지를 새로 고치지 않고도 웹 페이지의 콘텐츠를 동적으로 로드하고 업데이트하는 것을 용이하게 합니다. 이는 사용자 경험을 크게 향상시킵니다.

양식 유효성 검사: 자바스크립트를 사용하면 클라이언트 측 양식 유효성 검사를 가능하게 하여 사용자 입력이 특정 기준을 충족하는지 확인하고 제출하기 전에 데이터 정확성을 향상시킵니다.

자바스크립트 파일은 버그 바운티 프로그램에서 핵심적인 역할을 합니다. 보안 연구자들은 웹 응용 프로그램의 취약점을 식별하고 보고합니다. 이러한 파일에는 다음과 같은 민감한 정보가 포함될 수 있습니다:

- AWS 액세스 키
- AWS 비밀 키
- API 키
- 비밀번호
- 관리자 자격 증명
- 비밀 토큰
- OAuth 토큰
- OAuth 토큰 비밀번호

<div class="content-ad"></div>

그런 민감한 정보를 발견하면 정보 노출로 보고할 수 있습니다. 정보에 자격 증명이 포함되어 있는 경우, 액세스 제어의 파괴와 같은 보안 취약점으로 이어질 수 있으며, 이에 따라 보고해야 합니다.

![이미지](/assets/img/2024-05-20-AnalyzingJavaScriptFilesToFindBugs_0.png)

주요 질문: JavaScript 파일을 어떻게 분석할 수 있을까요?

답은 간단합니다 — 페이지 소스를 확인하세요.

<div class="content-ad"></div>


![image](https://miro.medium.com/v2/resize:fit:996/0*MfcnicBkd4dj3md_.gif)

Just kidding.

I found valid login credentials in JavaScript files. Here are the steps to do it:

- Get a list of your domains (we call it domains.txt).
- Use a tool for crawling URLs, such as Katana, Waybackurls, or gau.


<div class="content-ad"></div>

```js
cat domains.txt | katana | grep js | httpx -mc 200 | tee js.txt
```

제공해 주신 명령어 분해는 명확하고 정확합니다. 요약해 보면 다음과 같습니다:

- cat domains.txt | katana: 이 명령어는 domains.txt 파일의 내용을 읽어와 그것을 파이프(|)를 통해 Katana에 전달합니다. Katana는 domains.txt에 나열된 URL을 크롤링하여 추가 URL 및 경로를 찾습니다.
- grep js: grep 명령어는 Katana의 출력을 필터링하여 ".js" 패턴이 포함된 라인만 포함하도록 합니다. 이는 JavaScript 파일을 나타내며 검색 범위를 JavaScript 리소스로 좁히는 데 도움이 됩니다.
- httpx -mc 200: 이 명령어는 httpx 도구를 사용하여 필터링된 URL에 HTTP 요청을 보내고 응답을 검색합니다. -mc 200 옵션은 성공적인 HTTP 상태 코드 200(OK)를 반환하는 URL만 표시됨을 보장합니다. 이렇게 함으로써 존재하지 않거나 오류를 반환하는 URL을 필터링합니다.
- tee js.txt: tee 명령어는 필터링된 URL을 화면에 표시하고 동시에 js.txt라는 파일에 저장합니다. 이 파일에는 스캔 과정 중에 식별된 JavaScript 파일 URL 목록이 포함됩니다.

전반적으로, 이 명령어 시퀀스는 제공된 도메인을 JavaScript 파일에 대해 스캔하고 관련 없는 URL을 필터링하며 적절한 JavaScript 파일 URL을 추가 분석이나 테스트를 위해 저장하는 데 효과적입니다.

<div class="content-ad"></div>

너클리로 스캐닝

```bash
nuclei -l js.txt -t ~/nuclei-templates/exposures/ -o js_bugs.txt
```

또는 이 코드를 사용하세요:

```bash
file="js.txt"
# 파일 내 각 줄에 대해 반복
while IFS= read -r link
do
    # wget을 사용하여 JavaScript 파일 다운로드
    wget "$link"
done < "$file"
```

<div class="content-ad"></div>


grep -r -E "aws_access_key|aws_secret_key|api key|passwd|pwd|heroku|slack|firebase|swagger|aws_secret_key|aws key|password|ftp password|jdbc|db|sql|secret jet|config|admin|pwd|json|gcp|htaccess|.env|ssh key|.git|access key|secret token|oauth_token|oauth_token_secret|smtp" *.js


**BOOM GUYS**

![Image](/assets/img/2024-05-20-AnalyzingJavaScriptFilesToFindBugs_1.png)

I hope it is helpful for you.


<div class="content-ad"></div>

## 중요: 여기 방문해주세요

우리의 유튜브 채널을 팔로우하고 구독하지 마세요

팔로우: [https://medium.com/@rajput623929](https://medium.com/@rajput623929)

구독: [https://www.youtube.com/channel/UCBiIg0P8onz7EZgXNhjpR4A/](https://www.youtube.com/channel/UCBiIg0P8onz7EZgXNhjpR4A/)

<div class="content-ad"></div>

가입 : https://t.me/MrHorbio

관련 콘텐츠 :