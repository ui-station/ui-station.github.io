---
title: "SwiftUI 아키텍처 완벽 해부 1부"
description: ""
coverImage: "/assets/img/2024-06-30-DemystifyingSwiftUIArchitecturePartI_0.png"
date: 2024-06-30 19:31
ogImage: 
  url: /assets/img/2024-06-30-DemystifyingSwiftUIArchitecturePartI_0.png
tag: Tech
originalTitle: "Demystifying SwiftUI Architecture (Part I)"
link: "https://medium.com/@tammy.ho52/demystifying-swiftui-architecture-part-i-d6c36997ac4b"
---


SwiftUI를 처음 공부하기 시작했을 때, 처음 모바일 앱을 구조화하는 데 많은 시간을 소비했어요. 이 질문의 핵심은 관심사 분리와 모듈화에 있어요. 여기서, 나는 여러분이 시간, 에너지, 그리고 뇌 자원을 절약하는 데 도움이 될 것을 바라면서 제 발견을 소개할 거예요. 바로 시작해 볼까요?

참고: 초보자부터 중급자용 복잡도 애플리케이션을 고려했어요. 내가 제공하는 포괄적인 개요와 강조된 미묘한 점들을 통해, 여러분은 더 관심 있는 구성 요소를 자세히 연구할 수 있겠죠.

첫 번째 단계는 아키텍처 패턴을 결정하는 것이에요. MVVM (Model-View-View Model)은 제 추천이지만, 여러분의 프로젝트에 맞지 않는다면 다른 패턴을 선택하시길 권장해요. MVVM 아키텍처 패턴이 수업 및 실제 앱에서 널리 사용되기 때문에, MVVM 아키텍처를 구현하는 방법에 대해 다룬 자료가 풍부하게 있어요. 다른 가능한 아키텍처 패턴으로는 MVC, VIPER, RIBs가 있어요.

오늘은 서비스 레이어를 갖춘 MVVM 아키텍처 패턴에 대해 이야기할 거예요.

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

- 모델: 애플리케이션 뒤의 데이터 및 비즈니스 로직 기능을 나타냅니다. 모델은 일반적으로 클래스와/또는 구조체로 구성됩니다. 클래스 대신 구조체를 사용해야 하는지의 주된 차이점은 앱이 같은 객체를 전달하는지 (클래스) 또는 속성의 복사본을 사용하는지 (구조체)에 달려 있습니다. 클래스의 속성이 필요하지 않다면 구조체가 선호되는 데이터 구조입니다.

- 뷰: 사용자에게 정보/그래픽을 표시하고 사용자로부터 입력을 받는 사용자 인터페이스입니다. 뷰는 애플리케이션의 데이터와 사용자 간의 양방향 통신 채널로 작동합니다.

주요 뷰 구성 요소는 다음과 같습니다:
- 컨테이너 (예: NavigationStack, List, Form, ScrollView, Group, Grid, TabView): 뷰 구성 요소를 함께 조합합니다
- 스택 (예: HStack, VStack, ZStack): 구조화된 스택 뷰 내에서 컨트롤 및 디스플레이를 배치합니다
- 컨트롤 (예: Button, Picker, TextField, Toggle, Slider): 사용자 입력을 수락합니다
- 디스플레이 (예: Text, Image, Label): 사용자에게 정보를 표시합니다
- 레이아웃 도구 (예: Spacer, Divider, GeometryReader): 더 정확하게 뷰 구성 요소를 배치하여 원하는 뷰를 달성합니다
- 팝업 (예: Alert, Sheet): 현재 네비게이션 흐름에서 벗어나고 사용자에게 추가 뷰/정보를 표시합니다

- 뷰 모델: 모델의 데이터와 뷰의 사용자 인터페이스 사이의 중개자 역할을 합니다. 뷰 모델은 Observable Object 프로토콜 (@ObservedObject / @StateObject)을 통해 애플리케이션의 상태를 관리하며, iOS 17부터 Observable Macro도 시작했습니다. Observable Object 프로토콜은 뷰 모델의 @Published 속성이 변경 사항을 뷰로 게시할 수 있게 합니다. 결과적으로 뷰는 관찰된 변경 사항에 기반하여 다시 그려집니다. 뷰 모델에는 또한 모델 데이터를 원하는 뷰 형식으로 변환하는 데이터 변환 함수와 서비스 계층과의 상호 작용을 관리하는 기능이 포함되어 있습니다.

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

— 서비스 레이어: MVVM 아키텍처 패턴 외부의 데이터 작업을 처리합니다. 데이터를 검색하거나 저장하고 응용 프로그램에 제3자 서비스/소프트웨어 패키지를 통합하는 등의 작업을 다루며, 이 내용은 이 게시물에서 다루지 않습니다.

- API / URL을 통한 데이터 검색: 모바일 장치는 주로 저장 공간 한정과 보안 기능 때문에 외부 서버에 데이터를 저장합니다. 앱은 API / URL 호출을 통해 외부 데이터를 검색하며, 높은 오류 처리 능력을 갖추고 있습니다 (앱이 치명적 오류로 인해 충돌하는 것은 피해야 합니다). 데이터를 검색한 후, 이를 인코딩/디코딩하여 네트워크와 네이티브 뷰 모델 친화적인 데이터 형식으로 변환합니다.

- 로컬 (UserDefaults) 및 외부 (Apple의 CoreData, Apple의 CloudKit, 제3자) 솔루션을 통한 데이터 유지: 사용자가 앱을 사용할 때, 앱 데이터가 세션 간에 유지되기를 기대합니다. UserDefaults는 사용자 환경설정 및 앱 설정을 저장하는 간단한 솔루션입니다. 데이터 유지 프레임워크/솔루션은 큰 데이터 세트를 저장하기 위해 필요합니다. CoreData는 Apple의 기본 데이터 유지 프레임워크로, 기본적으로 SQLite를 사용합니다. CoreData는 학습 곡선이 있지만 오프라인 기능, 캐싱, 고급 정렬/쿼리와 같은 내장 기능을 제공합니다. 다른 주목할 만한 솔루션으로는 Apple의 CloudKit이 있으며, Firebase와 Realm과 같은 제3자 솔루션이 있습니다.

요약하자면, 모바일 앱 아키텍처는 실제로 관심사 분리 (MVVM + 서비스)와 각 레이어 내에서의 모듈화로 구축됩니다. 이 글이 유익했기를 바라며, 다음 시리즈도 기대해주시기 바랍니다!