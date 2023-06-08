## Glide vs Coil
Gilde와 Coil을 비교하기 전에 두 라이브러리가 어떤 라이브러리인지 간단하게 알고 가자!

<br>

### Glide
Glide는 Android에서 추천하는 이미지 로드 라이브러리이다.

Glide는 다른 이미지 로드 라이브러리들 보다 쉽게 이미지를 로드하고 더 나아가서 이미지 캐싱까지 해주는 편리한 라이브러리이다.

<br>

### 사용법
간단하게 사용법을 알아보자!

```kt
fun ImageView.setImage(url: String) {
    Glide.with(this)
        .load(url)
        .into(this)
}
```

사용법은 매우 간단하다!

`with`에 Context또는 View를 지정한다.

`load`에 불러올 이미지를 지정한다.

`into`에 불러온 이미지를 넣을 ImageView를 지정한다.


<br>

### Glide에서 제공하는 함수들
Glide는 단순히 이미지를 로드할 뿐만 아니라 여러가지 처리를 위한 함수들을 제공한다.

```kt
fun ImageView.glide(url: String) {
    Glide.with(this)
        .load(url)
        .override(500,500)
        .thumbnail(0.1f)
        .placeholder(android.R.drawable.btn_plus)
        .error(android.R.drawable.stat_sys_warning)
        .into(this)
}
```

override : 불러오는 이미지가 너무 클 경우, 이미지 로딩 속도가 지나치게 느려지거나 심하면 OOM으로 앱이 죽어버리는 상황이 발생할 수 있는데, override를 통해 이미지의 크기를 조절해서 로드할 수 있다.

thumbnail : override와 같은 상황에서 화질이 낮은 이미지를 먼저 보여주고 원본 이미지를 보여줄 수 있다.

placeholder : 이미지가 로딩 되기 전 표시되는 이미지를 지정한다.

error : 이미지 로드에 실패했을 때 표시되는 이미지를 지정한다.

<br>

이렇게 Glide에 대해 간단하게 알아봤다. 다음은 Coil로 넘어가보자!

<br>

### Coil
Coil은 Coroutine Image Loader의 약자를 가지고있는 Kotlin Coroutines로 만들어진 가벼운 Android 이미지 로딩 라이브러리이다!

그래서 Coroutine 자체가 내장이 되어 있어 Coroutine Library를 별도로 설치하지 않아도 되는 장점이 있다.

또한 열심히 업데이트가 돼고 있어 미래가 기대되는 라이브러리라고 한다! 하지만 한가지 아쉬운점은 국내에서 사용사례를 찾아보기 힘들고 관련자료가 많이 없다보니 실무에 적용하기에는 살짝 힘들수도 있다.
<br>

### 사용법
사용 방법은 매우 간단하다.

```kotlin
imageView.load("https://ww.100-seung-min-babo/image.jpg")

imageView.load(R.drawable.image)

imageView.load(File("/path/to/image.jpg"))
```

또한 Coil은 4가지의 Image Transformations를 제공한다

BlurTransformation : 이미지를 흐릿하게 보이게하는 Gaussian Blur를 적용한다.

CircleCropTransformation : 이미지의 중심을 기준으로 원형으로 이미지를 자른다

GrayscaleTransformation : Grayscale로 음영처리를 적용한다.

RoundedCornersTransformation : 사이즈에 맞도록 이미지를 자르고 이미지 모서리를 둥글게 라운드를 적용한다.

<br>

이제 마지막으로 Glide와 Coil을 비교해보자!

<br>

## Glide vs Coil
둘의 성능을 비교해보겠다!

일단 간단한 앱의 예시를 가져왔다.RecyclerView를 이용해서 하나의 화면에 엄청나게 큰 이미지 목록을 보여주는 앱이다.

