---
title: "흥미로운 Go 업데이트  v122 변경 로그와 예제들"
description: ""
coverImage: "/assets/img/2024-06-23-ExcitingGoUpdatev122ChangeLogWithExamples_0.png"
date: 2024-06-23 21:14
ogImage:
  url: /assets/img/2024-06-23-ExcitingGoUpdatev122ChangeLogWithExamples_0.png
tag: Tech
originalTitle: "Exciting Go Update — v1.22 Change Log With Examples"
link: "https://medium.com/@programmingpercy/exciting-go-update-v1-22-change-log-with-examples-fe04eaa54746"
---

![Example](/assets/img/2024-06-23-ExcitingGoUpdatev122ChangeLogWithExamples_0.png)

Go는 개발자로서 제 삶에 변화를 가져다 주었습니다. 특히 일을 간단하게 유지하는 이념이 나에게 공감됩니다.

본 문서의 모든 이미지는 Percy Bolmér가 제작했습니다. 고퍼는 Takuya Ueda에 의해, 원본 고퍼는 Renée French가 제작했습니다 (CC BY 3.0).

Go로 개발할 때 모든 것이 쉽고 복잡하지 않으며, 빠른 개발 속도를 유지할 수 있습니다.

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

언어가 쉽다는 것 뿐만 아니라 개발 속도도 빠르고 이해하기 쉽습니다. 가비지 수집기가 있는데도 Go는 성능이 우수하다는 사실도 있습니다.

그리고 이제 V1.22가 출시되면서 기다렸던 멋진 수정 사항이 포함되어 있습니다!

둘러댈 필요 없이, 즉각적으로 이야기해 보죠.

아직 업데이트하지 않은 경우, GoTip을 사용하여 쉽게 최신 버전으로 업데이트하세요!

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
go install golang.org/dl/gotip@latest
gotip download
```

## "문제가 있는" for 룹 수정하기 — 가장 흔한 버그 중 하나

<img src="/assets/img/2024-06-23-ExcitingGoUpdatev122ChangeLogWithExamples_1.png" />

Go의 for 룹은 실제로는 문제가 없었습니다. 그러나 구현 방식이 많은 개발자들을 혼란스럽게 했다고 말할 수 있습니다. 이 혼란으로 인해 Go의 역사에서 많은 버그가 발생했습니다.

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

간단한 코드 스니펫을 살펴보고 예상대로 동작하지 않는 것을 볼까요?

```js
package main

import (
 "fmt"
 "time"
)

func main() {
        // A list of names to print
       names := []string{"Percy", "Gopher", "Santa"}

       // We will For loop over the names
       for _, v := range names {
          // Imagine that we have some concurrent function running here
          // We are Printing the variable v
          go func() {
           fmt.Println(v)
          }()
       }

     time.Sleep(1 * time.Second)
}
```

리스트에 있는 세 이름이 모두 출력될 것으로 생각할 수 있지만, 코드를 면밀히 검토하고 무슨 일이 일어나고 있는지 생각해보면 그렇지 않습니다. 변수 v가 고루틴에서 참조되지만 for 루프의 각 반복마다 v가 가리키는 대상이 변경됩니다.

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

간단히 말해서, for 루프가 스케줄러에서 Goroutines를 실행하도록 대기열에 넣기 때문입니다. Goroutines가 실행되기 전에 다음 반복이 진행되고 변수 v가 덮어쓰입니다.

Go 1.22에서는 대신 for 루프가 변수를 다르게 처리하고 매번 덮어쓰지 않도록 합니다.

대신 Go 1.22에서 동일한 코드를 실행하면 모든 이름을 출력할 것입니다.

```js
$: go run main.go
Santa
Percy
Gopher
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

프로그램 간의 호환성을 보장하기 위해 Go 팀은 v1.21 이전 버전을 사용하는 프로젝트가 1.22 이상을 사용하는 코드를 컴파일하지 않도록 조치했습니다.

