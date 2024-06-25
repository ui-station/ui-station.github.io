---
title: "건축 알아야 할 필수 요약 정보"
description: ""
coverImage: "/assets/img/2024-06-23-ArchitectureThecheatsheet_0.png"
date: 2024-06-23 22:08
ogImage:
  url: /assets/img/2024-06-23-ArchitectureThecheatsheet_0.png
tag: Tech
originalTitle: "Architecture : The cheat sheet"
link: "https://medium.com/scub-lab/architecture-patterns-the-cheat-sheet-e8b5386f4b4b"
---

![Architecture Patterns](/assets/img/2024-06-23-ArchitectureThecheatsheet_0.png)

이 논문은 다양한 소프트웨어 아키텍처 패턴, 모델, 철학 및 전략에 대한 간결한 요약을 제공하여 이러한 패턴의 독특한 특성, 응용 및 소프트웨어 디자인에 미치는 영향에 대한 통찰을 제공합니다. 이러한 패턴은 현대 소프트웨어 공학에서 주요 접근 방식과 전략을 대표하며 각각 특정 요구 사항과 도전에 대응합니다. 이 패턴은 특정 필요에 가장 적합한 접근 방법을 선택하는 아키텍트와 개발자를 지원하는 것을 목표로 합니다.

소프트웨어 아키텍처 패턴은 소프트웨어 개발 중복잡한 아키텍처적 도전에 대응하기 위해 사용되는 기본 지침입니다. 이는 반복되는 문제에 대한 구조화된 솔루션을 제공하여 효율성, 확장성 및 유지보수 가능성을 보장합니다.

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

## Backend For Frontend (BFF)

개별 프런트엔드 애플리케이션의 요구 사항에 맞춘 특정 백엔드 서비스를 생성하여 통신 및 데이터 전달을 최적화하는 것을 포함합니다.

주요 포커스: 특정 프런트엔드 애플리케이션에 맞춘 백엔드 서비스 생성.

장점: 통신 및 데이터 전달을 최적화합니다.

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

테이블 태그를 Markdown 형식으로 변경해주세요.

## Trade-offs

- 중복 로직 발생 가능
- 추가 유지보수 필요

## 발행/구독 패턴

주요 내용: 메시징 시스템에서 생성자와 소비자 간의 결합도 낮춤.

장점: 확장성 향상 및 시스템 유연성 제고, 비동기 통신 지원.

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

## 트레이드오프: 메시지 관리 복잡성 증가, 브로커 신뢰에 의존.

## 사이드카 패턴

주요 응용 프로그램 기능을 향상하거나 확장하는 데 중점을 둡니다.

혜택: 기능을 격리하고 모듈화하며, 유지 보수가 쉬워집니다.

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

테이블 태그를 Markdown 형식으로 변경해보세요.

| Trade-offs              | Can increase system complexity, additional resource consumption. |
| ----------------------- | ---------------------------------------------------------------- |
| **Data-Driven Testing** |                                                                  |
| **Focus**               | Enhancing testing processes by using data-driven methodologies.  |
| **Benefits**            | Increases test coverage, improves efficiency.                    |

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

타협: 철저한 데이터 관리가 필요하며, 데이터 관련 오류가 발생할 수 있습니다.

## 회로 차단기

분산 시스템의 실패를 고상하게 처리하고, 고장이 전파되지 않도록 막아주는 방법을 제공합니다. 전기 회로 차단기처럼 작동하여, 서비스 고장 시 요청의 흐름을 중지시켜 회복을 허용합니다.

주요 기능: 분산 시스템에서 고장 처리.

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

혜택: 서비스들이 복구할 시간을 확보하여 연쇄 실패를 방지합니다.

희생 요소: 잘못된 경계 값을 피하기 위해 신중한 임계값 설정이 필요합니다.

## API 게이트웨이

클라이언트로부터 요청을 관리하고 라우팅하는 중간 계층 역할을 하는데, 통합된 인터페이스, 보안 및 기타 횡단 관심사를 제공하여 다양한 서비스로 요청을 라우팅합니다.

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

테이블 태그를 마크다운 형식으로 변경해주세요.

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

읽기 및 쓰기 작업을 구별하여 별도의 모델로 최적화하여 성능과 확장성을 향상시킵니다, 특히 복잡하고 고수요 환경에서 특히 유용합니다.

