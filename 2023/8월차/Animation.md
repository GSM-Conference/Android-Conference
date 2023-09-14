## Animation
---
Android 개발을 하다보면 좀 더 고급진 UI를 제공하기 위해 애니메이션을 추가하여 고급스러운 앱을 만들어 내고자 한다.

이때 Animation을 쓰려고 보면 여러가지 많은 방법들이 존재한다는 걸 알게된다.

ex.ValueAnimator, AnimatorSet, ViewPropertyAnimator... 등 많은 방법중 하나를 사용하게 될 것이다.

Android는 이렇게 애니메이션 적용을 위해 Animator라는 클래스를 제공해주지만 애니메이션을 만들어 낼 수 있는 방법이 다양함에도 불구하고 대부분 비슷하게 동작하기 때문에 적재적소로 사용하기 힘들다.

<br>

언제 무엇을 사용해야 할까?

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FNf2Tn%2FbtqFZ5fwUSN%2FYaCcKW7OySKhcaulQ9jbe0%2Fimg.png)

<br>

---

### 1. 간단한 애니메이션에서는 ViewPropertyAnimator를 이용하는것이 좋다.

오직 하나의 뷰에서만 애니메이션이 동작하길 원하고, repeat, reverse, play together 등등 복합적으로 애니메이션이 동작하지 않아도 되는 경우에는 오직 ViewPropertyAnimator만을 사용해도 충분하다.

```kotlin
view.animate()
    .alpha(0.5f)
    .scaleX(0.5f)
    .scaleY(0.5f)
    .setDuration(3000)
    .withStartAction {
        // 애니메이션이 시작될 때 수행할 작업 작성
    }
    .withEndAction {
        // 애니메이션이 종료될 때 수행할 작업 작성
    }
```

위와 같은 방식으로 사용하면 해당 뷰는 현재 상태로부터 3초동안 alpha가 0.5로, scale이 0.5로 진행되게 되며

애니메이션 시작, 종료 시 필요한 동작들을 Runnable로 실행 시킬 수 있다.

이 방법의 장점을 객체를 직접 Animator obj로 인스턴스화 할 필요 없으므로, 간결한 애니메이션을 작성하고자 한다면 ViewPropertyAnimator을 선택하는 것이 바람직하다.

하지만 ViewPropertyAnimator에는 단점이 존재한다.

- 반복적인 Animation을 만들 수 없다.

- 오직 end value만을 설정 할 수 있고 start value를 설정 할 수 없다. (즉, 뷰의 현재 상태에서부터 end로만 갈 수 있다.)

- ViewPropertyAnimator에서는 오직 간단한 애니메이션만 수행 할 수 있다.

- 순차적인 애니메이션(A애니메이션 다음 B애니메이션) 혹은 복합적인 애니메이션(A, B를 동시에 실행하는 애니메이션)이 불가능하다.

<br>

---

### 2. 반복적인 애니메이션이 필요하다면 ObjectAnimator를 사용하자

하나의 뷰에서 간단한 애니메이션을 만들고자 하고 반복적으로 동작하기를 바란다면, ObjectAnimator를 사용하면 된다.

```kotlin
val anim = ObjectAnimator.ofFloat(view, "translationY", 300f)
anim.duration = 5000
anim.repeatCount = ValueAnimator.INFINITE
anim.repeatMode = ValueAnimator.REVERSE
anim.start()
```
ViewPropertyAnimator와 다르게 이것은 Repeat이라는 것을 가질 수 있기 때문에 애니메이션을 반복적으로 사용할 수 있는 장점이 있다.

 ObjectAnimator의 특별한 기능이라고 한다면 XML을 이용하여 애니메이션을 만들 수 있는 장점이 있다.

하지만 해당 ObjectAnimator도 결국 기본적인 애니메이션 속성만 가지고 있을 뿐,

순차적인 애니메이션(A애니메이션 다음 B애니메이션) 혹은 복합적인 애니메이션(A, B를 동시에 실행하는 애니메이션)이 불가능하기에 간단한 애니메이션에서만 사용 할 수 있다.

<br>

---

### 3. 복합적인 애니메이션 사용을 위해서는 PropertyValueHolders를 사용하자
 

하나의 뷰에서 하나 이상의 애니메이션을 동시에 실행시키고자 한다면 PropertyValueHolder을 사용하면 된다.

이 PropertyValuesHolder는 설정 후 ObjectAnimator에 아래와 같이 사용할 수 있다.

```kotlin
val rotationX = PropertyValuesHolder.ofFloat(View.ROTATION_X, 0f, 3600f)
val textColor = PropertyValuesHolder.ofInt(
    "textColor",
    Color.parseColor("#FFFF0000"),
    Color.parseColor("#FF0000FF")
)
textColor.setEvaluator(ArgbEvaluator())

val animator = ObjectAnimator.ofPropertyValuesHolder(view, rotationX, textColor)
animator.duration = 5000
animator.interpolator = AccelerateDecelerateInterpolator()
animator.repeatCount = ValueAnimator.INFINITE
animator.repeatMode = ValueAnimator.REVERSE
animator.start()
```

