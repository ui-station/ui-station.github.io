---
title: "모노게임 XNA에서 물 쉐이더를 만드는 방법"
description: ""
coverImage: "/assets/img/2024-05-20-HowtocreateWatershaderinMonogameXNA_0.png"
date: 2024-05-20 16:37
ogImage: 
  url: /assets/img/2024-05-20-HowtocreateWatershaderinMonogameXNA_0.png
tag: Tech
originalTitle: "How to create Water shader in Monogame XNA"
link: "https://medium.com/@gabriel.enrique.digiorgio/how-to-create-water-shader-in-monogame-xna-0a7e82092c85"
---


# 소개

이 기사에서는 Monogame/XNA에서 간단한 물 쉐이더를 만드는 기본 개념을 다룰 것입니다. 이 쉐이더는 플레이너 반사, 굴절, 프레넬 효과 및 반사 하이라이트를 사용할 것입니다. 작성 시점에서 Monogame 버전 3.8.1로 진행되었습니다. 자세히 살펴보고 싶다면 여기 소스 코드가 있습니다.

# 기본 사항

우리 쉐이더가 어떻게 작동하는지 더 잘 이해하기 위해 여러 단계로 나눠 설명하겠습니다:

<div class="content-ad"></div>

- 쿼드/플랫 표면 추가하기
- 반사 및 굴절 텍스처 추가하기 (프레넬 효과를 사용하여 둘 사이의 러핑)
- 왜곡 맵을 사용하여 작은 파도 시뮬레이션하기
- 광택을 위한 노말 맵 추가하기

# 그리기

그려지는 순서는 다음과 같습니다:

- 굴절 그리기
- 반사 그리기
- 물 그리기
- 장면에서 다른 개체 그리기

<div class="content-ad"></div>

이 순서는 원하는 시각적 효과를 달성하기 위해 각 구성 요소가 올바른 순서로 렌더링되도록합니다.

# 코드 설정

우리의 워터 쉐이더 기술을 올바르게 구현하기 위해 코드에서 몇 가지 설정을 해야합니다. 설명을 간단히하기 위해 이미 정의된 QuadPrimitive 클래스를 사용할 것입니다. 이 클래스는 정점 버퍼(법선과 텍스처 좌표에 대한 정보를 보냄)와 인덱스 버퍼를 가집니다. 또한 FreeCamera 클래스를 사용하여 카메라 이동을 처리할 것이지만 이 튜토리얼의 주요 내용은 아니기 때문에 자세히 다루지 않겠습니다.

먼저 우리는 우리의 쿼드, 그의 월드 매트릭스, 높이 및 파도 속도를 생성할 것입니다. 나중에는 쉐이더 매개변수에서 파도 속도를 사용할 것입니다. 또한 이 높이는 반사와 굴절을 계산하는 중요한 요소가 될 것입니다. 게다가 우리는 쉐이더를 선언할 것입니다.

<div class="content-ad"></div>

```csharp
// 카메라
private FreeCamera _freeCamera;
private readonly Vector3 _cameraInitialPosition = new(0f, 50f, 300f);

// 물
private QuadPrimitive _quad;
private const float QuadHeight = 0f;
private const float WaveSpeed = 0.05f;
private Matrix _quadWorld;
private Effect _waterShader;
```

아울 Initialize 메소드는 다음과 같이 보여야 합니다:

```csharp
protected override void Initialize()
{
  _graphicsDeviceManager.PreferredBackBufferWidth = GraphicsAdapter.DefaultAdapter.CurrentDisplayMode.Width - 100;
  _graphicsDeviceManager.PreferredBackBufferHeight = GraphicsAdapter.DefaultAdapter.CurrentDisplayMode.Height - 100;
  _graphicsDeviceManager.ApplyChanges();

  // 카메라
  _freeCamera = new FreeCamera(GraphicsDevice.Viewport.AspectRatio, _cameraInitialPosition);

  // 물
  _quad = new QuadPrimitive(GraphicsDevice);
  var quadPosition = new Vector3(0f, QuadHeight, 0f);
  _quadWorld = Matrix.CreateScale(3000f, 0f, 3000f) * Matrix.CreateTranslation(quadPosition);

  base.Initialize();
}
```

