---
title: "Python 정규 표현식을 사용하여 여러 공백을 하나의 공백으로 압축하기"
description: ""
coverImage: "/assets/img/2024-05-20-CondensingMultipleSpacesIntoOneSpaceUsingPythonRegex_0.png"
date: 2024-05-20 15:59
ogImage: 
  url: /assets/img/2024-05-20-CondensingMultipleSpacesIntoOneSpaceUsingPythonRegex_0.png
tag: Tech
originalTitle: "Condensing Multiple Spaces Into One Space Using Python Regex"
link: "https://medium.com/gitconnected/condensing-multiple-spaces-into-one-space-using-python-regex-8b5378bd9e45"
---



![image](/assets/img/2024-05-20-CondensingMultipleSpacesIntoOneSpaceUsingPythonRegex_0.png)

만약 여러 개의 공백 문자를 하나의 공백 문자로 변환해야 한다면, 이 기사가 여러분을 위한 것입니다.

```js
x = 'apple   orange  pear     pineapple'
```

^ 이 문자열에서 단어 사이에 여러 개의 공백이 있습니다. 여러 개의 공백을 하나의 공백으로 압축해야 합니다.

<div class="content-ad"></div>

```js
output = 'apple orange pear pineapple'
```

# 수동 방법

```js
x = 'apple   orange  pear     pineapple'

def condense(x):
    words = x.split(' ')
    words = [w for w in words if w]
    return ' '.join(words)

print(condense(x))

# apple orange pear pineapple
```

- .split()을 사용하면 `['apple', '', '', 'orange', '', 'pear', '', '', '', '', 'pineapple']` 와 같이 단어가 됩니다.
- 리스트 내포는 모든 빈 문자열을 필터링하고 [`apple`, `orange`, `pear`, `pineapple`]를 얻습니다.
- ` ` .join()은 이 4개 단어를 `apple orange pear pineapple`로 결합합니다.

<div class="content-ad"></div>

하지만 이 방법은 다소 수동적입니다. 한 줄의 코드로 정규식을 사용하여 이 문제를 해결할 수 있다는 걸 아셨나요?

# 정규식 방법

```js
x = 'apple   orange  pear     pineapple'

import re
print(re.sub(' +', ' ', x))

# apple orange pear pineapple
```

- re.sub은 정규식 ` +`와 일치하는 모든 문자열을 ` `로 바꿉니다.
- ` +`는 1개 이상의 공백을 포함하는 모든 문자열과 일치합니다.
- 이는 연속으로 여러 개의 공백이 포함된 모든 경우에 일치합니다.
- 각각의 경우가 한 개의 공백으로 대체됩니다.
- 이게 전부입니다.

<div class="content-ad"></div>

# 결론

이 내용을 이해하기 쉽고 명확했기를 바랍니다.

# 크리에이터로서 저를 지원하고 싶다면

- 이 이야기에 대해 50번 박수를 치세요
- 생각을 나누는 댓글을 남겨주세요
- 이야기에서 가장 마음에 드는 부분을 강조해주세요

<div class="content-ad"></div>

감사합니다! 이런 작은 행동들이 큰 도움이 되어요. 정말 감사드려요!

YouTube: [https://www.youtube.com/@zlliu246](https://www.youtube.com/@zlliu246)

LinkedIn: [https://www.linkedin.com/in/zlliu/](https://www.linkedin.com/in/zlliu/)

나의 이북: [https://zlliu.co/ebooks](https://zlliu.co/ebooks)