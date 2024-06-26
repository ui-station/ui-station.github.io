---
title: "Java 21과 Spring Boot 3x를 사용한 gdocweb 개발 및 그 이상"
description: ""
coverImage: "/assets/img/2024-06-23-BuildinggdocwebwithJava21SpringBoot3xandBeyond_0.png"
date: 2024-06-23 20:29
ogImage:
  url: /assets/img/2024-06-23-BuildinggdocwebwithJava21SpringBoot3xandBeyond_0.png
tag: Tech
originalTitle: "Building gdocweb with Java 21, Spring Boot 3.x and Beyond"
link: "https://medium.com/javarevisited/building-gdocweb-with-java-21-spring-boot-3-x-and-beyond-9167f1ad4daa"
---

![BuildinggdocwebwithJava21SpringBoot3xandBeyond](/assets/img/2024-06-23-BuildinggdocwebwithJava21SpringBoot3xandBeyond_0.png)

새 프로젝트를 시작하는 것은 항상 흥분과 어려운 결정의 조합입니다, 특히 Google Docs와 GitHub Pages 같은 익숙한 도구들을 함께 사용할 때는 더욱 그렇습니다. 이것은 많은 사람들에게 삶을 더 쉽게 만들어줄 것으로 기대한 도구 gdocweb을 구축한 이야기입니다. Java 21과 Spring Boot 3.x를 선택한 이유, 일부 시행착오를 거친 뒤 GraalVM을 포기한 이유, 그리고 좀 더 복잡한 옵션보다 간단한 VPS와 Docker Compose가 이겼다는 이야기를 들려드리겠습니다. 저는 또한 Postgres와 JPA를 선택했지만 Flyway와 같은 마이그레이션 도구는 피했습니다. 이것은 유용하고 효율적인 것을 만들려는 엔지니어의 선택, 변화 및 가끔의 '아하' 순간들을 솔직하게 기록한 이야기입니다.

# gdocweb 소개

gdocweb을 구축하는 기술적 세부 사항과 의사 결정의 미로에 빠지기 전에, gdocweb이 무엇이며 어떤 문제를 해결하는지를 이해하는 것부터 시작해볼까요? 간단히 말해, gdocweb은 Google Docs를 GitHub Pages에 연결하는 것입니다. GitHub의 모든 강력함과 Google Docs의 모든 사용 편의성을 갖춘 무료 사이트를 생성하는 간단한 웹 빌더입니다.

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

웹사이트 구축 및 문서 작성에 일반적으로 동반되는 복잡성을 제거하기 위해 gdocweb를 개발하기로 결정했어요. 이는 콘텐츠를 발행하고 유지 관리하는 데 번거로움 없이 사용하고자 하는 사용자들을 위한 것이지만, GitHub의 강력함에는 빠져들지만 마크다운의 세세한 부분까지 다루고 싶지 않은 숙달된 사용자들을 위한 것입니다.

일반 대중을 위해 gdocweb를 설명하는 짧은 비디오가 있어요:

# Java 21 및 Spring Boot 3.x: 혁신과 성숙함

gdocweb와 같이 프로젝트를 혼자 이끌 때, 팀이나 기업 환경에서는 더 어려울 수 있는 기술 선택을 마음대로 할 수 있어요. 이 자유로움으로 인해 이 프로젝트에서 Java 21 및 Spring Boot 3.x를 선택했어요. 현재 장기 지원(LTS) 버전인 Java를 선택한 결정은 올바른 선택이었어요. 최신 기술을 사용하는 것이 항상 유혹적이지만, Java 21을 선택한 이유는 새로운 것을 사용하는 데 그치지 않고, 시대를 견뎌낼 수 있고 현대적인 개발 요구를 충족시키기 위해 진화한 플랫폼을 활용하고자 했기 때문이에요. 가상 쓰레드는 Java 21 선택의 주요 부분이었어요. 이러한 프로젝트에서 비용은 매우 중요한 요소이며, 서버로부터 최대 처리량을 뽑아내는 것은 이러한 상황에서 중요합니다.

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

자바는 이미 성숙한 기술로, 최신 버전에서도 신뢰감을 제공합니다. 비슷하게, 최신 버전인 스프링 부트 3.x도 견고하고 테스트된 프레임워크 계보에서 나온 제품입니다. 오랜 명성을 자랑하는 보수적인 선택이지만 기능과 성능 측면에서 혁신적입니다.

