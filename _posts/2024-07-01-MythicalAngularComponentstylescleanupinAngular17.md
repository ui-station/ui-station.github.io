---
title: "Angular 17의 컴포넌트 스타일 정리 방법"
description: ""
coverImage: "/assets/img/2024-07-01-MythicalAngularComponentstylescleanupinAngular17_0.png"
date: 2024-07-01 16:20
ogImage: 
  url: /assets/img/2024-07-01-MythicalAngularComponentstylescleanupinAngular17_0.png
tag: Tech
originalTitle: "Mythical Angular — Component styles cleanup in Angular 17"
link: "https://medium.com/@kamil.galek/mythical-angular-component-styles-cleanup-in-angular-17-f799a08b2abc"
---


안녕하세요!

최신 릴리스 노트에 언급되지 않은 흥미로운 동작을 발견했습니다. 이 기사에서는 이것이 무엇인지 보여드리고, 왜 그것이 꽤 흥미로운 변화라고 생각하는지 알려드리겠습니다.

# 스타일 정리 문제

Angular가 동적으로 생성된 구성 요소를 제거한 후에도 스타일을 제거하지 않는 문제를 발견했습니다. 즉, 동적 구성 요소를 생성하면 (심지어 DOM에 연결할 필요도 없이), Angular가 스타일을 추가한 후 구성 요소가 제거될 때 스타일을 제거하지 않는다는 것이죠.

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

검사하고 테스트하는 것은 매우 쉽습니다.

# 테스트

항상 모든 코드는 내 GitHub에서 제공됩니다. experiment-styles-cleanup 저장소를 찾아보세요.

저는 응용 프로그램에 스타일을 추가하는 것 외에 아무 것도 하지 않는 간단한 구성 요소로 테스트를 시작합니다.

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
import {Component, ViewEncapsulation} from '@angular/core';

@Component({
  selector: 'app-dynamic',
  template: '',
  encapsulation: ViewEncapsulation.None,
  styles: [`
    .test {
      background: red;
      color: white;
      border: 4px dashed black;
      font-weight: bold;
    }
  `]
})
export class DynamicComponent {
}
```

여기서 볼 수 있듯이, 꽤 간단합니다. 템플릿도 로직도 없고, 스타일만 있습니다. 중요한 점은 ViewEncapsulation.None으로 캡슐화를 설정했기 때문에 이 스타일을 컴포넌트 외부에 적용할 수 있다는 것입니다.

다음으로, 루트 AppComponent를 수정해보겠습니다.

```js
import {Component, ComponentFactoryResolver, inject, Injector, OnDestroy} from '@angular/core';
import {DynamicComponent} from "./dynamic.component";

@Component({
  selector: 'app-root',
  template: `
    <div class="test">
      이 상자가 빨간색일 때 스타일이 적용됩니다
    </div>

    <button (click)="createComponent()">눌러보세요!</button>
  `
})
export class AppComponent {
  private readonly componentFactoryResolver = inject(ComponentFactoryResolver);
  private readonly injector = inject(Injector);

  createComponent(): void {
    const factory = this.componentFactoryResolver.resolveComponentFactory(DynamicComponent);
    const componentRef = factory.create(this.injector);

    setTimeout(() => componentRef.destroy(), 5000);
  }
}
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

AppComponent에서 두 가지가 있습니다.

첫 번째는 test 클래스가 있는 div입니다. Angular이 DynamicComponent에서 스타일을 추가할 때 스타일이 적용됩니다. 그래서 캡슐화를 ViewEncapsulation.None으로 설정했습니다.

두 번째로는 버튼이 있습니다. 클릭하면 ComponentFactoryResolver를 사용하여 동적으로 DynamicComponent를 생성한 후 5초 후에 제거합니다. setTimeout에 메모리 누수가 있음을 알고 있지만 이것은 테스트일 뿐이니 괜찮아요?

전체 테스트는 간단합니다. 버튼을 클릭하면 div에 스타일이 적용되어야 합니다.

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


![한국어](/assets/img/2024-07-01-MythicalAngularComponentstylescleanupinAngular17_0.png)

5초간 기다리고, 두 가지 가능한 종료가 있습니다:

- div에 스타일이 있는 경우 - 해당 컴포넌트의 스타일이 제거되지 않았음을 의미합니다
- div에 스타일이 없는 경우 - Angular가 컴포넌트 제거 후 정리를 수행 중임을 의미합니다

# Angular 15 & 16


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

아마도 예상했겠지만, Angular 15와 16에서는 컴포넌트와 함께 스타일이 제거되지 않습니다. experiment-styles-cleanup 저장소의 코드를 사용하여 직접 해볼 수 있어요. 주요 브랜치와 v16 브랜치를 사용해 보세요.

