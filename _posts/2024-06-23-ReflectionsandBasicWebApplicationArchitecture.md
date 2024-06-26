---
title: "기본 웹 애플리케이션 아키텍처와 회고 핵심 개념 및 예시"
description: ""
coverImage: "/assets/img/2024-06-23-ReflectionsandBasicWebApplicationArchitecture_0.png"
date: 2024-06-23 00:17
ogImage:
  url: /assets/img/2024-06-23-ReflectionsandBasicWebApplicationArchitecture_0.png
tag: Tech
originalTitle: "Reflections and Basic Web Application Architecture"
link: "https://medium.com/@blaizebodda/reflections-and-basic-web-application-architecture-1d8ec693ec75"
---

<img src="/assets/img/2024-06-23-ReflectionsandBasicWebApplicationArchitecture_0.png" />

둘째 주가 끝나고, GitHub와 버전 관리에 대해 많이 배웠어요!

이번 주에 저는 GitHub에서 저장소를 만들고, 커맨드 라인 인터페이스(CLI)를 사용하여 컴퓨터에 복제했어요. 또한 CLI로 커밋하고 README 파일을 GitHub에 푸시하고, 새로운 브랜치를 만들고, 파일을 업데이트하고, 풀 리퀘스트를 메인 브랜치에 병합하는 등 많은 작업을 할 수 있다는 것을 발견했어요. 게다가, 컴퓨터에 아마존 웹 서비스(AWS) CLI를 설치하고 연습했어요.

이번 주에 웹 애플리케이션 아키텍처의 기본을 배우고, 배운 내용을 강화하기 위해 간단한 설정을 디자인하는 데 많은 시간을 보냈어요. AWS와 어떻게 관련이 있는지 알아볼까요?

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

# 웹 애플리케이션 아키텍처

그렇다면, 먼저 웹 애플리케이션 아키텍처는 무엇일까요?

웹 애플리케이션 아키텍처는 웹 사이트가 어떻게 구성되어 작동하는지를 나타냅니다. 세 가지 주요 구성 요소가 있습니다: 프론트엔드, 백엔드 및 데이터베이스 (아래에 만든 다이어그램 참조). 프론트엔드는 당신이 보고 클릭하는 것들인데, HTML, CSS 및 JavaScript로 만들어진 버튼이나 사진과 같은 것들입니다. 백엔드는 웹 사이트의 뇌와 같습니다. 모든 로직과 처리를 처리하며 Python이나 JavaScript와 같은 언어를 사용합니다. 데이터베이스는 모든 정보가 저장되는 곳으로, 큰 디지털 파일 캐비닛처럼 작동합니다. 이러한 부분들은 웹을 통해 서로 대화하여 웹 사이트를 사용할 때 모든 것이 원활히 작동하도록 합니다. 아래는 모든 게 어떻게 작동하는지에 대한 이미지입니다.

![웹 애플리케이션 아키텍처 다이어그램](/assets/img/2024-06-23-ReflectionsandBasicWebApplicationArchitecture_1.png)

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

# 용어 설명

위 다이어그램에 포함된 내용을 더 잘 설명하기 위한 어휘 목록입니다.

최종 사용자: 제품이나 서비스를 사용하는 사람입니다. 이 경우에는 웹 애플리케이션과 상호 작용하는 사람으로, 휴대폰이나 태블릿과 같은 모바일 기기를 통해 웹 애플리케이션과 상호 작용하는 사람을 의미합니다.

프론트엔드: 웹사이트나 앱의 일부로, 사용자가 보고 상호 작용하는 부분을 말합니다. 화면에 나타나는 모든 것인 버튼, 그림, 텍스트, 메뉴 등을 포함합니다. HTML, CSS, JavaScript와 같은 도구를 사용하여 멋지게 보이고 매끈하게 작동하도록 만들어집니다.

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

도메인 이름 시스템(Domain Name System)은 전화번호부와 비슷해요. www.amazon.com 같은 웹사이트를 방문하려고 할 때 브라우저에 입력하면 DNS가 빠르게 웹사이트가 저장된 위치(IP 주소)를 찾아내고 컴퓨터에게 어떻게 찾아갈 지 알려줘요. 이렇게 하면 복잡한 주소를 알 필요 없이 원하는 웹사이트에 방문할 수 있어요. AWS Route 53은 AWS가 제공하는 DNS 서비스에요.

