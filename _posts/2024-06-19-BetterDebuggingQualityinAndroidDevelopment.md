---
title: "Android 개발에서 향상된 디버깅 품질"
description: ""
coverImage: "/assets/img/2024-06-19-BetterDebuggingQualityinAndroidDevelopment_0.png"
date: 2024-06-19 13:56
ogImage: 
  url: /assets/img/2024-06-19-BetterDebuggingQualityinAndroidDevelopment_0.png
tag: Tech
originalTitle: "Better Debugging Quality in Android Development"
link: "https://medium.com/@sevbanbuyer/better-debugging-quality-in-android-development-4dda79483225"
---



![Better Debugging Quality in Android Development](/assets/img/2024-06-19-BetterDebuggingQualityinAndroidDevelopment_0.png)

개발 중에는 때때로, 이 검사 또는 등록 양식을 한 번 더 다시 쓰지 않으면 안 되는 상황이 발생합니다. 와락그러면서도 작은 버그를 찾기 위해 그 순응하지 않는 작은 고집스러운 버그를 발견하게 됩니다. 바로 이 점 때문에 우리에게 현저한 피해를 줄 수 있다는 것을 잊지 마세요. 그러므로 더 이상 그만 하기로 해야 합니다.

이 기사에서는 개발자로서 우리에게 매우 보편적인 보스 같은 여정을 겪어볼 것입니다. 지금 우리는 왼쪽, 오른쪽, 여기저기에 명령을 내리는 것을 좋아하는 우리 개발자들에게 익숙한 이야기죠 😀.

오늘 하루, 우리는 테스트 디바이스나 에뮬레이터를 제어하는 것뿐만 아니라 그 양식을 여러 번 작성하는 본능적 욕구도 제어할 것입니다.


<div class="content-ad"></div>

당신은 개발자로서. 위의 텍스트를 친근한 어조로 한국어로 번역해주세요.

"어떤가요??"라고 물을 수 있어요. 우리는 adbcommands를 사용할 거에요. ADB(Android Debug Bridge)라는 것은 안드로이드 장치나 에뮬레이터를 제어하기 위한 명령줄 도구에요. "안드로이드 디버그..." "커마..."만 듣고 어딘가로 가지 마세요. 약간 지루해 보일 수도 있지만, 제가 가능한 한 재미있게 만들어 드릴게요.

사실 마지막에는 이 명령을 한 번, 두 번, 세 번 정도만 사용할 거에요... 우리가 선택한 바로 가기로 이 명령들을 사용할 필요가 없게 할 거에요. (또는 경우에 따라 윈도우를 사용한다면 일명 하나의 명령만 남겨두게 할 거에요. 🙂).

# 사용자 정의 가능한 입력 플로우 스크립트 생성

우선, 장치/에뮬레이터와 상호작용할 명령어들이 여기 있어요:

<div class="content-ad"></div>

```js
# 호스트 안드로이드 장치의 터미널에서 명령을 열거나 실행합니다.
adb shell

# 현재 활성화된 TextField에 지정된 문자열을 입력합니다.
adb shell input text "Hello World!"

# 장치 화면의 지정된 좌표를 탭합니다.
adb shell input tap 700 1500
```

지금 당신은 아마 "정적 좌표를 사용하여 텍스트 필드를 찾을까?"라고 생각할 수 있습니다. 전혀, 그렇지 않습니다. 하지만 당신이 자신을 어렵게 만들고 싶다면 사용할 수 있습니다 :D. 화면에 기대되는 UI 요소의 좌표를 찾을 때, 우리는 uiautomator의 덤프를 사용할 것입니다. 이를 다운로드하려면 Python3가 필요합니다.

```js
adb exec-out uiautomator dump /dev/tty
```

이 명령어가 마법이 시작되는 곳입니다. UI Automator는 XML 형식의 현재 화면 보기 계층에 대한 덤프를 생성합니다. 이 명령을 입력하면 이 파일이 터미널에 표시될 것입니다. 그리고 우리가 관심 있는 부분은 실제로 이 파일의 각 요소의 [bounds] 태그이며, 다음은 그 노드 중 하나의 서식이 적용된 버전입니다.


