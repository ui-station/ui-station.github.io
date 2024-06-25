---
title: "편리한 클라우드 배포 Terraform과 GitHub Actions를 활용하여 AWS에서 NET API와 Angular 프론트엔드를 런칭하기 PART 23"
description: ""
coverImage: "/assets/img/2024-06-19-EffortlessclouddeploymentLaunchNETAPIwithAngularFrontendonAWSUsingTerraformandGitHubActionsPART23_0.png"
date: 2024-06-19 12:54
ogImage:
  url: /assets/img/2024-06-19-EffortlessclouddeploymentLaunchNETAPIwithAngularFrontendonAWSUsingTerraformandGitHubActionsPART23_0.png
tag: Tech
originalTitle: "Effortless cloud deployment: Launch .NET API with Angular Frontend on AWS Using Terraform and GitHub Actions [PART 2 3]"
link: "https://medium.com/@lukasksiezak/effortless-cloud-deployment-launch-net-6b454fca5ac1"
---

<img src="/assets/img/2024-06-19-EffortlessclouddeploymentLaunchNETAPIwithAngularFrontendonAWSUsingTerraformandGitHubActionsPART23_0.png" />

# 소개

이 기사에서는 이전 기사에서 시작한 설정을 계속할 것입니다: IaC 기본: Terraform 및 GitHub Actions를 사용한 RDS 배포. 목표는 AWS에서 완전히 기능하는 .NET 백엔드(API)를 구성하는 것입니다. 응용 프로그램은 공개적으로 액세스 가능하며 RDS 데이터베이스에 연결될 것입니다. 민감한 정보 검색에 Secrets Manager를 활용할 것입니다.

PART 1에서 이미 다음을 설정했습니다:

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

- Terraform을 지원하기 위한 AWS 백엔드 (S3에 상태 파일, DynamoDB에 상태 잠금 기능 포함).
- Terraform을 이용해 인프라를 배포하기 위한 GitHub Actions.
- DBeaver를 사용하여 테스트된 공개적으로 접근 가능한 RDS 데이터베이스.

PART 2에서는 다음을 다룰 예정입니다::

- RDS, ECR 및 Secrets Manager를 포함한 인프라 저장소 설정.
- Secrets Manager에 비밀을 전송하는 인프라 파이프라인 생성 (데이터베이스 호스트, 사용자, 비밀번호).
- API용 Dockerfile 정의.
- 어플리케이션을 위해 Docker 컨테이너를 실행하는 ECS 서비스 구성.
- API를 노출시키기 위해 로드 밸런서 구현.

이 글을 마치면 GitHub Actions와 Terraform을 이용해 CI/CD 파이프라인을 통해 AWS에서 .NET 백엔드를 완벽히 설정할 수 있게 될 것입니다.

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

# 인프라 구성하기

나는 논리적 구성 요소를 별도의 저장소로 분리하기로 결정했습니다. 이 접근법은 구조를 읽기 쉽게 만들 뿐만 아니라 인프라스트럭처의 코드 (IaC) 정의를 단순화합니다.

![이미지](/assets/img/2024-06-19-EffortlessclouddeploymentLaunchNETAPIwithAngularFrontendonAWSUsingTerraformandGitHubActionsPART23_1.png)

인프라 프로젝트는 자주 변경되지 않는 리소스를 만드는 것에 책임을 지기 때문에 분리됩니다. 이 프로젝트에서는 RDS, ECR 및 Secrets Manager의 생성을 구성했습니다. 파이프라인은 RDS 확장, ECR 또는 Secrets Manager 이름 변경과 같은 경우에 가끔 실행해야 할 수 있지만, 이러한 인스턴스는 드물 것입니다.

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

이 설정을 사용하여 개별 환경(개발, 스테이징 및 프로덕션과 같은)을 관리하는 데 상당한 잠재력이 있습니다. 그러나 이 기사는 이 측면을 다루지 않도록 중점을 두고 있습니다.

# App.Infra 프로젝트

이전에 언급했듯이, 인프라 프로젝트는 RDS, ECR 저장소 및 Secrets Manager(보강에 RDS 암호 저장)를 설정합니다. 이 분리는 인프라 관점에서 상대적으로 정적인 이러한 구성 요소 때문에 의도적입니다. 또한, ECR 저장소가 준비되어 있어야 하는 것이 중요합니다. Docker 이미지의 대상 역할을 합니다. 따라서 빌드 순서도 관련이 있습니다.

