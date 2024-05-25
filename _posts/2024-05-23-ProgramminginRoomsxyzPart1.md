---
title: "Roomsxyz에서의 프로그래밍 파트 1"
description: ""
coverImage: "/assets/img/2024-05-23-ProgramminginRoomsxyzPart1_0.png"
date: 2024-05-23 13:39
ogImage: 
  url: /assets/img/2024-05-23-ProgramminginRoomsxyzPart1_0.png
tag: Tech
originalTitle: "Programming in Rooms.xyz (Part 1)"
link: "https://medium.com/@btco_code/programming-in-rooms-xyz-part-1-cb498b2b4301"
---


Part 1 | Part 2 | Part 3

Rooms.xyz은 정확히 판타지 콘솔은 아니지만 (과거 몇 가지 기사를 써온 주제) 많은 동일한 원칙을 공유합니다. 그것은 당신이 보설 기반 미니 게임과 상호 작용 경험을 매우 쉽게 만들 수 있는 도구이며, 보설 객체와 Lua 스크립팅을 사용합니다.

공개: 나는 Rooms.xyz 뒤의 3인 스타트업 "Things, Inc."의 직원입니다. "Inc" 부분이 다소 기업적으로 보일 수 있지만, 사실은 우리는 무엇을 하는지 모르지만 멋진 것을 만들고 싶어 하는 3인 조직입니다.

이 기사는 객체의 동작을 사용자 정의하기 위해 Rooms에서 Lua 코드를 작성하는 방법의 프로그래밍 모델과 기본 사항 (매우 표면적으로)을 면밀히 살펴봅니다.

<div class="content-ad"></div>

Part 1에서는 기초 사항을 다룰 것입니다:

- 방과 물건
- 기본 프로그래밍
- 물건 이동
- 순서

![이미지](/assets/img/2024-05-23-ProgramminginRoomsxyzPart1_0.png)

# 프로그래밍을 해야 할까요?

<div class="content-ad"></div>

음, 디저트를 먹어야 하나요? 이 기사의 전제는 당신이 프로그래밍에 흥미를 느끼며 프로그래밍이 재미있다는 것입니다. 사실 당신과 나는 당신을 프로그래밍을 하지 못하게 하는 데는 약간의 노력이 필요할 것을 알고 있어요. 만약 토스트기에 API가 있다면 당신은 그것을 프로그래밍할 것입니다. 아마 그것에 API가 있는지도 모르겠네요. 토스트기 프로그래밍을 멈추고 돌아오세요.

이 모든 말의 요점은: 아니, Rooms에서 프로그래밍을 할 필요는 없어요. 단순히 기존 객체를 기반으로 아름다운 방을 만들 수 있어요(라이브러리에는 많은 객체가 있어요!), 많은 객체는 이미 기존 동작을 갖고 있어요. 그러나 높은 천장에 다다르고 동작이 재미있고 독특한 방을 만들고 싶다면, 프로그래밍이 그 방법이에요!

# 방이란 무엇인가요?

방은 작은 상호작용 3D 환경입니다. 그게 바로 당신이 만들고 있는 것. 방이나 상호작용 3D 환경에 흥미가 없다면, 저런, Rooms.xyz를 즐기진 못할 거예요. 방은 미니 게임이 될 수도 있고 사용자가 접근할 수 있는 즐거운(아닐 수도 있지만, 우리는 그렇길 바래요) 경험이 될 수도 있어요.

<div class="content-ad"></div>

![이미지](/assets/img/2024-05-23-ProgramminginRoomsxyzPart1_1.png)

이것은 미니 게임의 예시입니다. 볼링공을 클릭하여 볼링을 할 수 있는 미니 볼링 게임입니다. 아마 볼링은 이렇게 하는 거겠지요.

# 물건이란?

물건이란 객체입니다. 정말, 물건? 더 일반적인 이름을 선택할 수 없었을까요? 음, 네, 객체는 더 일반적이었겠죠. 가끔 커피를 충분히 마시지 않은 경우에는 그것들을 객체라고도 부르기도 합니다.

<div class="content-ad"></div>

그래서 방 안에 있는 각 물체는 물체(Thing)입니다. 위쪽 방에서 볼링공은 물체입니다. 각 핀도 물체이고, 테이블도 물체이며, 쥬크박스도 물체입니다. 이해하시죠. 모든 것이 물체입니다.

