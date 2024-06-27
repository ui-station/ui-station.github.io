---
title: "Firebase로 안드로이드 애플리케이션에서 안전한 SMS 인증 방법"
description: ""
coverImage: "/assets/img/2024-06-27-SecureSMSVerificationinAndroidApplicationswithFirebase_0.png"
date: 2024-06-27 19:22
ogImage: 
  url: /assets/img/2024-06-27-SecureSMSVerificationinAndroidApplicationswithFirebase_0.png
tag: Tech
originalTitle: "Secure SMS Verification in Android Applications with Firebase"
link: "https://medium.com/@backtosyns/secure-sms-verification-in-android-applications-with-firebase-ac5c249c5a97"
---


![이미지](/assets/img/2024-06-27-SecureSMSVerificationinAndroidApplicationswithFirebase_0.png)

안녕하세요! 이 기사에서는 안드로이드 애플리케이션에서 Firebase의 SMS 인증 기능을 사용하여 사용자 인증하는 방법을 단계별로 설명하겠습니다. SMS 인증은 사용자의 전화번호를 확인하고 안전한 로그인을 보장하는 효과적인 방법입니다. 이 예에서는 프로젝트를 만들고 필요한 모든 단계를 거칠 것입니다.

# 1. Firebase 프로젝트 만들기

## Firebase 콘솔 설정

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

Firebase 콘솔에 로그인해 주세요:

- Firebase 콘솔로 이동합니다.
- Google 계정으로 로그인합니다.

## 새 프로젝트 만들기:

- Firebase 콘솔에서 “프로젝트 추가” 버튼을 클릭합니다.
- 프로젝트 이름을 입력하고 “계속”을 클릭합니다.
- Google 애널리틱스를 활성화하라는 요청이 표시될 수 있습니다. 원하는 옵션을 선택하고 “계속”을 클릭합니다.

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

## 프로젝트 설정 완료:

- 프로젝트 생성 프로세스가 완료되면 “프로젝트 관리” 버튼을 클릭하여 프로젝트 페이지로 이동합니다.
- App Check 옵션을 활성화하고 이 섹션에서 앱을 등록합니다. Firebase App Check를 활성화하여 Play Integrity API를 사용하면 앱의 보안을 강화하고 Firebase 서비스에 대한 무단 액세스를 방지할 수 있습니다.

# 2. Firebase에 Android 앱 추가하기

Firebase에 앱 추가하기:

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

- 프로젝트 페이지에서 상단의 "프로젝트 개요" 섹션에서 Android 아이콘을 클릭하세요.
- 패키지 이름 (예: com.ek.firebasesmssample), 앱 별명 및 SHA-1 인증서 지문을 입력해주세요. SHA-1 인증서 지문을 얻으려면 다음 단계를 따라주세요:
- Android Studio에서 터미널을 열고 다음 명령을 입력하세요:

```js
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

- 출력에서 SHA1 값이 인증서 지문입니다.
- "앱 등록" 버튼을 클릭하세요.

## Google 서비스 JSON 파일 다운로드하기:

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

- google-services.json 파일을 다운로드하여 Android 프로젝트의 앱 디렉토리에 추가해주세요.

## 3. Firebase SDK를 프로젝트에 추가하기

Gradle 파일 구성하기:

- 프로젝트 수준의 build.gradle 파일에 다음 라인을 추가해주세요:

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

```gradle
classpath 'com.google.gms:google-services:4.3.3'
```

다음 라인을 앱 수준의 build.gradle 파일에 추가하세요:

```gradle
apply plugin: 'com.google.gms.google-services'

dependencies {
    implementation platform('com.google.firebase:firebase-bom:26.1.0')
    implementation 'com.google.firebase:firebase-auth'
    implementation 'com.google.firebase:firebase-analytics'
}
```

# 4. Firebase 인증 활성화하기

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

인증 설정하기:

- Firebase 콘솔의 왼쪽 메뉴에서 "인증" 섹션으로 이동합니다.
- "로그인 방법" 탭을 클릭합니다.
- 전화 번호 로그인 방법을 찾아 "활성화"를 클릭합니다.
- 선택적으로 메시지 템플릿을 사용자 정의할 수 있습니다.
- "저장" 버튼을 클릭합니다.

# 5. 안드로이드 애플리케이션 코딩

## 프로젝트 구조 및 UI 디자인

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

우선, 안드로이드 프로젝트를 만들고 필요한 파일을 설정하세요.

activity_login.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="16dp">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="SMS 인증을 위한 전화번호를 입력하세요."
        android:textSize="12sp" />

    <com.google.android.material.textfield.TextInputLayout
        android:id="@+id/phone_number_layout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="128dp">

        <EditText
            android:id="@+id/phone_number_input"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="전화번호"
            android:inputType="phone" />
    </com.google.android.material.textfield.TextInputLayout>

    <Button
        android:id="@+id/login_button"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="48dp"
        android:paddingVertical="12dp"
        android:textSize="16sp"
        android:textColor="@color/white"
        android:backgroundTint="@color/red"
        android:text="로그인 →" />

</LinearLayout>
```

