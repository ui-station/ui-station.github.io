---
title: "깊이 우선 탐색DFS 방법으로 JSON 조작 탐구하기"
description: ""
coverImage: "/assets/img/2024-06-23-ExploringJSONManipulationUsingDepth-FirstSearchDFSApproach_0.png"
date: 2024-06-23 20:33
ogImage:
  url: /assets/img/2024-06-23-ExploringJSONManipulationUsingDepth-FirstSearchDFSApproach_0.png
tag: Tech
originalTitle: "Exploring JSON Manipulation Using Depth-First Search (DFS) Approach"
link: "https://medium.com/@yatharthmishra01/title-exploring-json-manipulation-using-depth-first-search-dfs-approach-0d2a58285fbe"
---

![2024-06-23-ExploringJSONManipulationUsingDepth-FirstSearchDFSApproach_0.png](/assets/img/2024-06-23-ExploringJSONManipulationUsingDepth-FirstSearchDFSApproach_0.png)

# 소개

우리 조직의 애플리케이션 기능을 향상시키는 과정에서, JSON 조작에 관련된 매력적인 문제를 만났습니다. 특정 경로에 있는 JSON 노드의 값을 수정하는 작업은 트리 구조를 탐색하는 것과 유사한 문제입니다. 이를 해결하기 위해 Depth-First Search (DFS) 접근 방식을 활용해보았고, 이 방법이 이 문제를 효과적으로 해결하는 데 도움이 되었습니다.

# 문제 이해하기

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

JSON (JavaScript Object Notation)은 컴퓨터 과학에서 사용되는 트리와 유사한 계층 구조와 간결함으로 인해 데이터 교환에 널리 사용됩니다. 문제는 이와 같은 트리 구조 내에서 특정 경로의 값을 동적으로 액세스하고 수정해야 하는 것이 필요했습니다. 예를 들어, 경로 a.b.c에서 '"a": '"b": '"c": 123''를 변환하는 것은 전형적인 JSON 탐색 및 수정을 보여줍니다.

# 접근 방식 및 통찰

## 1. JSON을 트리로 다루기

JSON의 중첩된 키-값 쌍은 트리에서 노드로 해석될 수 있으며, 객체는 내부 노드를 나타내고 배열은 노드들의 집합을 나타냅니다. 각 노드는 루트에서 리프 노드까지 탐색하는 것과 유사하게 경로를 통해 액세스할 수 있습니다.

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

## 2. 깊이 우선 탐색 (DFS) 응용

DFS는 기본적인 트리 순회 알고리즘으로, 다음 문제 해결에 중요한 역할을 했습니다:

- 재귀적 탐색: 루트(최상위 객체)에서 시작하여 중첩된 객체와 배열을 재귀적으로 탐색합니다.
- 경로 탐색: 제공된 경로 문자열 (a.b.c - `["a", "b", "c"])을 사용하여 중첩된 구조물을 효율적으로 탐색합니다.
- 유연한 수정: 대상 노드를 식별한 후, 간단한 값 업데이트 또는 배열 내 요소 변환 등 구체적인 요구에 따라 값을 수정할 수 있습니다.

## 3. 구현 여정

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

- JSON 파싱: Java의 org.json과 같은 라이브러리를 활용하여 JSON 객체를 분석하고 조작하는 작업을 원활하게 수행했습니다.
- 경로 처리: 경로 문자열을 동적으로 분할하고 해석하여 중첩된 객체를 탐색하는 데 활용했습니다.
- 시나리오 처리: JSON 객체 내에 배열이 존재하는 경우와 같은 시나리오를 다루며, 모든 요소가 수정되어야 하는 경우를 대응했습니다.

# 코드 구현 예제

```java
import org.json.*;

public class JsonManipulation {

    public static String modifyJsonNode(String json, String path) {
        JSONObject jsonObj = new JSONObject(json);
        String[] keys = path.split("\\.");
        modifyNode(jsonObj, keys, 0);
        return jsonObj.toString();
    }

    private static void modifyNode(JSONObject jsonObj, String[] keys, int index) {
        String key = keys[index];
        if (index == keys.length - 1) {
            if (jsonObj.get(key) instanceof JSONArray) {
                JSONArray array = jsonObj.getJSONArray(key);
                for (int i = 0; i < array.length(); i++) {
                    array.put(i, "modified");
                }
            } else {
                jsonObj.put(key, "modified");
            }
        } else {
            modifyNode(jsonObj.getJSONObject(key), keys, index + 1);
        }
    }

    public static void main(String[] args) {
        String json = "{\"a\": {\"b\": {\"c\": 123}}";
        String path = "a.b.c";
        System.out.println(modifyJsonNode(json, path));
    }
}
```

# 결론

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

DFS를 활용한 JSON 조작 탐구는 이 알고리즘이 JSON과 같은 계층 구조 데이터를 탐색하고 수정하는 다양성을 강조합니다. DFS 원칙을 적용함으로써 우리는 특정 경로를 기반으로 JSON 노드에 동적으로 접근하고 수정하는 작업을 효율적으로 처리했습니다.

소프트웨어 엔지니어로서 DFS와 같은 알고리즘 기법을 받아들이면 복잡한 데이터 조작 작업을 자신 있게 다룰 수 있습니다. 이 여정은 일상적인 코딩 과제에서 알고리즘적 사고의 중요성을 강조하며 문제 해결 능력과 소프트웨어 개발 혁신 능력을 함께 향상시킵니다.
