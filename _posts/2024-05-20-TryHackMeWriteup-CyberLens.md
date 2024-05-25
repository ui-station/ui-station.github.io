---
title: "TryHackMe Writeup - CyberLens를 번역해보겠습니다"
description: ""
coverImage: "/assets/img/2024-05-20-TryHackMeWriteup-CyberLens_0.png"
date: 2024-05-20 18:10
ogImage: 
  url: /assets/img/2024-05-20-TryHackMeWriteup-CyberLens_0.png
tag: Tech
originalTitle: "TryHackMe Writeup - CyberLens"
link: "https://medium.com/@MrHDK/cyberlens-5d39ea4ff23b"
---


## CyberLens 웹 서버를 악용하고 숨겨진 플래그를 발견할 수 있을까요?

"사이버 렌즈" CTF 룸에 오신 것을 환영합니다!

2024년 5월 17일에 공개된 이 CTF 챌린지는 약 120분 정도 소요되며 "쉬움" 난이도로 평가됩니다.
하지만 제 개인적인 경험에 따르면, 완료하는 데 2시간이 넘게 걸렸어요.

🕵️‍♂️ 준비가 되셨나요? 룸을 찾을 수 있습니다.

<div class="content-ad"></div>

🔍 챌린지 설명 확인: 챌린지에 대한 자세한 내용은 방의 내용을 확인하세요.

![이미지](/assets/img/2024-05-20-TryHackMeWriteup-CyberLens_0.png)

## 주의 사항

1. IP를 /etc/hosts 파일에 추가하세요: `sudo echo [IP] cyberlens.thm >> /etc/hosts`

<div class="content-ad"></div>


이 공간은 CyberLens 및 그들의 전문 도구에 대해 다루며, 스테가노그래피에 초점을 맞출 수도 있습니다. 화법 및 메타데이터를 위해 이미지를 분석하거나 숨겨진 플래그를 찾기 위해 이러한 도구 내의 취약점을 이용할 수도 있습니다.

## 열거

우리는 빠른 스캔을 위해 올드스쿨 nmap으로 시작합니다.
그리고 흥미로운 포트가 열려 있는 것을 발견합니다

```js
─# nmap 10.10.189.206 -A
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-18 09:40 EDT
Nmap scan report for cyberlens.thm (10.10.189.206)
Host is up (0.10s latency).
Not shown: 995 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Apache httpd 2.4.57 ((Win64))
|_http-server-header: Apache/2.4.57 (Win64)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: CyberLens: Unveiling the Hidden Matrix
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=CyberLens
| Not valid before: 2024-05-17T13:10:13
|_Not valid after:  2024-11-16T13:10:13
| rdp-ntlm-info: 
|   Target_Name: CYBERLENS
|   NetBIOS_Domain_Name: CYBERLENS
|   NetBIOS_Computer_Name: CYBERLENS
|   DNS_Domain_Name: CyberLens
|   DNS_Computer_Name: CyberLens
|   Product_Version: 10.0.17763
|_  System_Time: 2024-05-18T13:47:39+00:00
|_ssl-date: 2024-05-18T13:47:47+00:00; -10s from scanner time.
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7)... redacted ...

Network Distance: 2 hops
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-05-18T13:47:41
|_  start_date: N/A
|_clock-skew: mean: -10s, deviation: 0s, median: -10s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

TRACEROUTE (using port 587/tcp)
HOP RTT       ADDRESS
1   125.60 ms 10.9.0.1
2   121.35 ms cyberlens.thm (10.10.189.206)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 464.64 seconds
```

<div class="content-ad"></div>

- 80/tcp: Apache httpd 2.4.57에서 오픈된 HTTP — CVE 발견 안 됨
- 135/tcp: Microsoft Windows RPC 오픈
- 139/tcp: Microsoft Windows netbios-ssn 오픈
- 445/tcp: Microsoft-DS 오픈
- 3389/tcp: Microsoft Terminal Services (RDP) 오픈

우리는 웹사이트를 돌아다니다가, dirb를 실행하여 새로운 디렉토리를 발견하였어요.

![이미지](/assets/img/2024-05-20-TryHackMeWriteup-CyberLens_1.png)

우리의 관심을 끄는 두 가지 발견:

<div class="content-ad"></div>

1. 연락 양식: 현재 유용한 정보가 없습니다.
2. 이미지 추출기: 저희 조사에 대한 잠재적인 통찰력을 약속하는 온라인 메타데이터 추출 서비스입니다.

![이미지](/assets/img/2024-05-20-TryHackMeWriteup-CyberLens_2.png)

