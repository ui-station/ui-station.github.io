---
title: "라즈베리 파이 마스토돈 인스턴스 백업은 잘 정리되어 있나요"
description: ""
coverImage: "/assets/img/2024-05-23-TheRaspberryPiMastodonInstanceDoYouHaveYourBackupsSorted_0.png"
date: 2024-05-23 17:00
ogImage:
  url: /assets/img/2024-05-23-TheRaspberryPiMastodonInstanceDoYouHaveYourBackupsSorted_0.png
tag: Tech
originalTitle: "The Raspberry Pi Mastodon Instance: Do You Have Your Backups Sorted?"
link: "https://medium.com/better-programming/the-raspberry-pi-mastodon-instance-do-you-have-your-backups-sorted-a99dab81fdd5"
---

![Raspberry Pi Mastodon Instance](/assets/img/2024-05-23-TheRaspberryPiMastodonInstanceDoYouHaveYourBackupsSorted_0.png)

얼마 전에 나는 라즈베리 파이에 나만의 Mastodon 인스턴스를 설정했어. 왜냐하면? 음, 나는 인터넷에서 기본 통신 인프라를 되찾는 것이 중요하다고 생각하기 때문이야. 게다가, 거대한 분산 네트워크에서 자신의 소셜 미디어 노드를 설정하는 것은 백엔드 아키텍처에 대해 배울 수 있는 좋은 기회야. 좋은 소식은, 상대적으로 쉽고 보람있어! 지난해 4월에 파이를 설정한 후, 전혀 신경 쓸 일이 없었어.

하지만, 물론, 문제가 발생하기도 해. 그리고 여기서 이야기하고 싶은 것이 바로 그거야. 당신이 자체 호스팅된 인스턴스가 고장났을 때 어떻게 되는지, 다시 가동하는 것이 얼마나 어려운지에 대해 이야기할 거야. 이 기사에서는 나의 경험을 공유하고 라즈베리 파이 Mastodon 인스턴스를 운영하는 데 궁금해하는 사람들을 위한 조언 몇 가지를 전할 거야. 하지만 그 전에, 내 설정에 대해 조금 언급해야 해.

## 어떤 것을 사용하고 있을까요?

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

모든 것을 직접 실행하고 있기 때문에 '클라우드' 컴퓨팅 서비스를 사용하지 않고 모든 서버 구성 요소에 대한 책임을 짊어지고 있어요. 주변에 Raspberry Pi 4 Model B(2018)가 있었는데, 표준 라우터와 독립적인 USB-C 전원 공급 장치에 연결했어요. 라우터 연결에는 이더넷 케이블 및 Pi 위치에 따라 Wi-Fi를 사용했어요. Mastodon이 사용하는 포트(80번은 HTTP, 443번은 HTTPS, 22번은 SSH)를 라우터로 포워딩하고 Raspberry Pi의 로컬 주소(터미널에서 hostname -I를 입력하여 확인)로 포트를 포워드하도록 라우터를 구성하세요. 그런 다음 도메인 이름의 DNS 설정을 사용하여 같은 포트를 라우터의 공개 IP 주소로 포워드하세요.

![Raspberry Pi Image](/assets/img/2024-05-23-TheRaspberryPiMastodonInstanceDoYouHaveYourBackupsSorted_1.png)

Raspberry Pi는 훌륭한 서버로 사용될 수 있어요. 이 작고 저렴한 컴퓨터는 놀랄 만큼 빠르고 8GB의 램을 갖추고 있어요. 작은 Mastodon 인스턴스에 충분하죠. 작은 친구는 주 시스템 드라이브로 32GB SD 카드(Kingston Canvas Select Plus)를 사용했어요. 이 SD 카드에 대해서는 조금 더 말씀드리겠지만, 괜찮긴 하지만 화려하거나 빠르지는 않아요. 제 운영 체제는 SD 카드에 미리 설치된 기본 Raspbian Linux에요. Mastodon을 가동하기 위해 joinmastodon.org에서 '기기 준비' 및 '소스로부터 설치' 문서를 따라했어요(그 순서대로). 상대적으로 간단했어요.

