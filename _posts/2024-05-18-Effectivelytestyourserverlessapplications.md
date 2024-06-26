---
title: "서버리스 애플리케이션을 효과적으로 테스트하세요"
description: ""
coverImage: "/assets/img/2024-05-18-Effectivelytestyourserverlessapplications_0.png"
date: 2024-05-18 16:11
ogImage:
  url: /assets/img/2024-05-18-Effectivelytestyourserverlessapplications_0.png
tag: Tech
originalTitle: "Effectively test your serverless applications"
link: "https://medium.com/towards-aws/effectively-test-your-serverless-applications-d00d92eba72e"
---

서버리스로 이동하는 것은 다른 종류의 아키텍처와 마찬가지로 장단점이 있습니다. 클라우드에 코드만 전달하고 서버를 사용자로부터 추상화하는 아이디어는 몇몇 사람들에게 게임 체인저가 될 수 있습니다. 그러나 이 비교적 새로운 종류의 기술은 대면에서 많은 놀라운 기능을 제공하지만 그 함의는 상당합니다.

AWS에서 서버리스 솔루션을 구현해보기 시작하는 모든 개발자의 가장 핵심적인 질문은 이것입니다:

![이미지](/assets/img/2024-05-18-Effectivelytestyourserverlessapplications_0.png)

Express 또는 Django와 같은 서버풀 프레임워크로부터 API를 작성하는 전통적인 배경에서 온 백엔드 개발자라면, nodemon, python manage.py runserver 또는 개발 프로세스 중에 컨테이너 볼륨을 활용한다면 docker-compose up -d와 같은 해당하는 핫 리로딩 기능에 익숙할 것입니다. 그러나 서버리스에서는 전통적인 백엔드의 개발자 경험을 완전히 포괄하거나 유사한 핫 리로딩 기능이 없다는 것을 알 수 있을 겁니다.

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

서버리스 솔루션을 구현하는 주요 이유 중 하나는 서버리스가 클라우드에 구축되기 때문에 개발자들에게 다소 독특한 경험을 제공한다는 것입니다. AWS Lambda 및 AWS의 모든 다른 서버리스 서비스들은 로컬 환경이 아닌 클라우드에서 최적의 작동을 합니다. 그러나 이러한 이념은 일부 사람들에게는 꽤 문제가 될 수 있습니다.

그렇고 그렇지 않습니다. 서버리스에서는 클라우드에서 자체적인 순간적인 환경을 만들 수 있는 능력을 가지고 있습니다. 클라우드 컴퓨팅의 일반적인 요금 체계는 사용한 만큼만 지불해야 한다는 것입니다. 그러나 서버리스에서는 일정한 범위내에서 이는 사실이지만, 더 정확히 말하면 요금 체계를 정의하는 것은 최종 사용자가 사용한만큼만 지불해야 한다는 것입니다. 따라서, 서버리스의 요금 체계를 통해 클라우드에서 환경을 생성하고 소멸시킬 때 비용이 거의 발생하지 않습니다.

클라우드에 자체 개발 환경을 배포할 수 있는 것은 제품 환경을 모방하는 데 매우 유리합니다. 그러나 여전히 모든 코드 변경마다 배포해야 한다는 것이 거부 사항으로 느껴질 수 있습니다. Serverless Framework나 AWS CDK와 같은 기존 IaC(Infrastructure as Code) 솔루션에서 서버리스를 사용하고 있다면, 가장 짜증나는 피드백 루프에 익숙할 지도 모릅니다.

위에서 언급한 대로 클라우드에 임시 개발 환경을 배포하는 것은 코드가 예상대로 작동한다는 확신을 얻는 장점이 있습니다. 생산 환경으로 전송될 때의 동작도 마찬가지일 것입니다. 이 과정은 의심의 여지 없이 최적의 결과를 제공할 것이지만, 여전히 코드 변경마다 배포하고 각 개발자에게 클라우드 환경 접근을 허용해야 한다는 번거로움이 있습니다. 또한 각 배포는 애플리케이션의 크기에 따라 약 3~10분이 소요됩니다.

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

