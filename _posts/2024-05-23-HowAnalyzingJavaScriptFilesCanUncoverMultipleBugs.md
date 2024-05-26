---
title: "자바스크립트 파일 분석으로 여러 버그를 발견하는 방법"
description: ""
coverImage: "/assets/img/2024-05-23-HowAnalyzingJavaScriptFilesCanUncoverMultipleBugs_0.png"
date: 2024-05-23 18:45
ogImage:
  url: /assets/img/2024-05-23-HowAnalyzingJavaScriptFilesCanUncoverMultipleBugs_0.png
tag: Tech
originalTitle: "How Analyzing JavaScript Files Can Uncover Multiple Bugs"
link: "https://medium.com/@l0da/how-i-hacked-one-of-these-big-companies-js-files-analysis-7cf47372b642"
---


![image](/assets/img/2024-05-23-HowAnalyzingJavaScriptFilesCanUncoverMultipleBugs_0.png)

السَّلاَمُ عَلَيْكُمْ وَرَحْمَةُ اللهِ وَبَرَكَاتُه

Peace be upon you, and the mercy of Allah, and His blessings

Hello World !


<div class="content-ad"></div>

4월 2일, 특정 타겟 BBP에 대한 정찰을 시작했어요. 유감스럽게도 이 글을 쓰는 시점까지 그들은 공개 공개 요청에 대답하지 않았습니다. 그래서 나는 그것을 '삭제됨'이라고 부를 것이에요.

나는 주요 도메인을 선택했어요. 그 곳은 게시물 작성, 댓글 남기기, 그리고 플랫폼 내에서 사용자에게 메시지 보내기와 같은 기능을 갖춘 소셜 웹사이트였어요. 그리고 수동 테스트를 시작했어요.

프로세스 중 어느 시점에서 메인 JS 파일로 이동하여 로컬로 다운로드했어요. 아마 뭔가 발견할 수 있을 것 같아서 그 파일을 읽어보기 위해서 말이에요!

JS 코드가 약 220k 라인이었는데, 그냥 "아니, 내가 이겨."라고 생각했어요.

<div class="content-ad"></div>


![Image](/assets/img/2024-05-23-HowAnalyzingJavaScriptFilesCanUncoverMultipleBugs_1.png)

당연히 전체를 다 읽을 생각은 없었습니다 (아닌 걸까요?). 그 대상의 버그를 찾아 큰 회사나 공개 버그 바운티 프로그램에서 버그를 발견하는 두려움을 극복하기로 했습니다.

그래서 나는 JS 파일을 정신적으로 섹션으로 나누었습니다. 매일 웹사이트를 수동으로 테스트하고 지루해지면 JS 파일의 일부를 읽어 나가기로 했습니다.

그 결과 그 대상에 대한 몇 개의 서브도메인 코드를 발견할 수 있었습니다.


<div class="content-ad"></div>

```js
var i = t(97871),
        e = t(42868),
        g = t(20737),
        _ = t(38717),
function f(s, r) {
            (this.appService = s),
              (this.responseService = r),
              (this.clientDomains = {
                1111: [
                  "webqa.redacted.com",
                  "localhost:4200",
                  "localhost:4000",
                ],
                "32 chars id smth idk fr!": [
                  "qc-dashboard.rs.zz.redacted.com",
                  "domain-website-something.redacted.com",
                ],
                "something-Redacted": [
                  "place-dashboard.website.domain.redacted.com",
                  "employee-site-testing.redacted.com",
                ],
              });
```

이 중에서 webqa Redacted com만 작동 중이었습니다. 그것은 웹 품질 보증 사이트였습니다. 원래 테스트하던 사이트의 복제본이었지만 프로덕션 웹 사이트에 라이브로 이동하기 전에 테스트 중인 몇 가지 새로운 기능이 포함되어 있었습니다. 코드 기반은 거의 동일했습니다. 추가 탐색을 위해 주요 JS 파일을 다운로드했지만 차이가 거의 없었습니다.

스테이징 사이트의 사이트맵에서 흥미로운 사실을 알게 되었습니다. 그들이 아마도 테스트하고 있는 보안 문제에 대한 참조 번호가 포함되어 있었습니다.

<img src="/assets/img/2024-05-23-HowAnalyzingJavaScriptFilesCanUncoverMultipleBugs_2.png" />

<div class="content-ad"></div>

그러한 환경에서는 개발자들이 중요한 버그를 테스트할 가능성이 높아지며, 공격자에게 기회의 창을 제공할 수 있습니다. 공격자는 웹 사이트를 모니터링하여 심각한 버그가 스테이징 환경에 도입될 때까지 기다린 다음 해당 버그를 프로덕션 웹 사이트에서 잠재적으로 악용할 수 있습니다.

저는 모니터링할 수 있지만 중복되지 않을까 생각했습니다.

"(당신이 아니라면 다른 사람이 보고할 수도 있습니다)"

저는 CVSS 3.1로 높은 중요도로 보고했습니다.

<div class="content-ad"></div>

테이블 태그를 Markdown 형식으로 변경해주세요.

<div class="content-ad"></div>

테이블 태그를 마크다운 형식으로 변경해주세요.

<div class="content-ad"></div>

X/Twitter에서 저를 팔로우하세요: [https://twitter.com/L0daW](https://twitter.com/L0daW)
