---
title: "윈도우 서버 2022에 KB5034439을 설치하려고 할 때 발생하는 0x80070643 오류 해결 방법"
description: ""
coverImage: "/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_0.png"
date: 2024-05-23 15:25
ogImage:
  url: /assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_0.png
tag: Tech
originalTitle: "Error 0x80070643 when installing KB5034439 on Windows Server 2022."
link: "https://medium.com/@hasanudin31113/error-0x80070643-when-installing-kb5034439-on-windows-server-2022-32b7abd74eb3"
---

안녕하세요, 지난주 발생한 문제에 대해 이야기하려고 해요.

Windows Server 2022에서 Windows 업데이트를 확인했을 때 다음과 같은 오류가 있었어요:

![에러 이미지](/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_0.png)

그리고 KB5034439를 설치하는 동안 오류가 발생했음을 확인했는데, 해결해야 할 여러 제안이 있었지만 문제를 해결하지 못했어요.

<div class="content-ad"></div>

그리고 마침내 그 문제를 해결할 수 있는 올바른 참조를 찾았어요. 이제 아래 단계를 따라주세요.

# 복구 파티션을 수동으로 크기 조정하기

- 관리자 권한으로 명령 프롬프트 창(cmd)을 엽니다.

![이미지](/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_1.png)

<div class="content-ad"></div>

2. WinRE가 설치된 경우, WinRE 디렉토리 경로가 있는 "Windows RE 위치"가 있어야 합니다. WinRE 상태를 확인하려면 reagentc /info를 실행하세요. 예시: "Windows RE 위치:

\\?\GLOBALROOT\device\harddisk0\partition4\Recovery\WindowsRE

![이미지](/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_2.png)

3. WinRE 비활성화를 위해 reagentc /disable를 실행하세요"

<div class="content-ad"></div>


![Error Screenshot](/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_3.png)

4. Shrink the OS partition and prepare the disk for a new recovery partition.
a. To shrink the OS, run diskpart

![Error Screenshot](/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_4.png)

b. Run list disk


<div class="content-ad"></div>


![Error message](/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_5.png)

c. To select the OS disk, run `sel disk OS disk index`. This should be the same disk index as WinRE.

![Error message](/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_6.png)

d. To check the partition under the OS disk and find the OS partition, run `list part`


<div class="content-ad"></div>


![Error Image 1](/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_7.png)

e. To select the OS partition, run `sel part OS partition index`

![Error Image 2](/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_8.png)

f. Run `shrink desired=250 minimum=250`


<div class="content-ad"></div>


![Error Screenshot 9](/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_9.png)

g. To select the WinRE partition, run `sel part WinRE partition index`

![Error Screenshot 10](/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_10.png)

h. To delete the WinRE partition, run `delete partition override`


<div class="content-ad"></div>


![이미지](/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_11.png)

5. 새 복구 파티션을 생성합니다.

a. 먼저, 디스크 파티션 스타일이 GUID Partition Table (GPT) 또는 Master Boot Record (MBR)인지 확인합니다. 이를 확인하려면 list disk를 실행합니다. "Gpt" 열에 별표(*)가 있는지 확인합니다. 별표(*)가 있는 경우 드라이브가 GPT이고, 그렇지 않으면 MBR입니다.

![이미지](/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_12.png)


<div class="content-ad"></div>

1. GPT 디스크인 경우, create partition primary id=de94bba4-06d1-4d40-a16a-bfd50179d6ac 명령을 실행한 다음 gpt attributes =0x8000000000000001 명령을 실행하세요.

![image](/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_13.png)

2. MBR 디스크인 경우, create partition primary id=27 명령을 실행하세요.

3. 파티션을 포맷하려면, format quick fs=ntfs label="Windows RE tools" 명령을 실행하세요.

<div class="content-ad"></div>


<img src="/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_14.png" />

6. To confirm that the WinRE partition is created, run `list vol`

<img src="/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_15.png" />

7. To exit from diskpart, run `exit`


<div class="content-ad"></div>


![Error Image 1](/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_16.png)

8. To re-enable WinRE, run `reagentc /enable`

![Error Image 2](/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_17.png)

9. To confirm where WinRE is installed, run `reagentc /info`


<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_18.png" />

알겠어요, 프로세스가 완료되었습니다. 이제 Windows 업데이트를 다시 실행하고 성공했는지 확인해보세요.

<img src="/assets/img/2024-05-23-Error0x80070643wheninstallingKB5034439onWindowsServer2022_19.png" />

이게 도움이 되기를 바라며, 행운을 빕니다.

<div class="content-ad"></div>

참고문헌:
