**Clean Architecture**
==================

## **Clean Architecture란?**

클린 아키텍쳐는 클린코드의 저자 로버트 마틴이 제안한 시스템 아키텍쳐로, 기존의 계층형 아키텍쳐가 가지던 의존성에서 벗어나도록 하는 설계를 제공한다.   
![pic](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FDQemw%2FbtrstXaQlp3%2F2KZbouOuLYQXeMOIfCIKC0%2Fimg.jpg)   
### 엔티티(Entities)
- 핵심 업무 규칙을 캡슐화 한다.
- 메서드를 가지는 객체, 일련의 데이터 구조와 함수의 집합이다.
- 가장 변하지 않으며 외부로부터 영향을 받지 않는 영역이다.
### 유즈 케이스(Use Case)
- 애플리케이션의 특화된 업무 규칙을 포함한다.
- 시스템의 모든 유즈 케이스를 캡슐화하고 구현한다.
- 엔티티로 들어오고 나가는 데이터 흐름을 조정하고 조작한다.
### 인터페이스 어댑터(Interface Adapter)
- 일련의 어댑터들로 구성한다.
- 외부 인터페이스에서 들어오는 데이터를 유즈 케이스와 엔티티에서 처리하기 편한 방식으로 변환하며, 유즈 케이스와 엔티티에서 나가는 데이터를 외부 인터페이스에서 처리하기 편한 방식으로 변환한다.
- 컨트롤러, 프레젠터, 게이트웨이 등이 여기에 속한다.
### 프레임워크와 드라이버(Framework & Drivers)
- 시스템의 핵심 업무와는 관련없는 세부사항이다.
- 프레임워크나, 데이터베이스, 웹 서버등이 이에 해당한다.

이때 클린아키텍쳐는 경계를 가장 중요하게 생각한다.
>소프트웨어 아키텍쳐는 선을 긋는 기술이며, 이러한 선을 경계(boundary)라고 부른다.   
경계는 소프트웨어 요소를 서로 분리하고, 경계 한편에 있는 요소가 반대편에 있는 요소를 알지 못하게 막는다.   
 -Robert Martin

 화살표의 방향은 의존성을 뜻한다. 클린 아키텍쳐의 의존성은 밖에서 안으로 향하고, 바깥원은 안쪽 원에 영향을 미치지 않는다. 경계의 바깥으로 갈수록 덜 중요하고 세부적인 영역으로 표현되며, 안으로 갈수록 고수준(좀더 추상화된 개념)으로 표현된다.   

 **바깥 원은 안쪽 원에 영향을 미치지 않는다!**

 ## **클린 아키텍쳐가 필요한 이유?**
 만약 비슷한 비즈니스 로직을 가진 두 앱을 통합하거나 서비스를 웹으로 확장할 때, 만약 클린 아키텍쳐를 도입 했다면, 단순하게 인터페이스 어댑터 영역과 프레임워크와 드라이브 영역만 수정하면 된다.   
 왜냐하면 비즈니스 로직 자체는 변하지 않았기 때문이다. 이와같이 클린 아키텍쳐는 비즈니스 로직은 바꾸지 않으면서, 언제든 DB와 프레임워크에 구애받지 않고 교체할 수 있는 아키텍쳐이다.   

## **안드로이드에서의 클린 아키텍쳐**

![pic](https://image.toast.com/aaaadh/real/2022/techblog/02%282%29.png)   
- 프레젠테이션 계층(Presentation Layer)
    - 뷰(View): 직접적으로 플랫폼 의존적인 구현, 즉 UI 화면 표시와 사용자 입력을 담당한다.   
    단순하게 프레젠터가 명령하는 일만 수행한다.
    - 프레젠터(Presenter): MVVM의 ViewModel과 같이, 사용자 입력이 왔을 때 어떤 반응을 해야 하는지에 대한 판단을 하는 영역이다. 무엇을 그려야 할지도 알고있는 영역이다.

- 도메인 계층(Domain Layer)
    - 유즈 케이스(Use Case): 비즈니스 로직이 들어있는 영역이다.
    - 모델(Entity): 앱의 실질적인 데이터이다.

- 데이터 계층(Data Layer)
    - 리포지터리(Repository): 유즈 케이스가 필요로 하는 데이터의 저장 및 수정 등의 기능을 제공하는 영역으로, 데이터 소스를 인터페이스로 참조하여, 로컬 DB와 네트워크 통신을 자유롭게 할 수 있다.
    - 데이터 소스(Data Source): 실제 데이터의 입출력이 여기서 실행된다.

데이터의 흐름은 다음과 같다.   
![pic](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FPJTRL%2Fbtq8IgnyP7P%2FUiDj9o89fYDn0kJKUkleHk%2Fimg.jpg)   

사용자의 인터렉션이 발생하면 이벤트는 위에서 아래로, 아래서 위로 흐른다. 사용자가 버튼을 클릭하면 **UI -> 프레젠터 -> 유즈 케이스 -> 엔티티 -> 리포지터리 -> 데이터 소스**로 이동하게 된다.   

위 흐름에서 의문이 생길 수 있다.
- 위 원에서 도메인 계층에 속해있던 엔티티가 왜 데이터 계층에 있는지?
- 트랜스레이터는 어디서 나왔나?
- 도메인 계층이 데이터 계층을 알고 있어야 데이터를 보낼 수 있는게 아닌가?

먼저, 데이터계층의 엔티티는 위 원에서의 엔티티가 아니다. 원의 엔티티는 도메인계층의 모델이며, 데이터 계층의 엔티티는 네트워크나 로컬 DB에서 받아온 DTO를 의미한다.   
따라서 계층을 횡단할 때 해당 계층에 맞게 변환해야 한다. 도메인 계층에서 모델이 트랜스레이터를 거쳐, 데이터 계층의 엔티티로 변환되는 것이다.(이는 반대로도 가능하다).   
또한 실제로 도메인 계층은 데이터 계층을 참고하고 있지 않는다. 레포지토리에서 이루어지는 의존성 역전법칙 때문이다.
>**의존성 역전이란?**   
객체 지향 프로그래밍에서 의존 관계 역전 원칙은 소프트웨어 모듈들을 분리하는 특정 형식을 지칭한다. 이 원칙을 따르면, 상위 계층(정책 결정)이 하위 계층(세부 사항)에 의존하는 전통적인 의존관계를 반전시킴으로써 상위 계층이 하위 계층의 구현으로부터 독립되게 만들 수 있다.

단순하게 말하면, 인터페이스로 만들고, 도메인 계층에서 인터페이스를 참조하면 된다.   
![pic](https://image.toast.com/aaaadh/real/2022/techblog/04%283%29.png)   

## **예외사항**
도메인 계층은 프레임워크에 종속되지 않게 짜야한다.   
하지만 도메인 계층에 어쩔 수 없이 안드로이드 라이브러리를 implementation 해야 하는 경우가 있다. 그 중 하나가 Paging3 라이브러리 이다.   

구글이 만든 Paging3 를 사용하려면 Paging Source를 사용해야 하고, 그렇다면 도메인 계층에 안드로이드가 종속되버리는 딜레마가 생기게 된다.

여기서 해결법은 testImplementation을 implementation 하는 것이다.
```gradle
// 안드로이드에 대한 종속성이 없어서 sync가 가능하다.
// alternatively - without Android dependencies for tests
testImplementation "androidx.paging:paging-common:$paging_version"
```
이를 통해, Android에 종속된 라이브러리도 사용할 수 있다.