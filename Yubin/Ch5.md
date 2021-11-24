# 람다로 프로그래밍

**람다 식** : 기본적으로 다른 함수에 넘길 수 있는 작은 조각

- 컬렉션 처리에서 람다를 자주 사용한다.

<br/>

## 1. 람다 식과 멤버 참조

### 1.1. 람다 소개: 코드 블록을 함수 인자로 넘기기

- 람다 식 ▶️ 코드 블록을 변수에 저장하거나 다른 함수에 넘겨야 하는 경우 함수를 선언할 필요가 없고 코드 블록을 직접 함수의 인자로 전달할 수 있다.

  ```kotlin
  button.setOnClickListener { /* 클릭 시 수행할 동작 */ }
  ```

<br/>

### 1.2. 람다와 컬렉션

- 람다가 없다면 컬렉션을 편리하게 처리할 수 있는 좋은 라이브러리를 제공하기 힘들다.

- 단지 함수나 프로퍼티를 반환하는 역할을 수행하는 람다는 멤버 참조로 대치할 수 있다.

  ex) `People.maxBy(Person::age)`

<br/>

### 1.3. 람다 식의 문법

- 코틀린 람다 식은 항상 중괄호로 둘러싸여 있다.

- 화살표(->)가 인자 목록과 람다 본문을 구분해준다.

- 람다 식을 변수에 저장할 수 있다.

  ```kotlin
  val sum = { x: Int, y: Int -> x + y}
  println(sum(1,2))	// 변수에 저장된 람다를 호출
  ```

- 코드의 일부분을 블록으로 둘러싸 실행할 필요가 있다면 **run**을 사용한다.

  - `run` : 인자로 받은 람다를 실행해주는 라이브러리 함수

    ```kotlin
    run { println(42) }
    ```

- ```kotlin
  people.maxBy ({ p: Person -> p.age })
  ```

  컴파일러가 문맥으로부터 유추할 수 있는 인자 타입은 적지 않아도 된다. 인자가 단 하나뿐인 경우 굳이 인자에 이름을 붙이지 않아도 된다.

  ```kotlin
  people.maxBy { it.age }	// it : 자동 생성된 파라미터 이름
  ```

  - 람다의 파라미터가 하나뿐이고 그 타입을 컴파일러가 추론할 수 있는 경우 it을 바로 쓸 수 있다.

- 람다를 변수에 저장할 때는 파라미터 타입을 명시해야 한다.

<br/>

### 1.4. 현재 영역에 있는 변수에 접근

- 람다를 함수 안에서 정의하면 함수의 파라미터뿐 아니라 람다 정의의 앞에 선언된 로컬 변수까지 람다에서 모두 사용할 수 있다.

  - `forEach` : 컬렉션의 모든 원소에 대해 람다를 호출해주는 가장 기본적인 컬렉션 조작 함수 중 하나.

    - 람다 안에서 바깥 함수의 로컬 변수 변경하기

      ```kotlin
      fun printProblemCounts (responses: Collection<String>) {
          var clientErrors = 0	// 람다에서 사용할 변수 정의
          var serverErrors = 0
          responses.forEach {
              if (it.startsWith("4")) {
                  clientErrors++	// 람다 안에서 람다 밖의 변수 변경
              } else if (it.startsWith("5")) {
                  serverErrors++
              }
          }
          println("$clientErrors client errors, $serverErrors server errors")
      }
      ```

    - 람다가 포획한 변수 : `clientErrors`, `serverErrors`와 같이 람다 안에서 사용하는 외부 변수

    - 어떤 함수가 자신의 로컬 변수를 포획한 람다를 반환하거나 다른 변수에 저장한다면 로컬 변수의 생명주기와 함수의 생명주기가 달라질 수 있다.

- 람다를 이벤트 핸들러나 다른 비동기적으로 실행되는 코드로 활용하는 경우 함수 호출이 끝난 다음에 로컬 변수가 변경될 수 있다.

<br/>

### 1.5. 멤버 참조

