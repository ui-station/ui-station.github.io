---
title: "원격 개발, 또는 메인프레임을 사랑하게 된 방법"
description: ""
coverImage: "/assets/img/2024-06-19-RemotedevelopmentorHowIlearnedtostopworryingandlovetheMainframe_0.png"
date: 2024-06-19 12:50
ogImage:
  url: /assets/img/2024-06-19-RemotedevelopmentorHowIlearnedtostopworryingandlovetheMainframe_0.png
tag: Tech
originalTitle: "Remote development, or: How I learned to stop worrying and love the Mainframe"
link: "https://medium.com/homullus/remote-development-or-how-i-learned-to-stop-worrying-and-love-the-mainframe-90165147a57d"
---

![2024-06-19-RemotedevelopmentorHowIlearnedtostopworryingandlovetheMainframe_0](/assets/img/2024-06-19-RemotedevelopmentorHowIlearnedtostopworryingandlovetheMainframe_0.png)

리모트 서버에서의 개발은 생각보다 어렵지 않습니다. 사실 저렴한 VPS 도플릿이나 클러스터를 사용하는 것은 로컬 이진 해석기를 여러 개 사용하거나 Mac용 Docker를 사용하는 것보다 미쳤다고 할 정도의 혜택이 있습니다.

2022년 업데이트: 요즘에는 님버스(Nimbus)만 사용합니다.

여기에서 한 발 더 나아가서, 컨테이너 중심 환경에서 어떻게 작업하는지 배워보겠습니다. 여기서 우리는 완전한 네이티브 리눅스 성능과 17시간 배터리, 그리고 제로 팬 소음을 얻을 것입니다. 우리는 작은 도플릿 VPS를 임대하고, VSCode 및 JetBrains의 IntelliJ Idea CE를 통해 원격 개발 환경을 설정할 것입니다.

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

항상 글이 너무 길어서 연구 과정을 이야기하는데, 읽기 싫다면 "클라우드에서 로컬 개발 환경을 실행하세요. 생각한 것만큼 어렵지 않습니다" 라는 것만 기억해 주세요. 여기에 목차가 있습니다:

- 🐳 개발에서의 Docker
  — Swapping and swinging
  — Docker For Mac을 포기한 이유
  — Parallels 모험
- 💻 개발에서 얇은 클라이언트의 경우
  — 가격
  — 성능
  — 학습 기회
- 🛰 원격 개발 서버 설정
  — 보안에 대한 메모
  — 자동 생성
- 🌍 원격 개발 기능이 있는 코드 편집기
  — 서버에 액세스하기 위해 VSCode Remote 사용
  — 원격 서버와 함께 JetBrains Projector 사용 방법

# Docker에서 개발

생산 환경에서 컨테이너를 사용하도록 설득하려고 하지 않을 것입니다. 그것은 이미 너무나 당연한 일입니다. 여기서 제가 살펴보고 있는 것은 Docker가 팀의 통합성과 생산성에 미치는 흔히 언급되지 않은 영향입니다. - 맥에서의 개발용 Docker.

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

몇 년 전, 저는 꽤 큰 기업에서 꽤 큰 핵심 제품을 다루는 다양한 시니어도 함께하는 30명의 엔지니어 팀을 이끌었습니다. 시험적인 프로젝트로 Docker를 우리 프로덕션 환경에 (조심스럽게) 도입하는 것을 맡게 되었습니다.

![이미지](/assets/img/2024-06-19-RemotedevelopmentorHowIlearnedtostopworryingandlovetheMainframe_1.png)

대부분 사람들이 Docker에 대해 이야기할 때, 그것이 프로덕션에 미치는 이점에 대해 얘기를 합니다. 그러나 누구도 팀에 어떤 영향을 미치는지에 대해 이야기하지 않습니다. 저는 개발에서 그것이 효과적이라는 것을 직접 경험했습니다. 오늘날 이것은 모든 개발자가 이해하고 사용해야 할 중요한 도구로 간주합니다.

