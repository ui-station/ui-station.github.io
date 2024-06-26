---
title: "유닉스 리눅스에서 ls 명령어 해부하기"
description: ""
coverImage: "/assets/img/2024-05-18-DemystifyingthelsCommandinUnixLinux_0.png"
date: 2024-05-18 17:28
ogImage:
  url: /assets/img/2024-05-18-DemystifyingthelsCommandinUnixLinux_0.png
tag: Tech
originalTitle: "Demystifying the “ls” Command in Unix Linux"
link: "https://medium.com/@silentstorm29/demystifying-the-ls-command-in-unix-linux-d63690b40ec2"
---

ls 명령어의 기능을 간소화했어요.

작은 이야기가 먼저!!!!

3년 넘게 매일 리눅스 시스템과 함께 일했어요. 로봇처럼 생각 없이 과제를 수행했죠. 셸 스크립트와 Python 코드를 사용해서 자동화 작업을 했어요. 그러나 2024년 1월 29일, 어떤 명령어들이 작동하는 방식에 대한 호기심을 자극하는 문제를 마주했어요. 이 호기심은 명령어들이 어떻게 내부적으로 작동하는지 고려해보지 않은 저에게 놀라운 충격이었어요.

누군가는 이 깨달음이 당연히 느껴질 수도 있겠지만, 제게는 진정한 성장의 순간이에요. 이 블로그를 지나치게 복잡하게 만들기보다, 현재 배우고 있는 것에 집중하고 싶어요. 저는 발견하는 모든 새로운 사실을 문서화하기로 결심했어요.

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

제가 마주한 문제에 대해 이해하기 쉽게 설명하기 위해 나중 게시물에서 더 자세히 다룰 예정입니다. 여기서 설명하는 것이 가장 좋은 방법이 아닐 수 있으니 말이죠. 이제 첫 학습 경험을 향해 나아가 봅시다.

# 그럼... 시작해봅시다!!

설명에 따르면... Unix/Linux에서 ls Command 해석하기

