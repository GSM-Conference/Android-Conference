**Paging**
========
## **What is Paging?**
Paging이란 데이터를 가져올 때 한 번에 모든 데이터를 가져오는 것이 아니라 일정한 덩어리로 나눠서 가져오는 것을 뜻한다.   
예를 들어, 구글에서 어떤 키워드를 검색하게 되면 한 번에 모든 데이터를 가져오는 것이 아니라 10페이지씩 데이터를 가져오게 된다.   
페이징을 사용하면 성능, 메모리, 비용 측면에서 굉장히 효율적이다.


## **Jetpack Paging Library**
Android Jetpack에서는 페이징을 위한 Paging3 라이브러리를 제공한다. Paging3 라이브러리는 로컬 저장소나 네트워크를 통해 데이터를 나누어 효율적으로 로딩할 수 있게 도와준다. Paging3는 구글에서 권장하는 Android 앱 아키텍쳐에 맞게 설계되었으며, 다른 Jetpack컴포넌트와 잘 동작할 수 있도록 설계되었다.   

## **Paging3의 장점**
 - 페이징된 데이터의 메모리내 캐싱. 이렇게 하면 앱이 패이징 데이터로 작업하는 동안 시스템 리소스를 효율적으로 사용할 수 있다.
 - 요청 중복 제거 기능이 기본적으로 제공되어 앱에서 네트워크 대역폭과 시스템 리소스를 효율적으로 사용할 수 있다.
 - 사용자가 로드된 데이터의 끝까지 스크롤할 때 구성 가능한 RecyclerView 어댑터가 자동으로 데이터를 요청한다.
 - Kotlin 코루틴 및 Flow뿐만 아니라 LiveData 및 RxJava를 최고수준으로 지원한다.
 - 새로고침 및 재시도 기능을 포함하여 오류처리를 기본적으로 지원한다.

 ## **Paging3 아키텍쳐**
1. Repository Layer
2. ViewModel Layer
3. UI Layer
![pic](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FV8yuR%2Fbtq8c1FhYu5%2FQaKRQ6lbSFubJ5K2jryKvK%2Fimg.jpg)

### **Repository Layer**
Repository Layer의 페이징 라이브러리 구성요소는 PagingSource 이다. 각 PagingSource 객체는 데이터 소스와 이 소스에서 데이터를 검색하는 방법을 정의한다. PagingSource 객체는 네트워크 소스 및 로컬 데이터베이스를 포함한 단일 소스에서 데이터를 로드할 수 있다.   

사용할 수 있는 다른 페이징 라이브러리 구성요소는 RemoteMediator이다. RemoteMediator 객체는 로컬 데이터베이스 캐시가 있는 네트워크 데이터 소스와 같은 계층화된 데이터 소스의 페이징을 처리한다.

### **ViewModel Layer**
Pager 구성요소는 PagingSource 객체 및 PagingConfig 구성객체를 바탕으로 반응형 스트림에 노출되는 PagingData 인스턴스를 구성하기위한 공개 API를 제공한다.   

ViewModel레이어를 UI에 연결하는 구성요소는 PagingData이다. PagingData객체는 페이징된 데이더의 스냅샷을 보유하는 컨테이너이다. PagingSource 객체를 쿼리하여 결과를 저장한다.   

### **UI Layer**
UI Layer의 기본 페이징 라이브러리 구성요소는 페이지로 나눈 데이터를 처리하는 RecyclerView 어댑터인 PagingDataAdapter이다. PagingDataAdapter 대신 AsyncPagingDataDiffer 구성요소를 사용하여 고유한 Custom Adapter를 사용할 수 있다.

## **Use Paging3**

