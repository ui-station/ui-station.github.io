---
title: "플러터 FVM을 Windows 및 MacOS에서 사용하기"
description: ""
coverImage: "/assets/img/2024-06-19-UsingFlutterFVMinWindowsMacOS_0.png"
date: 2024-06-19 11:17
ogImage: 
  url: /assets/img/2024-06-19-UsingFlutterFVMinWindowsMacOS_0.png
tag: Tech
originalTitle: "Using Flutter FVM in Windows , MacOS"
link: "https://medium.com/@iliyass.zamouri/using-flutter-fvm-in-windows-1c23e38bccdb"
---


<img src="/assets/img/2024-06-19-UsingFlutterFVMinWindowsMacOS_0.png" />

Windows에서:

FVM을 진행하기 전에 먼저 Flutter를 설치해야 합니다. 다음은 설치 방법입니다:

단계 1: Flutter 설치하기:

<div class="content-ad"></div>

- Flutter 웹 사이트(https://flutter.dev/)에 방문하고 “시작하기” 버튼을 클릭하세요.
- “Windows” 버튼을 클릭하여 Windows용 Flutter SDK를 다운로드하세요.
- 다운로드한 Flutter SDK ZIP 파일을 원하는 위치에 압축 해제하세요. 예를 들어, “C:\flutter”에 압축을 해제할 수 있습니다.
- 시스템의 PATH 변수에 Flutter SDK를 추가하세요. 시작 메뉴를 열고 “환경 변수”를 검색하세요. “시스템 환경 변수 편집” 옵션을 선택하세요.
- "시스템 속성" 창에서 "환경 변수" 버튼을 클릭하세요.
- "시스템 변수" 섹션에서 "Path" 변수를 찾고, 선택한 후 “편집” 버튼을 클릭하세요.
- “새로 만들기” 버튼을 클릭하고 Flutter SDK 폴더 내의 “bin” 디렉토리 경로를 추가하세요 (예: “C:\flutter\bin”).
- 변경 사항을 저장하려면 모든 열린 창에서 “확인”을 클릭하세요.
- 새로운 명령 프롬프트 또는 PowerShell 창을 열고 “flutter doctor”를 입력하여 설치를 확인하세요. 이 명령은 검사를 수행하고 Flutter 설치 상태를 표시합니다.

단계 2: Flutter 버전 관리 (FVM) 설치:

이제 Flutter가 설치되었으므로, FVM을 설정하여 서로 다른 Flutter 버전을 효율적으로 관리해 봅시다.

- 명령 프롬프트 또는 PowerShell 창을 열고 다음 명령을 실행하여 Chocolatey를 설치하세요.

<div class="content-ad"></div>

```js
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))   
```

2. Install FVM using Chocolatey in the same PowerShell session:

```js
choco install fvm
```

3. Make FVM usable globally.

<div class="content-ad"></div>



4. Configure VSCode to use the current active Flutter SDK version of a project. Add these lines in the Preferences Settings.


C:\Users\[current_user]\AppData\Roaming\Code\User\settings.json



"dart.flutterSdkPath": ".fvm/flutter_sdk",
"dart.flutterSdkPaths": ["/Users/usr/fvm/versions"],
// Remove .fvm files from search
"search.exclude": {
    "**/.fvm": true
},
// Remove from file watching
"files.watcherExclude": {
    "**/.fvm": true
}


<div class="content-ad"></div>

맥OS에서:

단계 1: Flutter 설치하기:

- 공식 Flutter 웹사이트(https://flutter.dev)에서 Flutter SDK를 다운로드합니다.
- 다운로드한 파일을 원하는 디렉토리에 압축을 푸세요. 예를 들어, /Users/your_username/flutter에 압축을 푸는 것이 좋습니다.

- Flutter 환경 변수 설정하기:

<div class="content-ad"></div>

- 터미널을 열고 다음 명령을 실행하세요: open ~/.bash_profile.
- 파일 끝에 다음 라인을 추가하세요:

```js
export PATH="$PATH:/Users/your_username/flutter/bin"
export PATH="$PATH:/Users/your_username/.pub-cache/bin"
```

- your_username을 실제 macOS 사용자 이름으로 바꾸세요.
- .bash_profile 파일을 저장하고 닫으세요.
- 변경 사항을 적용하려면 터미널에서 다음 명령을 실행하세요: source ~/.bash_profile.

2단계: Flutter Version Management (FVM) 설치

<div class="content-ad"></div>

- FVM 설치하기:

    - 터미널을 열고 다음 명령을 실행하세요: brew install fvm.
    - 설치가 완료되면 fvm doctor를 실행하여 FVM이 올바르게 설치되었는지 확인하세요.
  
- FVM 환경 변수 설정하기:

    - 터미널을 열고 다음 명령을 실행하세요: open ~/.bash_profile.
    - 파일 끝에 다음 줄을 추가하세요.

<div class="content-ad"></div>

```bash
export FVM_HOME=~/.fvm 
export PATH="$PATH:$FVM_HOME/default/bin"
```

- .bash_profile 파일을 저장하고 닫으세요.
- 터미널에서 다음 명령어를 실행하여 변경사항을 적용하세요: source ~/.bash_profile

FVM 설정 및 Flutter 버전 관리: FVM를 설치했으므로 이제 손쉽게 Flutter 버전을 설정하고 관리할 수 있습니다.

- Flutter 프로젝트를 저장할 새 디렉토리를 만드세요. 예를 들어, 사용자 폴더에 "Projects" 디렉토리를 생성할 수 있습니다.
- 명령 프롬프트나 PowerShell 창을 열고 프로젝트를 생성할 디렉토리로 이동하세요.
- FVM를 초기화하기 위해 다음 명령어를 실행하세요:

<div class="content-ad"></div>

```js
fvm use stable
```

- 이 명령어는 안정 버전의 Flutter을 다운로드하고 설정합니다.
- FVM을 사용하여 다른 버전의 Flutter을 설치하려면 다음 명령을 실행하고 `version`을 원하는 버전으로 바꿉니다. 예시: 2.5.3

```js
fvm install <version>
```

- 프로세스가 완료되면 다음 명령을 사용하여 새로운 Flutter 프로젝트를 생성할 수 있습니다:

<div class="content-ad"></div>

```js
fvm flutter create my_flutter_project
```

- 원하는 프로젝트 이름으로 "my_flutter_project"를 대체하세요.
- Flutter 버전을 변경하려면 다음 명령을 사용할 수 있습니다:

```js
fvm use <version>
```

- "version"을 원하는 Flutter 버전으로 대체하세요. "stable" 또는 "2.5.0"과 같은 것이 될 수 있습니다.

<div class="content-ad"></div>

결론: Windows에서 Flutter Version Management (FVM)을 사용하여 Flutter를 설치하면 여러 개의 Flutter 버전을 관리할 수 있습니다.