(*) 벽과 바닥도 물체입니다만, 누구에게도 말하지 마세요. 왜냐하면 이건 우리 디자인의 이상한 부분이라서 아직 그것이 맞는지 확신하지 못하기 때문이에요.

# 물체를 그렇게 행동하게 만드는 것은 무엇인가요?

스포일러 경고: 코드입니다. 다른 것으로 생각했나요?

<div class="content-ad"></div>

수정 모드에서 물건을 클릭하고 "Code" 버튼을 클릭하면 코드 편집기가 표시됩니다.

<img src="/assets/img/2024-05-23-ProgramminginRoomsxyzPart1_2.png" />

코드 편집기에 Lua 코드를 입력할 수 있습니다. Lua는 브라질인이 만든 사랑스러운 스크립트 언어입니다. 나도 브라질인은 아니에요. 다른 브라질인이 만든 거에요. 우리가 참 많죠.

어쨌든, Lua를 자바스크립트의 불편한 부분 없는 버전으로 생각해보세요.

<div class="content-ad"></div>

```markdown
![ProgramminginRoomsxyzPart1_3](/assets/img/2024-05-23-ProgramminginRoomsxyzPart1_3.png)

우리가 코드가 없기 때문에 처음부터 시작할 필요가 없다는 메시지가 나옵니다. 간단한 템플릿을 선택하여 시작하는 것이 좋습니다. Hello 템플릿을 선택해주세요. 이 템플릿을 사용하면 좋습니다. 왜냐하면 더 복잡한 것들을 탐험할 때 직접 모든 것을 배울 수 있다는 것을 깨닫고 이 기사를 읽는 것을 중단할 수도 있기 때문입니다.

또한 모든 API 함수에 대한 완전한 문서가 있으므로 이 자습서를 읽은 후 (또는 중간 중간에) API가 수행할 수 있는 모든 것을 알아볼 수 있습니다.

# 코딩에 들어가기 전에...
```

<div class="content-ad"></div>

Rooms.xyz에서 작업을 저장하려면 회원 계정을 만들어야 합니다. 현재 Rooms.xyz가 알파 상태이기 때문에 공식 홈페이지를 통해 초기 액세스를 요청해야 합니다. 홈페이지에서 "초기 액세스 요청" 버튼을 클릭하면 됩니다.

그러나 계정을 만들지 않고도 이 튜토리얼을 따라할 수 있습니다. 유일한 주의점은 만든 방을 저장하고 공유할 수 없다는 것입니다.

# 안녕하세요라고 말하기

안녕하세요 템플릿을 클릭하면 미리 작성된 코드가 표시됩니다. 클릭하면 객체가 "안녕하세요"라고 말하는 매우 흥미진진한 스크립트입니다.

<div class="content-ad"></div>

```markdown
![Programming in Roomsxyz Part1_4](/assets/img/2024-05-23-ProgramminginRoomsxyzPart1_4.png)

Now run it, either in the preview window (click Update Preview), or get out of the code editor and enter Preview mode.

This thing at the top is how you switch from Edit to Preview (that is, Play) mode. You probably already figured this out, but just in case, it's here:

![Programming in Roomsxyz Part1_5](/assets/img/2024-05-23-ProgramminginRoomsxyzPart1_5.png)
```

<div class="content-ad"></div>

테이블 태그를 마크다운 형식으로 변경해 보세요.

<div class="content-ad"></div>

앞으로는 코드 스크린샷을 찍지 않을 거에요 (코드를 보여주는 바보 같은 방식이라서, 커피숍에서 다른 개발자들이 내 모습을 이상하게 쳐다보고 있거든). 대신에 이렇게 작은 블록 안에 코드를 직접 쓸 거에요:

```js
-- 사용자가 클릭했을 때 실행되는 함수입니다.
function onClick()
  say("Hello")
end
```

물론 그 테이블이 모든 걸 상상할 수 있는 어떤 말이라도 할 수 있도록 string을 수정할 수 있어요. 완전히 다른 문구인 아래와 같이요:

<img src="/assets/img/2024-05-23-ProgramminginRoomsxyzPart1_7.png" />

<div class="content-ad"></div>

당신이 더 나은 내용을 찾을 수 있다고 확신해요.

