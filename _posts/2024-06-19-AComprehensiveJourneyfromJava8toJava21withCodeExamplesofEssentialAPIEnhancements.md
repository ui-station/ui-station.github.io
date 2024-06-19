---
title: "자바 8부터 자바 21까지의 포괄적인 여정 중요 API 개선 사항의 코드 예제"
description: ""
coverImage: "/assets/img/2024-06-19-AComprehensiveJourneyfromJava8toJava21withCodeExamplesofEssentialAPIEnhancements_0.png"
date: 2024-06-19 21:56
ogImage: 
  url: /assets/img/2024-06-19-AComprehensiveJourneyfromJava8toJava21withCodeExamplesofEssentialAPIEnhancements_0.png
tag: Tech
originalTitle: "A Comprehensive Journey from Java 8 to Java 21 with Code Examples of Essential API Enhancements”"
link: "https://medium.com/gitconnected/a-comprehensive-journey-from-java-8-to-java-21-with-code-examples-of-essential-api-enhancements-6817d2ab3ba8"
---


## 안녕하세요 친구들, 이 기사에서는 다양한 Java 버전에 대해 이야기하고 싶습니다. 나는 Java 8부터 Java 21까지의 각 버전에서 소개된 모든 중요한 기능과 API를 다루고, 좀 더 심도있는 통찰력을 얻기 위해 코딩 예제를 제시하려고 합니다.

이는 Java 초보자뿐만 아니라 Java 8이나 Java 11과 같은 오래된 버전의 Java에서 작업하고 있는 Java 개발자들에게 도움이 될 것입니다. 자바 세계에서 무슨 일이 일어나고 있는지업데이트하고 싶어 하는 사람들에게 유용할 것입니다.

자 그럼 시작해봅시다,

![이미지](/assets/img/2024-06-19-AComprehensiveJourneyfromJava8toJava21withCodeExamplesofEssentialAPIEnhancements_0.png)

<div class="content-ad"></div>

## 특징:

람다 표현식:

- 익명 함수 사용으로 함수형 프로그래밍 가능.
- 함수형 인터페이스 작성을 위한 간결한 구문.

함수형 인터페이스:

<div class="content-ad"></div>

- 람다 표현식을 사용하는 데 도움이되는 단일 추상 메소드를 가진 인터페이스입니다.
- @FunctionalInterface 어노테이션을 사용하여 해당 인터페이스를 표시합니다.

스트림 API:

- 요소 시퀀스를 처리하기 위해 Stream이라는 새로운 추상화를 소개합니다.
- filter, map, reduce 등의 함수형 스타일 작업을 지원합니다.

메소드 참조:

<div class="content-ad"></div>

- 람다 표현식에 대한 간편한 표기법을 제공합니다.
- 메서드나 생성자를 :: 연산자를 사용하여 참조할 수 있습니다.

Optional 클래스:

- 비어 있을 수도 있고 비어 있지 않은 값을 포함할 수 있는 컨테이너 객체입니다.
- null 체크를 더 효과적으로 처리하고 NullPointerException을 방지하는 데 도움이 됩니다.

새로운 날짜 및 시간 API:

<div class="content-ad"></div>

- java.time 패키지는 날짜와 시간을 다루는 보다 포괄적이고 유연한 API를 소개했습니다.
- 이전의 java.util.Date 및 java.util.Calendar 클래스에서 발생한 다양한 문제를 해결합니다.

기본 메소드:

- 인터페이스에 메소드 구현을 허용합니다.
- 기존 구현을 망가뜨리지 않고 인터페이스를 발전시키는 데 도움이 됩니다.

Nashorn JavaScript 엔진:

<div class="content-ad"></div>

- 이전 Rhino JavaScript 엔진을 대체합니다.
- 더 나은 성능을 제공하며 최신 JavaScript 표준과 더 호환됩니다.

Parallel Streams:

- parallel() 메서드를 사용하여 스트림을 병렬 처리할 수 있습니다.
- 특정 유형의 작업에서 멀티코어 시스템에서 성능을 향상시킵니다.

Collectors:

<div class="content-ad"></div>

- 일반적인 축소 작업을 위한 Collectors 클래스의 유틸리티 메서드 집합을 소개합니다. 예를들어 toList(), toSet(), joining() 등이 있습니다.

java.util.function 패키지의 함수형 인터페이스:

- 람다 표현식을 지원하기 위해 Predicate, Function, Consumer 및 Supplier와 같은 새로운 함수형 인터페이스가 추가되었습니다.

## 개선된 Process API:

<div class="content-ad"></div>

- 자바 9은 원시 프로세스를 더 잘 제어할 수 있도록 Process API에 개선 사항을 도입했습니다. 새로운 ProcessHandle 클래스를 사용하면 개발자가 프로세스와 관련된 정보를 얻고 상호 작용할 수 있습니다.

```js
// ProcessHandle API를 사용하여 현재 프로세스에 대한 정보 가져오기
public class ProcessHandleExample {
    public static void main(String[] args) {
        ProcessHandle currentProcess = ProcessHandle.current();
        System.out.println("프로세스 ID: " + currentProcess.pid());
        System.out.println("실행 중? " + currentProcess.isAlive());
    }
}
```

## 컬렉션 팩토리 메서드:

- 자바 9에서는 컬렉션 인터페이스(List, Set, Map 등)에 새로운 정적 팩토리 메서드를 추가하여 이러한 컬렉션의 불변 인스턴스를 더 편리하게 생성할 수 있게 했습니다.

<div class="content-ad"></div>

```js
import java.util.List;

public class CollectionFactoryMethodsExample {
    public static void main(String[] args) {
        // List.of() 팩토리 메서드를 사용하여 변경할 수 없는 목록 생성
        List<String> colors = List.of("Red", "Green", "Blue");
        System.out.println(colors);
    }
}
```

## 향상된 스트림 API:

- takeWhile, dropWhile, ofNullable과 같은 여러 새로운 메서드로 스트림 API가 향상되어 스트림 작업의 유연성과 기능성을 향상시켰습니다.

