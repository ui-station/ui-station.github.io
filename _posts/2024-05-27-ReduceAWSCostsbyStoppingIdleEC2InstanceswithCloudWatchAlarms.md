---
title: "AWS 비용을 절감하는 방법 CloudWatch 알람을 사용하여 유휴 상태의 EC2 인스턴스 중지하기"
description: ""
coverImage: "/assets/img/2024-05-27-ReduceAWSCostsbyStoppingIdleEC2InstanceswithCloudWatchAlarms_0.png"
date: 2024-05-27 16:57
ogImage:
  url: /assets/img/2024-05-27-ReduceAWSCostsbyStoppingIdleEC2InstanceswithCloudWatchAlarms_0.png
tag: Tech
originalTitle: "Reduce AWS Costs by Stopping Idle EC2 Instances with CloudWatch Alarms"
link: "https://medium.com/@meriemiag/reduce-aws-costs-by-stopping-idle-ec2-instances-with-cloudwatch-alarms-ecca40ebf85a"
---

클라우드 리소스를 효율적으로 관리하는 것은 클라우드 서비스를 이용하는 사람에게 매우 중요합니다, 특히 비용을 통제하는 측면에서 말이죠. 한 가지 흔한 문제는 개발, 테스트 또는 임시 작업을 위해 사용하는 EC2 인스턴스를 중지하는 것을 잊는 경우입니다. 이러한 실수는 뜻밖에 높은 청구서를 유발할 수 있습니다.

제 경우에는 주로 테스트 목적으로 특정 도구를 설치한 EC2 인스턴스를 갖고 있습니다. 이 인스턴스는 짧은 기간 동안 사용됩니다. 작업을 완료한 후 인스턴스를 일반적으로 중지합니다. 그러나 때로는 산만해지거나 다른 작업으로 이동하여 그것을 잊어버리곤 합니다 😓. 다시 돌아와서 그것이 계속 실행 중인 것을 발견하면 놀라곤 합니다, 그로 인해 청구서가 높아지죠 😲.

이 문제를 해결하기 위해, 나만이 중지할 것을 기억하도록 의존할 수 없다는 것을 깨달았고 자동화해야 한다는 것을 이해했습니다. 발견한 해결책은 1시간 동안 활동이 감지되지 않으면 인스턴스를 자동으로 중지하는 CloudWatch 경보를 생성하는 것입니다. 이렇게 하면 실제 사용한 만큼만 지불하게 됩니다.

이 글에서는 이 해결책을 여러분과 공유하려 합니다. CloudWatch 경보를 소개하는 것부터 시작하여 비활성 EC2 인스턴스를 자동으로 중지하기 위해 AWS 관리 콘솔 및 Terraform을 사용하여 경보를 구성하는 단계별 안내서를 제공할 것입니다.

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

# Cloudwatch Alarm

Amazon CloudWatch은 AWS의 모니터링 서비스입니다. 이 서비스는 AWS 서비스, 사용자 정의 응용 프로그램 및 온프레미스 응용 프로그램에서 수집된 메트릭과 로그를 수집하는 중앙 저장소 역할을 합니다. CloudWatch 중요 기능 중 하나는 수집된 데이터를 기반으로 알람을 구성할 수 있는 CloudWatch 알람입니다.

CloudWatch 알람은 단일 메트릭의 값(단일 또는 복합)을 지정한 시간 동안 확인하고, 정의한 임계값에 도달하면 지정한 작업을 실행합니다.

CloudWatch 알람을 이해할 때, 필로우스트림을 모니터링하는 보안 카메라로 상상해보세요. 이 카메라의 역할은 문의 상태를 추적하는 것입니다. 문이 열려 있는지 닫혀 있는지를 확인합니다. 카메라가 문이 너무 오래 열려 있는 등 이상한 상황을 감지하면 알림을 트리거하여 경고합니다. 마찬가지로, CloudWatch 알람은 CPU 사용량과 같은 AWS 리소스의 특정 메트릭을 모니터링합니다. 메트릭이 미리 정의된 임계값을 초과하면(문이 너무 오래 열려 있는 것처럼) 알람이 트리거되어 문제를 알리고 조치를 취할 수 있도록 알려줍니다.

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

