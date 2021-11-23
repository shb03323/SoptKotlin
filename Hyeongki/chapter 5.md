## 5. 람다로 프로그래밍

람다 식(lambda expression) 또는 람다는 기본적으로 다른 함수에 넘길 수 있는 작은 코드 조각을 뜻한다.



### 5.1 람다 식과 멤버 참조

#### 5.1.1 람다 소개: 코드 블록을 함수 인자로 넘기기

이벤트 핸들러와 같은 일련의 동작을 변수에 저장하거나 다른 함수에 넘기기 위해서 자바에서는 무명 내부 클래스를 이용했다. 

하지만, 함수형 프로그래밍에서는 함수를 값처럼 다루기 때문에 코드 블록을 직접 함수의 인자로 전달 할 수 있다.

```kotlin
button.setOnClickListener { /* 클릭 시 수행할 동작 */ }
```



#### 5.1.2 람다와 컬렉션

람다를 이용해 컬렉션 라이브러리 함수를 간단하게 사용할 수 있다. 

모든 컬렉션에 대해 maxBy 함수를 호출할 수 있으며, 다음과 같이 비교에 사용할 값을 반환하는 함수를 람다로 전달한다.

```kotlin
people.maxBy { it.age }
```



단지 함수나 프로퍼티를 반환하는 역할을 수행하는 람다는 멤버 참조로 대치할 수 있다.

```kotlin
people.maxBy(Person::age)
```



#### 5.1.3 람다 식의 문법

람다 식은 항상 중괄호로 둘러싸여 있다.

파라미터 목록 주변에 괄호가 없다.

화살표(->)로 파라미터 목록과 본문을 구분한다.

```kotlin
val sum = { x: Int, y: Int -> x + y }
println(sum(1, 2))
```

위와 같이 람다 식을 변수에 저장하여 호출할 수 있다.



코드의 일부분을 블록으로 둘러싸 실행할 필요가 있다면 `run`을 사용한다.

- `run` : 인자로 받은 람다를 실행해주는 라이브러리 함수

```kotlin
run {println(42)}
```



앞의 maxBy 함수를 호출하는 예제에서 제공된 기능을 제거하고 정식으로 람다를 작성하면 다음과 같다.

```kotlin
people.maxBy({ p: Person -> p.age })
```

코틀린에서는 함수 호출 시 마지막에 있는 인자가 람다 식이라면 그 람다를 괄호 밖으로 빼낼 수 있다는 문법 관습이 있다.

```kotlin
people.maxBy() { p: Person -> p.age }
```

람다가 어떤 함수의 유일한 인자이고 괄호 뒤에 람다를 썼다면 호출 시 빈 괄호를 없애도 된다.

```kotlin
people.maxBy { p: Person -> p.age }
```



또한 컴파일러가 람다 파라미터 타입도 추론할 수 있으므로 이를 명시하지 않아도 된다.

```kotlin
people.maxBy { p -> p.age }
```



람다의 파라미터가 하나뿐이고 그 타입을 컴파일러가 추론할 수 있는 경우 it을 바로 쓸 수 있다.

```kotlin
poeple.maxBy { it.age }
```



#### 5.1.4 현재 영역에 있는 변수에 접근

람다를 함수 안에서 정의하면 함수의 파라미터뿐 아니라 람다 정의의 앞에 선언된 로컬 변수까지 람다에서 모두 사용할 수 있다.

```kotlin
fun printMessagesWithPrefix(messages: Collection<String>, prefix: String) {
	messages.forEach {
        println("$prefix $it")
    }	
}
```



코틀린에서는 람다에서 람다 밖 함수에 있는 파이널이 아닌 변수에 접근할 수 있고, 그 변수를 변경할 수도 있다.

