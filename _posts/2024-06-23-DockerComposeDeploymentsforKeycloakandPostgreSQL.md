---
title: "Keycloak 및 PostgreSQL을 Docker Compose로 배포하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-DockerComposeDeploymentsforKeycloakandPostgreSQL_0.png"
date: 2024-06-23 22:47
ogImage:
  url: /assets/img/2024-06-23-DockerComposeDeploymentsforKeycloakandPostgreSQL_0.png
tag: Tech
originalTitle: "Docker Compose Deployments for Keycloak and PostgreSQL"
link: "https://medium.com/@disa2aka/docker-deployments-for-keycloak-and-postgresql-e75707b155e5"
---

# 키클로크란 무엇인가요?

![이미지](/assets/img/2024-06-23-DockerComposeDeploymentsforKeycloakandPostgreSQL_0.png)

## 중앙 집중식 사용자 관리

키클로크를 사용하면 사용자 관리를 한곳에서 중앙 집중화할 수 있어 여러 애플리케이션과 서비스 간에 사용자, 역할 및 권한을 쉽게 관리할 수 있습니다. 사용자 페더레이션을 지원하여 기존 사용자 디렉터리(예: LDAP 또는 Active Directory)와 통합할 수 있습니다.

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

## 단일 로그인(SSO) 및 단일 로그아웃

Keycloak의 가장 중요한 기능 중 하나는 단일 로그인(SSO)을 지원한다는 점입니다. SSO를 통해 사용자는 한 번 로그인하면 각각의 애플리케이션에서 다시 로그인해야 할 필요 없이 여러 애플리케이션에 접근할 수 있습니다. 비슷하게, 단일 로그아웃 기능을 사용하면 사용자는 모든 애플리케이션에서 동시에 로그아웃할 수 있습니다.

## 소셜 로그인

Keycloak은 소셜 로그인 기능을 지원하며, 사용자들이 Google, Facebook, Twitter 등의 소셜 미디어 계정을 사용하여 로그인할 수 있습니다. 이 기능은 등록 및 로그인 절차를 간소화하여 사용자 경험을 향상시킵니다.

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

## 다중 인증 (MFA)

보안을 강화하기 위해 Keycloak은 다중 인증(MFA)을 지원합니다. 이는 사용자가 응용 프로그램에 액세스하려면 두 개 이상의 인증 요소를 제공해야 하도록하여 추가 보안 계층을 추가합니다.

## OpenID Connect (OIDC) 및 SAML

Keycloak은 OpenID Connect (OIDC) 및 SAML 2.0과 같은 현대 프로토콜을 구현하여 인증 및 승인을 처리하여 다양한 응용 프로그램과 호환성이 있고 다재다능합니다.

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

## 사용자화 가능한 테마

Keycloak에서 제공하는 로그인 페이지의 외관과 느낌은 귀하의 브랜딩 요구 사항에 따라 사용자화할 수 있습니다. Keycloak은 테마 사용자화를 허용하여 로그인, 등록 및 계정 관리 페이지의 외관을 변경할 수 있습니다.

## 관리 콘솔

Keycloak은 렘(Realms), 사용자, 역할 및 권한을 관리하기 위한 쉽게 사용할 수 있는 웹 기반 관리 콘솔이 제공됩니다. Keycloak에서 렘은 사용자, 자격 증명, 역할 및 그룹을 관리하는 공간입니다.

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

## 보안

Keycloak은 SSL/TLS, 비밀번호 정책, 브루트 포스 탐지 등을 포함한 견고한 보안 기능을 기본 제공합니다. 또한 사용자 자격 증명을 안전하게 저장할 수 있습니다.

## API 액세스 관리

Keycloak을 사용하면 토큰(JWT 토큰 또는 SAML 어설션)을 사용하여 애플리케이션 API를 안전하게 보호할 수 있습니다. 보호해야 하는 리소스 및 해당 리소스에 액세스 할 수 있는 역할 또는 클라이언트를 쉽게 정의할 수 있습니다.

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

## 확장성 및 고가용성

Keycloak은 확장 가능하게 설계되어 있으며 고가용성 구성으로 배포할 수 있어 사용자와 애플리케이션에 항상 인증 서비스를 제공할 수 있습니다.

# 도커로 설정하기

docker-compose.yml 파일

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

