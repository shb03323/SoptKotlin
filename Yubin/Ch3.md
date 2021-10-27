# 함수 정의와 호출

## 1. 코틀린에서 컬렉션 만들기

- 코틀린은 자신만의 컬렉션 기능을 제공하지 않는다.

  - ```Kotlin
    val set = hashSetOf(1, 7, 53)
    val list = arrayListOf(1, 7, 53)
    val map = hashMapOf(1 to "one", 7 to "seven", 53 to "fifty-three")
    ```

    ```kotlin
    >>> println(set.javaClass)
    class java.util.HashSet
    >>> println(list.javaClass)
    class java.util.ArrayList
    >>> println(map.javaClass)
    class java.util.HashMap
    ```

  - 코틀린에서 자체 컬렉션을 제공하지 않는 이유

    : 표준 자바 컬렉션을 활용하면 자바 코드와 상호작용하기가 훨씬 더 쉽다. 자바에서 코틀린 함수를 호출하거나 코틀린에서 자바 함수를 호출할 때 자바와 코틀린 컬렉션을 서로 변환할 필요가 없다.

- 코틀린 컬렉션은 자바 컬렉션과 똑같은 클래스지만 자바보다 더 많은 기능을 쓸 수 있다. 

  - 예를 들어 리스트의 마지막 원소를 가져오거나 수로 이뤄진 컬렉션에서 최댓값을 찾을 수 있다.

    ```kotlin
    >>> val strings = listOf("first", "second", "fourteenth")
    >>> println(strings.last())
    fourteenth
    >>> val numbers = setOf(1, 14, 2)
    >>> println(numbers.max())
    14
    ```



---



## 2. 함수를 호출하기 쉽게 만들기

- 디폴트 구현과 다르게 하기 위해서 자바에서는 자바 프로젝트에 구아바나 아파지 커먼즈 같은 서드파티 프로젝트를 추가하거나 직접 관련 로직을 구현해야 한다. 코틀린에서는 이런 요구 사항을 처리할 수 있는 함수가 표준 라이브러리에 이미 들어있다.

- joinToString() 함수의 초기 구현

  ```kotlin
  fun <T> joinToString(
      collection: Collection<T>,
      separator: String,
      prefix: String.
      postfix: String
  ): String {
      val result = StringBuilder(prefix)
      for ((index, element) in collection.withIndex()) {
          if (index > 0) result.append(separator)	// 첫 원소 앞에 구분자를 붙이면 안됨
          result.append(element)
      }
      result.append(postfix)
      return result.toString()
  }
  ```

  - 컬렉션의 원소를 StringBuilder의 뒤에 덧붙인다. 

  - 원소 사이에 separator를 추가하고, StringBuilder의 맨 앞과 맨 뒤에는 prefix와 postfix를 추가한다.

  - 이 함수는 generic하기 때문에 어떤 타입의 값을 원소로 하는 컬렉션이든 처리할 수 있다.

  - 실행

    ```kotlin
    >>> val list = listOf(1, 2, 3)
    >>> println(joinToString(list, "; ", "(", ")"))
    (1; 2; 3)
    ```

  - 함수를 호출할 때마다 매번 네 인자를 모두 전달하는 것이 번거롭다.



### 2.1. 이름 붙인 인자

- 코틀린으로 작성한 함수를 호출할 때는 함수에 전달하는 인자 중 일부(또는 전부)의 이름을 명시할 수 있다. 호출 시 인자 중 어느 하나라도 이름을 명시하고 나면 혼동을 막기 위해 그 뒤에 오는 모든 인자는 이름을 꼭 명시해야 한다.



### 2.2. 디폴트 파라미터 값

- 자바에서는 일부 클래스에서 overloading한 메소드가 너무 많아진다는 문제가 있다. 모든 overloading 함수에 대해 대부분의 설명을 반복해 달아야 하고 인자 중 일부가 생략된 overload 함수를 호출할 때 어떤 함수가 불릴지 모호한 경우가 생긴다.

- 코틀린에서는 함수 선언에서 파라미터의 디폴트 값을 지정할 수 있으므로 이런 overload 중 상당수를 피할 수 있다. 대부분의 경우 아무 prefix나 postfix 없이 콤마로 원소를 구분한다.

  - 디폴트 파라미터 값을 사용해 joinToString() 정의

    ```kotlin
    fun <T> joinToString(
    	collection: Collection<T>,
        separator: String = ", ",
        prefix: String = "",
        postfix: String = ""
    ): String
    ```

