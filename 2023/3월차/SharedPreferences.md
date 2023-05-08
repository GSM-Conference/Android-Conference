SharedPreferences
================
안드로이드 앱 개발을 진행하다 보면, 앱의 데이터들을 저장하여 관리해야 할 상황들이 존재한다. 데이터의 양이 많거나 중요 데이터의 경우 서버나 DB에 저장해야겠지만, 간단한 설정값이나 문자열 같은 데이터를 저장하기 위해 DB를 사용하기는 부담스럽기 때문에 SharedPreferences를 사용하는 것이 적합하다.   

## **SharedPreferences의 특징**
- 보통 초기 설정값이나 자동 로그인 여부 등 간단한 값을 저장하기 위해 사용
- Application에 파일 형태로 데이터를 저장한다.
- Application이 삭제되기 전까지 저장한 데이터가 보존된다.
- Key-value 방식
```
MODE의 종류
 - MODE_PRIVATE : 생성한 Application에서만 사용이 가능하다.
 - MODE_WORLD_READABLE : 외부 App에서 사용 가능, 하지만 읽기만 가능.
 - MODE_WORLD_WRITEABLE : 외부 App에서 사용 가능, 읽기/쓰기 가능
```

## **SharedPreferences사용**

SharedPreferences는 앱의 어디서든 전역적으로 사용하기 때문에 싱글톤 패턴을 사용해서 어디서든 접근이 가능하게 만드는 것이 좋다.  

SharedPreferences 클래스는 앱에 있는 다른 액티비티보다 먼저 생성되어야 다른 곳에 데이터를 넘겨줄 수 있다. 이러기 위해서는 Application에 해당하는 클래스를 생성한 뒤, 전역 변수로 SharePreferences를 가지고 있어야 한다. Application()을 상속받는 MyApplication 클래스를 생성하여, onCreate()보다 먼저 prefs를 초기화 해준다.

### **MyApplication.kt**
```kt
class MyApplication : Application() {
    companion object {
        lateinit var prefs: PreferenceUtill
    }

    override fun onCreate() {
        prefs = PreferenceUtil(applicationContext)
        super.onCreate()
    }
}
```

MyApplication클래스를 생성했다면, Manifest에 등록해줘야 한다.
### **AndroidManifest.xml**
```xml
<application
    android:name="com.example.test.MyApplication"
</application>
```

다음은 SharedPreferences를 변수로 가지고 있는 PreferenceUtil 클래스를 생성한다. getString()과 setString() 메소드를 만들어서 SharedPreferences에 접근하여 데이터를 저장하거나 가져올 수 있다.

### **PreferenceUtil.kt**
```kt
class PreferenceUtil(context: Context) {
    private val prefs: SharedPreferences =
        context.getSharedPreferences("prefs_name", Context.MODE_PRIVATE)
    
    fun getString(key: String, defValue: String){
        return prefs.getString(key, defValue).toString()
    }

    fun setString(key: String, str: String){
        return prefs.edit().putString(ket, str).apply()
    }
}
```
데이터를 저장하거나 가져올 때는 아래의 예시코드를 사용해 접근하면 된다.
```kt
//데이터 조회
MyApplication.prefs.getString("email", "no email")
//데이터 저장
MyApplication.prefs.setString("email", "safdf@gmail.com")
```