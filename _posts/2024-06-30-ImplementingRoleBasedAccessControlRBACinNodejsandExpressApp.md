---
title: "Nodejs와 Express 앱에서 역할 기반 접근 제어RBAC 구현하는 방법"
description: ""
coverImage: "/assets/img/2024-06-30-ImplementingRoleBasedAccessControlRBACinNodejsandExpressApp_0.png"
date: 2024-06-30 21:53
ogImage: 
  url: /assets/img/2024-06-30-ImplementingRoleBasedAccessControlRBACinNodejsandExpressApp_0.png
tag: Tech
originalTitle: "Implementing Role Based Access Control (RBAC) in Node.js and Express App"
link: "https://medium.com/permify-tech-blog/implementing-role-based-access-control-rbac-in-node-js-and-express-app-87fe692c0b02"
---


이 기사에서는 단 몇 분 안에 Node.js 및 Express 애플리케이션에 Role Based Access Control (RBAC)을 구현하는 방법을 알려드립니다.

![이미지](/assets/img/2024-06-30-ImplementingRoleBasedAccessControlRBACinNodejsandExpressApp_0.png)

응용 프로그램 내에서 특정 기능 및 데이터에 액세스할 수 있는 권한이 있는 사용자만이 액세스할 수 있도록 보장하기 위해 Role-Based Access Control (RBAC)을 구현하는 것이 중요합니다.

이 기사에서는 Permify를 사용하여 Node.js 및 Express 애플리케이션에 RBAC을 구현하는 방법을 안내해드릴 것입니다.

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

# Node.js Express 프로젝트 설정하기

Express.js 프로젝트를 위한 애플리케이션 뼈대를 빠르게 생성하려면 express-generator 도구를 사용할 수 있습니다. 시작하기 위해 다음 단계를 따르세요:

## 단계 1. express-generator 설치하기:

Node.js 버전 8.2.0 이상을 사용 중이라면 npx 명령어를 사용하여 애플리케이션 생성기를 실행할 수 있습니다:

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
npx express-generator
```

이전 Node 버전의 경우, 애플리케이션 생성기를 글로벌 npm 패키지로 설치할 수 있어요:

```js
npm install -g express-generator
```

# 단계 2. Express 애플리케이션 생성하기:

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

express-generator을 설치하셨으면 Express 애플리케이션을 생성할 수 있어요. 다음 명령어를 사용하여 옵션을 확인할 수 있어요:

```js
express -h
```

이렇게 하면 Express 애플리케이션 생성을 위한 가능한 옵션을 확인할 수 있어요.

예를 들어, Pug 뷰 엔진을 사용하여 permify-rbac-app이라는 Express 앱을 생성하려면 다음과 같이 실행할 수 있어요.

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
express --view=pug permify-rbac-app
```

이 명령어는 현재 작업 디렉토리에 permify-rbac-app이라는 폴더를 생성하고 Express 애플리케이션에 필요한 파일과 폴더를 함께 만듭니다.

# 단계 3. 종속성 설치:

새로 만든 Express 애플리케이션 디렉토리로 이동하세요:

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
cd permify-rbac-app
```

그런 다음 npm을 사용하여 프로젝트 종속성을 설치하세요:

```js
npm install express && npm install @permify-node
```

# 단계 4. Express 애플리케이션 시작하기:

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

MacOS 또는 Linux에서는 다음 명령어로 앱을 시작할 수 있어요:

```js
DEBUG=permify-rbac-app:* npm start
```

# 단계 5. 애플리케이션에 액세스하기:

Express 애플리케이션이 실행되면 브라우저에서 http://localhost:3000/을 통해 액세스할 수 있어요.

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

# 디렉토리 구조:

생성된 Express 애플리케이션은 다음과 같은 디렉토리 구조를 가지게 됩니다:

```js
permify-rbac-app/
├── app.js
├── bin/
│   └── www
├── package.json
├── public/
│   ├── images/
│   ├── javascripts/
│   └── stylesheets/
│       └── style.css
├── routes/
│   ├── index.js
│   └── users.js
└── views/
    ├── error.pug
    ├── index.pug
    └── layout.pug
