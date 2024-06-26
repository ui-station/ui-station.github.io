---
title: "새 프로그래밍 언어를 배울 때입니다"
description: ""
coverImage: "/assets/img/2024-06-19-ItsTimeforaNewProgrammingLanguage_0.png"
date: 2024-06-19 10:21
ogImage:
  url: /assets/img/2024-06-19-ItsTimeforaNewProgrammingLanguage_0.png
tag: Tech
originalTitle: "Its Time for a New Programming Language"
link: "https://medium.com/@peterfraedrich/its-time-for-a-new-programming-language-f04e24704101"
---

![New Programming Language](/assets/img/2024-06-19-ItsTimeforaNewProgrammingLanguage_0.png)

요즘은 프로그래밍 언어의 역사에서 다른 모든 시점과 비교할 때 선택의 여지가 많아서 선택 장애를 느낍니다. 언어 디자인의 발전, LLVM과 같은 도구 상자, 인터넷을 통한 프로그래밍의 민주화, 그리고 많은 다른 요인들이 새로운 언어들의 황금 시대로 이끌고 있습니다. 매주 Zig, Deno, Nim, Rust, Flutter, Kotlin, Go, Crystal, Carbon 등 많은 새로운 언어가 인기를 얻는 것을 볼 수 있습니다. 그러나 실제로 사용되는 언어들을 조사해 보면, 대부분 계속해서 JavaScript, TypeScript, Python, Java, Ruby, PHP 등 평범한 언어들이 사용되고 있습니다. 이에 대한 이유는 새로운 언어들이 항상 등장하지만, 일부는 혁신적인 디자인 결정이나 기본 기능을 갖추고 있더라도, 그중에서 심각한 제품 수준의 작업과 아키텍처를 위해 설계된 것이 거의 없다는 것입니다. 저는 그것들을 '장난감 언어'라고 부르기는 싫지만, Deno나 Crystal 같은 언어로 작동하는 심각한 제품 서비스가 곧 나올 것으로 생각되지는 않습니다.

"그러면 평범한 언어들에 무엇이 문제인가요?" 라고 물어볼 수도 있습니다.

그런데 그것은 정말 좋은 질문일 것입니다. 솔직히 말해서, 문제는 그들이 무엇이 잘못됐는지보다는, 어떻게 현재의 아키텍처와 생태계에 적합하며 어떤 종류의 작업을 수행할지입니다.

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

# 자바스크립트, 타입스크립트, 노드JS, 커피스크립트 등

자바스크립트는 프로그래밍 언어의 인간을 흉내 내려는 비명쳐 옷을 입은 세 마리 너구리입니다. 1995년 처음 등장한 이후에 계속해서 추가된 모든 기능은 그때의 디자인에는 고려되지 않았던 것들 같아요. 자바스크립트 안에서 모든 일을 수행하는 방법은 일반적으로 세 가지에서 여덟 가지까지 다양하며, 각각의 방법은 특이한 부작용과 위험을 가지고 있어요. 이 고대의 심연마법을 다뤄야 하는데, 이것은 V8 엔진의 물에서 세례를 받지 않은 사람에게는 자바스크립트를 효율적이고 효과적으로 작성하기 어렵게 만들어요. 프론트엔드와 백엔드 작업을 동일한 언어로 수행하는 아이디어는 좋은 아이디어로 들릴 수 있지만, 실제로 프론트엔드와 백엔드가 공통으로 가지고 있는 것은 기본 구문과 불필요한 3rd-party NPM 패키지를 사랑하는 것뿐이에요.

노드JS, 타입스크립트, 커피스크립트 등 자바스크립트에서 파생된 다른 언어 또는 변형도 이 역시 같은 원리에 따라 작동합니다. 그들도 결국 다른 너구리를 추가하고 있죠.

그리고, 자바스크립트가 얼마나 엉망인지 못 믿겠다면, 이 놀라운 번개 토크를 보세요: WAT

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

# 파이썬