# 핸들러 (미리 정의된 함수)

이전 예제에서 우리의 함수는 onClick으로 불렸어요. 이것은 우연이 아니에요: 사용자가 무언가를 클릭할 때 실행되는 함수에요. 모든 Thing은 고유의 onClick 함수를 가질 수 있어요.

이것은 중복되어 보일 수 있지만, onClick 함수는 반드시 onClick으로 불러져야 해요. "onclick"이나 "ONCLICK"이나 "clementine"으로 불러도 작동하지 않아요. 엔진은 정확히 onClick이라는 이름을 대소문자 구분하여 찾기 때문이에요.

<div class="content-ad"></div>

일부 다른 핸들러 함수의 예시는 다음과 같습니다:

- onStart(): 방이 시작될 때 호출됩니다.
- onCollision(): 물체가 다른 물체와 충돌할 때 호출됩니다.
- onButtonDown(): 가상 조이스틱 버튼이 눌렸을 때 호출됩니다.
- onButtonUp(): 가상 조이스틱 버튼이 놓였을 때 호출됩니다.
- onUpdate(): 한 프레임당 한 번 호출됩니다 (초당 60번).

지금은 이에 대해 걱정할 필요가 없어요. 다음에 일부를 살펴볼 텐데, 그 중 일부는 무시할 거에요. 하지만 그 당시에는 멀리 나아가서 당신이 이 목록을 기억할 정도가 되지 않을 거예요. 그리고 제가 이를 건너뛰었다는 걸 알아차리지 못할 거에요.

# onStart() 함수

<div class="content-ad"></div>

방이 시작할 때 사용자의 작업과 상관없이 무언가를 하고 싶을 때 유용한 기능이에요.

```js
-- 방이 시작할 때 이 함수가 실행됩니다.
function onStart()
  say("나를 클릭해주세요!")
end

-- 사용자가 클릭할 때 이 함수가 실행됩니다.
function onClick()
  say("클릭해주셔서 감사합니다")
end
```

원하는 경우에는 onStart() 안에 초기화 코드를 넣는 대신 함수 밖에 초기화 코드를 놓을 수도 있어요. 이건 스타일의 문제에요:

```js
-- 방이 시작할 때 이 코드가 실행됩니다.
say("나를 클릭해주세요!")

-- 사용자가 클릭할 때 이 함수가 실행됩니다.
function onClick()
  say("클릭해주셔서 감사합니다")
end
```

<div class="content-ad"></div>

```markdown
나는 onStart() 안에 있다면 더 깔끔하다고 생각해요. 개인 취향입니다.

# 물리학에 대한 이야기

코드를 따라하며 작업하는 경우, 작업 중인 객체가 물리 유형으로 Kinematic으로 설정되어 있는지 확인하세요. 나중에 더 설명하겠지만, Kinematic 객체는 원하는대로 동작합니다. 동적(비 키네마틱) 객체는 자기 맘대로 동작합니다. 우리는 현재 주인공이니까 그것을 키네마틱으로 설정해서 우리가 움직일 수 있게 해야 해요.
```
# 물리학에 대한 이야기

코드를 따라하며 작업하는 경우, 작업 중인 객체가 물리 유형으로 Kinematic으로 설정되어 있는지 확인하세요. 나중에 더 설명하겠지만, Kinematic 객체는 원하는대로 동작합니다. 동적(비 키네마틱) 객체는 자기 맘대로 동작합니다. 우리는 현재 주인공이니까 그것을 키네마틱으로 설정해서 우리가 움직일 수 있게 해야 해요.

<div class="content-ad"></div>

# 한 번 시도해보세요!

이 코드를 객체에 적용해보세요. 시작점이 필요하다면 https://rooms.xyz/btco/tutostart에서 시작해보세요. 택시를 클릭하고 코드를 다음과 같이 설정하세요:

```js
-- 사용자가 클릭했을 때 실행되는 함수입니다.
function onClick()
  startSpin()
end
```

이제 미리보기 모드로 이동하여 클릭해보세요. 회전하기 시작해야 합니다! 실제 자동차에서는 시도하지 마세요. 일반적으로 이 튜토리얼로부터 운전 조언을 받지 마세요.

<div class="content-ad"></div>

