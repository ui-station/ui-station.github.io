---
title: "파이썬에서 쉘 명령어를 올바르게 실행하는 방법"
description: ""
coverImage: "/assets/img/2024-05-23-TheRightWaytoRunShellCommandsFromPython_0.png"
date: 2024-05-23 15:08
ogImage:
  url: /assets/img/2024-05-23-TheRightWaytoRunShellCommandsFromPython_0.png
tag: Tech
originalTitle: "The Right Way to Run Shell Commands From Python"
link: "https://medium.com/better-programming/the-right-way-to-run-shell-commands-from-python-c05f0b9d6cb7"
---

<img src="/assets/img/2024-05-23-TheRightWaytoRunShellCommandsFromPython_0.png" />

파이썬은 모든 것을 자동화하는 데 인기 있는 선택지입니다. 이는 시스템 관리 작업을 자동화하거나 다른 프로그램을 실행하거나 운영 체제와 상호 작용하는 작업을 포함합니다. 그러나 파이썬에서 이를 수행하는 많은 방법이 있습니다. 대부분은 논란이 될 수 있는 방법입니다.

그래서 이 기사에서는 다른 프로세스를 실행하는 데 사용할 수 있는 파이썬 옵션을 모두 살펴볼 것입니다. 나쁜 방법, 좋은 방법, 그리고 무엇보다도 올바른 방법을 중점적으로 살펴보겠습니다.

# 옵션들

<div class="content-ad"></div>

파이썬은 다른 프로그램과 상호 작용하는 내장 옵션이 너무 많습니다. 그 중에서도 어떤 것은 좋고 나쁨이 있지만 솔직히 말해서 나는 어느 것도 좋아하지 않아요. 각 옵션을 빠르게 살펴보고 특정 모듈을 사용해야 하는 시점(있는 경우)을 확인해 봅시다.

# 내장 도구

일반적인 지침은 다른 프로그램이나 OS 명령을 직접 호출하는 대신 기본 기능을 사용하는 것이어야 합니다. 그래서 먼저 파이썬의 기본 옵션을 살펴보겠습니다:

- pathlib — 파일/디렉토리를 생성하거나 삭제해야 하거나 파일이 존재하는지 확인하거나 권한을 변경해야 할 때는 시스템 명령을 실행할 이유가 없습니다. 단순히 pathlib을 사용하세요. 필요한 모든 것이 포함되어 있습니다. pathlib를 사용하기 시작하면 glob이나 os.path와 같은 다른 파이썬 모듈을 잊을 수 있다는 것을 깨닫게 될 거예요.
- tempfile — 비슷하게, 임시 파일이 필요할 때는 tempfile 모듈을 사용하세요. /tmp에 수동으로 접근할 필요가 없습니다.
- shutil — pathlib은 파이썬에서 대부분의 파일 관련 요구 사항을 충족할 것입니다. 그러나 파일을 복사하거나 이동하거나 chown 또는 아카이브를 생성해야 하는 경우에는 shutil을 사용해야 합니다.
- signal — 신호 핸들러를 사용해야 하는 경우에 필요합니다.
- syslog — Unix syslog에 대한 인터페이스를 위해 사용합니다.

<div class="content-ad"></div>

만약 위에 제공된 내장 옵션들 중에서 아무것도 원하는 대로 만족스럽지 않다면, 그때만 운영 체제나 다른 프로그램과 직접 상호 작용하는 것이 의미가 있습니다.

# OS 모듈

최악의 옵션부터 시작해서 os 모듈은 운영 체제와 상호 작용하기 위한 저수준 함수를 제공하지만, 많은 함수들이 다른 모듈에서 대체되었습니다.

단순히 다른 프로그램을 호출하고 싶다면 os.system 함수를 사용할 수 있지만, 사용하면 안 됩니다. 당신에게 예시를 들어 주고 싶지 않아요, 왜냐하면 그냥 사용하지 말아야 하기 때문입니다.

