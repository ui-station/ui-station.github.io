---
title: "뒷면에는 Terraform - 운영에 강력한 조합, 개발자에게 훌륭한 도구"
description: ""
coverImage: "/assets/img/2024-05-20-BackstageandTerraformAPowerfulCombinationforOpsWonderfulforDevs_0.png"
date: 2024-05-20 17:26
ogImage: 
  url: /assets/img/2024-05-20-BackstageandTerraformAPowerfulCombinationforOpsWonderfulforDevs_0.png
tag: Tech
originalTitle: "Backstage and Terraform — A Powerful Combination for Ops, Wonderful for Devs"
link: "https://medium.com/@_gdantas/backstage-and-terraform-a-powerful-combination-for-ops-wonderful-for-devs-c04ebce849f0"
---


DevOps/Platform 세계가 끊임없이 진화하는 가운데 많은 기업들이 전통적인 도전에 직면하고 있습니다, 특히 클라우드 네이티브 기술을 널리 채용한 기업들은 더 그렇습니다.

아마 당신도 적나라히 알고 계실 인프라스트럭처 자동화(IaC)의 유명한 시나리오를 만난 적이 있을 것입니다.

코드를 통해 클라우드 리소스를 관리하는 IaC는 오늘날 거의 필수적이 되어갔습니다.

시간이 지남에 따라 이 방식이 스스로 입증되어 오고 있지만, 채택이 증가함에 따라 어느 정도 도전이 팀들의 일상에 영향을 끼치기 시작하는 것은 당연한 일입니다. 아마도 개발팀의 일원으로 그 시나리오를 이해할 수 있을 것입니다.

<div class="content-ad"></div>

클라우드에서 새로운 리소스를 생성해야 할 때 데브옵스/시스템 신뢰성 엔지니어(SRE) 팀에게 리소스를 생성해 달라는 티켓을 열어야 할 필요가 있다는 사실을 깨닫게 될 겁니다. 이는 모든 리소스가 인프라스트럭처를 코드로만(IaC) 생성하기 때문입니다.

이 작업은 기능의 개발 시간에서 상당한 시간을 차지합니다. 아니면 심지어 리소스 생성을 위한 풀 리퀘스트를 직접 작성할 권한이 있더라도 자신이 리소스를 생성하기 위해 새로운 언어(Terraform)를 배워야 한다는 사실에 맞닥뜨릴 수도 있습니다.

혹시 이제는 플랫폼 엔지니어링이라는 용어를 접하고 개발자 포털의 개념을 마주친 적이 있을 수도 있습니다.

또한 Backstage.io에 대해 들어본 적이 있을 겁니다.

<div class="content-ad"></div>

(이미 확인하지 않은 경우 다른 게시물을 소개해드릴게요 - 링크).

