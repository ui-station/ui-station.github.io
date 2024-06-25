---
title: "제가 처음으로 애플 스토어에 앱을 배포한 방법 1"
description: ""
coverImage: "/assets/img/2024-06-22-HowIpublishedmyfirstapptoAppleStore1_0.png"
date: 2024-06-22 23:20
ogImage:
  url: /assets/img/2024-06-22-HowIpublishedmyfirstapptoAppleStore1_0.png
tag: Tech
originalTitle: "How I published my first app to Apple Store #1"
link: "https://medium.com/@uladz.mikula/how-i-published-my-first-app-to-apple-store-1-5df0007e2c51"
---

![이미지](/assets/img/2024-06-22-HowIpublishedmyfirstapptoAppleStore1_0.png)

# 배경

안녕하세요! 저는 울라즈입니다. 첫 번째 iOS 애플리케이션을 작성 중이에요. 지난 주에 물병에서 물을 쏟아 노트북이 고장 났어요. 그래서 다음 몇 주 동안 코딩을 못하니 대신 글을 쓸 거예요...

지난 몇 년 동안 자신의 일을 시작하고자 끊임없이 생각했어요. 나 자신을 위해 일하는 것을 꿈꿨죠. 새로운 사업을 시작하기 위한 기준은 몇 가지가 있었어요. 여가 시간에 해야하고 필요한 기술이나 배우기를 원하는 욕망이 있어야 했어요. 그리고 초기 투자가 적고 6개월 이내에 초기 결과를 볼 수 있는 잠재력이 있어야 했어요. 드롭 배송이나 프린트 온 디맨드 티셔츠 스토어부터 내 카페나 어린이 장난감 가게까지 아이디어는 다양했어요. 많은 아이디어가 지루해서 폐기되었지만 무언가가 동작하지 않을까 두려워서 대부분이었어요.

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

두 해 전에 아빠가 되었고 성장하는 아기의 활동을 추적하기 위해 앱을 다운로드했어요. 이름은 기억이 안 나지만 목적을 잘 해결해 주었어요. 유료로 제공되어야 했던 몇 가지 기능이 구독 없이 사용할 수 없어서 아쉬웠지만, 급하게 제 자신의 앱을 만들어보려고 생각했어요.

<img src="/assets/img/2024-06-22-HowIpublishedmyfirstapptoAppleStore1_1.png" />

이 아이디어가 마음에 들어서 모든 조건을 충족시킬 수 있다고 생각했어요: 매일 1~2시간을 코딩할 수 있었고, 휴대폰 개발을 배우고 싶었으며, 자금이 필요하지 않았어요 (전혀 사실이 아니라면, 다음 부분에서 설명할게요), 그리고 6개월 안에 완성할 수 있을 것 같았어요. 판매할 계획은 없었고, 주된 목적은 배우기와 개인적인 용도였어요. 릴리스 프로세스를 간소화하고 싶었기 때문에 변화를 가하기 위해 쉽게 새 버전을 릴리스할 수 있지 않을까 생각했어요. 만약 모든 것이 잘 풀린다면 미래에 수익창출을 시도하고 수동적인 소득을 얻을 수 있겠죠. 그래서 밤에 피트니스 볼 위에 앉아 딸을 흔들며 한 손으로 타이핑을 시작했어요.

첫 번째 일은 무엇을 작성할지와 어느 플랫폼을 위해 작성할지를 결정하는 것이었어요. iOS에만 제한하지 않기로 결정해서 안드로이드 스마트폰 사용자들이 제 아름다운 작품을 사용할 기회를 놓치지 않게 하려고 했죠. 그래서 React Native과 Flutter 중에서 선택했어요. 회사에서 내부 서비스를 지원하기 위해 React를 배워야 했는데, React Native를 선호하는 이유였죠. 공식 문서를 보고 예상대로 시작해봤는데, 시뮬레이터에서 Hello World 예제를 실행하려고 해보니 예상보다 순조롭지 않았어요. 환경 설정 과정이 어색해서 제 동기부여가 금방 떨어졌어요. 종속성이 설치되지 않고, 시뮬레이터가 실행되지 않거나 이유를 알 수 없이 다운되기도 했어요. 게다가 앱 릴리스 프로세스가 불분명해서 2~3주간 투쟁한 끝에 아이디어를 포기했어요. 게다가 우리 아이의 일상이 안정되고 유료 앱을 사용하지 않게 되었거든요.

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

![앱 이미지](/assets/img/2024-06-22-HowIpublishedmyfirstapptoAppleStore1_2.png)

아홉 달이 지나자 우리는 다른 도시로 이사를 가게 되었고, 저는 체육관에 가입했어요. 이전에도 시도해 봤지만 오래 기억하지 못했어요. 이번에는 체육관이 걸어서 2분 거리에 있어서 규칙적으로 빠뜨릴 가능성이 거의 없었어요. 세트와 무게를 기록할 앱을 찾기 시작했고, 여러 개가 있었어요. 처음 발견한 앱이 나에게 적합했지만, 시간이 지남에 따라, 한 세트만 주석을 남길 수 없고, 세 개 이상의 루틴을 만들 수 없다는 것을 깨달았어요.

그래서 나는 내 앱 아이디어로 돌아가고 무엇으로 쓸지 고민했어요. 이번에는 가장 쉬운 길인 작게 시작하고 빨리 배우는 길을 선택하기로 결정했어요. 주로 나 자신을 위해 쓰기 때문에, iOS만 지원하도록 제한하기로 했어요. 진입 장벽이 매우 낮았고, 애플의 공식 자습서로 시작했고, 처음 두 시간 안에 시험 앱을 핸드폰에 올렸어요. 그래서 매일 업무를 끝낸 후에 운동 기록 앱을 만들기 시작했어요 💪.

지금까지입니다! 끝까지 읽어 주신 모든 분들께 감사드려요!

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

다음 게시물에서는 다음과 같은 주제를 다룰 예정입니다:

- 개발 프로세스 및 왜 스위프트를 배우지 않고 ChatGPT에게 물어보기로 결정했는지
- 첫 번째 릴리스
- 향후 계획

여기서 스크린샷을 확인하거나 다운로드할 수 있습니다. 광고나 등록이 필요하지 않으며 무료로 제공됩니다.