# 평면 반사


<div class="content-ad"></div>

코드를 진행하기 전에 우리는 평면 반사가 어떻게 작동하는지 이해해야 합니다. 평면 반사의 예시를 살펴보겠습니다:

![Planar Reflection](/assets/img/2024-05-20-HowtocreateWatershaderinMonogameXNA_0.png)

이 예시에서 우리는 반사면, 카메라, 그리고 어떤 객체가 주어졌을 때 반사가 이렇게 보일 것이라는 것을 볼 수 있습니다. 이것을 코드로 나중에 변환하기 위해, 반사 카메라의 시각에서 새로운 뷰 매트릭스를 계산해야 합니다.

## 반사 카메라 위치

<div class="content-ad"></div>

먼저, 반사 카메라 위치를 계산하겠습니다. 이미 카메라 위치, 평면 위치 및 평면 법선을 알고 있으므로 카메라 위치에서 평면 위치를 빼면 뷰 방향 벡터를 얻을 수 있습니다:

뷰 방향 = 카메라 위치 - 평면 위치

![image](/assets/img/2024-05-20-HowtocreateWatershaderinMonogameXNA_1.png)

또한 투영 벡터를 추가했는데 이것은 다음과 같습니다. 그런 다음 해당 투영의 길이를 사용하여 평면상의 해당 지점까지의 거리를 찾을 수 있습니다.
이제 우리는 직각 삼각형을 가지고 있습니다. 투영 길이는 다음과 같아야 합니다:

<div class="content-ad"></div>

투영 길이 = 시야 방향 * cos(각도)

각도는 평면 법선과 시야 방향 사이의 각도를 의미합니다.

간단하게 표현하면 다음과 같이 다시 쓸 수 있습니다:

투영 길이 = 내적(평면 법선, 시야 방향)

<div class="content-ad"></div>

좋아요, 이제 우리에게는 프로젝션 길이가 있습니다. 이것을 사용하여 반사 카메라 위치를 얻을 수 있습니다:

반사 카메라 위치 = 카메라 위치 — 2 * 평면 법선 * 프로젝션 길이

## 반사 카메라 뷰 매트릭스

반사 카메라 뷰 매트릭스를 얻으려면 반사 카메라 포워드를 계산해야 합니다. 함수 Reflect(vector, normal)를 사용하여 특정 법선을 갖는 표면에서 벡터의 반사를 얻을 수 있습니다.

<div class="content-ad"></div>

카메라 반사 벡터를 계산한 후, Reflection Camera Up을 계산하는 다음 단계입니다. 카메라의 위쪽 벡터를 반사면의 법선을 기준으로 반사하는 Vector3.Reflect 함수를 다시 사용할 것입니다.

Reflection Camera Up = Vector3.Reflect(Camera Up, Plane Normal)

Reflection Camera Position, Reflection Camera Forward 및 Reflection Camera Up 벡터가 모두 계산되었으므로 이제 Reflection Camera View Matrix를 구성하는 데 필요한 모든 구성 요소를 갖추게 되었습니다.

<div class="content-ad"></div>

리플렉션 카메라 뷰 = Matrix.CreateLookAt(리플렉션 카메라 위치, 리플렉션 카메라 위치 + 리플렉션 카메라 전방, 리플렉션 카메라 위치)

# 리플렉션 그리기

이제 리플렉션 카메라 뷰 행렬의 필요 구성 요소를 계산했으므로, 일반적으로 RenderTarget2D와 같은 텍스처에 리플렉션을 그릴 수 있습니다. 이를 위해 코드에서 이전에 계산한 값을 렌더링 프로세스에 통합할 수 있습니다. 다음 코드 조각은 이를 어떻게 달성할 수 있는지 보여줍니다:

```js
private void DrawReflection(Matrix world, Matrix view, Matrix projection, GameTime gameTime)
{
    // 렌더 타겟을 리플렉션 텍스처로 설정
    GraphicsDevice.SetRenderTarget(_reflectionRenderTarget);
    GraphicsDevice.Clear(ClearOptions.Target | 
    ClearOptions.DepthBuffer, Color.CornflowerBlue, 1f, 0);
            
    var quadNormal = Vector3.Up;         
    var viewDirection = _freeCamera.Position - _quadWorld.Translation;       
    var projLength = Vector3.Dot(quadNormal, viewDirection);
    var reflectionCamPos = _freeCamera.Position - 2 * quadNormal * projLength;
    var reflectionCamForward = Vector3.Reflect(_freeCamera.FrontDirection, 
    quadNormal);
    var reflectionCamUp = Vector3.Reflect(_freeCamera.UpDirection, quadNormal);  
    var reflectionCamView = Matrix.CreateLookAt(reflectionCamPos, 
                reflectionCamPos + reflectionCamForward, reflectionCamUp);
    
    // 리플렉션 카메라 시점에서 씬 그리기
    DrawScene(reflectionCamView, projection, reflectionCamPos, 
    _reflectionClippingPlane);
    
    // 렌더 타겟을 기본(화면)으로 재설정
    GraphicsDevice.SetRenderTarget(null);
         
    // 물 그리기
    DrawWater(world, view, projection, reflectionCamView, gameTime);
}
```

<div class="content-ad"></div>

DrawWater 메서드에서는 필요한 매개변수를 전달할 것이며, 이에 대해 나중에 자세히 다룰 것입니다.

## 클리핑 평면

클리핑 평면 _reflectionClippingPlane이 아직 설명되지 않았다는 것을 눈치채셨나요? 그 목적에 대해 자세히 살펴보겠습니다. 이 클리핑 평면은 수면 위에 있는 객체만 그릴 때 사용됩니다. 이를 통해 객체의 상단 반만이 물에 반사되어 실제 반사에서 관찰되는 것과 동일하게 됩니다. 다음과 같이 선언할 수 있습니다:

```js
private readonly Vector4 _reflectionClippingPlane = new Vector4(0f, 1f, 0f, -QuadHeight);
```

<div class="content-ad"></div>

이 평면은 수학적 방정식 (Ax + By + Cz + D = 0)으로 정의됩니다. 여기서 (A, B, C)는 평면에 수직인 법선 벡터를 나타내고, D는 해당 법선 벡터를 따라 원점으로부터의 거리를 나타냅니다.

우리의 경우, 물 표면은 XZ 평면과 평행한 평면으로 작용하며, 그 법선 벡터는 직접 위쪽을 가리킵니다 (0, 1, 0).

우리는 물 속 객체를 다루는 셰이더를 위해 반사 클리핑 평면 방정식에 -QuadHeight 값을 활용합니다. 이 값은 나중에 픽셀 셰이더에서 clip 함수를 사용하여 Reflection Plane 아래의 픽셀을 클리핑할 때 사용됩니다. 다음은 예시입니다:

*Uniforms*
```js
float4 ClippingPlane;

struct VertexShaderInput
{
    float4 Position : POSITION0;
    float4 Normal : NORMAL;
};
 
struct VertexShaderOutput
{
    float4 Position : POSITION0;
    float3 WorldPosition : TEXCOORD0;
    float3 Normal : TEXCOORD1;
    float4 Clipping : TEXCOORD2;
};

VertexShaderOutput MainVS(VertexShaderInput input)
{
    VertexShaderOutput output = (VertexShaderOutput) 0;
 
    float4 worldPosition = mul(input.Position, World);
    float4 viewPosition = mul(worldPosition, View);
    output.Position = mul(viewPosition, Projection);
    output.WorldPosition = worldPosition;
    output.Normal = mul(input.Normal, InverseTransposeWorld);
    /*
    dot product를 사용하여 정점에서 클리핑 평면까지의 거리 계산
    이 결과 거리는 해당 픽셀을 픽셀 셰이더에서 버릴지 여부를 결정하는 데 사용됩니다
    */
    output.Clipping = dot(worldPosition, ClippingPlane);
 
    return output;
}

float4 MainPS(VertexShaderOutput input) : COLOR0
{
    clip(input.Clipping);
    
    // 픽셀 셰이더의 나머지 부분
}
```

