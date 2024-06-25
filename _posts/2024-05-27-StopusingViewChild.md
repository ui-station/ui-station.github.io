---
title: "ViewChild를 사용하지 말아요"
description: ""
coverImage: "/assets/img/2024-05-27-StopusingViewChild_0.png"
date: 2024-05-27 16:16
ogImage:
  url: /assets/img/2024-05-27-StopusingViewChild_0.png
tag: Tech
originalTitle: "Stop using @ViewChild"
link: "https://medium.com/@rk22mar/stop-using-viewchild-36479592bef7"
---

네이티브 DOM 요소에 액세스하는 것은 웹 개발에서 기본적인 작업입니다. 그러나 현대적인 프레임워크는 다양한 전략을 활용하여 직접 DOM 액세스의 필요성을 최소화합니다. 예를 들어 Angular는 *ngIf와 같은 디렉티브를 사용하여 요소를 조건부로 추가하거나 제거하고 *ngFor를 사용하여 요소를 반복합니다. 이러한 기술은 직접적인 DOM 조작을 추상화하여 Angular이나 유사한 프레임워크에서 어플리케이션을 개발할 때 덜 걱정해도 되도록 만듭니다.

이러한 추상화에도 불구하고 직접적인 DOM 액세스가 여전히 필요한 시나리오가 있습니다. 구글 맵과 같은 일부 써드파티 라이브러리는 동작을 위해 직접적인 DOM 요소가 필요합니다. 구글 맵은 맵을 초기화하고 표시하기 위해 div 요소가 필요합니다. 일반적으로 개발자는 @ViewChild 데코레이터를 사용하여 div 요소에 액세스하고 그것을 맵 API에 제공하여 초기화합니다.

```js
/// <reference types="@types/google.maps" />
import { AfterViewInit, Component, ElementRef, ViewChild } from '@angular/core';

@Component({
  selector: 'app-map-component',
  template: `<div class="map-container" #googleMap></div>`,
  styles: ['.map-container {height: 500px;}'],
})
export class MapComponentComponent implements AfterViewInit {
  @ViewChild('googleMap', { read: ElementRef }) googleMapElement!: ElementRef<HTMLDivElement>;
  map?: google.maps.Map;

  async ngAfterViewInit() {
    const { Map } = await google.maps.importLibrary("maps") as google.maps.MapsLibrary;
    // Pass the nativeElement to google map library
    this.map = new Map(this.googleMapElement.nativeElement, {
      center: { lat: -34.397, lng: 150.644 },
      zoom: 8,
    });
  }
}
```

이 접근 방식은 작동하지만 세 가지 주요 문제점을 도입합니다:

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

- 템플릿에서 div 요소를 제거하면 코드가 깨져서 맵 기능을 재사용할 수 없게 됩니다.
- 네이티브 요소에 대한 참조는 ngAfterViewInit 라이프사이클 후크 이후에만 사용할 수 있습니다.
- SOLID 디자인 원칙 중 단일 책임 원칙을 위반합니다. @ViewChild를 사용하는 컴포넌트는 맵을위한 데이터를 가져오는 것, 초기화하는 것, 설정하고 사용하는 것에 대한 책임이 생깁니다.

더 나은 접근 방식이 있습니다: 속성 지시자 사용.

이전 컴포넌트를 리팩토링하고 맵 속성 지시자를 만들어 봅시다.

```js
/// <reference types="@types/google.maps" />
import { Directive, ElementRef, OnInit } from '@angular/core';

@Directive({
  selector: 'div[appGoogleMap]'
})
export class GoogleMapDirective implements OnInit {
  map?: google.maps.Map;
  constructor(private elementRef: ElementRef<HTMLDivElement>) { }

  async ngOnInit() {
    const { Map } = await google.maps.importLibrary("maps") as google.maps.MapsLibrary;
    this.map = new Map(this.elementRef.nativeElement, {
      center: { lat: -34.397, lng: 150.644 },
      zoom: 8,
    });
  }
}

// 속성 지시자 사용
<div class="map-container" appGoogleMap></div>
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

다른 사용 사례를 고려해 보겠습니다: 캔버스 요소에 도형이나 이미지를 그리는 것입니다. 간단한 방법은 @ViewChild를 사용하여 네이티브 요소에서 그리기 컨텍스트를 가져오는 것입니다.

더 나은 대안은 속성 지시자를 사용하고 @Input 속성을 통해 그리기 정보를 전달하는 것입니다.

```js
import { Component, ElementRef, ViewChild } from '@angular/core';

@Component({
  selector: 'app-canvas-component',
  template: `<canvas #myCanvas></canvas>`,
})
export class CanvasComponentComponent {
  // 빌드 시 myCanvas를 확인할 수 있는 방법이 없음
  @ViewChild('myCanvas', { read: ElementRef }) canvasRef!: ElementRef<HTMLCanvasElement>;

  ngAfterViewInit() {
    const canvas = this.canvasRef.nativeElement;
    const canvasContext = canvas.getContext('2d');
    // 캔버스 컨텍스트를 사용하여 그리기
    canvasContext?.beginPath();
    canvasContext?.arc(75, 75, 50, 0, 2 * Math.PI);
    canvasContext?.stroke();
  }
}

// Vs
import { Directive, ElementRef } from '@angular/core';

@Directive({
  // 이 지시자는 Cavas 요소만 사용할 수 있습니다
  selector: 'canvas[appCanvas]'
})
export class CanvasDirective {
  constructor(private elementRef: ElementRef<HTMLCanvasElement>) {
    this.draw();
  }

  draw() {
    const canvas = this.elementRef.nativeElement;
    const canvasContext = canvas.getContext('2d');
    // 캔버스 컨텍스트를 사용하여 그리기
    canvasContext?.beginPath();
    canvasContext?.arc(75, 75, 50, 0, 2 * Math.PI);
    canvasContext?.stroke();
  }
}
// 속성 지시자 사용
<canvas appCanvas></canvas>
```

좋아 보이죠? 그런데 기다려 보세요, 더 있습니다.

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

우리는 첫 두 문제를 해결했지만 속성 지시자는 여전히 여러 책임을 처리하고 있습니다. 더 분해할 수 있을까요? 다음 블로그에서 그것을 더 탐구해보겠습니다.
