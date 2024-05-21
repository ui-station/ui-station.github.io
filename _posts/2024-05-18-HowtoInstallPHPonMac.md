---
title: "맥에 PHP 설치하는 방법"
description: ""
coverImage: "/assets/img/2024-05-18-HowtoInstallPHPonMac_0.png"
date: 2024-05-18 17:39
ogImage: 
  url: /assets/img/2024-05-18-HowtoInstallPHPonMac_0.png
tag: Tech
originalTitle: "How to Install PHP on Mac"
link: "https://medium.com/@rodolfovmartins/how-to-install-php-on-mac-6795ce469802"
---



![2024-05-18-HowtoInstallPHPonMac_0](/assets/img/2024-05-18-HowtoInstallPHPonMac_0.png)

PHP은 웹 애플리케이션을 개발하는 데 사용되는 인기있는 프로그래밍 언어입니다. Mac을 사용하고 PHP를 사용하여 웹 애플리케이션을 개발하려면 Mac에 PHP를 설치해야 합니다. 이 기사에서는 Mac에 PHP를 설치하는 과정을 단계별로 안내해 드리겠습니다.

## 단계 1: Homebrew 설치

Homebrew는 macOS용 패키지 관리자로, 소프트웨어 패키지를 쉽게 설치하고 관리할 수 있게 해줍니다. Homebrew를 설치하려면 Mac의 터미널 앱을 열고 다음 명령을 실행하십시오:


<div class="content-ad"></div>

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

이 명령은 Mac에 Homebrew를 다운로드하고 설치합니다.

## 단계 2: PHP 설치

Homebrew를 설치하면 PHP를 설치하는 데 사용할 수 있습니다. 터미널 앱에서 다음 명령을 실행하세요:

<div class="content-ad"></div>


```js
brew install php
```

이 명령은 Mac에 PHP의 최신 버전을 설치할 것입니다.

## 단계 3: PHP 버전 확인

PHP가 올바르게 설치되었는지 확인하려면 기기에 설치된 PHP 버전을 확인할 수 있습니다. 터미널 앱에서 다음 몤령을 실행하세요:
```

<div class="content-ad"></div>

```js
php -v
```

이 명령어를 실행하면 Mac에 설치된 PHP 버전이 표시됩니다.

## 단계 4: PHP 구성

PHP를 설치한 후에는 필요에 맞게 구성할 수 있습니다. 이를 위해 php.ini 파일을 수정하여 구성할 수 있습니다. php.ini 파일을 찾으려면 터미널 앱에서 다음 명령어를 실행하십시오:

<div class="content-ad"></div>

```js
php --ini
```

이 명령어를 실행하면 php.ini 파일의 위치가 표시됩니다. 그런 다음 텍스트 편집기를 사용하여 파일을 열고 필요한 변경 사항을 적용할 수 있습니다.

## 단계 5: PHP 서버 시작

터미널 앱에서 다음 명령어를 실행하여 PHP 서버를 시작할 수 있습니다:

<div class="content-ad"></div>

```js
php -S localhost:8000
```

이 명령어는 포트 8000에서 PHP 서버를 시작합니다. 그런 다음 웹 브라우저를 열고 http://localhost:8000에 가서 PHP 애플리케이션을 볼 수 있습니다.

축하합니다! Mac에 PHP를 성공적으로 설치했습니다. 이제 PHP를 사용하여 웹 애플리케이션을 개발할 수 있습니다.