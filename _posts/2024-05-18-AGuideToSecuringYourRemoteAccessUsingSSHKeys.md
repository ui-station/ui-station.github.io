---
title: "원격 액세스 보안하는 방법 SSH 키 사용 안내"
description: ""
coverImage: "/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_0.png"
date: 2024-05-18 17:32
ogImage:
  url: /assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_0.png
tag: Tech
originalTitle: "A Guide To Securing Your Remote Access Using SSH Keys"
link: "https://medium.com/bugbountywriteup/a-guide-to-securing-your-remote-access-using-ssh-keys-84b48097f3bf"
---

SSH 키 기반 인증을 사용하여 SSH 연결을 안전하게 하는 단계별 가이드

![SSH Key-Based Authentication](/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_0.png)

오늘의 글은 SSH 원격 세션의 보안을 강화하는 간단한 자습서를 제공합니다. Linux 서버에 SSH 키 기반 인증을 설정하는 방법에 중점을 두고 덜 안전한 암호 기반 인증에서 전환하는 방법을 안내합니다. 마지막으로 SSH 서버를 더 안전하게 하는 추가 방법에 대한 보너스 팁을 공유할 것입니다.

# 개요

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

SSH (Secure Shell) 키 기반 인증은 원격 서버에서 사용자의 신원을 인증하기 위해 암호화 키 쌍을 사용하는 보안 방법입니다. 이 방법은 컴퓨터에 공개 및 비밀 키 쌍을 생성하여 저장하고, SSH 서버를 구성하여 이러한 키를 인식하고 수락하도록 설정하는 것을 포함합니다. 이는 기존 암호 기반 인증과 관련된 위험을 줄이는 것으로 보안을 크게 향상시킵니다.

SSH 키는 쌍으로 생성되고 일반 텍스트 파일로 저장됩니다. 키 쌍은 두 부분으로 구성됩니다:

- 🔒 공개 키: 이 부분은 SSH 서버에 저장됩니다. 안전하게 공유할 수 있어 해당 서버가 연결할 때 신원을 인식할 수 있습니다.
- 🔑 비밀 키: 이 부분은 사용자의 컴퓨터에 저장되어야 하며 항상 안전하게 보관되어야 합니다. 이를 사용하여 서버와 인증하며, 절대로 공유해서는 안 됩니다. 비밀 키는 파일을 읽을 수 없도록 사용 권한을 설정해야 합니다.

## 작동 방식

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

- 컴퓨터에서 공개 및 개인 키 쌍을 생성합니다.
- 서버는 공개 키를 인식하고 해당하는 개인 키가 인증 중에 사용되면 액세스를 허용하도록 구성됩니다.
- 선택적으로 개인 키에 암호를 설정하여 추가 보안을 제공할 수 있습니다. 이 암호는 잘못된 손에 키가 넘어갈 경우 추가 보호층을 제공합니다.

# SSH 키를 사용한 인증의 장점

SSH 키 기반 인증은 비밀번호보다 더 나은 대안이므로 보안성과 편리성이 강화됩니다. 이렇게 왜 SSH 키를 사용해야 하는지 살펴보겠습니다:

- 향상된 보안: 키가 고유하고 추측하기 어려우므로 키 기반 인증은 브루트 포스 공격에 저항합니다.
- 자격 증명 노출 위험 감소: 서버가 침해당한 경우 권한 부여 자격 증명이 노출되는 위험이 없습니다.
- 사용의 용이성: 사용자들은 복잡한 암호를 기억하거나 적어두지 않아도 되므로, 암호 관련 위반 사례의 위험이 줄어듭니다.
- 자동화 지원: 암호 입력이 필요하지 않기 때문에 작업을 자동화할 수 있으며, 수동 개입 없이 Ansible과 같은 스크립트 및 도구가 SSH 서버와 상호작용할 수 있습니다.

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

# SSH를 위한 공개 키 인증 설정하기

기본 사항을 다뤘으니, 이 작업을 완료하는 단계에 대해 자세히 살펴보겠습니다.

SSH 키 쌍을 만드는 두 가지 옵션이 있습니다: Windows 기계 또는 Linux 기계에서. 단계는 일반적으로 비슷하지만, 각 환경에서 사용하는 소프트웨어 도구는 다릅니다.

## 방법 1: Linux에서 SSH 키 쌍 생성하기

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

스텝 1: Ed25519 알고리즘을 사용하여 새 SSH 키 쌍을 생성하세요.

