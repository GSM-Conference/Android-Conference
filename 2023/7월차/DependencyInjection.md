# Dependency Injection
## 1. Dpendency란?
둘 중 하나가 다른 하나를 어떤 용도로 사용하는 경우
```kt
class 안드로이드_개발 {
    fun xml_공부()
}

class 개발자 {
    val 안드로이드_개발 = 안드로이드_개발()
    fun 공부() {
        안드로이드_개발.xml_공부()
    }
}
```
## 2. Dependency Injection 사용 이유
```kt
class 웹_개발 {
    fun html_공부()
}

class 개발자 {
    val 웹_개발 = 웹_개발()
    fun 공부() {
        웹_개발.html_공부()
    }
}
```
위와 같이 `안드로이드_개발` 클래스가 달라지면 `개발자` 클래스에도 영향을 미치기 때문에 **재사용성**과 **유연성**을 떨어뜨려 유지보수에 어려움이 있다.
```kt
interface 개발 {
    fun 공부()
}

class 안드로이드_개발 : 개발 {
    fun 공부() {
        xml 공부에 관한 내용
    }
}

class 웹_개발 : 개발 {
    fun 공부() {
        html 공부에 관한 내용
    }
}

class 개발자(개발: 개발) {
    private var 개발: 개발

    init {
        this.개발 = 개발
    }

    fun 개발_변경(변경된_개발: 개발) {
        this.개발 = 변경된_개발
    }

    fun 공부() {
        개발.공부()
    }
}
```
위와 같이 사용할 클래스를 직접 생성하는 것이 아니라 생성자와 메서드를 통해서 외부에서 객체를 주입하여 결합도를 줄이고, 좀 더 유연한 코드를 작성할 수 있다.
## 3. Dependency Inejection
크게 두가지로 나눌 수 있다.
- 생성자 주입: 생성자를 통해 의존하는 객체를 전달
- 필드 주입, 세터 주입: 객체가 초기화된 후, 메서드를 통해 의존하는 객체를 전달
> 직접 의존성을 만들어 제공하고, 관리하는 것을 `dependency injection by hand`, `manual dependency injection`이라고 부른다.
>> manual dependency injection을 하게 되면 큰 규모의 앱 같은 경우 모든 종속성을 올바르게 연결하기 위한 `boilerplate code`가 발생할 수 있다.
>> 의존성을 전달하기 전에는 의존성을 구성할 수 없는 경우가 생길 수 있다.
> 위와 같은 문제를 해결하기 위해 `Container`를 사용한다.