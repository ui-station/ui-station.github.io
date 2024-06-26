---
title: "Docker 최신 개발 및 배포를 위한 필수 무기 "
description: ""
coverImage: "/assets/img/2024-06-23-DockerAGoodto-HaveArsenalforModernDevelopmentandDeployment_0.png"
date: 2024-06-23 22:03
ogImage:
  url: /assets/img/2024-06-23-DockerAGoodto-HaveArsenalforModernDevelopmentandDeployment_0.png
tag: Tech
originalTitle: "Docker: A Good to-Have Arsenal for Modern Development and Deployment"
link: "https://medium.com/@stenzr/docker-a-good-to-have-arsenal-for-modern-development-and-deployment-439a3167dd77"
---

![Docker](/assets/img/2024-06-23-DockerAGoodto-HaveArsenalforModernDevelopmentandDeployment_0.png)

소프트웨어 개발의 변화무쌍한 풍경에서 Docker는 빌드, 배포 및 애플리케이션 실행 프로세스를 최적화하는 필수 도구로 떠오르고 있습니다. 개발자, 시스템 관리자 또는 DevOps 팀의 일원이든 상관없이, Docker는 생산성과 효율성을 크게 향상시킬 수 있는 수많은 이점을 제공합니다.

# Docker란?

Docker는 가벼우며 휴대용 컨테이너 내에 애플리케이션을 자동으로 배포하는 오픈 소스 플랫폼입니다. 컨테이너에는 라이브러리, 시스템 도구, 코드 및 런타임과 같이 애플리케이션이 실행하는 데 필요한 모든 것이 포함되어 있습니다. 이를 통해 애플리케이션이 환경에 관계없이 일관되게 작동함이 보장됩니다.

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

# 도커를 사용하는 이유

## 1. 이식성

도커 컨테이너는 응용 프로그램과 해당 의존성을 캡슐화하여 다양한 환경에서 일관되게 실행되도록 보장합니다. 이는 전통적인 "내 컴퓨터에서는 작동하는데" 문제를 제거하고 배포 프로세스를 간단화합니다.

## 2. 격리

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

각 도커 컨테이너는 자체 자원과 환경 변수를 사용하여 독립적으로 실행됩니다. 이를 통해 응용 프로그램끼리 간섭하지 않고 더 안전하고 안정적인 환경을 유지할 수 있습니다.

## 3. 효율성

컨테이너는 호스트 OS 커널을 공유하기 때문에 가벼워요. 이는 별도의 운영 체제가 필요한 가상 머신과는 다릅니다. 결과적으로 시스템 리소스를 더 효율적으로 사용할 수 있으며 부하가 낮아집니다.

## 4. 확장성

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

도커는 여러 컨테이너 인스턴스를 생성하여 애플리케이션을 수평 확장하는 것을 쉽게 할 수 있습니다. 이는 로드 밸런싱 및 고가용성 설정에 특히 유용합니다.

## 5. 간소화된 개발 워크플로우

도커는 개발, 테스트 및 프로덕션 환경을 일관되게 제공하여 개발 워크플로우를 간소화합니다. 이는 개발 주기를 가속화시키고 환경별 문제를 해결하는 데 필요한 시간을 줄입니다.

# 도커 명령어 알아두면 유용합니다

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

## 1. 도커 버전

도커 이해하기 — 버전:

도커 --version 명령어는 도커 설치를 확인하는 데 중요한 간단한 도구입니다. 실행하면 시스템에 설치된 현재 도커 버전을 반환합니다. 이 명령어는 특히 트러블슈팅 및 도커 컨테이너 및 이미지와의 호환성을 보장하는 데 유용합니다. 특정 기능이나 버그 수정은 새로운 릴리스에서만 사용할 수 있습니다. 도커 버전을 추적함으로써 업그레이드에 대한 정보를 얻고 종속성을 효과적으로 관리할 수 있습니다.

## 2. 도커 이미지

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

Docker 이미지 관리하기:

`docker images` 명령어는 로컬 머신에 저장된 모든 Docker 이미지를 나열합니다. 각 이미지는 Docker 컨테이너를 생성하는 데 사용되는 템플릿으로, 모든 필요한 이진 파일, 라이브러리 및 종속성을 캡슐화합니다. 이 명령은 저장소 이름, 태그, 이미지 ID, 생성 날짜 및 크기와 같은 필수 정보를 표시합니다. 이미지를 효율적으로 관리하고, 더 이상 필요하지 않은 이미지를 식별하고 공간을 확보하는 데 도움이 됩니다. 예를 들어, 다양한 이미지에서 여러 컨테이너를 실행한 후, 낡은 이미지를 신속하게 식별하고 제거하여 깨끗하고 최적화된 Docker 환경을 유지할 수 있습니다.

## 3. docker run

`docker run`으로 컨테이너 실행하기:

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

도커 run 명령어는 도커 작업의 중심부로 지정된 이미지에서 새 컨테이너를 생성하고 시작하는 데 사용됩니다. 다양한 옵션을 사용하여 컨테이너의 런타임 동작을 조정할 수 있습니다. 이 옵션에는 대화식 터미널 세션 (-it), 백그라운드 실행 (-d), 포트 매핑 (-p) 등이 있습니다. 예를 들어 docker run -it ubuntu는 Ubuntu 컨테이너에서 대화식 터미널을 시작합니다. 이 명령어의 유연성을 통해 리소스 제한, 환경 변수, 볼륨 마운트 등을 구성하여 애플리케이션을 격리된 환경에서 신속하게 배포하고 확장할 수 있습니다.

## 4. docker ps

docker ps를 사용하여 컨테이너 모니터링하기:

docker ps 명령어는 현재 실행 중인 컨테이너의 스냅샷을 제공하며 컨테이너 ID, 이미지 이름, 실행된 명령, 생성 시간, 상태, 포트 및 이름과 같은 중요 정보를 표시합니다. 기본적으로 활성 컨테이너만 나열하지만 docker ps -a를 사용하면 중지된 컨테이너를 포함한 모든 컨테이너를 표시합니다. 이 명령어는 컨테이너 상태 모니터링, 문제 식별 및 컨테이너 수명주기 관리에 필수적입니다. 예를 들어, 예상대로 작동하지 않는 컨테이너를 신속하게 식별하고 문제를 해결하는 데 도움이 됩니다.

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

## 5. 도커 중지

도커 중지로 컨테이너를 안전하게 중지하자:

도커 중지 명령어는 실행 중인 컨테이너를 안전하게 중지하는 데 사용됩니다. 이 명령은 SIGTERM 신호를 보내어 컨테이너가 현재 작업을 완료하고 깨끗하게 종료되도록 합니다. 컨테이너가 지정된 시간 내에 중지되지 않으면 SIGKILL 신호를 보내어 강제로 중지시킵니다. 이 명령은 데이터 무결성을 유지하고 애플리케이션이 리소스를 해제하고 정리 작업을 수행한 다음 종료할 수 있도록 하는 데 중요합니다. 예를 들어 데이터베이스 컨테이너를 안전하게 중지하면 모든 보류 중인 트랜잭션이 올바르게 커밋됨을 보장할 수 있습니다.

## 6. 도커 시작

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

docker start를 사용하여 컨테이너 시작하기: docker start 명령어는 이전에 중지된 컨테이너를 시작하는 데 사용됩니다. 새로운 컨테이너를 만들고 시작하는 docker run과 달리, docker start는 기존의 컨테이너를 다시 시작하여 이전 상태와 구성을 보존합니다. 유지보수나 업데이트 후 빠르게 작업을 재개해야 할 때 컨테이너를 다시 구성할 필요 없이 사용할 수 있어 특히 유용합니다. 예를 들어, 웹 서버 컨테이너를 중지하여 구성을 업데이트한 후, docker start `container_id`를 사용하여 신속하게 온라인으로 다시 만들 수 있습니다.

## 7. docker rm

docker rm을 사용하여 컨테이너 삭제하기: docker rm 명령어는 하나 이상의 중지된 컨테이너를 삭제하는 데 사용됩니다. 이를 통해 사용하지 않는 컨테이너를 제거하여 Docker 환경을 깨끗이 유지하고 디스크 공간을 확보할 수 있습니다. 이 명령어로 실행 중인 컨테이너는 삭제할 수 없다는 점에 유의해야 합니다. 먼저 docker stop을 사용하여 중지해야 합니다. 예를 들어, 컨테이너에서 개발 빌드를 시험한 후 해당 컨테이너를 중지한 경우, docker rm `container_id`를 사용하여 혼란을 방지하고 리소스를 회수할 수 있습니다.

