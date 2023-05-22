## Array vs List
---
코틀린에서의 Array와 List를 비교해보겠다.

<br>

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
- 포인터를 사용하여 다음 값의 위츠를 가르키고 있으므로 삽입/삭제가 용이
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

그래서 따로 조사해봤는데 Array가 배열 초기화시 메모리에 바로 할당되어 ArrayList보다 속도가 빠르다고 합니다