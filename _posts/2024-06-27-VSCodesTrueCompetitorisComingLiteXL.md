---
title: "VSCode를 위협하는 진정한 경쟁자 LiteXL 등장"
description: ""
coverImage: "/assets/img/2024-06-27-VSCodesTrueCompetitorisComingLiteXL_0.png"
date: 2024-06-27 19:24
ogImage: 
  url: /assets/img/2024-06-27-VSCodesTrueCompetitorisComingLiteXL_0.png
tag: Tech
originalTitle: "VSCode’s True Competitor is Coming: LiteXL"
link: "https://medium.com/gitconnected/vscodes-true-competitor-is-coming-litexl-22275759812e"
---


![이미지](/assets/img/2024-06-27-VSCodesTrueCompetitorisComingLiteXL_0.png)

몇 십 년 전에는 일반 텍스트 편집기와 특정 통합 개발 환경(IDE)을 사용하여 다양한 프로그래밍 언어로 코드를 작성했습니다. 아직도 마이크로소프트 노트패드로 Java와 C를 배웠던 것을 기억할 만큼 오랜 시간이 지났죠. 이후에 프로그래머들은 구문 강조와 같은 기본 코드 편집 기능을 갖춘 텍스트 편집기를 사용하기 시작했습니다. 이러한 특별한 텍스트 편집기들인 Notepad++나 Sublime Text는 코드 편집기로 알려졌습니다. 한편, IDE는 또한 개발자가 소스 코드를 생산적으로 작성하는 데 도움이 되었지만 특정 기술 스택에 제한되어 있었습니다. 예를 들어, Windows 앱을 구축하기 위해 Visual Basic 코드를 작성하기 위해 Visual Basic 6 IDE를 사용했습니다.

이후, 코드 편집기들은 일반 IDE 기능을 갖추어 프로그래머들이 어떤 프로그래밍 언어로든 코드를 작성할 수 있도록 했습니다. 인기 있는 코드 편집기는 일반적으로 구문 강조, 디렉토리 트리, 터미널, 언어 린팅 통합, 디버거/컴파일러 통합이 갖춰진 완전한 기능의 코드 편집 영역을 갖추고 있습니다. 이러한 편집기들은 또한 다양한 기술 스택에 대한 편리한 도구를 개발할 수 있도록 최소한의 플러그인 시스템을 소개했습니다. 이러한 이유로 VSCode와 유사한 코드 편집기들이 코드 편집 방식을 재정의할 수 있었습니다.

VSCode는 소프트웨어 산업의 기본 코드 편집기가 되었습니다. 이는 어떤 인기 있는 기술 스택에도 IDE와 유사한 환경을 제공하지만 내부 설계로 인한 성능 문제가 있습니다. VSCode는 하이브리드 앱(web 앱이 네이티브 창에서 실행됨)이며 결코 네이티브 앱이 되지 않습니다. 오래된 버려진 데스크톱 컴퓨터에서도 부드럽게 실행되는 VSCode와 유사한 참된 네이티브 편집기가 있다고 상상해 보세요!

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

# VSCode은 네이티브 룩 앤 필을 모방한 하이브리드 앱입니다

VSCode은 마이크로소프트의 오픈 소스 제품으로, 플랫폼 별 UI 요소를 사용하는 네이티브 코드 편집기 프로그램을 개발할 수 있는 거대한 소프트웨어 회사입니다. 그렇다면 왜 VSCode가 하이브리드 앱이 되었을까요?

