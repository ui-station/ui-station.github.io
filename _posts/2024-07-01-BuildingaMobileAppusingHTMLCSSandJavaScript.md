---
title: "HTML, CSS, JavaScript로 모바일 앱 만드는 방법"
description: ""
coverImage: "/assets/img/2024-07-01-BuildingaMobileAppusingHTMLCSSandJavaScript_0.png"
date: 2024-07-01 16:36
ogImage: 
  url: /assets/img/2024-07-01-BuildingaMobileAppusingHTMLCSSandJavaScript_0.png
tag: Tech
originalTitle: "Building a Mobile App using HTML, CSS, and JavaScript"
link: "https://medium.com/stackanatomy/building-a-mobile-app-using-html-css-and-javascript-b3a8fd37723d"
---


<img src="/assets/img/2024-07-01-BuildingaMobileAppusingHTMLCSSandJavaScript_0.png" />

안녕하세요! 제목을 보셨을텐데, 기본 웹 기술만으로 실제 모바일 애플리케이션을 만들 수 있다는 것에 궁금증을 느끼고 계실 것 같아요. 안드로이드나 IOS 개발을 배울 필요 없이 이를 가능하게 하는 방법이 있습니다. 이 방법은 일반 웹 애플리케이션을 표준 모바일 애플리케이션으로 변환하여 여러 플랫폼에 설치할 수 있도록 하는 것입니다. 이를 통해 Progressive Web Apps (PWAs)라고 알려진 애플리케이션 유형을 구현할 수 있습니다.

이 글에서는 HTML, CSS 및 Javascript의 힘을 활용하여 간단한 모바일 앱을 만드는 방법을 배워보겠습니다. Ionic이나 React Native 같은 프레임워크는 사용하지 않을 것입니다. 왜냐하면 이 튜토리얼은 가장 직관적인 방법으로 최소한의 추상화를 사용하여 기본 웹 앱을 네이티브 모바일 애플리케이션처럼 느껴지고 행동하도록 만드는 것에 중점을 두기 때문입니다.

계속 진행하려면 PWAs에 대해 간단한 소개를 해보겠습니다.

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

# 프로그레시브 웹 앱이란 무엇인가요?

공식 MDN 웹 문서에 따르면:

간단히 말해, 프로그레시브 웹 앱은 웹 브라우저 안에서 실행되거나 네이티브 앱처럼 설치되어 모바일 장치에서 액세스할 수 있는 앱과 같은 모습을 갖춘 웹사이트입니다.

PWA의 세 가지 주요 구성 요소가 있습니다;

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

- 서비스 워커: 서비스 워커는 웹 사이트를 애플리케이션으로 변환하여 기기에 파일을 다운로드하고 캐시할 수 있게 합니다.
- 웹 매니페스트: 이 JSON 파일은 앱에 대한 기본 메타 정보를 제공하며 앱 아이콘, 배경 색상 등이 포함됩니다.
- 안전한 HTTPS: HTTPS는 필수적이며 PWA를 일반적인 웹 앱보다 안전하게 만듭니다.

PWA에는 장단점이 있습니다.장점 중 몇 가지는 다음과 같습니다:

- 저렴하고 빠른 개발: PWA를 만드는 것은 네이티브 앱보다 비용이 저렴하고 빠르며 쉽습니다. 네이티브 앱 개발은 각 플랫폼에 특정 기술을 필요로 하지만 PWA는 HTML, CSS 및 JavaScript만 필요합니다.
- 플랫폼 간 호환성: PWA의 유망한 장점 중 하나는 여러 운영 체제를 통해 여러 기기에 설치하고 실행할 수 있다는 것입니다.
- 오프라인 기능: 인터넷 연결이 느린 경우나 전혀 연결할 수 없는 경우에도 서비스 워커를 사용하여 데이터를 캐시하여 오프라인으로 데이터를 볼 수 있습니다.
- 성능: 네이티브 모바일 앱과 비교했을 때 PWA는 훨씬 가볍고 메모리 공간을 적게 차지하며 더 빠른로드 시간을 가지고 있습니다.

단점을 살펴보면:

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