- 함수를 값으로 바꿔서 직접 넘길 수 있다. 이때 이중 콜론(::)을 사용한다.

  ```kotlin
  val getAge = Person::age
  ```

- **멤버 참조** : `::`를 사용하는 식

- 멤버 참조는 프로퍼티나 메소드를 단 하나만 호출하는 함수 값을 만들어준다.

- `::`는 클래스 이름과 참조하려는 멤버(프로퍼티, 메소드) 이름 사이에 위치한다.

- 멤버 참조는 그 멤버를 호출하는 람다와 같은 타입이다.

- 최상위에 선언된 다른 클래스의 멤버가 아닌 함수나 프로퍼티를 참조할 수 있다.

- 생성자 참조(constructor reference)

  ▶️ 클래스 생성 작업을 연기하거나 저장해둘 수 있다.

  - `::` 뒤에 클래스 이름을 넣으면 생성자 참조를 만들 수 있다.

    ```kotlin
    >>> data class Person (val name: String, val age: Int)
    >>> val createPerson = ::Person
    >>> val p = createPerson("Alice", 29)
    >>> println(p)
    Person(name=Alice, age=29)
    ```

<br/>

---

## 2. 컬렉션 함수형 API

### 2.1. 필수적인 함수: filter와 map

- 컬렉션을 활용할 때 기반이 되는 함수다.

- `filter` : 컬렉션을 이터레이션하면서 주어진 람다에 각 원소를 넘겨서 람다가 true를 반환하는 원소만 모으는 함수

  ```kotlin
  data Class Person(val name: String, val age: Int)
  >>> val people = listOf(Person("Alice", 29), Person("Bob", 31))
  >>> println(people.filter { it.age > 30 })
  [Person(name=Bob, age=31)]
  ```

  - 컬렉션에서 원치 않는 원소를 제거할 수는 있지만 원소를 변환할 수는 없다.

- `map` : 주어진 람다를 컬렉션의 각 원소에 적용한 결과를 모아서 새 컬렉션을 만드는 함수

  ```kotlin
  >>> val people = listOf(Person("Alice", 29), Person("Bob", 31))
  >>> println(people.map{ it.name })
  [Alice, Bob]
  ```

  ```kotlin
  >>> people.filter { it.age > 30 }.map(Person::name)
  [Bob]
  ```

  ```kotlin
  val maxAge = people.maxBy(Person::age)!!.age
  people.filter { it.age == maxAge }
  ```

<br/>

### 2.2. all, any, count, find : 컬렉션에 술어 적용

- `all`, `any` : 컬렉션에 대해 자주 수행하는 연산으로 컬렉션의 모든 원소가 어떤 조건을 만족하는지 판단하는 연산.

- `count` : 조건을 만족하는 원소의 개수를 반환.

- `find` : 조건을 만족하는 첫 번째 원소를 반환.

  - 조건을 만족하는 원소가 없으면 null을 반환 ▶️ firstOrNull과 같다.

- ```kotlin
  >>> val canBeInClub27 = { p: Person -> p.age <= 27 }	// 어떤 사람의 나이가 27살 이하인지 판단하는 술어 함수
  >>> val people = listOf(Person("Alice", 29), Person("Bob", 31))
  >>> println(people.all(canBeInClub27))
  false
  >>> println(people.any(canBeInClub27))
  true
  >>> println(people.count(canBeInClub27))
  1
  >>> println(people.find(canBeInClub27))
  Person(name=Alice, age=27)
  ```

<br/>

### 2.3. groupBy: 리스트를 여러 그룹으로 이뤄진 맵으로 변경

- 특성을 파라미터로 전달하면 컬렉션을 자동으로 구분해준다.

- ```kotlin
  >>> val people = listOf(Person("Alice", 29), 
                          ... Person("Bob", 31), Person("Carol", 31))
  >>> println(people.groupBy{ it.age })
  ```

  이 연산의 결과는 컬렉션 원소를 구분하는 특성(age)이 key이고, key 값에 따른 각 그룹(Person 객체의 모임)이 값인 맵이다.

  ```kotlin
  { 29 = [Person(name=Bob, age=29)],
  31 = [Person(name=Alice, age=31), Person(name=Carol, age=31)]}
  ```