```kotlin
fun printProblemCounts (responses: Collection<String>) {
    var clientErrors = 0	
    var serverErrors = 0
    responses.forEach {
        if (it.startsWith("4")) {
            clientErrors++	
        } else if (it.startsWith("5")) {
            serverErrors++
        }
    }
    println("$clientErrors client errors, $serverErrors server errors")
}
```

이 예제의 prefix, clientErrors, serverErrors와 같이 람다 안에서 사용하는 외부 변수를 **람다가 포획(capture)한 변수**라고 한다.

어떤 함수가 자신의 로컬 변수를 포획한 람다를 반환하거나 다른 변수에 저장한다면 로컬 변수의 생명주기와 함수의 생명주기가 달라질 수 있다.



람다를 이벤트 핸들러나 다른 비동기적으로 실행되는 코드로 활용하는 경우 함수 호출이 끝난 다음에 로컬 변수가 변경될 수 있다.

```kotlin
fun tryToCountButtonClicks(button: Button): Int {
	val clicks = 0
	button.onClick { clicks++ }
	return clicks
}
```





#### 5.1.5 멤버 참조

 이중 콜론 (`::`)을 이용해서 함수를 값으로 바꿀 수 있다.

`::`를 사용하는 식을 **멤버 참조(member reference)**라고 부른다.

멤버 참조는 프로퍼티나 메소드를 단 하나만 호출하는 함수 값을 만들어준다.

```kotlin
val getAge = Person::age		// val getAge = { person: Person -> person.age }와 같은 역할을 수행한다.
```

::는 클래스 이름과 참조하려는 멤버(프로퍼티나 메소드) 이름 사이에 위치한다.

최상위에 선언된 다른 클래스의 멤버가 아닌 함수나 프로퍼티도 참조할 수 있다.



**생성자 참조(constructor reference)**를 사용하면 클래스 생성 작업을 연기하거나 저장해둘 수 있다.

`::`뒤에 클래스 이름을 넣으면 생성자 참조를 만들 수 있다.

```kotlin
data class Person(val name: String, val age: Int)
val createPerson = ::Person	// Person의 인스턴스를 만드는 동작을 값으로 저장한다.
```



확장 함수도 멤버 함수와 같은 방식으로 참조할 수 있다.

```kotlin
fun Person.isAdult() = age >= 21
val predicate = Person::isAdult
```



### 5.2 컬렉션 함수형 API

#### 5.2.1 필수적인 함수: filter와 map

filter와 map은 컬렉션을 활용할 때 기반이 되는 함수이다.

`filter` 함수는 컬렉션에서 원치 않는 원소를 제거한다. (filter는 원소를 변환할 수는 없다.)

```kotlin
data class Person(val name: String, val age: Int)
val list = listOf(1, 2, 3, 4)
println(list.filter { it % 2 ==0 })		// 짝수만 남게 된다.
```



`map` 함수는 주어진 람다를 컬렉션의 각 원소에 적용한 결과를 모아서 새 컬렉션을 만든다.

```kotlin
val list = listOf(1, 2, 3, 4)
println(list.map { it * it })
```



다음과 같이 클래스의 필드 중 원하는 값만 출력할 수도 있다.

```kotlin
val people = listOf(Person("Alice", 29), Person("Bob", 31))
println(people.map { it.name })
```

위의 코드를 멤버 참조를 이용하면 다음과 같이 작성할 수 있다.

```kotlin
people.map(Person::name)
```



다음은 최댓값을 한 번만 계산하게 만든 코드이다.

```kotlin
val maxAge = people.maxBy(Person::age)!!.age
people.filter { it.age == maxAge }
```



#### 5.2.2 all, any, count, find: 컬렉션에 술어 적용

`count` 함수는 조건을 만족하는 원소의 개수를 반환한다.

`find` 함수는 조건을 만족하는 첫 번째 원소를 반환한다.

`all` 함수는 모든 원소가 이 술어를 만족하는지 판단한다.

`any`함수는 술어를 만족하는 원소가 하나라도 있는지 판단한다.

