# Retrofit이란?
### Retrofit은 앱 개발 시 서버통신에 사용되는 API를 자바, 코틀린의 인터페이스 형태로 변환해 쉽게 호출할 수 있도록 도와주는 라이브러리 이다.
### 예를 들어 https://~~/user/{user}/repos라는 API가 존재한다고 가정해서 Retrofit라이브러리를 사용하면 아래 코드와 같은 인터페이스 형태로 변환할 수 있다.
```Kotlin
interface GitHubService{
    @GET("users/{user}/repos")
    fun getRepoList(@Path("user") user:String):Call<List<Repo>>
}
```

<br>

## Retrofit을 사용하기전 개발자가 준비해야 할 것
### 1. 안드로이드 프로젝트에 Intent 퍼미션 추가
### 2. Retrofit 라이브러리 프로젝트에 추가


<br>

## 1. 안드로이드 프로젝트에 Intent 퍼미션 추가
### 서버와의 통신은 인터넷을 거쳐야 하기 때문에 안드로이드 Manifest 파일에 internet 퍼미션을 추가해줘야 한다
```xml
<uses-permission android:name="android.permission.INTERNET" />
````

<br>

## 2. Retrofit 라이브러리를 프로젝트에 추가
```gradle
implementation 'com.squareup.retrofit2:retrofit:2.9.0'
implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
```