이 느립고 번거로운 피드백 루프를 개선하기 위한 노력이 있었습니다. Serverless Framework의 서버리스 오프라인과 같은 기능은 로컬에서 API 게이트웨이 및 람다 함수를 에뮬레이트할 수 있게 해줍니다. 이 기능을 통해 개발 프로세스를 확실히 가속화할 수 있습니다. 그러나 이 기능은 API 게이트웨이 및 람다 함수를 에뮬레이트하는 데로 제한되어 있습니다. DynamoDB, SQS, SNS 또는 S3와 같은 다른 서버리스 서비스를 에뮬레이트할 수 있는 다른 플러그인들도 있지만, 이를 설정하려면 상당한 운영 오버헤드가 필요합니다. 현재의 로컬 에뮬레이터는 클라우드 환경을 완벽하게 모방할 수 없습니다. 로컬 환경은 생산 환경을 완벽하게 반영하지 못할 수 있어 동작 및 보안 구성에서 차이가 발생할 수 있습니다. 이 접근 방식은 "내 컴퓨터에서는 작동하는데 실제 환경에서는 작동하지 않는다"는 부정적인 결과를 가져올 수 있습니다.

다른 예시로 AWS CDK의 cdk watch가 있습니다. AWS CDK는 완전히 단기적인 환경을 채택합니다. serverless offline 기능과 유사한 sam invoke local 같은 기능을 갖고 있지만, cdk watch는 로컬에서 Lambda 함수의 코드 변경 사항을 감지하고 해당 변경 사항만 배포합니다. 이 접근 방식은 피드백 루프를 완전히 새로 만들진 않지만 개발자 경험을 향상시킵니다. 서버리스 응용 프로그램 전체를 다시 배포하는 대신 코드가 변경된 Lambda 함수만 다시 배포하여 배포 속도를 높여줍니다(그러나 핫 리로딩만큼 빠르지는 않습니다). 그리고 터미널에서 스택내 Lambda 함수의 모든 CloudWatch 로그를 감시할 수 있습니다. AWS Management Console을 여는 번거로움 없이 콘솔에서 모든 로그를 확인할 수 있습니다.

이 접근 방식은 우수한 개발자 경험과 최적의 결과를 위한 한 걸음입니다. 그러나 배포 대기 시간은 여전히 기존 백엔드와 비교했을 때 핫 리로딩만큼 빠르지 않아 전통적인 백엔드에 비해 여전히 생산성 저하 요인으로 여길 사람이 많습니다.

서버리스 프레임워크나 AWS CDK와 같은 인프라스트럭처 코드 솔루션을 사용하는 것이 강력히 권고됩니다. 둘 중 하나를 선택하는 것은 크게 중요하지 않습니다. 이전 질문에 대한 대답으로, 코드 변경 시마다 재배포해야 할 경우, 이미 서버리스 프로젝트에서 이러한 IaC 솔루션을 사용하고 있다고 가정됩니다. 더 자세한 답변을 드리자면, 서버리스 응용 프로그램을 테스트하려면 여러분이 인프라 설정을 조정한 코드 변경마다 배포해야 하지만, Lambda 함수를 각 코드 변경마다 다시 배포할 필요가 없습니다. 이는 테스트를 통해 가능합니다.

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

# 서버리스 애플리케이션을 테스트해야 하는 이유

서버리스 애플리케이션에 대한 테스트를 생성하는 것은 로컬에서 해당 애플리케이션을 실행하는 또 다른 방법입니다. 개발자 경험, 생산성 및 코드 확신 사이의 완벽한 균형을 제공합니다. 이렇게 하면 프로덕션 환경으로 코드를 전달할 때 코드 확신을 얻을 때 개발자 경험을 희생할 필요가 없게 됩니다. 두 마리 토끼를 모두 잡을 수 있습니다!

