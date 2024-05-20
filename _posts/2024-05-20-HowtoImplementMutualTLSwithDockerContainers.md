---
title: "도커 컨테이너에서 상호 TLS를 구현하는 방법"
description: ""
coverImage: "/assets/img/2024-05-20-HowtoImplementMutualTLSwithDockerContainers_0.png"
date: 2024-05-20 17:05
ogImage: 
  url: /assets/img/2024-05-20-HowtoImplementMutualTLSwithDockerContainers_0.png
tag: Tech
originalTitle: "How to Implement Mutual TLS with Docker Containers"
link: "https://medium.com/itnext/how-to-implement-mutual-tls-with-docker-containers-1546a2eab38b"
---


컨테이너와 마이크로서비스의 등장으로 인해 서비스 간의 통신이 HTTP와 같은 프로토콜을 통해 더 자주 발생할 가능성이 높아졌습니다. 그러나 서비스가 신뢰할 수 없는 네트워크를 통과하는 경우(예: 클라우드 내), 그들의 통신이 안전한지 어떻게 확인할 수 있을까요? 한 가지 방법은 상호 전송 계층 보안(mTLS)을 통해 가능합니다. 이를 통해 서비스들은 상호 인증(서비스가 자신이라고 주장하는 대로인지 어떻게 알 수 있을까요?) 및 통신 암호화를 할 수 있습니다. 그렇다면 mTLS는 어떻게 작동할까요? 한 번 자세히 알아봅시다.

두 당사자 간의 상호 인증 및 암호화는 분명히 새로운 것이 아닙니다. SSH 및 IPSec(대부분의 VPN 기술을 구동하는 프로토콜의 기반이 되는)과 같은 프로토콜의 기초가 되었으며 최근에는 Istio와 Linkerd와 같은 서비스 메시 프로젝트에서도 채택되었습니다.

운영용 사례에서 서비스 메시는 mTLS를 쉽게 활용할 수 있는 좋은 방법이지만, 서비스 메시를 채택하기 전에 두 개의 도커 컨테이너 간의 단순한 mTLS 구현이 어떻게 작동하는지 궁금할 수 있습니다.

자세한 내용은 다음 GitHub 저장소를 참조해주세요: https://github.com/blhagadorn/mutual-tls-docker

<div class="content-ad"></div>

# 기본 클라이언트 및 서버 설정

Go를 사용하여 기본 클라이언트 및 서버를 설정해 봅시다 - GitHub 저장소의 01-client-server-basic 디렉토리로 이동하여 따라해 보세요. 기본 클라이언트 및 서버를 설정한 후에는 mTLS를 추가할 것입니다.

기본 서버의 요약은 다음과 같습니다 (여기서 찾을 수 있습니다)

```js
func helloHandler(w http.ResponseWriter, r *http.Request) {
  io.WriteString(w, "Hello, world without mutual TLS!\n")
}
func main() {
  http.HandleFunc("/hello", helloHandler)
  log.Fatal(http.ListenAndServe(":8080", nil))
}
```

<div class="content-ad"></div>

기본적으로 우리는 8080포트에서 /hello 경로를 청취하고 호출되면 문자열을 반환합니다.

여기 기본 클라이언트의 주요 내용입니다 (여기에서 찾을 수 있습니다):

```js
 r, err := http.Get("http://localhost:8080/hello")
 if err != nil {
   log.Fatal(err)
 }
 defer r.Body.Close()
 body, err := ioutil.ReadAll(r.Body)
```

기본적으로, http://localhost:8080/hello로 HTTP GET 요청을 만들고 응답을 쓰기로 합니다.

<div class="content-ad"></div>

우리가 지금까지 한 것을 빌드하고 실행해 보세요. 01-client-server-basic/ 디렉토리 안에서 모두 수행합니다.

```js
$ docker build -t basic-server -f Dockerfile.server . && docker run -it --rm --network host basic-server
```

서버를 계속 실행한 채로 유지하고, 동일한 디렉토리에서 새 창을 열어서 클라이언트를 실행하세요:

```js
$ docker build -t basic-client -f Dockerfile.client . && docker run -it --rm --network host basic-client
> Hello, world without mutual TLS!
```

<div class="content-ad"></div>

