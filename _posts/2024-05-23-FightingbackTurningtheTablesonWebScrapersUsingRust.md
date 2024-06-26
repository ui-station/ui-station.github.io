---
title: "싸워라 웹 스크레이퍼를 상대로 맞선다 - Rust를 이용하여"
description: ""
coverImage: "/assets/img/2024-05-23-FightingbackTurningtheTablesonWebScrapersUsingRust_0.png"
date: 2024-05-23 18:46
ogImage:
  url: /assets/img/2024-05-23-FightingbackTurningtheTablesonWebScrapersUsingRust_0.png
tag: Tech
originalTitle: "Fighting back: Turning the Tables on Web Scrapers Using Rust"
link: "https://medium.com/@ejeriksson/declaring-holy-cyber-war-on-web-scrapers-using-rust-564df967511a"
---

웹 취약점을 스캔하는 사람들을 괴롭히고 싶은 적이 있나요? 저는 분명히 그랬어요. 이것은 저가 그들을 처벌하는 방법을 찾은 이야기입니다. 그리고 Rust를 사용하여 개선하고, 그리고 밴을 이용해 내 웹 서버를 멈춘 이야기입니다.

# 단계 0: 짜증남

좋아요, 만약 어떤 규모의 웹사이트를 운영해 보셨다면, 액세스 로그를 확인해 보면 곧 여러분의 웹사이트와는 상관없는 요청이 많이 들어온다는 사실을 발견하실 겁니다. 많은 요청 중에는 /wp-login.php, /.env 및 /.git/config 등과 같은 경로를 보는 것들도 많이 있습니다. 큰 회사들도 마찬가지입니다. 이겈 기회로 활용할 수는 없을까요?

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

# 단계 1: 지옥의 문을 찾아서

물론 가능해요! 우리 짜증나는 봇 친구들을 괴롭힐 방법을 찾던 중, HellPot이라는 HTTP 허니팟을 발견했어요. 이 허니팟은 웹사이트를 스크랩하려는 봇들을 충돌시키기 위해 만들어졌는데, 단순히 봇들이 요청한 것을 제공해주면 되요. HellPot에 지정된 경로(예: /wp-login.php)로 HTTP 요청을 보내면, 프리드리히 니체의 "비극의 탄생(헬레니즘과 비관론)"에서 나오는 웹사이트처럼 보이는 데이터의 영원한 스트림을 만날 거예요. 우리는 robots.txt에 동일한 경로를 넣어서 bingbot이 몇 메가바이트/초의 속도로 니체를 경험하는 걸 피할 거예요.

어떻게 가능한 걸까요? HTTP 응답이 스트림이 될 수 있다는 걸 알았죠. 보통 이건 큰 파일을 전송할 때 사용돼요. 우리의 목적에 이것은 아주 좋아요: 우리는 같은 응답에서 계속 생성만 하면 되요, 스트림을 끊지 않고 계속해서 "완료!"라고 말하지 않아도 돼요.

그래서 뭐? 일단 아래 몇 가지를 고려해 봐요:

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

- 대부분의 웹 스크래퍼는 제대로 작성되지 않은 스크립트입니다.
- 대부분의 웹 스크래퍼는 클라우드 인스턴스에서 실행됩니다.
- 이러한 인스턴스에서는 메모리와 저장 공간이 제한됩니다.
- 대역폭도 제한됩니다.

그렇다면 shiddy_wp_scraper.py가 데이터를 끊임없이 보내는 웹사이트에 연결하면 어떻게 될까요? 일반적으로 그냥 데이터를 가져올 겁니다! 그리고 메모리보다 더 많은 기가바이트의 데이터를 가져오면, 운영 체제는 일반적으로 일부 프로세스가 종료되어야 한다고 판단하고, 배란방광! 웹 스크래퍼가 한 대 캐시트가 됩니다. 그러면 그것을 설정한 사람이 자동 재시작을 추가하는 것을 잊었으면 바로 희망할 수 있습니다.

그러나 프로세스는 반드시 충돌하지 않아도 됩니다. 그러나 다른 옵션도 재미있습니다:

- 디스크에 저장하기(디스크가 가득 찰 때 결국 충돌할 것입니다!)
- 대역폭 요금 부과(이에 대해 과금되는 경우)

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

