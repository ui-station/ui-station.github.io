---
title: "리눅스 시스템을 위한 10가지 문제 해결 명령어"
description: ""
coverImage: "/assets/img/2024-05-23-10TroubleshootingCommandsforLinuxSystems_0.png"
date: 2024-05-23 15:10
ogImage: 
  url: /assets/img/2024-05-23-10TroubleshootingCommandsforLinuxSystems_0.png
tag: Tech
originalTitle: "10 Troubleshooting Commands for Linux Systems"
link: "https://medium.com/@cstoppgmr/10-troubleshooting-commands-for-linux-systems-4fa8c3a1a466"
---


``` Markdown
![이미지](/assets/img/2024-05-23-10TroubleshootingCommandsforLinuxSystems_0.png)

# 1. CPU를 가장 많이 사용하는 프로세스 확인하는 방법

```js
$ ps H -eo pid,pcpu | sort -nk2 | tail
31396  0.6
31396  0.6
31396  0.6
31396  0.6
31396  0.6
31396  0.6
31396  0.6
31396  0.6
30904  1.0
30914  1.0
```

가장 CPU를 많이 사용하는 PID는 30914입니다. 음성오버: 실제로는 31396입니다.
```

<div class="content-ad"></div>

# 2. 가장 CPU 소모가 많은 프로세스의 PID에 해당하는 서비스 이름은 무엇인가요?

첫 번째 방법:

```js
$ ps aux | fgrep 30914
work 30914  1.0  0.8 309568 71668 ?  Sl   Feb02 124:44 ./router2 –conf=rs.conf
```

해당 프로세스는 ./router2입니다.

<div class="content-ad"></div>

번호 두 방법:

```js
$ ll /proc/30914
lrwxrwxrwx  1 work work 0 2월 10일 13:27 cwd -> /home/work/im-env/router2
lrwxrwxrwx  1 work work 0 2월 10일 13:27 exe -> /home/work/im-env/router2/router2
```

음성 안내: 멋져요, 전체 경로가 모두 나와 있네요.

# 3. 특정 포트의 연결 상태를 확인하는 방법은 무엇인가요?

<div class="content-ad"></div>

Method One:

```js
$ netstat -lap | fgrep 22022
tcp        0      0 1.2.3.4:22022          *:*                         LISTEN      31396/imui
tcp        0      0 1.2.3.4:22022          1.2.3.4:46642          ESTABLISHED 31396/imui
tcp        0      0 1.2.3.4:22022          1.2.3.4:46640          ESTABLISHED 31396/imui
```

Method Two:

```js
$ /usr/sbin/lsof -i :22022
COMMAND   PID USER   FD   TYPE   DEVICE SIZE NODE NAME
router  30904 work   50u  IPv4 69065770       TCP 1.2.3.4:46638->1.2.3.4:22022 (ESTABLISHED)
router  30904 work   51u  IPv4 69065772       TCP 1.2.3.4:46639->1.2.3.4:22022 (ESTABLISHED)
router  30904 work   52u  IPv4 69065774       TCP 1.2.3.4:46640->1.2.3.4:22022 (ESTABLISHED)
```

<div class="content-ad"></div>

# 4. 기계의 연결 수를 확인하는 방법은?

1.2.3.4의 SSH 데몬(sshd)이 22번 포트에서 수신 대기 중입니다. 1.2.3.4의 sshd 서비스에 대한 다양한 상태의 연결 수 (TIME_WAIT/CLOSE_WAIT/ESTABLISHED)를 어떻게 카운트할 수 있을까요?

```js
$ netstat -n | grep 1.2.3.4:22 | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'

$ netstat -lnpta | grep ssh | egrep "TIME_WAIT | CLOSE_WAIT | ESTABLISHED"
```

참고: netstat은 네트워크 연결 문제를 추적하는 데 자주 사용되는 도구이며, grep/awk와 결합하면 강력한 도구가 됩니다.

<div class="content-ad"></div>

# 5. 사전 백업 로그에서 데이터 쿼리하기

사전 백업 서비스.2022-06-26.log.bz2 로그에서 키워드 1.2.3.4을 포함하는 항목은 몇 개인가요?

