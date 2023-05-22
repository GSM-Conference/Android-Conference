## ViewPager란??
---
ViewPager??
- ViewPager는 콘텐츠가 있는 다양한 View를 스와이프하는 것을 제어하는 것이다.
- ViewPager는 PagerAdapter를 상속해서 구현하도록 하는 Adapter패턴으로 이루어져 있다.
- PagerAdapter는 추상 클래스 이므로 PagerAdapter를 상속해서 사용해야한다.
- 구글은 개발자들이 PagerAdapter를 쉽게 사용할 수 있다로고 PagerAdapter를 상속하는 2가지 Adapter를 제공한다.

<br>

### FragmentPagerAdapter
- PagerAdapter를 상속하고 있는 추상클래스이다.
- 모든 Fragment를 메모리에 저장해두기 때문에 빠르게 스와이핑해도 메모리에 로드된 Fragment를 가져다 쓰므로 버벅이지 않고 화면이 잘 넘어간다.
- 많은 개수의 Fragment를 가지고 있다면 저장해야할 Fragment도 많아지므로 메모리 측면에서 부담이 될 수 있다.

### FragmentStatePagerAdapter
- PagerAdapter를 상속하고 있는 추상 클래스이다.
- 필요에 따라 메모리에서 Fragment를 제거하고 다시 생성하며 상태를 유지한다.
- 메모리에는 각 Fragment의 상태값만 계속 저장하기 때문에 메모리 입장에서는 부담이 덜하다.

<br>

## PagerAdapter
- PagerAdapter는 ViewPager를 구현하는데 필요한 adapter를 제공하는 Base class이다.
- 보통 PagerAdapter를 직접 구현해 사용하기 보다, PagerAdapter를 이미 상속하고 있는 FragmentPagerAdapter, FragmentStatePagerAdapter를 사용한다.
- PagerAdapter를 상속하고 있는 하위 클래스들은 반드시 instantiateItem, destroyItem, getCount, isViewFromObject를 오버라이딩 해야한다.


<br>
<br>
<br>

## ViewPager2???
안드로이드 개발자들이 PagerAdapter, FragmentAdapter, FragmentStatePagerAdapter를 통해 화면을 스와이프하는 ViewPager를 잘 구현하고 있었는데 2019년에 ViewPager2라는 것이 등장했다. 등장한 이유는 PagerAdapter에 데이터 변경과 관련된 문제가 있었기 때문이다.

- PagerAdapter는 뷰페이져의 페이지 개수가 변경된다거나, 페이지에 데이터가 변경될 때 ViewPager에게 데이터 변경 콜백을 보내서 뷰페이저를 갱신하는 notifyDataSetChanged()라는 메서드를 제공하고 있다.

![viewpager2](https://velog.velcdn.com/images/dabin/post/c3cf9fc7-2ed4-44f4-b584-25cb8e73a818/image.png)

- PagerAdapter.notifyDataSetChanged()를 호출하면 메모리에 저장되어 있는 모든 페이지의 인스턴스에 대해 갱신처리가 이루어지는 로직인데, 이때 내부적으로 PagerAdapter.getItemPosition() 메소드를 호출해 새로운 포지션을 가져오게끔 되어있는데, getItemPosition()이 리턴하는 값이 POSITION_UNCHANGED(포지션에 변경이 없음)으로 고정되어 있던 것이다.

- 따라서 notifyDataSetChanged()를 호출해 페이지 갱신 로직을 요청해도 페이지에 변경이 없다는 상태로 고정되어있어 갱신이 이루어지지 않았는데, 이러한 문제점으로 인해 구글은 ViewPager2를 새롭게 등장시켰다!