```yaml
version: "3.7"

services:
  postgres:
    image: postgres:16.2
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    networks:
      - keycloak_network

  keycloak:
    image: quay.io/keycloak/keycloak:23.0.6
    command: start
    environment:
      KC_HOSTNAME: localhost
      KC_HOSTNAME_PORT: 8080
      KC_HOSTNAME_STRICT_BACKCHANNEL: false
      KC_HTTP_ENABLED: true
      KC_HOSTNAME_STRICT_HTTPS: false
      KC_HEALTH_ENABLED: true
      KEYCLOAK_ADMIN: ${KEYCLOAK_ADMIN}
      KEYCLOAK_ADMIN_PASSWORD: ${KEYCLOAK_ADMIN_PASSWORD}
      KC_DB: postgres
      KC_DB_URL: jdbc:postgresql://postgres/${POSTGRES_DB}
      KC_DB_USERNAME: ${POSTGRES_USER}
      KC_DB_PASSWORD: ${POSTGRES_PASSWORD}
    ports:
      - 8080:8080
    restart: always
    depends_on:
      - postgres
    networks:
      - keycloak_network

volumes:
  postgres_data:
    driver: local

networks:
  keycloak_network:
    driver: bridge
```

.env file

```yaml
POSTGRES_DB=keycloak_db
POSTGRES_USER=keycloak_db_user
POSTGRES_PASSWORD=keycloak_db_user_password
KEYCLOAK_ADMIN=admin
KEYCLOAK_ADMIN_PASSWORD=password
```

# Services

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

PostgreSQL Service (postgres):

- **이미지**: PostgreSQL 서버를 실행하기 위해 Docker 이미지 postgres:16.2를 사용합니다.
- **볼륨**: 데이터베이스 데이터의 지속적인 저장을 위해 컨테이너 내부의 /var/lib/postgresql/data에 대한 postgres_data라는 볼륨을 매핑합니다.
- **환경 변수**: 환경 변수로 지정된 이름(POSTGRES_DB), 사용자(POSTGRES_USER), 비밀번호(POSTGRES_PASSWORD)로 데이터베이스를 구성합니다.
- **네트워크**: Keycloak 서비스와의 통신을 위해 keycloak_network라는 사용자 정의 네트워크에 연결합니다.

Keycloak Service (keycloak):

- **이미지**: Keycloak 서버를 실행하기 위해 quay.io/keycloak/keycloak:23.0.6을 활용합니다.
- **명령어**: Keycloak을 실행하기 위해 start를 지정합니다.
- **환경 변수**: 호스트명(KC_HOSTNAME), 포트(KC_HOSTNAME_PORT), HTTP 구성(KC_HTTP_ENABLED, KC_HOSTNAME_STRICT_HTTPS), 헬스 체크(KC_HEALTH_ENABLED), 관리자 자격 증명(KEYCLOAK_ADMIN, KEYCLOAK_ADMIN_PASSWORD), 데이터베이스 연결 세부 정보(KC_DB, KC_DB_URL, KC_DB_USERNAME, KC_DB_PASSWORD) 등 다양한 설정을 구성합니다.
- **포트**: 호스트의 포트 8080을 웹 접근을 위해 Keycloak 컨테이너의 포트 8080에 매핑하여 노출합니다.
- **재시작 정책**: 수동으로 중지할 때까지 항상 다시 시작되도록 구성합니다.
- **의존성**: Postgres 서비스에 종속성을 선언하여 먼저 시작되도록 합니다.
- **네트워크**: keycloak_network에도 연결됩니다.

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

# 볼륨

- postgres_data: 기본 로컬 스토리지 드라이버를 사용하여 컨테이너를 다시 시작할 때도 지속적으로 PostgreSQL 데이터를 저장하는 명명된 볼륨입니다.

# 네트워크

- keycloak_network: 브릿지 드라이버를 사용하는 사용자 정의 네트워크로, Keycloak과 PostgreSQL 컨테이너 간의 통신을 원활하게 합니다.

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

# 환경 변수

데이터베이스 구성 (POSTGRES_DB, POSTGRES_USER, POSTGRES_PASSWORD) 및 Keycloak 관리자 자격 증명 (KEYCLOAK_ADMIN, KEYCLOAK_ADMIN_PASSWORD)의 값을 지정하는 변수입니다. 서비스가 안전하게 통신하고 작동하는 데 필수적입니다.

# 요약

이 Docker Compose 파일은 인증 시스템을 제공하는 Keycloak와 PostgreSQL을 설정합니다. 데이터 저장을 위한 인증 시스템을 제공합니다. 액세스를 위한 환경 변수 및 서비스 구성을 지정하며 서비스 간 통신을 위한 사용자 지정 네트워크가 포함되어 있습니다. 개발에 이상적이며 응용 프로그램 인증 관리를 위한 기반을 제공합니다.

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

# 소스 코드

전체 코드 및 더 많은 예제는 GitHub 저장소를 확인하세요: ForgeContainer
