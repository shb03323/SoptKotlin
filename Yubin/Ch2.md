# 코틀린 기초

## 1. 기본 요소 : 함수와 변수
### 1.1. Hello, World!
 - 함수를 선언할 때 fun 키워드를 사용한다.
 - 파라미터 이름 뒤에 그 파라미터의 타입을 쓴다.
 - 변수를 선언할 때도 마찬가지 방식으로 타입을 지정한다.
 - 함수를 최상위 수준에 정의할 수 있다.
 - 꼭 클래스 안에 함수를 넣을 필요가 없다. -> 자바와 다른 점
 - 배열 처리를 위한 문법이 따로 존재하지 않는다 -> 자바와 다른 점
 - 코틀린 표준 라이브러리는 여러 가지 표준 자바 라이브러리 함수를 간결하게 사용할 수 있게 감싼 wrapper를 제공한다.
   - ex) System.out.println 대신 println 사용
 - 줄 끝에 세미콜론(;)을 붙이지 않아도 된다. -> 최신 프로그래밍 언어 경향
</br>

### 1.2. 함수
 - 함수 선언은 fun 키워드로 시작한다. fun 다음에는 함수 이름이 오고 함수 이름 뒤에는 괄호 안에 파라미터 목록이 온다.   
   함수의 반환 타입은 파라미터 목록의 닫는 괄호 다음에 오는데, 괄호와 반환 타입 사이를 콜론(:)으로 구분해야 한다.   
   ```kotlin
   fun max(a : Int, b : Int) : Int {
      return if (a > b) a else b
   }
   ```
 - *문(statement)과 식(expression)의 구분*    
   : 코틀린에서 if는 문이 아닌 식이다. 식은 값을 만들어내며 다른 식의 하위 요소로 계산에 참여할 수 있는 반면    
   문은 자신을 둘러싸고 있는 가장 안쪽 블록의 최상위 요소로 존재하며 아무런 값을 만들어내지 않는다는 차이가 있다.
   
 - **식이 본문인 함수**    
   위의 함수를 아래와 같이 더 간결하게 표현할 수 있다.
   ```kotlin
   fun max(a : Int, b: Int) : Int = if (a > b) a else b
   ```
   본문이 중괄호로 둘러싸인 함수를 *블록이 본문인 함수*라 부르고,
   등호와 식으로 이뤄진 함수를 *식이 본문인 함수*라고 부른다.
   코틀린에서는 식이 본문인 함수가 자주 쓰인다. 식이 본문인 함수의 경우 굳이 사용자가 반환 타입을 적지 않아도 컴파일러가 함수 본문 식을 분석해서 식의 결과 타입을 함수 반환 타입으로 정해준다.
   이렇게 컴파일러가 타입을 분석해 프로그래머 대신 프로그램 구성 요소의 타입을 정해주는 기능을 *타입 추론(tyep inference)* 이라 부른다.
   </br>
   
### 1.3. 변수
 - 코틀린에서는 키워드로 변수 선언을 시작하는 대신 변수 이름 뒤에 타입을 명시하거나 생략하게 허용한다.
 - 타입을 지정하지 않으면 컴파일러가 초기화 식을 분석해서 초기화 식의 타입을 변수 타입으로 지정한다.
 - 초기화 식을 사용하지 않고 변수를 선언하려면 변수 타입을 반드시 명시해야 한다.
   ```kotlin
   val answer : Int
   answer = 42
   ```
 - **변경 가능한 변수와 변경 불가능한 변수**
   - **val**(value에서 따옴) : 변경 불가능한 참조를 저장하는 변수. val로 선언된 변수는 일단 초기화하고 나면 재대입이 불가능하다. 자바에서의 final 변수에 해당한다.
   - **var**(variable에서 따옴) : 변경 가능한 참조. 변수의 값을 바꿀 수 있다. 자바에서의 일반 변수에 해당한다.
   - 기본적으로 모든 변수를 val 키워드를 사용해 불변 변수로 선언하고, 나중에 꼭 필요할 때에만 var로 변경하는 것이 좋다.
   - val 참조 자체는 불변일지라도 그 참조가 가리키는 객체의 내부 값은 변경될 수 있다.
     ```kotlin
     val languages = arrayListOf("Java")
     languages.add("Kotlin")
     ```
   - var 키워드를 사용하면 변수의 값을 변경할 수 있지만 변수의 타입은 고정돼 바뀌지 않는다. 아래의 코드는 컴파일할 수 없다.
     ```kotlin
     var answer = 42
     answer = "no answer"
     ```
     문자열 리터럴에서 컴파일 오류가 발생한다. 그 타입(String)이 컴파일러가 기대하는 타입(Int)와 다르기 때문이다.
     </br>