참고: 여러분이 사용 중인 연결이 미터링되는 경우나 대역폭이 제한되어 있는 경우에는 이를 권장하지 않습니다. 웹 스크레이핑 해커들보다 더 많은 것을 잃을 수 있으니까요!

# 단계 2: 지옥에서 더 뜨겁게 달군다

저는 HellPot을 사용하여 웹 스크레이퍼를 무너뜨리는 것을 정말 즐기는데, 전체 프로세스에 개인적인 접촉이 덜한 것 같아서 조금 아쉬웠어요. 제가 HellPot에 일부 기여를 했지만, Go는 제 첫 번째 언어가 아니었고, 원하는 기능이 부족했어요. 그렇다면 뭘 했을까요? 당연히 Rust로 다시 작성했죠.

약간의 Rust 해킹 뒤에, 저는 crates.io에 pandoras_pot을 게시했어요. 기본 원칙은 같아요: 요청, 연결, 쿨레이드를 마시고, 무너뜨리기. 하지만 원하는 멋진 기능 몇 가지를 추가하는 자유도를 가져서 제가 원했던 기능을 더했어요:

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

- 성능이 더 좋아졌어요. 결국, Rust는 엄청나게 빠르고 안전해요!
- 데이터를 생성하는 더 많은 방법이 있어요. 정적 파일을 계속해서 보내거나, 마르코프 체인 출력 대신 랜덤 문자열을 보내는 것도 좋아요 (마르코프 체인을 지원하는 내 마르코프 체인 라이브러리 markovish도 있어요).
- 당신이 사용자로서 고통의 근원을 제공할 수 있어요. 니체를 좋아하지 않으세요? 칸트는 어떠세요? 어제의 신문? 삼 학년 때 크러쉬에게 보낸 사랑의 편지?
- 건강 포트도 있어요. 이제 인스턴스 간에 활성 로드 밸런싱을 할 수 있어요. 이제 봇들은 러지안 룰렛을 할 수 있어요: 이들은 겸손한 출력을 가진 라즈베리 파이에 연결하거나, 심지어 가장 강력한 봇도 부술 수 있는 LED가 달린 게이밍 컴퓨터에 연결할 수 있어요. LED 몬스터가 오프라인이라면 로드 밸런서는 대신 Pi를 선택할 거에요!
- 최대 동시 스트림 양 및 속도 제한과 같은 남용 방지 기능도 있어요.

판도라스 팟을 반복해서 개선했더니, 이제 만족할만한 수준에 도달했다고 생각해요. 물론 여러분도 직접 시도해볼 수 있어요! README에는 전체 설정 가이드가 포함되어 있어요.

![웹 스크랩러를 상대로 싸워 살인하려면](/assets/img/2024-05-23-FightingbackTurningtheTablesonWebScrapersUsingRust_0.png)

# 단계 3: 밴으로 웹 서버 살인하기

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

그래서 잘 되는 것 같았어요? 정말 좋았어요! 정말 많은 봇들이 데이터를 다운로드하는 것을 좋아하는 것으로 밝혀졌어요. 여기에 들어오는 연결의 멋진 그래프를 넣을 수 있던 곳이죠. 우리에게는 유용했던 점이지만, 결국 3년 전 식료품점 주차장에서 산 쓰레기 e-머신이 웹 서버로 사용되고 있던 것이 움직이는 밴 안에서의 여정을 즐기지 못했다는 사실을 깨달았어요. 누가 그런걸 예상했을까요? 제 정신을 회복할 수 있을 것 같지만, 하드 드라이브는 물리적으로 회복할 수 없을 것 같아요. 그리고 아니요, 저는 접속 로그를 백업하지 않았어요. 저장 공간을 절약하기 위해서 소실될 리 없는 것에 대해서만 백업을 예약했거든요.

하지만 몇 가지 스크린샷과 관찰 결과가 있어요! 다음은 전에 pandoras_pot으로부터의 연결을 나열하기 위해 만든 (매우 빠르고 더러운) 통계 페이지의 스크린샷이에요:

![이미지](/assets/img/2024-05-23-FightingbackTurningtheTablesonWebScrapersUsingRust_1.png)

