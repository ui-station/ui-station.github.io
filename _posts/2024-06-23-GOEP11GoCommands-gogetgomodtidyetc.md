---
title: "Go EP11 Go 명령어 - go get, go mod tidy 등 완벽 가이드"
description: ""
coverImage: "/assets/img/2024-06-23-GOEP11GoCommands-gogetgomodtidyetc_0.png"
date: 2024-06-23 21:10
ogImage: 
  url: /assets/img/2024-06-23-GOEP11GoCommands-gogetgomodtidyetc_0.png
tag: Tech
originalTitle: "GO EP11: Go Commands - go get, go mod tidy, etc"
link: "https://medium.com/gitconnected/getting-cozy-with-go-commands-go-get-go-mod-tidy-etc-7438cf199460"
---


<img src="/assets/img/2024-06-23-GOEP11GoCommands-gogetgomodtidyetc_0.png" />

안녕하세요! 이번 토론은 특히 go.mod 파일을 중심으로 Go 모듈에 관한 내용을 다룹니다. 또한 두 모듈이 같은 종속성을 가지고 있지만 다른 버전을 사용할 때 Go가 어떻게 종속성을 해결하는지에 대해서도 살펴봅니다.

이전 섹션의 내용을 이어서, go install, go mod와 go get, go install 사이의 차이 등 다른 Go 명령에 대해서도 이야기합니다.

만약 GOROOT, GOPATH, GOCACHE와 같은 환경 변수 뿐만 아니라 빌드 이미지 파이프라인을 가속화하는 방법과 같은 기초적인 Go 환경에 관한 이전 섹션을 읽지 않았다면, 아래 링크를 확인해보세요: GO EP10: GOROOT, GOPATH, GOCACHE

<div class="content-ad"></div>

Go 1.11에서 소개된 모듈 인식 모드에 대해 이야기할 예정이에요. Go 1.13부터는 기본적으로 활성화되는데, 이 모드를 사용하여 프로젝트의 종속성을 관리하는 방법도 함께 알아볼 거에요.

모듈 인식 모드는 Go 명령어를 사용하여 종속성을 관리할 수 있는 기능이에요. 다시 말해, go build, go test, go get 등의 명령어는 모듈의 context 내에서 작동하여 go.mod 파일에 지정된 버전에 따라 모든 종속성이 해결되도록 합니다.

그리고, 이 모드는 go.sum 파일을 도입하는데요. 이 파일은 모듈의 종속성의 체크섬을 포함하여 무결성을 확인하고 변조를 방지합니다. 다음 이야기에서 더 자세히 다루겠어요.

<div class="content-ad"></div>

# go.mod이란 무엇인가요?

go.mod 파일 또는 go 모듈은 기본적으로 여러 개의 Go 패키지를 구성하고 관리하는 방법입니다.

이러한 패키지들은 단일 단위로 버전이 지정되어 릴리스되며 배포됩니다. 이러한 모듈은 GitHub과 같은 저장소 또는 전용 모듈 프록시 서버에서 패치하는 것이 익숙할 것입니다.

사람들은 종종 이를 "패키지"라고 부르지만, 기술적으로는 "모듈"에 더 가깝습니다. 그래서 패키지를 패치한다고 할 때, 실제로 전체 모듈을 패치하는 것입니다.

<div class="content-ad"></div>

go.mod 파일은 무엇을 하는 파일인가요?

모듈 자체를 정의하는 파일입니다. 모듈의 이름, 사용하는 Go 버전, 필요한 종속성(직접 또는 간접적으로) 등을 알려줍니다.

모듈 이름은 모듈의 import 경로에 해당합니다. 해당 모듈에서 패키지를 사용하려면 모듈 이름을 접두사로 붙여서 가져와야 합니다.

```js
# go.mod
module thisismodulename
```

<div class="content-ad"></div>

```js
// 이 모듈에서 패키지를 가져옵니다
import "thisismodulename/any/package"
```

현재 디렉토리에 모듈을 만들거나 초기화하려면 `module-name` 명령어로 go mod init을 사용할 수 있습니다. 물론, 디렉토리가 이전에 모듈이 아니어야 합니다.

