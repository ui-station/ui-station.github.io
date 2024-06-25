---
title: "테라폼 인터뷰 질문들"
description: ""
coverImage: "/assets/img/2024-06-19-TerraformInterviewQuestions_0.png"
date: 2024-06-19 13:37
ogImage:
  url: /assets/img/2024-06-19-TerraformInterviewQuestions_0.png
tag: Tech
originalTitle: "Terraform Interview Questions"
link: "https://medium.com/@dksoni4530/terraform-interview-questions-4988bedcec80"
---

먼저, 아래 Markdown 형식으로 표 태그를 변경하실 수 있습니다.

![image](/assets/img/2024-06-19-TerraformInterviewQuestions_0.png)

Q1: 테라폼을 사용하여 ec2 인스턴스를 만들었다고 가정해보겠습니다. 그리고 생성 후, 상태 파일에서 해당 항목을 제거했습니다. 그럼, terraform apply를 실행하면 어떻게 될까요?

- 상태 파일에서 항목을 제거했기 때문에 terraform은 더 이상 해당 리소스를 관리하지 않게 됩니다. 따라서, 다음 apply에서는 새로운 리소스를 생성하게 됩니다.

Q2: Terraform에서 상태 파일(State file)이란 무엇인가요?

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

- 상태 파일은 Terraform이 배포한 모든 인프라를 추적하는 파일입니다.

Q3: 테라폼 상태 파일을 저장하는 가장 좋은 방법은 무엇인가요?

- 상태 파일을 저장하는 가장 좋은 방법은 S3 또는 GitLab 관리 테라폼 상태와 같은 원격 백엔드에 유지하는 것입니다. 이렇게 하면 여러 사람이 동일한 코드 자원에 작업할 때 자원 중복이 발생하지 않습니다.

Q4: 테라폼 상태 잠금이란 무엇인가요?

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

- 테라폼 코드를 작성할 때 terraform plan, apply 또는 destroy를 실행하면 테라폼이 파괴적인 작업을 방지하기 위해 상태 파일을 잠근다.

Q5: 테라폼 백엔드란 무엇인가요?

- 백엔드는 테라폼이 상태 데이터 파일을 저장하는 위치를 정의합니다. 테라폼은 관리하는 리소스를 추적하기 위해 지속적인 상태 데이터를 사용합니다.

Q6: 테라폼에서 플러그인이란 무엇인가요?

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

- 플러그인은 HCL 코드를 API 호출로 변환하고 해당 공급업체(AWS, GCP)에 요청을 보내는 역할을 합니다.

Q7: 널 리소스(null resource)란 무엇인가요?

- 널(null)이라는 접두어가 붙어 있는 것을 보니 이 리소스는 클라우드 인프라에 존재하지 않을 것을 의미합니다.
- Terraform의 null_resource는 다음과 같은 시나리오에서 사용할 수 있습니다 -
- 쉘 명령 실행
- 로컬 프로비저너 및 원격 프로비저너와 함께 사용할 수 있습니다.
- Terraform 모듈, Terraform count, Terraform 데이터 소스, 로컬 변수와 함께 사용할 수 있습니다.
- 출력 블록에도 사용할 수 있습니다.

Q8: 프로비저너의 종류는 무엇이 있나요?

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

- 원격 exec: 원격 서버에서 Terraform을 사용하여 명령 실행
- 로컬 exec: 로컬 시스템에서 Terraform을 사용하여 명령 실행

Q9: Terraform 모듈의 사용 목적은 무엇인가요?

- 한 번만 terraform 모듈을 생성하고 필요할 때마다 재사용할 수 있습니다.
- 코드를 표준화하려고
- 코드 중복을 줄이려고
- 모듈을 버전별로 관리할 수 있습니다.

Q10: Terraform으로 EC2와 VPC를 생성했는데 불행하게도 tfstate 파일이 삭제되었습니다. 복구할 수 있나요? (파일은 S3나 Dynamo DB에 있지 않고 로컬 머신에만 있음)

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

- Terraform import 명령을 사용하여 Terraform에 의해 생성된 리소스를 가져올 수 있습니다. 그러면 해당 리소스가 상태 파일에 저장됩니다.

Q11: 만약 VPC, EC2, 보안 그룹, 액세스 키, 서브넷과 같이 여러 모듈을 생성했다면, Terraform은 어떻게 첫 번째로 배포할 리소스를 알 수 있을까요?

- Terraform은 코드 내의 리소스 참조를 기반으로 종속성 그래프를 자동으로 파악합니다. 리소스간의 관계를 이해하고, 이 정보를 사용하여 리소스를 생성 또는 수정해야 할 순서를 결정합니다.
- depends_on 키워드를 사용하여 명시적인 종속성을 정의할 수 있습니다.

Q12: 어떻게 로직을 변경하지 않고 특정 리소스를 삭제/파괴할 수 있을까요?

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

- taint과 destroy 명령어 사용하기
- 해당 리소스를 taint하려면 terraform taint RESOURCE_TYPE.RESOURCE_NAME 명령어를 사용해야 합니다.
- 리소스를 taint한 후, tainted된 리소스를 제거하기 위해 terraform destroy -target=RESOURCE_TYPE.RESOURCE_NAME 명령어를 실행할 수 있습니다.

Q13: 테라폼에서 리소스 이름을 변경하는 방법은 무엇인가요?

- 테라폼 mv 명령어를 사용하여 리소스 이름을 변경할 수 있습니다.

Q14: 테라폼을 사용하여 EC2 인스턴스를 생성했는데, 누군가 수동으로 변경을 했다면, 다음에 Terraform plan을 실행하면 무엇이 발생하나요?

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

- Terraform 상태가 일치하지 않으면 Terraform이 EC2 인스턴스를 원하는 상태로 수정합니다. 즉, .tf 파일에 정의한 것과 일치하게 됩니다.

Q15: Terraform에서 locals와 variables의 차이점은 무엇인가요?

- 변수는 variables.tf 파일에 정의되거나 variables 키워드를 사용하여 정의됩니다. 변수는 덮어쓸 수 있지만 로컬 변수는 덮어쓸 수 없습니다.
- 따라서 변수를 덮어쓰지 못하게 제한하려면 locals을 사용해야 합니다.
