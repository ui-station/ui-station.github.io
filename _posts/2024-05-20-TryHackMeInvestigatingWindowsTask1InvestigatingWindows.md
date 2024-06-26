---
title: "TryHackMe 윈도우 탐색 - 태스크 1 윈도우 탐색"
description: ""
coverImage: "/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_0.png"
date: 2024-05-20 18:17
ogImage:
  url: /assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_0.png
tag: Tech
originalTitle: "TryHackMe Investigating Windows — Task 1 Investigating Windows"
link: "https://medium.com/@haircutfish/tryhackme-investigating-windows-task-1-investigating-windows-da65f32cf67f"
---

윈도우 머신이 해킹되었어요. 당신의 임무는 이 윈도우 머신을 조사하고 해커가 무엇을 했는지 단서를 찾는 겁니다.

여기 도전 과제가 있어요. 이 도전 과제는 완전히 표지에 적힌 대로에요. 이전에 침해당한 윈도우 머신을 조사하는 것에 대한 몇 가지 도전 과제가 있어요.

RDP를 사용하여 머신에 연결해보세요. 해당 머신의 자격 증명은 다음과 같아요:

사용자명: Administrator
비밀번호: letmein123!

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

위에 있는 내용을 번역하면 다음과 같습니다.

# 아래의 질문에 대답해주세요

# 시작하기

작업을 시작하기 전에 먼저 몇 가지를 처리해야 합니다. 먼저, 가상 머신과 OpenVPN을 시작하겠습니다. 가상 머신의 경우, 작업 2로 돌아가서 녹색으로 표시된 "가상 머신 시작" 버튼을 클릭해주세요.

(해당 기기는 핑(ICMP)에 응답하지 않을 수 있으며 부팅이 완료되기까지 몇 분이 소요될 수 있습니다.)

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

\<img src="/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_0.png" />

OpenVPN을 사용할 때는 컴퓨터에서 프로그램을 실행한 후 슬라이더를 클릭하여 TryHackMe VPN 네트워크에 연결하세요.

\<img src="/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_1.png" />

컴퓨터에서 openVPN을 어떻게 가져오고 사용하는지 확실하지 않다면 TryHackMe Access로 이동하세요. 거기에서 필요한 파일 및 openVPN 다운로드 위치에 대한 정보를 얻을 수 있습니다.

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

이제 가상 머신이 실행되고 우리의 openVPN이 연결되었으므로 Active Directory 가상 머신에 RDP(원격 데스크톱)로 연결할 수 있습니다. 다음은 그 방법입니다. Windows 머신을 사용할 수 있습니다. 모든 Windows PC에는 이미 RDP가 설치되어 있습니다. RDP를 열려면 키보드에서 Windows 키를 누르세요. 시작 메뉴가 열릴 것이지만 해야 할 일은 "rdp"만 입력하면 됩니다. Windows가 자동으로 검색하여 한 가지 결과를 보여줄 것이고, 그것은 원격 데스크톱 연결입니다. 엔터를 눌러 열어주세요.

![RDP](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_2.png)

창이 팝업되어 원격 데스크톱 연결을 위한 화면이 나타납니다. 창의 왼쪽 하단에 "옵션 표시"라고 표시된 당근 모양이 있습니다. 해당 당근을 클릭하세요.

![Options](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_3.png)

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

창이 확장되어 사용자 이름을 추가할 수 있는 옵션이 제공됩니다. 먼저, 컴퓨터 필드에는 TryHackMe가 제공한 Active Directory 기계의 IP 주소를 입력합니다. 두 번째로, 사용자 이름 필드에 Administrator를 입력합니다(이렇게 입력하여 THM 도메인에 연결합니다). 마지막으로, 우리 기계에 연결하기 위해 오른쪽 하단의 연결 버튼을 클릭합니다.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_4.png)

창이 팝업되어 암호를 입력하라는 메시지가 표시됩니다. 따라서 암호 필드에 letmein123!을 입력한 후 확인을 누릅니다.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_5.png)

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

커야할 일을 확인하십시오. 불안한 메시지가 표시될 것이지만 안심하세요. 이겪은 원격 PC에 연결하려면 '예' 버튼을 클릭하면 됩니다.

![image](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_6.png)

그러면 Windows 데스크톱 환경으로 이동하게 됩니다.

![image](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_7.png)

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

