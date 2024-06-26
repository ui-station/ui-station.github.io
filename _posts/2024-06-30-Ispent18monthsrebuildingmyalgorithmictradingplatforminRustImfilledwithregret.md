---
title: "알고리즘 트레이딩 플랫폼을 Rust로 18개월 재구축 후 후회한 이유"
description: ""
coverImage: "/assets/img/2024-06-30-Ispent18monthsrebuildingmyalgorithmictradingplatforminRustImfilledwithregret_0.png"
date: 2024-06-30 19:20
ogImage: 
  url: /assets/img/2024-06-30-Ispent18monthsrebuildingmyalgorithmictradingplatforminRustImfilledwithregret_0.png
tag: Tech
originalTitle: "I spent 18 months rebuilding my algorithmic trading platform in Rust. I’m filled with regret."
link: "https://medium.com/@austin-starks/i-spent-18-months-rebuilding-my-algorithmic-trading-in-rust-im-filled-with-regret-d300dcc147e0"
---


## NexusTrade - AI-Powered Algorithmic Trading Platform

저는 어린이자 희망 넘치는 러스트 팬이었습니다. 서류상으로 러스트는 신이 디자인한 프로그래밍 언어와 같았습니다. 러스트는 가장 빠르고 안전한 프로그래밍 언어 중 하나입니다.

러스트가 완벽한 언어라고 생각하는 사람이 많았습니다. 온라인에서 러스트 프로그래밍 언어에 대해 검색하면 압도적으로 긍정적인 의견을 만나게 될 것입니다. 매체의 모든 안내서, 레딧의 모든 게시물, 스택 오버플로우의 모든 답변이 빛나고 있습니다.

![이미지](/assets/img/2024-06-30-Ispent18monthsrebuildingmyalgorithmictradingplatforminRustImfilledwithregret_0.png)

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

주어진 이유로 TypeScript에서 떨어지기로 결정했고, 오픈소스 알고리즘 트레이딩 시스템 전체를 Rust로 다시 작성하기로 했습니다.

# Rust에 중립적인 평가를 내린 적이 있어요. 그걸 철회합니다.

저는 4개월 전 Rust에 대한 제 경험에 대해 썼습니다. 제 마지막 글에서 저는 속도와 열거형 및 강력한 유형화와 같은 언어 설계의 일부 측면을 정말 좋아하는 반면, 그 언어를 정말 좋아하지는 않았다고 결론 내렸습니다. 제 글은 Reddit에서 가혹한 비평을 받았는데, 그 중 하나는 제 글을 작성할 때 ChatGPT를 사용했다고 비난하는 높은 투표를 받은 댓글이 포함되어 있었어요.

![2024-06-30-Ispent18monthsrebuildingmyalgorithmictradingplatforminRustImfilledwithregret_1.png](/assets/img/2024-06-30-Ispent18monthsrebuildingmyalgorithmictradingplatforminRustImfilledwithregret_1.png)

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

포스팅 후에 Rust에 공정한 기회를 주지 않았다고 생각했어요. 아마도 그냥 순진했거나 잘못된 기대로 들어왔던 걸지도 몰라요.

이제 언어를 좀 더 오래 사용해본 뒤, 자신있게 한 가지 결론을 낼 수 있어요…

이 언어는 정말 쓰레기야.

# Rust에서 정말 싫어하는 점

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

만약 Rust가 무슨 열망인지에 대한 기사를 찾고 있다면 인터넷 어디든 찾아보세요. 이 언어에 대해 중립적인 정보를 찾기 어려울 겁니다. 이 기사는 이 게으른 언어에 대한 제 혐오에 집중한 랜트일 겁니다.

![이미지](/assets/img/2024-06-30-Ispent18monthsrebuildingmyalgorithmictradingplatforminRustImfilledwithregret_2.png)

## 끔찍하고 장황하며 직관적이지 않은 구문 및 의미론

![이미지](/assets/img/2024-06-30-Ispent18monthsrebuildingmyalgorithmictradingplatforminRustImfilledwithregret_3.png)

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

아무도 Rust가 극악한 의미론을 가지고 있다고 말한 적이 있다면 당신 얼굴을 향해 거짓말을 하고 있어요. 어떤 부분들은, 만일 극도로 강력한 큰 언어 모델에 접근할 수 없다면, 함수를 작성하는 것이 문자 그대로 불가능해질 수 있어요. 저는 run_transaction 함수에서 where 절을 찾는 데 90분을 쓰고 싶지 않아요. 단지 제 미친 함수를 작성하고 싶을 뿐이에요.

결국, 제가 도우미 함수 아이디어를 완전히 포기해야 했어요, 왜냐하면 코드를 컴파일할 수 없었거든요. 사람들이 Rust의 가장 큰 강점으로 주장하는 것(오류를 제거하기 위한 엄격한 컴파일러)은 Rust의 가장 큰 결함 중 하나예요. 그냥 가비지 컬렉터를 주시고, 제가 하고 싶은 대로 하게 해주세요!

반면에, 만약 Go로 이 같은 함수를 작성한다면, 다음과 같이 보일 거예요:

![이미지](/assets/img/2024-06-30-Ispent18monthsrebuildingmyalgorithmictradingplatforminRustImfilledwithregret_4.png)

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

