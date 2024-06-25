---
title: "계산기 앱 만들기  Gojek 엔지니어링 부트캠프 첫날"
description: ""
coverImage: "/assets/img/2024-06-22-CalculatorAppGojekEngineeringBootcampDayOne_0.png"
date: 2024-06-22 22:44
ogImage:
  url: /assets/img/2024-06-22-CalculatorAppGojekEngineeringBootcampDayOne_0.png
tag: Tech
originalTitle: "Calculator App — Gojek Engineering Bootcamp Day One"
link: "https://medium.com/@codewithisa/calculator-app-gojek-engineering-bootcamp-day-one-5d56f2a599e5"
---

내가 Gojek Engineering Bootcamp 첫 날에 만든 프로젝트에 대해 이야기하고 싶어. 나는 Calculator 앱을 만들었어. 이 앱은 Android 개발을 위해 학생으로서 주어진 프로젝트 중 일부로, XML 레이아웃 및 Kotlin에 대해 배우는 과정의 일환이었어. 프로젝트를 완료한 지 얼마 되지 않아서, 여전히 신선한 기분이어서 여기에 대해 써 보고 싶어.

![Calculator App Gojek Engineering Bootcamp Day One 0](/assets/img/2024-06-22-CalculatorAppGojekEngineeringBootcampDayOne_0.png)

프로젝트를 마치기 위해, 나는 XML부터 시작했어. 여기서 ConstraintLayout을 사용했어. 이 레이아웃을 사용함으로써 Calculator 단어, 결과 상자, 숫자 입력 등을 자유롭게 배치할 수 있었어. 제목("Calculator"라고 적힌 부분)에는 textStyle를 Bold로 설정한 TextView를 사용했어. 결과 상자에도 TextView를 사용했지만, 여기에는 흥미로운 점이 있어. 결과 텍스트의 글꼴이 다른 텍스트와 다르다는 것을 알 수 있어. 네, 결과 텍스트는 숫자 글꼴을 사용했어. 나는 "download number font ttf"라는 키워드로 구글에서 검색하여 해당 글꼴을 얻었어. ttf는 파일 형식을 나타냈어. 이 글꼴을 다운로드하고(압축 파일이었기 때문에) 추출한 다음 res.font 폴더에 넣었어.

![Calculator App Gojek Engineering Bootcamp Day One 1](/assets/img/2024-06-22-CalculatorAppGojekEngineeringBootcampDayOne_1.png)

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

결과 상자도 다소 독특합니다. 가까이 보면 상자의 텍스트가 아래 오른쪽에 배치되어 있습니다. 이것은 형식 설정 때문입니다. 형식을 지정하지 않으면 텍스트가 왼쪽 상단에 배치됩니다. 결과 상자의 코드는 다음과 같습니다.

```js
<TextView
  android:id="@+id/result"
  android:layout_width="match_parent"
  android:layout_height="120dp"
  android:text="0"
  android:textSize="80dp"
  android:gravity="bottom|right"
  android:fontFamily="@font/calculator_font"
  android:background="@color/cardview_shadow_start_color"
  app:layout_constraintTop_toBottomOf="@id/title"
  app:layout_constraintStart_toStartOf="parent"
></TextView>
```

숫자 입력란에는 EditText를 사용했고, 연산자 버튼(더하기, 빼기 등)은 다음과 같이 제약 조건이 있는 버튼을 사용했습니다.

```js
    <Button
        android:id="@+id/btnPlus"
        android:layout_width="70dp"
        android:layout_height="wrap_content"
        android:text="+"
        app:layout_constraintTop_toBottomOf="@id/secondInput"
        app:layout_constraintStart_toStartOf="parent">

    </Button>

    <Button
        android:id="@+id/btnMinus"
        android:layout_width="70dp"
        android:layout_height="wrap_content"
        android:text="-"
        app:layout_constraintEnd_toStartOf="@id/btnTimes"
        app:layout_constraintTop_toBottomOf="@id/secondInput"
        app:layout_constraintStart_toEndOf="@id/btnPlus">

    </Button>

    <Button
        android:id="@+id/btnTimes"
        android:layout_width="70dp"
        android:layout_height="wrap_content"
        android:text="*"
        app:layout_constraintEnd_toStartOf="@id/btnDivide"
        app:layout_constraintTop_toBottomOf="@id/secondInput"
        app:layout_constraintStart_toEndOf="@id/btnMinus">

    </Button>

    <Button
        android:id="@+id/btnDivide"
        android:layout_width="70dp"
        android:layout_height="wrap_content"
        android:text="/"
        app:layout_constraintTop_toBottomOf="@id/secondInput"
        app:layout_constraintStart_toEndOf="@id/btnTimes"
        app:layout_constraintEnd_toStartOf="@id/btnClear">

    </Button>

    <Button
        android:id="@+id/btnClear"
        android:layout_width="70dp"
        android:layout_height="wrap_content"
        android:text="C"
        app:layout_constraintTop_toBottomOf="@id/secondInput"
        app:layout_constraintEnd_toEndOf="parent"
        android:backgroundTint="#FF0000">
    </Button>
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

좋아요, 레이아웃 작업을 마쳤으니 다음 단계로 진행해봐요. 그 전에 viewBinding을 사용하려면 build.gradle 파일에 something을 추가해야 해요.

```js
    buildFeatures {
        viewBinding = true
    }
