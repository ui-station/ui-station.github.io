---
title: "Argo CD v212 릴리스 후보판"
description: ""
coverImage: "/assets/img/2024-06-19-ArgoCDv212ReleaseCandidate_0.png"
date: 2024-06-19 12:59
ogImage:
  url: /assets/img/2024-06-19-ArgoCDv212ReleaseCandidate_0.png
tag: Tech
originalTitle: "Argo CD v2.12 Release Candidate"
link: "https://medium.com/argo-project/argo-cd-v2-12-release-candidate-90793368bfb5"
---

저희가 기쁜 마음으로 Argo CD v2.12 릴리스 후보판이 공개되었다는 소식을 전해드립니다! 이번 릴리스에는 30개 이상의 새로운 기능, 70여 개의 버그 수정, 그리고 60개의 문서 업데이트가 포함되어 있어요.

곧바로 릴리스 후보판을 테스트하고 마주한 어떤 버그나 문제에 대한 피드백을 보내주시면 감사하겠습니다. 이는 여러분이 의견을 전할 수 있고 Argo CD가 더 나아지도록 도와줄 수 있는 큰 기회입니다.

# Multi-source application advancements

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

여러 소스에서 Argo CD 애플리케이션을 생성하는 것은 오랜 시간 동안 가장 요청이 많았던 Argo CD 기능 중 하나였습니다. 이 기능을 통해 여러 위치(예: 공개 Helm 차트 및 로컬 값 파일)에서 정보를 그룹화하여 단일 Argo CD 애플리케이션을 형성할 수 있습니다. 여러 소스를 정의하는 초기 지원은 이미 Argo CD 버전 2.6에 추가되었고, CLI를 위한 지원은 2.11에 추가되었습니다. UI는 여전히 애플리케이션이 단일 소스를 가지고 있다고 가정하고 롤백과 같은 특정 CLI 기능은 여러 소스를 가진 애플리케이션에 대해 아직 지원되지 않았습니다.

Argo CD 버전 2.12에서는 여러 소스 애플리케이션의 롤백이 이제 Argo CD UI 및 CLI에서 모두 가능합니다.

롤백 기능 외에도, 애플리케이션 세부 정보 페이지에 새로운 "소스" 탭이 추가되어 사용자가 애플리케이션의 소스를 관리(보기 및 편집)할 수 있습니다.

<img src="/assets/img/2024-06-19-ArgoCDv212ReleaseCandidate_1.png" />

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

Keith Chong (Red Hat)과 Jorge Turrado에게 이러한 기능들을 구현해 줘서 감사합니다.

# 프로젝트별 저장소 자격증명 개선사항

현재 Argo CD API에서는 동일한 URL을 공유하는 여러 저장소 자격증명을 허용하지 않습니다. 저장소 자격증명이 argocd 네임스페이스에 직접 추가된 경우, argocd-server는 오류를 반환하지 않지만 이 작업은 작동하지 않습니다. URL과 일치하는 첫 번째 시크릿이 반환되며 순서도 정의되어 있지 않기 때문입니다.

Argo CD 버전 2.12부터는 여러 앱 프로젝트가 동일한 URL을 가진 별도의 저장소 자격증명을 가질 수 있도록 합니다.

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

안녕하세요 Blake Pettersson님(Akuity)!

# Kubernetes 이벤트에 레이블 추가하기

Argo CD 버전 2.12에서 사용자들은 Argo CD에서 생성된 k8s 이벤트에 애플리케이션 레이블을 노출할 수 있게 될 것입니다. `resource.includeEventLabelKeys`에서 정의된 특정 레이블 키를 가진 애플리케이션에 대해 생성된 이벤트에 대응하는 레이블이 이벤트에 첨부될 것입니다. 이 연결은 이러한 레이블을 사용하는 애플리케이션을 기반으로 이벤트를 필터링하거나 처리하는 것을 더 간단하게 만들어 줍니다.

이 기능을 구현해준 Siddhesh Ghadi(Red Hat)님에게 감사드립니다.

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

# 일관성있는 샤딩 알고리즘

Argo CD는 다른 Argo CD 애플리케이션 컨트롤러에 대한 샤딩 적용 기능을 제공합니다. 이를 통해 특정 클러스터를 특정 컨트롤러에 할당하여 부하를 다양한 샤드로 분산시킬 수 있습니다.

기존의 샤딩 알고리즘인 레거시 방법과 라운드 로빈 알고리즘을 포함한 기존 방식은 최적의 부하 분산 유지와 불필요한 클러스터-샤드 할당 변경을 최소화하는 데 제한 사항이 있었습니다.

Argo CD 버전 2.12부터 새로운 샤딩 알고리즘인 "일관적 해싱(consistent-hashing)"이 소개되었으며, 이는 클러스터-샤드 할당 변경을 줄이고 리소스 이용률을 최적화합니다.

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

이번 특징을 구현한 Akram Ben Aissi (Red Hat)에게 감사드립니다.

# 기타 주목할만한 변경 사항

릴리스에 추가된 몇 가지 새로운 변경 사항은 다음과 같습니다.

- 시멘틱 버전 태그 해결을 위한 git 클라이언트 업데이트 (Stone Payments의 Pablo Aguilar가 수행)
- Application Set Git Generator가 이제 GPG 서명 확인을 지원합니다 (Red Hat의 Ishita Sequeira가 수행)
- ls-remote 요청 실패 지표 추가 (Jack-R-lantern이 수행)
- 새로운 주석 argocd.argoproj.io/sync-options: Force=true 추가 (CyberAgent, Inc.의 Kota Kimura가 수행)
- gRPC 메시지 크기를 환경 변수로 설정하는 지원 추가 (Codefresh의 Pavel Kostohrys가 수행)
- 삭제 팝업에서 종속 리소스 목록 표시 (Intuit의 Alexandre Gaudreault가 수행)
- old tracking label applications.argoproj.io/app-name에 대한 지원 제거 (Akuity의 Soumya Ghosh Dastidar가 수행)
- Argo CD CLI에 대한 fish 쉘 완성 지원 추가 (Sn0rt가 수행)
- 로컬로 존재하는 체크아웃할 커밋이 있는 경우 git fetch 호출 건너뛰기 (Shady Rafehi가 수행)

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

# 새 릴리스는 어디에서 받을 수 있나요?

더 자세한 내용과 설치 지침은 릴리스 노트와 업그레이드 지침을 확인해주세요. 릴리스 후보를 시도하고 피드백을 공유해주세요. Argo 커뮤니티의 모든 기여자와 사용자들께 기여, 피드백 및 릴리스 테스트에서 도와준 점에 크게 감사드립니다!