친구야, 개발자들에게 이용자 친화적인 크로스 플랫폼 앱을 만드는 것은 어려운 작업이죠. 각 플랫폼 SDK의 프로그래밍 언어를 사용하여 플랫폼별 UI 요소가 포함된 네이티브 앱을 개발하는 경우, 각 플랫폼마다 별도의 프로젝트를 유지해야 합니다. 직접 렌더링 그래픽 라이브러리를 사용하고 모든 UI 요소를 네이티브로 빌드하는 것은 웹 기반 라이브러리와 가장 간단한 스타일링 언어인 CSS를 사용할 수 없으므로 시간이 많이 걸리죠. 하이브리드 앱을 개발하는 것은 웹 앱을 데스크톱 앱으로 전환할 수 있게 해주는 Electron과 같은 프레임워크가 존재하기 때문에, 크로스 플랫폼 데스크톱 앱을 빠르게 개발할 수 있는 가장 좋은 방법입니다. 게다가 하이브리드 개발 방식은 사용자들을 기여자로 바꾸어 버립니다. 모두가 웹 앱을 만들고 테스트하는 방법을 알기 때문이죠.

VSCode은 Electron을 사용하고 있기 때문에, 여러분이 좋아하는 코드 편집기는 웹 앱이며, 웹뷰 컴포넌트(즉, 크로미움) 내에서 실행되고 있으며 Node.js API와 함께 동작하는 모습입니다.

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


![Image 1](/assets/img/2024-06-27-VSCodesTrueCompetitorisComingLiteXL_1.png)

VSCode는 실제로 수백만 명의 개발자들을 능력을 부여하는 멋진 fully-featured 편집기이지만, 문제는 하드웨어 리소스를 최적으로 활용하는 진정한 네이티브 앱이 아니라는 것입니다 - 이것은 크롬 프레임을 포함하는 네이티브 창 내에서 실행될 뿐인, 네이티브 앱처럼 보이는 복잡한 웹 앱입니다:

![Image 2](/assets/img/2024-06-27-VSCodesTrueCompetitorisComingLiteXL_2.png)

고성능 컴퓨터라도 VSCode가 웹 앱임을 느끼게 하지 않겠지만, 일부 개발자들은 Electron으로는 해결할 수 없는 성능 문제로 인해 네이티브 VSCode 대안을 찾기 시작했습니다.


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

# VSCode 대체품 찾기

VSCode 생태계에는 수천 개의 놀라운 확장 기능과 수백만 명의 활발한 사용자가 있기 때문에 아직 VSCode의 모든 측면과 완벽하게 맞는 코드 편집기는 없습니다. 그러나, 우리는 좋은 플러그인 시스템을 제공하며 잘 유지되는 저장소와 잘 설계된 코드베이스를 갖춘 좋은 네이티브 VSCode 대안을 찾을 수 있습니다. 그러고 나서 오픈 소스 커뮤니티의 지원을 받아 VSCode 수준으로 개선할 수 있습니다. 그러나 VSCode 대안으로 또 다른 하이브리드 코드 편집기를 찾는 것은 성능 문제를 해결하지 못합니다. 그들은 여전히 네이티브와 유사한 웹 앱입니다:

- Adobe Brackets 하이브리드 코드 편집기 앱은 CEF (Chromium Embedded Framework)을 사용합니다.
- VSCodium은 마이크로소프트 텔레메트리 서비스를 제거했지만, 여전히 하이브리드 앱입니다.
- Nucleus는 Tauri를 사용하는등 일렉트론과 유사한 하이브리드 앱 프레임워크를 사용하는 코드 편집기는 성능 문제가 있습니다.

화면에 모든 GUI 요소를 수천 개의 DOM 요소를 사용하지 않고 네이티브로 렌더링하는 네이티브 코드 편집기를 찾아야 합니다. 다음 미리보기에서 VSCode가 하는 것처럼 모든 GUI 요소를 네이티브로 화면에 렌더링하는 VSCode 대체 후보를 찾아야 합니다:

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

<table> 태그를 Markdown 형식으로 바꿔보세요.

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

크로스 플랫폼 네이티브 코드 편집기 중에서 VSCode의 진정한 경쟁 상대가 될 잠재력을 갖고 있는 것은 무엇인가요?

# LiteXL: VSCode의 진정한 경쟁 상대

