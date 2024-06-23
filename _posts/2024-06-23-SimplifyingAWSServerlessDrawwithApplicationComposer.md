---
title: "AWS 서버리스 간편 이해 Application Composer로 그리기"
description: ""
coverImage: "/assets/img/2024-06-23-SimplifyingAWSServerlessDrawwithApplicationComposer_0.png"
date: 2024-06-23 22:32
ogImage: 
  url: /assets/img/2024-06-23-SimplifyingAWSServerlessDrawwithApplicationComposer_0.png
tag: Tech
originalTitle: "Simplifying AWS Serverless: Draw with Application Composer"
link: "https://medium.com/@isenberg-ran/simplifying-aws-serverless-draw-with-application-composer-573c802e23fd"
---



![Image 1](/assets/img/2024-06-23-SimplifyingAWSServerlessDrawwithApplicationComposer_0.png)

서버리스 개발자로서, 우리는 기본 코드로 서비스를 개발하며, 최근에는 코드로 인프라를 작성하지만 대부분은 AWS 리소스를 생성하기 위해 코드나 구성을 작성한다는 것을 의미합니다. 그 동안 우리가 잘못하고 있었을까요? AWS에서 서버리스 서비스를 더 나은 방식으로 구축할 수 있는 간단하고 더 좋은 방법이 있을까요?

이 게시물에서는 AWS Application Composer를 사용하여 인프라 다이어그램을 그리면서 서버리스 서비스를 구축하는 방법에 대해 알아볼 수 있습니다. 저는 사용 방법, 장점 및 현재 제한 사항, 그리고 제 희망 목록을 공유하겠습니다.

![Image 2](/assets/img/2024-06-23-SimplifyingAWSServerlessDrawwithApplicationComposer_1.png)


<div class="content-ad"></div>

이 블로그 포스트는 "Ran The Builder" 웹사이트에서 최초로 게시되었습니다.

# IaC와 DevOps는 어렵습니다

리소스 구성 파일이나 AWS CDK, SAM 또는 Terraform 코드를 작성하는 것은 어렵습니다. 도구를 배워야 하고, 문서를 검토하고, 해당 도구의 모베스트 프랙티스를 배워야 하며 (여기에서 내 CDK 모베스트 프랙티스 포스트를 읽어보세요), 코드 또는 구성을 작성해야 합니다.

과거에는 개발자들이 Helm 차트와 같은 리소스를 빌드하는 구성 파일을 작성하는 것을 피했었습니다 (K8S의 생각에 오글거리곤 합니다), 이는 개발자들 사이에서 이러한 도구들의 채택 속도를 늦추었습니다. 그러나 서버리스와 같은 기술들과 함께 사용되는 최신 도구들인 AWS CDK, SAM과 코드로부터의 인프라스트럭처의 최근 트렌드와 같은 기술들이 등장함에 따라 개발자들은 DevOps 마인드를 수용하고 인프라 소스 코드를 작성하는 데 보다 개방적으로 대해요. 그렇지만 여전히 어려운 일입니다.

<div class="content-ad"></div>

오늘은 새로 발견한 옵션 하나를 소개하고 싶어요 — 코드 없이도 자료 다이어그램을 그려서 리소스를 구축하는 방법입니다.

# 인프라 구축을 그려볼까요?!

네, 인프라 다이어그램을 그리는 거죠. 우리 모두 이런 작업을 할 때가 있죠.

저는 아키텍트로서 서비스 동작을 설명하고 서비스 도메인이 어떻게 연결되는지를 나타내는 고수준 설계를 그리곤 해요.

<div class="content-ad"></div>

개발자들은 내 고수준 디자인을 가져와서 서비스의 실제 AWS 서버리스 리소스를 설명하는 저수준 디자인으로 바꿉니다. 우리는 Lucidchart나 Draw.io 같은 다이어그램 도구를 사용해요.

서비스 인프라 다이어그램은 새로운 기능을 설명하거나 새 팀원에게 서비스를 소개하거나 서비스가 어떻게 작동하는지 명확히 이해하는 데 좋은 도구에요.

