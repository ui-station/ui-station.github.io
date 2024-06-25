---
title: "활용하여 포워터쉘PowerShell을 사용하여 Active Directory로부터 비트로커BitLocker 복구 암호 가져오기 포함된 Whole-OU-to-CSV 스크립트와 함께"
description: ""
coverImage: "/assets/img/2024-05-18-HowToGetBitLockerRecoveryPasswordsfromActiveDirectoryUsingPowerShellWithWhole-OU-to-CSVScriptIncluded_0.png"
date: 2024-05-18 17:48
ogImage:
  url: /assets/img/2024-05-18-HowToGetBitLockerRecoveryPasswordsfromActiveDirectoryUsingPowerShellWithWhole-OU-to-CSVScriptIncluded_0.png
tag: Tech
originalTitle: "How To Get BitLocker Recovery Passwords from Active Directory Using PowerShell (With “Whole-OU-to-CSV’ Script Included)"
link: "https://medium.com/@dbilanoski/how-to-get-bitlocker-recovery-passwords-from-active-directory-using-powershell-with-30a93e8dd8f2"
---

최근에는 자동화된 전체 사이트 BIOS 업데이트 작업이 있었고 일부 홈 사용자들이 BIOS 업데이트 예약 이전에 BitLocker 일시 중지가 발생하지 않은 상황이었습니다. 이는 BIOS 업데이트가 실행되고 BitLocker 복구 프롬프트가 트리거될 것을 의미했기 때문에, 다음 재부팅에서 아름다운 파란색 BitLocker 복구 화면이 표시되어 사용자들이 비트록 컴퓨터에 액세스하고 BitLocker 비밀번호를 요청하는 상황이 발생했습니다.

이것이 소수의 사용자만 영향을 미친다면, ADUC (Active Directory Users and Computers) 콘솔의 그래픽 인터페이스를 통해 이 정보에 액세스하는 것이 충분할 것입니다. 그러나 숫자가 계속해서 증가하면서 오프시간 동안 지원을 받아야 하는 필요성이 점점 높아지는 경우, 설치된 RSAT가 있는 랩톱에 액세스할 수 없는 상황에서 이 정보를 모바일 친화적인 텍스트 형식으로 빠르게 내보내는 것이 필수적이 됩니다.

오늘은 PowerShell을 사용하여 BitLocker 비밀번호를 내보내는 방법을 살펴보겠습니다. 끝으로, 조직 내 전체 Organizational Units (OUs)를 실용적인 csv 파일로 내보내는 준비가 된 PowerShell 스크립트를 사용할 수 있게 될 것입니다.

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

# 급한 사람들을 위한 안내

- 예제와 함께 PowerShell 단계별 접근 방식.
- AD에서 BitLocker 암호를 수집하기 위한 전체 OU를 CSV로 추출하기 위한 PowerShell 스크립트.

# BitLocker가 뭐길래?

BitLocker는 Windows의 내장 암호화 기능으로, 전체 드라이브를 암호화하여 사용자 데이터를 안전하게 보호합니다. 활성화되면 사용자 장치에 대한 무단 액세스가 복구 키를 제공하지 않는 한 차단됨을 보장합니다.

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

사실, 무단 액세스는 하드웨어, BIOS 설정 및 업데이트 변경, 부팅 구성 또는 환경 변경 등을 의미할 수 있습니다. 이 모든 것은 사용자가 컴퓨터에 접근할 수 없게 만들 수 있으므로 복구 키를 올바르게 보관하는 것이 중요합니다.

BitLocker 복구 정보를 저장할 때는 폴더, USB 장치, Microsoft 계정 또는 Active Directory에 저장하는 몇 가지 옵션이 있습니다. 이 경우 AD 컴퓨터 객체에 저장된 정보를 활용한 작업에 대해 논의하고 있습니다. BitLocker를 구현할 때는 이러한 사항들을 신중히 고려해야 하며, 여기에 있는 문서는 좋은 참고 자료가 될 것입니다.

