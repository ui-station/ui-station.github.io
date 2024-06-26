---
title: "Vault 비밀, 엔진, 경로 및 더 많은 작동 방식"
description: ""
coverImage: "/assets/img/2024-05-17-HowVaultsecretsenginespathsandmorework_0.png"
date: 2024-05-17 18:26
ogImage:
  url: /assets/img/2024-05-17-HowVaultsecretsenginespathsandmorework_0.png
tag: Tech
originalTitle: "How Vault secrets, engines, paths and more work"
link: "https://medium.com/@martin.hodges/vault-secrets-engines-paths-and-roles-explained-aa3e1a84037d"
---

## Hashicorp Vault은 구성 관리를 중앙 집중화하는 시크릿 관리 시스템입니다. 처음에 만났을 때 너무 방대한 설명서 때문에 헷갈렸었어요. 제게 제공된 정보가 제가 쉽게 이해할 수 있는 형태가 아니었거든요. 그래서 이 기사를 작성하여 그 간극을 좁히는 데 도움이 되길 바래봅니다.

![HowVaultsecretsenginespathsandmorework](/assets/img/2024-05-17-HowVaultsecretsenginespathsandmorework_0.png)

이 기사에서는 Hashicorp 자습서를 자세히 살펴보기 전에 이해해야 할 개념에 대해 설명했습니다. 여기서 목표는 작업 방법을 보여주는 것이 아니라 작동 원리를 설명하는 것입니다.

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

본 글에서 Vault의 설치 과정을 다루지는 않았지만 다른 문서에서 다루었으니 참고하세요.

# 기본 개념

Vault가 작동하는 방식에 기본적으로 필요한 여러 개념이 있습니다:

- 네임스페이스
- 시크릿
- 시크릿 엔진
- 경로
- 인증 방법
- 정책
- 토큰
- 래핑된 토큰
- 개체와 별칭
- 그룹 및 별칭
- 역할

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

만약 이 내용을 이해하지 못한다면, Vault를 사용 사례에 어떻게 적용해야 하는지 이해하는 데 어려움을 겪을 수도 있어요.

## 네임스페이스

네임스페이스는 동일한 배포 내에서 가상 Vault 인스턴스를 만들어냅니다.

네임스페이스는 서로 독립적으로 Vault의 설정을 관리하는 다른 팀, 고객 또는 테넌트를 허용합니다. 네임스페이스 내에서 비밀, 엔진, 정책 등은 다른 네임스페이스에서 완전히 격리되어 있어요.

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

비기업 사용자의 경우, 네임스페이스를 무시할 수 있습니다.

## 비밀 정보

알고 계실지도 모르겠지만 Vault는 비밀 정보를 관리합니다. 그렇다면 비밀 정보란 무엇일까요?

비밀 정보란 기본적으로 접근 권한을 엄격히 제어해야 하는 정보입니다.

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

Vault는 모든 유형의 비밀을 관리할 수 있지만, 특정 유형의 비밀을 처리하기 위해 내장된 기능이 있습니다. 이러한 유형은 다음과 같습니다:

- 키-값 쌍
- 비밀번호
- 데이터베이스 접근
- TLS 키 및 인증서
- API 키
- 암호화 키
- SSH 키
- 토큰
- 등등…

배포 구성에 따라 비밀의 크기 제한이 있습니다. 기본적으로 통합(raft) 저장 계층을 사용하는 경우, 제한은 1MiB입니다.

## 시크릿 엔진

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

시크릿 엔진은 Vault가 특정 유형의 시크릿을 관리할 수 있도록 하는 플러그인으로 볼 수 있습니다. 실제로, 나는 엔진을 시크릿 관리자로 생각하지만 Vault에서는 엔진이라고 부릅니다.

![image](/assets/img/2024-05-17-HowVaultsecretsenginespathsandmorework_1.png)

각 시크릿 엔진은 고유한 기능을 가지고 있지만, 전반적으로 시크릿을 다음과 같이 허용합니다:

- 생성, 읽기, 수정 및 삭제
- 암호화\*
- 만료 시간 (TTL)에 따라 만료
- TTL 새로 고침되는지 여부에 관계없이 최대 수명 후 만료
- 자동으로 생성되어 클라이언트 및 서버 응용 프로그램에 삽입됨
- 취소됨
- 버전 관리됨

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

엔진의 예로는 특정 권한 범위를 설정받은 일시적인 로그인 자격 증명을 생성할 수 있는 데이터베이스 엔진이 있습니다. 요청에 새 자격 증명을 생성함으로써 해당 자격 증명을 회전시킵니다.

시크릿 엔진은 활성화 및 비활성화됩니다. 활성화되면 필요한 시크릿 관리 서비스를 제공하기 위해 구성됩니다. 비활성화되면 엔진은 모든 구성과 시크릿을 포함한 모든 정보가 삭제되며, 이것들은 저장소에서 삭제되어 되돌릴 수 없습니다.

동일 유형의 엔진을 여러 인스턴스로 활성화하는 것이 가능하다는 점을 기억해야 합니다. 예를 들어 3개의 KV 엔진과 2개의 데이터베이스 엔진을 활성화할 수 있습니다. 이는 추가적인 보안을 제공합니다. 한 엔진 인스턴스는 같은 유형의 다른 엔진에서부터 아무 것도 접근할 수 없습니다.

Vault 내부에서 사용되는 Identity 시크릿 엔진이 있지만 이에 대해서는 나중에 자세히 알아보겠습니다.

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

## 경로

파일 시스템과 같이 Vault는 비밀을 경로를 통해 추적합니다. 컴퓨터에서는 /my-work/docs/myreport.txt에 파일이 있을 수 있습니다. Vault를 사용할 때는 /my-secrets/my-app/login에 비밀이 있을 수 있습니다.

비밀의 경로는 세 부분에서 구성됩니다:

`namespace`/`엔진 마운트 지점`/`비밀 경로`

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

엔진을 활성화하면 엔진 마운트 포인트에서 그렇게 합니다. 기본적으로 이 마운트 포인트는 엔진의 이름으로 지정됩니다 (예: 데이터베이스) 하지만 이를 변경하거나 경로를 포함할 수도 있습니다. 예를 들어 my-dbs/postgres와 같이 이름을 지정할 수 있습니다. 이렇게 하면 다른 엔진을 my-dbs/my-sql과 같은 위치에서 활성화할 수 있습니다.

경로의 두 번째 부분은 마운트 포인트 내의 특정 Secret의 경로를 식별합니다 (즉, 엔진 내에서).

예를 들어 (이 예시에서 마운트 포인트는 ``를 사용하여 식별되었습니다):

