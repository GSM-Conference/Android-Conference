## Navigation Drawer?
---
Navigation Drawer란 쉽게 알려주자면 아래의 사진과 같다

![navi](https://cdn.discordapp.com/attachments/1089420730963329074/1133929659717931088/image.png)

다들 한번씩 스마트폰 앱을 사용해봤다면 가장 오른쪽의 아이콘이 익숙할텐데 것이다. 보통 저런 아이콘을 가진 View를 Tool bar 라고 하는데 Navigation Drawer를 사용하면 아래와 같이 클릭했을때 새로운 레이아웃이 나올것이다.

![navi1](https://cdn.discordapp.com/attachments/1089420730963329074/1133929699341500496/image.png)

또한 Navigation Drawer는 2가지의 요소로 이루어져 있는데 `Header`와 `Content`이다. 아래의 사진처럼 이루어져있는데 안드로이드 공식 홈페이지에서 잘 표현해 준 것 같아 가져왔다.

![navi2](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcuGGlS%2FbtqRx1mOKqU%2FkSMQBHqM9HMKrOKyRLJVL0%2Fimg.png)

Android Navigation Drawer에 대해 간단히 알아봤으니 이제 실제로 구현하는 방법에 대해 알아보자.

일단 Toolbar를 사용하기 위해서는 `theme.xml`을 액션바를 사용하지 않도록 바꿔줘야한다.

```xml
<style name="Theme.NavigationDrawer" parent="Theme.MaterialComponents.DayNight.NoActionBar">
```

<br>

Navigation Drawer를 사용하기 위해서는 DrawerLayout을 사용해야 하는데, 가장 바깥에 DrawerLayout을 깔아주고, 그 안에 원하는 레이아웃을 넣은 후 화면을 구성하면 된다. 

Toolbar를 사용하고 싶다면 자신이 작성한 레이아웃안에 Toolbar를 넣어주면 된다. 여기서 NavigationView가 바깥에 있는 이유는 Navigation Drawer화면은 화면에 계속 보여지는 것이 아니므로 아래의 xml 코드를 본다면 Relative Layout 밖에 View를 넣어주는 것이다. 

따라서 NavigationView는 RelativeLayout 밖이고, 마지막에 넣어줘야한다.

`activity_main.xml`
```xml

<androidx.drawerlayout.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity"
    android:id="@+id/drawer_layout">

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <androidx.appcompat.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:theme="@style/Theme.NavigationDrawer">

        </androidx.appcompat.widget.Toolbar>

    </RelativeLayout>

    <com.google.android.material.navigation.NavigationView
        android:id="@+id/navigation_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        android:fitsSystemWindows="true"
        app:headerLayout="@layout/navi_header"
        app:menu="@menu/navi_menu" />

</androidx.drawerlayout.widget.DrawerLayout>
```

<br>

위에서 말했던 것처럼 NavigationDrawer는 `Header`와 `Content`로 이루어져 있는데 Header와 Content는 따로 구현하는 편인 것 같다.

`navi_header.xml`
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <ImageView
        android:id="@+id/iv_image"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_marginTop="50dp"
        android:layout_gravity="center"
        app:srcCompat="@mipmap/ic_launcher_round" />

    <TextView
        android:id="@+id/tv_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_marginTop="10dp"
        android:text ="홍길동"
        android:textSize="20sp"/>

    <TextView
        android:id="@+id/tv_info"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="20, 남자"
        android:textSize="14sp"/>

</LinearLayout>
```

<br>

`Content`부분은 보통 menu로 구현을 한다.

`navi_menu.xml`
```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">

    <item
        android:id="@+id/item_info"
        android:title="내 정보" />
    <item
        android:id="@+id/item_report"
        android:title="리포트" />
    <item
        android:id="@+id/item_setting"
        android:title="계정 설정" />
    <item
        android:id="@+id/item_notice"
        android:title="공지사항" />
    <item
        android:id="@+id/item_service_center"
        android:title="고객센터" />

</menu>
```

이제 xml이 아닌 실제로 구현하는 코드를 확인해보겠다.

`MainActivity.kt`
```kotlin
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.google.android.material.navigation.NavigationView
import androidx.drawerlayout.widget.DrawerLayout
import androidx.appcompat.widget.Toolbar

class MainActivity : AppCompatActivity() {
    private lateinit var toolbar: Toolbar
    private lateinit var drawerLayout: DrawerLayout
    private lateinit var navigationView: NavigationView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        setupViews()
    }

    private fun setupViews() {
        // 툴바 초기화
        toolbar = findViewById(R.id.toolbar)
        setSupportActionBar(toolbar)

        supportActionBar?.apply {
            setDisplayHomeAsUpEnabled(true) // 왼쪽 상단 버튼 만들기
            setHomeAsUpIndicator(R.drawable.ic_baseline_dehaze_24) // 왼쪽 상단 버튼 아이콘 지정
        }

        // 드로어 레이아웃과 내비게이션 뷰 초기화
        drawerLayout = findViewById(R.id.drawer_layout)
        navigationView = findViewById(R.id.navigation_view)

        // 왼쪽 상단 버튼(홈 버튼) 클릭 이벤트 설정
        toolbar.setNavigationOnClickListener {
            drawerLayout.openDrawer(navigationView) // 내비게이션 뷰를 열어서 드로어 레이아웃을 보여줌
        }
    }

    override fun onBackPressed() {
        if (drawerLayout.isDrawerOpen(navigationView)) {
            drawerLayout.closeDrawer(navigationView) // 드로어 레이아웃이 열려있다면 닫기
        } else {
            super.onBackPressed() // 그렇지 않으면 기본 동작(뒤로가기) 수행
        }
    }
}
```

<br>

## 실행 화면
![실행화면](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FB8eAO%2FbtqRqcpCXWi%2Fc3kNBaGFkQLbMzsAdBWqi0%2Fimg.png)

위의 사진의 왼쪽 상단의 버튼을 누른다면?

![실행화면1](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fdhu5tw%2FbtqRqRertuC%2FNXIl2d0zK9LhU80TCHDQnk%2Fimg.png)

이 사진처럼 기존에 설정한 Header.xml과 Content.xml이 나올것이다!