## 메모리 고려하기

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

좋아요, 좀 더 깊게 메모리에 대해 알아봅시다. 당신이 Mastodon 인스턴스를 직접 호스팅해야 한다고 생각하면 중요하니까요. 여기서 기본 설정과는 달라지는 부분이 있습니다.

내가 하는 것처럼 소수의 계정만 호스팅하더라도, 당신의 인스턴스는 상당량의 기가바이트 데이터를 누적하게 됩니다. 이 데이터는 한편으로는 텍스트 상태를 저장하는 PostgreSQL 데이터베이스에 (타임라인을 이루는 텍스트 상태뿐만 아니라 모든 시스템 환경 및 설정) 저장되고, 다른 한편으로는 ~/live/public/system이 기본값인 폴더에 저장됩니다 (프로필 사진, 공유 미디어 등이 이 폴더에 저장됩니다). 특히 후자의 폴더는 소셜 네트워크가 커짐에 따라 매우 부피가 커진다는 것을 상상할 수 있을 것입니다 — 어떤 사람들은 비디오를 올리는 걸 좋아하잖아요.

라즈베리 파이가 메모리 부족 상태가 되는 것을 방지하기 위해, 라즈베리 파이의 USB 포트에 연결된 1테라바이트 삼성 포터블 SSD를 추가했습니다. (위의 그림에서 파이는 드라이브 위에 놓여 있습니다.) 이제 부피가 커진 /system/ 폴더가 여기에 위치하게 됩니다. 기본 설정을 재정의합니다. Mastodon이 미디어를 저장하는 위치는 ~/live/.env.production 파일에서 변경할 수 있으며, Mastodon을 위한 Nginx 구성에 별칭을 추가해야 할 수도 있습니다. 나의 경우에는 .env.production 파일에 설정하였습니다:

```js
PAPERCLIP_ROOT_PATH= /media/mastodon/system
PAPERCLIP_ROOT_URL= /storage/system
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

Nginx 구성 파일에 다음 줄을 HTTP 및 HTTPS 구성에 모두 추가해주세요:

```js
location /storage {alias /media/mastodon; }
```

또한 외부 드라이브가 올바르게 마운트되었는지 확인하고 사용자 mastodon이 해당 드라이브에 쓸 수 있도록 해야 합니다.

처음 드라이브를 추가했을 때 제 서버는 행복하게 실행되었지만 미디어 업로드를 할 때마다 500 오류가 발생했습니다 (비슷한 오류를 만나면 터미널에서 journalctl -u mastodon-web -f를 실행하여 해당 인스턴스와 주고받는 HTTP 요청의 실시간 뷰를 확인할 수 있습니다). 제 경우 문제는 소프트웨어를 실행하는 'mastodon' 사용자가 마운트된 SSD 드라이브에 쓰기 권한이 없었기 때문입니다! 업로드가 실패한 것이 당연했습니다. 이 문제를 해결하기 위해 /etc/fstab 파일(리눅스의 마운트 포인트를 정의하는 파일)에서 드라이브 항목을 수정하여 모든 사용자에게 읽기 및 쓰기 권한을 부여했습니다.

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

/dev/sda1 /media vfat rw,user 0 0

이 설정은 몇 번의 시행착오로부터 나왔어요; 적어도 내 시스템에는 작동합니다. 만약 내가 무언가를 형편없게나 안전하지 않게 한 것 같다면 알려주세요! 하지만 이 메모리 솔루션은 더 이상 버벅거리거나 스크롤하는 데에 한 년 이상 사용해보니 잘 작동하고 있어요.

/system/ 폴더는 52.7 GB를 차지하고있어요 (~디스크 공간의 5%). 캐시된 미디어 파일을 31일 후에 지우도록 인스턴스를 구성했기 때문에 급속히 커지는 것이 아니에요. 여기에는 충분한 공간이 있어요. 반면에, 내 Postgres 데이터베이스는 여전히 기본 위치인 /var/lib/postgresql/11/main에 저장되어 있어요. (내 Postgres 버전이 11이라면서요, 잘 작동하더라구요.) 현재 Postgres 데이터베이스의 크기는 2.55 GB이에요. 이는 32 GB 시스템 드라이브의 8%에 해당하기 때문에 큰 재앙은 아니지만, 내 취향에는 너무 비대해요 (때로는 드라이브 공간이 17% 밖에 남지 않는 경우도 있었어요). 언젠가는 Postgres 데이터베이스를 외부 SSD로 이동하고 싶다고 생각해요. 그건 그때의 이야기거든요.

라즈베리 파이 마스토돈 인스턴스에 추가 드라이브를 연결하는 것은 약간 복잡할 수 있고, 정확한 설정은 외부 드라이브의 파일 시스템에 따라 다를 거에요. 하지만 이렇게 하는 것은 거의 항상 좋은 생각이에요 (아래에서 설명할 이유 중 하나 때문이죠).

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

## 하지만 백업은 하시나요?

처음에 말했듯이, 잘못된 일이 벌어질 때에 대해 이야기하려고 합니다. 자체 인스턴스를 호스팅할 때는 서버가 다운될 경우를 생각해봐야 합니다. 시간이 흘러 저는 방대한 소셜 미디어 네트워크를 구축했고 거의 매일 친구들과 소통하기 위해 Mastodon을 사용합니다. 그리고 내일 그 모든 것이 여전히 남아있을 것을 누군가에게 의존할 수 없습니다. 다시 말해, 백업 계획이 없다면 인스턴스에 호스팅된 계정들과 함께 하룻밤 사이에 모든 것을 잃을 수 있습니다: 게시물, 사진 및 중요하게도 인스턴스의 사람들이 구축한 네트워크까지.

따라서 Raspberry Pi Mastodon 인스턴스를 설정하기 전에 위험 평가를 수행해야 합니다. 내 구성을 고려할 때 무엇이 잘못 될 수 있는지 스스로에게 묻고, 그럴 경우 얼마나 나쁠지 고민해보고, 일이 심각하게 잘못될 때 원하는 기능을 복구하는 데 필요한 것이 무엇인지 생각해보세요. 분명히, 일부 문제에는 인내가 필요할 수도 있습니다(예: 인터넷 제공업체 문제). 그러나 다른 문제는 남아있는 것에서 서버를 복원해야 할 수도 있습니다.

모든 경우들 중에서, 저는 가장 준비하고 싶었던 위험은 SD 카드의 고장이었습니다. SD 카드에서 리눅스를 실행하는 것은 SD 카드가 불안정하기 때문에 좋은 생각은 아닙니다. 그러나 어쨌든, 그것이 Raspberry Pi가 하는 일이기 때문입니다. 따라서 SD 메모리가 복구할 수 없을 정도로 고장 나서 리눅스와 Mastodon 소프트웨어를 처음부터 다시 설치해야 하는 상황이 오면 필요한 것이 무엇일까요?

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

위험을 완화하기 위해 두 가지 방안을 고안했어요.¹ 첫 번째는 이미 이야기한 것처럼, /system/ 폴더를 훨씬 신뢰할 수 있는 SSD 드라이브로 이동하는 거예요. SD 카드 고장 시에도 미디어 저장 공간이 안전하게 보호될 거예요. 좋아요. 그런데 PostgreSQL 데이터베이스는 어떨까요? 이 데이터들은 아마도 인스턴스가 구축한 소셜 네트워크를 정의하는 만큼 더 중요할 거예요.

결국, 매일 데이터베이스를 자동으로 백업하기로 결정했어요. 매일 밤 실행되는 쉘 스크립트를 작성했어요 (/etc/crontab에 추가했어요). 이 스크립트는 Postgres 데이터베이스 내용(기본적으로 mastodon_production이에요)을 외부 SSD에 저장된 백업 파일로 덤프하는 거예요.

이를 위해 다음 명령어를 스크립트로 작성했어요: sudo -u postgres pg_dump mastodon_production ` /media/backup/mastodon_production.dump. 추가적인 안전을 위해 파일을 gzip(암호화)하고 FTP를 통해 다른 원격 서버로 보내는 거예요. 그 서버에는 가장 최근의 .dump 파일 세 개를 유지하고, 이전 .dump 파일은 자동으로 삭제돼요. (언젠가 스크립트를 GitHub에 공유할 수도 있겠지만, 특별한 것은 아니에요.)

