## Array vs List
---
코틀린에서의 Array와 List를 비교해보겠다.


## Array

특징
- 같은 자료형을 가진 값(value: 자료형)들을 하나로 나타낸 것
- 초기화와 동시에 크기(size)가 정해짐
- `메모리 공간에 연속적`으로 저장됨
- 인덱스(index)를 통하여 값(value: 자료형)에 접근
- `정적`타입

<br>

장점
- 인덱스(index)를 통한 검색이 용이함
- 연속된 메모리 공간을 사용하여 메모리 관리가 편리함

<br>

단점
- 배열에서 값을 삭제하더라도 배열의 크기가 줄어들지 않아 메모리가 낭비됨
- 배열은 정적이므로 배열의 크기를 컴파일 이전에 지정해야함
- 배열은 정적이므로 배열의 크기가 정해진 이후 배열의 크기를 재조정 할 수 없음

<br>
<br>

## List

특징
- `순서`가 있는 엘리먼트(element: 자료형)들의 집합, 시퀀스(Sequence)라고도 부름
- `불연속적인 메모리 공간`을 점유하여 메모리 관리가 용이
- `동적`타입
- 포인터를 통해 값에 접근
- 인덱스(index)는 몇 번째 데이터인가 정도의 의미를 갖음

<br>

장점
- 포인터를 사용하여 다음 값의 위치를 가르키고 있으므로 삽입/삭제가 용이
- 동적타입으로 크기가 정해져있지 않음
- 빈틈 없는 엘리먼트(element: 자료형) 적재라는 장점을 취한 자료구조
- 메모리의 재사용이 용이
- 불연속적인 메모리공간을 점유하여 메모리 관리가 편리함

<br>

단점
- 불연속적인 메모리공간을 사용하여 검색 성능이 좋지 않음
- 포인터를 통하여 다음 값을 가르키므로 배열 대비 추가적인 메모리 공간이 필요
- 빈 요소(element: null)을 허용하지 않음

<br>

## Kotlin에서의 Arrays와 List

Kotlin Arrays 특징
- Kotlin에서는 Arrays는 Primitive type arrays임
- Primitive type arrays 에는 ByteArray, ShortArray, IntArray, BooleanArray 등이 있음

<br>

Kotlin List 특징
- Kotlin의 List는 MutableList를 List인터페이스에 업캐스팅하여 사용함

<br>

Array와 List의 차이점

- Iterator 
    - Arrays : Iterator를 내부적으로 구현
    - List : Iterable 인터페이스를 구현하여, interface 구현에 의해 만들어짐

- Index
    - Arrays : Index를 통한 접근 가능
    - List : Index를 통한 접근 가능

- isEmpty, contains
    - Arrays : 불가
    - List : 가능

- add, remove
    - Arrays : 불가
    - List : 가능

Array는 Collection에서 제공하는 기능을 사용할 수 없거나 직접 구현해서 사용해야 함

<br>
<br>

## 성능비교
---
`Array : 11.380855ms`	

`List : 3547.518ms`

`ArrayList : 0.392117ms`

`MutableList : 3902.1213ms`

결론부터 말하자면 환경에 따라 다르다! 만약 인덱스로 요소 접근시 Array보다 List가 약 300배 정도 느리다고 한다!

아 그리고 여담으로 Array가 ArrayList보다 약 40배 느린데, 이 것은 아직 이유를 찾지 못했다고 합니다..? 추측만 있다고 함

그래서 따로 조사해봤는데 Array가 배열 초기화시 메모리에 바로 할당되어 ArrayList보다 속도가 빠르다고 합니다.

<br>
<br>
<br>

## 자료구조에서의 Array vs List
---
두 자료구조의 서로 다른 큰 특징은 Array는 `메모리 상에 데이터가 연속적으로 저장`되고, List는 `메모리 상에 데이터가 비연속적으로 저장`된다.

Array와 List 모두 선형 자료구조로 매우 유사하다.

<br>