# CloudWatch 알람의 주요 구성 요소

- 메트릭: 메트릭은 시간이 지남에 따라 모니터링하는 성능 데이터입니다. 시간이 지남에 따라 다른 값을 갖는 변수처럼, 이러한 값의 변동은 응용 프로그램이 원래대로 작동하는지 확인하기 위한 결정을 내릴 때 중요합니다. 메트릭에는 CPU 사용량, 디스크 I/O, 메모리 사용량 및 사용자 정의 응용 프로그램 메트릭 등이 포함될 수 있습니다. 이러한 메트릭은 정기적으로 수집되고 CloudWatch에 저장됩니다.
- 임계값: 이것은 메트릭 데이터가 평가되는 값을 의미합니다. 예를 들어, CPU 사용량의 70%를 임계값으로 설정할 수 있습니다. 메트릭 값이 임계값보다 작거나 같거나 더 클지에 따라, 액션을 트리거할 수 있습니다.
- 기간(초): 기간은 메트릭 값이 수집되는 빈도를 결정합니다. 이는 필요에 따라 몇 초에서 몇 시간까지 범위가 될 수 있습니다.
- 통계량: 이것은 각 기간에 대해 메트릭 데이터가 어떻게 집계되는지를 지정합니다. 일반적인 통계량으로는 평균, 합계, 최소값 및 최대값이 있습니다. 메트릭이 매 분마다 수집되고 알람 기간을 5분으로 설정한 경우, 5분 동안 수집된 5개 값의 평균이 각 기간에 대한 메트릭 값으로 계산됩니다.
- 평가 기간: 알람의 상태를 평가하는 데 고려할 최근 기간 수입니다.
- 알람 데이터포인트: 알람을 트리거하려면 메트릭이 임계값을 위반해야 하는 평가 기간 수입니다. 예를 들어, 3개의 평가 기간이 있고 2개의 알람 데이터포인트를 설정한 경우, 임계값은 3개 기간 중 적어도 2개에서 위반되어야 알람이 트리거됩니다.
- 알람 액션: 알람 상태가 변경될 때 취해지는 조치입니다. 이는 Amazon SNS를 통해 알림을 보내거나 EC2 인스턴스를 중지, 종료 또는 다시 부팅하거나 AWS Lambda 함수를 호출하거나 Auto Scaling 작업을 수행하는 것 등이 포함될 수 있습니다. 단일 알람에 여러 액션을 구성할 수 있습니다.

# CloudWatch 알람 예시

각 CloudWatch 알람의 구성 요소의 역할과 시간 경과에 따른 알람 상태 평가를 설명하는 예시를 살펴보겠습니다.

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

알람 예제는 EC2 인스턴스의 평균 CPU 사용률을 모니터링하도록 설정되어 있습니다. CPUUtilization 메트릭은 5분 간격으로 수집되고 집계됩니다. 알람은 연속적으로 3개의 기간 동안의 평균 CPU 사용률을 평가합니다. 평균 CPU 사용률이 최소 2개의 평가 기간 중에서 70%의 임계값을 초과하면 알람이 정의된 작업을 트리거합니다.

- 메트릭 : CPUUtilization
- 통계 : 평균
- 기간 : 300초 (5분)
- 임계값 : 70%
- 알람 조건 : 크거나 같음
- 평가 기간 : 3
- 알람에 필요한 데이터포인트 : 2

아래 표는 알람이 시간이 지남에 따라 어떻게 트리거되는지 보여줍니다 :

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

