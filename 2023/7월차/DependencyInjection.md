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

직접 의존성을 만들어 제공하고, 관리하는 것을 `dependency injection by hand`, `manual dependency injection`이라고 부른다.
> 사용 시 주의할 점
> - manual dependency injection을 하게 되면 큰 규모의 앱 같은 경우 모든 종속성을 올바르게 연결하기 위한 `boilerplate code`가 발생할 수 있다.
> - 의존성을 전달하기 전에는 의존성을 구성할 수 없는 경우가 생길 수 있다.
> 
> 위와 같은 문제를 해결하기 위해 `Container`를 사용한다.
## 4. DI 프레임워크
### Dagger
##### 필수 5가지 개념
- Inject
`필드`, `생성자`, `메서드`에 붙여 Component로 부터 의존성 객체를 주입 요청하는 `annotation`
`interface`에서는 생성자가 없으므로 사용불가, 써드파티 라이브러리 등의 클래스는 참조가 불가능하여 사용불가
- Component
`interface`, `abstract class`에서만 사용가능
컴파일 타임에 Dagger와 클래스 이름이 합쳐진 Dagger 클래스 자동 생성
Module을 이용하여 의존성 객체 생성, Inject로 요청 받은 객체 주입
- Provision Method
매개변수가 없고, Module이 제공하는 객체의 타입을 반환형으로 갖는다.
- Member-Injection Method
의존성을 주입시킬 객체를 메서드의 파라미터로 넘기는 방식이다.
Module에 초기설정이 필요할 때는 @Component.Builder annotation을 사용한다.
```kt
@Component(modules = [Moduel_A::class, Module_B::class])
interface MyComponent {
    @Component.Builder
    interface Builder {
        fun moduleA(moduleA: Module_A) : Builder
        fun moduleB(moduleB: Module_B) : Builder
        fun build() : MyComponent
}

DaggerMyComponent.builder()
    .moduleA(Module_A())
    .moduleB(Module_B())
    .build()
```
`dagger-android 2.22`부터 파라미터를 1개만 받을 수 있는 Builder를 보완하기 위한 @Component.Factory를 사용할 수 있다.
```kt
@Component(modules = [Moduel_A::class, Module_B::class])
interface MyComponent {
    @Component.Factory
    interface Factory {
        fun create(moduleA: Module_A, moduleB: Module_B) : MyComponent
}

DaggerMyComponent.factory()
    .create(Module_A(), Module_B())
```
- SubComponent
부모가 있는 자식 컴포넌트
- Module
의존성 객체를 생성하고, Scope에 따라 객체를 관리하기도 한다.
반드시 **클래스**에만 붙여야 한다.
> 주의 할 점
> Null을 인젝션하는 것은 금지되어있어, @Nullable annotation을 사용해야 Null 인젝션이 가능해진다.
- Scope
생성된 객체의 `lifecycle`범위이다. 주로 `perActivty`, `perFragment` 등으로 화면의 생명주기와 맞춰서 사용한다.
### Koin
Kotlin DSL로 만들어진 DI 라이브러리이다.
Dagger에 비해 구성요소가 복잡하지 않고, 러닝 커브가 낮다. 
리플랙션을 이용해 런타임에 의존성 주입을 하여서 앱 성능이 저하된다는 단점이 존재한다.
##### Koin에서 사용하는 키워드
- `module`
모듈로 선언하고 변수에 저장한다.
- `single`
앱이 실행되는 동안 계속 유지되는 싱글톤 객체를 생성한다.
- `factory`
요청할 때마다 매번 새로운 객체를 생성한다.
- `get()`
컴포넌트 내에서 알맞은 의존성을 주입 받는다.
- `viewmodel`
뷰모델을 주입할 때 사용한다.
```kt
val appModule = module {
    single { SampleRepository() }
    factory { SampleController(get()) }
}

val viewModelModule = module {
    viewModel { SampleViewModel() }
}

class SampleApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        startKoin {
            androidLogger()
            androidContext(this@SampleApplication)
            modules(appModule)
            modules(viewModelModule)
        }
    }
}
``` 
- `by inject()`
Koin에 등록된 객체를 lazy 하게 주입 받을 수 있다.
- `by viewModel()`
koin에 등록된 viewModel을 주입 받을 수 있다.
```kt
class SampleActivity : AppCompatActivity() {
    val controller: SampleController by inject()
    val viewModel: SampleViewModel by viewModel()
}
```
### Hilt
Dagger를 기반으로 만든 DI 라이브러리이다.
공식문서 참고 시 업데이트가 느리기에 최신버전과는 사용법이 다를 수 있다.
`Activity`, `Fragment`, `View`, `Service`, `BroadcastReceiver`에서 주입 받을 수 있다.
![Hilt Hierarchy](https://blog.kakaocdn.net/dn/cS5zP5/btqTlGVKsaM/rkP9GuEsvTF3R9indCykx1/img.png)
- `@HiltAndroidApp`
Application에서 사용하는 annotation으로 ApplicationClass와 ApplicationContext에서 많이 접근하기 때문에 필수적으로 추가해야한다.
> 주의 할 점
> interface와 외부 라이브러리는 Hilt가 어떻게 객체를 생성하는 지 몰라서 Inject를 해줄 수 없다.
> 이를 해결하기 위해 `Module`을 만들어 사용한다.
- `@InstallIn`
어디에서 사용하는지 알려주기 위해 사용하는 annotation이다.
![Hilt InstallIn](https://velog.velcdn.com/images%2Fhaanbink%2Fpost%2F69eeb592-c119-4e32-94d4-6edf7954587a%2Fimage.png)
![Hilt Lifecycle](https://velog.velcdn.com/images%2Fhaanbink%2Fpost%2F73bfb6f3-e5e4-497b-ab53-075a1be05c31%2F%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202022-03-16%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%202.42.19.png)
- `@Provides`
외부 라이브러리 객체를 주입 시킬 때 사용하는 annotation이다.
- `@Binds`
interface타입의 객체를 어떻게 만드는지 알려주기 위해 사용하는 annotation이다.