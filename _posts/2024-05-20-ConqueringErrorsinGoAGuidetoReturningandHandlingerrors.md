---
title: "고 오류 정복하기 오류 반환과 처리에 관한 안내"
description: ""
coverImage: "/assets/img/2024-05-20-ConqueringErrorsinGoAGuidetoReturningandHandlingerrors_0.png"
date: 2024-05-20 16:27
ogImage: 
  url: /assets/img/2024-05-20-ConqueringErrorsinGoAGuidetoReturningandHandlingerrors_0.png
tag: Tech
originalTitle: "Conquering Errors in Go: A Guide to Returning and Handling errors"
link: "https://medium.com/ride-blog/conquering-errors-in-go-a-guide-to-returns-and-handling-a13885905433"
---


## Go 오류 처리 마스터하기 위한 초보자 가이드

![이미지](/assets/img/2024-05-20-ConqueringErrorsinGoAGuidetoReturningandHandlingerrors_0.png)

# 레벨 1: if err != nil

이것은 오류를 반환하는 가장 간단한 방법입니다. 대부분의 사람들은 이 패턴에 익숙합니다. 오류를 반환할 수 있는 함수를 호출하고, 오류가 nil인지 확인하고, nil이 아니면 오류를 반환합니다.

<div class="content-ad"></div>

```Go
import (
 "errors"
 "fmt"
)

func doSomething() (float64, error) {
 result, err := mayReturnError();
 if err != nil {
  return 0, err
 }
 return result, nil
}
```

## 이 방식의 문제점

이 방식이 가장 간단하고 사실상 가장 많이 사용되는 방법이지만 중대한 문제점이 있습니다: 컨텍스트 부족. 깊은 호출 스택이 있는 경우 어떤 함수가 오류를 발생시켰는지 알 수 없습니다.

A() 함수가 B()를 호출하고, B()가 C()를 호출하고, C()가 다음과 같은 오류를 반환하는 호출 스택을 상상해보십시오:```

<div class="content-ad"></div>

```js
package main

import (
 "errors"
 "fmt"
)

func A(x int) (int, error) {
 result, err := B(x)
 if err != nil {
  return 0, err
 }
 return result * 3, nil
}

func B(x int) (int, error) {
 result, err := C(x)
 if err != nil {
  return 0, err
 }
 return result + 2, nil
}

func C(x int) (int, error) {
 if x < 0 {
  return 0, errors.New("negative value not allowed")
 }
 return x * x, nil
}

func main() {
 // Call function A with invalid input
 result, err := A(-2)
 if err == nil {
  fmt.Println("결과:", result)
 } else {
  fmt.Println("에러:", err)
 }
}
```

만약 이 프로그램을 실행하면 다음과 같이 출력됩니다.

```js
에러: negative value not allowed
```

우리는 호출 스택에서 이 오류가 어디에서 발생했는지에 대한 컨텍스트가 없습니다. 특정 오류 문자열을 찾아 해당 오류가 어디에서 발생했는지를 알아내기 위해 프로그램을 코드 편집기에서 열어야 합니다.```

<div class="content-ad"></div>

# 레벨 2: 에러 감싸기

에러에 대한 컨텍스트를 추가하기 위해 fmt.Errorf를 사용하여 에러를 감싸줍니다.

```go
package main

import (
	"errors"
	"fmt"
)

func A(x int) (int, error) {
	result, err := B(x)
	if err != nil {
		return 0, fmt.Errorf("A: %w", err)
	}
	return result * 3, nil
}

func B(x int) (int, error) {
	result, err := C(x)
	if err != nil {
		return 0, fmt.Errorf("B: %w", err)
	}
	return result + 2, nil
}

func C(x int) (int, error) {
	if x < 0 {
		return 0, fmt.Errorf("C: %w", errors.New("음수 값은 허용되지 않습니다"))
	}
	return x * x, nil
}

func main() {
	// 잘못된 입력으로 함수 A를 호출합니다.
	result, err := A(-2)
	if err == nil {
		fmt.Println("결과:", result)
	} else {
		fmt.Println("에러:", err)
	}
}
```

이 프로그램을 실행하면 아래와 같은 출력을 얻게 됩니다.

<div class="content-ad"></div>

```js
오류: A: B: C: 음수 값은 허용되지 않습니다
```

이제 우리는 콜 스택을 이해했습니다.

하지만 여전히 문제가 있습니다.

## 이 방법의 문제

<div class="content-ad"></div>

에러가 발생한 위치를 알게 되었지만 여전히 어떤 부분이 잘못되었는지는 알 수 없습니다.

# Level 3: 세부적인 에러

