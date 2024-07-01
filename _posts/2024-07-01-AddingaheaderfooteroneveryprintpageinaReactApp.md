---
title: "React 앱에서 모든 인쇄 페이지에 헤더와 푸터 추가하는 방법"
description: ""
coverImage: "/assets/img/2024-07-01-AddingaheaderfooteroneveryprintpageinaReactApp_0.png"
date: 2024-07-01 16:28
ogImage: 
  url: /assets/img/2024-07-01-AddingaheaderfooteroneveryprintpageinaReactApp_0.png
tag: Tech
originalTitle: "Adding a header , footer on every print page in a React App"
link: "https://medium.com/readytowork-org/adding-a-header-footer-on-every-print-page-in-a-react-app-66ceccf9b35c"
---


프런트엔드 애플리케이션을 개발 중일 때, 종종 화면에 표시된 뷰를 인쇄해야 할 필요가 발생합니다. 더불어, 출력된 문서가 화면의 스크린샷이 아니라 하드코피용으로 개인화된 뷰로 보이도록 몇 가지 변경을 해야 합니다. 이 글에서는 헤더 및 푸터의 외관을 인쇄 시 마다 모든 페이지에 표시되도록 변경하는 개인화된 변경 중 하나를 살펴보겠습니다. 그러면 공식 문서처럼 보일 것입니다.

![이미지](/assets/img/2024-07-01-AddingaheaderfooteroneveryprintpageinaReactApp_0.png)

이를 실현하는 데 필요한 꿀팁은 꽤 간단합니다. 인쇄할 콘텐츠를 테이블 안으로 감싸고, 헤더를 thead 태그 내에 보이도록, 푸터는 tfoot 태그 안에 보이도록 포함하면 됩니다. 이 방법은 CSS, JavaScript 및 브라우저의 인쇄 기능에 기반하고 있으며 추가 패키지가 필요하지 않습니다. 따라서 React 애플리케이션 뿐만 아니라 다른 프런트엔드 자바스크립트 프레임워크에도 적용 가능합니다. 그러나 단점은 브라우저의 호환성에 크게 의존한다는 것입니다.

![이미지](/assets/img/2024-07-01-AddingaheaderfooteroneveryprintpageinaReactApp_1.png)

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

이제 리액트 애플리케이션을 먼저 만들어 구현을 살펴보겠습니다.

```js
npx create-react-app react-print
```

다음으로 인쇄할 내용을 받아 헤더와 푸터로 래핑하는 컴포넌트를 만듭니다. 해당 컴포넌트는 클릭할 때 인쇄를 트리거하는 버튼과 thead 내부에 헤더 컨텐츠, tbody 내부에 인쇄할 내용, tfoot 내부에 푸터 컨텐츠를 가진 테이블을 렌더링합니다. 데모를 위해 리액트 로고와 헤더로 텍스트, 푸터로 간단한 텍스트를 보여줍니다.

```js
import logo from './../logo.svg';
const PrintComponent = ({children}) => {
    const printAction = () => {
        window.print()
    }
    return (<>
          <button className={"print-preview-button"} onClick={printAction}>{"Print Preview"}</button>
        <table className="print-component">
            <thead>
                <tr>
                <th>
                <img src={logo} height={"40px"} width={"40px"} alt="로고" />
                <div>
                {"페이지 헤더"}
                </div>
                </th>
                </tr>
            </thead>
            <tbody>
                <tr>
                <td>
                    {children}
                </td>
                </tr>
            </tbody>
            <tfoot className="table-footer">
                <tr>
                <td>
                {"페이지 푸터"}
                </td>
                </tr>
            </tfoot>
        </table>
        </>
    )
}

export default PrintComponent
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

첫째로 버튼은 초기에 표시되고 인쇄 페이지에는 숨겨져야 하며, 내용은 초기에 숨겨져 있고 인쇄 페이지에만 표시되어야 합니다. 따라서 이 고려사항을 염두에 두고 여기에 애플리케이션을 위한 간단한 스타일시트가 있습니다.

```js
.print-preview-button {
  @media print {
    display: none;
  }
}

.print-component {
  display: none;
  @media print {
    display: table;
    .테이블-풋터 > tr > td{
      text-align: center;
      background-color: grey;
      color: white;
    }
  }
}
```

단순히 주요 App.js 내에서 컴포넌트를 가져와 필요한 내용을 자녀로 전달함으로써, 버튼을 클릭한 후의 결과를 볼 수 있습니다.

```js
import './App.css';
import PrintComponent from './Components/Print';

function App() {
  return (
    <div>
      <PrintComponent>
        <div>
          {`What is Lorem Ipsum?
          // 내용 생략...
          `}
        </div>
        <div>
          {`What is Lorem Ipsum?
          // 내용 생략...
          `}
        </div>
      </PrintComponent>
    </div>
  );
}

export default App;
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

버튼을 클릭하면 아래 이미지에서 볼 수 있듯이 헤더와 푸터가 두 페이지 모두에 나타납니다.

![image1](/assets/img/2024-07-01-AddingaheaderfooteroneveryprintpageinaReactApp_2.png)

![image2](/assets/img/2024-07-01-AddingaheaderfooteroneveryprintpageinaReactApp_3.png)

인쇄 페이지의 모든 페이지에 헤더 및 푸터를 표시하는 데 성공했습니다. 그러나 작은 문제가 있습니다. 두 번째 페이지에서 푸터가 페이지 하단이 아닌 콘텐츠가 끝나는 위치에 있음을 볼 수 있습니다. 이를 수용하는 요구사항을 가진 사람들에게는 추가 사용자 정의가 필요하지 않을 수 있습니다. 그러나 푸터가 항상 페이지 하단에 나타나기를 원하는 사람들을 위해 아래에서 논의된 방법을 적용해야 합니다.

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

