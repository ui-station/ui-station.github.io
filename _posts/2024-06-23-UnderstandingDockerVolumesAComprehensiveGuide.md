---
title: "Docker 볼륨 이해하기 완벽 가이드"
description: ""
coverImage: "/assets/img/2024-06-23-UnderstandingDockerVolumesAComprehensiveGuide_0.png"
date: 2024-06-23 22:52
ogImage:
  url: /assets/img/2024-06-23-UnderstandingDockerVolumesAComprehensiveGuide_0.png
tag: Tech
originalTitle: "Understanding Docker Volumes: A Comprehensive Guide"
link: "https://medium.com/@therahulsarkar/understanding-docker-volumes-a-comprehensive-guide-46339aa9ac53"
---

도커(Docker)의 컨테이너는 상태를 유지하지 않고 쉽게 폐기할 수 있는 방식으로 설계되었습니다. 볼륨(Volumes)은 컨테이너가 생성하고 사용하는 데이터를 단일 컨테이너의 수명 주기를 넘어서 계속 유지하는 방법을 제공합니다. 이는 데이터베이스, 파일 저장소 및 지속적인 저장 공간이 필요한 다른 응용 프로그램에 필수적입니다.

본 문서는 도커 볼륨을 생성하고 사용하는 다양한 방법을 탐구하며, 실제 응용 사례를 설명하기 위한 예제가 포함되어 있습니다.

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

# Docker Volume 소개

Docker 볼륨은 Docker 컨테이너에서 생성된 데이터를 저장하고 사용하기 위해 설계된 지속적인 저장 메커니즘입니다. 이들은 데이터 수명주기를 컨테이너 수명주기와 분리하여 데이터가 컨테이너가 삭제되거나 다시 생성되더라도 손상되지 않도록 보장합니다.

# Docker Volume의 종류

## Named Volumes

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

이름이 지정된 볼륨은 사용자가 정의한 볼륨으로, 이름으로 쉽게 참조하고 여러 컨테이너 간에 재사용할 수 있습니다. 이러한 볼륨은 Docker의 내부 볼륨 저장소에 저장됩니다.

```js
docker volume create myVolume
docker run -d --name my_container -v myVolume:/data node_container
```

## 익명 볼륨

이름이 지정되지 않은 볼륨은 생성된 이름이 없을 때 생성됩니다. 이러한 볼륨들은 일반적으로 컨테이너의 수명주기를 넘어서 지속되지 않아야 하는 일시적인 데이터에 사용됩니다.

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
도커 실행 -d --name my_container -v /data node_container
```

## 바인드 마운트

바인드 마운트는 호스트 파일 시스템의 디렉터리나 파일을 컨테이너에 매핑합니다. 이를 통해 호스트 파일 시스템에 직접 액세스하여 데이터를 호스트와 컨테이너 간에 공유해야하는 시나리오에 이상적입니다.

```js
도커 실행 -d --name my_container -v /호스트/경로:/data node_container
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

## tmpfs 볼륨

tmpfs 볼륨은 컨테이너의 메모리에 임시 파일 시스템을 마운트합니다. 민감한 정보나 임시 파일과 같이 디스크에 쓰여서는 안 되는 비영구 데이터를 저장하는 데 유용합니다.

```js
docker run -d --name my_container --tmpfs /data node_container
```

# Docker 볼륨 생성 및 관리

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

## 볼륨 만들기

이름이 지정된 볼륨을 만들려면 다음 명령을 사용하세요:

```js
docker volume create myVolume
```

## 볼륨 검사

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

볼륨을 검사하고 세부 정보를 확인하려면

```js
docker volume inspect myVolume
```

## 볼륨 제거

더 이상 필요하지 않은 볼륨을 제거하려면:

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
도커 볼륨 삭제 myVolume
```

![이미지](/assets/img/2024-06-23-UnderstandingDockerVolumesAComprehensiveGuide_2.png)

# 도커 볼륨 사용하기

## 명명된 볼륨 마운트하기

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

특정 이름의 볼륨을 컨테이너에 마운트하려면:

```js
docker run -d -v myVolume:/app/data myImage
```

## 익명 볼륨 마운트

익명 볼륨을 마운트하려면:

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
도커 실행 -d -v /app/data myImage
```

## 바인드 마운트 사용하기

바인드 마운트를 사용하려면 호스트 경로와 컨테이너 경로를 지정하십시오:

```js
도커 실행 -d -v /host/data:/app/data myImage
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

# 예시

## 1. 이름있는 볼륨을 사용하여 데이터 유지하기

이름이 지정된 볼륨을 생성하고 컨테이너에서 사용하기

```js
docker volume create mydata
docker run -d -v mydata:/app/data myImage
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

컨테이너 내부의 /app/data 경로에 작성된 모든 데이터는 컨테이너가 삭제되더라도 유지됩니다.

## 2. 컨테이너 간 데이터 공유

여러 컨테이너 간 데이터를 공유하려면 명명된 볼륨을 사용할 수 있습니다:

```js
docker volume create shared_data
docker run -d -v shared_data:/app/data container1
docker run -d -v shared_data:/app/data container2
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

![Understanding Docker Volumes](/assets/img/2024-06-23-UnderstandingDockerVolumesAComprehensiveGuide_3.png)

두 컨테이너 모두 /app/data에 읽고 쓸 수 있어 데이터 공유가 가능합니다.

## 개발을 위한 Bind Mount 사용

호스트 디렉토리를 컨테이너에 매핑하는 Bind Mount를 사용하여 실시간 코드 변경을 가능하게 합니다.

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
도커 실행 -d -v $(pwd):/app myImage
```

호스트 디렉토리의 파일에 대한 변경 사항은 즉시 컨테이너에 반영됩니다.

# 간단한 Node.js 애플리케이션 예제

## 단계 1: Node.js 애플리케이션 생성

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

먼저 파일에 데이터를 작성하는 간단한 Node.js 애플리케이션을 만들어봅시다:

![이미지](/assets/img/2024-06-23-UnderstandingDockerVolumesAComprehensiveGuide_4.png)

## 단계 2: Dockerfile 생성

다음으로 Node.js 애플리케이션을 위한 Dockerfile을 만들어봅시다.

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
FROM node:14

WORKDIR /app

COPY . .

CMD ["node", "app.js"]
```

## Step 3: 이제 도커 이미지를 빌드하세요

![Step 3](/assets/img/2024-06-23-UnderstandingDockerVolumesAComprehensiveGuide_5.png)

## Step 4: 컨테이너를 실행하세요

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

<img src="/assets/img/2024-06-23-UnderstandingDockerVolumesAComprehensiveGuide_6.png" />

- myVolume: 호스트 머신에있는 볼륨의 이름입니다.
- :/app/data: 이는 컨테이너 내부의 마운트 포인트를 지정합니다. 이 경우 호스트의 myVolume 볼륨을 컨테이너 내부의 /app/data 디렉토리로 마운트합니다.

## 단계 5: data.txt 파일의 내용을 확인합니다.

<img src="/assets/img/2024-06-23-UnderstandingDockerVolumesAComprehensiveGuide_7.png" />

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

<img src="/assets/img/2024-06-23-UnderstandingDockerVolumesAComprehensiveGuide_8.png" />

# 도커 볼륨 사용에 대한 최상의 방법

- 컨테이너 수명 주기를 초과하는 데이터에 사용할 때는 명명된 볼륨을 사용합니다.
- 개발 목적이거나 호스트 파일에 직접 액세스해야 할 때는 바인드 마운트를 사용합니다.
- 사용되지 않는 볼륨을 정기적으로 검사하고 정리하여 공간을 확보합니다.
- 보안 위험을 피하기 위해 바인드 마운트를 사용할 때 올바른 액세스 권한을 보장합니다.

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

도커 볼륨은 컨테이너화된 응용 프로그램의 유연성과 효율성을 향상시키는 강력한 기능입니다. 다양한 유형의 볼륨을 이해하고 관리하는 방법을 알면 도커를 사용하여 컨테이너 내에서 데이터를 지속적으로 유지, 공유 및 관리할 수 있습니다.
