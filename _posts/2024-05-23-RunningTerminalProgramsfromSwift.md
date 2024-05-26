---
title: "스위프트로 터미널 프로그램 실행하기"
description: ""
coverImage: "/assets/img/2024-05-23-RunningTerminalProgramsfromSwift_0.png"
date: 2024-05-23 15:14
ogImage:
  url: /assets/img/2024-05-23-RunningTerminalProgramsfromSwift_0.png
tag: Tech
originalTitle: "Running Terminal Programs from Swift"
link: "https://medium.com/@gaitatzis/running-terminal-programs-from-swift-680db09a02b4"
---

스위프트와 명령 줄을 통합하면 개발자가 스위프트 애플리케이션 내에서 쉘 명령어의 기능을 활용할 수 있습니다. 이 기능은 주로 작업 자동화, 스크립트 실행 또는 시스템 수준 기능에 직접 액세스하는 데 유용합니다. 이 기사에서는 실용적인 예제를 사용하여 스위프트에서 터미널 프로그램을 실행하는 방법을 살펴보겠습니다.

# 종속성 가져오기

프로세스 실행에는 Foundation 라이브러리가 필요합니다.

```js
import Foundation
```

<div class="content-ad"></div>

Foundation 라이브러리는 애플의 개발 생태계의 핵심 부분으로, 필수적인 데이터 유형, 컬렉션 및 운영 체제 서비스를 제공하여 날짜, 시간 및 프로세스와 같은 작업을 관리합니다. Foundation을 사용하여 프로세스를 실행하면 시스템의 명령 줄과 상호 작용하기 위한 강력하고 테스트된 API를 활용할 수 있습니다.

# 프로세스 실행

Swift에서 터미널 애플리케이션을 실행하는 것은 본질적으로 직접 프로그램을 실행하는 대신 zsh와 같은 쉘에서 명령 줄 프로그램을 실행해야 한다는 것을 의미합니다.

따라서 다른 프로그램을 실행하도록 지시하는 /bin/zsh를 실행해야 합니다. 여러분은 bash 스크립트에서처럼 유사한 개념인 #!/bin/bash를 알고 있을 수 있습니다. 이는 “bash를 사용하여 이 프로그램을 실행”을 의미합니다. 이는 터미널과 상호 작용하는 이진 프로그램에 대한 비슷한 개념입니다.

<div class="content-ad"></div>

본질적으로, 우리는 환경 변수를 전달하여 터미널 프로그램을 실행하도록 Swift에 지시할 것입니다 (예: PATH=/usr/bin, 실행 쉘(예: /bin/zsh), 그리고 실행할 프로그램 및 옵션을 포함한 내용(예: ls ~)):

![Running Terminal Programs from Swift](/assets/img/2024-05-23-RunningTerminalProgramsfromSwift_0.png)

## 1. 프로세스 설정하기

우선, Process의 인스턴스를 생성합니다.

<div class="content-ad"></div>

```js
let process = Process()
```

Shell 명령어의 실행은 Process 인스턴스에서 처리되며, 출력과 에러 스트림은 Pipe 인스턴스가 캡처합니다.

## 2. Process 구성

다음으로, 인수와 사용할 쉘을 정의합니다. 우리는 zsh 쉘에 명령을 실행하도록 지시할 것이므로, 프로세스의 launchPath를 /bin/zsh로 설정하고 프로그램 및 인수를 쉘의 인수로 -c `프로그램 및 인수`를 전달할 것입니다.


<div class="content-ad"></div>

```js
let command = "ls ~"
process.launchPath = "/bin/zsh" // 또는 "/bin/bash" 당신의 셸에 따라 다름
process.arguments = ["-c", command]
```

-c 인자는 쉘이 명령어 문자열을 실행하도록 지시합니다. command는 예를 들어 ls ~나 echo "Hello world"와 같은 표준 프로그램 실행을 나타내는 문자열일 수 있습니다.

일부 프로그램은 MacOS의 보안 제약으로 인해 실행할 수 없습니다. 예를 들어, Swift 프로그램에서 /bin/ps를 실행할 수 없는 이유는 특정 보안 제약을 위반하기 때문입니다.

많은 프로그램은 환경 변수가 설정되어야 합니다. 예를 들어, 프로그램이 다른 프로그램을 찾기 위해 PATH 환경 변수에 의존하는 것이 일반적입니다.


<div class="content-ad"></div>

테이블 태그를 Markdown 형식으로 변경해주세요.