<div class="content-ad"></div>

클립 함수는 지정된 조건에 따라 픽셀을 삭제하는 기능을 담당합니다. 셰이더에서 -QuadHeight를 활용하여 물 표면 아래에 있는 물체를 나타내는 픽셀들이 렌더링 중에 삭제되어 최종 반사 이미지에서 클리핑되는 효과가 있습니다.

또한, 위쪽의 물 위에 있는 픽셀을 삭제하려는 경우에도 이 방법이 작동합니다. 이 경우 굴절을 위해 다음과 같이 설정할 수 있습니다:

```js
private readonly Vector4 _refractionClippingPlane = new(0f, -1f, 0f, QuadHeight);
```

# 굴절

<div class="content-ad"></div>

리프렉션을 렌더 대상에 그리는 것은 반사를 렌더링하는 것과 비교할 때 간단한 프로세스입니다. 이 작업은 카메라의 관점에서 장면을 렌더링하면서 _refractionClippingPlane을 사용하여 수면 위의 픽셀을 버립니다.

```js
private void DrawRefraction()
{
    // Refraction Texture을 렌더 타겟으로 설정
    GraphicsDevice.SetRenderTarget(_refractionRenderTarget);
    GraphicsDevice.Clear(ClearOptions.Target | ClearOptions.DepthBuffer, Color.CornflowerBlue, 1f, 0);
            
    // 수면 위에 있는 객체를 자르면서 장면을 일반적으로 그립니다
    DrawScene(_freeCamera.View, _freeCamera.Projection, _freeCamera.Position, _refractionClippingPlane);
            
    // 렌더 타겟을 기본값(화면)으로 재설정
    GraphicsDevice.SetRenderTarget(null);
}
```

좋아요, 이제 실제 수 쉐이더로 진행하여 이 모든 내용이 어떻게 결합되는지 살펴봅시다.

# 수 쉐이더

<div class="content-ad"></div>

먼저, 우리의 물 쉐이더에 필요한 균일 및 텍스처를 선언할 것입니다. 이에는 조명, 카메라 위치, 물의 움직임 및 사용할 다양한 맵에 대한 텍스처 샘플러를 위한 매개변수가 포함됩니다:

```js
float KSpecular;
float Shininess;
float3 LightPosition;
float3 LightColor;
float3 CameraPosition;

float MoveFactor;
float2 Tiling;
float WaveStrength;

float4x4 ReflectionView;
float4x4 Projection;
float4x4 WorldViewProjection;
float4x4 World;

texture RefractionTexture;
sampler2D refractionSampler = sampler_state
{
    Texture = (RefractionTexture);
    ADDRESSU = Clamp;
    ADDRESSV = Clamp;
    MINFILTER = Linear;
    MAGFILTER = Linear;
    MIPFILTER = Linear;
};

texture ReflectionTexture;
sampler2D reflectionSampler = sampler_state
{
    Texture = (ReflectionTexture);
    ADDRESSU = Clamp;
    ADDRESSV = Clamp;
    MINFILTER = Linear;
    MAGFILTER = Linear;
    MIPFILTER = Linear;
};

texture DistortionMap;
sampler2D distortionSampler = sampler_state
{
    Texture = (DistortionMap);
    ADDRESSU = WRAP;
    ADDRESSV = WRAP;
    MINFILTER = Linear;
    MAGFILTER = Linear;
    MIPFILTER = Linear;
};

texture NormalMap;
sampler2D normalMapSampler = sampler_state
{
    Texture = (NormalMap);
    ADDRESSU = WRAP;
    ADDRESSV = WRAP;
    MINFILTER = Linear;
    MAGFILTER = Linear;
    MIPFILTER = Linear;
};
```

