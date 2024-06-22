---
title: "KMM에서 Proto DataStore 마법을 해제하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-UnlockingProtoDataStoreMagicinKMM_0.png"
date: 2024-06-23 01:17
ogImage: 
  url: /assets/img/2024-06-23-UnlockingProtoDataStoreMagicinKMM_0.png
tag: Tech
originalTitle: "Unlocking Proto DataStore Magic in KMM"
link: "https://medium.com/@aribmomin111/unlocking-proto-datastore-magic-in-kmm-d397f40a0805"
---


<img src="/assets/img/2024-06-23-UnlockingProtoDataStoreMagicinKMM_0.png" />

모바일 개발의 빠르게 진화하는 세계에서 Kotlin Multiplatform Mobile (KMM)은 안드로이드 및 iOS 애플리케이션을 위한 공유 코드를 작성할 수 있게 해줍니다. 중요한 도전 중 하나는 안드로이드의 SharedPreference나 iOS의 NSUserDefaults와 같은 효율적이고 신뢰할 수 있는 키-값 데이터 저장 솔루션을 찾는 것입니다. 이 글에서는 강력하고 타입 안전한 솔루션 Proto DataStore를 KMM 프로젝트에 통합하는 방법을 탐구합니다.

# DataStore

Jetpack DataStore는 프로토콜 버퍼를 사용하여 키-값 쌍 또는 형식화된 객체를 저장할 수 있는 데이터 저장 솔루션입니다. DataStore는 Kotlin 코루틴과 Flow를 사용하여 데이터를 비동기적으로, 일관되게 및 트랜잭션 처리할 수 있습니다.

<div class="content-ad"></div>

DataStore은 두 가지 다른 구현을 제공합니다:

- Preferences DataStore는 키를 사용하여 데이터를 저장하고 액세스합니다. 이 구현은 미리 정의된 스키마가 필요하지 않으며 형식 안전성을 제공하지 않습니다.
- Proto DataStore는 사용자 정의 데이터 유형의 인스턴스로 데이터를 저장합니다. 이 구현은 프로토콜 버퍼를 사용하여 스키마를 정의해야 하지만 형식 안전성을 제공합니다.

Google이 이제 DataStore Multiplatform을 출시했습니다!

이 문서에서는 KMM에 Proto DataStore를 구현하여 Protocol Buffer를 활용할 것입니다. KMM에서 Protocol Buffer를 사용하기 위해 Wire를 사용했습니다.

<div class="content-ad"></div>

# 전체 코드

# 단계별 구현

- app/build.gradle.kts 파일에 DataStore 종속성 추가

```js
commonMain.dependencies {
  // 다른 종속성들
  implementation("androidx.datastore:datastore-core-okio:1.1.1")
}
```

<div class="content-ad"></div>

- app/build.gradle.kts 파일에 Wire Gradle 플러그인을 추가해주세요.

```js
plugins {
  // 다른 플러그인들
  id("com.squareup.wire") version "5.0.0-alpha03"
}
```

- app/build.gradle.kts 파일에 Wire Gradle 설정을 추가해주세요.

```js
wire {
    kotlin {}
    sourcePath {
        srcDir("src/commonMain/proto")
    }
}
```

<div class="content-ad"></div>

- src/commonMain/proto 디렉토리 아래 preference_data.proto 파일을 생성해주세요

![아이미지](/assets/img/2024-06-23-UnlockingProtoDataStoreMagicinKMM_1.png)

```proto
// preference_data.proto 파일

syntax = "proto3";

package com.areeb.proto_datastore_kmm;

message PreferenceData {
  int32 counter = 1;
}
```

- PreferenceData에 대한 Protocol Buffer 직렬화기를 생성해주세요

<div class="content-ad"></div>