<div class="content-ad"></div>


<img src="/assets/img/2024-06-19-BetterDebuggingQualityinAndroidDevelopment_1.png" />

그렇게 말하니까 아마 이해가 가실 거에요. 우리는 각 요소의 경계를 가져오기 위한 파서를 만들어야 합니다. 물론 원하는 때마다 관련 요소에 접근 하는 방법은 그들의 이름을 호출하는 것입니다 :). 아래 스크립트에서 우리는 효과적으로 해당 목표를 달성하기 위해 터미널 명령을 사용합니다.

```js
# UI 요소를 탭하는 함수 (요소가 존재하는 경우)
function tapIfExists() {
    # UI 요소의 좌표 가져오기
    coords=$(getCoords "${1}")
    
    # 좌표가 찾아졌는지 확인
    if [[ "$coords" != "-" ]]; then
        # adb를 사용하여 좌표 탭하기
        adb shell input tap "$coords"
    fi
}

# 텍스트를 기반으로 UI 요소의 좌표를 가져오는 함수
function getCoords() {
    # UI 계층 구조를 덤프하고 변수에 저장
    fastdump=$(/usr/bin/python3 -c "import uiautomator2 as u2;d=u2.connect(); out=d.dump_hierarchy(); print(out)")
    
    # 지정된 텍스트가 있는 요소가 있는지 확인
    present=$(echo "$fastdump" | grep "text=\"${1}\"")
    
    # 요소를 찾은 경우
    if [[ $? -eq 0 ]]; then
        # 덤프에서 좌표 추출
        arr=($(echo "$fastdump" | sed 's/></>\n</g' | grep "text=\"${1}\"" | grep -o "\[.*\]" | sed 's/\]\[/,/g' | sed 's/\]//g;s/\[//g;s/,/\n/g'))
        
        # 중앙 x좌표 계산
        x=$(echo "(${arr[0]}+${arr[2]})/2" | bc)
        
        # 중앙 y좌표 계산
        y=$(echo "(${arr[1]}+${arr[3]})/2" | bc)
        
        # 좌표 출력
        echo "$x $y"
    else
        # 요소를 찾지 못한 경우 "-" 출력
        echo "-"
    fi
}
```

이렇게 하면 모든 것이 훨씬 더 쉬워집니다. 예를 들어 로그인 플로우를 만들었습니다:


<div class="content-ad"></div>

```javascript
# ...
# 파일 끝에 사용자 정의 로직을 추가하는 중...

tapIfExists "E-mail"
adb shell input text "sevbanbuyer@gmail.com"
tapIfExists "Password"
adb shell input text "asdasdfasd"
tapIfExists "SIGN IN"
```

이 스크립트로 우리는 등록 과정을 단 한 개의 명령으로 줄였어요: ./`your-script-file-name` 하지만 아직 끝나지 않았습니다. 만약 Mac을 사용하고 계시다면 입력 과정에 대한 단축키를 만들 수 있어요. 이를 위해 먼저 Mac의 Automator 앱으로 Applescript 워크플로우를 만들어야 해요.

<img src="/assets/img/2024-06-19-BetterDebuggingQualityinAndroidDevelopment_2.png" />

<img src="/assets/img/2024-06-19-BetterDebuggingQualityinAndroidDevelopment_3.png" />


<div class="content-ad"></div>

```js
# Automator의 applescript에 다음을 넣으세요       
on run {input, parameters}
 do shell script "<path-to-your-script>"
end run
```