마침내, 지난 주에 위험이 현실이 되었어요. 라즈베리 파이의 SD 카드가 고장나서 기계에 완전히 접근할 수 없었어요. 제 백업 전략은 충분했을까요?

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

## 백업에서 복원하기

정확한 대답은: 아니요. 제 백업 전략이 충분하지 않았습니다. 그러나 운이 좋게도 충분히 가까웠습니다. 온라인으로 돌아왔고, 이것은 마스토돈 인스턴스를 복구하는 것이 어렵지 않음을 증명했습니다. 심지어 Raspbian 운영 체제와 마스토돈 소프트웨어를 처음부터 다시 설치해야 하는 경우에도요. 단지 백업 전략을 설정하기만 하면 됩니다.

그래서 무엇이 있었을까요? 제 추측으로는 Pi를 잘못해서 전원을 끄면 SD 카드의 부트 섹터가 손상되었습니다. 이것은 매우 쉽게 발생합니다. 많은 사람들이 전원이 차단된 후 데이터를 잃은 적이 있습니다. 고려하지 못하고 나는 Pi를 다른 위치로 옮기기 위해 연결을 해제하고 싶을 때 데스크톱에서 '종료'를 클릭했습니다. 이후에야 작년에 스스로에게 남겨둔 안전하게 종료하는 가장 좋은 방법에 대한 메모를 읽었는데, 터미널에서 sudo shutdown -h now만 사용해야 한다는 것을 알게 되었습니다(-h 옵션은 시스템이 하고 있는 모든 일을 중단하도록 안내합니다). 우측. 어쨌든, 시스템을 부팅하지 못하게 되었고, 따라서 더 이상 마스토돈 서버를 실행할 수 없었습니다.

무엇을 해야할까요? 조금 당황해서 맥에서 Ubuntu를 실행하는 VirtualBox 머신을 사용하여 카드를 외장 드라이브로 마운트하고 표준 디스크 도구와 터미널에서의 디스크 관리 프로그램 fsck로 수리하려고 노력했습니다. 운이 없었습니다. 그러나 Ubuntu에서는 /home/ 폴더를 포함하는 파티션을 마운트하고 액세스할 수 있었기 때문에 복구에 필요한 몇 가지 파일을 아직 백업하지 않은 상태로 구할 수 있었습니다. 경성.

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

어떤 파일이 필요할까요? 이 질문에 대한 최상의 답변을 Mastodon 문서에서 찾았어요. '새로운 장치로 이전하기'라는 설명에서 찾았죠. 왜냐하면 Raspbian과 Mastodon 소프트웨어를 처음부터 다시 설치해야 할 때 해야 할 일이기 때문이에요. 새로운 장치를 설정하고 있는 것이거든요. 제 구성을 고려하면, 서버 기능을 간단하게 복구하기 위해 필요한 것들의 목록은 아래와 같아요:

- Postgres 데이터베이스 덤프 (기본적으로 이것은 mastodon_production입니다)
- /system/ 폴더의 복사본 (기본적으로 이것은 ~/live/public/system입니다)
- ~/live/.env.production 파일의 복사본
- Nginx 구성 파일의 복사본 (기본적으로 /etc/nginx/sites-available/mastodon)

SD 카드가 다운됐을 때, 외부 SSD에 (1)과 (2)가 포함되어 있었어요. 이건 꼭 필요한 파일이에요. 그리고 (3)과 (4)에 대해서는, 다행히 다른 리눅스 장치에 장착했을 때 카드로부터 여전히 이 파일들을 검색할 수 있었어요.

