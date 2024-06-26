---
title: "러스트 앱을 여러 아키텍처용으로 크로스 컴파일하기"
description: ""
coverImage: "/assets/img/2024-05-18-Cross-compileyourRustappformultiplearchitectures_0.png"
date: 2024-05-18 19:07
ogImage:
  url: /assets/img/2024-05-18-Cross-compileyourRustappformultiplearchitectures_0.png
tag: Tech
originalTitle: "Cross-compile your Rust app for multiple architectures"
link: "https://medium.com/gitconnected/cross-compile-your-rust-app-for-multiple-architectures-069bf98d0728"
---

<img src="/assets/img/2024-05-18-Cross-compileyourRustappformultiplearchitectures_0.png" />

Rust는 앱 개발을 위한 포맷부터 문서 작성까지 포괄적인 도구를 갖춘 신속하고 견고한 언어입니다. 그러나 컴파일된 언어이므로 다양한 아키텍처 간 호환성을 보장하기 위해 추가적인 노력이 필요합니다. 다행히 Rust는 이를 개발자들을 위해 간소화했습니다. 오늘은 Rust로 기본적인 HTTP 서버 애플리케이션을 작성하고 ARMv7 프로세서용으로 크로스 컴파일하여 네트워크 연결을 통해 STM32MP1 보드에 배포하는 방법에 대해 살펴볼 것입니다.

# 준비물

크로스 컴파일 작업을 시작하기 전에, 특정 사전 준비물이 갖추어져 있어야 합니다. 계속 진행하기 전에 다음 사항을 확인하세요:

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

- Rust이 컴퓨터에 설치되어 있습니다.
- 당신은 싱글보드 컴퓨터 또는 컴퓨터와 다른 아키텍처를 가진 장치를 사용하고 있습니다. (저는 STM32MP1을 사용하지만 라즈베리파이, 비글본 또는 다른 장치를 사용할 수 있습니다)
- 싱글보드 컴퓨터에서 리눅스 배포판이 실행 중입니다.
- 당신은 보드의 IP 주소를 알고 있으며 SSH를 통해 연결할 수 있는 능력을 가지고 있습니다.

# 안녕하세요, 새로운 Arch!

먼저 Rust 프로젝트가 포함될 디렉토리를 생성합니다. 가장 간단한 방법은 원하는 이름으로 빈 폴더를 수동으로 생성한 다음 (예: mkdir hello-new-arch), 해당 폴더로 이동하여 cargo init --bin을 실행하는 것입니다. 이 명령은 실행 가능한 (바이너리) "hello new arch" 애플리케이션을 위한 모든 필요한 소스 파일을 생성합니다. 터미널에서 cargo run으로 앱을 컴파일하고 실행하면 모든 것이 잘 작동하면 터미널에 "Hello, world!" 메시지가 출력됩니다.

이제 백그라운드에서 실행되는 간단한 HTTP 서버를 생성할 준비가 되었습니다. Rust는 이 작업을 수행하기 위한 여러 우수한 옵션을 제공합니다. 저는 이 작업을 수행하기 위해 Axum을 선호합니다. 이는 이전 프로젝트에서의 경험과 Tokio 비동기 런타임 팀과의 관련성으로 인해입니다.

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

먼저 Cargo.toml 파일에 필요한 종속성을 추가해야 합니다.

```js
[package]
name = "hello-new-arch"
version = "0.1.0"
edition = "2021"

[dependencies]
axum = "0.7.2"
tokio = { version = "1.35.0", features = ["full"] }
```

다음으로 Axum GitHub 저장소에서 'hello-world' 예제 애플리케이션을 복제합니다. 그 후에는 작은 수정만 필요합니다.

저희가 가장 크게 변경하는 부분은 127.0.0.1:3000에 바인딩하는 대신에 실제로 물리적 네트워크 인터페이스 상에서 노출되지 않는 루프백 주소인 0.0.0.0:3000에 바인딩한다는 것입니다.

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

우리는 이제 로컬에서 cargo run을 통해 다시 실행하고 모든 것이 잘 작동하면 터미널에 "listening on..." 메시지를 볼 수 있습니다. 그런 다음 브라우저 탭을 열어 http://localhost:3000/으로 이동하여 "Hello from another architecture!" 헤더가 포함된 페이지가 제공되는지 확인합니다.

# 크로스 컴파일 단계!

현재, 우리 앱을 보드에 업로드하려고 하면 실행할 수 없다는 것을 알게 될 것입니다. 이 문제는 우리 프로그램이 보드의 아키텍처와 호환되지 않는 x86 프로세서용으로 컴파일되었기 때문에 발생합니다.

Rust를 크로스 컴파일하려면 보드에서 사용 중인 아키텍처를 확인해야 합니다. 한 번 결정되면, 현재 Rust 툴체인에 적합한 타겟 플랫폼을 설치해야 합니다. Rust는 Tier1, Tier2 및 Tier3 수준으로 분류된 많은 아키텍처에 대한 광범위한 지원을 제공합니다. 이러한 티어 간의 차이점에 대한 포괄적인 이해를 위해 문서를 참조하십시오.

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

