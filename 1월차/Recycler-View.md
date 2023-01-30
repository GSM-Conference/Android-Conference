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
![리사이클러뷰 예시](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcuPzPK%2Fbtq6Vh2hIvC%2FwSkSEb8RK47PUZPLJC85Q0%2Fimg.png)


<br>
<br>
<br>

``` Kotlin
public static class LocationViewHolder extends RecyclerView.ViewHolder {
    CardView cardView;
    TextView address;
    
    public LocationViewHolder(@NonNull View itemView) {
        super(itemView);

        cardView = itemView.findViewById(R.id.cardView);
        address = itemView.findViewById(R.id.address);
    }
} 
```

#### 위 그림은 주소를 RecyclerView로 구현한 모습입니다. 각 TextView는 address를 저장하고 있다.

#### 즉, address 변수에 다른 주소를 저장하며 모든 주소 목록을 나열할 건데 오직 10개 내외의 ViewHolder 객체를 만들어서 계속 재사용할 것이다. 위 코드를 보면 **LocationViewHolder**가 TextView를 가지고 있음을 나타낸다.

<br>

## **Adapter와 LayoutManager**
#### RecyclerView를 사용하려면 2가지가 필수이다.

<br>

#### **Adapter**는 수백개의 주소 이름이 담긴 List를 RecyclerView에 바인딩(Binding)시켜주기 위한 사전 작업이 이루어지는 객체이다. (직접 작성해서 RecyclerView에 적용시킨다.)
#### **LayoutManager**는 많은 역할을 하지만, 여기서는 간단하게 스크롤을 위아래로 할지, 좌우로 할지 결정하는 역할만 고려한다.
```Kotlin
@Override
protected void onCreate(@Nullable Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.location_view);
    
    recyclerView = findViewById(R.id.recyclerView);
    recyclerView.setLayoutManager(new LinearLayoutManager(this));
    
    locationAdapter = new LocationAdapter(this, this, locationList);
    recyclerView.setAdapter(locationAdapter);
}
````


<br>
<br>
<br>

## **LocationAdapter**
#### Adapter의 코드를 먼저 확인해보면 
```Kotlin
public class LocationAdapter extends RecyclerView.Adapter<LocationAdapter.LocationViewHolder> {
    Context context;
    Activity activity;
    List<Location> locationList;

    LocationAdapter(Context context, Activity activity, ArrayList locationList) {
        this.context = context;
        this.activity = activity;
        this.locationList = locationList;
    }

    @NonNull
    @Override
    public LocationAdapter.LocationViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        LayoutInflater layoutInflater = LayoutInflater.from(parent.getContext());
        View view = layoutInflater.inflate(R.layout.location_item, parent, false);
        return new LocationAdapter.LocationViewHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull LocationAdapter.LocationViewHolder holder, int position) {
        Location location = locationList.get(position);
        String address = location.getStreetAddress();
        holder.address.setText(address);
    }

    @Override
    public int getItemCount() {
        return locationList.size();
    }

    public static class LocationViewHolder extends RecyclerView.ViewHolder {

        CardView cardView;
        TextView address;

        public LocationViewHolder(@NonNull View itemView) {
            super(itemView);

            cardView = itemView.findViewById(R.id.cardView);
            address = itemView.findViewById(R.id.address);
        }
    }
}
````

<br>

#### 먼저 **LocationAdapter**의 생성자로 주소 데이터가 담겨있는 **LocationList**를 받는데 그 후 **RecyclerView.Adapter**를 상속받는데 이때, 제네릭 타입으로는 직접 생성한 **LocationViewHolder**를 넣어주면된다.

<br>
<br>

## **getltemCount**
#### Adapter생성시, 가장 먼저 호출되는 함수인데 여기선 우리가 뿌려줄 <u>**데이터의 전체 길이를 리턴**</u>한다. 만약 이를 0으로 retrun하면 Adapter는 보여줄 데이터가 없다고 판단하는데 그래서 데이터베이스나 List에 데이터가 있더라도 View에 표시되지 않습니다.
```Kotlin
@Override
public int getItemCount() {
    return locationList.size();
}
````

<br>
<br>

## **onCreateViewHolder**
#### **getltemCount** 다음으로 호출되는 함수이다. 이름에서 알 수 있듯이 ViewHolder가 생성되는 함수로 ViewHolder객체를 만들어주면 된다.

<br>

```Kotlin
@NonNull
@Override
public LocationAdapter.LocationViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
    LayoutInflater layoutInflater = LayoutInflater.from(parent.getContext());
    View view = layoutInflater.inflate(R.layout.location_item, parent, false);
    return new LocationAdapter.LocationViewHolder(view);
}
````
#### 맨 위에서 언급했듯이 전체 리스트 목록이 딱 10개라면, 위 아래 버퍼를 생각해서 13~15개 정도 여유있게 View 객체가 생성된다. 정확하게 말하자면 **View 객체를 담고 있는 ViewHolder가 생성**되는 것인데 그래서 **onCreateViewHolder**함수는 딱 13~15번 정도만 호출되고 더 이상 호출되지 않는다.

<br>

#### **return**값을 보면 **LocationViewHolder**의 생성자에 **View**객체를 넘겨주는데, 이 객체는 위의 그림에서 본 한 개의 주소 목록이 디자인 되어있는 레이아웃이다. 즉, ViewHolder는 래이아웃을 인자로 받아서 기억하고 있는 것이다. 이제 계속해서 재사용되는 **ViewHolder(레이아웃)** 들에 데이터를 바인딩(Binding)하는 작업만 남아있다.

<br>
<br>

## **onBindViewHolder**
#### 지금까지 생성된 **ViewHolder에 데이터를 바인딩**해주는 함수이다.
#### 예를 들어, 데이터가 스크롤 되어서 맨 위에 있던 ViewHolder 객체가 맨 아래로 이동한다면, 그 레이아웃은 재활용하되, 데이터는 새롭게 바인딩되는 것이다.
```Kotlin
@Override
public void onBindViewHolder(@NonNull LocationAdapter.LocationViewHolder holder, int position) {
    Location location = locationList.get(position);
    String address = location.getStreetAddress();
}
```
#### 이 때, 새로 보여질 데이터의 인덱스는 **position**이라는 이름으로 사용 가능하다. 즉,아래에서 새롭게 올라오는 데이터가 리스트의 20번째 데이터라면 position에는 20이 들어오게 된다.

<br>

#### 바로 위에서 언급한 **onCreateViewHolder**는 **ViewHolder객체**를 만들기 위해 13~15번만 호출된다. 하지만, **onBindViewHolder**는 스크롤을 통해 데이터가 새롭게 바인딩될 때마다 호출된다. 스크롤을 무한정 돌린다면,
**onBindViewHolder**도 무한정 호출되는것이다.
####  다만, <u>**우리가 실제 사용하는 View객체는 오직 13~15개만 있는것이다.**</u>

<br>
<br>
<br>
<br>

# **Result**
### **RecyclerView**에 많은 객체를 생성했을 때의 결과는 다음과 같다.
### 실제로는 사진 아래 더 많은 데이터가 많은데 위에서 예시로 든 숫자들과는 조금 다르지만, 모바일 화면과 한 개의 데이터가 차지하는 공간에 따라서 처음에 생성되는 ViewHolder의 개수는 얼마든지 바뀔 수 있다!!!

<br>

![Result](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcQY1wu%2Fbtq6Ub9bLVg%2FZ7nuasXCNzdukRPdbHAZ2K%2Fimg.png)