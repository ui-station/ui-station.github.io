---
title: "쉘 스크립트를 여전히 작성해야 하는 이유"
description: ""
coverImage: "/assets/img/2024-05-17-WhyYouShouldStillWriteShellScripts_0.png"
date: 2024-05-17 18:51
ogImage:
  url: /assets/img/2024-05-17-WhyYouShouldStillWriteShellScripts_0.png
tag: Tech
originalTitle: "Why You Should Still Write Shell Scripts"
link: "https://medium.com/itnext/why-you-should-still-write-shell-scripts-0a24e9174ee5"
---

고전적 접근 방식에 대한 현대적인 사례.

![이미지](/assets/img/2024-05-17-WhyYouShouldStillWriteShellScripts_0.png)

솔직히 말해서, grep -o `Response Code:.*` | cut -f 3 -d ` ` | sort | uniq -c | sort -h 3을 지켜보는 것은 고오급한 Kubernetes Operator를 사용하여 일어나는 것만큼 흥미롭지 않습니다. 많은 소프트웨어 전문가들, 시스템 관리자의 건강한 비율을 포함하여 긴 셸 스크립트를 보는 것은 공포와 혼란의 조합으로 인식합니다. 그러나 나는 현대 엔지니어가 갖춰야 할 유용한 기술 중 하나로 셸 스크립트를 편안하게 작성하고 한 줄 기반으로 만드는 능력을 주장합니다.

대학 시절에 스크립팅 수업을 들었습니다. 수업의 처음 몇 주는 Perl로 넘어가기 전에 셸의 기본 사항을 배우는 데 집중했습니다. 우스꽝스럽게도, 지금은 이 매우 같은 강의를 가르치지만 내용은 Perl에서 Python으로 변화했습니다. 불가피하게, 나의 학생들은 저와 같은 어려움을 겪습니다: 셸 구문은 난해하고 혼란스럽고 기억하기 어려운 것들입니다. 그것은 단순히 "오래된" 느낌이 들어서, 첫 번째 셸 스크립트를 작성하는 것은 어둠 속에서 헤매고 있는 것과 같은 느낌입니다.

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

시간이 흐르면서 저는 쉘을 매우 강력한 기능과 풍부한 역사를 가진 도구로 칭찬하게 되었습니다. 쉘 스크립트를 작성하는 방법을 배우는 것은 아마도 여러분이 익숙한 것과는 다르게 사용자 친화적이지 않을 수도 있습니다. 특히 Python이나 Go와 같은 언어에서 오신 경우에는 더 그렇습니다. 그러나 나는 모두가 쉘 스크립팅 능력을 키우는 데 시간을 쏟는 것이 매우 값진 일이라고 생각합니다.

아래에는 파일을 열 때 #!/bin/bash라는 구문을 보고 신경 쓰이는 경우 고려해 볼 다섯 가지 이유가 있습니다:

## Reason 1: Containers Demand It

컨테이너는 현재 워크로드를 배포하는 표준 방식입니다. 동의하든 그렇지 않든, 쿠버네티스 열풍에 흔들리지 않았더라도 환경에서 컨테이너화된 워크로드를 배포 중인 가능성은 높습니다.

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

바이너리 엔트리포인트에 의존하는 컨테이너를 빌드할 수 있지만, 보통 컨테이너는 시작 스크립트를 실행합니다. 이는 실행 시간 구성을 제한하는 기회를 제공합니다. 고품질 쉘 스크립트를 작성하면 컨테이너 엔트리포인트의 품질을 향상시켜 컨테이너 이미지 사용자에게 강력한 실행 시간 구성을 제공할 수 있습니다. 이는 오류 확인부터 자격 증명 또는 구성 매개변수를 자동으로 생성하는 것과 같은 더 복잡한 구성까지 모두 포함됩니다.

