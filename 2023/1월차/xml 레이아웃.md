# Layout의 종류와 속성
#### 레이아웃(Layout)은 View를 예쁘게 담아주는 역할을 한다.
#### 레이아웃마다 성질이 달라서 자신이 구상한 것에 맞게 구현하려면 레이아웃의 성질을 파악해야한다.

<br>

**자주 쓰는 레이아웃(Layout)의 종류**

<br>

## 1. 리니어 레이아웃 (Linear Layout)
#### Linear(선의/선형의)라는 뜻처럼 가로나 세로 방향으로 일열로 나열할 때 사용한다.

<br>


![리니어 레이아웃1](https://blog.kakaocdn.net/dn/dZxbcU/btqUYHEZYSw/iLUf7r8k1ZlvGEG0yzPnGK/img.png)

#### TextView가 Horizonta(수평, 가로, 왼쪽에서 오른쪽)으로 배열 된 Linear Layout 


![리니어 레이아웃2](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FLnyba%2FbtqUTmIFowg%2FWpt8ZUD6RGiJVDEx6HW4AK%2Fimg.png)

#### TextView가 Vertical(수직, 세로, 위에서 아래)로 배열된 LinearLayout

<br>
<br>
<br>

## 2. 렐러티브 레이아웃 (Relative Layout)
#### 렐러티브 레이아웃은 뷰를 특정 뷰나 부모의 위치에 맞게 배치를 할 수 있다.

![랠러티브 레이아웃](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FPEl8i%2FbtqURu1toU0%2FSi6C7k6OcmM5UgEOfLRkL0%2Fimg.png)

#### 사진에서 보다시피 TextView1이 부모의 중앙, TextView2는 TextView1의 오른쪽 밑에 위치하고있고 TextView3는 TextView2의 왼쪽 밑, TextView4또한 TextView3의 왼쪽 밑에 특정 뷰나 레이아웃의 **상대적인(Relative)** 위치에 배치 할 수 있다.

<br>
<br>
<br>

 ## 3. 테이블 레이아웃 (Table Layout)
 #### 테이블 레이아웃은 뷰들을 표(Table)처럼 배치가 가능하다. 엑셀처럼 표의 크기와 모양, 테두리 색 변경이 가능하다.


![테이블 레이아웃](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FKU9k5%2FbtqUYF1wH0v%2FFbpX78BViTASoT5XmY98f1%2Fimg.png)

<br>
<br>
<br>

## 4. 프레임 레이아웃 (Frame Layout)
#### 프레임 레이아웃은 뷰들을 액자(Frame)처럼 배치 할 수 있다.


![프레임 레이아웃](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbnnRJt%2FbtqUOCZQZaF%2FmYDEEkiWml5EfxLngYdLy1%2Fimg.png)


<br>
<br>
<br>

## 5. 드로워 레이아웃 (Drawer Layout)
#### 드로워 레아아웃은 뷰들을 서랍(Drawer)에서 꺼내듯 배치 할 수 있습니다.

![드로워 레이아웃](https://blog.kakaocdn.net/dn/kRMMa/btqUZuemAQ1/Z7bx4zdxvrUnkJ4WLscsg0/img.png)
#### 평소에는 보이지 않으며 슬라이드 or 함수처리(버튼 클릭이나 특정 입력)를 통해 표시되게 할 수 있다.

<br>
<br>
<br>

## 6. 컨스트레인트 레이아웃 (Constraint Layout)
#### 컨스트레인트 레이아웃은 뷰의 위치를 반드시 강제(Constraint)적으로 지정해주어야 한다. 쉽게 말하자면 자기가 원하는 위치에 세세하게 배치할 수 있다는 것!

![컨스트레인트 레이아웃](https://blog.kakaocdn.net/dn/pWWDA/btqUUprEGmt/mLQaFQsk1ZVFSZDKD4o9fK/img.png)
#### 사진에서 보다시피 제약 받지 않고 원하는 위치에 세세하게 배치되어 있는 것을 볼 수 있다.

<br>
<br>
<br>

### **위에서 설명한 레이아웃들을 여러개 활용하여 하나의 xml 파일에 적용해 사용 할 수 도 있다!!😎**