```js
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class StreamAPIImprovementsExample {
    public static void main(String[] args) {
        // 예시 1: takeWhile
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        List<Integer> lessThanFive = numbers.stream()
                .takeWhile(n -> n < 5)
                .collect(Collectors.toList());

        System.out.println("5보다 작은 숫자: " + lessThanFive);

        // 예시 2: dropWhile
        List<Integer> greaterThanThree = numbers.stream()
                .dropWhile(n -> n <= 3)
                .collect(Collectors.toList());

        System.out.println("3보다 큰 숫자: " + greaterThanThree);

        // 예시 3: ofNullable

        // 예시 3: ofNullable
        String value1 = "안녕";
        String value2 = null;

        // null이 아닌 값이 있는 예시
        Stream.ofNullable(value1)
                .ifPresentOrElse(v -> System.out.println("ofNullable 예제 - null이 아닌 값: " + v),
                        () -> System.out.println("ofNullable 예제 - null 값"));

        // null 값이 있는 예시
        Stream.ofNullable(value2)
                .ifPresentOrElse(v -> System.out.println("ofNullable 예제 - null이 아닌 값: " + v),
                        () -> System.out.println("ofNullable 예제 - null 값"));

        // null 안전한 스트림 예시
        List<String> names = Arrays.asList("Alice", "Bob", null, "Charlie", null, "David");
        List<String> nonNullNames = names.stream()
                .flatMap(name -> StreamAPIImprovementsExample.nullSafeStream(name))
                .collect(Collectors.toList());

        System.out.println("null이 아닌 이름: " + nonNullNames);
    }

    // 잠재적으로 null인 값을 스트림으로 만드는 헬퍼 메서드
    private static <T> java.util.stream.Stream<T> nullSafeStream(T value) {
        return value == null ? java.util.stream.Stream.empty() : java.util.stream.Stream.of(value);
    }
}
```

<div class="content-ad"></div>

이 예제에서:

- takeWhile은 특정 조건이 충족될 때까지 스트림에서 요소를 가져오는 데 사용됩니다 (이 경우에는 5보다 작은 숫자).
- dropWhile은 특정 조건이 충족될 때까지 스트림에서 요소를 삭제하는 데 사용됩니다 (이 경우에는 3 이하의 숫자).
- ofNullable은 잠재적으로 null인 값을 사용하여 스트림을 생성하고 null 값을 필터링하여 null 이름을 제외합니다.

## 인터페이스의 비공개 메서드:

- Java 9의 인터페이스는 비공개 메서드를 가질 수 있습니다. 이를 통해 공통 기능을 인터페이스 내에 캡슐화하여 외부 클래스에 노출하지 않고 사용할 수 있습니다.

<div class="content-ad"></div>

```js
// 프라이빗 메서드를 가진 인터페이스
public interface PrivateMethodInterface {
    default void publicMethod() {
        // 퍼블릭 메서드에서 프라이빗 메서드 호출 가능
        privateMethod();
    }

    private void privateMethod() {
        System.out.println("인터페이스 안의 프라이빗 메서드");
    }
}
```

HTTP/2 클라이언트:

- Java 9에서는 HTTP/2 및 WebSocket을 지원하는 새로운 가벼운 HTTP 클라이언트가 도입되었습니다. 이 클라이언트는 예전 HttpURLConnection API보다 효율적이고 유연하게 설계되었습니다.

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class HttpClientExample {
    public static void main(String[] args) throws Exception {
        HttpClient httpClient = HttpClient.newHttpClient();
        HttpRequest httpRequest = HttpRequest.newBuilder()
                .uri(new URI("https://www.example.com"))
                .GET()
                .build();

        HttpResponse<String> response = httpClient.send(httpRequest, HttpResponse.BodyHandlers.ofString());
        System.out.println("응답 코드: " + response.statusCode());
        System.out.println("응답 본문: " + response.body());
    }
}
```

<div class="content-ad"></div>

로컬 변수 타입 추론 (var):

- 자바 10에서는 var 키워드를 사용하여 로컬 변수 타입 추론 기능을 도입했습니다. 이를 통해 개발자는 타입을 명시적으로 지정하지 않고 로컬 변수를 선언할 수 있으며, 할당된 값에 기반하여 컴파일러가 추론하도록 합니다.

```js
public class LocalVarInference {

    /**
     * 허용: 로컬 변수로만 사용
     * 허용되지 않음: 클래스 필드, 메소드 매개변수 등 다른 곳 (멤버 변수, 여타곳)
     * var 키워드 책임있게 사용하는 것이 좋습니다!
     *
     * 사용 사례:
     *  - 타입이 명확한 경우 (문자열, 정수)
     *  - 너무 긴, 복잡한 타입을 줄이기 위하여
     *
     * 사용하지 말아야 할 때:
     *      - 반환 값이 명확하지 않은 경우 (var data = service.getData();)
     */

    public static void main(String[] args) {

        // 허용되지만 장점이 별로 없음
        var b = "b";
        var c = 5; // int
        var d = 5.0; // double
        var httpClient = HttpClient.newHttpClient();

        // 추론할때 쉬운 경우 :)
        var list = List.of(1, 2.0, "3");

        // 이름이 긴 타입일 때 장점이 더욱 분명해집니다
        var reader = new BufferedReader(null);
        // vs.
        BufferedReader reader2 = new BufferedReader(null);
    }
}
```

Optional API - 새로운 메소드가 도입되었습니다.

<div class="content-ad"></div>

```java
public class OptionalApi {

    /**
     * 새로운 .orElseThrow() 메서드
     */
    public static void main(String[] args) {

        Optional<Flight> earliestFlight = FlightSchedule.getFlights()
                .stream()
                .filter(f -> "Boston".equals(f.from()))
                .filter(f -> "San Francisco".equals(f.to()))
                .min(comparing(Flight::date));

        earliestFlight.orElseThrow(FlightNotFoundException::new);
    }
}
```

HTTP client

```java
public class HttpClientBasicExample {

