# diffUtil
### diffUtil이란?

oldList와 newList 차이를 계산하고 oldList를 newList로 변환하는 업데이트 작업 목록을 출력할 수 있는 유틸성 클래스이다.

업데이트 작업 목록은 newList가 Insert, Remove, Update 모두 포함된다.

---
### diffUtil의 사용법
diffUtil은 difference algorithm 을 사용하여 하나의 목록을 다른 목록으로 변환하기 위한 최소 업데이트수를 계산한다.

우선 아래와 같이 diffUtil.Callback 을 상속받는 DiffCallback 클래스를 만들고 비교할 대상을 지정해 준다.
 

```Kotlin
class DiffUtilCallback(
    private val oldData: List<Int>,
    private val newData: List<Int>
    ) : DiffUtil.Callback() {
    override fun areItemsTheSame(oldItemPosition: Int, newItemPosition: Int) : Boolean{
        val oldItem = oldData[oldItemPosition]
        val newItem = newData[newItemPosition]

        return if (oldItem is Board && newItem is Board) {
            oldItem.id == newItem.id
        } 
        else {
            false
        }
    }

    override fun getOldListSize(): Int = oldData.size

    override fun getNewListSize(): Int = newData.size

    override fun areContentsTheSame(oldItemPosition: Int, newItemPosition: Int): Boolean = oldData[oldItemPosition] == newData[newItemPosition]
}
```


+ 위처럼 Callback을 상속 받는 것 이외에도 ItemCallback으로 만들어 사용하는 방법이 있다. 이를 이용하면 이런식으로 간단하게 구현이 가능하다.
```Kotlin
private val mDiffCallback = object : DiffUtil.ItemCallback<Test>() {
    override fun areItemTheSame(oldItem: Test, newItem: Test) : Boolean {
        return oldItem.id == newItem.id
    }

    override fun areContentsTheSame(oldItem: Test, Test: SleepNight) : Boolean {
        return oldItem.title == newItem.title
    }
}
```

<br>
<br>
<br>

--- 

### diffUtil 메소드들

- getOldListSize : 기존에 있던 리스트의 크기 반환
- getNewListSize : 변경될 리스트의 크기 반환
- areItemsTheSame : 두 개의 아이템이 같은지 확인, 고유의 값(ID)을 통해 비교
- areContentsTheSame : 두 개의 아이템 항목이 같은지 확인
- getChangePayLoad : 변경 내용에 대한 페이로드를 가져옴

`주의 해야할 메소드`

areItemsTheSame, areContentsTheSame 메소드를 구별할 줄 알아야하는데 areItemsTHeSame은 아이템이 같은지 판단해야 하기 때문에 주로 고유의 값이나 ID, 해쉬값으로 비교를 한다. 

그리고 areItemsTheSame 메소드가 true를 반환해야 **areContentsTheSame** 메소드를 호출한다. 

당연히 아이템 자체가 다르면 아이템 내 데이터를 확인할 필요도 없고 아이템이 같더라도 데이터는 다른 값으로 세팅할 수 있어서 areContentsTheSame메소드로 확인한다. 

결론적으로 areItemsTheSame과 areContentsTheSame 메소드가 모두 true면 같은 아이템으로 판단하여 업데이트 하지 않게 된다.