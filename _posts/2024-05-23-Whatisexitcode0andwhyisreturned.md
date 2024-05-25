---
title: "이번 글에서는 종료 코드 0이란 무엇이며 왜 반환되는지에 대해 알아보겠습니다"
description: ""
coverImage: "/assets/img/2024-05-23-Whatisexitcode0andwhyisreturned_0.png"
date: 2024-05-23 15:11
ogImage: 
  url: /assets/img/2024-05-23-Whatisexitcode0andwhyisreturned_0.png
tag: Tech
originalTitle: "What is exit code 0 and why is returned?"
link: "https://medium.com/gitconnected/what-is-exit-code-0-and-why-is-returned-13735b758f9e"
---


프로그래밍의 복잡한 세계에서는 종료 코드를 이해하는 것이 코드 실행의 동작과 결과를 이해하는 데 중요합니다. 이 연구에서는 종료 코드 0의 중요성을 해석하여 다양한 프로그래밍 언어에서의 영향을 알아보고, 프로그램 종료의 기본 메커니즘을 밝힐 것입니다.

다음 예시를 살펴봅시다:

```js
int main() {
  return 0
}
```

<div class="content-ad"></div>

```js
gcc main.c -o main
```

이 간단한 C 프로그램에서 return 0; 문은 종료 코드 0으로 프로그램을 종료함을 나타냅니다.

비슷한 맥락에서, Node.js에서 종료 코드가 처리되는 방법을 살펴보죠:

```js
process.exit()
```

<div class="content-ad"></div>

이번 탐구에서 우리는 bash의 유용한 기능에 초점을 맞춰볼 거에요. 이 기능을 통해 우리는 명령어나 프로그램의 이전 반환 상태를 확인할 수 있어요.

```js
echo $?
```

두 프로그램을 실행한 결과 모두 0이에요.

# 종료 코드 0: 성공의 보편적 상징

<div class="content-ad"></div>

다양한 프로그래밍 언어, C 및 파생 버전을 포함하여, 0의 종료 코드는 오류 없이 성공적으로 실행되었음을 나타냅니다. 프로그램이 기대대로 작업을 완료하면 종료 코드 0을 반환하여 최종 사용자에게 모든 것이 순조롭게 진행되었다는 것을 알립니다. 이 관례는 스크립트 및 자동화된 프로세스가 프로그램이 성공적으로 실행되었는지 또는 문제가 발생했는지를 확인할 수 있도록 합니다.

하지만 아마도 이 프로그램이 어떻게 로드되는지 궁금할 것입니다. 알아보려고 해 봅시다. 우리 바이너리를 분해할 수 있는 objdump를 사용할 수 있습니다. 다음 명령어를 사용하십시오:

```js
objdump -d -M intel main
```

<div class="content-ad"></div>

우리의 여정은 _start에서 시작되어 __libc_start_main을 거쳐 Linux에서 시스템 호출인 _exit으로 끝납니다.

https://man7.org/linux/man-pages/man2/_exit.2.html

운영 체제는 프로세스를 생성하고 종료하는 책임이 있음을 알 수 있습니다. Linux 커널에서 우리는 아래 이미지에서 보여지는 것처럼 프로세스를 시각화할 수 있습니다.

![프로세스 시각화](/assets/img/2024-05-23-Whatisexitcode0andwhyisreturned_1.png)

<div class="content-ad"></div>

Linux에서 사용되는 다른 내부 코드들을 언급할 수 있어요.

```js
#define EPERM   1 /* Operation not permitted */
#define ENOENT   2 /* No such file or directory */
#define ESRCH   3 /* No such process */
#define EINTR   4 /* Interrupted system call */
#define EIO   5 /* I/O error */
#define ENXIO   6 /* No such device or address */
#define E2BIG   7 /* Argument list too long */
#define ENOEXEC   8 /* Exec format error */
#define EBADF   9 /* Bad file number */
#define ECHILD  10 /* No child processes */
#define EAGAIN  11 /* Try again */
#define ENOMEM  12 /* Out of memory */
#define EACCES  13 /* Permission denied */
#define EFAULT  14 /* Bad address */
#define ENOTBLK  15 /* Block device required */
#define EBUSY  16 /* Device or resource busy */
#define EEXIST  17 /* File exists */
#define EXDEV  18 /* Cross-device link */
#define ENODEV  19 /* No such device */
#define ENOTDIR  20 /* Not a directory */
#define EISDIR  21 /* Is a directory */
#define EINVAL  22 /* Invalid argument */
#define ENFILE  23 /* File table overflow */
#define EMFILE  24 /* Too many open files */
#define ENOTTY  25 /* Not a typewriter */
#define ETXTBSY  26 /* Text file busy */
#define EFBIG  27 /* File too large */
#define ENOSPC  28 /* No space left on device */
#define ESPIPE  29 /* Illegal seek */
#define EROFS  30 /* Read-only file system */
#define EMLINK  31 /* Too many links */
#define EPIPE  32 /* Broken pipe */
#define EDOM  33 /* Math argument out of domain of func */
#define ERANGE  34 /* Math result not representable */
```

# 결론

C의 main 함수나 Node.js나 다른 프로그래밍 언어에서 process.exit를 사용할 때, 프로그래머가 정의한 종료 코드로 프로세스를 닫기 위해 _exit 시스템 호출이 내부적으로 사용됩니다. 그러나 프로그래머로서 여러분은 프로세스의 적절한 종료와 종료된 프로세스에 대한 사용자에게 가장 좋은 결과를 제공하기 위한 책임이 있습니다.