이 예에서는 평균 CPU 이용률이 70% 임계 값을 기간 3에서 초과하지만 알람이 트리거되지 않습니다. 이는 임계 값을 3 개의 평가 기간 중 1 회만 초과하기 때문입니다 (65-60-70).평균 CPU 이용률이 70% 임계 값을 다시 초과하는 시점은 기간 5로, 이는 평가 기간에서 2 번째 초과입니다 (70-60-75). 따라서 알람이 트리거되고 ALARM 상태로 들어갑니다.

# 비활성 인스턴스를 자동으로 중지시키기 위한 CloudWatch 알람 설정

![CloudWatch 알람을 사용하여 유휴 EC2 인스턴스 중단으로 AWS 비용 절감하기](/assets/img/2024-05-27-ReduceAWSCostsbyStoppingIdleEC2InstanceswithCloudWatchAlarms_1.png)

나는 비활성 인스턴스를 감지하는 데 사용할 메트릭으로 CPUUtilization을 선택했습니다. 이 메트릭은 기본적으로 5분마다 수집되며 CloudWatch로 전송됩니다. 알람은 1시간 동안 (5분씩 12회) CPU 이용률이 2% 미만으로 유지된 경우에만 트리거됩니다. 알람에 의해 트리거된 작업은 인스턴스 중지 및 이메일 통지입니다.

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

- 메트릭: CPUUtilization
- 통계: 평균
- 기간: 300초 (5분)
- 임계값: 2%
- 경보 조건: 이하
- 평가 기간: 12
- 경보 발생 데이터 포인트: 12
- 조치: SNS 이메일 알림 + EC2 인스턴스 중지

# 콘솔을 이용한 해결 방법

## 단계 1. 메트릭 및 조건 지정

모니터링해야 하는 인스턴스의 CPUUtilization 메트릭을 선택한 후 경보 조건을 지정하세요.

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

![AWS Cost Reduction Step 2](/assets/img/2024-05-27-ReduceAWSCostsbyStoppingIdleEC2InstanceswithCloudWatchAlarms_2.png)

![AWS Cost Reduction Step 3](/assets/img/2024-05-27-ReduceAWSCostsbyStoppingIdleEC2InstanceswithCloudWatchAlarms_3.png)

![AWS Cost Reduction Step 4](/assets/img/2024-05-27-ReduceAWSCostsbyStoppingIdleEC2InstanceswithCloudWatchAlarms_4.png)

![AWS Cost Reduction Step 5](/assets/img/2024-05-27-ReduceAWSCostsbyStoppingIdleEC2InstanceswithCloudWatchAlarms_5.png)

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

## 단계 2. 작업 구성

첫 번째 작업은 이메일 구독이 있는 SNS 주제로 알림을 보내는 것입니다. 이렇게 하면 알람이 인스턴스를 중지할 때 알림을 받을 수 있습니다. 이 단계에서 SNS 주제를 생성할 수도 있고, 이미 생성한 경우 기존 주제를 참조할 수도 있습니다.

두 번째 작업은 인스턴스를 중지하는 것입니다.

![AWS 비용 절감 이미지](/assets/img/2024-05-27-ReduceAWSCostsbyStoppingIdleEC2InstanceswithCloudWatchAlarms_6.png)

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

![이미지](/assets/img/2024-05-27-ReduceAWSCostsbyStoppingIdleEC2InstanceswithCloudWatchAlarms_7.png)

## 단계 3. 이름 및 설명 추가

알람에 이름을 지정하고 설명도 추가할 수 있습니다.

![이미지](/assets/img/2024-05-27-ReduceAWSCostsbyStoppingIdleEC2InstanceswithCloudWatchAlarms_8.png)

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

## 단계 4. 미리보기 및 생성

모든 구성 요약을 확인하세요. 모든 것이 올바르다면 경보 생성을 확인하십시오.

결과는 아래 스크린샷과 같아야 합니다.

<img src="/assets/img/2024-05-27-ReduceAWSCostsbyStoppingIdleEC2InstanceswithCloudWatchAlarms_9.png" />

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