### **의존성 추가**
```gradle
implementation "androidx.paging:paging-runtime:$paging_version"
```
### **PagingSource**
```kotlin
class SamplePagingSource @Inject constructor(
    private val service: SampleBackendService
) : PagingSource<Int, String>() {

    override suspend fun load(params: LoadParams<Int>): LoadResult<Int, String>{
        return try {
            val next = params.key ?: 0
            val response = service.getPagingData(next)

            LoadResult.Page(
                data = response.data,
                prevKey = if(next == 0) null else next - 1,
                nextKey = next + 1
            )
        } catch (e: Exception) {
            LoadResult.Error(e)
        }
    }

    override fun getRefreshKey(state: PagingState<Int, String>): Int? {
        return state.anchorPosition?.let { anchorPosition ->
            state.closestPageToPosition(anchorPosition)?.prevKey?.plus(1)
                ?: state.closestPageToPosition(anchorPosition)?.nextKey?.minus(1)
        }
    }
}
```
PagingSource를 구현하기 위해 추상 클래스인 PagingSource를 상속받아서 SamplePageSource를 만들어준다. 구현해야 할 메서드는 load()와 getRefreshKey()가 있다. load()는 params를 바탕으로 페이지의 데이터를 반환하게 된다. getRefreshKey()는 refresh시 다시 시작할 key를 반환해 주면 된다.   

load()의 인자로 넘어오는 params의 key 값이 페이지 정보이다. 추가적으로 loadSize도 가지고 있다.   

return인 LoadResult는 Page, Error 두 가지가 있다. LoadResult.Page는 정상적인 경우에 사용하면 된다. LoadResult.Error는 Exception 발생이나 데이터가 문제가 있을경우 사용하면 된다.   

getRefreshKey()는 PagingState를 인자로 받는다. PagingState는 로드된 페이지 및 마지막으로 액세스 한 위치 등의 페이징 시스템의 스냅샷 상태를 가지고 있다.   

### **Repository**
```kotlin
class PagingRepository @Inject constructor(
    private val service: SampleBackendService
) {

    fun getPagingData(): Flow<PagingData<String>> {
        return Pager(PagingConfig(pageSize = 10)) {
            SamplePagingSource(service)
        }.flow
    }
}
```
Repository에서 Pager와 PagingSource를 사용하여 PagingData로 반환해 준다. PaginConfig로 페이저의 기본 설정을 정의해준 뒤 Pager 객체를 생성한다. 추가적으로 Pager 생성 시 초기 키 값을 지정해 줄 수도 있다.   

Pager를 생성 후 Flow 형태로 반환해 준다. 반환 형태는 Observable, LiveData로도 가능하다.   

### **ViewModel**
```kotlin
@HiltViewModel
class PagingViewModel @Inject constructor(
    private val repository: PagingRepository
) : ViewModel() {
    val pagingData = repository.getPagingData().cachedIn(viewModelScope)
}
```
Repository에서 PagingData를 가져온다. cachedIn(ViewModelScope)를 사용하여 캐싱을 해줄 수 있다.

### **PagingDataAdapter**
```kotlin
class PagingAdapter : PagingDataAdapter<String, PagingViewHolder>(diffCallback) {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): PagingViewHolder {
        val layoutInflater = LayoutInflater.from(parent.context)
        return PagingViewHolder(
            ItemSampleBinding.inflate(layoutInflater, parent, false)
        )
    }

    override fun onBindViewHolder(holder: PagingViewHolder, position: Int) {
        val item = getItem(position)
        if (item != null) {
            holder.bind(item)
        }
    }

    companion object {
        private val diffCallback = object : DiffUtil.ItemCallback<String>() {
            override fun areItemsTheSame(oldItem: String, newItem: String): Boolean {
                return oldItem == newItem
            }

            override fun areContentsTheSame(oldItem: String, newItem: String): Boolean {
                return oldItem == newItem
            }
        }
    }
}

class PagingViewHolder(
    private val binding: ItemSampleBinding
): RecyclerView.ViewHolder(binding.root) {

    fun bind(value: String) {
        binding.textView.text = value
    }
}
```
구현할 때 기존 RecyclerView.Adapter를 구현했던것과 유사하게 구현해주면 된다. 차이가 하나 있다면 diffUtill을 구현해 줘야 한다.