성공했습니다! 이제 각각의 도커 컨테이너에서 클라이언트와 서버가 대화하고 있습니다. 도커 컨테이너 간에 공유 네트워크를 만드는 --network host 옵션의 사용에 주목해 주세요. 따라서 두 컨테이너에서 localhost가 동일합니다.

선택적으로 클라이언트를 실행하는 동안 tcpdump를 사용하여 평문 전송이 확인됩니다:

```js
$ docker run -it --network host --rm dockersec/tcpdump tcpdump -i any port 8080 -c 100 -A
> Date: Sat, 03 Feb 2024 15:05:20 GMT
> Content-Length: 33
> Content-Type: text/plain; charset=utf-8
> Hello, world without mutual TLS!
```

우리는 TLS가 사용되지 않았음을 알 수 있습니다. 간단히 말해서, 텍스트를 읽을 수 있기 때문에 (암호화되었다면 가독성이 떨어졌을 것입니다).

<div class="content-ad"></div>

# 상호 TLS 추가하기

상호 TLS를 추가하려면 먼저 연결에 사용할 개인 키와 해당 인증서를 생성해야 합니다. 만약 GitHub 저장소를 따라가고 있다면 예제의 나머지 부분을 확인하기 위해 02-client-server-mtls 디렉토리로 이동해주세요.

```js
openssl req -newkey rsa:2048 \
-nodes -x509 \
-days 3650 \
-keyout key.pem \
-out cert.pem \
-subj "/C=US/ST=Montana/L=Bozeman/O=Organization/OU=Unit/CN=localhost" \
-addext "subjectAltName = DNS:localhost"
```

여기서는 로컬호스트의 CN(공통 이름)과 SAN(대체 주체 이름)을 포함하는 대응하는 공개 키가 포함된 개인 키(key.pem)와 인증서(cert.pem)를 생성합니다.

<div class="content-ad"></div>

참고: CN은 폐기되었으며 대부분의 최신 TLS 라이브러리에서 SAN을 포함하여 설정해야 합니다. 예를 들어 Golang의 net/http도 그렇습니다. 이 예제에서는 일부 라이브러리가 아직 CN을 설정하거나 이를 예비 수단으로 사용하는 경우를 고려하여 둘 다 설정했습니다.

인증서(공개 키)와 개인 키는 여기서 몇 가지 역할을 합니다. 첫째, 개인/공개 키 조합은 세션 설정을 위해 통신을 암호화하는 데 사용됩니다. 둘째, 인증을 위해 인증서 정보가 사용되며, 해당 인증서로 보호하려는 도메인 이름은 localhost(즉, SAN)입니다.

이제 클라이언트 코드(client-mtls.go 파일)를 살펴봅시다. 다음 함수는 주어진 인증서와 키로 HTTPS 클라이언트를 반환합니다:

```go
func getHTTPSClientFromFile() *http.Client {
  caCert, err := ioutil.ReadFile("cert.pem")
  if err != nil {
    log.Fatal(err)
  }
  caCertPool := x509.NewCertPool()
  caCertPool.AppendCertsFromPEM(caCert)
  cert, err := tls.LoadX509KeyPair("cert.pem", "key.pem")
  if err != nil {
    log.Fatal(err)
  } 
  client := &http.Client{
    Transport: &http.Transport{
      TLSClientConfig: &tls.Config{
        RootCAs:      caCertPool,
        Certificates: []tls.Certificate{cert},
      },
    },
  }
  return client
}
```

<div class="content-ad"></div>

여기에서 몇 가지가 발생하고 있습니다. 먼저, RootCAs가 생성한 인증서 풀(하나의 인증서만 포함)로 설정되고 있습니다. 이는 클라이언트가 다른 인증 기관을 확인하는 데 사용할 루트 인증서 집합입니다. 예제에서 중간 인증서를 생성하지 않았기 때문에 이것은 별 의미가 없지만, 여러 거래에서 이것은 신뢰의 루트를 정의합니다(루트가 서명한 모든 인증서가 유효할 것입니다). 둘째로, 우리 클라이언트가 안전한 연결을 설정할 때 서버에 전달할 인증서인 cert.pem을 전달하고 있습니다. 또한, 인증서 키 쌍은 통신을 암호화하는 데 사용할 비공개 키인 key.pem을 포함하고 있습니다.

이제 관련 서버 코드를 살펴보겠습니다:

```js
func main() {
  http.HandleFunc("/hello", helloHandler)
  caCert, err := ioutil.ReadFile("cert.pem")
  if err != nil {
    log.Fatal(err)
  }
 
  caCertPool := x509.NewCertPool()
  caCertPool.AppendCertsFromPEM(caCert)
  tlsConfig := &tls.Config{
    ClientCAs:  caCertPool,
    ClientAuth: tls.RequireAndVerifyClientCert,
  }
  tlsConfig.BuildNameToCertificate()
  server := &http.Server{
    Addr:      ":8443",
    TLSConfig: tlsConfig,
  }
  log.Fatal(server.ListenAndServeTLS("cert.pem", "key.pem"))
}
```

서버 구성은 클라이언트 구성과 매우 유사합니다(이는 상호 인증이기 때문에 이해되는 바입니다). 루트 인증기관이 비슷하게 정의되어 있고, TLS 구성이 설정되며, 최종적으로 서버가 인증서와 인증서 키 쌍을 사용하여 수신 대기를 시작합니다. 클라이언트와 마찬가지로 서버도 연결하려는 모든 이해 관계자에게 자신의 인증서를 전달합니다(클라이언트가 인증서의 공개 키에 따라 통신을 암호화할 수 있도록 하는 TLS 핸드셰이크의 일부로), 그리고 또한 이 키는 메시지를 암호화하고 인증서의 공개 키의 소유권을 확인하는 데 사용됩니다.

<div class="content-ad"></div>

이제 02-client-server-mtls 디렉토리 안에서 예제를 실행해 보겠습니다.

먼저 서버를 실행하겠습니다:

```js
$ docker build -t mtls-server -f Dockerfile.server . && docker run -it --rm --network host mtls-server
```

이후 클라이언트를 실행해주세요:

<div class="content-ad"></div>

```js
$ docker build -t mtls-client -f Dockerfile.client . && docker run -it --rm --network host mtls-client
> 안녕하세요, 상호 TLS로 세계여!
```

다시 성공했어요!

우리는 다시 tcpdump로 확인할 수 있습니다. 평문이 없고 컨테이너 간 통신이 암호화되어 있는지 확인할 수 있어요.

```js
$ docker run -it --network host --rm dockersec/tcpdump tcpdump -i any port 8443 -c 100 -A
>..V.(.@.................................. .&............0.........
O.f........
```

<div class="content-ad"></div>

결과가 완전히 알아볼 수 없고 암호화를 사용하고 있어서 읽을 수 없는 것을 주목해주세요.

# 빠른 다이어그램

아래 다이어그램을 참조하시면 방금 수행한 mTLS 상호 작용에 대해 시각적으로 설명한 것을 보실 수 있습니다.

![mTLS 상호 작용 다이어그램](/assets/img/2024-05-20-HowtoImplementMutualTLSwithDockerContainers_0.png)

<div class="content-ad"></div>

# 마무리

지금까지 상호 TLS 없이 한 클라이언트-서버 상호 작용과 상호 TLS가 포함된 다른 클라이언트-서버 상호 작용을 성공적으로 만들었습니다. 우리는 TLS를 추가하기 위해 로컬호스트를 SAN으로 사용한 키와 인증서를 생성했습니다. 이후에 우리는 클라이언트 코드를 편집하여 루트 CA에 대한 TLS 구성을 포함시키고 통신을 암호화할 인증서와 개인 키를 지정했습니다. 서버 코드도 마찬가지로 루트 CA를 지정하고, 서버가 청취해야 할 인증서와 키를 지정했습니다.

이제 mTLS를 마이크로서비스 간에 서비스 메시를 통해 고려할 수 있는 기초가 생겼기를 바랍니다. 저희의 예에서는 한 번 인증서와 키를 생성하고 수동으로 구성에 입력했지만, 서비스 메시는 종종 짧은 갱신 주기로 자동으로 해당 인증서를 회전하고, 일반 통신을 측면 프록시를 통해 라우팅하며 — 그런 다음 일반 통신을 mTLS로 업그레이드하고 목적지에 도달하면 복호화합니다. 본질적으로 mTLS는 보이지 않고, 이것이 강력한 프록시 구성과 제어 계층의 마법입니다.

이 글을 통해 컨테이너화된 작업을 보호하는 방법에 대해 조금이나마 배우셨기를 바랍니다. 그리고 언제나 — 더 많은 글을 보기 위해 저를 Medium에서 팔로우하거나 Twitter에서 확인해 보세요.