파이썬을 정말 좋아합니다. 파이썬은 진입하기 쉽고, 구문이 명확하고 간결하며, 대부분의 작업을 잘 처리할 만큼 유연합니다. 최근 버전에서 타입 주석 및 match/case가 추가되면서 파이썬은 최근에 훌륭한 생활 편의성 개선을 이루었습니다. 그러나 이러한 기능들이 파이썬을 구원하지 못하는 주요 단점은 속도가 느리다는 것입니다. 다른 언어들과 비교했을 때 정말 느립니다. 해석되는 언어인 것이 이러한 측면에서 도움이 되지 않지만, 문제의 핵심은 GIL(Global Interpreter Lock)에 있습니다. GIL은 Python 코드가 스레드 안전을 위해 CPU의 단일 코어에서만 실행되도록 보장하지만, 병렬 컴퓨팅과 동시성에 대한 유연성과 속도가 저하됩니다. 쓰레드 사용을 포함한 병렬화를 단 하나의 코어로 제한하는 것은 무의미하다는 점이 있습니다.

실제로 프로덕션 환경에서 파이썬 API는 일반적으로 그 자체만으로 배포되지 않습니다 (적어도 그래서 되어서는 안됩니다). Flask, Sanic, FastAPI 등은 모두 파이썬 앱과 동일한 제약 사항으로 묶여 있기 때문에 웹 트래픽을 신뢰성 있게 그리고 어느 정도 성능적으로 제공하기 위해 병렬 처리 프레임워크 내에서 실행되어야 합니다. 이로 인해 배포와 디버깅이 더 복잡해집니다.

또한 파이썬 앱을 배포하는 방법에 대해 이야기해야 합니다. 그게 좋지 않다고요. pip, 파이썬의 패키지 관리자는 종속성을 전역적으로 설치하도록 기본값으로 설정되어 있어 한 번에 같은 라이브러리의 두 가지 버전을 설치할 수 없습니다. 이는 같은 기계에서 여러 개의 Python 앱을 사용하려고 할 때 문제가 발생합니다. 또한 배포 관련해서는 더 복잡해집니다. virtualenv나 poetry와 같은 도구가 종속성 격리를 쉽게 만들어 주긴 하지만, 이러한 도구를 사용해 Python을 기반으로 하는 도구를 최종 사용자에게 배포하는 데 의존하는 것은 어리석은 짓입니다.

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

파이썬의 구문은 깨끗하고 읽기 쉽습니다 - 대부분의 경우에요. 물론, 의미 있는 공백은 심각한 문제이며 그런 것은 결코 있어서는 안 되지만, 대부분 다른 부분들은 이치에 맞게 구성돼 있어요.

# 루비

내 의견으로는 루비는 최악이에요. 확고하고 표명적인 팬층을 가지고 있는데, 그건 확실한 것이지만, 이 언어는 구조적으로 좋지 않고 제거돼야 할 필요가 있어요.

빠르지 않아요.

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

그것은 쉽게 읽을 수 없어서 미안해요.

그리고 대부분의 경우, 그것은 참혹하고 끔찍한 프레임워크와 함께 사용되어서 더는 앞으로 보거나 언급되어서는 안 될 정도로 깊은 심연으로 던져져야 하는 Rails입니다.

루비 온 레일 애플리케이션은 장난감입니다. 그것들은 진지한 작업, 심각한 애플리케이션 또는 진지한 것을 위한 것이 아닙니다. 대형 루비 상점들이 다른 언어로 전환한 이유가 있습니다. Shopify가 대표적입니다.

게다가, 루비 생태계와 관련된 문화는 나쁜 행동과 품질이 낮은 코드를 장려합니다. 실시간으로 실제 애플리케이션 상태를 수정하기 위해 Ruby 콘솔에 들어가야만 애플리케이션이 배포되거나 데이터베이스 마이그레이션을 할 수 있는 상황을 너무나 많이 겪어온 횟수는 정말 너무 많습니다. 그 행동을 장려하는 사람은 배척되어야 한다고 생각해요.

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

간단히 말해서 루비는 💩야.

# PHP

PHP는 올바르게 하는 것들에 대해 모두, 보안 취약점과 성능 병목 현상의 거대한 혼란입니다. PHP의 멋진 점은 HTTP 요청의 맥락에서 독점적으로 실행될 수 있다는 것이고, 그게 멋진 점의 전부다.

PHP의 구문은 말할 수 없을 만큼 이상해. .를 사용한 문자열 연결? 욕나올 정도로 추해.

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

2005년이 전화를 걸어왔어요. 그 모던하지 않은 웹 앱들을 돌려달라고 합니다!

# 러스트

