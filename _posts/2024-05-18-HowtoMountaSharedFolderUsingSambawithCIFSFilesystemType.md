---
title: "Samba를 사용하여 CIFS 파일 시스템 유형을 이용해 공유 폴더를 마운트하는 방법"
description: ""
coverImage: "/assets/img/2024-05-18-HowtoMountaSharedFolderUsingSambawithCIFSFilesystemType_0.png"
date: 2024-05-18 17:34
ogImage:
  url: /assets/img/2024-05-18-HowtoMountaSharedFolderUsingSambawithCIFSFilesystemType_0.png
tag: Tech
originalTitle: "How to Mount a Shared Folder Using Samba with CIFS Filesystem Type"
link: "https://medium.com/@dannysvof/how-to-mount-a-shared-folder-using-samba-with-cifs-filesystem-type-376ac603a004"
---

안녕하세요! 이 튜토리얼에서는 Linux 시스템에서 올바른 파일 시스템 유형(cifs)을 사용하여 Samba를 사용하여 호스트 머신의 공유 폴더를 마운트하는 단계를 살펴볼 것입니다.

# 단계 1: Samba 공유 설정

Samba 설치: 먼저 호스트 머신에 Samba를 설치합니다:

```js
sudo apt update
sudo apt install samba
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

Samba 구성: Samba 구성 파일을 편집하세요:

```js
sudo vim /etc/samba/smb.conf
```

파일의 끝에 다음 라인을 추가하세요:

```js
[shared_folder]
path = /공유할/폴더/경로
writable = yes
guest ok = yes
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

# 단계 2: 필요한 패키지 설치

시스템에 cifs-utils 패키지가 설치되어 있는지 확인하세요. 패키지 관리자를 사용하여 설치할 수 있습니다:

```js
sudo apt update
sudo apt install cifs-utils
```

# 단계 3: 호스트 머신의 IP 주소 찾기

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

다음 명령을 사용하여 호스트 머신의 IP 주소를 찾을 수 있습니다:

```js
ip addr show | grep inet | grep -v 127.0.0.1 | awk '{print $2}' | cut -d'/' -f1
```

이 명령은 호스트 머신의 IP 주소를 출력합니다.

# 4단계: 마운트 포인트 만들기

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

공유 폴더를 마운트할 디렉토리를 생성해보세요. 예를 들어, 홈 디렉토리에 shared_folder라는 디렉토리를 만들어봅시다:

```js
mkdir ~/shared_folder
```

# 단계 5: 공유 폴더 마운트하기

마운트 명령을 사용하여 공유 폴더를 마운트하세요. host_ip를 호스트 머신의 IP 주소로, shared_folder_name을 공유 폴더의 이름으로 대체하세요:

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
sudo mount -t cifs //호스트IP/공유폴더명 ~/공유폴더 -o guest
```

만약 공유 폴더에 인증이 필요하다면 사용자 이름과 비밀번호를 제공할 수도 있어요:

```js
sudo mount -t cifs //호스트IP/공유폴더명 ~/공유폴더 -o username=당신의사용자이름,password=당신의비밀번호
```

# 단계 6: 마운트 확인하기

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

공유된 폴더가 올바르게 마운트되었는지 확인하려면 마운트 지점 디렉터리의 내용을 나열하여 확인해보세요:

```js
ls ~/shared_folder
```

이 명령을 실행하면 공유된 폴더의 내용이 나열됩니다.

# 단계 7: 파일 접근 및 수정

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

이제 로컬 머신에서 공유 폴더의 파일에 직접 액세스하고 수정할 수 있어요.

# 단계 8: 공유 폴더에서 마운트 해제하기 (선택 사항)

공유 폴더 작업을 완료했을 때 umount 명령을 사용하여 마운트를 해제할 수 있어요:

```js
sudo umount ~/shared_folder
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

# 단계 9: 자동 마운트 (선택 사항)

시스템 부팅할 때마다 공유 폴더를 자동으로 마운트하려면 /etc/fstab 파일에 항목을 추가할 수 있습니다.

```js
//host_ip/shared_folder_name  /home/your_username/shared_folder  cifs  guest  0  0
```

호스트 IP, 공유 폴더 이름 및 /home/your_username/shared_folder를 세팅에 맞는 적절한 값으로 교체해주세요.

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

위 항목을 추가한 후 시스템을 재부팅하거나 다음과 같이 수동으로 공유 폴더를 마운트할 수 있습니다:

```js
sudo mount -a
```

끝났어요! 이제 올바른 파일 시스템 유형(cifs)을 사용하여 Samba를 통해 공유 폴더를 마운트했습니다. 이제 공유 폴더의 파일에 액세스하고 필요에 따라 작업할 수 있습니다.
