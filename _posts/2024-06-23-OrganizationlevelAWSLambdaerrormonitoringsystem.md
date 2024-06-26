---
title: "조직 수준에서 AWS Lambda 오류 모니터링 시스템 구축하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-OrganizationlevelAWSLambdaerrormonitoringsystem_0.png"
date: 2024-06-23 22:36
ogImage:
  url: /assets/img/2024-06-23-OrganizationlevelAWSLambdaerrormonitoringsystem_0.png
tag: Tech
originalTitle: "Organization level AWS Lambda error monitoring system"
link: "https://medium.com/@rrakshithgr/organization-level-aws-lambda-error-monitoring-system-39bf37c92373"
---

AWS Lambda, Amazon Web Services (AWS)의 일부입니다. 이것은 응용 프로그램이 구축되고 배포되는 방식을 변경합니다. 쉬운 확장성, 비용 절감 및 간편한 관리와 같은 많은 장점을 제공합니다. Lambda를 사용하면 서버 관리 걱정 없이 코드 작성에 집중할 수 있습니다.

## 문제 설명

조직적 환경에서 Lambda 함수의 활용은 종종 광범위합니다. 예를 들어 여러 리전 및 계정에 걸쳐 여러 사용자 정의 구성 규칙을 배포하는 것은 상당수의 Lambda 함수를 초래할 수 있습니다. 우리의 경우, 15개 영역에 300개 이상의 계정에 걸쳐 배포된 50개 이상의 사용자 정의 구성 규칙이 있습니다. 이 규모에서는 이러한 함수에서 오류를 감지하는 것이 중요한 도전이 됩니다. 일반적으로 각 계정 내의 개인이 이를 인지하고 보고하는 데 의존하기 때문입니다. 이를 해결하기 위해 우리는 시스템에서 발생할 수 있는 오류를 사전에 감지하고 알림을 보내는 자동화된 시스템을 개발했습니다.

## 솔루션

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

이전에 설명한 상황을 해결하기 위한 한 가지 솔루션은 CloudWatch의 Log 그룹 수준 구독 필터라는 기능을 활용하는 것입니다. 이러한 필터를 사용하면 사용자가 CloudWatch Logs에서 로그 이벤트의 실시간 스트림에 액세스하고 해당 이벤트를 Amazon Kinesis 스트림, Amazon Data Firehose 스트림 또는 AWS Lambda와 같은 다른 서비스로 라우팅하여 사용자 지정 처리, 분석 또는 다른 시스템과의 통합을 수행할 수 있습니다. 수신 서비스로 전송된 로그 이벤트는 base64로 인코딩되어 gzip 형식으로 압축됩니다.

![이미지](/assets/img/2024-06-23-OrganizationlevelAWSLambdaerrormonitoringsystem_0.png)

명확성을 높이기 위해 이 기사를 회원 계정과 마스터 계정 두 가지 구분된 섹션으로 나눠보겠습니다:

## 회원 계정

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

우리의 상황에서는 로그 이벤트를 AWS Lambda 함수 (회원 람다로 지칭)로 라우팅할 것입니다. 이 Lambda 함수는 이벤트를 압축 해제하고 계정 ID, 로그 그룹, 지역 및 오류 메시지와 같은 필수 정보를 추출할 것입니다. 이 정보는 회원 람다에 의해 마스터 계정 내에 위치한 SQS 큐로 배치되며 교차 계정 액세스 역할을 통해 지원됩니다.

Lambda 함수를 위해 관련 로그 그룹을 모니터링하려면 로그 그룹에 구독 필터를 첨부해야 합니다.

![image](/assets/img/2024-06-23-OrganizationlevelAWSLambdaerrormonitoringsystem_1.png)

이는 대상 Lambda 역할 ARN을 지정하고 적절한 로그 형식을 선택하고 필터 패턴을 정의하는 것을 포함합니다 (사용 사례에 따라 옵션이 다를 수 있습니다). 이 경우 로그 형식으로 'other'를 선택했습니다. 필터 패턴은 임의의 로그 이벤트에서 'ERROR' 단어의 발생을 감지하도록 구성되어 있습니다 (필터 패턴을 요구 사항에 따라 맞춤화할 수 있으며 참조할 수 있는 이 링크를 참조하세요). 감지되면 지정된 Lambda 함수 (회원 람다)가 트리거됩니다.

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

지정된 필터 패턴을 구독 필터에 감지하면 Member 람다가 트리거됩니다. 그 후 람다는 이벤트를 이전에 설명한 대로 처리하며, 오류 메시지를 짧은 오류 메시지(액세스 거부 또는 지원하지 않는 작업과 같은 주요 오류 키워드가 포함된)와 긴 오류 메시지(전체 오류 메시지 포함) 두 가지 유형으로 분류합니다. 마지막으로 처리된 데이터를 중앙 SQS 큐로 전송합니다.

## 마스터 계정

마스터 람다는 중앙 SQS 큐에서 10분마다 트리거되도록 예약되어 있으며, 계정 ID, 로그 그룹 이름, 지역, 짧은 오류 메시지 및 긴 오류 메시지를 중요 정보로 추출합니다. 이 데이터는 이후에 DynamoDB 테이블에 저장되며, 로그 그룹 이름이 파티션 키이고 짧은 오류 메시지가 정렬 키로 사용됩니다. 또한 "발생 횟수"라는 속성을 1로 설정하여 동일한 오류의 발생 횟수를 나타내며, "해결 상태"라는 속성(오류의 상태를 지정)은 "발생"으로 설정됩니다.