하지만 이 결정도 순탄치 않았어요. Google API 접근을 통합하는 과정에서 보안 CASA 티어 2 검토를 거쳐야 했죠. 이때 자바 21을 선택한 것이 문제였습니다. 해당 검토 도구는 JDK 11을 위해 제작됐기 때문에 JDK 21에서도 작동은 했지만 프로세스에 약간의 스트레스를 준 케이스였습니다. 최신 기술 버전을 다룰 때 예상치 못한 어려움이 있을 수 있다는 것을 다시 상기시켜 주었죠. 심지어 자바와 같이 성숙한 기술이더라도요.

스프링 부트 3.x로 전환하는 과정도 다양한 도전이 있었는데, 특히 보안 구성 변경 때문에 어려움을 겪었습니다. 이러한 수정으로 인해 대부분의 온라인 샘플과 안내서가 사용할 수 없게 되어 초기 설정을 많이 망가뜨렸죠. 이러한 변화에 적응하고 새로운 방법을 찾는 학습 곡선이었지만, 대부분의 다른 측면은 비교적 간단했고, 스프링 부트 3.x에 대해 할 수 있는 가장 좋은 칭찬은 스프링 부트 2.x와 매우 유사하다는 점입니다.

# 효율을 위한 GraalVM 네이티브 이미지

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

GraalVM 네이티브 이미지에 대한 저의 관심은 기본적으로 메모리 사용량을 줄이고 시작 시간을 빠르게 하는 약속 때문이었습니다. 낮은 메모리 요구 사항으로 더 많은 서버 인스턴스를 실행하여 더 나은 확장성과 내구성을 가질 수 있다는 아이디어를 갖고 있었고, 시작 시간이 빨라지면 장애로부터 더 빠른 회복이 가능해져 신뢰할 수 있는 서비스를 유지하는 데 중요했습니다.

# GraalVM 구현

GraalVM을 동작시키는 것은 쉽지 않았지만 너무 어렵지는 않았습니다. 몇 차례의 시행착오 끝에 GraalVM 프로젝트를 빌드하고 Docker에 업로드하는 지속적 통합(CI) 프로세스를 설정할 수 있었습니다. 제가 M1 Mac을 사용하고 있지만 서버는 Intel 아키텍쳐에서 실행 중이었기 때문에 이러한 설정이 필요했습니다. 이러한 설정으로 업데이트마다 18분의 대기 시간을 처리해야 했는데, 개발 주기에 상당한 지연이었습니다.

# 프로덕션에서 직면한 문제들

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

프로젝트의 프로덕션 및 스테이징 환경을 테스트하기 시작하면서 상황이 복잡해졌어요. 네이티브 이미지에서 빠진 라이브러리 코드로 '두더지 잡기' 상황이 벌어졌죠. 해결한 문제마다 새로운 문제가 발생하고, 각 업데이트마다 18분씩 걸리는 과정이 답답함을 더했어요.

문제의 결정적 순간은 Google API 라이브러리와의 호환성 문제를 깨달았을 때였어요. 이 문제를 해결하기 위해서는 GraalVM 빌드에서의 철저한 테스트가 필요했는데, 이미 느린 빌드 시간으로 부담을 느끼고 있었죠. 제 작은 프로젝트에 대해선 수고에 비해 이득이 미미한 병목 현상이었어요.

# 진행 방향 결정

GraalVM은 자원을 아낄 수 있는 이상적인 선택처럼 보였지만, 현실은 다르았어요. 제한된 GitHub Actions 분을 사용하고 철저한 테스트를 요구하여, 이 프로젝트 규모에선 실용적이지 않았죠. 결국, GraalVM 경로를 포기하기로 결정했어요.

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

만약 GraalVM을 사용하기로 결정하신다면, 저는 다음 GitHub Actions 스크립트를 사용했었는데요. 여러분의 여정에 도움이 됐으면 좋겠어요:

```js
name: Java CI with Maven
on:
  push:
    branches: [ "master" ]
  pull_request:
    branches: [ "master" ]
jobs:
  build:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:latest
        env:
          POSTGRES_PASSWORD: yourpassword
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    steps:
    - uses: actions/checkout@v3
    - uses: graalvm/setup-graalvm@v1
      with:
        java-version: '21'
        version: '22.3.2'
        distribution: 'graalvm'
        cache: 'maven'
        components: 'native-image'
        native-image-job-reports: 'true'
        github-token: ${ secrets.GITHUB_TOKEN }
    - name: Wait for PostgreSQL
      run: sleep 10
    - name: Build with Maven
      run: mvn -Pnative native:compile
    - name: Build Docker Image
      run: docker build -t autosite:latest .
    - name: Log in to Docker Hub
      uses: docker/login-action@v1
      with:
        username: ${ secrets.DOCKERHUB_USERNAME }
        password: ${ secrets.DOCKERHUB_TOKEN }
    - name: Push Docker Image
      run: |
        docker tag autosite:latest mydockeruser/autosite:latest
        docker push mydockeruser/autosite:latest
```

이 구성은 GraalVM의 혜택을 활용하기 위한 제 시도의 중요한 부분이었지만, 프로젝트가 진화함에 따라 기술 선택에서 이상주의와 배포 및 유지보수의 실용성 사이의 상충을 이해하는 데 도움이 되었습니다.

# 배포: VPS 및 Docker Compose

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

gdocweb을 배포할 때 고려해야 할 여러 가지 방법이 있었어요. 각 옵션에는 각각의 장단점이 있었지만, 신중한 평가 끝에 Docker Compose를 사용하는 Virtual Private Server (VPS)를 사용하기로 결정했어요. 제 생각 프로세스를 자세히 살펴보고, 이 선택이 제 요구 사항에 가장 적합한 이유를 알아봐요.

# 원시 VPS 배포 피하기

VPS에 애플리케이션을 직접 설치하는 간단한 방법을 제외했어요. 이 방법은 이주 편의성, 테스트 및 유연성 면에서 부족했어요. 컨테이너는 더 간편하고 효율적인 접근 방식을 제공해요. 서로 다른 환경 사이에서 추상화 수준과 일관성을 제공하여 매우 가치 있는 것이에요.

# 관리형 컨테이너 및 오케스트레이션 회피하기

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

관리형 컨테이너와 오케스트레이션 (예: k8s)은 다른 옵션이었고, 확장성과 관리 용이성을 제공하지만 다른 영역에서 복잡성을 도입합니다. 예를 들어, 관리형 Kubernetes 서비스를 사용할 때 데이터베이스에 대해 클라우드 스토리지에 의존해야 한다는 것이 종종 곧 비용이 빠르게 증가할 수 있다는 것을 의미합니다. 나의 철학은 프로젝트 초기 단계에서 횡적 규모보다는 비용에 초점을 맞추는 것이었습니다.

작은 규모에서 최적화하고 안정화하지 않으면 성장함에 따라 문제가 점점 더 심각해질 것입니다. 확장은 이상적으로는 수직 확장부터 시작해야 하며, 이후에 수평 확장으로 이동해야 합니다. 수직 확장은 더 많은 CPU/RAM을 의미하고, 수평 확장은 추가적인 머신을 추가합니다. 수직 확장은 비용 효율적일 뿐만 아니라 기술적 측면에서도 중요합니다. 간단한 프로파일링 도구를 사용하여 성능 병목 현상을 식별하기가 더 쉬워집니다.

반면에, 수평 확장은 더 많은 인스턴스를 추가하여 이러한 문제를 숨기는 경우가 많습니다. 이는 높은 비용과 숨겨진 성능 문제로 이어질 수 있습니다.

# Docker Compose의 선택

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

도커 컴포즈는 몇 가지 이유로 명백한 우승자로 떴어요. 데이터베이스와 응용 프로그램 컨테이너를 원활하게 통합할 수 있게 해주었습니다. 그들의 통신은 폐쇄된 네트워크 안에서 이루어지므로 외부에서 접근할 수 있는 포트가 없어 안전성이 높아졌습니다. 또한 비용은 고정되어 예측 가능하며 사용량에 따른 뜻밖의 추가 비용이 없다는 장점이 있어요.

이러한 설정은 더 확장된 컨테이너 오케스트레이션 시스템의 부담과 복잡성 없이 유연성과 컨테이너화의 쉬움을 제공했어요. 배포 프로세스를 복잡하게 만들지 않으면서도 필요한 기능을 충분히 제공하는 완벽한 중간 지점이었습니다.

