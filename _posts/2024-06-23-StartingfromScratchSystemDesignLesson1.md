---
title: "시스템 설계를 처음부터 배우기 레슨 1"
description: ""
coverImage: "/assets/img/2024-06-23-StartingfromScratchSystemDesignLesson1_0.png"
date: 2024-06-23 23:17
ogImage: 
  url: /assets/img/2024-06-23-StartingfromScratchSystemDesignLesson1_0.png
tag: Tech
originalTitle: "Starting from Scratch: System Design Lesson 1"
link: "https://medium.com/@priyasrivastava18official/starting-from-scratch-system-design-lesson-1-2fec913f6eac"
---


가장 중요한 것은 모든 시스템 디자인 질문에 대해 마음속에 일반적인 구조를 만들고 그 기본 구조를 토대로 스스로를 발전시키고 향상시켜야 한다는 것을 기억해야 합니다.

다음 설계를 살펴보고 이를 향후 레슨의 기본으로 활용해 봅시다.

너에게 너무나 압도적으로 보일지도 몰라요...

<div class="content-ad"></div>

깊게 숨을 들이펴 보세요, 단계별로 차근차근 진행할 거에요. :)

# BABY STEPS :)

- 먼저 API-Gateway로 요청이 전달되어 시스템의 입구 역할을 합니다.
- 그런 다음 로드 밸런서에 요청이 전달됩니다. 이는 한 대의 서버가 너무 많은 요청을 받지 않도록 관리하는 역할을 합니다. 요청은 다른 인스턴스로 분산되어 시스템이 올바르게 작동할 수 있도록 보장합니다.
- 분산 캐시가 사용되어 지연 시간을 줄일 수 있습니다. 항상 백엔드로 호출할 필요는 없습니다.
- 그리고 쿠버네티스, 도커 및 여러 백엔드 서비스 인스턴스를 사용하는 구조가 나옵니다. 간단히 말해 백엔드 서비스는 비즈니스 요구사항을 충족하며, 쿠버네티스는 모든 인스턴스가 켜져 있을 필요가 없는 경우 자동으로 확장을 관리합니다.
- 그런 다음 캐시를 사용할 수 있습니다. (여기서 주의해야 할 점: 캐시 무효화, 캐시 제거 정책)
- 구조적인 데이터는 RDBMS로 이동합니다.

이곳에서 리더-팔로워 아키텍처가 따라지며 (리플리케이션 전략은 높은 읽기 요구량을 처리하고 리더가 다운되었을 때 데이터 손실을 줄이는 데 사용됩니다.)

<div class="content-ad"></div>

7. 필요한 데이터는 비즈니스 로직이 요구사항을 처리하고 프로세스가 성공했을 때 서비스로 다시 전송됩니다.

7a. 데이터는 또한 ELK, Kibana, Grafana로 전송되어 관측 메트릭 및 로그 추적이 이루어집니다.

8. 9. 10.

사용자는 요청이 완료되었음을 알림받아야 합니다. 이를 위해 알림 메시지가 알림 대기열로 전송되어 사용자가 오프라인 상태라도 메시지가 지속됩니다.

<div class="content-ad"></div>

예외:

8`a-8`b

연결 문제 또는 기술적 결함으로 요청이 성공적으로 처리되지 않은 경우, 해당 요청(또는 메시지)은 다시 서비스로 전송되어 재처리됩니다.

5`a-5`b

<div class="content-ad"></div>

우리가 비구조화된 데이터를 저장해야 하는 사용 사례에서는 NoSQL 데이터베이스에 데이터를 저장하고 Kibana/Grafana/ELK로 데이터를 보내어 분석 목적이나 로그 추적에 활용합니다.

감사합니다. 저희 사람들을 계속 성장해 주세요!