러스트는 아마도 이 목록 중에서 가장 진지한 언어일 것입니다. 이것은 낮은 수준의 시스템 언어로 간주됩니다 (비교적으로 어셈블리어 수준은 아니고, 좀 더 C 수준으로 이해하셔야 합니다). 그리고 이 목록에 있는 다른 언어들과 달리, 러스트는 리눅스 커널이나 향후 윈도우 버전과 같은 주요 시스템에 중요한 부분이 될 수 있다는 점에서 매우 주목받고 있습니다.

러스트는 매우 헌신적인 추종자 그룹을 갖고 있고, 그 이유가 충분합니다. 이 언어는 빠르고 메모리 안전하며, C나 C++보다 쓰기 편하면서도 이러한 언어들의 성능과 유연성을 모두 간직하고 있습니다. Cargo 패키지 매니저는 번창하는 생태계를 가지고 있습니다. 모두 좋은 일들이죠.

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

하지만 Rust가 아주 잘 하지 못하는 몇 가지가 있습니다. 그 중 가장 큰 것은 동시성입니다. Rust에서 동시성을 사용하는 애플리케이션을 작성하는 것은 즐거운 작업이 아니라고 합니다. 동시성은 사실상 여기저기에서 생각한 것보다는 별겄 것이었습니다. 미래에는 나아질 수 있겠지만, 현재로서는 조금 문제가 있는 상태입니다.

동시에 Rust 커뮤니티에서는 현재 매크로를 어떻게 사용해야 하는지에 대한 논쟁이 있는 것으로 보입니다. "큰 매크로 논쟁"에 대한 논쟁이 해결될 때까지 조심하는 것이 좋을 것 같습니다.

또한, 사소한 것이지만, Rust 구문은 두 가지 제가 가장 싫어하는 규칙을 사용합니다: 이중 콜론 (::) 및 세미콜론 (;).

# Go

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

"20년간의 소프트웨어 개발 업무를 무시하고 언어를 설계한다면 어떻게 될까?"라는 질문에 대한 대답은 바로 Go입니다.

이전에 Go에 대해 작성한 내용이 있는데, 특히 Go의 패키지 관리가 출시 당시 간과되었으며, 최근에야 어느 정도 개선되었다는 점을 언급했습니다. Go 1.0을 적절한 패키지 관리 기능 없이 공개한 것은 큰 실수였죠.

Go는 일부 기능에서 정말로 뛰어났다고 할 수 있습니다. Go의 동시성은 고루틴을 통해 쉬우며 사용자 친화적입니다. 하지만 다른 기능들은 신중하게 고려되지 않았거나 산업 표준을 따라가지 못하거나 간단히 이상하게 설계되었습니다.

- 구조체 태그는 프로그램이 어떻게 작동하는지를 바꾸는 마술 주석이라고 할 수 있습니다.
- 에러 처리는 반복적이고 짜증 날 수 있습니다.
- Go는 명확성 대신 관례를 선택합니다: 메서드 및 매개변수 노출은 키워드가 아닌 대소문자 변경으로 제어됩니다. 대문자로 시작하는 메서드 또는 속성은 공개되며, 소문자로 시작하는 것은 (myMethod는 비공개이고 MyMethod는 공개) 그렇지 않습니다.
- 또한 일부 패키지와 기능은 CGO를 필요로 하고, 일부는 순수 Go입니다. 이로 인해 여러 플랫폼에 Go 앱을 배포하기 어렵게 만들 수 있습니다."

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

하지만 나에게 있어 Go가 제대로 된 언어로 인정받기를 원한다면, 프로덕션 환경에서 사용할 수 있는 언어로서의 역할을 수행하려면 가장 필요한 기능 중 하나는 동적으로 공유 라이브러리에서 심볼을 가져올 수 있는 기능입니다. Go 애플리케이션은 항상 해당 프로그램을 실행하는 데 필요한 모든 코드를 포함하는 단일 이진 파일입니다. 애플리케이션은 외부 구성 또는 파일을 읽을 수 있지만 외부 파일에서 코드를 읽지 않습니다. 이것은 웹 앱과 같이 자체 포함된 애플리케이션을 작성하는 경우 매우 합리적으로 이해되지만, 확장성이 필요한 경우에는 어떠한 미친 듯한 우회 방법을 수행해야 합니다. 가장 일반적인 우회 방법은 주 프로세스가 완전히 다른 애플리케이션을 실행하는 자식 프로세스를 생성하고 두 프로세스가 RPC를 통해 통신하도록 하는 것입니다. 이러한 사례로는 Terraform이 좋은 예시입니다: Terraform 공급자는 Terraform 주 프로세스와 RPC를 통해 통신하는 자체 포함된 Go 애플리케이션일 뿐입니다. 다른 어떤 언어에서는 각 프로바이더를 위한 공통 인터페이스를 정의하고 단순히 메인 애플리케이션에 클래스나 구조체를 로드할 뿐입니다. 네트워킹 스택이 필요한 IPC 방법을 사용하면서 전체 프로세스를 생성하는 것이 아닙니다!

