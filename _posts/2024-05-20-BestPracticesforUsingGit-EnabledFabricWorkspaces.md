---
title: "Git-활성화된 패브릭 워크스페이스 사용을 위한 최상의 실천법"
description: ""
coverImage: "/assets/img/2024-05-20-BestPracticesforUsingGit-EnabledFabricWorkspaces_0.png"
date: 2024-05-20 18:48
ogImage:
  url: /assets/img/2024-05-20-BestPracticesforUsingGit-EnabledFabricWorkspaces_0.png
tag: Tech
originalTitle: "Best Practices for Using Git-Enabled Fabric Workspaces"
link: "https://medium.com/@mjtpena/best-practices-for-using-git-enabled-fabric-workspaces-8bffd92d4bd5"
---

효율적으로 데이터 파이프라인을 관리하는 것은 원활한 개발, 테스트 및 배포 프로세스를 보장하기 위한 중요한 요소입니다. Microsoft Fabric 작업 공간과 Git을 통합하면 이러한 파이프라인을 효과적으로 관리할 수 있습니다. 데이터 아키텍트 및 엔지니어로 전환하기 전에 다양한 소프트웨어 프로젝트에서 CI/CD를 활용하는 데 중점을 둔 소프트웨어 엔지니어였습니다. 이 게시물은 Git을 활용한 Fabric 작업 공간의 최선의 실천 방법을 이해하기 위해 그림을 활용한 나의 해석에 대해 다룹니다.

![Diagram](/assets/img/2024-05-20-BestPracticesforUsingGit-EnabledFabricWorkspaces_0.png)

이 그림은 Azure DevOps와 Microsoft Fabric 간의 통합을 개발 환경에서 제품 환경까지의 흐름을 보여줍니다.

## Azure DevOps (Git)

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

• Main Branch: 안정적인 릴리스를 위한 메인 브랜치입니다.

• Develop Branch: 지속적인 개발을 위한 주요 브랜치입니다.

• Feature Branches: 특정 기능 개발을 위한 브랜치입니다.

## Microsoft Fabric (Workspaces)

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

- 릴리스 파이프라인: 다양한 환경(개발, 스테이징, 프로덕션)으로의 릴리스를 관리합니다.
- 개발 작업 공간: 개별 개발자 환경.

# 최상의 실천 방법

이것들은 대규모 또는 복잡한 Microsoft Fabric 프로젝트를 유지하는 데 도움이 되는 주요 배운 점입니다.

## 브랜칭 전략

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

파이프라인, 노트북, 리포트, 데이터 웨어하우스, 레이크하우스 등을 작업할 수 있는 Fabric "개발자"들이 git을 배워야 합니다. 나는 많은 데이터 엔지니어가 분석가나 DBA 경로에서 왔다는 것을 경험상 알기에, git을 배우는 것이 어려울 수 있다는 걸 알고 있어요. 이들의 경력 초창기에 git을 배우지 않은 경우가 많았습니다. 그러나 특히 Microsoft Fabric에서 큰 프로젝트를 수행하는 경우에는 협업을 위해 git이 필수적입니다.

기능 브랜치: 각 개발자는 자신의 기능이나 버그 수정을 위해 개발 브랜치의 브랜치를 만듭니다. Fabric을 네이티브로 사용하는 경우, "MJ의 프로젝트 A 작업 공간"과 같이 개인 작업 공간에 이를 연결할 수 있습니다. 작성 시점 기준으로 "내 작업 공간"을 Git으로 활성화할 수는 없으므로, 이를 위해 새로운 작업 공간을 할당해야 합니다. Power BI 리포트와 같은 경우 개발자가 Power BI 데스크톱을 사용하는 경우, 이 접근법 또한 작동하며 Fabric 작업 공간에 독립적입니다.

개발 브랜치: 새로운 기능을 통합하고 본 브랜치로 병합하기 전에 테스트하는 데에 이 브랜치를 사용하세요. 개발자가 실수로 이 브랜치에 푸시하거나 풀 리퀘스트 없이 수정하지 못하도록 브랜치 정책을 구현해야 합니다. 이 프로세스는 다른 개발자의 작업을 배포하기 전에 누군가가 검토해야 한다는 것을 강제합니다.

