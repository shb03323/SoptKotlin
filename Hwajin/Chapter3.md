# 3장. 함수 정의와 호출
</br>

## 코틀린에서 컬렉션 만들기
* 코틀린은 자신만의 컬렉션 기능을 제공하지 않음
* 코틀린 컬렉션은 자바 컬렉션과 똑같은 클래스지만, 더 많은 기능 사용 가능
</br>

## 함수를 호출하기 쉽게 만들기
* 직접 구현하기
  * joinToString 함수는 컬렉션의 원소를 StringBuilder의 뒤에 덧붙임. 원소 사이에 구분자(separator)를 추가하고, StringBuilder의 맨 앞과 맨 뒤에는 접두사(prefix)와 접미사(postfix) 추가
  ```kotlin
  fun <T> joinToString(
    collection: Collection<T>,
    separator: String,
    prefix: String,
    postfix: String
  ) : String {
    val result = StringBuilder(prefix)
    for ((index, element) in collection.withIndex()) {
      if (index > 0) result.append(separator)
      result.append(element)
    }
    result.append(postfix)
    return result.toString()
  }
  ``` 
  * 이 함수는 제너릭함
    * 어떤 타입의 값을 원소로 하는 컬렉션이든 처리 가능 
  ```kotlin
    joinToString(list,"; ","(", ")"))
  ```
  * 출력값 (1; 2; 3)
</br>

* 이름 붙인 인자
  * 위의 함수는 가독성이 별로임
  * 코틀린으로 작성한 함수를 호출할 때는 함수에 전달하는 인자 중 일부 또는 전부의 이름을 명시 가능
  * 인자 중 하나라도 이름을 명시하고 나면 그 뒤에 오는 모든 인자는 이름을 꼭 명시
  ```kotlin
  joinToString(collection, separator = ";", prefix = "(", postfix = ")")
  ``` 
</br>

* 디폴트 파라미터 값
  * 코틀린에서는 함수 선언에서 파라미터의 디폴트 값을 지정할 수 있으므로 오버로드를 피할 수 있음
  * 대부분의 경우 아무 접두사나 접미사 없이 콤마로 원소를 구분
  ```kotlin
  fun <T> joinToString(
    collection: Collection<T>,
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
  ) : String
  ``` 
  * 함수를 호출할 때 모든 인자를 쓸 수도 있고, 일부 생략 가능
    * 일부를 생략하면 뒷부분의 인자들이 생략
    * 이름 븉인 인자를 사용하는 경우에는 중간에 있는 인자 생략 가능, 지정하고 싶은 인자를 이름을 붙여서 순서와 관계없이 지정 가능
  * 함수를 호출하는 쪽이 아닌 함수 선언 쪽에서 지정 
</br>

* 정적인 유틸리티 클래스 없애기: 최상위 함수와 프로퍼티
  * 함수를 직접 소스 파일의 최상위 수준, 모든 다른 클래스 밖에 위치시키기
  * joinToString 함수를 strings 패키지에 직접 넣기
  ```kotlin
  // joinToString() 함수를 최상위 함수로 선언
  package strings
  fun joinToString(...) : String { ... }
  ``` 
  </br>
  * 최상위 프로퍼티
    * 프로퍼티도 파일의 최상위 수준에 놓을 수 있음 
    ```kotlin
    var opCount = 0 // 최상위 프로퍼티를 선언
    fun performOperation() {
      opCount++ // 최상위 프로퍼티의 값 변경
    }
    fun reportOperationCount() {
      println("Oeration performed $opCount times") // 최상위 프로퍼티의 값을 읽음
    }
    ``` 
</br>

## 메소드를 다른 클래스에 추가: 확장 함수와 확장 프로퍼티
* 확장 함수
  * 기존 자바 API를 재작성하지 않고도 코틀린이 제공하는 여러 편리한 기능을 사용하도록 도와주는 역할
  * 어떤 클래스의 멤버 메소드인 것처럼 호출할 수 있지만 그 클래스의 밖에 선언된 함수
  * 오버라이드 
  * 확장 함수 만들기
    * 함수 이름 앞에 그 함수가 확장할 클래스 이름 추가
    * 클래스 이름: 수신 객체 타입
    * 수신 객체: 확장 함수가 호출되는 대상이 되는 값  
    ```kotlin
    fun String.lastChar() : Char = this.get(this.length -1)
    // String은 수신 객체 타입
    // this는 수신 객체
    
    // 호출은 일반 클래스 멤버 호출과 동일
    println("Kotlin".lastChar())
    ```
