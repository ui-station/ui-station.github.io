---
title: "iOS에서 이미지 자원을 효율적으로 관리하는 5가지 전략"
description: ""
coverImage: "/assets/img/2024-06-19-5StrategiesforManagingImageResourcesEffectivelyiniOS_0.png"
date: 2024-06-19 11:19
ogImage: 
  url: /assets/img/2024-06-19-5StrategiesforManagingImageResourcesEffectivelyiniOS_0.png
tag: Tech
originalTitle: "5 Strategies for Managing Image Resources Effectively in iOS"
link: "https://medium.com/gitconnected/5-effective-strategies-for-managing-image-resources-in-ios-45681f475461"
---



![Image](/assets/img/2024-06-19-5StrategiesforManagingImageResourcesEffectivelyiniOS_0.png)

이미지 리소스 관리는 엔지니어들이 종종 간과하는 측면입니다. 충분한 주의를 기울이지 않으면 앱의 성능 뿐만 아니라 생산성에도 영향을 미칠 수 있습니다.

이 기사에서는 이미지 리소스를 관리하는 데 우리 엔지니어링 팀이 사용하는 몇 가지 효과적인 전략을 소개하겠습니다. 이러한 전략을 프로젝트에 적용하면 앱 크기를 줄이고 메모리 사용량을 줄이며 응용 프로그램의 성능을 향상시킬 수 있습니다.

시작해 보겠습니다!


<div class="content-ad"></div>

# 목차

- PDF 또는 PNG?
- 자산 압축하기
- 이미지 다운샘플링
- 중복 자산 제거
- 사용되지 않는 자산 제거

# 1. PDF 또는 PNG?

이전 프로젝트에서 팀은 모든 이미지에 PDF 형식을 사용했습니다. 주된 이유는:

<div class="content-ad"></div>

- 유지 관리가 쉽습니다: 3 개의 PNG 이미지(1x, 2x, 3x) 대신 1 개의 PDF 이미지만 유지 관리하면 됩니다.
- Xcode는 PDF 형식에 대한 벡터 데이터 보존을 지원합니다.

하지만, PDF 이미지는 PNG 이미지보다 더 무겁습니다.

Xcode 15 이전에는 아카이브할 때 Xcode가 PDF 이미지를 3개의 PNG 이미지로 변환했습니다. Xcode 15부터는 PDF 이미지를 아카이브할 때 3개의 PDF 이미지로 변환하고, 불필요한 리소스를 제거하기 위해 App thinning을 사용합니다.

다음 이미지는 예제 프로젝트를 아카이브한 후의 "Assets.car" 파일입니다. 각 PDF 이미지가 3개의 PDF 이미지로 생성됨을 확인할 수 있습니다:

<div class="content-ad"></div>


![image1](/assets/img/2024-06-19-5StrategiesforManagingImageResourcesEffectivelyiniOS_1.png)

Now, let’s compare the “Asset.car” file size between using PNG images vs using PDF images:

![image2](/assets/img/2024-06-19-5StrategiesforManagingImageResourcesEffectivelyiniOS_2.png)

The “Assets.car” size in the project that uses PNG images is much smaller than using PDF images.


<div class="content-ad"></div>

## 권장 사항:

- 이미지의 애플리케이션 크기와 성능을 향상시키기 위해 PNG 형식을 사용해주세요.
- 다양한 뷰 프레임에서 확대/축소를 지원하기 위해 벡터 데이터를 보존해야 하는 이미지에는 PDF 형식을 사용해주세요.

# 2. 이미지 압축

앱 크기를 줄이는 두 번째 방법은 이미지를 프로젝트에 추가하기 전에 압축하는 것입니다.

<div class="content-ad"></div>

다음 이미지를 살펴봅시다. 이미지를 몇 번 압축한 후 이미지 크기가 83% 감소했지만 품질 차이를 거의 느끼지 못할 수 있어요(특히 iPhone 화면에서). 이미지가 사용되는 용도를 고려하면 앱에 최적의 압축 설정을 결정하는 데 도움이 될 거에요.

![Image](/assets/img/2024-06-19-5StrategiesforManagingImageResourcesEffectivelyiniOS_3.png)

## 제안:

- TinyImage Compressor를 사용하세요 — Figma 플러그인 도구로 Figma에서 직접 압축된 이미지를 내보내는 데 도움이 됩니다.
- 저희 대규모 엔지니어링 팀에서는 수백 개의 아이콘이 있어요. 크기를 관리하기 위해 사전 커밋 후크 워크플로에 스크립트를 추가합니다. 최근에 추가된 아이콘의 크기가 100 KB를 초과하는지 확인해요. 그렇다면, 엔지니어에게 압축할 것을 요청합니다.

<div class="content-ad"></div>

# 3. 이미지 다운샘플링

## 문제

이미지의 크기를 고려하지 않고 그냥 사용하지 마세요. WWDC 2018에서의 알림:

이미지를 표시하는 데 필요한 메모리 푸트프린트를 계산하는 공식은 다음과 같습니다:

<div class="content-ad"></div>

만약 5MB 크기의 이미지(6000x4000px)가 있다면, 해당 이미지를 디코딩하고 표시하는 데 필요한 메모리 공간은 96MB에 달할 거예요.

300x200 크기의 UIImageView에 6000x4000 px 이미지를 표시하는 것은 메모리를 엄청나게 낭비하는 일이에요. Apple은 WWDC 2018에서 Downsampling 기술을 소개하여 이미지를 화면에 표시하기 전에 크기를 조정하는 방법을 소개했는데, 이를 통해 메모리 소비를 크게 줄일 수 있어요.