    /**
     * 클라이언트 생성, GET 요청 보내기, 응답 정보 출력
     */
    public static void main(String... args) throws Exception {
        HttpClient client = HttpClient.newHttpClient();

        HttpRequest request =
                HttpRequest.newBuilder(URI.create("https://github.com/"))
                        .GET()  // 기본값, 생략 가능
                        .build();

        HttpResponse<String> response =
                client.send(request, HttpResponse.BodyHandlers.ofString());

        print("상태 코드: " + response.statusCode());

        print(response.headers().map());
    }
}
```

## 새로운 파일 메서드:

<div class="content-ad"></div>

Java 11에서는 java.nio.file 패키지에 여러 가지 새로운 메서드가 추가되었는데, 파일 및 디렉토리 작업에 대한 추가 기능을 제공합니다. 주목할 만한 몇 가지 메서드는 다음과 같습니다:

- Files.readString(Path path) 및 Files.writeString(Path path, CharSequence content, OpenOption... options):

- 이러한 메서드를 사용하여 파일의 내용을 문자열로 읽고 쓰는 작업을 간단하게 처리할 수 있습니다. readString 메서드는 파일의 전체 내용을 문자열로 읽어오고, writeString 메서드는 문자열을 파일에 씁니다.

2. Files.readAllLines(Path path) 및 Files.write(Path path, Iterable<? extends CharSequence> lines, OpenOption... options):

<div class="content-ad"></div>

- 이러한 메서드는 파일 내용을 문자열 목록으로 읽고 쓰는 작업을 간단하게 해줍니다. readAllLines 메서드는 파일에서 모든 줄을 목록으로 읽고, write 메서드는 문자열 컬렉션을 파일에 쓰기 위한 메서드입니다.

3. Files.newBufferedReader(Path path) 및 Files.newBufferedWriter(Path path, OpenOption... options):

- 이러한 메서드는 파일을 효율적으로 읽고 쓰기 위한 버퍼드 리더와 라이터를 생성합니다. 이들은 문자 스트림을 다루는 프로세스를 간단하게 합니다.

4. files.mismatch(Path path1, Path path2):

<div class="content-ad"></div>

- 이 메서드는 두 파일의 내용을 비교하여 첫 번째 불일치하는 바이트의 위치를 반환합니다. 파일이 동일한 경우 -1을 반환합니다.

```js
public class NewFilesMethods {

    static String filePath = System.getProperty("user.dir") + "/src/main/resources/";
    static String file_1 = filePath + "file_1.txt";

    /**
     * Files.readString() and .writeString()
     */
    public static void main(String[] args) throws IOException {

        // 파일 읽기가 이제 훨씬 쉬워졌습니다.
        // 대용량 파일과 사용하면 안됨
        Path path = Paths.get(file_1);
        String content = Files.readString(path);
        print(content);

        Path newFile = Paths.get(filePath + "newFile.txt");
        if(!Files.exists(newFile)) {
            Files.writeString(newFile, "some str", StandardOpenOption.CREATE);
        } else {
            Files.writeString(newFile, "some str", StandardOpenOption.TRUNCATE_EXISTING);
        }
    }
}
```

## 콤팩트 숫자 포맷팅:

Java 12에서 JEP 357의 일환으로 "콤팩트 숫자 포맷팅"이라는 새로운 기능이 소개되었습니다. 이 개선은 지역별 방식으로 대형 숫자를 더 간결하게 포맷하는 방법을 제공합니다.

<div class="content-ad"></div>

java.text 패키지의 NumberFormat 클래스가 새로운 Style enum을 지원할 수 있도록 업데이트되었습니다. 이 Style에는 Style.SHORT 및 Style.LONG 상수가 포함되어 있습니다. 이러한 스타일은 특정 로케일에 기반해 큰 숫자를 간결한 형태로 포맷하는 데 사용할 수 있습니다.

```java
import java.text.NumberFormat;
import java.util.Locale;

public class CompactNumberFormattingExample {
    public static void main(String[] args) {
        // 컴팩트 스타일의 숫자 포매터 생성
        NumberFormat compactFormatter = NumberFormat.getCompactNumberInstance(Locale.US, NumberFormat.Style.SHORT);

        // 큰 숫자 형식화
        System.out.println("Short Format: " + compactFormatter.format(1000));  // 결과: 1K
        System.out.println("Short Format: " + compactFormatter.format(1000000));  // 결과: 1M

        // 긴 형식의 컴팩트 스타일 숫자 포매터 생성
        NumberFormat compactLongFormatter = NumberFormat.getCompactNumberInstance(Locale.US, NumberFormat.Style.LONG);

        // 긴 스타일로 큰 숫자 형식화
        System.out.println("Long Format: " + compactLongFormatter.format(10000000));  // 결과: 10 million
        System.out.println("Long Format: " + compactLongFormatter.format(1000000000));  // 결과: 1 billion
    }
}
```

## String::indent (JEP 326):

- Java 12의 String 클래스에는 indent(int n)이라는 새로운 메서드가 도입되었습니다. 이 메서드는 문자열의 각 줄의 들여쓰기를 지정된 공백 수만큼 조정하는 데 사용됩니다.

<div class="content-ad"></div>

```java
String indentedString = "Hello\nWorld".indent(3);
// indentedString이 이제 "   Hello\n   World"가 됩니다.
```

java.util.Arrays에 추가된 새로운 메서드 (JEP 326):

- Java 12에서는 java.util.Arrays 클래스에 copyOfRange 및 Comparator를 사용하는 equals 변형 등을 포함한 여러 새로운 메서드가 추가되었습니다.

java.util.stream.Collectors의 개선 사항 (JEP 325):

<div class="content-ad"></div>

- Java 12에서 도입된 Collectors 유틸리티 클래스에는 teeing과 같은 새로운 수집기가 추가되었는데, 이를 사용하면 두 개의 수집기를 결합하여 하나의 수집기로 만들 수 있습니다.

## 새로운 파일 메소드:

```js
public class NewFilesMethod {

    static String filePath = System.getProperty("user.dir") + "/src/main/resources/";
    static String file_1 = filePath + "file_1.txt";
    static String file_2 = filePath + "file_2.txt";

    public static void main(String[] args) throws IOException {

        // 두 파일의 내용에서 첫 번째 불일치하는 바이트의 위치를 찾아 반환합니다.
        // 불일치가 없는 경우 -1L을 반환합니다.
        long result = Files.mismatch(Paths.get(file_1), Paths.get(file_2));

        print(result);      // -1
    }
}
```

특별히 흥미로운 일은 없었습니다:
- ByteBuffer에 대한 API 업데이트
- 지역화 업데이트 (새로운 문자 및 이모지 지원)
- GC 업데이트

<div class="content-ad"></div>

## “Switch Expressions” (SE) 대신 “Switch Statements” (SS):

향상된 Switch 표현식:

- Java 12에서 미리보기 기능으로 소개되었으며 Java 13에서 최종화된 스위치 표현식은 개발자들이 switch 문을 표현식으로 사용할 수 있도록 해주어 더 간결하고 표현력 있는 구문을 제공합니다.

```java
int dayOfWeek = 2;
String dayType = switch (dayOfWeek) {
    case 1, 2, 3, 4, 5 -> "평일";
    case 6, 7 -> "주말";
    default -> throw new IllegalArgumentException("유효하지 않은 요일: " + dayOfWeek);
};
```

<div class="content-ad"></div>

"수확" 문장:

- "수확" 문장은 스위치 표현식을 보완하기 위해 Java 14에서 소개되었습니다. 이를 사용하여 스위치 암에서 반환할 값을 지정할 수 있어, 명령형과 함수형 스타일을 융합하는 데 더 많은 유연성을 제공합니다.

```js
String dayType = switch (dayOfWeek) {
    case 1, 2, 3, 4, 5 -> {
        System.out.println("근무일");
        yield "평일";
    }
    case 6, 7 -> {
        System.out.println("주말");
        yield "주말";
    }
    default -> throw new IllegalArgumentException("유효하지 않은 요일: " + dayOfWeek);
};
```

또 다른 예시,

<div class="content-ad"></div>

```java
/**
 * "스위치 표현식" (SE) 대신 "스위치 문" (SS)
 * (둘 다 사용할 수 있지만 SE가 SS보다 낫습니다)
 */