예를 들어, 제 STM32MP1 보드에는 ARMv7 32비트 프로세서가 탑재되어 있습니다. 공식 Rust 책에 따르면, 이 구성을 위한 필수 대상은 armv7-unknown-linux-gnueabihf입니다. 대략적으로, 선택한 대상 구성 요소는 다음과 같은 의미를 갖습니다:

- armv7: 대상 프로세서에 사용할 아키텍처인 ARM v7
- unknown: 사용할 서브 아키텍처; 여기서는 기본 옵션을 의미함
- linux: 대상 운영 체제
- gnueabihf: 대상 ABI; gnu는 실행 중에 일부 기능에 GNU C 라이브러리(이른바 libc로도 알려짐)를 의존한다는 것을 의미하며, hf는 하드웨어 부동 소수점 연산을 지원한다는 것을 의미함

이 특정 대상을 위해 Rust를 구성하려면 다음 명령을 실행하세요:

```js
rustup target add armv7-unknown-linux-gnueabihf
```

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

이제 이 명령어를 사용하여 교차 컴파일을 시도해 봅시다:

```js
cargo build --release --target=armv7-unknown-linux-gnueabihf
```

그러나 잠시 후에 다음과 유사한 오류가 발생할 가능성이 있습니다:

```js
error: linking with `cc` failed: exit code: 1
```

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

2진 파일이 성공적으로 컴파일되었지만 링킹 실패가 발생했습니다. 이 문제가 발생하는 이유는 우리 개발 머신에서 Cargo가 x86 바이너리나 호스트 시스템의 특정 아키텍처를 위해 구성된 cc 및 ld에 의존하기 때문입니다. 이로 인해 ARM 바이너리를 조립할 필요한 지식이 부족합니다. 이 작업에 더 적합한 링커를 사용하도록 Cargo를 안내해야 합니다. 또한 ARM 아키텍처와 관련된 컴파일 또는 링크 작업을 처음 시도하는 경우 필요한 도구를 아직 설치하지 않았을 가능성이 높습니다. 이 문제를 해결하려면 Ubuntu에서 다음 명령을 사용하십시오:

```js
sudo apt install gcc-arm-linux-gnueabihf
```

이제 ARM에 적합한 링커와 컴파일러를 설치하고, 'arm-linux-gnueabihf-gcc' 명령어를 호출하여 테스트해보겠습니다(인수 없이 호출하면 즉시 종료됨).

그러나 Cargo가 바이너리의 링킹 단계에서 이를 활용하도록하려면 안내가 필요합니다. 이를 위해 ./.cargo/config라는 새 파일을 만들어 다음 내용을 입력해야 합니다:

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

[target.armv7-unknown-linux-gnueabihf]
linker = "arm-linux-gnueabihf-gcc"

이렇게 하면 ARM-specific 버전의 gcc를 사용하여 armv7-unknown-linux-gnueabihf Rust target을 위해 컴파일된 이진 파일을 링킹하는데 cargo가 지시됩니다.

## http-server를 시도해봅시다

좋아요, 이제 보드의 아키텍처에 맞는 올바른 컴파일 설정을 성공적으로 설정했으니, 여러 단계를 고려하여 프로세스를 간소화하기 위해 셸 파일을 스크립팅하는 것이 좋겠네요. 이 스크립트는 배포 워크플로를 자동화하여 실행을 더 쉽게 할 것입니다. 동일한 디렉토리에 deploy.rs라는 이름의 텍스트 파일을 생성하고 다음 콘텐츠를 추가합니다. 각 명령어의 기능에 대한 명확성을 위해 주석도 추가했어요:

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

그러면 chmod +x ./deploy.rs와 같은 명령으로 스크립트를 실행 가능하게 만들어 줍니다. 그러면 ./deploy를 통해 직접 실행할 수 있습니다. 이제 바로 시도해 볼 수 있고, 만약 deploy 스크립트에 매개변수를 올바르게 작성했다면, 서버에서 "listening on 'your board ip address'"라는 로그 라인을 마침내 볼 수 있게 됩니다. 이는 서버가 최종적으로 대상 보드에서 올바르게 실행되고 있음을 의미합니다!

웹 서버에 액세스하려면 브라우저 탭을 열고 http://'your_board_ip_address':3000으로 이동하면 됩니다. 이제 이 익숙한 웹 페이지가 보드에서 직접 제공되고 있습니다. 접근성을 확인하기 위해 다른 기기(같은 네트워크에 연결된 스마트폰 등)에서 접속을 시도할 수 있습니다.

이 프로그램이 프로덕션에 즉시 적합한 것은 아닙니다. SSH 세션을 종료한 후에도 계속 작동되도록 보장하려면 지속적인 실행을 지원하는 시스템을 구현해야 할 수도 있습니다. 이러한 개선 사항 및 조정 사항은 향후 기사에서 다룰 수도 있습니다.

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

관찰해 보았을 때, 크로스 컴파일은 인내와 해당 하드웨어에 대한 이해가 필요합니다. 그러나 러스트의 생태계와 관련 도구를 활용하면, 처음에 예상했던 것보다 덜 어려울 수 있습니다. 독자 여러분, 감사합니다!
