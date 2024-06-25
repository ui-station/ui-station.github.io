---
title: "루비 커맨드 라인 옵션을 다루는 OptionParser 사용 사례와 함께"
description: ""
coverImage: "/assets/img/2024-06-19-RubyCommand-lineoptionsusingOptionParserWithUseCase_0.png"
date: 2024-06-19 10:24
ogImage:
  url: /assets/img/2024-06-19-RubyCommand-lineoptionsusingOptionParserWithUseCase_0.png
tag: Tech
originalTitle: "Ruby Command-line options using OptionParser [With Use Case]"
link: "https://medium.com/pengenpaham/ruby-command-line-options-using-optionparser-with-use-case-c96487670efe"
---

터미널에서 실행하는 간단한 응용 프로그램이나 스크립트를 명령줄 응용 프로그램이라고 합니다. 대부분의 경우, 명령줄 응용 프로그램을 사용할 때는 옵션과 인수를 사용하여 상호 작용해야 합니다.

이 문서에서는 명령줄 옵션과 인수를 처리하기 위해 루비 OptionParser를 사용하는 방법을 배우게 될 것입니다. 간단한 사용 사례로도 배워보겠습니다.

# 목차

- 사용 사례
- 디자인 구매
- OptParse 동작
- 결론
- 참고문헌

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

# 사용 사례

더 잘 이해하기 위해 간단한 사용 사례로 학습하겠습니다. 아래는 우리의 루비 스크립트에 대한 요구사항 목록입니다:

- 이 스크립트는 3rd party 웹사이트로부터 데이터를 수집하는 scrapper.rb라고 불립니다.
- 주어진 URL을 기반으로 데이터를 수집할 수 있어야 합니다.
- verbose 옵션을 사용하여 데이터를 수집할 수 있지만, 이는 선택 사항입니다.
- 수집된 데이터를 JSON 및 CSV로 내보낼 수 있어야 합니다.
- 결과를 출력하기 위해 내보내기 또는 미내보내기를 사용할 수 있어야 합니다.
- 짧은 이름 및 긴 이름 옵션을 사용할 수 있어야 합니다.
- 필수 매개변수는 --target-url뿐입니다.
- 사용 가능한 옵션과 매개변수를 확인하기 위해 --help 옵션을 사용할 수 있어야 합니다.

# 디자인 구매하기

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

위의 요구사항에 따라 아래와 같은 디자인이 완성되었습니다:

![Ruby Command-line options using OptionParser With Use Case](/assets/img/2024-06-19-RubyCommand-lineoptionsusingOptionParserWithUseCase_0.png)

우리는 루비 스크립트를 scrapper.rb라는 이름으로 지었습니다. 선택적 옵션, 필수 옵션, 선택적 인자를 가지고 있음을 보실 수 있습니다. 그리고 가장 중요한 것은 사용 가능한 스크립트 옵션 목록을 표시하기 위해 --help 옵션을 구현해야 합니다.

옵션 파서를 사용하여 처리하는 방법을 살펴보겠습니다. 정말로 생각보다 간단합니다 :))

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

# OptionParser 활용하기

위에서 설계한 내용을 OptionParser로 구현해봅시다.

## ARGV

시작하기 전에, 루비에는 명령행 옵션과 인수를 가져오기 위한 ARGV가 있습니다. 이는 우리 프로그램에서 배열로 출력됩니다.

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

![Image](/assets/img/2024-06-19-RubyCommand-lineoptionsusingOptionParserWithUseCase_1.png)

6번 라인을 살펴보세요. 옵션과 인자를 사용하여 스크립트를 실행하면 모든 값을 배열 값으로 출력합니다.

```js
➜ ruby scrapper.rb --name MyName --age 10
["--name", "MyName", "--age", "10"]
```

ARGV에 대해 더 많이 알고 싶다면 [이 링크](https://link-to-more-info-about-ARGV)를 확인하세요.

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

## 기본 설정 및 도움말 옵션

optparse를 설정하고 --hello와 같은 샘플 옵션을 추가하여 스크립트가 제대로 작동하는지 확인합니다.

![이미지](/assets/img/2024-06-19-RubyCommand-lineoptionsusingOptionParserWithUseCase_2.png)

긴 줄을 추가한 것 처럼 보여서 놀랐죠! 그러나 optparse를 사용할 때 기본 구조를 보여주는 것입니다. 자세한 내용은 아래와 같습니다:

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

- line 1: optparse를 사용하려면 반드시 요구해야 합니다.
- line 10: parser로 OptionParser 객체를 초기화합니다.
- line 13: hello라는 간단한 옵션을 추가합니다. --hello로 사용할 수 있고 부울 값으로 반환됩니다.
- line 16–17: options라는 빈 해시를 생성하고 17번째 줄에서 파싱 결과를 할당합니다.

이제 scrapper.rb를 실행할 때 --help 및 --hello를 사용할 수 있습니다.

```js
➜ ruby scrapper.rb --hello
[]
{:hello=>true}

➜ ruby scrapper.rb --help
Usage: scrapper [options]
        --hello                      hello world from parser
```

보시는 대로, --help를 사용하면 13번째 줄의 설명을 기반으로 명령 설명이 출력됩니다.

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

네, 맞아요. optparse은 자동으로 우리를 위해 --help 옵션을 만들어 줄 거에요.

## 대상 URL

--hello 옵션을 제거하고 라인 12를 사용해서 대체해요. 우리는 Short Name과 Long Name을 사용하고 필수 인자를 가진 옵션을 추가해요.

![이미지](/assets/img/2024-06-19-RubyCommand-lineoptionsusingOptionParserWithUseCase_3.png)

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

테스트해보세요:

```js
➜ ruby scrapper.rb --target-url
scrapper.rb:17:in `<main>': missing argument: --target-url (OptionParser::MissingArgument)

➜ ruby scrapper.rb -t
scrapper.rb:17:in `<main>': missing argument: -t (OptionParser::MissingArgument)

➜ ruby scrapper.rb --target-url https://MyTargetUrl
[]
{:"target-url"=>"https://MyTargetUrl"}

➜ ruby scrapper.rb -t https://MyTargetUrl
[]
{:"target-url"=>"https://MyTargetUrl"}
```

## 내보내기

내보내기를 위해 `[no]export`라는 새로운 옵션을 추가했습니다. 이 옵션의 값은 부울(boolean)입니다. 이것은 스크립트에서 `--export` 및 `--no-export` 옵션이 사용 가능하게 만듭니다.

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

<img src="/assets/img/2024-06-19-RubyCommand-lineoptionsusingOptionParserWithUseCase_4.png" />

테스트해보세요:

```js
➜ ruby scrapper.rb --export
[]
{:export=>true}

➜ ruby scrapper.rb --no-export
[]
{:export=>false}
```

## 형식

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

저희 스크립트는 출력 형식 지원을 하며, JSON 및 CSV 형식만 지원합니다.

이 경우를 처리하려면 명시적인 값을 사용해야 합니다. 간단히 [‘JSON’, ‘CSV’]를 추가하면 됩니다. 저희 스크립트는 정의된 명시적인 값만 허용할 것입니다.

이와 관련된 자세한 내용은 아래 이미지를 참고해 주세요.

![이미지](/assets/img/2024-06-19-RubyCommand-lineoptionsusingOptionParserWithUseCase_5.png)

테스트해 보세요!

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

➜ ruby scrapper.rb --format HTML
scrapper.rb:19:in `<main>': 올바르지 않은 인수입니다: --format HTML (OptionParser::InvalidArgument)

➜ ruby scrapper.rb --format
scrapper.rb:19:in `<main>': 인수가 누락되었습니다: --format (OptionParser::MissingArgument)

➜ ruby scrapper.rb --format JSON
[]
{:format=>"JSON"}

➜ ruby scrapper.rb -f JSON
[]
{:format=>"JSON"}

## 상세모드

마지막으로 --verbose라는 옵션이 있으면 15번 라인에 이것을 추가하세요.

![Ruby Command Line Options Using OptionParser with Use Case](/assets/img/2024-06-19-RubyCommand-lineoptionsusingOptionParserWithUseCase_6.png)

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

위 내용을 테스트해보세요:

```js
➜ ruby scrapper.rb -v
[]
{:verbose=>true}

➜ ruby scrapper.rb --verbose
[]
{:verbose=>true}
```

## 도움말

위에서 언급한대로, OptionParser는 자동으로 --help 옵션을 위한 명령어를 만들어줍니다. 스크립트를 업데이트하여 --help 옵션을 처리할 필요가 없습니다.

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

최종 도움말 출력을 확인해 봅시다.

```js
➜ ruby scrapper.rb --help
Usage: scrapper [options]
    -t, --target-url VALUE           데이터를 가져올 대상 URL입니다.
        --[no-]export                데이터를 내보내려면 'export'를 사용하고, 터미널 출력만 되도록 하려면 'no-export'를 사용합니다.
    -f, --format VALUE               JSON 또는 CSV로 출력 형식을 정의합니다.
    -v, --verbose                    출력을 자세하게 설정합니다.
```

## 배너

터미널에서 더 나은 출력 메시지를 위해 parser.banner를 사용할 때, 프리 텍스트로 배너 값을 설정할 수 있습니다.

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

![image](/assets/img/2024-06-19-RubyCommand-lineoptionsusingOptionParserWithUseCase_7.png)

Try it out:

```js
➜ ruby scrapper.rb --help
사용법: scrapper.rb [options]
    -t, --target-url VALUE           데이터를 가져올 대상 URL입니다.
        --[no-]export                데이터를 내보내려면 'export', 터미널 출력만을 원하면 'no-export'를 사용하십시오.
    -f, --format VALUE               JSON 또는 CSV로 출력 형식을 정의합니다.
    -v, --verbose                    출력을 상세하게 설정합니다.
```

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

루비에는 많은 강력한 기능들이 있어요. 이 경우에는 옵션 파서를 사용하여 명령행 옵션과 인수를 다루고 있어요.

이 글이 도움이 되었으면 좋겠어요. 읽어 주셔서 감사합니다.

루비 친구들, 즐거운 하루 보내세요! 💎

# 참고문헌