화면 왼쪽 하단의 시작 아이콘을 클릭하세요. 시작 메뉴가 나타나면 Windows PowerShell로 이동해서 클릭하세요.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_8.png)

이제 아래의 질문에 대답하기 시작할 준비가 되었습니다.

## 윈도우 머신의 버전과 연도는 무엇인가요?

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

## 진행 방법:

PowerShell이 열렸으니 몇 가지 명령을 실행하여 정보를 얻어봅시다. 시작하려면 Get-ComputerInfo 명령을 사용하여 모든 시스템 정보를 나열할 수 있습니다.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_9.png)

우리는 이를 좁히어야 합니다. 원하는 정보를 알고 있다면 Get-ComputerInfo -Property “Os\*” 명령을 사용하여 해당 정보만 출력할 수 있습니다. 이렇게 하면 'Os'로 시작하는 것만 출력됩니다. 답은 첫 번째 출력 항목으로 확인할 수 있습니다.

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

![image](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_10.png)

Answer: Windows Server 2016

## Which user logged in last?

## How To:

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

먼저 LocalUser의 이름을 확인해야 합니다. 이를 위해 Get-LocalUser 명령을 사용할 수 있어요. 이 명령을 통해 LocalUser를 나열하고 작업할 수 있는 목록을 얻을 수 있어요.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_11.png)

이제 LocalUser 목록을 가지고 net user 'username' |findstr “Last” 명령을 사용해서 해당 사용자의 최근 로그인을 볼 수 있어요. 이 명령을 실행하면 사용자가 시스템에 마지막으로 로그인한 시간을 확인할 수 있어요. 정말 놀랄만한 답을 얻게 될 거에요.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_12.png)

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

답변: 관리자

## 존이 시스템에 마지막으로 로그인한 시간은 언제인가요?

답변 형식: MM/DD/YYYY H:MM:SS AM/PM

## 사용 방법:

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

이전 질문에서의 결과를 다시 살펴보세요. John을 보세요. 그 정담을 복사하여 답변란에 붙여넣기하세요. 월과 일 앞에 0 두 개를 추가하면 됩니다.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_13.png)

답변: 03/02/2019 5:48:32 PM

## 시스템이 처음 시작될 때 어떤 IP에 연결하나요?

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

## 방법:

답이 호스트 파일에 있는지 확인해보려면 호스트 파일을 살펴보세요. 파일 경로는 C:\ `Windows` System32 `drivers` 등입니다. 이 폴더 안에 들어가면 호스트 파일을 더블 클릭하세요.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_14.png)

호스트를 더블 클릭하여 열면 파일을 열 어떻게 할지를 물어보는 창이 나타납니다. 메모장을 클릭하세요.

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

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_15.png)

파일 안에서 무언가 이상한 점이 보입니다. 먼저, 많은 사이트들이 로컬 호스트를 가리키고 있고, 두 번째로 구글이 여기에 있습니다. DNS 독립을 시도하고 있는 것으로 보입니다. 백업 파일을 가지고 있거나 이전 상태로 되돌릴 수 있다면, 반드시 알 수 있을 것입니다. 레지스트리로 이동하여 이 부팅 프로그램이 어디에 연결되어 있는지 확인해보아야 할 것입니다.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_16.png)

이제 원격 데스크톱에서 Windows 키를 누르거나 시작 메뉴 아이콘을 클릭하세요. 단순히 regedit를 입력하면 결과가 하나만 표시됩니다. 해당 항목을 클릭하거나 Enter를 눌러주세요.

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

![image](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_17.png)