```js
#!/bin/bash

ADB_PATH="<adb의 전체 경로>" # 주의 !

function tapIfExists(){
    coords=$(getCoords "${1}")
    if [[ "$coords" != "-" ]]; then
        $ADB_PATH shell input tap "$coords" # 주의 !
    fi
}

function getCoords(){
    fastdump=$(/usr/bin/python3 -c "import uiautomator2 as u2;d=u2.connect(); out=d.dump_hierarchy(); print(out)")
    present=$(echo "$fastdump" | grep "text\=\"${1}\"")
    if [[ $? -eq 0 ]]; then
        arr=($(echo "$fastdump" | sed 's/></>\n</g' | grep "text\=\"${1}\"" | grep -o "\[.*\]" | sed 's/\]\[/,/g' | sed 's/\]//g;s/\[//g;s/,/\n/g'))
        x=$(echo "(${arr[0]}+${arr[2]})/2" | bc)
        y=$(echo "(${arr[1]}+${arr[3]})/2" | bc)
        echo "$x $y"
    else
        echo "-"
    fi
}

tapIfExists "이메일"
$ADB_PATH shell input text "sevbanbuyer@gmail.com" # 주의 !
tapIfExists "암호"
$ADB_PATH input text "asdasdfasd"  # 주의 !
tapIfExists "로그인"
```

Applescript에 완전한 경로가 포함된 스크립트를 제공하면, Settings에서 `Keyboard` Keyboard Shortcuts  `Services` General로 이동하고 그 섹션에 스크립트가 표시됩니다. 이제 원하는 단축키를 할당할 수 있습니다.

<img src="/assets/img/2024-06-19-BetterDebuggingQualityinAndroidDevelopment_4.png" />


<div class="content-ad"></div>

여기 출력물이 있어요:

![Dumpsys](https://miro.medium.com/v2/resize:fit:784/1*OA1yF70mPJNjQ78lzlFoDg.gif)

# Dumpsys

dumpsys 명령어는 장치에서 실행 중인 모든 시스템 서비스에 대한 진단 정보를 제공합니다.

<div class="content-ad"></div>

위의 표를 다음과 같이 변경해 주세요:


| 주제               | 설명                                      |
|--------------------|-------------------------------------------|
| 활동 및 프래그먼트 | - 활동 및 프래그먼트에 대한 정보          |
| 배터리             | - 배터리 상태 및 관리에 관한 정보          |
| 알람               | - 알람 설정 및 관리에 관한 정보            |
| 패키지 정보       | - 앱 패키지 정보에 대한 정보               |
| 작업 스케줄러     | - 작업 스케줄러 사용법과 정보에 대한 정보 |


이 서비스들과 상호작용하면서 코드를 통해 어떤 일이 벌어지는지를 알아내므로 버그에 대한 효율적인 추측이 가능합니다.

"AlarmManager를 사용하여 알람을 설정했는데, 정말 설정되었을까요?"

<div class="content-ad"></div>

"조각을 확장했지만 아무것도 보이지 않아요. 제가 제대로 한 걸까요?"

- 리뷰 중인 앱을 분석하고 실제로 무엇을 보고 있는지 확인하기 위해 dumpsysis를 사용하는 것이 좋습니다.

다음은 명령어입니다:

```js
adb shell dumpsys <service>

adb shell dumpsys battery
```

<div class="content-ad"></div>

![이미지](/assets/img/2024-06-19-BetterDebuggingQualityinAndroidDevelopment_5.png)

덤프시스에서 grep을 사용하여 검색할 수 있습니다. 예를 들어:

```js
adb shell dumpsys | grep "ACTIVITY"

adb shell dumpsys | grep "BottomNavigationItemView"
```

# 로컬 파일의 내용 보기/변경

<div class="content-ad"></div>

로컬 스토리지를 다룰 때 종종 어려움을 겪곤 합니다.

- 스토리지 데이터를 쿼리하기 위해 중단점을 설정하거나
- printlines 또는 로그를 추가하는 것은 수정 및 빌드가 필요하여 시간이 많이 소요됩니다.

우리는 이 디버깅 프로세스를 최적화할 수 있는 기회가 있습니다.

- 앱의 내부 데이터는 data/path에 저장됩니다.
- 이론적으로 우리는 cd, ls, echo, nano 등의 일반 터미널 명령어를 사용하여 기기에서 이 데이터를 볼 수 있지만 실제로는 이와 같은 문제가 발생합니다.

<div class="content-ad"></div>


![Image](/assets/img/2024-06-19-BetterDebuggingQualityinAndroidDevelopment_6.png)

안드로이드는 앱의 리소스에 쉽게 액세스할 수 없도록 허용하지 않는다는 점이 좋은 것 같아요. 

그런데 앱의 내부 데이터의 정확한 경로는 다음과 같아요: /data/user/0/`your.package.name` 그래도 저 정확한 경로를 알아도 들어가지 못하는 걸 보면 다른 방법이 필요할 걸요.

이를 위해 안드로이드의 각 앱이 사실 별칭으로 패키지 이름을 가진 사용자라는 사실을 활용할 거에요:


<div class="content-ad"></div>


![Screenshot 1](/assets/img/2024-06-19-BetterDebuggingQualityinAndroidDevelopment_7.png)

우리는 run-as 명령어를 사용하여 앱이 셀프인 것처럼 속이는 데 사용했습니다. 이제 우리는 앱의 내부 저장소를 lsin 할 수 있고, sharedPref, 데이터베이스 또는 파일과 같은 데이터를 볼 수 있습니다. 이 데이터들은 코드베이스 내에서 파일 API를 사용하여 생성된 것입니다.

![Screenshot 2](/assets/img/2024-06-19-BetterDebuggingQualityinAndroidDevelopment_8.png)

그리고 BUM! 여기가 제 앱의 공유 프리퍼런스 파일이고, onboarding_key와 해당 값을 볼 수 있습니다:


<div class="content-ad"></div>

\<img src="/assets/img/2024-06-19-BetterDebuggingQualityinAndroidDevelopment_9.png" />

이제 이 선호 값을 어떻게 편집할 수 있어서 앱을 다시 빌드하지 않고 변경 사항을 확실히 적용하는 데 시간을 낭비하지 않을 수 있는지 묻게 될 것입니다. 일반적으로 터미널에서 사용하는 텍스트 편집기를 adb 쉘에서 사용할 수는 없지만 여기에 대한 해결책이 있습니다: sedcommand. -iflag로 파일을 직접 덮어쓸 수 있으므로 여기 제 앱의 온보딩 키에 대한 적용입니다:

```js
emu64a:/data/user/0/com.sevban.tradejournal/shared_prefs $ grep "onboarding_key" presentation.MainActivity.xml                                                                          
    <boolean name="onboarding_key" value="true" />
emu64a:/data/user/0/com.sevban.tradejournal/shared_prefs $ 

cat presentation.MainActivity.xml | 
grep "onboarding_key" | 
sed -i "s/true/false/g" presentation.MainActivity.xml
           
emu64a:/data/user/0/com.sevban.tradejournal/shared_prefs $ grep "onboarding_key" presentation.MainActivity.xml                                                                          
    <boolean name="onboarding_key" value="false" />
```

# 결론

<div class="content-ad"></div>

안드로이드 개발에서 디버깅 프로세스를 개선하는 과정은 어렵지 않을 수 있어요. ADB 및 uiautomator와 같은 도구를 활용하여 양식 작성과 같은 반복적인 작업을 자동화하고 앱의 동작에 대한 깊은 통찰력을 얻을 수 있어요.

사용자 정의 입력 흐름 스크립트를 생성하면 UI 상호작용을 자동화하여 디버깅을 더 효율적이고 단조로운 일이 줄어들어요. dumpsys를 사용하고 run-as 및 sed 명령어를 사용하여 로컬 파일에 액세스하면 문제를 진단하고 효과적으로 해결할 수 있어요.

이러한 기술을 활용하면 디버깅 프로세스를 변화시킬 수 있어요. 가능한 경우 자동화 도구를 적극적으로 활용하고 더욱 원활하고 생산적인 개발 경험을 누려보세요.

참고 문헌:

<div class="content-ad"></div>

https://www.youtube.com/watch?v=DcU1czPxQ10&t=1326s