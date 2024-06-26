---
title: "라라벨 세일과 도커 컴포즈 파일을 사용자 정의하는 방법"
description: ""
coverImage: "/assets/img/2024-06-19-HowtoCustomizeLaravelSailwithDockerComposeFiles_0.png"
date: 2024-06-19 12:42
ogImage:
  url: /assets/img/2024-06-19-HowtoCustomizeLaravelSailwithDockerComposeFiles_0.png
tag: Tech
originalTitle: "How to Customize Laravel Sail with Docker Compose Files?"
link: "https://medium.com/codex/customize-laravel-sail-with-docker-compose-files-52d51465a60d"
---

![](/assets/img/2024-06-19-HowtoCustomizeLaravelSailwithDockerComposeFiles_0.png)

라라벨 Sail은 라라벨 개발에 혁명을 일으키는 도구입니다. 필요한 모든 서비스가 이미 설치된 미리 구성된 도커 환경을 제공합니다. 이를 통해 수동 설정이 필요없어지고, 팀 전체에 일관된 개발 환경을 제공합니다. 하지만 프로젝트에 특정 요구 사항이 있다면 어떨까요? 라라벨 Sail은 도커 컴포즈 파일을 사용하여 쉽게 사용자 정의할 수 있는 기능을 제공합니다.

도커 컴포즈는 Sail의 핵심입니다. 여러 도커 컨테이너의 구성 및 조정을 관리합니다. docker-compose.yml 파일이 도커 컴포즈의 핵심이며, 애플리케이션에 필요한 서비스(컨테이너)를 정의합니다. 이 파일을 조정함으로써 Sail 환경을 프로젝트 요구 사항에 맞게 완벽히 맞출 수 있습니다.

이 문서에서는 Docker Compose를 사용하여 라라벨 Sail을 사용자 정의하는 방법에 대해 알아보겠습니다. 서비스, 볼륨, 포트, 환경 변수를 조정하여 개발 경험을 효율적으로 만들고 프로젝트 요구 사항에 완벽하게 부합하도록 만들어 보겠습니다.

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

# 도커-컴포즈.yml 파일 이해하기

도커-컴포즈.yml 파일은 Laravel Sail 환경의 청사진으로 작용합니다. 이 파일은 도커 컴포즈에게 어떻게 설정하고 관리해야 하는지 지시하여 애플리케이션이 원활하게 실행되도록 하는 다양한 서비스(컨테이너)를 제어합니다. 이 파일을 텍스트 편집기에서 열면 일반적으로 다음과 같은 여러 주요 섹션을 포함하는 구조화된 형식을 볼 수 있습니다:

# 1. 버전

이 섹션은 도커 컴포즈 파일 형식 버전을 지정합니다. 최신 버전이 더 많은 기능을 제공하지만, Sail은 보다 널리 접근 가능한 지원되는 버전을 사용합니다.

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

# 2. 서비스

이것은 구성의 핵심입니다. 이것은 응용 프로그램에 필요한 각 서비스(컨테이너)를 정의합니다. 일반적인 Sail 서비스는 다음과 같습니다:

- laravel.test: Laravel 응용 프로그램을 실행하는 컨테이너입니다.
- mysql: 데이터베이스 컨테이너로, 일반적으로 MySQL을 사용합니다.
- phpmyadmin: 데이터베이스를 관리하기 위한 그래픽 사용자 인터페이스(GUI)를 제공하는 컨테이너입니다.

각 서비스 정의에는 기본 이미지(예: php:8.1), 노출할 포트(예: 웹 트래픽용으로 80:80), 마운트할 볼륨(프로젝트 코드 및 데이터) 및 구성에 대한 환경 변수와 같은 세부 정보가 포함됩니다.

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

# 3. 볼륨

이 섹션은 컨테이너 재시작 시에도 데이터를 유지하는 이름이 지정된 볼륨을 정의합니다. 예를 들어, 애플리케이션 코드와 데이터베이스 파일을 볼륨으로 마운트하여, 컨테이너가 중지되고 다시 생성되어도 그 데이터가 보존되도록 할 수 있습니다.

# 4. 네트워크

