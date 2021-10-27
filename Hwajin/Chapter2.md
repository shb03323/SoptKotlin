# 1장. 코틀린 기초
</br>

## 기본 요소: 함수와 변수
#### <함수>
```kotlin
    fun main(args: Array<String>) {
      println("Hello, world!")
    }
```
* 함수를 선언할 때 fun 키워드 사용
* 파라미터 이름 뒤에 그 파라미터의 타입 작성
* 함수를 최상위 수준에 정의 가능
  * 클래스 안에 함수를 넣을 필요 없음 
* 배열도 일반적인 클래스
* 출력할 때 println이라는 wrapper 사용
  * wrapper : 표준 자바 라이브러리 함수를 간결하게 사용할 수 있도록 제공 
* 줄 끝에 세미콜론(;)을 붙이지 않음
</br>

* 코틀린 함수 정의

</br>

* 문(statement)과 식(expression)의 구분
  * 코틀린에서 if는 식O 문X
  * 식: 값을 만들어 내며 다른 식의 하위 요소로 계산에 참여 가능
  * 문: 자신을 둘러싸고 있는 가장 안쪽 블록의 최상위 요소로 존재하며 아무런 값을 만들어내지 않음
  * 자바에서는 모든 제어 구조가 문, 코틀린에서는 모든 제어 구조가 식
  * 자바에서는 대입문은 식, 코틀린에서는 문
</br>

* 식이 본문인 함수
```kotlin
    fun max(a: Int, b: Int) : Int = if (a > b) a else b
```
  * 등호와 식으로 이뤄진 함수 
  * 반환 타입을 생략하면 더 간단한 함수
    * 타입 추론 : 식이 본문인 함수의 경우 컴파일러가 타입을 분석해 프로그래머 대신 프로그램 구성 요소의 타입을 지정
  * <-> 블록이 본문인 함수 : 본문이 중괄호로 둘러싸인 함수
    * 반환 타입을 지정하고 return문을 사용해 반환값을 명시   
</br>

#### <변수>
* 코틀린에서는 키워드로 변수 선언, 변수 이름 뒤에 타입을 명시하거나 생략
```kotlin
    val answer: Int = 1
    val answer = 1
```
</br>

* 변수 선언 시 사용하는 키워드
  * val (value)
    * 변경 불가능한 참조를 저장
    * 초기화하고 나면 재대입 불가능
    * 자바의 final
    * 블록이 실행될 때 오직 한 초기화 문장만 실행된다면 val값을 다른 여러 값으로 초기화 가능 (ex) if-else
    * 참조 자체는 불변일지라도 그 참조가 가리키는 객체의 내부 값은 변경 가능
  * var (variable)
    * 변경 가능한 참조
    * 변수 타입은 고정
</br>

* 문자열 템플릿
```kotlin
    fun main(args: Array<String>) {
      val name = if (args.size > 0) args[0] else "Kotlin"
      println("Hello, $name!") // Bob을 인자로 넘기면 Hello, Bob! 출력, 인자가 없으면 Hello, Kotlin을 출력
    }
```
  * 문자열 리터럴이 필요한 곳에 변수를 넣되 변수 앞에 $ 추가
  * 문자를 문자열에 넣고 싶으면 \를 사용 -> \$x
</br>

```kotlin
    fun main(args: Array<String>) {
      println("Hello, ${if (args.size > 0) args[0] else "someone"}!")
    }
```
  * 복잡한 식도 중괄호({})로 둘러싸서 문자열 템플릿 안에 입력 가능
  * 중괄호로 둘러싼 식 안에서 큰 따옴표 사용 가능
</br>

## 클래스와 프로퍼티
```kotlin
    class Person (val name: String)
```
* 값 객체 (value object) : 코드가 없이 데이터만 저장하는 클래스
* 코틀린의ㅣ 기본 가시성의 public이라 이런 경우 변경자 생략 가능 
</br>

* 프로퍼티


## 선택 표현과 처리: enum과 when

</br>

## 대상을 이터레이션: while과 for 루프

</br>

## 코틀린의 예외 

</br>

## 요약

</br>
