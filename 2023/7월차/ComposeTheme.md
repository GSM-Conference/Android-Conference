# Custom Theme
Android 개발을 하다보면 다크모드와 라이트모드를 지원해야할때가 있다. 이때 Material을 사용하여 컬러를 적용하면 보다 쉽게 다크모드를 지원하는 앱을 만들 수 있다고 한다.

또한 몇몇 컴포넌트들은 별도로 컬러를 지정해주게 되는 상황이 오는데, 이때마다 컬러를 코드에 입력하기엔 관리 포인트도 늘어나고 다크모드까지 제공하려면 여간 힘든일이 아니다.


---
## 사용법
일단 우선 팔레트, 토큰, 롤, 테마를 정의해준다.
```kt
// 팔레트
val primary_100 = Color(0xffffffff)
val primary_99 = Color(0xfff2fff4)
val primary_95 = Color(0xffbeffd6)
val primary_90 = Color(0xff91f7ba)
val primary_80 = Color(0xff75dba0)
val primary_70 = Color(0xff59be86)
val primary_60 = Color(0xff3ba36d)
val primary_50 = Color(0xff158855)
val primary_40 = Color(0xff006d41)
// .... 기타 코드들은 너무 많아 생략...ㅋ

// 토큰
val md_theme_light_primary = primary_40
val md_theme_dark_primary = primary_80

// 롤-토큰
private val LightThemeColors = lightColorScheme(
    primary = md_theme_light_primary,
    onPrimary = md_theme_light_onPrimary,
)

private val DarkThemeColors = darkColorScheme(
    primary = md_theme_dark_primary,
    onPrimary = md_theme_dark_onPrimary,
)

// 앱 테마 설정
@Composable
fun AppTheme (content: @Composable () -> Unit) {
    val isDynamicColor = true
    val isDarkTheme = isSystemInDarkTheme()
    val dynamicColor = isDynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S
    val colorScheme = when {
        dynamicColor && isDarkTheme -> {
            dynamicDarkColorScheme(LocalContext.current)
        }
        dynamicColor && !isDarkTheme -> {
            dynamicLightColorScheme(LocalContext.current)
        }
        isDarkTheme -> DarkThemeColors
        else -> LightThemeColors
    }
    
    MaterialTheme (
        colorScheme = colorScheme,
        typography = typography,
        content = {
			Surface(content = content)
        },
    )
}
```

다음으론 베이스 컴포넌트들을 만들어둔다.
```kt
@Composable
fun MyText(label: String) {
    Text (
        style = MaterialTheme.typography.labelSmall.copy(
            color= MaterialTheme.colorScheme.primary
        ),
        text = label
    )
}
```
위와같이 material과 테마와 베이스 컴포넌트를 잡아두고 원하는 곳에서 아래처럼 사용하면, 디자이너와 약속된 디자인 가이드에다 따라 나름 편하게 개발을할 수 있게된다. (다크모드 걱정없이)
```kt
Column {
    MyText("아이디")
}
```
하지만 이렇게만 만들어두면 끝이 나는게 아니다. 만약 메인 UI 에서 텍스트는 0xFF333333(lightmode) 0xFF999999(darkmode)을 사용해야한다면?  그 말은 material3에서 제공하는 기본 롤 외에 다른 컬러를 사용할 일이 생긴 것이다.

아래와 같이 급조할 수가 있을 것이다
```kt
@Composable
fun MyTextForMainUI(label: String) {
    Text (
        style = MaterialTheme.typography.labelSmall.copy(
            color= if(isSystemInDarkTheme()) Color(0xFF999999) else Color(0xFF333333)
        ),
        text = label
    )
}
```
이렇게 하게되면 정리가 된거같으나 위 코드에서는 몇가지의 문제점이 있다.

1.매직넘버로 코드가 박힌 점

2.MyTextForMainUI함수에 다크모드에대한 종속성이 생긴 점

더 큰 문제는 다크모드 설정이 **@Composable** 함수에서밖에 되지 않는다.
내가 적용하고싶은 코드는 머티리얼 처럼 기본 오브젝트를 가져오면 알아서 다크테마로 변경이 됐으면 좋겠다. 다음과 같이 말이다.
```kt
@Composable
fun MyPlaceholder(label: String) {
    Text(
        text = placeholder,
        style = CustomMaterialTheme.typography.placeholderLarge.copy(
            color= CustomMaterialTheme.colorScheme.mySchemePrimary,
        ),
    )
}
```