- 함수의 디폴트 파라미터 값은 함수를 호출하는 쪽이 아니라 함수 선언 쪽에서 지정된다. 따라서 어떤 클래스 안에 정의된 함수의 디폴트 값을 바꾸고 그 클래스가 포함된 파일을 재컴파일하면 그 함수를 호출하는 코드 중에 값을 지정하지 않은 모든 인자는 자동으로 바뀐 디폴트 값을 적용받는다.





### 2.3. 정적인 유틸리티 클래스 없애기: 최상위 함수와 프로퍼티

- 코틀린에서는 JDK의 Collections 클래스와 같은 무의미한 클래스가 필요 없다. 대신 함수를 직접 소스파일의 최상위 수준, 모든 다른 클래스의 밖에 위치시키면 된다. 그런 함수들은 여전히 그 파일의 맨 앞에 정의된 패키지의 멤버 함수이므로 다른 패키지에서 그 함수를 사용하고 싶을 때는 그 함수가 정의된 패키지를 임포트해야만 한다. 하지만 임포트 시 유틸리티 클래스 이름이 추가로 들어갈 필요는 없다.

- joinToString() 함수를 최상위 함수로 선언하기

  ```kotlin
  package strings
  fun joinToString(...): String {...}
  ```

  이 함수를 자바 등의 다른 JVM 언어에서 호출하고 싶다면 코드가 어떻게 컴파일되는지 알아야 joinToString과 같은 최상위 함수를 사용할 수 있다. 하지만 코틀린만 사용하는 경우에는 그냥 그런 클래스가 생긴다는 사실만 기억하면 된다.

- 코틀린 컴파일러가 생성하는 클래스의 이름은 최상위 함수가 들어있던 코틀린 소스 파일의 이름과 대응한다. 코틀린 파일의 모든 최상위 함수는 이 클래스의 정적인 메소드가 된다. 

- 파일에 대응하는 클래스의 이름 변경하기

  - 코틀린 최상위 함수가 포함되는 클래스의 이름을 바꾸고 싶다면 파일에 @JvmName 애노테이션을 추가하면 된다. @JvmName 애노테이션은 파일의 맨 앞, 패키지 이름 선언 이전에 위치해야 한다.

    ```kotlin
    @file:JvmName("StringFunctions") // 클래스 이름을 지정하는 애노테이션
    package strings	//@file:JvmName 애노테이션 뒤에 패키지 문이 와야 한다.
    fun joinToString(...): String {...}
    ```

    

- **최상위 프로퍼티**

  : 함수와 마찬가지로 프로퍼티도 파일의 최상위 수준에 놓을 수 있다. 

  ```kotlin
  var opCount = 0	// 최상위 프로퍼티 선언
  fun performOperation() {	// 최상위 프로퍼티의 값 변경
      opCount++
      // ...
  }
  fun reportOperationCount() {
      println("Operation performed $opCount times")	// 최상위 프로퍼티의 값 읽음
  }
  ```



---



## 3. 메소드를 다른 클래스에 추가: 확장 함수와 확장 프로퍼티

- 확장 함수(extension function)은 기존 자바 API를 재작성하지 않고도 코틀린이 제공하는 여러 편리한 기능을 사용할 수 있게 해준다. 확장 함수는 어떤 클래스의 멤버 메소드인 것처럼 호출할 수 있지만 그 클래스의 밖에 선언된 함수다.

- 어떤 문자열의 마지막 문자를 돌려주는 메소드

  ```kotlin
  package strings
  fun String.lastChar(): Char = this.get(this.length - 1)
  // String => 수신 객체 타입
  // this => 수신 객체
  ```

  확장 함수를 만드려면 추가하려는 함수 이름 앞에 그 함수가 확장할 클래스의 이름을 덧붙이기만 하면 된다. 클래스 이름을 **수신 객체 타입(receiver type)**이라 부르며, 확장 함수가 호출되는 대상이 되는 값(객체)을 **수신 객체(receiver object)**라고 부른다.

### 3.1. 임포트와 확장 함수

- 확장 함수를 사용하기 위해서는 그 함수를 다른 클래스나 함수와 마찬가지로 임포트해야만 한다. 코틀린에서는 클래스를 임포트할 때와 동일한 구문을 사용해 개별 함수를 임포트할 수 있다.

  ```kotlin
  import strings.lastChar
  val c = "Kotlin".lastChar()
  ```

  as 키워드를 사용하면 임포트한 클래스나 함수를 다른 이름으로 부를 수 있다.

  ```kotlin
  import strings.lastChar as last
  val c = "Kotlin".last()
  ```