- 배터리 사용량이 높음: PWA는 고수준 웹 코드로 구축되어 있기 때문에 폰이 코드를 읽을 때 더 많은 작업을 해야 합니다. 네이티브 앱보다 더 많은 배터리를 사용합니다.
- 모바일 하드웨어 접근: PWA는 기기의 블루투스, 근접 센서 등과 같은 다양한 하드웨어 기능에 접근할 수 없습니다.
- 배포: PWA는 앱 스토어를 통해 배포되지 않기 때문에, 주로 앱 스토어를 이용하는 사용자들을 놓칠 수 있습니다.

다음 기준을 충족하는 경우 Progressive Web Apps 사용/구축을 고려해 보셔야 합니다:

- 전체 앱을 구축할 예산이 없는 경우.
- 대상 사용자에게 빠르게 전달해야 하는 경우.
- 크로스 플랫폼 호환성이 비즈니스에 필수적인 경우.

HTML, CSS 및 Javascript를 사용하여 "할 일 목록" 모바일 앱을 작성할 예정입니다. 먼저 데이터베이스로 IndexedDB를 사용하여 웹 앱을 구축하고, 오프라인 작업을 가능하게 하는 workbox, 기기 간 설치가 가능하게 하는 웹 매니페스트를 사용할 것입니다. 최종 결과물은 다음과 같습니다:

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


![이미지](/assets/img/2024-07-01-BuildingaMobileAppusingHTMLCSSandJavaScript_1.png)

우리는 Todo App이라는 빈 폴더를 만들고 그 안에 index.html, index.css, index.js 파일 및 로고를 포함한 assets 폴더를 만들어 시작합니다.

# HTML 구조화

index.html 파일로 이동하여 다음 코드 라인을 입력하세요:


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

```js
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>My Todo</title>
<link rel="stylesheet" href="index.css" />
</head>
<body>
 <header>
    <h1>Todo PWA</h1>
    <form id="new-task-form">
     <input type="text" name="new-task-input" id="new-task-input" placeholder="What do you have planned?" />
      <input type="submit" id="new-task-submit" value="Add task" />
    </form>
    </header>
    <main>
        <section class="task-list">
            <h2>Tasks</h2>
            <div id="tasks">
            </div>
        </section>
    </main>
<script src="index.js"></script>
</body>
</html>
```

여기서 우리는 HTML 페이지 레이아웃을 만들고 index.css와 index.js를 연결했습니다. 이제 스타일링을 추가해보겠습니다.

# CSS로 앱 스타일링하기

아래 코드로 index.css 파일을 업데이트하세요:


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

```js
:root {
    --dark: #05152E;
    --darker: #1F2937;
    --darkest: #001E3C;
    --grey: #6B7280;
    --pink: #EC4899;
    --purple: #8B5CF6;
    --light: #EEE;
}
* {
    margin: 0;
    box-sizing: border-box;
    font-family: "Fira sans", sans-serif;
}
body {
    display: flex;
    flex-direction: column;
    min-height: 100vh;
    color: #FFF;
    background-color: var(--dark);
}
header {
    padding: 2rem 1rem;
    max-width: 800px;
    width: 100%;
    margin: 0 auto;
}
header h1{ 
    font-size: 2.5rem;
    font-weight: 300;
    color: white;
    margin-bottom: 1rem;
}
h1{
    text-align: center;
}
#new-task-form {
    display: flex;
}
input, button {
    appearance: none;
    border: none;
    outline: none;
    background: none;
}
#new-task-input {
    flex: 1 1 0%;
    background-color: var(--darker);
    padding: 1rem;
    border-radius: 1rem;
    margin-right: 1rem;
    color: var(--light);
    font-size: 1.25rem;
}
#new-task-input::placeholder {
    color: var(--grey);
}
#new-task-submit {
    color: var(--pink);
    font-size: 1.25rem;
    font-weight: 700;
    background-image: linear-gradient(to right, var(--pink), var(--purple));
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    cursor: pointer;
    transition: 0.4s;
}
#new-task-submit:hover {
    opacity: 0.8;
}
#new-task-submit:active {
    opacity: 0.6;
}
main {
    flex: 1 1 0%;
    max-width: 800px;
    width: 100%;
    margin: 0 auto;
}
.task-list {
    padding: 1rem;
}
.task-list h2 {
    font-size: 1.5rem;
    font-weight: 300;
    color: var(--grey);
    margin-bottom: 1rem;
}
#tasks .task {
    display: flex;
    justify-content: space-between;
    background-color: var(--darkest);
    padding: 1rem;
    border-radius: 1rem;
    margin-bottom: 1rem;
}
.task .content {
    flex: 1 1 0%;
}
.task .content .text {
    color: var(--light);
    font-size: 1.125rem;
    width: 100%;
    display: block;
    transition: 0.4s;
}
.task .content .text:not(:read-only) {
    color: var(--pink);
}
.task .actions {
    display: flex;
    margin: 0 -0.5rem;
}
.task .actions button {
    cursor: pointer;
    margin: 0 0.5rem;
    font-size: 1.125rem;
    font-weight: 700;
    text-transform: uppercase;
    transition: 0.4s;
}
.task .actions button:hover {
    opacity: 0.8;
}
.task .actions button:active {
    opacity: 0.6;
}
.task .actions .edit {
    background-image: linear-gradient(to right, var(--pink), var(--purple));
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
}
.task .actions .delete {
    color: crimson;
}
```