```

이 구조에는 주요 애플리케이션 파일(app.js), 서버 구성(bin/www), 라우트(routes), 뷰(views), 공용 에셋(public assets), 그리고 프로젝트 종속성이 포함된 package.json 파일이 포함되어 있습니다.

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

Express.js 프로젝트를 설정했으니, 이제 Express 애플리케이션에 RBAC를 구현해 보겠습니다.

# Node.js 및 Express에서 RBAC 구현하기

Node.js 및 Express 애플리케이션에 Role-Based Access Control (RBAC)을 구현하는 것에는 몇 가지 단계가 연루됩니다. 다음은 기본적인 구현 방법입니다:

# 단계 1: Permify를 사용하여 RBAC 모델 설계하기

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

Permify는 개방 소스 권한 부여 서비스로, 개발자들이 권한 시스템을 만들 수 있게 합니다. Permify를 이용하면 권한을 모델링하고, 환경에 중앙 권한 서비스를 만들어 애플리케이션 및 서비스에서 액세스 확인을 수행할 수 있습니다.

이를 위해 클라이언트 SDKs를 제공하여, 미들웨어에 추가하여 접근 확인과 같은 권한 요청을 보낼 수 있습니다.

Permify는 역할, 권한 및 관계를 정의하기 위한 강력한 도메인 특화 언어(DSL)를 제공합니다. Permify Playground를 활용하여 RBAC 모델을 실험하고 시각화할 수 있습니다.

![이미지](/assets/img/2024-06-30-ImplementingRoleBasedAccessControlRBACinNodejsandExpressApp_1.png)

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

이 게시물에서는 귀하의 조직 내 사용자가 역할에 따라 문서에 액세스할 수 있는 간단한 파일 기반 권한 부여 시스템을 개발할 것입니다.

행정자, 관리자 및 직원과 같은 다양한 역할은 파일을 보기, 편집 또는 삭제하는 데 서로 다른 수준의 액세스 권한을 가질 수 있습니다.

## Permify DSL을 사용한 역할 및 권한 정의

다음은 RBAC 모델을 위한 Permify DSL 스키마 예시입니다:

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
entity user {} 

entity organization {
    // roles 
    relation admin @user    
    relation member @user    
    relation manager @user    
    relation agent @user  
    
    // organization files access permissions
    permission view_files = admin or manager or (member not agent)
    permission delete_file = admin 
    
    // vendor files access permissions
    permission view_vendor_files = admin or manager or agent
    permission delete_vendor_file = agent
}
```

Roles and Permissions:

- Roles: The schema defines roles for the organization entity, including admin, member, manager, and agent. These roles determine the level of access and permissions users have within the organization.
- Permissions: Actions such as `view_files`, `edit_files`, `delete_file`, `view_vendor_files`, `edit_vendor_files`, and `delete_vendor_file` define the specific permissions associated with each role. For example, only admins can delete organization files, while managers and members have different levels of access.

Resource Types:

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

- 스키마는 조직 파일과 공급 업체 파일을 구별하여 각각의 권한 집합을 가지고 있습니다. 이는 응용 프로그램 내의 다른 유형의 리소스에 대한 접근을 세밀하게 제어할 수 있도록 합니다.

이제 RBAC 스키마를 정의했으니 Permify Local Server를 설정하는 단계로 넘어갈 것입니다.

# 단계 2: Docker를 사용하여 Permify Local Server 설정하기

Docker는 컨테이너화된 환경을 제공함으로써 우리의 설정에서 중요한 역할을 합니다.

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

이 환경은 모든 권한 쿼리를 처리하는 마이크로서비스인 Permify의 효율적인 작동을 위한 필수 요소입니다.

이제 Docker Container를 사용하여 Permify Server를 설정하는 필요한 단계를 설명하겠습니다:

## Docker Container를 사용하여 Permify Server 실행

- 터미널 창을 열고 다음 명령을 실행하여 Permify Server Docker 이미지를 가져오고 컨테이너를 시작합니다:

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


sudo docker run -p 3476:3476 -p 3478:3478 ghcr.io/permify/permify serve


