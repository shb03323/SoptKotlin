# 2. 코틀린 기초



## 2.1 기본 요소 : 함수와 변수

### 2.1.1 Hello, World!

---

```kotlin
fun main(args: Array<String>){
    println("Hello, world")
}
```

- **fun** 키워드로 함수 선언
- 파라미터 이름 : 파라미터 타입으로 지정
- 함수를 **최상위** 수준에서 정의할 수 있음 (함수가 클래스 밖에 있어도 됨)
- **배열** 처리를 위한 문법이 따로 존재하지 않고, 일반적인 클래스와 마찬가지임
- 출력 메소드는 **println** (wrapper를 통해 표준 자바 라이브러리 함수를 간결하게 사용할 수 있도록 지원)
- **세미콜론**을 붙이지 않아도 된다



### 2.1.2 함수

---

```kotlin
fun max(a: Int, b: Int) : Int {		//파라미터, 반환 타입
    return if (a > b) a else b		
}
```

- 코틀린에서는 if가 문장이 아닌 결과를 만드는 식이다.

  - statement (문)
    자신을 둘러싸고 있는 가장 안쪽 블록의 최상위 요소로 존재하며, 아무런 값을 만들어내지 않는다

  - expression (식)

    값을 만들어 내며 다른 식의 하위 요소로 계산에 참여할 수 있다



- ##### 식이 본문인 함수

  ```kotlin
  fun max(a: Int, b: Int): Int = if (a > b) a else b
  ```

  - 코틀린에서는 중괄호와 return을 없애고 등호(=)를 사용하여 간결하게 함수를 표현할 수 있다.

  - 중괄호로 둘러싸인 함수 : **블록이 본문인 함수**

  - 등호와 식으로 이뤄진 함수 : **식이 본문인 함수**

  - **타입추론**을 통해 반환 타입을 생략할 수 있다.

    ```kotlin
    fun max(a: Int, b: Int) = if (a > b) a else b
    ```



### 2.1.3 변수

---

- ##### immutable 변수 : val(value)

  - 일단 초기화하고 나면 재대입이 불가능하다. (자바에서 final)
  - 블록을 실행할 때 한 번만 초기화되어야 한다.
  - 컴파일러가 초기화 문장이 하나만 실행됨을 확인할 수 있다면, 조건에 따라 val 값을 여러 값으로 초기화할 수 있다.
  - 참조 자체는 불변일지라도 객체 내부 값은 변경될 수 있다.

  

- ##### mutable 변수 : var(variable)

  - 값을 변경할 수 있다.
  - 변수 타입은 고정되어 바뀌지 않는다. (type mismatch 컴파일 오류 발생)
  - 변환 함수 또는 값을 강제 타입 변환하여 다른 타입을 사용할 수 있다.

- 변수 선언 시 타입을 명시하지 않아도 타입추론을 지원한다. 하지만, 초기화 식이 없을 경우에는 변수 타입을 반드시 명시해야한다.

  ```kotlin
  val answer = 42	//타입 추론
  val question: Int	//초기화하지 않기 때문에 변수 타입 명시 필요
  ```



### 2.1.4 문자열 템플릿

---

```kotlin
fun main(args: Array<String>){
    val name = if (args.size > 0) args[0] else "Kotlin"
    println("Hello, $name!")	//문자열 템플릿을 이용해 name 변수를 문자열 리터럴 안에서 사용
}
```

- 문자열 리터럴 내부에서 **$변수**의 방식으로 문자열 템플릿을 사용할 수 있다.

- 컴파일 시점에 정적으로 검사된다.

- $문자를 문자열에 넣고 싶다면 \를 사용해 이스케이프 시켜야 한다. 

- 중괄호를 이용해 식(expression)도 문자열 템플릿에 넣을 수 있다.

  ```kotlin
  println("Hello, ${args[0]}!")
  ```

  - 중괄호 내부에서 큰 따옴표를 이용해 리터럴 문자열을 사용할 수 있다.

> 변수 이름에 한글이 들어갈 수 있기 때문에, Hello, $name님 같은 경우 '님'까지 변수 명으로 인식한다. 이 경우에는 중괄호를 사용하여 명확하게 구분해준다.





