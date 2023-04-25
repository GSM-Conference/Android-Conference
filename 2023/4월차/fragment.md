## Fragment 란?
![Fragment](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcNgs8A%2FbtqwdMwQKHe%2FrOtDWmRu4dKFehgCaaKwdK%2Fimg.png)

쉽게 말하자면 화면 분할을 가능하게 해주는 것이다.

<br>

앱에서는 한 화면에 여러 부분 화면을 넣는 경우가 많다.

ex. 레이아웃 A 안에 레이아웃 B

이때 부분 화면을 레이아웃을 넣어서 만드는 것이 아닌 Fragment(프래그먼트) 를 넣어서 만들 수 있다.

<br>

- 차이 fragment로 만들면, 한번 만들고 나서 재사용이 가능하다.

- 재사용하기 위해서 fragment는 activity를 본떠서 만들었다.
--> fragment는 activity의 특성과 layout의 특성을 둘 다 가지고 있다고 볼 수 있다.

- 만약 한 fragment가 activity 전체를 덮게 만들면 activity는 바뀌지 않고 화면 전환하는 것 같은 효과를 준다.

- fragment는 activity 위에 올려야 동작한다. 이는 xml 또는 코틀린코드에서 설정 가능하다.

- fragment에는 setContextView() 메소드가 없기 때문에, Layoutlnflater를 사용해 인플레이션을 해야한다. 이는 onCreateView() 메소드에서 사용할 수 있다. 

<br>

## 데이터 전달 방법들

1. bundle과 FragmentManager로 전달

2. Fragment Result API를 사용하여 Data 전달

3. Fragment간 공통의 ViewModel(ex. HostActivity의 ViewModel)로 전달

4. Jetpack의 Navigation에서 제공하는 safe-args로 전달

이러한 방법들이 있다. 하나씩 소개하자면

### 1. FragmentManager에 Bundle로 Data를 담아서 전달

전송하려는 Fragment class
```Kotlin
//PassBundleFragment는 본인이 전달하고자 하는 Fragment class
 val bundle = Bundle()
 bundle.putString("key", "value")
 
val passBundleBFragment = PassBundleBFragment()

passBundleBFragment.arguments = bundle 

parentFragmentManager.beginTransaction()
	.replace(R.id.fragment_container_bundle, PassBundleFragment())
	.commit()
```

받을 Fragment class

```Kotlin
override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
	var result = arguments?.getString("key")
	
    	return inflater.inflate(R.layout.fragment_pass_b, container, false)
    }

```
이렇게 data를 전달할 수 있다.


<br>

