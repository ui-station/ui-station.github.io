---
title: "Alpine, Distroless, 아니면 Scratch 도커 이미지 선택 가이드"
description: ""
coverImage: "/assets/img/2024-06-23-alpinedistrolessorscratch_0.png"
date: 2024-06-23 00:36
ogImage: 
  url: /assets/img/2024-06-23-alpinedistrolessorscratch_0.png
tag: Tech
originalTitle: "alpine, distroless or scratch?"
link: "https://medium.com/google-cloud/alpine-distroless-or-scratch-caac35250e0b"
---


저는 최근 온라인 부티크 샘플 앱의 4개 Golang 앱을 알파인에서 스크래치로 이주했어요. 그런 과정에서 배운 멋진 것들이 있습니다.

알파인은 우리의 컨테이너화된 애플리케이션의 컨테이너 이미지 크기를 줄이는 데 인기 있는 이미지입니다. 그로 인해 보안 상태도 개선됩니다(공격 표면이 적고 CVE가 적습니다).

하지만 이것만으로 충분하지는 않아요. 알파인에는 아직 취약점이 있는 패키지가 포함되어 있고, busybox와 wget이 있어서 프로덕션 환경에 적합하지 않은 것이죠.

알파인보다 더 나은 것을 할 수 있을까요? 그럼! distroless가 도움이 될 거에요.

<div class="content-ad"></div>

더 많고 더 나은 것을 할 수 있을까요? 네! scratch가 도움이 될 수 있어요.

이 세 가지 이미지 간의 차이를 살펴봅시다.

이를 위해 Kelsey Hightower의 helloworld 앱을 사용하고 scratch를 사용하여 테스트해보겠지만, 알파인 및 distroless로도 테스트를 수행할 거에요.

```js
FROM golang:1.22
WORKDIR /go/src/github.com/kelseyhightower/app/
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build .

# FROM alpine
# FROM gcr.io/distroless/static
# FROM cgr.dev/chainguard/static
FROM scratch
COPY --from=0 /go/src/github.com/kelseyhightower/app/helloworld .
ENTRYPOINT ["/helloworld"]
```

<div class="content-ad"></div>

# 크기

다양한 변형의 컨테이너 이미지를 빌드한 후에 이제 크기를 비교해봅시다:

```js
REPOSITORY   TAG          IMAGE ID       CREATED          SIZE
helloworld   alpine       2c2991efd7cd   7 minutes ago    14.4MB
helloworld   distroless   08308f5bc54d   16 minutes ago   9.03MB
helloworld   chainguard   eaa8a9d18fef   8 seconds ago    7.79MB
helloworld   scratch      287ad0140c46   32 minutes ago   7.04MB
```

모두 매우 가벼우며 작습니다. Alpine은 더 크며, 더 많은 패키지를 제공하기 때문에 Scratch보다 두 배 더 큽니다.

<div class="content-ad"></div>

이 블로그 포스트인 "이미지 크기는 핵심이 아니다"는 이미지 크기에만 집중해서는 안 된다는 이유를 설명합니다. 작은 컨테이너 이미지를 가지는 것은 종종 성능이 더 우수하고 보안 수준이 높다는 좋은 신호입니다.

# 패키지

이제 빌드된 이 컨테이너 이미지에 포함된 패키지를 비교해 보겠습니다. 이를 위해 syft를 사용해 봅시다.

알파인 (17 개 패키지):

<div class="content-ad"></div>

```js
이름                    버전               유형        
alpine-baselayout       3.4.3-r2              apk          
alpine-baselayout-data  3.4.3-r2              apk          
alpine-keys             2.4-r1                apk          
apk-tools               2.14.0-r5             apk          
busybox                 1.36.1-r15            apk          
busybox-binsh           1.36.1-r15            apk          
ca-certificates-bundle  20230506-r0           apk          
helloworld              (devel)               go-module    
libc-utils              0.7.2-r5              apk          
libcrypto3              3.1.4-r5              apk          
libssl3                 3.1.4-r5              apk          
musl                    1.2.4_git20230717-r4  apk          
musl-utils              1.2.4_git20230717-r4  apk          
scanelf                 1.3.7-r2              apk          
ssl_client              1.36.1-r15            apk          
stdlib                  go1.22.2              go-module    
zlib                    1.3.1-r0              apk
```

distroless (5 개):

```js
이름        버전          유형        
base-files  12.4+deb12u5     deb          
helloworld  (devel)          go-module    
netbase     6.4              deb          
stdlib      go1.22.2         go-module    
tzdata      2024a-0+deb12u1  deb
```

chainguard (7 개):

<div class="content-ad"></div>

```js
이름                    버전                    유형        
알파인-베이스레이아웃-데이터  3.6.4-r0                 apk          
알파인-키               2.4-r1                   apk          
알파인-릴리스            3.20.0_alpha20240329-r0  apk          
CA-인증서 번들         20240226-r0              apk          
헬로우월드              (개발 중)                  go-모듈    
표준 라이브러리          go1.22.2                 go-모듈    
시간대 데이터            2024a-r1                 apk
```

기본 (2 개의 패키지):

```js
이름        버전   유형        
헬로우월드  (개발 중)   go-모듈    
표준      go1.22.2  go-모듈
```

