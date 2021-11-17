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