본 브랜치: 이 브랜치를 제품용 코드의 진리의 원천으로 유지하세요. 여기에는 안정적이고 테스트된 코드만 병합해야 합니다. 나중에 변경 내용을 롤백해야 하는 경우를 대비해 나중을 위해 충분한 태깅 전략을 사용하세요. 보너스로, 메인 브랜치에 유지된 히스토리가 두 가지다 하더라도 릴리스#1과 릴리스#75를 분리하는 릴리스 태그를 구현하는 것도 좋은 연습입니다.

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

메인 브랜치가 패브릭 워크스페이스에서 git을 사용할 수 없는 것에 유의해주세요. 워크스페이스에 연결된 브랜치는 개발 브랜치 뿐입니다. 다음 섹션에서 이에 대해 더 설명하겠습니다.

## 패브릭 Git 통합

Microsoft 패브릭 워크스페이스(파워 BI 워크스페이스 포함)는 git 통합을 지원합니다. 현재는 Azure DevOps만 지원하고 있으며, GitHub 통합을 기다리고 있습니다.

이를 위해 워크스페이스 설정을 클릭하고 "Git 통합" 탭으로 이동하세요. 이를 Azure DevOps에 연결하세요.

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

![image1](/assets/img/2024-05-20-BestPracticesforUsingGit-EnabledFabricWorkspaces_1.png)

통합을 위해 git 폴더를 추가하는 것을 추천하지 않습니다. Fabric는 분석을 위해 통합된 프로젝트로 표현되어야 합니다. 이는 응용 프로그램 소스 코드, 데이터베이스 스크립트 등을 저장소에 추가할 계획인 경우 복잡해지기 때문입니다. 이는 지원되지만, 개발자들이 Azure DevOps와 Fabric git 간에 컨텍스트 전환이 필요할 때 혼란스러울 수 있습니다.

즉, 모든 이러한 파일들:

![image2](/assets/img/2024-05-20-BestPracticesforUsingGit-EnabledFabricWorkspaces_2.png)

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

아래와 같이 Azure DevOps 저장소에서 테이블 태그가 표시됩니다:

![2024-05-20-BestPracticesforUsingGit-EnabledFabricWorkspaces_3.png](/assets/img/2024-05-20-BestPracticesforUsingGit-EnabledFabricWorkspaces_3.png)

## 작업 공간 관리

![2024-05-20-BestPracticesforUsingGit-EnabledFabricWorkspaces_4.png](/assets/img/2024-05-20-BestPracticesforUsingGit-EnabledFabricWorkspaces_4.png)

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

이미 이전에 언급한대로, 개발자들은 기능 브랜치를 워크스페이스에 연결합니다. 이 방식의 단점 또는 "불편함"은 개발자들이 새로운 기능을 개발할 때 이 프로세스를 반복해야 한다는 점입니다. 미래에는 "원 클릭 워크스페이스 생성" 기능이 있으면 더 원할텐데, 이 기능은 일시적인 워크스페이스를 의도한 것입니다. 그러나 반대로, 보통 기능 개발에는 몇 일이 걸리기 때문에 매주 한두 번 이 프로세스를 수행해야 한다는 것은 익숙해지면 나쁘지 않습니다.

![이미지](/assets/img/2024-05-20-BestPracticesforUsingGit-EnabledFabricWorkspaces_5.png)

기능 브랜치에서의 풀 리퀘스트가 개발 브랜치로 병합되면, Fabric에서 개발 워크스페이스로 동기화됩니다. 이 워크스페이스는 여러 개발자들 사이에서 "놀이터"로 공유됩니다. 여기서 개발자들은 여기서 실험하고 실수를 할 수 있으며, 엔드 투 엔드 파이프라인과 보고서가 어떻게 나타나는지 확인할 수 있습니다. 대부분의 경우, 이 워크스페이스는 개발자가 액세스 권한을 갖고 있는 데이터 소스에도 연결됩니다.