<br/>

### 2.4. flatMap과 flatten: 중첩된 컬렉션 안의 원소 처리

- `flatMap` : 먼저 인자로 주어진 람다를 컬렉션의 모든 객체에 적용하고(map) 람다를 적용한 결과 얻어지는 여러 리스트를 한 리스트로 한데 모은다(flatten).

- ```kotlin
  >>> val strings = listOf("abc", "def")
  >>> println(strings.flatMap { it.toList() })
  [a, b, c, d, e, f]
  ```

- 모든 중첩된 리스트의 원소를 한 리스트로 모을 때 ▶️ `flatMap` 

- 특별히 편환해야 할 내용이 없다면 ▶️ `flatten`



---

## 3. 지연 계산(lazy) 컬렉션 연산

- **시퀀스**를 사용하면 중간 임시 컬렉션을 사용하지 않고도 컬렉션 연산을 인쇄할 수 있다.

- 코틀린 지연 계산 시퀀스는 Sequence 인터페이스에서 시작한다.

- Sequence 인터페이스의 장점

  : 시퀀스의 원소는 필요할 때 비로소 계산 ▶️  중간 처리 결과를 저장하지 않고도 연산을 연쇄적으로 적용해서 효율적으로 계산을 수행한다.



### 3.1. 시퀀스 연산 실행: 중간 연산과 최종 연산

- 시퀀스에 대한 연산은 **중간 연산**과 **최종 연산**으로 나뉜다.

  - 중간 연산: 다른 시퀀스(최초 시퀀스의 원소를 변환하는 방법을 앎)를 반환.
  - 최종 연산: 결과(최초 컬렉션에 대해 변환을 적용한 시퀀스로부터 일련의 계산을 수행해 얻을 수 있는 컬렉션이나 원소, 숫자 또는 객체)를 반환.

- 중간 연산은 항상 지연 계산된다.

- ```kotlin
  >>> listOf(1, 2, 3, 4).asSequence()
  ...				.map { print("map($it) "); it * it }
  ...				.filter { print("filter($it) "); it % 2 == 0 }		// 중간 연산
  ...				.toList()		// 최종 연산								
  map(1) filter(1) map(2) filter(4) map(3) filter(9) map(4) filter(16)
  ```

  - 중간 연산까지 실행하면 아무 내용도 출력되지 않음 ▶️  map과 filter 변환이 늦춰져서 결과를 얻을 필요가 있을 때, 즉 최종 연산이 호출될 때 적용이 된다.
  - 최종 연산을 호출하면 연기됐던 모든 계산이 수행된다.
  - 모든 연산은 각 원소에 대해 순차적으로 적용됨 ▶️  원소에 연산을 차례대로 적용하다가 결과가 얻어지면 그 이후의 원소에 대해서는 변환이 이뤄지지 않을 수 있다.

- 시퀀스를 사용하면 컬렉션에 대해 수행하는 연산의 순서도 성능에 영향을 끼친다.

  - 사람의 컬렉션이 있는데 이름이 어떤 길이보다 짧은 사람의 명단을 얻고 싶은 경우
    - `map` 먼저 수행 ▶️  모든 원소를 변환한다.
    - `filter` 먼저 수행 ▶️  부적절한 원소를 먼저 제외하기 때문에 그런 원소는 변환되지 않는다.

<br/>

### 3.2. 시퀀스 만들기

- 시퀀스를 만드는 다른 방법으로 `generateSequence` 함수를 사용할 수 있다.

  - 자연수의 시퀀스를 생성하고 사용하기

    ```kotlin
    >>> val naturalNumbers = generateSequence(0) { it + 1 }
    >>> val numbersTo100 = naturalNumbers.takeWhile { it <= 100 }
    >>> println(numbersTo100.sum())
    5050
    ```

    - `naturalNumbers`와 `numbersTo100` 은 모두 시퀀스이다.
    - 연산을 지연 계산함 ▶️  최종 연산을 수행하기 전까지는 시퀀스의 각 숫자는 계산되지 않는다.

