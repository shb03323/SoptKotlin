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

-  함수를 값으로 바꿔서 직접 넘길 수 있다. 이때 이중 콜론(::)을 사용한다.

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