이를 달성하는 데 필요한 트릭은 우리 앞에 있습니다. 이미지에서 볼 수 있듯이, 첫 번째 페이지에는 페이지 하단에 푸터가 있으므로 구성 요소에 적절한 높이를 설정하면 푸터가 페이지 하단에 나타납니다. 그러나 우리는 이를 수행하는 데 일부 문제가 있습니다. 먼저, 그 적합한 높이는 무엇인가요? 게다가, 컨텐츠는 처음에 display: none으로 설정되어 있기 때문에 높이가 0입니다. 이 모든 것들을 다음 접근법에서 처리할 것입니다.

- 컨텐츠가 차지할 높이를 가져옵니다.
- 인쇄용으로 해당 높이를 설정합니다.

고지: 아래 구현은 A4 포트레이트 인쇄 모드를 기준으로 수행되었습니다. 접근법은 동일하지만 사용된 숫자는 선호에 따라 다를 수 있습니다.

A4 크기의 포트레이트 모드에서 1 페이지에 이상적인 높이는 1045px입니다. 이는 대략적인 값이며 필요에 부합하지 않는 경우 이를 변경해 보세요. 이는 계산의 시작점이며 이에 따라 달라질 것입니다. 마찬가지로, 720px의 폭은 동일한 구성을 갖는 기본 여백에 이상적인 폭입니다.

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

이제 주어진 너비에 대한 콘텐츠가 차지하는 총 높이를 계산할 것입니다. 인쇄 미리보기가 표시되기 전에 인쇄 구성 요소에 클래스를 추가하여 해당 높이를 결정할 수 있도록 스타일을 변경합니다. 클래스에는 다음과 같은 스타일이 있습니다.

```js
.temp-class-for-height {
// 프린트 스타일과 충돌하지 않도록 미디어가 인쇄되지 않음
  @media not print {
  // A4 세로 방향
  width: 720px;
  visibility: hidden;
  display: table;
  }
}
```

```js
const printElement = document.getElementById("print-component")
printElement.classList.add("temp-class-for-height")
const height = printElement.clientHeight
```

이 시점에서 얻는 높이는 단일 헤더 및 푸터가 있지만 인쇄 페이지에는 각 페이지에 존재합니다. 따라서 필요한 총 페이지 수를 결정해야 합니다. 이미 각 인쇄 페이지의 높이 표준을 설정했으므로 총량을 다음과 같이 결정할 수 있습니다.

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

```js
const numberOfPage = Math.ceil(height / PAGE_HEIGHT)
```

만약 페이지 수가 1이라면, 이미 헤더와 푸터를 한 번씩 표시했다고 가정하므로 더 이상 계산할 필요가 없습니다. 그러나 여러 번 표시해야 하는 경우, 필요한 높이를 아래와 같이 계산할 수 있습니다.

```js
let requiredHeight = heightWithSingleHeader
if (numberOfPage > 1) {
    const headerHeight = printElement.getElementsByTagName("thead")?.[0]?.clientHeight || 0
    const footerHeight = printElement.getElementsByTagName("tfoot")?.[0]?.clientHeight || 0
    requiredHeight -= (numberOfPage - 1) * (headerHeight + footerHeight) 
}
```

이제 계산된 높이로 인쇄 구성요소의 높이를 설정하고 인쇄를 시작하겠습니다.

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

```js
printElement.style.height = `${requiredHeight}px`
window.print()
```

이 시점에, 우리는 원하는 형식의 인쇄 미리 보기를 볼 수 있을 것입니다. 그러나 최근에 높이를 결정하기 위해 한 조작들은 일시적인 변화였기 때문에 되돌릴 수 있습니다. 따라서 미리 보기가 표시된 후에 추가된 클래스를 제거하고 높이를 초기 레이아웃과 같은 상태로 설정하겠습니다.

```js
printElement.classList.remove("temp-class-for-height")
printElement.style.height = `auto
```

모든 이러한 변경 사항은 인쇄 동작을 처리하는 함수 내에서 이루어졌으므로 최종 변경 사항은 다음과 같습니다.

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

```js
const printAction = () => {
  const PAGE_HEIGHT = 1045;
  const printElement = document.getElementById("print-component");
  if (printElement) {
    printElement.classList.add("temp-class-for-height");
    const height = printElement.clientHeight;
    const numberOfPage = Math.ceil(height / PAGE_HEIGHT);
    const heightWithSingleHeader = numberOfPage * PAGE_HEIGHT;
    let requiredHeight = heightWithSingleHeader;
    if (numberOfPage > 1) {
      const headerHeight = printElement.getElementsByTagName("thead")?.[0]?.clientHeight;
      const footerHeight = printElement.getElementsByTagName("tfoot")?.[0]?.clientHeight;
      requiredHeight -= (numberOfPage - 1) * (headerHeight + footerHeight);
    }
    printElement.style.height = `${requiredHeight}px`;
    printElement.classList.remove("temp-class-for-height");
  }
  window.print();
  if (printElement) {
    printElement.style.height = `auto`;
  }
}
```

두 번째 인쇄 페이지의 최종 출력물은 아래와 같이 표시되며, 콘텐츠가 그리 길지 않은 경우에도 푸터가 하단에 표시됩니다.

<img src="/assets/img/2024-07-01-AddingaheaderfooteroneveryprintpageinaReactApp_4.png" />

유사한 경우에 해결책을 찾는 사람들에게 도움이 되길 바랍니다.