## 2.2 클래스와 프로퍼티

- 코틀린의 기본 가시성은 public이다.

- 코드 없이 데이터만 저장하는 클래스인 **값 객체** (value object)를 간결하게 기술할 수 있도록 지원한다.

- new를 사용하지 않고 생성자를 호출한다.

  ```kotlin
  class Person(val name: String)
  ```
  
  


### 2.2.1 프로퍼티

---

- ##### 프로퍼티

  필드와 접근자(getter, setter)를 묶은 것

- 코틀린에서는 프로퍼티를 언어 기본 기능으로 제공하며, 코틀린 프로퍼티는 자바의 필드와 접근자 메소드를 완전히 대신한다. (자바는 필드에 데이터를 저장하지만, 코틀린은 필드와 게터/세터를 묶은 프로퍼티를 기본으로 제공한다)
- 프로퍼티를 사용하면 자동으로 알맞는 게터/세터를 통해 필드에 접근한다.

```kotlin
class Person(
	val name: String,	// 읽기 전용 프로퍼티, (private) 필드 & (public) getter
	var isMarried: Boolean	//쓸 수 있는 프로퍼티, (private) 필드 & (public) getter/setter
)
```

- 자바-코틀린 변환기를 통해 자바에서 작성된 게터/세터와 코틀린의 프로퍼티가 자동으로 변환된다.

  - ###### getter/setter 이름 규칙

    - 게터

      이름 앞에 get을 붙인다.

      단, is로 시작하는 프로퍼티에는 get을 붙이지 않고 그대로 사용한다.

    - 세터

      이름 앞에 set을 붙인다.

      단, is로 시작하는 프로퍼티에는 is를 set으로 변경하여 사용한다.

```kotlin
val person = Person("Bob", false)	//new 키워드 없이 생성자를 호출한다
println(person.name)	//프로퍼티 이름을 직접 사용해도 코틀린이 자동으로 게터를 호출한다.
println(person.isMarried)	
```

- ###### backing field

  - 프로퍼티의 값을 저장하기 위한 필드
  - 커스텀 게터를 작성하여 프로퍼티 값을 바로 계산하도록 만들 수 있다.



##### 🟢 생성자에 val, var의 유무 차이 

```kotlin
class Person(val name: Stirng)	// 1
class Person(name: String)		// 2
```

 1. 멤버 변수로 치환된다.

    ```java
    /* 1번이 변환된 자바 코드 */
    public final class Person{
        @NotNull
        private final String name;
        
        @NotNull
        public final String getName(){
            return this.name;
        }
        
        public Person(@NotNull String name){
            Intrinsics.checkParameterIsNotNull(name, "name");
            super();
            this.name = name;
        }
    }
    ```

 2. 생성자 또는 프로퍼티를 초기화하는 식에서만 사용 가능하다.

    ```java
    /* 2번이 변환된 자바 코드 */
    public final class Person{
        public Person(@NotNull String name){
            Intrinsics.checkParameterIsNotNull(name, "name");
            super();
        }
    }
    ```

    이처럼 생성자의 파라미터로 전달되기 때문에 생성자 외 다른 메소드에서는 사용할 수 없다. (프로퍼티가 되지 못한다)	

[참고]: https://wooooooak.github.io/kotlin/2019/05/24/property/	"코틀린 프로퍼티"



### 2.2.2 커스텀 접근자

---

프로퍼티의 접근자를 직접 작성할 수 있다.

```kotlin
class Rectangle (val height: Int, val width: Int){
	val isSquare: Boolean	// backing field 없이 게터만 존재하는 프로퍼티
    	get(){
			return height == width
        }
}
```

위의 예시는 기존의 파라미터가 없는 함수를 프로퍼티를 통해 작성한 것으로 둘 중 어느 방식을 사용해도 괜찮다.

또한 get() = height == width로 작성하여 더 간결하게 표현할 수 있다.



### 2.2.3 디렉터리와 패키지

---

- 같은 패키지에 속해 있다면 다른 파일에서 정의한 선언도 임포트를 통해 직접 사용할 수 있다.