이 단계는 키페어를 로컬 컴퓨터에서 생성한 후에 나중에 서버로 업로드할 것으로 가정합니다. 서버에서 키페어를 생성하고 개인 키를 로컬 호스트로 다운로드하는 것보다 안전합니다.

Ed25519 알고리즘을 사용하여 새 키 쌍을 생성하세요:

```js
ssh-keygen -t ed25519 -C "cybersecmav@warfare.systems"
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

- -t: 알고리즘의 종류 (키), 우리의 경우 Ed25519.
- -C: SSH 키를 구분할 수 있도록 도와주는 설명 (선택 사항).

파일 이름을 요구하면 기본 이름과 경로를 수락하려면 Enter 키를 누르세요. SSH 키는 일반적으로 ~/.ssh/ 디렉터리에 저장됩니다.

![이미지](/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_1.png)

단계 2: 원격 서버 준비하기

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

만약 ~/.ssh 디렉토리와 authorized_keys 파일이 존재하지 않는다면 다음을 실행해주세요:

```js
mkdir ~/.ssh
touch ~/.ssh/authorized_keys
```

~/.ssh 디렉토리에 위치한 authorized_keys 파일은 SSH 서버에 액세스할 수 있는 공개 키 목록을 포함하고 있습니다.

~/.ssh 디렉토리와 authorized_keys 파일에 적절한 파일 권한을 설정해주세요:

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

```js
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

- chmod 700 ~/.ssh: 해당 디렉토리의 파일을 읽기, 쓰기, 실행할 수 있는 권한을 소유자에게만 부여합니다.
- chmod 600 ~/.ssh/authorized_keys: 소유자에게만 파일에 액세스할 수 있도록 제약을 두어 다른 사람이 파일을 보거나 수정하는 것을 방지하여 SSH 키를 보호합니다.

로컬 머신에서 공개 키를 원격 서버로 업로드하세요:

```js
scp id_ed2551.pub cybersecmav@warfare.systems:/home/cybersecmav/id_ed2551.pub
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

아래와 같이 업로드한 공개 키를 .ssh/ 디렉터리로 이동해 주세요:

```js
mv id_ed2551.pub ~/.ssh/
chmod 600 ~/.ssh/id_ed2551.pub
```

공개 키의 출력을 authorized_keys 파일로 복사해 주세요:

```js
cat ~/.ssh/id_ed2551.pub >> ~/.ssh/authorized_keys
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

authorized_keys의 내용을 확인하여 공개 키가 추가되었는지 확인해보세요:

```js
cat .ssh/authorized_keys
ssh-ed25519 AAA<<...REDACTED......>>1uEqXysh cybersecmav@warfare.systems
```

모든 올바른 폴더 및 파일 권한이 있는지 확인해봅시다:

```js
cybersecmav@ares:~$ cd .ssh
cybersecmav@ares:~/.ssh$ ls -la
total 16
drwx------ 2 cybersecmav cybersecmav 4096 Apr 26 14:32 .
drwxr-xr-x 5 cybersecmav cybersecmav 4096 Apr 29 13:32 ..
-rw------- 1 cybersecmav cybersecmav  109 Apr 26 14:33 authorized_keys
-rw-r--r-- 1 cybersecmav cybersecmav  109 Apr 23 19:49 id_ed2551.pub
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

Step 3: 공개 키 인증을 지원하도록 SSH 서버 구성하기

편집을 위해 SSH 서버 구성 파일을 엽니다:

```js
sudo nano /etc/ssh/sshd_config
```

PubkeyAuthentication 설정을 찾아서 아래로 스크롤합니다. "no"에서 "yes"로 변경하세요. 또한, AuthorizedKeysFile로 시작하는 줄이 주석 처리되어 있지 않은지 확인하거나 누락된 경우 추가하세요.

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

```plaintext
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys .ssh/authorized_keys2
```

다음은 /etc/sshd_config 파일이 보이는 방식입니다:

![2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_2.png](/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_2.png)

4단계: 구성 변경을 적용하기 위해 SSH 서버를 다시로드하세요

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

sshd_config 파일을 변경할 때는 변경 사항이 적용되려면 SSH 서버를 다시로드하거나 다시 시작해야 합니다.

Debian/Ubuntu:

```js
sudo systemctl reload ssh
sudo systemctl restart ssh
```

Redhat/CentOS:

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

```js
sudo systemctl reload sshd
sudo systemctl restart sshd
```

## 방법 2: Windows에서 SSH 키 쌍 생성

로컬 머신으로 Windows를 사용하고 있다면, PuTTY 클라이언트의 키 생성기인 puttygen.exe를 사용하여 SSH 키 쌍을 생성할 수 있습니다.

- 다음 위치에서 PuTTY를 다운로드하세요: https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html
- puttygen.exe를 엽니다. 기본적으로 RSA를 키 생성으로 선택하지만, 원격 SSH 서버가 지원하는 경우 ED25519를 선택할 수도 있습니다.

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

<img src="/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_3.png" />

- "Generate"를 클릭하여 새로운 공개/개인 키 쌍을 생성합니다.

<img src="/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_4.png" />

- 나중에 원격 서버의 ~/.ssh/ 디렉토리의 authorized_keys 파일에 붙여 넣을 공개 키를 복사합니다.

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

![image](/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_5.png)

- Save the public key as: id_ed2551.pub
- Save the private key as: id_ed2551.ppk

![image](/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_6.png)

- Alternatively, you can save them using the File menu option

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

![Save and keep a copy of your SSH keys](/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_7.png)

![SSH key image 2](/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_8.png)

![SSH key image 3](/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_9.png)

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

- `/home/username/.ssh/authorized_keys` 파일에 공개 키 값을 새 줄에 붙여 넣으세요.

![Step 10](/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_10.png)

- 윈도우즈에서 PuTTY SSH 클라이언트를 사용하여 키 기반 인증을 통한 원격 호스트로의 SSH 연결을 테스트합니다.
- putty.exe 클라이언트를 열고 원격 SSH 서버 세부 정보를 입력하세요.

![Step 11](/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_11.png)

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

- 지금 가장 중요한 단계는 리모트 서버에 연결하기 전에 방금 생성한 올바른 개인 키를 선택했는지 확인하는 것입니다.

![SSH Key](/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_12.png)

- "Open"을 클릭하면 원격 대상과의 SSH 세션이 열립니다.

- 이제 새로운 공개/개인 키 쌍을 사용하여 인증된 SSH 세션이 열려 있어야 합니다. 아래 예시를 확인해주세요.

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

<img src="/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_13.png" />

이제 SSH 키 기반 인증으로 로그인되었습니다.

# 추가 정보: SSH 서버 보안 팁

SSH 키 기반 인증의 기본 사항을 다룬 이제, SSH 서버를 보다 더 보호하기 위한 몇 가지 추가 보안 팁을 살펴봅시다. 이러한 모범 사례를 시행함으로써 SSH 서버의 보안성을 높일 수 있습니다.

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

## 팁 1: 비밀번호 인증 비활성화

SSH 키 기반 인증이 작동하는지 확인한 후, SSH 구성 파일인 /etc/ssh/sshd_config에서 PasswordAuthentication을 "no"로 설정하여 비밀번호 기반 로그인을 비활성화하세요.

```sh
sudo nano /etc/ssh/sshd_config
```

![이미지](/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_14.png)

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

## 팁 2: 루트 로그인 제한

SSH를 통한 직접적인 루트 액세스를 방지하려면 PermitRootLogin을 "no"로 설정하세요. 이렇게 하면 사용자가 루트 계정으로 로그인하는 것이 불가능해지며 관리 작업을 위해 sudo를 사용해야 합니다.

![이미지](/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_15.png)

## 팁 3: 비표준 SSH 포트 사용

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

기본 SSH 포트를 22에서 더 높거나 보통과 다른 숫자로 변경하면 무분별한 자동 스캔 및 브루트포스 공격이 더 어려워질 수 있습니다. 이는 위협의 대부분을 이루는 것입니다. 그러나 SSH 서버를 모든 공격으로부터 완전히 보호하는 것은 아니라는 것을 명심하세요. 결정적인 해커는 여전히 SSH 포트를 찾아내고 대상으로 삼을 수 있습니다.

![Image](/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_16.png)

## 팁 4: SSH 로깅 및 모니터링 활성화

SSH 로깅이 활성화되어 있어야 연결 시도를 추적하고 이상한 활동을 감지할 수 있습니다. 무단 액세스나 브루트포스 공격의 징후를 탐지하기 위해 로그를 정기적으로 모니터링하세요.

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

대부분의 시스템에서 SSH 로깅은 기본적으로 활성화되어 있습니다. 그러나 SSH 설정 파일 /etc/ssh/sshd_config을 확인하여 비활성화되어 있지 않은지 확인해보세요.

단계 1: SSH 서비스 구성 파일에서 SSH 로깅 활성화하기

SyslogFacility를 "AUTH"로 설정하고 LogLevel은 "INFO"로 설정하세요.

![이미지](/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_17.png)

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

다른 로그 레벨을 탐색하려면 아래 섹션을 참조하세요. Verbose 및 Debug는 많은 로그를 생성하므로 진단 또는 디버깅에만 유용하기 때문에 권하지 않습니다.

SSH 로그 레벨

- QUIET: 거의 아무것도 로그하지 않아 최소한의 출력을 제공합니다.
- FATAL: SSH 서버 작동을 중지하는 중요한 오류만 로그합니다.
- ERROR: 중요한 문제 및 심각한 오류를 로그합니다.
- INFO: 기본 레벨로, SSH 작업에 대한 일반 정보를 로깅합니다.
- VERBOSE: SSH 프로세스에 대한 자세한 정보를 로그하며, 문제 해결에 유용합니다.
- DEBUG: 디버깅을 위한 방대한 로그를 생성하며, 일반적으로 일상적으로 사용하지 않습니다.

단계 2: 로그 저장소 구성

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

보통 SSH 로그는 /var/log/auth.log (Debian/Ubuntu) 또는 /var/log/secure (Red Hat/CentOS)에 저장됩니다.

```js
$ ls -la /var/log/auth.log
-rw-r----- 1 root adm 292525 May 4 10:18 /var/log/auth.log
```

이 파일들이 존재하고 접근 가능한지 확인하세요. 소유자가 root로 설정되어 있고 파일 권한이 로그 파일을 안전하게 보호하기 위해 적절하게 설정되었는지 확인하세요.

```js
chmod 640 /var/log/auth.log
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