public class SwitchExpressions {

    public static void main(String[] args) {

        oldStyleWithBreak(FruitType.APPLE);

        withSwitchExpression(FruitType.PEAR);

        switchExpressionWithReturn(FruitType.KIWI);

        switchWithYield(FruitType.PINEAPPLE);
    }

    // 예전 방식은 더 복잡하고 오류 발생 가능성이 높음 ("break;"을 잊으면 switch가 지나가버릴 수 있음)
    private static void oldStyleWithBreak(FruitType fruit) {
        print("==== break를 사용한 예전 방식 ====");
        switch (fruit) {
            case APPLE, PEAR:
                print("보통 과일");
                break;
            case PINEAPPLE, KIWI:
                print("이국적인 과일");
                break;
            default:
                print("정의되지 않은 과일");
        }
    }

    private static void withSwitchExpression(FruitType fruit) {
        print("==== 스위치 표현식을 사용한 방식 ====");
        switch (fruit) {
            case APPLE, PEAR -> print("보통 과일");
            case PINEAPPLE -> print("이국적인 과일");
            default -> print("정의되지 않은 과일");
        }
    }

    private static void switchExpressionWithReturn(FruitType fruit) {
        print("==== 반환 값이 있는 경우 ====");

        // 직접 "return switch"을 사용할 수도 있음
        String text = switch (fruit) {
            case APPLE, PEAR -> "보통 과일";
            case PINEAPPLE -> "이국적인 과일";
            default -> "정의되지 않은 과일";
        };
        print(text);
    }

    /**
     * "Yield"는 "return"과 비슷하지만 중요한 차이가 있음:
     * "yield"는 값을 반환하고 switch 문을 종료함. 실행은 특정 메서드 내에서 유지됨
     * "return"은 switch문 및 특정 메서드를 종료함
     */
    // https://stackoverflow.com/questions/58049131/what-does-the-new-keyword-yield-mean-in-java-13
    private static void switchWithYield(FruitType fruit) {
        print("==== yield를 사용한 경우 ====");
        String text = switch (fruit) {
            case APPLE, PEAR -> {
                print("주어진 과일은: " + fruit);
                yield "보통 과일";
            }
            case PINEAPPLE -> "이국적인 과일";
            default -> "정의되지 않은 과일";
        };
        print(text);
    }

    public enum FruitType {APPLE, PEAR, PINEAPPLE, KIWI}
}
```

## 텍스트 블록

텍스트 블록은 여러 줄에 걸친 문자열 리터럴을 나타내는 새로운 유형의 리터럴입니다. 여러 줄에 걸친 소스 코드의 문자열을 작성하고 유지하는 작업을 간단하게 만들면서 이스케이프 시퀀스를 피하려고 합니다.

텍스트 블록을 사용하지 않은 예시:```

<div class="content-ad"></div>

```js
String html = """
<html>
    <body>
        <p>Hello, world</p>
    </body>
</html>
""";
```

텍스트 블록의 주요 기능은 다음과 같습니다:```

<div class="content-ad"></div>

- 여러 줄 문자열: 텍스트 블록을 사용하면 여러 줄 문자열을 더 자연스럽게 표현할 수 있어 코드 가독성을 향상시킵니다.
- 공백 제어: 각 줄의 시작과 끝 공백이 제거되어 들여쓰기를 더 잘 제어할 수 있습니다.
- 이스케이프 시퀀스: 텍스트 블록 내에서 이스케이프 시퀀스는 여전히 유효하며 특수 문자를 포함할 수 있습니다.

텍스트 블록은 HTML, XML, JSON 또는 SQL 쿼리와 같은 여러 줄 콘텐츠를 포함하는 문자열을 더 쉽게 표현할 수 있도록 설계되었습니다. Java 15나 이후 버전에서 텍스트 블록과 관련된 업데이트나 새로운 기능이 있었을 경우 해당 버전의 공식 문서나 릴리스 노트를 확인하는 것이 좋습니다.

```js
/**
 * TextBlocks에 대한 사용 사례 (Java 15의 새로운 기능 > Text Blocks in Practice)
 * - 마크다운을 사용한 텍스트 블록
 * - 테스트, 하드 코딩된 JSON 문자열 정의
 * - 간단한 템플릿
 */
public class TextBlocks {

    public static void main(String[] args) {
        oldStyle();
        emptyBlock();
        jsonBlock();
        jsonMovedEndQuoteBlock();
        jsonMovedBracketsBlock();
    }

    private static void oldStyle() {
        print("******** 기존 스타일 ********");

        String text = "{\n" +
                "  \"name\": \"John Doe\",\n" +
                "  \"age\": 45,\n" +
                "  \"address\": \"Doe Street, 23, Java Town\"\n" +
                "}";
        print(text);
    }

    private static void emptyBlock() {
        print("******** 빈 블록 ********");
        String text = """ 
                """;
        print("|" + text + "|");
    }

    private static void jsonBlock() {
        print("******** JSON 블록 ********");

        String text = """
                {
                  "name": "John Doe",
                  "age": 45,
                  "address": "Doe Street, 23, Java Town"
                }
                """; // <-- 첫 번째 " 문자와 정렬되어 있으면 들여쓰기 없음
        print(text);
    }

    private static void jsonMovedEndQuoteBlock() {
        print("******** JSON 이동된 끝 따옴표 블록 ********");

        String text = """
                  {
                    "name": "John Doe",
                    "age": 45,
                    "address": "Doe Street, 23, Java Town"
                  }
                       """;
        print(text);
    }

