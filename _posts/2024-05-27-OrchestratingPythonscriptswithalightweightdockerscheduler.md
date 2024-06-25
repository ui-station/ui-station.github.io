---
title: "Python 스크립트를 가벼운 도커 스케줄러로 조율하기"
description: ""
coverImage: "/assets/img/2024-05-27-OrchestratingPythonscriptswithalightweightdockerscheduler_0.png"
date: 2024-05-27 17:21
ogImage:
  url: /assets/img/2024-05-27-OrchestratingPythonscriptswithalightweightdockerscheduler_0.png
tag: Tech
originalTitle: "Orchestrating Python scripts with a lightweight docker scheduler"
link: "https://medium.com/@flavio-mtps/orchestrating-python-scripts-with-a-lightweight-docker-scheduler-e5d69ac9340c"
---

<img src="/assets/img/2024-05-27-OrchestratingPythonscriptswithalightweightdockerscheduler_0.png" />

안녕하세요,

요즘에 미니 PC를 사서 집에 작은 개인 서버를 세팅해봤어요 — 진짜 게이머 같은 스타일, ㅋㅋㅋ. 이 서버에서 몇 가지 개인 프로젝트를 돌릴 계획이었는데, 그래서 crontab만큼 단순하지 않은 가벼운 Python 스크립트 스케줄러가 필요했어요.

조사를 하다가 Cronicle을 발견했고, Docker에서 실행할 수 있는 프로젝트도 찾았어요.

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

![이미지](/assets/img/2024-05-27-OrchestratingPythonscriptswithalightweightdockerscheduler_1.png)

문제는... Cronicle은 기본적으로 셸 및 HTTP 요청 두 가지 유형의 작업만 지원합니다. 셸 스크립트에서 Python을 실행하는 것이 항상 최상의 경험은 아니며 (게다가 이미지에는 Python이 설치되어 있지도 않습니다. Cronicle은 Node.js에서 실행됩니다).

그래서 저는 Python 환경이 설정되고 Cronicle 내부에 사용할 준비가 된 기존 이미지를 기반으로 나만의 도커 이미지를 만들기로 결정했습니다!

```js
FROM soulteary/cronicle:0.9.46

ENV PYTHONUNBUFFERED=1

RUN apk add --no-cache python3 py3-pip

COPY bin/python-script-plugin.py /opt/cronicle/bin/python-script-plugin.py
RUN chmod +x /opt/cronicle/bin/python-script-plugin.py
COPY config/plugins.pixl /tmp/plugins.pixl
RUN /opt/cronicle/bin/control.sh import /tmp/plugins.pixl
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

내 플러그인에서는 스크립트뿐만 아니라 작업을 생성할 때 Python 라이브러리, 환경 변수, 실행 매개변수를 구성할 수 있어요. 각 Python "이벤트" (Cronicle이 작업/작업을 위한 용어로 사용하는 용어)는 자체 "런타임"에서 실행되며, 라이브러리나 환경 변수를 혼합하지 않아 모든 실행에서 무결성을 보장해요.

![이미지](/assets/img/2024-05-27-OrchestratingPythonscriptswithalightweightdockerscheduler_2.png)

개인 프로젝트(또는 소규모/중규모 전문 프로젝트)를 진행 중인 분들을 위해 Cronicle은 놀라운 오케스트레이터 대안이에요. 왜냐하면:

- 작업 실행 일정 짜기 및 연결하기
- 이메일 알림
- 작업 실행 로그, 통계, 이력 등에 접근하기 가능해요.

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

![Image](/assets/img/2024-05-27-OrchestratingPythonscriptswithalightweightdockerscheduler_3.png)

The link to my repository with the image is [here](repository_link).
Feel free to access my other repositories, I post a lot of snippets and personal projects that could help you!
