## ShortCut
---
Short Cut은 구글이 지원하는 앱을 롱클릭 했을때 특정 작업을 수행할 수 있는 바로가기를 정의할 수 있도록 하고 있다. 

안드로이드 앱을 꾹 눌러 보면 위의 화면처럼 앱 내의 특정 작업을 할 수 있는 화면으로 바로 이동시켜주는 리스트를 볼 수 있다. 바로 이를 Shortcut(바로가기)라고 얘기한다.

![image](https://blog.kakaocdn.net/dn/nRwhi/btqHxa19sFl/3Z4E0DrEVFdaPEGhUdapl0/img.png)

위의 사진이 예시라고 할 수 있겠다.

<br>

## Shortcut types
- Static Shortcuts
- Dynamic Shortcuts
- Pinned Shortcuts

Shortcut 타입은 위와 같이 총 세 가지가 있다. Shortcut 한 개는 하나 이상의 intent를 참조하며, 각 인텐트는 사용자가 Shortcut을 선택했을 때 앱에서 특정 작업을 시작한다.

 어떤 Shortcut 유형을 만들지는 앱의 주요 사용 케이스에 따라서 결정하면 된다. 대중적으로 사용되는 앱들의 예시는 다음과 같은 경우가 있다.

- 이메일 앱에서 새 이메일 작성하기
- 카카오톡과 같은 메신저 앱에서 친구에게 메시지 보내기
- 넷플릭스 같은 미디어 앱에서 프로그램의 다음 에피소드 재생하기
- 게임 앱에서 마지막 저장 지점 불러오기

<br>

## Static Shortcuts
이름에서 알 수 있듯이 정적인 shortcut이다. 이 shortcut은 APK나 App Bundle로 패키징된 리소스 파일에 정의되어 있다. 앱 실행 중에 변경하거나 할 수 없다.

<br>

## Dynamic Shortcuts
이것도 이름에서 알 수 있듯이 동적인.. 다이나믹한 shortcut이다. 런타임에 앱에서 만들거나 업데이트하고 삭제할 수 있다.

<br>

## Pinned Shortcuts
이 shortcut은 앱을 롱클릭하여 선택하는 shortcut이 아닌, 런처에 바로가기 아이콘을 꺼내 놓은 shortcut이다. 사용자가 권한을 허용하면 런타임에 추가할 수 있다.

---

## Static Shortcuts 추가
Static Shortcuts은 xml과 manifest에 meta-data태그를 추가해주기만 하면 된다.

![iamage](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fsqmuc%2FbtrGEJpAI6r%2F2zdVPqYFJDeT8xjRHsnpH1%2Fimg.png)

xml폴더를 따로 만들고, shortcuts.xml을 만들어서 사용했다. 

xml 폴더로 개발하지 않는 경우, 추후 meta-data에 선언할때 경로를 자신이 만든 것과 일치하게만 설정해주면 된다.

```xml
<shortcuts xmlns:android="http://schemas.android.com/apk/res/android">
    <shortcut
        android:shortcutId="manage_shortcuts"
        android:enabled="true"
        android:icon="@drawable/ic_launcher_foreground"
        android:shortcutShortLabel="@string/short_label"
        android:shortcutLongLabel="@string/short_label"
        android:shortcutDisabledMessage="@string/short_label">
        <intent
            android:action="android.intent.action.VIEW"
            android:targetPackage="com.example.ssa_modular"
            android:targetClass="com.example.ssa_modular.MainActivity" />
    </shortcut>

    <shortcut
        android:shortcutId="manage_shortcuts2"
        android:icon="@drawable/ic_launcher_background"
        android:shortcutShortLabel="@string/long_label"
        android:shortcutLongLabel="@string/long_label"
        android:shortcutDisabledMessage="@string/long_label">
        <intent
            android:action="android.intent.action.VIEW"
            android:targetPackage="com.example.ssa_modular"
            android:targetClass="com.example.ssa_modular.MainActivity" />
    </shortcut>
</shortcuts>
```
작성한 shortcuts.xml이다.

최 상위는 shortcuts로 선언하며, 내부에 shortcut를 필요한 만큼 작성하여 사용하면 된다.

Android 공식 문서에서는 최대 15개까지 가능하지만, 4개까지만 사용하는 것이 좋다고 한다.

선언된 shortcut의 속성 값을 확인해보자.

- shortcutId : 바로가기의 ID
- enabled : 해당 바로가기의 사용 여부. default 값은 true이다.
- icon : 바로가기의 icon
- shortcutShortLabel : 바로가기의 이름. 최대 10글자까지 노출.
- shortcutLongLabel : 바로가기의 이름. 최대 25글자까지 노출. 보이는 공간이 충분하면 알아서 LongLabel로 보여준다.
- shortcutDisabledMessage : 바로가기를 사용할 수 없을 때 노출되는 메시지.

여기서 봐야 할 것은 enabled과 String 값이다.

disabledMessage는 enabled가 true일 때는 아무런 동작을 하지 않는 값이지만, false일 경우 바로가기를 눌렀을 때 나오는 메시지이므로 작성을 해두는 것이 좋다.

물론, 해당값을 작성하지 않는다고 해서 아무런 동작을 안 하는 것이 아닌, default 메시지가 나올뿐이다.

![enabled](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fc1ftqa%2FbtrGBiNXwAC%2FhopB5PYzoZrvitSxeqAx80%2Fimg.png)

enabled를 false로 선언하게 되면 앱 아이콘을 롱 클릭했을 때 해당 shortcut은 나오지 않는다. 하지만, true인 상태에서 shortcut을 만들고, false로 다시 빌드하게 되면 해당 바로가기가 disable 처리 된다. 같은 아이콘으로 확인해 보았을 때, 위의 사진처럼 사용 가능 여부가 명확하게 보이게 된다.

확인이 필요한 String 값이란 것은, short, long label과 disableMessage에 들어가는 값을 말하는 것인데, 보통 간단한 샘플을 만들어서 확인할 때는 string.xml을 사용하지 않고 하드 코딩으로 값을 넣어서 확인하곤 한다.

하지만, 해당 부분은 하드 코딩으로 string 값을 넣으면 사용할 수 없고 반드시 리소스 파일에 작성하고 해당 리소스 파일을 사용해야 한다.

내부 요소로 선언할 수 있는 것은, Intent, Categories, capability-binding가 존재하는데, 이번에는 기본적인 설정만 할 예정이므로 Intent만 선언하여 사용하였다.

Intent에서는 Action이 반드시 포함되어야 하며, MainActivity를 실행시키는 바로가기 이므로 VIEW를 호출해주고 MainActivity가 존재하는 경로와 Package를 설정해주도록 하였다.

![intent](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FL3MFX%2FbtrGE3nYRBc%2FEa7iz4NkmMmFQ3Z3LlIq60%2Fimg.png)

그것 외에도 Intent 안에 선언할 수 있는 속성 값들은 존재하지만, 기본적인 설정을 하기에는 위의 세 가지만 선언해도 상관없다. 이처럼 xml파일을 구성해 주었으면, manifest 파일에서 meta-data로 선언해주어야 한다.

```xml
<activity
    android:name=".MainActivity"
    android:exported="true"
    android:launchMode="singleTop">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />

        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
    <meta-data
        android:name="android.app.shortcuts"
        android:resource="@xml/shortcuts" />
</activity>
```

선언하는 위치는 MAIN, LAUNCHER로 intent-filter에 선언되어 있는 activity에 선언해주면 된다.

조사한 자료에서는 SAA-Modular Sample에 바로가기를 추가하였기 때문에 신경을 쓰지 않았지만, 위와 같은 intent-filter가 선언되어 있지 않는 acitivty에 선언하게 되면, 빌드는 정상적으로 되지만 shortcut이 정상적으로 나오지 않는다고 한다.

![amaskd](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FO3DxQ%2FbtrGBfQKvRQ%2F3IncSaNm85UJCKS5Jut8qK%2Fimg.png)

여기까지 설정을 했다면 앱을 빌드하고 앱 아이콘을 롱 터치하면 다음과 같이 나오게 된다.

---
## Dynamic Shortcuts
위에서 설명한대로 런타임에 앱에서 업데이트를 하거나 삭제를 할 수 있는 shortcut이다. 이번 예제는 버튼을 눌렀을 때 바로가기를 생성하도록 만들어 보겠다.

```kotlin

override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)

    binding.dynamicShortcut.setOnClickListener {
        addDynamicShortCuts()
    }

        ...
}

private fun addDynamicShortCuts() {
    val shortcut = ShortcutInfoCompat.Builder(requireActivity(), "blog_shortcuts")
        .setShortLabel("Heeg's Blog")
        .setLongLabel("Heeg's Blog")
        .setIcon(IconCompat.createWithResource(requireActivity(),
            R.drawable.ic_launcher_foreground))
        .setIntent(Intent(Intent.ACTION_VIEW,
            Uri.parse("https://heegs.tistory.com/")))
        .build()

    ShortcutManagerCompat.pushDynamicShortcut(requireActivity(), shortcut)

    Toast.makeText(activity, "Shortcut 종류가 추가 되었습니다.", Toast.LENGTH_SHORT).show()
}
```

addDynamicShortCuts 함수를 보면, ShortcutnfoCompat을 통해 바로가기를 만드는 것을 알 수 있다.

Builder에 들어가는 값은 context와 id 값이다. 해당 id 값을 사용하여 동일한 shortcut이 생성되지 않게 된다.

short, long Label의 경우 정적 바로가기 생성과 동일한 역할을 하며, 바로가기의 아이콘 또한 설정이 가능하다.

setIntent를 통하여 해당 바로가기를 눌렀을 때의 동작을 작성해주면 되는데, 필자는 해당 바로가기를 통하여 블로그를 열 수 있도록 설정해 두었다.

Intent를 사용하여 작업을 지정하는 것이기 때문에, 별도의 화면으로 바로 이동한다던지, 데이터를 던진다던지 등의 작업을 수행할 수 있는데, 이것은 다음 고정된 바로가기를 만들 때 작업해보도록 하겠다.

 

shortcut에 대한 설정이 끝나면, ShortcutManagerCompat에서 제공하는 pushDynamicShortcut를 통해 설정한 shortcut을 업데이트하게 된다.

기존에 설정된 shortcut이 변경된다면 업데이트가 되고, 새로운 shortcut이라면 추가되게 된다.

```kotlin
private fun removeDynamicShortCuts() {
    val shortCutList = listOf("blog_shortcuts")
    ShortcutManagerCompat.removeDynamicShortcuts(requireActivity(), shortCutList)
    
    // All Delete
    ShortcutManagerCompat.removeAllDynamicShortcuts(requireActivity())
}
```

반대로, shortcut의 삭제를 위해서는 removeDynamicShortcuts과, removeAllDynamicShortcuts를 사용하면 된다.

removeDynamicShortcuts는 list에 삭제할 바로가기 id값을 넣어주면 해당 동적 바로가기가, removeAllDynamicShortcuts의 경우 context만 넣으면 모든 동적 바로가기가 삭제된다.

---
## Pinned Shortcuts

![imaasdge](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fn3wqy%2FbtrGIZ0o0u8%2FKzqKEYPJpXqGetiFWzFMIK%2Fimg.png)

Pinned Shortcuts은 이처럼 사용자가 편하게 바로가기를 추가할 수 있도록 도와주는 기능을 한다.

![as,dlasd](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fct7mg1%2FbtrGJ7Khk26%2FWZaWkfTQjfP7QEhzLQVddK%2Fimg.png)

고정된 바로가기의 경우, Android 8.0 (VersionCode 26) 이상 사용이 가능한데, isRequestPinShortcutSupported라는 함수를 통해 고정된 바로가기를 지원하는지 체크한 후 추가를 하면 된다.

또한, 위의 이미지처럼 해당 함수 자체가 Android 8.0 이상에서만 지원하기 때문에 

```kotlin
@RequiresApi(Build.VERSION_CODES.O)
```
해당 어노테이션을 추가해주어야 한다.

고정된 바로가기를 지원하는 기기인지 확인 후 해당 조건 문 안에 고정된 바로가기를 생성해주면 되는데, 우선 Android 공식 문서에 나와있는 예제를 확인해보자.

 ```kotlin
val shortcutManager = getSystemService(ShortcutManager::class.java)

if (shortcutManager!!.isRequestPinShortcutSupported) {
    // Assumes there's already a shortcut with the ID "my-shortcut".
    // The shortcut must be enabled.
    val pinShortcutInfo = ShortcutInfo.Builder(context, "my-shortcut").build()

    // Create the PendingIntent object only if your app needs to be notified
    // that the user allowed the shortcut to be pinned. Note that, if the
    // pinning operation fails, your app isn't notified. We assume here that the
    // app has implemented a method called createShortcutResultIntent() that
    // returns a broadcast intent.
    val pinnedShortcutCallbackIntent = shortcutManager.createShortcutResultIntent(pinShortcutInfo)

    // Configure the intent so that your app's broadcast receiver gets
    // the callback successfully.For details, see PendingIntent.getBroadcast().
    val successCallback = PendingIntent.getBroadcast(context, /* request code */ 0,
            pinnedShortcutCallbackIntent, /* flags */ 0)

    shortcutManager.requestPinShortcut(pinShortcutInfo,
            successCallback.intentSender)
}
 ```

보통, 예제를 그대로 사용하면 아주 기본적인 상태로 사용이 가능하기 마련인데, 그대로 복사해서 사용하면 오류가 발생한다. 따라서, 정상적으로 사용하기 위해서는 추가적으로 몇 가지 작업을 해주어야 한다.

그대로 빌드를 해보면 나오는 오류들을 해결해주면 되는데, 총 세 가지 작업을 진행해주면 된다.

- Shortcut의 Label 작성
- Shortcut의 Intent 작성
- PendingIntent에서 Flag 값 작성

```kotlin
val pinShortcutInfo = ShortcutInfo.Builder(context, "pin-shortcut")
    .setShortLabel("MoveFragment")
    .setLongLabel("MoveFragment")
    .setIntent(
        Intent().run {
            action = Intent.ACTION_SEND
            putExtras(sendData)
            setClass(requireActivity(), requireActivity()::class.java)
        }
    )
    .build()
val pinnedShortcutCallbackIntent =
    shortcutManager.createShortcutResultIntent(pinShortcutInfo)

val successCallback =
    PendingIntent.getBroadcast(context, 0, pinnedShortcutCallbackIntent, FLAG_IMMUTABLE)

shortcutManager.requestPinShortcut(pinShortcutInfo,
    successCallback.intentSender)
```

pinshortcutInfo에 바로가기에 대한 정보를 넣어주면 되는데, 아이콘은 별도로 넣어주지 않아도 바로가기 생성이 가능하다.

 

PendingIntent에서 Flag는 Android 12 (VersionCode 31)을 타깃 하는 경우 반드시 추가하도록 변경이 되었고, IMMUTABLE을 권장하고 있기 때문에 해당 값으로 설정하였다.

타깃 버전이 낮은 경우 해당 부분은 별도로 변경하지 않아도 상관없다.

```kotlin
@RequiresApi(Build.VERSION_CODES.O)
private fun addPinShortCuts() {
    val shortcutManager = requireActivity().getSystemService(ShortcutManager::class.java)

    if (shortcutManager!!.isRequestPinShortcutSupported) {
        val shortCutCount = shortcutManager.pinnedShortcuts.size

        var isExist = false
        if (shortCutCount > 0) {
            for (index in 0 until shortCutCount) {
                if (shortcutManager.pinnedShortcuts[index].id == "pin-shortcut") {
                    isExist = true
                }
            }
        }

        if (isExist) {
            Toast.makeText(activity, "이미 존재하는 Shortcut입니다.", Toast.LENGTH_SHORT).show()
            return
        }

        val sendData = Bundle().also {
            it.putString("destination", "MoveFragment")
            it.putString("other", "sample")
        }

        val pinShortcutInfo = ShortcutInfo.Builder(context, "pin-shortcut")
            .setShortLabel("MoveFragment")
            .setLongLabel("MoveFragment")
            .setIntent(
                Intent().run {
                    action = Intent.ACTION_SEND
                    putExtras(sendData)
                    setClass(requireActivity(), requireActivity()::class.java)
                }
            )
            .build()
        val pinnedShortcutCallbackIntent =
            shortcutManager.createShortcutResultIntent(pinShortcutInfo)

        val successCallback =
            PendingIntent.getBroadcast(context, 0, pinnedShortcutCallbackIntent, FLAG_IMMUTABLE)

        shortcutManager.requestPinShortcut(pinShortcutInfo,
            successCallback.intentSender)
    }
}
```
전체 코드를 보면 다음과 같다.

고정된 바로가기를 지원하는 경우에만 함수를 수행하도록 조건을 추가했고, 추가된 바로가기의 개수를 확인해서 지금 추가하려는 바로가기가 없는 경우에만 추가할 수 있도록 조건을 추가하였다.

setIntent부분을 보면 Bundle 데이터를 만들어서 던지는 것으로 구현하였는데, 정적 바로가기와는 다르게 앱 내부에서 바로가기를 통해 화면을 변경하기 위해 작성해 두었다.

전달되는 destination 값에 따라서 보이는 Fragment를 변경할 수 있도록 데이터를 넘겨주게 하였다.

우선 위의 코드처럼만 Intent를 던져주게 되면, 바로가기를 통해 앱 접근을 하면 해당 데이터를 가지고 접근하게 되므로 MainActivity에서 이 값에 따른 처리를 해주면 된다.

```kotlin
class MainActivity : AppCompatActivity() {

    private val viewModel: IntroViewModel by viewModel()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
            ...

        viewModel.setIntentData(intent.extras)
    }
}
```

우선, SAA구조로 Fragment에서 데이터를 컨트롤해야 하는데, 이를 쉽게 하기 위해 IntroViewModel을 MainActivity에서도 사용하도록 추가하였다.

viewModel을 통해 bundle 데이터를 저장하고, 
```kotlin
findNavController().navigate(com.example.navigation.R.id.mainFragment, intentData)
```
IntroFragment에서 화면을 MainFragment로 화면을 전환시킬 때 해당 Bundle 데이터를 함께 던지도록 하였다.

![navi](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FZffop%2FbtrGKxowPqv%2Fu0scFT1PZZVJyOfJkZ3wN1%2Fimg.png)

Navigation에서는 이처럼 navigate 함수가 오버로딩 되어있기 때문에 Bundle 타입의 변수를 쉽게 다른 Fragment에 전달할 수 있다.

```kotlin
override fun onActivityCreated(savedInstanceState: Bundle?) {
    super.onActivityCreated(savedInstanceState)
    initCallback()

    val intentData = arguments?.getString("destination")

    intentData?.let { setIntentData(it) }
}

private fun setIntentData(intentData: String) {
    val naviIndex = when (intentData) {
        "TextFragment" -> 1
        "MoveFragment" -> 2
        else -> 0
    }

    binding.navView.menu[naviIndex].onNavDestinationSelected(navController!!)
}
```
MainFragment에서는 이처럼 전달받은 값에 따라 ChildFragment를 변경하도록 하여서, 위에서 만든 고정된 바로가기를 통해 MainFragment에서 MoveFragment를 보이게 되는 것이다.

바로가기를 만드는 것 자체는 아주 간단하게 가능한데, 코드로 설명을 하자니 길어진 것 같다.

결국 중요한 부분은 Intent를 통해서 어떠한 데이터를 던질 것이고, 이것을 받았을 때의 컨트롤만 잘할 수 있다면 원하는 바로가기를 만들 수 있다는 것이다.

물론, 3가지 종류의 바로가기가 있기 때문에 어떠한 것을 선택하여 사용할지는 상황에 따라  달라지겠지만, 잘 사용만 하면 아주 편리한 서비스를 제공해줄 수 있을 것 같다.