    private static void jsonMovedBracketsBlock() {
        print("******** JSON 이동된 괄호 블록 ********");

        String text = """
                  {
                    "name": "John Doe",
                    "age": 45,
                    "address": "Doe Street, 23, Java Town"
                  }
                """; // <-- 세 번째 " 문자와 정렬되어 2칸 들여쓰기
        print(text);
    }
}
```

## instanceof에 대한 패턴 매칭:

<div class="content-ad"></div>

자바 16의 instanceof에 대한 패턴 매칭은 유용한 기능으로 형식 검사와 추출을 개선합니다. 여기에 이 기능의 주요 측면을 요약해 두었어요:

기능:

- 단일 형식 대신 타입 패턴을 소개합니다.
- instanceof 검사 내에서 추출된 객체를 보유할 변수를 선언할 수 있습니다.
- 형식 검사 및 캐스팅을 더 간결하고 가독성 있게 결합한 표현입니다.

혜택:

<div class="content-ad"></div>

- 번거로운 코드 줄이기: 별도의 instanceof 확인, 형 변환 및 변수 선언이 필요 없게 합니다.
- 가독성 향상: 코드가 더 명확해지고 복잡한 유형 계층 구조에 대해 이해하기 쉬워집니다.
- 오류 감소: 잘못된 유형으로 인한 캐스트 예외 발생 가능성이 줄어듭니다.

구문

```javascript
if (obj instanceof String s) {
  // 여기서 "s"를 String으로 직접 사용
} else if (obj instanceof List<Integer> list) {
  // 여기서 "list"를 List <Integer>로 직접 사용
} else {
  // 다른 경우 처리
}
```

예제

<div class="content-ad"></div>

```java
public class PatternMatchingForInstanceof {

    public static void main(String[] args) {

        Object o = new Book("Harry Potter", Set.of("Jon Doe"));

        // 옛날 방식
        if (o instanceof Book) {
            Book book = (Book) o;
            print("The book's author(s) are " + book.getAuthors());
        }

        // 새 방식
        if (o instanceof Book book) {
            print("The book's author(s) are " + book.getAuthors());
        }

    }
}
```

## 레코드:

자바의 레코드는 변경할 수 없는 데이터를 보관하기 위해 특별히 설계된 특수한 유형의 클래스입니다. 이는 단순한 데이터 구조를 처리할 때 보일러플레이트 코드를 줄이고 가독성 및 유지보수성을 향상시킬 수 있도록 도와줍니다.

- 이들의 주요 특성을 살펴봅시다:
```  

<div class="content-ad"></div>

1. 간결함:

- 기존 클래스와는 달리 레코드는 정의하는 데 필요한 코드가 최소화됩니다. 레코드 선언에서 데이터 필드(구성 요소)만 지정하면 됩니다. 그러면 컴파일러가 다음과 같은 필수 메서드를 자동으로 생성합니다:
- 각 구성 요소에 대한 매개변수가 있는 생성자.
- 각 구성 요소에 대한 게터.
- 구성 요소 값을 기반으로 한 equals 및 hashCode 메서드.
- 레코드의 상태를 나타내는 toString 메서드.

2. 변경 불가능성:

- 레코드 필드는 final로 선언되어 있어, 레코드가 생성된 후에는 그 내부에 저장된 데이터를 변경할 수 없습니다. 이는 데이터 일관성을 보장하고 스레드 안전성에 대한 고려사항을 단순화합니다.

<div class="content-ad"></div>

3. 가독성:

- 레코드의 자동 생성 메서드와 예측 가능한 동작은 코드 가독성을 향상시키고 레코드가 어떤 것을 나타내며 프로그램의 다른 부분과 어떻게 상호 작용하는지 이해하기 쉽게 만듭니다.

4. 오류 감소:

- 보일러플레이트를 최소화함으로써 레코드는 getter를 잊거나 equals를 잘못 구현하는 것과 같은 일반적인 실수의 위험을 줄입니다. 이는 더 견고하고 신뢰할 수 있는 코드로 이어집니다.

<div class="content-ad"></div>

전반적으로 레코드는 Java 개발자가 간결하고 불변 그리고 가독성 좋은 데이터 구조를 만드는 데 유용한 도구입니다. 이는 보다 깔끔하고 유지보수 가능한 코드를 유도합니다.

```js
/**
 * 레코드는 데이터 전용 불변 클래스입니다 (따라서 특정한 사용 사례가 있음)
 * 클래스의 특정 형태(예: enum과 같은)로 제한된(전문화된) 형태입니다
 * 상태 변경 등을 의도한 객체에는 적합하지 않습니다.
 * <p>
 * 레코드는 다음과 관련이 없습니다:
 * - 보일러플레이트 감소 기법
 * <p>
 * 레코드는 생성자, 게터, 필드; equals, hashCode, toString을 생성합니다
 * <p>
 * 사용 사례:
 * - 불변 데이터 모델링
 * - 데이터를 읽기 전용으로 메모리에 보관
 * - DTOs - 데이터 전송 객체
 */
public class RecordsDemo {

    public static void main(String[] args) {
        Product p1 = new Product("우유", 50);
        Product p2 = new Product("우유", 50);

        print(p1.price()); // "get" 접두사 없이 사용
        print(p1);         // 자동으로 생성된 toString() 출력- Product[name=우유, price=50]

        print(p1 == p2);       // false    - 다른 객체
        print(p1.equals(p2));  // true     - 값들(milk, 50)은 auto-generated equals()/hashCode()에 의해 비교됨
    }
}

/**
 * 매개변수를 "Components"라고 함
 * 더 많은 필드 원할 시, 시그니처에 추가 필요
 * 확장은 허용되지 않지만 인터페이스 구현은 가능
 */
public record Product(String name, int price) {

    // 정적 필드는 가능하지만 비정적은 허용하지 않음
    static String country = "US";

    // 모든 필드를 가진 생성자가 생성됨

    // Validation 추가 가능
    public Product {
        if(price < 0) {
            throw new IllegalArgumentException();
        }
    }

    // toString()과 같은 auto-generated 메소드들 오버라이딩 가능
}
```

## 날짜 및 시간 형식 API:

- Java 16의 DateTimeFormatter API의 일반적 사용법 및 기능: 형식 패턴 이해, 사용자 정의 형식 생성, 날짜 및 시간 구문 분석, 사용 가능한 형식 설정 포함
- Java 16에서 소개된 날짜 형식팅을 위한 새로운 기능: 특히 "B" 심볼과 다양한 스타일을 사용한 일시 지원
- SimpleDateFormat과 같은 이전 서식기와 DateTimeFormatter의 비교: 각 접근 방식의 장단점 탐색
- 특정 형식 작업에 DateTimeFormatter 사용 예제: 다양한 로케일에서 날짜 형식화, 타임존 처리, 더 사람이 이해하기 쉬운 표현 생성하기 등을 포함하여.