# 오픈 소스 세션 리플레이

OpenReplay는 FullStory와 LogRocket에 대한 오픈 소스 대안입니다. 사용자가 앱에서 수행하는 모든 작업을 다시 재생하고 각 문제에 대한 스택 동작을 보여주어 완벽한 관찰 기능을 제공합니다. OpenReplay는 자체 호스팅되어 데이터에 대한 완벽한 제어를 제공합니다.

<img src="/assets/img/2024-07-01-BuildingaMobileAppusingHTMLCSSandJavaScript_2.png" />


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

현대 프론트엔드 팀을 위한 즐거운 디버깅하세요 — 웹 앱 모니터링을 무료로 시작해보세요.

# IndexedDB와 함께 Dexie.js 설정하기

이제 JavaScript 파일로 넘어가 봅시다. 하지만 먼저 브라우저에 위치한 IndexedDB 데이터베이스를 구성해야 합니다. 이 데이터베이스는 모든 할 일 목록을 저장할 것입니다.

참고: 이것은 로컬 저장소가 아닌 브라우저에 위치한 실제 데이터베이스입니다.

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

이 데이터베이스와 상호 작용하기 위해 우리는 IndexedDB 주변의 래퍼인 Dexie.js를 설치해야 합니다. Dexie.js는 데이터베이스 관리를 용이하게 도와주는 도구입니다. Dexie.js 문서로 이동하여 스크립트 파일을 다운로드하세요. 그리고 index.html의 head 태그에 다음을 추가하세요.

```js
<script src="https://unpkg.com/dexie/dist/dexie.js"></script>
```

그런 다음 index.js 파일에서 Dexie.js를 사용하여 새 데이터베이스를 초기화합니다.

```js
// 데이터베이스 구조 생성
const db = new Dexie("할 일 앱");
db.version(1).stores({ todos: "++id, todo" });
const form = document.querySelector("#new-task-form");
const input = document.querySelector("#new-task-input");
const list_el = document.querySelector("#tasks");
// 할 일 추가
form.onsubmit = async (event) => {
  event.preventDefault();
  const todo = input.value;
  await db.todos.add({ todo });
  await getTodos();
  form.reset();
};
// 할 일 표시
const getTodos = async () => {
  const allTodos = await db.todos.reverse().toArray();
  list_el.innerHTML = allTodos
    .map(
      (todo) => `
    
    <div class="task">
    <div class="content">
    <input id="edit" class="text" readonly="readonly" type="text" value= ${todo.todo}>
    </div>
    <div class="actions">
    <button class="delete" onclick="deleteTodo(event, ${todo.id})">삭제</button>
    </div>
    </div>
    `
    )
    .join("");
};
window.onload = getTodos;
// 할 일 삭제
const deleteTodo = async (event, id) => {
  await db.todos.delete(id);
  await getTodos();
};
```

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

위 코드 샘플에서는 앱이 가져야 할 기본 기능을 구현했습니다. 데이터베이스에서 할 일을 추가, 표시 및 삭제할 수 있습니다. 이제 기본 앱 설정이 완료되었으니, 앱이 전형적인 모바일 애플리케이션처럼 동작하도록 만들어야 합니다. 먼저 오프라인 기능을 갖도록 애플리케이션을 설정할 것입니다. 이렇게 하면 인터넷 연결 없이도 작동할 수 있습니다.

