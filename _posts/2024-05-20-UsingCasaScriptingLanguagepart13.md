---
title: "C를 스크립팅 언어로 활용하기, 제13부"
description: ""
coverImage: "/assets/img/2024-05-20-UsingCasaScriptingLanguagepart13_0.png"
date: 2024-05-20 16:32
ogImage: 
  url: /assets/img/2024-05-20-UsingCasaScriptingLanguagepart13_0.png
tag: Tech
originalTitle: "Using C++ as a Scripting Language, part 13"
link: "https://medium.com/@fwsgonzo/using-c-as-a-scripting-language-part-13-cb99c38205d9"
---


힙 관련 함수 최적화

게임을 만들 때 병목 구간이 되지 않는 많은 부분이 있습니다. 그런 섹션에서는 종종 안전하고 표준적인 코드를 작성합니다. 보통 임시 힙 할당이 포함된 코드입니다.

프로그램이 무엇을 하는지 모니터링할 수 있는 heap 및 다른 시스템 호출에 대한 상세한 설정이 있습니다. 그러나 실제 이야기는 RISC-V 어셈블리를 읽는 데 있습니다. C++에서 new를 호출하면 먼저 빈 래퍼로 이동한 다음 실제 C++ new 함수로 이동합니다. 그러면 다시 내 힙 시스템 호출 래퍼를 호출하고 최종적으로 시스템 호출을 호출합니다. C++에서는 new가 할당이 실패하면 예외를 throw할 수 있지만 여기에선 무시됩니다. 모든 것을 제어하고 있기 때문입니다. 할당에 실패하면 에뮬레이터가 예외를 throw합니다. 메모리가 부족하거나 실행 시간이 너무 길어지면 마찬가지입니다. 그러므로 new 호출 체인을 피하고 최상의 경우에는 직접 시스템 호출을 호출하고 싶습니다.

이에 대한 간단한 벤치마크를 만들었습니다:

<div class="content-ad"></div>

```md
Benchmarking it, it took around 50ns. That’s not too bad. But, it can be improved just by avoiding all the calls that do nothing but call another function.

So, the first thing to do is to call the system call wrapper directly. This meant that I had to forego the return value from free, because in C the free function doesn’t have a return value. Otherwise the A0 register would be clobbered, and I would have a very mysterious bug on my hands. Running it, I found that it heavily reduced the run-time, now at 31ns. A 38% run-time reduction.

The last thing to try, was to write inline functions for new and delete, which would call my inline assembly functions sys_malloc and sys_free:
```

<div class="content-ad"></div>

```js
인라인 void* sys_malloc(std::size_t size) {
  register void*   ret asm("a0");
  register size_t  a0  asm("a0") = size;
  register long syscall_id asm("a7") = SYSCALL_MALLOC;

  asm volatile ("ecall"
  : "=m"(*(char(*)[size]) ret), "=r"(ret)
  : "r"(a0), "r"(syscall_id));
  return ret;
}
인라인 void  sys_free(void* ptr)
{
  register void*  a0  asm("a0") = ptr;
  register long syscall_id asm("a7") = SYSCALL_FREE;

  asm volatile ("ecall"
  :
  : "r"(a0), "r"(syscall_id));
}

이제 ret이 레지스터 A0을 재할당하는 것을 명시하는 것을 기억해야 했지만, 모든 테스트, 벤치마크 그리고 제 게임에서 모두 잘 실행되었습니다. 지금까지 잘 되고 있어요. 벤치마크 실행 시간은 미친 듯한 19ns로 나왔어요.

0000000050000b94 <_ZL16bench_alloc_freev>:
    50000b94:   40000513                li      a0,1024
    50000b98:   23a00893                li      a7,570
    50000b9c:   00000073                ecall
    50000ba0:   00050663                beqz    a0,50000bac <_ZL16bench_alloc_freev+0x18>
    50000ba4:   23d00893                li      a7,573
    50000ba8:   00000073                ecall
    50000bac:   00008067                ret

어셈블리를 살펴보니, 완벽해 보이고, 더 이상 개선할 수 없을 것 같아요. 실행 속도가 너무 빨라서 당연히 이렇게 되는 거예요. 거의 네이티브 성능에 가깝습니다.
```

<div class="content-ad"></div>

```markdown
![이미지](/assets/img/2024-05-20-UsingCasaScriptingLanguagepart13_0.png)

wasmtime을 malloc() - free() 콤보로 실행했고, 평균 66ns가 걸린 것을 발견했어요. 48ns의 호출 오버헤드를 뺀 18ns의 런타임이 편안하게 나왔어요. 정말 빠른 속도죠. 이제는 제가 만든 힙 할당기가 문제인지 궁금해졌어요. 아마 맞겠죠? 지배적일 것입니다. 여기 차트가 있어요:

![차트](/assets/img/2024-05-20-UsingCasaScriptingLanguagepart13_1.png)

더 빠른 힙 할당기를 작성할 에너지가 없을 것 같아요. 특히, 내 힙 할당기가 견고하고 잘 테스트되어 있을 때 말이에요. 오랜 시간 잘 돌아가다가 이상한 메모리 조각화도 그리 심하게 일어나지 않아요. 게임을 만드는 것이 중요한데, 그럴 때 힙 할당기 같은 기본 할당기를 바꾸는 게 무서울 때가 있죠.
```  

<div class="content-ad"></div>

그래도 괜찮네요. 제가 가까이 왔군요!

-곤조