LoginActivity.kt

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

이제 LoginActivity 클래스를 만들고 아래 코드를 추가해주세요:

```js
import android.os.Bundle
import android.text.Editable
import android.text.TextWatcher
import android.util.Log
import android.widget.Button
import android.widget.EditText
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import androidx.core.widget.doAfterTextChanged
import com.google.android.material.textfield.TextInputLayout
import com.google.firebase.FirebaseApp
import com.google.firebase.FirebaseException
import com.google.firebase.appcheck.FirebaseAppCheck
import com.google.firebase.appcheck.playintegrity.PlayIntegrityAppCheckProviderFactory
import com.google.firebase.auth.FirebaseAuth
import com.google.firebase.auth.PhoneAuthCredential
import com.google.firebase.auth.PhoneAuthProvider
import java.util.concurrent.TimeUnit
import java.util.regex.Pattern

class LoginActivity : AppCompatActivity() {

    private lateinit var phoneNumberInput: EditText
    private lateinit var loginButton: Button
    private lateinit var phoneNumberLayout: TextInputLayout

    private lateinit var auth: FirebaseAuth

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_login)

        phoneNumberInput = findViewById(R.id.phone_number_input)
        loginButton = findViewById(R.id.login_button)
        phoneNumberLayout = findViewById(R.id.phone_number_layout)

        auth = FirebaseAuth.getInstance()
        FirebaseApp.initializeApp(this)
        val firebaseAppCheck = FirebaseAppCheck.getInstance()
        firebaseAppCheck.installAppCheckProviderFactory(
            PlayIntegrityAppCheckProviderFactory.getInstance()
        )
        phoneNumberInput.addTextChangedListener(object : TextWatcher {
            private var current = ""
            override fun afterTextChanged(s: Editable?) {
                if (s.toString() != current) {
                    phoneNumberInput.removeTextChangedListener(this)

                    val cleanString = s.toString().replace(" ", "").replace("+90", "")
                    val formatted = cleanString.chunked(3).joinToString(" ")

                    if (cleanString.length > 6) {
                        val part1 = cleanString.substring(0, 3)
                        val part2 = cleanString.substring(3, 6)
                        val part3 = cleanString.substring(6)
                        current = "+90 $part1 $part2$part3"
                    } else {
                        current = "+90 $formatted"
                    }

                    phoneNumberInput.setText(current)
                    phoneNumberInput.setSelection(current.length)

                    phoneNumberInput.addTextChangedListener(this)
                }
            }

            override fun beforeTextChanged(s: CharSequence?, start: Int, count: Int, after: Int) {}

            override fun onTextChanged(s: CharSequence?, start: Int, before: Int, count: Int) {}
        })

        loginButton.setOnClickListener {
            val phoneNumber = phoneNumberInput.text.toString()
            if (isValidPhoneNumber(phoneNumber)) {
                startPhoneNumberVerification(phoneNumber)
            } else {
                Toast.makeText(this, "Please enter a valid phone number", Toast.LENGTH_SHORT).show()
            }
        }
    }

    private fun isValidPhoneNumber(phoneNumber: String): Boolean {
        val pattern = Pattern.compile("^\\+?[1-9]\\d{1,14}\$")
        return pattern.matcher(phoneNumber.replace(" ", "")).matches()
    }

    private fun startPhoneNumberVerification(phoneNumber: String) {
        val callbacks = object : PhoneAuthProvider.OnVerificationStateChangedCallbacks() {

            override fun onVerificationCompleted(credential: PhoneAuthCredential) {
                // 인증이 성공했을 때 실행되는 콜백입니다.
                // 여기에서 로그인 작업을 수행할 수 있습니다.
                signInWithPhoneAuthCredential(credential)
            }

            override fun onVerificationFailed(e: FirebaseException) {
                // 인증이 실패했을 때 실행되는 콜백입니다.
                // 오류 메시지를 표시합니다.
                Log.e("onVerificationFailed", e.message ?: "오류가 발생했습니다")
            }

            override fun onCodeSent(
                verificationId: String,
                token: PhoneAuthProvider.ForceResendingToken
            ) {
                Log.e("onCodeSent", "verificationId: $verificationId")
                // 인증 코드가 전송될 때 실행되는 콜백입니다.
                // 수신한 코드를 사용하여 자격 증명을 생성할 수 있습니다.
                val credential = PhoneAuthProvider.getCredential(verificationId, "123456")
                signInWithPhoneAuthCredential(credential)
            }
        }

        // 핸드폰 번호와 콜백을 사용하여 인증 프로세스 시작
        PhoneAuthProvider.getInstance().verifyPhoneNumber(
            phoneNumber, // 핸드폰 번호
            60, // 시간 제한
            TimeUnit.SECONDS, // 시간 제한 단위
            this, // 액티비티 (또는 프래그먼트)
            callbacks
        )
    }

    private fun signInWithPhoneAuthCredential(credential: PhoneAuthCredential) {
        auth.signInWithCredential(credential)
            .addOnCompleteListener(this) { task ->
                if (task.isSuccessful) {
                    // 로그인 성공
                    val user = task.result?.user
                    val uid = user?.uid // 사용자 ID
                    val phoneNumber = user?.phoneNumber // 핸드폰 번호
                    val providerId = user?.providerId // 제공자 ID

                    sendUserToBackend(uid)
                } else {
                    Log.e("signInWithCredential", "signInWithCredential:failure", task.exception)
                    // 로그인 실패
                }
            }
    }

    private fun sendUserToBackend(uid: String?) {
        Log.e("sendUserToBackend", "UID: $uid")
        // 이 함수는 사용자의 UID를 백엔드로 전송하는 데 사용할 수 있습니다.
    }
}
```