여기서 소개할 테스트 유형은 "remocal testing"입니다. 로컬 코드(람다 함수 코드)를 원격/배포된 AWS 서비스와 테스트할 것입니다. 이는 일시적 환경의 사용과 동시에 로컬 코드 호출을 활용합니다. 따라서 제안하는 피드백 루프는 다음과 같습니다:

일찍 봤을 때, 이전의 피드백 루프와 비교했을 때 큰 차이는 없습니다. 그러나 이 방법은 더 빠르며 동시에 로컬 코드가 클라우드에서 작동할 것임을 더 많은 코드 확신을 제공합니다. 왜 그런 걸까요? 클라우드에서의 배포 단계가 다른 단계보다 자주 실행되지 않기 때문입니다. 이 단계는 본래 람다 함수 이외에 필요한 서비스를 배포하는 것으로, 예를 들어 DynamoDB 테이블이나 S3 버킷과 같은 서비스가 있겠습니다. 그러고나면 다시, 우리의 로컬 코드는 클라우드에서 이러한 배포된 서비스들을 대상으로 테스트됩니다. 이는 프로덕션 환경에 더 가까운 근사치를 제공합니다. 최종적으로, 여러분이 자주 만나게 될 피드백 루프는 아래와 같을 것입니다:

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

이 제안된 피드백 루프는 전통적인 백엔드와 매우 유사한 것이 아닌가요?

로컬 코드가 작동하려면 기존 AWS 리소스에 의존하므로 전체 서버리스 애플리케이션을 배포해야 하는 경우는 다음과 같습니다:

- 새로운 DynamoDB 테이블, S3 버킷 또는 람다 함수 이외에 필요한 서비스를 사용할 때.
- 엔드 투 엔드 테스트를 수행할 준비가 된 경우.

위 질문에 대한 답은 명백한 "아니요"입니다. 같은 것이 아니에요. 전체 서버리스 애플리케이션을 배포하는 데는 규모나 필요한 리소스의 수에 따라 5~10분이 걸릴 수 있습니다. 단일 람다 함수의 오타로 인해 다시 배포해야 할 경우를 상상해 보세요. 그럼 여러분은 변경 사항이 반영되고 함수의 동작을 테스트할 수 있도록 5~10분을 기다려야 합니다. 끔찍하죠? 반면 로컬 테스트는 실행되는 테스트의 양에 따라 최대 1분 정도 소요될 수 있어요.

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

게다가, 전통적인 백앤드를 지원하는 핫 리로딩과 유사한 동작을 원한다면, Jest와 같은 현대 테스트 프레임워크는 이미 파일의 변경을 감시하고 상호작용하며 테스트를 실행하는 watch 모드를 갖고 있습니다. 따라서 로컬 람다 함수 코드에서 코드 변경이 발생하자마자, 테스트 프레임워크가 해당 코드 변경과 관련된 모든 테스트를 자동으로 실행합니다.

# 구현해야 할 테스트 유형은 무엇인가요?

![2024-05-18-Effectivelytestyourserverlessapplications_1.png](/assets/img/2024-05-18-Effectivelytestyourserverlessapplications_1.png)

이 질문에 대한 대답은 특정 유형의 테스트가 여러분에게 제공하는 투자 수익에 따라 실제로 달려있습니다. 서버리스 시스템이나 아마도 다른 분산 시스템에서 가치가 큰 테스트 유형은 통합 테스트입니다. 위의 테스트 허니콤을 참조할 수 있습니다.

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

![이미지](/assets/img/2024-05-18-Effectivelytestyourserverlessapplications_2.png)

테스트의 양 또는 테스트 커버리지에 대해서는 얘기하지 않습니다. 중요한 것은 테스트가 어떤 투자 수익(Return of Investment)을 제공하는지입니다. 서버리스 유형의 아키텍처와 마이크로서비스를 촉진하는 경우, 통합 테스트를 구현하는 것이 더 많은 의미를 갖습니다. 왜냐하면 복잡성의 많은 부분이 람다 코드 외부에 존재하기 때문입니다. 마이크로서비스를 테스트하는 가장 좋은 방법은 코드가 다른 서비스와 상호 작용하는 방식을 테스트하는 것입니다.

