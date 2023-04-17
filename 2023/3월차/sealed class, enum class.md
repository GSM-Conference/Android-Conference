## Enum클래스와 Sealed클래스
---
Enum class vs Sealed class 

👉 둘다 타입을 제한적으로 사용하고자 할 때 많이 사용하게 된다.

- `Enum`에서는 특정 값을 single instance로서 하나의 객체만 제한적으로 사용할 수 있으며, 생성자의 형태도 동일해야만 한다.
- `Sealed`에서는 state를 포함하고 있는 여러개의 instance를 가질 수 있고, 생성자도 각각의 특징에 따라서 다르게 가져갈 수 있다. ⇒ 정적이 아닌 다양한 state를 사용할 수 있다.

🎁 Sealed Class
- 자기 자신이 추상 클래스이고, 자신을 상속받는 여러 서브 클래스들을 가질 수 있다. ⇒ enum클래스와 달리 상속을 지원하기 때문에, 다양한 동작 구현 가능
- 상속받는 서브 클래스의 종류를 제한할 수 있음.
- **상태가 바뀌지 않는 경우 `object`를 사용하는것을 권장**

- sealed 클래스의 서브클래스들은 반드시 같은 파일내에 선언되어야 함
    - 단, sealed클래스의 서브 클래스를 상속한 클래스들은 같은 파일내에 없어도 됨.
    - sealed클래스는 기본적으로 abstract 클래스임
    - sealed클래스는 private 생성자만 갖게 됨
    - 하위 클래스는 `class`, `data class`, `object class`로 정의할 수 있다.

```Kotlin
sealed class Color
object Red : Color()
object Green : Color()
object Blue : Color()
//object 클래스로 정의하면 singleton 패턴이 적용됨

//객체 생성
val color : Color = Red

//중첩 클래스로 선언
sealed class Color {
	object Red : Color()
	object Green : Color()
	object Blue : Color()
}

val color : Color = Color.Red
```

`Sealed class`로 부터 얻는 이점 ⇒ when을 사용할 때 Sealed class를 사용하면 else 구문을 추가하지 않아도 된다.

```Kotlin
sealed class Color {
    object Red: Color()
    object Green: Color()
}

val color : Color = Color.Red
val font = when (color) {
    is Color.Red -> {
        "Noto Sans"
    }
    is Color.Green -> {
        "Open Sans"
    }
    // No error!
		//하지만 sealed에 blue를 추가해주고 when에서 blue에 대한 정의가
//없다면 오류 

}
```

---
## 🐧 Enum Class
```Kotlin
enum class Color(val r: Int, val g: Int, val b: Int){
    RED(255,0,0), ORANGE(255, 165, 0),
    YELLOW(255,255,0), GREEN(0,255,0),
    BLUE(0,0,255)
    
    fun rgb() = ( r * 256 + g) * 256 + b
}

fun getColorName(color: Color) = when (color) {
    Color.RED -> "빨강"
    Color.ORANGE -> "주황"
    Color.YELLOW -> "노랑"
    Color.GREEN -> "초록"
    Color.BLUE -> "파랑"
}

fun main() {
    println(getColorName(Color.BLUE))
}
```
⇒ RED(255,0,0)를 RED(255,0,20)으로 변경하고 싶지만 Enum 클래스는 최초에 설정한 enum 각각에 대한 상태를 변경할 수 없다.

⇒ 상속 또한, 불가능하기 때문에 enum에 대한 서브 클래스를 생성할 수도 없다.