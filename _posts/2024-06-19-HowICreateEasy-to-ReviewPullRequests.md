---
title: "잘 검토할 수 있는 풀 리퀘스트를 만드는 방법"
description: ""
coverImage: "/assets/img/2024-06-19-HowICreateEasy-to-ReviewPullRequests_0.png"
date: 2024-06-19 10:51
ogImage:
  url: /assets/img/2024-06-19-HowICreateEasy-to-ReviewPullRequests_0.png
tag: Tech
originalTitle: "How I Create Easy-to-Review Pull Requests"
link: "https://medium.com/stackademic/how-i-create-easy-to-review-pull-requests-48fb62c41882"
---

## 생산성 | 프로그래밍 | 혁신

![이미지](/assets/img/2024-06-19-HowICreateEasy-to-ReviewPullRequests_0.png)

리뷰하기 쉬운 풀 리퀘스트(Pull Requests)를 작성하면 머지 프로세스가 빨라집니다. 머지 속도를 높이면 비즈니스 진행 속도와 제품 품질 향상 속도가 빨라집니다.

그러므로, 리뷰하기 쉬운 풀 리퀘스트를 만드는 것이 매우 중요하다고 생각합니다.

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

## 1. 변경된 파일이 너무 많습니까?

풀 리퀘스트를 생성하기 전 (또는 구현하기 전) 확인해야 할 사항 중 하나는 작업의 세분성입니다. 작업의 세분성이 너무 크면 한 번의 풀 리퀘스트에서 변경된 파일의 수가 증가하여 리뷰어의 작업 부담이 커집니다. 변경된 파일의 수가 10개를 초과하면 해당 기능을 분할할 수 있는지 다시 고려하게 됩니다.

하나의 기능을 모두 한 번에 풀 리퀘스트에 들어가게 하기보다는 작업을 분할하고 개별적으로 릴리스할 수 있는 단위로 풀 리퀘스트를 그룹화하는 것이 리뷰어의 작업 부담을 줄일 수 있습니다. 따라서 작업을 세분화하여 리뷰하기 쉬운 풀 리퀘스트를 생성하는 것이 중요하다고 생각합니다.

## 2. 요약 섹션 강화하기

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

가장 중요한 것은 풀 리퀘스트 요약 섹션을 개선하는 것이라고 생각해요. 리뷰에 필요한 모든 정보를 요약 섹션에 모아두는 걸 염두에 두고 있어요.

일부 내용은 디자인 제안서 링크와 같은 것들이 명세를 요약한 계획 문서와 중복되지만, 풀 리퀘스트의 요약 섹션에 요약함으로써 전환을 쉽게하고 리뷰 시간을 줄일 수 있다고 생각해요. 구체적으로는 다음 내용을 포함하고 있어요.

- 기능이 필요한 배경
- 작업 세부 내용
- 작동 확인된 항목
- 구현 영향 범위
- 구현하는 동안 고민했던 포인트
- 스크린샷 (디자인 변경이 있는 경우)
- 다양한 링크
- 작업 티켓 링크
- 계획 문서 링크
- 명세를 논의하는 채팅이 있다면 링크 제공
- Figma와 같은 디자인 아이디어 링크

또한 팀 내에서 템플릿을 만들고 해당 항목에 따라 만드는 것도 중요하다고 생각해요.

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

## 3. 리뷰 요청하기 전 자가 검토

리뷰 요청을 하기 전에 자가 검토를 하는 것이 중요합니다. 요약 섹션이 이해하기 쉬운지, 팀의 코딩 규칙을 위반하는 부분이 있는지, 영향 범위 내의 빠뜨린 부분이 있는지 다시 한번 확인할 것입니다.

자가 검토 항목을 준비하고 이전 리뷰에서 받았던 동일한 코멘트를 받지 않도록 주의하고 있습니다. 리뷰를 요청하기 전에 자가 검토를 실시함으로써 구현 중에 보지 못했던 실수를 알 수 있고 더 나은 구현 방법을 발견할 수 있습니다.

자가 검토를 실시함으로써 리뷰를 요청하기 전 리드 타임은 조금 더 길어지지만 그 결과로 리뷰어의 부담이 줄어들고 리뷰해야 할 영역에 집중할 수 있습니다. 우리는 자가 검토를 중요시합니다.

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

내 자기평가 항목 중 일부를 소개하고 싶어요. 이것은 어떤 사람들은 너무 자명해서 확인할 필요가 없다고 생각할 수 있는 항목이라고 생각해요. 그러나 두려움이나 긴장 상태일 때는 이런 중요한 항목도 놓치기 쉬우니, 리뷰를 제출하기 전에 이 항목들을 한 번 확인해요.

## 코드 확인

- 라우팅 추가 부분은 ABC 순서로 정리되어 있나요?
- 스키마 파일에 추가된 열은 응집력을 고려하여 정렬되었나요?
- 업데이트나 저장과 같은 레코드 저장 메서드는 save!나 update!와 같은 예외를 일으키는 방식으로 사용되었나요?
- N + 1 문제가 있나요?

## 코드 이외의 확인할 사항

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

- 파일 변경 사항이 너무 많나요? 10개 이상의 파일이 있는 경우 주의하세요.
- 풀 리퀘스트를 생성할 때 자신을 할당했나요?
- 중요한 디자인 변경 사항이 포함된 작업의 경우, 스마트폰 또는 PC 화면뿐만 아니라 태블릿 화면에서도 표시를 확인했나요? (화면 캡처도 함께 첨부해야 합니다)

## 개요

이번에는 '리뷰하기 쉬운 풀 리퀘스트를 제출하는 방법'이라는 제목의 기사를 작성했습니다. 작업의 세분화, 요약 및 자가 검토는 특별한 일이 아니며, 모두 당연한 것으로 여겨질 수 있습니다.

그러나 저는 보통의 일을 철저히 하는 것이 중요하다고 믿습니다. 비즈니스 성장 속도를 높이기 위해, 계속해서 리뷰하기 쉬운 풀 리퀘스트를 작성할 것입니다.

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

# 스택아데믹

끝까지 읽어 주셔서 감사합니다. 떠나시기 전에:

- 작가를 응원하고 팔로우해 주시면 감사하겠습니다! 👏
- 저희를 팔로우해 주세요! X | LinkedIn | YouTube | Discord
- 다른 플랫폼에서도 만나보세요: In Plain English | CoFeed | Venture