# Powershell을 사용하여 Active Directory에서 BitLocker 비밀번호 가져오기

BitLocker 복구 정보는 Active Directory의 컴퓨터 객체 내에 저장되어 있지만 기본 객체 속성에는 포함되어 있지 않기 때문에 Get-ADComputer로 확인할 수 없습니다. 우리가 필요한 것은 ms-FVE-RecoveryInformation 클래스에 액세스하여 해당 ms-FVE-RecoveryPassword 속성을 확인하는 것입니다.

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

먼저 관리자 권한으로 PowerShell을 실행하고 ActiveDirectory PowerShell 모듈이 설치되어 있는지 확인해주세요.

```js
Import-module ActiveDirectory
```

## 컴퓨터 객체 가져오기

나중에 비밀번호 데이터를 검색할 수 있도록 Active Directory에서 컴퓨터 객체를 검색해야 합니다.

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

동일한 줄에 DistinguishedName을 추출하여 변수에 저장하여 텍스트 문자열로 사용할 수 있도록 준비하겠습니다.

```js
$ComputerDname = $(Get-AdComputer hroslp17).DistinguishedName
```

<img src="/assets/img/2024-05-18-HowToGetBitLockerRecoveryPasswordsfromActiveDirectoryUsingPowerShellWithWhole-OU-to-CSVScriptIncluded_1.png" />

## msFVE-RecoveryInformation 개체 가져오기

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

이제 msFVE-RecoveryInformation 클래스를 포함하도록 필터링된 AD 객체를 검색할 것입니다. 이 클래스를 포함하면 BitLocker 회복 비밀번호를 비롯한 다른 속성이 있을 것입니다.

검색할 때 SearchBase 매개변수를 추가하여 이전에 저장한 DistinguishedName을 사용하여 컴퓨터 객체를 대상으로 설정할 것입니다. 그렇지 않으면 해당 명령은 전체 도메인에 대한 객체를 반환하므로 원하지 않을 것입니다.

탐구를 위해 속성 매개변수를 지정하여 각 속성을 포함하고 이를 변수에 저장하여 보유한 속성을 검사할 수 있습니다.

```js
$RecoveryObj=Get-ADObject -Filter {objectclass -eq 'msFVE-RecoveryInformation'} -SearchBase $ComputerDname -Properties *
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

이 클래스 인스턴스의 속성과 메서드를 살펴보면 스크립트에서 사용할 수 있는 많은 유용한 정보들이 있습니다. 사용할 두 가지 정보는:

- msFVE-RecoveryPassword: 비트록커 비밀번호가 들어 있습니다.
- whenCreated: 생성일이 포함되어 있습니다.

![이미지](/assets/img/2024-05-18-HowToGetBitLockerRecoveryPasswordsfromActiveDirectoryUsingPowerShellWithWhole-OU-to-CSVScriptIncluded_2.png)

## 비트록커 복구 비밀번호 추출하기

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

이제 $RecoveryObj 변수에는 컴퓨터와 관련된 모든 복구 데이터가 저장되어 있습니다. 이제 속성 값을 액세스하기 위해 마침표 표기법과 속성 이름을 사용하여 간단히 출력할 수 있습니다.

```js
$RecoveryObj.'msFVE-RecoveryPassword'
```

<img src="/assets/img/2024-05-18-HowToGetBitLockerRecoveryPasswordsfromActiveDirectoryUsingPowerShellWithWhole-OU-to-CSVScriptIncluded_3.png" />

해당 점을 유의하세요. 중요한 점은 대시(-) 문자를 이스케이프하기 위해 작은따옴표를 사용하는 것입니다.

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

## 너무 긴 한 줄

우리는 조금의 가독성을 희생하면서 이것을 한 줄로 더 요약할 수 있습니다.

이전 모든 복구 암호는 복구 객체 내에 보관되므로, 우리는 whenCreated라는 추가 속성을 추가하여 그것을 사용해 최신 것을 가져올 수 있도록 정렬하고 있습니다.

```js
$(Get-ADObject -Filter {objectclass -eq 'msFVE-RecoveryInformation'} -SearchBase $(Get-AdComputer hroslp17).DistinguishedName -Properties 'msFVE-RecoveryPassword',whencreated | sort whenCreated).'msFVE-RecoveryPassword'
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

