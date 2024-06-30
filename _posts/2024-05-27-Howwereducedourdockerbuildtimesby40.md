---
title: "우리가 도커 빌드 시간을 40 줄인 방법"
description: ""
coverImage: "/assets/img/2024-05-27-Howwereducedourdockerbuildtimesby40_0.png"
date: 2024-05-27 17:22
ogImage:
  url: /assets/img/2024-05-27-Howwereducedourdockerbuildtimesby40_0.png
tag: Tech
originalTitle: "How we reduced our docker build times by 40%"
link: "https://medium.com/datamindedbe/how-we-reduced-our-docker-build-times-by-40-afea7b7f5fe7"
---

많은 기업들과 마찬가지로, 저희 회사도 제품에 사용되는 모든 구성 요소에 대한 도커 이미지를 빌드합니다. 시간이 지남에 따라 몇 가지 이미지가 점점 커지고, 또한 CI 빌드 시간이 점점 오래 걸리게 되었습니다. 제 목표는 CI 빌드가 5분을 넘지 않도록 하는 것입니다. 이 아이디어는 커피를 마시기에 이상적인 시간이기 때문에 나왔습니다. 그 시간을 넘어가면, 개발자의 생산성이 떨어지게 됩니다.

생산성이 감소하는 이유는 다음과 같습니다:

- 개발자들은 빌드가 완료되기를 기다려야 하며, 따라서 시간을 낭비합니다.
- 개발자들은 새로운 작업을 시작하고 나중에 되돌아옵니다. 이는 더 많은 문맥 전환을 요구하며 종종 비효율성으로 이어집니다.

[이미지](/assets/img/2024-05-27-Howwereducedourdockerbuildtimesby40_0.png)

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

이 블로그 포스트에서는 적용한 2가지 작은 변경 사항을 설명하고, 빌드 시간이 drasctic하게 개선된 결과를 보여드리고 싶습니다. 이러한 개선 사항에 집중하기 전에 Dockerfile 작성에 대한 최상의 실천 방법을 이미 준수하고 있는지 확인하세요.

- 레이어 수를 최소화합니다
- 멀티 스테이지 빌드를 사용합니다
- 최소한의 베이스 이미지를 사용합니다
- …

# Buildkit vs Buildx

먼저 Buildkit과 Buildx에 대해 설명하겠습니다. 이 두 용어는 종종 서로 교차적으로 사용되지만 실제로 동일하지는 않습니다. 이 게시물을 작성하기 전에 나도 두 용어 사이의 차이를 완전히 이해하지 못했습니다.

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

## 빌킷

빌킷은 기존의 도커 빌더를 대체하는 개선된 백엔드입니다. 2018년부터 도커와 함께 제공되어 기본 빌더로 설정되었습니다.

다음과 같은 많은 흥미로운 기능을 제공합니다:

- 개선된 캐싱 기능
- 서로 다른 레이어를 병렬로 빌드
- 기본 이미지를 지연해서 로드합니다 (≥ 빌킷 0.9)

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

Buildkit을 사용할 때 docker build 명령의 출력이 더 깔끔하고 구조화된 모습이 빠르게 눈에 띕니다.

23.0 버전보다 오래된 docker 버전을 사용할 때 Buildkit을 사용하는 일반적인 방법은 다음과 같이 Buildkit 인수를 설정하는 것입니다:

```js
DOCKER_BUILDKIT=1 docker build --platform linux/amd64 . -t someImage:someVersion
DOCKER_BUILDKIT=1 docker push someImage:someVersion
```

## Buildx

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

빌드엑스(Buildx)는 도커의 플러그인으로, 도커에서 빌드킷(Buildkit)의 모든 잠재력을 활용할 수 있게 해줍니다. 이것은 빌드킷이 새로운 구성 옵션을 지원하는데, 이를 모두 도커 빌드 명령에 거슬러 호환되는 방식으로 통합하기 어려운 경우에 만들어졌습니다.

이미지를 빌드하는 데 추가로, 빌드엑스는 여러 빌더를 관리하는 것을 지원합니다. CI에서 유용하게 쓰일 수 있으며, 공유 도커 데몬을 수정하지 않고 다른 설정으로 환경을 정의하는 데 도움이 됩니다.

빌드엑스를 시작하는 방법은 다음과 같습니다:

```js
docker buildx create --bootstrap --name builder
docker buildx use builder
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

# 원격 캐시의 혜택

빌드를 가속화하는 첫 번째 방법은 이미지를 원격 레지스트리에 캐시하는 것입니다. 이렇게 하면 일반적으로 CI에서 수행되는 빌드와 같이 다른 기계에서 빌드하는 경우에도 빌드 캐시를 활용할 수 있습니다. 이를 해결하기 위해 많은 사람들이 새 이미지 버전을 빌드하기 전에 이미지의 최신 버전을 다운로드했습니다. 이점은 변경되지 않은 레이어를 캐시할 수 있다는 것인데, 처음에 전체 이미지를 다운받는 데 시간이 걸릴 수 있지만 레이어를 재사용할 수 있는 것은 보장할 수 없습니다. 예를 들어, 다음 명령어를 사용했습니다:

```js
docker pull someImage:latest || true
docker build --platform linux/amd64 . \
-t someImage:someVersion \
--cache-from someImage:latest
```

Buildx를 사용하면 캐시 정보를 원격 위치(예: 컨테이너 레지스트리, Blob 스토리지 등)에 저장할 수 있습니다. 빌더는 주어진 레이어가 이미 존재하는지 확인하고, 그렇다면 다시 만들지 않고 재사용합니다. 이를 로컬로 다운로드하지 않고도 수행할 수 있습니다. 이 메커니즘을 활용하기 위해 이전 명령어를 재작성한 것은 다음과 같습니다:

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

docker buildx build --platform linux/amd64 . \
-t someImage:someVersion --push \
--cache-to type=inline,mode=max \
--cache-from someImage:somePreviousVersion

"max" 모드는 결과 이미지에 사용되지 않는 레이어도 모든 빌드 정보를 저장한다는 것을 의미합니다 (예: 멀티 스테이지 빌드 사용 시). 기본적으로 "min" 모드가 사용되며 최종 이미지에 존재하는 레이어에 대한 빌드 정보만 저장합니다.

캐싱의 특수 사례는 캐시 데이터를 "inline"으로 저장하는 것이며, 이미지와 함께 캐싱된다는 것을 의미합니다. 이 옵션은 Buildx 없이 Buildkit을 사용할 때도 지원됩니다. 멀티 스테이지 빌드를 사용할 때 시작하기 가장 쉽지만 출력물과 캐시 사이에 명확한 구분을 제공하지 않으며 조심해야 합니다. 캐시 데이터를 "inline"으로 저장하는 명령어는 다음과 같습니다:

docker buildx build --platform linux/amd64 . \
-t someImage:someVersion --push \
--cache-to type=inline,mode=max \
--cache-from someImage:somePreviousVersion

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

# Docker 이미지에 파일 추가하는 새로운 방법

도커는 Dockerfile 작성을 위한 새로운 구문 버전, 즉 #syntax=docker/dockerfile:1.4를 소개했습니다. 이 버전은 COPY 및 ADD 명령에 대한 추가 링크 옵션을 지원합니다.

이전에 COPY 또는 ADD 명령을 사용할 때 빌더가 새로운 스냅샷을 만들어 새 파일을 기존 파일 시스템과 병합했습니다. 이 작업을 수행하려면 부모 레이어가 모두 존재해야 하는데, 그렇지 않으면 대상 디렉토리가 아직 존재하지 않을 수 있습니다. 최종적으로 이미지(빌드 명령의 결과물)는 각각의 스냅샷 간의 차이를 포함하는 레이어 당 tarball로 구성됩니다.

```js
FROM baseImage:version
COPY binary /opt/
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

링크 옵션을 사용하면 새 파일은 이전 레이어에 의존하지 않고 자체 스냅샷에 넣습니다. 링크된 파일은 자체 tarball에 저장되며 서로 다른 tarball들이 연결되어 파일 시스템에 의존하지 않고 연결됩니다. 다음 이미지에 설명이 나와 있습니다.

![이미지](/assets/img/2024-05-27-Howwereducedourdockerbuildtimesby40_1.png)

```jsfile
# syntax=docker/dockerfile:1.4
FROM baseImage:version
COPY [--chown=<user>:<group>] [--chmod=<perms>] --link binary /opt/
```

주요 장점은 파일이 이제 이전 레이어에 의존하지 않는다는 것입니다. 파일이 변경되지 않았다면 이전 레이어가 변경되더라도 레이어를 재사용할 수 있습니다.

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

또한 여러 레이어의 데이터 복사가 이제 병렬로 실행될 수 있기 때문에 빌드 속도를 높일 수도 있습니다.

# 결론

이 블로그 포스트에서는 CI 파이프라인을 최적화한 후 얻은 몇 가지 새로운 통찰을 설명합니다. 전체 도커 빌드 시간을 40% 줄이게 된 두 가지 작은 변경 사항에 대해 논의하겠습니다.

- 빌드 캐시 정보를 원격으로 저장
- 도커 이미지에 파일을 추가하거나 복사할 때 링크 옵션 사용