### 1.4. 더 쉽게 문자열 형식 지정: 문자열 템플릿
 - 변수 이름 앞에 $를 붙이거나, 식을 ${식}처럼 ${}로 둘러싸면 변수나 식의 값을 문자열 안에 넣을 수 있다.
 - 문자열 템플릿 안에 $로 변수를 지정할 때 변수명 바로 뒤에 한글을 붙여서 사용하면 코틀린 컴파일러는 영문자와 한글을 한꺼번에 식별자로 인식해서 unresolved reference 오류를 발생시킨다.  
   `'${name}님 반가워요!'` 처럼 변수 이름을 { }로 감싸서 해결할 수 있다.
</br>

## 2. 클래스와 프로퍼티
### 2.1. 프로퍼티
 - 자바에서는 필드와 접근자를 한데 묶어 *프로퍼티(property)* 라고 부른다.
 - 코틀린은 프로퍼티를 언어 기본 기능으로 제공하며, 코틀린 프로퍼티는 자바의 필드와 접근자 메소드를 완전히 대신한다. 클래스에서 프로퍼티를 선언할 때는 변수 선언 방법과 마찬가지로 val이나 var를 사용한다.
 - *클래스 안에서 변경 가능한 프로퍼티 선언하기*   
   ```kotlin
   class Person(
      val name: String, // 읽기 전용 프로퍼티. 코틀린은 (비공개) 필드와 필드를 읽는 단순한 (공개) 게터를 만들어낸다.
      var isMarried: Boolean  // 쓸 수 있는 프로퍼티. 코틀린은 (비공개) 필드, (공개)게터, (공개)세터를 만들어낸다.
   )
   ```
   코틀린은 값을 저장하기 위한 비공개 필드와 그 필드에 값을 저장하기 위한 세터, 필드의 값을 위한 게터로 이뤄진 간단한 디폴트 접근자 구현을 제공한다.
  - *자바에서 Person 클래스를 사용하는 방법*
    ```java
    Person person = new Person("Bob", true);
    System.out.println(person.getName());
    System.out.println(person.isMarried());
    ```
  - *코틀린에서 Person 클래스 사용하는 방법*
    ```kotlin
    val person = Person("Bob", true)
    println(person.name)
    println(person.isMarried)
    ```
    게터를 호출하는 대신 프로퍼티를 직접 사용했다. 로직은 자바에서 Person 클래스를 사용할 때와 동일하지만 코드는 더 간결해졌다.
    
### 2.2. 커스텀 접근자
 - *프로퍼티의 접근자를 직접 작성하는 방법*
   ```kotlin
   class Rectangle (val height: Int, val width: Int) {
      val isSquare: Boolean
         get() { // 프로퍼티 게터 선언
            return height == width
         }
   }
   ```
   - isSquare 프로퍼티에는 자체 값을 저장하는 필드가 필요 없다. 클라이언트가 프로퍼티에 접근할 때마다 게터가 프로퍼티 값을 매번 다시 계산한다.
   또한, get() = height == width 라고 해도 된다.
   - 파라미터가 없는 함수를 정의하는 방식과 커스텀 게터를 정의하는 방식의 현이나 성능상 차이는 없다. 가독성에서 차이가 날 뿐이다.   

   