```js
<kv>/path/to/my/kv-secret
<database>/path/to/my/postgres-secret/credentials
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

어떤 비밀 정보도 같은 엔진 내에서 동일한 하위 경로를 가질 수 없습니다. 이것은 말이 되지만(마운트 지점과 달리) 일부 하위 경로를 공유할 수도 있습니다. 다음과 같이:

```js
<database>/path/to/my/postgres-secret/credentials
<database>/path/to/my/mysql-secret/credentials
<database>/path/to/my/oracle-secret/credentials
```

UI 내에서 이것은 폴더 구조처럼 작동하여 엔진(database) - `path` - `to` - `my` - `postgres-secret`까지 credentials까지 이동할 수 있습니다.

경로에 연결된 것은 비밀 정보뿐만 아니라 Vault에서 관리하는 리소스에도 해당되며, 인증 방법과 정책도 경로를 가지고 있습니다.

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

경로를 떠나기 전에, 리소스에 대한 작업 또한 경로를 통해 참조될 수 있다는 점을 이해하는 것이 중요합니다. 이 경우 경로는 다음과 같이 생성됩니다:

`namespace`/`engine mount point`/`path to secret`/`action`

## 인증 방법

지금까지 Secrets에 대해 이야기했지만, 이제는 어떻게 해당 Secrets에 접근하는지 살펴보아야 합니다.

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

Vault에 액세스하는 세 가지 방법이 있습니다:

- 사용자 인터페이스(UI)를 통해
- 명령 줄 도구(CLI)를 통해
- HTTP REST API(API)를 통해

사실, 이들은 모두 주로 API를 사용합니다. 이것을 이해하는 것이 중요한데, UI나 CLI에서 수행하는 작업에 대해 curl 명령어를 구성할 수 있다는 의미입니다. 스크립트를 작성할 때 매우 유용합니다.

‘우리’라는 말을 사용할 때, 물리적 서버, 애플리케이션 또는 사람 모두 식별(즉, 인증)되어야 하고, Vault를 사용하기 전에 특정 비밀(Secret)에 액세스하도록 허용(즉, 권한 부여)되어야 합니다. 권한 부여에 대해서는 나중에 다시 다루겠습니다.

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

우리는 고객을 '우리'라고 부르겠습니다.

그래서, Vault는 고객을 어떻게 인증하나요? 사용 가능한 여러 가지 방법이 있습니다. Vault는 이러한 방법을 Auth 방법이라고 부릅니다:

- AppRole
- OAuth JWT
- OIDC
- TLS 인증서
- 사용자명/암호
- 클라우드 공급업체 (예: AWS, Azure, Google Cloud)
- Kubernetes 서비스 계정
- LDAP
- 그 외...

고객은 Auth 방법 중 하나를 사용하여 인증하고, 한 번 인증되면 클라이언트 토큰이 제공되어 클라이언트가 수행하려는 작업을 수행할 수 있는지 여부를 결정하는 데 사용됩니다.

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

![HowVaultsecretsenginespathsandmorework](/assets/img/2024-05-17-HowVaultsecretsenginespathsandmorework_2.png)

각 Auth Method는 클라이언트를 인증하는 고유한 방법을 갖고 있습니다. 일부는 사전 공유된 시크릿을 사용하고, 일부는 Vault 내에 저장된 로그인 자격 증명을 사용하며, 일부는 AWS IAM과 같은 타사 서비스를 사용합니다. Kubernetes 내의 서비스 계정을 사용하여 인증하는 것도 가능합니다.

동일한 유형의 여러 Engine을 활성화할 수 있는 것처럼, Auth Method도 마찬가지입니다. 엔진과 마찬가지로, 활성화된 각 Auth Method에는 마운트 지점이 지정되어 UI, CLI 및/또는 API를 통해 참조할 수 있습니다. 기본 이름이 부여되거나 고유한 이름을 정의할 수 있습니다. 모든 Auth Method는 auth/ 경로 아래에 마운트됩니다. 예: auth/`auth method 이름`.

## 정책

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

클라이언트가 비밀에 액세스하는 방법을 알아보기 전에 정책에 대해 이해해야 합니다.

정책은 권한을 부여합니다. 하나 이상의 정책에서 권한을 부여하지 않은 경우 권한이 부여되지 않습니다.

정책을 만들 때, 명령줄을 통해 직접 만들 수도 있지만 파일에 추가한 다음 파일을 로드하는 것이 더 쉽습니다. 정책은 Hashicorp Configuration Language (HCL)로 작성됩니다.

정책은 특정 경로에 부여되는 권한을 정의합니다. 액션을 포함할 수 있는데, 예를 들어 auth/token/lookup-self와 같습니다. 그런 다음 해당 경로에서 허용된 기능이 권한으로 정의됩니다. 기본 정책의 일부는 다음과 같습니다:

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

```json
# 자신의 속성을 조회할 수 있는 토큰 허용
path "auth/token/lookup-self" {
    capabilities = ["read"]
}

# 자신을 갱신할 수 있는 토큰 허용
path "auth/token/renew-self" {
    capabilities = ["update"]
}