### Array
우선 배열이란 같은 성질을 갖는 항목들의 집합이다.
같은 특성을 갖는 원소들이 순서대로 구성된 집합으로 선형 자료 구조이며,
메모리 상에 연속적으로 데이터가 저장된 연접 리스트(순차 리스트)에 해당한다.
순차적으로 저장된 데이터를 참조하는 데에는 Index가 사용된다.
(1차원 배열, 2차원 배열, 3차원 배열)

![Array1](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FV6kox%2FbtroJiYjiJ1%2FXoNZcohFyQiLfVOhmkozpK%2Fimg.png)

### 특징
- 고정된 크기를 갖는다.(데이터의 개수가 정해져 있다.)

- 배열의 크기를 10으로 지정한다면, 내부에 데이터가 5개만 있더라도 실제 배열의 크기는 10이다. -> 메모리가 낭비될 수 있다.

- 논리적 저장 순서와 물리적 저장 순서가 일치하다. (메모리 상에 데이터도 순차적으로 저장된다.)

- Cache Hit Rate 가 높다.

- 추가적으로 소모되는 메모리 양(오버헤드)이 거의 없다. (데이터의 양에 맞게 크기를 정해놓고 사용하기 때문)

- 삽입과 삭제의 경우 O(N)의 시간 복잡도를 갖는다.

- 원소에 접근할 때는 O(1)의 시간 복잡도를 갖는다.

- 기억 장소를 미리 확보해야 한다.

### Cache Hit Rate?
Cache Hit Rate란 CPU가 참조하고자 하는 메모리가 캐시에 존재하고 있을 경우를 말하는데, 이 비율이 높으면 높을수록 좋은 성능을 가지고 있다고 할 수 있다.

우선 메모리에 대한 개념 중 참조 지역성 원리라는 것이 있는데, 참조 지역성 원리란 동일한 값 또는 해당 값에 관계된 스토리지 위치가 자주 액세스 되는 특성으로, 지역성의 원리라고도 부른다.

이 참조 지역성 원리에는 3가지 종류가 있다.

1. 공간 지역성(Spacial Locality) : 참조된 주소와 인접한 주소의 내용이 다시 참조되는 특성

2. 시간 지역성(Temporal Locality) : 최근에 참조된 주소는 빠른 시간 내에 다시 참조되는 특성

3. 순차 지역성(Sequential Locality) : 데이터가 순차적으로 액세스 되는 특성, 공간 지역성에 편입되어 설명되기도 함

배열은 메모리 상 연속적으로 데이터가 저장되어있다고 했다.

즉, 배열은 공간 지역성이 좋아 높은 Cache Hit Rate를 가진다고 할 수 있다. 

<br>
<br>
<br>

### List
자료 구조의 관점에서 보면 배열 또한 리스트에 포함되지만, 프로그래밍 언어의 관점으로 리스트란 배열이 가지고 있는 인덱스라는 장점을 버리고, 빈틈없는 데이터의 적재라는 장점을 취한 인터페이스다.

즉, LinkedList(연결 리스트), ArrayList 등의 선형 자료 구조를 구현할 때 사용되는 추상 자료형이다. 같은 특성을 갖는 원소들이 순서대로 구성된 집합인 선형 자료 구조이다.

하지만 데이터가 메모리 상에 연속적으로 저장되진 않기 때문에 순차 리스트가 아닌 연결 리스트라고 불린다.

### 특징
- 배열은 크기가 정해져 있고, 메모리 상에 데이터가 연속적으로 있어야 하기 때문에 메모리의 낭비가 발생할 수 있다.
하지만 리스트는 빈 공간을 허용하지 않기 때문에 이러한 메모리의 낭비는 없다.

- 데이터들이 순차적으로 구성된 집합이지만, 일반적으로 데이터가 메모리 상에 연속적으로 있진 않다.
(실제 메모리 주소 랜덤) 따라서 Cache Hit Rate가 낮다.
- 크기가 가변적이다.

- 원소에 접근할 때 O(N)의 시간 복잡도를 갖는다.

- 삽입과 삭제의 경우 O(N)의 시간 복잡도를 갖는다.

- 삽입, 삭제, 크기 등 해당 리스트를 사용하기 위한 다양한 속성과 메서드가 존재한다. (기본 동작은 구현할 필요 없다.)

