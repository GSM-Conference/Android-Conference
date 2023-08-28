**아이콘 동적변경**
==========

안드로이드에서 제공하는 PackageManager, activity-alias를 사용해 이미 설치된 앱의 아이콘을 동적으로 변경할 수 있다.   

## **How to?**
1. 우선 기본 앱 아이콘, 변경할 앱 아이콘을 준비한다.

2. MainActivityAlias.kt 라는 클래스를 만들고 일반 액티비티처럼 AppCompatActivity를 상속한다.

3. AndroidManifest.xml에 아래 값을 추가한다.   
android:roundIcon 에는 변경할 roundIcon을 android:icon에는 변경할 icon을 넣는다.

```xml
<activity-alias
    android:exported="true"
    android:name=".MainActivityAlias"
    android:roundIcon="@mipmap/ic_launcher_client_foreground"
    android:label="@string/app_name"
    android:icon="@mipmap/ic_launcher_client"
    android:enabled="false"
    android:targetActivity=".MainActivity">

    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity-alias>
```

4. MainActivity
```kt
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        binding.button.setOnClickListener {
            val manager = packageManager
            
            // MainActivity의 컴포넌트의 상태를 가져옵니다.
            val value = manager.getComponentEnabledSetting(ComponentName(
                applicationContext,
                MainActivity::class.java
            ))
            
            // 가져온 상태가 ENABLE 일 경우 DISABLE하고
            // MainActivityAlias의 상태를 ENABLE 해줍니다.
            if(value == PackageManager.COMPONENT_ENABLED_STATE_ENABLED) {
                manager.setComponentEnabledSetting(
                    ComponentName(
                        applicationContext,
                        MainActivity::class.java
                    ),
                    PackageManager.COMPONENT_ENABLED_STATE_DISABLED,
                    PackageManager.DONT_KILL_APP
                )
                manager.setComponentEnabledSetting(
                    ComponentName(
                        applicationContext,
                        MainActivityAlias::class.java
                    ),
                    PackageManager.COMPONENT_ENABLED_STATE_ENABLED,
                    PackageManager.DONT_KILL_APP

                )
            } else {
	
            // 가져온 상태가 DISABLE 일 경우 EABLE하고
            // MainActivity의 상태를 ENABLE 해줍니다.
                manager.setComponentEnabledSetting(
                    ComponentName(
                        applicationContext,
                        MainActivity::class.java
                    ),
                    PackageManager.COMPONENT_ENABLED_STATE_ENABLED,
                    PackageManager.DONT_KILL_APP
                )
            }
        }
    }
}
