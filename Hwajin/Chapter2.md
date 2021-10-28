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
  * 언어 기본 기능
  * 자바의 필드와 접근자 메소드 대신
  * val로 선언한 프로퍼티는 읽기 전용, var로 선언한 프로퍼티는 변경 가능
  ```kotlin
  class Person (
       val name: String, // 읽기 전용 프로퍼티
       val isMarried: Boolean // 쓸 수 있는 프로퍼티
   )
  ```
</br>

* 커스텀 접근자
```kotlin
class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean
    get() { // 프로퍼티 게더 선언
        return height == width
    }
}
```
</br>

* 코틀린 소스코드 구조: 디렉터리와 패키지
  * 모든 코틀린 파일의 맨 앞에 package문을 넣을 수 있음
    * 파일 안에 있는 모든 선언(클래스, 함수, 프로퍼티 등)이 해당 패키지 안으로
  * 다른 패키지에 정의한 선언을 사용하려면 임포트문 사용
</br>  

## 선택 표현과 처리: enum과 when
* enum 클래스 
```kotlin
enum class Color {
    RED, ORANGE, BLUE
}
```
  * enum은 소프트 키워드 (soft keyword)
  * enum 클래스 안에 프로퍼티나 메소드 정의 가능
  * enum 클래스 안에 메소드를 정의하는 경우 반드시 enum 상수 목록과 메소드 정의 사이에 세미콜론
</br>

* when으로 enum 클래스 다루기
  * 각 분기의 끝에 break를 넣지 않아도 됨
  * 한 when 분기 안에 여러 값 사용 가능
  * enum 상수 값을 임포트해서 enum 클래스 수식자 없이 enum 사용 가능
  ```kotlin
  fun getWarmth (color: Color) = when(color) {
      Color.RED, Color.ORANGE -> "warm"
      Color.BLUE -> "cold"
  }
  ```
  ```kotlin
  fun getWarmth (color: Color) = when(color) {
      Color.RED, Color.ORANGE -> "warm"
      Color.BLUE -> "cold"
  }  
  ```
</br>

* 스마트 캐스트: 타입 검사와 타입 캐스트를 조합
  * 프로그래머 대신 컴파일러가 캐스팅
  * 굳이 변수를 원하는 타입으로 캐스팅하지 않아도 처음부터 그 변수가 원하는 타입으로 선언된 것처럼 사용
</br>

## 대상을 이터레이션: while과 for 루프
* while 루프
  * while
  * do-while 
  ```kotlin
  while (조건) { // 조건이 참인 동안 본문을 반복 실행
    /*...*/
  }
  
  do {
    /*...*/
  } while (조건) // 맨 처음에 무조건 본문을 한 번 실행한 다음, 조건이 참인 동안 본문을 반복 실행
  ```
</br>

* 수에 대한 이터레이션: 범위와 수열
  * 범위
    * 기본적으로 두 값으로 이뤄진 구간
    * 보통 두 값은 정수 등의 숫자 타입의 값이며, .. 연산자로 시작 값과 끝 값을 연결해서 범위를 만듦
    * 코틀린의 범위는 폐구간(닫힌 구간) 또는 양끝을 포함하는 구간
    ```kotlin
    val oneToTen = 1..10  
    ```
  * 수열
    * 100 downTo 1 : 역방향 수열 -> 역방향 수열의 기본 증가 값은 -1
    * step2 : 증가값의 절대값이 2로 변경

* 맵에 대한 이터레이션


## 코틀린의 예외 
* try, cathch, finally
  * throws 절이 코드에 없음
  ```kotlin
    fun readNumber(reader: BufferedReader): Int? { // 함수가 던질 수 있는 예외를 명시할 필요가 없
        try { 
            val line = reader.readLine()
            return Integer.parseInt(line)
        }
        catch (e: NumberFormatException) {
            return null
        }
        finally {
            reader.close()
        }
    }
  ```
</br>

* try를 식으로 사용
  * try의 값을 변수에 대입 가능
  * try의 본문은 반드시 중괄호 {}로 둘러싸야 함
  ```kotlin
    fun readNumber(reader: BufferedReader) : Int? {
        try {
            val line = reader.readLine()
            return Integer.parseInt(line)
        }
        catch (e: NumberFormatException) {
            return null
        }
        finally {
            reader.close()
        }
    }
  ```
  
</br>

## 요약
* 함수를 정의할 때 fun 키워드 사용. val과 var은 각각 읽기 전용 변수와 변경 가능한 변수를 선언할 때 사용
* 문자열 템플릿을 사용하면 코드가 간결해짐. 변수 이름 앞에 $를 붙이거나, ${식}
* 코틀린에서는 값 객체 클래스를 아주 간결하게 표현 가능
* if는 코틀린에서 식이며, 값을 만들어냄
* switch와 비슷한 when이 존재
* 컴파일러가 스마트 캐스트를 활용해 자동으로 타입 변경
* for, while, do-while 루프는 자바와 비슷
* 1..5와 같은 식은 범위를 만들어냄. 범위 안에 유무를 검사하기 위해 in 사용
* 예외 처리는 자바와 비슷. 다만, 코틀린에서는 함수가 던질 수 있는 예외 선언하지 않아도 됨.

</br>