엔드투엔드 테스트는 애플리케이션 전체를 시작부터 끝까지 테스트합니다. 이는 최적의 수익을 제공하지만 실행하는 데 시간이 오래 걸리기 때문에 이러한 유형의 테스트의 투자 수익은 상당히 낮습니다. 반면, 유닛 테스트는 람다 함수의 도메인 로직을 처리할 수 있기 때문에 대부분 낮은 수익과 투자 수익을 제공할 것으로 예상됩니다. 유닛 테스트는 데이터베이스 레이어 및 다른 서비스와의 모든 상호 작용을 가짜로 만들어 가치를 거의 제공하지 않습니다.

# 실전 예제

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

<img src="/assets/img/2024-05-18-Effectivelytestyourserverlessapplications_3.png" />

다음 REST API를 구현하여 블로그 게시물과 해당 테스트를 생성해 봅시다. 이 예시에서는 Serverless Framework를 내 선택의 IaC로 사용했습니다. 따라서 동일하게 따라갈 수 있는 저장소는 여기에서 찾을 수 있습니다:

## 사전 준비사항

- AWS 프로필 구성: export AWS_PROFILE=`you-profile-name`
- 의존성 설치: npm install
- serverless 애플리케이션 배포: npm run sls -- deploy --stage `your-ephemeral-environment-name`. 꼭! `your-ephemeral-environment-name`을 본인의 임시 환경 이름으로 변경하지 않도록 주의해 주세요.
- 당신만의 .env.`your-ephemeral-environment-name`을 생성하세요.

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

다음은 환경 파일의 형식이어야 합니다.

```js
API_BASE_URL = XXX;
BLOG_POSTS_TABLE = XXX;
AWS_REGION = XXX;
```

이러한 환경 변수의 값을 AWS Management Console 또는 Serverless Framework에서 제공하는 플러그인을 통해 얻을 수 있습니다.

## 제안된 피드백 루프

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

테이블 태그를 마크다운 형식으로 변경하세요.

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

위의 통합 테스트는 지역 Lambda 함수 코드(const handler = require(“@src/functions/createBlogPost).handler)를 가리키고 있습니다. 람다 함수가 클라우드에 배포된 것이 아닙니다. 따라서 우리가 통합 테스트를 실행할 때, 지역 람다 함수 코드가 실행되지만 그 모든 DynamoDB 작업은 클라우드의 DynamoDB 테이블로 전송됩니다. 이것은 원격 테스팅입니다. 여기서 지역 코드(이 경우 우리의 지역 람다 함수)가 원격/배포된 AWS 서비스(이 경우 DynamoDB 테이블)에 대해 테스트됩니다.

다음은 통합 테스트를 실행하는 단계입니다:

- export STAGE=`your-ephemeral-environment-name`. 이는 .env.`your-ephemeral-environment-name`을 참조하기 위함입니다.
- npm run integration-test

![테스트 이미지](/assets/img/2024-05-18-Effectivelytestyourserverlessapplications_4.png)

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

이제 Lambda 함수를 편집하고 싶다면, 얼마든지 해보세요! 블로그 게시물의 제목과 내용 이외에도 스니펫 필드와 같은 다른 필드를 저장할 수도 있습니다. 스니펫 필드는 내용의 처음 50자를 보여주거나 created_at 및 updated_at 필드를 추가할 수도 있습니다. 간단히 테스트를 다시 실행하여 코드 동작을 확인해보세요. 배포하고 3분에서 5분을 기다리지 않아도 코드 동작을 확인할 수 있습니다.

게다가, 자신의 IDE에서 스텝 디버거를 사용해보는 것도 좋아요. 이것들은 서버리스 애플리케이션을 테스트하는 큰 장점 중 일부일 뿐입니다. 속도와 민첩성을 제공하여 Lambda 코드를 개발하고 향상된 생산성을 실현할 수 있습니다.