### 2. Fragment Result API를 사용하여 Data 전달
![data](https://developer.android.com/images/guide/fragments/fragment-a-to-b.png)

2020년도에 도입된 기능이다. 의존성 추가 후 사용 가능하다.

Gradle
```gradle
//(AppModule)
dependencies {
	...
    implementation "androidx.fragment:fragment-ktx:1.3.0"
}
```

전송하려는 Fragment class
```kotlin
//PassBundleFragment는 본인이 전달하고자 하는 Fragment class
//fragment_container_bundle은 본인의 Fragment가 담겨있는 Container
button.setOnClickListener {
    val result = "result"
    setFragmentResult("requestKey", bundleOf("bundleKey" to result))
    parentFragmentManager.beginTransaction()
       .replace(R.id.fragment_container_bundle, PassBundleFragment())
       .commit()
}
```

받을 Fragment class
```Kotlin
setFragmentResultListener("requestKey") { requestKey, bundle ->
        val result = bundle.getString("bundleKey")
        // Do something with the result
    }
```

"requestKey"는 사용하는 Fragment에서 어떤 Listener에게 데이터를 전달할 지 결정하기 위한 식별자로 사용된다.

다른 Fragment에서 같은 "requetKey"로 setFragmentResultListener를 적용할 경우 마지막에 적용한 listener가 호출된다.

<br>

### 3. ViewModel을 이용한 Data 전달

![viewmodeldata](https://images.velog.io/images/sysout-achieve/post/38b2703e-7642-41e4-8b92-3951ea4dbb1c/image.png)

하나의 Activity에서 container로 여러 Fragment를 이동하는 경우 사용할 수 있다.

Fragment가 Activity 하위에 위치하기 때문에 사용할 수 있는 아이디어다.

Activity를 공유하는 여러 Fragmnet들은 Activity의 메모리를 공유할 수 있고 AAC의 ViewModel은 Activity의 lifecycle보다 오래 살아있는 것이 보장되기 때문에 안전하게 공통의 Activity의 ViewModel을 사용하여 데이터 전달이 가능하다.

### Code

```kotlin
class ViewModelPassActivity : AppCompatActivity() {
    val viewModel : PassingViewModel by viewModels()
}

class PassAFragment : Fragment() {
    val viewModel : PassingViewModel by activityViewModels()
}

class PassBFragment : Fragment() {
    val viewModel: PassingViewModel by activityViewModels()
}
```

Activity에 Fragment들이 같은 데이터를 바라볼 수 있게 ViewModel 객체를 만들고 그 객체에 데이터를 전달하여 간단하게 Fragment 통신을 달성할 수 있다.

<br>

### 4. Jetpack의 Navigation에서 제공하는 safe-args로 전달

이 방법은 Jetpack의 Navigation을 사용하는 프로젝트에서 적용하기 좋은 방법이다.

장점으로는 navigation.xml에서 화면전환에 필요한 액션과 data를 정의할 수 있고, defaultValue도 정의가 가능하다. 또한, 전달하는 데이터 타입이 받는 곳의 데이터 타입과 다를 경우 compile 에러를 일으켜 의도하지 않은 상황이 발생할 확률을 줄여준다.

간결하고 안정적인 처리의 장점이 있지만 다른 Data전달 방식보다 환경세팅하는 것이 많고 알아야 할 기능들이 많다. 단지 Fragment Data전달을 위해 Navigation을 쓰는 것은 비효율적이지만 전체 프로젝트에 Navigation을 적용했을 때 얻는 이점이 많으니 고민하고 결정해야 한다고 한다.

### 전달 방식
```xml
//Navigation.xml에서 이동할 화면에 대한 정의(action)와 전달할 데이터(argument)를 정의한다.
<?xml version="1.0" encoding="utf-8"?>
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/nav_graph"
    app:startDestination="@id/passArgsAFragment">

    <fragment
        android:id="@+id/passArgsAFragment"
        android:name="com.gunt.fragmentdatapassexample.pass.passargs.PassArgsAFragment"
        android:label="PassArgsAFragment" >
        <action
            android:id="@+id/passAToB"
            app:destination="@id/passArgsBFragment"
            app:launchSingleTop="true" />
        <argument
            android:name="argsString"
            app:argType="string"
            android:defaultValue=""/>
    </fragment>
    <fragment
        android:id="@+id/passArgsBFragment"
        android:name="com.gunt.fragmentdatapassexample.pass.passargs.PassArgsBFragment"
        android:label="PassArgsBFragment" >
        <action
            android:id="@+id/passBToA"
            app:destination="@id/passArgsAFragment"
            app:launchSingleTop="true"
            app:popUpTo="@id/nav_graph" />
        <argument
            android:name="argsString"
            app:argType="string"
            android:defaultValue=""/>
    </fragment>
</navigation>
```

전송하려는 Fragment class
```kotlin
//액션에 본인이 보내려는 데이터를 담아 액션을 수행한다.
binding.btnSend.setOnClickListener {
            val action = PassArgsAFragmentDirections.passAToB(binding.etText.text.toString())
            findNavController().navigate(action)
        }
```

받을 Fragmnet class
```kotlin
 val args: PassArgsAFragmentArgs by navArgs()
 binding.textView.text = args.argsString
```

> 참고 https://github.com/sysout-achieve/FragmentDataPassExample