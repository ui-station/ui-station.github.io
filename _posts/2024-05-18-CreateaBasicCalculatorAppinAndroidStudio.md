---
title: "안녕하세요 안드로이드 스튜디오에서 기본 계산기 앱을 만들어 보는 방법을 알려드리겠습니다 함께 즐겁게 코딩해봐요 "
description: ""
coverImage: "/assets/img/2024-05-18-CreateaBasicCalculatorAppinAndroidStudio_0.png"
date: 2024-05-18 15:49
ogImage: 
  url: /assets/img/2024-05-18-CreateaBasicCalculatorAppinAndroidStudio_0.png
tag: Tech
originalTitle: "Create a Basic Calculator App in Android Studio"
link: "https://medium.com/@nikhil725051/create-a-basic-calculator-app-in-android-studio-5d6aec52b159"
---


<img src="/assets/img/2024-05-18-CreateaBasicCalculatorAppinAndroidStudio_0.png" />

계산기 앱을 만드는 것은 초보 Android 개발자에게 기본적인 연습입니다. 이 튜토리얼에서는 Android Studio에서 Java를 사용하여 기본 계산기 앱을 만드는 과정을 안내하겠습니다. 코드와 XML 레이아웃을 단계별로 다루어 각 구성 요소의 목적과 기능을 설명할 것입니다.

필수 준비물: 시작하기 전에 Android Studio가 시스템에 설치되어 있고 설정되어 있는지 확인하세요.

단계 1: 프로젝트 설정

<div class="content-ad"></div>

- 안드로이드 스튜디오를 실행하고 적절한 이름과 패키지로 새로운 안드로이드 프로젝트를 생성하세요.
- 프로젝트에 적합한 형태 요소와 최소 API 레벨을 선택하세요.

### 단계 2: XML 레이아웃

- activity_main.xml 레이아웃 파일을 열어주세요.
- UI 요소를 정의하세요: 두 개의 EditText 뷰(숫자 입력 용), 각 작업(더하기, 빼기, 곱하기, 나누기, 제곱근)에 대한 버튼, 그리고 결과를 표시할 TextView를 추가해주세요.

activity_main.xml

<div class="content-ad"></div>

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/num1EditText"
        android:layout_width="0dp"
        android:layout_height="48dp"
        android:layout_marginTop="44dp"
        android:hint="Enter number 1"
        android:inputType="numberDecimal"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <EditText
        android:id="@+id/num2EditText"
        android:layout_width="0dp"
        android:layout_height="48dp"
        android:layout_marginTop="12dp"
        android:hint="Enter number 2"
        android:inputType="numberDecimal"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.47"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/num1EditText" />

    <Button
        android:id="@+id/addButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="20dp"
        android:text="+"
        android:textSize="16sp"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/num2EditText" />

    <Button
        android:id="@+id/subtractButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="20dp"
        android:text="-"
        android:textSize="16sp"
        app:layout_constraintEnd_toStartOf="@+id/multiplyButton"
        app:layout_constraintStart_toEndOf="@+id/addButton"
        app:layout_constraintTop_toBottomOf="@+id/num2EditText" />

    <Button
        android:id="@+id/multiplyButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="20dp"
        android:text="x"
        android:textSize="16sp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/num2EditText" />

    <Button
        android:id="@+id/divideButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="20dp"
        android:text="/"
        android:textSize="16sp"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/addButton" />

    <Button
        android:id="@+id/sqrtButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="20dp"
        android:layout_marginEnd="140dp"
        android:text="Sqrt"
        android:textSize="16sp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/subtractButton" />

    <TextView
        android:id="@+id/resultTextView"
        android:layout_width="84dp"
        android:layout_height="41dp"
        android:layout_marginStart="4dp"
        android:layout_marginTop="40dp"
        android:text="Result: "
        android:textSize="18sp"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/divideButton" />
</androidx.constraintlayout.widget.ConstraintLayout>
```  

```java
package com.example.simplecalculator;

import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

import com.example.simplecalculator.R;

import java.text.DecimalFormat;

public class MainActivity extends AppCompatActivity {

    private EditText num1EditText, num2EditText;
    private TextView resultTextView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        num1EditText = findViewById(R.id.num1EditText);
        num2EditText = findViewById(R.id.num2EditText);
        resultTextView = findViewById(R.id.resultTextView);

        Button addButton = findViewById(R.id.addButton);
        addButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                performCalculation('+');
            }
        });

        Button subtractButton = findViewById(R.id.subtractButton);
        subtractButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                performCalculation('-');
            }
        });

        Button multiplyButton = findViewById(R.id.multiplyButton);
        multiplyButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                performCalculation('*');
            }
        });

        Button divideButton = findViewById(R.id.divideButton);
        divideButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                performCalculation('/');
            }
        });

        Button sqrtButton = findViewById(R.id.sqrtButton);
        sqrtButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                calculateSquareRoot();
            }
        });
    }

    private void performCalculation(char operator) {
        String num1Str = num1EditText.getText().toString();
        String num2Str = num2EditText.getText().toString();

        if (num1Str.isEmpty() || num2Str.isEmpty()) {
            Toast.makeText(this, "Please enter both numbers", Toast.LENGTH_SHORT).show();
            return;
        }

        double num1 = Double.parseDouble(num1Str);
        double num2 = Double.parseDouble(num2Str);
        double result = 0;

        switch (operator) {
            case '+':
                result = num1 + num2;
                break;
            case '-':
                result = num1 - num2;
                break;
            case '*':
                result = num1 * num2;
                break;
            case '/':
                if (num2 != 0) {
                    result = num1 / num2;
                } else {
                    Toast.makeText(this, "Cannot divide by zero", Toast.LENGTH_SHORT).show();
                    return;
                }
                break;
        }

        DecimalFormat df = new DecimalFormat("#.##");
        resultTextView.setText("Result: " + df.format(result));
    }

    private void calculateSquareRoot() {
        String num1Str = num1EditText.getText().toString();

        if (num1Str.isEmpty()) {
            Toast.makeText(this, "Please enter a number", Toast.LENGTH_SHORT).show();
            return;
        }

        double num = Double.parseDouble(num1Str);
        double sqrtResult = Math.sqrt(num);
        DecimalFormat df = new DecimalFormat("#.##");
        resultTextView.setText("Square Root: " + df.format(sqrtResult));
    }
}
```

<div class="content-ad"></div>

calculateSquareRoot 메소드는 첫 번째 EditText 뷰에서 입력 숫자를 가져와 Math.sqrt 함수를 사용하여 제곱근을 계산하고 결과를 TextView에 소수점 둘째 자리까지 표시합니다.

결과:

![output](/assets/img/2024-05-18-CreateaBasicCalculatorAppinAndroidStudio_1.png)

요약: 이 튜토리얼에서는 Java와 Android Studio를 사용하여 간단한 계산기 앱을 만드는 방법을 배웠습니다. XML 레이아웃 디자인, UI 요소 초기화 및 계산 로직을 위한 Java 코드를 다루었으며, 숫자의 제곱근을 계산하는 기능을 추가했습니다. 이 앱을 개발함으로써 Android 앱 개발과 사용자 인터페이스 상호 작용에 대한 기본적인 이해를 얻을 수 있습니다. 앱의 기능을 더 추가하고 기능을 향상시키기 위해 더 많은 기능을 탐험해보세요.