이제 테라폼 파일로 직접 들어가 봅시다:

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

```js
공급업체 "aws" {
  지역 = var.aws_region
}

리소스 "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"

  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = {
    Name = "main"
  }
}

리소스 "aws_subnet" "main_subnet_1" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = "eu-north-1a"

  tags = {
    Name = "main-subnet-1"
  }
}

리소스 "aws_subnet" "main_subnet_2" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.2.0/24"
  availability_zone = "eu-north-1b"

  tags = {
    Name = "main-subnet-2"
  }
}

리소스 "aws_security_group" "rds_sg" {
  name        = "rds_security_group"
  description = "어디서나 접근 허용하는 RDS용 보안 그룹"
  vpc_id      = aws_vpc.main.id

  ingress {
    from_port   = 5432
    to_port     = 5432
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "rds_security_group"
  }
}

리소스 "aws_db_subnet_group" "default" {
  name       = "main-subnet-group"
  subnet_ids = [aws_subnet.main_subnet_1.id, aws_subnet.main_subnet_2.id]

  tags = {
    Name = "main-subnet-group"
  }
}

리소스 "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id
}

리소스 "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.main.id
  }
}

리소스 "aws_route_table_association" "subnet_association_1" {
  subnet_id      = aws_subnet.main_subnet_1.id
  route_table_id = aws_route_table.public.id
}

리소스 "aws_route_table_association" "subnet_association_2" {
  subnet_id      = aws_subnet.main_subnet_2.id
  route_table_id = aws_route_table.public.id
}

리소스 "aws_db_instance" "default" {
  allocated_storage    = 20
  engine               = "postgres"
  engine_version       = "16.2"
  instance_class       = var.db_instance_class
  db_name              = var.db_name
  username             = var.db_username
  password             = var.db_password
  parameter_group_name = "default.postgres16"
  skip_final_snapshot  = true
  publicly_accessible  = true

  vpc_security_group_ids = [aws_security_group.rds_sg.id]
  db_subnet_group_name   = aws_db_subnet_group.default.name
}

리소스 "aws_ecr_repository" "app_repository" {
  name = "app-repo"
  image_scanning_configuration {
    scan_on_push = true
  }
  image_tag_mutability = "MUTABLE"
}

리소스 "aws_secretsmanager_secret" "rds_secret" {
  name        = "app-secrets-manager"
  description = "APP 비밀"
}

리소스 "aws_secretsmanager_secret_version" "rds_secret_version" {
  secret_id     = aws_secretsmanager_secret.rds_secret.id
  secret_string = jsonencode({
    DB_HOST     = aws_db_instance.default.endpoint
    DB_USER     = var.db_username
    DB_PASSWORD = var.db_password
    DB_NAME     = var.db_name
  })
}
```

위 글에서는 VPC 및 RDS 구성요소가 PART 1에서 다뤄졌습니다. 이제 ECR 및 Secrets Manager에 집중해 보겠습니다.

## ECR 리포지토리

```js
리소스 "aws_ecr_repository" "app_repository" {
  name = "app-repo"
  image_scanning_configuration {
    scan_on_push = true
  }
  image_tag_mutability = "MUTABLE"
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

이 블록은 Docker 이미지를 저장하기 위한 Elastic Container Registry (ECR) 리포지토리를 생성하며, 이미지 푸시 시 이미지 스캔이 활성화되어 있습니다.

## Secrets Manager

```js
resource "aws_secretsmanager_secret" "rds_secret" {
  name        = "app-secrets-manager"
  description = "APP secrets"
}