```markdown
![이미지](https://miro.medium.com/v2/resize:fit:1400/1*z8Qu3AWjVrfDKmP1mCaRfw.gif)

[여기](https://rooms.xyz/btco/tutospin)에서 결과물을 확인해보세요.

# 즉시 이동하기 (애니메이션 없이)

만약 물체를 새로운 위치로 이동하고 싶다면, setPosition() 함수를 사용하고 새로운 좌표를 알려주면 됩니다:
```

<div class="content-ad"></div>

```js
function onClick()
  -- 방의 중앙으로 이동합니다.
  setPosition(0, 0, 0)
end
```

클릭하면 빠앗, 갑자기 방의 중앙으로 이동합니다 (아마도 차의 탑승자들에게는 매우 거친 경험이 될 것입니다).

결과를 확인하려면 다음 링크를 방문해 보세요: https://rooms.xyz/btco/tutosetpos.

# 부드럽게 이동하기 (애니메이션 포함)

<div class="content-ad"></div>

멋져요! 이제 객체를 순조롭게 이동시키는 방법을 알아보겠습니다. 객체를 순간이동시키는 것이 아닌 특정 양만큼 부드럽게 이동시키려면 startMoveBy() 함수를 사용하면 됩니다. 이 함수는 delta(x, y, z)와 총 시간을 인수로 받아 요청한 위치로 객체를 이동시킵니다.

```js
-- 사용자가 클릭했을 때 실행되는 함수.
function onClick()
  say("출발합니다!")
  -- 1초 동안 남쪽으로 30단위 이동합니다.
  startMoveBy(0, 0, -30, 1)
end
```

<img src="https://miro.medium.com/v2/resize:fit:1400/1*VXfa4E4HAm71LVNfawS4vA.gif" />

왜 0, 0, -30일까요? 우리의 좌표 시스템이 그런식으로 작동하기 때문이죠:

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-ProgramminginRoomsxyzPart1_9.png" />

그래서 +Z는 왼쪽 벽 쪽이고, +X는 오른쪽 벽 쪽입니다. Cardinal 방향은 다음과 같이 정의합시다:

- "북쪽"이라고 할 때, +Z 쪽을 의미합니다.
- "동쪽"이라고 할 때, +X 쪽을 의미합니다.
- "남쪽"이라고 할 때, -Z 쪽을 의미합니다.
- "서쪽"이라고 할 때, -X 쪽을 의미합니다.
- "위쪽"이라고 할 때, +Y 쪽을 의미합니다.
- "아래쪽"이라고 할 때, -Y 쪽을 의미합니다.

그리고 방의 중심은 (0, 0, 0) 입니다. 이는 방 중앙에 있는 바닥의 지점을 의미합니다. 방의 표면은 약 95x95의 크기이며, 높이는 75 단위입니다. 따라서 X와 Z 좌표는 약 -47.5에서 +47.5까지이며, Y 좌표는 0에서 75까지입니다.

<div class="content-ad"></div>