PropertyValuesHolder는 기본적인 애니메이션 속성들뿐만 아니라 리플렉션 (textColor, backgroundColor 등등)을 통해 모든 뷰 객체 속성을 사용 할 수 있다.

PropertyValuesHolder도 마찬가지로 XML을 통해 애니메이션 생성이 가능하다는 장점이 있다.

<br>

---

### 4. 복합 애니메이션 및 순차 애니메이션을 위해서는 AnimatorSet을 사용하자

하나의 뷰가 아닌 여러개의 뷰가 존재할 때, 모든 뷰가 동시에 애니메이션이 동작해야할 때

(뷰 A는 오른쪽으로, 뷰 B는 왼쪽으로) 

혹은 하나의 뷰에서 순차적으로 여러개의 애니메이션이 동작해야할 때

(뷰 A가 오른쪽으로 3초 갔다가 왼쪽으로 2초 갔다가 아래로 5초)

이러한 복합적인 요소들이 상호작용할 때 AnimatorSet을 사용한다.

```kotlin
val rotationX = PropertyValuesHolder.ofFloat(View.ROTATION_X, 0f, 3600f)
val textColorX = PropertyValuesHolder.ofInt(
    "textColor",
    Color.parseColor("#FFFF0000"),
    Color.parseColor("#FF0000FF")
)
textColorX.setEvaluator(ArgbEvaluator())

val animator1 = ObjectAnimator.ofPropertyValuesHolder(view, rotationX, textColorX)
animator1.duration = 5000
animator1.interpolator = AccelerateDecelerateInterpolator()

val rotationY = PropertyValuesHolder.ofFloat(View.ROTATION_Y, 0f, 3600f)
val textColorY = PropertyValuesHolder.ofInt(
    "textColor",
    Color.parseColor("#FF0000FF"),
    Color.parseColor("#FFFF0000")
)
textColorY.setEvaluator(ArgbEvaluator())

val animator2 = ObjectAnimator.ofPropertyValuesHolder(view, rotationY, textColorY)
animator2.duration = 5000
animator2.interpolator = AccelerateDecelerateInterpolator()

val animatorSet = AnimatorSet()
animatorSet.play(animator2).after(animator1)
animatorSet.start()
```

ObjectAnimator, PropertyValuesHolder 및 AnimatorSet 세가지 객체의 조합으로 다양하고 복합적인 애니메이션을 구현할 수 있다.

하지만 AnimatorSet의 한 가지 단점은 반복적인 애니메이션이 불가능하는 단점이 있다.

 

** 마찬가지로 animatorSet도 XML 구현이 가능하다. **

<br>

---

### 5. 애니메이션의 커스터마이징을 극대화 시키기 위해서는 ValueAnimator을 사용한다.
 

뷰 또는 객체의 속성이 아닌것에 애니메이션을 적용하려는 경우

가장 기본적인 애니메이터 클래스인 ValueAnimator를 사용해야한다.

아래에는 ValueAnimator을 커스텀을 잘 나타낸 코드를 보여주고있다.

ValueAnimator와 AnimatorSet를 혼합하여 사용하면 가장 화려한 애니메이션을 만들 수 있다.

하지만 그만큼 cost가 많이 드는 것은 감안해야 한다.

```kotlin
import android.animation.ArgbEvaluator
import android.animation.TimeAnimator
import android.animation.ValueAnimator
import android.app.Activity
import android.graphics.Color
import android.graphics.drawable.GradientDrawable
import android.view.View

fun startAnimation(viewId: Int, activity: Activity) {
    val start = Color.parseColor("#FDB72B")
    val mid = Color.parseColor("#88FDB72B")
    val end = Color.TRANSPARENT

    val evaluator = ArgbEvaluator()
    val preloader = activity.findViewById<View>(R.id.gradientPreloaderView)
    preloader.visibility = View.VISIBLE
    val gradient = preloader.background as? GradientDrawable

    val animator = TimeAnimator.ofFloat(0.0f, 1.0f)
    animator.duration = 500
    animator.repeatCount = ValueAnimator.INFINITE
    animator.repeatMode = ValueAnimator.REVERSE
    animator.addUpdateListener { valueAnimator ->
        val fraction = valueAnimator.animatedFraction
        val newStart = evaluator.evaluate(fraction, start, end) as Int
        val newMid = evaluator.evaluate(fraction, mid, start) as Int
        val newEnd = evaluator.evaluate(fraction, end, mid) as Int
        val newArray = intArrayOf(newStart, newMid, newEnd)
        gradient?.colors = newArray
    }

    animator.start()
}

fun stopAnimation(viewId: Int, activity: Activity) {
    activity.findViewById<View>(viewId).animate().alpha(0f).setDuration(125).start()
}

```