이곳에는 swiftsenpai와 Kseniia Zozulia님이 Downsampling을 적용하는 방법에 대해 작성한 좋은 기사들이 있어요 👍

## 자동화하세요!

<div class="content-ad"></div>

대형 이미지를 표시함으로써 불필요한 메모리를 낭비하고 있는지 어떻게 알 수 있을까요? 수동 접근 방식은 이미지 크기와 이미지 뷰의 프레임을 비교해야 합니다.

네, 우리 모두는 수동으로 귀찮은 일을 하는 것을 싫어합니다 🥵

간단하게 자동화된 접근 방식은 UIImageView의 하위 클래스를 만드는 것입니다. UIImageView의 프레임보다 큰 이미지를 사용할 때마다, 이미지 뷰에 경고 이미지를 표시할 수 있습니다.

다음은 접근 방식의 예시 버전입니다:

<div class="content-ad"></div>

```swift
final class CustomImageView: UIImageView {

    var warning: UIImageView?
    var blurBackground: UIVisualEffectView?

    override var image: UIImage? {
        didSet {
            super.image = image

// Debug 모드에서 이미지가 너무 커서 이미지 뷰 위에 경고가 표시됩니다.
#if DEBUG
            if isImageTooLarge {
                if warning != nil { return }
                let blurBackground = UIVisualEffectView(effect: UIBlurEffect(style: .systemUltraThinMaterial))
                addSubview(blurBackground)
                blurBackground.frame = bounds
                blurBackground.alpha = 0.7
                self.blurBackground = blurBackground
                
                let warning = UIImageView(image: UIImage(systemName: "exclamationmark.triangle"))
                warning.tintColor = .red
                warning.translatesAutoresizingMaskIntoConstraints = false
                addSubview(warning)
                NSLayoutConstraint.activate([
                    warning.centerXAnchor.constraint(equalTo: centerXAnchor),
                    warning.centerYAnchor.constraint(equalTo: centerYAnchor)
                ])
                self.warning = warning
            } else {
                warning?.removeFromSuperview()
                warning = nil
            }
        }
#endif
    }
    
// 이미지가 이미지 뷰의 프레임보다 큰지 확인
    private var isImageTooLarge: Bool { 
        guard let image = image else { return false }
        return image.size.height * image.size.width > self.frame.height * self.frame.width
    }
}
```

아래는 적절하지 않은 이미지 크기를 사용할 때의 예시입니다. 엔지니어는 바로 알아차릴 수 있습니다. 그 후 이미지를 조절하기 위해 다운샘플링 기술을 적용할 수 있습니다. 👏

![이미지](/assets/img/2024-06-19-5StrategiesforManagingImageResourcesEffectivelyiniOS_4.png)

## 제안:

<div class="content-ad"></div>

- 특정 크기로 이미지를 가져오는 fetch 지원, 예: https://'PATH_TO_IMAGE'/'IMAGE_ID'/'size'.jpg
- 이미지 크기를 조정하기 위해 다운샘플링 기술 사용

# 4. 중복 이미지 제거

큰 프로젝트에서 여러 엔지니어들이 다양한 팀에서 작업하고 있습니다. 그들이 자주 마주치는 시나리오는 프로젝트 전반에 중복된 이미지가 있는 것입니다. 이는 앱 크기를 증가시키며, 이미지를 수동으로 유지하는 것은 지루하고 시간이 많이 소요되는 작업입니다.

우리 팀은 중복 이미지를 확인하는 프로세스를 자동화했습니다. 프로젝트의 모든 이미지를 스캔하고 중복을 비교하기 위한 Python 스크립트를 작성했습니다. 더 나아가, 이 스크립트를 우리의 CI 워크플로에 통합하여 때때로 확인하고, 그런 다음 팀에 보고서를 보내게 했습니다.

<div class="content-ad"></div>

제안:

GitHub에서는 중복 이미지를 감지하는 오픈 소스 Python 프로젝트가 있어요. 예를 들어 이 프로젝트가 있어요. 이를 다운로드하여 프로젝트에 심술 없이 통합할 수 있어요.

# 5. 사용되지 않는 이미지 제거

중복 이미지와 유사하게 사용되지 않는 이미지는 앱 크기를 불필요하게 증가시키기 때문에 제거해야 해요.
FengNiao는 사용되지 않는 이미지를 감지하고 제거하는 데 도움이 되는 오픈 소스 도구에요:

<div class="content-ad"></div>


![image](/assets/img/2024-06-19-5StrategiesforManagingImageResourcesEffectivelyiniOS_5.png)

## 제안:

- 이 도구를 CI 워크플로우에 통합하여 사용하지 않는 이미지가 있을 때 팀에 Slack 알림을 보낼 수 있습니다. 팀은 필요에 따라 이미지를 확인하고 수동으로 제거할 수 있습니다.

# 결론


<div class="content-ad"></div>

이미지 리소스를 관리하는 데 조금 더 노력하면 앱의 성능을 효과적으로 개선할 수 있어요.

자산을 관리하기 위한 다른 팁이 있다면 주저하지 마시고 댓글을 남겨주세요 :D

이 글이 도움이 되었다면 좋아요 버튼을 누르고 댓글을 남겨주세요. 그러면 Medium이 다른 사람들에게 이 글을 추천할 거에요.

읽어 주셔서 감사합니다.

<div class="content-ad"></div>


![image](https://miro.medium.com/v2/resize:fit:1200/0*CN-R8hHdtZlcJFoV.gif)