Go는 제네릭과 slices.Contains()와 같은 편의 기능과 같이 처음부터 갖추어져야 했던 새로운 기능을 차츰 추가해왔지만, Go의 지도 팀은 너무 변화를 꺼려하여 나는 이 언어의 장기적인 방향을 믿지 못합니다.

## 자바

자바는 자바스크립트와 마찬가지로, 원래의 형태와는 구별이 어려울 정도로 여러 차례 개조되고 변형되어온 오래된 언어입니다. 처음에는 가정용 엔터테인먼트 시스템을 위해 설계되었지만, 지금은 포춘 500 대 기업 및 아직 앱릿을 개발하는 3명의 사람을 위한 표준 기업용 언어로 변모했습니다.

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

자바의 장점 중 하나는 가상 머신(JVM)입니다. 이는 자바 바이트코드와 기계 코드 사이의 변환 계층이며, 동일한 코드를 상상할 수 있는 거의 모든 하드웨어나 플랫폼에서 실행할 수 있게 해줍니다. JVM이 모든 번역을 처리하기 때문에 ARM 기반 임베디드 장치를 위해 작성한 코드가 인텔 PC나 Apple Silicon Mac에서 실행되어 비싼 테스트 준비나 복잡한 가상화의 필요성이 없어집니다. 그러나 이 접근 방식의 단점은 속도입니다. Java 앱은 손수 사용, JIT 컴파일러 조정 및 선행 조치 없이는 "빠르지" 않을 수 있습니다. Spring과 같은 프레임워크는 매크로 프로그래밍과 코드 생성으로 인한 오버헤드를 더합니다.

자바가 "나쁨"이라기보다는 타 세미콜론 사용 이외에는 별로 "나쁜" 요소가 없습니다. 그러나 "좋은" 요소도 많지 않기 때문에, 프로그래밍 언어 중에서 니켈백(Nickelback)같은 존재입니다. 우리가 그것을 포기하고 대신 새롭고 대체할 수 있는 것으로 다시 시작한다면 모두가 더 나은 결과를 얻을 것이라고 생각합니다.

# 새로운 언어

이제 일반적인 언어들이 모두 어떤 면에서 약간 불만족스럽다는 점에 대해 이야기했으니, 가상의 새 언어에 대해 생각해보는 재미 있는 부분에 대해 이야기해보겠습니다.

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

먼저, 우리 언어가 다른 것들과 뚜렷히 구별되는 몇 가지 주요 원칙 또는 기능을 정의해야 한다고 생각해요:

- 읽고 학습하기 쉬움 — Python과 같이 쉽게 시작하고 설정할 수 있으며 천천히 심층적인 내용에 녹아 들 수 있어야 해요.
- 어디서나 사용 가능해야 함 — 특정 서버나 프로세서 몇 개로 한정되지 않고 어디서든 실행할 수 있어야 해요.
- 병행 처리가 쉬워야 함

그렇다면 이를 실제로 적용하면 어떻게 될까요?

가장 먼저 할 일은 Java의 JVM 개념을 채택하는 것이 좋을 것 같아요. 그러나 한 가지 중요한 점을 바꿔야 해요: JVM과 실행 환경을 애플리케이션과 함께 번들로 제공할 거에요. 네, 이는 컴파일 시 타겟 아키텍처를 지정해야 하는 번거로움이 있겠지만, 컴파일 시 최적화와 정적 링킹을 통해 불필요한 코드를 제거할 수 있어 Java 9+의 사용자 지정 JRE 번들링과 유사한 방식으로 우리에게 Java보다 사이즈와 성능의 장점을 제공할 거에요.

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