resource "aws_secretsmanager_secret_version" "rds_secret_version" {
  secret_id     = aws_secretsmanager_secret.rds_secret.id
  secret_string = jsonencode({
    DB_HOST     = aws_db_instance.default.endpoint
    DB_USER     = var.db_username
    DB_PASSWORD = var.db_password
    db_name     = var.db_name
  })
}
```

이 블록은 Secrets Manager를 생성하고 그 안에 RDS 데이터베이스 자격 증명을 저장합니다.

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

액션 정의는 기사의 이전 부분과 비교해 변경되지 않았습니다. 우리는 표준 경로를 실행합니다: AWS에 로그인 - `Init` - `Validate` - `Plan` - `Apply Terraform`. Github 액션을 활성화하고 실행합니다. 결과적으로 로컬 DB 클라이언트에서 RDS와 통신할 수 있어야 하며, AWS 콘솔에서 연결 문자열을 구축하기 위한 미리 입력된 시크릿이 포함된 Secrets Manager와 ECR(컨테이너 레지스트리)를 찾아야 합니다.

## C#을 사용하여 Secrets Manager에 액세스

어플리케이션의 보안을 보장하는 것은 인터넷에 노출되는 서비스를 개발하는 중요한 측면입니다. 코드나 환경 변수또는 Docker 컨테이너에 비밀번호를 저장하면 민감한 정보가 노출될 수 있습니다. 더 안전한 접근 방식은 민감한 데이터를 관리하기 위해 AWS Secrets Manager를 사용하는 것입니다.

아래에서는 AWS Secrets Manager에 저장된 시크릿을 사용하여 PostgreSQL 연결 문자열을 구성하는 방법을 보여드리겠습니다.

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

먼저 AWSSDK.SecretsManager nuget을 다운로드해야 합니다. 작업이 완료되면 SecretsManagerService를 빌드해 봅시다:

```js
using Amazon;
using Amazon.SecretsManager;
using Amazon.SecretsManager.Model;

namespace AppMonitor.AWS
{
    public class SecretsManagerService
    {
        private readonly IAmazonSecretsManager _secretsManager;

        public SecretsManagerService(IAmazonSecretsManager secretsManager)
        {
            _secretsManager = secretsManager;
        }

        public async Task<string> GetSecretValueAsync(string secretName)
        {
            var request = new GetSecretValueRequest
            {
                SecretId = secretName
            };

            var response = await _secretsManager.GetSecretValueAsync(request);
            return response.SecretString;
        }
    }
}
```

다음으로, DBConnectionStringProvider 클래스에서 SecretsManagerService를 사용합니다:

```js
using Newtonsoft.Json.Linq;
using System.Configuration;

namespace AppMonitor.AWS
{
    public class AwsDatabaseConnectionStringProvider
    {
        private readonly SecretsManagerService _secretsManagerService;

        public AwsDatabaseConnectionStringProvider(SecretsManagerService secretsManagerService)
        {
            _secretsManagerService = secretsManagerService;
        }