핵심: 읽기 및 쓰기 작업을 분리합니다.

혜택: 성능과 확장성을 최적화합니다.

타협: 복잡성을 더하며, 최종 일관성 문제를 발생시킬 수 있습니다.

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

## 아웃박스 패턴

분산 시스템에서 신뢰할 수 있는 메시지 전달을 보장하는 도구로, 특히 마이크로서비스 아키텍처에서 메시지를 전달하기 전에 일시적으로 저장합니다.

주요 포인트: 분산 시스템에서 신뢰할 수 있는 메시지 전달을 보장합니다.

이점: 전송 실패 중에 데이터 유실을 방지합니다.

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

Trade-offs: 구현 복잡성 추가, 지연 가능성이 발생할 수 있습니다.

## 멀티 테넌시

Keycloak를 사용하여 인증을 수행하고, Angular와 Springboot를 각각 프론트엔드 및 백엔드 개발에 사용하여 멀티 테넌트 시스템을 구현하는 방법에 대해 논의합니다.

중점: 특정 기술을 사용하여 멀티 테넌트 시스템을 구현하는 것입니다.

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

혜택: SaaS 애플리케이션에서 효율적인 인증 관리.

희생해야 할 점: 복잡한 설정, 여러 기술 통합이 필요함.

## 아키텍처 안티 패턴

소프트웨어 아키텍처에 대한 일반적인 함정과 ‘안티 패턴’을 강조하여 건강하고 효율적인 소프트웨어 시스템을 유지하기 위해 피해야 할 내용에 대한 통찰을 제공합니다.

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

# 아키텍처 도구

이 섹션에서는 아키텍처 실무를 향상시키기 위해 사용되는 모델, 철학 및 전략에 대해 설명합니다. 이들은 패턴으로 간주되지 않지만 여전히 훌륭한 시스템을 구축하는 데 매우 유용합니다.

## C4 모델

소프트웨어 아키텍처의 포괄적인 시각화를 제공하는 데 초점을 맞추며, Context, Containers, Components 및 Code 네 가지 수준으로 분해합니다. 서로 다른 추상화 수준에서 소프트웨어 구조를 이해하고 소통하는 데 도움이 됩니다.

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

중점: 소프트웨어 아키텍처 시각화 - 네 가지 수준을 통해

혜택: 소프트웨어 구조의 이해와 커뮤니케이션 강화

Trade-offs: 작은 시스템에는 너무 복잡할 수 있음

## Domain-Driven Design (DDD)

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

소프트웨어 디자인을 도메인 복잡성에 밀접하게 맞추며, 모델 주도 접근 방식을 사용합니다. 기술 및 도메인 전문가 간의 협업을 강조하여 공통 언어와 공유된 이해를 만들어냅니다.

중점: 소프트웨어 디자인을 도메인 복잡성에 맞추기

장점: 협업을 용이하게 하고 공통 언어를 만들어냄

불리한 점: 도메인에 대한 심층적인 이해가 필요하며, 복잡할 수 있음

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

## Strangler Pattern

레거시 시스템의 일부를 점진적으로 대체하여 새로운 기술로의 원활한 이행 및 업데이트를 가능하게 하는 것을 목표로 합니다.

주요 내용: 레거시 시스템의 점진적인 대체

혜택: 기존 기능을 중단하지 않고 점진적 업데이트가 가능합니다.

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

Trade-offs: 느리고 자원을 많이 소비할 수 있습니다.

저는 디지털 회사의 CTO이자 건축 부서장입니다. 기술 전략의 개발, 솔루션 설계 및 R&D 프로젝트를 주도적으로 진행하고 있습니다.

읽어주셔서 감사합니다! 이 기사가 마음에 드셨다면, 👏을 눌러주시고 다른 사람들이 이를 발견할 수 있도록 도와주세요. 아래 댓글 섹션에 의견을 공유해주세요.

# Scub Lab

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

우리 커뮤니티의 일원이 되어 주셔서 감사합니다! 떠나기 전에:

- 작가에게 박수를 보내고 팔로우해주세요! 👏
- lab.scub.net에서 더 많은 콘텐츠를 찾아보세요! 🚀
- 무료 주간 뉴스레터에 가입해주세요. 🗞️
- 트위터(X), 링크드인, 그리고 우리의 웹사이트에서 우리를 팔로우해주세요.
