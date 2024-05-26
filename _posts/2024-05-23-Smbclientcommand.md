---
title: "Smbclient 명령어"
description: ""
coverImage: "/assets/img/2024-05-23-Smbclientcommand_0.png"
date: 2024-05-23 15:23
ogImage: 
  url: /assets/img/2024-05-23-Smbclientcommand_0.png
tag: Tech
originalTitle: "Smbclient command"
link: "https://medium.com/@ibo1916a/smbclient-command-2803de274e46"
---


샘바 파일 서버에 연결하는 두 가지 다른 방법이 있습니다. 아래와 같습니다:

터미널에서 smbclient 명령어를 사용하여 연결
" smb://filename " 형식의 주소를 입력하여 파일 시스템에서 연결
이 기사에서는 터미널에서 삼바 파일 서버에 연결하고 드라이브 트랜잭션을 수행하는 방법을 살펴보겠습니다. 시작해 봅시다 :)

Smbclient은 FTP 연결과 유사한 명령 줄 도구입니다. 이 명령은 SMB 리소스에 액세스를 제공합니다. 일반적인 smbclient 명령어는 다음과 같습니다:

SMB 공유 목록 열기:

<div class="content-ad"></div>

```js
smbclient -L //server_name -U users
```

SMB 공유: (비밀번호를 입력해야 함.)

```js
smclient //server/share -U user
```

SMB 공유에 직접 연결: (비밀번호 필요 없음, 하지만 비밀번호가 화면에 표시됨.)


<div class="content-ad"></div>

```js
smclient //server/share -U user%password
```

더 구체적인 용도를 위한 일반적인 smbclient 플래그는 아래에 나열되어 있습니다:

“-L” 플래그 (— list)는 서버의 공유를 나열하는 데 사용되는 플래그입니다.

“-U” 플래그 (— username [%password])는 파일 서버에 로그인할 때 사용할 사용자 이름 (및 선택적으로 암호)를 지정하는 데 사용됩니다.

<div class="content-ad"></div>

“-a” 플래그 (— authentication-file)는 연결을 설정하기 위한 사용자 비밀번호 정보를 보관하는 파일을 지정하는 데 사용하는 플래그입니다. 지정해야 하는 파일의 형식은 다음과 같아야 합니다.

“-B” 플래그 (— browse): 이 플래그는 DNS를 사용하여 SMB 서버를 찾습니다.

“-p” 플래그 (— port)는 연결할 포트를 선택하는 데 사용됩니다. 이 플래그를 사용하지 않으면 기본 포트는 포트 139입니다.

“-I” 플래그 (— IP-address IP-address)는 연결을 위해 NetBIOS 이름이 아닌 서버의 IP 주소를 제공하는 데 사용됩니다.

<div class="content-ad"></div>

"플래그( —— kerberos): Kerberos를 사용하여 인증을 시도하는 데 사용됩니다.

디버그 수준 플래그( —— debuglevel)은 더 자세한 정보를 로그 파일에 제공합니다. 0부터 10까지의 값이 제공될 수 있습니다.

또한, smbclient를 다양한 방법으로 연결할 수 있습니다. 다음과 같습니다:
1) 서버 NetBIOS 이름:

\`\`\`js
smbclient -L fileserver
\`\`\`"

<div class="content-ad"></div>

2) 해당 서버의 IP 주소로 다음 명령어를 사용하세요:

```js
smbclient -L x.x.x.x
```

3) 백슬래시를 사용하여 공유에 직접 링크를 하려면 다음과 같이 입력하세요:

```js
smbclient \\\\fileserver\\share
```

<div class="content-ad"></div>

4) 위의 옵션은 따옴표를 사용하여도 수행할 수 있습니다:

```js
smbclient "\\fileserver\share"
```