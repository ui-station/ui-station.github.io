---
title: "Docker, Uptime Kuma, 그리고 Traefik을 사용하여 웹사이트 모니터링 하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-UseDockerUptimeKumaandTraefikToMonitorYourWebsite_0.png"
date: 2024-06-23 00:46
ogImage:
  url: /assets/img/2024-06-23-UseDockerUptimeKumaandTraefikToMonitorYourWebsite_0.png
tag: Tech
originalTitle: "Use Docker, Uptime Kuma, and Traefik To Monitor Your Website"
link: "https://medium.com/gitconnected/use-docker-uptime-kuma-and-traefik-to-monitor-your-website-593373f9e0c2"
---

<img src="/assets/img/2024-06-23-UseDockerUptimeKumaandTraefikToMonitorYourWebsite_0.png" />

# 소개

이 문서에서는 Docker/Docker Swarm을 사용하여 로컬 PC 또는 서버에서 웹사이트 모니터링을 설정하는 방법을 보여드리려고 합니다. prometheus, node-exporter, 또는 graphana와 같이 복잡한 모니터링 스택 대신에 NodeJs와 Vue로 작성된 가벼운 대안인 Uptime Kuma를 소개할 예정입니다.

이 프로젝트는 오픈 소스로 GitHub에서 찾을 수 있습니다: https://github.com/louislam/uptime-kuma

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

이 대안을 사용하게 된 중요한 속성들은 다음과 같아요:

- UI가 아주 멋져요!
- Docker/Docker Swarm을 사용한 아주 쉬운 설정
- 매우 간편한 구성
- Discord, Slack, 이메일 (SMTP) 등을 통한 알림. 전체 목록을 보려면 여기를 클릭하세요.

# 준비 사항

Uptime Kuma를 서버 또는 로컬 머신에서 실행하려면 환경을 준비해야 해요. 저는 Traefik을 Docker Swarm에서 실행되는 리버스 프록시로 실행하여 Docker Compose로 배포하는 것을 좋아해요. Traefik을 사용하면 단일 Compose 파일을 만들어서 서비스를 배포하고 도메인에 대한 Let's Encrypt 인증서를 자동으로 발급할 수 있어요.

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

이 두 가지 전제 조건을 간단히 설명한 후, Uptime Kuma를 배포하는 방법을 보여드릴 거에요.

## Docker

Docker는 다양한 종류의 애플리케이션을 개발하고 배포하며 실행하기 위해 널리 사용되는 플랫폼이에요. 이를 통해 인프라를 애플리케이션에서 분리하여 한 기계에서 다른 기계로 소프트웨어를 빠르게 전달할 수 있게 해줘요.

Docker를 사용하여 소프트웨어를 구현하면 흔한 "내 컴퓨터에서는 작동하는데" 같은 인프라 문제를 효과적으로 해결할 수 있어요.

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

'img' 태그를 Markdown 형식으로 변경해주세요.

![](/assets/img/2024-06-23-UseDockerUptimeKumaandTraefikToMonitorYourWebsite_1.png)

시스템이나 서버에 Docker를 설치하려면 docker.com의 공식 튜토리얼을 따라 진행하세요.

로컬 머신에 Uptime Kuma만 호스팅하고 싶고 Windows를 사용하며 Docker Desktop을 설치할 수 없다면 이 가이드를 따라 진행할 수 있습니다:

## Traefik

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

Traefik이란 무엇인가요?

Traefik은 도커 환경에서 배포된 서비스로 들어오는 요청을 전달하는 데 사용됩니다. 또한 Traefik이 관리하는 모든 도메인에 대해 자동으로 Let’s Encrypt SSL 인증서를 생성할 수 있는 기능이 있습니다.

도커 환경에서 로컬 Traefik 서비스를 설정하려면 이 도커 Compose 파일을 다운로드하고 다음 명령어를 실행하면 됩니다:

1. Traefik에서 사용되는 외부 네트워크를 생성하세요.

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

2. 필요한 변수 내보내기

3. 컨테이너 시작하기

트라픽 대시보드에 접속하려면 https://dashboard.yourdomain.de 로 이동하고 다음으로 로그인하세요:

```js
username: devadmin;
password: devto;
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

도커 스웜 모드 환경에서 Traefik을 배포하는 방법은 다음과 같습니다. 이 도커 Compose 파일을 다운로드하고 1단계와 2단계를 수행한 후 다음과 같이 배포하면 됩니다:

Traefik에 대한 깊은 이해를 얻으려면 다음 기사에서 도커 내에서 배포하는 방법과 도커 스웜 모드 내에서 배포하는 방법을 읽어보실 수 있습니다.

# Docker를 사용하여 Uptime Kuma 배포하기

Uptime Kuma를 배포하는 방법은 세 가지 환경에서 가능합니다: 로컬에서 도커로, 도커와 Traefik을 사용하여 서버에서, 그리고 도커를 실행하는 서버 클러스터에서 Traefik과 함께.

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

## 로컬 Docker 환경에서 배포하기

Uptime Kuma를 테스트하는 가장 쉬운 방법은 새로운 Docker Compose 파일을 만들어 다음 내용을 붙여넣어 자신의 장치에서 로컬로 실행하는 것입니다.

이 Docker Compose 파일은 공식 Uptime Kuma 이미지를 사용하여 포트를 1337로 매핑하고 Uptime Kuma의 데이터 폴더를 저장하기 위해 공유 폴더를 사용합니다.

폴더로 이동하여 Docker Compose 파일을 실행하려면 다음과 같이 실행하세요:

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

위 명령이 완료되면 https://localhost:1337/을 열어 Uptime Kuma 인스턴스에 액세스할 수 있습니다.

## Traefik을 사용하여 Docker/Docker Swarm으로 원격으로 배포

고유한 서버를 실행하고 Docker를 사용하여 단일 서버를 실행하거나 Docker의 스웜 모드로 서버 클러스터를 실행하고 있는 경우 Docker Compose 파일을 업데이트해야 합니다.

당신이 나의 튜토리얼에 설명된대로 Traefik 인스턴스를 설정했다고 가정하겠습니다.

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

새로운 Docker Compose 파일을 만들고 다음 내용을 붙여넣으세요:

이 Docker Compose 파일을 배포하기 전에 Traefik을 위한 것처럼 도메인을 내보내야 합니다:

다른 설정을 필요에 맞게 조정하세요(Domain, container_name, network 및 volumes) 그리고 Uptime Kuma를 배포하세요:

Traefik을 사용하는 Docker in Docker Swarm 모드의 경우,이 Compose 파일을 사용하여 데이터를 저장하고 싶은 노드에 monitor이름의 새로운 노드 레이블을 작성하고 PRIMARY_DOMAIN을 내보낸 후 아래 명령어로 배포하세요:

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

몇 초/분 후에 Uptime Kuma가 성공적으로 배포되어서 https://monitor.PRIMARY_DOMAIN/에서 인스턴스에 액세스할 수 있게 됩니다.

# Uptime Kuma 구성

Uptime Kuma가 성공적으로 배포된 후 처음으로 액세스하면 안전한 관리자 비밀번호를 구성해야 합니다.

작업을 완료하신 후에는 이 버튼을 눌러 첫 번째 모니터를 추가해보세요:

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

![2024-06-23-UseDockerUptimeKumaandTraefikToMonitorYourWebsite_2.png](/assets/img/2024-06-23-UseDockerUptimeKumaandTraefikToMonitorYourWebsite_2.png)

새로운 UI가 나타나며 특정 동작을 정의할 수 있습니다:

![2024-06-23-UseDockerUptimeKumaandTraefikToMonitorYourWebsite_3.png](/assets/img/2024-06-23-UseDockerUptimeKumaandTraefikToMonitorYourWebsite_3.png)

이제 첫 번째 모니터를 만들었으므로, Discord, 이메일 또는 기타 어떤 유형의 알림도 구성해야 합니다. 모니터가 예상대로 작동하지 않을 때마다 알림을 받게 됩니다. 이 과정은 보통 매우 쉽고 다음으로 설명될 것입니다.

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

## 디스코드 알림 설정하기 (그리고 슬랙, 팀즈)

디스코드 알림 (또는 팀즈, 슬랙)을 활성화하려면 Uptime Kuma가 경고를 보낼 서버와 채널에 대한 디스코드 (또는 팀즈, 슬랙) 웹훅이 필요합니다. 디스코드에서는 이를 '서버 설정 - 통합 - 웹훅 생성'을 통해 얻을 수 있습니다. 웹훅을 만든 후에 해당 웹훅 URL을 복사합니다. 팀즈나 슬랙에서도 거의 비슷하게 작동해야 합니다.

그런 다음, 모니터를 편집하고 "알림 설정"을 누릅니다. 나타나는 대화 상자에서 디스코드 (또는 팀즈, 슬랙)을 선택하고 친숙한 이름을 지어주고 웹훅 URL을 붙여넣습니다. 디스코드의 경우 UI는 다음과 같이 보이어야 합니다:

![디스코드 설정 화면](/assets/img/2024-06-23-UseDockerUptimeKumaandTraefikToMonitorYourWebsite_4.png)

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

**테이블 태그를 Markdown 형식으로 변경하면 됩니다.**

기존 모니터에도 적용하거나 새로운 모니터의 기본 설정으로 지정할 수 있습니다.

## 이메일 알림 설정하기

만약 디스코드, 팀즈, 또는 슬랙 알림을 원치 않는다면, 이메일 알림을 생성할 수도 있습니다. 설정하기 위해 이메일 계정의 호스트명, 포트, 사용자명, 비밀번호, 보내는/받는 이메일, 그리고 인증 방법 (StartTLS 또는 SSL)이 필요합니다. 또한 새로운 모니터의 기본 설정으로 지정하거나 기존 모니터에 추가할 수도 있습니다.

# 마지막으로

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

이 기사에서는 Uptime Kuma를 로컬 또는 Docker 또는 Docker Swarm 환경에 Traefik 역방향 프록시와 함께 설치하는 방법을 보여드렸어요. 이 가벼운 도구는 설정이 쉽고 다양한 구성 옵션을 제공하기 때문에 어떤 웹사이트든 감시하는 데 아주 좋아요. 소프트웨어 개발자나 블로그 호스터라면 Uptime Kuma를 추천드릴게요. 이를 통해 서비스나 블로그가 제대로 작동하는지 확인할 수 있어요.

이 튜토리얼은 여기까지에요. 이제 설정할 수 있을 거에요. 여전히 궁금한 점이 있다면 댓글 섹션에 질문해 주세요.

이 기사를 즐겁게 읽었다면 소중한 생각을 댓글로 남겨주십시오! 피드백을 듣고 싶어요.

누구에게든 이 기사를 공유하고 제 개인 블로그, LinkedIn, Twitter 및 GitHub에서 연락해 주세요.

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

이 글은 제 블로그에도 게재되었습니다: [https://www.paulsblog.dev/use-docker-uptime-kuma-and-traefik-to-monitor-your-website/](https://www.paulsblog.dev/use-docker-uptime-kuma-and-traefik-to-monitor-your-website/)
