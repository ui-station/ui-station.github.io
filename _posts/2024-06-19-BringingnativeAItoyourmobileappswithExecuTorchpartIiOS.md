---
title: "원활한 사용을 위해 ExecuTorch와 함께 모바일 앱에 원시 AI를 적용하기 - 파트 1 - iOS"
description: ""
coverImage: "/assets/img/2024-06-19-BringingnativeAItoyourmobileappswithExecuTorchpartIiOS_0.png"
date: 2024-06-19 14:08
ogImage:
  url: /assets/img/2024-06-19-BringingnativeAItoyourmobileappswithExecuTorchpartIiOS_0.png
tag: Tech
originalTitle: "Bringing native AI to your mobile apps with ExecuTorch— part I — iOS"
link: "https://medium.com/swmansion/bringing-native-ai-to-your-mobile-apps-with-executorch-part-i-ios-f1562a4556e8"
---

<img src="/assets/img/2024-06-19-BringingnativeAItoyourmobileappswithExecuTorchpartIiOS_0.png" />

ExecuTorch는 PyTorch를 기반으로 한 새로운 프레임워크로, PyTorch AI 모델을 로컬 배포에 적합한 형식으로 내보낼 수 있게 해줍니다. 이는 약간의 네이티브 코드와 함께 React Native 앱에 AI 기능을 쉽게 통합할 수 있음을 의미합니다. iOS의 경우, Apple의 Neural Engines 덕분에 고성능을 제공하는 CoreML 백엔드를 활용할 수 있습니다.

이 튜토리얼 시리즈에서는 API 호출이 필요 없이 자신이 선택한 AI 모델을 앱에서 직접 사용하는 방법을 안내해 드릴 것입니다.

## 1. 모델 내보내기

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

가장 먼저 해야 할 일은 PyTorch 모델을 .pte 파일로 내보내는 것인데, 이는 본질적으로 PyTorch 실행 가능 파일입니다. 이 부분은 이미 처리해 드렸지만, 만약 여러분의 모델을 사용하고 싶으시다면 ExecuTorch의 Python API를 사용하여 가능합니다. 만약 재현하고 싶다면, 저희 레포지토리에서 모델을 다운로드할 수 있습니다. 여기에서 내보낸 모델들은 PyTorch 예제 레포지토리에서 가져왔습니다. 이를 하기 전에, 환경 설정이 필수적입니다. 기본 내보내기 튜토리얼은 다음을 참조하세요:

전체 프로세스는 여러 단계로 이뤄져 있으며, 사용 사례에 따라 다를 수 있습니다. 모델은 먼저 그래프 표현으로 변환되며, 이후 여러분의 필요에 맞게 최적화될 수 있습니다. 이것은 컴파일, 옵셔널 양자화, 메모리 계획 등을 포함한 다단계 프로세스로, 최종적으로 실행 가능 파일로 내보냅니다. 실행 시, 특정 하드웨어 대상용으로 빌드된 ExecuTorch를 사용하여 장치에서 효율적으로 추론을 수행합니다.

![이미지](/assets/img/2024-06-19-BringingnativeAItoyourmobileappswithExecuTorchpartIiOS_1.png)

이 튜토리얼에서는 이미지를 멋진 아트스타일로 변환하는 스타일 전송 모델을 사용할 것입니다. 만약 웹 및 비디오에 대해 비슷한 일을 하는 방법에 궁금하다면 이전 기사를 확인해보세요. 원하는 모델을 사용하고 싶다면 아래 스크립트를 사용할 수 있습니다. 이를 통해 CoreML 백엔드에 적합한 .pte 파일이 생성될 것입니다.

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

## 2. 라이브러리 빌드 및 설정하기

실제 코딩 부분에 들어가기 전에, 라이브러리를 빌드하고 Xcode 프로젝트에 연결해야 합니다. 빌드 프로세스는 문서에서 자세히 설명되어 있으므로, 익숙해지는 것을 강력히 권장합니다. 그러나 우리는 이미 빌드했으며 편의를 위해 GitHub 저장소에서 빌드를 다운로드할 수 있습니다.

라이브러리를 다운로드한 후 (coreml_backend.xcframework와 executorch.xcframework), Xcode 프로젝트로 이동하여 -` 빌드 패스 -` 라이브러리와 함께 링크하기 -` +를 클릭하여 프로젝트에 추가해야 합니다. 또한 Xcode에서 기본 제공되는 CoreML.framework와 Accelerate.framework도 추가해야 합니다.

코딩에 들어가기 전에 빌드 설정 -` 다른 링커 플래그로 이동하여 -all_load 플래그를 추가해야 합니다. 이를 통해 라이브러리가 제대로 연결되도록 할 수 있습니다.

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