`startPhoneNumberVerification` 메서드에서 전화번호 확인 프로세스 중 발생하는 이벤트들이 `onVerificationCompleted`, `onVerificationFailed`, `onCodeSent`와 같은 콜백을 사용하여 정의되어 있습니다.

`signInWithPhoneAuthCredential` 메서드는 Firebase 인증을 사용하여 로그인 작업을 수행합니다. 확인이 성공적일 경우, 사용자 정보를 가져와 백엔드로 전송할 수 있습니다.

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


![Secure SMS Verification in Android Applications with Firebase](/assets/img/2024-06-27-SecureSMSVerificationinAndroidApplicationswithFirebase_1.png)

위 모든 단계를 완료한 후에는 Firebase로부터 SMS를 수신할 것입니다. SMS를 통해 받은 코드를 입력하면 signInWithPhoneAuthCredential 메서드가 이를 확인하고 응용 프로그램 흐름이 계속됩니다.

# 결론

본 문서에서 안내한 단계를 따라 Android 애플리케이션에서 Firebase를 사용하여 안전한 SMS 확인을 구현할 수 있게 됩니다. 이 방법은 사용자 인증의 보안성을 향상시키는데 그치지 않고 사용자들에게 원활한 로그인 경험을 제공합니다.


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

SMS 인증을 구현하면 무단 액세스가 크게 줄어들고, 앱에 로그인할 수 있는 것은 정당한 사용자뿐이라는 것을 보장할 수 있습니다. 또한 Firebase의 강력한 기능과 쉬운 통합을 통해, 인증 및 보안을 관리하는 강력한 도구로 활용할 수 있습니다.

# 참고 자료

- Firebase 문서
- Google Play 무결성 API
- SHA-1 인증서 지문
- Firebase용 Gradle 구성

참고: 개발 단계에서 Google이 자동으로 reCaptcha 서비스를 활성화할 수 있으며, SMS 인증을 수행하는 동안 reCaptcha 화면을 볼 수 있습니다. 주제에서 벗어나지 않도록 자세히 다루지 않았습니다. 더 자세한 정보는 여기에서 확인할 수 있습니다.

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

Note-2: 현재 Firebase를 통해 보내는 메시지 템플릿을 사용자 정의하는 것은 불가능합니다. 사용자 정의 메시지 유형을 사용하려면 다른 공급업체를 사용해야 합니다.

이 기사가 안드로이드 애플리케이션에서 Firebase를 사용하여 SMS 인증을 구현하는 데 도움이 되기를 바랍니다. 궁금한 점이 있거나 추가 지원이 필요하면 아래에 댓글을 남기세요.

![image](https://miro.medium.com/v2/resize:fit:1200/0*vvlpXIMRKoxTUlrJ.gif)