**Navigation**
============
> Navigation은 Android 어플리케이션 내에서 '대상' 사이를 탐색하는 프레임워크로, 대상이 Fragment, Activity 또는 기타 구성요소로 구현되었는지에 관계없이 일관된 API를 제공합니다.   

즉, 화면이동을 보다 쉽고 편리하게 해주는 Jetpack의 라이브러리이다.
## **Navigation 구성요소**
Jetpack Navigation은 3가지의 구성요소로 이루어져 있다.

### **1. Navigation Graph**
모든 탐색관련 정보가 모여있는 xml 리소스.   
이곳에서 화면이동 destination, animation, argument 등 다양한 정보를 수정하고 만들 수 있다.
### **2. NavHost**
탐색그래프에서 대상을 표시하는 빈 컨테이너.   
간단하게 Fragment Navigation을 위한 화면의 빈 위젯 혹은 빈 창이다.
### **3. NavController**
NavHost에서 탐색을 관리하는 객체.   
사용자가 앱 내에서 이동할 때 NavHost에서 대상 콘텐츠를 전환하는 작업을 한다.   

**Use Jetpack Navigation**
=============
## 1. build.gradle(:app)
Jetpack Navigation을 사용하기 위해 모듈 수준의 그래들 파일에 의존성을 추가해준다.   
```gradle
 // Jetpack Navigation
    implementation 'androidx.navigation:navigation-fragment-ktx:$nav_version'
    implementation 'androidx.navigation:navigation-ui-ktx:$nav_version'
```
## 2. res/navigation/nav_graph.xml
1. res 디렉토리를 우클릭하고, New -> Android Resource File을 선택한다.

2. File name 필드에 'nav_graph'같은 이름을 입력한다.

3. Resource type 드롭다운 목록에서 Navigation을 선택후 생성한다.   

첫번째 탐색 그래프를 추가할 때 Android Studio에서 res 디렉토리에 navigation 리소스 디렉토리를 만든다. 이 디렉토리에는 탐색 그래프 xml 파일이 포함된다.   
nav_graph는 Navigation 탐색 그래프가 된다. 탐색 그래프를 통해 앱 내에서 사용자가 이용 가능한 모든 경로가 표시된다.   

```xml
<?xml version="1.0" encoding="utf-8"?>
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/nav_graph"
    app:startDestination="@id/fragmentFirst">

    <fragment
        android:id="@+id/fragmentOne"
        android:name="com.example.navigation.nav.FirstFragment"
        android:label="fragment_first"
        tools:layout="@layout/fragment_first" >
        <action
            android:id="@+id/action_fragmentFirst_to_fragmentSecond"
            app:destination="@id/fragmentSecond" />
    </fragment>

    <fragment
        android:id="@+id/fragmentSecond"
        android:name="com.example.navigation.nav.SecondFragment"
        android:label="fragment_second"
        tools:layout="@layout/fragment_second" >
        <action
            android:id="@+id/action_fragmenSecond_to_fragmentThird"
            app:destination="@id/fragmentThird" />
    </fragment>

    <fragment
        android:id="@+id/fragmentThird"
        android:name="com.example.navigation.nav..ThirdFragment"
        android:label="fragment_third"
        tools:layout="@layout/fragment_third" >
        <action
            android:id="@+id/action_fragmentThird_to_fragmentFirst"
            app:destination="@id/fragmentFirst" />
    </fragment>

</navigation>
```
app:StartDestination="@id/fragmentFirst" 는 navigation의 시작점을 나타냅니다.   

## 3. MainActivity
NavHost에 대한 정의를 한다.   
<FragmentContainerView> 태그 안에 프래그먼트 대상을 표시하는 기본 NavHost 구현인 NavHostFragment가 포함되도록   
android:name="androidx.navigation.fragment.NavHostFragment" 코드를 추가한다.   
app:defaultNavHost="true" 코드는 시스템의 백버튼 등의 이벤트가 그래프와 연동이 되도록 합니다. true로 설정하지 않을 경우 백버튼시 앱이 종료됩니다.   
app:navGraph="@navigation/nav_graph" 코드는 어떤 Navigation 탐색 그래프를 사용하는지 명시해준다.   
## 4. XXXFragment.kt
NavController를 통해 NavHost에서 콘텐츠를 전환할 수 있도록 한다.   