- 시퀀스를 사용하는 일반적인 용례 중 하나: 객체의 조상으로 이뤄진 시퀀스를 만들어내는 것

  - 상위 디텍터리의 시퀀스를 생성하고 사용하기

    ```kotlin
    fun File.isInsideHiddenDirectory() =
    	generateSequence(this) { it.parentFile }.any{ it.isHidden }
    >>> val file = Fiel("/Users/svtk/.HiddenDir/a.txt")
    >>> println(file.isInsideHiddenDirectory())
    true
    ```

    - 조건을 만족하는 디렉터리를 찾은 뒤에는 더 이상 상위 디렉터리를 뒤지지 않는다.

<br/>

---

## 4. 자바 함수형 인터페이스 활용

### 4.1. 자바 메소드에 람다를 인자로 전달

- 함수형 인터페이스를 인자로 원하는 자바 메소드에 코틀린 람다를 전달할 수 있다.

- Runnable 타입의 파라미터를 받는 메소드가 있을 때 코틀린에서는 람다를 이 함수에 넘길 수 있다. 컴파일러는 자동으로 람다를 Runnable 인스턴스로 변환해준다.

  ```kotlin
  void postponeComputation(int delay, Runnable computation)	/* 자바 */
  postponeComputation(1000) { println(42) }
  ```

  - Runnable 인스턴스 : Runnable을 구현한 무명 클래스의 인스턴스. 컴파일러는 자동으로 그런 무명 클래스와 인스턴스를 만들어준다.

- 람다와 무명 객체의 차이

  - 무명 객체 : 메소드를 호출할 때마다 새로운 객체가 생성된다.

  - 람다 : 정의가 들어있는 함수의 변수에 접근하지 않는 람다에 대응하는 무명 객체를 메소드를 호출할 때마다 반복 사용한다.

  - 명시적인 object 선언을 사용하면서 람다와 동일한 코드는 다음과 같다.

    ```kotlin
    val runnable = Runnable { println(42) }	// 전역 변수로 컴파일되므로 프로그램 안에 단 하나의 인스턴스만 존재한다.
    fun handleComputation() {
      postponeComputation(1000, runnable)	// 모든 handleComputation 호출에 같은 객체를 사용한다.
    }
    ```

    - Runnable 인스턴스를 변수에 저장하고 메소드를 호출할 때마다 그 인스턴스를 사용한다.

- 람다가 주변 영역의 변수를 포획한다면 컴파일러는 매번 주변 영역의 변수를 포획한 새로운 인스턴스를 생성해준다.

  - id를 필드를 저장하는 새로운 Runnable 인스턴스를 매번 새로 만들어 사용하는 함수

    ```kotlin
    fun handleComputation(id: String) {	// 람다 안에서 "id" 변수를 포획한다.
      postponeComputation(1000) { println(id) }	// handleComputation을 호출할 때마다 새로 Runnable 인스턴스를 만든다.
    }
    ```

- 대부분의 코틀린 확장 함수들은 `inline` 표시가 붙어있다.

<br/>

### 4.2. SAM 생성자: 람다를 함수형 인터페이스로 명시적으로 변경

- **SAM 생성자**: 함수형 인터페이스의 인스턴스로 변환할 수 있게 컴파일러가 자동으로 생성된 함수.

- 컴파일러가 자동으로 람다를 함수형 인터페이스 무명 클래스로 바꾸지 못하는 경우 SAM 생성자를 사용할 수 있다.

- SAM 생성자를 이용해 값 반환하기

  ```kotlin
  fun createAllDoneRunnable(): Runnable {
    return Runnable { println("All done!") }
  }
  >>> createAllDoneRunnable().run()
  All done!
  ```

