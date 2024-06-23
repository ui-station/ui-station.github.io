---
title: "Swift에서 Typed 오류와 Untyped 오류 이해하기 새로운 에러 처리 방법"
description: ""
coverImage: "/assets/img/2024-06-23-UnderstandingTypedandUntypedErrorsinSwiftANewApproachtoErrorHandling_0.png"
date: 2024-06-23 23:35
ogImage: 
  url: /assets/img/2024-06-23-UnderstandingTypedandUntypedErrorsinSwiftANewApproachtoErrorHandling_0.png
tag: Tech
originalTitle: "Understanding Typed and Untyped Errors in Swift: A New Approach to Error Handling"
link: "https://medium.com/@alibarac/understanding-typed-and-untyped-errors-in-swift-a-new-approach-to-error-handling-d87961d4608a"
---


안녕하세요, Swift 개발자 여러분! 오늘은 우리의 일상 코딩 생활에서 매우 중요한 주제에 대해 이야기를 나누고 싶어요: 에러 처리입니다. 최근 WWDC 2024에서 Apple이 Swift에서 "타입드 에러"라 불리는 새로운 에러 처리 방법을 소개했어요. 따라서, 우리가 언타입드와 타입드 에러가 무엇인지, 그들이 어떻게 작동하는지, 그리고 왜 사용하고 싶을지 알아보도록 하겠습니다.

## 언타입드 에러: 고전적인 방식

먼저, 전통적인 방식인 언타입드 에러에 대해 이야기해보겠어요. Swift에서 오랫동안 코딩을 해왔다면, 이 방법에 익숙할 것입니다. Swift에서는 일반적으로 Error 프로토콜을 사용하여 에러를 처리합니다. 이 방법을 이용하면 Error 프로토콜을 준수하는 어떤 에러든 throw할 수 있어 매우 유연합니다. 그러나 때로는 너무 유연하여 어떤 종류의 에러를 기대해야 하는지 항상 알기 어려울 수 있습니다.

```js
enum StringParseError: Error {
    case invalidCharacter(Character, at: String.Index)
}

func parseNumber(from input: String) throws -> Int {
    for (index, character) in input.enumerated() {
        guard let _ = character.wholeNumberValue else {
            throw StringParseError.invalidCharacter(character, at: input.index(input.startIndex, offsetBy: index))
        }
    }
    return Int(input) ?? 0
}

do {
    let number = try parseNumber(from: "12a45")
} catch let error as StringParseError {
    switch error {
    case .invalidCharacter(let character, let index):
        print("Invalid character '\(character)' found at index \(index).")
    }
} catch {
    print("An unexpected error occurred: \(error).")
}
```

<div class="content-ad"></div>

## 이곳에서 무슨 일이 일어나고 있나요:

- StringParseError: Error 프로토콜을 준수하는 enum입니다.
- parseNumber(from:): 이 함수는 문자열의 각 문자를 확인합니다. 유효하지 않은 문자를 발견하면 StringParseError를 throw합니다.
- do-catch 블록: 이는 parseNumber 함수를 호출하고 발생 가능한 오류를 catch합니다. StringParseError 및 다른 오류는 일반적으로 처리됩니다.

## Typed Errors: 새로운 접근 방식

자, 이제 이 새롭고 멋진 기능인 typed errors에 대해 이야기해보겠습니다. WWDC 2024에서 소개된 이 기능은 함수가 throw할 수 있는 오류의 정확한 유형을 지정할 수 있게 해줍니다. 이를 통해 오류 처리를 더 정확하고 유형 안전하게 만들어줍니다.

<div class="content-ad"></div>

```js
열거자 ParseError: 오류 {
    case nonNumericCharacter(Character, at: String.Index)
}

func parseInt(from input: String) throws(ParseError) -> Int {
    for (index, character) in input.enumerated() {
        if character.isNumber == false {
            throw ParseError.nonNumericCharacter(character, at: input.index(input.startIndex, offsetBy: index))
        }
    }
    return Int(input) ?? 0
}

do {
    let value = try parseInt(from: "123x56")
} catch {
    print("ParseError 발생: \(error)")
}
```

# 여기에서 무슨 일이 벌어지고 있나요:

- parseInt(from:): 이 함수는 이제 throws(ParseError)를 사용하여 ParseError만 throw할 수 있음을 나타냅니다.
- do-catch 블록: 정확히 어떤 유형의 오류가 발생할 수 있는지 알기 때문에 catch 블록에서 오류 유형을 지정할 필요가 없습니다.

# Any 및 Never 오류 유형

<div class="content-ad"></div>

타입 애너효메이션을 사용하면 Error 및 Never를 지원하여 더 많은 유연성을 제공합니다.

```js
func parseValue(from input: String) throws(any Error) -> Int {
    if input.isEmpty {
        throw NSError(domain: "ParseDomain", code: 100, userInfo: [NSLocalizedDescriptionKey: "Input string is empty."])
    }
    return Int(input) ?? 0
}

func parseValue(from input: String) -> Int {
    return Int(input) ?? 0
}

func parseValue(from input: String) throws(Never) -> Int {
    return Int(input) ?? 0
}
```

# 이곳에서 하는 일:

- throws(any Error): 함수는 Error 프로토콜을 준수하는 모든 오류를 던질 수 있습니다.
- throws(Never): 함수는 오류를 던지지 않으며 throws 키워드가 기술적으로 필요하지만 오류를 던지지 않는다는 것을 명시적으로 나타낼 수 있습니다.

<div class="content-ad"></div>

# 마무리

타입별 오류를 사용하면 오류 처리가 더 명확하고 이해하기 쉬워집니다. 함수가 던질 수 있는 오류 유형을 지정함으로써 코드를 더 안전하고 유지보수하기 쉽게 만들 수 있습니다. WWDC 2024에서 소개된 이 새로운 방식은 믿을 수 있고 유지보수 가능한 응용 프로그램을 작성하는 데 도움이 됩니다.

다음에 Swift 프로젝트에서 오류 처리를 작업할 때는 타입별 오류를 한 번 시도해 보세요. 이를 통해 버그를 초기에 잡을 수 있고 코드를 더 예측 가능하며 디버깅하기 쉬워질 수 있습니다.

즐거운 코딩 되세요! 여러분의 Swift 프로젝트가 오류 없이 잘 진행되길 바랍니다! 이 주제를 더 깊게 파고들고 싶다면, 공식 Swift 문서를 확인해보세요.