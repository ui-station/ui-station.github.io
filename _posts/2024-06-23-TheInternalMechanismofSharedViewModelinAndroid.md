---
title: "안드로이드에서 공유 ViewModel의 내부 메커니즘 이해하기"
description: ""
coverImage: "/assets/img/2024-06-23-TheInternalMechanismofSharedViewModelinAndroid_0.png"
date: 2024-06-23 01:24
ogImage: 
  url: /assets/img/2024-06-23-TheInternalMechanismofSharedViewModelinAndroid_0.png
tag: Tech
originalTitle: "The Internal Mechanism of Shared ViewModel in Android"
link: "https://medium.com/@sandeepkella23/the-internal-mechanism-of-shared-viewmodel-in-android-3a0ccda4e344"
---


![이미지](/assets/img/2024-06-23-TheInternalMechanismofSharedViewModelinAndroid_0.png)

# 안드로이드에서의 Shared ViewModel이란 무엇인가요?

안녕하세요! 여러분은 앱의 다양한 부분을 데이터를 계속 주고받는 모든 번거로움 없이 동기화하는 방법을 궁금해 하신 적이 있나요? 그려면 Shared ViewModel을 소개할게요. 이는 앱 구성 요소를 위한 그룹 채팅처럼 작동합니다. 개별 메시지를 주고받는 대신에 모두가 자동으로 함께 유지됩니다. 정말 편리하죠?

# Shared ViewModel은 어떻게 작동하나요?

<div class="content-ad"></div>

당신이 서프라이즈 파티를 준비하고 있다고 상상해보세요. 여러 명의 친구들(당신의 조각들)이 계획에 대한 변경 사항마다 각각에게 전화를 걸지 않고도 업데이트를 유지해야 합니다. 이 때 공유된 ViewModel이 등장합니다. 이는 모두가 동일한 페이지에 머무를 수 있도록하는 마스터 파티 플래너와 같은 역할을 합니다.

## 단계 1: 마스터 플래너(ViewModelProvider)

ViewModelProvider은 마스터 파티 플래너와 같습니다. 파티 세부 정보(ViewModel)를 요청하면 이미 계획이 있는지 확인합니다. 있으면 제공하고, 그렇지 않으면 새로운 계획을 작성합니다.

다음은 ViewModel을 가져오는 방법입니다:

<div class="content-ad"></div>

```js
val viewModel = ViewModelProvider(requireActivity()).get(SharedViewModel::class.java)
```

requireActivity()을 사용하면 ViewModel이 해당 activity의 모든 fragment들 사이에서 공유됩니다. 이는 파티 준비 위원회 전체를 위한 한 곳의 중앙 그룹 채팅을 가지는 것과 같습니다.

## 단계 2: 저장 공간 (ViewModelStore)

뒷면에는 모든 계획(ViewModels)이 보관되는 저장 공간(ViewModelStore)이 있습니다. 이 방은 활동의 수명주기에 묶여 있습니다. 따라서 활동이 살아있는 한 계획은 안전하게 보관됩니다.

<div class="content-ad"></div>

## 단계 3: 계획의 수호자(ViewModelStoreOwner)

ViewModelStoreOwner은 보관실의 열쇠 소유자와 같습니다. 활동(Activity) 및 프래그먼트(Fragment)는 이 인터페이스를 구현하며, 공유된 계획에 접근할 수 있도록 보장합니다.

다음은 활동에서의 사용 예시입니다:

```java
public class MainActivity extends AppCompatActivity implements ViewModelStoreOwner {
    private ViewModelStore mViewModelStore = new ViewModelStore();

    @Override
    public ViewModelStore getViewModelStore() {
        return mViewModelStore;
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mViewModelStore.clear(); // 활동이 종료될 때 ViewModelStore를 비움
    }
}
```

<div class="content-ad"></div>

## 단계 4: 모두가 최신 상태 유지하기 (데이터 공유)

모든 조각들이 동일한 그룹 채팅(ViewModel)의 일부이기 때문에 어떠한 변경 사항이 있더라도 자동으로 최신 상태를 유지합니다. 각 친구에게 개별적으로 전화할 필요가 없습니다.

실제 작동 방식은 다음과 같습니다:

```js
class SharedViewModel : ViewModel() {
    val data = MutableLiveData<String>()
}

class FirstFragment : Fragment() {
    private lateinit var viewModel: SharedViewModel

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        viewModel = ViewModelProvider(requireActivity()).get(SharedViewModel::class.java)
        viewModel.data.observe(viewLifecycleOwner, Observer { data ->
            // 새 데이터로 UI 업데이트
        })
        return inflater.inflate(R.layout.fragment_first, container, false)
    }
}

class SecondFragment : Fragment() {
    private lateinit var viewModel: SharedViewModel

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        viewModel = ViewModelProvider(requireActivity()).get(SharedViewModel ::class.java)
        viewModel.data.observe(viewLifecycleOwner, Observer { data ->
            // 새 데이터로 UI 업데이트
        })
        return inflater.inflate(R.layout.fragment_second, container, false)
    }
}
```

<div class="content-ad"></div>

FirstFragment이 viewModel.data를 업데이트하면, SecondFragment도 업데이트를 받습니다. 마치 한 친구가 그룹 채팅에 글을 올리면 모두가 즉시 메시지를 받는 것처럼 동작합니다.

# 요약

그래서 Shared ViewModel의 마법입니다! ViewModelProvider, ViewModelStore, ViewModelStoreOwner를 사용하여 추가 노력 없이 모든 fragment를 동기화 상태로 유지합니다. 항상 모두가 같은 페이지에 있는지 확인하는 수퍼 체계적인 파티 플래너가 있는 것처럼 동작합니다. 추가 질문이 있거나 더 깊이 파고들고 싶으시면 언제든지 알려주세요. 즐거운 코딩하세요! 😊