```kt
class MainActivity : AppCompatActivity() {
 
    private lateinit var binding: ActivityMainBinding
    private lateinit var imageAdapter: ImageAdapter
 
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
 
        imageAdapter = ImageAdapter()
        binding.recyclerView.setHasFixedSize(true)
        binding.recyclerView.layoutManager = LinearLayoutManager(this)
        binding.recyclerView.adapter = imageAdapter
 
 
        val imageList = listOf(
            "https://wallpapershome.com/images/pages/pic_h/22741.jpg",
            "https://wallpapershome.com/images/pages/pic_h/18952.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22312.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22790.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22746.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22749.jpg",
            "https://wallpapershome.com/images/pages/pic_h/20322.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22740.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22796.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22803.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22784.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22820.jpeg",
            "https://wallpapershome.com/images/pages/pic_h/22836.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22830.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22833.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22793.jpeg",
            "https://wallpapershome.com/images/pages/pic_h/22745.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22805.jpg",
            "https://wallpapershome.com/images/pages/pic_h/19241.png",
            "https://wallpapershome.com/images/pages/pic_h/22861.jpg",
            "https://wallpapershome.com/images/pages/pic_h/20445.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22797.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22957.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22944.jpeg",
            "https://wallpapershome.com/images/pages/pic_h/22311.jpg",
            "https://wallpapershome.com/images/pages/pic_h/23166.jpg",
            "https://wallpapershome.com/images/pages/pic_h/23168.jpg",
            "https://wallpapershome.com/images/pages/pic_h/21665.jpg",
            "https://wallpapershome.com/images/pages/pic_h/23114.jpg",
            "https://wallpapershome.com/images/pages/pic_h/23115.jpg",
            "https://wallpapershome.com/images/pages/pic_h/23024.jpg",
            "https://wallpapershome.com/images/pages/pic_h/23053.jpg",
            "https://wallpapershome.com/images/pages/pic_h/23019.jpg",
            "https://wallpapershome.com/images/pages/pic_h/23017.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22998.jpg",
            "https://wallpapershome.com/images/pages/pic_h/22999.jpg",
            "https://wallpapershome.com/images/pages/pic_h/23033.jpg",
            "https://wallpapershome.com/images/pages/pic_h/23034.jpg",
            "https://wallpapershome.com/images/pages/pic_h/23035.jpg",
            "https://wallpapershome.com/images/pages/pic_h/23022.jpg",
            "https://wallpapershome.com/images/pages/pic_h/23023.jpg",
            "https://wallpapershome.com/images/pages/pic_h/23025.jpg",
            "https://wallpapershome.com/images/pages/pic_h/23026.jpg",
            "https://wallpapershome.com/images/pages/pic_h/23018.jpg"
        )
        imageAdapter.addList(imageList)
    }
}
```

<br>
<br>
<br>

### Test - 그냥 호출
아무처리도 하지않고 고화질 이미지를 호출했을때 메모리 사용량을 관찰했다.
```kt
 val imageUrl = getItemAt(position)
                
// Glide
Glide.with(itemView).load(imageUrl).into(imageView)

// Coil
imageView.load(imageUrl)
```

<br>

### Glide
모든 이미지를 로딩하고 아래에서 위깢 ㅣ스크롤 2번 한 후에 안정된 상태의 메모리 상태이다. 아주 잠깐 300MB까지 치솟다가 잠잠해졌다.

![glide_test](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fcu2QO4%2FbtqSgLQ3cdU%2FBiOKTNK7Vr08H8EIfawLr0%2Fimg.png)

![glide_test1](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FBJVaf%2FbtqSa9SpKGm%2FM3iFP76mJh0QzkP59mfVW1%2Fimg.png)

<br>

### Coil
Coil또한 메모리 상태를 관찰해보았다. 그래픽에 사용된 메모리는 Glide 111MB에 비하면 엄청나게 차이가 많은 편은 아닌데, Native 부분에서 50MB나 차이가 있었다. 메모리 사용량이 높을때에도 100MB대를 유지했다. 아마 코루틴을 사용해서 가볍다고 했던 Coil의 장점이 드러난듯 하다.

![coil_test](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FblHrg1%2FbtqSduhN946%2FP2bKqXSBpM4XFnaSpijY61%2Fimg.png)

![coil_test1](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FJd8Cf%2FbtqSduotLVp%2FIzncCUqHRhZXtKkyJAbNU1%2Fimg.png)