## 3. 코딩 파트 — Xcode

구현으로 넘어가면 이제 Objective-C++ 파일을 만들어야 합니다. 이를 StyleTransferModule.mm으로 부르겠습니다. 우리 코드에서는 React Native 앱에서 네이티브 모듈을 사용하여 이 메서드를 호출합니다. 그러나 이것은 선택 사항이며 사용 사례에 맞게 조정할 수 있습니다.

```js
// StyleTransferModule.mm

#import "StyleTransferModule.h"
#import "ImageProcessor.h"
#import <executorch/extension/module/module.h>

using namespace ::torch::executor;

const int32_t imageSize = 640;
const int32_t numChannels = 3;

@implementation StyleTransferModule {
  std::unique_ptr<Module> styleTransferModel;
}


RCT_EXPORT_METHOD(initModule:(NSString *)modelFileName resolver:(RCTPromiseResolveBlock)resolve rejecter:(RCTPromiseRejectBlock)reject) {
    NSString *styleTransferPath = [[NSBundle mainBundle] pathForResource:modelFileName ofType:@"pte"];
    styleTransferModel= std::make_unique<Module>(styleTransferPath.UTF8String);

    if (styleTransferModel) {
        resolve(@"Module created successfully");
    } else {
        NSError *error = [NSError errorWithDomain:@"com.example.module" code:1 userInfo:nil];
        reject(@"module_error", @"Failed to create module", error);
    }
}
```

먼저 Module 클래스의 인스턴스를 만들어야 합니다. 이는 ExecuTorch 프레임워크의 일부입니다. 이를 통해 모델의 forward()와 같은 내보낸 메서드를 호출할 수 있습니다. 클래스를 인스턴스화하려면 이전에 언급한 .pte 파일의 경로를 전달해야 합니다. 또한 RCT_EXPORT_METHOD 매크로를 사용하여 메서드를 JS로 내보냅니다.

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

모델을 실행하기 전에 다음 단계는 입력 텐서를 준비하는 것입니다. 머신 러닝 프레임워크와 작업을 해본 적이 있다면 텐서에 익숙할 것입니다. 고수준에서 말하면, 이것은 GPU에서 실행할 수 있는 배열입니다.

텐서를 만들려면 이미지의 원시 RGB 데이터를 가져와야 합니다. 저희 저장소에서는 여러 모델을 로드하기 때문에 코드가 약간 다릅니다. 하지만 간단하게 유지하기 위해 이 튜토리얼에서는 한 모델에 집중하겠습니다.

```js
// StyleTransferModule.mm
RCT_EXPORT_METHOD(applyStyleTransfer:(NSString *)imageUri resolver:(RCTPromiseResolveBlock)resolve rejecter:(RCTPromiseRejectBlock)reject) {
{
    if (!styleTransferModel) {
        reject(@"module_error", @"Module not initialized", [NSError errorWithDomain:@"com.example.module" code:1 userInfo:nil]);
        return;
    }

    NSURL *url = [NSURL URLWithString:imageUri];
    NSData *data = [NSData dataWithContentsOfURL:url];
    if (!data) {
      reject(@"img_loading_error", @"Unable to load image data", nil);
      return;
    }
    UIImage *inputImage = [UIImage imageWithData:data];
```

다시 한번 해당 메서드를 JS에 내보냅니다. applyStyleTransfer라고 부르며, NSString 포인터를 받도록 만들었습니다. 이는 표준 JS 문자열과 해당됩니다. 이 문자열은 우리 입력 이미지의 URI가 될 것입니다. 이제 모델이 예상하는 데이터 형식인 원시 RGB 데이터 배열을 만들어야 합니다.

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
// StyleTransferModule.mm
// ...

// StyleTransferModule.mm
// ...

  CGSize targetSize = CGSizeMake(imageSize, imageSize);
  UIImage *resizedImage = [ImageProcessor resizeImage:inputImage toSize:targetSize];

  // to float array - the input
  float *imageData = [ImageProcessor imageToFloatArray:resizedImage size:&targetSize];

  // make it a tensor
  int32_t sizes[] = {1, numChannels, imageSize, imageSize};
  TensorImpl inputTensorImpl(ScalarType::Float, std::size(sizes), sizes, imageData);
  Tensor inputTensor = Tensor(&inputTensorImpl);