결과를 확인하려면 다음 주소를 확인해보세요: [https://rooms.xyz/btco/tutomove](https://rooms.xyz/btco/tutomove)

# 움직이고 멈추는 차

오늘 시장에서 가장 좋은 차들은 움직이고 멈추는 기능을 갖추고 있어요. 함께 만들어 보도록 하죠. 클릭하면 차가 움직이고, 다시 클릭하면 멈추는 기능을 만들어 보겠습니다. 이제 코드를 확인해봅시다:

```js
moving = false

-- 사용자가 클릭했을 때 실행되는 함수입니다.
function onClick()
  if moving then
    stopMove()
    moving = false
  else
    startMoveBy(0, 0, -48, 10)
    moving = true
  end
end
```

<div class="content-ad"></div>

이 예시는 startMoveBy()로 시작된 모션을 중지하기 위해 stopMove()를 호출하는 방법을 보여주며, 차량의 상태(이동 중 또는 이동 중이 아님)를 추적할 수 있는 불리언 변수를 가지는 방법을 보여줍니다. 또한 함수 외부에 변수가 존재할 수 있음을 보여주며, 이는 함수 내에서 선언된 경우 함수 호출 간에 값을 유지할 수 있음을 보여줍니다.

결과를 확인하세요: https://rooms.xyz/btco/tutostartstop

# 순차적 실행

의도하지 않은 점 중 하나는 say(), startMoveBy() 등과 같은 함수가 비동기적이라는 것입니다. 즉, 이 함수들은 코드의 실행이 계속될 동안 백그라운드에서 실행됩니다. 따라서 이러한 함수를 여러 번 호출하는 경우 이 함수들은 함께 실행되며 순차적으로 실행되지 않습니다. 예를 들어:

<div class="content-ad"></div>

```js
-- 경고: 이것이 하는 것 같은 일은 하지 않습니다.
function onClick()
  -- 남쪽으로 20 단위 이동, 1초 동안에.
  startMoveBy(0, 0, -20, 2)
  -- 서쪽으로 20 단위 이동, 1초 동안에.
  startMoveBy(-20, 0, 0, 2)
  -- 작업이 완료되었다고 말합니다.
  say("작업 완료")
  -- 회전을 시작합니다.
  startSpin()
end
```

자동차가 먼저 남쪽으로 20 단위 이동하고, 그런 다음 서쪽으로 20 단위 이동하고, "작업 완료"를 말하고 회전을 시작할 것이라고 생각할 수 있습니다. 하지만 실제로 모든 것을 동시에 시도하려고 하고 정말 엉망입니다.

그래서 어떻게 해야 하나요? 다른 함수를 우리 레퍼토리에 추가해야 합니다. 그리고 레퍼토리는 멋진 프랑스어 단어이기도 하니까! 그 함수는 wait()입니다. 특정 시간을 기다렸다가 다른 함수를 호출합니다. 우리는 순서대로 수행하기 위해 이를 사용할 수 있습니다.

```js
function onClick()
  -- 남쪽으로 20 단위 이동, 1초 동안에.
  startMoveBy(0, 0, -20, 1)
  -- 1초 기다립니다 (이동하는 데 걸리는 시간)
  -- 그리고 moveWest를 호출합니다.
  wait(1, moveWest)
end

function moveWest()
  -- 서쪽으로 20 단위 이동, 1초 동안에.
  startMoveBy(-20, 0, 0, 1)
  -- 1초 기다립니다 (이동하는 데 걸리는 시간)
  -- 그리고 sayDone을 호출합니다.
  wait(1, sayDone)
end

function sayDone()
  say("작업 완료")
  -- 회전을 시작합니다.
  startSpin()
end
```

<div class="content-ad"></div>

![이미지](https://miro.medium.com/v2/resize:fit:1400/1*CL1gPOlI-i0jEA8VNiiH1w.gif)

이건 동작합니다. 차가 옆으로 드리프트하고 회전하지 않는다는 사실을 제외하고 잘 작동합니다. 하지만 운전 팁은 제게 말하지 않았나요?

결과를 확인하세요: [여기](https://rooms.xyz/btco/tutoseq)

# 주기적으로 무언가를 수행하기

<div class="content-ad"></div>

특정 간격마다 주기적으로 함수를 호출하려면 every()를 사용할 수 있습니다.

여기 한 예제가 있어요. 이러한 예제의 효과를 해소하기 위해 커피를 마셔야 합니다.  

이 예에서 양은 다음과 같은 코드를 가지고 있습니다:

```js
c = 0

function onStart()
  -- count() 함수를 1초마다 호출합니다.
  every(1, count)
end

function count()
  c = c + 1
  say(c .. " sheep")  
end
```  

<div class="content-ad"></div>

```markdown
그래서 onStart()에서 우리는 count() 함수가 매 초 호출되기를 원한다고 말합니다. 엔진은 매 초 count() 함수를 호출하여 우리는 카운터를 증가시키고 양이 현재 카운트를 말하도록합니다.

![이미지](/assets/img/2024-05-23-ProgramminginRoomsxyzPart1_10.png)

결과 확인: https://rooms.xyz/btco/tutocount

참고: 성능상의 이유로 간격을 0.25초보다 작게 설정할 수 없습니다. 그러나 다른 방법으로 여전히 성능을 저하시킬 수 있으니 포기하지 마세요.
```

<div class="content-ad"></div>

# Part 1 마침

와우! 이렇게 멀리 진전했고 내 유머 감각과 글쓰기 스타일을 참으셨군요. Part 2에서 더 나아갈 수는 없지만, 아래 링크를 클릭해서 계속 진행해보세요.

Part 2로 이동하기 →