```js
$ bzcat service.2022-06-26.log.bz2 | grep '1.2.3.4' | wc -l

$ bzgrep '1.2.3.4' service.2022-06-26.log.bz2 | wc -l

$ less service.2022-06-26.log.bz2 | grep '10.37.9.11' | wc -l
```

참고: 온라인 로그 파일은 일반적으로 bz2로 압축된 후 보존됩니다. 쿼리를 위해 해제하면 많은 공간과 시간이 소비됩니다. 따라서, 연구 및 개발 동료들이 숙달해야할 bzcat 및 bzgrep는 필수 도구입니다.

<div class="content-ad"></div>

# 6. 백업 서비스 팁

백업을 위해 /opt/web/service_web 디렉토리를 패킹하되 로그 디렉토리는 제외하고, 패킹한 파일을 /opt/backup 디렉토리에 저장하세요.

```js
$ tar -zcvf /opt/backup/service_web.tar.gz \
    -exclude /opt/web/service_web/logs \
    /opt/web/service_web
```

참고: 이 명령은 온라인 애플리케이션에서 흔히 사용됩니다. 프로젝트를 패킹하고 이전해야 할 때 로그 디렉토리를 제외해야 할 때가 종종 있습니다. 이런 시나리오에서는 `exclude` 매개변수를 잘 활용하는 것이 중요합니다.

<div class="content-ad"></div>

# 7. 쓰레드 카운트 조회

서버의 서비스를 위해 실행 중인 전체 쓰레드 수를 조회합니다. 기계의 쓰레드 수가 경고 임계값을 초과할 때 해당 프로세스와 쓰레드 정보를 빠르게 식별해야 합니다.

```js
$ ps -eLf | wc -l

$ pstree -p | wc -l
```

# 8. 디스크 경고, 가장 큰 파일 비우기

<div class="content-ad"></div>

많은 예외 로그 파일을 찾아 서버에서 실행 중인 Tomcat 서버에서 생성된 파일을 공간을 확보하세요. 파일에 "log" 키워드가 포함되어 있고 1GB보다 큰 경우를 가정합니다.

단계 1: 파일 찾기.

```js
$ find / -type f -name "*log*" | xargs ls -lSh | more

$ du -a / | sort -rn | grep log | more

$ find / -name '*log*' -size +1000M -exec du -h {} \;
```

단계 2: 파일을 비우기.

<div class="content-ad"></div>

가정적으로 찾은 파일이 a.log인 경우, 해당 파일을 완전히 비우는 올바른 방법은:

```js
$ echo "" > a.log
```

이렇게 하면 파일 공간이 즉시 해제됩니다.

많은 사람들이 사용하는 방법:

<div class="content-ad"></div>

```js
$ rm -rf a.log
```

파일을 삭제하면서도 Tomcat 서비스가 여전히 실행 중인 경우 공간이 즉시 해제되지 않을 수 있습니다. 공간을 확보하려면 Tomcat을 다시 시작해야 합니다.

# 9. 파일 표시, 주석 필터링

서버.conf 파일을 표시하고 #로 시작하는 주석 줄을 마스킹합니다.

<div class="content-ad"></div>

```js
$ sed -n '/^[#]/!p' server.conf

$ sed -e '/^#/d' server.conf

$ grep -v "^#" server.conf
```

# 10. 디스크 IO 예외 해결 방법

디스크 IO 예외, 예를 들어 느린 쓰기 또는 높은 현재 사용량과 같은 문제를 해결하는 방법을 알아보세요. 높은 디스크 IO 예외를 일으키는 프로세스 ID를 식별해야 합니다.

단계 1:

<div class="content-ad"></div>

```sh
$ iotop -o
```

현재 디스크에 쓰기 중인 모든 프로세스 ID를 보십시오.

단계 2: 만약 쓰기 표시기가 낮고 주요 쓰기 작업이 거의 없다면, 디스크 자체를 확인해야 합니다. 시스템을 확인할 수 있습니다.

```sh
$ dmesg
```

<div class="content-ad"></div>

차후에 한 번 `/var/log/message` 파일을 확인해보세요. 여기에 어떤 디스크 오류 메시지가 있는지 확인할 수 있어요. 동시에, 쓰기 속도가 느린 디스크에 빈 파일을 만들어보세요. 디스크의 고장으로 인해 쓰기가 안 되는지 확인할 수 있어요.