# OIDC 공급자의 인증 엔드포인트로 요청을 보낼 수 있는 토큰 허용
path "identity/oidc/provider/+/authorize" {
    capabilities = ["read", "update"]
}
```

위와 같이 정책은 여러 경로에 매핑되고 여러 기능을 허용할 수 있음을 알 수 있습니다. 경로 끝에 \*를 포함하면 해당 경로의 모든 하위 경로에 정책이 적용됩니다. 경로의 leaf를 /+/로 대체하면 해당 leaf의 모든 값을 일치시킵니다. 이는 경로에 ID가 포함된 경우 유용합니다.

권한은 누적되므로 사용자가 여러 정책과 관련이 있는 경우 모든 정책에서 모든 권한을 갖습니다.

그러나 정책들이 함께 수집될 때 두 정책 경로가 겹칠 때, 예를 들어 secrets/\* 및 secrets/my-secret과 같은 경우, 더 구체적인 경로의 기능이 사용됩니다. 더 정확히는 Hashicorp 문서에 따르면, 우선순위 순서로 두 유사한 경로 p1 및 p2가 주어질 때, p2가 선호되며 다음과 같은 경우 선택됩니다:

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

- 만약 p1에 \* 또는 +이 p2보다 왼쪽에 나타나면
- p1에 \*가 있고 p2에 없을 경우
- p1에 +가 p2보다 더 많을 경우
- p1이 p2보다 짧을 경우
- 사전에 p2보다 p1이 먼저 나타나야 할 경우

다시 말해, 경로가 더 구체적일수록 선호됩니다.

![image](/assets/img/2024-05-17-HowVaultsecretsenginespathsandmorework_3.png)

기능에는 (그들의 HTTP 동사를 포함하여):

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

- 생성 [POST/PUT]
- 조회 [GET]
- 업데이트 [POST/PUT]
- 패치 [PATCH]
- 삭제 [DELETE]
- 목록 [LIST]

HTTP 동사와 직접적으로 관련이 없는 세 가지 기능이 더 있습니다:

- sudo — root로 보호된 경로에 액세스 허용
- deny — 다른 기능 및 정책과 관계없이 모든 액세스 거부
- subscribe — 이벤트 유형에 대한 구독 허용

경로 일치를 통해 보안정책이 Secrets와 다른 리소스와 관련되어 있는 방식을 볼 수 있습니다. 그러나 아직 우리가 보지 않은 것은 보안정책이 요청과 어떻게 연관되어 있는지입니다. 그것을 알아보기 위해선 엔티티와 그룹을 이해해야 합니다.

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

이 문서는 정책의 기본 사항만 다루었습니다. 자세한 내용은 Hashicorp 문서를 참조하십시오.

## 엔티티와 별칭

엔티티는 Vault 내에서 클라이언트를 나타냅니다. 클라이언트는 사람, 애플리케이션 및 시스템으로 나타낼 수 있다는 점을 알아보았습니다. 모두 엔티티로 나타낼 수 있습니다.

특정 엔티티(예: 당신 또는 나)는 여러 Auth 방법(예: 사용자 이름/암호, GitHub 계정 등)을 통해 인증될 수 있습니다. 즉, 각 Auth 방법에서 엔티티를 나타내는 신원이 해당 엔티티에 연결되어야 하는 중요합니다. 이 작업은 Entity Aliases를 통해 수행됩니다.

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

한 Entity는 0개 이상의 Entity Aliases를 가질 수 있습니다.

![Image](/assets/img/2024-05-17-HowVaultsecretsenginespathsandmorework_4.png)

이전에 Policies에 대해 이야기했습니다. 각 Entity는 하나 이상의 Policies와 관련될 수 있습니다. 클라이언트가 인증하는 방식에 상관없이, 그들은 Entity와 관련된 Policies에 제공된 권한을 받게 됩니다.

우리는 이것이 Policy가 Entity에 연관될 수 있는 유일한 방법이 아니라는 것을 볼 것입니다.

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

## 그룹 및 별칭

Entity는 유용하지만 액세스 관리가 어려워질 수 있습니다. 왜냐하면 각 Policy를 모든 Entity에 연결해야하기 때문입니다.

대부분의 조직은 팀과 다부서 팀을 사용하여 구조화됩니다. 각 팀은 각자의 책임을 부여받습니다.

Vault는 이를 그룹을 사용하여 모델링합니다. 그룹은 임의의 수의 Entity를 보유할 수 있으며 Entity는 임의의 수의 그룹에 속할 수 있습니다. 그룹은 부모-자식 관계로 다른 그룹을 포함 할 수도 있습니다. Entity가 그룹의 일부인 경우 해당 그룹은 직접 그룹으로 알려집니다. 어떤 부모, 할아버지, 증조부모 등도 간접적 그룹으로 알려집니다.

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

![image](/assets/img/2024-05-17-HowVaultsecretsenginespathsandmorework_5.png)

이제 그룹은 정책과 연관시킬 수 있습니다. 해당 그룹의 모든 엔티티는 해당 그룹이나 부모, 조부모 그룹의 정책을 받습니다.

이제 엔티티 그룹의 액세스를 한 곳에서 관리할 수 있습니다.

그룹 외에도 Vault는 그룹 별칭의 개념을 지원합니다. 이 별칭은 Active Directory 그룹과 같은 외부 그룹과 관련이 있습니다.

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

## 토큰

알겠어요. 우리는 Secrets에 대해 이야기했고, Policies가 Secrets의 경로에 따라 연결되는 방법과 Policies가 엔터티 및 그룹과 연결되는 방법에 대해 이야기했어요.

하지만, 우리가 본 적이 없는 것은 제가 Secret에 대한 액세스를 요청할 때 Vault가 제가 필요한 액세스를 가지고 있는지 여부를 어떻게 알 수 있는지 입니다.

이는 토큰을 사용하여 달성됩니다.

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

토큰은 Vault 내에서 Secrets 및 Auth Methods에 대한 액세스를 관리하는 데 필수적입니다.

클라이언트가 자원(예: 생성, 읽기, 업데이트 및 삭제)을 사용하려면 유효한 토큰을 전달해야 합니다. 토큰을 통해 Vault는 해당 토큰과 관련된 정책 및 따라서 클라이언트의 기능을 결정합니다.

모든 토큰에는 갱신되어야 하는 만료 시간(TTL)이 있습니다. 심지어 토큰 생성 시 최대 TTL이 있을 수 있으며 갱신 중일 때에도 해당됩니다. 이에는 초기화된 Vault에서 생성된 Root Token을 제외합니다.

사용자가 토큰을 생성하도록 요청하면 해당 토큰은 사용자가 인증했을 때 제공된 정책을 상속합니다.

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

이제, 원래 토큰을 사용하여 새로운 토큰을 만드는 경우, 클라이언트는 새로운 토큰과 관련된 정책을 원래 토큰의 하위 집합으로 제한할 수 있습니다. 이를 통해 클라이언트는 '최소 권한' 액세스를 Vault에 요청할 수 있습니다.

토큰은 인증된 클라이언트(개체)를 해당 개체에 연결된 정책과 관련시켜주며, 이러한 것들은 개체가 리소스에 액세스할 수 있는 권한을 제공합니다. 이러한 방식으로 Vault는 클라이언트가 리소스에 수행할 수 있는 권한이 있는지를 결정할 수 있습니다.

예를 들어, 사용자는 사용자 이름과 비밀번호를 사용하여 로그인하고 시크릿을 생성하고 읽는 데 사용할 수 있는 토큰을 받을 수 있지만 업데이트는 할 수 없는 토큰을 받을 수 있습니다.

토큰이 발급될 때 클라이언트에게 부여되는 권한은 다음의 조합입니다:

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

- 인증에 사용된 토큰들
- 클라이언트를 대표하는 엔티티에게 주어진 토큰들
- 엔티티가 직접 속한 그룹의 토큰들
- 엔티티가 간접적으로 속한 그룹의 토큰들

토큰에는 다음과 같은 속성이 있습니다:

- 선택적인 유효기간 (TTL)
- 갱신할 수 있는 능력 (또는 그렇지 않을 수도 있음)
- 취소할 수 있는 능력

토큰을 획득한 후 원본의 자식이 되는 다른 토큰을 얻는 것이 가능합니다. 이는 두 가지 이유로 유용합니다:

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

- 부모 토큰을 폐기하면 모든 하위 토큰이 재귀적으로 폐기됩니다.
- 토큰을 요청할 때

토큰에 대한 자세한 내용은 다음 해시코프 문서를 참조하십시오.

## 래핑된 토큰

래핑된 토큰은 일회용 토큰입니다. 클라이언트와의 핸드셰이크에 유용하며 클라이언트에게 원본 토큰을 전달하는 데 사용됩니다.

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

설명된 방식은 Wrapped Token이 원래 Token을 참조하는 것이라고 합니다.

Wrapped Token을 받으면 Vault에 Original Token을 요청하여 얻을 수 있습니다. 이미 언래핑된 경우 오류가 발생하고 위반이 발생했음을 알 수 있습니다. 원본 Token은 취소될 수 있고 새로운 Token을 생성할 수 있습니다.

Vault는 임의의 정보를 래핑하는 데 필요한 도구도 제공합니다.

## Roles

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

기본 개념에서 Vault의 기본 개념을 떠날 때 전통적인 보안 세계에서 역할에 대해 얘기할 가치가 있다고 생 생각해요. 역할은 하나 이상의 사용자에게 권한 집합을 적용하는 데 사용됩니다. 이렇게 하면 사용자 그룹의 구성이 더 쉬워지며, 특정 권한 집합을 가져야 하는 사용자는 그 역할을 부여받습니다.

그룹에 연결된 정책은 이와 같은 방식으로 작용합니다. 그룹은 전통적인 역할과 유사하게 볼 수 있으며, 엔티티 그룹을 권한 집합(또는 Vault가 그들을 언급하는 기능)에 연결합니다.

주변을 둘러보면 역할이 Vault와 관련해 다음 두 가지 방법으로 언급된다는 것을 알게 될 거예요:

- 데이터베이스 역할
- 토큰 역할
- AppRole 역할

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

데이터베이스 역할: 이러한 역할은 실제로 데이터베이스 엔진과 연관되어 있으며 데이터베이스 내의 역할을 나타냅니다. 즉, 자격 증명이 요청될 때 특정 데이터베이스 역할을 위해 요청할 수 있습니다.

토큰 역할: 토큰 역할을 생성할 수 있으며 이는 일련의 프리셋 또는 토큰의 템플릿으로 작동할 수 있습니다. 한 번 생성되면 지정된 토큰 역할에 기반한 자식 토큰을 생성할 수 있습니다.

앱롤 역할: AppRole 인증 방법을 사용하는 경우 AppRole 역할을 만들 수 있습니다. 이를 통해 응용 프로그램이이 방법으로 인증할 때 수신할 권한 및 기타 특성을 정의할 수 있습니다. AppRole 역할에는 응용 프로그램이 제출해야하는 ID가 부여됩니다. 그런 다음 응용 프로그램이 제출해야하는 비밀 ID를 생성할 수도 있습니다. 이렇게하면 AppRole 역할의 정책과 관련된 토큰을 얻을 수 있습니다.

# 요약

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

이 기사에서 Vault 뒤에 있는 기본 개념을 소개했어요. 네임스페이스, 마운트 포인트 및 경로를 통해 비밀 및 다른 리소스를 찾는 방법을 살펴봤어요.

또한 엔티티 및 인증 방법이 어떻게 사용되는지, 매칭 경로를 통해 리소스 액세스를 제어하기 위해 관련 정책이 있는 토큰을 생성하는 방법도 살펴봤어요.

이러한 개념을 이해하면 Hashicorp의 제공하는 문서를 더 쉽게 이해할 수 있을 거예요.

이 기사가 흥미로웠다면, 제게 박수를 한 번 부탁드릴게요. 이를 통해 사람들이 유용하게 여기는 것과 앞으로 써야 할 기사에 대한 정보를 얻을 수 있어요. 아이디어가 있으시면 댓글에 추가해주세요.