```kotlin
val canBeInClub27 = { p: Person -> p.age <= 27 }

val people = listOf(Person("Alice", 27), Person("Bob", 31))
println(people.all(canBeInClub27))
println(people.any(canBeInClub27))
println(people.count(canBeInClub27))
println(people.find(canBeInClub27))
```



#### 5.2.3 groupBy: 리스트를 여러 그룹으로 이뤄진 맵으로 변경

`groupBy` 함수는 특성을 파라미터로 전달하면 컬렉션을 자동으로 구분해준다.

```kotlin
val people = listOf(Person("Alice", 31), Person("Bob", 29), Person("Carol", 31))
println(people.groupBy { it.age })
```



각 그룹은 리스트이기 때문에, 위의 경우에선 반환 타입은 `Map<Int, List<Person>>`이 되었다.



#### 5.2.4 flatMap과 flatten: 중첩된 컬렉션 안의 원소 처리

`flatMap` 함수는 먼저 인자로 주어진 람다를 컬렉션의 모든 객체에 적용하고(**map**) 람다를 적용한 결과 얻어지는 여러 리스트를 한 리스트로 모은다(**flatten**)

```kotlin
val strings = listOf("abc", "def")
println(strings.flatMap { it.toList() })
```

위의 예제에서 `toList`함수를 문자열에 적용하면 그 문자열에 속한 모든 문자로 이뤄진 리스트가 만들어지는데, 이후 flatten되어 하나의 리스트로 묶어졌다.



```kotlin
val books = listOf(Book("Thursday Next", listOf("Jasper Fforde")), Book("Mort", ListOf("Terry Pratchett", "Neil Gaiman")))
println(books.flatMap { it.authors }.toSet())
```

위의 예제는 하나의 책을 여러 저자가 함께 쓸 수 있기 때문에 중첩 리스트로 구성된 book을 저자만 표시하도록 구현한 것이다.



리스트의 리스트가 있는데 모든 중첩된 리스트의 원소를 한 리스트로 모아야 한다면 `flatMap`을 사용할 수 있다.

하지만 특별히 변환해야 할 내용이 없다면 리스트의 리스트를 평평하게 펼치기만 하면 된다. 이 때는 `flatten` 함수를 사용하면 된다.



### 5.3 지연 계산(lazy) 컬렉션 연산

`map`이나 `filter` 같은 컬렉션 함수는 리스트를 **즉시** 생성하여 반환하지만, **시퀀스**를 사용하면 중간 임시 컬렉션 없이 연쇄적으로 연산을 할 수 있다.

`asSequence` 확장 함수를 호출하면 모든 컬렉션을 시퀀스로 바꿀 수 있으며, 원소가 많을 수록 성능이 향상된다.

시퀀스 내부 원소에 접근할 때는 `iterator` 메소드를 사용할 수 있다. 

```kotlin
person.asSequence()						// 컬렉션을 시퀀스로 변환
	.map(Person::name)					// 시퀀스도 컬렉션과 같은 API 제공
	.filter { it.startsWith("A") }
	.toList()							// 시퀀스를 다시 리스트로 변환
```



#### 5.3.1 시퀀스 연산 실행: 중간 연산과 최종 연산

시퀀스에 대한 연산은 **중간**(intermediate) 연산과 **최종**(terminal) 연산으로 나뉜다.

중간 연산은 항상 지연 계산되며, 최종 연산을 호출하면 연기됐던 모든 계산이 순서대로 수행된다. (최종 연산이 없으면 중간 연산은 수행되지 않음)

```kotlin
println(listOf(1, 2, 3, 4).asSequence()
       					.map { it * it }.find { it > 3 })			// 결과 : 4
```

sequence를 사용했기 때문에 `find` 함수가 1\*1, 2\*2의 순서대로 조건을 찾아서 결과는 4가 나온다.



#### 5.3.2 시퀀스 만들기