- 한 파일 안에서 다른 여러 패키지에 속해있는 이름이 같은 함수를 가져와 사용해야 하는 경우 이름을 바꿔서 임포트하면 이름 충돌을 막을 수 있다. 코틀린 문법상 확장 함수는 반드시 짧은 이름을 써야 한다. 따라서 임포트할 때 이름을 바꾸는 것이 확장 함수 이름 충돌을 해결할 수 있는 유일한 방법이다.



### 3.2. 자바에서 확장 함수 호출

- 내부적으로 확장 함수는 수신 객체를 첫 번째 인자로 받는 정적 메소드다. 그래서 확장 함수를 호출해도 다른 어댑터 객체나 실행 시점 부가 비용이 들지 않는다. 이런 설계로 인해 자바에서 확장 함수를 사용하기도 편하다. 정적 메소드를 호출하면서 첫 번째 인자로 수신 객체를 넘기기만 하면 된다.

- 확장 함수를 StringUtil.kt 파일에 정의했다면 다음과 같이 호출할 수 있다.

  ```java
  /* java */
  char c = StringUtilKt.lastChar("Java");
  ```

  

### 3.3. 확장 함수로 유틸리티 함수 정의

- joinToString()를 확장으로 정의하기

  ```kotlin
  fun <T> Collection<T>.joinToString(
  	seperator: String = ", ",
  	prefix: String = "",
      postfix: String = ""
  ): String{
      val result = StringBuilder(prefix)
      for ((index, element) in this.withIndex())
      if (index > 0) result.append(separator)
      result.append(element)
      result.append(postfix)
      return result.toString()
  }
  ```

  ```kotlin
  >>> val list = listOf(1, 2, 3)
  >>> println(list.joinToString(separator = "; ",
                               prefix = "(", postfix = ")"))
  	(1; 2; 3)
  ```

  이제 joinToString을 클래스의 멤버처럼 호출할 수 있다.

  ```kotlin
  >>> val list = arrayListOf(1, 2, 3)
  >>> println(list.joinToString(" "))
  	1 2 3
  ```



### 3.4. 확장 함수는 오버라이드할 수 없다

- 멤버 함수 오버라이드하기

  ```kotlin
  open class View {
      open fun click() = println("View clicked")
  }
  class Button: View() {
      override fun click() = println("Button clicked")
  }
  ```

  - 확장 함수는 클래스의 일부가 아니다. 확장 함수는 클래스의 밖에 선언된다. 

- 확장 함수는 오버라이드할 수 없다.

  ```kotlin
  fun View.showOff() = println("I'm a view!")
  fun Button.showOff() = println("I'm a button!")
  ```

  ```
  >>> val view: View = Button()
  >>> view.showOff()
  	I'm a View!
  ```

  view가 가리키는 객체의 실제 타입이 Button이지만, 이 경우 view의 타입이 View이기 때문에 무조건 View의 확장 함수가 호출된다.



### 3.5. 확장 프로퍼티

- 확장 프로퍼티를 사용하면 기존 클래스 객체에 대한 프로퍼티 형식의 구문으로 사용할 수 있는 API를 추가할 수 있다. 프로퍼티라는 이름으로 불리기는 하지만 실제로 확장 프로퍼티는 아무 상태도 가질 수 없다.

- 확장 프로퍼티 선언하기

  ```kotlin
  val String.lastChar: Char
  	get() = get(length - 1)
  ```

  StringBuilder에 같은 프로퍼티를 정의한다면 StringBuilder의 맨 마지막 문자는 변경 가능하므로 프로퍼티를 var로 만들 수 있다.

- 변경 가능한 확장 프로퍼티 선언하기

  ```kotlin
  var StringBuilder.lastChar: Char
  	get() = get(length - 1)
  	set(value: Char) {
          this.setCharAt(length - 1, value)
      }
  ```

  확장 프로퍼티를 사용하는 방법은 멤버 프로퍼티를 사용하는 방법과 같다.

  ```kotlin
  >>> println("Kotlin".lastChar)
  	n
  >>> val sb = StringBuilder("Kotlin?")
  >>> sb.lastChar = '!'
  >>> println(sb)
  	Kotlin!
  ```



---