- 코틀린에서는 클래스뿐만 아니라 함수도 같은 방법으로 임포트할 수 있다.

- ###### star import

  패키지 이름 뒤에 .*를 추가하여 패키지 안의 모든 선언을 임포트할 수 있다.

  클래스뿐만 아니라 최상위에 정의된 함수나 프로퍼티까지 모두 불러오는 점을 유의해야 한다.

- 패키지는 계층적 구조를 이루며, 패키지별로 디렉토리를 구성하는 것이 권장된다. (자바 방식을 따라서 마이그레이션할 때의 문제를 방지한다)

```kotlin
package geometry.shapes
import java.util.Random

class Rectangle(val height: Int, val width: Int){
    val isSquare: Boolean
    	get() = height == width
}
fun createRandomRectangle() : Rectangle{
    val random = Random()
    return Rectangle(random.nextInt(), random.nextInt())
}
```

​	클래스와 함수 선언을 패키지에 넣은 예제이다.

```kotlin
package geometry.example
import geometry.shapes.createRandomRectangle

fun main(args: Array<String>){
    println(createRandomRectangle().isSquare)	// 랜덤으로 생성한 직사각형이 정사각형인지 출력 (true/false)
}
```

​	앞서 만든 함수를 임포트해서 사용하는 것이다.



## 2.3 enum과 when

코틀린에는 자바의 switch를 대치한 when이 있다. enum, when과 smart cast에 대해서 살펴본다.



### 2.3.1 enum 클래스 정의

---

- ##### enum

  ```kotlin
  enum class Color {
      RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
  }
  ```

  - 코틀린에서 enum은 class 앞에 있을 때만 특별한 의미를 지니는 **soft keyword**이다.

  - enum 클래스 안에도 프로퍼티나 메소드를 정의할 수 있다.

    ```kotlin
    enum classs Color (
        val r: Int, val g: Int, val b: Int 		// 상수의 프로퍼티 정의
    ) {
        RED(255,0,0), ORANGE(255,165,0),	// 각 상수를 생성할 때 그에 대한 프로퍼티 값 지정
        YELLOW(255,255,0), GREEN(0,255,0), BLUE(0,0,255), 
        INDIGO(75,0,130), VIOLET(238,130,238);	// 반드시 세미콜론 사용해야 함
        
        fun rgb() = (r * 256 + g) * 256 + b		// enum 클래스 안에서 메소드 정의
    }
    ```

  - enum 클래스 안에 메소드를 정의하는 경우 반드시 enum <u>상수 목록과 메소드 정의 사이</u>에 세미콜론을 넣어야 한다.

  



### 2.3.2 when으로 enum 클래스 다루기

---

if와 마찬가지로 when도 값을 만들어내는 식이다.

switch문과 달리 각 분기의 끝에 break를 넣지 않아도 된다.

```kotlin
fun getMnemonic(color: Color) = 
	when (color) {
        Color.RED -> "Richard"
        Color.ORANGE -> "Of"
        Color.YELLO -> "York"
        Color.GREEN -> "Gave"
        Color.BLUE -> "Battle"
        Color.INDIGO -> "In"
        Color.VIOLET -> "Vain"
    }
```



한 분기 안에서 여러 패턴을 사용할 때는 콤마로 구분한다.

```kotlin
fun getWarmth(color: Color) = when(color){
    Color.RED, Color.ORANGE. Color.YELLOW -> "warm"
    Color.GREEN -> "neutral"
    Color.BLUE, Color.INDIGO, Color.VIOLET -> "cold"
}
```



Color enum 클래스를 임포트하여 코드를 더 간결하게 만들 수 있다.

```kotlin
import ch02.colors.Color	// 다른 패키지에서 정의한 Color 클래스를 임포트한다.
import ch02.colors.Color.*	// enum 상수를 모두 임포트한다.
```

위처럼 임포트하여 enum 상수를 Color.상수가 아닌 상수 이름만 사용할 수 있다.



### 2.3.3 when과 임의의 객체를 함께 사용

---