우리는 이제 이들을 우리의 필요에 맞게 현명하게 사용할 수 있어요. 사용하는 프로그래밍 언어에 따라 무엇이 들어있는지 정확히 알아야 하는 것이 정말 멋지고 중요하죠?

<div class="content-ad"></div>

# CVEs

불필요한 종속성과 패키지의 수를 줄이면 유지 보수 시간과 관련된 CVEs 업데이트로부터 오는 피로를 줄일 수 있어요.

만약 trivy와 같은 도구를 사용한다면, 이 블로그 글을 작성할 때 alpine만 CVEs가 있는 것을 확인할 수 있어요:

```js
helloworld:alpine (alpine 3.19.1)
=================================
Total: 2 (UNKNOWN: 0, LOW: 2, MEDIUM: 0, HIGH: 0, CRITICAL: 0)

|  라이브러리 | 취약점 | 심각도 | 상태 | 설치된 버전 | 수정된 버전 | 제목 |
|----------|----------|--------|------|-----------------|-----------------|------------------------------------------------------------------|
| libcrypto3 | CVE-2024-2511 | LOW | fixed | 3.1.4-r5 | 3.1.4-r6 | openssl: Unbounded memory growth with session handling in TLSv1.3 |
| | | | | | | [자세히 보기](https://avd.aquasec.com/nvd/cve-2024-2511) |
| libssl3 | | | | | | |
| | | | | | | |
| | | | | | | |
```

<div class="content-ad"></div>

이 예시에서는 낮은 수준의 보안이지만 그래도 중요합니다. 여전히 자신의 앱 패키지의 CVEs를 다루어야 하므로 기본 이미지에서 이를 제거하는 것은 항상 큰 이점입니다.

# 더 많은 보안

다음 명령어를 사용하여 모두를 특권 없이 실행하는 것은 어려움이 없습니다. 모두 성공적으로 작동합니다:

```js
docker run \
    -d \
    -p 8080:8080 \
    --read-only \
    --cap-drop=ALL \
    --user=65532 \
    CONTAINER_IMAGE
```

<div class="content-ad"></div>

그리고 docker exec 또는 kubectl exec를 실행할 수 있는 유일한 컨테이너 이미지는 alpine 이미지입니다. 다른 이미지들은 셸을 갖고 있지 않기 때문에 이 작업을 허용하지 않습니다. 보안적인 측면에서 셸이 없는 것은 보안 포지션을 개선하는 좋은 실천법이죠 (다시 말해, 공격 표면을 줄입니다).

# 이것으로 마치겠습니다!

만약 정적으로 컴파일된 Golang 또는 Rust 앱을 사용한다면, scratch를 사용하세요. ca-certificates나 tzdata와 같은 것이 필요하다면 gcr.io/distroless/static 또는 cgr.dev/chainguard/static을 선택하세요.

이것이 바로 쿠버네티스 프로젝트가 이미 4년간 진행해 온 작업입니다.

<div class="content-ad"></div>

만약 libc가 필요하다면 gcr.io/distroless/base-nossl 또는 libssl이 포함된 gcr.io/distroless/base를 사용할 수 있어요.

또한 Java, .NET, Python 등 다른 유형의 앱을 위한 더 많은 컨테이너 이미지들도 있어요.

distroless 주변에서 더 많은 노력들도 있어요:

- Chainguard는 중요한 역할을 하는데, 그들은 많은 distroless 이미지를 가지고 있어요.
- RedHat은 UBI Micro를 가지고 있어요.
- Ubuntu는 Chiseled을 가지고 있어요 — 이제 Microsoft가 dotnet 컨테이너 이미지와 함께 포함시켰어요. 예를 들어, 저는 이 컨테이너와 다른 컨테이너를 alpine에서 chiseled로 마이그레이션 했어요.

<div class="content-ad"></div>

또한, 클러스터 내에서 배포하는 외부 컨테이너에는 distroless flavor를 요청하십시오. 예를 들어, Jib를 사용하는 경우, 이제 distroless를 기본으로 사용합니다. Istio의 경우 Istio 사이드카 프록시에 distroless를 선택적으로 사용할 수 있습니다.

distrolesss를 사용하면 컨테이너에 대한 디버깅 기능이 제거됩니다(쉘 없음, 패키지 관리자 없음, wget/curl 없음). 하지만 생산 환경에서 디버그 모드로 진입하는 것은 좋지 않은 실천 방법입니다(잠재적 해커에게 더 많은 도구를 제공하기 때문입니다). 대신에 kubernetes debug 또는 initContainers와 같은 기능을 사용할 수도 있습니다. 필요한 경우에는요.

# 리소스

- scratch for checkout, frontend, productcatalog and shipping by mathieu-benoit · Pull Request #2512 · GoogleCloudPlatform/microservices-demo (github.com)
- Is Your Container Image Really Distroless? | Docker
- erickduran/docker-distroless-poc: A simple Proof of Concept of a vulnerable web app using a distroless image and Python. (github.com)
- Chiselled Ubuntu containers: the benefits of combining Distroless and Ubuntu | Ubuntu
- Why I Will Never Use Alpine Linux Ever Again | by Martin Heinz | Better Programming