## 4. 컬렉션 처리: 가변 길이 인자, 중위 함수 호출, 라이브러리 지원

- vararg 키워드를 사용하면 호출 시 인자 개수가 달라질 수 있는 함수를 정의할 수 있다.
- 중위 함수 호출 구문을 사용하면 인자가 하나뿐인 메소드를 간편하게 호출할 수 있다.
- 구조 분해 선언을 사용하면 복합적인 값을 분해해서 여러 변수에 나눠 담을 수 있다.

### 4.1. 자바 컬렉션 API 확장

```kotlin
>>> val strings: List<String> = listOf("first", "second", "fourteenth")
>>> strings.last()
	fourteenth
>>> val numbers: collection<Int> = setOf(1, 14, 2)
>>> numbers.max()
	14
```



### 4.2. 가변 인자 함수: 인자의 개수가 달라질 수 있는 함수 정의

- 가변 길이 인자는 메소드를 호출할 때 원하는 개수만큼 값을 인자로 넘기면 자바 컴파일러가 배열에 그 값들을 넣어주는 기능이다. 코틀린의 가변 길이 인자도 자바와 비슷하지만 문법이 조금 다르다. 타입 뒤에 ...를 붙이는 대신 코틀린에서는 파라미터 앞에 vararg 변경자를 붙인다.

- 이미 배열에 들어있는 원소를 가변 길이 인자로 넘길 때 자바에서는 배열을 그냥 넘기면 되지만 코틀린에서는 배열을 명시적으로 풀어서 배열의 각 원소가 인자로 전달되게 해야 한다. 기술적으로는 **스프레드 연산자**가 그런 작업을 해준다.

  ```kotlin
  fun main(args: Array<String>) {
      val list = listOf("args: ", *args)	// 스프레드 연산자가 배열의 내용을 펼쳐줌
      println(list)
  }
  ```



### 4.3. 값의 쌍 다루기: 중위 호출과 구조 분해 선언

- 맵을 만들려면 mapOf 함수를 사용한다.

  ```kotlin
  val map = mapOf(1 to "one", 7 to "seven", 53 to "fifty-three")
  ```

  여기서 to라는 단어는 코틀린 키워드가 아니다. 이 코드는 **중위 호출**이라는 특별한 방식으로 to라는 일반 메소드를 호출한 것이다.

- 중위 호출 시에는 수신 객체와 유일한 메소드 인자 사이에 메소드 이름을 넣는다. 다음 두 호출은 동일하다.

  ```kotlin
  1.to("one")	// "to" 메소드를 일반적인 방식으로 호출
  1 to "one"	// "to" 메소드를 중위 호출 방식으로 호출
  ```

- 인자가 하나뿐인 일반 메소드나 인자가 하나뿐인 확장 함수에 중위 호출을 사용할 수 있다. 함수를 중위 호출에 사용하게 허용하고 싶으면 infix 변경자를 함수 선언 앞에 추가해야 한다. 

  ```kot
  infix fun Any.to(other: Any) = Pair(this, other)
  ```

  이 to함수는 Pair의 인스턴스를 반환한다. Pair는 코틀린 표준 라이브러리 클래스로, 그 이름대로 두 원소로 이뤄진 순서쌍을 표현한다. 실제로 to는 제네릭 함수짐나 여기서는 설명을 위해 그런 세부 사항을 생략했다.

  Pair의 내용으로 두 변수를 즉시 초기화할 수 있다.

  ```kotlin
  val (number, name) = 1 to "one"
  ```

  이런 기능을 **구조 분해 선언**이라고 부른다. 

  Pair 인스턴스 외 다른 객체에도 구조 분해를 적용할 수 있다. 예를 들어 key와 value라는 두 변수를 맵의 원소를 사용해 초기화할 수 있다. 루프에서도 구조 분해 선언을 활용할 수 있다. joinToString에서 본 withIndex를 구조 분해 선언과 조합하면 컬렉션 원소의 인덱스와 값을 따로 변수에 담을 수 있다.

  ```kotlin
  for ((intdex, element) in collection.withIndex()) {
      println("$index: $element")
  }
  ```

- to함수는 확장 함수다. to를 사용하면 타입과 관계없이 임의의 순서쌍을 만들 수 있다. 이는 to의 수신 객체가 제네릭하다는 뜻이다. 1 to "one", "one" to 1,list to list.size() 등의 호출이 모두 잘 작동한다. 



---

## 5. 문자열과 정규식 다루기