게다가: 엔트리포인트 스크립트를 POSIX 쉘(#!/bin/sh)로만 제한하는 방법을 배우면 컨테이너 이미지에서 Bash를 제거하고 그 크기와 공격 표면을 줄일 수 있습니다.

# 공식 MariaDB 컨테이너는 623줄의 도커 엔트리포인트 쉘 스크립트를 사용합니다.

$ podman exec -it mariadb wc -l /usr/local/bin/docker-entrypoint.sh
623 /usr/local/bin/docker-entrypoint.sh

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

로그 및 추적 프로젝트는 대략 백만 개 정도가 있어요. 왜 그럴까요? 로깅은 도전적입니다, 특히 분산 마이크로서비스 세계에서요. 멋진 추적 도구와 로그 집계 서비스는 훌륭하지만 종종 명령줄에서 찾을 수 있는 그 유연성을 제공하지 못할 때가 많아요. 저는 파이프된 명령어 한 줄을 작성하는 것이 대부분의 로깅 시스템 UI를 헤매는 것보다 빠르다고 생각해요.

GUI를 버리지 않는 것을 추천하지 않아요. 오히려 그게 유용해요. 텍스트 데이터를 그래픽 인터페이스와 명령줄 모두에서 탐색하는 것이 유용하기 때문이죠. 내 전형적인 워크플로우는 로깅 UI(예: 키바나)를 사용하여 관심 있는 로그로 대략적으로 필터링한 다음 로그 메시지를 다운로드하여 쉽게 구문 분석하기 위해 셸 유틸리티를 사용하는 것입니다. 원하는 것을 더 잘 알게 되면 실제 쿼리가 있는 대시보드를 작성하게 될 거예요.

생산 문제를 디버깅할 때도 아마 당신은 당신의 서버나 쿠버네티스 클러스터에 앉아 있을 거예요. 장애 발생 시 분 단위가 중요할 때 몇 번의 키 입력으로 바로 이론을 테스트하는 것이 절대적으로 중요합니다. 문제가 심각해서 로그 수집 자체가 고장났을 수도 있고, 아니면 터미널과 로깅 도구 간의 컨텍스트 전환을 계속할 여유가 없을 수도 있어요. 그 어떤 경우에도, 단순한 한 줄짜리 명령어를 만들어 내는 데 익숙해서 다행할 것입니다.

배우는 곡선은 있지만, 쉘에서 사용 가능한 많은 유틸리티로 편안해지면 쉘 명령어를 사용하여 데이터를 "탐색"하는 것이 훨씬 쉽다는 것을 알게 되실 거예요. 필요한 데이터를 빠르게 반환하는 작은 스크립트 라이브러리를 점차적으로 구축할 수 있을 거예요. 제가 가장 좋아하는 것 중 하나는 제 Nginx 로그를 구문 분석하고 서버를 공격하는 상위 10개 IP 주소를 알려주는 간단한 Bash 한 줄짜리입니다.

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
$ cut -f 1 -d ' ' /var/log/nginx/access.log | sort | uniq -c | sort -hr | head -n 10
     38 65.108.66.11
     20 91.240.118.252
     18 194.110.115.68
     17 3.234.236.64
     11 190.117.58.32
     11 172.56.209.241
     11 165.225.233.21
     10 95.161.221.0
     10 85.173.195.175
     10 37.79.10.45
```

# Reason 3: 어떤 (텍스트) 데이터 형식이든

우리가 일상적으로 다루는 대부분의 데이터는 텍스트 형식입니다: 애플리케이션 데이터, 설정 파일, 로그 메시지 등등. 이러한 데이터나 로그 집계 플랫폼에서 이 데이터를 다루는 것은 데이터의 형식(또는 스키마)에 대해 신중한 고려를 요구합니다. 이는 장기적인 저장 및 검색을 위해 이상적인 선택입니다: 데이터 형식을 이해하는 것은 복잡한 데이터를 효율적으로 저장하고 쿼리하는 데 중요합니다.

하지만 당신은 아마도 데이터를 탐색하는 데 편의를 제공하는 방식으로 빠르고 쉽게 데이터를 다루고 싶어하는 상황에 직면해 본 적이 있을 것입니다. 아직 다루고 있는 데이터의 구조나 형식에 대해 이해하고 있지 않을 수도 있습니다. 이를 이해하려는 과정에 있을 수도 있고, 특정 데이터의 일부를 빠르게 쿼리하거나 변환하고 싶을 수도 있습니다.

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

쉘(shell)이 빛을 발하는 작업들이에요! 대부분의 쉘 도구들은 텍스트의 구조에 대해 크게 신경 쓰지 않고 원시 텍스트와 직접 작동합니다. 임의로 형식이 지정되지 않은 텍스트 파일에서 모두 dev를 prod로 변경하고 싶다면, 쉘이 도움이 될 거예요. JSON이나 YAML과 같은 구조화된 데이터 형식을 이미 사용하고 있다면, jq나 yq와 같은 도구를 사용해서 이러한 형식과 함께 작업할 수 있어요.

```js
# 개발용 구성 파일
$ cat /tmp/sample.json
{
    "Environment": "dev",
    "Database": {
        "Host": "dev.example.com"
    },
    "AppName": "iot-dev",
    "CacheShards": [
        "east1-cache-dev-1",
        "east1-cache-dev-2",
        "east1-cache-dev-3"
    ]
}

# Sed는 JSON, YAML, XML 또는 다른 형식이든 상관하지 않아요.
# 텍스트이면 됩니다!
$ sed -s 's/dev/prod/g' /tmp/sample.json
{
    "Environment": "prod",
    "Database": {
        "Host": "prod.example.com"
    },
    "AppName": "iot-prod",
    "CacheShards": [
        "east1-cache-prod-1",
        "east1-cache-prod-2",
        "east1-cache-prod-3"
    ]
}
```

# 이유 4: 자동화를 지지합니다

자동화는 소프트웨어 엔지니어부터 운영 전문가에 이르기까지 모두의 책임이에요. 소프트웨어 분야에서 일하고 있다면 이미 자동화의 이점을 잘 알고 있고, 아마도 자동화된 몇 가지 프로세스를 진정으로 자랑스럽게 자동화했을 거예요.

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

셸 스크립트는 존재하는 자동화 방식 중 가장 일반적이고 간단하며 기본적인 유형 중 하나입니다. Ansible, Puppet, Chef, Kubernetes 및 기타 도구들이 나오기 전에는 셸만 있었습니다. 옛날의 시스템 관리자들은 사용자 관리부터 보안 감사까지 모든 것을 다루기 위해 스크립트를 작성했습니다. 이러한 프로세스를 자동화하기 위해 다른 도구를 배울 필요가 없었기 때문에 셸은 시스템과 상호 작용하는 유일한 방법이었죠.

이러한 형태의 자동화를 이해하는 것은 그저 향수에 젖은 연습을 하는 것 이상입니다. 종종 복잡한 프로세스를 자동화하기 위해서는 간단한 셸 스크립트만으로 충분할 수 있습니다. 게다가 많은 자동화 플랫폼들은 이미 강력한 셸 스크립트의 래퍼일 뿐입니다. 셸 스크립트를 작성하고 읽는 방법을 이해하는 것은 자동화 기초에 대한 더 나은 이해를 제공받게 되며, 복잡한 문제를 간단한 방식으로 해결하는 데 도움이 될 것입니다.

# 이유 5: 모든 곳에서 존재하는 기술

현대 컴퓨팅 세계의 현실은 대부분의 기업이 이종 컴퓨팅 워크로드를 실행한다는 것입니다: 가상 머신, 컨테이너, 물리적 호스트 등이 있습니다. 워크로드는 클라우드, 온프레미스 및 엣지에서 실행됩니다. 우수한 엔지니어들은 이러한 환경을 횡단하여 편안하게 작업할 수 있어서 자신을 다른 사람들과 구분지을 수 있습니다.

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

하지만 최신 데이터 처리 또는 로그 분석 도구가 없어서 간소화된 컨테이너 애플리케이션 문제 해결에 어려움이 있을 때는 어떻게 해야 할까요? 새로운 팀(또는 직장)으로 옮겨갔을 때 새 역할에서 사용하는 도구체인이 완전히 다를 경우에는 어떻게 해야 할까요?

기본적인 셸 스크립트 작성에 능숙하다면 걱정할 필요가 없습니다. 완전히 다른 환경의 원격 시스템에 로그인하여 로그 데이터를 캡처하거나 자동화된 결함 해결 기능을 동일하게 수행할 수 있습니다. 이런 셸 스크립트를 작성하여 산업 분야에서 계속 발전시키는 경력을 쌓을 수 있습니다. 셸은 시간에 걸쳐 검증되어왔으며, 업계에서의 시간 동안 그것이 성과를 거두는 것을 발견하게 될 것입니다.

# 결론

작은 셸 스크립트도구 상자를 구축하세요. 퇴사할 때 멋진 Kubernetes 오퍼레이터, 놀라운 Kibana 대시보드 또는 멋진 커맨드 라인 도구를 가져갈 수 없을 수도 있습니다. 또한 그것은 간단히 기억에서 재구성하기에 너무 복잡할 수도 있습니다. 그러나 다음 회사에서는 셸이 포함된 시스템이 있을 것이며, 그럼에도 불구하고 당신이 익숙하고 쉽게 어떤 작업이든 수행할 수 있다는 것을 확신할 수 있습니다.

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

확실하게 말씀드리자면: 쉘 스크립트 작성에는 제약과 도전이 있습니다. 하지만 그에 대한 논의는 다음에 하도록 하죠. 그래도 컨테이너 엔트리포인트를 파헤치는 과정에서 #!/bin/sh를 보게 된다고 두려워하지 마세요. 구문과 스타일에 익숙해지면, 강력한 쉘 스크립트가 소프트웨어 전문가로써 우리의 삶을 더 쉽게 만든다는 점을 감사히 여기게 될 거에요.
