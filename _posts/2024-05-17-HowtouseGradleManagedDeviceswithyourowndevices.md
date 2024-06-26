---
title: "자신의 디바이스와 함께 Gradle 관리 디바이스를 사용하는 방법"
description: ""
coverImage: "/assets/img/2024-05-17-HowtouseGradleManagedDeviceswithyourowndevices_0.png"
date: 2024-05-17 17:48
ogImage:
  url: /assets/img/2024-05-17-HowtouseGradleManagedDeviceswithyourowndevices_0.png
tag: Tech
originalTitle: "How to use Gradle Managed Devices with your own devices"
link: "https://medium.com/bumble-tech/how-to-use-gradle-managed-devices-with-your-own-devices-750d6709552d"
---

<img src="/assets/img/2024-05-17-HowtouseGradleManagedDeviceswithyourowndevices_0.png" />

요즘 Google은 안드로이드 Gradle 플러그인을 위한 새로운 기능, Firebase Test Lab for Gradle Managed Devices를 소개했어요. 이 기능은 Gradle Managed Devices API를 사용하여 테스트를 Gradle이 실행되는 동일한 기계가 아니라 Firebase Test Lab(유료 기능) 내에서 원격 가상 또는 물리 장치에서 시작합니다. 이 기사에서는 해당 기능과 Firebase Test Lab과 같은 방식으로 테스트를 원격으로 시작하기 위해 자체 디바이스 팜을 사용하는 방법, 그리고 여러 장치 간에 실행을 병렬화하는 방법에 대해 다룰 거예요.

# Gradle Managed Devices

처음에는 Gradle Managed Devices가 안드로이드 Gradle 플러그인에게 에뮬레이터의 생성, 시작 및 종료 과정을 위임하는 목적으로 출시되었습니다.

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

android {
testOptions {
managedDevices {
devices {
register("pixel2api30", com.android.build.api.dsl.ManagedVirtualDevice) {
device = "Pixel 2"
apiLevel = 30
systemImageSource = "aosp"
}
}
}
}
}

위의 구성을 사용하면 pixel2api30Check 작업을 통해 UI 테스트를 실행할 수 있으며, 기기를 연결하거나 에뮬레이터를 실행할 필요가 없습니다. 이 테스트 실행 환경은 다양한 기계에서 동일합니다.

<img src="/assets/img/2024-05-17-HowtouseGradleManagedDeviceswithyourowndevices_1.png" />

# Firebase Test Lab

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

파이어베이스 테스트 랩을 통한 Gradle Managed Devices는 Android Dev Summit 2022에서 최근 소개된 새로운 기능입니다. 이제 Gradle에서 바로 테스트 랩에서 UI 테스트를 실행할 수 있어서 명령줄 도구나 웹 UI를 사용할 필요가 없어졌어요.

```js
plugins {
    id 'com.google.firebase.testlab'
}

android {
    testOptions {
        managedDevices {
            devices {
                register("pixel2api30", com.google.firebase.testlab.gradle.ManagedDevice) {
                    device = "Pixel2"
                    apiLevel = 30
                }
            }
        }
    }
}
```

<img src="/assets/img/2024-05-17-HowtouseGradleManagedDeviceswithyourowndevices_2.png" />

우리 자신의 장치로도 같은 작업을 할 수 있을까요? 함께 알아봐요.

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

# 커스텀 디바이스 구현

여러분이 아시다시피, 저희는 com.android.build.api.dsl.ManagedVirtualDevice 또는 com.google.firebase.testlab.gradle.ManagedDevice를 사용하고 있는데, 이 둘 모두 com.android.build.api.dsl.Device 인터페이스를 구현하고 있습니다. 그래서 우리가 직접 커스텀한 디바이스를 구현하려고 하면 어떻게 될까요?

필요한 Gradle 코드, 플러그인 등을 모두 추가할 새 모듈을 만들어봅시다. buildSrc 폴더를 사용하거나 새로운 included build를 생성하여 할 수 있습니다. 여기서는 새로운 Gradle 플러그인을 생성하는 과정에 대해서는 다루지 않겠지만, 관련 정보는 공식 문서나 저의 다른 글에서 찾아볼 수 있습니다.