해당 추가 파일들은 제 인스턴스의 특정 구성 설정을 포함했어요. 외부 미디어 폴더를 위한 여분의 사항, 알림에 사용된 SMTP 서버의 세부사항, 활성 브라우저 세션, 이중 인증 및 푸시 알림을 위한 '비밀' 등을 포함해요. 꼭 필요한 건 아니지만, 이 파일들이 있어서 복구가 가능했던 것이죠. 이 모든 정보를 필요한대로 정확히 가지고 있어서 제 삶을 훨씬 쉽게 만들어줬어요. 큰 도움이 되었어요.

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

이제 긴급한 상황에서 해당 파일이 필요할 것이라는 사실은 나를 놀라게 할 필요가 없었습니다. 왜냐하면 이전에 내가 필요에 맞게 조정한 파일들이었기 때문이었습니다! 그럼에도 불구하고, 그들을 백업 루틴에 포함시키는 것을 잊었습니다.

이것이 내 초기 백업 계획이 잘못된 한 가지였습니다. 하지만 적어도 똑같이 중요한 것: 필요할 때까지 백업을 테스트해보지 않았습니다. 실제 문제가 발생했을 때, 가장 중요한 백업 파일은 있음에도 불구하고 어떻게 복원해야 할지 몰랐습니다. 이로 인해 상황이 불필요하게 스트레스 받는 일이 되었습니다.

나에게는 앞으로의 교훈입니다. 단순히 백업 파일을 생성했다고 만족하지 마세요. 이러한 파일을 만드는 이유인 백업 복원 프로세스의 다른 절반을 놓치지 마세요. 백업만 하는 것이 아닌, 백업으로부터 데이터를 복원하는 연습도 해보세요. 백업을 의존하기 전에 해당 백업에서 데이터를 복원하는 연습을 하세요. 그렇게 하였더라면 내 백업이 충분하지 않다는 것을 알게 되었을 것입니다. 실제 문제가 발생했을 때 무엇을 해야 하는지 알고 있다면, 상황을 훨씬 효율적으로, 더 적은 스트레스와 땀으로 복구할 수 있습니다.

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

이번에는 이야기가 행복한 결말을 맞이했습니다. 이 글을 쓰는 이유 중 하나는 아마도 나중에 이와 비슷한 문제에 직면할 수도 있기 때문이고, 필요한 단계를 상기시키기 위해 매뉴얼이 있으면 유용하다고 생각하기 때문입니다. 물론 당신에게도 도움이 될 수 있기를 바라겠습니다.

특히, 라즈베리 파이에 자신만의 Mastodon 인스턴스를 실행하는 것이 덜 두렵다면 고대로 해보길 희망합니다. 진정으로 말하면, 온라인 커뮤니케이션을 제어하기 위한 실험을 해볼 수 있습니다. 결국, 머신에 접근 권한을 잃는 것이 처음에 보이는 것만큼 무섭지 않습니다. 시작하기 전에 위험 평가를 수행하고, 신뢰할 수 있는 백업 루틴을 시행하고, 즉시 필요해지기 전에 백업에서 머신 복원을 실습하는 시간을 갖는다면 문제 없습니다. 이것들을 수행한다면, 작은 파이의 성능을 신뢰할 수 있을 것입니다.

¹ 시도해보지 않은 대안적인 전략: SD 카드를 정기적으로 복제하는 것입니다. 복제본이 있다면 원본이 손상된 경우 카드를 교체하고 다시 사용할 수 있습니다. PostgreSQL 덤프 대비 정기적인 복제가 번거로울 것으로 생각해서 이 방법을 선택하지 않았습니다. 하지만 여기에 좋은 대안이 있는 것을 우연히 놓친 것일 수도 있습니다.

² 공식 문서의 목록이 더 상세하지만, 다른 몇 가지 기본 설정을 사용했기 때문에 다른 여러 파일들에 대해 걱정할 필요가 없었습니다. 그러나 서버 사용을 시작하기 전에 '새로운 머신으로 이동' 문서를 읽고, '서버 백업' 문서를 읽는 것이 좋습니다.
