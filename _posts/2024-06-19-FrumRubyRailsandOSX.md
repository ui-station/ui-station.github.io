---
title: "프럼, 루비, 레일즈, 그리고 OS X"
description: ""
coverImage: "/assets/img/2024-06-19-FrumRubyRailsandOSX_0.png"
date: 2024-06-19 10:21
ogImage: 
  url: /assets/img/2024-06-19-FrumRubyRailsandOSX_0.png
tag: Tech
originalTitle: "Frum, Ruby, Rails and OS X"
link: "https://medium.com/@spaquet/frum-ruby-rails-and-os-x-54acd94bccb3"
---


## OpenSSL 3를 찾아서

몇 년 전에 러스트로 작성된 루비 버전 관리자인 Frum을 발견했습니다.

빠르고 긍정적인 면에서는 실용적입니다. 필요 없는 기능은 없으면서 잘 유지되고 있습니다.

그래서 Apple 실리콘 기반 OS X Sonoma에서 내 설정을 업데이트한 것을 보여드릴게요.

<div class="content-ad"></div>

- 홈브루를 설치해주세요. 이미 설치되어 있지 않다면, 아래 좋은 설치 가이드를 읽어보세요
- 레일즈 설정에 유용한 몇 가지 라이브러리를 설치해주세요: brew install readline libyaml openssl nvm
- 노드 설치하기 nvm install 22 (노드 버전 22가 최신 버전이었지만, 프로젝트에 필요한 버전으로 맞춰서 선택할 수 있어요)
- 다음 명령어 실행하기 frum install 3.3.3 --with-openssl-dir=$(brew --prefix openssl@3) --with-libyaml-dir=$(brew --prefix libyaml)
- .zprofile 또는 .zshrc에 다음 추가하기: eval “$(frum init)”

그리고 와! 최신 openssl을 활용한 작동 중인 루비 3.3(.3)를 사용하게 되었어요.

SSL 관련 문제가 있으면 안 되니까, 제 설정에는 버전 1로 다운그레이드하는 것은 선택지가 아니었어요.

마지막으로, 이제 gem install rails을 실행하여 레일즈를 설치할 수 있어요.