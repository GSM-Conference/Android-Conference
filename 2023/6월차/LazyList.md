**LazyList**
==========
### Compose에서 리스트를 구현하기 위해 기본적으로 Column과 Row로 구현하며, 화면에만 보이는 compose만 그리도록 하는 **lazy list를 이용하여 기존의 RecyclerView를 대체한다.**

```kt
@Composable
fun SimpleList() {
    Column {
        repeat(100) {
            Text("Item $it")
        }
    }
}
```
단순하게 100개의 아이템을 표현하기 위해선 위와 같이 해줄 수 있다.

하지만, 첫 번째로 화면에 보이지 않는 모든 compose를 생성하기 때문에 비효율 적이고, 두 번째로 Column은 기본적으로 스크롤을 지원하지 않기 때문에 실제로 아래의 아이템을 볼 수 없다.   
먼저 두 번째 문제를 해결하기 위해서는 스크롤이 가능하도록 Modifier에 추가적인 작업을 해야한다.
```kt
@Composable
fun SimpleList1() {
    // 스크롤의 position의 상태를 저장.
    val scrollState = rememberScrollState()

    Column(Modifier.verticalScroll(scrollState)) {
        repeat(100) {
            Text("Item $it")
        }
    }
}
```
이제 스크롤은 가능하지만 보지 않을 수도 있는 **모든 compose**를 생성하는 문제가 여전히 남아있다.

## **LazyList**
위와같은 Column이나 Row를 사용하는 경우는 단순히 composable을 배치하기 위해서 일뿐 실제 recyclerView를 대체하기 위해서 LazyColumn, LazyRow를 사용한다.
```kt
@Composable
fun SimpleList() {
    // 스크롤의 position의 상태를 저장.
    val scrollState = rememberLazyListState()

    LazyColumn(state = scrollState) {
        items(100) {
            Text("Item $it")
        }
    }
}
```
LazyColumn은 DSL의 형태로 list의 item을 넘겨받는다. 따라서 items에 list의 크기나, Array, List를 넘겨서 데이터를 그리도록 한다.   
화면에 보이는 만큼만 compose가 실행되면서 화면에 그려지기 때문에 효율적이지만 recyclerView가 View를 재활용하는 것처럼 compose를 재활용하지 않는다.
```kt
// item list를 넘기는 경우
@Composable
fun SimpleList(itemList: List<String>) {
    // 스크롤의 position의 상태를 저장.
    val scrollState = rememberLazyListState()

    LazyColumn(state = scrollState) {
        items(items = itemList){
            Text("Item $it")
        }
    }
}
```
일반적인 Composable의 경우 param으로 사용되는 content는 @Composable block을 넘겨받는다. 하지만 Lazy Component들은 DSL로 구성된 LaztListScope()를 제공한다. 개발자가 block 내부에 표현하고자 하는 list를 item 형태로 넣어주고 나면 layout이나 스크롤에 따라서 이를 그려야 할지는 lazy component가 결정한다.
```kt
LazyColumn(modifier = Modifier.fillMaxWidth().fillMaxHeight(),
        contentPadding = PaddingValues(horizontal = 20.dp, vertical = 10.dp)
    ) {
    ...
 }
 ```
 리스트 전체에 padding을 주기 위해서는 contentPadding속성을 이용한다.   
 만약 상, 하, 좌, 우의 속성을 모두 다르게 주려면 PaddingValues(top=..., bottom...)형태의 함수를 사용할 수 있다.

 RecycleView에서는 아이템의 추가, 삭제, 이동시에 기본적인 애니메이션이 동작한다. 하지만 Compose에서는 지원하지 않는다.