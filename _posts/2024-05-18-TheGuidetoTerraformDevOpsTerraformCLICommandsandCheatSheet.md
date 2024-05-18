---
title: "테라폼 DevOps 안내서 테라폼 CLI 명령어 및 치트 시트"
description: ""
coverImage: "/assets/img/2024-05-18-TheGuidetoTerraformDevOpsTerraformCLICommandsandCheatSheet_0.png"
date: 2024-05-18 17:01
ogImage: 
  url: /assets/img/2024-05-18-TheGuidetoTerraformDevOpsTerraformCLICommandsandCheatSheet_0.png
tag: Tech
originalTitle: "The Guide to Terraform DevOps: Terraform CLI Commands and Cheat Sheet"
link: "https://medium.com/towards-aws/the-guide-to-terraform-devops-terraform-cli-commands-and-cheat-sheet-581a5b029b53"
---


![이미지](/assets/img/2024-05-18-TheGuidetoTerraformDevOpsTerraformCLICommandsandCheatSheet_0.png)

# 목차

- 목차
- Terraform CLI 및 명령어 치트 시트
  - 초기화:
  - 구성 관리:
  - 인프라 관리:
  - 상태 관리:
  - 워크스페이스:
  - 로그:
  - 로그 레벨 활성화 및 제어:
  - 로그 파일 지정:
  - 셸 명령으로 로그 캡처:
  - 다른 유용한 명령어:
  - 추가 팁:
- 결론
- 나에 대해
- 참고 문헌

# Terraform CLI 및 명령어 치트 시트

<div class="content-ad"></div>

테라폼 명령어는 인프라를 자동으로 프로비저닝하고 관리합니다. 이를 통해 수동으로 구성 작업을 줄이고 시간을 절약하며 오류를 줄일 수 있습니다.

테라폼은 인프라를 코드로 관리하기 위한 다양한 명령어를 제공합니다.

아래는 자주 사용되는 명령어에 대한 빠른 참조 가이드입니다:

<div class="content-ad"></div>

## 초기화:

- terraform init: 플러그인을 다운로드하고 백엔드 구성을 수행하여 작업 디렉토리를 초기화합니다.

![이미지](/assets/img/2024-05-18-TheGuidetoTerraformDevOpsTerraformCLICommandsandCheatSheet_2.png)

![이미지](/assets/img/2024-05-18-TheGuidetoTerraformDevOpsTerraformCLICommandsandCheatSheet_3.png)

<div class="content-ad"></div>

## 구성 관리:

- terraform fmt: Terraform 구성 파일을 가독성 있게 형식화합니다.
- terraform validate: Terraform 구성 구문을 유효성 검사합니다.

![이미지](/assets/img/2024-05-18-TheGuidetoTerraformDevOpsTerraformCLICommandsandCheatSheet_4.png)

## 인프라 관리:

<div class="content-ad"></div>

- terraform plan: 인프라 변경에 대한 자세한 실행 계획을 생성합니다.
- terraform apply: 계획된 인프라 변경을 계획에 따라 적용합니다.
- terraform apply -auto-approve: 확인 프롬프트 없이 계획을 적용합니다 (주의하여 사용해주세요!).
- terraform destroy: Terraform 구성에 따라 기존 인프라를 파괴합니다.
- terraform destroy -auto-approve: 확인 없이 인프라를 파괴합니다 (주의하여 사용해주세요!).

![이미지](/assets/img/2024-05-18-TheGuidetoTerraformDevOpsTerraformCLICommandsandCheatSheet_5.png)

![이미지](/assets/img/2024-05-18-TheGuidetoTerraformDevOpsTerraformCLICommandsandCheatSheet_6.png)

## 상태 관리:

<div class="content-ad"></div>

- terraform state list: Terraform이 관리하는 모든 리소스를 상태 파일에서 나열합니다.
- terraform state show: 상태 파일에서 특정 리소스의 세부 정보를 표시합니다.
- terraform state rm: 리소스를 Terraform 상태에서 제거하지만 공급자에서 파괴하지는 않습니다.

![이미지](/assets/img/2024-05-18-TheGuidetoTerraformDevOpsTerraformCLICommandsandCheatSheet_7.png)

더 많은 정보는 여기에서 확인할 수 있습니다:

## Workspaces:

<div class="content-ad"></div>

- terraform workspace new: 새 워크스페이스를 만듭니다.
- terraform workspace select: 기존 워크스페이스를 선택합니다.
- terraform workspace list: 사용 가능한 모든 워크스페이스를 나열합니다.

## 로그:

테라폼 자체에는 전통적인 방식으로 로그를 볼 수 있는 내장 명령어가 없습니다. 그러나 환경 변수를 사용하여 테라폼의 로깅 동작을 제어할 수 있습니다. 다음은 테라폼 로그를 관리하는 방법입니다:

## 로그 레벨 활성화 및 제어하기:

<div class="content-ad"></div>

- 테라폼은 TF_LOG 환경 변수를 사용하여 로그의 상세 수준을 제어합니다. 이를 다음 수준 중 하나로 설정할 수 있습니다:
- ERROR: 오직 오류만 기록합니다 (기본값)
- WARN: 경고 및 오류를 기록합니다
- INFO: 정보 메시지, 경고 및 오류를 기록합니다
- DEBUG: 자세한 디버그 정보를 기록합니다
- TRACE: 가장 상세한 추적 정보를 기록합니다 (불안정한 형식)
- 예시 (디버그 로그를 활성화):

```js
export TF_LOG="DEBUG"
terraform plan
```

## 로그 파일 지정하기:

- TF_LOG_PATH 환경 변수를 사용하여 테라폼이 로그를 작성할 특정 파일을 정의합니다.
- 예시 (로그를 “terraform.log”에 작성):

<div class="content-ad"></div>

```js
export TF_LOG_PATH="terraform.log"
terraform apply
```

## 셸 명령어로 로그 캡처하기:

- 테라폼 명령어를 셸 기능과 결합하여 로그를 캡처하고 형식화할 수 있습니다:
- 예시 (디버그 로그와 함께 플랜 출력 캡처):

```js
terraform plan -no-color 2>&1 | tee plan.log
```

<div class="content-ad"></div>

```markdown
![이미지](/assets/img/2024-05-18-TheGuidetoTerraformDevOpsTerraformCLICommandsandCheatSheet_8.png)

- 설명:
  - no-color: 색이 없는 출력을 사용하여 해석을 쉽게 할 수 있습니다.
  - 2>&1: 표준 출력(stdout)과 표준 오류(stderr)를 다음 명령어로 리디렉션합니다.
  - tee plan.log: "plan.log"이라는 파일을 생성하고 캡처한 출력을 해당 파일에 기록합니다.

## 기타 유용한 명령어:

- terraform get: Terraform 모듈을 다운로드하고 설치합니다. (프로젝트 내의 모듈에 대해서)
- terraform providers: 구성된 제공자에 관한 정보를 표시합니다.
- terraform output: Terraform 구성에서 출력 값들을 표시합니다.
- terraform version: Terraform 버전 정보를 표시합니다.
```

<div class="content-ad"></div>

## 추가 팁:

- 자세한 도움말을 얻으려면 모든 명령어에 -help 플래그를 사용하세요 (예: terraform init -help).
- 모든 명령어와 옵션에 대한 포괄적인 정보를 위해 공식 Terraform 문서를 참조하세요 https://developer.hashicorp.com/terraform/cli/commands.

# 결론

Terraform CLI를 숙달하고 중요한 명령어와 구성을 이해하면 인프라를 코드로 효율적으로 관리하고, 배포를 간소화하며, 확장 가능하고 재현 가능한 환경을 유지할 수 있습니다. 이는 인프라의 신뢰성과 민첩성을 향상시킬 것입니다.

<div class="content-ad"></div>

감사합니다! 🙌🏻 구독하지 않고 CLAP 👏을 누르지 말고 잊지 마세요! 다음 기사에서 만나요.🤘

# 저에 대해

“안녕하세요! Joel O’Wembo입니다. AWS 인증 클라우드 아키텍트이자 백엔드 개발자이자 AWS 커뮤니티 빌더입니다. 저는 필리핀에 거주하고 있습니다 🇵🇭 클라우드 아키텍처, 데브옵스, 그리고 고가용성(HA) 원칙에 대한 깊은 이해력을 바탕으로 강력한 전문성을 제공합니다. 제 지식을 활용하여 고가용성을 갖춘 견고하고 확장 가능한 클라우드 응용프로그램을 효율적인 기업 배포를 위해 오픈 소스 도구를 사용하여 만듭니다.”

저자(Joel O. Wembo)에 대한 더 많은 정보는 방문하십시오:

<div class="content-ad"></div>

링크:

- Linkedin: [https://www.linkedin.com/in/joelotepawembo/](https://www.linkedin.com/in/joelotepawembo/)
- 웹사이트: [https://joelwembo.com](https://joelwembo.com)
- 트위터: [https://twitter.com/joelwembo1](https://twitter.com/joelwembo1)
- GitHub: [https://github.com/joelwembo](https://github.com/joelwembo)
- 포트폴리오: [joelwembo.github.io](joelwembo.github.io)
- Patreon: [https://www.patreon.com/joelwembo](https://www.patreon.com/joelwembo)

# 참고 문헌