<div class="content-ad"></div>

```java
public class DateTimeFormatterApi {

    static Map<TextStyle, Locale> map = Map.of(
            TextStyle.FULL, Locale.US,
            TextStyle.SHORT, Locale.FRENCH,
            TextStyle.NARROW, Locale.GERMAN
    );

    public static void main(String[] args) {

        for (var entry : map.entrySet()) {

            LocalDateTime now = LocalDateTime.now();
            DateTimeFormatter formatter = new DateTimeFormatterBuilder()
                    .appendPattern("yyyy-MM-dd hh:mm ")
                    .appendDayPeriodText(entry.getKey())    // at night, du soir, abends, etc.
                    .toFormatter(entry.getValue());

            String formattedDateTime = now.format(formatter);
            print(formattedDateTime);
        }
    }
}
```

## Stream API의 변경 사항:

Java 16에서 Stream API에 흥미로운 변경 사항이 있었습니다. 더욱 강력하고 편리하게 사용할 수 있게 되었습니다. 주요 하이라이트는 다음과 같습니다:

1. Stream.toList() 메서드: 이 새로운 메서드는 스트림의 요소를 목록으로 수집하는 간결한 방법을 제공합니다. 이전에는 collect(Collectors.toList())를 사용해야 했지만, 이제는 약간 중복된 방법입니다.```

<div class="content-ad"></div>

2. Stream.mapMulti() 메소드: 이 메소드를 사용하면 스트림의 각 요소를 제로 또는 그 이상의 요소로 매핑하여 결과 요소의 새로운 스트림을 생성할 수 있습니다. 복잡한 데이터 구조를 분할하거나 펼치는 데 편리합니다.

3. 향상된 줄 바꿈 처리: Java 16에서 java.io.LineNumberReader 클래스에서 줄 바꿈자를 정의하는 방법을 명확히하였습니다. 이는 모순을 제거하고 줄 기반 데이터를 읽을 때 일관된 동작을 보장합니다.

4. 기타 작은 변경점:

- 이제 문자열 스트림은 중간 작업이 필요하지 않고 limit 및 skip 메소드를 직접 지원합니다.
- peek 메소드를 병렬 스트림과 함께 사용할 수 있으므로 병렬성에 영향을주지 않고 부작용을 발생시킬 수 있습니다.

<div class="content-ad"></div>

```java
public class StreamApi {

    public static void main(String[] args) {

        List<Integer> ints = Stream.of(1, 2, 3)
                .filter(n -> n < 3)
                .toList();  // new, instead of the verbose .collect(Collectors.toList())

        ints.forEach(System.out::println);

    }
}
```

## Sealed classes(Subclassing):

Sealed 클래스는 Java 17 (JEP 409)에서 소개된 새로운 기능으로 상속 계층구조에 대해 더 많은 제어를 제공합니다. 본질적으로 클래스나 인터페이스를 확장하거나 구현하는 것을 제한할 수 있습니다. 이는 다양한 이유로 매우 유용할 수 있습니다:

1. 향상된 타입 안전성: 허용된 하위 클래스를 명시함으로써 코드를 손상시키거나 보안 취약점을 도입할 수 있는 예기치 않은 확장을 방지합니다.
```

<div class="content-ad"></div>

2. 개선된 라이브러리 디자인: 라이브러리 내에서 폐쇄된 생태계를 만들 수 있어서, 사용자가 승인된 확장 기능만 사용하고 호환되지 않는 구현을 만들지 않도록 보장할 수 있습니다.

3. 쉬운 코드 유지 관리: 가능한 서브클래스 집합을 정확히 알면 코드에 대한 추론이 간단해지며 이해와 유지보수가 쉬워집니다.

sealed 클래스는 어떻게 작동합니까?

sealed 키워드를 사용하여 클래스 또는 인터페이스를 sealed로 선언합니다. 그런 다음 permits 절을 사용하여 확장하거나 구현할 수 있는 클래스 목록을 지정합니다. 이 허용된 클래스만 직접 상속할 수 있고, 다른 모든 클래스는 금지됩니다.

<div class="content-ad"></div>

```js
sealed class Shape {
  permits Circle, Square, Triangle;
  // ... 구현 세부사항
}

class Circle extends Shape {
  // ...
}

// 사각형이 허용되지 않아 컴파일 오류가 발생합니다
class Rectangle extends Shape {
  // ...
}
```

봉인된 클래스나 인터페이스는 지정된 클래스와 인터페이스만이 상속하거나 구현할 수 있습니다.

이점:

- 1) 의도를 전달하여 잘 정의되고 제한된 가능한 구현을 강제화할 수 있음
- 2) 더 나은 보안 — 제 3자 코드로부터의 예기치 않은 또는 무단 서브클래싱과 행위 예방을 돕습니다

<div class="content-ad"></div>

규칙:

- 1. sealed 클래스는 "permits"를 사용하여 다른 클래스가 하위 클래스로 지정될 수 있게 합니다.
- 2. 하위 클래스는 반드시 final, sealed 또는 non-sealed 이어야 합니다. (그렇지 않으면 코드가 컴파일되지 않습니다)
- 3. 허용된 하위 클래스는 상위 sealed 클래스를 확장해야 합니다. permit을 사용하지 않고 허용하는 것은 허용되지 않습니다.
- 4. permits로 지정된 클래스들은 상위 클래스와 근접하게 위치해야 합니다:
    - 동일한 모듈에 있어야 합니다 (상위 클래스가 명명된 모듈에 있는 경우) (Java 9 모듈화 참조)
    - 동일한 패키지에 있어야 합니다 (상위 클래스가 무명 모듈에 있는 경우).

4번 항목에 대한 더 자세한 내용:

- Sealed 클래스와 그 (직접적인) 하위 클래스는 함께 컴파일되고 유지되어야 하기 때문에 밀접하게 결합됩니다.
- 모듈화된 환경에서는 "동일한 모듈"; 모듈화되지 않은 환경에서는 이를 가장 잘 나타내는 것이 "동일한 패키지"입니다.
- 모듈을 사용한다면 모듈이 제공하는 안전한 경계 때문에 몇 가지 추가적인 유연성을 얻을 수 있습니다.

<div class="content-ad"></div>

## 기본 UTF-8 설정:

Java 18에서는 플랫폼의 기본 문자 인코딩이 UTF-8로 변경되었습니다. 이는 현대 표준에 맞추어 문자 처리를 간소화하고 있습니다.