나는 http://cyberlens.thm/images/에서 찾은 이미지를 힌트를 얻기 위해 도구에 업로드했지만 유용한 메타데이터를 찾을 수 없었습니다.

다음으로 "메타데이터 가져오기" 버튼을 조사하고 해당 이벤트를 추적했습니다. 이로써 다음이 드러났습니다:

<div class="content-ad"></div>

<img src="/assets/img/2024-05-20-TryHackMeWriteup-CyberLens_3.png" />

nmap 결과에 이 포트가 나오지 않았어요. 이 포트는 일반적이지 않은 포트라서이고, -p- 플래그를 사용하지 않았기 때문입니다. 일반적으로는 권장되지 않아요.

<img src="/assets/img/2024-05-20-TryHackMeWriteup-CyberLens_4.png" />

첫 번째로는 해당 아파치 티카 버전에 대한 CVE를 찾아보는 거에요.

<div class="content-ad"></div>

Metasploit을 시작하고,

![CyberLens_5](/assets/img/2024-05-20-TryHackMeWriteup-CyberLens_5.png)

## 접근 방법

Metasploit을 구성하고 (set RHOSTS, RPORT, LPORT), 그런 다음 exploit을 실행합니다. 그리고 바로 Meterpreter 세션을 열었습니다. 와우!

<div class="content-ad"></div>

![이미지](/assets/img/2024-05-20-TryHackMeWriteup-CyberLens_6.png)

우선 CyberLens의 문서에서 깃발을 찾으려고 했어요. 대신 관리 부분을 우연히 발견했어요. 거기에서 승격을 위해 사용할 자격 증명을 찾았어요.

![이미지](/assets/img/2024-05-20-TryHackMeWriteup-CyberLens_7.png)

힌트에서 이해한대로 대부분의 Windows 깃발은 데스크톱에 있기 때문에 거기로 가서 첫 번째 깃발을 얻었어요.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-20-TryHackMeWriteup-CyberLens_8.png" />

## 권한 상슨

이전에 찾은 자격 증명을 다시 보면, 원격 데스크톱 프로토콜(RDP) 포트 3389과 일치합니다.
그래서 remmina를 실행하지만, 동일한 사용자로 굳어 버린 것을 발견합니다. 이는 클래식한 실수였습니다. 사용자 이름에 주의를 기울이지 않았기 때문입니다. 권한 상슨이 내가 희망했던 만큼 간단하지 않을 것이라는 것을 알게 되었습니다. 그럼에도 불구하고 GUI 인터페이스를 보유하고 있기 때문에 이제 시스템을 쉽게 탐색할 방법이 제공되었습니다.

다음으로 모든 서비스를 나열했습니다:

<div class="content-ad"></div>

```js
sc.exe query state=all
```

하지만 리스트가 굉장히 길었고, 수작업으로 걸러내는 대신 더 포괄적인 분석을 위해 WinPEAS를 실행하기로 결정했습니다.

이미 존재하는 Metasploit 쉘을 고려할 때, 나는 나의 접근 방식을 전환했습니다. WinPEAS 대신 Metasploit의 multi/recon/local_exploit_suggester 모듈을 사용하여 권한 상승을 위한 잠재적인 취약점을 식별했습니다.

<img src="/assets/img/2024-05-20-TryHackMeWriteup-CyberLens_9.png" />


<div class="content-ad"></div>

세션을 설정하고 모듈을 실행하여 총 세 가지 취약점을 발견했어요. 먼저 첫 번째 취약점부터 시작해서 해당 옵션을 구성한 후(exploit를 실행하기 전에 LHOST, LPORT, SESSION을 설정해주세요).

그리고 이제 NT AUTHORITY\SYSTEM으로 권한 상승했어요.

![TryHackMeWriteup-CyberLens_10](/assets/img/2024-05-20-TryHackMeWriteup-CyberLens_10.png)

관리자의 데스크톱에 깃발이 있을 거에요.

<div class="content-ad"></div>

# 결론

- 주의 깊은 열거: 중요한 서비스를 식별하면서 모든 포트를 스캔하는 것을 잊지 마세요 (-p-).
- 수동 열거: 잠재적으로 간과된 단서를 잡는 데 중요합니다.
- 도전의 조합: 웹 취약점 분석과 권한 상승을 결합했습니다.
- 적응성 있는 전략: 수동 서비스 분석 및 WinPEAS에서 Metasploit의 익스플로잇 제안자로 전환했습니다.
- 주요 발견: 취약점을 발견하고 악용하여 사용자와 관리자 깃발을 얻었습니다.
- 필수 기술: 적응적 전략 및 실용적 사이버 보안 기술을 강화했습니다.

LinkedIn에서 저와 연결하세요.