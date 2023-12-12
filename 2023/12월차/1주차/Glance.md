# Glance
## Glance란?
Compose에서 Widget을 만들 수 있는 라이브러리
현재 알파버전만 출시되었다.
AGP가 7.0.0 이상, Compose가 1.1.0 이상, CompileSdk가 31이상 minSdk가 21이상이어야 한다.
## Glance의 작동방식
![glace 작동방식](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fdt0EG6%2FbtrZrwskHHi%2F24Jx3ZTOPgI6fkfEdbPMY1%2Fimg.png)
Jetpack Compose의 런타임을 사용하여 Composable을 RemoteView로 변환하여 앱 위젯을 표시할 수 있다.
크게 화면을 표시하는 GlanceAppWidget과 세부설정을 하는 GlanceAppWidgetReceiver로 구분할 수 있다.
## 구현
##### Manifest
```xml
<receiver android:name=".MyWidgetReceiver" android:exported="true">
    <intent-filter>
        <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
    </intent-filter>
    <meta-data
        android:name="android.appwidget.provider"
        android:resource="@xml/my_widget_info" />
</receiver>
```
##### my_widget_info.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
    android:previewImage="@drawable/compose"
    android:description="@string/app_name"
    android:minResizeWidth="50dp"
    android:minResizeHeight="50dp"
    android:resizeMode="horizontal|vertical"
    android:widgetCategory="home_screen" />
```
##### MyWiget.kt
```kt
class MyWidget(private val name: String): GlanceAppWidget() {
    @Composable
    override fun Content() {
        Text(text = "Hello $name")
    }
}
class MyWidgetReceiver : GlanceAppWidgetReceiver() {
    override val glanceAppWidget = MyWidget("SeungMin")
}
```
## Glance의 기본지식
#### Modifier
일반 Modifier가 아닌 GlanceModifier를 사용한다.
#### Click
- actionStartActivity
  activity를 실행시켜 준다.
- actionRunCallback
  미리 만들어두었던 callback을 실행시켜준다.
#### SizeMode
- Single
  Content() 메소드를 한번만 호출하되 메타 데이터에서 정의된 최소 크기로 적용이 된다. 크기를 변경하여도 콘텐츠는 새로고침 되지 않는다.
- Exact
  사용자가 위젯의 크기를 조정할 때마다 UI를 다시 만들어 Content() 함수를 다시 호출한다.
- Responsive
  Exact처럼 크기를 변경할 때 UI를 변경할 수 있지만, 개발자가 지정한 사이즈로만 표시가 가능하고 각각의 호출에 맞는 크기를 매핑하여 메모리에 저장하기 때문에 Content() 함수를 다시 호출하지 않는다.
#### State
{data}PreferencesKey를 이용하여 해당 데이터를 저장하는 preferences를 만들어준다.
updateAppWidgetState()를 사용하여, context와 glaceId를 넣어서 바꾸고 싶은 UI를 업데이트 한다.
currentState를 이용하여 preferences를 사용한다.
```kotlin
private val currentTime = stringPreferencesKey("time")

class MyWidget: GlanceAppWidget() {
    @Composable
    override fun Content() {
        val time = currentState(key = currentTime) ?: LocalTime.now().toString()
    }
}

class RefreshCallBack: ActionCallback {
    override suspend fun onAction(
        context: Context,
        glanceId: GlanceId,
        parameters: ActionParameters,
    ) {
        updateAppWidgetState(context, glanceId) {
            it[currentTime] = LocalTime.now().toString()
        }
        TestWidget().update(context, glanceId)
    }
}
```