```

그런 다음 MainActivity에서, 먼저 XML을 inflate하고 해당 TextView, EditText, Button과 같은 객체를 얻어와요.

```js
var resultTv = binding.result;
var result: Double;

var firstInput = binding.firstInput;
var secondInput = binding.secondInput;

var plusButton = binding.btnPlus;
var minusButton = binding.btnMinus;
var timesButton = binding.btnTimes;
var divideButton = binding.btnDivide;
var clearButton = binding.btnClear;
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

그럼 각각의 연산자에 기반한 계산을 도와주는 몇 가지 함수를 선언했습니다.

```js
    fun adding(a: Double, b : Double):Double{
        return a + b
    }

    fun minus(a: Double, b : Double) : Double {
        return a - b
    }

    fun times(a: Double, b : Double) : Double {
        return a * b
    }

    fun divide(a: Double, b : Double) : Double {
        if (b == 0.0){
            throw IOException()
        }
        return a / b
    }
```

divide 함수를 보면, b가 영인지 여부를 확인하는 것을 볼 수 있습니다. 왜일까요? 그 이유는 b가 식 아래에있는 숫자이기 때문입니다 (나눗셈 연산에서 "a"를 "b"로 나눈 것은 a/b입니다) 그리고 우리는 0으로 a를 나눌 수 없습니다. 왜냐하면 이것은 오류를 반환할 것이기 때문입니다. 왜 오류가 발생하나요? 바로 아래 숫자가 0으로 접근하거나 b가 점점 작아질수록, 연산의 결과가 무한에 수렴하고 이것은 이 상황에서는 좋지 않습니다. 결론적으로 b가 영일 때, 이를 허용할 수 없으므로 이러한 경우를 처리하지 않으면 프로그램도 오류가 발생합니다. 그래서 b가 영인 경우마다 IOException을 throw 합니다.

그 뒤에는 모든 버튼의 onClickListener를 처리합니다. 버튼이 눌릴 때마다 연산을 수행하고 싶습니다.

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

첫째로, 수학 연산에서 첫 번째 숫자나 두 번째 숫자가 0인지 확인됩니다. 둘 중 하나가 0이면 계산을 수행할 수 없습니다. 입력란이 비어있다고 0으로 간주하고 싶지 않기 때문에 이렇게 처리했습니다. 그런 다음 양쪽 숫자가 모두 null이 아닌 경우에만 연산을 계속할 수 있습니다. 이 경우에 null은 사용자가 숫자 입력란에 아무것도 넣지 않았음을 의미합니다.

또한 roundToString() 함수도 있습니다. 이 함수는 무엇을 하는 걸까요?

```js
    fun Double.roundToString() = when {
        toInt().toDouble() == this -> toInt()
        else -> this
    }.toString()
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

roundToString은 Double 클래스 라이브러리에 추가된 함수로, 소수점 뒤에 0이 있는 경우에만 double을 정수로 변환하는 기능을 제공합니다 (예: 0.0은 0으로 변환되고 1.2는 그대로 1.2로 유지됩니다).

또한 roundOffDecimal() 함수가 있습니다.

```kotlin
fun roundOffDecimal(number: Double): Double {
    val df = DecimalFormat("#.##")
    df.roundingMode = RoundingMode.FLOOR
    return df.format(number).toDouble()
}
```

이 함수는 숫자를 더 짧게 만듭니다. 예를 들어 1을 3으로 나눈 결과는 0.33333...이지만, 이 함수를 사용하면 결과가 0.33만 나오게 됩니다.

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

테이블 태그를 마크다운 형식으로 변경해주세요.

그리고 나누기 연산에서 IOException은 Toast를 이용해 catch했습니다.

이제는 모두입니다. 이 프로젝트는 아직 초보적인 단계에 머물고 있습니다. 나중에 더 많은 기능을 추가할 예정이에요.

나중에 봐요!