- ##### setOf

  여러 객체를 포함하는 집합인 Set 객체로 만드는 함수

- ##### Set

  원소가 모여 있는 컬렉션으로, 각 원소의 순서는 중요하지 않다.

when 식은 인자 값과 매치하는 조건 값을 찾을 때까지 각 분기를 검사한다. 객체 사이를 매치할 때는 **동등성(equality)**를 사용한다.

```kotlin
fun mix(cl: Color, c2: Color) = 
	when (setOf(c1, c2)) {				// when은 인자로 받은 개겣가 각 분기 조건에 있는 객체와 같은지 테스트한다.
        setOf(RED, YELLOW) -> ORANGE
        setOf(YELLOW, BLUE) -> GREEN
        setOf(BLUE, VIOLET) -> INDIGO
        else -> throw Exception("Dirty Color")	// 매치되는 분기 조건이 없을 경우
    }
```



### 2.3.4 인자 없는 when 사용

---

위의 예제에서 분기 조건에서 비교를 할 때마다 Set 객체를 생성하기 때문에, 인자가 없는 when 식을 사용해 불필요한 가비지 객체가 늘어나는 것을 막을 수 있다.

자주 호출되는 함수일 때 사용하며 코드의 가독성은 떨어지지만 성능은 향상된다.

```kotlin
fun mixOptimized(c1: Color, c2: Color) = 
	when {
        (c1 == RED && c2 == YELLOW) ||
        (c1 == YELLO && c2 == RED) -> 
        	ORANGE
        
        /* ... */
        
       	else -> throw Exception("Dirty Color")
    }
```



인자 없는 when을 사용할 때는 각 분기의 조건이 불리언 결과를 계산하는 식이어야 한다.



### 2.3.5 스마트 캐스트 : 타입 검사와 타입 캐스트를 조합

---

- 클래스가 구현하는 인터페이스를 지정하기 위해 콜론(:) 뒤에 인터페이스 이름을 사용한다.



```kotlin
interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr
```

> 간단한 산술식을 계산하는 함수를 예제로 사용한다. 식을 트리 구조로 저장하며, 노드는 Sum 또는 Num이다.
>
> Num은 항상 leaf 노드이며, Sum은 non-terminal 노드이다. (두 개의 자식 노드를 가지고 있다)
>
> 식을 위한 Expr 인터페이스가 있고, Sum과 Num 클래스는 Expr 인터페이스를 구현한다.
>
> Expr은 아무 메소드도 선언하지 않으며, 여러 타입의 식 객체를 아우르는 공통 타입 역할만 수행한다.



- ##### is

  자바의 instanceof 처럼 변수 타입을 검사한다.

- ##### 스마트 캐스트 (smart cast)

  is로 검사하면, 컴파일러가 자동으로 캐스팅을 수행해준다.

  - 해당 프로퍼티는 반드시 val 이어야 한다.
  - 해당 프로퍼티에 커스텀 접근자를 사용해서는 안된다.



```kotlin
fun eval(e: Expr) : Int {
    if (e is Num){
        val n = e as Num	// as를 사용하여 명시적으로 e를 Num으로 캐스팅하였다. (is에서 스마트 캐스팅이 되기 때문에 불필요한 중복)
    	return n.value
    }
    if (e is Sum){
        return eval(e.right) + eval(e.left)	// 스마트 캐스트를 사용하기 때문에 Sum 클래스의 프로퍼티로 접근이 가능하다.
    }
}
```



### 2.3.6 리팩토링: if를 when으로 변경

---

코틀린에서는 if가 표현식(expression)으로, 값을 만들어 내기 때문에 3항 연산자가 따로 없다. 

```kotlin
fun eval(e: Expr) : Int = 
	if(e is Num)
        e.value
    else if(e is Sum)
        eval(e.right) + eval(e.left)
    else
        throw IllegalArgumentException("Unknown expression")

```

이 코드를 when을 사용해 더 다듬을 수 있다.



```kotlin
fun eval(e: Expr) : Int = 
	when(e) {
        is Num ->
        	e.value
        is Sum ->
        	eval(e.right) + eval(e.left)
        else ->
        	throw IllegalArgumentException("Unknown expression")
    }
```