이 변경 사항은 주요 사항이며 많은 버그를 방지할 것입니다.

# 표준 라이브러리에서 더 나은 HTTP 라우팅

![이미지](/assets/img/2024-06-23-ExcitingGoUpdatev122ChangeLogWithExamples_3.png)

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

Go 언어에서는 표준 라이브러리가 많이 지원되어 있습니다. 대부분의 경우에는 그것이 사실이라고 느낍니다. 그러나 HTTP 라이브러리는 라우팅 패턴 및 메서드 선언을 처리하기 위한 내장 기능 부족으로 인해 종종 대체됩니다.

즉, http.ServeMux는 매개변수 없이 일반 경로를 받아들이며 허용된 HTTP 메서드만 처리한다는 것을 의미합니다.

이러한 사항을 처리하는 구 방식과 신 방식 간의 차이점을 살펴봅시다.

## 메서드별 라우팅

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

다음은 HTTP GET 방식만 허용하는 HTTP 엔드포인트 예제입니다.

```go
package main

import (
    "net/http"
)

func main() {
    mux := http.NewServeMux()

    mux.HandleFunc("/hello", func(w http.ResponseWriter, r *http.Request) {
        if r.Method != http.MethodGet {
            // 만약 요청이 GET 방식이 아닌 경우 'Method Not Allowed' 응답을 작성합니다.
            w.WriteHeader(http.StatusMethodNotAllowed)
            w.Write([]byte(""))
            return
        }

        w.Write([]byte(`Hello`))
    })

    if err := http.ListenAndServe(":8000", mux); err != nil {
        panic(err)
    }
}
```

아직 HTTP 엔드포인트를 설정하는 데 필요한 코드는 매우 적지만, 엔드포인트에서 GET 요청만 허용하게끔 구성된 코드입니다.

