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


<br>
<br>
<br>

# retrofit에서 사용하는 어노테이션
Retrofit을 이용하기 위해 만든 Interface에서 메소드와 어노테이션을 통해서 요청이 처리되는 방법을 나타낼 수 있다.
- @GET : HTTP 메소드를 정의하는 어노테이션(조회)

- @POST : HTTP 메소드를 정의하는 어노테이션(생성)

- @PUT : HTTP 메소드를 정의하는 어노테이션(전체 수정)

- @PATCH : HTTP 메소드를 정의하는 어노테이션(일부 수정)

- @DELETE : HTTP 메소드를 정의하는 어노테이션(삭제)

- @HEAD : HTTP 메소드를 정의하는 어노테이션(헤더정보만 요청)

- @OPTIONS : HTTP 메소드를 정의하는 어노테이션 (특정 URL에 의해 지원되는 요청 메소드의 목록 리턴)

- @Body : 전송할 데이터를 모델 객체로 지정하고 싶을때 사용하는 어노테이ㅏ션

- @Path : URL의 경로를 동적으로 지정해야 할때 사용하는 어노테이션

- @Query : 경로에 ?를 이용해 데이터를 전달해도 되지만, 함수의 매개변수값을 서버에 전달하고 싶을 때 사용하는 어노테이션

- @QuertyMap : 전송할 매개변수 데이터가 많을때 사용하는 어노테이셔ㅑㄴ

- @FormUrlEncoded : 데이터를 URL인코딩 형태로 만들어 전송할 때 사용하는 어노테이션

- @Field : 인코딩 될 데이터에 추가하는 어노테이션

- @Header : 헤더값 변경 시 사용하는 어노테이션 

- @Url : baseUrl을 무시하고 전혀 다른 URL을 지정할 때 사용하는 어노테이션
