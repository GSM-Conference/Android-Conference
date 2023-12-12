# DeepLink

Android DeepLink를 알기 전에 그 전에 기본적인 정의를 알아보자!

DeepLink는 홈페이지가 아닌 홈페이지 내에 특정 화면에 한 번에 도달할 수 있는 링크라고 한다.

구글 검색 결과에서 보이는 각종 사이트들은 사실 링크로 연결되어 있다. 예를 들어 검색했을때 나오는 네이버 블로그 글이나 안드로이드 공식 개발자 사이트 글이 있다.

그중에 네이버 블로그 글을 클릭하게 되면 이동하여 열리는 페이지는 네이버 블로그 공식 홈페이지로 가는 것이 아니라 DeepLink를 통해 자신이 검색한 내용에 대해서 설명되어 있는 개인 블로그로 들어가게 되는데 바로 이런 걸 DeepLink라고 볼 수 있다.

ex. 

https://www.blog.naver.com <- 일반 링크

https://www.blog.naver.com/100sm <- 딥링크

![deeplink](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fcavr3l%2FbtqWjqhXCfP%2FpFvSKkDL7pGQ0Y7oQQkKik%2Fimg.png)

그런데 DeepLink라는 개념은 모바일과 웹에서 모두 사용되는 개념이다.


웹같은 경우에는 브라우저 상단에 URL이 오픈되어 있어 쉽게 딥링크를 이해할 수 있을 것이다. 하지만 모바일은 보통 URL이 사용자에게 오픈되어 있지 않아서 쉽게 알 수가 없다. 

그렇다면 모바일에서 DeepLink는 어떤 형태로 구현되어 있을까?

---

## 모바일 환경에서의 DeepLink
모바일 환경에서의 DeepLink는 이런식으로 구현되어 있다.

![DeepLink](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbvI6UA%2FbtqV356YlwY%2FhX9Ahrfnn5lTkKbl8vFsj1%2Fimg.png)
 
데스크톱이 아닌 모바일 환경에서 네이티브 앱이 아닌 웹사이트를 통해 네이버 웹툰에 들어간 화면이다. 그러면 하단에 앱 설치하기 알람이 올라올텐데 아무래도 모바일 웹보다는 네이티브 앱이 더 사용자에게 좋은 UX/UI 경험을 줄 수 있을 것이다. 이럴 때 앱을 설치하라는 DeepLink를 사용자에게 추천하는 것이다.

또한 DeepLink를 구현하는 방법에는 여러가지가 있는데 그중 아래의 URL Scheme방식에 대해 알아보겠다.

## URL Scheme
URL Scheme방식은 Android, iOS 모두 사용 가능한 최초의 DeepLink 수단이다. 

***scheme://host_path***

우선 위의 형태로 정의되어 있는 링크들을 URL Scheme이라고 부른다.

예를 들어 https://www.naver.com 처럼 URL과 market:// 등이 모두 해당한다.

URL Scheme은 리소스를 구별하기 위한 용도로 사용하기 때문에 우리 앱을 부르는 고유한 수단으로 사용할 수 있다.

아래의 사진처럼 모바일 기기에서 URL Scheme 링크를 클릭했을때 앱이 캐치하여 반응하게 만들수 있다.

![URL_Scheme](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdwXvN6%2FbtqWdatijyy%2FjtQAPdj6s4Y8dq0RGgbsUK%2Fimg.png)

우린 Android의 Manifest.xml의 intent filter에 대해서 알고 있을것이다.

Android의 Activity가 명시적 인텐트가 아닌 암시적 인텐트로 실행되기 위해 사용되는 태그이다.

![intent_filter](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbzqJc8%2FbtqWx5D4hOI%2FxKEC4nK2SVlsN5kksqBsh1%2Fimg.png)

보면 위의 data 태그에 URL Scheme을 정의할 수 있다. scheme는 mashup, host는 deeplink로 정의하면,
mashup://deeplink 가 우리 앱의 딥 링크로 설정된 것이다.

## URL Scheme 값 전달
mashup://deeplink?date=20210206&message=전체 세미나

URL 파라미터처럼? 뒤에 매개변수 데이터를 전달하고 앱에서 받는 상황이 있을 수 있다.

위의 웹툰 DeepLink 예시를 보아도 더복서 웹툰 작가 등등 동일한 DeepLink의 다른 작가를 화면으로 보여주어야 할 때 매개변수가 필요할것이다.

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.main)

        val action: String? = intent?.action
        val data: Uri? = intent?.data

	if (action == Intent.ACTION_VIEW) {
		val date = data?.getQueryParameter("date")  //20210206
		val message = data?.getQueryParameter("message") //전체 세미나

		...
	}
 }
```

## URL Scheme의 한계점
URL Scheme 방식의 DeepLink는 마켓 앱의 수가 상대적으로 적었던 시기에는 문제없이 사용할 수 있었지만,
앱 수가 증가하면서 생각하지 못한 문제가 발생하기 시작했다. 바로 앱 Scheme 값이 서로 중복되는 경우에 대한 문제이다.

![Duplicate](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FxIQ9E%2FbtqWx6C4gij%2FirAkEokuf9Z7WMgugtNVlk%2Fimg.png)

Google Play 스토어 앱은 자신들의 앱에서 market:// 사용 중이었는데, 이후 원스토어와 Galaxy Store도 동일한 URL Scheme를 사용하게 되었다고 한다. 

유명한 앱의 스킴 값을 알아내서 동일한 스킴으로 앱을 배포를 하는 사람들이 늘어나게 되면서 유명한 앱들이 골치 아프게 된 것 이다.

![App_Scheme](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FOTH06%2FbtqWc8I7UAc%2F4SK4fvTInjcxQ0aKW5W8i1%2Fimg.png)


동일한 URL Scheme을 사용하여 DeepLink를 클릭하게 되면 위와 같은 화면이 보이게 되는데 여기서 사용자가 market:// 에 대한 DeepLink를 통해 열리는 앱을 원스토어로 고정하는 순간 Google Play Store 앱과 Galaxy Store 앱은 이후 딥 링크의 기능을 잃어버리게 된다.

URL Scheme 방식은 정말 간단한 게 DeepLink를 추가하는 방법이었지만, 간단한 만큼 다른 앱들도 동일하게 추가할 수 있다는 단점이 생겨버린 방식이였다.

<br>
<br>
<br>

## AppLink
---
위의 URL Scheme의 한계점을 해결하기 위해 나온 AppLink에 대해서 알아보겠다.

AppLink 2015년도에 Google/IO에서 발표한 기술이다. 바로 이 AppLink가 Scheme값이 중복되는 문제를 해결했다.

App Link는 http, https로 Scheme를 제한해서 URL Scheme 자체가 하나의 도메인을 나타나게끔 변경했는데 그로 인해 URL Scheme에서 자유롭게 정의했던 market:// 이나 Line:// 형식은 사용할 수 없다.

예를 들어 스마트폰 브라우저 앱 주소창에 http://naver.com 을 입력하면 네이버 앱이 바로 오픈되어 사용할 수 있게 하는 것이 AppLink라고 보면 편할 것 이다. 안드로이드에서는 Manifest 파일에서

![AppLink](https://help.dfinery.io/hc/article_attachments/360052990633/mceclip0.png)

이렇게 도메인을 등록 가능하다.

하지만 안타깝게도 앱링크가 아직까지는 완전하지 않다. 모든 앱에서 앱링크 오픈을 지원하는 것이 아니기 때문이다.

앱링크는 구글에서 만든 앱에서만 동작하고, 구글 이외에 앱에서는 정상적으로 동작하지 않습니다.