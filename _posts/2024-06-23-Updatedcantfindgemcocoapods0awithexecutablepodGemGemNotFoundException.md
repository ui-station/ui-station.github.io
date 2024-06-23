---
title: "업데이트 실행 가능한 pod로 gem cocoapods  0a를 찾을 수 없는 경우 해결 방법 GemGemNotFoundException"
description: ""
coverImage: "/assets/img/2024-06-23-Updatedcantfindgemcocoapods0awithexecutablepodGemGemNotFoundException_0.png"
date: 2024-06-23 20:45
ogImage: 
  url: /assets/img/2024-06-23-Updatedcantfindgemcocoapods0awithexecutablepodGemGemNotFoundException_0.png
tag: Tech
originalTitle: "Updated : can’t find gem cocoapods (>= 0.a) with executable pod (Gem::GemNotFoundException)"
link: "https://medium.com/@hemanthkollanur/cant-find-gem-cocoapods-0-a-with-executable-pod-gem-gemnotfoundexception-5389f63dc8ad"
---


이는 CocoaPods가 시스템에 설치되어 있지 않거나 PATH 변수에 설치되어 있지만 실행 파일인 'pod'를 찾을 수 없음을 의미합니다.

![이미지](/assets/img/2024-06-23-Updatedcantfindgemcocoapods0awithexecutablepodGemGemNotFoundException_0.png)

문제:

```js
.rvm/rubies/ruby-2.7.6/lib/ruby/site_ruby/2.7.0/rubygems.rb:264:in `find_spec_for_exe': can't find gem cocoapods (>= 0.a) with executable pod (Gem::GemNotFoundException)
```

<div class="content-ad"></div>

해결책:

```javascript
// cocoapod이 설치되어 있는지 확인하세요. 아니라면
sudo gem install cocoapods

gem pristine --all

// gem 실행 시 권한 오류가 발생한다면 (Errno::EACCES), 슈퍼 사용자를 사용하세요
```

```javascript
bundle i 또는 bundle install
```

2024년 2월 업데이트:

<div class="content-ad"></div>

```js
/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/rubygems.rb:283:in `find_spec_for_exe': 실행 가능한 'pod'로 cocoapods (>= 0.a) 젬을 찾을 수 없습니다 (Gem::GemNotFoundException)
 from /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/rubygems.rb:302:in `activate_bin_path'
 from /usr/local/bin/pod:23:in `<main>'
```

# 알 수 없는 루비 인터프리터 버전 (어떻게 처리할지 모름): `=2.6.10.

```js
nano ~/.bash_profile
source ~/.bash_profile
# bash_profile에 아래 경로 추가
```

```js
# .bash_profile에 RVM이 가장 마지막에 있지 않으면 오류가 발생할 수 있음
export PATH="$PATH:$HOME/.rvm/bin" # Ruby 버전을 관리하기 위해 RVM을 스크립트에 추가
export PATH="$GEM_HOME/bin:$PATH"
[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm"  # RVM을 셸 세션에 함수로 로드
```