`asSequence` 함수 외에도, `generateSequence` 함수를 사용해서 시퀀스를 만들 수 있다.

```kotlin
val naturalNumbers = generateSequence(0) { it + 1 }
val numbersTo100 = naturalNumbers.takeWhile { it <= 100 }
println(numbersTo100.sum())				// naturalNumbers, numbersTo100은 시퀀스이므로, 최종 연산인 sum() 함수가 호출될 때까지 지연된다.
```



다음 예제는 어떤 파일의 상위 디렉터리를 뒤지면서 숨김(hidden) 속성을 가진 디렉터리가 있는지 검사함으로써 파일이 감춰진 디렉터리 안에 있는지 알아본다.

```kotlin
fun File.isInsideHiddenDirectory() = 
	generateSequence(this) { it.parentFile }.any { it.isHidden }
```

여기서 `any`를 `find`로 바꾸면 원하는 디렉터리를 찾을 수도 있다.



### 5.4 자바 함수형 인터페이스 활용

#### 5.4.1 자바 메소드에 람다를 인자로 전달

함수형 인터페이스를 인자로 원하는 자바 메소드에 코틀린 람다를 전달할 수 있다.

자바에서 다음과 같이 Runnable 타입의 파라미터를 받는 메소드가 있을 때

```java
void postponeComputation(int delay, Runnable computation);
```

코틀린에서 람다를 함수에 넘기면 컴파일러는 자동으로 람다를 Runnable 인스턴스로 변환해준다.

```kotlin
postponeComputation(1000) { println(42) }
```



컴파일러는 자동으로 무명 클래스와 인스턴스를 만들어주지만, 람다와 무명 객체 사이에는 차이가 있다.

- 무명 객체 명시적 선언 : 메소드를 호출할 때마다 새로운 객체 생성
- 람다 사용 : 람다에 대응하는 무명 객체를 메소드를 호출할 때마다 반복 사용



람다가 주변 영역의 변수를 포획한다면 매 호출마다 같은 인스턴스를 <u>사용할 수 없다.</u>

이런 경우 컴파일러는 매번 주변 영역의 변수를 포획한 새로운 인스턴스를 생성해준다.

```kotlin
fun handleComputation(id: String) {					// 람다 안에서 id 변수를 호출한다.
	postponeComputation(1000) { println(id) }		// handleComputation을 호출할 때마다 새로 Runnable 인스턴스를 생성한다.
}
```



`incline`으로 표시된 코틀린 함수에게 람다를 넘기면 아무런 무명 클래스도 만들어지지 않는다. 대부분의 코틀린 확장 함수들은 `inline` 표시가 붙어있다.



#### 5.4.2 SAM 생성자: 람다를 함수형 인터페이스로 명시적으로 변경

SAM 생성자는 람다를 함수형 인터페이스의 인스턴스로 변환할 수 있게 컴파일러가 자동으로 생성한 함수다.

컴파일러가 자동으로 람다를 함수형 인터페이스 무명 클래스로 바꾸지 못하는 경우 SAM 생성자를 사용할 수 있다.



- SAM 생성자의 이름은 사용하려는 함수형 인터페이스의 이름과 같다.
- SAM 생성자는 그 함수형 인터페이스의 유일한 추상 메소드의 본문에 사용할 람다만을 인자로 받아서 함수형 인터페이스를 구현하는 클래스의 인스턴스를 반환한다.

```kotlin
fun createAllDoneRunnable(): Runnable {
    return Runnable { println("All done!") }
}
```



다음과 같이 여러 버튼에 같은 리스너를 적용할 때 SAM 생성자를 이용해 람다를 함수형 인터페이스 인스턴스로 만들어서 변수에 저장할 수 있다.

```kotlin
val listener = OnClickListener { view -> 
	val text = when (view.id) {
        R.id.button1 -> "First button"
        R.id.button2 -> "Second button"
        else -> "Unknown button"
    }
	toast(text)
}
```



