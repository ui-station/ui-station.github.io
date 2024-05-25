---
title: "HTTP 파라미터 오염을 통한 오픈 리다이렉트"
description: ""
coverImage: "/assets/img/2024-05-23-Open-RedirectThroughHTTPParameterPollution_0.png"
date: 2024-05-23 18:46
ogImage: 
  url: /assets/img/2024-05-23-Open-RedirectThroughHTTPParameterPollution_0.png
tag: Tech
originalTitle: "Open-Redirect Through HTTP Parameter Pollution"
link: "https://medium.com/@davidkarpinski1/open-redirect-through-http-parameter-pollution-ce5a3be7c78e"
---


안녕하세요 여러분, 어떠세요?

제 친구
Saigo
의 요청으로, 예전에 신고한 Open-Redirect에 대한 설명을 작성 중입니다. 실제로 큰 영향을 미치지는 않았지만요.

![Open-Redirect](/assets/img/2024-05-23-Open-RedirectThroughHTTPParameterPollution_0.png)

# 버그 발견

<div class="content-ad"></div>

저번 글에서 오픈 리다이렉션에 대해 설명한 적이 있죠. 그럼 바로 실습으로 넘어가 보겠습니다. 테스트할 대상은 redacted.com이라고 지칭하겠습니다. 로그인 URL의 next 파라미터에 절대 URL이 전달되고 있다는 점을 발견했습니다:

```js
https://redacted.com/login.php?next=https://redacted.com/account.php
```

처음에는 next 파라미터를 http://evil.com으로 변경해 보았지만 실패했어요. 그래서 HTTP 파라미터 오염을 테스트하기로 결정했습니다. PHP로 구축된 사이트였기 때문에 파라미터의 마지막 등장이 일반적으로 우선권을 가집니다. 따라서 제가 만든 최종 URL은 다음과 같습니다:

```js
https://redacted.com/login.php?next=https://redacted.com/account.php?next=http://evil.com
```

<div class="content-ad"></div>

로그인 후 http://evil.com으로 성공적으로 리디렉션되었습니다.

감사합니다!