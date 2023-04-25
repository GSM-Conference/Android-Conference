## Intent란
Component를 실행하기 위해 시스템에 넘기는 정보를 Intent 라고 말한다.

즉 실행하고자 하는 컴포넌트 정보를 담은 intent 구성 -> 시스템 -> Intent 정보를 통해 그에 맞는 Component를 실행하게 된다.

안드로이드는 Component 기반의 구조이고 이때 Intent에 의해 내부적으로 개발자가 작성한 Activity 같은 클래스들이 아래와 같이 동작하게 된다.

![Inten_동작과정](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fd6IrCs%2FbtqvXvVX8Gj%2F3zQfYcFASWlxRQcKdVuLWK%2Fimg.png) 

이때 위의 각각이 Class이지만 각 Class가 상호결합 하지 않는 상태로 실행된다. 그래서 이를 Component 라고 한다.

<br>
<br>

## Android Application 구성 요소와 Intent
---
![4가지구성요소](https://velog.velcdn.com/images%2Fjojo_devstory%2Fpost%2F9138556b-4a4c-4c48-a6dc-c9abc34e9b46%2F%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202020-03-06%20%EC%98%A4%EC%A0%84%2011.51.43.png)

안드로이드 어플리케이션을 구성하는 4가지 기본 요소에는 Activity, Service, Broadcast Receiver, Content Provider가 있다.

이때 위에서 말한 Intent 가 이러한 어플리케이션 구성요소(컴포넌트) 간에 작업 수행을 위한 정보를 전달하는 역할을 한다.
우리는 이미 Intent 를 사용하고 있었던 것이다. 가장 대중적인 예시를 하나 들자면 화면 전환을 할때마다 우린 Intent 를 사용하게 된다.

즉, Intent 는 컴포넌트 A 가 컴포넌트 B 를 호출할 때 필요한 정보를 담고 있으며, 이 정보에는 호출되는 컴포넌트 B 의 이름이 명시적으로 표시되거나, 속성들이 암시적으로 표시되기도 한다.

![구성요소](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcTFdDN%2FbtqyeI8sUl0%2Fpnk5Nq64yTKUexNc3DheCk%2Fimg.png)

또한 호출된 컴포넌트 B 가 호출한 컴포넌트 A 로 어떠한 결과를 전달할 때도 Intent 가 사용된다. 어떠한 컴포넌트를 호출하냐에 따라 사용되는 대표적인 메소드로는 다음과 같은 것들이 있다.
```md
Methods

startActivity():: 새로운 액티비티를 화면에 띄울 때

startServicsec(), bindService():: 서비스와 관련

broadcastIntent():: 브로드캐스팅을 수행할 때 
```
<br>
<br>

## Intent의 구분
Inten는 `Explicit Intent(명시적 인텐트)`와 `Implicit Intent(암시적 인텐트)`로 구분할 수 있는데, Intent 에 클래스 객체나 컴포넌트 이름을 지정하여 **호출될 대상을 확실히 알 수 있는 경우**에는 명시적 인텐트, 호출될 대상의 속성들을 지정했지만 **호출될 대상이 달라질 수 있는 경우**에는 암시적 인텐트라고 한다.


<br>

### 명시적 인텐트(Explicit Intent)

![명시적_인텐트](https://velog.velcdn.com/images%2Fchanghee09%2Fpost%2F52d1af40-2161-4caf-9823-29739d600e47%2Fimage.png)

실행하고자 하는 Component 클래스명을 Intent에 담는 방법.
주로 동일 App에서 다른 Component를 실행시킬때 사용한다.

<br>

### 암시적 인텐트(Implicit Intent)

![암시적_인텐트](https://velog.velcdn.com/images%2Fchanghee09%2Fpost%2F54969bf2-496f-46ab-8eb2-4ff18fe898cd%2Fimage.png)

암시적 인텐트는 다른 응용 프로그램의 컴포넌트를 호출할 때 사용된다.

즉, 클래스명을 알 수 없는 외부 앱의 컴포넌트를 실행할 때 이용한다. 이때는 Intent Filter 라는 것을 주로 이용하는데 Intent Filter는 Intent의 작업, 데이터 및 카테고리를 기반으로 어느 유형의 Intent를 수락하는지 지정하는 역할이다. 시스템은 Intent가 Intent Filter 중 하나를 통과한 경우에만 암시적 인텐트를 앱 구성 요소에 전달한다. 