이것은 모두 기본적인 내용입니다. 이제 이 기능에 대한 자세한 내용을 살펴보겠습니다.

# go get: 의존성 관리 (빌드하지 않고 설치하지 않음)

<div class="content-ad"></div>

Go에서의 go get 명령은 주 모듈의 go.mod 파일에 있는 모듈 종속성을 업데이트하는 데 사용됩니다. 또한 명령줄에 지정한 패키지를 빌드하고 설치하는 데도 사용됩니다.

go get을 실행하면 어떤 것을 업데이트할지를 사용자가 지정한 내용에 기반하여 결정합니다. 이를 하는 몇 가지 방법이 있습니다: 패키지(예: github.com/user/project/package), 모듈 경로(github.com/user/project)를 지정하거나 .과 같은 패턴을 사용할 수도 있습니다.

네, go get을 그냥 실행하면 현재 디렉토리 (".")를 지정한 것처럼 동작합니다. 현재 디렉토리에서만 모든 가져오기된 패키지의 누락된 모듈을 업데이트합니다. 많은 사람들은 이 작업을 위해 go get ./... 또는 go mod tidy를 사용합니다.

go get ./...를 사용하면 패턴은 ./...이며 이는 모든 하위 디렉토리를 나타냅니다. 이 쿼리는 이를 와일드카드 패턴으로 인식하고 현재 디렉토리 및 그 하위 디렉토리에 있는 모든 패키지로 해석합니다. 당연히 주 모듈 내에서 이루어집니다.

<div class="content-ad"></div>

## go get 작동 방식

go get을 실행하면 처음으로 업데이트해야 할 모듈을 결정합니다. 모듈, 패키지 또는 경로 패턴의 목록을 인수로 제공합니다.

각 인수는 버전 쿼리 접미사를 포함할 수 있으며, 이 멋진 용어는 @ 기호 다음에 원하는 버전을 추가할 수 있다는 것을 의미합니다:

- 특정 버전 @v1.2.3: 해당 정확한 버전 사용.
- 버전 접두사 @v1.2: 해당 접두사로 시작하는 최신 버전을 가져옵니다.
- 브랜치 @main 또는 태그 @v1.2.3: 해당 브랜치나 태그의 최신 버전을 가리킵니다.
- 커밋 해시 또는 리비전 @abcdef: 해당 특정 커밋 사용.
- 최신 버전 @latest 또는 최신 패치 버전 @patch: 가장 최신 버전 또는 패치를 가져옵니다.

<div class="content-ad"></div>

한번 go get이 당신이 제공한 인수를 기반으로 사용할 모듈 및 버전을 찾아내면, 당신의 프로젝트의 go.mod 파일을 업데이트하여 의존성에 대한 최소 요구 버전을 추적합니다. 새로운 의존성이 더 높은 버전이 필요한 경우, go get은 또한 이를 반영하도록 자동으로 go.mod 파일을 업데이트합니다.

그런데, 만약 A와 B 두 의존성이 있고, 두 모듈이 같은 C 모듈을 다른 버전으로 사용하는 경우는 어떻게 처리할까요?

## Go가 의존성 해결하는 방법

Go는 의존성을 처리하고 버전 충돌을 해결하기 위해 Minimal Version Selection (MVS)라는 것을 사용합니다.

<div class="content-ad"></div>

MVS는 복잡해 보이지만, 이해하기 쉽게 말하면 다른 모듈에서 모든 요구 사항을 충족하는 각 모듈의 가장 낮은 버전을 선택합니다. 이 방법을 통해 의존성을 가능한 한 최소화하면서 관련된 모든 것의 요구 사항을 충족시킵니다.

예를 들어, 세 개의 모듈 A, B 및 C가 있다고 가정해 봅시다.

- 모듈 A@1.3.2와 모듈 B@1.0.0은 둘 다 모듈 C에 의존합니다.
- 그러나 A는 C@2.3.2가 필요하고, B는 C@2.3.0이 필요합니다.
- C에는 2.4.1이라는 더 새로운 버전도 있습니다.

Go는 C의 어떤 버전을 사용할지 어떻게 결정할까요?