## 8. docker rmi

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

도커 이미지 관리하기: 도커 rmi 명령어는 로컬 저장소에서 하나 이상의 도커 이미지를 삭제하는 데 사용됩니다. 이는 디스크 공간을 관리하고 오래된 이미지를 삭제하는 데 중요합니다. 도커 rmi `이미지_아이디`를 실행하면, 도커는 해당 이미지를 사용 중인 컨테이너가 없는 경우 이미지를 삭제합니다. 예를 들어, 애플리케이션을 업그레이드하고 새 이미지를 만든 후 이전 이미지를 삭제하여 혼란을 방지하고 저장 공간을 절약할 수 있습니다. 이 명령어는 조직화되고 효율적인 도커 환경을 유지하는 데 도움이 됩니다.

## 9. 도커 exec

도커 exec를 사용하여 컨테이너 내에서 명령어 실행하기: 도커 exec 명령어를 사용하면 이미 실행 중인 컨테이너 내에서 명령어를 실행할 수 있습니다. 이는 디버깅, 조사 또는 컨테이너의 상태를 중지하지 않고 수정하는 데 유용합니다. 예를 들어, docker exec -it `컨테이너_아이디` bash를 실행하면 컨테이너 내에서 대화형 터미널을 열어 파일 시스템을 탐색하거나 패키지를 설치하고 로그를 확인할 수 있습니다. 이 명령어는 컨테이너와 상호작용하고 필요한 유지 관리 작업을 동적으로 수행하는 강력한 방법을 제공합니다.

## 10. 도커 logs

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

도커 로그 검색하기: 도커 로그 명령은 특정 컨테이너의 로그를 가져와서 동작을 파악하고 디버깅하는 데 도움을 줍니다. 기본적으로 이 명령어는 컨테이너의 표준 출력(stdout)과 표준 에러(stderr) 스트림을 보여줍니다. -f와 같은 옵션을 사용하여 로그를 실시간으로 추적하거나 --tail을 사용하여 마지막 몇 줄을 볼 수 있습니다. 예를 들어, docker logs -f `container_id` 명령은 웹 서버 로그를 실시간으로 모니터링할 수 있어 문제를 진단하고 컨테이너가 정상적으로 작동하는지 확인하는 데 도움이 됩니다.

## 11. docker build

도커 빌드로 이미지 생성하기: 도커 빌드 명령은 Dockerfile과 컨텍스트로부터 도커 이미지를 생성합니다. 컨텍스트는 지정된 경로나 URL에 있는 파일 세트입니다. 이 명령은 Dockerfile을 읽고 각 명령을 처리하여 새 이미지를 생성합니다. 예를 들어, docker build -t myapp:latest . 명령은 현재 디렉토리에 있는 Dockerfile에서 myapp:latest로 태그된 이미지를 빌드합니다. 이 명령은 일관된, 재현 가능한 환경을 만들고 응용 프로그램과 그 종속성을 캡슐화하는 데 중요합니다.

## 12. docker tag

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

도커 태그를 사용하여 이미지에 태그 지정하기: 도커 tag 명령어는 기존 이미지에 새로운 태그를 생성하여 같은 이미지를 다른 이름이나 버전으로 참조할 수 있게 합니다. 이미지 버전을 관리하고 저장소 구성을 용이하게 하는 데 유용합니다. 예를 들어, myapp:latest 이미지를 myrepo/myapp:1.0으로 태그 지정하려면 docker tag myapp:latest myrepo/myapp:1.0을 입력하면 됩니다. 이렇게 하면 myapp:latest 이미지를 myrepo/myapp:1.0으로 태그하여 원격 저장소에 푸시하거나 동일 이미지의 다른 버전을 구별하는 데 도움이 됩니다. 올바른 태그 지정은 명확한 버전 관리 시스템을 유지하고 CI/CD 파이프라인을 간소화하는 데 도움이 됩니다.

## 13. 도커 푸시

도커 푸시를 사용하여 레지스트리에 이미지 푸시하기: 도커 push 명령어는 로컬에 태그 지정된 이미지를 도커 허브나 비공개 레지스트리와 같은 도커 레지스트리에 업로드합니다. 이를 통해 이미지를 다른 사용자와 공유하거나 다른 기기에 배포할 수 있게 됩니다. 예를 들어, docker push myrepo/myapp:1.0를 입력하면 myapp:1.0 이미지를 Docker Hub의 myrepo 저장소로 푸시할 수 있습니다. 도커 push를 사용하면 응용 프로그램 이미지를 다양한 환경에 분산 배포하여 배포의 일관성과 신뢰성을 보장할 수 있습니다.