```js
public class Utf8ByDefault {

    // https://openjdk.org/jeps/400 - Platform Default Encoding

    public static void main(String[] args) throws IOException {

        // 문제:
        // 1) 윈도우에서는 아스키 테이블 외의 문자, 예를 들어 특이한 유니코드 문자,을 지정하지 않고 FileWriter을 사용하여 쓰기
        // 2) 파일을 맥과 같은 UNIX 기반 OS로 복사하거나 전송한 다음 시스템의 기본 문자 인코딩을 사용하여 파일을 읽음
        // 3) 예상되는 결과 - 엉망인 출력
        // 따라서 문제는 예측불가능한 동작입니다.
        FileWriter writer = new FileWriter("out.txt");

        // Java 18 이전의 해결책: 항상 문자 집합을 지정하십시오 (그리고 그것을 잊지 않도록 행운을 빕니다!)
        FileWriter writer2 = new FileWriter("out.txt", StandardCharsets.UTF_8);

        // Java 18부터의 해결책: UTF-8은 이제 기본값이므로 문자 집합을 지정할 필요가 없습니다.

    }
}
```

## 간단한 웹 서버:

<div class="content-ad"></div>

이 새로운 API는 정적 파일을 제공하는 기본 웹 서버를 제공하여 빠른 프로토타이핑 및 임베디드 애플리케이션에 이상적입니다.

- 시스템에 Java 18 이상이 설치되어 있는지 확인합니다.
- 정적 파일(HTML, CSS, JavaScript, 이미지 등)을 특정 디렉토리에 준비해 두세요.

```js
import com.sun.net.httpserver.HttpServer;
import com.sun.net.httpserver.SimpleFileServer;

import java.net.InetSocketAddress;

public class SimpleWebServer {
    public static void main(String[] args) throws Exception {
        String documentRoot = "/path/to/your/static/files";  // 실제 디렉토리로 교체합니다
        int port = 8080;  // 필요에 따라 포트를 변경할 수 있습니다

        HttpServer server = HttpServer.create(new InetSocketAddress(port), 0);
        SimpleFileServer fileServer = new SimpleFileServer(documentRoot);
        server.setExecutor(null);  // 단일 스레드 실행자 사용
        server.createContext("/", fileServer);

        server.start();
        System.out.println("포트 " + port + "에서 서버가 시작되었습니다.");
    }
}
```

```js
<!DOCTYPE html>
<html>
<head>
    <title>문서 제목</title>
</head>

<body>
이 페이지는 "jwebserver" 명령을 사용하여 Java의 Simple Web Server로 제공될 수 있습니다.
</body>

</html>
```

<div class="content-ad"></div>

아래와 같은 방법으로 실행해보세요:

1. Java 파일 컴파일하기: javac SimpleWebServer.java
2. 컴파일된 클래스 실행하기: java SimpleWebServer

접속하기:

- 웹 브라우저를 열고 http://localhost:8080 (또는 지정된 포트)로 이동합니다.
- 정적 파일 디렉토리에서 기본 파일 (보통 index.html)이 제공되는 것을 확인할 수 있어요.

<div class="content-ad"></div>

5. 서버를 중지하는 방법:

- 서버를 중지하려면 실행 중인 터미널에서 Ctrl+C를 누릅니다.

가장 간단한 방법으로 시작하기:

1) 이 패키지(java18)에서 터미널을 엽니다.

<div class="content-ad"></div>

2) "java -version"을 실행하고 Java가 적어도 18 버전인지 확인해주세요.

3) "jwebserver" 명령어를 실행해주세요.

다음과 같은 메시지가 표시되어야 합니다:

기본적으로 루프백에 바인딩됩니다. 모든 인터페이스에 대해 사용하려면 "-b 0.0.0.0" 또는 "-b ::"를 사용하십시오.

<div class="content-ad"></div>

127.0.0.1 포트 8000에서 경로/디렉토리 및 하위 디렉토리를 제공하고 있어요.

URL http://127.0.0.1:8000/

HTML 페이지는 이제 다음 주소에서 제공됩니다: http://127.0.0.1:8000/java18/doc.html

IP 주소, 포트 및 기타 매개변수를 변경할 수 있어요.

<div class="content-ad"></div>

## HEAD() 편의 메서드 추가:

```js
public class HttpHeadDemo {

    /**
     * HEAD() 편의 메서드 추가
     */
    public static void main(String[] args) throws IOException, InterruptedException {
        HttpRequest head = HttpRequest.newBuilder(URI.create("https://api.github.com/"))
                .HEAD()
                .build();

        var response = HttpClient.newHttpClient().send(head, HttpResponse.BodyHandlers.ofString());

        print(response);
    }
}
```

Method Handles로 Core Reflection 재구현: 이 재구현은 리플렉션 기능의 성능과 안정성을 개선하기 위한 것입니다.

제거를 위한 Finalization 과거화: 자원 정리를 위해 고안된 Finalization은 고유한 단점이 있습니다. 이를 과거화함으로써 더 안전하고 신뢰할 수 있는 대안들이 제공됩니다.

<div class="content-ad"></div>

미리보기 또는 인큐베이터 기능:

## 가상 스레드:

이 기능은 운영 체제 스레드 위에서 실행되는 가벼운 스레드를 소개하여 이를 통해 동시 프로그래밍을 간단히하고 특정 작업 부하의 성능을 향상시키는 것을 목표로합니다.

<div class="content-ad"></div>

아래는 Java 21에서 가상 스레드를 소개하는 간단한 데모입니다:

- 가상 스레드 생성:

```js
Thread vThread1 = Thread.ofVirtual().start(() -> {
  for (int i = 0; i < 10; i++) {
    System.out.println("가상 스레드 1: " + i);
  }
});

Thread vThread2 = Thread.ofVirtual().start(() -> {
  for (int i = 0; i < 10; i++) {
    System.out.println("가상 스레드 2: " + i);
  }
});
```

<div class="content-ad"></div>

2. 완료 대기:

```js
vThread1.join();
vThread2.join();
```

결과:

이렇게 하면 두 가상 스레드의 출력이 교차되어 풀 OS 스레드의 오버헤드 없이 동시 실행이 나타납니다. 다음과 같은 내용을 볼 수 있습니다:

<div class="content-ad"></div>

```js
가상 스레드 1: 0
가상 스레드 2: 0
가상 스레드 1: 1
가상 스레드 2: 1
...
가상 스레드 1: 9
가상 스레드 2: 9
```

