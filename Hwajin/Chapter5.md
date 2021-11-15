# 5장. 람다로 프로그래밍
* 람다 식(람다) : 다른 함수에 넘길 수 있는 작은 코드 조각
* 컬렉션 처리에서 자주 사용
* 특별한 수신 객체 지정 람다
</br>

## 람다 식과 멤버 참조
* 람다 소개: 코드 블록을 함수 인자로 넘기기
  * 일련의 동작을 변수에 저장하거나 다른 함수에 넘겨야 하는 경우 람다 식을 사용하면 함수를 선언할 필요가 없고 코드 블록을 직접 함수 인자로 전달
</br>

* 람다와 컬렉션
  * 람다는 컬렉션을 편리하게 처리할 수 있는 좋은 라이브러리 제공
    * ex) maxBy 함수 : 가장 큰 원소를 찾기 위해 비교에 사용할 값을 돌려주는 함수를 인자로 받음
</br>

* 람다 식의 문법
  * 코틀린 람다 식은 항상 중괄호로 둘러싸여 있음
  * 인자 목록 주변에 괄호가 없음. 화살표(->)가 인자 목록과 람다 본문을 구분
  ```kotlin
  val sum = { x : Int, y : Int -> x + y }
  println(sum(1,2)) //3 // 변수에 지정된 람다를 호출
  ```
  * 이렇게 코드 일부분을 블록으로 둘러싸 실행할 필요가 있다면 run을 사용
    * run : 인자로 받은 람다를 실행해주는 라이브러리 함수
    ```kotlin
    run { println(42) } //42
    ``` 
  * 람다가 어떤 함수의 유일한 인자이고 괄호 뒤에 람다를 썼다면 호출 시빈 괄호를 없애기 가능
  * 람다의 파라미터 이름을 디폴트 이름인 it으로 바꾸면 더 간단한 식을 만들 수 있음
  * 람다 사용 예
  ```kotlin
  // 이름 붙인 인자를 사용해 람다 넘기기
  val people = listOf(Person("이몽룡", 29), Person("성춘향", 31))
  val names = people.joinToString(separator = " ", transform = { p : Person -> p.name })
  
  // 람다를 괄호 밖에 전달하기
  people.joinToString(" ") { p: People -> p.name }
  
  // 람다 파라미터 타입 제거하기
  people.maxBy { p: Person -> p.age } // 파라미터 타입 명시
  people.maxBy { p -> p.age } // 파라미터 타입 생략. 컴파일러가 추론
  
  // 디폴트 파라미터 이름 it tkdydgkrl
  people.maxBy { it.age } // "it"은 자동 생성된 파라미터 이름
  ```
  * 람다에 변수를 저장할 때는 파라미터 타입 명시 
</br>

* 현재 영역에 있는 변수에 접근
  * 람다를 함수 안에서 정의하면 함수의 파라미터뿐 아니라 람다 정의의 앞에 선언된 로컬 변수까지 람다에서 모두 사용 가능
  * 코틀린 람다 안에서는 파이널 변수가 아닌 변수에 접근 가능
  * 람다 안에서 바깥의 변수 변경 가능 
  ```kotlin
  // 람다 안에서 바깥 함수의 로컬 변수 변경
  fun printProblemCounts(response: Collection<String> ) {
    // 람다에서 사용할 변수 정의
    var cliendErrors = 0
    var serverErrors = 0
    responses.forEach {
      if (it.startsWith("4")) {
        cliendErrors++ // 람다 안에서 람다 밖의 변수 변경
      } else if (it.startsWith("5")) {
        serverErrors++  // 람다 안에서 람다 밖의 변수 변경
      }
    }
    println($clientErrors client errors, $serverErrors server errors")
  }
  
  val responses = listOf("200 OK", "418 I'm a teapot", "500 Internal Server Error")
  printProblemCounts(response) // I client errors, I server errors
  ```
  * 위의 prefix, clientErrors, serverErrors와 같이 람다 안에서 사용하는 외부 변수를 '람다가 포획한 변수'라고 함
  * 람다를 이벤트 핸들러나 다른 비동기적으로 실행 되는 코드로 화룡할 경우 함수 호출이 끝난 다음 로컬 변수가 변경될 수 있음
</br>

* 멤버 참조
  * 함수를 값으로 바꾸는 것
  * 이중 콜론(::) 사용
  * 프로퍼티나 메소드를 단 하나만 호출하는 함수를 만듦 
  * 참조 대상에 관계 없이 멤버 참조 뒤에는 괄호를 넣기 불가
  * 멤버를 호출하는 람다와 같은 타입
  * 최상위에 선언된 함수나 프로퍼티 참조 가능
  * 람다가 인자가 여럿인 다른 함수한테 작업을 위임하는 경우 람다를 정의하지 않고 직접 위임 함수에 대한 참조를 제공하면 편리
  * 생성자 참조를 사용하면 클래스 생성 작업을 연기하거나 저장해둘 수 있음
  * 확장 함수도 참조 가능
  ```kotlin
  val getAge = Person::age  
  
  people.maxBy(Person::age)
  people.maxBy{ p -> p.age }
  people.maxBy { it.age }
  
  fun salute() = println("Salute!")
  run(::salute) // Salute!
  ```
</br>

## 컬렉션 함수형 API
* 필수적인 함수: filter와 map
  * 컬렉션을 활용할 때 기반이 되는 함수
  * filter
    *  컬렉션을 이터레이션하면서 주어진 람다에 각 원소를 넘겨서 람다가 true를 반환하는 원소만 모음
    *  결과는 입력 컬렉션의 언소 중에서 주어진 술어를 만족하는 원소만으로 이루어진 새로운 컬렉션
    *  원치 않는 원소를 제거할 수 있지만, 변환할 수는 없음 
    ```kotlin
    val list = listOf(1, 2, 3, 4)
    println(list.filter { it % 2 == 0 }) // [2, 4]
    ```
  * map
    * 주어진 람다를 컬렉션의 각 원소에 적용한 결과를 모아서 새 컬렉션을 만듦 
    * 원치 않는 원소 제거
    ```kotlin
    val list = listOf(1, 2, 3, 4)
    println(list.map (it * it) // [1, 4, 9, 16]
    ```
</br>

* all. any. count. find: 컬렉션에 술어 적용
  * all : 컬렉션의 모든 원소가 어떤 조건을 만족하는지 판단
  * any : 컬렉션 안에 어떤 조건을 만족하는 원소가 있는지 판단
  * count : 조건을 만족하는 원소의 개수 반환
  * find : 조건을 만족하는 첫 번째 원소 반환
</br>

* groupBy: 리스트를 여러 그룹으로 이뤄진 맵으로 변경
  * 특성을 파라미터로 전달하면 컬렉션을 자동으로 구분해주는 함수
  ```kotlin
  val list = listOf("a", "ab", "b")
  println(list.groupBy(String::first)) // {a=[a, ab], b=[b]}
  ``` 
</br>

* flatMap과 flatten: 중첩된 컬렉션 안의 원소 처리
  * flatMap
    * 인자로 주어진 람다를 컬렉션의 모든 객체에 적용하고(또는 매핑) 람다를 적용한 결과 얻어지는 여러 리스트를 한 리스트로 한데 모은다(또는 펼치기 flatten)
    ```kotlin
    val strings = listOf("abc", "def")
    println(strings.flatMap { it.toList() }) // [a, b, c, d, e, f]
    ```
  * 중첩된 리스트의 원소를 한 리스트로 모아야 한다면 flatMap
  * 특별히 반환해야 할 내용이 없다면 flatten
</br>

## 요약

</br>