몇 년 전에, 저는 컴퓨터에서 VSCode가 잘 작동하지 않아서 원래의 VSCode 대안을 찾고 있었습니다. 대부분의 개발자들처럼, 저 또한 저렴한 컴퓨터를 탓하며 VSCode와 같은 하이브리드 앱의 성능 문제를 숨기기 위해 하드웨어 구성 요소를 업그레이드할 수 있었지만, 대신에 VSCode만큼 성장 잠재력이 있는 네이티브 코드 편집기를 찾기 시작했습니다.

GitHub에서 Lite라는 흥미로운 네이티브 코드 편집기를 발견했습니다. Lite는 구문 강조, 빠른 명령, 파일 트리 뷰, 다중 탭 등 기본 코드 편집 기능을 갖춘 최소한의 현대적인 코드 편집기입니다.

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


![VSCodesTrueCompetitorisComingLiteXL_3.png](/assets/img/2024-06-27-VSCodesTrueCompetitorisComingLiteXL_3.png)

Lite의 코드베이스는 네이티브 성능 및 개발자 친화적인 확장성을 신중히 고려하여 잘 설계되었습니다. Lite는 C 및 Lua로 작성되었습니다. 낮은 수준의 렌더링에는 C를 사용하고 코드 편집기 및 플러그인 구현에는 내장 경량 Lua 런타임을 사용합니다. Lite의 목표는 누구나 확장할 수 있는 최소한의 편집기를 제공하는 것이었습니다.

![VSCodesTrueCompetitorisComingLiteXL_4.png](/assets/img/2024-06-27-VSCodesTrueCompetitorisComingLiteXL_4.png)

Lite는 완료된 프로젝트이지만, VSCode와 경쟁하기 위한 일부 기능이 누락되어 있어 기여자들이 확장된 기능을 갖춘 저장소 포크를 만들기 시작했습니다. LiteXL은 Lite 오픈 소스 프로젝트의 분기점(fork)입니다. LiteXL은 원본 Lite 프로젝트와 비교하여 다양한 성능 및 사용성 개선 사항이 있습니다.


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

LiteXL은 세 개의 층으로 구현된 잘 설계된 아키텍처 패턴을 따릅니다:

- Embedder: 컴퓨터 공학 분야 전체를 지원하는 C 언어로 작성되었습니다. 이 층은 내장된 Lua 인터프리터 및 SDL 그래픽 라이브러리 기반의 렌더링 API로 구성되어 있습니다.
- Core: Lua를 사용하여 편집 영역, 플러그인 시스템 및 기타 코어 구성 요소를 임베더 층 위에 구현합니다.
- 플러그인: 구문 강조, 테마, 트리 뷰, 미니맵 (스크롤 가능한 작은 코드 미리보기) 및 자동 완성과 같은 확장된 기능을 통합합니다.

코어 편집기 설정을 조정하고 플러그인을 사용하여 LiteXL을 VSCode와 유사하지만 네이티브 편집기 앱으로 변환할 수 있습니다.

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

LiteXL이 내가 사용하는 1위 네이티브 VSCode 대체 앱이 된 이유를 공유해볼게요:

- VSCode는 구문 강조를 사용하여 편집기를 열 때 6초가 걸리고, 물리 메모리를 550MB 이상 사용하며 스크롤 중 CPU 사용량이 70%까지 올라가고, 디스크 공간을 500MB 이상 차지하며, 여러 편집기 인스턴스를 열 경우 컴퓨터 전체가 느려집니다.
- LiteXL은 구문 강조가 적용된 편집기를 0.5초 미만으로 빠르게 열어주며, 물리 메모리를 단 35MB만 사용하고, CPU 사용량은 최대 20%까지만 올라가며, 디스크 공간은 5.6MB 정도밖에 차지하지 않으며, 여러 편집기 인스턴스를 매끄럽게 열 수 있습니다.

LiteXL은 VSCode와 똑같은 모습이며, 내가 사용했던 모든 VSCode 기능을 효율적인 자원 사용으로 제공하죠!