일반적으로 Java, Go, JS 등에서 채택한 C 스타일 구문을 유지하는 것이 좋을 것 같아요. 다만 세미콜론은 필요 없으니 걱정 안 하셔도 돼요. 세미콜론 팬이신 분들도 계시겠지만, 매 줄마다 ; 를 입력하는 것은 짜증나고 불필요한 일이에요.

Python에서는 편의 기능과 강력한 표준 라이브러리를 차용하고 싶어요. Python은 편의와 아름다움에 초점을 맞춘 언어인데, 이런 유용한 메서드와 함수들이 다른 많은 언어에서 소홀히 여겨지는 경우가 많아요. 특히 더 "진지한" 언어들에서 말이죠.

Go에서는 확실히 병행성 모델을 가져와야 해요. 코루틴, 웨이트그룹, 채널을 통해 병행 작업을 효과적으로 처리하고 관리할 수 있어요. 코루틴에 대해 좀 더 제어할 수 있는 기능을 원해요. 시작하면 완벽한 코드를 작성하거나 루틴 자체에 어떤 종류의 안전장치를 구현해야 해요. 현재 실행 중인 코루틴을 볼 수 있고 필요하다면 강제로 중지시킬 수 있는 방법이 있으면 좋겠죠.

또한 다중 코어 시스템의 장점을 활용할 수 있는 능력도 중요해요. 어떤 언어는 이를 더 잘 처리하지만, 응용 프로그램에서 사용 가능한 모든 컴퓨팅 자원과 메모리를 활용할 수 있는 것이 저희 새 언어의 장기적인 성공에 필수적이에요.

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

Go와 다르게, 의미를 전달하기 위해 관행에 의존하는 대신 명시적으로 해야 한다고 생각해요. 이것은 메서드, 변수, 속성 등에 public 또는 export와 같은 키워드를 사용하는 것을 의미합니다.

패키지 관리는 중요하며, Rust가 우리에게 가르쳐 준 것 중 하나는 견고한 패키지 관리 생태계의 중요성입니다. 우리 언어는 자체 패키지 관리자와 패키지 저장소를 갖추고 제 1일에 출시될 것입니다. Go나 Deno와 같은 몇 가지 최신 언어와는 달리, 저는 패키지에 대해 GitHub나 다른 버전 관리 시스템에 직접 의존하고 싶지 않아요.

또한 Rust처럼, 컴파일러가 자세하고 합리적인 피드백과 오류 메시지를 제공하는 것이 중요합니다. Go 프로그램에서 문제를 만난 후에 암호화되고 도움이 되지 않는 오류 메시지를 받는 것보다 더 답답한 일은 없어요.

JavaScript처럼, 우리 언어는 클라이언트 및 서버 측에서 모두 실행할 수 있는 것이 좋아요. 그것은 WebAssembly(또는 V8!)를 컴파일하는 것 중 하나가 되며, 브라우저를 타겟으로 하는 프로그램을 작성하는 데 필요한 네이티브 지원을 갖춰야 합니다.

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

컴파일된 애플리케이션은 정적으로 링크될 것이지만, 여전히 동적 링킹과 심볼 임포트를 지원해야 합니다. 이는 Go가 할 수 없는 확장성과 유연성을 제공할 것입니다.

마지막으로, 그리고 아마도 가장 중요한 것은 파이썬의 문화와 방향을 받아들이고 싶다는 점입니다. 파이썬은 코드 스타일부터 새로운 기능에 이르기까지 모든 것이 파이썬 향상 제안(PEP)으로 자세히 설명되며, 공개적으로 투표를 거쳐 최종 채택이나 거부 여부를 결정하기 위해 이사회로 회부됩니다. 이러한 접근은 파이썬이 스타일(PEP-8)과 같은 것들을 표준화하는 데만 그치지 않고, 사용자가 원하는 대로 새로운 기능을 추가하고 최신 유지할 수 있도록 했습니다. 다만 합리적인 범위 내에서요.

이 새로운 언어를 만드는 데 도움을 주고 싶다면, 댓글을 남겨주세요. 또한 빠뜨린 기능이나 원하는 기능을 댓글로 알려주세요!

## 제가 하는 일을 좋아하시고 어떤 방식으로든 도움을 주고 싶으시다면, 커피 한 잔 사주세요!

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

## ☕ [https://ko-fi.com/peterfraedrich](https://ko-fi.com/peterfraedrich)
