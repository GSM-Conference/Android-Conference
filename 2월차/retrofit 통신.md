# Retrofit
![예시](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FpRiLS%2Fbtq2c3VEut4%2FJpOPa9ljQHOMfcJqYzDJnK%2Fimg.png)

---
#### 대부분의 안드로이드 개발자들은 통신 라이브러리로 **Retrofit**을 사용하고 있다고 한다.
#### Retrofit에 대해서는간단하게 한번 재설명하자면 Retrofit은 http 통신을 사용할때 쉽게 사용할 수 있는 라이브러리이고,안드로이드에서 api 서버와 통신을 할때 주로 쓰인다.

<br>
<br>

### Restrofit 예시

```Kotlin
interface GitHubService {
        @GET("users/{user}/repos")
        fun listRepos(@Path("user") user: String?): Call<List<Repo?>?>?
    }

    val retrofit: Retrofit = Builder()
        .baseUrl("https://api.github.com/")
        .build()
    val service: GitHubService = retrofit.create(GitHubService::class.java)

    val repos: Call<List<Repo>> = service.listRepos("octocat")   

```
### REST API 콜을 인터페이스 형식으로 준비한다. 그리고 Retrofit 객체를 만들어서 인터페이스의 인스턴스를 생성하고, 마지막으로 인터페이스를 동기 혹은 비동기적으로 구동시켜 response를 반환받는다.