레지스트리 편집기 창이 나타납니다. 다음 경로를 따르세요. HKEY_LOCAL_MACHINE `SOFTWARE` Microsoft `Windows` CurrentVersion ` Run. 해당 위치에 도착하면 두 개의 값이 표시됩니다. UpdateSvc를 확인하면 원격 머신이 시작될 때 연결하는 IP 주소가 나타납니다.

![image](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_18.png)

답변: 10.34.2.3

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

## 어드민 권한을 갖고 있는 두 개의 계정은 무엇인가요?

답변 형식: username1, username2

## 방법:

관리자 그룹에 속한 사용자를 확인하려면 다음 명령을 사용할 수 있습니다. Get-LocalGroupMember -Group “Administrators”. 명령을 실행하려면 enter 키를 누르세요. 출력에서 정답을 확인할 수 있습니다. 정답을 얻기 위해 사용자를 변경해야 할 수도 있어요(저는 그랬어요).

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

아래는 Markdown 형식으로 변환한 내용입니다.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_19.png)

답변: Jenny, Guest

## 악성 스케줄된 태스크의 이름은 무엇인가요?

## 방법:

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

이 시스템에서 예약된 작업이 무엇인지 확인해 봅시다. 이를 위해 Get-ScheduledTask 명령을 사용하세요. 명령을 실행하려면 Enter 키를 누르세요.

![image](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_20.png)

예약된 많은 작업들을 확인할 수 있습니다. 이 목록을 좀 더 작게 만들어 봅시다. 이를 위해 Get-Scheduled | where '$\_.TaskPath -eq “\”' 명령을 실행할 수 있습니다. 이 명령은 '\' TaskPath를 가진 것만 출력하며, 목록을 여섯 개로 줄일 수 있습니다. 이 프로그램 중 하나가 악성인데, 그것이 무엇인지 찾을 수 있나요?

![image](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_21.png)

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

답변: 파일 시스템 정리

## 해당 작업은 매일 실행하려고 했던 파일은 무엇인가요?

## 방법:

명령을 $task에 대입하려면 다음 명령을 사용합니다. $task = Get-ScheduledTask | Where TaskName -EQ "Clean file system"을 입력하세요.

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

![Screenshot](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_22.png)

Now we can run the command `$task.Actions` to see what will execute when we run this program. Press enter to run the command, and get your answer.

![Screenshot](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_23.png)

Answer: nc.ps1

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

## 이 파일이 로컬로 수신 대기하는 포트는 무엇입니까?

## 방법:

이 답변을 보려면 이전 질문의 출력을 확인하고 포트는 그 위에 있는데, Arguments 열에 있습니다.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_24.png)

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

Answer: 1348

## 제니가 마지막으로 로그인한 시간은 언제였나요?

## 방법:

우리는 이 명령어를 이전에 실행했었지만, 정보를 다시 가져오기 위해 다시 실행할 수 있습니다. 명령어는 net user Jenny | findstr "Last" 입니다. 명령어를 실행하고 이 질문에 대한 답변을 얻으려면 엔터를 누르세요.

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

<img src="/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_25.png" />

대답: Never

## 언제 침해가 발생했나요?

대답 형식: MM/DD/YYYY

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

## 방법:

태스크 바에서 파일 탐색기 아이콘을 클릭하여 파일 탐색기를 엽니다. 창이 나타나면 로컬 디스크(C:)를 클릭하십시오.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_26.png)

이제 C 폴더 안에 있게 될 텐데, 폴더를 보면 눈에 띄는 날짜가 있을 것입니다. 여기에 속하지 않는 폴더도 보일 것입니다. 여러 번 나타나는 날짜가 정답입니다. THM으로 답을 입력할 때 월과 일 앞에는 0을 넣으십시오.

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

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_27.png)

답변: 03/02/2019

## Windows가 새로운 로그온에 처음으로 특권을 할당한 시간은 언제입니까?

답변 형식: MM/DD/YYYY HH:MM:SS AM/PM

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

## 방법:

시작 메뉴 아이콘에 가서 클릭한 후 팝업 메뉴에서 '이벤트 뷰어(Event Viewer)'를 클릭합니다.

![image](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_28.png)

우리가 찾고 있는 것은 보안 문제이므로 일단 보안 로그를 먼저 살펴보고 원하는 내용을 찾을 수 있는지 확인해야 합니다. 그러나 많은 로그가 있을 것을 알기 때문에 로그를 좁혀야 합니다. 이를 위해 이벤트 뷰어 창의 오른쪽에 있는 '사용자 지정 보기 만들기(Create Custom View)'를 클릭합니다.

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

![image](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_29.png)

팝업 창이 나타납니다. 먼저 'Logged' 필드를 클릭하면 드롭다운 상자가 나타납니다. 'Custom Range...'을 클릭하세요.

![image](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_30.png)

'From:' 섹션에서 'First Event'라고 써 있는 곳을 클릭한 다음 드롭다운 메뉴에서 'Events On'을 클릭하세요. 'To:' 섹션에서도 동일한 작업을 수행하세요.

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

<pre>
<img src="/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_31.png" />

이전 질문에서 우리는 날짜가 2019년 3월 2일임을 알고 있으므로 해당 날짜를 입력할 수 있습니다. 마지막 질문에서 공격이 일어난 대략적인 시간을 알고 있으므로, 오후 4:00:00부터 4:30:00까지 입력할 수 있습니다. 날짜 필드를 채우고 나면 창의 아래쪽에 있는 OK 버튼을 클릭하세요.

<img src="/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_32.png" />

이제 살펴볼 로그를 선택해야 합니다. 이를 위해 Event Logs: 필드를 클릭한 후, 드롭다운이 나타나면 Windows Logs 옆의 작은 +를 클릭한 후 마지막으로 Security를 클릭하세요. 이것으로 해당 로그가 선택됩니다.
</pre>

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

아래의 명령을 Markdown 형식으로 변경하실 수 있습니다.

![이미지 설명](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_33.png)

[사용자 정의 보기 창 하단에 있는 OK 버튼을 클릭하십시오.](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_34.png)

필터를 저장할 수 있는 창이 나타납니다. 이름을 지정하라는 요청이 있을 텐데, 창 오른쪽에 있는 OK 버튼을 클릭하십시오.

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

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_35.png)

우리는 살펴볼 이벤트가 297개 있어요. 처음부터 시작해 봐요. 이벤트 갯수 오른쪽에 있는 밑으로 스크롤해서 맨 밑으로 가세요.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_36.png)

맨 밑으로 가면 천천히 스크롤을 올려가면서 Task Category를 살펴보세요. 이것은 우리가 찾는 것, 즉 새로운 로그인에 대한 권한 변경을 나타냅니다. 보안 그룹 관리를 찾아보세요. 찾으시면 정답에 대한 날짜와 시간을 확인하세요. 답변에 월과 일 앞에 0을 넣는 것을 잊지 마세요.

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

아래의 마크다운 형식으로 표 태그를 변경해주세요.

![image](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_37.png)

Answer: 03/02/2019 4:04:47 PM

## Windows 암호를 가져 오는 데 사용 된 도구는 무엇입니까?

## 방법:

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

명령 프롬프트가 계속해서 C:\TMP\mim.exe로 나타납니다. 가능한 도구를 확인할 수 있는 장소입니다.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_38.png)

화면 하단의 작업 표시줄에 있는 파일 탐색기 아이콘을 클릭하세요. 파일 탐색기 창이 나타나면 로컬 디스크 (C:)를 클릭하여 이 디렉터리를 열어보세요.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_39.png)

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

TMP 디렉토리를 두 번 클릭해주세요.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_40.png)

이 디렉토리 안으로 들어가면 여러 파일, 워드 문서 및 파워셸 명령이 있을 거에요. 우리가 계속 보던 mim.exe라는 애플리케이션을 찾아보면 그 아래에 mim-out이라는 워드 문서가 있어요. 이것은 해당 애플리케이션과 관련이 있을 수 있으니 열어봅시다. mim-out을 더블클릭해주세요.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_41.png)

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

When mim-out 오픈시, 알고 있어야 할 도구 이름이 나타날 것입니다.

![도구 이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_42.png)

답변: mimkatz

## 공격자의 외부 제어 및 명령 서버 IP 주소는 무엇이었습니까?

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

## 어떻게:

이제 호스트 파일로 돌아가 봅시다. 디렉토리 경로 필드 옆의 위쪽 화살표를 클릭하면 한 단계 위로 이동할 수 있어요.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_43.png)

호스트 파일에 도착하려면 이 파일 경로를 따라가세요: C:\ `Windows` System32 `drivers` 등등. 거기에 도착하면 hosts를 두 번 클릭하여 열어보세요.

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

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_44.png)

여기에 파일을 열 방법을 물어보는 창이 표시됩니다. 노트패드를 클릭하세요.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_45.png)

이 파일 안에 google.com과 www.google.com이 있다는 것을 볼 수 있습니다. 이는 호스트 파일에 입력해야 할 필요가 없는데 PC가 DNS 서버로 이동하여 찾을 수 있기 때문에 매우 의심스럽습니다. 위협 요소가 C2 서버 IP를 이 방식으로 숨길 수 있습니다. 호스트 머신에서 명령 프롬프트를 열어 Google.com에 핑을 보내어 호스트 파일과 일치하는 경우에는 합법적인 것이고, 그렇지 않으면 의심스러울 수 있습니다.

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

![TryHackMeInvestigatingWindowsTask1InvestigatingWindows_46](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_46.png)

호스트 파일과 일치하지 않습니다. 이는 대부분 C2 서버 IP입니다. 이것을 TryHackMe에 답으로 입력하세요. 또 다른 답변에 필요한 것이 있기 때문에 이 파일을 열어 둡니다.

![TryHackMeInvestigatingWindowsTask1InvestigatingWindows_47](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_47.png)

답변: 76.32.97.132

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

## 서버 웹 사이트를 통해 업로드 된 쉘의 확장자 이름이 무엇이었습니까?

## 방법:

이제 우리는 또 다른 수상한 디렉토리를 확인할 것입니다. 파일 탐색기 창으로 돌아가면 창의 왼쪽에 빠른 링크 섹션이 있습니다. 여러 디렉토리에 빠르게 액세스할 수 있습니다. Local Disk (C:)를 찾아보세요. 그 아래에 우리가 찾는 디렉토리가 있어야 합니다. inetpub인데요, 해당 디렉토리로 바로 이동해보세요.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_48.png)

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

이 디렉터리 안에는 wwwroot라는 다른 디렉터리가 있습니다. 이 디렉터리로 이동하려면 클릭하세요.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_49.png)

이제 이 디렉터리 안에서 세 개의 파일을 볼 수 있습니다. 두 개는 .JSP 파일이고 다른 하나는 .GIF 파일입니다. .GIF 파일은 컴퓨터에서 매우 의심스럽지 않지만 .JSP 파일은 무엇인가요? 빠른 구글 검색으로 알아냅니다.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_50.png)

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

이 자바 파일이 여기에 무엇을 하고 있는 건가요? 우리가 찾고 있는 파일 확장자일 수도 있어 보여요. 질문에는 업로드된 셸의 확장자가 무엇인지 물었지만, 그들은 두 개의 다른 파일에 대해 이야기하고 있어요.

이미지:

![tryhackme image](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_51.png)

답변: .jsp

## 공격자가 마지막으로 열었던 포트는 무엇이었나요?

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

## 사용 방법:

개방된 포트에 대해 알아보는 좋은 방법은 방화벽 로그를 확인하는 것입니다. 이를 확인하려면 화면 왼쪽 하단의 시작 아이콘을 클릭한 다음 방화벽이라고 입력한 후에 유일한 항목을 클릭하거나 Enter를 누릅니다.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_52.png)

Windows 방화벽 및 고급 보안 창이 나타납니다. 나타날 경우 좌측에 있는 Inbound Rules를 클릭합니다. 이 과정에서 많은 인바운드 규칙이 표시됩니다.

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

아래는 우리가 찾고 있는 것을 찾기 위해 대부분을 필터링하겠습니다. 오른쪽 열로 이동하세요. 그룹으로 필터링을 클릭한 다음, 드롭다운 메뉴 아래쪽으로 스크롤하여 그룹이 없는 규칙을 클릭하세요.

이제 결과가 두 개만 표시될 것입니다. 하나는 매우 의심스러워 보입니다. 첫 번째 항목인 개발용 외부 연결 허용을 두 번 클릭하세요.

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

![image](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_55.png)

A window will pop-up giving details about this rule, look for the tab labeled Protocols and Ports and click on it.

![image](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_56.png)

Now in here you will see the port that is opened, this is the answer to the question.

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

테이블 태그를 Markdown 형식으로 변경하세요.

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

호스트 파일로 돌아가주세요. 만약 위의 지침에 따라 호스트 파일에 들어가지 않았다면요. 내가 이전에 말했던, dns 서버에서 쉽게 찾을 수 있는 특정 웹 사이트가 호스트 파일에 있는 것에 대해 기억하시나요? 같은 웹 사이트가 C2 서버에 연결되어 있다는 것이 이 질문의 답입니다.

![이미지](/assets/img/2024-05-20-TryHackMeInvestigatingWindowsTask1InvestigatingWindows_58.png)

이렇게해서 이 방을 모두 끝내셨어요!!! 축하해요!!!!!! 마지막으로, 질문들이 여러 섹션을 왔다갔다하게 만들어서 그렇게 좋아하지 않았어요. 여러분은 몇 가지 추적을 계속하고 싶었지만, 질문들이 다른 방향으로 이끌었거든요. 그러니 이 방을 하실 때, 질문을 순서대로 해야한다는 제한은 없다고 기억해주세요. 도움이 되었기를 바래요. 감사합니다!!!