### 2.3.7 if와 when의 분기에서 블록 사용

---

if와 when 모두 분기에서 중괄호를 사용해 블록을 사용할 수 있다.

이 때, <u>블록의 마지막 식이 블록의 결과</u>이다.

```kotlin
fun evalWithLogging(e: Expr): Int = 
	when(e){
        is Num->{
        	println("num: ${e.value}")
        	e.value			// 블록의 결과
        }
        is Sum->{
            val left = evalWithLogging(e.left)
            val right = evalwithLogging(e.right)
            println("sum: $left + $right")
            left + right	// 블록의 결과
        }
        else -> throw IllegalArgumentException("Unknown expression")
    }
```





## 2.4 대상을 이터레이션: while과 for 루프

 

### 2.4.1 while 루프

---

while과 do-while 루프가 있으며, 자바의 문법과 다르지 않다.

```kotlin
while (조건) {
	/* ... */
}
do {
	/* ... */
} while(조건)
```



### 2.4.2 수에 대한 이터레이션: 범위와 수열

---

자바에서 초깃값, 증가 값, 최종 값을 대신하여 코틀린에서는 범위(range)를 사용한다.

- ##### 범위 (range)

  코틀린의 범위는 **폐구간**이며, 이는 양끝을 포함한다는 뜻이다.

- ##### 수열 (progression)

  어떤 범위에 속한 값을 일정한 순서로 이터레이션하는 경우
  
- ##### for .. in 루프

  수열에 대한 이터레이션을 수행하는 루프

  범위를 설정할 때 양 끝 값 사이에 ..를 사용한다.

  

다음은 3으로 나눠떨어지면 피즈, 5로 나눠떨어지면 버즈, 둘 다 해당되면 피즈버즈라고 말해야 하는 '피즈버즈 게임'을 구현한 예시이다.

```kotlin
fun fizzBuzz(i: Int) = when{
    i%15==0 -> "FizzBuzz "
    i%3==0 -> "Fizz "
    i%5==0 -> "Buzz "
    else -> "$i "
}
```



1부터 100까지 피즈버즈 게임을 출력하기 위해서는 다음과 같이 이터레이션을 할 수 있다.

```kotlin
for (i in 1..100)
	print(fizzBuzz(i))
```



100부터 거꾸로 세되 짝수만으로 게임을 진행하는 것을 다음과 같이 구현할 수 있다. 

```kotlin
for (i in 100 downTo 1 step 2)
	print(fizzBuzz(i))
```



- downTo : 역방향 수열을 만든다.

- step : 증가의 절댓값을 설정한다.

- until : 닫힌 구간 범위를 사용한다.

  이 키워드들은 3장에서 자세히 다룬다.



### 2.4.3 맵에 대한 이터레이션

---

```kotlin
val binaryReps = TreeMap<Char, String>()		// 키에 대해 정렬하기 위해 TreeMap을 사용한다.

for(c in 'A'..'F'){								// A~F까지 문자의 범위를 사용해 이터레이션한다.
    val binary = Integer.toBinaryString(c.toInt())	// ASCII 코드를 2진 표현으로 바꾼다.
    binaryReps[c] = binary							// c를 키로 c의 2진 표현을 맵에 넣는다.
}

for((letter, binary) in binaryReps){	// 맵에 대해 이터레이션한다. 맵의 키와 값을 두 변수에 각각 대입한다.
    println("$letter = $binary")
}
```

위와 같이 for 루프를 사용해 이터레이션하려는 컬렉션의 원소를 풀 수 있다. 원소를 풀어서 letter와 binary라는 두 변수에 저장하여 출력한다. letter에는 키가 들어가고, binary에는 2진 표현이 들어간다.

- get ↔ map[key]

- put ↔ map[key] = value

  ex) binaryReps.put(c, binary) ↔ binaryReps[c] = binary



이러한 구조 분해 구문을 다른 컬렉션에서도 활용할 수 있다.

```kotlin
val list = arrayListOf("10", "11", "1001")
for((index, elemet) in list.withIndex()){
	println("$index: $element")
}
```



