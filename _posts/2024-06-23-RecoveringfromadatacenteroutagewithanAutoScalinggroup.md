---
title: "Auto Scaling 그룹으로 데이터 센터 장애 복구하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-RecoveringfromadatacenteroutagewithanAutoScalinggroup_0.png"
date: 2024-06-23 22:42
ogImage:
  url: /assets/img/2024-06-23-RecoveringfromadatacenteroutagewithanAutoScalinggroup_0.png
tag: Tech
originalTitle: "Recovering from a data center outage with an Auto Scaling group"
link: "https://medium.com/@vikas.taank_40391/recovering-from-a-data-center-outage-with-an-auto-scaling-group-dcb99ac753a0"
---

이전 글에서 논의한 시스템 상태 확인 및 클라우드워치를 사용하여 EC2 인스턴스를 복구하는 것은 가능합니다.

하지만 만약 전체 데이터 센터가 전원 장애, 화재 또는 다른 재해로 인해 중단된다면 어떨까요?

## 가상 머신을 복구하는 과정에서는 동일한 데이터 센터에서 EC2 인스턴스를 시작하려고 하기 때문에 실패합니다.

AWS는 실패에 대비하여 설계되어 있어서, 전체 데이터 센터가 중단되는 이러한 드문 경우에도 대비할 수 있습니다. AWS 지역은 여러 데이터 센터가 가용 영역으로 그룹화된 것으로 구성되어 있습니다. 여러 가용 영역에 작업 부하를 분산시킴으로써 데이터 센터 장애로부터 복구할 수 있습니다.

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

다중 가용 영역을 이용한 고가용성 설정 구축 시 발생할 수 있는 두 가지 문제점이 있습니다:

- 다른 가용 영역으로 failover된 후에는 기본 설정에서 네트워크 연결 스토리지(EBS)에 저장된 데이터에 액세스할 수 없을 수 있습니다. 이 경우에는 데이터에 접근할 수 없는 경우가 발생할 수 있는데, 이는 해당 가용 영역이 온라인 상태가 될 때까지 데이터(EBS 볼륨에 저장된)에 액세스할 수 없는 상황이지만 데이터는 손실되지 않습니다.
- 다른 가용 영역에서 같은 개인 IP 주소로 새 가상 머신을 시작할 수 없습니다. 이는 서브넷이 가용 영역에 바인딩되고 각 서브넷에는 고유한 IP 주소 범위가 있기 때문입니다. 디폴트로, 회복 후에도 동일한 공용 IP 주소를 자동으로 유지할 수 없으며, 이는 CloudWatch 경보가 회복을 트리거하는 경우처럼 전에 논의한 경우와 같습니다.

# 자동 확장을 이용하여 다른 가용 영역으로 실패한 가상 머신 복구

첫 번째 독서에서 CloudWatch 경보를 사용하여 실패 시 Jenkins CI 서버를 실행 중인 가상 머신의 복구를 트리거하는 방법을 사용했습니다.

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

이 메커니즘은 필요한 경우 원본 가상 머신의 동일한 사본을 시작합니다. 이것은 가상 머신의 사설 IP 주소와 EBS 볼륨이 단일 서브넷과 단일 가용 영역에 바운드되어 있기 때문에 동일한 가용 영역에서만 가능합니다.

그러나 만약 가용 영역 장애가 발생할 경우 젠킨스 서버를 사용하여 새로운 소프트웨어를 테스트, 빌드 및 배포할 수 없다는 사실에 팀원들이 만족스럽지 않다고 가정해 보겠습니다. 다른 가용 영역에서 복구할 수 있는 도구가 필요합니다.

다른 가용 영역으로 장애 조치(failover)하는 것은 자동 확장(autoscaling)의 도움을 받아 가능합니다.

자동 확장은 EC2 서비스의 일부이며, 가용 영역이 사용 불가능한 경우에도 지정된 수의 EC2 인스턴스가 실행 중인지를 보장하는 데 도움을 줍니다. 자동 확장을 사용하여 가상 머신을 시작하고 원본 인스턴스가 실패할 경우 새 인스턴스가 시작되도록 할 수 있습니다.

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

가상 머신을 여러 서브넷에서 시작하는 데 사용할 수 있습니다. 예를 들어 전체 가용 영역의 장애가 발생하면 다른 가용 영역의 다른 서브넷에서 새 인스턴스를 시작할 수 있습니다.

필요한 경우 다른 가용 영역에서 복구할 수 있는 가상 머신을 만들기 위해 다음 명령을 실행하세요. $Password는 8~40자로 이루어진 암호로 교체해주세요:

```js
$ aws cloudformation create-stack --stack-name jenkins-multiaz \
 --template-url https:/ /s3.amazonaws.com/\
 awsinaction-code3/chapter13/multiaz.yaml \
 --parameters "ParameterKey=JenkinsAdminPassword,
 ParameterValue=$Password" \
 --capabilities CAPABILITY_IAM
```

# 오토스케일링을 구성하려면 구성의 다음 두 부분을 만들어야 합니다:

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

- 런치 템플릿에는 EC2 인스턴스를 시작하는 데 필요한 모든 정보가 포함되어 있습니다: 인스턴스 유형(가상 머신의 크기) 및 시작할 이미지(AMI)입니다.
- 오토 스케일링 그룹은 EC2 서비스에게 특정 런치 템플릿으로 시작해야 할 가상 머신의 수, 인스턴스를 모니터링하는 방법 및 EC2 인스턴스를 시작해야 하는 서브넷 등을 알려줍니다.

읽어주셔서 감사합니다. 다음에는 오토 스케일링 그룹에 대해 더 자세히 알아보고 런치 템플릿의 다양한 매개변수에 대해 논의할 예정입니다.