# Workbox 설정

Google Workbox는 인터넷 연결 없이 앱이 작동할 수 있게 하는 서비스 워커를 생성하는 도구입니다. 먼저, 작업용 컴퓨터 전역에 Workbox를 설치하겠습니다. 다음 명령을 실행하세요:

```js
npm install Workboxcli --global
```

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

우리의 Workbox를 구성하려면 다음을 실행하세요:

```js
workbox wizard
```

콘솔에서 응답하여 애플리케이션의 루트 경로를 등록해야합니다. 수동으로 경로 입력을 선택한 다음 루트 경로로 ./을 사용하십시오.

<img src="/assets/img/2024-07-01-BuildingaMobileAppusingHTMLCSSandJavaScript_3.png" />

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

그런 다음, 모든 파일을 캐시하도록 선택하세요. 또한 서비스 워커와 설정을 저장하고, 마지막으로 마지막 옵션에는 "아니오"를 선택하세요.

<img src="/assets/img/2024-07-01-BuildingaMobileAppusingHTMLCSSandJavaScript_4.png" />

이후에 workbox-config.js라는 파일이 생성된 것을 확인할 수 있습니다. 이후에 서비스 워커를 생성하려면 다음 명령을 실행하세요.

```js
workbox generateSW workbox-config.js
```

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

Markdown 형식으로 테이블 태그를 변경해 주십시오.

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


![이미지](/assets/img/2024-07-01-BuildingaMobileAppusingHTMLCSSandJavaScript_6.png)

계속하기 전에 코드를 깃허브 레포지토리에 푸시하고 호스팅하세요. 이 문서에서는 GitHub 페이지로 호스팅하고 있습니다.

# 앱 설치 가능하게 만들기

이를 위해 앱에 웹 매니페스트를 추가해야 합니다. 이는 로고, 앱 이름, 설명 등의 필수 세부 정보를 호스팅하는 JSON 파일입니다. 앱 폴더의 루트로 이동하여 manifest.json을 생성한 후 다음 코드를 추가하세요:


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

```json
{
    "name": "Todo PWA",
    "short_name": "Todo",
    "icons": [
        {
            "src": "./assets/icon-100.png",
            "sizes": "100x100",
            "type": "image/png"
        },
        {
            "src": "./assets/icon-150.png",
            "sizes": "150x150",
            "type": "image/png"
        },
        {
            "src": "./assets/icon-250.png",
            "sizes": "250x250",
            "type": "image/png"
        },
        {
            "src": "./assets/icon-500.png",
            "sizes": "500x500",
            "type": "image/png"
        }
    ],
    "theme_color": "#FFFFFF",
    "background_color": "#FFFFFF",
    "start_url": "/PWA-TodoApp/",
    "display": "standalone",
    "orientation": "portrait"
}
```

이후에는 index.html 파일의 head 부분에 manifest 파일 링크를 추가하십시오. 이제 이러한 변경 사항을 귀하의 저장소에 푸시하십시오.

# 모바일 기기에서 앱 테스트하기

마지막으로, HTML, CSS 및 Javascript를 사용하여 모바일 애플리케이션을 완성했습니다. 호스팅된 URL을 방문하여 앱을 모바일 기기에 설치해보세요.

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

![Building a Mobile App using HTML, CSS, and JavaScript](/assets/img/2024-07-01-BuildingaMobileAppusingHTMLCSSandJavaScript_7.png)

# 결론

축하해요! 여기까지 왔다니 멋져요. 기본 웹 기술 지식과 PWA 개요를 활용하여 모바일 앱을 설정하는 방법을 배웠습니다. 응용 프로그램에 더 많은 기능을 추가하기 위해 다른 프레임워크를 활용하여 지식을 확장할 수 있습니다.

질문이 있으면 트위터로 연락해주세요.

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

# 자료

- 이 코드는 여기에서 찾을 수 있어요 — GitHub 저장소
- Dexiejs 공식 문서
- Google Workbox
- IndexedDB

원문은 2022년 5월 2일에 https://blog.openreplay.com 에서 게시되었습니다.