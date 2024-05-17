---
title: "리눅스 68에서 사용자 정의 시스템 호출 만들기"
description: ""
coverImage: "/assets/img/2024-05-17-CreateCustomSystemCallonLinux68_0.png"
date: 2024-05-17 18:49
ogImage: 
  url: /assets/img/2024-05-17-CreateCustomSystemCallonLinux68_0.png
tag: Tech
originalTitle: "Create Custom System Call on Linux 6.8"
link: "https://medium.com/@aryan20/create-custom-system-call-on-linux-6-8-126edef6caaf"
---



여태껏 사용자 정의 시스템 호출을 만들어보고 싶으셨나요? 과제를 위해서든, 즐거움을 위해서든, 또는 커널에 대해 더 많이 배우기 위해서든, 시스템 호출은 우리 시스템에 대해 더 많이 알아갈 수 있는 멋진 방법입니다.

# 이 안내에 따를 이유는?

이 주제에 대한 다양한 안내서가 있지만, 커널 개발 속도의 문제 때문에 문제가 발생합니다. 대부분의 안내서들은 오래되었고 다양한 오류를 발생시키기 때문에, 저는 이 포스트를 오류를 경험하고 그것들을 해결한 후에 작성하게 되었습니다 :)

## 커널 컴파일을 위한 시스템 설정

<div class="content-ad"></div>

Red Hat / Fedora / Open Suse 기반 시스템에서는 아래와 같이 간편하게 실행할 수 있어요.

```bash
sudo dnf builddep kernel
sudo dnf install kernel-devel
```

Debian / Ubuntu 기반 시스템에서는

```bash
sudo apt-get install build-essential vim git cscope libncurses-dev libssl-dev bison flex
```

<div class="content-ad"></div>

## 커널 가져오기

커널 소스 트리를 복제하세요. 특히 6.8 브랜치를 복제할 것이지만, 지침은 커널 개발자가 프로세스를 변경할 때까지 최신 브랜치에서도 작동해야 합니다.

```js
git clone --depth=1 --branch v6.8 https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
```

이상적으로, 복제된 버전은 현재 사용 중인 커널 버전과 같거나 더 높아야 합니다.

<div class="content-ad"></div>

현재 커널 버전은 다음 명령어를 통해 확인할 수 있어요

```bash
uname -r
```

## 새로운 시스템 호출 생성

다음을 수행해주세요

<div class="content-ad"></div>

```markdown
```js
cd linux
make mrproper
mkdir hello
cd hello
touch hello.c
touch Makefile
```

이 명령은 다운로드한 커널 소스 코드 내에 "hello"라는 폴더를 만들고, 그 안에 hello.c(시스템 호출 코드)와 Makefile(컴파일 규칙) 두 개의 파일을 생성합니다.

좋아하는 텍스트 편집기에서 hello.c를 열고 다음 코드를 넣어주세요.

```js
#include <linux/kernel.h>
#include <linux/syscalls.h>

