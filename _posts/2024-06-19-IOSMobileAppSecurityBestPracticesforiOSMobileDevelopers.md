---
title: "iOS 모바일 앱 보안 iOS 모바일 개발자를 위한 최선의 모범 사례"
description: ""
coverImage: "/assets/img/2024-06-19-IOSMobileAppSecurityBestPracticesforiOSMobileDevelopers_0.png"
date: 2024-06-19 14:06
ogImage: 
  url: /assets/img/2024-06-19-IOSMobileAppSecurityBestPracticesforiOSMobileDevelopers_0.png
tag: Tech
originalTitle: "IOS Mobile App Security: Best Practices for iOS Mobile Developers."
link: "https://medium.com/@pratap89singh/ios-mobile-app-security-best-practices-for-ios-mobile-developers-a7e9375d40be"
---



![image](/assets/img/2024-06-19-IOSMobileAppSecurityBestPracticesforiOSMobileDevelopers_0.png)

iOS 개발자들은 OWASP Top 10을 기반으로 코드 보안, 데이터 저장 및 통신 보안에 중점을 두어야 합니다. iOS는 안드로이드보다 취약성이 적지만 여전히 보안 문제에 직면합니다. 코드와 데이터 통신을 안전하게 보호하여 변조 및 무단 코드 접근을 방지하는 것은 개발자/조직의 책임입니다.

개발자가 주의를 기울여야 할 초보 수준의 위협을 다루기 위해 모든 iOS 개발자는 다음을 주의깊게 살펴봐야 합니다:

# 1. 화면 녹화 및 화면 캡처:


<div class="content-ad"></div>

공격자가 로그인 페이지와 입력된 사용자 이름 및 비밀번호를 캡처할 수 있는 민감한 화면을 녹화할 수 있습니다. 비디오 스트리밍 애플리케이션에서는 사용자가 유료 비디오 콘텐츠를 스트리밍하고 녹화할 수 있습니다.
은행 애플리케이션에서 스크린샷이나 화면 녹화가 진행되면 민감한 거래 세부 정보가 노출될 수 있는 높은 위험이 있습니다.

우리는 UIScreen.capturedDidChangeNotification을 관찰하고 UIScreen.main.isCaptured를 확인할 수 있습니다.

NotificationCenter를 사용하여 isCaptured의 변경 사항을 관찰합니다:

```js
NotificationCenter.default.addObserver(self, selector: #selector(screenCaptureDidChange),
                                       name: UIScreen.capturedDidChangeNotification,
                                       object: nil)
```

<div class="content-ad"></div>

알림 처리를 담당하는 함수입니다.

```js
@objc func screenCaptureDidChange() {
    print("screenCaptureDidChange.. isCapturing: \(UIScreen.main.isCaptured)")
    
    if UIScreen.main.isCaptured {
        //TODO: They started capturing..
        print("screenCaptureDidChange - 녹화 중입니다.")
    } else {
        //TODO: They stopped capturing..
        print("screenCaptureDidChange - 녹화가 중지되었습니다.")
    }
}
```

# 2. 약한 탈옥 탐지:

중요한 점은 탈옥된 기기에서 응용 프로그램 로직 및 동작이 손상될 수 있고, 이는 응용 프로그램을 공격에 노출시킬 수 있습니다. 그러나 어떤 해커라도 이러한 기본 보안 확인을 우회할 수 있음을 인식하는 것이 중요합니다. 따라서 탈옥 탐지 방법에만 의존하여 응용 프로그램의 안전을 보장하는 것이 충분하지 않을 수 있다는 것을 명심하는 것이 좋습니다.

<div class="content-ad"></div>

다음 텍스트는 기기가 탈옥되었는지 여부를 식별하는 데 관련된 지침 또는 지침으로 보입니다. 이 식별에 도움이 되는 세 가지 테스트가 포함되어 있습니다:

- 기기가 탈옥되었는지 여부를 식별하는 한 가지 방법은 탈옥된 기기에 설치된 고유한 파일 및 응용 프로그램을 확인하는 것입니다. 개발자는 이 테스트를 사용하여 파일 시스템에서 이러한 파일을 찾을 수 있습니다.

```js
private var filesPathToCheck: [String] {
    
    return ["/private/var/lib/apt",
            "/Applications/Cydia.app",
            "/private/var/lib/cydia",
            "/private/var/tmp/cydia.log",
            "/Applications/RockApp.app",
            "/Applications/Icy.app",
            "/Applications/WinterBoard.app",
            "/Applications/SBSetttings.app",
            "/Applications/blackra1n.app",
            "/Applications/IntelliScreen.app",
            "/Applications/Snoop-itConfig.app",
            "/usr/libexec/cydia/",
            "/usr/sbin/frida-server",
            "/usr/bin/cycript",
            "/usr/local/bin/cycript",
            "/usr/lib/libcycript.dylib",
            "/bin/sh",
            "/usr/libexec/sftp-server",
            "/usr/libexec/ssh-keysign",
            "/Library/MobileSubstrate/MobileSubstrate.dylib",
            "/bin/bash",
            "/usr/sbin/sshd",
            "/etc/apt",
            "/usr/bin/ssh",
            "/bin.sh",
            "/var/checkra1n.dmg",
            "/System/Library/LaunchDaemons/com.saurik.Cydia.Startup.plist",
            "/System/Library/LaunchDaemons/com.ikey.bbot.plist",
            "/Library/MobileSubstrate/DynamicLibraries/LiveClock.plist",
            "/Library/MobileSubstrate/DynamicLibraries/Veency.plist"]
}

func isJailBrokenFilesPresentInTheDirectory() -> Bool{
        var checkFileIfExist: Bool = false
        filesPathToCheck.forEach {
            checkFileIfExist =  fm.fileExists(atPath: $0) ? true : false
            if checkFileIfExist{
                return
            }
        }
        
        return checkFileIfExist
    }
```

2. 응용 프로그램이 탈옥되었는지 여부를 확인하는 또 다른 방법은 샌드박싱 규칙을 준수하는지 여부를 확인하는 것입니다. 파일이 응용 프로그램 번들 외부에서 수정될 수 있는지 확인함으로써 이를 식별할 수 있습니다. 개발자는 응용 프로그램이 샌드박싱 규칙을 준수하는지 확인하려면 이 테스트를 사용할 수 있습니다.

<div class="content-ad"></div>

```swift
func canEditSandboxFilesForJailBreakDetection() -> Bool {
    let jailBreakTestText = "JailBreak 테스트"
    do {
        try jailBreakTestText.write(toFile: "/private/jailBreakTestText.txt", atomically: true, encoding: String.Encoding.utf8)
        return true
    } catch {
        let resultJailBroken = isJailBrokenFilesPresentInTheDirectory()
        return resultJailBroken
    }
}
```

3. 어플리케이션이 Cydia URL scheme (Cydia://)을 호출했을 때 성공하면, 기기가 탈옥되었다는 것을 의미합니다. 이 테스트는 개발자가 이 확인을 수행했는지를 확인합니다.

```swift
// JailBreak 감지를 위해 확장된 프로토콜 함수
func assignJailBreakCheckType() -> Bool {
    // 시뮬레이터에서 실행 중이라면 앱의 일반 흐름을 따릅니다
    if !isSimulator {
        // 기기에 Cydia 앱이 설치되어 있는지 확인
        guard UIApplication.shared.canOpenURL(URL(string: "cydia://")!) else {
            return false
        }
        return true
    }
    return true
}
```

# 3. 키체인 데이터 보호:

<div class="content-ad"></div>

JailBroken 기기에서 취약한 접근성 옵션을 가진 키 체인 항목이 다른 애플리케이션이나 물리적으로 접근하는 공격자에게 쉽게 노출될 수 있습니다. 그러나 개발자는 이 보안 위험을 완화하기 위해 여러 작업을 선택할 수 있습니다.

- kSecAttrAccessibleWhenUnlocked
- kSecAttrAccessibleAfterFirstUnlock
- kSecAttrAccessibleAlways
- kSecAttrAccessibleWhenUnlockedThisDeviceOnly
- kSecAttrAccessibleAfterFirstUnlockThisDeviceOnly
- kSecAttrAccessibleAlwaysThisDeviceOnly

'kSecAttrAccessibleWhenUnlocked'와 같이 가장 쉬운 또는 취약할 가능성이 있는 옵션을 선택하는 것은 잠재적인 보안 위험을 유발할 수 있습니다.

`kSecAttrAccessibleAfterFirstUnlock` 또는 `kSecAttrAccessibleAfterFirstUnlockThisDeviceOnly` 모드는 애플리케이션이 백그라운드 처리를 위해 키체인 항목이 필요한 경우에만 사용해야 합니다.

<div class="content-ad"></div>

# 4. 파일 데이터 보호:

새 파일을 저장할 때, 개발자는 다음 옵션 중 하나를 선택하여 데이터 보호에 더 나은 이용 방법을 선택할 수 있습니다:

- atomic:
데이터를 먼저 보조 파일로 작성한 다음 쓰기가 완료되면 원본 파일을 보조 파일로 교체하는 옵션입니다.
- withoutOverwriting:
파일에 데이터를 작성하려고 하지만 대상 파일이 이미 존재하는 경우 실패하고 에러가 발생하는 옵션입니다.
- noFileProtection:
파일을 쓸 때 암호화하지 않는 옵션입니다.
- completeFileProtection:
장치의 잠금이 해제된 상태에서만 파일에 접근할 수 있는 옵션입니다.
- completeFileProtectionUnlessOpen:
장치가 잠금 해제된 상태이거나 파일이 이미 열려 있는 경우에만 파일에 접근할 수 있는 옵션입니다.
- completeFileProtectionUntilFirstUserAuthentication:
사용자가 장치를 처음 잠금 해제한 후에만 파일에 접근할 수 있는 옵션입니다.
- fileProtectionMask:
시스템이 데이터에 할당하는 파일 보호 옵션을 결정할 때 사용되는 옵션입니다.

'NSFileProtectionNone'과 같이 가장 쉬운 또는 취약성이 높은 옵션을 선택하는 것은 잠재적인 보안 위험을 초래할 수 있습니다.

<div class="content-ad"></div>

모든 파일에 데이터 보호를 제공하려면 'completeFileProtectionUnlessOpen' 및 'completeFileProtectionUntilFirstUserAuthentication'을 사용하는 것이 좋습니다.

첫 번째 쓰기 시 파일 암호화

```js
do {
    try data.write(to: fileURL, options: .completeFileProtection)
}
catch {
   // 오류 처리.
}
```

기존 파일의 경우 NSFileManager/FileManager 또는 NSURL을 사용할 수 있습니다:

<div class="content-ad"></div>

```js
FileManager.default.setAttributes([.protectionKey: .completeFileProtection], ofItemAtPath: fileURL.path)
// 또는
// `fileProtection` 속성은 읽기 전용이므로 `NSURL`로 캐스트합니다.
(fileURL as NSURL).setResourceValue(URLFileProtection.complete, forKey: .fileProtectionKey)
```

Core Data를 사용하면 영구 저장소를 추가할 때 보호 유형을 전달할 수 있습니다:

```js
persistentStoreCoordinator.addPersistentStore(ofType: NSSQLiteStoreType, configurationName: nil, at: storeURL, options: [NSPersistentStoreFileProtectionKey: .completeFileProtection])
```

# 5. 암호 필드 표시/숨기기:


<div class="content-ad"></div>

어플리케이션 비밀번호가 로그인 화면 및 기타 민감한 정보 화면을 녹화하는 동안 노출될 수 있습니다.

녹화된 화면을 식별하면, 패스워드 텍스트 필드 또는 다른 민감한 텍스트 필드에 '마스크'를 사용하여 데이터 보안을 보호해주세요.
어플이 녹화 중일 때는 키보드를 숨기거나 없애주세요.

```js
if(isRecording){
    let maskView = UIView(frame: CGRect(x: 64, y: 0, width: 128, height: 128))
    maskView.backgroundColor = .blue
    maskView.layer.cornerRadius = 64
    yourView.mask = maskView
}
```

# 6. SSL 인증서 만료일:

<div class="content-ad"></div>

일반적으로 인증서는 1년 동안 유효하며 작업을 계속하려면 갱신이 필요합니다. 갱신이 제때 완료되지 않으면 앱이 작동을 멈출 수 있습니다.

만료 날짜 이전에 인증서를 갱신하고 IPA를 업데이트하여 원활한 앱 기능을 보장하는 것이 좋습니다. 또한, 매번 실행할 때 인증서 만료를 확인하고 만료 날짜 몇 일 전에 새로운 인증서를 요청하는 것이 중요합니다.

# 7. 인증서 핀 없음:

공격자가 해당 도메인에 대한 유효한 인증서를 만들 수 있다면 사용자를 속여 안전한 웹사이트를 방문하고 있는 것으로 오해시킬 수 있습니다. 실제로는 사용자가 사칭된 웹사이트로 연결되는 상황이 됩니다. 이 공격은 `중간자 공격(Man-in-the-Middle attack)`으로 알려져 있으며 공격자가 사용자와 웹사이트 사이의 트래픽을 가로채고 해독하여 민감한 정보를 노출시키고 다른 취약점을 악용할 수 있습니다.

<div class="content-ad"></div>

아래는 delegate가 구현해야 하는 내용입니다:

URLSession:task:didReceiveChallenge:completionHandler:
모든 연결을 인증서 인증을 통해 처리해야 합니다.

```js
func urlSession(_ session: URLSession, didReceive challenge: URLAuthenticationChallenge, completionHandler: @escaping (URLSession.AuthChallengeDisposition, URLCredential?) -> Void) {
  if let trust = challenge.protectionSpace.serverTrust, SecTrustGetCertificateCount(trust) > 0 {
    if let certificate = SecTrustGetCertificateAtIndex(trust, 0) {
      let data = SecCertificateCopyData(certificate) as Data
        if certificates.contains(data) {
          completionHandler(.useCredential, URLCredential(trust: trust))
          return
         } else {
           //TODO: Throw SSL Certificate Mismatch
         }
      }
  }
  completionHandler(.cancelAuthenticationChallenge, nil)
}
```

# 8. HTTP 요청의 사용법

<div class="content-ad"></div>

애플리케이션은 안전하지 않은 통신 채널(HTTP)을 사용합니다. 이는 피해자와 동일한 네트워크에 있는 공격자가 공격자가 제어하는 서버로의 301 HTTP 리디렉션 응답을 주입하여 중간자 공격을 수행할 수 있다는 것을 의미합니다.

## 9. 개인 정보 자원 액세스:

애플리케이션은 연락처, 위치, 블루투스 장치 ID, 카메라 및 마이크와 같은 개인 장치 및/또는 사용자 자원에 액세스할 수 있습니다. 그러나 이는 데이터가 인터넷을 통해 평문으로 전송될 경우 데이터 유출로 이어질 수 있습니다. 이를 방지하기 위해 개발자는 이러한 자원에 대한 액세스가 안전한 정책을 따르도록 보장해야 합니다. 즉, 데이터를 서버로 전송하기 전에 암호화한다는 것입니다. 또한, 자원에 안전하지 않은 액세스 방식을 사용하는 써드파티 라이브러리가 사용 중이 아닌지 확인하는 것이 중요합니다. 개인 자원에 대한 모든 액세스를 검증하고 보안 정책을 준수하도록 강제해야 합니다. 광고 식별자나 ABAddressBookRef 등을 사용하는 경우와 같이요.

<div class="content-ad"></div>

# 10. 디버그 로그 활성화:

릴리스 빌드에서는 메소드 완료 세부 정보와 민감한 정보를 출력하는 디버그 로그를 피해야 합니다.

#ifDef DEBUG 또는 디버그 빌드에서만 debug=1을 활성화하십시오. NSLog는 엄격히 피해야 합니다.

```js
#ifdef DEBUG
print("로그 문구")
#endif
```