이 명령어는 GitHub Container Registry에서 Permify Server 이미지를 다운로드하고 다음의 기본 설정으로 Permify, 저희의 권한 부여 서비스를 설정합니다:

- REST API는 3476 포트에서 실행됩니다.
- gRPC 서비스는 3478 포트에서 실행됩니다.
- 권한 데이터는 컴퓨터 메모리에 저장됩니다.

다음과 유사한 메시지가 표시됩니다:

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


┌────────────────────────────────────────────────────────┐
│                    Permify v0.9.5                      │
│          Fine-grained Authorization Service            │
│                                                        │
│    docs: ............... https://docs.permify.co       │
│    github: .. https://github.com/Permify/permify       │
│    blog: ............... https://permify.co/blog       │
│                                                        │
└────────────────────────────────────────────────────────┘
time=2024-03-22T14:59:09.851Z level=INFO msg="🚀 starting permify service..."
time=2024-03-22T14:59:09.851Z level=ERROR msg="Account ID is not set. Please fill in the Account ID for better support. Get your Account ID from https://permify.co/account"
time=2024-03-22T14:59:09.859Z level=INFO msg="🚀 grpc server successfully started: 3478"
time=2024-03-22T14:59:09.859Z level=INFO msg="🚀 invoker grpc server successfully started: 5000"
time=2024-03-22T14:59:09.867Z level=INFO msg="🚀 http server successfully started: 3476"


## Permify 서버 확인

컨테이너가 실행 중이면 health check endpoint에 액세스하여 Permify 서버가 정상적으로 작동하는지 확인할 수 있습니다. Postman을 열고 http://localhost:3476/healthz로 GET 요청을 보내세요. Permify 서버가 올바르게 실행 중이면 서비스가 건강한 것을 나타내는 응답을 볼 수 있어요.

<img src="/assets/img/2024-06-30-ImplementingRoleBasedAccessControlRBACinNodejsandExpressApp_2.png" />


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

위의 이미지를 보면 Permify 서버가 작동 중이라는 것을 확인할 수 있습니다. 이제 Node.js 및 Express 애플리케이션에 통합하는 작업을 진행할 수 있습니다.

# 단계 3: Permify Node.js 클라이언트 초기화

이 튜토리얼에서는 Permify Node 클라이언트를 사용하여 응용 프로그램의 권한 부여를 제어합니다. Permify Swagger 문서에서 사용 가능한 엔드포인트 목록을 찾을 수 있습니다. 우리는 Permify의 접근 제어 체크를 사용하여 엔드포인트를 안전하게 보호할 것입니다.

이제 클라이언트를 초기화해 봅시다.

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

```javascript
// create-tenant.js
const permify = require("@permify/permify-node");

const client = new permify.grpc.newClient({
    endpoint: "localhost:3478",
})
```

# 단계 4: 권한 모델 구성

이제 Permify 서버가 실행 중이므로 권한 모델을 구성해야 합니다. 이후에 테스트 액세스 확인을 수행할 준비가 될 것입니다.

권한 모델을 구성하려면 생성한 스키마를 Permify에게 Permify schema.write 메서드를 사용하여 전송합니다.

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
//create-schema.js

const permify = require("@permify/permify-node");

const client = new permify.grpc.newClient({
    endpoint: "localhost:3478",
})

