---
title: "모든 안드로이드 개발자가 알아야 할 ObjectAnimator로 애니메이션 구현하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-EveryAndroiddevelopershouldknowaboutObjectAnimatortouseanimationsinyourApps_0.png"
date: 2024-06-23 21:04
ogImage: 
  url: /assets/img/2024-06-23-EveryAndroiddevelopershouldknowaboutObjectAnimatortouseanimationsinyourApps_0.png
tag: Tech
originalTitle: "Every Android developer should know about ObjectAnimator to use animations in your Apps"
link: "https://medium.com/@sandeepkella23/every-android-developer-should-know-about-objectanimator-to-use-animations-in-your-apps-1e6330de8873"
---


마크다운 형식으로 테이블 태그를 변경해주세요.

<div class="content-ad"></div>

- 목적: 객체의 속성을 시간에 따라 애니메이션화합니다.
- 상속: ValueAnimator로부터 상속받으며 그 기능을 확장하여 속성을 직접 애니메이션화합니다.

2. 속성 애니메이션 vs. 뷰 애니메이션:

- 속성 애니메이션: 뷰 속성뿐만 아니라 모든 객체의 속성을 애니메이션화합니다.
- 뷰 애니메이션: 위치, 크기, 회전 등과 같은 특정 뷰 속성에 대해 제한됩니다.

# 기본 사용법

<div class="content-ad"></div>

- ObjectAnimator를 만드는 방법:
  
- 방법: 정적 메서드를 사용하여 인스턴스를 생성합니다.
- 예시:

```js
ObjectAnimator animator = ObjectAnimator.ofFloat(targetView, "translationX", 0f, 100f);
animator.setDuration(1000);  // 1초
animator.start();
```

2. 애니메이트할 공통 속성:

<div class="content-ad"></div>

- alpha: 화면을 서서히 사라지게 합니다.
- translationX, translationY: 화면을 X 또는 Y 축을 따라 이동시킵니다.
- rotation, rotationX, rotationY: 피벗 지점을 중심으로 화면을 회전시킵니다.
- scaleX, scaleY: X 또는 Y 방향으로 화면의 크기를 조절합니다.

# 고급 사용법

- AnimatorSet:

- 목적: 여러 애니메이션을 함께 또는 순차적으로 실행합니다.
- 예시:

<div class="content-ad"></div>

```js
ObjectAnimator scaleX = ObjectAnimator.ofFloat(targetView, "scaleX", 1f, 1.5f);
ObjectAnimator scaleY = ObjectAnimator.ofFloat(targetView, "scaleY", 1f, 1.5f);
AnimatorSet animatorSet = new AnimatorSet();
animatorSet.playTogether(scaleX, scaleY);
animatorSet.setDuration(1000);
animatorSet.start();
```

2. PropertyValuesHolder:

- 목적: 하나의 애니메이터로 개체의 여러 속성을 동시에 애니메이션화합니다.
- 예시:

```js
PropertyValuesHolder pvhX = PropertyValuesHolder.ofFloat("scaleX", 1f, 1.5f);
PropertyValuesHolder pvhY = PropertyValuesHolder.ofFloat("scaleY", 1f, 1.5f);
ObjectAnimator animator = ObjectAnimator.ofPropertyValuesHolder(targetView, pvhX, pvhY);
animator.setDuration(1000);
animator.start();
```

<div class="content-ad"></div>

3. AnimatorListener:

- 목적: 애니메이션 이벤트에 대응합니다.
- 구현:

```js
PropertyValuesHolder pvhX = PropertyValuesHolder.ofFloat("scaleX", 1f, 1.5f);
PropertyValuesHolder pvhY = PropertyValuesHolder.ofFloat("scaleY", 1f, 1.5f);
ObjectAnimator animator = ObjectAnimator.ofPropertyValuesHolder(targetView, pvhX, pvhY);
animator.setDuration(1000);
animator.start();
```

4. Interpolator:

<div class="content-ad"></div>

- 목적: 애니메이션의 변화율을 제어합니다.
- 일반적으로 사용되는 보간기: LinearInterpolator, AccelerateInterpolator, DecelerateInterpolator, BounceInterpolator.
- 예시:

```js
animator.setInterpolator(new BounceInterpolator());
```

5. 키프레임:

- 목적: 애니메이션 타임라인에서 특정 지점을 정의합니다.
- 예시:

<div class="content-ad"></div>

```js
Keyframe kf0 = Keyframe.ofFloat(0f, 0f);
Keyframe kf1 = Keyframe.ofFloat(0.5f, 200f);
Keyframe kf2 = Keyframe.ofFloat(1f, 0f);
PropertyValuesHolder pvh = PropertyValuesHolder.ofKeyframe("translationX", kf0, kf1, kf2);
ObjectAnimator animator = ObjectAnimator.ofPropertyValuesHolder(targetView, pvh);
animator.setDuration(2000);
animator.start();
```

## Pratical Examples

- Fading a View:

```js
ObjectAnimator fadeOut = ObjectAnimator.ofFloat(targetView, "alpha", 1f, 0f);
fadeOut.setDuration(2000);
fadeOut.start();
```

<div class="content-ad"></div>

2. 바운스 애니메이션:

```js
ObjectAnimator bounceAnim = ObjectAnimator.ofFloat(targetView, "translationY", 0f, 300f);
bounceAnim.setInterpolator(new BounceInterpolator());
bounceAnim.setDuration(2000);
bounceAnim.start();
```

3. 연속 애니메이션:

```js
ObjectAnimator moveRight = ObjectAnimator.ofFloat(targetView, "translationX", 0f, 300f);
ObjectAnimator moveDown = ObjectAnimator.ofFloat(targetView, "translationY", 0f, 300f);
AnimatorSet set = new AnimatorSet();
set.playSequentially(moveRight, moveDown);
set.setDuration(2000);
set.start();
```

<div class="content-ad"></div>

## 최선의 실첀법

- 레이아웃 패스 최소화하기:

- onDraw와 같이 자주 호출되는 메서드에서 애니메이션을 생성하거나 시작하는 것을 피하세요.

2. 애니메이터를 재사용하세요.

<div class="content-ad"></div>

가능한 경우 ObjectAnimator 인스턴스를 재사용하여 성능을 향상시킵니다.

3. 하드웨어 가속화:

- 애니메이션을 부드럽게 실행하기 위해 하드웨어 가속화를 활용하세요.

4. 테스트:

<div class="content-ad"></div>

- 서로 다른 기기와 화면 크기에서 애니메이션을 테스트하여 일관된 동작을 보장하세요.

5. 사용자 정의 속성:

- PropertyValuesHolder 및 사용자 정의 속성 세터/게터를 사용하여 일반적이지 않은 속성을 애니메이션화하세요.

ObjectAnimator를 이해하고 효과적으로 활용함으로써 Android 애플리케이션의 사용자 경험을 향상시키는 매혹적이고 순조로운 애니메이션을 만들 수 있습니다.