## 통합 테스트의 주의사항

통합 테스트는 우리가 원하던 속도와 생산성을 제공하지만, 전체 사용자 여정을 완전히 다루지는 못한다는 점을 유의해야 합니다.

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

이 질문에 대한 답변은 전제 조건으로 수행한 단계 중 하나이며, AWS 프로필을 내보낼 때 수행한 요소 중 하나입니다. 로컬 람다 코드는 현재 AWS 프로필의 AWS 자격 증명을 가정합니다.

네, 무서운 것 맞죠? 그래서 로컬 람다 코드 작성 시 주의해야 합니다. 그러나 우리가 로컬 람다 코드를 클라우드로 배포할 때는 이와 같은 경우가 아닙니다. 람다 함수는 항상 기본 실행 역할을 가정합니다. Serverless Framework는 이를 추상화하는 데 훌륭한 작업을 하지만, 여전히 Lambda 함수가 다른 서비스와 통신하려면 특정 권한을 추가해야 합니다. serverless.yml에서 람다 함수를 만드는 방법을 살펴보세요:

```js
## serverless.yml

...

functions:
  createBlogPost:
    handler: src/functions/createBlogPost.handler
    environment:
      BLOG_POSTS_TABLE: !Ref BlogPostsTable
    iamRoleStatements:
      - Effect: Allow
        Action:
          - dynamodb:PutItem
        Resource: !GetAtt BlogPostsTable.Arn
    events:
      - httpApi: "POST /blogposts"

...
```

만약 람다 함수에 dynamodb:PutItem 권한을 부여해야 한다는 것을 명시하지 않으면, 클라우드의 람다 함수가 지정된 DynamoDB 테이블에 PutItem 작업을 호출할 권한이 없다는 오류가 발생할 수 있습니다.

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

이는 우리의 통합 테스트에서 찾지 못한 것입니다. 그래서 우리는 엔드 투 엔드 테스트라는 다른 유형의 테스트를 시도합니다. 이 create blog post 기능의 엔드 투 엔드 테스트는 다음과 같습니다:

```js
// __tests__/test_cases/e2e/createBlogPost.test.ts

require("dotenv").config({
 path: `.env.${process.env.STAGE}`,
});
import axios from "axios";
const chance = require("chance").Chance();

describe("When we call POST /blogposts", () => {
 it("should create a new item", async () => {
  console.log(`API_BASE_URL: ${process.env.API_BASE_URL}`);

  const title = chance.sentence({ words: 5 });
  const content = chance.paragraph();
  let response;

  try {
   response = await axios.post(
    `${process.env.API_BASE_URL}/blogposts`,
    {
     title,
     content,
    },
    {
     headers: {
      "Content-Type": "application/json",
      Authorization: "test",
     },
    }
   );
  } catch (error: any) {
   console.error(error);
  }

  expect(response).toBeDefined();

  if (response) {
   expect(response.status).toBe(201);
   expect(response.data.data.title).toBe(title);
   expect(response.data.data.content).toBe(content);
  }
 });
});
```

먼저, 엔드 투 엔드 테스트가 작동하려면 먼저 람다 함수를 클라우드에 배포해야 한다는 사실을 알아두는 것이 중요합니다. 위의 코드에서 보듯이, 클라우드의 람다 함수를 호출하는데 이 람다 함수의 API 엔드포인트를 통해 호출되며, 해당 엔드포인트는 POST /blogposts입니다. 로컬 람다 함수 코드가 아닙니다.

다음은 우리의 e2e 테스트를 실행하는 단계입니다:

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

- 애플리케이션을 배포하세요: npm run sls -- deploy --stage `당신의-임시환경이름`
- export STAGE=`당신의-임시환경이름`. 이것은 .env.`당신의-임시환경이름`을 참조하기 위한 것입니다.
- npm run e2e-test

![효과적으로 서버리스 애플리케이션을 테스트하는 방법](/assets/img/2024-05-18-Effectivelytestyourserverlessapplications_5.png)

