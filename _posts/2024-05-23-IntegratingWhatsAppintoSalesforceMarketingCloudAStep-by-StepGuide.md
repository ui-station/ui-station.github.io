---
title: "Salesforce 마케팅 클라우드에 WhatsApp 통합하기 단계별 가이드"
description: ""
coverImage: "/assets/img/2024-05-23-IntegratingWhatsAppintoSalesforceMarketingCloudAStep-by-StepGuide_0.png"
date: 2024-05-23 13:06
ogImage: 
  url: /assets/img/2024-05-23-IntegratingWhatsAppintoSalesforceMarketingCloudAStep-by-StepGuide_0.png
tag: Tech
originalTitle: "Integrating WhatsApp into Salesforce Marketing Cloud: A Step-by-Step Guide"
link: "https://medium.com/@rruchi49/integrating-whatsapp-into-salesforce-marketing-cloud-a-step-by-step-guide-cc5cfe9e6b1c"
---


<img src="/assets/img/2024-05-23-IntegratingWhatsAppintoSalesforceMarketingCloudAStep-by-StepGuide_0.png" />

즉각적인 메시징 시대에 있어 WhatsApp와 Salesforce Marketing Cloud (SFMC)를 통합하면 고객과의 참여를 크게 향상시키고 커뮤니케이션을 간소화할 수 있습니다. 이 통합을 통해 기업은 WhatsApp의 대규모 사용자 기반과 실시간 커뮤니케이션 기능을 마케팅 자동화 워크플로 내에서 직접 활용할 수 있습니다. Salesforce Marketing Cloud에 WhatsApp를 통합하는 방법에 대한 포괄적인 안내서입니다.

# 단계 1: 사전 요구 사항 이해

• Salesforce Marketing Cloud 계정: 필요한 설정을 구성할 관리자 액세스 권한이 있는지 확인하세요.

<div class="content-ad"></div>

• WhatsApp 비즈니스 계정: 공식 WhatsApp 비즈니스 솔루션 제공업체(BSP)를 통해 승인된 WhatsApp 비즈니스 계정이 필요합니다. 

• Twilio 계정: Twilio는 WhatsApp를 SFMC와 통합할 수 있게 해주는 인기있는 BSP입니다. 활성화된 Twilio 계정과 API 자격 증명을 보유하고 있는지 확인해주세요.

# 단계 2: WhatsApp용 Twilio 설정하기

1. Twilio 가입: Twilio에서 계정을 생성하고 Twilio Console로 이동하세요.

<div class="content-ad"></div>

2. WhatsApp Business 신청: Twilio를 통해 WhatsApp 비즈니스 프로필을 제출하고 승인을 받으세요. 이 과정에는 비즈니스 정보 제공과 전화번호 인증이 포함됩니다.

3. API 자격 증명 획득: 승인된 후 Twilio 계정 SID, Auth Token 및 WhatsApp 활성화된 전화번호를 받으세요.

# 단계 3: Salesforce Marketing Cloud 구성

1. Salesforce Marketing Cloud에 로그인: 관리자 자격 증명을 사용하여 SFMC 대시보드에 액세스하세요.

<div class="content-ad"></div>

2. Mobile Studio로 이동하려면: SFMC 인터페이스에서 Mobile Studio로 이동합니다.

3. WhatsApp 채널 만들기:

- '관리'로 이동하고 '계정 설정'을 선택합니다.

- '채널'을 클릭하고 'WhatsApp'을 선택합니다.

<div class="content-ad"></div>

• 필요한 정보를 입력하세요. Twilio 계정 SID, 인증 토큰 및 WhatsApp 번호를 포함해야 합니다.

4. 연락처 데이터 설정: SFMC의 연락처 데이터에는 전화번호가 WhatsApp 통신용으로 형식화되어 있어야 합니다. 이는 일반적으로 전화번호 앞에 0이나 특수 문자가 없는 국제 형식을 사용하는 것을 의미합니다.

# 단계 4: WhatsApp 메시지 작성

1. 메시지 템플릿 생성: WhatsApp은 비즈니스에서 시작된 대화에 대해 사전 승인된 메시지 템플릿이 필요합니다. Twilio에서 이러한 템플릿을 생성하고 승인을 요청하세요.

