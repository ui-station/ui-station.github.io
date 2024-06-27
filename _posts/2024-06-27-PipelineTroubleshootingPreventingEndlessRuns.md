---
title: "파이프라인 문제 해결 무한 실행 방지 방법"
description: ""
coverImage: "/assets/img/2024-06-27-PipelineTroubleshootingPreventingEndlessRuns_0.png"
date: 2024-06-27 19:31
ogImage: 
  url: /assets/img/2024-06-27-PipelineTroubleshootingPreventingEndlessRuns_0.png
tag: Tech
originalTitle: "Pipeline Troubleshooting: Preventing Endless Runs"
link: "https://medium.com/@pattiya.work/pipeline-troubleshooting-preventing-endless-runs-edba505f1b23"
---


Jenkins 가이드를 따르다가 파이프라인이 계속 실행되는 문제가 발생한다면, 여기 가이드가 도움이 될 거예요. 대략 10-20분 정도면, Jenkins 튜토리얼 웹사이트의 예제 프로젝트를 활용하여 이 문제를 해결하는 방법을 배울 수 있어요.

Jenkins에 사용자 이름: admin, 비밀번호: admin으로 로그인한 후, simple-node-js-react-npm-app 프로젝트로 이동한 다음 파이프라인을 실행하려고 시도했는데 desktop-jenkins_agent-1-node 컨테이너를 아직 시작하지 않았다면, 콘솔 출력에 아래와 같은 내용이 표시될 거예요.

이 문제를 마주한 경우 이렇게 보일 거예요.

출력은 'docker-ssh-jenkins-agent'이 오프라인이며 빌드가 계속 실행되는 상황입니다.

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

# 단계 1: Jenkins 컨트롤러 컨테이너 시작

먼저, Docker Desktop을 열고 quickstart-tutorials라는 컨테이너를 찾아보세요.

그 다음으로, quickstart-tutorials-jenkins_controller-1 컨테이너를 찾아 체크박스 옆의 화살표 기호를 클릭하여 실행 상태로 변경하세요.

# 단계 2: Jenkins에 로그인하고 Jenkins 에이전트 확인

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

어떤 웹 브라우저에서 http://localhost:8080을 검색한 후, USERNAME: admin, PASSWORD: admin으로 로그인해주세요.

!! 이 프로젝트 외에 다른 컨테이너를 동시에 실행 중이 아닌지 확인해주세요.

이후 Jenkins 관리로 이동하시고 - '노드'로 이동하면 Agent docker-ssh-jenkins-agent가 오프라인 상태임을 확인할 수 있습니다.

docker-ssh-jenkins-agent - 로그 페이지로 이동하여 로그를 확인해주세요. 이는 아래 그림과 같이 표시됩니다.

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

연결이 끊어졌습니다. 이는 desktop-jenkins_agent-1-node 컨테이너를 실행해야 한다는 것을 의미합니다.

## 단계 3: Jenkins 에이전트 컨테이너 시작

다시 Docker Desktop을 열어서 quickstart-tutorials 컨테이너 내부에 있는 desktop-jenkins_agent-1-node 컨테이너를 찾고 실행하십시오.

## 단계 4: 에이전트 실행

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

Jenkins 웹 사이트에 접속하신 후, "관리" - "노드" - "docker-ssh-jenkins-agent"로 이동하여 "에이전트 실행" 버튼을 클릭하고 로그를 확인해주세요.

"에이전트가 성공적으로 연결되어 온라인 상태입니다"라는 메시지가 표시될 것입니다.

이후, simple-node-js-react-npm-app 프로젝트 페이지로 돌아가서 "지금 빌드"를 한 번 더 클릭하여 다시 확인할 수 있습니다.

위 단계를 따라하신 후, 대시보드 - "simple-node-js-react-npm-app" 페이지로 이동해서 "지금 빌드"를 클릭하고, 왼쪽 패널에서 최신 빌드 기록을 클릭한 뒤, 왼쪽 내비게이션 패널에서 "입력 대기"를 선택하시면 됩니다.

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

아래 순서를 따르면 Jenkins에서 계속해서 실행되는 파이프라인 문제를 해결할 수 있습니다. 이는 실행이 원할하게 되는 뿐만 아니라 Jenkins와 Docker를 관리하는 방법을 더 잘 이해하는 데도 도움이 됩니다.