```kt
// commonMain 안에 있습니다
// PreferenceSerializer.kt

object PreferenceSerializer : OkioSerializer<PreferenceData> {
    override val defaultValue: PreferenceData
        get() = PreferenceData()

    override suspend fun readFrom(source: BufferedSource): PreferenceData {
        try {
            return PreferenceData.ADAPTER.decode(source)
        } catch (exception: IOException) {
            throw Exception(exception.message ?: "Serialization Exception")
        }
    }

    override suspend fun writeTo(t: PreferenceData, sink: BufferedSink) {
        sink.write(t.encode())
    }
}
```

- 각 플랫폼(여기서 안드로이드 및 iOS)을 위한 Proto DataStore 인스턴스를 생성합니다

```kt
// commonMain 안에 있습니다
// DataStore.kt

internal const val DATA_STORE_FILE_NAME = "proto_datastore.preferences_pb"

expect fun getDataStore(): DataStore<PreferenceData>

fun createDataStore(
    fileSystem: FileSystem,
    producePath: () -> Path
): DataStore<PreferenceData> =
    DataStoreFactory.create(
        storage = OkioStorage(
            fileSystem = fileSystem,
            producePath = producePath,
            serializer = PreferenceSerializer,
        ),
    )
```

```kt
// androidMain 안에 있습니다
// ProtoDataStore.kt

actual fun getDataStore(): DataStore<PreferenceData> {
    val content = requireNotNull(AndroidPlatformContextProvider.context)
    val producePath = { content.filesDir.resolve(DATA_STORE_FILE_NAME).absolutePath.toPath() }

    return createDataStore(fileSystem = FileSystem.SYSTEM, producePath = producePath)
}
```

<div class="content-ad"></div>

```kotlin
// iosMain 내부
// ProtoDataStore.kt

실제 getDataStore() 함수:

actual fun getDataStore(): DataStore<PreferenceData> {
    @OptIn(ExperimentalForeignApi::class)
    val producePath = {
        val documentDirectory: NSURL? = NSFileManager.defaultManager.URLForDirectory(
            directory = NSDocumentDirectory,
            inDomain = NSUserDomainMask,
            appropriateForURL = null,
            create = false,
            error = null,
        )
        requireNotNull(documentDirectory).path + "/$DATA_STORE_FILE_NAME"
    }

    return createDataStore(fileSystem = FileSystem.SYSTEM, producePath = { producePath().toPath() })
}
```

- DataStore를 사용하여 키-값 쌍을 가져오고 저장합니다.

```kotlin
// commonMain 내부
// Preference.kt

Preference 인터페이스:

interface Preference {
    suspend fun updateCounter()
    fun getCounter(): Flow<Int>
}

PreferenceImpl 클래스:

PreferenceImpl(private val dataStore: DataStore<PreferenceData> = getDataStore()) :
    Preference {

    override suspend fun updateCounter() {
        dataStore.updateData { data ->
            data.copy(counter = data.counter + 1)
        }
    }

    override fun getCounter(): Flow<Int> {
        return dataStore.data.map { data ->
            data.counter
        }
    }
}
```

- Compose Multiplatform에서 DataStore 사용법


<div class="content-ad"></div>

```kotlin
@Composable
@Preview
fun App() {
    MaterialTheme {
        val composableScope = rememberCoroutineScope()
        val preference: Preference = remember { PreferenceImpl() }
        val counter by preference.getCounter().collectAsState(initial = 0)

        Column(
            Modifier.fillMaxSize(),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.Center,
        ) {
            Button(
                onClick = {
                    composableScope.launch {
                        preference.updateCounter()
                    }
                },
            ) {
                Text("Click me! Counter: $counter")
            }
        }
    }
}
```

결과

<img src="https://miro.medium.com/v2/resize:fit:648/1*LLMohc4VOkcL1wdhGDjp_Q.gif" />

<img src="https://miro.medium.com/v2/resize:fit:600/1*myxIzIctkEZvgk81vKV0-w.gif" />


<div class="content-ad"></div>

아래 코멘트에 여러분의 제안과 개선 사항을 남겨주세요. 편안한 마음으로 연락해주세요!

LinkedIn에서 저와 연결해보세요.