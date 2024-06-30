---
title: "도커 다이어트 보안 및 속도를 위한 데비안 이미지"
description: ""
coverImage: "/assets/img/2024-05-20-DockeronaDietDistrolessImagesforSecuritySpeed_0.png"
date: 2024-05-20 17:03
ogImage:
  url: /assets/img/2024-05-20-DockeronaDietDistrolessImagesforSecuritySpeed_0.png
tag: Tech
originalTitle: "Docker on a Diet: Distroless Images for Security , Speed"
link: "https://medium.com/@sajal.devops/docker-on-a-diet-distroless-images-for-security-speed-2a4145f5c56d"
---

<img src="/assets/img/2024-05-20-DockeronaDietDistrolessImagesforSecuritySpeed_0.png" />

컨테이너화된 세상에서 이미지 크기가 중요합니다. 작은 이미지는 더 빠른 배포, 줄어든 저장 요구 사항 및 향상된 보안으로 이어집니다. Google의 Distroless Docker 이미지가 등장하는 곳입니다.

Distroless 이미지는 극단적으로 최소한한 이미지입니다. 패키지 관리자, 셸 및 불필요한 유틸리티를 포함한 무겁고 복잡한 운영 체제 계층을 제거합니다. 대신, 이들은 당신의 애플리케이션이 실행하기 위해 필요한 것에만 집중합니다: 애플리케이션 자체와 필수 런타임 종속성입니다.

# 왜 Distroless를 사용해야 할까요?

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

Distroless 이미지를 사용하는 이점은 매우 많습니다:

- 크기가 작음: Distroless 이미지는 극히 작아서 종종 몇 메가바이트에 불과합니다. 이는 배포가 빨라지고 컨테이너 레지스트리 및 클러스터의 저장 공간을 줄이는 것을 의미합니다.
- 향상된 보안: 불필요한 구성 요소를 제거함으로써 Distroless 이미지는 잠재적인 취약점을 위한 공격 표면을 줄입니다. 관리해야 할 패키지가 적기 때문에 보안 패치 작업도 매우 간단해집니다.
- 성능 향상: 작은 이미지는 컨테이너의 시작 시간이 빨라지도록 돕습니다.

# Distroless로 시작하기

Distroless는 다양한 언어 및 런타임을 지원하는 기본 이미지를 제공합니다. 다음은 이를 사용하는 빠른 가이드입니다:

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