- SAM 생성자의 이름 == 사용하려는 함수형 인터페이스의 이름

- SAM 생성자는 그 함수형 인터페이스의 유일한 추상 메소드의 본문에 사용할 람다만을 인자로 받아서 함수형 인터페이스를 구현하는 클래스의 인스턴스를 반환한다.

- 여러 버튼에 같은 리스너를 적용하고 싶다면 SAM 생성자를 통해 람다를 함수형 인터페이스 인스턴스로 만들어서 변수에 저장해 활용할 수 있다.

  - SAM 생성자를 사용해 listener 인스턴스 재사용하기

    ```kotlin
    val listener = onClickListener { view ->
                                   val text = when(view.id) {	// view.id를 사용해 어떤 버튼이 클릭됐는지 판단.
                                     R.id.button1 -> "First button"
                                     R.id.button2 -> "Seconde button"
                                     else -> "Unknown button"
                                   }
                                   toast(text)	// "text"의 값을 사용자에게 보여줌.
                                   }
    ```

- 오버로드한 메소드 중에서 어떤 타입의 메소드를 선택해 람다를 변환해 넘겨줘야 할지 모호한 경우 명시적으로 SAM 생성자를 적용하면 컴파일 오류를 피할 수 있다.

<br/>

---

## 5. 수신 객체 지정 람다: with와 apply

- 수신 객체 지정 람다(lambda with receiver) : 수신 객체를 명시하지 않고 람다의 본문 안에서 다른 객체의 메소드를 호출할 수 있는 기능을 가진 람다.

### 5.1. with 함수

- 파라미터가 2개 있는 함수.
- 첫 번째 인자로 받은 객체를 두 번째 인자로 받은 람다의 수신 객체로 만든다. 인자로 받은 람다 본문에서는 `this`를 사용해 그 수신 객체에 접근할 수 있다.
- `with` 가 반환하는 값은 람다 코드를 실행한 결과며, 그 결과는 람다 식의 본문에 있는 마지막 식의 값이다.

<br/>

### 5.2.  apply 함수

- `with`와의 유일한 차이: `apply` 는 항상 자신에게 전달된 객체, 즉 수신 객체를 반환한다.

<br/>

---

## 🔑 요약

- 람다를 사용하면 코드 조각을 다른 함수에게 인자로 넘길 수 있다.
- 코틀린에서는 람다가 함수 인자인 경우 괄호 밖으로 람다를 빼낼 수 있고, 람다의 인자가 단 하나뿐인 경우 인자 이름을 지정하지 않고 `it`이라는 디폴트 이름으로 부를 수 있다.
- 람다 안에 있는 코드는 그 람다가 들어있는 바깥 함수의 변수를 읽거나 쓸 수 있다.
- 메소드, 생성자, 프로퍼티의 이름 앞에 `::` 를  붙이면 각각에 대한 참조를 만들 수 있다. 그런 참조를 람다 대신 다른 함수에게 넘길 수 있다.
- `filter`, `map`, `all`, `any` 등의 함수를 활용하면 컬렉션에 대한 대부분의 연산을 직접 원소를 이터레이션하지 않고 수행할 수 있다.
- 시퀀스를 사용하면 중간 결과를 담는 컬렉션을 생성하지 않고도 컬렉션에 대한 여러 연산을 조합할 수 있다.
- 함수형 인터페이스(추상 메소드가 단 하나뿐인 SAM 인터페이스)를 인자로 받는 자바 함술를 호출할 경우 람다를 함수형 인터페이스 인자 대신 넘길 수 있다.
- 수신 객체 지정 람다를 사용하면 람다 안에서 미리 정해둔 수신 객체의 메소드를 직접 호출할 수 있다.
- 표준 라이브러리의 `with` 함수를 사용하면 어떤 객체에 대한 참조를 반복해서 언급하지 않으면서 그 객체의 메소드를 호출할 수 있다. `apply`를 사용하면 어떤 객체라도 빌더 스타일의 API를 사용해 생성하고 초기화할 수 있다.