이 쉐이더에서 KSpecular, Shininess, LightPosition, LightColor, CameraPosition과 같은 균일은 조명 및 시각화 매개변수를 정의합니다. MoveFactor, Tiling, WaveStrength는 물의 움직임과 외관을 제어합니다. ReflectionView, Projection, WorldViewProjection, World와 같은 행렬은 좌표를 변환하는 데 사용됩니다. refractionSampler, reflectionSampler, distortionSampler, normalMapSampler와 같은 텍스처 샘플러는 물 표면을 렌더링하는 데 필요한 해당 텍스처를 샘플링하는 데 사용됩니다.

왜곡 맵에는 다음과 같은 모양의 텍스처를 사용할 것입니다:

<div class="content-ad"></div>

![이미지](/assets/img/2024-05-20-HowtocreateWatershaderinMonogameXNA_2.png)

이 텍스처를 사용하여 텍스처 좌표를 왜곡시켜 물 표면에 작은 파도 효과를 만들 수 있습니다. 이 왜곡은 물의 동적이고 현실적인 외관을 모방하는 데 도움이 됩니다.

Normal Map으로는 다른 텍스처를 사용할 것입니다. 여기 예시가 있습니다:

![이미지](/assets/img/2024-05-20-HowtocreateWatershaderinMonogameXNA_3.png)

<div class="content-ad"></div>

이 지도에는 물과 빛이 상호 작용하는 방식을 시뮬레이션하기 위해 필수적인 표면 법선에 대한 정보가 포함되어 있습니다. 왜곡 맵이 텍스처 좌표를 조작하는 데 사용되는 반면, 법선 맵은 빛 관련 계산에 사용됩니다.

## 버텍스 셰이더

우리의 버텍스 셰이더에서는 버텍스 데이터를 픽셀 셰이더에 대비하여 준비하기 위해 변환합니다. 이 과정을 자세히 살펴보겠습니다:

```js
struct VertexShaderInput
{
    float4 Position : POSITION0;
    float4 Normal : NORMAL;
    float2 TextureCoordinates : TEXCOORD0;
};

struct VertexShaderOutput
{
    float4 Position : SV_POSITION;
    float2 TextureCoordinates : TEXCOORD0;
    float4 WorldPosition : TEXCOORD1;
    float4 Normal : TEXCOORD2;
    float4 ReflectionPosition : TEXCOORD3;
    float4 RefractionPosition : TEXCOORD4;
};

VertexShaderOutput MainVS(in VertexShaderInput input)
{
    VertexShaderOutput output = (VertexShaderOutput) 0;

    // 버텍스 위치를 클립 공간에 변환합니다
    output.Position = mul(input.Position, WorldViewProjection);
    
    // 버텍스 위치를 월드 공간으로 변환합니다
    output.WorldPosition = mul(input.Position, World);
    
    // 어떤 변경도 없이 버텍스 법선을 전달합니다
    output.Normal = input.Normal;
    
    // 타일링 요소로 텍스처 좌표를 조정합니다
    output.TextureCoordinates = input.TextureCoordinates * Tiling;
    
    // 반사 위치와 월드 매트릭스를 계산합니다
    float4x4 reflectProjectWorld = mul(ReflectionView, Projection);
    reflectProjectWorld = mul(World, reflectProjectWorld);
    output.ReflectionPosition = mul(input.Position, reflectProjectWorld);
    
    // 굴절 위치는 클립 공간 위치와 동일합니다
    output.RefractionPosition = output.Position;
    
    return output;
}
```

<div class="content-ad"></div>

좋아요, 이제 정점 셰이더 부분을 완료했으니 픽셀 셰이더로 넘어갈 수 있겠네요.

# 픽셀 셰이더

## 프로젝티브 텍스처 매핑

먼저, 프로젝티브 굴절 텍스처 좌표를 정규화 장치 좌표 (NDC) 공간으로 변환합니다. 그런 다음, DirectX (DX) 텍스처를 정확히 샘플링할 수 있도록 xy 좌표를 적절하게 조정하여 크기를 조절하고 오프셋을 적용합니다.