알람은 새로 생성되었을 때이거나 지정된 기준을 바탕으로 알람 상태를 결정하기에 충분한 데이터가 없을 때 상태가 "데이터 부족"으로 표시될 수 있습니다.

경고 메시지는 다음과 같습니다: "이 작업은 확인 대기 중인 엔드포인트가 있는 SNS 주제로 메시지를 보냅니다. 엔드포인트가 확인될 때까지 예상대로 작동하지 않을 수 있습니다. SNS에서 구독 확인 이메일을 찾거나 주제를 검토하세요." 해당 메시지는 생성된 SNS 주제에 대한 이메일 구독을 확인하는 것에 관한 것입니다. SNS 주제를 생성할 때 제공한 이메일 주소로 수신한 이메일을 열고 구독을 확인해야 합니다.

![이미지](/assets/img/2024-05-27-ReduceAWSCostsbyStoppingIdleEC2InstanceswithCloudWatchAlarms_10.png)

구독을 확인한 후에 알람이 지정된 메트릭에 도달하면 아래 스크린샷과 같은 결과를 얻어야 합니다. 모든 것이 잘 작동합니다.

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

![CloudWatch Alarm Screenshot 1](/assets/img/2024-05-27-ReduceAWSCostsbyStoppingIdleEC2InstanceswithCloudWatchAlarms_11.png)

CloudWatch 알람이 올바르게 설정되어 있고 조건을 충족하면 작업을 트리거할 준비가 되어 있습니다. 이 경우 아래 스크린샷과 같은 결과를 볼 수 있습니다.

![CloudWatch Alarm Screenshot 2](/assets/img/2024-05-27-ReduceAWSCostsbyStoppingIdleEC2InstanceswithCloudWatchAlarms_12.png)

![CloudWatch Alarm Screenshot 3](/assets/img/2024-05-27-ReduceAWSCostsbyStoppingIdleEC2InstanceswithCloudWatchAlarms_13.png)

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

# 테라폼을 사용한 솔루션

클라우드워치 알람을 빠르고 자동화되며 일관된 방식으로 설정하고 싶다면, 테라폼을 사용하는 것이 훌륭한 선택입니다. 아래에는 클라우드워치 알람을 설정하는 데 사용되는 주요 파일인 cloudwatch_alarm.tf와 sns.tf 파일을 제시합니다. 전체 테라폼 코드는 제 GitHub 저장소에서 확인할 수 있습니다.

```js
resource "aws_cloudwatch_metric_alarm" "ec2_cpu_alarm" {
  comparison_operator       = "LessThanOrEqualToThreshold"
  evaluation_periods        = "12"
  datapoints_to_alarm       = "12"
  metric_name               = "CPUUtilization"
  namespace                 = "AWS/EC2"
  period                    = "300" #초
  statistic                 = "Average"
  threshold                 = "2"
  alarm_description         = "이 알람은 EC2 인스턴스가 1시간 동안 비활성 상태로 유지될 경우 중지됩니다."
  treat_missing_data        = "missing"
  insufficient_data_actions = []
  alarm_actions             = [aws_sns_topic.topic.arn, "arn:aws:automate:${var.region}:ec2:stop"]

  alarm_name = "stop_test_instance_alarm"

  dimensions = {
    InstanceId = var.instance_ID
  }
}
```

```js
resource "aws_sns_topic" "topic" {
  name = "stop_test_instance_alarm_topic"
}

resource "aws_sns_topic_subscription" "topic_email_subscription" {
  topic_arn = aws_sns_topic.topic.arn
  protocol  = "email"
  endpoint  = var.email_address
}
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

모든 리소스가 생성된 후에는, 생성된 SNS 주제의 구독을 확인해야 합니다. 받은 이메일 안내에 따라 (콘솔 솔루션에서 설명한 대로) 구독을 확인해주세요.

이 기사가 EC2 인스턴스를 효율적으로 관리하여 불필요한 사용을 방지하는 데 도움이 되길 바랍니다.