이 섹션은 컨테이너 간 통신을 위한 사용자 정의 네트워크를 정의합니다. 기본적으로 Sail은 환경 내에서 서비스 간 통신을 위해 브리지 네트워크를 사용합니다.

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

# 5. 환경

이 섹션에서는 애플리케이션 컨테이너 내에서 액세스할 수 있는 환경 변수를 정의할 수 있습니다. 이러한 변수는 데이터베이스 자격 증명이나 API 키와 같은 애플리케이션의 다양한 측면을 구성하는 데 사용할 수 있습니다.

도커 컴포즈(.yml) 파일의 각 섹션의 목적과 구조를 이해하는 것은 사용자 정의에 들어가기 전에 중요합니다. 이러한 섹션들을 숙지하여 스스로를 잘 준비시켜 변경하고 Sail 환경을 프로젝트의 특정 요구 사항에 맞게 맞춤화할 수 있을 것입니다.

# 서비스 사용자 정의하기

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

우리가 본 것처럼, docker-compose.yml 파일의 서비스 섹션은 Sail 환경에서 실행되는 각 컨테이너를 정의합니다. 이것이 맞춤화의 마법이 일어나는 곳입니다! 프로젝트에 맞춰 서비스를 개인화하는 몇 가지 방법을 살펴봅시다:

## 1. 베이스 이미지:

베이스 이미지는 컨테이너 내의 운영 체제 및 미리 설치된 소프트웨어를 지정합니다. 기본적으로 Sail은 laravel.test 서비스에 특정 PHP 버전 이미지(예: php:8.1)를 사용합니다. 그러나 프로젝트가 새로운 기능을 위해 다른 PHP 버전을 필요로 하는 경우(예: php:8.2), docker-compose.yml 파일에서 단순히 베이스 이미지 정의를 수정할 수 있습니다. 다음은 예시입니다:

## 2. 포트:

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

