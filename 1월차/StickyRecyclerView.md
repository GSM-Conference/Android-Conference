Sticky Header Recyclerview
==========================
## Sticky Header
#### Sticky Header란 사용자가 페이지를 아래로 스크롤 할 때, 리스트 중 하나의 아이템을 상단에 고정시키는 것을 의미한다.   
## Sticky Header Recyclerview
리사이클러뷰 내부의 데이터 리스트를 위한 Sticky Header가 필요한 여러 상황이 있다.   
그리고 당연하게, 안드로이드는 이걸 시행하기 위한 UI컴포넌트를 가지고 있지 않다.   
이 기능을 달성하기 위해 여러 외부 라이브러리들이 있지만 외부 라이브러리를 사용하는것은 항상 위험이 있다.   
우리가 사용할 라이브러리가 미래의 안드로이드 버전을 위해 업데이트가 될 것인지에 대한 의심이 있기 때문에 외부 라이브러리를 사용하지 않고 이 기능을 구현하는 간단한 방법은 바로 커스텀 RecyclerView ItemDecoration을 작성하고, ```onDrawOver(canvas: Canvas, parent: RecyclerView, state: State)```함수를 오버라이드 하여 사용하는 방법이다.