<div class="content-ad"></div>


![이미지1](/assets/img/2024-06-23-GOEP11GoCommands-gogetgomodtidyetc_1.png)

MVS는 모듈 A와 B에서 모듈 C의 모든 요구 사항을 확인합니다. A는 C 버전 2.3.2를 필요로 하고, B는 C 버전 2.3.0을 필요로 합니다. MVS는 이러한 요구 사항을 모두 충족하는 가장 낮은 C 버전을 선택합니다.

이 경우, 2.3.0과 2.3.2를 모두 충족하는 가장 작은 버전은 2.3.2입니다. C를 2.4.1로 선택하는 것은 가장 낮은 버전이 아니므로 2.3.2를 선택합니다.

![이미지2](/assets/img/2024-06-23-GOEP11GoCommands-gogetgomodtidyetc_2.png)


<div class="content-ad"></div>

호환성은 Go 모듈이 사용하는 Semantic Versioning (semver)으로 관리됩니다.

만약 모듈이 semver을 따른다면, 어떤 중요한 변경 사항이 있을 때는 major 버전을 올려야 합니다 (major.minor.patch). 2.3.0 버전과 2.3.2 버전은 둘 다 같은 major 버전 (2.3.x)을 가지고 있으므로 두 버전은 서로 하위 호환성을 유지해야 합니다.

다른 버전들이 호환되도록 유지되는 것은 모듈 작성자의 책임입니다.

모듈이 major 버전 2 이상이 되면, 경로에 major 버전 번호가 포함됩니다 (예: /v2 또는 /v3):

<div class="content-ad"></div>

```js
github.com/user/project/v2
github.com/user/project/v3
```

다른 메이저 버전을 가진 모듈은 별개의 모듈로 처리됩니다. 그래서 동일한 프로젝트에서 C@2.3.2와 C@3.0.0을 함께 사용할 수 있습니다.

예를 들면, 당신의 go.mod 파일은 다음과 같을 수 있습니다:

```js
module yourproject

require (
    github.com/user/A v1.0.0
    github.com/user/B v1.0.0
)
require (
    github.com/user/C/v2 v2.3.2 // indirect
    github.com/user/C/v3 v3.0.0 // indirect
)
```

<div class="content-ad"></div>

// 간접 코멘트는 프로젝트가 C 모듈을 직접적으로 가져오지 않는다는 것을 의미합니다. A 또는 B가 필요하기 때문에 그 모듈이 존재합니다.

한 가지 작은 점: go get은 업데이트하거나 누락된 테스트 종속성을 추가하지 않습니다. 이를 포함하려면 -t 플래그를 사용하십시오. 예를 들어 go get -t ./...

다양한 상황에서 go get이 작동하는 방법을 보는 몇 가지 예제를 살펴봅시다.

## go get .

<div class="content-ad"></div>

`go get .`이나 `go get ./...`을 사용하면 현재 디렉토리나 하위 디렉토리에 있는 모든 누락된 종속성을 찾아 go.mod 파일에 추가합니다.

여기서 핵심은 "누락된"입니다.

즉, 이미 명시되지 않은 종속성을 확인하고 추가합니다. 최신 버전으로 기존 종속성을 업데이트하지는 않습니다. 단, 다음 예제처럼 -u 플래그와 함께 명시적으로 요청하지 않는 한.

## go get -u .

<div class="content-ad"></div>

`go get .` 명령에 `-u` 플래그를 사용하면 현재 디렉토리에서 기존 종속성을 가장 최신의 마이너 또는 패치 버전으로 업데이트합니다. 기억해 주세요, 새로운 주 버전으로는 업데이트되지 않는다는 것을 주의하세요. 왜냐하면 이것은 다른 모듈로 취급됩니다.

메인 모듈의 모든 종속성을 최신 버전으로 업데이트하려면 `go get -u ./...`을 사용할 수 있습니다.

대부분의 경우에는 `-u` 플래그를 명시적으로 지정하지 않아도, 종속성이 오래되었거나 누락된 경우 go get이 여전히 업데이트할 수 있습니다.

## go get github.com/user/project

<div class="content-ad"></div>