도커 컴포즈를 사용함으로써 환경을 효율적으로 제어하고 배포 프로세스를 간단하고 관리하기 쉽게 유지했어요. 이 결정은 gdocweb의 일반적인 이념 — 간결함, 효율성, 실용성과 완벽하게 부합했어요.

# 프론트엔드: 현대적인 대안들보다 Thymeleaf를 선호합니다.

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

gdocweb의 프론트엔드 개발은 나에게 조금 도전이었습니다. React와 유사한 프레임워크가 주를 이루는 시대에 Thymeleaf를 선택하는 것은 거꾸로 보일 수 있지만, 이 결정은 실용적인 고려와 프로젝트 요구 사항 및 개발자로서의 내 강점에 대한 명확한 이해를 기반으로 합니다.

# React: 현대적이지만 일치하지 않는 해결책

React는 틀림없이 현대적이고 강력하지만, 그것만의 복잡성이 딸려옵니다. React에 대한 내 경험은 많은 개발자들이 편안한 영역을 벗어나 시도하는 것과 유사합니다 - 기능적이지만 정확히 능숙하지는 않습니다. 내 코드를 보는 시니어 React 개발자들이 느끼는 당황한 표정을 보았습니다. 다른 사람이 작성한 복잡한 Java 코드를 읽을 때 내가 느끼는 것과 비슷합니다.

React의 학습 곡선과 특정 시나리오에서의 성능 저하, 깊은 전문 지식 없이는 세련된 결과물을 얻을 수 없는 위험 등을 고려하여 gdocweb에 적합성을 재고하게 되었습니다.

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

# Thymeleaf의 매력

Thymeleaf는 다른 방면에서 더 직관적인 접근 방식을 제공하며, 이 프로젝트의 간결함과 효율성에 잘 맞습니다. React와 같은 프레임워크와 비교할 때 낡은 것으로 여겨질 수 있는 HTML 기반의 인터페이스가 상당한 장점을 가지고 있습니다:

- 페이지 흐름의 간단함: Thymeleaf는 이해하기 쉽고 디버깅하기 쉬운 흐름을 제공하여 이 프로젝트에 실용적인 선택이 됩니다.
- 성능과 속도: 빠른 성능으로 유명하며, 좋은 사용자 경험을 제공하는 중요한 요소입니다.
- NPM이 필요 없음: Thymeleaf는 추가적인 패키지 관리가 필요 없으므로 복잡성과 잠재적인 취약점을 줄입니다.
- 클라이언트 측 취약점의 위험 감소: Thymeleaf의 서버 측 성격은 클라이언트 측 문제의 위험을 본질적으로 줄입니다.

# 동적 기능에 대한 HTMX 고려

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

HTMX를 사용하여 프론트엔드의 동적 동작을 구현하는 아이디어가 머릿속을 스쳤어요. HTMX는 동적 기능을 쉽게 추가할 수 있다고 해서 얼마 전부터 주목하고 있었어요. 그러나 기본적으로 간단한 위자드인 gdocweb과 같은 도구에 HTMX가 정말 필요한지 스스로에게 물어보았어요. 제 결론은 HTMX를 선택하는 것이 기술적인 필요성보다는 내가 이력서에 쓰기 좋을 것 같아하는 Resume Driven Design (RDD)일지도 모른다는 것이었어요.

요약하면, Thymeleaf 선택은 실용성, 익숙함, 효율성을 결합한 것이었어요. 더 현대적이지만 복잡성과 과도한 부담이 있는 다른 프레임워크를 사용할 필요는 없었지만 빠르고 간편하며 효과적인 프론트엔드를 구축할 수 있게 했어요.

# 마지막으로

이 글에서 중요한 점은 기술 선택에서 실용성의 중요성입니다. 우리가 직접 프로젝트를 구축할 때는 더 최신 기술을 실험하기 쉽지만, 이는 가파른 경사입니다. 실험을 하면서도 익숙한 지식을 바탕으로 균형을 유지해야 해요.

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

GraalVM을 사용하면서 기술 선택을 프로젝트 요구 사항과 조화롭게 맞추고 도전에 적응하는 유연성이 얼마나 중요한지를 깨달았어요. 기술에서 때로는 더 간단하고 시험된 길이 가장 효과적일 수 있다는 것을 상기시켜 주는 것 같아요.