client.schema.write({
    tenantId: "t1",
    schema: "entity user {} \n\nentity organization {\n\n    relation admin @user    \n    relation member @user    \n    relation manager @user    \n    relation agent @user  \n\n    action view_files = admin or manager or (member not agent)\n    action edit_files = admin or manager\n    action delete_file = admin\n    action view_vendor_files = admin or manager or agent\n    action edit_vendor_files = admin or agent\n    action delete_vendor_file = agent\n\n} "
}).then((response) => {
    // handle response
    console.log(response)
})
```

위 코드는 Permify 라이브러리를 사용하여 새로운 스키마를 생성합니다.

이 코드는 localhost의 3478 포트에서 실행 중인 Permify 서버에 연결하도록 구축되었으며, write 메서드를 호출하여 t1 테넌트를 위한 스키마를 정의합니다.

이 스키마는 사용자 및 조직과 관련관계 및 작업과 같은 엔티티를 정의합니다.

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

이제 이 스크립트를 실행해 볼게요

```js
node create-schema.js
```

![스크린샷 이미지](/assets/img/2024-06-30-ImplementingRoleBasedAccessControlRBACinNodejsandExpressApp_3.png)

위 스크린샷에서 새로운 스키마가 Permify Node Js Client를 사용하여 성공적으로 구성되었음을 확인할 수 있어요.

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

만세! 🥳 Permify 권한 부여 서비스 설정을 성공적으로 마쳤어요. 이제 권한 모델이 설정되었고 사용할 준비가 끝났답니다!

다음 단계에서는 액세스 제어를 위한 미들웨어를 만들 것입니다.

# 단계 5: 액세스 제어 미들웨어 생성

여기서는 Express 미들웨어를 개발해서 경로에 기반한 역할 기반 액세스 제어를 강제하는 방법을 한 예제로 보여줄게요.

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

Permify 액세스 확인 엔드포인트를 미들웨어에 구현하여 보호된 리소스에 액세스하기 전에 사용자의 역할과 권한을 확인하는 방법도 배울 것입니다.

```js
// auth.js

// Permify 클라이언트 가져오기
const permify = require('@permify/permify-node');

const client = new permify.grpc.newClient({
  endpoint: "localhost:3478",
});

// 사용자의 권한을 확인하는 미들웨어 함수
const checkPermissions = (permissionType) => {
  return async (req, res, next) => {
    try {
      // req.params.id가 존재하는지 확인
      if (!req.params.id) {
        throw new Error('요청 매개변수에서 사용자 ID가 누락되었습니다');
      }

      // 필요에 따라 permissionType을 문자열로 변환
      const permTypeString = String(permissionType);

      // Permify 확인 요청을 위한 데이터 준비
      const checkRes = await client.permission.check({
        tenantId: 't1',
        metadata: {
          schemaVersion: '',
          snapToken: '',
          depth: 20,
        },
        entity: {
          type: 'organization',
          id: "1",
        },
        permission: permTypeString, // 변환된 permissionType 사용
        subject: {
          type: 'user',
          id: req.params.id,
        },
      });

      if (checkRes.can === 1) {
        // 사용자가 권한이 있는 경우
        req.authorized = 'authorized';
        next();
      } else {
        // 사용자가 권한이 없는 경우
        req.authorized = 'not authorized';
        next();
      }
    } catch (err) {
      console.error('권한 확인 중 오류 발생:', err.message); // 실제 오류 메시지 기록
      res.status(500).send(err.message); // 디버깅을 위해 실제 오류 메시지를 클라이언트로 보냄
    }
  };
};