- 코틀린 문자열은 자바 문자열과 같다. 코틀린 코드가 만들어낸 문자여릉ㄹ 아무 자바 메소드에 넘겨도 되며, 자바 코드에서 받은 문자열을 아무 코틀린 표준 라이브러리 함수에 전달해도 전혀 문제없다. 



### 5.1. 문자열 나누기

- 코틀린에서는 자바의 split 대신에 여러 가지 다른 조합의 파라미터를 받는 split 확장 함수를 제공함으로써 혼동을 야기하는 메소드를 감춘다. 정규식을 파라미터로 받는 함수는 String이 아닌 Regex 타입의 값을 받는다. 따라서 코틀린에서는 split 함수에 전달하는 값의 타입에 따라 정규식이나 일반 텍스트 중 어느 것으로 문자열을 분리하는지 쉽게 알 수 있다.

- 마침표나 대시(-)로 문자열을 분리하는 예

  ```kotlin
  >>> println("12.345-6.A".split("\\.|-".toRegex()))	// 정규식을 명시적으로 만듦
  [12, 345, 6, A]
  ```

- 정규식을 처리하는 API는 표준 자바 라이브러리 API와 비슷하지만 좀 더 코틀린답게 변경됐다. 예를 들어 코틀린에서는 toRegex 확장 함수를 사용해 문자열을 정규식으로 변환할 수 있다.

- 구분 문자열을 하나 이상 인자로 받는 함수

  ```kotlin
  >>> println("12.345-6.A".split(".", "-"))
  [12, 345, 6, A]
  ```

  이렇게 여러 문자를 받을 수 있는 코틀린 확장 함수는 자바에 있는 단 하나의 문자만 받을 수 있는 메소드를 대신한다.



---

### 5.2. 정규식과 3중 따옴표로 묶은 문자열

- String 확장 함수를 사용해 경로 파싱하기

  ```kotlin
  fun parsePath(path: String) {
      val directory = path.substringBeforeLast("/")
      val fullName = path.substringAfterLast("/")
      val fileName = fullName.substringBeforeLast(".")
      val extension = fullName.substringAfterLast(".")
      println("Dir: $directory, name: $fileName, ext: $extension")
  }
  >>> parsePath("/Users/yole/kotlin-book/chapter.adoc")
  Dir: /Users/yole/kotlin-book, name: chapter, ext: adoc
  ```

- 코틀린에서는 정규식을 사용하지 않고도 문자열을 쉽게 파싱할 수 있다. 정규식이 필요할 때는 코틀린 라이브러리를 사용하면 더 편하다.

  - 경로 파싱에 정규식 사용하기

    ```kotlin
    fun parsePath(path: String) {
        val regex = """(.+)/(.+)\.(.+)""".toRegex()
        val matchResult = regex.matchEntire(path)
        if (matchResult != null) {
            val (directory, filename, extension) = matchResult.destructured
            println("Dir: $directory, name: $fileName, ext: $extension")
        }
    }
    ```



---

### 5.3. 여러 줄 3중 따옴표 문자열

- 3중 따옴표 문자열에는 줄 바꿈을 표현하는 아무 문자열이나 그대로 들어간다. 따라서 3중 따옴표를 쓰면 줄 바꿈이 들어있는 프로그램 텍스트를 쉽게 문자열로 만들 수 있다.

  - ASCII 아트

    ```kotlin
    val kotlinLogo = """|  //
    					.| //
    					.| / \"""
    >>> println(kotlinLogo.trimMargin("."))
    |  //
    | //
    | / \
    ```

- 3중 따옴표 문자열 안에 문자열 템플릿을 사용할 수도 있다. 그러나 3중 따옴표 문자열 안에서는 이스케이프를 할 수 없기 때문에 문자열 템플릿의 시작을 표현하는 $를 3중 따옴표 문자열 안에 넣을 수 없다는 문제가 생긴다. 3중 따옴표 문자열 안에 $를 넣어야 한다면 `val price = """${'$'}99.0"""`처럼 어쩔 수 없이 문자열 템플릿 안에 '$' 문자를 넣어야 한다.



---

## 6. 코드 다듬기: 로컬 함수와 확장

- 코틀린에서는 함수에서 추출한 함수를 원 함수 내부에 중첩시킬 수 있다. 그렇게 하면 문법적인 부가 비용을 들이지 않고도 깔끔하게 코드를 조직할 수 있다.

