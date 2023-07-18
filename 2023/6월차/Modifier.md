## Modifier란
Modifier란 레이아웃을 변경하고 이벤트 리스너 추가 등을 가능하게 만들어주는 코틀린 객체이다. 이건 사용한 간단한 에제들을 몇 가지 확인해본다.

<br>
<br>

 

먼저 가로, 세로를 각각 정의하는 Modifier부터 확인해본다. 가로 길이를 설정하려면 Modifier.width(width: dp)를 사용하고 세로 길이를 설정하려면 Modifier.height(height:dp)를 사용한다.

```kotlin
class LayoutModifierActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            Box(
                modifier = Modifier.width(200.dp)
                    .fillMaxHeight()
                    .background(Color.Blue)
            )
        }
    }
}
```

 
가로로 200dp 길이, 세로로 꽉찬 파란색의 박스를 화면에 표시하라는 코드이다.
이렇게 하면 아래와 같은 화면이 나타난다.

![width](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdymOgg%2FbtrGS481ZBD%2F8yaATly2K0PlkiWecv5GG1%2Fimg.png)

세로는 Modifier.height()를 쓰면 된다. 그리고 fillMaxHeight()를 fillMaxWidth()로 바꾼다. 만약 fillMaxHeight()를 그대로 사용하면 어떻게 되는지 한번 확인해보자.

```kotlin
class LayoutModifierActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            Box(
                modifier = Modifier.height(200.dp)
                    .fillMaxWidth()
                    .background(Color.Blue)
            )
        }
    }
}
```


![height](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbjM1NJ%2FbtrGUrWZanF%2FYQP9mPOzVVPn77bmlLwq9K%2Fimg.png)

그리고 fillMaxWidth(), fillMaxHeight() 안에 float 값을 넣어서 얼마나 표시할지에 대한 비율을 설정할 수도 있다.

```kotlin

class LayoutModifierActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            LazyColumn {
                item { SamePaddingComponent() }
                item { CustomPaddingComponent() }
                item { OffsetComponent() }
                item { AspectRatioComponent() }
            }
        }
    }
}

@Composable
fun SamePaddingComponent() {
    Surface(color = Color.LightGray) {
        Text(
            text = "상하좌우 모두 16 padding인 텍스트뷰",
            modifier = Modifier.padding(16.dp),
            style = TextStyle(
                fontSize = 20.sp,
                fontFamily = FontFamily.Serif
            )
        )
    }
}

@Composable
fun CustomPaddingComponent() {
    Surface(color = Color.Cyan) {
        Text(
            text = "This text has 32dp start padding, 4dp end padding, 32dp top padding & 0dp bottom padding padding in each direction",
            modifier = Modifier.padding(
                start = 32.dp,
                end = 4.dp,
                top = 32.dp,
                bottom = 0.dp
            ),
            style = TextStyle(
                fontSize = 20.sp,
                fontFamily = FontFamily.Serif
            )
        )
    }
}

@Composable
fun OffsetComponent() {
    Surface(
        color = Color.Green,
        modifier = Modifier.offset(x = 8.dp, y = 8.dp)
    ) {
        Text(
            text = "This text is using an offset of 8 dp instead of padding. Padding also ends up" +
                    " modifying the size of the layout. Using offset instead ensures that the " +
                    "size of the layout retains its size.",
            style = TextStyle(
                fontSize = 20.sp
            )
        )
    }
}

@Composable
fun AspectRatioComponent() {
    Surface(
        color = Color.LightGray,
        modifier = Modifier.aspectRatio(16 / 9f).padding(top = 16.dp)
    ) {
        Text(
            text = "This text is wrapped in a layout that has a fixed aspect ratio of 16/9",
            style = TextStyle(
                fontSize = 20.sp,
                fontFamily = FontFamily.Serif
            ),
            modifier = Modifier.padding(16.dp)
        )
    }
}
```

위의 코드를 보면 3가지의 modifier를 Surface() 안에 사용한 것을 볼 수 있다.

- Modifier.padding()
- Modifier.offset()
- Modifier.aspectRatio()

Modifier.padding()은 요소 주변에 공간을 만든다. XML 방식으로 화면을 만들때 사용했던 방식과 같다.

Modifier.offset은 원래 위치를 기준으로 레이아웃을 설정한다. LTR, RTL 중 어떤 컨텍스트냐에 따라 이동하는 방향이 달라지지만 absoluteOffset을 사용하면 그런 거 상관없이 항상 오른쪽으로 이동시킨다.

Modifier.aspectRatio()는 컴포넌트의 크기 조정을 비율로 설정한다.

![example](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcsGl7d%2FbtrGT0EsOL3%2FKyDVtTNeW0Bzt1REeeLeu0%2Fimg.png)

그리고 Compose에는 마진이란 개념이 없다. 위 사진의 녹색 이미지를 보면 위아래로 일정 공간이 있지만 코드를 보면 마진은 사용하지 않았다. 그렇다면 Compose에서 마진을 구현하려면 어떻게 해야하나? 방법은 아래와 같다.

```Kotlin
val shape = CircleShape
Text(
    text = "Text 1",
    style = TextStyle(
        color = Color.White,
        fontWeight = FontWeight.Bold,
        textAlign = TextAlign.Center),
    modifier = Modifier.fillMaxWidth()
        .padding(16.dp) //1번 Padding
        .border(2.dp, MaterialTheme.colors.secondary, shape)
        .background(MaterialTheme.colors.primary, shape)
        .padding(16.dp) //2번 Padding
)
```

이런식으로 적용한다 약간 이해하기 쉽게? 설명해 보자면 `첫번째 padding`은 구성요소랑 테두리 사이에 공백을 추가하는 것이다. 그런 다음에 배경과 테두리가 정의 된다. 두번째 Padding은 테두리와 텍스트 사이의 공간을 추가하기 위해서 새 항목이 설정되는 것이다.

위의 코드의 결과는 다음과 같다

![margin_example](https://i.stack.imgur.com/oqLvk.png)

조금 더 이해하기 쉬운 코드를 하나 찾았는데 코드는 아래와 같다

```Kotlin
@Composable
fun PaddingExample() {
    Text(
        text = "Hello World!",
        color = Color.White,
        modifier = Modifier
            .padding(8.dp) // margin
            .border(2.dp, Color.White) // outer border
            .padding(8.dp) // space between the borders
            .border(2.dp, Color.Green) // inner border
            .padding(8.dp) // padding
    )
}
```

![margin_example1](https://i.stack.imgur.com/Isj1X.png)


또한 위의 방식이 아니라 Spacer라는 걸 이용하여 마진을 구현할 수도 있다고 한다

```kotlin
Spacer(modifier = Modifier.width(10.dp))
```

위의 Spacer를 이용하면

```kotlin
Text(
    text = stringResource(id = R.string.share_your_posters),
    fontSize = 16.sp,
    color = Color.Black
)

Spacer(modifier = Modifier.width(10.dp))

Image(painter = painterResource(id = R.drawable.ic_starts), contentDescription = null)
```

이런식으로 2개의 Composable 사이에 여백을 추가하는 코드를 작성할 수 있다.