여기서 Git과 릴리스 관리 간의 분리선이 깨지게 됩니다.

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

## 릴리스 관리

![이미지](/assets/img/2024-05-20-BestPracticesforUsingGit-EnabledFabricWorkspaces_6.png)

가장 먼저 눈에 띄는 것은 스테이징과 프로덕션 워크스페이스가 Git에 연결되어 있지 않다는 점입니다. 이 접근 방식을 선택한 이유에는 여러 가지 이유가 있습니다.

- 개발과 배포의 역할과 책임을 분리하기 위함입니다. 2~3명의 개발자로 구성된 팀에서는 큰 문제가 아니지만 대규모 팀에서는 더 나은 접근 방식일 수 있습니다. 모든 사람이 누가 어떤 플랫폼 및 기능에 액세스 권한이 있는지 잘 알 수 있기 때문에 관리자들이 특정 정책 및 액세스를 관리하는 데 도움이 됩니다.
- "비개발자"는 Git을 사용할 필요가 없습니다. 대규모 조직에서는 이해관계자 및 임원과 같은 다른 역할이 Git을 사용할 필요가 없을 수 있습니다. 그러나 반대로 보고서를 보고 권한을 행사하고 싶어할 것입니다. 이 접근 방식은 이러한 시나리오에서 잘 작동합니다.
- 승인 시나리오. 스테이징 및 프로덕션으로의 릴리스 승인을 위해 누군가가 Git을 통해 확인할 필요가 없습니다. 대규모 조직에서는 적절한 릴리스 프로세스가 있지만 이 방식의 장점은 사실 "매니저"나 승인자가 개발자가 브랜치를 병합하는 대신 버튼을 클릭할 수 있다는 것입니다. 감사 추적은 관리자 X가 배포를 승인했음을 말할 수 있습니다.

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

<img src="/assets/img/2024-05-20-BestPracticesforUsingGit-EnabledFabricWorkspaces_7.png" />

## 요약 및 도전 과제

어떤 방식이든 의견이 갈리는 점이 있고, 이는 제가 다룬 대규모 Fabric 구현에 적합한 방식입니다. 이 방법이 가장 적합하지 않은 시나리오도 있습니다:

- 소규모 팀 (모든 작업을 하는 2명의 개발자)
- 멀티테넌시, 즉 스테이징과 프로덕션에 서로 다른 도메인이 있는 경우 (Entra ID). 이는 더 복잡한 기업 내 관계에도 적용됩니다.
- Azure DevOps에 액세스할 수 없는 경우 (당연히)

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

요약하면, 이 방법은 팀으로 작업하고 협업하는 데 도움이 되기 위해 Microsoft fabric 작업 공간에 Git를 활용하는 접근 방식입니다. 주요 강조점은 다음과 같습니다:

- 각 개발자는 자체 기능 브랜치와 Fabric 작업 공간에서 작업해야 합니다.
- 개발 브랜치를 사용하여 기능 브랜치를 병합하고 동료 검토를 촉진합니다. 그런 다음 개발 브랜치를 Fabric의 개발 작업 공간에 동기화합니다.
- Fabric 배포 파이프라인에서 배포를 수행하며 Git의 범위를 벗어납니다. 그러나 어떤 프로덕션 릴리스든 버전 관리 목적을 위해 메인 브랜치에도 태깅 및 표시되도록 해야 합니다.

이 다이어그램의 더 간단한 버전도 있습니다. 여기에는 메인 브랜치만 사용합니다. 이는 좋은 시작점이 될 수도 있습니다.

![다이어그램](/assets/img/2024-05-20-BestPracticesforUsingGit-EnabledFabricWorkspaces_8.png)

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

이 방법에 대해 어떻게 생각하시나요? 놓친 부분이 있나요? 더 좋은 대안이 있다고 생각하시나요? 댓글로 알려주세요.
