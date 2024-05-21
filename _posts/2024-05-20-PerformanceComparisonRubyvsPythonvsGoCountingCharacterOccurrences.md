---
title: "성능 비교 Ruby 대 Python 대 Go  문자 발생 횟수 세기"
description: ""
coverImage: "/assets/img/2024-05-20-PerformanceComparisonRubyvsPythonvsGoCountingCharacterOccurrences_0.png"
date: 2024-05-20 15:51
ogImage: 
  url: /assets/img/2024-05-20-PerformanceComparisonRubyvsPythonvsGoCountingCharacterOccurrences_0.png
tag: Tech
originalTitle: "Performance Comparison: Ruby vs. Python vs. Go — Counting Character Occurrences"
link: "https://medium.com/@vishalsadriya1224/performance-comparison-ruby-vs-python-vs-go-counting-character-occurrences-e824b5918106"
---


<img src="/assets/img/2024-05-20-PerformanceComparisonRubyvsPythonvsGoCountingCharacterOccurrences_0.png" />

# 소개

저는 얼마 동안 Ruby로 작업해오다 최근 Python과 Go를 배우기 시작했습니다. 오늘은 릿코드 문제 1347번, '두 문자열을 애너그램으로 만들기 위한 최소 단계 수'를 해결했습니다. Ruby 언어로 쉽게 해결했는데 궁금해서 Python과 Go에서도 시도해 보았더니, 놀랄 정도로 Go가 다른 두 언어와 비교했을 때 얼마나 빠르고 효율적인지에 깜짝 놀랐습니다. 이 차이를 보는 것이 흥미로운데, 더 많은 코딩 모험에 대해 더 많이 공유하고 싶습니다!

이 성능 탐구에서는 간단한 알고리즘을 세 가지 인기 프로그래밍 언어인 Ruby, Python 및 Go에서 실행 시간과 메모리 사용량에 대해 탐구합니다. 작업은 문자열에서 문자 발생 횟수를 계산하고 한 문자열을 다른 문자열로 변환하는 데 필요한 최소 단계를 계산하는 것을 포함합니다. 각 언어로 프로그램을 작성하는 것을 넘어, 이 분석은 Ruby, Python 및 Go가 도전 과제를 어떻게 처리하는지에 대해 밝혀내어, 실행 시 동작 및 메모리 효율성에 대한 통찰을 제공합니다. 이 언어들의 동작을 보다 깊게 이해하기 위해 계속 주목해 주세요!

<div class="content-ad"></div>

루비 구현:

```js
def min_steps(s, t)
  min_steps = 0
  h = Hash.new(0)
  s.each_char do |c|
    h[c] += 1
  end

  t.each_char do |c|
    h[c].positive? ? h[c] -= 1 : min_steps += 1
  end
  min_steps
end
```

파이썬 구현:

```js
class Solution:
    def minSteps(self, s: str, t: str) -> int:
        d = {}
        count = 0
        for c in s:
            d[c] = 1 if not d.get(c) else d[c] + 1
        
        for c in t:
            if d.get(c):
                d[c] -= 1
            else:
                count += 1
        
        return count
```

<div class="content-ad"></div>

Go 구현:

```js
func minSteps(s string, t string) int {
    minSteps := 0
    h := make(map[rune]int)

    for _, c := range s {
        h[c]++
    }

    for _, c := range t {
        if h[c] > 0 {
            h[c]--
        } else {
            minSteps++
        }
    }

    return minSteps
}
```

결과:


| 언어     | 실행 시간 (밀리초) | 메모리 사용량 (메가바이트) |
|----------|------------------|--------------------------|
| Ruby     | 504              | 218.2                    |
| Python   | 164              | 17.9                     |
| Go       | 58               | 6.6                      |


<div class="content-ad"></div>

# 관찰 및 설명:

루비, 파이썬 및 Go로 구현된 제공된 알고리즘의 성능 비교에서 런타임 및 메모리 사용량에 상당한 차이가 관찰되었습니다. 이러한 차이는 각 프로그래밍 언어의 독특한 설계 철학과 실행 모델에서 비롯됩니다.

- 런타임 차이:

- 루비: 루비는 해석형 언어로, 컴파일된 언어와 비교해 일반적으로 더 높은 런타임을 갖습니다.
- 파이썬: 파이썬은 루비보다 더 좋은 런타임 성능을 갖지만 Go에는 미치지 못합니다.
- Go: Go는 정적 형식의 컴파일 언어로, 강력한 성능 특성을 갖추어 가장 빠른 런타임을 제공합니다.

<div class="content-ad"></div>

## 2. 메모리 사용량:

- Ruby와 Python: 이러한 언어들은 특히 동적 상황에서 더 높은 메모리 사용량으로 알려져 있습니다.
- Go: 효율성을 위해 설계된 Go는 낮은 메모리 소비로 이어집니다.

## 3. 언어 디자인:

- Ruby와 Python: 동적 타입 및 고수준 추상화는 유연성에 기여하지만 성능에 영향을 줄 수 있습니다.
- Go: 정적 타입 및 컴파일된 Go는 간결함과 성능을 강조합니다.

<div class="content-ad"></div>

4. Garbage Collection:

- 루비와 파이썬: 자동 메모리 관리(가비지 컬렉션)는 오버헤드를 발생시킬 수 있습니다.
- Go: 효율적인 가비지 컬렉션 전략을 갖추어 낮은 메모리 사용량을 제공합니다.

5. 실행 환경:

- 루비와 파이썬: 인터프리터 언어는 런타임 환경에 의존하여 오버헤드가 발생할 수 있습니다.
- Go: 네이티브 이진 파일이 컴파일되어 인터프리터를 필요로 하지 않고 빠른 실행이 가능합니다.

<div class="content-ad"></div>

# 결론:

이 비교에서 관찰된 성능 차이는 언어 특징, 런타임 환경 및 메모리 관리 전략 사이의 트레이드오프를 강조합니다. 정적으로 타입 지정되고 컴파일된 Go 언어는 런타임 및 메모리 사용량 측면에서 우수한 성능을 보여줍니다. 개발자 친화적인 문법과 동적 기능을 제공하는 Python과 Ruby는 사용 편의성을 위해 성능을 어느 정도 포기할 수 있습니다. 이러한 차이를 이해하면 개발자들이 개발 속도, 사용 편의성 및 성능 요구사항과 같은 요소를 균형있게 고려해 특정 사용 사례에 적합한 언어를 선택할 수 있습니다.