<div class="content-ad"></div>

2. SFMC로 템플릿 가져오기: 승인되면 Salesforce Marketing Cloud에 이러한 템플릿을 가져옵니다.

# 단계 5: SFMC에서 WhatsApp 캠페인 만들기

1. Journey Builder: SFMC의 Journey Builder를 사용하여 WhatsApp을 커뮤니케이션 채널로 포함한 고객 여정을 만듭니다.

- 고객 여정 워크플로에 'WhatsApp 전송' 활동을 끌어다 놓습니다.

<div class="content-ad"></div>

• 미리 승인된 템플릿을 선택하고 연락처 데이터를 매핑하여 메시지를 개인화하세요.

2. 자동화 스튜디오: 고객이 취한 특정 이벤트 또는 조치에 따라 WhatsApp 메시지를 트리거하는 자동화를 설정하세요.

# 단계 6: 테스트 및 배포

1. 통합 테스트: 라이브로 이동하기 전에 메시지가 올바르게 보내지고 수신되는지 확인하기 위해 철저한 테스트를 수행하세요.

<div class="content-ad"></div>

2. 모니터링 및 최적화: 배포 후에는 SFMC의 분석 도구를 통해 WhatsApp 캠페인의 성능을 모니터링하고 최적화하세요. 참여 지표와 피드백에 기반하여 최적화하세요.

시나리오: 소매 회사가 Salesforce Marketing Cloud 내에서 마케팅 및 서비스 업무에 WhatsApp을 통합하여 고객 지원 및 참여를 개선하고자 합니다.

# 목표:

1. WhatsApp을 통해 실시간 고객 지원 제공하기.

<div class="content-ad"></div>

**솔루션:**

- 실시간 지원: 고객은 지원 문제를 위해 WhatsApp을 통해 채팅을 시작할 수 있습니다. SFMC와의 통합을 통해 고객 서비스 담당자가 고객 데이터 및 상호 작용 기록에 액세스하여 신속하고 맞춤형 지원을 제공할 수 있습니다.

<div class="content-ad"></div>

• 프로모션 및 업데이트: 주문 확인, 배송 알림 및 개인 맞춤 프로모션 제공을 위해 자동화된 WhatsApp 메시지를 보낼 수 있어 고객들이 항상 정보를 받고 참여할 수 있도록 합니다.

# 구현 단계별 순서:

## 1. WhatsApp을 위한 Twilio 설정:

## 2. SFMC 구성:

<div class="content-ad"></div>

## 3. 메시지 템플릿 만들기:

## 4. 고객 여정 구축:

## • 환영 메시지:

## • 주문 확인:

<div class="content-ad"></div>

## • 배송 알림:

## • 프로모션 제안:

• SFMC의 분석 도구를 사용하여 WhatsApp 캠페인의 성능을 추적하세요.

• 오픈률, 응답률 및 고객 피드백을 모니터링하세요.

<div class="content-ad"></div>

• 데이터 통찰을 기반으로 메시지와 워크플로우를 지속적으로 최적화하세요.

## • 맞춤화:

고객 데이터를 활용하여 WhatsApp 메시지를 맞춤화하여 참여도와 응답률을 향상시킵니다.

## • 준수:

<div class="content-ad"></div>

WhatsApp의 비즈니스 정책과 GDPR와 같은 데이터 보호 규정을 준수해주세요.

## • 세분화:

대상을 분할하여 타겟팅된 및 관련성 있는 메시지를 보내어 캠페인의 효과를 향상시켜주세요.

Follow Me:

<div class="content-ad"></div>

루치카 산돌카 (함께成長합시다) 🫱🏻‍🫲🏽

또는

매일 Salesforce 업데이트를 받기 위해 WhatsApp 커뮤니티에 참여해주세요.

참여하기 위해 스캔: 📲

<div class="content-ad"></div>

`/assets/img/2024-05-23-IntegratingWhatsAppintoSalesforceMarketingCloudAStep-by-StepGuide_1.png`를 사용하려면 이미지 태그를 Markdown 형식으로 변경하면 됩니다.