![BitLocker Recovery Passwords Scraper](/assets/img/2024-05-18-HowToGetBitLockerRecoveryPasswordsfromActiveDirectoryUsingPowerShellWithWhole-OU-to-CSVScriptIncluded_4.png)

# Whole-OU-to-CSV BitLocker Passwords Scraper PowerShell Script

For a complete solution to export recovery information for multiple computers at once, refer to the script below.

To use it:

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

- 올바른 Active Directory OU로 컴퓨터 변수를 구성하세요.
- 내보낸 목록을 저장할 CSV 파일의 절대 경로로 csvPath 변수를 구성하세요.
- 스크립트를 높은 권한 PowerShell에서 실행하세요 (관리자 권한으로 실행).

결과는 HostName 및 RecoveryPassword를 포함하는 두 열 CSV 파일이 생성됩니다.

```js
# AD 대상 OU 설정
$computers = Get-ADComputer -Filter * -SearchBase "OU=your-ou,OU=your-out,DC=your-domain,DC=local"
# 출력 CSV 파일의 절대 경로 설정
$csvPath = "C:\bitlocker-list.csv"
# 데이터를 저장할 출력 배열 선언
$output = @()
# CSV 헤더 선언
$output += "HostName, RecoveryPassword"

# 컴퓨터 반복, BitLocker가 저장되어 있는지 확인
foreach ($computer in $computers) {
    # msFVE-RecoverInfo 개체 검색 및 생성 날짜를 기준으로 내림차순으로 정렬하여 최신 키가 검색되도록 함
    $fetch = $(Get-ADObject -Filter {objectclass -eq 'msFVE-RecoveryInformation'} -SearchBase $computer.DistinguishedName -Properties 'msFVE-RecoveryPassword',whencreated | Sort-Object WhenCreated -Descending).'msFVE-RecoveryPassword'
    # 비어 있으면 "BitLocker not active"를 데이터 개체에 작성
    if (-Not $fetch) {
        $output += ($computer.Name,"BitLocker not active") -join ","
    }
    # 하나 이상의 키가 있는 경우, 첫 번째 키를 가져옴 (최신 키일 것임).
    elseif ($fetch.Count -gt 1) {
        $output += ($computer.Name, $fetch[0]) -join ","
    }
    # 단일 키가 있는 경우, 해당 키를 가져옴.
    else {
        $output += ($computer.Name, $fetch) -join ","
    }
}

# 출력을 CSV로 내보내기
$output | Out-File -FilePath $csvPath
```

사용자가 업무 장치에 잠겨있을 때 지원하기는 결코 쉽지 않습니다. 특히 48자리의 숫자로 된 암호가 유일한 접근 방법인 경우 더 그렇습니다.

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

희망을 품어요. 이것이 당신이 빠르게 정리하는 데 도움이 될 것입니다.

# 저자의 언급

이곳까지 오신 것을 축하드려요! 음, 그럼 제가 꽤 괜찮은 작가이거나 당신이 훌륭한 독자인 것 같네요. 후자를 선택하죠 😅.

저는 기업 혼돈을 탐색하는 경험이 풍부한 IT 서비스 제공 엔지니어 다닐로에요. 스크립트 작성, 시스템 관리자 작업 등에 대해 다른 곳에서 충분히 문서화되지 않은 주제에 대해 글을 씁니다. 지식을 공유하고 글쓰기 기술을 향상시키기 위한 목적으로요.

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

만약 도와드릴 수 있다면 손뼉을 치고 피드백을 남겨주세요.