## 14. 도커 inspect

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

도커 객체 검사하기: 도커 인스펙트를 통해 도커 객체를 검사할 수 있습니다. docker inspect 명령어는 컨테이너, 이미지, 볼륨 및 네트워크와 같은 도커 객체에 대한 상세 정보를 제공합니다. 이 명령은 설정, 상태 및 자원 사용량을 포함한 JSON 형식의 데이터를 반환합니다. 예를 들어, docker inspect `container_id` 명령을 실행하면 특정 컨테이너에 대한 포괄적인 세부 정보를 얻을 수 있어 문제 해결 및 구성 확인에 도움이 됩니다. 이 명령은 도커 환경을 깊이 이해하고 모든 것이 예상대로 구성되어 있는지 확인하는 데 매우 유용합니다.

## 15. docker network ls

도커 네트워크 목록 보기: docker network ls 명령을 사용하면 시스템에 있는 모든 도커 네트워크, 즉 브릿지, 호스트 및 오버레이 네트워크를 표시합니다. 이를 통해 네트워크 구성을 관리하고 문제를 해결하여 컨테이너가 의도한 대로 통신할 수 있도록 할 수 있습니다. 예를 들어, docker network ls를 실행하면 이름, ID, 드라이버 및 범위를 포함한 네트워크 목록이 표시됩니다. 네트워크를 이해하고 관리하는 것은 다중 컨테이너 애플리케이션을 설정하고 안전하고 격리된 통신 채널을 보장하는 데 중요합니다.

## 16. docker volume ls

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

도커 볼륨 ls를 사용하여 볼륨 관리: 도커 볼륨 ls 명령어는 시스템의 모든 도커 볼륨을 나열합니다. 볼륨은 영속적인 저장을 위해 사용되며, 컨테이너가 제거된 후에도 데이터를 지속할 수 있습니다. 이 명령어는 볼륨을 식별하고 관리하여 데이터가 올바르게 저장되며 컨테이너 간에 공유될 수 있도록 돕습니다. 예를 들어, 도커 볼륨 ls를 실행하면 모든 볼륨을 표시하여 사용되지 않는 볼륨을 찾고 정리할 수 있어 저장소 관리를 최적화할 수 있습니다.

## 17. 도커 커밋

도커 커밋을 사용하여 컨테이너에서 이미지 생성: 도커 커밋 명령어는 기존 컨테이너의 변경 사항에서 새 이미지를 생성합니다. 이는 컨테이너의 상태를 이미지로 저장하여 다시 사용하거나 공유하는 데 유용합니다. 예를 들어, 업데이트나 새 구성이 있는 실행 중인 컨테이너가 있다면 docker commit `container_id` mynewimage를 실행하여 해당 변경 사항을 반영한 새 이미지를 생성할 수 있습니다. 이 명령어는 미래 배포를 위해 컨테이너의 상태를 캡처하거나 개발 및 테스트 중 베이스라인 이미지를 생성하는 데 유용합니다.

## 18. 도커 업데이트

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

도커 업데이트 명령어는 실행 중인 컨테이너의 리소스 및 구성을 동적으로 조정하는 강력한 도구입니다. 이 도구를 사용하면 컨테이너를 중지시키지 않고도 성능을 최적화하고 리소스 할당을 관리하며 컨테이너화된 애플리케이션의 안정성을 향상시킬 수 있습니다. CPU 및 메모리 제한과 같은 매개변수를 실시간으로 수정함으로써 도커 업데이트를 통해 변화하는 요구 사항과 워크로드에 효과적으로 대응할 수 있으며, 컨테이너가 원활하고 효과적으로 작동할 수 있도록 필요한 리소스를 확보할 수 있습니다.

```js
docker update — memory 512m <container_id>
```

## 19. 도커 시스템 프룬