에러가 충분히 설명이 되지 않았습니다. 이것을 보다 복잡한 예제로 보여주기 위해 다음과 같이 조금 더 복잡한 예제가 필요합니다.

```js
import (
 "errors"
 "fmt"
)

func DoSomething() (int, error) {
 result, err := DoSomethingElseWithTwoSteps()
 if err != nil {
  return 0, fmt.Errorf("DoSomething: %w", err)
 }
 return result * 3, nil
}

func DoSomethingElseWithTwoSteps() (int, error) {
 stepOne, err := StepOne()
 if err != nil {
  return 0, fmt.Errorf("DoSomethingElseWithTwoSteps:%w", err)
 }

 stepTwo, err := StepTwo()
 if err != nil {
  return 0, fmt.Errorf("DoSomethingElseWithTwoSteps: %w", err)
 }

 return stepOne + StepTwo, nil
}
```

<div class="content-ad"></div>

이 예시에서 오류가 반환되면 어떤 작업이 실패했는지 명확하지 않습니다. StepOne인지 StepTwo인지 알 수 없습니다. 동일한 오류인 Error: DoSomething: DoSomethingElseWithTwoSteps: UnderlyingError가 발생합니다.

그 문제를 해결하려면 발생한 구체적인 문제에 대한 문맥을 추가해야 합니다.

```js
import (
 "errors"
 "fmt"
)

func DoSomething() (int, error) {
 result, err := DoSomethingElseWithTwoSteps()
 if err != nil {
  return 0, fmt.Errorf("DoSomething: %w", err)
 }
 return result * 3, nil
}

func DoSomethingElseWithTwoSteps() (int, error) {
 stepOne, err := StepOne()
 if err != nil {
  return 0, fmt.Errorf("DoSomethingElseWithTwoSteps: StepOne: %w", err)
 }

 stepTwo, err := StepTwo()
 if err != nil {
  return 0, fmt.Errorf("DoSomethingElseWithTwoSteps: StepTwo: %w", err)
 }

 return stepOne + StepTwo, nil
}
```

이제 StepOne이 실패하면 Error: DoSomething: DoSomethingElseWithTwoSteps: StepOne failed: UnderlyingError를 반환합니다.

<div class="content-ad"></div>

## 이 방법의 문제점

이 오류는 이제 함수 이름을 사용하여 호출 스택을 표현합니다. 그러나 오류의 본질을 표현하지는 않습니다. 오류는 이야기를 전달해야 합니다.

좋은 예는 HTTP 상태 코드입니다. 404를 받으면 원하는 리소스가 존재하지 않음을 알 수 있습니다.

# 레벨 4: 오류 표식

<div class="content-ad"></div>

에러 센티널은 재사용할 수 있는 미리 정의된 오류 상수입니다.

함수가 실패할 수 있는 원인은 다양할 수 있지만, 일반적으로 4가지 카테고리로 나눌 수 있습니다. "찾을 수 없음" 오류, "이미 존재함" 오류, "실패한 전제조건" 오류, 그리고 "내부 오류"입니다. 이는 gRPC 상태 코드에서 영감을 받았습니다. 각 카테고리를 한 문장으로 설명해 드릴게요. 

"찾을 수 없음" 오류: 호출자가 원하는 리소스가 존재하지 않습니다. 예시: 삭제된 게시물.

"이미 존재함" 오류: 호출자가 생성하려는 리소스가 이미 존재합니다. 예시: 같은 이름을 가진 조직.

<div class="content-ad"></div>

실패한 전제조건 오류: 작업 요청자가 실행하려는 작업이 실행 조건을 충족하지 않거나 나쁜 상태에 있습니다. 예시: 잔액이 0원인 계좌에서 출금을 시도하는 경우.

내부 오류: 이러한 카테고리에 속하지 않는 다른 오류로 내부 오류가 발생한 경우입니다.

이러한 종류의 오류만 가지고 있어도 충분하지 않습니다. 호출자에게 어떤 종류의 오류인지 알려주어야 합니다. 이를 위해 오류 센티널 및 errors.Is를 사용합니다.

사용자가 지갑 잔액을 조회하고 업데이트할 수 있는 REST API가 있다고 상상해보세요. 데이터베이스에서 지갑을 조회할 때 오류 센티널을 활용하는 방법을 살펴봅시다.

<div class="content-ad"></div>

