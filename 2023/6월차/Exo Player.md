**ExoPlayer**
===============
ExoPlayer는 Android 프레임 워크에 속하지 않고 Android SDK에서 별도로 배포되는 오픈소스 프로젝트이다. ExoPlayer의 표준 오디오 및 동영상 구성요소는 Android 4.1(API 레벨 16)에서 출시된 Android MediaCodec API를 기반으로 한다.   

ExoPlayer는 MediaPlayer에서 지원하지 않는 DASH(Dynamic adaptive streaming over HTTP), SmoothStreaming 및 일반 암호화 같은 기능을 제공한다. 

# **ExoPlayer 사용준비**
### **1) 빌드 종속 항목 추가**
build.gradle(app) 파일의 dependencies에 아래 코드를 입력한다.
```gradle
implementation 'com.google.exoplayer:exoplayer:$exoplayer_version'
```
ExoPlayer는 사용 목적에 따라 필요한 라이브러리를 추가할 수 있다. 만일 그렇지 않으면 많은 양의 확장함수를 다운로드하기 때문에 APK파일의 크기가 커지고 앱의 성능이 저하될 수 있다.   

core와 ui라이브러리만 추가할 수 있다.
```gradle
implementation 'com.google.exoplayer-core:exoplayer:$exoplayer_version'
implementation 'com.google.exoplayer:exoplayer-ui:$exoplayer_version'
```
core라이브러리는 항상 필요한 라이브러리이므로 항상 추가해 준다.

### **2)Java 8**
build.gradle(app) 파일에서 Java 8을 지원하도록 설정한다.
```gradle
compileOptions {
  targetCompatibility JavaVersion.VERSION_1_8
}
```

### **3)멀티덱스 사용 설정**
앱 및 앱이 참조하는 라이브러리에서 메서드의 개수가 65,536개를 초과하게 되면 빌드 오류가 발생하게 된다. 이 오류는 앱이 안드로이드 아키텍쳐의 제한에 도달했음을 알리는 것이며, 65,536이라는 숫자는 단일 DEX 바이트 코드 파일내에서 코드가 호출할 수 있는 참조의 총 개수를 나타낸다.   

이때 멀티덱스를 사용하여 앱이 다중 DEX파일을 빌드하고 읽을 수 있게 한다.   

API 레벨 21 이상에서는 멀티덱스가 기본적으로 사용되지만, 20이하에서는 그렇지 않기 때문에 이 경우 멀티덱스 라이브러리를 build.gradle(module)파일에 추가해 준다.

# **ExoPlayer 구현**
## **1. PlayerView**
PlayerView는 실제 영상 또는 소리를 화면에 표시할 때 사용할 UI 요소이다. xml파일에 다음과 같이 선언한다.
```xml
<com.google.android.exoplayer2.ui.PlayerView
    android:id="@+id/playerView"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="@color/black"
    app:controller_layout_id="@layout/custom_exo_view"
    app:auto_show="true"
    app:resize_mode="fixed_width"
    app:surface_type="surface_view"
    app:use_controller="true" />
```
PlayerView는 다양한 속성을 사용해 커스텀할 수 있다.
- **auto_show**   
    콘텐츠가 재생되거나 정지할 때, 혹은 끝나거나 재생에 실패할 때 컨트롤러가 자동으로 나오도록 설정하는 속성이다.

- **resize_mode**   
    재생되는 콘텐츠를 playerview크기에 맞게 설정하는 속성이다.   

    fit, fixed_width, fixed_height,fill, zoom등의 속성으로 설정할 수 있다.   

    fixed_width로 설정하면 playerview의 너비에 맞게 콘텐츠 크기가 리사이즈 된다.

- **surface_type**   
    비디오 플레이백에 사용될 surface의 유형을 설정한다.   

    surface_view, texture_view, spherical_gl_surface_view, video_decoder_gl_surface_view, 그리고 none이 있다.   

    만일 오디오만 재생을 한다면 none이 권장된다. surface_view나 texture_view는 비용이 많이들 수 있기 때문이다.   

    만일 일반적인 비디오 재생을 한다면 surface_view가 texture_view보다 재생에 있어서 이점이 더 많다.

