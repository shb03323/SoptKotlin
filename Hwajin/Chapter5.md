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
## 지연 계산(lazy) 컬렉션 연산
* 시퀀스를 사용하면 중간 임시 컬렉션을 사용하지 않고도 컬렉션 연산은 연쇄할 수 있음
```kotlin
people.asSequence() // 원본 컬렉션을 시퀀스로 변환
   .map(Person::name) // 시퀀스도 컬렉션과 똑같은
   .filter { it.startsWith("A") } // API를 제공
   .toList() // 결과 시퀀스를 다시 리스트로 변환
```
* 코틀린 지연 계산 시퀀스는 SEquence 인터페이스에서 시작
* Sequence 안에는 iterator라는 단 하나의 메소드 존재 - 메소드를 통해 시퀀스로부터 원소값을 얻을 수 있음
* Sequence 인터페이스의 강점
  * 중간 처리 결과를 저장하지 않고도 연산을 연쇄적으로 적용해서 효율적으로 계산 수행
</br>

* 시퀀스 연산 실행: 중간 연산과 최종 연산
  * 중간 연산: 다른 시퀀스를 반환
  * 최종 연산: 결과를 반환
  * 위의 예시에서 map{}.filter{}는 중간연산. toList()는 최종연산
  * 원소에 연산을 차례대로 적용하다가 결과가 얻어지면 그 이후의 원소에 대해서는 변환이 이루어지지 않을 수도 있음
  * 컬렉션에 대해 수행하는 연산의 순서도 성능에 영향을 끼침
</br>

* 시퀀스 만들기 
  * asSequence()를 호출해 시퀀스를 만들었음
  * generateSequence 함수를 사용해서 만들 수도 있음
  ```kotlin
  // 자연수의 시퀀스를 생성하고 사용
  val naturalNumbers = generateSequence(0) { it + 1 }
  val numberTo100 = naturalNumbers.takeWhile {it <= 100 }
  println(numbersTo100.sum()) // 모든 지연 연산읜 sum의 결과를 계산할 때 수행
  ``` 
</br>

## 자바 함수형 인터페이스 활용
* 함수형 인터페이스 or SAM 인터페이스
  * 추상메소드가 하나만 있는 인터페이스
  * SAM : 단일 추상 메소드
</br>
* 자바 메소드에 람다를 인자로 전달
  * 객체를 명시적으로 선언하는 경우 메소드를 호출할 때마다 새로운 객체 생성 but 람다는 정의가 들어있는 함수의 변수에 접근하지 않는 람다에 대응하는 무명 객체를 메소드를 호출할 때마다 반복 사용
  * 람다가 주변 영역의 변수를 포획한다면 매 호출마다 같은 인스턴스를 사용할 수 없음 -> 컴파일러가 매번 주변 영역의 변수를 포획한 새로운 인스턴스 생성
  * 대부분의 경우 람다와 자바 함수형 인터페이스 사이의 변환은 자동으로 이루어짐
</br>
* SAM 생성자: 람다를 함수형 인터페이스로 명시적으로 변경
  * SAM 생성자는 함수형 인터페이스의 인스턴스로 변환할 수 있게 컴파일러가 자동으로 생성한 함수
  * 컴파일러가 자동으로 람다를 함수형 인터페이스 무명 클래스로 바꾸지 못하는 경우 SAM 생성자 사용 가능
  * 람다로 생성한 함수형 인터페이스 인스턴스를 변수에 저장해야 하는 경우에도 SAM 생성자 사용 가능
  * 함수형 인터페이스를 요구하는 메소드를 호출할 때 대부분의 SAM 변환을 컴파일러가 자동으로 수행할 수 있지만, 가끔 오버로드한 메소드 중에서 어떤 타입의 메소드를 선택해 람다를 변환해 넘겨줘야 할지 모호한 때 명시적으로 SAM 생성자를 적용하면 컴파일 오류를 피할 수 있음
  ```kotlin
  // SAM 생성자를 사용해 값 반환하기  
  fun createAllDoneRunnable() : Runnable {
     return Runnable {println("Done!") }
  }
  >>> createAllDoneRunnable().run() // Done!
  ``` 
</br>

## 수신 객체 지정 람다: with와 apply
* 수신 객체 지정 람다
  * 수신 객체를 명시하지 않고 람다의 본문 안에서 다른 객체의 메소드를 호출할 수 있게 하는 것
  * with 함수
    * 어떤 객체의 이름을 반복하지 않고도 그 객체에 대해 다양한 연산을 수행하도록 도와줌
    * with가 반환하는 값은 람다 코드를 실행한 결과, 그 결과는 람다 식의 본문에 있는 마지막 식의 값
    ```kotlin
    // with을 사용해 알파벳 만들기
    fun alphabet () : String {
       val stringBuilder = StringBuilder()
       return with(stringBuilder) { // 메소드를 호출하려는 수신 객체 지정
          for (letter in 'A'..'Z') {
             this.append(letter) // this를 명시해 앞에 지정한 수신 객체의 메소드 호출
          }
          append("\nNow I know the alphabet!") //this를 생략하고 메소드 호출
          this.toString() //람다에서 값 반환
       }
    }
    ```  
  * apply 함수
    * 람다의 결과 대신 수신 객체가 필요한 경우
    * 항상 자신에게 전달된 객체(수신객체)를 반환
    * 객체의 인스턴스를 만들면서 즉시 프로퍼티 중 일부를 초기화해야 하는 경우 
    ```kotlin
    // apply를 사용해 알파벳 만들기
    fun alphabet () = StringBuilder().apply {
       for (letter in 'A'..'Z') {
          append(letter)
       }
       append("\nNow I know the alphabet!")
    }.toString
    ``` 
</br>

## 요약
* 람다를 사용하면 코드 조각을 다른 함수에게 인자로 넘길 수 있음
* 코틀린에서 람다가 함수 인자인 경우 괄호 밖으로 람다를 빼낼 수 있고, 람다의 인자가 단 하나뿐인 경우 인자 이름을 지정하지 않고 it이라는 티폴트 이름으로 부를 수 있음
* 람다 안에 있는 코드는 그 람다가 들어있는 바깥 함수의 변수를 읽거나 쓰기 기능
* 메소드, 생성자, 프로퍼티 이름 앞에 ::을 붙이면 각각에 대한 참조를 만들 수 있음. 그런 참조를 람다 대신 다른 함수에게 넘길 수 있음
* filter, map, all, any 등의 함수를 활용하면 컬렉션에 대한 대부분의 연산을 직접 원소를 이터레이션하지 않고 수행 가능
* 시퀀스를 사용하면 중간 결과를 담는 컬렉션을 생성하지 않고도 컬렉션에 대한 여러 연산 조합 가능
* 함수형 인터페이스(추상메소드가 하나뿐인 SAM 인터페이스)를 인자로 받는 자바 함수를 호출할 경우 람다를 함수형 인터페이스 인자 대신 넘길 수 있음
* 수신 객체 지정 람다를 사용하면 람다 안에서 미리 정해둔 수신 객체의 메소드 직접 호출 가능
* 표준 라이브러리의 with함수를 사용하면 어던 객체에 대한 참조를 반복해서 언급하지 않으면서 그 객체의 메소드를 호출할 수 있음. apply를 사용하면 어떤 객체라도 빌더 스타일의 API를 사용해 생성하고 초기화할 수 있음
</br>