<div class="content-ad"></div>

```csharp
float4 MainPS(VertexShaderOutput input) : COLOR
{
    // 굴절

    float4 refractionTexCoord;
    refractionTexCoord = input.RefractionPosition;
    
    // 화면 위치 좌표
    refractionTexCoord.xyz /= refractionTexCoord.w;
    
    // 오프셋 조정
    refractionTexCoord.x = 0.5f * refractionTexCoord.x + 0.5f;
    refractionTexCoord.y = -0.5f * refractionTexCoord.y + 0.5f;
    
    // 카메라로부터의 거리에 따라 굴절 더하기
    refractionTexCoord.z = 0.001f / refractionTexCoord.z;
    float2 refractionTex = refractionTexCoord.xy - refractionTexCoord.z;
    
    // 반사

    float4 reflectionTexCoord;
    reflectionTexCoord = input.ReflectionPosition;
    
    // 화면 위치 좌표
    reflectionTexCoord.xyz /= reflectionTexCoord.w;
    
    // 오프셋 조정
    reflectionTexCoord.x = 0.5f * reflectionTexCoord.x + 0.5f;
    reflectionTexCoord.y = -0.5f * reflectionTexCoord.y + 0.5f;
    
    // 카메라로부터의 거리에 따라 반사 더하기
    reflectionTexCoord.z = 0.001f / reflectionTexCoord.z;
    float2 reflectionTex = reflectionTexCoord.xy + reflectionTexCoord.z;

    // 셰이더의 나머지 부분
}
```

굴절에 대해서는 카메라로부터의 거리에 따라 텍스처 좌표를 조정하여 굴절 효과를 향상시킵니다. 이 조정을 통해 카메라로부터 더 멀리 있는 객체가 더 많이 굴절되어 물 속 장면을 더 현실적으로 렌더링할 수 있습니다.

반사에 대해서도 반사 텍스처 좌표를 NDC 공간으로 변환하고 카메라로부터의 거리에 따라 조정합니다. 이것은 더 뚜렷한 반사 효과를 만들어냄과 동시에, 카메라로부터 더 멀리 있는 객체가 더 강렬하게 반사되도록 도와줍니다.

## 왜곡 맵 / DuDv 맵

<div class="content-ad"></div>

이제 위에 표시된 왜곡 맵을 사용하여 반사 및 굴절 텍스처를 샘플링할 예정입니다.

이 맵은 빨간색과 녹색으로 이뤄진, 기본적으로 float2로 이루어져 있습니다. 그러나 이 값들은 항상 이 왜곡 맵에서 양수일 것이므로, 왜곡은 항상 양수일 것이고, 값은 (0.0, 0.0)에서 (1.0, 1.0) 사이입니다. 이 전체적인 리얼리티를 위해 양수와 음수 왜곡을 가질 수 있으면 좋을 것 같습니다.

이 값을 (-1.0, -1.0)에서 (1.0, 1.0) 사이로 변환하기 위해 단순히 2를 곱하고 1을 뺍니다. 그런 다음 이 값을 사용하여 간단히 반사 및 굴절 텍스처 좌표를 왜곡시킵니다.

```js
float2 distortion = (tex2D(distortionSampler, Input.TextureCoordinates)).rg 
                    * 2.0 - 1.0) * WaveStrength;

reflectionTex += distortion;
refractionTex += distortion;
```

<div class="content-ad"></div>

이 왜곡은 기본 설정에서 조금 강할 수 있습니다. 그래서 우리는 이 왜곡을 약간 낮추기 위해 이전에 선언한 WaveStrength로 사용할 것입니다.

## 파도 이동하기

물이 움직이는 것처럼 보이게 하는 방법은 Distortion Map을 샘플링하는 위치에 오프셋을 사용하는 것입니다. 이미 이를 선언했는데, 그것이 MoveFactor이며 이 오프셋을 시간이 지남에 따라 변경할 것입니다.