### 5.5 수신 객체 지정 람다: with와 apply

- **수신 객체 지정 람다(lambda with receiver)** : 수신 객체를 명시하지 않고 람다의 본문 안에서 다른 객체의 메소드를 호출



#### 5.5.1 with 함수

`with`은 언어가 제공하는 특별한 구문처럼 보이지만 실제로는 파라미터가 2개인 함수다.

첫 번째 인자로 받은 객체를 두 번째 인자로 받은 람다의 수신 객체로 만든다.

인자로 받은 람다 본문에서는 `this`를 사용해 그 수신 객체에 접근할 수 있다.

```kotlin
fun alphabet() = with(StringBuilder()) {
    for (letter in 'A'..'Z') {
        append(letter)
    }
    append("\nNow I know the alphabet!")
    toString()
}
```



> with 외부의 메소드 이름과 충돌할 경우 `this@OuterClass.toString()`과 같은 형태로 명시적으로 외부 메소드를 호출할 수 있다.



#### 5.5.2 apply 함수

`apply` 함수는 거의 `with`과 같으며, 항상 **수신 객체**를 반환한다는 점이 유일한 차이다.

```kotlin
fun alphabet() = StringBuilder().apply {
	for (letter in 'A'..'Z') {
        append(letter)
    }
    append("\nNow I know the alphabet!")
}.toString()
```



`apply` 함수는 객체의 인스턴스를 만들면서 즉시 프로퍼티 중 일부를 초기화해야 하는 경우 유용하다.

```kotlin
fun createViewWithCustomAttributes(context: Context) = 
	TextView(context).apply {
        text = "Sample Text"
        textSize = 20.0
        setPadding(10, 0, 0, 0)
    }
```



with, apply 외에도 표준 라이브러리의 `buildString` 함수는 StringBuilder를 활용해 String을 만드는 경우 사용할 수 있는 방법이다.

```kotlin
fun alphabet() = buildString {
    for (letter in 'A'..'Z') {
        append(letter)
    }
    append("\nNow I know the alphabet!")
}
```



### 5.6 요약

- 람다를 사용하면 코드 조각을 다른 함수에게 인자로 넘길 수 있다.
- 코틀린에서는 람다가 함수 인자인 경우 괄호 밖으로 람다를 빼낼 수 있고, 람다의 인자가 단 하나뿐인 경우 인자 이름을 지정하지 않고 `it`이라는 디폴트 이름으로 부를 수 있다.
- 람다 안에 있는 코드는 그 람다가 들어있는 바깥 함수의 변수를 읽거나 쓸 수 있다.
- 메소드, 생성자, 프로퍼티의 이름 앞에 `::`을 붙이면 각각에 대한 참조를 만들 수 있다. 그런 참조를 람다 대신 다른 함수에게 넘길 수 있다.
- `filter`, `map`, `all`, `any` 등의 함수를 활용하면 컬렉션에 대한 대부분의 연산을 직접 원소를 이터레이션하지 않고 수행할 수 있다.
- 시퀀스를 사용하면 중간 결과를 담는 컬렉션을 생성하지 않고도 컬렉션에 대한 여러 연산을 조합할 수 있다.
- 함수형 인터페이스(추상 메소드가 단 하나뿐인 SAM 인터페이스)를 인자로 받는 자바 함수를 호출할 경우 람다를 함수형 인터페이스 인자 대신 넘길 수 있다.
- 수신 객체 지정 람다를 사용하면 람다 안에서 미리 정해둔 수신 객체의 메소드를 직접 호출할 수 있다.
- 표준 라이브러리의 `with` 함수를 사용하면 어떤 객체에 대한 참조를 반복해서 언급하지 않으면서 그 객체의 메소드를 호출할 수 있다. `apply`를 사용하면 어떤 객체라도 빌더 스타일의 API를 사용해 생성하고 초기화할 수 있다.
