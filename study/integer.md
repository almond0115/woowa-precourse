# Integer vs int
int는 변수 선언 할 때, Integer는 정수형을 객체로 사용할 때 쓰는 걸로만 알고 있었습니다. 프리코스 문제 풀이를 진행하면서 int와 Integer의 좀 더 정확한 차이를 알고 싶었습니다.

## int 는 무엇인가?  &rarr; `primitive type`
> int 는 변수의 타입 (data type) 입니다.

변수(variable)는 `값을 저장할 수 있는 메모리 상의 공간`을 의미합니다.
```c
int a = 3;
char firstName = "H";
```
앞에 적힌 int와 char가 변수의 형을 지정해 주고 있는 `변수의 타입 (data type)` 이라 합니다. <br>
즉, 자료형은 `data의 type에 따라 값이 저장될 공간의 크기와 저장 형식을 정의한 것`이라고 볼 수 있습니다.
<br>

## Integer 는 무엇인가? &rarr; `wrapper class`
```java
ArrayList<Integer> intList = new ArrayList<Integer>();
intList.add(1);
intList.add(2);
System.out.println(intList.get(0));
```
```java
String stringNum = "123";
int intNum = Integer.parseInt(stringNum);
System.out.println(intNum);
```

`Integer`는 무엇인가 하면, (1)에서 다룬 기본형을 표현해야 하는 경우가 있습니다.
* 매개변수로 객체를 필요로 할 때
* 기본형 값이 아닌 객체로 저장해야 할 때
* 객체 간 비교가 필요할 때
<br>

모든 기본형은 래퍼 클래스를 생성할 수 있습니다.

## int와 Integer 차이점은?

int : 자료형 (Primitive type)
* 산술 연산 가능함
* null로 초기화 불가
* 저장 공간이 4Byte 라고 작음

Integer : 래퍼 클래스 (Wrapper class)
* Unboxing 하지 않을 시 산술 연산 불가능함
* null 값 처리 가능
* 저장 공간이 큼
* null 값으로 처리가 가능해 SQL에 용이하게 쓰임

<br>

```
boxing : Primitive type -> Wrapper class 변환 (int to Integer)
unboxing : Wrapper class -> Primitive type 변환 (Integer to int)
```

