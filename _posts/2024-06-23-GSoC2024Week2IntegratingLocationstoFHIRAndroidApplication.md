---
title: "GSoC 2024 두 번째 주  FHIR 안드로이드 애플리케이션에 위치 통합하기"
description: ""
coverImage: "/assets/img/2024-06-23-GSoC2024Week2IntegratingLocationstoFHIRAndroidApplication_0.png"
date: 2024-06-23 20:58
ogImage: 
  url: /assets/img/2024-06-23-GSoC2024Week2IntegratingLocationstoFHIRAndroidApplication_0.png
tag: Tech
originalTitle: "GSoC 2024: Week 2 | Integrating Locations to FHIR Android Application"
link: "https://medium.com/@panchalparth_91743/gsoc-2024-week-2-integrating-locations-to-fhir-android-application-f300ef3b1622"
---


안녕하세요 여러분! 👋

모두가 행복한 한 주를 보내고 계시기를 바랍니다! 🌈 이번주는 저희 어플리케이션에 위치 기능을 통합하는 작업을 진행하면서 모험 가득한 한 주였어요. 이제 사용자들은 지정된 위치를 동기화하고 특정 위치를 선택하며 선택한 위치에 환자를 등록할 수 있어요. 자세한 내용을 알아보도록 하겠습니다! 🚀

<img src="/assets/img/2024-06-23-GSoC2024Week2IntegratingLocationstoFHIRAndroidApplication_0.png" />

# 🌍 안드로이드 FHIR 어플리케이션에 위치 동기화하기

<div class="content-ad"></div>

안녕하세요! 안드로이드 FHIR SDK의 엔진 라이브러리를 사용하여 FHIR 리소스를 로컬에 저장하고 서버와 동기화하고 있어요. OpenMRS는 위치 데이터를 FHIR 리소스로 저장하며, Download Work Manager의 구현에 URL 경로를 추가하기만 하면 됐어요.

🔗 OpenMRS 위치 URL: Location?_summary=data&_tag=Login+Location

나중에 한 번의 커밋에서 위치 URL을 첫 번째 인덱스로 이동했어요. 이 변경으로 위치가 먼저 로드되어 사용자가 환자 및 만남 데이터가 동기화되는 동안 선택할 수 있도록 되었어요. 효율성이 승리했네요! 🏆 이 변경 사항은 여기 커밋에서 확인할 수 있어요.

# 📱 위치 선택 화면 만들기

<div class="content-ad"></div>

먼저, 홈 화면에서 선택 위치 화면으로 이동할 수 있는 옵션을 추가했어요. 🏠

![이미지](/assets/img/2024-06-23-GSoC2024Week2IntegratingLocationstoFHIRAndroidApplication_1.png)

선택 위치 화면은 현재 위치 세부 정보를 CardView에 보여주며, 드롭다운 메뉴를 통해 위치를 선택할 수 있도록 멋진 디자인으로 구성되어 있어요. 위치를 선택하면 현재 위치 세부 정보가 업데이트됩니다.

이 세부 사항들은 앱 전체에서 쉽게 액세스하기 위해 데이터 저장소 환경 설정에 저장돼요. Location.id와 Location.name 두 가지가 모두 필요에 맞게 저장돼요.

<div class="content-ad"></div>

처음에는 간단한 드롭다운을 계획했었지만, 멋진 멘토들의 통찰력 덕분에 사용자들이 수백 개의 위치를 가질 수 있다는 것을 깨달았어요. 그래서 최대한 편리하도록 드롭다운을 필터링 가능하게 만들었어요! 🛠️

📝 이 변경 사항을 위한 커밋:

- LocationViewModel 생성
- 위치 POC의 초기 통합.

# 🧭 앱 전반에 걸쳐 현재 위치 정보 통합하기

<div class="content-ad"></div>

우리 멘토들의 소중한 안내 덕분에, 환자 등록 화면의 사이드 패널과 맨 위에 위치 정보를 추가했어요. 이 정보를 여러 곳에서 확인할 수 있으니 사용자들에게 정말 편리하고 저장된 데이터를 재확인할 수 있게 도와줘요. 🔍

이제 사용자가 위치를 선택하기 전까지는 환자를 추가하는 네비게이션이 차단되어요. 또한 등록된 환자의 FHIR 환자 자원에는 사용자가 선택한 위치 자원이 포함될 거예요. 🎯

📝 이 변경 사항에 대한 커밋 내역:

- 등록된 새 환자를 차단하는 로직을 통합함
- 위치 화면에 현재 위치를 추가함
- (기능) 환자 추가 화면에 현재 위치 추가함
- (기능) 첫 설정: 환자 자원 저장 시 현재 위치를 추가함
- (기능) 첫 설정: 사이드 패널에 현재 위치 추가함.

<div class="content-ad"></div>

# 🔮 다음 주 계획

다음 주 일정은 다음과 같습니다:

- 🧪 서버와 동기화하여 위치 데이터 저장 로직을 구현하고 유효성을 검사합니다.
- ✨ MVP 1의 구현을 정리하고 MVP 2의 구현을 시작합니다.

함께 해 주셔서 정말 감사합니다! 여러분의 지원은 저에게 큰 힘이 됩니다. 더 많은 업데이트를 기대해 주세요. 코딩을 즐기세요! 🎉👩‍💻👨‍💻 Frederic Deniger, Jose Francisco, Pedro Sousa, ICRC 팀 및 OpenMRS 팀께 소중한 지도를 해 준 데에 박수를 보냅니다.

<div class="content-ad"></div>

읽어 주셔서 감사합니다! 언제든지 건강 관련, 풀 스택 개발, FHIR, OpenMRS 안드로이드, Open Health Stack, 노래 추천 등과 관련된 모든 것에 대해 저에게 연락하실 수 있습니다! 🎵

LinkedIn이나 OpenMRS Talk에서 저를 찾아보고 GitHub에서 제 프로젝트를 확인해보세요.