기본적으로 Sail은 특정 컨테이너 포트를 호스트 머신 포트로 매핑합니다. 이를 통해 편리한 URL(일반적으로 http://localhost:80)을 통해 응용 프로그램에 액세스할 수 있습니다. 그런데 여러 프로젝트를 동시에 작업하고 있을 때는 어떻게 해야 할까요? 각 프로젝트의 laravel.test 서비스에 대해 포트를 사용자 정의하여 충돌을 피할 수 있습니다. 다음은 방법입니다:

이 구성은 컨테이너의 포트 80(웹 트래픽)를 호스트 머신의 포트 8080으로 노출합니다. 이제 http://localhost:8080에서 응용 프로그램에 액세스할 수 있습니다.

# 3. 볼륨:

볼륨은 호스트 머신과 컨테이너 간에 공유되는 디렉터리입니다. 이를 통해 컨테이너가 다시 생성되어도 응용 프로그램 코드 및 데이터베이스 파일과 같은 데이터를 지속적으로 유지할 수 있습니다. Sail은 일부 볼륨을 사전 구성하지만 특정 요구 사항에 따라 추가 볼륨을 추가할 수 있습니다.

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

예를 들어, 프로젝트 디렉토리 외부에 사용자 지정 구성 파일이 있다고 상상해보세요. laravel.test 서비스에서 이 파일을 볼륨으로 마운트할 수 있습니다.

이는 호스트 머신의 my-custom-config.php 파일을 컨테이너 내의 /var/www/html 디렉토리로 마운트하여 응용 프로그램에서 이에 액세스할 수 있도록 하는 것입니다.

# 4. 환경 변수:

환경 변수는 코드 자체를 수정하지 않고 응용 프로그램을 구성하는 방법을 제공합니다. Sail은 일부 기본 환경 변수를 정의하지만, 프로젝트에 특정한 사용자 정의 환경 변수를 추가할 수 있습니다. 다음은 예시입니다:

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

이 환경 변수는 env() 도우미 함수를 사용하여 Laravel 애플리케이션 내에서 액세스할 수 있습니다.

# Sail 고급 사용자 정의

이전 섹션에서는 핵심 서비스 사용자 정의에 초점을 맞췄지만, Laravel Sail은 숙련된 개발자들을 위해 더 많은 유연성을 제공합니다. 여기에는 고급 옵션 중 일부를 살펴볼 수 있습니다:

# 1. 네트워크:

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

기본적으로 Sail은 서비스간 통신을 위해 브릿지 네트워크를 사용합니다. 그러나 더 세밀한 제어가 필요한 복잡한 애플리케이션의 경우, docker-compose.yml 파일에서 사용자 정의 네트워크를 정의할 수 있습니다. 이러한 네트워크를 사용하면 서비스를 격리하고 통신 패턴을 제어할 수 있습니다.

중요한 참고 사항: 사용자 정의 네트워크 구성은 Docker 네트워킹 개념에 대한 심층적인 이해가 필요하며, 고급 Docker Compose 기술에 익숙한 개발자를 대상으로 권장됩니다.

# 2. 커스텀 서비스 추가하기:

Sail은 MySQL과 같은 미리 구성된 서비스를 제공하지만, 캐싱을 위해 Memcached나 이메일 테스트를 위해 Mailhog와 같은 추가 서비스가 필요한 경우는 어떨까요? Docker Compose의 장점은 다양한 서비스를 통합할 수 있는 능력에 있습니다. docker-compose.yml 파일에서 원하는 이미지와 구성 옵션을 지정하여 사용자 정의 서비스를 정의할 수 있습니다.

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

예를 들어 Memcached 서비스를 추가하려면:

기억하세요: 사용자 정의 서비스를 추가하려면 그들의 목적과 구성 옵션을 이해해야 합니다. 선택한 서비스 이미지의 공식 문서를 참조하여 구체적인 지침을 얻을 수 있습니다.

# 3. 도커 Secrets 사용하기:

보안이 중요합니다. 데이터베이스 자격 증명과 같은 민감한 정보를 docker-compose.yml 파일에 직접 저장하는 것은 권장되지 않습니다. Docker Compose는 "비밀"이라고 불리는 안전한 솔루션을 제공합니다. 이 비밀은 docker-compose.yml 파일 외부에서 정의하고 서비스 내에서 환경 변수를 사용하여 참조할 수 있습니다.

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

중요 사항: Docker 시크릿을 사용하려면 추가 구성이 필요하며, 민감한 정보를 위한 별도의 파일을 관리해야 할 수도 있습니다. 이 옵션을 구현하기 전에 Docker 보안 모베스트 프랙티스를 충분히 이해해야 합니다.

이러한 고급 사용자 정의 옵션은 Laravel Sail 환경에 대한 새로운 수준의 유연성을 제공합니다. 그러나 이를 주의 깊게 다루고 Docker Compose 개념에 대해 꽤 꽤 잘 알고 있어야 합니다. 초심자들에게는 고급 구성으로 들어가기 전에 핵심 서비스 사용자 정의를 숙달해야 합니다.

# 결론

Laravel Sail은 사전 구성된 Docker 환경을 활용하여 Laravel 개발 워크플로우를 간소화할 수 있습니다. 하지만 진정한 매력은 사용자 정의할 수 있다는 점에 있습니다. Docker Compose 파일을 활용하여 Sail 설정을 프로젝트 요구 사항에 완벽히 맞게 조정할 수 있습니다.

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

이 기사에서는 서비스 베이스 이미지 및 포트를 조정하여 볼륨을 마운트하고 사용자 정의 환경 변수를 정의하는 등 다양한 사용자 정의 옵션을 살펴보았습니다. 우리는 심화된 영역으로 들어가 커스텀 네트워크를 추가하고 서비스를 추가하며 Docker 시크릿을 활용하여 보안을 강화했습니다.

기억하세요, 사용자 정의는 강력한 도구이지만 효과적으로 사용하려면 docker-compose.yml 파일과 Docker Compose 개념을 이해해야 합니다. 작은 규모로 시작하여 기본 사용자 정의를 실험하고 확신이 생기면 점진적으로 고급 옵션을 탐험해보세요.

라라벨 Sail의 전체 잠재력을 발휘하고 싶나요? 깊이 있는 내용은 공식 Sail 문서를 참조하고 온라인 Docker Compose 자료의 방대한 세계를 탐험해보세요. 아래 댓글 섹션에서 여러분의 경험을 공유하고 질문해도 좋습니다. 즐거운 사용자 정의 되세요!