</br>

* 임포트와 확장 함수
  * 확장 함수를 사용하기 위해서는 임포트 필수
  ```kotlin
  import strings.lastChar
  
  // *을 사용한 임포트 가능
  import string.*
  
  // as 키워드를 사용해 다른 이름으로 부르기 가능
  // -> 확장 함수 이름 충돌 해결 방법
  import string.lastChar as last
  ```  
</br>

* 확장 함수로 유틸리티 함수 정의
```kotlin
  // joinToString()을 확장으로 정의
  fun <T> Collection<T>.joinToString( // Collection<T>에 대한 확장 함수를 선언
    // 파라미터의 디폴트 값 지정
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
  ) : String {
    val result = StringBuilder(prefix)
    for ((index, element) in collection.withIndex()) { // this는 수신 객체. 여기서는 T 타입의 원소로 이뤄진 컬렉션
      if (index > 0) result.append(separator)
      result.append(element)
    }
    result.append(postfix)
    return result.toString()
  }
  ``` 
  * 이제 joinToString을 클래스의 멤버 처럼 호출 가능
  ```kotlin
  val list = arrayListOf(1, 2, 3)
  println(list.joinToString(" "))
  
  // 1 2 3 
  ``` 
</br>

* 확장 프로퍼티
  * 기존 클래스 객체에 대한 프로퍼티 형식의ㅣ 구문으로 사용할 수 있는 API 추가 가능
  * 아무 상태도 가질 수 없음
  * 프로퍼티 문법으로 더 짧게 코드 작성 가능
  ```kotlin
  // 확장 프로퍼티 선언
  // -> 기본 게더 구현 제공 X 최소한 게더 정의 필수
  // -> 초기화 코드 사용 X
  val String.lastChar: Char
    get() = get(length -1)
    
  // 변경 가능한 확장 프로퍼티 선언
  var StringBuilder.lastChar: Char
    get() = get(length-1) // 프로퍼티 게더
    set(value: Char) {
      this.setCharAt(length -1, value) // 프로퍼티 세터
    }
  ```
  ```kotlin
  val sb = StringBuilder("Kotlin?")
  sb.lastChar= '!'
  println(sb)
  
  // 출력값 Kotlin!
  ```
</br>

## 컬렉션 처리: 가변 길이 인자, 중위 함수 호출, 라이브러리 지원
* varage 키워드 사용하면 호출 시 인자 개수가 달라질 수 있는 함수 정의 가능
* 중위 함수 호출 구문 사용 시 인자가 하나뿐인 메소드를 간편하게 호출 가능
* 구조 분해 선언을 사용하면 복합적인 값을 분해해서 여러 변수에 나눠 담기 가능
</br>

* 가변 인자 함수
  * 인자의 개수가 달라질 수 있는 함수
  * 코틀린에서는 파라미터 앞에 varage 변경자를 붙임
  * 배열에 들어있는 원소를 가변 길이 인자로 넘길 때는 기술적으로는 스프레드 연산자 사용, 실제로는 배열 앞에 * 붙이기
  ```kotlin
  fun main(args: Array<String> {
    val liast = listOf("args: ", *args) // 스프레드 연산자가 배열의 내용을 펼쳐줌
    println(list)
  }
  ``` 
</br>

* 값의 쌍 다루기: 중위 호출과 구조 분해 선언
  * 중위 호출
    * 인자가 하나뿐인 일반 메소드나 인자가 하나뿐인 확장 함수에 사용 가능
    * 함수를 중위 호출에 사용하게 허용하고 싶으면 infix 변경자를 함수 선언 앞에 추가   
  ```kotlin
  val map = mapOf (1 to "one", 7 to "seven")
  ``` 
  * 여기서 to는 키워드가 아닌 중위 호출이라는 방식으로 to라는 일반 메소드 호출
  ```kotlin
  // 호출 방법
  1.to("one") // "to" 메소드를 일반적인 방식으로 호출
  1 to "one" // "to" 메소드를 중위 호출 방식으로 호출
  ```
  </br>
  
  * 구조 분해 선언
  ```kotlin
  // to 함수의 정의를 간략하게 줄인 코드
  infix fun Any.to(other: Any) = Pair(this, other)
  ```
    * Pair는 코틀린 표준 라이브러리 클래스로 두 원소로 이루어진 순서쌍을 표현
    ```kotlin
    // Pair의 내용으로 두 변수 즉시 초기화 가능 - 구조 분해 선언
    val (number, name) = 1 to "one"
    ```
    * Pair 인스턴스 외 다른 객체에도 구조 분해 선언 사용 가능
    * 예를 들어 key와 value라는 두 변수를 맵의 원소를 사용해 초기화