SYSCALL_DEFINE0(hello) {
    pr_info("Hello World\n");
    return 0;
}
```

<div class="content-ad"></div>

커널 로그에 "Hello World"가 출력됩니다.

우리는 단순히 프린트만 할 것이기 때문에 n=0을 사용합니다.

이제 다음을 Makefile에 추가해주세요

```markdown
obj-y := hello.o
```

<div class="content-ad"></div>

이제

```js
cd ..
cd include/linux/
```

이 디렉토리 안에서 "syscalls.h" 파일을 열고 다음을 추가하세요.

```js
asmlinkage long sys_hello(void)
```

<div class="content-ad"></div>

<img src="/assets/img/2024-05-17-CreateCustomSystemCallonLinux68_0.png" />

이것은 이전에 만든 syscall 함수에 대한 프로토타입입니다.

커널 루트 내의 "Kbuild" 파일을 열고 (cd ../..) 맨 아래에 다음을 추가하십시오.

```js
obj-y += hello/
```

<div class="content-ad"></div>

아래는 우리가 포함한 새 폴더도 컴파일하도록 커널 빌드 시스템에 지시합니다.

이 작업이 완료되면, 아키텍처별 테이블에 시스콜 항목도 추가해 주어야 합니다.

각 CPU 아키텍처는 특정 시스콜을 가질 수 있으며, 우리의 시스콜이 어떤 아키텍처를 위해 만들어졌는지 알려주어야 합니다.

<div class="content-ad"></div>

x86_64 아키텍처에서 파일은

```js
arch/x86/entry/syscalls/syscall_64.tbl
```

빈번호를 사용하고 테이블 주석에서 금지된 번호를 사용하지 않도록 유의하여 시스콜 항목을 추가하세요.

제가 사용 가능한 번호 중 462번이 비어 있어서 새 항목을 다음과 같이 추가했습니다.

<div class="content-ad"></div>

```js
462 공통 안녕 sys_hello
```

<img src="/assets/img/2024-05-17-CreateCustomSystemCallonLinux68_2.png" />

여기서 462은 우리의 시스템 호출에 매핑되며 두 아키텍처에 대해 공통인 sys_hello로 명명된 hello 시스템 호출의 진입 함수(entry function)입니다.

## 새 커널을 컴파일하고 설치하기

<div class="content-ad"></div>

다음 명령을 실행하십시오.

주의: 이 안내를 따르더라도 시스템의 안전, 보안, 무결성과 안정성을 보장하지 않습니다. 여기 나열된 모든 지침은 제 경험이며 귀하의 시스템에서의 결과를 대표하지 않습니다. 조심히 주의하여 진행하십시오.

이제 법적인 부분은 끝냈으니, 계속해봅시다 ;)

```js
cp /boot/config-$(uname -r) .config
make olddefconfig
make -j $(nproc)
sudo make -j $(nproc) modules_install
sudo make install
```

<div class="content-ad"></div>

현재 부팅된 커널의 구성 파일을 복사하고, 빌드 시스템에 동일한 값을 사용하도록 요청하여 기본값을 설정합니다. 그런 다음 nproc에 의해 주어진 코어 수에 따라 병렬 처리를 사용하여 커널을 빌드합니다. 이후 사용자 정의 커널을 설치합니다 (자체 책임으로).

커널 컴파일에는 많은 시간이 소요되므로 커피 한 잔 또는 10잔을 마시며 터미널에서 스크롤되는 텍스트 줄을 즐기세요.

시스템 속도에 따라 몇 시간이 걸릴 수 있으므로 실제 소요 시간이 다를 수 있습니다. 이 단계에서 열이 치기도 할 수 있으니 온도를 확인하려면 팬이 소리를 지를 수도 있습니다 (내 경우에도 그렇었어요).

## 재미있는 부분, 새로운 시스템 호출 사용

<div class="content-ad"></div>

이제 시스템 호출이 우리의 커널에 통합되었으니 시스템을 재부팅하고 부팅 중에 grub에서 새로운 커스텀 커널을 선택해주세요

![image](/assets/img/2024-05-17-CreateCustomSystemCallonLinux68_3.png)

부팅 후 시스템 호출을 사용하는 C 프로그램을 작성해봅시다.

다음 내용을 가진 파일 "test.c"를 생성하세요.

<div class="content-ad"></div>

```js
#include <stdio.h>
#include <sys/syscall.h>
#include <unistd.h>
int main(void) {
  printf("%ld\n", syscall(462));
  return 0;
}
```

당신이 테이블에서 시스템 콜에 대해 선택한 번호로 462을 대체하세요.

프로그램을 컴파일하고 실행하세요.

```js
make test
chmod +x test
./test
```

<div class="content-ad"></div>

모두 정상적으로 진행되면, 터미널에서 "0"을 출력하고 시스콜 출력이 커널 로그에 표시됩니다.

다음 명령어를 사용하여 로그에 액세스하세요.

```bash
sudo dmesg | tail
```

그러면 원하는 시스템 콜 메시지가 출력된 것을 확인할 수 있습니다.

<div class="content-ad"></div>

만약 성공적으로 수행했다면 축하드려요 🎉 좋아요와 댓글이 있다면 감사하겠습니다 :) 꼭 여러분의 이야기를 공유해 주세요 :D

다시 한 번 아래 사항을 기억해 주세요:

- 커널을 컴파일하는 데 많은 시간이 걸립니다.
- 새로 컴파일한 커널은 상당한 용량을 차지하므로 저장 공간 확인이 필요합니다.
- 리눅스 커널은 코드 변경이 빠르게 이뤄집니다.