```js
silentstorm29@cloudmaniac ~ % ls -larth
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

![image](/assets/img/2024-05-18-DemystifyingthelsCommandinUnixLinux_0.png)

이게 일반적으로 우리가 사용하는 거잖아요??

ls 명령어는 Unix 및 Unix와 유사한 운영 체제의 중요한 요소입니다. 이 명령 줄 유틸리티는 디렉토리의 내용을 나열하는데, 보다 많은 기능을 제공합니다. 파일 및 디렉토리의 속성을 보기 위한 다양한 옵션을 제공하는데, 이는 권한, 소유권, 크기 및 수정 시간을 포함합니다.

# ls 명령어의 힘을 발휘하기

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

ls 명령어는 다양한 방식으로 사용할 수 있는 다재다능한 명령어입니다:

- ls: 현재 디렉토리의 파일 및 디렉토리를 나열하는 간단한 방법입니다.
- ls -l: 자세한 목록 형식으로 파일 및 디렉토리를 공개합니다.
- ls -a: 숨겨진 파일 및 디렉토리를 모두 보여줍니다(점으로 시작하는 파일 포함).
- ls -lh: 파일 및 디렉토리를 크기가 사용자 친화적인 형식으로 자세한 목록 형식으로 표시합니다.
- ls /경로/디렉토리: 특정 디렉토리의 내용을 나열합니다.
  ls 명령어는 유닉스와 비슷한 환경에서 중요한 위치를 차지하며 파일 시스템 탐색과 파일 관리에 필수적입니다.

# 커튼 뒤: 시스템 호출

유닉스와 비슷한 운영 체제에서 ls 명령어를 실행하면 여러 시스템 호출이 발생합니다. 다음은 발생하는 마법의 간소화된 순서입니다:

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

- 쉘(예: bash 또는 zsh)은 새 프로세스, 즉 쉘 프로세스의 복제본을 생성하기 위해 fork() 시스템 호출을 사용합니다.
- 그런 다음, 새 프로세스는 exec() 시스템 호출을 사용하여 자신의 이미지를 ls 프로그램으로 대체하고 ls 프로그램을 메모리에 로드하여 실행을 시작합니다.
- ls 프로그램은 디렉토리 내용을 읽고 파일에 대한 정보를 가져오기 위해 여러 시스템 호출을 활용합니다. 이러한 시스템 호출에는 open(), read(), getdents(), stat(), lstat(), fstat() 등이 포함됩니다.
- ls 프로그램은 또한 write() 시스템 호출을 사용하여 디렉토리 목록을 표준 출력(일반적으로 여러분의 터미널)에 출력합니다.
- ls 프로그램이 작업을 완료하면 exit()를 호출하여 종료합니다. ls 프로그램이 완료될 때까지 대기했던 원래 쉘 프로세스는 wait() 시스템 호출을 통해 완료 알림을 받습니다.

# 옵션, 인수, 와일드카드 마스터하기

ls 명령은 옵션, 인수 및 와일드카드를 사용하여 동작을 수정할 수 있도록 유연하게 설계되어 있습니다. 각 구성 요소가 작동하는 방법을 살펴보겠습니다:

옵션:

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

옵션은 ls 명령어의 동작을 조절합니다. 예를 들어, -l은 자세한 형식으로 출력을 보여주고, -a는 숨겨진 파일을 포함한 모든 파일을 표시하며, -h는 파일 크기를 사람이 이해하기 쉬운 형식으로 표시합니다. ls 명령어는 이러한 옵션을 구문 분석하고 동작을 조정합니다. 이 과정에서 직접 시스템 호출을 수행하지는 않지만, 나중에 수행될 시스템 호출을 영향을 줄 수 있습니다. 예를 들어, -l 옵션은 ls가 각 파일에 대한 자세한 정보를 가져오기 위해 stat() 또는 lstat() 시스템 호출을 수행하도록 유도합니다.

인수:

인수는 일반적으로 ls가 작용해야 할 디렉터리 또는 파일의 이름입니다. 인수를 지정하지 않으면 ls는 현재 디렉터리의 내용을 나열합니다. 디렉터리 이름을 제공하면 해당 디렉터리의 내용을 나열합니다. 파일 이름을 제공하면 해당 파일만 표시합니다 (파일이 있는 경우). ls 명령어는 인수를 파일이나 디렉터리로 처리하여 나열합니다. 이때 인수가 디렉터리인지 파일인지 확인하기 위해 stat() 또는 lstat() 시스템 호출을 사용합니다. 디렉터리일 경우 ls는 open() 및 getdents() 시스템 호출을 사용하여 내용을 읽어옵니다.

와일드카드:

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

와일드카드는 다른 문자를 대표하는 기호입니다. 가장 흔한 와일드카드는 "_"로, 모든 문자의 어떤 개수를 나타냅니다. 예를 들어, ls _.txt는 .txt로 끝나는 모든 파일을 나열합니다. 또 다른 흔한 와일드카드는 "?"로, 어떤 하나의 문자를 나타냅니다. 예를 들어, ls ?.txt는 한 글자 이름을 가지고 .txt로 끝나는 모든 파일을 나열합니다. 쉘(=ls 명령어가 아닌)은 명령이 실행되기 전 와일드카드를 확장합니다. 예를 들어 ls _.txt를 실행하면 쉘은 현재 디렉토리에 있는 모든 .txt 파일의 이름으로 _.txt를 대체합니다. 이 과정에는 디렉터리 내용을 읽기 위해 open(), getdents(), close() 시스템 호출이 필요하며, 와일드카드 패턴에 따라 파일을 필터링하기 위해 stat() 또는 lstat()을 사용할 수도 있습니다. 와일드카드 확장 후, ls 명령이 확장된 인수와 함께 실행됩니다.

옵션, 인수, 와일드카드를 함께 사용하는 예시는 다음과 같습니다:

```js
silentstorm29@cloudmaniac ~ %ls -lh *.txt
```

<img src="/assets/img/2024-05-18-DemystifyingthelsCommandinUnixLinux_1.png" />

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

현재 디렉터리에서 .txt 파일을 자세한 형식으로 인간이 읽을 수 있는 파일 크기로 나열하는 이 명령어를 사용할 거에요.

# 마무리!!

`ls` 명령어의 내부 동작에 대해 파헤치는 것은 마술사의 마술 비밀을 발견하는 것과 같아요. 단순한 명령어가 정밀한 시스템 호출들이 조화롭게 작동하는 과정을 보는 것은 매우 흥미롭죠. 다음에 `ls`를 입력할 때 눈에 보이는 것보다 훨씬 많은 일이 벌어지고 있다는 것을 알게 될 거예요. 매일 사용하는 명령어 뒤의 마법을 계속 탐험하고 발견하세요 🔎. 질문은 댓글 섹션에서 환영합니다 — 즐거운 학습을 기대해요!

<img src="/assets/img/2024-05-18-DemystifyingthelsCommandinUnixLinux_2.png" />

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

잘 가!!
