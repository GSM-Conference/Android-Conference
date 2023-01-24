**ViewModel에서 이벤트를 처리하는 방법**
==================================
![pic](https://miro.medium.com/max/1400/1*WvRhTqDJOTQUtrlvpMOotw.webp)
ViewModel에서 이벤트 처리법은 LiveData를 사용하는 것을 시작으로 EventFlow를 사용하는 방법까지 나왔다.   
# **Step.1**
### *LiveData + Event*
![pic](https://miro.medium.com/max/884/0*cDZkky8Xk_lGyo3E.webp)
ViewModel에서 Livedata를 사용해서 이벤트를 처리하면 이벤트가 한번만 발생시키는 것이 아니라 observe할때마다 직전의 값이 발생하는 중복의 문제가 있다.   
1. ListActivity에서 Toast를 띄우라는 event를 날림
2. DetailActivity로 들어갔다가 다시 돌아옴
3. LiveData를 Observe하고 있던 Observer는 inactive되었다가 active되면서 observe시작
4. 1번의 Toast를 띄우라는 바로 직전의 Event가 다시 날라옴(LiveData는 observe할때 항상 마지막 값을 방출함)
5. 의도하지 않고 Toast가 발생하는 문제 발생

그래서 Event를 한번만 emit하고 consume시키는 Event Wrapper개념을 만들어서 LiveData와 함께 사용하는것으로 해결합니다.

# **Step.2**
### *SingleLiveData*

Step.1의 방법을 사용하면서 매번 ```LiveData<Event<XXX>>``` 로 사용하는 건 너무 번거롭다.   
그래서 이 Event Wrapper와 LiveData를 조합한 별도의 SingleLiveData라는 개념을 만들었다.   
이 SingleLiveData를 사용해서 코드를 더 간결하게 작성할 수 있게 되었다.   
이전   
```kt
private val _showToastEvent = MutableLiveData<Event<String>>()
val showToastEvent: LiveData<Event<String>> = _showToastEvent
```
이후
```kt
private val _showToastEvent = MutableSingleLiveData<String>()
val showToastEvent: SingleLiveData<String>() = _showToastEvent
```
# **Step.3**
### *SharedFlow*

![pic](https://miro.medium.com/max/1400/0*R07ojNRzVbIngL0g.webp)
프로젝트 코드를 Clean Architecture 패턴으로 구현하고 모든 layer를 module로 나누어서 관리할 때   
원칙적으로 ViewModel은 presentation layer에 위치하고 있으므로 특정 플랫폼과의 관계가 없어야 한다.   
즉, ``` import android.xxx```같은 코드가 없어야 한다.   
따라서 android 패키지에 해당하는 LiveData를 ViewModel에서 사용하는 것은 좋지 않을 수 있다.   
그래서 StateFlow, SharedFlow를 사용한다.   
결론적으로 LiveData -> StateFlow, SingleLiveData -> SharedFlow로 대체될 수 있다.   
ViewModel은 LiveData(안드로이드 프레임 워크)종속성으로부터 벗어날 수 있게 된다.   
기존의 SingleLiveData를 SharedFlow로 변경하고 observe대신에 collect하는 코드로 변경해주기만 하면 된다.
# **Step.4**
### *SharedFlow*
처리해야 할 Event가 3개인 경우, 기존에는 각각 3개의 SharedFlow를 만들어줬었다.
```kt
private val _showToastEvent = MutableSharedFlow<String>()
val showToastEvent = _showToastEvent.asSharedFlow()
private val _aaaEvent = MutableSharedFlow<String>()
val aaaEvent = _aaaEvent.asSharedFlow()
private val _bbbEvent = MutableSharedFlow<Int>()
val bbbEvent = _bbbEvent.asSharedFlow()
```
또한 각각의 Event를 collect하는 코드도 3개를 작성해야 한다.   
이것은 너무 귀찮은 일이기 때문에   
우리는 Event를 전파하는 단 1개의 eventFlow만을 만들고 이 Event를 Sealed class 형태로 만들어서 상황에 맞게 처리하도록 할 수 있다.   
UI에서는 1개의 eventFlow를 collect하고 Event유형에 맞게 처리하도록 분기하기만 하면 된다.
# **Step.5**
### *SharedFlow + Sealed Class + Lifecycle*   
하지만 Step.4의 방식에도 문제가 있다.   
문제가 되는 예시상황은 
1. ViewModel에서 서버와 통신하면서 위치데이터를 주기적으로 emit
2. UI에서는 위치데이터가 변경되는것을 감지하고 있다가 변경될때마다 화면에 새로 그림
3. 홈 버튼을 눌러서 화면이 BackGround에 있을때는 화면에 새로그릴 필요가 없음
4. UI가 안보일 때에는 데이터를 observe하고 있을 필요가 없음

이 문제의 가장 원시적이지만 확실한 해결방법은 onStart()에서 collect를 시작하고, onStop()에서 cancel하는 것이다.   
이 문제를 쉽게 해결하는 방법은 repeatOnLifecycle()이라는 함수를 사용하는 것이다.   
이 함수를 사용하면 번거롭게 onStart()/onStop()에서 작성을 하지 않아도 Lifecycle에 맞게 알아서 collect/cancel을 반복해 준다.
# **Step.6**
### *EventFlow + Sealed Class + Lifecycle*
또 다른 문제가 있다. 
```
목록에서 특정 item을 선택하면, 서버에서 상태를 체크한 뒤 상세화면을 실행하는 시나리오
```
1. 만약, 특정 item을 선택한 뒤
2. 서버 상태 체크가 끝나기전 홈버튼을 눌러 앱이 백그라운드로 내려갔다면 
3. 상세화면을 실행하는 event를 emit해도 onStop() 상태이기 때문에 이벤트를 받지 못함   

즉, event를 observe하고 있는 곳이 아무데도 없다면,
해당 event는 유실되어서 event가 발생했던것을 나중에라도 알 수 없다는 것이다.   
그래서 Event가 발생했을 때 이를 캐시하고 있다가 event의 consume여부에 따라서 새로 구독하는 observe가 있을 때 event를 전파할지 여부를 결정해주는 별도의 EventFlow를 만들어 사용할 수 있다.