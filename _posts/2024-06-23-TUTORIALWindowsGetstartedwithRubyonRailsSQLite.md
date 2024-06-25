---
title: "튜토리얼 Windows Ruby on Rails  SQLite 시작하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-TUTORIALWindowsGetstartedwithRubyonRailsSQLite_0.png"
date: 2024-06-23 20:51
ogImage:
  url: /assets/img/2024-06-23-TUTORIALWindowsGetstartedwithRubyonRailsSQLite_0.png
tag: Tech
originalTitle: "TUTORIAL (Windows)— Get started with Ruby on Rails + SQLite"
link: "https://medium.com/@mclaramarinho/tutorial-windows-get-started-with-ruby-on-rails-sqlite-2f85ff8721b0"
---

안녕하세요! 🙂 이 직선적인 튜토리얼에서는 루비 온 레일즈 + sqlite3 설치 방법을 안내해 드릴 거에요.

## 루비 설치하기

- 이 URL에서 루비 + Devkit 설치 프로그램을 다운로드하세요. 여기서는 선호하는 버전을 선택할 수 있어요. 이 튜토리얼을 작성한 날짜를 기준으로 최신 안정화 버전은 이 URL에서 사용 가능해요.
- 설치 프로그램을 실행하세요. 모든 옵션을 선택하고 계속 다음을 눌러 설치를 진행하세요.
- 설치가 완료되면 터미널이 열리고 MSYS2 설치에 대해 묻습니다. 그냥 Enter 키를 누르면 기본 설정으로 설치됩니다.
- MSYS2 설치가 완료되면 다시 Enter 키를 눌러 터미널을 닫으세요.

## 루비젬 설치하기

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

- RubyGems(루비의 패키지 관리 프레임워크)를 이 URL에서 다운로드하세요(ZIP 형식 파일 받기). 이 튜토리얼 작성 시점을 기준으로, 최신 안정 버전이 이 URL에서 제공됩니다.
- 방금 다운로드한 파일을 압축 해제하고 해당 폴더에서 터미널을 열어주세요.
- 다음 명령어를 입력하세요: ruby setup.rb.
- 작업이 완료되면 터미널을 닫고 다른 터미널을 열어주세요.

## RAILS 설치하기

- 다음 명령어를 입력하세요: gem install -V rails. 이 명령은 호환되는 최신 버전의 Rails를 설치합니다.
- 작업이 완료되면, Rails가 설치되어 있어야 합니다. Rails 버전을 확인하려면 rails -v 명령을 실행하세요. 오류가 발생하면 설치가 올바르게 이루어지지 않았다는 것을 의미합니다.

## BUNDLER 설치하기

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

- 이제 'gem install -V bundler' 명령을 실행해주세요. Bundler는 필요한 정확한 젬 및 버전을 추적하고 설치하여 Ruby 프로젝트에 일관된 환경을 제공합니다.

## SQLITE3 설치

- 마지막 단계는 'gem install -V sqlite3'을 실행하는 것입니다.

## PSYCH 설치

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

- gem install psych 명령어를 실행해주세요. --platform=ruby를 꼭 붙여주셔야 합니다.
- 다음 단계로 넘어가시기 전에 설치가 완료될 때까지 기다려주세요.

## 첫 번째 프로젝트 만들기

- 첫 번째 레일즈 프로젝트를 만들려면 프로젝트를 저장할 위치로 이동하세요. 예: cd desktop.
- NameOfProject로 레일즈 프로젝트를 생성하기 위해 rails new NameOfProject를 입력하세요. 이 명령어는 많은 파일을 생성하고 프로젝트의 종속성을 설치합니다.

잘 동작하기를 바래요 :)