하지만, 인프라 다이어그램에도 문제가 있죠.

## 다이어그램은 (거의) 항상 동기화되어 있지 않아요

<div class="content-ad"></div>

시간이 지남에 따라 기능이 추가되고 버그가 수정됩니다. 개발자들은 원래 디자인을 변경하며, 일부 디자인 요소는 우선 순위 때문에 제외되고 발전되지 않습니다. 꼼꼼히 작성하고 자랑스러워했던 원래 인프라 다이어그램을 기억하고 계십니까? 지금 실제 서비스 인프라와 동기화되어 있지 않으며 빠르게 가치를 잃고 있습니다.

이 문제를 해결하기 위해 항상 코드 또는 인프라 구성 파일(AWS CDK, 제 경우)로 돌아갑니다. 코드는 거짓말을 하지 않지만 코드를 살펴보는 데는 시간과 기술이 필요합니다.

최적의 해결책은 서비스 인프라 다이어그램을 서비스 코드와 동기화하는 것입니다. 다이어그램을 항상 업데이트해야 한다는 것을 기억하는 것은 정말 골치 아픕니다. 아무도 그렇게 하지 않습니다.

다행히도 AWS는 이를 자동으로 수행하고 동시에 서비스 인프라를 구축하는 것을 더 직관적이고 재미있게 만들 수 있는 방법을 고안했습니다.

<div class="content-ad"></div>

그들은 AWS 응용 프로그램 컴포저를 구축함으로써 그를 성취했어요. 이 도구를 사용하면 인프라 다이어그램을 그리고 이를 AWS SAM 또는 CloudFormation 템플릿으로 변환할 수 있어요.

# AWS 응용 프로그램 컴포저

몇 년 전인 2021년에, AWS는 이 문제를 해결하려고 시도한 스타트업인 "Stackery"를 인수했어요. 2022년 12월로 빨리 가면, AWS 응용 프로그램 컴포저가 탄생했어요.

그것은 지난 2023년 3월에 GA(일반 사용 가능)로 출시되었어요. AWS re:invent 2023에서, 저의 주목을 끈 거대한 발표가 있었어요.

<div class="content-ad"></div>

콘솔에서 작업하고 다이어그램을 그리느라 애쓰지 않아도 됩니다. 이제 Visual Studio IDE에서 편안하게 Application Composer를 사용할 수 있어요.

사용자 경험과 개발 흐름을 살펴보겠습니다.

## 사용자 경험

빈 프로젝트를 시작하거나 기존 프로젝트를 불러올 수 있어요. 저는 AWS Lambda Handler 쿡북 템플릿 프로젝트를 불러와서 어떻게 작동하는지 확인해 보기로 했어요.

<div class="content-ad"></div>

서비스 다이어그램을 제공합니다 (무엇이 웃긴 게, 인프라와 동기화가 안 맞고 CloudWatch 알람 및 대시보드가 빠져 있어요):

![Service Diagram](/assets/img/2024-06-23-SimplifyingAWSServerlessDrawwithApplicationComposer_2.png)

이제 기존 프로젝트 CloudFormation 템플릿으로 다이어그램을 생성해 보겠습니다. Mac에서는 'CMD + SHIFT + P'를 클릭하여 프로젝트의 CloudFormation/SAM 템플릿을 선택하세요:

- AWS 툴킷 확장 프로그램을 설치했는지 확인해주세요!

<div class="content-ad"></div>


![Diagram 3](/assets/img/2024-06-23-SimplifyingAWSServerlessDrawwithApplicationComposer_3.png)

화면에 보이는 첫 번째 출력물은 많은 리소스 및 그들 사이의 연결을 포함한 다이어그램입니다. 각 리소스는 AWS 아이콘, 리소스 이름 및 현재 구성이 표시됩니다.

매우 유용한 기능 중 하나는 리소스를 그룹 또는 논리적 도메인 아래로 그룹화할 수 있다는 것입니다. 나는 리소스를 CDK 구조체로 나눠놨지만 이 분할이 CloudFormation에 반영되지 않았습니다. 모든 것을 정리하는 데 2분이 걸렸고 이 아름다운 다이어그램을 얻었습니다:

![Diagram 4](/assets/img/2024-06-23-SimplifyingAWSServerlessDrawwithApplicationComposer_4.png)


<div class="content-ad"></div>

CRUD API 그룹(API GW, 람다 함수, 두 개의 DynamoDB 테이블), CloudWatch 리소스, 그리고 AppConfig 그룹이 있습니다. 그리고 이 리소스들 사이의 모든 연결도 있습니다.

처음 인상: 감명받았어요! 이 것은 매우 가독성이 좋고 다이어그램 불일치 문제를 해결합니다. 다이어그램이 일치하지 않는 문제가 기억나시나요? 여기서 한 변경사항은 SAM 또는 CloudFormation 템플릿으로 자동으로 번역되어 써집니다. 심지어 그룹까지! CloudFormation에 멋진 새로운 'ApplicationComposer' 속성이 추가되었어요.

새로운 서버리스 리소스를 추가하고 구성하는 것이 얼마나 쉬운지 확인해봅시다.

## 서버리스 리소스 추가하기

<div class="content-ad"></div>

새로운 CloudFormation 리소스를 추가하려고 해요. 리소스 검색이 빠르고 훌륭해요; 1000개 이상의 리소스를 추가할 수 있어요. 람다 함수를 선택해보죠. 간편하게 사용할 수 있는 메뉴가 나오는데, 필수 필드와 드롭다운 목록이 모두 제공돼요:

![Lambda Function](/assets/img/2024-06-23-SimplifyingAWSServerlessDrawwithApplicationComposer_5.png)

일반적으로 같은 것을 위해 많은 CDK 코드를 작성하는데, 여기서는 UI에서 처리할 수 있어요.

다른 리소스를 추가해보려고 해요, AppConfig 배포를요.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-23-SimplifyingAWSServerlessDrawwithApplicationComposer_6.png" />

여기서 경험이 조금 더 나아질 수 있어요. 자원 구성 패널에는 미리 정의된 드롭다운 목록이 없어요. 저는 CloudFormation 구성 코드를 작성해야 해요. 일반적이지 않은 많은 자원들은 람다 함수의 것과 같은 드롭다운 목록이 아닌 설정 상자가 있어요. 모든 구성을 입력하는 구성 상자가 있어요. 이 부분은 채우기 어렵고 이해하기 어려울 수 있어요. 그러나 공식 자원 문서로의 링크와 샘플 구성 예제를 제안해주는 AI 버튼이 있어서 좋아요.

전반적으로, 보다 일반적인 서버리스 자원을 사용한다면 훌륭해요. 시간이 지나면 더 많은 자원들이 VIP 대우를 받을 거예요. 모든 정직함을 다해서 말하자면, CDK가 많은 새로운 서비스에 대해 L2 구성 추상화를 제공하지 않는 것과 비교할 수 있어요.

## 자원 연결

<div class="content-ad"></div>

한 리소스에서 다른 리소스로 선을 그어서 연결할 수 있어요. 예를 들어 API 게이트웨이를 람다 함수에 연결하고, 특정 함수를 경로의 대상으로 설정할 수 있어요. 굉장히 쉽죠. 하지만 일부 리소스만 지원돼요. SQS 큐를 SNS 주제에 연결하려고 했는데, 아직 지원되지 않는다는 팝업 툴팁이 나왔어요. 지원되면 경험도 매우 간편할 거예요.

![](/assets/img/2024-06-23-SimplifyingAWSServerlessDrawwithApplicationComposer_7.png)

# 제한사항

그래서 이미 두 가지 제한사항을 이야기했어요:

<div class="content-ad"></div>

- 리소스 구성 - 드롭다운 기능이 부족하여 이상적인 사용자 경험은 아니지만 작동할 수 있습니다.
- 일부 연결해야 하는 리소스를 선으로 연결하는 것이 불가능합니다.

하지만 논의해야 할 다른 제한 사항이 있습니다.

저는 대규모 서비스의 CloudFormation 템플릿에서 Application Composer를 사용했을 때 리소스 한도 경고 메시지를 받았습니다. 이 문제는 해결될 것입니다. 그러나 현재는 제한 사항입니다.