### 2.4.4 in으로 컬렉션이나 범위의 원소 검사

---

- ##### in 연산자

  어떤 값이 범위에 속하는지 검사할 수 있다.

- ##### !in 연산자

  어떤 값이 범위에 속하지 않는지 검사할 수 있다.



```kotlin
fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'		// 'a'<=c && c<='z'로 변환된다/
fun isNotDigit(c: Char) = c !in '0'..'9'
```



다음과 같이 while 식에서도 in과 !in 연산자를 사용할 수 있다.

```kotlin
fun recognize(c: Char) = when(c){
    in '0'..'9' -> "It's a digit!"
    in 'a'..'z', in 'A'..'Z' -> "It's a letter!"
    else -> "I don't know.."
}
```



- in 연산자는 Comparable 객체에도 사용할 수 있다.



## 2.5 코틀린의 예외 처리

코틀린의 기본 예외 처리 구문은 자바와 비슷하다.

오류가 발생하면 예외를 throw하고, 발생한 예외를 함수 호출 단에서 catch하지 않으면 함수 호출 스택을 거슬러 올라가면서 rethorw한다.



### 2.5.1 try, catch, finally

---

```kotlin
fun readNumber(reader: BufferedReader): Int? {
    try{
        val line = reader.readLine()
        return Integer.parseInt(line)
    }
    catch (e: NumberFormatException){
        return null
    }
    finally{
		reader.close()
    }
}
```

자바 코드와 가장 큰 차이는 throws 절이 코드에 없다는 점이다.

자바에서는 체크 예외를 명시적으로 처리해야한다. 호출한 함수에서 발생할 수 있는 예외를 모두 catch로 처리해야 하며, 처리하지 않은 예외는 throws 절에 명시해야한다.

코틀린에서는 예외 처리를 강제하지 않는다.



### 2.5.2 try를 식으로 사용

---

```kotlin
fun readNumber(read: BufferedReader){
    val number = try{
        Integer.parseInt(reader.readLine())		// 예외가 발생하지 않으면 이 값을 사용한다.
    } catch (e: NumberFormatException){
        null									// 예외가 발생하면 null 값을 사용한다.
    }
    println(number)
}
```

코틀린의 try 키워드는 식이기 때문에 그 값을 변수에 대입할 수 있다.



## 2.6 요약

- 함수를 정의할 때 fun 키워드를 사용한다. val과 var는 각각 읽기 전용 변수와 변경 가능한 변수를 선언할 때 쓰인다.
- 문자열 템플릿을 사용하면 문자열을 연결하지 않아도 되므로 코드가 간결해진다. 변수 이름 앞에 $를 붙이거나, 식을 ${식}처럼 ${}로 둘러싸면 변수나 식의 값을 문자열 안에 넣을 수 있다.
- 코틀린에서는 값 객체 클래스를 아주 간결하게 표현할 수 있다. (코드 없이 데이터만 저장하는 객체)
- 다른 언어에도 있는 if는 코틀린에서 식이며, 값을 만들어낸다.
- 코틀린 when은 자바의 switch와 비슷하지만 더 강력하다.
- 어떤 변수의 타입을 검사하고 나면 굳이 그 변수를 캐스팅하지 않아도 검사한 타입의 변수처럼 사용할 수 있다. (스마트 캐스팅 by 컴파일러)
- for, while, do-while 루프는 자바가 제공하는 같은 키워드의 기능과 비슷하다. 코틀린에서는 이터레이션하거나, 이터레이션하면서 컬렉션의 원소와 인덱스를 함께 사용하는 경우 더 편리하게 for를 사용할 수 있다.
- 1..5와 같은 식은 범위를 만들어낸다. 범위와 수열은 코틀린에서 같은 문법을 사용하며, for 루프에 대해 같은 추상화를 제공한다. 어떤 값이 범위 안에 들어있거나 들어있지 않은지 검사하기 위해서 in이나 !in을 사용한다.
- 코틀린 예외 처리는 자바와 비슷하다. 다만 코틀린에서는 함수가 던질 수 있는 예외를 선언하지 않아도 된다.