<div class="content-ad"></div>

os는 처음 선택하긴 좋지 않지만, 유용하게 사용할 수 있는 몇 가지 함수가 있습니다:

```js
import os

print(os.getenv('PATH'))
# /home/martin/.local/bin:/usr/local/sbin:/usr/local/bin:...
print(os.uname())
# posix.uname_result(sysname='Linux', nodename='...', release='...', version='...', machine='x86_64')
print(os.times())
# posix.times_result(user=0.01, system=0.0, children_user=0.0, children_system=0.0, elapsed=1740.63)
print(os.cpu_count())
# 16
print(os.getloadavg())
# (2.021484375, 2.35595703125, 2.04052734375)
old_umask = os.umask(0o022)
# 파일 처리...
os.umask(old_umask)  # 이전 umask로 되돌리기

# 'random' 모듈의 의사 난수보다 더 좋은 난수가 필요한 경우에만 사용하세요:
from base64 import b64encode

random_bytes = os.urandom(64)
print(b64encode(random_bytes).decode('utf-8'))
# C2F3kHjdzxcP7461ETRj/YZredUf+NH...hxz9MXXHJNfo5nXVH7e5olqLwhahqFCe/mzLQ==
```

위에 설명된 함수들 외에도, fd(파일 기술자), 파이프, PTY 열기, chroot, chmod, mkdir, kill, stat을 생성하는 함수들이 있지만, 더 좋은 옵션이 있기 때문에 사용을 권장하지 않습니다. 심지어 os.popen, os.spawn 또는 os.system을 사용하지 않도록 docs에 나온 부분에서 os를 subprocess 모듈로 대체하는 방법을 보여줍니다.

파일/경로 작업에 os 모듈을 사용하지 말아주세요. os.path 및 기타 경로 관련 함수 대신 pathlib를 사용하는 방법에 대한 전체 섹션이 있습니다.

<div class="content-ad"></div>

os 모듈의 대부분의 남은 함수는 OS(또는 C 언어) API에 직접적으로 연결되어 있습니다. 예를 들어 os.dup, os.splice, os.mkfifo, os.execv, os.fork 등이 있습니다. 이러한 모든 함수를 사용해야 한다면 파이썬이 그 작업에 적합한 언어인지 확신하지 못합니다.

# 서브프로세스 모듈

파이썬에서 두 번째 — 약간 나은 — 옵션은 서브프로세스 모듈입니다. 아래는 서브프로세스 모듈의 예시입니다:

```js
import subprocess

p = subprocess.run('ls -l', shell=True, check=True, capture_output=True, encoding='utf-8')

# 'p'는 'CompletedProcess(args='ls -la', returncode=0)'의 인스턴스입니다
print(f'Command {p.args} exited with {p.returncode} code, output: \n{p.stdout}')
# Command ls -la exited with 0 code

# total 36
# drwxrwxr-x  2 martin martin  4096 apr 22 12:53 .
# drwxrwxr-x 42 martin martin 20480 apr 22 11:01 ..
# ...
```

<div class="content-ad"></div>

문서에서 설명된대로:

대부분의 경우 subprocess.run을 사용하여 kwargs를 전달하여 해당 동작을 변경하는 것이 충분합니다. 예를 들어 shell=True를 사용하면 명령을 단일 문자열로 전달할 수 있고, check=True를 사용하면 종료 코드가 0이 아닌 경우 예외를 throw하고, capture_output=True를 사용하면 stdout 속성을 채울 수 있습니다.

subprocess.run()은 프로세스를 호출하는 권장되는 방법입니다. 이 모듈에서 다른 (불필요하고 사용되지 않는) 옵션도 있습니다: call, check_call, check_output, getstatusoutput, getoutput. 일반적으로 아래와 같이 run과 Popen만 사용해야 합니다:

```js
with subprocess.Popen(['ls', '-la'], stdout=subprocess.PIPE, encoding='utf-8') as process:
    # process.wait(timeout=5)  # 코드만 반환: 0
    outs, errs = process.communicate(timeout=5)
    print(f'Command {process.args}가 {process.returncode} 코드로 종료되었으며 출력: \n{outs}')

# 파이프
import shlex
ls = shlex.split('ls -la')
awk = shlex.split("awk '{print $9}'")
ls_process = subprocess.Popen(ls, stdout=subprocess.PIPE)
awk_process = subprocess.Popen(awk, stdin=ls_process.stdout, stdout=subprocess.PIPE, encoding='utf-8')

for line in awk_process.stdout:
    print(line.strip())
    # .
    # ..
    # examples.py
    # ...
```

<div class="content-ad"></div>

위의 첫 번째 예제는 이전에 소개된 subprocess.run의 Popen 등가물을 보여줍니다. 그러나 run이 제공하는 것보다 더 많은 유연성이 필요할 때만 Popen을 사용해야 합니다. 두 번째 예제에서는 한 명령의 출력을 다른 명령으로 파이핑하는 방법을 볼 수 있습니다. ls -la | awk `'print $9'`를 효과적으로 실행하는 방법입니다. 또한, shlex.split을 사용했는데, 이는 문자열을 토큰의 배열로 분할하는 편리한 함수로, shell=True를 사용하지 않고 Popen이나 run으로 전달할 수 있습니다.

Popen을 사용할 때, 프로세스와 더 많은 상호작용을 위해 terminate(), kill() 및 send_signal()을 추가적으로 사용할 수 있습니다.

이전 예제에서는 실제로 오류 처리를 거의 하지 않았지만, 다른 프로세스를 실행할 때 많은 문제가 발생할 수 있습니다. 간단한 스크립팅의 경우, check=True가 있으면 충분할 것으로 생각됩니다. 이는 호출 프로세스가 0이 아닌 반환 코드를 만나면 CalledProcessError가 발생하므로 프로그램이 빠르고 강하게 실패합니다. timeout 인수도 설정하면 TimeoutExpired 예외도 받을 수 있지만, 일반적으로 subproccess 모듈의 모든 예외는 SubprocessError에서 상속받습니다. 예외를 잡고 싶을 경우에는 SubprocessError를 감시하면 됩니다.

# 올바른 방법

<div class="content-ad"></div>

파이썬의 신조(격언)은 다음과 같습니다:

하지만 지금까지 파이썬의 내장 모듈들로 많은 방법을 보았습니다. 하지만 그 중에 어떤 것이 옳은 것일까요? 내 의견으로는 그 중 하나도 아닙니다.

파이썬의 표준 라이브러리를 좋아하지만, subprocess 모듈이 더 나은 "배터리" 중에 하나라고 생각합니다.

만약 파이썬에서 다른 프로세스를 많이 조합하는 상황이라면, sh 라이브러리를 한 번 살펴보는 것을 권장합니다:

<div class="content-ad"></div>

```bash
# https://pypi.org/project/sh/
# pip install sh
import sh

# $PATH에 있는 어떤 명령어든 실행...
print(sh.ls('-la'))

ls_cmd = sh.Command('ls')
print(ls_cmd('-la'))  # 명시적으로
# total 36
# drwxrwxr-x  2 martin martin  4096 apr  8 14:18 .
# drwxrwxr-x 41 martin martin 20480 apr  7 15:23 ..
# -rw-rw-r--  1 martin martin    30 apr  8 14:18 examples.py

# 만약 PATH에 명령어가 없다면:
custom_cmd = sh.Command('/path/to/my/cmd')
custom_cmd('some', 'args')

with sh.contrib.sudo:
    # 'sudo'를 사용하여 작업 수행...
    ...
```

sh.some_command을 호출하면, sh 라이브러리가 해당 이름의 내장 셸 명령어나 $PATH에 있는 이진 파일을 찾습니다. 그런 명령어를 찾으면 그대로 실행됩니다. 명령어가 $PATH에 없는 경우에는 Command의 인스턴스를 생성하고 그렇게 호출할 수 있습니다. sudo를 사용해야 하는 경우에는 contrib 모듈의 sudo context manager를 사용할 수 있습니다. 너무 간단하고 직관적이죠?