우선, 대부분의 연결은 공용 클라우드 제공업체에서 온 것이에요. 트래픽의 많은 부분이 Tor 네트워크를 통해 전송되었다고 의심했지만, 가장 많은 트래픽을 소비하는 IP 주소들 확인 결과 그 중 아무것도 Tor 네트워크로 연결되지 않았어요. 일부는 스스로 “비공개”이고 “익명”이라고 자처하는 클라우드 제공업체에서 왔어요. 아마도 NSA의 허니팟일 수도 있고, 진짜일 수도 있어요. 여기에서 링크하지는 않을 거에요.

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

또 다른 흥미로운 관찰은 많은 연결 다운로드 용량이 2GB, 3GB 또는 0.5GB와 같은 좋은 숫자 부근에 끝나는 것입니다. 봇들이 이를 다운로드 제한으로 설정한 것일 수 있지만, 이를 높게 설정한 이유는 없습니다. 아마도 이것은 클라우드 제공 업체의 메모리 한도일 수도 있습니다. 대형 클라우드 제공 업체 중 하나를 확인하면 RAM이 가격 결정 요소 중 하나인 것을 볼 수 있으며, 이들은 주로 이와 같은 깔끔한 짝수로 제공됩니다. 나는 단순히 봇들이 '청춘의 슬픔'에서 텍스트로 메모리를 모두 채워서 충돌하고, 그 후에 희망적으로 보다 깨달은 개인이 되는 프로세스라고 생각합니다.

또한 많은 봇들이 30초의 시간제한을 가지고 있음을 알았는데, 성능에 초점을 맞추는 것은 합리적입니다. pandoras_pot은 30초 안에 일부 봇을 충돌시키기 위해 노력해야 합니다. 이는 완전히 불가능한 일이 아닙니다. 인터넷 연결 및 하드웨어에 따라 100MB/s 이상의 속도에 도달하는 것이 쉽게 가능하기 때문입니다 (이는 토스터기 또는 주전자와 비슷한 성능을 가진 옛날 e-machine에서 수행된 것이며, 훨씬 높은 속도로 진행될 수 있습니다). 암호화 및 압축이 큰 영향을 미친다는 점을 주목해야 합니다. 압축에 대해 언급하자면, 악의적인 요청에 Zip 폭탄으로 응답하는 사람들이 있는 것으로 보입니다. 아마도 향후 기능일지도 모릅니다?

# 단계 4: 미래를 위한 아이디어

자, 재미있었죠. 이제 대부분 잘못 구성된 웹사이트에 접속하는 봇들을 충돌시킬 수 있습니다. 그러나 더 나아갈 수 있을까요?

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

Contact Us란 상자를 웹사이트에 넣어 본 적이 있는 사람은 늘 reCAPTCHA를 통과하는 일부 봇이 있다는 것을 알 것입니다 (특히 구현 시에 절약을 한다면). 그런데, "제출" 버튼은 단지 서버로 요청을 보내는 것뿐이죠. 다른 곳으로 향한다면 안타까울 테니까요...

의심스러운 트래픽을 재설정하는 것도 가능합니다. AbuseIPDB에서 들어오는 요청을 즉시 재지정하는 방법은 어떨까요?

참고: IP 주소를 맹목적으로 재지정하지 마세요. 어떤 사람들은 무료 인터넷에 액세스하려면 Tor가 필요합니다. 또 다른 사람들은 ISP로부터 다른 사람들과 공용 주소를 공유하도록 강요받습니다.

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

웹사이트를 설정할 때 많은 사람이 방문할 것입니다. 그 중 대부분은 사람이 아닙니다. 대부분은 무해하지만 실제로 .env 파일을 공개 웹사이트에 포함시켰다면 곤란한 상황에 처할 수도 있습니다.

어떤 사람들은 더 나은 봇을 만들어 pandoras_pot을 탐지할 수 있다고 지적할 수 있습니다. 이는 사실이지만, 그렇게까지 해야 할 필요가 있을까요? Pandora_pot을 사용하지 않는 사람들에게 조금 농담을 치는 것이 어떨까요? 여기서 우리가 장점을 가지고 있습니다: pandoras_pot은 구성을 사용하여 응답의 모양을 변경할 수 있습니다. 이것은 코드에서 탐지하는 것보다 훨씬 쉽습니다. 다른 사람들도 pandoras_pot을 사용하여 자신만의 구성을 만든다면 피하는 것이 어려워집니다.

요약하자면: 친절하게 대해주세요, /robots.txt를 존중해주세요, 그렇지 않으면 영원한 결과를 받게 될 수 있습니다.
