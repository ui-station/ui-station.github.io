---
title: "sh와 Bash의 차이점"
description: ""
coverImage: "/assets/img/2024-05-20-WhatstheDifferenceBetweenshandBash_0.png"
date: 2024-05-20 17:56
ogImage:
  url: /assets/img/2024-05-20-WhatstheDifferenceBetweenshandBash_0.png
tag: Tech
originalTitle: "What’s the Difference Between sh and Bash?"
link: "https://medium.com/@shalinpatel./whats-the-difference-between-sh-and-bash-f8fa6b2cd9f0"
---

<img src="/assets/img/2024-05-20-WhatstheDifferenceBetweenshandBash_0.png" />

# 1. 개요

이 튜토리얼에서는 sh와 Bash 간의 차이점과 제공하는 기능에 대해 알아볼 것입니다. 마지막으로 쓸 쉘에 대해 논의할 것입니다.

# 2. 쉘이란 무엇인가요?

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

쉘은 사용자가 명령을 입력하고 해석한 다음 처리하기 위해 운영 체제로 전달하는 컴퓨터 프로그램입니다. 따라서 사용자와 운영 체제 사이의 인터페이스 역할을 하며 사용자가 컴퓨터와 상호 작용할 수 있게 해줍니다. 쉘과 상호 작용하기 위해서는 gnome-terminal, konsole 또는 st와 같은 터미널 에뮬레이터가 필요합니다.

대부분의 리눅스 기반 운영 체제에는 적어도 하나의 쉘 프로그램이 제공됩니다. 쉘 프로그램은 대부분 Dash, Bash 또는 둘 다일 것입니다.

## 2.1. 현재 사용 중인 쉘

우리는 /etc/passwd 파일을 grep을 사용해서 간단히 읽어 현재 사용 중인 쉘을 확인할 수 있습니다:

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

```js
$ grep $USER /etc/passwd
hey:x:1000:998::/home/hey:/bin/bash
```

- 그렙 명령은 패턴과 읽을 파일을 예상합니다.
- $USER 환경 변수는 현재 로그인한 사용자로 확장됩니다.
- /etc/passwd 파일은 사용자 계정 정보를 저장합니다.

해당 명령을 실행하여 기본 셸이 bash 임을 확인할 수 있습니다. 따라서 터미널 에뮬레이터를 사용할 때는 우리의 명령이 bash에 의해 해석될 것입니다.

# 2.2. 설치된 셸

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

저희가 현재 설치된 모든 셸을 확인하려면 /etc/shells 파일을 읽어보면 됩니다:

```bash
$ cat /etc/shells
# 유효한 로그인 셸의 경로
# 자세한 내용은 shells(5)를 참조하세요.
```

```bash
/bin/sh
/bin/bash
/bin/dash
```

저희는 기계에 셸이 세 개 설치되어 있는 것을 확인할 수 있습니다. 기술적으로, 두 개의 셸만 있지만 다음 섹션에서 그 이유를 알아볼 것입니다.

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

# 3. sh

sh은 또한 Bourne Shell로 알려져 있으며 POSIX 표준에 따라 정의된 UNIX 유사 시스템용 명령 프로그래밍 언어입니다. sh는 키보드 또는 파일(일반적으로 스크립트 파일이라고 함)에서 입력을 받을 수 있습니다. 대부분의 Linux 시스템에서는 원래 Bourne Shell, dash 및 ksh와 같은 프로그램을 통해 구현됩니다.

# 3.1. POSIX 시스템의 sh

POSIX는 IEEE에서 정의한 표준 군으로, 공급 업체들이 운영 체제를 호환되도록 만들기 위한 것입니다. 이것은 우리가 일련의 지침을 따라 다중 운영 체제를 위한 크로스 플랫폼 소프트웨어를 개발할 수 있도록 도와줍니다. sh는 이러한 표준을 준수합니다.

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

대부분의 Linux 시스템에서 sh은 Bourne Shell의 실제 구현으로의 심볼릭 링크입니다. 다음 명령을 통해 확인할 수 있습니다:

```js
$ file -h /bin/sh
/bin/sh: symbolic link to dash
```

우리는 /bin/sh가 dash에 심볼릭 링크된 것을 볼 수 있습니다. 이는 Debian 기반 배포판에서 사용되는 POSIX 호환 쉘입니다. 쉘 스크립트에서는 #!/bin/sh를 첫 번째 줄에 넣을 수 있는데, 이렇게 하면 dash에서 실행됩니다:

```js
#!/bin/sh
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

```js
echo Hello, World!
```

스크립트는 #!/bin/sh 라인이 가리키는 대로 실행됩니다. 우리의 경우에는 dash가 될 것입니다. 따라서 우리의 스크립트는 POSIX 표준을 준수하기 때문에 다른 POSIX 호환 운영 체제로 이식할 수 있습니다.

## 3.2. 흔한 실수

대부분의 셸 스크립트는 첫 번째 줄에 #!/bin/sh를 가지고 있지만, /bin/sh가 Bourne 호환 셸을 가리키는 심볼릭 링크일 수 없다는 점을 알아야 합니다. 때로는 스크립트 작성자들이 /bin/sh가 /bin/bash 또는 /bin/dash를 가리킨다고 가정하지만, 이러한 것은 필수적이지 않을 수 있습니다.

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

따라서 스크립트를 작성하고 실행하기 전에 /bin/sh의 유형을 확인하는 것이 좋은 실천입니다.

## 4. Bash

sh와 마찬가지로 Bash(Bourne Again Shell)는 명령어 언어 처리기이자 쉘입니다. 대부분의 Linux 배포판에서 기본 로그인 쉘이며 sh의 기능을 지원하면서 그 이상의 확장 기능을 제공하는 sh의 상위 집합입니다. 그러나 대부분의 명령은 sh에서와 유사하게 작동합니다.

Bash가 출시된 이후, Linux 운영 체제의 사실상 표준 쉘로 사용되어 왔습니다.

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

# 4.1. Bash를 POSIX 규격 셸로 사용하기

오랜 시간 동안 /bin/sh은 /bin/bash로 링크되어 있었습니다. 시간이 지남에 따라 bash는 더 많은 기능과 확장 기능을 개발했는데, 이 중 일부는 POSIX 표준을 준수하지 않았습니다. 그 결과, 대부분의 Linux 배포판에서는 더 이상 옵션으로 사용되지 않았고, 다른 POSIX 규격 셸이 대신 사용되었습니다.

그러나 여전히 Bash를 POSIX 규격 모드에서 사용할 수 있으며, 이를 다음과 같이 bash 명령에 --posix 플래그를 사용하여 호출할 수 있습니다:

```bash
$ bash --posix
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

대안으로 Bash 스크립트를 POSIX 표준에 맞게 조정할 수도 있습니다:

```js
#!/bin/bash
```

```js
set -o posix
echo Hello, World
```

set 명령어는 스크립트 내에서 옵션을 활성화하는데, 이 경우에는 스크립트를 POSIX 모드로 실행하게 됩니다. 따라서 FreeBSD 및 UNIX와 같은 다른 운영 체제에서도 스크립트를 사용할 수 있게 만들어줍니다.

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

# 4.2. 특징

Bash는 현대적인 프로그래밍 언어와 많이 닮은 많은 유연성과 구문을 제공합니다. Bash에서 소개된 몇 가지 주목할만한 기능은 다음과 같습니다:

- `TAB` 키를 사용하여 빠르게 명령을 완전할 수 있는 명령행 완성
- `Up` 화살표 키 또는 `CTRL-R`을 사용하여 이전에 실행한 명령을 빠르게 검색하는 명령 히스토리
- 외부 프로그램을 사용하지 않고 산술 평가
- 문자열 인덱스와 배열을 만들 수 있는 연상 배열
- 명령행 편집을 위한 키보드 단축키
- 수정 가능성을 통해 Bash가 제공하는 기본 프레젠테이션을 수정할 수 있습니다

# 4.3. Bash 스크립트 작성

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

Bash는 파일에서 명령어를 읽을 수도 있습니다. 첫 번째 줄에 #!/bin/bash Shebang을 지정한 후에 스크립트를 작성합니다:

```shell
#!/bin/bash
```

```shell
# 숫자가 짝수인지 홀수인지 확인
read -p "숫자를 입력하세요: " number
if [ `expr $number % 2` -eq 0 ]; then
    echo "${number}은(는) 짝수입니다"
else
    echo "${number}은(는) 홀수입니다"
fi
```

스크립트 파일을 저장하고 실행 가능하도록 만들어주세요.

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

```sh
$ chmod +x is_even.sh
$ ./is_even
숫자를 입력하세요: 13
13는 홀수입니다
```

이 스크립트는 대부분의 리눅스 배포판에서 실행할 수 있지만, FreeBSD에서 동일한 스크립트를 실행하면 문제가 발생할 수 있습니다.

# 5. 어떤 것을 사용해야 할까요?

두 쉘 모두 유용하며, 다른 상황에서 활용할 수 있습니다. 예를 들어, sh를 사용하면 스크립트를 여러 시스템 간에 호환되게 할 수 있습니다. 반면에 Bash는 유연한 구문과 더 매력적인 기능을 제공하기 때문에 선택할 수도 있습니다.

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

그래도 휴대성과 호환성에 대해 걱정한다면 Bash를 사용하여 순수한 POSIX 모드에서 실행할 수 있습니다. 또한, sh 스크립트를 작성하면 대부분 변경하지 않고 Bash에서 실행될 것입니다. 왜냐하면 Bash는 sh와 하위 호환성이 있기 때문입니다.

# 6. 결론

본 문서에서는 셸 프로그램의 개요와 시스템에 현재 설치된 쉘을 어떻게 찾을 수 있는지 살펴보았습니다. 그리고 Bash와 sh 사이의 차이점과 기능 그리고 POSIX와의 일치에 대해 알아보았습니다.
