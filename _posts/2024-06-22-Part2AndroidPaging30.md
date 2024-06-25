---
title: "2024 최신 Android Paging 30 Part 2 적용 방법"
description: ""
coverImage: "/assets/img/2024-06-22-Part2AndroidPaging30_0.png"
date: 2024-06-22 22:46
ogImage:
  url: /assets/img/2024-06-22-Part2AndroidPaging30_0.png
tag: Tech
originalTitle: "Part — 2 Android Paging 3.0"
link: "https://medium.com/@sandeepkella23/part-2-android-paging-3-0-27b0d05994ba"
---

![그림](/assets/img/2024-06-22-Part2AndroidPaging30_0.png)

안드로이드에서의 Paging 3.0은 이전 버전보다 여러 가지 개선 사항과 변화를 가져와서 더 강력하고 사용하기 쉽게 만들어졌어요. 아래에는 Paging 3.0의 각 구성 요소를 구현하는 방법에 대한 자세한 설명이 있습니다.

# Paging 3.0의 주요 구성 요소

- PagingSource: DataSource를 대체하며 서로 다른 소스로부터 데이터를 페이징하는 데 사용되는 주요 API 역할을 합니다.
- Pager: 페이징된 데이터의 Flow 또는 LiveData를 생성하는 데 사용됩니다.
- PagingData: 페이징된 데이터의 스트림을 나타냅니다.
- PagingDataAdapter: PagedListAdapter를 대체하며 RecyclerView에서 페이징된 데이터를 표시하는 데 사용됩니다.

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

# 구현 단계

- PagingSource 정의:

- PagingSource는 데이터를 데이터 소스에서 로드하는 역할을 담당합니다.
- 다양한 페이지에 대해 데이터를 로드하는 방법을 정의함으로써 PagingSource를 구현합니다.

```kotlin
class MyPagingSource(private val apiService: ApiService) : PagingSource<Int, MyItem>() {

    override suspend fun load(params: LoadParams<Int>): LoadResult<Int, MyItem> {
        return try {
            // 데이터 소스에서 데이터 로드
            val nextPageNumber = params.key ?: 1
            val response = apiService.getItems(nextPageNumber, params.loadSize)
            LoadResult.Page(
                data = response.items,
                prevKey = if (nextPageNumber == 1) null else nextPageNumber - 1,
                nextKey = if (response.items.isEmpty()) null else nextPageNumber + 1
            )
        } catch (e: Exception) {
            LoadResult.Error(e)
        }
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

2. 페이저 생성하기:

- 페이저 객체는 PagingData의 Flow 또는 LiveData를 생성하는 데 사용됩니다.
- 페이저를 PagingConfig와 PagingSource로 구성합니다.

```js
class MyRepository(private val apiService: ApiService) {

    fun getPagingData(): Flow<PagingData<MyItem>> {
        return Pager(
            config = PagingConfig(
                pageSize = 20,
                enablePlaceholders = false
            ),
            pagingSourceFactory = { MyPagingSource(apiService) }
        ).flow
    }
}
```

3. ViewModel에서 PagingData 관찰하기:

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

- 뷰모델에서 PagingData를 관찰하기 위해 Flow를 사용하세요.
- LiveData를 선호한다면 LiveData와 함께 작업하십시오.

```js
class MyRepository(private val apiService: ApiService) {

    fun getPagingData(): Flow<PagingData<MyItem>> {
        return Pager(
            config = PagingConfig(
                pageSize = 20,
                enablePlaceholders = false
            ),
            pagingSourceFactory = { MyPagingSource(apiService) }
        ).flow
    }
}
```

4. PagingDataAdapter로 RecyclerView 설정하기:

- PagingDataAdapter를 사용하여 데이터를 RecyclerView에 바인딩합니다.
- PagingData가 변경될 때 새 데이터를 어댑터에 제출하세요.

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
class MyViewModel(private val repository: MyRepository) : ViewModel() {

    val pagingDataFlow = repository.getPagingData().cachedIn(viewModelScope)
}
```

5. Activity/Fragment에서 모든 것을 연결하기:

- PagingData를 관찰하고 어댑터에 제출합니다.

```js
class MyPagingDataAdapter : PagingDataAdapter<MyItem, MyViewHolder>(MyDiffCallback()) {

    override fun onBindViewHolder(holder: MyViewHolder, position: Int) {
        val item = getItem(position)
        if (item != null) {
            holder.bind(item)
        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): MyViewHolder {
        val view = LayoutInflater.from(parent.context).inflate(R.layout.item_view, parent, false)
        return MyViewHolder(view)
    }

    class MyDiffCallback : DiffUtil.ItemCallback<MyItem>() {
        override fun areItemsTheSame(oldItem: MyItem, newItem: MyItem): Boolean {
            return oldItem.id == newItem.id
        }

        override fun areContentsTheSame(oldItem: MyItem, newItem: MyItem): Boolean {
            return oldItem == newItem
        }
    }
}

class MyViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
    fun bind(item: MyItem) {
        // 뷰에 데이터를 바인딩합니다
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

# 대용량 데이터 처리

Paging 3.0은 다음과 같은 방식으로 대용량 데이터 세트를 효과적으로 처리합니다:

- 점진적 로딩: 필요할 때 데이터를 청크 단위로 로드하여 메모리 사용량을 줄입니다.
- 백그라운드 로딩: 데이터 로딩을 백그라운드에서 처리하여 반응이 빠른 UI를 제공합니다.
- 오류 처리: 실패한 로드를 다시 시도할 수 있는 메커니즘을 제공합니다.
- 캐싱: 불필요한 네트워크 또는 데이터베이스 호출을 방지하기 위해 인메모리 캐싱을 지원합니다.
- 변환: PagingData에 직접 적용할 수 있는 데이터 변환(예: 맵, 필터)을 허용합니다.

Paging 3.0을 사용하여 대용량 데이터 세트를 원활하게 처리할 수 있는 효율적이고 반응이 뛰어난 애플리케이션을 만들 수 있습니다.
