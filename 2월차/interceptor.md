**Interceptor**
===========
Interceptro란 API 통신에서 요청에 monitor, rewrite, retry 할 수 있는 강력한 매커니즘이다.   
Interceptor를 통해서 우리는 API통신을 만들 때, 통신 과정을 모니터링 하거나 특별한 작업을 수행할 수 있다. 쉽게 말해서, Interceptor기능은 공항 보안요원이 보안검사하는 과정과 비슷하다.   
Interceptor는 중앙에서 API호출들을 모니터링 하는 것처럼 다양하게 사용된다.   
일반적으로 각각의 네트워크 호출에 대해 logger를 달아야 할 필요가 있지만 Interceptor를 이용한다면 하나의 logger를 추가하여 모든 네트워크 호출에 대해 동작하게 할 수 있다.
## **Interceptor의 종류**
### Interceptor에는 2가지 타입이 있다.
1. **ApplicationInterceptor**: Application Code와 Okhttp Core Library사이에 추가된 Interceptor.
- 이런 Interceptor들은 addInterceptor()를 이용한다.
2. **NetworkInterceptor**: Okhttp Core Library와 서버 사이에 추가된 Interceptor
- 이런 Interceptor들은 addNetworkInterceptor()를 사용한다.

## **OkhttpClient에 Interceptor추가**
```kt
fun devgeekHttpClient(): OkhttpClient {
    val builder = OkhttpClient().newBuilder().addInterceptor(/*myInterceptor*/)
    return builder.build()
}
```
위의 코드의 addInterceptor를 통해 내가 만든 Interceptor를 추가 할 수 있다.
### **Error-Interceptor**
```kt
class devgeekInterceptor: Interceptor{
    override fun intercept(chain: Interceptor.Chain): Response {
        val request = chain.request()
        val response = chain.proceed(request)

        when (response.code()) {
            400 -> {
                // show bad request error message
            }
            401 -> {
                // show unauthorized error message
            }
            403 -> {
                // show forbidden message
            }
            404 -> {
                // show not found message
            }

        }
        return response
    }
}
```
1. 먼저, chain.request()로부터 request를 받는다.
2. chain.proceed()에 request를 담아 보내, 서버로부터 온 response를 저장한다.
3. proceed(request)에서 response code를 확인하고 원하는 동작을 수행한다

예를 들어 401 Error를 받는다면, 로그아웃과 같은 동작을 수행한다.
### **Adding Header(Access Token)**
```kt
class UserAuth: Interceptor{
    override fun intercept(chain: Interceptor.Chain): Response {
        val requestBuilder = chain.request().newBuilder()
        var auth = "" // token
        requestBuilder.addHeader("Auth", auth)
        requestBuilder.addHeader("User-Agent", "App")
        return chain.proceed(requestBuilder.build())
    }
}
```
1. 먼저 chain.request()로 부터 request를 받아 newBuilder()로 requestBuilder를 생성한다.
2. 다음으로 local storage에서 header token을 읽어온다.
3. requestBuilder에 addHeader(K, V)를 이용하여 헤더정보를 담는다.
4. chain.proceed()에 requestBuilder를 담아 요청보낸다.