- 이미지 선택: 사용 가능한 이미지를 탐색하려면 GitHub의 Distroless 프로젝트(https://github.com/GoogleContainerTools/distroless)로 이동하세요. 인기 있는 옵션으로는 일반 목적의 애플리케이션용 gcr.io/distroless/base 및 언어별 이미지인 gcr.io/distroless/java17이 있습니다.
- Dockerfile 빌드: Dockerfile에서 선택한 Distroless 이미지를 기본 레이어로 사용하세요.
- 애플리케이션 복사: COPY 명령을 사용하여 애플리케이션 코드와 필요한 종속성을 이미지로 복사하세요.
- 엔트리포인트 정의: Distroless 이미지에는 셸이 없으므로 ENTRYPOINT 명령을 사용하여 애플리케이션의 엔트리포인트를 정의해야 합니다. 이는 벡터 형태로 지정해야 합니다. (예: ["/app/my_app"])

# JAVA를 위한 Distroless 이미지 사용

Distroless 이미지 사용의 장점을 설명하기 위해 Java 애플리케이션 개발 프로세스에 이를 원활하게 통합하는 방법을 탐색해볼까요?

애플리케이션 이미지를 빌드하는 데 사용된 샘플 Dockerfile입니다.

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
FROM gcr.io/distroless/java17:nonroot
EXPOSE 8080
COPY target/*.jar app.jar
CMD ["app.jar"]
```

취약점 수와 이미지 크기를 비교한 분석 결과, 상당한 개선이 있었습니다. 이러한 향상을 보여주는 자세한 통계는 다음과 같습니다.

이전에 사용한 openjdk 베이스 이미지

이전 취약점 통계: 총 96개 | 중요: 0 | 높음: 23 | 중간: 73

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

![이미지](/assets/img/2024-05-20-DockeronaDietDistrolessImagesforSecuritySpeed_1.png)

Java distroless 이미지 사용 중

현재 취약점 통계: 총 1 | 심각: 0 | 높음: 0 | 중간: 1

![이미지](/assets/img/2024-05-20-DockeronaDietDistrolessImagesforSecuritySpeed_2.png)

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

베이스 Java 이미지 크기: 226MB [이미지 크기의 50% 감소]

# NGINX에 Distroless 이미지 사용하기

미리 빌드된 Nginx Distroless 이미지가 즉시 사용 가능하지 않기 때문에, 베이스 Distroless 이미지를 사용하여 커스텀 이미지를 만드는 방법을 살펴보겠습니다.

애플리케이션 이미지를 빌드하는 데 사용된 샘플 Dockerfile입니다.

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
# Nginx를 빌드 이미지로 사용
FROM nginx:1.25 as build

# 메인 이미지로 distroless nonroot 사용
FROM gcr.io/distroless/base-debian11:nonroot
COPY --from=build /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/ca-certificates.crt
COPY --from=build /etc/passwd /etc/passwd
COPY --from=build /etc/group /etc/group
USER nginx

# 빌드 이미지로부터 필요한 Nginx 라이브러리 복사
COPY --from=build /lib/x86_64-linux-gnu/libdl.so.2 /lib/x86_64-linux-gnu/libdl.so.2
COPY --from=build /lib/x86_64-linux-gnu/libc.so.6 /lib/x86_64-linux-gnu/libc.so.6
COPY --from=build /lib/x86_64-linux-gnu/libz.so.1 /lib/x86_64-linux-gnu/libz.so.1
COPY --from=build /lib/x86_64-linux-gnu/libcrypt.so.1 /lib/x86_64-linux-gnu/libcrypt.so.1
COPY --from=build /lib/x86_64-linux-gnu/libpthread.so.0 /lib/x86_64-linux-gnu/libpthread.so.0
COPY --from=build /lib64/ld-linux-x86-64.so.2 /lib64/ld-linux-x86-64.so.2
COPY --from=build /usr/lib/x86_64-linux-gnu/libssl.so.3 /usr/lib/x86_64-linux-gnu/libssl.so.3
COPY --from=build /usr/lib/x86_64-linux-gnu/libpcre2-8.so.0 /usr/lib/x86_64-linux-gnu/libpcre2-8.so.0
COPY --from=build /usr/lib/x86_64-linux-gnu/libcrypto.so.3 /usr/lib/x86_64-linux-gnu/libcrypto.so.3

# 빌드 이미지로부터 필요한 이진 파일 및 디렉토리 복사
COPY --from=build /usr/sbin/nginx /usr/sbin/nginx
COPY --from=build /etc/nginx /etc/nginx
COPY --from=build --chown=nginx:nginx /var/cache/nginx /var/cache/nginx
COPY --from=build --chown=nginx:nginx /var/log/nginx /var/log/nginx
COPY --from=build --chown=nginx:nginx /usr/share/nginx/html /usr/share/nginx/html

COPY nginx.conf /etc/nginx/nginx.conf
COPY default.conf /etc/nginx/conf.d/

ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]
```

Nginx 베이스 이미지의 취약점 카운트와 이미지 크기를 비교 분석하였습니다.

이전에 사용한 nginx 알파인 베이스 이미지

이전 취약성 통계 "Total: 32 | Critical: 1 | High: 13 | Medium: 18"

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

![Dockerona Diet Distroless Images for Security Speed 3](/assets/img/2024-05-20-DockeronaDietDistrolessImagesforSecuritySpeed_3.png)

Using base distroless image crafted with nginx

Current Vulnerability Stats: Total: 0 | Critical: 0 | High: 0 | Medium: 0

![Dockerona Diet Distroless Images for Security Speed 4](/assets/img/2024-05-20-DockeronaDietDistrolessImagesforSecuritySpeed_4.png)

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

기본 Nginx 이미지의 크기: 28MB [이미지 크기 30% 감소]

# 주의 사항

Distroless는 큰 장점을 제공하지만 몇 가지 주의할 점이 있습니다:

- 쉘 액세스 없음: Distroless 이미지에는 디자인상 셸이 없습니다. 컨테이너 내에서 디버깅을 하려면 추가 도구나 멀티 스테이지 빌드가 필요할 수 있습니다.
- 기능 제한: 패키지 관리자가 없기 때문에 애플리케이션이 필요로 하는 추가 라이브러리, 유틸리티 또는 인증서를 명시적으로 포함해야 합니다.

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

# Distroless 또는 Alpine...어느 쪽을 선택해야 할까요?

Distroless와 Alpine 이미지의 장단점을 이해하여, 귀하의 애플리케이션 요구사항과 개발 흐름에 가장 적합한 결정을 내릴 수 있습니다.

![이미지](/assets/img/2024-05-20-DockeronaDietDistrolessImagesforSecuritySpeed_5.png)

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

Distroless 이미지는 안전하고 효율적인 도커 컨테이너를 만드는 매력적인 옵션입니다. 미니멀리즘을 포용함으로써 더 작은 공격 표면, 빠른 배포 및 향상된 성능을 얻을 수 있습니다. 다음 번에 도커 이미지를 만들 때는 여분의 부담을 줄이고 Distroless 기본 이미지를 선택하는 것을 고려해보세요.
