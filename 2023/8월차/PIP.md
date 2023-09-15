**Picture In Picture**
===============
Android API 24(7.0) 부터 다중 창을 제공한다. 바로 액티비티 안에 액티비티를 띄울 수 있는 기능이다. 안드로이드 폰의 경우에는 오버뷰 버튼을 이용해 화면을 분할해서 두 앱의 액티비티를 분할로 보여주는 기능이 있었고, PIP의 경우에는 안드로이드 TV에서만 지원이 가능했다.   

하지만 API 26(8.0)부터 PIP를 안드로이드 폰에서 활용할 수 있다.

# **사용방법**

## **AndroidManifest**
```xml
<activity
    android:name=".PIPActivity"
    android:supportsPictureInPicture="true">
</activity>
```
## **Activity**
```kt
//// PictureInPictureParams builder 선언
var pipParamBuilder: PictureInPictureParams.Builder? = null
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)

    pipParamBuilder = if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.O) {
        PictureInPictureParams.Builder()
    } else {
        null
    }

    binding.btn.setOnClickListener {
        minimize()
    }

    private fun minimize() {
        when {
            android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.O -> {
            	//	pip 창 사이즈 
                val aspectRational = Rational(100, 100)
                pipParamBuilder?.let { ppBuilder ->
                    ppBuilder.setAspectRatio(aspectRational)?.build()
                    enterPictureInPictureMode(ppBuilder.build())
                }
            }
            android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.N -> {
            	// deprecated
                enterPictureInPictureMode()
            }
            else -> {
                // 지원 불가
            }
        }
    }

    override fun onUserLeaveHint() {
        super.onUserLeaveHint()
        minimize()
    }

    override fun onPause() {
        super.onPause()
        minimize()
    }

    // pip <--> 앱 전환시
    override fun onPictureInPictureModeChanged(isInPictureInPictureMode: Boolean, newConfig: Configuration?) {
        super.onPictureInPictureModeChanged(isInPictureInPictureMode, newConfig)
        when(isInPictureInPictureMode) {
            true -> {
                Log.d("log", "in PIP")
            }
            else -> {
                Log.d("log", "application")
            }
        }
    }
}