이제 UIImage를 가져와서 사용자 정의 ImageProcessor에 전달합니다. 이것은 사용 사례와 모델에 따라 다양한 전처리 부분입니다. 여기서는 640x640 크기의 이미지 및 float 값 배열이 필요합니다. 크기 조정이 항상 필요한 것은 아니며 동적 입력 형태로 모델을 내보낼 수 있습니다. ImageProcessor가 정확히 무엇을 하는지 보려면 여기를 클릭하세요. 이후에는 해당 데이터로부터 텐서를 생성해야 하므로 데이터 및 텐서 크기를 TensorImpl 생성자에 전달해야 합니다. 마지막으로 TensorImpl을 Tensor 자체로 전달해야 합니다.

다음 단계는 텐서를 모델에 전달하는 것입니다. 이 부분은 매우 간단합니다. 이전에 생성된 Tensor를 EValue로 래핑하고 벡터에 넣은 다음 forward() 메서드(또는 내보낸 다른 메서드)를 실행하기만 하면 됩니다. 벡터에 넣는 이유는 여러 입력을 예상하는 모델이 있기 때문입니다.

// StyleTransferModule.mm
// ...

const auto result = styleTransferModel->forward({EValue(inputTensor)});
if (!result.ok()) {
NSError *error = [NSError
        errorWithDomain:@"ModelForwardFailure"
        code:NSInteger(result.error())
        userInfo:@{NSLocalizedDescriptionKey: [NSString stringWithFormat:@"Failed to run forward on the torch module, error code: %i", result.error()]}];
  reject(@"model_failure", error.localizedDescription, error);
}
const float *outputData = result->at(0).toTensor().const_data_ptr<float>();
free(imageData);

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

추론 중에 오류가 발생했는지 확인하려면 .ok() 메서드를 호출하면 됩니다. outputData 변수는 모델 호출 결과에 대한 포인터입니다. 이는 분류 작업의 확률부터 LLM 출력까지 어떤 것이든 될 수 있습니다. 마지막으로 후속 처리 단계를 수행하고 출력 이미지 URI를 JS 쪽에 반환해야 합니다.

// StyleTransferModule.mm
// ...

CGSize outputSize = CGSizeMake(imageSize, imageSize);
UIImage *outputImage = [ImageProcessor imageFromFloatArray:outputData size:outputSize];

// save img to tmp dir, return URI
NSString *outputPath = [NSTemporaryDirectory() stringByAppendingPathComponent:@"processed_image.png"];
if ([UIImagePNGRepresentation(outputImage) writeToFile:outputPath atomically:YES]) {
  NSURL *fileURL = [NSURL fileURLWithPath:outputPath];
  resolve([fileURL absoluteString]);
} else {
  reject(@"img_write_error", @"Failed to write processed image to file", nil);
}

그것이 거의 다입니다. 이 접근 방식을 React Native 앱에서 어떻게 사용할 수 있는지 보여주는 데모 앱을 준비했습니다. 왼쪽에 원본 이미지, 오른쪽에 모델 출력이 표시됩니다.

<img src="/assets/img/2024-06-19-BringingnativeAItoyourmobileappswithExecuTorchpartIiOS_2.png" />

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

## 마지막으로

축하해요! 모델을 성공적으로 실행했어요. ExecuTorch는 LLaMa만큼 큰 모델도 완전히 기기에서 실행할 수 있게 해 주는 거대한 프레임워크야.

이 시리즈의 다음 부분에서는 Android에서도 똑같이 할 수 있는 방법을 보여줄 거에요. 또한 곧 우리의 객체 제거 데모와 관련된 꽤 인상적인 기능을 구현하는 튜토리얼을 공개할 예정이에요 👀. 우리의 AI 및 멀티미디어 작업에 대해 계속해서 소식을 받고 싶다면 RTC.ON 소식지에 가입해주세요. 계속 연락을 유지해 주세요!

우리는 소프트웨어 마스터즈(Software Mansion)입니다: 소프트웨어 개발 컨설턴트, AI 탐험가, 멀티미디어 전문가, React Native 코어 기여자 및 커뮤니티 빌더들이에요. 우리를 고용하고 싶다면 projects@swmansion.com 으로 연락해 주세요.
```