</br>

## 문자열과 정규식 다루기
* 문자열 나누기
  * 마침표(.)는 모든 문자를 나타내는 정규식으로 해석
  * 정규식을 파라미터로 받는 함수는 String이 아닌 Regex 타입의 값을 받음
  * 모클린에서는 split 함수에 전달하는 값의 타입에 따라 정규식이나 일반 텍스트 중 어느 것으로 문자열을 분리하는지 쉽게 알 수 있음
  ```kotlin
  println("12.345-6.A".split("\\.|-".toReget())) // 정규식을 명시적으로 만듦
  
  // 출력값 [12, 345, 6, A]
  
  // 간단한 경우 정규식을 쓸 필요 없음
  println("12.345-6.A".split(".", "-")) // 여러 구분 문자열 지정
  ``` 
</br>

* 정규식과 3중 따옴표로 묶은 문자열
  * 3중 따옴표 문자열에서는 역슬래시(\)를 포함한 어떤 문자도 이스케이프할 필요 없음
    * 정규식의 경우 마침표 기호를 이스케이프하려면 \\. 3중 따옴표 문자열에서는 \.
  ```kotlin
  fun parsePath(path: String) {
    val reget = """(.+)/(.+)\.(.+)""".toReget()
    val matchResult = regex.matchEntire(path)
    if (matchResult != null) {
      val directory, filename, extension) = matchResult.destructured
      println("Dir: $directory, name: $filename, ext: $extension")
  }
  ```
</br>

* 여러 줄 3중 따옴표 문자열
  * 3중 따옴표 문자열에는 줄 바꿈을 표현하는 아무 문자열이나 (이스케이프 없이) 그대로 들어감
  * 들여쓰기나 줄바꿈을 포함한 모든 문자가 들어감
  ```kotlin
  var kotlinnLogo = """| //
                       .| //"""
  println(kotlinLogo.trimMargin("."))
  ``` 
</br>

## 코드 다듬기: 로컬 함수와 확장
* 코틀린에서는 함수에서 추출한 함수르르 원 함수 내부에 중첩 가능
* 흔히 발생하는 코드 중복을 로컬 함수를 통해 제거
```kotlin
// 로컬 함수를 사용해 코드 중복 줄이기
class User(val id: Int, val name: String, val address: String)
fun saveUser(user:User) {
  fun validate(user : User, value: String, fieldName: String) { // 한 필드를 검증하는 로컬 함수 정의
    if (value.isEmpty()) {
      throw IllegalArgumentException(
        "Can't save user ${user.id}: empty $filedName"
      )
    }
  }
  // 로컬 함수를 호출해서 각 필드를 
  validate(user, user.name, "Name")
  validate(user, user.address, "Address")
  
  //user를 데이터베이스에 저장
}
```

## 요약
* 코틀린은 자체 컬렉션 클래스를 정의하지 않지만 자바 클래스를 확장해서 더 풍부한 API 제공
* 함수 파라미터의 디폴트 값을 정의하면 오버로딩한 함수를 정의할 필요성이 줄어듦. 이름 붙인 인자를 사용하면 함수 호출의 가독성 증가
* 코틀린 파일에서 클래스 멤버가 아닌 최상위 함수와 프로퍼티 직접 선언 가능 -> 코드 구조가 더 유연
* 확장함수와 프로퍼티를 사용하면 외부 라이브러리에 정의된 클래스를 포함해 모든 클래스의 API를 그 클래스의 소스코드를 바꿀 필요 없이 확장 가능
* 중위 호출을 통해 인자가 하나 밖에 없는 메소드나 확장 함수를 더 깔끔한 구문으로 호출 가능
* 코틀린은 다양한 문자열 처리 함수 제공
* 자바 문자열로 표현하려면 수많은 이스케이프가 필요한 문자열의 경우 3중 따옴표 문자열 사용 -> 더 깔끔
* 로컬 함수 사용 -> 깔끔, 중복 제거

</br>
