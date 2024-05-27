---
title: "새로운 Kotlin 어노테이션 프로세서 KSP를 사용해보세요"
description: ""
coverImage: "/assets/img/2024-05-27-KapttoKSP_0.png"
date: 2024-05-27 16:16
ogImage: 
  url: /assets/img/2024-05-27-KapttoKSP_0.png
tag: Tech
originalTitle: "Kapt to KSP"
link: "https://medium.com/@rk27/kapt-to-ksp-61a65208393c"
---


안녕하세요 여러분, 이번 시리즈를 통해 KAPT에서 KSP로 변경하는 것에 대해 이야기할 거예요.

KAPT(코틀린 어노테이션 처리 도구)는 코틀린 코드에서 자바 어노테이션 프로세서를 사용할 수 있게 해줘요. 심지어 해당 프로세서가 코틀린을 명시적으로 지원하지 않더라도요. 이 과정은 코틀린 파일에서 자바 스텁을 생성하여 프로세서가 읽을 수 있게 합니다. 이 스텁 생성은 비용이 많이 드는 작업으로, 빌드 속도에 상당한 영향을 미칩니다.

프로젝트에 KSP를 어떻게 포함시킬까요?

프로젝트 최상위 build.gradle.kts 파일에 KSP 플러그인을 포함하세요.

<div class="content-ad"></div>

```kotlin
플러그인 {
    id 'com.google.devtools.ksp' version '1.8.10-1.0.9' 적용하지 않음
}
```

다음으로, 모듈 레벨의 build.gradle.kts 파일을 활성화해주세요.

```kotlin
플러그인 {
    id 'com.google.devtools.ksp'
}
```

KAPT 라이브러리를 KSP로 교체해주세요
```

<div class="content-ad"></div>

```js
    kapt 'androidx.room:room-compiler:2.5.0' //제거
    ksp 'androidx.room:room-compiler:2.5.0'  //추가
```
  
이 종속성을 추가한 후에 프로젝트를 동기화하세요.

프로젝트에서 KAPT 플러그인을 제거하세요:

더는 KAPT 요구 사항이 필요하지 않을 때 KAPT 플러그인을 삭제하세요.

<div class="content-ad"></div>

```js
plugins {
    id 'org.jetbrains.kotlin.kapt'
}

//프로젝트에서 남은 kapt 플러그인도 제거해야 합니다
```

# KSP2 베타 버전 출시!

KSP2는 KSP API의 새로운 구현체입니다. KSP 1.x보다 더 빠르고 쉽게 사용할 수 있을 것입니다. 자세한 내용은 KSP2 소개를 참고해주세요.

# 즐거운 코딩하세요 🚀
```