함수의 핵심은 상대적으로 동일하지만, 코드가 정상적으로 작동하는 방법을 알아내기 위해 역처 엄청 하실 필요가 없어요. 그냥 작동해요! 

## 엄청난 오류 처리

Rust는 오류 처리에 대해 정말 멋진 일을 합니다. unsafe 언랩을 피한다면, 코드가 실행되고 계속 실행될 것이라고 확신할 수 있어요. NilPointerExceptions나 처리되지 않은 오류는 더 이상 발생하지 않죠. 좋지 않나요! (그렇죠?)

잘못되었어요. 왜냐하면 데이터가 잘못되거나 예기치 못한 일이 발생할 때, 일이 어째서 그렇게 된 건지 알아내기 위해 고군분투해야 해요. 아마 제가 바보인 건지, 스택 트레이스를 활성화하는 방법을 모르는 건지 모르겠어요. 하지만 어플리케이션에서 오류가 발생하면, 왜 그런 일이 일어났는지 전혀 모르겠어요.

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

<img src="/assets/img/2024-06-30-Ispent18monthsrebuildingmyalgorithmictradingplatforminRustImfilledwithregret_5.png" />

그와 대조적으로 Python과 같은 언어는 정확히 무엇이 발생했는지를 당신에게 알려주는 아름다운 예술같은 스택 추적을 제공합니다. Go에서도 errors.Wrap(...)를 사용하여 응용 프로그램 전체의 오류 스택을 볼 수 있습니다. 아마도 나는 제 바보일지도 모릅니다, 왜냐하면 Rust에서 오류를 만나면 어떤 일이 발생했는지 이해하려고 하느라 혼란스러워합니다. 제 응용 프로그램 전체에 eprintln!(...)이 흩어져 있어야 합니다.

사실, 아니죠. 저는 바보가 아닙니다. 이것은 결함이 있는 언어 디자인입니다.

## 화가 나는 커뮤니티

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

핫한 주장: 러스트 커뮤니티는 가식적으로 친절하고 멋진 모습을 내세운 것만큼 실제로 그렇지 않을 수도 있어요. 그들은 자기들이 좋아하는 언어에 결함이 있다고 말해주는 걸 싫어하는 허세 부리는 사람들이라고 할 수 있어요.

![이미지](/assets/img/2024-06-30-Ispent18monthsrebuildingmyalgorithmictradingplatforminRustImfilledwithregret_6.png)

예를 들어, 저는 Rust 서브레딧에서 MongoDB Rust 크레이트로 에러 핸들링을 개선하는 방법에 대해 질문했더니 다음과 같은 답변을 받았어요:

- 포스트그레스로 바꾸세요 (그래, 몇 개의 구리 에러 메시지 땜에 전체 데이터베이스 설계를 다시 하겠지)
- 왜 MongoDB를 쓰는 거야? (나는 그것이 좋아. 다음 질문은?)
- MongoDB는 고(Go)와 파이썬(Python)에서도 나쁘죠 (어쩌면 그렇겠지만, TypeScript에서는 괜찮아요. 그리고 네 뭐라고 하는 건 내 질문에 대한 대답이 되지 않아요)
- (거의 없는) 실제로 도움이 되는 에러 메시지 개선 제안

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

Rust와 같이 사이코패스 같은 프로그래밍 커뮤니티는 없다고 생각해. 이 언어의 엄청난 학습 곡선, 장황함, 형편없는 오류 메시지, 엉뚱한 구문, 의심스러운 언어 디자인 선택과 같은 큰 결함들을 모두 무시하고 있어. 그들은 개발자의 능력 문제일거라고 주장하는데, 나는 그것이 미친 짓이라고 생각해!

# 마지막으로

모든 이야기를 한 마디로 정리하면, Rust에는 몇 가지 장점이 있다. 빠르다는 점... 음, 그게 대부분이야.

아마 안전한 측면이 있다고 해야겠군. C++과 비교해 보면 명백히 더 나은 언어지만, 다른 언어(예: Go)와 비교할 때는 "안전성"이 오히려 단점이라고 생각해. 내 어플리케이션이 실행하는데 몇십 밀리초 더 걸리더라도, 개발 시간이 절반으로 줄어든다면 더 나은 것 같아.

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

밝은 면에서 말하자면, Go로 앱을 개발한다고 선택한 나는 아마 후회를 할 것이다. "만약 더 빠를 수 있다면?" 라고 생각할 것이고, "Rust가 빵을 썰어 먹기 좋은 제품이라는 또 다른 기사가 있네. 어떻게 이런 실수를 했지!" 라고 스스로 생각할 것이다.

적어도 이제 Rust를 알게 되어 어떤 것이든 배울 수 있다고 느낀다. 혹은 그냥 재미로 OCaml을 배울 수도 있지 않을까? Rust보다 그렇게 나쁠리가 없을 텐데?

읽어 주셔서 감사합니다! 알고리즘을 트레이딩하고 인공지능에 관심이 있다면 오로라의 인사이트로 구독하세요! Rust가 얼마나 빠른지 확인하려면 오늘 NexusTrade에 계정을 만들어보세요!