- 팀 내에서 평등화 작용을 합니다. 주니어들이 스택의 나머지에서 덜 고립되게 합니다. 시니어들은 이국적인 설정을 할 수 없습니다. 모두가 전체 스택을 더 잘 이해하고 덜 두려워하며 실험에 더 열려지게 됩니다.
- 모든 엔지니어들에게 로컬을 프로덕션과 같은 환경으로 생각하도록 강제시킵니다. 어떤 꼼수도 허용되지 않습니다.
- 설정 및 문서화를 추상화합니다. 앱 자체에 집중할 수 있게 합니다.
- 그 결과, 공유된 비밀 및 데이터베이스 관리가 더 이상 yaml 파일을 잔뜩 다루고 구성 관련 문제를 처리하는 수동 작업이 아니었습니다.
- 코드 편집기, 린터, 테스트, 종속성은 모두 애플리케이션과 같은 런타임에서 실행됩니다.
- 로컬에서 많은 프로젝트를 한꺼번에 처리하기 쉬워졌습니다. 다른 팀의 엔지니어도 참여할 수 있습니다. 더 많은 협업, 멤버 교체 및 상호 교류가 이루어집니다.
- 이제 코드 리뷰에 로컬 체크아웃, 테스트 실행 및 심지어 QA까지 포함됩니다.
- 프로그램 코드 또는 설정 변경, 프로덕션 업그레이드 또는 잘못된 에셋 배치와 같은 지저분한 실천 방지합니다.
- 그리고 더 많은 이점들이 있습니다...

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

코드베이스의 일부로 최상위 레이어 또는 인프라를 유지하는 것은 모든 이해 관계자에게 도움이 됩니다. 이 방법으로 도구들을 서로 분리시키고 팀이 설정을 자유롭게 실험하고 매우 빨리 반복할 수 있게 합니다.

저에게는 개발/운영 일치성은 인프라, 구조 또는 설정을 자유롭게 변경하고 장애 없이 배포할 수 있는 것을 의미합니다. 자동화된 다양한 테스트와 CI 파이프라인이 환경별로 동일한 동작을 할 것이라는 확신을 줍니다.

내게 첫 번째 "자랑스러운 아빠의 순간"은 프론트엔드 엔지니어가 PR을 만들었을 때 발생했습니다. 거기에는 몇 가지 복잡한 빌드/자산 폴더와 모든 관련된 셸 스크립트를 스스로 이동했습니다. 그들이 한 해 전에 시도조차 하지 않았던 단계였습니다.

이제 그들은 자신만의 스택을 소유하고 자랑스러워합니다.

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

어쨌든, Docker가 좋은 성과를 냈습니다. 회사에서는 오늘날까지도 사용하고 있고, 제가 더 이상 그곳의 일원이 아니더라도, 그들이 널리 도입할 계획을 세우고 있다는 소식을 듣고 기뻤습니다.

[![이미지](/assets/img/2024-06-19-RemotedevelopmentorHowIlearnedtostopworryingandlovetheMainframe_2.png)](이미지 주소)

## 교체와 전환

2021년에 대한 영감을 줄만한 재미있는 전쟁 스토리 시간입니다...

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

언젠가 오래 전에, 교수 블라디미르 렐리카닌은 저희 반에게 Git과 MAMP 환경 설정에 대해 가르치고 계셨어요. 농담으로 무언가 말씀하셨는데, 오늘까지 기억나는데요: "새 컴퓨터에 앉아서 30분 이내에 완전히 설정이 되고 코딩을 시작하지 못하면 시간을 낭비하는 거예요. 무언가가 잘못되었다는 신호에요. 아마도 스택을 이해하지 못하고 있다는 거죠".

이제는 2021년인데, 5분 이내로 처리하고 싶네요. 그보다 더 빨리요.