```js
import (
 "fmt"
 "net/http"
 "errors"
)

// 이것들은 에러를 표현하는 상수들입니다.
var (
  WalletDoesNotExistErr = errors.New("지갑이 존재하지 않습니다") // 발견되지 않은 에러 타입
  CouldNotGetWalletErr = errors.New("지갑을 가져올 수 없습니다") // 내부 에러 타입
)

func getWalletFromDB(id int) (int, error) {
 // 더미 구현: 데이터베이스에서 지갑을 검색하는 시뮬레이션
 balance, err := db.get(id)

 if err != nil {
  if balance == nil {
    return 0, fmt.Errorf("%w: Wallet(id:%s)가 존재하지 않습니다: %w", WalletDoesNotExistErr, id, err)
  } else {
    return 0, fmt.Errorf("%w: 데이터베이스에서 Wallet(id:%s)를 가져올 수 없습니다: %w", CouldNotGetWalletErr, id, err)
  }
 }

 return *balance, nil
}
```

이제 이제 REST 핸들러에서 다음과 같이 할 수 있는 것을 보여줍니다.

```js
func getWalletBalance() {
 wallet, err := getWalletFromDB(id)

 if errors.Is(err, WalletDoesNotExistErr) {
  // 404 반환
 } else if errors.Is(err, CouldNotGetWalletErr) {
  // 500 반환
 }
}
```

다른 예제를 살펴보겠습니다. 사용자가 잔액을 업데이트하고자 하는 경우를 보겠습니다.
```

<div class="content-ad"></div>

```js
import (
 "fmt"
 "net/http"
 "errors"
)

var (
  WalletDoesNotExistErr = errors.New("Wallet does not exist") // Not Found Error Type
  CouldNotDebitWalletErr = errors.New("Could not debit Wallet") // Internal Error Type
  InsufficientWalletBalanceErr = errors.New("Insufficient balance in Wallet") // Failed Precondition Error Type
)

func debitWalletInDB(id int, amount int) error {
 // Dummy implementation: simulate retrieving a wallet from a database
 balance, err := db.get(id)

 if err != nil {
  if balance == nil {
    return fmt.Errorf("%w: Wallet(id:%s) does not exist: %w", WalletDoesNotExistErr, id, err)
  } else {
    return fmt.Errorf("%w: could not get Wallet(id:%s) from db: %w", CouldNotDebitWalletErr, id, err)
  }
 }

 if *balance <= 0 {
   return 0, fmt.Errorf("%w: Wallet(id:%s) balance is 0", InsufficientWalletBalanceErr, id)
 }

 updatedBalance := *balance - amount
 
 // Dummy implementation: simulate updating a wallet in a database
 err := db.update(id, updatedBalance)

 if err != nil {
   return fmt.Errorf("%w: could not update Wallet(id:%s) in db: %w", CouldNotDebitWalletErr, id, err)
 }

 return nil
}
```

## Using sentinels for better error messages

You might have noticed that I have a particular way of formatting errors. I prefer structuring an error message in one of two ways:

- `fmt.Errorf("%w: description: %w", Sentinel, err)` or
- `fmt.Errorf("%w: description", Sentinel)`

<div class="content-ad"></div>

이것은 오류가 발생한 이유와 근본적인 원인을 알려주는 이야기를 만듭니다.

이것은 중요합니다. 왜냐하면 위 예제에서 보는 바와 같이 동일한 유형의 오류가 두 가지 다른 근본적인 이슈로 인해 발생할 수 있기 때문입니다. 따라서 설명이 우리가 정확히 무엇이 잘못되었는지와 그 이유를 정확히 찾게 도와줍니다.

# 보너스: 에러를 기록하는 곳

당신이 발견한 모든 오류를 기록해서는 안 된다는 것에 놀랄지도 모릅니다. 왜냐하면 로그가 아래와 같이 보이기 때문입니다.

<div class="content-ad"></div>

```js
에러: C: 음수 값은 허용되지 않습니다
에러: B: C: 음수 값은 허용되지 않습니다
에러: A: B: C: 음수 값은 허용되지 않습니다
```

에러를 기록하는 곳은 반드시 "처리"할 수 있는 경우에만 해야 합니다. 여기서 처리란 호출자가 에러를 받아서 무언가 처리하고 계속 실행할 수 있도록 하는 것을 의미합니다.

전형적인 예시는 다시 말해 REST 핸들러일 것입니다. REST 핸들러가 에러를 받으면 해당 에러의 타입을 살펴보고, 적절한 상태 코드로 응답을 보내고 에러 전파를 중단할 수 있습니다.

```js
func getWalletBalance() {
 wallet, err := getWalletFromDB(id)

 if err != nil {
  fmt.Printf("%w", err) // 여기에서는 오직 에러를 로깅합니다
 }

 if errors.Is(err, WalletDoesNotExistErr) {
  // 404 반환
 } else if errors.Is(err, CouldNotGetWalletErr) {
  // 500 반환
 }
}
```