## 팁 5: 침입 탐지 (Fail2ban) 구현하기

Fail2Ban은 로그인 시도를 모니터링하고 많은 실패한 시도 후 IP 주소를 차단하여 SSH 서버를 보호할 수 있습니다. 이는 브루트포스 공격의 일반적인 징후입니다. 설치한 후에는 SSH 로그를 주시하도록 설정하고 의심스러운 IP를 차단할 규칙을 구성하세요. SSH 서버의 보안에 추가적인 방어층을 더하는 효과적인 방법입니다.

Fail2Ban을 설정하고 구성하는 것은 이 글의 범위를 벗어납니다. 그러나 추가 보안을 위해 서버에 설치하는 것을 적극 추천합니다.

# 결론

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

이 기사에서는 SSH 키 기반 인증의 혜택을 다루었는데, 이를 통해 보안을 강화하고 브루트포스 공격의 위험을 줄일 수 있다는 점을 강조했습니다. SSH 키를 생성하고 서버에 구성하는 방법을 안내하며 적절한 보안을 유지하는 방법을 보여드렸습니다. 또한 SSH 서버를 안전하게 유지하기 위한 추가 팁을 살펴봤는데, 이에는 기본 포트 변경, Fail2Ban 구현, 그리고 철저한 SSH 로깅 및 모니터링 활성화가 포함되었습니다.

전체적으로 SSH 키 기반 인증은 원격 액세스를 보다 안전하고 편리하게 관리하는 방법을 제공합니다. 패스워드 기반 로그인의 효과적인 대체제로서, 미인가된 액세스 가능성을 줄이고 보안을 희생하지 않고 자동화가 가능합니다.

권장 사항으로는 개인 키에 항상 암호를 설정하고, 이상한 활동을 정기적으로 모니터링하며, 키 기반 설정에 확신을 갖게 되면 패스워드 인증을 비활성화하는 것을 고려해야 합니다. Fail2Ban을 구현하면 브루트포스 공격의 징후를 보이는 IP 주소를 차단하여 자동화된 보호층을 추가할 수 있습니다.

이러한 단계를 따라가면 안전하고 신뢰할 수 있는 SSH 설정으로 나아갈 수 있습니다.

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

이 안내서를 통해 함께해 주셔서 감사합니다. SSH 연결을 안전하게 유지하고 서버를 안전하게 보호하는 데 도움이 되었으면 좋겠습니다! 🔐

안전 유지하시고 보안 유지하시길 바랍니다!

CyberSecMaverick

![Guide Image](/assets/img/2024-05-18-AGuideToSecuringYourRemoteAccessUsingSSHKeys_18.png)
