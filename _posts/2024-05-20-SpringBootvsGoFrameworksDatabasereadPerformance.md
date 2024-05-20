---
title: "스프링 부트 대 Go 프레임워크 데이터베이스 조회 성능"
description: ""
coverImage: "/assets/img/2024-05-20-SpringBootvsGoFrameworksDatabasereadPerformance_0.png"
date: 2024-05-20 15:41
ogImage: 
  url: /assets/img/2024-05-20-SpringBootvsGoFrameworksDatabasereadPerformance_0.png
tag: Tech
originalTitle: "SpringBoot vs Go Frameworks: Database read Performance"
link: "https://medium.com/deno-the-complete-reference/springboot-vs-go-frameworks-database-read-performance-80b6e159451f"
---


<img src="/assets/img/2024-05-20-SpringBootvsGoFrameworksDatabasereadPerformance_0.png" />

안녕하세요! 이 글은 요청받은 기사입니다. 독자들이 Spring Boot와 가상 스레드를 비교하고 Gin, Fiber, Echo와 같은 인기 있는 Go 프레임워크를 최신 버전으로 비교해 달라고 요청했습니다. Spring Boot는 매우 포괄적이고 다양한 기능 세트를 갖춘 프레임워크인 반면에 언급된 Go 프레임워크들은 비교적 간단하며(Express와 유사한) 기능이 간단한 것에 유의해 주세요.

이전 기사에서 우리는 가장 간단한 "hello world" 케이스에서 Spring Boot가 Go 프레임워크와 어떻게 맞붙는지 살펴보았습니다.

이번 기사에서는 좀 더 실제적인 케이스에 초점을 맞출 것입니다: 데이터베이스 조회입니다. 간단한 사용 예는 다음과 같습니다:

<div class="content-ad"></div>

- HTTP 요청 받기
- 요청 본문(JSON)에서 userEmail 매개변수 추출하기
- 추출된 이메일로 데이터베이스 조회 수행
- HTTP 응답에서 사용자 레코드 반환

# 테스트 설정

모든 테스트는 MacBook Pro M2에서 16GB RAM 및 8+4 CPU 코어를 사용하여 실행되었습니다. 소프트웨어 버전은 다음과 같습니다:

- SpringBoot 3.2.5 (Java 21.0.3)
- Go 1.22.3

<div class="content-ad"></div>


테스트는 Bombardier 로드 테스터를 사용하여 진행됩니다.

모바일 앱에서는 Bun ORM을 사용하고 있습니다 (Bun JS 런타임과 혼동하지 마세요).

애플리케이션 코드는 다음과 같습니다:

SpringBoot

<div class="content-ad"></div>

기록을 표시해왔던 다음과 같은 과정을 반복하셨습니다.

<div class="content-ad"></div>

다음과 같이 차트 형식으로 결과가 제공됩니다:

- ![이미지1](/assets/img/2024-05-20-SpringBootvsGoFrameworksDatabasereadPerformance_1.png)
- ![이미지2](/assets/img/2024-05-20-SpringBootvsGoFrameworksDatabasereadPerformance_2.png)
- ![이미지3](/assets/img/2024-05-20-SpringBootvsGoFrameworksDatabasereadPerformance_3.png)

<div class="content-ad"></div>

```markdown
![Image 1](/assets/img/2024-05-20-SpringBootvsGoFrameworksDatabasereadPerformance_4.png)

![Image 2](/assets/img/2024-05-20-SpringBootvsGoFrameworksDatabasereadPerformance_5.png)

![Image 3](/assets/img/2024-05-20-SpringBootvsGoFrameworksDatabasereadPerformance_6.png)

![Image 4](/assets/img/2024-05-20-SpringBootvsGoFrameworksDatabasereadPerformance_7.png)
```

<div class="content-ad"></div>

<img src="/assets/img/2024-05-20-SpringBootvsGoFrameworksDatabasereadPerformance_8.png" />

<img src="/assets/img/2024-05-20-SpringBootvsGoFrameworksDatabasereadPerformance_9.png" />

<img src="/assets/img/2024-05-20-SpringBootvsGoFrameworksDatabasereadPerformance_10.png" />

# 분석

<div class="content-ad"></div>

먼저, Spring Boot와 Go 프레임워크는 매우 다릅니다. Spring의 기능 세트는 간단한 Go 프레임워크에 비해 너무 방대합니다.

데이터베이스 읽기와 같이 I/O 집중적인 경우에는 Go 프레임워크가 Spring Boot에 비해 2배 빠릅니다. Go 측에서 가장 빠른 것은 38K RPS를 제공하는 Fiber입니다.

Spring Boot의 CPU 사용량은 모든 Go 프레임워크와 비교할 만큼 유사합니다. 그러나 Spring Boot의 메모리 사용량은 Go 프레임워크와 비교했을 때 너무 높습니다.

승자: Fiber

<div class="content-ad"></div>

여기에 비슷한 "hello world" 비교가 있습니다: