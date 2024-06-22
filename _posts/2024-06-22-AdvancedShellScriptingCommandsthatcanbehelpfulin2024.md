---
title: "2024년에 유용할 고급 Shell 스크립팅 명령어 9선"
description: ""
coverImage: "/assets/img/2024-06-22-AdvancedShellScriptingCommandsthatcanbehelpfulin2024_0.png"
date: 2024-06-22 23:42
ogImage: 
  url: /assets/img/2024-06-22-AdvancedShellScriptingCommandsthatcanbehelpfulin2024_0.png
tag: Tech
originalTitle: "Advanced Shell Scripting Commands that can be helpful in 2024"
link: "https://medium.com/@devendunegi06/advanced-shell-scripting-commands-that-can-be-helpful-in-2024-1cfbf4e3ab34"
---



![이미지](/assets/img/2024-06-22-AdvancedShellScriptingCommandsthatcanbehelpfulin2024_0.png)

안녕하세요! 저는 최근 회사에서 일하는 동안 일반 업무 외에도 쉘 스크립팅을 사용하여 도구를 만들었습니다.

쉘의 구문은 다른 프로그래밍 언어와 비교했을 때 약간 복잡하여 이해하고 구현하기가 조금 어렵지만, 쉘 스크립팅은 자동화를 위한 다양한 용도로 사용됩니다. 쉘 스크립팅을 공부하면서 얻은 지식으로 매우 만족스럽습니다.

다음은 제가 사용하고 코딩하는 동안 도움이 된 일련의 명령어입니다.


<div class="content-ad"></div>

- grep: 쉘 스크립팅에서 사용되는 매우 유용한 명령어로, 파일에서 패턴이나 정규 표현식을 찾아 해당 grep이 제공하는 옵션에 따라 출력을 내용에 맞게 얻을 수 있습니다.

```js
grep [options] pattern filename
```

일부 옵션은 다음과 같습니다:-

- c: 이 옵션은 파일에서 패턴을 포함하는 일치하는 행의 수를 세는데 사용됩니다. 예를 들어 grep -c “hello” sample.txt
- -l: 이 옵션은 해당 패턴을 포함하는 행을 출력합니다. 예를 들어 grep -l “finding” commentsTracker.sh
- -i: 이 옵션은 대소문자를 구별하지 않고 파일 내에서 검색하므로 해당 단어의 어떤 형태든 검색됩니다.

<div class="content-ad"></div>

2) mailx: 리눅스에서 자동화된 작업에서 메일을 보내는 데 사용됩니다. 우리의 경우에는 특정 시나리오 하에서 mailx를 사용하여 메일을 보내는 것이 상당히 유용했어요.

```sh
echo -e "내용" | mailx -s "제목" -r 발신자 수신자1 수신자2
```

여기서 내용을 우리가 원하는 본문 내용으로, 제목을 이메일 본문의 제목으로 대체할 수 있어요.

mailx를 사용하면 -c를 사용하여 참조 메일 수신자도 추가할 수 있어요.

<div class="content-ad"></div>

```js
echo -e "내용" | mailx -s "제목" -r 발신자 -c 참조사용자1,참조사용자2,참조사용자3 수신자1 수신자2
```

여기서 우리는 참조 사용자 ccuser1, ccuser2, ccuser3과 수신자1 및 수신자2가 메일 수신함에 있을 사용자 세 명을 가지고 있음을 볼 수 있습니다.
이 기능은 매우 흥미로운 것입니다.

3) ` :- 이 파일에 데이터를 쓰는 데 사용됩니다. 데이터가 이미 존재하는 경우 데이터가 덮어쓰여집니다.

파일이 없으면 파일이 만들어집니다.


<div class="content-ad"></div>

```js
echo "Hello Medium" > file.txt
```

file.txt:- Hello Medium

` :- On the other hand, this appends data to the file instead of overwriting it.

```js
echo "Adding this text" >> file.txt
```

<div class="content-ad"></div>

아래 내용을 Markdown 형식으로 변경해보세요.

파일.txt: - 안녕하세요 Medium. 이 텍스트를 추가합니다.

4) **: - *검색할 문자열을 사용할 수 있습니다.*

예: search_string = "어디에 있나요"

<div class="content-ad"></div>

예를 들어, 변수인 description에 정확한 문자열 search_string1을 포함하는 문자열에서 이 연산자를 사용하여 문자열을 검색할 수 있습니다. 정확히 동일한 문자열을 검색하므로 공백도 문제가 될 수 있습니다.

```js
if [[ $description == *"$search_string1"* ]]; then
      echo "description에 search_string1이 있습니다."
```

5) =~:- =~를 사용하여 지정된 문자열에서 부분 문자열을 일치시킬 수 있고, 해당 문자열에 부분 문자열이 있는지 확인할 수 있습니다.

```js
if [[ $variable =~ YES|Yes|Y ]]; then
  echo "변수에 YES 또는 Yes 또는 Y 부분 문자열이 포함되어 있습니다."
```

<div class="content-ad"></div>

여기 지정된 문자열을 검색할 때 사용자에게 정말 편리합니다.

6) Export:- 이 명령은 변수를 내보내어 후속 프로세스가 나중에 액세스할 수 있도록 하는 데 사용됩니다.

```js
export BATCH_DATE="29042001"
```

예를 들어 셸 스크립트에서 BATCH_DATE를 내보내고 나중에이 셸 스크립트가 호출한 파일 내에서 액세스합니다.

<div class="content-ad"></div>


echo $BATCH_DATE

일반적으로 코드 내에서 변수 condition을 내보내고 사용하고 싶을 때 사용됩니다.

이 글에서 무언가를 얻었다면 좋아요와 댓글 부탁드립니다. 이는 제가 더 많은 유용한 글을 작성할 자극이 됩니다.

LinkedIn에서 저와 연결하고 팔로우해주세요: https://www.linkedin.com/in/devendu-negi-142b6618b/


<div class="content-ad"></div>

제 YouTube 채널을 한 번 둘러보시는 것을 고려해 주시면 좋겠어요! 저는 힌디어로 콘텐츠를 올리고 있으며 일상 명언의 짧은 영상도 함께 제작하고 있어요.

https://www.youtube.com/channel/UCaGvN7VhqTRa9sCHxcgJ-Pw

누군가가 DSA에 대해 1대1로 안내를 받고 싶다면 topmate에서 저와 연락할 수 있어요: https://topmate.io/devendu_negi/

커피 한 잔 사주시면 감사하겠어요!
https://buymeacoffee.com/devendunegi

<div class="content-ad"></div>

만약 내 다른 글들이 당신의 소프트웨어 엔지니어링 여정에 가치를 더했다면, 더 많은 글을 보기 위해 내 계정을 팔로우해보세요.

코딩을 즐기며, 당신의 목표를 이루길 바라요 :)