이 명령은 모듈 github.com/user/project을 다운로드하고 go.mod 파일에 추가합니다. 이미 명시된 경우, 최신 소규모 또는 패치 버전으로 모듈을 업데이트합니다.

기본적으로 버전을 지정하지 않거나 (또는 버전 쿼리 접미사를 지정하지 않는 경우) 최신 버전으로 업그레이드하려고 한다고 가정하며, 마치 go get github.com/user/project@upgrade를 사용하는 것과 같습니다.


go get github.com/user/project/package


패키지 자체가 모듈이 아닌 경우에도 go get은 해당 패키지를 제공하는 모듈을 업데이트합니다.

<div class="content-ad"></div>

`go get github.com/user/project`을 입력하면 github.com/user/project에서 패키지를 가져옵니다. 숨겨진 `@upgrade` 동작을 사용하여 패키지를 포함하는 모듈의 최신 버전을 가져옵니다.

## go get github.com/user/project@v1.2.3

이 명령어는 모듈을 지정된 버전인 v1.2.3으로 업데이트합니다. 현재 버전에 따라 해당 버전과 일치하도록 모듈을 업그레이드하거나 다운그레이드할 수 있습니다.

# go install: 패키지 빌드 및 설치

<div class="content-ad"></div>

패키지를 빌드하고 설치하는 것은 무엇을 의미할까요?

프로젝트에서 소스 코드를 사용하기 위해 종속성을 다운로드하는 것과는 달리, go install은 종속성의 소스 코드를 이진 파일로 빌드하고 $GOPATH/bin 디렉토리로 이동시켜 설치합니다. 이렇게 하면 터미널에서 사용할 수 있게 됩니다.

```js
$ go install golang.org/x/tools/gopls@latest
```

이 명령을 실행하고 $GOBIN 폴더를 확인하면, gopls라는 실행 파일이 있을 것입니다. 그리고 $GOBIN이 $PATH에 있는 경우 터미널에서 gopls를 실행할 수 있습니다.

<div class="content-ad"></div>

```js
$ gopls 버전
golang.org/x/tools/gopls v0.15.3
```

Go install 명령어는 누락된 종속성을 다운로드하고 현재 디렉토리에 있는 현재 모듈을 빌드합니다.

따라서 일부 사람들이 go install을 종속성을 관리하기 위해 사용하는 실수를 할 수 있습니다. 종속성을 다운로드하긴 하지만, 그것이 주요 역할은 아니며, 실제로 프로젝트를 빌드하고 생성된 이진 파일을 $GOBIN 디렉토리에 설치하는 것이 목적입니다.

그래서 go install과 go get의 차이점은 무엇인가요?

<div class="content-ad"></div>

go install은 패키지를 빌드하고 설치하는 데 사용되고, go get은 종속성을 관리하는 데 사용됩니다. 예전 Go 버전에서는 go get이 go.mod 파일을 업데이트한 후 패키지를 빌드하고 $GOPATH/bin에 설치했던 것으로 혼란스러운 경우가 많습니다.

하지만 Go 1.16부터는 go install이 빌드 및 설치에 사용되는 주요 명령어가 되었고, go get은 go.mod 파일에서 요구 사항을 관리하는 데 집중하게 되었습니다.

# go mod

go mod 명령어 패밀리는 주 모듈과 그 종속성을 관리하는 데 사용됩니다. 이러한 명령어는 프로젝트의 go.mod 파일을 만들고 편집하며 유지하는 데 도움이 됩니다.

<div class="content-ad"></div>

## go mod init

이름에서 알 수 있듯이 이 명령은 현재 디렉토리에 모듈을 초기화합니다.

```js
go mod init github.com/user/project
```

이 명령을 실행한 후에는 현재 디렉토리에 go.mod 파일이 생성되고 그 외에는 할 일이 없습니다.

<div class="content-ad"></div>

작은 것 하나만 바꿔드릴게요! 모듈 이름이나 경로는 선택 사항이에요. Go는 기존 코드, 구성 파일 또는 현재 디렉토리 구조를 기반으로 자주 이를 알아낼 수 있어요:

## go mod tidy