새로 생성한 Gradle 플러그인 모듈에서 MyDevice를 선언하고 해당 디바이스를 애플리케이션 모듈의 build.gradle에서 사용해보세요.

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
인터페이스 MyDevice : Device
```

```js
android {
    testOptions {
        managedDevices {
            devices {
                register("myDevice", MyDevice) {}
            }
        }
    }
}
```

프로젝트를 동기화하려고 하면 다음 예외가 발생합니다: 이 컨테이너에서 MyDevice를 생성할 수 없습니다. 이것은 android.testOptions.managedDevices.devices 컨테이너가 MyDevice를 인스턴스화할 방법을 모르기 때문입니다. 왜냐하면 MyDevice가 인터페이스이기 때문입니다.

그렇다면 Android Gradle 플러그인은 이 문제를 어떻게 관리할까요? com.android.build.api.dsl.ManagedVirtualDevice를 검색하면 다음 코드를 찾을 수 있습니다:

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
dslServices.polymorphicDomainObjectContainer(Device::class.java).apply {
    registerBinding(
        com.android.build.api.dsl.ManagedVirtualDevice::class.java,
        com.android.build.gradle.internal.dsl.ManagedVirtualDevice::class.java
    )
}
```

registerBinding을 사용하면 플러그인은 컨테이너에 com.android.build.api.dsl.ManagedVirtualDevice 유형의 어떤 것을 추가하려고 시도하는 API 클라이언트가 있을 때 내부 클래스 com.android.build.gradle.internal.dsl.ManagedVirtualDevice를 사용하도록 지시하는 것입니다. 컨테이너는 ManagedVirtualDevice 클래스의 인스턴스를 생성하고 해당 인스턴스를 register 메소드의 람다에 제공할 것입니다.

동일한 작업을 하기 위해 MyDevice를 구현하는 추상 클래스와 사용자 지정 Gradle 플러그인이 필요하며 해당 플러그인을 프로젝트에 적용해야 합니다.

```js
internal abstract class MyDeviceImpl(
    private val name: String,
): MyDevice {
    override fun getName(): String = name
}

class MyDevicePlugin : Plugin<Project> {
    override fun apply(target: Project) {
        target.plugins.withType(AndroidBasePlugin::class.java) {
            target.extensions.configure(CommonExtension::class.java) {
                it.testOptions.managedDevices.devices.registerBinding(
                    MyDevice::class.java,
                    MyDeviceImpl::class.java,
                )
            }
        }
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

```js
plugins {
    id `my-device-plugin`
}

android {
    testOptions {
        managedDevices {
            devices {
                register("myDevice", MyDevice) {}
            }
        }
    }
}
```

그래서 다시 동기화를 시도하여 다른 예외를 확인해 봅시다:

```js
Caused by: java.lang.IllegalStateException: 지원되지 않는 관리형 장치 유형:
 class com.bumble.devicefarm.plugin.device.farm.DeviceFarmImpl_Decorated
 at com.android.build.gradle.internal.TaskManager.createTestDevicesForVariant(TaskManager.kt:1905)
```

스택 추적을 따라가보면, gradle.properties에 android.experimental.testOptions.managedDevices.customDevice=true를 추가해야 하며 MyDevice는 ManagedDeviceTestRunnerFactory를 구현해야 한다는 것을 알 수 있습니다. 따라서 더 자세히 조사해 보겠습니다.

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

```kotlin
내부 추상 클래스 MyDeviceImpl(
  private val name: String
) : MyDevice, ManagedDeviceTestRunnerFactory {

  override fun getName(): String = name

  override fun createTestRunner(
    project: Project,
    workerExecutor: WorkerExecutor,
    useOrchestrator: Boolean,
    enableEmulatorDisplay: Boolean
  ): ManagedDeviceTestRunner =
    MyDeviceTestRunner()

}
```

팩토리 자체에는 중요한 값이 없습니다. 반환되는 클래스 ManagedDeviceTestRunner이 더 흥미로운 부분입니다.

```kotlin
interface ManagedDeviceTestRunner {

  // 모든 테스트 케이스가 통과되면 true를 반환합니다. 그렇지 않으면 false를 반환합니다.
  fun runTests(
    managedDevice: Device,
    runId: String,
    outputDirectory: File,
    coverageOutputDirectory: File,
    additionalTestOutputDir: File?,
    projectPath: String,
    variantName: String,
    testData: StaticTestData,
    additionalInstallOptions: List<String>,
    helperApks: Set<File>,
    logger: Logger
  ): Boolean

}
```

runTests 메서드는 각 Gradle 모듈에 대해 호출되며, 테스트를 실행하는 데 사용할 수 있는 많은 데이터가 포함되어 있습니다. 여기서 testData를 사용하여 APK를 가져와 설치하고, 계장을 통해 테스트를 실행할 수 있습니다.

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

# Instrumentation

안녕하세요! 안드로이드 스튜디오에서 테스트를 실행하는 방법은 대부분 우리가 잘 알고 있습니다. 테스트 이름 근처의 실행 버튼을 클릭하거나 Gradle을 통해 connectedAndroidTest를 실행하는 방법이 있죠. 이제 이러한 도구 없이도 어떻게 테스트를 실행할 수 있는지 알아봅시다.

이 두 가지 접근 방법은 디바이스에서 am instrument 명령을 사용합니다. 이 명령은 ADB를 통해 실행됩니다. 더 자세한 내용은 공식 문서를 참고해보세요.

```js
adb shell am instrument -w <test_package_name>/<runner_class>
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

테스트를 실행하려면 runTests 메서드에서 동일한 작업을 해야 합니다. ADB 작업을 수행하기 위해 mobile.dev의 dadb 라이브러리를 사용할 것입니다. 이 라이브러리를 사용하면 ADB 실행 파일을 이용하지 않고 ADB 프로토콜을 통해 디바이스에 직접 연결할 수 있습니다. 이렇게 하면 작업 속도가 향상되며 편리하게 사용할 수 있습니다. 관련 블로그 포스트에서 이에 대해 자세히 읽을 수 있습니다.

runTests 메서드 내에서 Dadb를 사용하여 로컬 에뮬레이터에 연결하고 APK를 설치하고 테스트를 실행해보겠습니다.

```kotlin
override fun runTests(...): Boolean {
    // 로컬 에뮬레이터에 연결
    Dadb.create("localhost", 5555).use { dadb ->
        // 애플리케이션 APK 설치
        val apks = testData.testedApkFinder.invoke(DadbDeviceConfigProvider(dadb))
        // 라이브러리 모듈인 경우 비워둡니다.
        if (apks.isNotEmpty()) {
            // 앱 번들을 지원하기 위해 여러 개의 APK 설치 사용
            dadb.installMultiple(apks)
        }
        // 계기 APK 설치
        dadb.install(testData.testApk)

        // 테스트 실행
        dadb.shell("am instrument -w ${testData.applicationId}/${testData.instrumentationRunner}")
    }
    return true
}
```

runTests 메서드와 StaticTestData 클래스에는 많은 매개변수가 있습니다. 단순화를 위해 모든 것이 작동하는 데 필요한 최소한의 세트만 사용할 것입니다. 다음과 같은 것을 사용할 것입니다:

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

- testData.testedApkFinder는 필요한 애플리케이션 APK를 가져오기 위해 사용됩니다. 라이브러리 모듈의 경우 빈 목록을 반환합니다. App Bundle에서 적절한 APK 목록을 제공하기 위해 DeviceConfigProvider를 받습니다.
- testData.testApk은 androidTest 폴더에서 코드를 포함하는 instrumentation APK입니다.
- testData.applicationId는 instrument 명령으로 실행해야 하는 애플리케이션 ID입니다.
- testData.instrumentationRunner는 build.gradle 파일에서 지정한 androidx.test.runner.AndroidJUnitRunner와 같은 테스트 러너입니다.

일단 사용자 정의 DeviceConfigProvider의 구현은 건너뜁니다. 이는 dadb.shell("getptop name").output과 같은 다수의 속성에 대해 locale, 화면 밀도, 언어, 지역 및 ABI를 가져오도록 단순히 호출할 뿐입니다. 프로젝트 저장소에서 구현 세부 정보를 확인할 수 있습니다.