- 코드 중복을 보여주는 예제

  ```kotlin
  class User(val id: Int, val name: String, val address: String)
  fun savaUser(user: User) {
      if (user.name.isEmpty()) {		// 필드 검증 중복
          throw IllegalArgumentException(
              "Can't save user ${user.id}: empty Name")
      }
      if (user.address.isEmpty()) {	// 필드 검증 중복
          throw IllegalArgumentException(
              "Can't save user ${user.id}: empty Address")
      }
      // user를 데이터베이스에 저장
  }
  ```

  ```
  >>> saveUser(User(1, "", ""))
  java.lang.IllegalArgumentException: Can't save user 1: empty Name
  ```

  이런 경우 검증 코드를 로컬 함수로 분리하면 중복을 없애는 동시에 코드 구조를 깔끔하게 유지할 수 있다.

- 로컬 함수를 사용해 코드 중복 줄이기

  ```kotlin
  class User(val id: Int, val name: String, val address: String)
  fun savaUser(user: User) {
      fun validate(user: User,
                  value: String,
                   fieldName: String) {
          if (value.isEmpty()) {
              throw IllegalArgumentException(
              	"Can't save user ${user.id}: empty $fieldName")
          }
      }
      validate(user, user.name, "Name")
      validate(user, user.address, "Address")	// 로컬 함수를 호출해서 각 필드를 검증
      // user를 데이터베이스에 저장
  ```

  검증 로직 중복은 사라졌고, 필요하면 User의 다른 필드에 대한 검증도 쉽게 추가할 수 있다.

- 로컬 함수는 자신이 속한 바깥 함수의 모든 파라미터와 변수를 사용할 수 있다.

  - 로컬 함수에서 바깥 함수의 파라미터 접근하기

    ```kotlin
    class User(val id: Int, val name: String, val address: String)
    fun saveUser(user: User) {
        fun validate(value: String, fieldName: String) {
            if (value.isEmpty()) {
                throw IllegalArgumentException(
                "Can't save user ${user.id}: " + "empty $fieldName")
                // 바깥 함수의 파라미터에 직접 접근
            }
        }
        validate(user.name, "Name")
        validate(user.address, "Address")
        // user를 데이터베이스에 저장
    }
    ```

  검증 로직을 User 클래스를 확장한 함수로 만들 수도 있다.

  - 검증 로직을 확장 함수로 추출하기

    ```kotlin
    class User(val id: Int, val name: String, val address: String)
    fun User.validateBeforeSave() {
        fun validate(value: String, fieldName: String) {
            if (value.isEmpty()) {
                throw IllegalArgumentException(
                "Can't save user $id: empty $fieldName")
            }
        }
        validate(name, "Name")
        validate(address, "Address")
    }
    fun saveUser(user: User) {
        user.validateBeforeSave()	// 확장 함수 호출
        // user를 데이터베이스에 저장
    }
    ```

    

---

## 7. 요약

- 코틀린은 자체 컬렉션 클래스를 정의하지 않지만 자바 클래스를 확장해서 더 풍부한 API를 제공한다.
- 함수 파라미터의 디폴트 값을 정의하면 오버로딩한 함수를 정의할 필요성이 줄어든다. 이름 붙인 인자를 사용하면 함수의 인자가 많을 때 함수 호출의 가독성을 더 향상시킬 수 있다.
- 코틀린 파일에서 클래스 멤버가 아닌 최상위 함수와 프로퍼티를 직접 선언할 수 있다. 이를 활용하면 코드 구조를 더 유연하게 만들 수 있다.
- 확장 함수와 플퍼티를 사용하면 외부 라이브러리에 정의된 클래스를 포함해 모든 클래스의 API를 그 클래스의 소스코드를 바꿀 필요 없이 확장할 수 있다. 확장 함수를 사용해도 실행 시점에 부가 비용이 들지 않는다.
- 중위 호출을 통해 인자가 하나 밖에 없는 메소드나 확장 함수를 더 깔끔한 구문으로 호출할 수 있다.
- 코틀린은 정규식과 일반 문자열을 처리할 때 유용한 다양한 문자열 처리 함수를 제공한다.
- 자바 문자열로 표현하려면 수많은 이스케이프가 필요한 문자열의 경우 3중 따옴표 문자열을 사용하면 더 깔끔하게 표현할 수 있다.
- 로컬 함수를 써서 코드를 더 깔끔하게 유지하면서 중복을 제거할 수 있다.