module.exports = checkPermissions;
```

위 코드는 사용자 권한을 확인하기 위해 Permify 라이브러리를 활용한 checkPermission 미들웨어 함수를 구현하는 것을 목표로 구성되었습니다.

실행시 요청 매개변수에서 사용자 ID를 추출하고, 필요에 따라 권한 유형을 문자열로 변환한 후, Permify의 "permission.check" 메서드를 사용하여 Permify 서버에 권한 확인 요청을 보냅니다. 권한이 부여된 경우 요청 객체에 "authorized"를 추가하며, 그렇지 않은 경우 "not authorized"를 추가합니다.

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

오류는 디버깅을 위해 더 자세히 기록되고 클라이언트에 반환됩니다.

다음으로, 이전에 만든 미들웨어를 Node.js 및 Express 애플리케이션에 통합하여 역할 기반 접근 제어 (RBAC)를 강제하고 적절한 역할 및 권한이 있는 인증된 사용자만 특정 경로에 액세스할 수 있도록 보장할 것입니다.

# 단계 6: RBAC로 경로 보호하기

이제 만든 미들웨어를 사용하여 경로를 안전하게 만들어봅시다. 우리의 애플리케이션에서 다양한 경로를 보호하기 위해 checkPermissions 미들웨어를 적용할 것입니다.

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
// app.js

// 필요한 모듈 가져오기
const express = require('express');
const permify = require("@permify/permify-node");
const authMiddleware = require('./auth'); // 인증 미들웨어 가져오기

// Express 앱 생성
const app = express();

// userInfo를 채우는 사용자 지정 미들웨어 정의
app.use((req, res, next) => {
  // 사용자 인증 시뮬레이션 및 userInfo 채우기
  req.userInfo = {
    id: req.params.id // 요청 매개변수에서 id 추출
    // 필요한 경우 다른 사용자 정보 추가
  };
  next();
});

// 라우트 정의

// '/users/:id' 경로에 대한 권한 확인을 적용하려는 경우
app.get('/users/viewFiles/:id', authMiddleware('view_files'), (req, res) => {
  // 미들웨어가 요청을 통과시키면 라우트 로직 처리
  if (req.authorized === 'authorized') {
    res.send('이 사용자 경로에 액세스할 수 있습니다');
  } else {
    res.status(403).send('이 사용자 리소스에 액세스할 권한이 없습니다');
  }
});

// '/admin/deleteVendorFiles/:id' 경로에 대한 권한 확인을 적용하려는 경우
app.get('/admin/deleteVendorFiles/:id', authMiddleware('delete_vendor_file'), (req, res) => {
  // 미들웨어가 요청을 통과시키면 라우트 로직 처리
  if (req.authorized === 'authorized') {
    res.send('이 관리자 경로에 액세스할 수 있습니다');
  } else {
    res.status(403).send('이 관리자 리소스에 액세스할 권한이 없습니다');
  }
});

// 서버 시작
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`서버가 ${PORT}포트에서 실행 중입니다`);
});
```

이 코드는 특정 라우트가 authMiddleware라는 사용자 지정 미들웨어를 사용하여 보호되는 Express 애플리케이션을 포트 3000에서 설정합니다.

이 authMiddleware라는 미들웨어는 auth.js 파일에서 가져온 Permify와 통합하여 권한 확인을 수행합니다. 아래는 미들웨어에 의해 보호되는 라우트입니다;

authMiddleware에 의해 보호되는 라우트:

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

- /users/viewFiles/:id 경로: 이 경로는 파일을 볼 권한을 가진 사용자만 접근할 수 있도록 보장합니다.
- /admin/viewFiles/:id 경로: 이 경로는 업체 파일을 삭제할 권한을 가진 관리자만 접근할 수 있도록 보장합니다.

이러한 경로에 authMiddleware를 적용하여 Permify가 부여한 권한에 따라 접근이 제한됩니다.

구현을 테스트해봅시다!

# RBAC 구현 테스트

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

아래는 Markdown 형식으로 표를 작성하였습니다.


| 사용자ID | 역할         | 
|--------|--------------|
| alice  | 멤버         | 


이 표는 사용자 alice에게 멤버 역할을 부여하기 위한 Permify Nodejs 클라이언트의 data.write 메소드를 사용하는 예시 코드입니다.

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

그 코드를 실행하고 Postman을 사용하여 /users/viewFiles/ API 엔드포인트에 방문해 보세요.

![image](/assets/img/2024-06-30-ImplementingRoleBasedAccessControlRBACinNodejsandExpressApp_5.png)

이제 코드를 실행한 후 Alice는 /users/viewFiles/ API 엔드포인트에 성공적으로 액세스할 수 있습니다.

# 결론

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

인가(authorization)가 한 번만 설정하는 것이 아니라 계속된 과정이라는 것을 인지하는 것이 중요합니다.

따라서 모델을 정기적으로 검토하고 철저한 테스트를 수행하며 애플리케이션이 발전함에 따라 적응시키는 것이 중요합니다.

이 가이드는 Node.js 애플리케이션에 RBAC(Role-Based Access Control)을 구현하는 데 튼튼한 기초 역할을 합니다.

그러나 개별 요구 사항에 정확히 맞게 RBAC 모델을 맞춤화하는 데 더 심층적으로 파고들고 망설이지 마세요.

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

Permify의 기능을 활용하면 권한 관리를 최적화하고 견고하고 안전한 애플리케이션 환경을 육성할 수 있어요.