![이미지](/assets/img/2024-07-01-MythicalAngularComponentstylescleanupinAngular17_1.png)

참고: 시간이 부족하다면 권장합니다. GitHub Pages를 통해 배포한 코드를 확인할 수 있어요. 다음은 링크입니다:

- [v15 링크](galczo5.github.io/experiment-styles-cleanup/v15)
- [v16 링크](galczo5.github.io/experiment-styles-cleanup/v16)

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

# 앵귤러 17

앵귤러 17로 업그레이드한 후에 동작이 변경되었습니다. 이제 스타일은 컴포넌트와 함께 제거됩니다.

내 저장소의 v17 브랜치를 확인하거나 galczo5.github.io/experiment-styles-cleanup/v17 애플리케이션을 사용해보세요.

![이미지](/assets/img/2024-07-01-MythicalAngularComponentstylescleanupinAngular17_2.png)

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

# Non-dynamic 컴포넌트와의 작업 방식은 어떤가요?

가끔 이러한 경우는 특이한 경우라고 생각할 수 있고, 우리가 많은 동적 컴포넌트를 만들지 않기 때문에 중요하지 않을 수도 있어요.

일반 ngIf 문장으로 어떻게 작동하는지 확인해봅시다.

이를 확인하려면 AppComponent의 코드에 필요한 변경 사항을 적용해야 했어요.

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

```javascript
import {Component, ComponentFactoryResolver, inject, Injector, OnDestroy} from '@angular/core';
import {DynamicComponent} from "./dynamic.component";

@Component({
  selector: 'app-root',
  template: `
    <div class="test">
      이 상자가 빨간색이 될 때 적용되는 스타일
    </div>

    <app-dynamic *ngIf="visible"/>

    <button (click)="createComponent()">클릭!</button>
  `
})
export class AppComponent {
  visible = false;

  createComponent(): void {
    this.visible = true;
    setTimeout(() => this.visible = false, 5000);
  }
}
```

결과는 예상대로입니다.

Angular 15 및 16 — 스타일이 제거되지 않음

- 브랜치 v15-ngIf 및 galczo5.github.io/experiment-styles-cleanup/v15-ngIf/
- 브랜치 v16-ngIf 및 galczo5.github.io/experiment-styles-cleanup/v16-ngIf/ 


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

안녕하세요! 다음과 같이 테이블 태그를 마크다운 형식으로 변경해주세요.


Angular 17 — 컴포넌트가 파괴된 후에 스타일이 제거됩니다

- Branch 17-ngIf 및 galczo5.github.io/experiment-styles-cleanup/v17-ngIf/

그래서, 동적으로 생성된 컴포넌트와 정확히 똑같이 동작합니다. 추가로 제거되지 않은 스타일이 브라우저에 얼마나 무겁게 작용하는지는 확신할 수 없지만, 스타일이 적을수록 처리하기 쉬울 것으로 예상됩니다. 아마 다음에는 측정해 볼 것입니다.

# 캡슐화된 스타일에 대해 어떤가요?


도움이 되었기를 바랍니다. 추가 설명이 필요하시면 언제든지 물어보세요.

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

내 모든 테스트에서는 ViewEncapsulation.None을 사용하여 어떻게 작동하는지 시각적으로 보여주었어. 기본으로 에뮬레이션된 캡슐화로 작동하는 지 확인하기 위해 컴포넌트 제거 후 DOM을 확인하기 위해 개발 도구를 사용했어. 문서의 `head` 부분에서 무슨 일이 일어나고 있는지 관찰해봐.

나에게 결과는 다음과 같았어:

- Angular 15 & 16 — 스타일이 제거되지 않아
- Angular 17 — 스타일이 제거되어

기본 캡슐화에서도 동일하게 작동해.

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

# 요약

솔직히 말해서, 컴포넌트를 파괴할 때 Angular가 기본적으로 스타일을 제거하지 않는다는 사실을 깨달을 때 약간 놀랐습니다. 몇 달 전에 물었더라면 Angular가 스타일을 제거한다고 베팅했을 것입니다.

제 생각으로는 Angular 17에서 도입된 변경 사항이 모든 개발자에게 더 직관적일 것으로 확신합니다.

요약하면, 제 테스트에 따르면 Angular 17 이전에 동적 컴포넌트든 ngIf를 사용하여 조건적으로 추가된 컴포넌트든 모든 컴포넌트가 파괴된 후에 추가된 모든 스타일이 제거되지 않았습니다. Angular 17은 이 동작을 변경하여 이제 컴포넌트 이후 불필요한 스타일이 정리됩니다.