![image](https://miro.medium.com/v2/resize:fit:1400/1*sYL2IGcBPy8XNyPu_4wPQg.gif)

포스트 처음에 언급한 문제를 해결하는 데 도움을 주는 소프트웨어 템플릿이라는 기능을 소개하고 싶어요. 이 기능이 여러분에게도 도움이 될 것 같아요.

여러분이 다양한 필드를 갖는 폼을 만들고 자동으로 Terraform 코드를 생성하는 파이프라인을 실행할 수 있다고 상상해보세요.

<div class="content-ad"></div>

개발자들에게는 몇 가지 필드가 있는 페이지일 뿐입니다.

유지보수자에게는 필요한 파일을 생성하는 데 도움이 되는 Jinja/Helm과 매우 유사한 템플릿 메커니즘과 YAML 파일이 필요합니다. 그것이 모두 담긴 페이지입니다. 원하는 모든 정보를 담은 풀 리퀘스트를 오픈합니다.

# 그러면, 어떻게 사용하나요?

전체 비밀은 한 파일에 있습니다. 여기서 포맷에 대해서 말씀드리겠습니다. 이 파일에서 양식과 실행 파이프라인을 모두 구성할 것이며, 이 포맷은 Kubernetes Custom Resource Definition (CRD)과 매우 유사한 형식을 따릅니다. 종류, 메타데이터 및 스펙이 있습니다.

<div class="content-ad"></div>

이 파일은 4개의 부분으로 나눠져 있어요.

## 메타데이터

이 부분에는 Backstage의 메인 페이지에서 당신의 템플릿을 보여주는 데 사용되는 정보들이 포함되어 있습니다. 제목, 설명, 책임 있는 팀과 함께 문서 링크 및 템플릿을 분류하는 데 사용되는 태그들을 추가할 수 있어요.

## 매개변수

<div class="content-ad"></div>

매개변수는 데이터 입력 양식이 어떻게 선언될지를 정하는 곳입니다. 이 양식은 내부적으로 react-jsonschema-form을 사용하므로 간단한 텍스트에서 여러 옵션을 가진 드롭다운까지 필드를 생성할 수 있습니다. 또한 텍스트가 민감한 정보인지 여부와 같은 다른 측면을 사용자가 선택한 값의 가능성과 패턴을 아주 잘 제어할 수 있습니다. 

이것은 사용자가 선택할 값의 가능성과 패턴을 매우 잘 제어할 수 있기 때문에 매우 유용합니다.

Backstage 문서 자체에는 사용 예제가 몇 가지 있습니다.

좋은 팁은 템플릿 편집기를 사용하는 것입니다. 이렇게 하면 양식이 어떻게 표시될지에 대한 빠른 피드백을 받을 수 있습니다.

<div class="content-ad"></div>

우리는 또 Backstage를 위해 외부 정보를 얻기 위해 사용자 정의 입력을 생성할 수도 있습니다. 이는 다른 API에 요청을 보내어 드롭다운에서 이를 표시하는 것을 포함합니다.

Backstage 문서에서 자세한 정보를 볼 수 있습니다:

## actions

액션은 플랫폼 팀이 최고의 작업을 보여줄 수 있는 곳입니다. 👩🏽‍💻✨👨🏽‍💻.

<div class="content-ad"></div>

<table>
  <tr>
    <th>이미지</th>
    <th>파일 경로</th>
  </tr>
  <tr>
    <td><img src="https://miro.medium.com/v2/resize:fit:1286/1*j8k216skDRrfd5bLDbEz6Q.gif" /></td>
    <td>Think of it as creating a pipeline.</td>
  </tr>
</table>

파이프라인을 생성하는 것으로 생각해보세요.

우리는 기본 Backstage 작업이나 플러그인으로 설치할 수 있는 것들을 사용하여 여러 단계를 만들 수 있습니다.

각 단계에는 다음과 같은 필드가 있습니다:

<div class="content-ad"></div>

- id: 파일의 다른 부분에서 참조하는 데 사용하는 고유 식별자입니다.
- name: 단계 실행 중 UI에 표시될 텍스트입니다.
- action: 사용 중인 작업의 식별자입니다.
- input: 작업에 전달할 수 있는 변수입니다.

GitHub Actions를 사용해 보셨다면 익숙할 것입니다.

Backstage에 있는 모든 작업을 확인하려면 /create/actions 페이지에서 작업의 이름과 받을 수 있는 입력을 볼 수 있습니다.

또한 이러한 작업에서는 양식에 입력된 값들을 $' parameters.field-id ' 구문을 사용하여 입력으로 사용할 수 있습니다.

<div class="content-ad"></div>

예시:


![Backstage and Terraform: A Powerful Combination for Ops, Wonderful for Devs](/assets/img/2024-05-20-BackstageandTerraformAPowerfulCombinationforOpsWonderfulforDevs_0.png)


아웃풋은 단계 실행이 끝나고 사용자에게 표시할 수 있는 링크입니다.

이렇게 하면 사용자가 다음 단계를 따르거나 생성된 결과를 확인할 수 있는 링크를 이미 제공할 수 있습니다.

<div class="content-ad"></div>

좋은 예시는 생성된 Pull Request에 링크를 추가하는 것입니다.

# 이제, 모든 것을 결합하여 실용적인 예시를 살펴봅시다.

이미 Atlantis와 Terraform을 사용하여 인프라 모노레포를 갖고 있다고 가정하고, SQS 큐를 생성하는 GitHub Pull Request의 자동화를 해봅시다.

이를 통해 어떤 팀이든 DevOps/SRE 팀이 만든 패턴을 따라 독립적으로 Pull Request를 생성할 수 있습니다.

<div class="content-ad"></div>

위의 파일을 Markdown 형식으로 변환하면 다음과 같습니다:

```yaml
apiVersion: scaffolder.backstage.io/v1beta3
kind: Template
metadata:
  name: sqs-with-dlq
  title: Standard AWS SQS 
  description: PR to create AWS SQS Queue
  tags:
    - infrastructure
    - terraform
    - aws
    - queue
  links:
    - title: Documentation
      url: https://backstage.io/docs/features/software-templates
      icon: docs
    - title: Source
      url: https://github.com.br/gabriel-dantas98/backstage-scaffolders/blob/main/basic-react-app/create-web-app.yaml
      icon: github
spec:
  owner: cloud_platform
  type: infra-a-code

  parameters:
    - title: Inform the SQS initial information
      required:
        - queue_name
      properties:
        queue_name:
          title: Queue name
          type: string
          description: |
            The name of the queue

        environment:
          title: The environment the resource is part of
          type: string
          description: Which environment your application will be created in is usually related to the AWS account you will be provisioning your resource to.
          default: prod
          enum: [prod, staging, dev]

        create_dlq:
          title: Create a DLQ with redrive policy? 
          type: boolean
          description: If you desire to create a DLQ with redrive policy
          default: true
          ui:widget: radio

    - title: Information about ownership (using in tags)
      properties:
        tribe:
          title: Tribe
          type: string
          description: |
            Select the Tribe owner of resource, we used as tags in AWS
          ui:field: OwnerPicker
          ui:options:
            allowedKinds:
              - group

        squad:
          title: Squad
          type: string
          description: |
            Select the Tribe owner of resource, we used as tags in AWS
          ui:field: OwnerPicker
          ui:options:
            allowedKinds:
              - group

  steps:
    - id: template
      name: Render terraform files
      action: fetch:template
      input:
        targetPath: ./templates/outputs
        url: ./skeleton
        values:
          queue_name: ${ parameters.queue_name }
          create_dlq: ${ parameters.create_dlq }
          environment: ${ parameters.environment }
          squad: ${ parameters.squad }
          tribe: ${ parameters.tribe }

    - id: show_workspace
      name: Show workspace files
      action: debug:log
      input:
        listWorkspace: true
  
    - id: terraform_pr
      name: Create terraform PR
      action: publish:github:pull-request
      input:
        repoUrl: github.com?owner=gabriel-dantas98&repo=piltover-infrastructure
        branchName: 'sw-template/sqs/${ parameters.queue_name }'
        title: '🔩 Create ${ parameters.queue_name } AWS SQS'
        description: |
          ## Creating SQS ${ parameters.queue_name }
          
          This is an initial pull request to create an SQS queue and was created based on the Backstage template.

          If you need to add more parameters, check the official documentation - https://registry.terraform.io/modules/terraform-aws-modules/sqs/aws/latest

          *created by: [Backstage Software Template](https://hextech-portal.gdantas.com.br/create)* 👷‍♂️⚙️👷‍♀️
        sourcePath: ./templates/outputs
        targetPath: 'aws/production/sqs/${ parameters.queue_name }'

    - id: label_pr
      name: Add labels to PR
      action: github:issues:label
      input:
        repoUrl: github.com?owner=gabriel-dantas98&repo=piltover-infrastructure
        number: '${ steps.terraform_pr.output.pullRequestNumber }'
        labels:
          - terraform
          - created-by-backstage
          - ${ parameters.environment }
          - sqs

  output:
    links:
      - title: 'Go to pull request :D'
        url: ${ steps.terraform_pr .output.remoteUrl }
        icon: github
      - title: 'To view more check documentation'
        icon: docs
        url: "https://registry.terraform.io/modules/terraform-aws-modules/sqs/aws/latest"
```

실행한 후 다음과 같은 Pull Request가 생성됩니다:

이로써 Atlantis가 팀의 저장소에 적용되어 있으면 별도의 검토를 거쳐 큐 생성을 적용할 수 있습니다.

<div class="content-ad"></div>

글이 더 막대해지지 않도록 몇 가지 Atlantis 구성에 도움이 될만한 링크들을 분리했어요:

# 받았으면 하는 소중한 팁을 공유하고 싶어요

- 가능한 간단하게 하려고 노력해보세요; 이 양식을 작성하는 사람이 회사에 오래 있든 짧게 있든 모두 사용할 수 있다고 생각해보세요.
- 설명을 자유롭게 사용하세요; 양식을 작성하는 사람에게 커뮤니케이션 및 맥락을 제공하는 최고의 도구입니다.
- 가능한 경우 Markdown에 구조 및 흐름 이미지를 사용하세요; 이는 더 많은 자신감과 가시성을 부여합니다. 양식을 사용하는 사람들이 도움이 되는 문서에 대한 링크를 삽입할 수 있는 기회를 잡아보세요.

<img src="https://miro.medium.com/v2/resize:fit:1000/1*JMykIYYqaU__zxhOqxGPUg.gif" />

<div class="content-ad"></div>

앞으로의 게시물에 대한 제안이 있으면 언제든지 환영합니다! 🚀

여기까지 오신 데 감사드립니다! 떠나실 때:

- 👏을 눌러주세요.
- gdantas.com.br에서 더 멋진 콘텐츠를 만나보세요. 🚀
- 스타일리시하게 유지하고 싶다면 https://reserva.ink/deployou에서 저희 티셔츠를 확인해보세요. 👕💻