얻은 다이어그램이 매우 크며 정렬하고 그룹화하는 데 많은 시간이 걸릴 것입니다. 또 다른 제한 사항은 CDK 지원의 부재입니다. 네, 알고 있어요. 적합한 L2 CDK 구조를 생성하는 것이 매우 어려운 일이지만 꿈은 꿀 수 있어요...

<div class="content-ad"></div>

상기 모든 사항에도 불구하고, 해당 서비스는 밝은 미래를 가지고 있으며, 이러한 제한 사항이 해결되면 내 일상 도구의 일부가 될 것입니다.

## 사용해야 하는 이유

IaC 작성보다 경험이 더 좋습니까? 더 직관적이거나 심지어 재미있습니까? 네, 100%입니다; 모든 리소스가 지원되고 사용자 정의 드롭다운 선택 기능이 있을 때 특히 쉽습니다.

CDK를 대체할 것인가요? 전체 CDK 지원이 있다면 아니요,하지만 문서 작성을 위한 인프라 다이어그램 작성과 같은 사용 사례가 있습니다.

<div class="content-ad"></div>

이것은 누구를 위한 것일까요? 이는 저수준 디자인을 그리고 작은 ~ 중간 규모 서비스의 정확한 인프라 다이어그램 표현을 원하는 사람들, 빠르게 프로토타입을 구축하고 싶은 사람들, 물론 기본적인 CloudFormation 또는 SAM을 사용하는 사람들에게 적합합니다. 자원을 구축하는 데 사용하지 않더라도 인프라 다이어그램을 그리는 데 사용하는 것은 의미가 있습니다. 최근에 AWS Lambda Handler Cookbook 문서에 애플리케이션 컴포저 리소스 이미지를 추가했는데, 간단히 생성할 수 있고 서비스 설명이 더 간단해졌기 때문입니다.

저는 이 서비스와 그 앞날에 흥분하고 있습니다. 이에는 발전할 여지가 많고 성장할 곳도 많이 있습니다. 사용자 경험을 개선하면 안 쓰기 힘들어질 것이며, 팀이 올바른 방향으로 나아가고 있습니다.

마지막으로, App Composer 팀 중 누군가가 이것을 읽고 계신다면, 제 소망과 이 서비스에 대한 비전은 아래에 있습니다.

# 애플리케이션 컴포저 소망목록

<div class="content-ad"></div>

만약 이 위시리스트 중 일부만 구현할 수 있다면, Application Composer는 뛰어날 것입니다:

- 채팅에서 기반 구성: 우리가 IDE 내에 있는 AI 유틸리티(Amazon Q 및 CodeWhisperer)를 사용하여 아이콘을 연결하고 다이어그램 리소스를 정의해야 할 이유가 없는데요? AI 비서에게 디자인할 것을 알리고 인프라 다이어그램을 그리고 구성하도록 하고 싶습니다.
- 자동 리소스 그룹화 — 같은 리소스 유형의 일부 유형을 그룹화합니다 — CW 대시보드, AppConfig 리소스 등등.
- 고수준 디자인 다이어그램 옵션 — "병합(Merge)" 옵션을 만들어 개체를 추상화한 도메인 그룹에 병합하는 옵션을 만들어보세요. 그리고 IDE에서 중첩 스택을 별도의 다이어그램으로 시각화합시다.
- 제약 사항 해결: L2 CDK 지원 및 상세 구성 옵션을 가진 더 많은 리소스 지원 추가.
- Github/Jenkins 플러그인을 통한 변경 사항 시각화 — PR을 검토할 때, 인프라 구성이나 추가/제거된 리소스가 시각적으로 무엇인지 이해하고 싶습니다. 이미 'CDK notifier'와 같은 부분적인 도구들이 그런 역할을 하는데요.
- 요소 복사-붙여넣기 — 어떤 이유에서인지 이 작업이 작동하지 않았습니다. 버그일 수도 있지만, 유사한 리소스를 빠르게 추가할 수 있으면 좋겠습니다.