오래된 멘토이자 친구인 보리스 체라닉은 /Sites 폴더 전체를 Dropbox에 보관했어요. 이렇게 하면 모든 컴퓨터에서 단순히 모든 변경 사항이나 편집기 구성을 빠르게 반영할 수 있었죠.

이제는 2021년이고, VPN과 SSH 키 뒤에 코드를 보관하는 게 더 좋을 것 같아요.

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

2016년에는 3D 및 VR 작업을 많이 수행하는 회사에서 일했습니다. 제 동료 Kole는 모든 것을 Dropbox에 보관했습니다. 만약 그가 강력한 컴퓨터가 필요한 프로젝트를 작업 중이었다면, 그는 그냥 Cmd+S를 눌러 더 강력한 데스크탑으로 전환할 수 있었습니다.

이제 2021년이 되어서 필요할 때 VPS의 규모를 확장하는 것이 더 좋습니다.

## Docker For Mac을 버리게 된 이유

내 30명의 엔지니어 팀을 기억하시나요? 모두에게 불행한 일이 되어버렸는데, Docker가 관련된 경우 Mac에서 코딩하는 성능과 일반적인 사용성에 만족스럽지 못했습니다.

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

숙고하며 구멍 속으로 들어갈수록 더 어둡기만 했어요. 때로는 정말로 돌아갈 길이 없는 것 같아서 막막했죠. 회사로서는 200대의 우분투 컴퓨터를 사서 관련된 모두에게(그들의 코드베이스도 포함하여) 스위칭하는 법을 가르쳐야 했어요.

저는 이 시리즈의 이전 두 부분에서 보았던 것처럼 문제를 해결할 방법을 찾았어요. 그러나 동시에, 제 우리가 부족한 도구로 인해 얼마나 많은 엔지니어링 에너지와 시간을 소비하는지를 더욱 명확하게 깨달았어요. 이제 저는 절대적으로 확신을 가지게 되었어요. 모든 도구, 편집기, 코드베이스, 이진파일을 반복가능하게(그리고 확장 가능하게) 설정하는 쉬운 방법이 있다면, 저희의 속도를 최소 30% 이상 높일 수 있을 것이라고, 그 영향이 학습과 멘토링에 미칠 효과가 시간이 지남에 따라 나타날 것이라고 확신합니다.

회사에서 새로운 도구를 챔피언하는 것은 수월해야 해요. 이전 시스템보다 훨씬 좋아야 해요. 그 차이가 명확해야 해요. 그 이유가 널리 이해되어야 해요. Docker for Mac을 저희 팀에 도입하는 것은 이런 것들 중 한 가지도 아니었어요.

## Parallels 모험

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