- **use_controller**   
    비디오를 재생하면 재생 버튼등이 있는 컨트롤러가 있다.   

    이를 사용할지 설정하는 속성이다. true로 설정할 시 컨트롤러를 사용할 수 있다.

- **controller_layout_id**   
    플레이어 뷰에서 사용할 컨트롤러의 레이아웃도 커스텀하여 지정할 수 있다.   
    ![ex](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdNbfjU%2FbtrgDUYcBwD%2F1KiLIG5s2LUojPsmFw0K2K%2Fimg.png)

# **ExoPlayer - 사용법**
ExoPlayer 객체를 만들어 준다. PlayerView에 ExoPlayer 객체를 바인딩해서 화면에 비디오를 표시한다.   

SimpleExoPlayer.Builder를 통해 만들어 주는 것이 쉽기 때문에 이를 사용한다.   

SimpleExoPlayer는 ExoPlayer의 인터페이스이다.   

다음과 같이 사용한다.   
```kt
simpleExoPlayer = SimpleExoPlayer.Builder(this)
    .build()
    .also{exoPlayer -> 
    playerView.player = exoPlayer
    }
```
build()메서드를 통해 만들어주고 PlayerView 객체의 player에 exoPlayer객체를 설정한다.   
이때 exoplayer 객체 생성은 메모리 해체와 대응되어야 한다.

# **사용법 - MediaItem**
MediaItem은 콘텐츠를 재생하기 위해서 필요한 가장 작은 요소이다.   

아래 코드처럼 url로부터 MediaItem을 만들 수 있다.   

그리고 setMediaItem() 메서드를 통해 exoplayer객체에 MediaItem을 설정하면 화면에 콘텐츠를 재생할 수 있다.
```kt
val videoUrl = urls.get(movieTitle.text.toString())
val mediaItem = MediaItem.fromUri(Uri.parse(videoUrl))
simpleExoPlayer.setMediaItem(mediaItem)
```

# **사용법 - 재생**
```kt
simpleExoPlayer.prepare()
var position = getSharedIntData("prefPlay", "position${currentTitle}")
simpleExoPlayer.seekTo(position.toLong())
simpleExoPlayer.play()
```
먼저 prepare() 메서드를 통해 재생 준비를 한다.   
만일 저장된 재생위치가 있다면 위 코드처럼 가져올 수 있고, 그렇지 않다면 바로 play() 메서드를 통해 재생을 시작하면 된다. 일반적으론 버튼이나 뷰의 클릭 리스너에 play()메서드를 넣어서 재생하도록 한다.

# **사용법 - 생명주기 메서드**
생명주기 메서드를 사용하여 exoplayer객체의 상태를 관리한다.
```kt
override fun onResume() {
        super.onResume()
        val position = getSharedIntData("prefPlay", "position${currentTitle}")
        simpleExoPlayer.seekTo(position.toLong())
    }

    override fun onPause() {
        super.onPause()
        simpleExoPlayer.pause()
        simpleExoPlayer.playWhenReady = true
        setSharedData("prefPlay", "position${currentTitle}", simpleExoPlayer.currentPosition.toInt())
    }

    override fun onStop() {
        super.onStop()
        simpleExoPlayer.stop()
        simpleExoPlayer.playWhenReady = false
    }

    override fun onDestroy() {
        super.onDestroy()
        simpleExoPlayer.release()
    }
```
- **onResume()**   
    화면이 "재개됨"상태로 진입하면 SharedPreferences에 저장된 재생위치를 가져와 해당 위치에서 재생할 수 있도록 설정한다.   

    이때 seekTo() 메서드를 사용한다.

- **onPause()**   
    동영상을 일시 정지하고, 재생위치를 저장한다.   

    그리고 playWhenReady를 true로 설정해 다시 재생될 준비가 되면 재생되도록 설정한다.

- **onStop()**   
    exoplayer를 정지하고 playWhenReady를 false로 설정합니다.

- **onDestroy()**   
    exoplayer를 release 합니다.