LiteXL은 지속적으로 유지보수되며, 모든 릴리스마다 인상적인 기능을 제공하면서 성능과 자원 사용량에 영향을 미치지 않죠. VSCode와 달리 LiteXL은 성능을 높이기 위해 원치 않는 기능을 제거할 수 있어요. Lite는 GUI 설정 패널을 제공하지 않았지만, LiteXL은 Lua와 LiteXL 렌더링 API 위에 구축된 사용자 정의 GUI 라이브러리로 VSCode와 비슷한 설정 패널을 구현하였습니다.

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


![LiteXL](https://miro.medium.com/v2/resize:fit:1400/1*ZgJ1WheQTnMz6Zj_Zjni_Q.gif)

LiteXL는 검증된 성능과 Lua 기반의 간단한 플러그인 개발을 장려하여 누구나 사용하고 개발자 확장 프로그램을 만들 수 있도록 도와줍니다. LiteXL 팀은 심지어 VSCode Marketplace와 같은 아이디어를 전달하는 플러그인 관리자를 소개했습니다.

# LiteXL vs. VSCode: 미래

VSCode는 마이크로소프트 제품으로, 많은 소프트웨어 개발 회사들이 직원들에게 권장합니다. 대부분의 회사들은 도메인별 활동을 개선하기 위해 자신들을 위한 VSCode 확장을 개발합니다. VSCode Marketplace를 통해 프로그래머들은 어떤 인기 있는 기술 스택 제품이라도 생산적으로 작업할 수 있습니다.


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

Only a few programmers understand the performance issues that they have to face with VSCode. 대부분의 개발자는 컴퓨터의 계산 능력으로 인해 VSCode를 네이티브 앱으로 사용할 수 있습니다. 그러나 사실은 우리가 구문 강조와 언어 린팅이 필요한 소스 파일을 편집하는 데 그만큼의 계산 능력이 필요하지 않다는 것이죠.

LiteXL은 GNU/Linux 시스템을 선택하는 프로그래머들과 같이 프로그래밍에 사용하는 도구의 성능에 진정으로 신경 쓰는 프로그래머들을 유치합니다.

LiteXL은 VSCode와 경쟁하여 웹 기반 요소를 사용하지 않고도 저사양 컴퓨터에서도 원활하게 실행되는 완전한 기능을 갖춘 VSCode와 유사한 크로스 플랫폼 편집기를 제공합니다. 아직 공식 LiteXL 웹 버전은 없지만, Emscripten을 사용하여 WebAssembly로 소스를 컴파일하여 웹 버전을 만들 수도 있습니다. 현재 LiteXL의 상태를 성숙한 VSCode와 비교할 수 없지만, 그 미래는 희망적이며, VSCode의 진정한 경쟁 상대가 되고 있습니다!

# 결론

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

개발자 그룹이 놀랍도록 빠르고 안전한 운영 체제를 만들었는데, 이 운영 체제는 이전에 만들어진 모든 운영 체제보다 뛰어나게 작동합니다. 사용자들이 즉시 새로 만들어진 운영 체제로 전환하여 기존 즐겨 사용하는 플랫폼을 버릴 것이라고 생각하십니까? 아니요, 시간이 필요합니다. 더 많은 개발자들이 LiteXL을 시도하고 그 빠른 성능과 간소하면서 확장 가능한 디자인을 경험할 것입니다. 그들이 자신의 CPU 사이클과 메모리를 VSCode로 낭비했음을 깨달았을 때, 그들은 LiteXL에 대해 더 생각할 것입니다 — 언젠가는 LiteXL을 사용하기 시작할지도 모릅니다.

LiteXL은 매일 더 생산적인 코드 편집기로 성장하고 있으며, VSCode가 제공하는 기능을 가지고 실제로 VSCode의 진정한 경쟁 상대가 되고 있습니다.

읽어 주셔서 감사합니다.