## Dialog 란
사용자가 결정을 내리거나 추가 정보를 입력하는 메시지를 표시하는 작은 창을 Dialog라고 한다. 사용자가 다음으로 계속 진행하기 전에 조치를 취하는 이벤트에 사용된다.

공식문서에서는 Base Class인 Dialog에 직접 인스턴스화 하는 것은 삼가야 한다고 소개하고 있으며 Sub Class인 AlertDialog, DatePickerDialog, TimePickerDialog를 사용하는 걸 권장하고 있다.

---

## 제목과 콘텐츠 영역 <-- ??
![setTitle](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbj09nh%2Fbtr9AjQ9b4p%2FesFzjrkTxwIOVFg8AlBwik%2Fimg.png)

![setArea](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbFCqXN%2Fbtr9sjdsAaL%2FC6J2oW5f31ATz7YwsfRpek%2Fimg.png)

```Kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        val Dialog: Button = findViewById(R.id.btndialog)

        Dialog.setOnClickListener {
            AlertDialog.Builder(this)
                .setTitle("제목 -> setTitle")
                .setMessage("설명 -> setMessage")
                .create()
                .show()
        }
    }
}

```

AlertDialog 클래스를 사용하면 여러가지 대화상자 디자인을 빌드할 수 있고, 두 번째 처럼 세 가지 영역이 있다.
- 제목: 선택사항이며 콘텐츠 영역에 상세한 메시지, 목록 또는 맞춤 레이아웃이 채워져 있는 경우에만 사용해야 한다.  단순한 메시지 또는 질문을 나타내야 하는 경우 제목은 없어도 된다

- 콘텐츠 영역: 메시지, 목록 또는 다른 맞춤 레이아웃을 표시할 수 있다

- 작업 버튼: 대화상자 하나에 작업 버튼이 세 개를 초과할 수 없다

---

## 버튼 추가
![setMessage](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fcz4FQP%2Fbtr9Ox8XzF2%2FcxP31fqDAP8utmLMTwsZP1%2Fimg.png)

```Kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        val Dialog: Button = findViewById(R.id.btndialog)

        Dialog.setOnClickListener {
            AlertDialog.Builder(this)
                .setTitle("제목 -> setTitle")
                .setMessage("설명 -> setMessage")
                .setPositiveButton("ok", object : DialogInterface.OnClickListener {
                    override fun onClick(dialog: DialogInterface, which: Int) {

                    }
                })
                .setNegativeButton("cancel", object : DialogInterface.OnClickListener {
                    override fun onClick(dialog: DialogInterface, which: Int) {

                    }
                })
                .setNeutralButton("neutral", object : DialogInterface.OnClickListener {
                    override fun onClick(dialog: DialogInterface, which: Int) {

                    }
                })
                .create()
                .show()
        }
    }
}
```

setButton에는 버튼의 제목이 필요하고, 사용자가 버튼을 눌렀을 때 실행할 작업을 정의하는 DialogInterface, OnClickListener가 필요하다.
- setPositiveButton : 작업을 수락하고 게속 진행하는 데 사용한다.

- setNegativeButton : 작업을 취소하는 데 사용한다.

- setNeutralButton : 사용자가 작업을 게속하고 싶지 않지만 취소하고 싶지도 않은 경우에 사용한다.

---

## 리스트
![list](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FoTF0m%2Fbtr94jPNFY6%2F3nGH7VW4JffL0bJHkmzkFk%2Fimg.png)

```Kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        val Dialog: Button = findViewById(R.id.btndialog)
        val array = arrayOf("1", "2", "3")

        Dialog.setOnClickListener {
            AlertDialog.Builder(this)
                .setTitle("list")
                .setItems(array, object : DialogInterface.OnClickListener {
                    override fun onClick(dialog: DialogInterface, which: Int) {
                        val currentItem = array[which]
                        Log.d("list", "currentItem : $currentItem")
                    }
                })
                .show()
        }
    }
}
```

목록은 대화상자의 콘텐츠 영역에 나타나므로 대화상자는 메시지와 목록을 둘 다 표시할 수는 없다. setTitle로 제목을 설정하고 setItems를 호출하고 배열을 전달해 목록의 항목을 지정한다. 동적 데이터는 setAdapter를 사용하여 목록을 지정할 수 있으며 Loader를 사용하면 콘텐츠가 비동기식으로 로드된다.

---
## 라디오박스(단일 선택)
![radioBox](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FPm20j%2Fbtr94j3p9h1%2FXwkM86BNGO1zDnaduPa81K%2Fimg.png)

```Kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        val Dialog: Button = findViewById(R.id.btndialog)
        val array = arrayOf("1", "2", "3")
        var checkedItemPosition = 0

        Dialog.setOnClickListener {
            AlertDialog.Builder(this)
                .setTitle("radio")
                .setSingleChoiceItems(array, checkedItemPosition, object : DialogInterface.OnClickListener {
                    override fun onClick(dialog: DialogInterface, which: Int) {
                        checkedItemPosition = which
                    }
                })
                .setPositiveButton("ok", object : DialogInterface.OnClickListener {
                    override fun onClick(dialog: DialogInterface?, which: Int) {

                    }
                })
                .show()
        }
    }
}
```

라디오박스를 추가하려면 setSingleChoiceItems를 사용한다. 선택한 항목을 변수로 저장해 사용자의 선택을 유지시킬 수 있다.

---
## 체크박스(다중 선택)
![checkBox](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FJuwEq%2Fbtr901QuZJo%2FzuYR1E2hJnTx0Q1CZfYqBK%2Fimg.png)

```Kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        val Dialog: Button = findViewById(R.id.btndialog)
        val array = arrayOf("1", "2", "3")
        val checkedArray = booleanArrayOf(false, false, false)

        Dialog.setOnClickListener {
            AlertDialog.Builder(this)
                .setTitle("checkbox")
                .setMultiChoiceItems(array, checkedArray, object : DialogInterface.OnMultiChoiceClickListener {
                    override fun onClick(dialog: DialogInterface, which: Int, isChecked: Boolean) {
                        checkedArray[which] = isChecked
                    }
                })
                .setPositiveButton("ok", object : DialogInterface.OnClickListener {
                    override fun onClick(dialog: DialogInterface?, which: Int) {

                    }
                })
                .show()
        }
    }
}
```

체크박스를 추가하려면 setMultiChoiceItems를 사용한다. 선택한 항목을 ArrayList에 저장해 사용자의 선택을 유지시킬 수 있다.

---

## 컨퍼런스 이후 추가 조사

<br>

### 커스텀 다이얼로그?

<br>

커스텀 다이얼로그란 위에서 말한 다이얼로그의 디폴트 디자인을 커스텀 할 수 있게 만드는 기능이다. 커스텀 다이얼로그의 사용 방법은 간단하다.

xml코드 짜기 -> 코드(Kotlin or Java)연결 -> 띄워서 사용

<br>

### 토스트??

<br>

토스트는 토스트 팝업을 띄워주는 기능을 하는 클래스인데 보통 사용자에게 정보를 알려주는 목적으로 사용된다. 나타났다가 자동으로 사라지는 메세지들이 보통 토스트이다.

ex. ![toast](https://t1.daumcdn.net/cfile/tistory/9924DB3359A409B132)