큐에서 동일한 오류 메시지를 찾았을 때, 마스터 람다는 먼저 해당 레코드에 대해 오류가 이미 테이블에 있는지 확인합니다. 만약 있다면 해당 레코드의 발생 횟수가 1씩 증가됩니다. 그러나 테이블에서 오류를 찾을 수 없다면, 해당 오류에 대한 새 레코드가 생성됩니다.

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

DynamoDB 테이블에 로그된 오류가 있으므로 발생 빈도에 기반한 알림 시스템을 구현할 수 있습니다. 이 시스템을 통해 오류는 심각도 수준으로 분류됩니다. 예를 들어, 발생 횟수가 10 미만이면 낮은 심각도로 분류되고, 10에서 20회 사이에 발생하면 높은 심각도로 간주되며, 20에서 30회 사이에 발생하면 중요한 심각도로 전환됩니다. 중요한 임계값에 도달하면 즉시 지정된 부서나 개발자에게 경고가 전송됩니다. 이 알림 메커니즘은 SES 템플릿화된 이메일을 사용하여 구현됩니다.

![이미지](/assets/img/2024-06-23-OrganizationlevelAWSLambdaerrormonitoringsystem_2.png)

또한 하루에 한 번씩 알림 시스템을 구축할 수 있습니다. 알림 Lambda 함수가 지정된 간격으로 매일 트리거되도록 예약됩니다. 이 함수는 "발생" 해상도 상태로 표시된 DynamoDB 테이블의 오류를 검색합니다. 그런 다음, 이러한 오류를 CSV 파일로 컴파일하고 시스템에서 발생한 오류를 나타내는 SES 이메일을 전송합니다. 처리된 오류에 대한 해상도 상태를 "통보됨"으로 업데이트합니다.

## 모니터링 시스템에 참여하는 중요한 람다의 신뢰성 보장하기

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

마스터 람다, 멤버 람다 및 알림 람다와 같은 함수들은 매우 중요합니다. 이러한 함수들은 오류 없이 작동해야하며, 오류가 발생할 경우 빠른 조치를 취하기 위해 즉시 알림이 발송되어야 합니다. 이를 위해 이러한 람다 함수들의 코드를 try-catch 블록으로 캡슐화합니다. catch 블록에서 잡힌 오류의 경우, 개발자들에게 즉시 알림을 보냄으로써 신속한 조치를 취할 수 있도록 합니다. 이런 선제적인 접근은 모니터링 시스템의 신뢰성과 견고성을 향상시킵니다.

## 비용 예산 산정

![링크](/assets/img/2024-06-23-OrganizationlevelAWSLambdaerrormonitoringsystem_3.png)

## 최선의 실천 방안

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

- 코드 작성시 로거 사용하기:

- 파이썬을 사용하는 경우, 코드에 로거를 통합하여 문제를 효과적으로 추적하고 디버깅하세요. 로거는 Lambda 함수의 성능 및 동작을 모니터링하고 가치 있는 통찰력을 제공합니다.

2. 구독 필터를 추가하세요:

- 람다 함수를 배포할 때 CloudFormation 템플릿에서 생성된 로그 그룹에 구독 필터를 추가하세요. 이 설정은 로그 이벤트의 실시간 모니터링 및 필터링을 가능케 하며, 오류를 빠르게 감지하고 대응할 수 있도록 지원합니다.

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

3. 필터 가능하지 않은 오류 걸러내기:

- 해결할 수 없는 오류를 식별하고 Member 람다를 통해 SQS 대기열로 진입하는 것을 방지하세요. 이러한 오류를 미리 걸러내면 소음을 줄이고 실행 가능한 문제에 집중하여 시스템이 효율적이고 효과적으로 유지될 수 있습니다.

참고:

최근 업데이트에서 AWS가 계정 수준 구독 필터를 발표했습니다. 이 새로운 기능은 단일 계정 수준 구독 필터를 사용하여 Amazon CloudWatch Logs로 흡수되는 실시간 로그 이벤트를 Amazon Kinesis Data Stream, Amazon Kinesis Data Firehose 또는 AWS Lambda로 전달하거나 사용자 지정 처리, 분석 또는 다른 목적지로 전달할 수 있게 합니다.

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

고객들은 현재 각 로그 그룹마다 구독 필터를 설정해야 합니다. 하지만 계정 수준의 구독 필터를 사용하면, 전체 계정을 위한 단일 구독 필터 정책을 설정함으로써 여러 개 또는 모든 로그 그룹에 인입된 로그를 외부로 전달할 수 있습니다. 이는 시간을 절약하고 관리 부담을 줄여줍니다.

이 방법의 단점은 SelectionCriteria에서 모니터링에서 제외할 로그 그룹만 지정할 수 있다는 것입니다. 조직 설정에서 제외하고 싶은 로그 그룹이 많을 수 있습니다. 그 결과, 제외된 로그 그룹 목록이 계속 늘어날 것이며, 새로운 로그 그룹을 제외해야 할 때마다 자주 업데이트가 필요할 것입니다. 그래서 현재는 이 방법을 택하지 않는 이유입니다.

아래 CloudFormation 코드를 참조하세요.

![CloudFormation Code](/assets/img/2024-06-23-OrganizationlevelAWSLambdaerrormonitoringsystem_4.png)

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

블로그를 읽어주셔서 감사합니다. 더 많은 유익한 콘텐츠를 받고 싶다면 저를 Medium과 LinkedIn에서 팔로우해주세요.