도커 시스템 프룬 명령어는 사용되지 않는 컨테이너, 네트워크, 이미지(둘 다 둔각하거나 참조되지 않는 이미지), 그리고 선택적으로 볼륨을 모두 제거하여 깨끗하고 효율적인 도커 환경을 유지하는 데 필수적인 도구입니다. 이러한 사용되지 않는 객체들은 시간이 지남에 따라 상당한 디스크 공간과 리소스를 소비할 수 있으며, 이는 혼잡하고 효율적이지 못한 설정으로 이어질 수 있습니다. 도커 시스템 프룬을 실행하여 빠르게 공간을 확보하고 도커 환경을 최적화함으로써 가벼우면서도 효율적으로 유지할 수 있습니다. 이 명령어는 특히 사용하지 않는 구성 요소를 수동으로 찾아내고 삭제하지 않고도 시스템을 원할하게 유지하고자 하는 개발자와 시스템 관리자들에게 매우 가치 있는 도구입니다.

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

## 20. docker save

도커 save 명령어는 도커 이미지를 tar 아카이브 파일로 내보내는 데 사용됩니다. 이미지 백업 및 배포를 관리하는 중요한 도구로 작용합니다. 도커 이미지를 tar 파일로 저장함으로써 사용자는 다른 환경 간의 이동을 보장하고 오프라인 전송을 용이하게 할 수 있습니다. 해당 명령어를 사용하면 개발, 테스트 및 프로덕션 환경 간에 이미지를 이동하거나 Docker 레지스트리에 직접 액세스 할 수 없는 협업자들과 이미지를 공유하는 작업이 단순화됩니다. docker save는 전체적으로 도커 이미지 관리에서 유연성과 신뢰성을 향상시키며 효율적인 워크플로와 백업 전략을 지원합니다.

```js
docker save -o myapp.tar myapp:latest
```

## 21. docker load

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

도커 로드 명령어는 Docker 이미지를 tar 아카이브 파일에서 Docker 환경으로 가져오는 데 사용됩니다. 이 명령어는 다양한 시스템 및 환경 간에 Docker 이미지를 배포하고 관리하는 데 필수적입니다. docker save로 생성된 tar 파일에서 이미지를 로드함으로써 사용자들은 빠르게 백업을 복원하거나 인터넷에 직접 액세스할 필요 없이 이미지를 오프라인으로 공유하거나 애플리케이션을 배포할 수 있습니다. 이는 개발, 테스트 및 운영 환경 간에 Docker 이미지를 전송하는 프로세스를 간소화하여 신속한 배포와 운영의 연속성을 보장합니다. 전반적으로, 도커 로드는 이미지 이식성을 유지하고 효율적인 도커 워크플로우를 용이하게 하는 데 중요한 역할을 합니다.

```js
도커 로드 < myapp.tar
```

## 22. docker restart

도커 재시작 명령어는 하나 이상의 실행 중인 도커 컨테이너를 정상적으로 재시작하는 데 사용됩니다. 이를 통해 컨테이너를 완전히 중지하지 않고 새로고침할 수 있어서 기본 애플리케이션의 구성 변경이나 업데이트에 유용합니다. 도커 재시작을 사용하면 컨테이너가 상태와 구성을 유지하면서 변경 사항을 원활하게 통합할 수 있습니다. 이 명령어는 다운타임과 중단을 최소화하여 운영 환경에서 도커화된 애플리케이션의 가용성과 성능을 유지하는 데 필수적인 도구입니다.

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
도커 다시 시작하기: `docker restart abc123`

## 23. 도커 스탯

도커 스탯 명령어는 실행 중인 도커 컨테이너의 자원 사용량에 대한 실시간 정보를 제공합니다. 도커 스탯을 실행하여 CPU 사용량, 메모리 소비량, 네트워크 I/O, 그리고 각 컨테이너의 블록 I/O와 같은 주요 메트릭을 모니터링할 수 있습니다. 이 명령어는 도커 환경에서 성능 모니터링, 용량 계획, 그리고 문제 해결을 위한 중요한 도구입니다. 지속적으로 업데이트되는 데이터 스트림을 표시하여 사용자가 리소스 병목 현상을 식별하고 컨테이너 구성을 최적화하며 효율적인 자원 할당을 보장할 수 있습니다. 총론적으로, 도커 스탯은 도커화된 애플리케이션의 자원 활용을 실시간으로 확인함으로써 애플리케이션의 건강과 안정성을 유지하는 데 중요한 도구입니다.

도커 스탯:

CONTAINER ID   NAME         CPU %     MEM USAGE / LIMIT    MEM %     NET I/O       BLOCK I/O   PIDS
a8d65c28c29f   webapp       0.50%     32MiB / 1GiB         3.20%     280kB / 12kB  0B / 0B     2
eb5a6e74b99d   database     1.20%     256MiB / 2GiB        12.80%    1.2MB / 45kB  0B / 0B     5
f6e49f3d2a1e   cache        0.10%     64MiB / 1GiB         6.40%     800kB / 2MB   0B / 0B     1

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

## 24. 도커 킬

도커 킬 명령어는 실행 중인 도커 컨테이너를 강제로 종료시켜주는 것으로, SIGKILL 신호를 보내어 컨테이너 내의 모든 프로세스를 즉시 중지시킵니다. 도커 스톱과 달리 우아한 종료(SIGTERM 후 필요한 경우 SIGKILL)를 시작하지 않고, 클린업이나 종료 절차를 우회합니다. 이는 무응답이거나 문제가 있는 컨테이너를 빠르고 갑작스럽게 중지하는 방법으로, 즉각적인 종료가 필요한 상황에서 주로 사용됩니다. 예를 들어 문제 해결 중이거나 정상적인 중지 방법이 작동하지 않을 때 사용됩니다.

## 25. 도커 탑

도커 탑 명령어는 Linux의 top 명령어가 작동하는 방식과 유사하게, 도커 컨테이너 내에서 실행 중인 프로세스를 간결하게 보여줍니다. 프로세스는 해당하는 프로세스 ID(PID), 사용자, 누적 CPU 시간 및 실행 중인 명령어와 함께 나열됩니다. 이 명령어는 도커 컨테이너 내에서의 실시간 모니터링과 문제 해결에 유용하며, 운영자 및 개발자는 빠르게 활성 프로세스를 식별하고 자원 활용률을 분석하며 성능 문제를 진단할 수 있습니다. 컨테이너 내부 동작에 대한 중요한 가시성을 제공하여 도커화된 애플리케이션을 효율적으로 관리하고 최적화하는 데 도움이 됩니다.

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

도커 상단 <컨테이너 ID>

도커 상단 my-nginx

PID    USER     TIME        COMMAND
1      root     0:00        nginx: master process nginx -g daemon off;
7      nginx    0:00        nginx: worker process

## 26. 도커 cp

도커 cp 명령어는 도커 컨테이너와 로컬 파일 시스템 간에 파일이나 디렉토리를 복사하는 데 사용됩니다. 이 명령어는 호스트 머신과 실행 중인 컨테이너 간, 또는 컨테이너 자체 간에 데이터 전송을 용이하게 합니다.

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

예시:

도커 컨테이너에서 ID가 abc123인 파일인 example.txt를 호스트 머신의 현재 디렉토리로 복사하려면 다음을 사용합니다:

docker cp abc123:/path/to/example.txt ./example.txt

반대로 로컬 파일 시스템에서 도커 컨테이너로 파일을 복사하려면 다음을 사용합니다:

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

docker cp ./localfile.txt abc123:/path/inside/container/

요약하면, 도커 명령어는 현대적인 컨테이너화된 애플리케이션 개발 및 배포에 필수적인 포괄적인 도구 세트를 형성합니다. 이미지를 생성하는 docker build와 컨테이너를 시작하는 docker run과 같은 기본적인 명령어부터 축적 및 확장과 같은 차원에서의 응용 프로그램을 관리하는 프로세스를 간소화하는 Docker는 여러 환경에서 응용 프로그램을 관리하고 확장하는 과정을 단순화합니다. docker-compose는 다중 서비스를 쉽게 정의하고 관리할 수 있는 오케스트레이션 능력을 향상시킵니다. docker stats를 통해 컨테이너 성능 모니터링이 용이하고, 문제 해결과 디버깅은 docker logs와 docker exec를 통해 용이해집니다. docker cp를 사용하면 컨테이너와 호스트 시스템 간에 원활한 파일 전송이 가능하며, 효율적인 데이터 관리를 지원합니다. 종합적으로, Docker 명령어는 개발자와 운영팀이 효율적으로 응용 프로그램을 구축, 배포 및 유지 관리할 수 있도록 돕습니다. 소프트웨어 개발 수명주기에서의 민첩성과 신뢰성을 유지합니다.
```