엔드 투 엔드 테스트는 API가 제대로 작동하는지 확인하는 데 많은 가치를 줄 수 있습니다. 그러나 우리가 e2e 테스트에 의존하기만 한다면 생산성이 크게 감소할 것입니다. 왜냐하면 앞서 설명한대로 매 코드 변경마다 배포해야 하기 때문에 개발자 경험을 향상시키는 목적을 반감시키게 됩니다.

# 다음 단계와 결론

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

위의 예시를 보면 단위 테스트가 없다는 것을 알 수 있습니다. 신중한 고려 끝에, createBlogPost 람다 함수에 대한 단위 테스트를 만드는 것은 전혀 가치가 없다는 결론에 도달했습니다. 단순히 요청 본문을 가져와 해당 값을 데이터베이스에 저장하는 것뿐입니다. 그 안에 사용자 정의나 복잡한 로직이 전혀 없었기 때문입니다.

그러나 우리 람다 함수의 이 코드 블록이 우리에게 단위 테스트를 작성해야 한다는 필요성을 알려준다고 주장할 수 있습니다:

```js
...

 const requestBody = JSON.parse(event.body || "{}");
 const title = requestBody.title;
 const content = requestBody.content;

 if (!title || !content) {
  return {
   statusCode: 400,
   body: JSON.stringify({
    message: "Title and content are required",
   }),
   headers: {
    "Content-Type": "application/json",
   },
  };
 }

...
```

그렇습니다, 제가 동의합니다. 이 코드 블록은 제목과 내용 필드가 없는지 확인하고 오류를 반환합니다. 이 코드 블록에 대한 단위 테스트를 작성하면 테스트 커버리지를 향상시킬 것이 확실합니다. 그러나 방금 전 언급했듯이 투자 수익에 관한 문제입니다. API Gateway 및 DynamoDB 테이블 관련 테스트가 즉시 필요한 "블로그 글 작성" 전체 구현의 다른 영역이 있습니다.

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

서버리스로의 전환은 특히 기존의 전통적인 단일 구조 모델에 익숙한 개발자들에게 중요한 패러다임 변화를 의미합니다. 확장 가능성, 비용 효율성 및 운영 효율성의 매력을 제공하면서도, 응용 프로그램이 디자인, 배포 및 관리되는 방식에 근본적인 변화를 요구합니다. 이 전환의 초기 단계는 상태 없는 컴퓨팅, 이벤트 주도 아키텍처 및 제3자 서비스 및 API 통합과 같은 복잡성을 다루면서 특히 어려울 수 있습니다. 이러한 급격한 학습 곡선은 필수적이며 학습 및 실험에 시간과 자원을 투자해야 합니다.

서버리스 솔루션을 구현하려고 노력한 모든 사람들은 이러한 문제를 모두 경험했으며, 제 자신도 개발자 경험을 개선하기 위해 몇 달의 연구와 개발을 진행했습니다. 서버리스는 정말 아름답고 당연히 계속 사용될 것입니다. 특히 중소기업 (MSMEs)을 위한 서버리스는 비용 효율적인 인프라 유형일 수 있지만, 여전히 완전히 새로운 개발자 경험을 받아들일 수 있는 학습 곡선이 존재합니다.

결론적으로, 초기 장벽에도 불구하고, 서버리스 아키텍처를 숙달하는 여정은 매우 보람 있을 수 있습니다. MSMEs의 경우, 확장성, 비용 절감 및 신속한 혁신 능력 측면에서 수익이 명백합니다. 기술이 성숙해지고 주변 커뮤니티가 성장함에 따라, 아마도 현재의 피드백 루프가 이러한 전환을 용이하게 만들고 동시에 현재의 개발자 경험을 향상시킬 수 있을지도 모릅니다. 따라서, 초기 도전은 중요하지 않더라도, 장기적으로는 서버리스 컴퓨팅의 혜택이 기업이 클라우드를 활용해 디지털 전환 계획을 추진하는데 매력적인 선택지로 만듭니다.