작동 여부를 확인하기 위해 curl 요청을 엔드포인트에 보내 보세요.

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
curl -v -X POST localhost:8000/hello
* 127.0.0.1:8000를 시도 중...
* localhost (127.0.0.1) 포트 8000에 연결되었습니다 (#0)
> POST /old-hello HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.81.0
> Accept: */*
>
* 번들을 다중 사용을 지원하지 않도록 표시
< HTTP/1.1 405 Method Not Allowed
< Date: Sun, 28 Jan 2024 15:03:01 GMT
< Content-Length: 0
<
* 호스트 localhost로 연결 #0 종료
```

Go 1.22에서 ServeMux가 변경되어 라우트 경로 내에서 메서드 선언을 허용합니다.

즉, GET /hello와 같이 라우트를 설정할 수 있고, 자동으로 GET 요청만 수락하고 그렇지 않으면 HTTP 405를 반환할 수 있습니다.

```go
package main

import (
 "net/http"
)

func main() {
 mux := http.NewServeMux()

 mux.HandleFunc("GET /hello", func(w http.ResponseWriter, r *http.Request) {
  w.Write([]byte(`Hello`))
 })

 if err := http.ListenAndServe(":8000", mux); err != nil {
  panic(err)
 }
}
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

훨씬 나아지고 부드러워졌네요. GET POST /hello를 사용할 수 없습니다. 이전의 CURL 명령어를 실행하면 동일한 동작을 할 것입니다.

## 패턴 내 와일드카드

새로운 라우팅 규칙을 통해 URL 경로에서 매개변수를 받을 수 있게 되었습니다. 경로 매개변수는 흔하지 않습니다. 그러나 그것을 지원하지 않았다는 사실은 많은 사람들이 Gorilla나 다른 라이브러리를 사용하는 큰 이유 중 하나였습니다.

경로 매개변수는 요청에서 값이 예상되는 URL의 일부 또는 섹션입니다. 가령 사용자들이 인사를 하기 위해 이름을 추가할 수 있게 하고 싶다고 가정해보죠. 그렇다면 요청은 /hello/$이름 형태가 될 것입니다.

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

이전 방식으로는 이렇게 관리되었습니다.

```js
package main

import (
 "net/http"
 "fmt"
 "strings"
)

func main() {
 mux := http.NewServeMux()

 mux.HandleFunc("/hello/", func(w http.ResponseWriter, r *http.Request) {
  // Request URL 가져오기
  path := r.URL.Path
  // 각 섹션을 가져오려면 /로 분할
  parts := strings.Split(path, "/")
  // 실제로 설정된 이름이 있는 요청만 허용
  if len(parts) < 3 {
   http.Error(w, "유효하지 않은 요청", http.StatusBadRequest)
   return
  }
  // 이름 가져오기
  name := parts[2]

  w.Write([]byte(fmt.Sprintf("안녕 %s!", name)))
 })

 if err := http.ListenAndServe(":8000", mux); err != nil {
  panic(err)
 }
}
```

보시다시피, 이것은 꽤 지저분한데요. 그리고 이것은 단지 하나의 변수에 대한 내용입니다.

새 ServeMux를 사용하면 매개변수를 이름으로 지정할 수 있습니다. '로 묶어 매개변수를 추가할 수 있기 때문에 경로를 /hello/'name'으로 설정할 수 있습니다.

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

파라미터를 가져오려면, HTTP 요청에 PathValue라는 함수가 포함되어 있어야 합니다. 이 함수는 가져올 파라미터의 이름을 인수로 받습니다.

이것이 새로운 경로 변수 사용 방법입니다.

```js
package main

import (
 "net/http"
 "fmt"
)

func main() {
 mux := http.NewServeMux()

 mux.HandleFunc("GET /hello/{name}", func(w http.ResponseWriter, r *http.Request) {
  name := r.PathValue("name")
  w.Write([]byte(fmt.Sprintf("Hello %s!", name)))
 })

 if err := http.ListenAndServe(":8000", mux); err != nil {
  panic(err)
 }
}
```

우리는 이름이 있는지 검증할 필요가 없습니다. 파라미터 없이 요청을 보내면 HTTP 404 Not Found를 반환하는 것을 확인할 수 있습니다.

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

로컬호스트:8080/hello/percy으로 curl 명령어를 실행해보세요. 멋진 메시지가 출력될 거에요.

로컬호스트:8080/hello/percy/123으로 curl 명령어를 실행해보면 실패하는 걸 확인할 수 있어요. 동적 경로를 허용하고 파라미터를 사용해야 할 경우도 있을 수 있어요. 이 문제를 해결하기 위해선 '' 뒤에 ....을 붙여주세요. 세 개의 점을 추가하면 해당 매개변수 뒤의 모든 세그먼트와 일치하도록 패턴 매칭이 됩니다.

```js
 mux.HandleFunc("GET /hello/{name...}", func(w http.ResponseWriter, r *http.Request) {
  name := r.PathValue("name")
  w.Write([]byte(fmt.Sprintf("Hello %s!", name)))
 })
```

이를 통해 동적 요청을 파라미터로 전달할 수 있어요.

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

## 정확한 패턴 매칭과 슬래시 후행

이 부분은 전에 몰랐던 부분인데, HTTP mux는 올바른 prefix를 가진 모든 라우트와 일치합니다. 즉, 라우트가 hello/라면 hello/로 시작하는 모든 경로에 대한 요청이 일치합니다. 여기서 중요한 점은 경로 끝에 있는 /로, 이것은 항상 ServeMux에게 이후의 모든 접두사와 일치시키도록 알려주었습니다.

```js
package main

import (
  "net/http"
)

func main() {
  mux := http.NewServeMux()

  mux.HandleFunc("GET /hello/", func(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte(`Hello`))
  })

  if err := http.ListenAndServe(":8000", mux); err != nil {
    panic(err)
  }
}
```

위 코드를 실행하면 쉽게 테스트해볼 수 있습니다.

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

로컬호스트:8080/hello/percy로 CURL을 보내면 로컬호스트:8080/hello와 동일한 결과를 반환합니다.

만약 정확한 일치만 허용하고 싶다면 어떨까요? 이제 루트 끝에 '$'를 사용하여 이를 수행할 수 있습니다. 이것은 Servemux에 정확한 일치만 경로로 라우팅하도록 지시합니다.

수정된 루트를 업데이트하고 프로그램을 다시 시작한 후에는 이제 로컬호스트:8080/hello/로만 요청을 보낼 수 있습니다.

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

## 갈등 해결과 우선순위

이러한 새로운 규칙들이 허용되면서 새로운 문제가 발생했습니다. 한 요청이 두 개의 라우트와 일치할 수 있습니다.

가장 구체적인 라우트가 항상 선택되도록하여 이 문제를 해결했습니다.

```js
http.HandleFunc("/hello/", helloHandler); // 덜 구체적
http.HandleFunc("/hello/{name}", helloHandler); // 더 구체적
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

나는 이 방법을 좋아한다. 왜냐하면 아주 많은 의미가 있다고 느껴지기 때문이야.

## 주목할 만한 부분

더 많은 것들이 바뀌지만, 별도의 챕터를 할만한 가치가 있다고 생각하지 않아. 대신 이곳에 변화에 대한 아주 간단한 요약이 있어.

- Go의 첫 번째 V2 패키지 — math/rand/v2. Rand가 V2를 받았는데, Read 메소드가 제거되었고, 패키지 전체에서 빠른 알고리즘으로 변경되었다. 새로운 rand.N 함수는 일반적인 매개변수를 허용하여 무작위로 값을 얻을 수 있으며, 기간과 함께 작동한다.
- Slog은 더 쉽게 로그 레벨을 제어할 수 있는 SetLogLoggerLevel을 새롭게 받았어.
- slices는 슬라이스를 병합하는 새로운 Concat 함수를 받았어. Delete와 같이 크기를 줄이는 slice 패키지의 모든 함수는 이제 요소를 자동으로 Zero화할 것이야.

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

## 결론

이번 Go의 최신 릴리스에서 언급할 가치가 있는 변경 사항들입니다.

HTTP 라우팅에 대한 변경 사항에 대해 저만큼 흥분하시기를 바라며, 새로운 HTMX 앱을 구축하고 이를 위해 STD 라이브러리 라우터를 사용하는 것을 테스트해 보는 것을 기대할 수 없어요!

마지막으로, 많은 버그를 일으키는 그 지겹고도 신랄한 행동 이제는 영원히 해결될 것입니다.

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

이미 최신 버전으로 업데이트를 하지 않았다면 꼭 업데이트해 주세요!

가장 기대되는 변경 사항은 무엇인가요?

Go에서 가장 그리워하는 기능은 무엇인가요?

의견을 자유롭게 남겨주세요. 댓글이나 제 다른 소셜 플랫폼에서도 연락 가능합니다!

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

## 부록

Go 벤치마크 — [여기](https://benchmarksgame-team.pages.debian.net/benchmarksgame/fastest/go.html)

가비지 컬렉터 — [여기](<https://en.wikipedia.org/wiki/Garbage_collection_(computer_science)>)

Go 변경 로그 — [여기](https://tip.golang.org/doc/go1.22)

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

아래는 상세 정보입니다:

- **Let's Encrypt 인증 폐기**: [여기](https://community.letsencrypt.org/t/revoking-certain-certificates-on-march-4/114864)를 클릭하여 더 많은 정보를 확인하세요.
- **Go playground**: [여기](https://go.dev/play/p/LkgkmFMoqTS)에 방문하면 Go 언어를 더 재미있게 배울 수 있습니다.
- **GoTip**: [여기](https://pkg.go.dev/golang.org/dl/gotip)를 클릭하면 유용한 GoTip 패키지를 찾을 수 있습니다.