가상 스레드 설명:

가상 스레드는 작은 기본 OS 스레드 풀 위에서 실행되는 가벼운 실행 단위입니다. 다음과 같은 여러 가지 장점을 제공합니다:

- 가벼운 무게: OS 스레드와 비교하여, 가상 스레드는 생성 및 컨텍스트 전환 비용이 상당히 낮습니다.
- 개선된 병행성: 제한된 수의 OS 스레드 내에 효율적으로 더 많은 가상 스레드를 관리할 수 있어서, 특정 작업 부하에 대한 자원을 더 잘 활용할 수 있습니다.
- 단순화된 병행성 프로그래밍: 가상 스레드는 복잡한 스레드 관리와 동기화를 제거하여, 개발자들에게 병행 프로그래밍을 더 쉽게 만들어 줍니다.
```

<div class="content-ad"></div>

다음은 가상 스레드의 예제이며 OS/플랫폼 스레드와 대조적입니다. 이 프로그램은 ExecutorService를 사용하여 10,000개의 작업을 생성하고 모든 작업이 완료될 때까지 기다립니다. JDK는 배완 스레드와 OS 스레드의 제한된 수에서 이를 실행하여 쉽게 동시 코드를 작성할 수 있도록 지원합니다.

```js
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    IntStream.range(0, 10_000).forEach(i -> {
        executor.submit(() -> {
            Thread.sleep(Duration.ofSeconds(1));
            return i;
        });
    });
}  // executor.close()는 암시적으로 호출되어 기다립니다
```

## 레코드 패턴 (프로젝트 앰버):

레코드는 Java 14에서 미리보기로 도입되었으며, Java 열거형도 함께 제공되었습니다. record는 Java의 또 다른 특별한 종류이며, 클래스 개발 프로세스를 단순한 데이터 운반자로만 동작하는 클래스로 쉽게 만들 수 있도록 도와줍니다.

<div class="content-ad"></div>

JDK 21에서 레코드 패턴과 타입 패턴을 중첩하여 사용하여 데이터 탐색 및 처리를 선언적이고 조합 가능한 형태로 가능하게 되었습니다.

```java
// 레코드 생성하기:

public record Todo(String title, boolean completed){}

// 객체 생성하기:

Todo t = new Todo(“Learn Java 21”, false);
```

JDK 21 이전에는 전체 레코드를 분해하여 액세서에 액세스해야 했습니다. 그러나 이제는 값들을 더 간단하게 얻을 수 있습니다. 예를 들어:

```java
static void printTodo(Object obj) {
    if (obj instanceof Todo(String title, boolean completed)) {
        System.out.print(title);
        System.out.print(completed);
    }
}
```

<div class="content-ad"></div>

레코드 패턴의 다른 장점은 중첩된 레코드 및 해당 값에 액세스하는 기능입니다. JEP 정의 자체에서 제시된 예시는 ColoredPoint에 중첩된 Rectangle 내부의 Point 값을 얻는 능력을 보여줍니다. 이전보다 더 유용해졌습니다. 이전에는 레코드를 매번 분해해야 했는데, 이제 중첩된 레코드의 값을 쉽게 얻을 수 있습니다.

```js
// Java 21부터
static void printColorOfUpperLeftPoint(Rectangle r) {
    if (r instanceof Rectangle(ColoredPoint(Point p, Color c),
                               ColoredPoint lr)) {
        System.out.println(c);
    }
}
```

## 순차적 컬렉션:

JDK 21에서는 새로운 컬렉션 인터페이스 세트가 소개되어 컬렉션 사용 경험을 향상시킵니다. 예를 들어, 컬렉션에서 요소의 역순을 얻고 싶다면, 사용 중인 컬렉션에 따라 번거로울 수 있습니다. 사용 중인 컬렉션에 따라 등장 순서를 가져오는 데 불일치가 있을 수 있습니다. 예를 들어, SortedSet은 하나를 구현하지만 HashSet는 그렇지 않기 때문에 서로 다른 데이터 세트에서 이 작업을 수행하는 것이 번거로울 수 있습니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-AComprehensiveJourneyfromJava8toJava21withCodeExamplesofEssentialAPIEnhancements_2.png" />

이 문제를 해결하기 위해 SequencedCollection 인터페이스는 reverse 메서드를 추가하고 첫 번째 및 마지막 요소를 가져오는 기능을 제공하여 순서를 보장합니다. 또한 SequencedMap 및 SequencedSet 인터페이스도 있습니다.

```js
interface SequencedCollection<E> extends Collection<E> {
    // 새로운 메서드
    SequencedCollection<E> reversed();
    // Deque에서 승급된 메서드
    void addFirst(E);
    void addLast(E);
    E getFirst();
    E getLast();
    E removeFirst();
    E removeLast();
}
```

## 문자열 템플릿:

<div class="content-ad"></div>

문자열 템플릿은 JDK 21의 미리 보기 기능입니다. 그러나 이는 문자열 조작에 더 많은 신뢰성과 더 나은 경험을 제공하여 때로는 원하지 않는 결과로 이어질 수 있는 일반적인 함정을 피하기 위해 노력합니다. 이제 템플릿 표현식을 작성하고 문자열에서 렌더링할 수 있습니다.

```java
// Java 21부터
String name = "Ajay";
String greeting = "Hello \{name}";
System.out.println(greeting);
```

이 경우 두 번째 줄이 표현식이며 호출시 Hello Ajay를 렌더링해야 합니다. 또한 보안 문제를 일으킬 수 있는 SQL 문 또는 HTML과 같은 불법적인 문자열의 가능성이 있는 경우 템플릿 규칙은 이스케이프된 따옴표만 허용하고 HTML 문서에서는 불법 엔티티를 허용하지 않습니다.

# 읽어 주셔서 감사합니다.

<div class="content-ad"></div>

- 👏 위 이야기에 박수를 보내주시고 저를 팔로우해주세요 👉
- 📰 미디엄에서 더 많은 콘텐츠를 읽어보세요 (자바 개발자 인터뷰에 관한 총 50개의 이야기)

제 책들은 여기서 확인하실 수 있습니다:

- 아마존에서 제공하는 [가이드: 명쾌한 자바 개발자 인터뷰 (킨들북)](Amazon) 및 Gumroad (PDF 형식).
- Gumroad에서 제공하는 [가이드: 명쾌한 스프링 부트 마이크로서비스 인터뷰 (PDF 형식)] 및 아마존 (킨들 eBook).
- 🔔 팔로우해보세요: LinkedIn | Twitter | YouTube