        public async Task<string> GetConnectionStringAsync()
        {
            var secretValue = await _secretsManagerService.GetSecretValueAsync("app-secrets-manager");
            var secretJson = JObject.Parse(secretValue); //newtonsoft.json

            var host = secretJson["DB_HOST"].ToString();
            var user = secretJson["DB_USER"].ToString();
            var password = secretJson["DB_PASSWORD"].ToString();
            var dbName = secretJson["DB_NAME"].ToString();

            return $"Host={host};Port=5432;Database={dbName};User ID={user};Password={password};";
        }
    }
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

마지막으로, 우리 스타트업에서 서비스를 설정해야 합니다 (이 스니펫은 AWSSDK.Extensions.NETCore.Setup 너겟을 필요로 합니다):

```js
  context.Services.AddAWSService<IAmazonSecretsManager>();
  context.Services.AddSingleton<SecretsManagerService>();
  context.Services.AddSingleton<AwsDatabaseConnectionStringProvider>();
```

# .NET 앱을 위한 Docker 이미지 빌드

도커는 가벼운 이식 가능한 컨테이너를 생성함으로써 응용 프로그램을 패키지화하고 배포하는 효율적인 방법을 제공합니다. 이러한 컨테이너는 쉽게 ECR로 푸시하고 AWS의 ECS 서비스로 배포할 수 있습니다. 아래는 우리 .NET 애플리케이션을 위한 Dockerfile입니다:

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

```js
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 44360

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

COPY ["AppMonitor/", "AppMonitor/"]
RUN dotnet restore "TheApp/TheApp.csproj"

COPY . .

WORKDIR "/src/TheApp"
RUN dotnet build "TheApp.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "TheApp.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .

ENV ASPNETCORE_URLS=http://*:44360
ENTRYPOINT ["dotnet", "TheApp.dll"]
```

다음 부분에서는 ECS를 구성하여 이 Docker 컨테이너를 실행하고 API를 노출하는 로드 밸런서를 구현할 것입니다.

## ECS 및 로드 밸런서 소개

Amazon Elastic Container Service (ECS)는 Docker를 사용하여 컨테이너화된 응용 프로그램을 배포, 관리 및 확장하기 쉽게 만들어주는 완전관리형 컨테이너 오케스트레이션 서비스입니다. ECS를 활용하면 기본 인프라를 관리할 필요없이 응용 프로그램을 구축하는 데 집중할 수 있습니다.

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

로드 밸런서(LB)는 여러 대상(예: ECS 작업) 사이로 들어오는 응용 프로그램 트래픽을 분산하는 서비스입니다. 로드 밸런서는 트래픽을 건강한 대상으로 라우팅하고 부하를 균형있게 분배하여 리소스 활용을 최적화하여 고가용성과 신뢰성을 보장합니다.

이 설정에서는 우리의 ECS 작업이 AWS Secrets Manager에 액세스하는 권한을 갖게되어 민감한 데이터의 안전한 관리를 보장합니다. 작업은 애플리케이션별 트래픽을 허용하도록 구성된 보안 그룹이 구성된 VPC에 배포될 것입니다. 애플리케이션 로드 밸런서(ALB)는 ECS 작업 간의 트래픽을 분산하여 부드럽고 신뢰할 수 있는 응용 프로그램 성능을 보장합니다.

## ECS를 위한 Terraform 구성

코드 예시

# main.tf

provider "aws" {
region = "eu-north-1"
}

data "aws_vpc" "main" {
filter {
name = "tag:Name"
values = ["main"]
}
}

...

resource "aws_iam_role_policy" "ecs_task_secrets_policy" {
name = "ecs-task-secrets-policy"
role = aws_iam_role.ecs_task_execution_role.id

policy = jsonencode({
Version = "2012-10-17",
Statement = [
{
Effect = "Allow",
Action = [
"secretsmanager:GetSecretValue"
],
Resource = "\*"
}
]
})
}

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

```js
# load_balancer.tf

resource "aws_lb" "app_lb" {
  name               = "app-lb"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.ecs_sg.id]
  subnets            = [data.aws_subnet.main_subnet_1.id, data.aws_subnet.main_subnet_2.id]

  enable_deletion_protection = false
}

resource "aws_lb_listener" "http_listener" {
  load_balancer_arn = aws_lb.app_lb.arn
  port              = "80"
  protocol          = "HTTP"

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.app_tg.arn
  }

  lifecycle {
    create_before_destroy = true
  }
}

resource "aws_lb_target_group" "app_tg" {
  name     = "app-tg"
  port     = 80
  protocol = "HTTP"
  vpc_id   = data.aws_vpc.main.id
  target_type = "ip"

  health_check {
    interval            = 30
    protocol            = "HTTP"
    timeout             = 5
    healthy_threshold   = 5
    unhealthy_threshold = 2
  }

  lifecycle {
    create_before_destroy = true
  }
}

resource "aws_ecs_cluster" "main" {
  name = "main-cluster"
}

resource "aws_ecs_service" "app_service" {
  name            = "app-service"
  cluster         = aws_ecs_cluster.main.id
  task_definition = aws_ecs_task_definition.app_task.arn
  desired_count   = 1
  launch_type     = "FARGATE"

  network_configuration {
    subnets         = [data.aws_subnet.main_subnet_1.id, data.aws_subnet.main_subnet_2.id]
    security_groups = [aws_security_group.ecs_sg.id]
    assign_public_ip = true
  }

  load_balancer {
    target_group_arn = aws_lb_target_group.app_tg.arn
    container_name   = "app-container"
    container_port   = 44360
  }

  depends_on = [
    aws_lb_listener.http_listener
  ]
}
```

## Provider and VPC Data Sources:

- The aws provider is set to the eu-north-1 region.
- Data sources are defined to fetch details of the existing VPC and subnets based on tags.

## Security Group for ECS:

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

- 보안 그룹 ecs_sg가 생성되어 HTTP, HTTPS 및 애플리케이션 트래픽 (포트 44360)을 허용하도록 설정되어 있습니다.

## IAM 역할 및 인스턴스 프로필:

- ECS 작업 실행 및 인스턴스 역할에 필요한 정책이 포함된 IAM 역할이 생성되었습니다.
- 인스턴스 프로필이 생성되고 인스턴스 역할에 연결되었습니다.

## 시작 구성 밑 오토 스케일링 그룹:

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

- ECS 인스턴스를 위한 ECS 최적화 AMI로 시작 구성이 생성됩니다.
- Auto Scaling 그룹이 ECS 인스턴스의 원하는 용량을 유지하도록 구성되어 있습니다.

## ECR Repository:

- ECR 저장소인 app-repo가 Docker 이미지를 가져오도록 참조됩니다.

## ECS 작업 정의:

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

- 작업 정의는 Docker 컨테이너 구성을 지정하는 것으로, 이미지, 포트 매핑 및 환경 변수를 포함합니다.
- 작업에는 AWS Secrets Manager에 액세스할 수 있도록 IAM 정책이 있습니다.

## 로드 밸런서 구성:

- 어플리케이션 로드 밸런서(ALB)가 설정되어 있으며, 리스너 및 타겟 그룹을 사용하여 ECS 작업으로의 트래픽 관리가 이루어집니다.
- 건강 점검이 구성되어 ALB가 건강한 인스턴스에만 트래픽을 라우팅하도록 합니다.

## ECS 클러스터 및 서비스:

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

- ECS 클러스터 main-cluster가 생성되었습니다.
- ECS 서비스 app-service는 Fargate 런치 유형을 사용하도록 구성되어 있어, 자동 스케일링 기능을 갖춘 서버리스 운영이 보장됩니다.

# Github 액션 정의 및 테스트

Github 액션의 워크플로우 정의는 간단하고 명확합니다. 도커 이미지 빌드 및 AWS 내 Elastic Container Registry로 푸시하는 것을 강조할 만한 유일한 사항입니다. YAML 정의로 들어가 봅시다:

```js
name: AWS로 배포

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: 코드 가져오기
        uses: actions/checkout@v2

      - name: .NET Core 설정
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: '8.0.x'

      - name: 종속성 복원
        run: dotnet restore src

      - name: 빌드
        run: dotnet build src --configuration Release --no-restore

      - name: 게시
        run: dotnet publish src --configuration Release --output ./output

      - name: 도커 빌드 설정
        uses: docker/setup-buildx-action@v1

      - name: Terraform을 위한 AWS 자격 증명 구성
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${secrets.AWS_ACCESS_KEY_ID}
          aws-secret-access-key: ${secrets.AWS_SECRET_ACCESS_KEY}
          aws-region: ${secrets.AWS_REGION}

      - name: Amazon ECR에 로그인
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v1

      - name: 도커 이미지 빌드 및 푸시
        run: |
          REPOSITORY_URI=$(aws ecr describe-repositories --repository-names app-repo --query "repositories[0].repositoryUri" --output text --region eu-north-1)
          docker build -t $REPOSITORY_URI:latest -f src/Dockerfile src
          docker push $REPOSITORY_URI:latest
        env:
          AWS_REGION: eu-north-1

      - name: Terraform 설정
        uses: hashicorp/setup-terraform@v1
        with:
          terraform_version: 1.8.5

      - name: Terraform 초기화
        run: terraform init -input=false
        working-directory: infra

      - name: Terraform 계획 수립
        run: terraform plan -input=false
        working-directory: infra
        env:
          TF_VAR_db_password: ${secrets.DB_PASSWORD}
          TF_VAR_aws_region: ${secrets.AWS_REGION}

      - name: Terraform 적용
        id: apply
        run: terraform apply -auto-approve -input=false
        working-directory: infra
        env:
          TF_VAR_db_password: ${secrets.DB_PASSWORD}
          TF_VAR_aws_region: ${secrets.AWS_REGION}
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

상기 GitHub 액션 설정은 .NET 앱을 AWS로 쉽게 배포할 수 있도록 해주는 내용입니다. 이 설정에는 .NET Core 설정부터 의존성 복원, 앱 빌드 및 게시, 그리고 컨테이너화를 위한 Docker 통합까지 모두 다루는 여러 단계가 포함되어 있습니다. 또한 AWS 자격 증명을 처리하고 Docker 이미지를 Amazon ECR에 빌드 및 푸시하며, Terraform을 사용하여 인프라 구축도 수행합니다. 기본적으로 전체 프로세스를 자동화하여 배포가 완전히 간편해집니다.

빌드가 성공적으로 완료되면 API의 URL을 얻기 위해 AWS 콘솔을 방문해보세요.

![이미지](/assets/img/2024-06-19-EffortlessclouddeploymentLaunchNETAPIwithAngularFrontendonAWSUsingTerraformandGitHubActionsPART23_2.png)

DNS 이름을 가져와 브라우저에서 해당 URL을 열어보세요. 모든 것이 잘 되었다면 API 백엔드가 응답해야 합니다.

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

# 요약

조금 길었지만, Terraform을 사용하여 AWS에 컨테이너화된 .NET API를 배포하는 모든 중요한 단계를 다루었습니다. 이 가이드가 새 프로젝트를 처음부터 시작하거나 인프라 설정의 복잡성을 탐색하는 데 도움이 되기를 바랍니다. 화이팅!