지금까지 다음 구조를 구현했습니다:

![image](/assets/img/2024-05-17-HowtouseGradleManagedDeviceswithyourowndevices_3.png)

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

# 결과 가져오기

runTests 메서드는 모든 테스트가 통과되면 true를 반환해야 하지만, 지금은 항상 true를 반환합니다. 계기 결과를 얻기 위해 조금 더 파고들어야 합니다.

기본적으로 명령줄에서 am instrument를 실행하려고 하면 실패 사항과 최종 결과만을 볼 수 있습니다. 더 많은 정보를 표시하려면 -r 및 -m 두 가지 플래그가 있습니다. 첫 번째는 결과를 텍스트 스트림으로 반환하고, 두 번째는 프로토콜 버퍼 스트림으로 반환합니다(API `= 26에서만 지원됨). 간단히 하기 위해 지금은 두 번째를 사용하겠습니다.

Android Gradle 플러그인은 RemoteAndroidTestRunner.StatusReporterMode.PROTO_STD 열거형을 사용하여 am instrument에서 원시 데이터를 수용할 수 있는 IInstrumentationResultParser 인스턴스를 만듭니다.

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

```kotlin
val mode = RemoteAndroidTestRunner.StatusReporterMode.PROTO_STD
val parser = mode.createInstrumentationResultParser(runId, emptyList())

Dadb.create(host, port).use { dadb ->
    ...
    dadb
        .openShell("am instrument -w ${mode.amInstrumentCommandArg} $arguments ${testData.applicationId}/${testData.instrumentationRunner}")
        .use { stream ->
            while (true) {
                val packet: AdbShellPacket = stream.read()
                if (packet is AdbShellPacket.Exit) break
                parser.addOutput(packet.payload, 0, packet.payload.size)
            }
            parser.flush()
        }
    ...
}
```

이번에는 shell 메서드 대신 openShell 메서드를 사용합니다. 이 메서드는 Protocol Buffers 스트림인 원시 데이터 스트림에 액세스할 수 있도록 해줍니다. 이 데이터는 IInstrumentationResultParser로 전달됩니다.

# HTML 및 XML 보고서 및 암시적인 예상

IInstrumentationResultParser는 테스트와 상태에 대해 청취자들에게 알립니다. emptyList()를 전달했을 것을 알 수 있습니다. 어떤 파서를 사용해야 할지 고려해보겠습니다. 몇 가지 사전 제작된 구현이 있지만, Android Gradle 플러그인이 ManagedVirtualDevice를 실행할 때 사용하는 com.android.build.gradle.internal.testing.CustomTestRunListener를 사용해야 합니다.

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

```kotlin
val xmlWriterListener = CustomTestRunListener(
    name,
    projectPath,
    variantName,
    LoggerWrapper(logger),
)
xmlWriterListener.setReportDir(outputDirectory)
xmlWriterListener.setHostName("localhost:5555")
...
val parser = mode.createInstrumentationResultParser(runId, listOf(xmlWriterListener))
...
return !xmlWriterListener.runResult.hasFailedTests()
```

CustomTestRunListener은 XmlTestRunListener를 확장하고 Android Gradle 플러그인, TeamCity 또는 여러분께 사용될 수 있는 테스트의 XML 보고서를 작성할 것입니다.

만약 CustomTestRunListener로 보고서를 생성하지 않고 myDeviceDebugAndroidTest를 실행하려고 하면, com.android.build.gradle.internal.tasks.ManagedDeviceInstrumentationTestResultAggregationTask에서 예외로 실패할 것입니다. 적어도 TEST-로 시작하는 하나의 XML 보고서를 생성할 것으로 예상합니다. 그리고 CustomTestRunListener야말로 이를 강요합니다. 이 작업은 XML과 HTML 보고서를 생성할 것이며, 아래 스크린샷에서 확인할 수 있습니다:

![스크린샷](/assets/img/2024-05-17-HowtouseGradleManagedDeviceswithyourowndevices_4.png)

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

이제 CustomTestRunListener를 사용하여 runTests 메서드에서 적절한 값을 반환할 수 있습니다. hasFailedTests 메서드에 액세스할 수 있기 때문입니다.

# 원격 실행

Dadb.create를 사용할 때 localhost뿐만 아니라 모든 IP 주소를 전달할 수도 있습니다. 이는 코드를 사용하여 원격 에뮬레이터나 장치에서 테스트를 실행할 수 있다는 것을 의미합니다. 이를 위해 MyDevice를 설정 가능하게 만들 수 있습니다.

```js
interface MyDevice : Device {

    @get:Input
    val host: Property<String>

    @get:Input
    val port: Property<Int>

}

internal abstract class MyDeviceImpl(
    private val name: String,
) : RemoteDevice, ManagedDeviceTestRunnerFactory {

    init {
        // 기본 매개변수
        host.convention("localhost")
        port.convention(5555)
    }

    ...
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

```js
register("remoteDevice", MyDevice) {
    it.host = "192.168.3.4"
    it.port = 43617
}
```

그런 다음 `Dadb.create(managedDevice.host.get(), managedDevice.port.get())`를 사용하세요.

![이미지](/assets/img/2024-05-17-HowtouseGradleManagedDeviceswithyourowndevices_5.png)

주의 깊게 읽는 독자는 이미 코드 없이 이를 수행할 수 있다는 것을 올바르게 알아차릴 수 있을 것입니다. 원격 장치를 adb 장치 목록에 표시하려면 adb connect IP:PORT를 호출하기만 하면 된다는 것이 충분하며, Android Studio 드롭다운 메뉴 안에서도 바로 확인할 수 있습니다. Gradle Managed Devices로 할 수 없는 테스트 디버깅을 할 수도 있습니다. 그러나 이러한 점을 수행할 수 있다는 것은 다음 단계에 중요한 요소입니다.

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

# 병렬 실행

기본적으로 테스트를 실행할 때 한 번에 한 장치에서만 실행됩니다. 여러 장치나 에뮬레이터에서 테스트를 병렬로 실행할 수 있는 기능이 있다면 좋을텐데요. 좋은 소식은 그런 방법을 찾았다는 것입니다.

AndroidJUnitRunner은 테스트를 샤드(shard)로 분할하여 하나의 샤드를 실행하는 기능을 지원합니다. 예를 들어, am instrument -w -e numShards 2 -e shardIndex 0는 두 개의 테스트 중 첫 번째 테스트를 실행하고, -e shardIndex 1은 두 개의 테스트 중 두 번째 테스트를 실행합니다.

하지만 이를 실제로 활용하려고 하면 다음과 같은 내용이 나타날 것입니다 (출처):

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
#!/usr/bin/env bash
./gradlew assembleAndroidTest
pids=
env ANDROID_SERIAL=emulator-5554 ./gradlew \
    connectedAndroidTest \
    -Pandroid.testInstrumentationRunnerArguments.numShards=2 \
    -Pandroid.testInstrumentationRunnerArguments.shardIndex=0 \
    -PtestReportsDir=build/testReports/shard0 \
    -PtestResultsDir=build/testResults/shard0 \
    &
pids+=" $!"
env ANDROID_SERIAL=emulator-5556 ./gradlew \
    connectedAndroidTest \
    -Pandroid.testInstrumentationRunnerArguments.numShards=2 \
    -Pandroid.testInstrumentationRunnerArguments.shardIndex=1 \
    -PtestReportsDir=build/testReports/shard1 \
    -PtestResultsDir=build/testResults/shard1 \
    &
pids+=" $!"
wait $pids || { echo "there were errors" >&2; exit 1; }
exit 0
```

이 방법은 ‘편리’에서는 거리가 먼 방법이며, 많은 매개변수를 사용하여 두 connectedAndroidTest 작업을 병렬로 실행하고 결과를 수동으로 병합한 후 TeamCity와 같은 곳에 보고해야 합니다.

Gradle Managed Devices는 android.experimental.androidTest.numManagedDeviceShards=`number_of_shards`와 같은 옵션을 통해 테스트 샤딩을 지원하지만, ManagedVirtualDevice에서만 작동합니다. 우리의 경우에는 자체적으로 관리하는 장치들과 함께 샤딩을 사용하고 싶습니다.

Gradle Managed Devices에서는 Device 추상화를 사용합니다. 이 추상화는 전체로 표현되는 여러 장치를 구현할 수 있습니다. 새로운 장치 유형을 소개하고 장치로 등록합시다.

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

```kotlin
인터페이스 MultipleDevices : Device {

    @get:Input
    val devices: ListProperty<String>

}
```

```kotlin
android {
    testOptions {
        managedDevices {
            devices {
                register("multipleDevices", com.example.MultipleDevices) {
                    it.devices.add("localhost:5555")
                    it.devices.add("localhost:5557")
                }
            }
        }
    }
}
```

ADB와 관련된 모든 것은 AdbRunner 클래스로 추출되었고 새 매개변수 ShardInfo(index, total)가 추가되었습니다. ShardInfo의 매개변수는 dadb.openShell(“am instrument”) 실행에 그대로 추가됩니다.

이제 테스트를 병렬로 실행하도록 ManagedDeviceTestRunner를 구현해야 합니다.

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

```kotlin
override fun runTests(...): Boolean {

    val devices = managedDevice.devices.get().map {
        // "host:port"를 Pair<String, Int>로 분리
        val split = it.split(':')
        split[0] to split[1].toInt()
    }

    val threadPool = Executors.newCachedThreadPool()

    val futures = devices.mapIndexed { index, (host, port) ->
        threadPool.submit(Callable {
            val runner = AdbRunner(
                host = host,
                port = port,
                shardInfo = AdbRunner.ShardInfo(
                    index = index,
                    total = devices.size,
                ),
            )
            val result = runner.run(
                // 각 디바이스에 대해 고유한 이름을 사용하여 독립적인 XML 보고서를 생성해야 합니다.
                name = "${managedDevice.name}-${host}-${port}",
                runId = runId,
                outputDirectory = outputDirectory,
                projectPath = projectPath,
                variantName = variantName,
                testData = testData,
                logger = logger,
            )
            result
        })
    }

    val success = futures.all { it.get() }

    threadPool.shutdown()

    return success
}
```

주의해야 할 사항:

- ThreadPool을 사용하고 있는데, 이는 Gradle에서 좋지 않은 방법입니다. 대신 WorkerExecutor를 사용해야 합니다. ManagedDeviceTestRunnerFactory의 매개변수에서 사용 가능한 WorkerExecutor 인스턴스를 얻을 수 있습니다. 현재 사용하지 않는 이유는 StaticTestData가 직렬화되지 않아 별도의 직렬화 가능한 데이터 홀더로 속성을 복사하여 WorkerExecutor에 전달하고 싶지 않기 때문입니다.
- 이름은 각 shard에 대해 고유해야 합니다. CustomTestRunListener에 이름을 전달하여 파일 이름에 이름이 포함된 XML 보고서가 생성됩니다. 모든 XML 보고서는 나중에 모아져 병합되므로 어떤 테스트가 어떤 디바이스에서 실행되었는지 확인할 수 있습니다.

<img src="/assets/img/2024-05-17-HowtouseGradleManagedDeviceswithyourowndevices_6.png" />

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

샤딩은 매우 중요하며 많은 UI 테스트 케이스에서 도움이 됩니다. 샘플 레파지토리에서는 라이브러리와 어플리케이션 모듈을 준비했는데, 두 모듈 모두 100개의 UI 테스트가 있습니다. 한 모듈에서 테스트를 실행하는 데 약 1분 10초가 걸립니다. 그러므로 두 모듈을 함께 실행하는 데는 약 2분 33초가 소요됩니다. 같은 테스트 스위트를 MultipleDevices에서 실행하면 약 1분 31초만에 완료됩니다. — 필요한 시간의 거의 절반입니다.

```js
  에뮬레이터 수  시간
 ----------- -----------
  1           2분 33초
  2           1분 31초
  3           43초
```

<img src="/assets/img/2024-05-17-HowtouseGradleManagedDeviceswithyourowndevices_7.png" />

# 병렬 원격 실행

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

비록 이렇게 급격한 개선이 있었지만, 하나의 문제에서는 자유로울 수 없습니다: 로컬에서 에뮬레이터를 실행해야 하기 때문에, 개발자 노트북의 자원을 많이 소비합니다.

이 문제를 해결하기 위해 수십 개의 에뮬레이터를 호스팅할 서버를 만들어 HTTP API를 다음과 같이 가지게 만들어보죠:

```js
GET /lease?devices=%number%

200 OK
[
    {
        host: "10.10.0.3",
        port: 5555,
        release_key: "ab34fd2d158f9"
    },
    ...
]

POST /release
[ "ab34fd2d158f9", ... ]

200 OK
```

해당 서버는 우리만 사용할 수 있는 단말기나 에뮬레이터의 집합을 반환하며, 해당 API 호출로 이들을 해제할 때까지만 사용할 수 있습니다. 이를 "장치 브로커"라고 부르도록 합시다.

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

디바이스 브로커는 개발자뿐만 아니라 CI(지속적인 통합)에도 혜택을 줍니다. 일반적인 CI 흐름은 앱을 빌드하고 에뮬레이터에서 테스트를 실행하는 것입니다. 이 두 단계는 CPU 및 메모리를 많이 사용하는 작업이므로 이러한 작업을 병렬로 수행할 수도 있습니다. 에뮬레이터를 다른 서버로 외부위탁함으로써 빌드 서버는 앱을 빌드하고 테스트 결과를 확인하는 데에 자원을 공유하는 대신 집중할 수 있게 됩니다.

Gradle 쪽의 구현은 상당히 간단하며 MultipleDevices와 유사한 모양입니다.

```js
interface DeviceFarm : Device {

    @get:Input
    val shards: Property<Int>

}

class DeviceBroker {

    fun lease(amount: Int): Collection<Device> {
        TODO("디바이스를 얻기 위한 네트워크 요청을 만듭니다. 이용 가능한 디바이스가 없는 경우 대기해야 합니다.")
    }

    fun release(devices: Collection<Device>) {
        TODO("다른 사용자를 위해 디바이스를 해제하기 위한 네트워크 요청을 만듭니다.")
    }

    class Device(
        val host: String,
        val port: Int,
        val releaseToken: String,
    )

}

internal class DeviceFarmTestRunner : ManagedDeviceTestRunner {

    override fun runTests(...): Boolean {
        val broker = DeviceBroker()
        val devices = broker.lease(managedDevice.shards.get())

        devices.forEachIndexed { shardIndex, device ->
            ...
        }

        broker.release(devices)

        return success
    }

}
```

![이미지](/assets/img/2024-05-17-HowtouseGradleManagedDeviceswithyourowndevices_8.png)

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

# 결과

우리는 Gradle Managed Devices와 통합하기 위한 사용자 정의 장치를 구현하는 방법에 대해 조사했습니다. 장치는 우리가 원하는 것으로 뒷받침될 수 있는 추상화입니다: 단일 장치 또는 에뮬레이터, 로컬 또는 Firebase Test Lab과 같은 웹 서비스에 원격으로 호스팅되거나 브로커 서비스가 있는 사용자 정의 장치 팜 등이 될 수 있습니다. 단일 장치의 경우에는 ADB를 통해 직접 adb connect를 사용하여 연결할 수 있으므로 실질적인 혜택이 없습니다(디버거를 연결하는 기능과 같은 기능을 손실하지 않으면서). 그러나 여러 원격 장치에 뒷받침된 장치를 구현함으로써 개발자의 노트북 및 CI 빌드 서버의 계산 리소스를 할당할 수 있습니다. 또한 샤딩 기능을 사용하여 테스트 실행을 병렬화하고, 2대의 장치를 사용할 경우 테스트 실행 속도를 2배 높일 수 있습니다.

코드는 저장소에서 사용할 수 있지만, 이것은 개념 증명으로 의도된 대로 프로덕션에서 사용되는 것이 아님을 명심하십시오. 코드는 조직에서 이 접근 방식을 도입하는 데 큰 도움이 될 수 있습니다. 구현 세부사항은 귀하의 인프라에 크게 의존합니다. 마지막으로 코드 자체는 오류에 강건하지 않으며 더 신뢰할 수 있는 방식으로 다시 작성해야 합니다.

질문이 있으시면 아래의 코멘트 섹션에서 자유롭게 물어봐 주세요.
