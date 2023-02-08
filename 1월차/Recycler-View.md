# **RecyclerView 란??**
#### 대량의 데이터set을 효율적으로 표시할 수 있는 View
#### 예를 들어 배달의 민족의 가게들처럼 특정 객체들을 리스트 형태로 나열하는데 활용할 수 있다.

<br>

#### 기본적으로 제공되는 ListView가 있지만, RecyclerView는 커스텀과 효율성 측면에서 장점이 더 많은 View라고 볼 수 있다.

<br>

## **RecyclerView의 재활용성**
![리사이클러뷰](https://miro.medium.com/max/902/0*Xwx342BsafQoro_-)


#### 위의 그림을 보면, ListView와 다르게 RecylcerView는 아래로 스크롤 할 때, 맨 위의 객체를 맨 아래로 이동시켜 재활용하는 것을 확인할 수 있는데 물론, 객체 자체만을 재활용하는 것으로 객체에 담겨지는 데이터는 새로 갱신하여 사용합니다.

<br>

#### 만약 맨 처음 화면에 보여질 View가 10개라면? 실제 데이터가 수천, 수백개라도 오로지 10개의 View 객체만 생성하여 재사용하는 것이다!

<br>

#### 반면에, ListView는 아래로 스크롤 할 때, 맨 위의 객체를 삭제하고 아랫 부분에 객체를 새로 생성하여 사용하는데 만약 스크롤을 위아래로 반복한다면 수백, 수천개의 View 객체가 생성/삭제가 반복된다.

<br>
 
## **ViewHolder**

#### 위에서 설명했듯이, 스크롤을 내릴 때, 사라지게될 맨 위의 객체를 맨 아래로 이동하여 재활용한다. 즉, 10개의 View 객체만 계속해서 위에서 아래로 이동하면서 재사용되는 것이다. 10개의 View 객체들은 언제든지 텍스트나 이미지를 변경할 수 있는데 따라서 맨 처음 10개의 View객체를 기억하고 있을(홀딩)객체가 필요한데 이것이 바로 **ViewHolder** 이다!

<br>

### RecyclerView 예시
```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    xmlns:tools="http://schemas.android.com/tools"
    android:paddingHorizontal="20dp"
    android:paddingVertical="10dp"
    android:background="@color/purple_500"
    xmlns:app="http://schemas.android.com/apk/res-auto">



    <TextView
        android:id="@+id/tv_rv_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/name"
        app:layout_constraintStart_toStartOf="parent"
        android:layout_marginStart="20dp"
        android:textSize="24sp"
        android:textColor="@color/white"
        app:layout_constraintTop_toTopOf="@id/tv_rv_name"/>

    <TextView
        android:id="@+id/tv_rv_order"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/order"
        android:layout_marginTop="10dp"
        android:layout_marginStart="20dp"
        android:textSize="18sp"
        app:layout_constraintStart_toStartOf="parent"
        android:textColor="@color/white"
        app:layout_constraintTop_toBottomOf="@id/tv_rv_name"/>


</androidx.constraintlayout.widget.ConstraintLayout>
```

### TextView 2개로 이루어진 Layout을 하나 만든다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rv_recyclerView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        app:layoutManager="androidx.recyclerview.widget.LinearLayoutManager"
        app:layout_constraintHorizontal_bias="0.0"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        tools:listitem="@layout/item_recycler" />


</androidx.constraintlayout.widget.ConstraintLayout>
```

### Linear형식으로 세로로 나열된 리스트를 만들어준다.

<br>

## Adapter와 ViewHolder 만들기


<br>

## Adapter

```Kotlin
package com.example.androidconference

import android.content.Context
import android.util.Log
import android.view.ViewGroup
import androidx.recyclerview.widget.RecyclerView
import android.view.LayoutInflater
import android.view.View
import android.widget.TextView
import com.example.androidconference.Data
import androidx.recyclerview.widget.RecyclerView.ViewHolder

class CustomAdapter(private val context: Context, private var items: List<Data>):RecyclerView.Adapter<CustomAdapter.ViewHolder>(){

    var datas = mutableListOf<Data>()

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder{
        val view = LayoutInflater.from(context).inflate(R.layout.item_recycler,parent,false)

        for(cnt in 0..itemCount){
            val a = cnt+1
            items[cnt].order = a.toString()
        }

        return ViewHolder(view)
    }

    override fun getItemCount(): Int = datas.size
    
    override fun onBindViewHolder(holder: ViewHolder, position: Int){
        holder.bind(datas[position])


    }
    
    inner class ViewHolder(view: View) : RecyclerView.ViewHolder(view){
        private val txtName: TextView = itemView.findViewById(R.id.tv_rv_name)
        private val txtOrder: TextView = itemView.findViewById(R.id.tv_rv_order)

        private var textOrder = txtOrder.text.toString()
        
        fun bind(item:Data){
            txtName.text = item.name
            txtOrder.text = textOrder.replace("n", item.order)
        }
    }

}
```

## Data

```Kotlin
package com.example.androidconference

data class Data (var name : String, var order: String)
```

## Adapter와 RecyclerView 연결

<br>

## MainActivity
```Kotlin
package com.example.androidconference

import com.example.androidconference.databinding.ActivityMainBinding
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.util.Log

class MainActivity : AppCompatActivity() {
    lateinit var binding: ActivityMainBinding

    lateinit var customAdapter: CustomAdapter

    val datas = mutableListOf<Data>()

    override fun onCreate(savedInstanceState: Bundle?){
        super.onCreate(savedInstanceState)
        setContentView(binding.root)
        binding = ActivityMainBinding.inflate(layoutInflater)
        Log.d("AAAADAS", "asdsa")
        initRecycler()
    }

    private val infoList = listOf(
        Data("채종인", "n") ,
        Data("채종인", "n") ,
        Data("채종인", "n")
    )

    private fun initRecycler(){
        customAdapter = CustomAdapter(this, infoList)
        binding.rvRecyclerView.adapter = customAdapter

        datas.apply{
            for(no in 1..100){
                val order = "${no}"
                customAdapter.datas = datas
                customAdapter.notifyDataSetChanged()
            }
        }
    }

}
```

<br>
<br>

# **Result**
