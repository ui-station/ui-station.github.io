---
title: "UUID 대 ULID 어떤 것을 사용해야 할까요"
description: ""
coverImage: "/assets/img/2024-06-19-UUIDvsULID_0.png"
date: 2024-06-19 10:00
ogImage:
  url: /assets/img/2024-06-19-UUIDvsULID_0.png
tag: Tech
originalTitle: "UUID vs ULID"
link: "https://medium.com/@kiarash.shamaii/uuid-vs-ulid-1073e38017a3"
---

먼저 UUID와 ULID는 무엇인가요?

UUID (Universally Unique Identifier):

- UUID는 컴퓨터 시스템에서 정보를 고유하게 식별하는 데 사용되는 128비트 숫자입니다.
- 이들은 RFC 4122 표준에서 정의되어 있으며 여러 가지 버전의 UUID를 설명합니다.
- UUID는 소프트웨어 개발, 데이터베이스 및 고유 식별자가 필요한 다른 응용 프로그램에서 널리 사용됩니다.
- UUID의 주요 장점은 고유성, 분산 생성 및 플랫폼 독립성입니다.
- 그러나 UUID는 본질적으로 날짜 순서로 정렬할 수 없기 때문에 특정 사용 사례에서는 단점일 수 있습니다.

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

ULID (Universally Unique Lexicographically Sortable Identifier):

- ULID는 렉시코그래픽적으로 정렬 가능한 128비트 식별자입니다.
- 이 식별자는 UUID의 대안으로 2016년에 도입되었으며 정렬 가능한 고유 식별자를 제공하는 것을 목표로 합니다.
- ULID는 타임스탬프(48비트)와 랜덤 부분(80비트)으로 구성되어 고유성과 시간순 정렬을 모두 가능하게 합니다.
- ULID의 타임스탬프 부분은 유닉스 에포크 이후의 밀리초 수를 기반으로 하므로 시간에 따라 정렬 가능합니다.
- ULID는 UUID와 크기 및 구조적으로 호환되지만 정렬 가능한 추가 혜택을 제공합니다.
- 시간 순서가 중요한 경우에 유의미할 수 있는데, 예를 들어 시계열 데이터 또는 이벤트 로깅 시나리오에서 활용될 수 있습니다.

UUIDs (자바에서의 Guid)는 데이터베이스에서 고유 식별자로 널리 사용됩니다. UUID는 무작위적이어서 분산 시스템에서 인기가 있습니다.

하지만 UUID에는 몇 가지 단점이 있습니다:

1. UUID는 데이터베이스 삽입 속도를 늦춥니다. 각 삽입은 클러스터화된 인덱스 인 B+ 트리를 업데이트해야 합니다. UUID가 무작위인 점 때문에 이 작업은 트리를 균형잡기 위한 비용이 많이 드는 작업입니다.

2. 더 높은 저장 비용. UUID는 128비트이며 이를 문자열 형식으로 인간이 읽을 수 있는 형태로 저장하는 경우 더 길어집니다.

그래서, ULID에 대해 소개하겠습니다.

ULID는 UUID의 단점을 해결하려고 합니다. 또한 128비트이므로 UUID와 호환됩니다. 그러나 UUID와 달리, ULID는 정렬 가능합니다. ULID의 처음 40비트는 타임스탬프를 나타내어 ULID가 단조로운 증가를 보입니다.

어떤 식별자를 선택할지 결정할 때는 애플리케이션의 특정 요구 사항에 따라 UUID와 ULID 중에서 선택하게 됩니다. UUID는 더 널리 채택되고 더 많은 지원을 받지만, ULID는 시간순서대로 정렬할 수 있는 이점을 제공합니다. 여러분의 기사에서는 두 식별자 간의 트레이드오프, 특히 한 쪽이 다른 쪽보다 선호되는 사용 사례를 탐구할 수 있습니다.

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

# UUID 및 ULID의 Java 예제:

UUID 예제:

```java
import java.util.UUID;

public class UUIDExample {
    public static void main(String[] args) {
        // 새 UUID 생성
        UUID uuid = UUID.randomUUID();
        System.out.println("UUID: " + uuid.toString());

        // 문자열에서 UUID 파싱
        String uuidString = "123e4567-e89b-12d3-a456-426655440000";
        UUID parsedUuid = UUID.fromString(uuidString);
        System.out.println("파싱된 UUID: " + parsedUuid);

        // 두 UUID 비교
        if (uuid.equals(parsedUuid)) {
            System.out.println("두 UUID가 동일합니다.");
        } else {
            System.out.println("두 UUID가 동일하지 않습니다.");
        }
    }
}
```

ULID 예제:

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

```java
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.node.ObjectNode;
import com.github.brianrubin.ulid.ULID;

public class ULIDExample {
    public static void main(String[] args) {
        // 새 ULID 생성
        ULID ulid = ULID.randomULID();
        System.out.println("ULID: " + ulid.toString());

        // 문자열에서 ULID 파싱
        String ulidString = "01GKKH6DAT6DS3B7MVTX3ABD2F";
        ULID parsedUlid = ULID.fromString(ulidString);
        System.out.println("파싱된 ULID: " + parsedUlid);

        // 두 ULID 비교
        if (ulid.equals(parsedUlid)) {
            System.out.println("ULID가 동일합니다.");
        } else {
            System.out.println("ULID가 다릅니다.");
        }

        // ULID를 JSON 객체로 변환
        ObjectMapper mapper = new ObjectMapper();
        ObjectNode jsonNode = mapper.createObjectNode();
        jsonNode.put("id", ulid.toString());
        System.out.println("ULID를 JSON으로: " + jsonNode.toString());
    }
}
```

UUID 예제에서는 새 UUID 생성, 문자열에서 UUID 파싱, 두 UUID 비교하는 방법을 보여줍니다.

ULID 예제에서는 com.github.brianrubin.ulid.ULID 라이브러리를 사용하여 ULID를 다루는 방법을 보여줍니다. 새 ULID 생성, 문자열에서 ULID 파싱, 두 ULID 비교, ULID를 JSON 객체로 변환하는 방법을 보여줍니다.

두 방식의 주요 차이점은 ULID가 식별 정보를 사전순으로 정렬할 수 있도록 설계되었으며 UUID와는 다르게 타임스탬프 구성 요소를 포함한다는 점입니다. 특정 응용 프로그램에서 유용할 수 있습니다.