### 2.3. 코틀린 소스코드 구조: 디렉터리와 패키지
 - 모든 코틀린 파일의 맨 앞에 package문을 넣을 수 있다. package문을 넣으면 그 파일 안에 있는 모든 선언(클래스, 함수, 프로퍼티 등)이 해당 패키지에 들어간다.
 - 같은 패키지에 속해 있다면 다른 파일에서 정의한 선언일지라도 직접 사용할 수 있다. 반면 다른 패키지에 정의한 선언을 사용하려면 임포트를 통해 선언을 불러와야 한다.
 - 자바와 마찬가지로 임포트문은 파일의 맨 앞에 와야 하며 import 키워드를 사용한다.
 - *클래스와 함수 선언을 패키지에 넣기*    
   ```kotlin
   package geometry.shapes  // 패키지 선언
   import java.util.Random  // 표준 자바 라이브러리 클래스를 임포트한다.
   class Rectangle(val height: Int, val width: Int) {
      val isSquare : Boolean
         get() = height == width
   }
   fun createRandomRectangle() : Rectangle {
      val random = Random()
      return Rectangle(random.nextInt(), random.nextInt())
   }
 - 코틀린에서는 클래스 임포트와 함수 임포트에 차이가 없으며, 모든 선언을 import 키워드로 가져올 수 있다. 최상위 함수는 그 이름을 써서 임포트할 수 있다.    
   *다른 패키지에 있는 함수 임포트하기*      
   ```kotlin
   package geometry.example
   import geometry.shapges.createRandomRectangle // 이름으로 함수 임포트하기
   fun main(args: Array<String>) {
      println(createRandomRectangle().isSquare) // 아주 드물게 "true"가 출력됨
   }
   ```
 - 자바에서는 디렉터리 구조가 패키지 구조를 그대로 따라야 하지만 코틀리넹서는 패키지 구조와 디렉터리 구조가 맞아 떨어질 필요는 없다. 코틀린에서는 여러 클래스를 한 파일에 넣을 수 있고, 파일의 이름도 마음대로 정할 수 있다. 하지만 대부분의 경우 자바와 같이 패키지별로 디렉터리를 구성하는 편이 낫다.
</br>

## 3. 선택 표현과 처리 : enum과 when
### 3.1. enum 클래스 정의
 - *간단한 enum 클래스 정의하기*
   ```kotlin
   enum class Color {
      RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
   }
   ```
 - enum 클래스 안에도 프로퍼티나 메소드를 정의할 수 있다.   
   *프로퍼티와 메소드가 있는 enum 클래스 선언하기*
   ```kotlin
   enum class Color(
      val r: Int, val g: Int, val b:Int  // 상수의 프로퍼티를 정의한다.
   ) {
      RED(255, 0, 0), ORANGE(255, 165, 0),  // 각 상수를 생성할 때 그에 대한 프로퍼티 값을 지정
      YELLOW(255, 255, 0), GREEN(0, 255, 0), BLUE(0, 0, 255),
      INDIGO(75, 0, 130), VIOLET(238, 130. 238);  // 반드시 세미콜론 사용
      fun rgb() = (r * 256 + g) * 256 + b  // enum 클래스 안에서 메소드를 정의
   }
   >>> println(Color.BLUE.rgb())
   255
   ```   
### 3.2. when으로 enum 클래스 다루기
 - 자바의 switch에 해당하는 코틀린 구성 요소가 when이다.
 - if와 마찬가지로 when도 값을 만들어내는 식이다. 따라서 식이 본문인 함수에 when을 바로 사용할 수 있다.         
   *when을 사용해 올바른 enum 값 찾기*
   ```kotlin
   fun getMnemonic(color: Color) =   // 함수의 반환 값으로 when 식을 직접 사용
      when (color) {   // 색이 특정 enum 상수와 같을 때 그 상수에 대응하는 문자열을 돌려줌
         Color.RED -> "Richard"
         Color.ORANGE -> "Of"
         Color.Yellow -> "York"
         Color.GREEN -> "Gave"
         Color.Blue -> "Battle"
         Color.INDIGO -> "IN"
         Color.VIOLET -> "Vain"
      }
   >>> println(getMnemonic(Color.BLUE))
   Battle
   ```
   한 분기 안에서 여러 값을 매치 패턴으로 사용할 수도 있다. 그럴 경우 값 사이를 콤마(,)로 분리한다.
   
### 3.3. when과 임의의 객체를 함께 사용
 - 분기 조건에 상수(enum 상수나 숫자 리터럴)만을 사용할 수 있는 자바 switch와 달리 코틀린 when의 분기 조건은 임의의 객체를 허용한다. 
 - *when의 분기 조건에 여러 다른 객체 사용하기*
   ```kotlin
   fun mix(c1: Color, c2: Color) = 
      when (setOf(c1, c2)) {  // when 식의 인자로 아무 객체나 사용할 수 있음
         setOf(RED, YELLOW) -> ORANGE // 두 색을 혼합해서 다른 색을 만들 수 있는 경우를 열거
         setOf(YELLOW, BLUE) -> GREEN
         setOF(BLUE, VIOLET) -> INDIGO
         else -> throw Exception("Dirty color")
      }
   >>> println(mix(BLUE, YELLOW))
   GREEN
   ```
 - when의 분기 조건 부분에 식을 넣을 수 있기 때문에 많은 경우 코드를 더 간결하게 작성할 수 있다.

### 3.4. 인자 없는 when 사용
 - 인자가 없는 when 식을 사용하면 불필요한 객체 생성을 막을 수 있다.
 - *인자가 없는 when*
   ```kotlin
   fun mixOptimized(c1: Color, c2: Color) = 
      when {
         (c1 == RED && c2 == YELLOW) ||
         (c1 == YELLOW && c2 == RED) -> ORANGE
         (c1 == YELLOW && c2 == BLUE) ||
         (c1 == BLUE && c2 == YELLOW) -> GREEN
         (c1 == BLUE && c2 == VIOLET) || 
         (c1 == VIOLET && c2 == BLUE) -> INDIGO
         else -> throw Exception("Dirty color")
      }
   >>> println(mixOptimized(BLUE, YELLOW))
   GREEN
   ```

### 3.5. 스마트 캐스트: 타입 검사와 타입 캐스트를 조합
 - 코틀린에서는 프로그래머 대신 컴파일러가 캐스팅을 해준다. 어떤 변수가 원하는 타입인지 일단 is로 검사하고 나면 굳이 변수를 캐스팅하지 않아도 검사한 타입의 변수처럼 사용할 수 있다. 컴파일러가 캐스팅을 수행해주는 것이다. 이를 **스마트 캐스트**라고 부른다.

### 3.6. 리팩토링 : if를 when으로 변경
 - *식을 표현하는 클래스 계층*
   ```kotlin
   interface Expr
   class Num(val value: Int): Expr  // value라는 프로퍼티만 존재하는 단순한 클래스로 Expr 인터페이스를 구현
   class Sum(val left: Expr, val right: Expr) : Expr // Expr 타입의 객체라면 어떤 것이나 Sum 연산의 인자가 될 수 있다. 따라서 Num이나 다른 Sum이 인자로 올 수 있다.
   ``` 
   </br>
   
   *if 연쇄를 사용해 식을 계산하기*
   ```kotlin
   fun eval(e: Expr) : Int {
      if (e is Num) {
         val n = e as Num
         return n.value
      }
      if (e is Sum) {
         return eval(e.right) + eval(e.left)
      }
      throw IllegalArgumentException("Unknown expression")
   }
   >>> println(eval(Sum(Sum(Num(1), Num(2)), Num(4))))
   7
   ```
   </br>
   
   코틀린에서는 if가 값을 만들어내기 때문에 자바와 달리 3항 연산자가 따로 없다. 이런 특성을 사용하면 eval 함수에서 return문과 중괄호를 없애고 if 식을 본문으로 사용해 더 간단하게 만들 수 있다.</br>
   
   *값을 만들어내는 if 식*
   ```kotlin
   fun eval(e: Expr): Int =
      if (e is Num) {
         e.value
      } else if (e is Sum) {
         eval(e.right) + eval(e.left)
      } else {
         throw IllegalArgumentException("Unknown expression")
      }
   >>> println(eval(Sum(Num(1), Num(2))))
   3
   ```
   </br>
   
   *if 중첩 대신 when 사용하기*
   ```kotlin
   fun eval(e: Expr) : Int = 
      when (e) {
         is Num ->  // 인자 타입을 검사하는 when 분기
            e.value // 이 부분에 스마트 캐스트가 쓰임 
         is Sum ->
            eval(e.right) + eval(e.left)
         else ->
            throw IllegalArgumentException("Unknown expression")
      }
   ```
   </br>
   
### 3.7. if와 when의 분기에서 블록 사용
 - if나 when 모두 분기에 블록을 사용할 수 있다. 그런 경우 블록의 마지막 문장이 블록 전체의 결과가 된다.    
 *분기에 복잡한 동작이 들어가 있는 when 사용하기*
 ```kotlin
 fun evalWithLogging(e: Expr) : Int =
    when (e) {
       is Num -> {
          println("num: ${e.value}")
          e.value // 이 식이 블록의 마지막 식이므로 e의 타입이 Num이면 e.value가 반환됨
       }
       is Sum -> {
          val left = evalWithLogging(e.left)
          val right = evalWithLogging(e.right)
          println("sum: $left + $right")
          left + right // e의 타입이 Sum이면 이 식의 값이 반환됨
       }
       else -> throw IllegalArgumentException("Unknown expression")
    }
 ```
 - '블록의 마지막 식이 블록의 결과'라는 규칙은 블록이 값을 만들어내야 하는 경우 항상 성립한다.   
    하지만 식이 본문인 함수는 블록을 본문으로 가질 수 없고 블록이 본문인 함수는 내부에 return문이 반드시 있어야 한다.
</br>

## 4. 대상을 이터레이션: while과 for루프
### 4.1. while 루프
 - 코틀린에는 while과 do-while 루프가 있다. 두 루프의 문법은 자바와 같다.
### 4.2. 수에 대한 이터레이션: 범위와 수열
 - 코틀린에는 자바의 for 루프에 해당하는 요소가 없는 대신 범위(range)를 사용한다.
 - 범위는 기본적으로 두 값으로 이뤄진 구간이다. 보통은 그 두 값은 정수 등의 숫자 타입의 값이며, .. 연산자로 시작 값과 끝 값을 연결해서 범위를 만든다.
 - 코틀린의 범위는 폐구간(닫힌 구간) 또는 양끝을 포함하는 구간이다.     
   ex) `val oneToTen = 1..10`
   에서 두번째 값, 즉 10이 항상 범위에 포함된다.
 - 어떤 범위에 속한 값을 일정한 순서로 이터레이션하는 경우를 **수열** 이라고 부른다.

### 4.3. 맵에 대한 이터레이션
 - *맵을 초기화하고 이터레이션하기*
 ```Kotlin
 val binaryReps = TreeMap<Char, String>()  // 키에 대해 정렬하기 위해 TreeMap을 사용
 for (c in 'A'..'F') {  // A부터 F까지 문자의 범위를 사용해 이터레이션함
    val binary = Integer.toBinaryString(c.toInt()) // 아스키 코드를 2진 표현으로 바꿈
    binaryReps[c] = binary // c를 키로 c의 2진 표현을 맵에 넣음
 }
 for ((letter, binary) in binaryReps) { // 맵에 대해 이터레이션함. 맵의 키와 값을 두 변수에 각각 대입
    println("$letter = $binary)
 }
 ```
  - .. 연산자를 숫자 타입의 값뿐 아니라 문자 타입의 값에도 적용할 수 있다.
  - for 루프를 사용해 이터레이션 하려는 컬렉션의 원소를 풀어서 letter와 binary라는 두 변수에 저장한다. letter에는 키가 들어가고, binary에는 2진 표현이 들어간다.
  - 맵에 사용했던 구조 분해 구문을 맵이 아닌 컬렉션에도 활용할 수 있다. 그런 구조 분해 구문을 사용하면 원소의 현재 인덱스를 유지하면서 컬렉션을 이터레이션할 수 있다. 인덱스를 저장하기 위한 변수를 별도로 선언하고 루프에서 매법 그 변수를 증가시킬 필요가 없다.
    ```Kotlin
    val list = arrayListOf("10", "11", "1001")
    for ((index, element) in list.withIndex()) {
       println("$index: $element)
    }
    ```
    </br>
    
### 4.4. in으로 컬렉션이나 범위의 원소 검사
 - in 연산자를 사용해 어떤 값이 범위에 속하는지 검사할 수 있다. 반대로 !in을 사용하면 어떤 값이 범위에 속하지 않는지 검사할 수 있다.
 - *in을 사용해 값이 범위에 속하는지 검사하기*
   ```Kotlin
   fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'
   fun isNotDigit(c: Char) = c !in '0'..'9'
   >>>println(isLetter('q'))
   true
   >>>println(isNotDigit('x'))
   true
   ```
 - *when에서 in 사용하기*
   ```Kotlin
   fun recognize(c: Char) = when (c) {
      in '0'..'9' -> "It's a digit!"  // c 값이 0~9 사이에 있는지 검사
      in 'a'..'z', in 'A'..'Z' -> "It's a letter!"  // 여러 범위 조건을 함께 사용해도 됨
      else -> "I don't know.."
   }
   >>> println(recognize('8'))
   It's a digit!
   ```
   </br>
   
## 5. 코틀린의 예외 
### 5.1. try, catch, finally
 - 자바와 마찬가지로 예외를 처리하려면 try와 catch, finally 절을 함께 사용한다.
 - *자바와 마찬가지로 try 사용하기*
   ```Kotlin
   fun readNumber(reader: BufferedReader) : Int? { // 함수가 던질 수 있는 예외를 명시할 필요 X
      try {
         val line = reader.readLine()
         return Integer.parseInt(line)
      }
      catch (e: NumberFormatException) { // 예외 타입을 :의 오른쪽에 씀
         return null
      }
      finally {  // finally는 자바와 똑같이 작용
         reader.close()
      }
   }
   >>> val reader = BufferedReader(StringReader("239"))
   >>> println(readNumber(reader))
   239
   ```
    - 자바 코드와 가장 큰 차이는 throws절이 코드에 없다는 점이다. 자바에서 함수를 작성할 때는 함수 선언 뒤에 throws IOException을 붙여야 한다. 자바에서는 IOException이 checked exception이고 checked exception을 명시적으로 처리해야 한다. 코틀린에서는 함수가 던지는 예외를 지정하지 않고 발생한 예외를 잡아내도 되고 잡아내지 않아도 된다.

### 5.2. try를 식으로 사용
 - *try를 식으로 사용하기*
   ```Kotlin
   fun readNumber(reader: BufferedReader) {
      val number = try {
         Integer.parseInt(reader.readLine()) // 이 식의 값이 "try" 식의 값이 됨
      } catch (e: NumberFormatException) {
         return
      }
      println(number)
   }
   >>> val reader = BufferedReader(StringReader("not a number")
   >>> readNumber(reader) // 아무것도 출력되지 않음
   ```
 - 코틀린의 try 키워드는 if나 when과 마찬가지로 식이다. 따라서 try의 값을 변수에 대입할 수 있다.   
   if와 달리 try의 본문을 반드시 중괄호 {}로 둘러싸야 한다. 다른 문장과 마찬가지로 try의 본문도 내부에 여러 문장이 있으면 마지막 식의 값이 전체 결과 값이다.
</br>

## 6. 요약
 - 함수를 정의할 때 fun 키워드를 사용한다. val과 var는 각각 읽기 전용 변수와 변경 가능한 변수를 선언할 때 쓰인다.
 - 문자열 템플릿을 사용하면 문자열을 연결하지 않아도 되므로 코드가 간결해진다.    
   변수 이름 앞에 $를 붙이거나, 식을 ${식}처럼 ${}로 둘러싸면 변수나 식의 값을 문자열 안에 넣을 수 있다.
 - 코틀린에서는 값 객체 클래스를 아주 간결하게 표현할 수 있다.
 - 다른 언어에도 있는 if는 코틀린에서 식이며, 값을 만들어낸다.
 - 코틀린 when은 자바의 switch와 비슷하지만 더 강력하다.
 - 어떤 변수의 타입을 검사하고 나면 굳이 그 변수를 캐스팅하지 않아도 검사한 타입의 변수처럼 사용할 수 있다. 
   그런 경우 컴파일러가 스마트 캐스트를 활용해 자동으로 타입을 바꿔준다.
 - for, while, do-while 루프는 자바가 제공하는 같은 키워드의 기능과 비슷하다. 하지만 코틀린의 for는 자바의 for보다 더 편리하다.    
   특히 맵을 이터레이션하거나 이터레이션하면서 컬렉션의 원소와 인덱스를 함께 사용해야 하는 경우 코틀린의 for가 더 편리하다.
 - `1..5`와 같은 식은 범위를 만들어낸다. 범위와 수열은 코틀린에서 같은 문법을 사용하며, for 루프에 대해 같은 추상화를 제공한다.   
   어떤 값이 범위 안에 들어있거나 들어있지 않은지 검사하기 위해서 in이나 !in을 사용한다.
 - 코틀린 예외 처리는 자바와 비슷하다. 다만 코틀린에서는 함수가 던질 수 있는 예외를 선언하지 않아도 된다.