대처 전략 중 하나(파트#2에 개요되어 있음)은 D4M을 제거하고 새로운 도커 컨텍스트를 위한 하이퍼바이저로 Parallels VM을 사용하는 것이었습니다. 이를 통해 우분투용으로 개발된 Parallels 팀이 여러 해 동안 개발한 모든 멋진 최적화 기능을 활용할 수 있었습니다.

이로써, 우리는 어떤 꼼수도 쓰지 않고 작동하는 도커를 가지게 되었고, 개발/프로덕션 둘 간의 일치성을 되찾았습니다. 성능은 거의 네이티브 수준이었고, 대표적으로 어려운 Symfony 캐시나 노드 모듈 폴더에 대한 완전한 동기화에도 그랬습니다.

이 모두를 고려할 때, 우리는 코드를 VM에 유지하고, 그곳에서 실행시키며, 우리 컴퓨터와 동기화시키고 있습니다. 호스트에서 코드 편집기를 실행하고 있습니다. 도커 컨텍스트, DNS, 포트 포워딩, 원격 해석기와 같은 기교를 사용합니다. SSH와 HTTP를 통해 그것과 소통합니다. VM은 우리 호스트 관점에서 원격 머신입니다.

그러므로 한 가지 의문이 제기됩니다 — 이미 많은 어려움을 겪고 이러한 모든 도구를 사용하고 있는데, 왜 우리는 VM을 완전히 제거하지 않고 그냥 그것을 원격 위치로 보내고, 멋지고 강력한 곳에 연결하여 같은 도구를 사용하지 않는지요?

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

<img src="/assets/img/2024-06-19-RemotedevelopmentorHowIlearnedtostopworryingandlovetheMainframe_3.png" />

# 개발에서 얇은 클라이언트 사용 사례

MVP 시간이네요! DigitalOcean에서 가장 저렴한 드롭렛을 구입했고, SSH 키를 만들고, 몇 개의 Elixir 저장소를 다운로드하고 그들의 도커 프로젝트를 시작했어요. 좋아, 예상대로 잘 작동했어요, 이제 뭘 해야 할까요?

빠르게 VSCode 인스턴스를 SSH를 통해 드롭렛에 연결하고 원격 폴더를 선택했어요. 코드를 편집하고 페이지를 다시로드했어요. 좋아, 충분히 잘 작동하네요, 예상대로요.

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

![image](/assets/img/2024-06-19-RemotedevelopmentorHowIlearnedtostopworryingandlovetheMainframe_4.png)

## 가격

원격 개발 방법의 가격은 노트북을 구매하는 것과 비슷합니다. 시장에서 절대 최고의 노트북을 구할 수 있습니다 — 저렴한 M1 MacBook Pro. 프로젝트 및 팀 구성원에 따라 다르지만, 약 500달러에서 1500달러 정도를 절약했습니다.

이제 드롭렛이나 EC2 인스턴스를 임대하고, 매월 15달러부터 50달러까지 지불합니다. 업타임만 지불한다면 훨씬 적게 지불할 수 있습니다. 그래도 가장 비싼 것을 가정해보면, 50달러입니다!

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

그래서, 우리가 처음으로 저축한 돈은 이제 VPS 공급업체에 매월 작은 지불로 연중에 걸쳐 분할될 것입니다(절대 최악의 경우).

일년이 지나가고, 혹은 두 해, 혹은 석 년이 지나가도 여전히 지출이 급증하지 않고 사람들은 새로운 컴퓨터가 필요하지 않습니다. Mac은 능력을 유지하는 시간으로 유명합니다. 더 심각한 프로젝트를 마주했나요? 그러면 드롭렛을 확장하세요!

## 성능

내 현재 노트북은 BTO MacBook입니다. 미친 듯이 $4000에 가까운 가격을 태웠죠. 원격 개발 세계에서, 저는 배터리가 오래 가고 열이 덜 발생하는 $900의 M1 기계와 제 노트북보다 훨씬 강력한 드롭렛을 구입했습니다. $3000 할인으로 더 나은 두 가지 도구를 얻었습니다.

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

![image](/assets/img/2024-06-19-RemotedevelopmentorHowIlearnedtostopworryingandlovetheMainframe_5.png)

물론 어느 기준에서도 인상적한 160GB의 RAM과 40개의 CPU 코어로 확장 가능한 드롭렛이 있습니다. 30분 안에 ML을 훈련해야 한다면, 그 유명한 Turbo 버튼을 눌러 몬스터 드롭렛을 생성해보세요.

EC2 인스턴스는 심지어 더 나은 성능을 자랑합니다 (다만 관리하고 예측하기 어려울 수 있습니다). GPU 최적화된 작업 부하를 사용하거나 운영 업무 시간을 기준으로 청구 금액을 절약할 수 있는 매우 구체적인 인스턴스를 사용할 수 있습니다.

숙련도가 충분하다면 기존 k8s 클러스터에 연결하고 이미 사용 가능한 다른 서비스를 사용할 수도 있습니다. 이에 대해 자세히 모르지만 Telepresence와 같이 해당 목적에 맞춘 특수 도구가 있다는 것은 여러해 전부터 알고 있습니다. 그렇지 않으면 DigitalOcean 관리 클러스터를 생성해보세요. 1클릭/무난한 작업으로 간단합니다!

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

<img src="/assets/img/2024-06-19-RemotedevelopmentorHowIlearnedtostopworryingandlovetheMainframe_6.png" />

요즘에는 제 노트북 CPU 사용량이 항상 20%를 넘지 않고 온도도 34도에서 안정적으로 유지되고 있어요. 이제 무리없이 무릎 위에 올려두고 사용할 수 있고, 한 번도 충전기를 꽂지 않고도 하루 종일 일할 수 있어요. 개발 활동보다 크롬이 더 많은 배터리를 소비하니 (광고: 그래서 저는 Safari를 사용하는 것을 추천해요).

<img src="/assets/img/2024-06-19-RemotedevelopmentorHowIlearnedtostopworryingandlovetheMainframe_7.png" />

## 학습 기회

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

어떤 사람들은 주니어들이 이것을 받아들이기가 어려울 것이라고 할지도 모릅니다. 하지만 제 경험상 시니어 개발자들이 저항을 표했지만, 주니어들은 실제로 아주 빨리 이해했어요. 그들은 CLI 사용에 조금 더 배워야 할 수도 있고, 때로는 전체 서버를 망칠 수도 있지만 — 그러니까 뭐요? 로컬 머신과는 다르게, 새로운 서버를 생성하고, 단 둘만에 다시 트랙에 올라갈 수 있어요.

그리고 이것은 엄청난 학습 기회입니다. 모든 형태와 크기의 개발자들이 실제 서버에서 운전대를 잡게 될 거예요! 그들은 SSH 사용하는 법, 자신의 코드가 어디에 저장되어 있는지, docker가 그에 어떻게 맞는지 등을 이해하게 될 거예요.

이것은 어떤 사람들에게는 그렇게 중요하지 않아 보일 수 있지만, 저는 프론트엔드 개발자가 이 개념을 이해하는 것이 코드 페어링 워크샵보다 크로스폴리네이션 전략으로서 더 가치 있다고 주장할 거예요. 그리고 한 번만 배우면 돼요.

# 원격 개발 서버 설정하기

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

업데이트 16.11.2021: 이후로 저는 새로운 워크박스를 생성해야 할 때 사용하는 Readme 파일을 조금 작성하기 시작했습니다. 언젠가는 자동화할 거지만, 지금은 매우 대략적인 Readme.md를 제공해요.

제가 좋아하는 것은 간단함입니다. DigitalOcean 패널에 접속하고 새로운 드롭렛을 생성하세요. 최신 Ubuntu나 다른 것들 중 마음에 드는 것을 선택하세요. 지불할 크기를 선택하세요. 요즘에는 일반적으로 4 CPU 사이즈를 사용하지만, 실제로는 조금 과잉이에요.

![이미지](/assets/img/2024-06-19-RemotedevelopmentorHowIlearnedtostopworryingandlovetheMainframe_8.png)

한 달에 20달러에 2 CPU는 꽤 좋은 거래에요. 충분한 RAM이 있어서 yarn 설치가 영원히 걸리지 않거나, 의존성 트리 계산 중에 Garbage Collection 한계에 부딪혀서 compose 설치가 실패하지 않아요. 여분의 돈이 있다면, 8GB/4CPU 설정을 추천합니다. 그 가치가 있어요.

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

가장 가까운 데이터 센터를 선택해주세요. 지연 시간은 별다른 문제가 없지만, 왜 안 하시겠어요! 옵션이 제공된다면 SSH 키와 모니터링을 삽입해주세요. 귀여운 이름을 지어서 만들어보세요!

SSH를 통해 VPS에 액세스하고, SSH 키를 생성하여 GitHub 계정에 추가해주세요. 귀하의 저장소를 복제할 수 있어야 하므로 GitHub에 추가해야 합니다.

패키지 목록을 업데이트하고, 시스템을 업그레이드하고, git, zip, docker 및 docker-compose와 같은 일반 소프트웨어를 설치해주세요.

## 보안에 대한 참고사항

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

설정 중에 SSH 키를 추가하지 않았다면, 구글링해보세요. DigitalOcean에는 그것을 하는 방법과 비밀번호 인증을 비활성화하는 방법에 대한 많은 자습서가 있어요.

이 목적으로 root 사용자를 사용하는 것을 좋아해요. 이것이 금기시되고 표식화된 주제라는 것을 알지만, 특정 사용 사례에서 여기서 더 이상 나아가는 필요성은 전혀 없어요. 기억하세요, 이 특정 기계는 아무것도 실행하지 않으며 공개적으로 접근할 수 없어요.

블로그 글 등의 리소스가 있는데 이들은 이러한 서버를 공급하고 제품 서버를 보호하는 방식과 동일하게 보호합니다.

저의 겸손한 의견으로는 이는 필요하지 않아요. SSH 키가 충분한 보호가 될 거에요 (안전하게 보관한다면요). 누군가가 HTTP로 공격하거나 스크랩을 하길 우려한다면, ufw에서 IP 주소를 화이트리스트에 추가하세요. 회사로서 이것을 읽는다면, 아마도 이미 회사 VPN을 가지고 계실 텐데, DigitalOcean에서 사설 네트워크를 생성하세요. 이에 대해 여전히 걱정이 된다면, DigitalOcean에 몇 가지 아이디어가 있는 것 같아요:

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

## 자동 생성물

DO에는 HTTP API, 테라폼 지원 및 심지어 앤서블 스크립트 지원이 있습니다. 회사나 기업에서 이 워크플로우를 다루고 있다면, 이 시점에서 기본 스냅샷 이미지를 만들고 필요할 때마다 작은 드롭릿을 생성합니다.

사실, 휴가를 떠날 때 계획할 때는 비슷한 프로세스를 수행합니다. — 머신의 스냅샷을 만들고, 그런 다음 파괴합니다. 이렇게하면 보관할 수 있고, 비용을 지불할 필요가 없습니다.

# 원격 개발 기능이 있는 코드 편집기

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

2021년이고 편집기들이 이 필요성을 알아보기 시작했다. 언제나 그래왔듯이, 오래된 학교의 편집기인 emacs와 vim은 이미 기본 설정으로 이 설정을 지원한다. 왜냐하면 그들을 droplet이나 컨테이너 내에서 실행할 수 있기 때문에 이 설정에 이미 액세스할 수 있다고!

최근 편집기들로 넘어올 때 (나는 아주 늦게 채택했기 때문에 냉소적인 미소를 짓는 중), VSCode를 사용하는 것을 권할 것이다. 원격 편집 기능이 있고, 그 주변의 전체 아키텍처가 내가 하는 일에 더 적합하다.

## 서버에 액세스하려면 VSCode 원격 사용

VSCode에는 내장된 "원격 컨테이너에 연결" 기능이 있습니다. 이 기능은 실제 편집기를 생성하고 컨테이너 내의 네이티브 인터프리터와 직접 작업할 수 있게 해줍니다.

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

먼저 할 일은 VSCode를 사용하여 원격 VPS에 SSH 워크스페이스로 연결하는 것입니다. .vscode/workspace.code-workspace 위치에 새 파일을 만들어 해당 폴더를 여러 프로젝트의 루트로 정의해야 합니다. 여기에는 표준 vscode 설정을 포함할 수도 있습니다. 예시는 다음과 같습니다:

```js
{
 "folders": [
  {
   "path": ".."
  }
 ],
 "settings": {
  "remote.autoForwardPorts": false,
  "workbench.editor.labelFormat": "medium",
  "workbench.colorCustomizations": {
   "activityBar.activeBackground": "#1f6fd0",
   "activityBar.activeBorder": "#ee90bb",
   "activityBar.background": "#1f6fd0",
   "activityBar.foreground": "#e7e7e7",
   "activityBar.inactiveForeground": "#e7e7e799",
   "activityBarBadge.background": "#ee90bb",
   "activityBarBadge.foreground": "#15202b",
   "statusBar.background": "#1857a4",
   "statusBar.foreground": "#e7e7e7",
   "statusBarItem.hoverBackground": "#1f6fd0",
   "statusBarItem.remoteBackground": "#c92121",
   "statusBarItem.remoteForeground": "#d3d3d3",
   "titleBar.activeBackground": "#1857a4",
   "titleBar.activeForeground": "#e7e7e7",
   "titleBar.inactiveBackground": "#1857a499",
   "titleBar.inactiveForeground": "#e7e7e799"
  }
 },
 "extensions": {
  "recommendations": [
   "hashicorp.terraform",
   "ms-azuretools.vscode-docker",
   "eamodio.gitlens",
   "k--kato.intellij-idea-keybindings",
   "mutantdino.resourcemonitor",
   "ow.vscode-subword-navigation",
   "redhat.vscode-yaml",
   "mikestead.dotenv",
   "ms-vscode-remote.remote-containers",
   "ckolkman.vscode-postgres",
   "mohsen1.prettify-json",
   "buianhthang.xml2json"
  ]
 }
}
```

여기서 터미널에 액세스하여 프로젝트를 클론하거나 시작할 수 있습니다. 그러나 여기에서는 편집을 진행하지 않습니다. 편집은 컨테이너 내부에서 수행됩니다.

컨테이너화된 앱을 시작하고, 쉘 파일, 도커 또는 도커 컴포즈가 됐던, 실행 중인 컨테이너에 "Attach to a Running Container" 기능을 사용하여 편집을 원하는 런타임을 포함한 컨테이너를 선택합니다. 이렇게 하면 해당 프로젝트가 있는 새 창이 열리며, 해당 프로젝트의 모든 설정과 린트가 표시됩니다.

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

VSCode는 표준 .vscode 구성 파일에서 내린 모든 결정을 존중해줍니다. 그래서 보통처럼 자유롭게 사용할 수 있어요! 이러한 파일들은 일반적으로 프로젝트 내부에 커밋되어 프로젝트에 대해 모든 팀 멤버가 동일한 규칙과 에디터 설정을 사용하도록 보장합니다. 의존성과 규칙을 추가하는 방법을 아래 예시를 참고하세요:

```js
$ cat .vscode/extensions.json
{
    "recommendations": [
        "jakebecker.elixir-ls",
        "pgourlain.erlang",
        "mutantdino.resourcemonitor",
        "mikestead.dotenv",
        "eamodio.gitlens"
    ]
}
```

제 편집 작업 흐름이 어떻게 보이는지 빠르게 살펴볼까요? 이를 비디오로 설명하는 게 조금 더 쉽겠지만, 괜찮으시겠지요?

이것이 저에게 다른 프로젝트를 이동하고 시작하고 중지하고 파일을 이동하며 도트파일, 설정 파일 또는 컨테이너를 만드는 매우 좋은 방법을 제공합니다.

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

지금은 docker-compose를 통해 프로젝트를 시작할 수 있습니다. 서로 다른 편집기 창을 서로 다른 컨테이너에 연결할 수 있습니다.

프로젝트 특정 창에서는 해당 런타임과 파일 시스템에 대한 접근이 가능합니다. 이는 편집기 내의 모든 린팅 및 구문 분석이 동일한 런타임으로 수행된다는 것을 의미합니다. 저는 호스트 머신이나 VPS에 node를 로컬로 설치하지 않았습니다. Intellisense가 정상적으로 작동합니다.

원한다면 두 런타임을 동일한 컨테이너에 유지할 수 있습니다. 하지만 저는 이들을 분리하는 것을 강력히 권장합니다.

재미를 위해, 동일한 VPS에서 동시에 다른 클라이언트를 위한 완전히 다른 프로젝트를 시작해 보죠. 해당 프로젝트는 elixir로 실행되며 다른 컨테이너나 그들의 런타임에 대해 전혀 알지 못합니다.

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

이는 VSCode 창마다 실제로 각 프로젝트 및 해당 런타임에 대해 매우 특화되고 간소화되어 있다는 것을 의미합니다. 각각이 서로 다른 설정(색상도 다름)을 가지고 있으며, 프로젝트의 로컬 .vscode 파일을 준수하고 서로 다른 일련의 확장 기능이 실행됩니다. Out of the box.

## 원격 서버와 함께 JetBrains Projector를 사용하는 방법

업데이트: 이 방법은 더 이상 진행하지 않기로 결정했습니다. macOS에서는 키 바인딩이 때때로 랜덤하게 작동하지 않는 것 같은 버그가 발생합니다.

JetBrains는 다른 아이디어를 가지고 있습니다. Docker 컨테이너 내에서 편집기를 생성한 다음, Projector 앱이나 브라우저를 사용하여 연결하는 방법을 허용합니다.

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

편집기는 프로젝트의 컨테이너 내에서 실행되지 않습니다. 대신 호스트에서 실행되며 호스트의 파일시스템 개요에 액세스할 수 있습니다. VPS에 직접 이진 파일로 설치하거나 Docker를 통해 별도의 컨테이너로 생성할 수 있습니다.

여기서의 작업 흐름은 드롭렛에 연결하여 프로젝트를 시작하고, Docker 컨테이너 기반 편집기를 시작하는 것입니다. 모든 파일이 VPS 호스트와 동기화되어 있기 때문에 편집기는 VPS 파일 시스템에서 파일을 편집할 수 있습니다.

그러나 JetBrains는 원격 인터프리터 사용을 가능하게 하는 데 많은 투자를 했기 때문에 모든 "두꺼운" 편집기가 원격 컨테이너 내에서 실행 중에 실행을 사용할 수 있습니다. 이는 편집기가 앱 실행과 동일한 런타임을 사용할 수 있는 능력이 있음을 의미합니다. 편집기는 컨테이너에 연결하고 편집기가 수행하는 컴파일에 대한 실행 시간을 사용할 수 있게 됩니다.

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

![Example image](/assets/img/2024-06-19-RemotedevelopmentorHowIlearnedtostopworryingandlovetheMainframe_10.png)

상상할 수 있겠지만, 이 방법에는 몇 가지 단점이 있습니다. 관리하기 어려울 뿐만 아니라 개발자가 코드를 컨테이너를 먼저 보는 관점을 보는 것만큼 깔끔하지 않습니다. 그러나 이 접근 방식은 로컬 에디터에서 했던 것과 매우 비슷합니다.

저는 JetBrains 제품을 오랫동안 사용해왔습니다. 그들이 얼마나 부피가 크고 압도적인지 항상 싫어했지만, 동시에 PHP, Ruby, Java 또는 Python을 다룰 때에는 더 좋은 IDE가 없다는 것을 직접 경험했습니다. 그러나 지난 몇 년간 특히 Python 및 PHP에서는 VSCode로 점점 더 많이 기울어졌습니다. 제 기능을 잃는 것이 어떻게든 개발의 용이성으로 보상된다는 점 때문입니다.

따라 주셔서 감사합니다.

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

저에게 귀하의 생각을 듣고 싶습니다. 완벽하지는 않지만, 지금까지 만들어본 최고의 설정입니다.

배포하기 쉽고, 확장하기 쉽고, 파괴하기 쉽고, 사용하기 쉽습니다.

한 가지 확실한 것은 이를 둘러싼 도구들은 앞으로 점점 더 나아질 것이며, 우리는 이를 통해 얻을 수밖에 없습니다!

다음에 뵙겠습니다.