이 효과를 얻기 위해 MoveFactor를 텍스처 좌표의 X 구성 요소에 추가하고 왜곡 맵을 샘플링합니다. 그 후 Y 구성 요소를 위해 두 번째 샘플링을 수행하여 다른 방향으로 추가 왜곡을 적용합니다. 이 프로세스는 왜곡 효과의 현실성을 강화하여 보다 동적인 외관을 만듭니다.

<div class="content-ad"></div>


```js
float2 distortedTexCoords = tex2D(distortionSampler, float2(input.TextureCoordinates.x + MoveFactor, input.TextureCoordinates.y)) * 0.01;
distortedTexCoords = input.TextureCoordinates + float2(distortedTexCoords.x, distortedTexCoords.y + MoveFactor);
float2 totalDistortion = (tex2D(distortionSampler, distortedTexCoords).rg * 2.0 - 1.0) * WaveStrength;

reflectionTex += totalDistortion;
refractionTex += totalDistortion;

// Sample both texture using the distorted texture coordinates
float4 reflectionColor = tex2D(reflectionSampler, reflectionTex);
float4 refractionColor = tex2D(refractionSampler, refractionTex);
```

첫 번째 줄에서 0.01을 곱하여 초기 샘플에 적용된 왜곡의 강도를 약간 조정했습니다.

## 프레넬

프레넬 효과가 무엇인지 모르겠다면, Dorian의 아주 좋은 설명을 보여드리겠습니다.

<div class="content-ad"></div>

이 효과를 구현하려면 다음과 같이 물에서부터의 뷰 방향과 법선을 사용해야 합니다:

```js
float3 viewDirection = normalize(CameraPosition - input.WorldPosition.xyz);
float refractiveFactor = dot(viewDirection, normalize(input.Normal.xyz));
```

그리고 결과물은 이전에 계산한 refractiveFactor 값을 사용하여 reflectionColor와 refractionColor 사이의 선형 보간값이 될 것입니다.

```js
float4 finalColor = lerp(reflectionColor, refractionColor, refractiveFactor);
```

<div class="content-ad"></div>

하지만 아직 끝나지 않았어요! 마지막으로 추가해야 할 것은 노멀 맵과 조명입니다.

## 노멀 맵

노멀 맵을 사용하여 물 표면의 다른 지점에서의 노멀을 나타낼 수 있습니다. 노멀 맵의 어떤 지점의 픽셀 색상은 그 지점에서의 물의 3D 노멀 벡터를 나타낼 수 있습니다. (R, G, B) -` (X, Y, Z). 저는 위에 보여드린 노멀 맵이 대부분 파란색인 이유는 파란 값이 위쪽 축을 나타내며, 우리의 경우에는 Y 축이기 때문입니다.

따라서, 우리는 노멀 맵 색의 파란 구성 요소를 노멀 벡터의 Y 구성 요소로 사용할 것이고, 노멀 맵의 빨간색과 초록색 구성 요소를 노멀 벡터의 X 및 Z 구성 요소로 사용할 수 있습니다. (R, G, B) -` (R, B, G).

<div class="content-ad"></div>

여기서 유일한 문제는 절대 음수 색상을 가질 수 없기 때문에 법선 벡터의 구성 요소도 양수여야한다는 것입니다. Y 구성 요소에 대해서는 문제가 되지 않습니다. 왜냐하면 표면 법선이 어느 정도로든 위로 향하도록 항상 원하기 때문입니다. 그러나 법선이 항상 양의 X 및 Z 방향을 가리키도록 하길 바라는 것은 아닙니다. 법선은 음의 X 및 Z 방향을 가리키도록도 할 수 있어야 합니다.

이를 위해 왜곡 맵에 사용한 것과 동일한 변환을 사용할 것입니다. 2를 곱하고 1을 빼는 것입니다. 그리고 이런 식으로 법선 맵에서 법선 벡터를 추출할 수 있습니다:

```js
float4 normalMapColor = tex2D(normalMapSampler, distortedTexCoords);
float3 normal = float3(normalMapColor.r * 2.0 - 1.0, normalMapColor.b, normalMapColor.g * 2.0 - 1.0);
normal = normalize(normal);
```