`go mod tidy`는 실제로 Go에서 가장 유용한 명령 중 하나에요. 이 명령은 프로젝트 내의 모든 패키지와 이들의 종속성을 확인하여 `go.mod` 파일을 정리하고 최적화할 수 있어요.

이것은 어떻게 작동하나요?

<div class="content-ad"></div>

- 모든 빌드 태그가 활성화된 것처럼 메인 모듈 및 해당 종속 항목의 모든 패키지를 재귀적으로 확인합니다 (// +build ignore를 제외하고).
- 메인 모듈의 패키지에서 사용하는 모든 모듈이 go.mod 파일에 명시되어 있는지 확인합니다. 모듈이 명시되어 있지 않으면 최신 버전을 찾아 다운로드합니다.
- 실제로 메인 모듈이나 해당 종속 항목에서 사용되지 않는 종속 항목을 제거합니다.
- 메인 모듈에 의해 간접적으로 사용되는 모든 추이적 종속 항목을 추가하며, // indirect로 표시합니다.

추이 종속성은 코드에서 직접적으로 사용되지는 않지만 의존하는 다른 모듈이 필요로 하는 것을 의미합니다.

go get과 달리 go mod tidy는 종속성을 최신 버전으로 업데이트하지 않습니다 (이를 위해서는 -u 플래그가 필요할 수 있습니다), 하지만 go.mod 파일에 테스트 종속성을 포함합니다.

## go mod download

<div class="content-ad"></div>

$GOPATH/pkg/mod 디렉토리에 대해 읽어보셨다면, go mod download은 go.mod 파일에 나열된 모든 종속성(직접 및 간접)을 다운로드하여 이 캐시를 채우는 명령어입니다.

go mod tidy가 하는 것처럼 소스 코드를 살펴볼 필요가 없습니다. 그냥 go.mod 파일을 읽고 모든 것을 다운로드합니다.

이것이 왜 Dockerfile에서 종속성을 캐시하고 프로젝트를 빌드하기 전에 사용되는 이유입니다. 단순히 go.mod 파일만 있으면 go mod download가 필요한 모든 것을 다운로드해 줍니다.

## go mod why

<div class="content-ad"></div>

만약 go.mod 파일에 패키지가 있는 이유를 궁금해 한다면, go mod why 명령어를 사용하여 알 수 있어요.

```js
$ go mod why github.com/user/project

# github.com/user/project
myproject/module/logic
github.com/user/anotherproject
github.com/user/project
```

go mod why 명령어는 주요 모듈에서 해당 패키지까지의 가장 짧은 경로를 보여줍니다.

이 예제에서 myproject/module/logic은 주요 모듈에서 가져오는 패키지로, github.com/user/anotherproject를 가져오고, 여기서 github.com/user/project를 가져옵니다.

<div class="content-ad"></div>

go mod 패밀리에 다른 명령어도 있지만 자주 사용되지는 않아요. 그래서 그냥 간략하게 살펴볼게요.

### go mod edit

보통은 go.mod 파일을 직접 편집하지만, go mod edit은 수동 편집 없이 go.mod 파일에 변경을 가해야 하는 도구나 스크립트에 유용해요.

### go mod graph

<div class="content-ad"></div>

`go mod graph` 명령어를 사용하면 프로젝트의 의존성을 이해할 수 있습니다. 해당 명령어는 모듈 요구 사항 그래프를 출력합니다. 각 모듈 버전이 어떻게 해당 모듈이 의존하는 모듈의 버전과 연결되어 있는지 보여줍니다.

“그래프”라고 불리지만 현재는 시각적 그래프가 아니라 각 모듈과 해당 모듈의 의존성 목록입니다.

## go mod vendor

다음에 판매자에 대해 자세히 알아보겠지만, 여기에 간단한 개요가 있습니다.

<div class="content-ad"></div>

인터넷에서 종속성을 다운로드하고 $GOPATH/pkg/mod에 캐시하는 대신에, go mod vendor를 사용하면 종속성을 다운로드하여 프로젝트의 루트 폴더에 바로 저장할 수 있습니다. 이렇게 하면 프로젝트를 더 이동하기 쉽게 만들 수 있습니다.