명령어의 결과를 파일에 쓰려면, 함수에 \_out 인수를 제공하면 됩니다:

```bash
sh.ip.address(_out='/tmp/ipaddr')
# 'ip address > /tmp/ipaddr'와 같습니다
```

<div class="content-ad"></div>

위에는 하위 명령을 호출하는 방법도 보여 줍니다. - 점을 사용하세요.

마지막으로 \_인 인수를 사용하여 파이프(|)를 사용할 수도 있습니다:

```js
print(sh.awk('{print $9}', _인=sh.ls('-la'))
# "ls -la | awk '{print $9}'"과 동일합니다

print(sh.wc('-l', _인=sh.ls('.', '-1'))
# "ls -1 | wc -l"과 동일합니다
```

오류 처리에 대해선 ErrorReturnCode 또는 TimeoutException 예외를 감시하면 됩니다:

<div class="content-ad"></div>

```md
시도:
sh.cat('/tmp/doesnt/exist')
except sh.ErrorReturnCode as e:
print(f'Command {e.full_cmd} exited with {e.exit_code}') # Command /usr/bin/cat /tmp/doesnt/exist exited with 1

curl = sh.curl('https://httpbin.org/delay/5', \_bg=True)
try:
curl.wait(timeout=3)
except sh.TimeoutException:
print("Command timed out...")
curl.kill()
```

선택적으로, 만약 프로세스가 시그널에 의해 종료된다면, SignalException을 받게 될 거에요. 특정 시그널을 확인할 수 있는데 예를 들면 SignalException_SIGKILL(또는 \_SIGTERM, \_SIGSTOP 등)으로 확인할 수 있어요.

이 라이브러리에는 내장된 로깅 지원도 있어요. 켜기만 하면 되는데요. 다음 코드가 도와줄 거에요:

```md
import logging

# 기본 로깅 켜기:

logging.basicConfig(level=logging.INFO)
sh.ls('-la')

# INFO:sh.command:<Command '/usr/bin/ls -la', pid 1631463>: process started

# 로그 레벨 변경:

logging.getLogger('sh').setLevel(logging.DEBUG)
sh.ls('-la')

# INFO:sh.command:<Command '/usr/bin/ls -la', pid 1631661>: process started

# DEBUG:sh.command:<Command '/usr/bin/ls -la'>: starting process

# DEBUG:sh.command.process:<Command '/usr/bin/ls -la'>.<Process 1631666 ['/usr/bin/ls', '-la']>: started process

# ...
```


<div class="content-ad"></div>

위의 예제들은 대부분의 사용 사례를 다룰 수 있지만, 더 고급/난해한 경우에는 라이브러리 문서의 튜토리얼이나 FAQ를 확인해보세요. 여기에는 추가적인 예제들이 있습니다.

# 마지막으로

다시 강조하고 싶은 점은 항상 시스템 명령어를 사용하는 대신 네이티브 Python 함수를 선호해야 한다는 것입니다. 또한 CLI 명령어를 직접 실행하는 대신 Kubernetes-client나 클라우드 제공업체의 SDK와 같은 서드파티 클라이언트 라이브러리를 사용하는 것을 항상 선호해야 합니다. 내 의견으로는, 쉘 대신 Python에 더 익숙하다면 시스템 관리자 배경에서 오더라도 적용됩니다. 마지막으로, Python은 쉘보다 훨씬 강력하고 견고한 언어이지만, 다른 프로그램/명령어를 너무 많이 연결해야 하는 경우에는 아마도 쉘 스크립트를 작성하는 것이 나을 수도 있습니다.

```js
연락하고 싶으세요?

이 글은 원본이 martinheinz.dev에 게시되었습니다.
```