```js
let environment = [
  "TERM": "xterm",
  "HOME": "/Users/example-user/",
  "PATH": "/opt/homebrew/bin:/opt/homebrew/sbin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin"
]
process.environment = environment
````

터미널에서 env를 실행하여 시스템 환경 변수를 실행 중에 확인할 수 있습니다.

<img src="/assets/img/2024-05-23-RunningTerminalProgramsfromSwift_1.png" />

<div class="content-ad"></div>

## 3. 프로세스 실행하기

그런 다음 macOS 버전을 확인하여 Foundation 라이브러리와의 호환성을 보장한 후 프로세스를 실행합니다:

```js
if #available(macOS 13.0, *) {
    try! process.run()
} else {
    process.launch()
}
```

macOS 13용 Foundation에서는 응용 프로그램 실행 중 예기치 않은 오류가 발생하면 오류를 throw하는 기능이 도입되었고 .launch() 메서드는 사용이 중단되었습니다. 이러한 이유로 macOS 버전이 13 이상인지 테스트하고 .run() 메서드를 시도하고 이전 버전의 경우 .launch() 메서드로 넘어갈 수 있습니다.

<div class="content-ad"></div>

에러를 잡아내는 것은 사용자나 개발자가 앱이 올바르게 작동하지 않음을 알 수 있는 가장 좋은 방법입니다.

# 출력 및 에러 잡기

출력을 잡기 위해서는 Pipe를 만들고, 이를 프로세스의 출력에 연결해야 합니다.

```js
let pipe = Pipe();
process.standardOutput = pipe;
process.standardError = pipe;
```

<div class="content-ad"></div>

프로세스가 완료되면, pipe는 프로세스 응답을 바이너리 배열로 수집할 것입니다. 대부분의 터미널 출력은 사람이 읽을 수 있는 텍스트이므로 UTF-8로 인코딩된 문자열로 변환할 수 있습니다:

```js
let data = pipe.fileHandleForReading.readDataToEndOfFile()
let output = String(data: data, encoding: .utf8) ?? ""
```

## 모두 함께 넣기

이러한 개념들을 결합하여, 터미널 명령을 실행하고 결과를 반환하는 함수를 만들 수 있습니다.

<div class="content-ad"></div>

```swift
func executeProcessAndReturnResult(_ command: String) -> String {
  let process = Process()
  let pipe = Pipe()
  let environment = [
    "TERM": "xterm",
    "HOME": "/Users/example-user/",
    "PATH": "/opt/homebrew/bin:/opt/homebrew/sbin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin"
  ]
  process.standardOutput = pipe
  process.standardError = pipe
  process.environment = environment
  process.launchPath = "/bin/zsh"
  process.arguments = ["-c", command]
  if #available(macOS 13.0, *) {
    try! process.run()
  } else {
    process.launch()
  }
  let data = pipe.fileHandleForReading.readDataToEndOfFile()
  let output = String(data: data, encoding: .utf8) ?? ""
  return output
}
```

따라서 다른 프로그램에서 이러한 함수를 호출할 수 있습니다. 예를 들어 사용자의 홈 폴더에있는 폴더를 나열하는 프로그램에서 ls ~을 사용하여 호출할 수 있습니다:

```swift
let response = executeProcessAndReturnResult("ls ~")
print(response)
// Desktop Documents Downloads Library Movies Pictures Public
```

# 데몬 프로세스 시작하기


<div class="content-ad"></div>

긴 시간이 걸리는 프로세스, 예를 들어 데몬(daemon) 같은 것을 시작하고 주 스레드와 바인딩을 해제하려면, 프로세스를 실행하기 전에 "불확정 상태"로 설정합니다:

```js
// ... let process = Process()
process.unbind(.isIndeterminate)
// ... process.run()
````

이렇게 하면 프로세스가 독립적으로 계속 실행되지만, 앱에서 출력을 캡처하는 것이 더 어려워집니다.

이러한 개념을 결합하여 터미널 명령어를 실행하고 출력을 반환하는 함수를 만들 수 있습니다.

<div class="content-ad"></div>

```swift
func executeDaemonProcess(_ command: String) -> String {
  let process = Process()
  process.environment = environment
  process.launchPath = "/bin/zsh"
  process.arguments = ["-c", command]
  process.qualityOfService = .background
  if #available(macOS 13.0, *) {
    try! process.run()
  } else {
    process.launch()
  }
}
```

그래서 백그라운드에서 실행되는 이러한 함수를 호출할 수 있습니다. 예를 들어 ~/Public 폴더에서 파일을 제공하는 Python HTTP 서버를 시작할 수 있습니다.

```swift
executeDaemonProcess("/usr/bin/python3 -m http.server -d ~/Public")
```

이 프로세스를 실행하면 ~/Public 폴더에서 파일을 제공하는 http://localhost:8080의 웹 서버가 생성됩니다.



<div class="content-ad"></div>

# 결론

Process 클래스를 사용하여 Swift 프로젝트에서 셸 명령 실행을 원활하게 통합할 수 있습니다. 이 기술을 사용하면 Swift 프로젝트 내에서 커맨드 라인 프로그램 및 RPC의 프론트 엔드를 만들거나 자동화 및 시스템 수준 스크립팅을 수행하는 다양한 가능성이 열립니다.

실제 구현에서는 강제 언래핑을 사용하고 모든 잠재적인 오류 및 예외 상황을 처리해야 함을 기억하세요. 위 예제는 모든 실패 시나리오를 처리하지 않으므로 주의해야 합니다.

