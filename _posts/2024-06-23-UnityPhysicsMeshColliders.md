---
title: "유니티 물리 시스템 메쉬 콜라이더 완벽 가이드"
description: ""
coverImage: "/assets/img/2024-06-23-UnityPhysicsMeshColliders_0.png"
date: 2024-06-23 00:11
ogImage: 
  url: /assets/img/2024-06-23-UnityPhysicsMeshColliders_0.png
tag: Tech
originalTitle: "Unity Physics — Mesh Colliders"
link: "https://medium.com/@geraldclarkaudio/unity-physics-mesh-colliders-fd37028adb26"
---


너는 Unity를 사용하면서 메시 콜라이더를 본 적이 있을 거야. 하지만 정확히 뭘까?

메시 콜라이더는 Unity가 해당 객체의 메시를 사용해 생성하는 콜라이더야. 예를 들어:

![이미지](/assets/img/2024-06-23-UnityPhysicsMeshColliders_0.png)

이 바럴이야. 메시 콜라이더 컴포넌트를 추가하면, 객체를 정확히 표현하는 콜라이더가 생성돼. 지금까지 다룬 다른 객체들처럼, 이 객체가 그냥 바닥으로 떨어지도록 원한다면.

<div class="content-ad"></div>

게임을 실행하면 오류가 발생합니다.

<img src="/assets/img/2024-06-23-UnityPhysicsMeshColliders_1.png" />

이 오류는 비볼록 메시 콜라이더에 키네마틱이 아닌 리짏바디가 연결된 경우 지원되지 않는다고 합니다. 키네마틱 리짏바디는 물리 엔진에서 무시되어 정체되고 물리 엔진에 영향을 받지 않아야 합니다.

하지만 이것의 장점은 무엇일까요? 리짏바디를 isKinematic으로 설정하고 이 객체를 사용하여 씬의 다른 리짏바디에 영향을 줄 수 있습니다. 예를들어,

<div class="content-ad"></div>


![sphere](https://miro.medium.com/v2/resize:fit:1400/1*Q7N3AFYNvo9bzu-BxBVkjw.gif)

As you can see, the sphere is affected by the shape of this barrel. If I use a non-convex mesh collider, the detail in the collider is slightly reduced, but it is still better than a capsule collider.

![collider](/assets/img/2024-06-23-UnityPhysicsMeshColliders_2.png)

Using the non-convex option also gives you a visual of the collider Unity generates.


<div class="content-ad"></div>

아래 테이블 태그를 Markdown 형식으로 변경해 주세요.


| Header One | Header Two |
|------------|------------|
| Row 1 Col 1 | Row 1 Col 2 |
| Row 2 Col 1 | Row 2 Col 2 |