백엔드는 모든 데이터와 정보가 저장되고 처리되는 곳이에요. 웹사이트에서 버튼을 클릭하여 메시지를 보내거나 물건을 구매하는 등의 작업을 할 때 백엔드가 그 요청을 처리해줘요.

로드 밸런서는 웹사이트와 앱의 교통 관리자와 같아요. 많은 사람들이 동시에 웹사이트를 방문하려고 할 때, 로드 밸런서는 모든 요청을 처리하는 데 도움을 줘요. 각 서버에 일을 고르게 분배하여 한 대의 서버가 과부하되지 않게 해요. 그래서 웹사이트는 모든 사용자에게 빠르고 반응성 있는 상태를 유지할 수 있어요. AWS에서 로드 밸런서를 "Elastic Load Balancer"(ELB)라고 부르고 있어요.

서버는 인터넷을 통해 다른 컴퓨터와 정보를 저장하고 공유하는 강력한 컴퓨터에요. 폰에서 웹사이트나 앱을 사용할 때 실제로 세계 어딘가의 서버에 연결하게 될 거예요. 이 서버는 온라인에서 보거나 상호작용하는 모든 사진, 비디오, 텍스트 등을 저장해요. 인터넷 상의 모든 것이 원할하고 빠르게 작동할 수 있도록 노력하며, 좋아하는 웹사이트와 앱에 언제든지 접속할 수 있도록 해줘요. AWS는 클라이언트가 활용할 수 있도록 전 세계의 큰 데이터 센터에 EC2 서버를 구축해 두고 있어요.

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

데이터베이스: 웹 애플리케이션 데이터베이스는 사용자 이름, 비밀번호, 사진 등 웹 사이트가 기억해야 하는 모든 중요한 정보를 보관하는 디지털 컨테이너와 같습니다. 웹 사이트에 가입하거나 댓글을 게시할 때 이 정보는 데이터베이스에 저장되어, 웹 사이트가 다음 방문 때에도 해당 정보를 기억할 수 있습니다. 데이터베이스는 웹 사이트의 모든 것이 원할하게 작동하고 정보가 안전하고 조직적으로 유지되도록 보장합니다. AWS RDS(관계형 데이터베이스 서비스)가 이러한 예시 중 하나입니다.

# 반성

2주차에는 일부 어려움이 있었지만 그때마다 능력이 더 향상된 것 같아요. 이 어려움들은 웹 개발 도구와 관행의 기본 원리에 대한 탐구의 여정으로 이끌어주었어요. GitHub를 이용해 저장소 생성, 복제, 명령 줄 인터페이스를 통한 버전 관리를 익혔고, AWS CLI를 탐험하며 앞으로의 프로젝트에서 귀중한 경험을 쌓을 수 있었어요.

이번 주 중요한 부분 중 하나는 웹 애플리케이션 아키텍처를 이해하는 데 헌신했습니다. 웹 사이트가 원활하게 작동하도록 보장하는 설계도와 같다는 것을 배웠어요. 이는 세 가지 핵심 부분으로 구성되어 있어요: 사용자가 버튼과 시각적 요소와 상호작용하는 프론트엔드, 로직 처리와 배경에서의 처리를 담당하는 백엔드, 모든 웹 사이트 정보를 안전하게 저장하는 데이터베이스가 있어요. 이러한 구성 요소는 협력하여 통일된 사용자 경험을 제공합니다.

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

미래를 기대하며, 웹 애플리케이션 아키텍처의 복잡성에 더 깊이 파고들기를 기대하고 있어요. 다음 주에는 이러한 기본 개념이 AWS 서비스와 어떻게 통합되어 내 이해력과 기술 세트를 더욱 향상시키는지 살펴볼 거에요. 클라우드 엔지니어링의 매혹적인 세계에 대한 더 많은 통찰력을 기대해 주세요!

지금은 여기까지입니다!

Blaize