우선 Hex를 지우고, 커스텀 컬러 팔레트를 만든다.
```kt
val custom_99 = Color(0xff999999)
val custom_80 = Color(0xff888888)
val custom_30 = Color(0xff333333)
// ...
```

그 후 Custom Color Scheme을 만든다.
```kt
object CustomColorLightTokens {
    val mySchemePrimary = custom_30
    val mySchemeSecondary = custom_30
}

object CustomColorDarkTokens {
    val mySchemePrimary = custom_99
    val mySchemeSecondary = custom_80
}

class CustomColorScheme(
    val mySchemePrimary: Color,
    val mySchemeSecondary: Color,
)

fun customLightColorScheme(
    mySchemePrimary: Color = CustomColorLightTokens.typoPrimary,
    mySchemeSecondary: Color = CustomColorLightTokens.typoSecondary,
) : CustomColorScheme=
    CustomColorScheme (
        mySchemePrimary,
        mySchemeSecondary,
    )

fun customDarkColorScheme(
    mySchemePrimary: Color = CustomColorDarkTokens.typoPrimary,
    mySchemeSecondary: Color = CustomColorDarkTokens.typoSecondary,
) : CustomColorScheme=
    CustomColorScheme (
        mySchemePrimary,
        mySchemeSecondary,
    )

```

이제 이 Scheme을 사용하기 위해 val로 소스에서 사용한다.
```kt
private val LightCustomThemeColors = customLightColorScheme()

private val DarkCustomThemeColors = customDarkColorScheme(
    mySchemeSecondary = // 새로운 컬러,
)
```
자 이제 매직넘버로 코드에서 작성하던 문제가 해결되고, 2번 문제인 다크모드에 대한 종속성을 제거하고자한다.

우선 오브젝트를 하나 만들고 LocalProvider로 등록할 수 있도록 설정해보자

```kt
object CustomMaterialTheme {
    val colorScheme: CustomColorScheme
        @Composable
        @ReadOnlyComposable
        get() = CustomLocalColorScheme.current

    // 타이포도 위에서 만들었던과 같은 방식으로 만들어주면 된다!
    val typography: CustomTypography
        @Composable
        @ReadOnlyComposable
        get() = CustomLocalTypography.current
}

internal val CustomLocalColorScheme = staticCompositionLocalOf { customLightColorScheme() }
internal val CustomLocalTypography = staticCompositionLocalOf { CustomTypography() }
```
이제 앱 테마 설정에서 local provier와 함께 설정해준다.

```kt
// 앱 테마 설정
@Composable
fun AppTheme (content: @Composable () -> Unit) {
    val isDynamicColor = true
    val isDarkTheme = isSystemInDarkTheme()
    val dynamicColor = isDynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S
    val colorScheme = when {
        dynamicColor && isDarkTheme -> {
            dynamicDarkColorScheme(LocalContext.current)
        }
        dynamicColor && !isDarkTheme -> {
            dynamicLightColorScheme(LocalContext.current)
        }
        isDarkTheme -> DarkThemeColors
        else -> LightThemeColors
    }
    
    val customColorScheme = when {
        isDarkTheme -> DarkCustomThemeColors
        else -> LightCustomThemeColors
    }
    
    MaterialTheme (
        colorScheme = colorScheme,
        typography = typography,
        content = {
             CompositionLocalProvider(
                CustomLocalColorScheme provides customColorScheme,
                CustomLocalTypography provides customTypography
            ) {
                Surface(content = content)
            }		
        },
    )
}
```

이렇게 Custom Theme을 만들고나면 이제 앞에서 소개한 것과 같이 다음처럼 쓸 수 있게된다.

```kt
@Composable
fun MyPlaceholder(label: String) {
    Text(
        text = placeholder,
        style = CustomMaterialTheme.typography.placeholderLarge.copy(
            color= CustomMaterialTheme.colorScheme.mySchemePrimary,
        ),
    )
}
```