이 추출된 법선 벡터를 활용하여 조명 효과를 계산할 것입니다.

<div class="content-ad"></div>

## 조명

우리가 사용할 조명 기법은 기본적으로 Blinn Phong 모델입니다. 이에 대해 더 알고 싶다면 여기를 클릭해주세요. 여기서 사용되는 유니폼은 KSpecular, Shininess, LightPosition 및 LightColor 입니다.

먼저, 빛의 방향과 하프 벡터를 구하기 위해 빛 위치와 빛 및 보기 방향의 합을 나타내는 벡터를 정규화합니다.

```js
float3 lightDirection = normalize(LightPosition - input.WorldPosition.xyz);
float3 halfVector = normalize(lightDirection + viewDirection);
```

<div class="content-ad"></div>

이 벡터들을 사용하여, 우리는 NdotL과 NdotH라는 닷 프로덕트를 계산합니다. 이는 각각 노멀 벡터와 라이트 방향, 그리고 노멀 벡터와 하프 벡터 사이의 각도를 나타냅니다.

```js
float NdotL = saturate(dot(normal, lightDirection));
float NdotH = saturate(dot(normal, halfVector));
```

이 닷 프로덕트를 이용하여, 우리는 Phong 반사 모델 방정식을 적용하여 스펙큘러 광원 기여도를 계산합니다.

```js
float3 specularLight = sign(NdotL) * KSpecular * LightColor * pow(NdotH, Shininess);
```

<div class="content-ad"></div>

마지막으로 반사광을 미리 계산된 반사 및 굴절 색과 결합하여 최종 색상 출력을 결정합니다.

```js
// 최종 계산
float4 finalColor = lerp(reflectionColor, refractionColor, refractiveFactor) + float4(specularLight, 0.0);
```

그게 다에요! 우리는 물 셰이더를 완전히 성공적으로 만들었습니다! 원하는 대로 매개변수를 조절할 수 있어요. 저는 이렇게 했어요:

```js
private void DrawWater(Matrix world, Matrix view, Matrix projection, Matrix reflectionView, GameTime gameTime)
{
    _waterShader.CurrentTechnique = _waterShader.Techniques["Water"];

    _waterShader.Parameters["World"].SetValue(world);
    _waterShader.Parameters["WorldViewProjection"].SetValue(world * view * projection);
    _waterShader.Parameters["ReflectionView"].SetValue(reflectionView);
    _waterShader.Parameters["Projection"].SetValue(projection);
            
    _waterShader.Parameters["ReflectionTexture"].SetValue(_reflectionRenderTarget);
    _waterShader.Parameters["RefractionTexture"].SetValue(_refractionRenderTarget);
    _waterShader.Parameters["DistortionMap"].SetValue(_distortionMap);
    _waterShader.Parameters["NormalMap"].SetValue(_normalMap);
    _waterShader.Parameters["Tiling"].SetValue(Vector2.One * 20f);
            
    _waterShader.Parameters["MoveFactor"].SetValue(WaveSpeed * (float)gameTime.TotalGameTime.TotalSeconds);
    _waterShader.Parameters["WaveStrength"].SetValue(0.01f);
            
    _waterShader.Parameters["CameraPosition"].SetValue(_freeCamera.Position);
    _waterShader.Parameters["LightPosition"].SetValue(_lightPosition);
    _waterShader.Parameters["LightColor"].SetValue(Color.White.ToVector3());
    _waterShader.Parameters["Shininess"].SetValue(25f);
    _waterShader.Parameters["KSpecular"].SetValue(0.3f);
            
    _quad.Draw(_waterShader);
}
```

<div class="content-ad"></div>

그리고 제 경우에는 다음과 같이 보입니다:

![image](/assets/img/2024-05-20-HowtocreateWatershaderinMonogameXNA_4.png)

이 글이 모노게임 그래픽 애플리케이션에서 이 효과를 이해하고 구현하는 데 도움이 되었으면 좋겠어요.