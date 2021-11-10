# 4장. 클래스, 객체, 인터페이스

## 클래스 계층 정의
* 코틀린 인터페이스
  * 추상 메소드뿐 아니라 구현이 있는 메소드도 정의 가능하지만 아무런 상태(필드)도 들어갈 수 없음
  * 클래스 이름 뒤에 콜론(:)을 붙이고 인터페이스와 클래스 이름을 적는 것으로 클래스 확장과 인터페이스 구현 처리
  ```kotlin
  // 단순한 인터페이스 구현
  class Button : Clickable {
    override fun click() = println("Clicked")
  }
  
  Button().click()
  // 출력값 Clicked
  ```
  * 인터페이스는 제한 없이 구현 가능하지만, 클래스는 오직 하나만 확장 가능
  * override 변경자는 상위 클래스나 상위 인터페이스에 있는 프로퍼티나 메소드를 오버라이드한다는 표시
  * 인터페이스 메소드도 디폴트 구현 제공 가능 -> 메소드 본문을 메소드 시그니처 뒤에 추가
  ```kotlin
  interface Clickable {
    fun click() // 일반 메소드 선언
    fun showOff() = println("Clickable") // 디폴트 구현이 있는 메소드
  }
  ```
  * 한 클래스에서 두 인터페이스를 함께 구현한다면 두 메소드를 아우르는 구현을 하위 클래스에 직접 구현
</br>

* open, final, abstract 변경자: 기본적으로 final
  * 취약한 기반 클래스
    * 하위 클래스가 기반 클래스에 대해 가졌던 가정이 기반 클래스를 변경함으로써 깨져버린 경우
    * 상속을 위한 설계와 문서를 갖추거나, 그럴 수 없다면 상속을 금지하라 
    * -> 메소드의 기본은 final
  * open
    * 어떤 클래스에 상속을 허용
    * 오버라이드를 허용하고 싶은 메소드나 프로퍼티의 앞에
  ```kotlin
  // 열린 메소드를 포함하는 열림 클래스 정의하기
  open class RichButton : Clickable { // 열린 클래스 -> 다른 클래스가 이 클래스 상속 가능
    fun disable() {} // 파이널 함수 -> 하위 클래스가 이 메소드 오버라이드 가능
    open fun animate() {} // 열린 함수 -> 하위 클래스에서 이 메소드 오버라이드 가능
    override fun click() {} // 상위 클래스에서 열려 있는 메소드를 오버라이드. 오버라이드한 메소드는 기본으로 열림
  }
  ```
  * 클래스 내에서 상속 제어 변경자의 의미
    * final : 오버라이드 X, 클래스 멤버의 기본 변경자
    * open : 오버라이드 O, 반드시 open을 명시해야 오버라이드 가능
    * abstract : 반드시 오버라이드, 추상 클래스 멤버에만 변경자 가능하며 추상클래스 멤버에는 구현이 있으면 안됨
    * override : 상위 클래스나 상위 인스턴스의 멤버를 오버라이드 하는 중, 오버라이드하는 멤버는 기본적으로 열린 상태, 하위 클래스의 오버라이드를 금지하려면 final 명시
</br>

* 가시성 변경자: 기본적으로 공개
  * 코드 기반에 있는 선언에 대한 클래스 외부 접근을 제어
  * 코틀린의 가시성 변경자
    * public(기본) : (클래스 멤버) 모든 곳에서 볼 수 있음 / (최상위 선언) 모든 곳에서 볼 수 있음
    * internal : (클래스 멤버) 같은 모듈 안에서만 볼 수 있음 / (최상위 선언) 같은 모듈 안에서만 볼 수 있음
    * protected : (클래스 멤버) 하위 클래스 안에서만 볼 수 있음 / (최상위 선언) 최상위 선언에 적용할 수 없음
    * private : (클래스 멤버) 같은 클래스 안에서만 볼 수 있음 / (최상위 선언) 같은 파일 안에서만 볼 수 있음
</br>

* 내부 클래스와 중첩된 클래스: 기본적으로 중첩 클래스
  * 코틀린의 중첩 클래스는 명시적으로 요청하지 않는 한 바깥쪽 클래스 인스턴스에 대한 접근 권한이 없음 
  ```kotlin
  // 중첩 클래스를 사용해 코틀린에서 View 구현
  class Button : View{
    override fun getCurrentState(): State = ButtonState()
    override fun restoreState(state: State) { /*...*/ } 
    class ButtonState : Statue { /*...*/ } // 이 클래스는 자바의 정적 중첩 클래스와 대응
  }
  ```
  * 자바와 코틀린의 중첩 클래스와 내부 클래스의 관계 (클래스 B 안에 정의된 클래스 A )
    * class A : 중첩 클래스 (바깥쪽 클래스에 대한 참조를 저장하지 않음)
    * inner class A : 내부 클래스 (바깥쪽 클래스에 대한 참조를 저장)
  * 내부 클래스 Inner 안에서 바깥쪽 클래스 Outer의 참조에 접근하려면 this@Outer라고 써야 함 
</br>

* 봉인된 클래스: 클래스 계층 정의 시 계층 확장 제한
  * 코틀린 컴파일러는 when을 사용해 Expr 타입의 값을 검사할 때 꼭 디폴트 분기인 else 분기를 덧붙이게 강제함 -> 문제발생
  * 클래스에 sealed 변경자를 붙이면 그 상위 클래스를 상속한 하위 클래스 정의 제한 가능
  * sealed 클래스의 하위 클래스를 정의할 때는 반드시 상위 클래스 안에 중첩
  ```kotlin
  //sealed 클래스로 식 표현
  sealed class Expr { // 기반 클래스를 sealed로 봉인
    class Num(val value: Int) : Expr() // 기반 클래스의 모든 하위 클래스를 중첩 클래스로 나열
    class Sum(val left: Expr, val right: Expr) : Expr()
  }
  
  fun eval (e: Expr): Int =
    when (e) { // when 식이 모든 하위 클래스를 검사하므로 별도의 else 분기가 없어도 됨
      is Expr.Num -> e.value
      is Expr.Sum -> eval (e.right) + eval(e.left)
    }
  ```
</br>

## 뻔하지 않은 생성자와 프로퍼티를 갖는 클래스 선언
* 주 생성자와 부 생성자 구분
  * 주 생성자 : 클래스를 초기화할 때 주로 사용하는 간략한 생성자, 클래스 본문 밖에서 정의
  * 부 생성자 : 클래스 본문 안에서 정의 
* 초기화 블록을 통해 초기화 로직 추가 가능
</br>

* 클래스 초기화: 주 생성자와 초기화 블록
  * 주 생성자
    * 클래스 이름 뒤에 오는 괄호로 둘러싸인 코드
    * 생성자 파라미터를 지정하고 그 생성자 파라미터에 의해 초기화되는 프로퍼티를 정의하는 목적
  </br>
  
    ```kotlin
    class User constructor(_nickname: String) { // 파라미터가 하나만 있는 주 생성자
      val nickname: String
      init { // 초기화 블록
        nickname = _nickname
      }
    }
    ``` 
    * constructor 키워드 : 주 생성자나 부 생성자 정의를 시작할 때 사용
    * init 키워드 : 초기화 블록 시작
    * 초기화 블록에는 클래스의 객체가 만들어질 때 실행될 초기화 코드가 들어감
    * _은 프로퍼티와 생성자 파라미터를 구분
    * 주 생성자 앞에 별다른 애노테이션이나 가시성 변경자가 없다면 constructor 생략 가능
  </br>
  
    * 주 생성자 파라미터 이름 앞에 val을 추가하는 방식으로 프로퍼티 정의와 초기화를 간략하게 사용 가능
    * 생성자 파라미터에도 디폴트 값 정의 가능
    * 클래스의 인스턴스를 만들려면 new 키워드 없이 생성자 직접 호출
    * 클래스에 기반 클래스가 있다면 주 생성자에서 기반 클래스의 생성자 호출
      * 기반 클래스 초기화는 기반 클래스 이름 뒤에 괄호를 치고 생성자 인자를 넘김
    * 클래스를 정의할 때 별도로 생성자를 정의하지 않으면 컴파일러가 자동으로 아무 일도 하지 않는 인자가 없는 디폴트 생성자를 만들어줌
      * 클래스 이름 뒤에는 꼭 빈 괄호가 들어감
    * 주 생성자에는 private 변경자를 붙일 수 있음
</br>

* 부 생성자: 상위 클래스를 다른 방식으로 초기화
  * constructor 키워드로 시작
  * 부 생성자 선언 갯수는 자유
  * this()를 통해 클래스 자신의 다른 생성자 호출 가능
  * 클래스에 주 생성자가 없다면 모든 부 생성자는 반드시 상위 클래스를 초기화하거나 다른 생성자에게 생성을 위임
  ```kotlin
  class MyButton : View {
    constructor(ctx: Context)
      : super(ctx) { // 상위 클래스의 생성자를 호출
      // ...
    }
    constructor (ctx: Context, attr: AttributeSet)
      : super(ctx, attr) { // 상위 클래스의 생성자를 호출
      // ...
    }
  }
  ```
  
  ```kotlin
  class MyButton : View {
    constructor(ctx: Context)
      : this(ctx, MY_STYLE) { // 이 클래스의 다른 생성자에게 위임한다
      // ...
    }
    constructor (ctx: Context, attr: AttributeSet)
      : super(ctx, attr) {
      // ...
    }
  }
  ```
</br>

* 인터페이스에 선언된 프로퍼티 구현
  * 인터페이스에 있는 프로퍼티 선언에는 뒷받침하는 필드나 게터 등의 정보가 없음
  * 상태를 저장할 필요가 있다면 인터페이스를 구현한 하위 클래스에서 상태 저장을 위한 프로퍼티 필요
  ```kotlin
  // 인터페이스의 프로퍼티 구현
  class PrivateUser (override val nickname: String) : User // 주 생성자에 있는 프로퍼티
  
  class SubscribingUser (val email: String) : User {
    override val nickname: String
      get() = email.substringBefore('@') // 커스텀 게터. 프로퍼티에 뒷받침하는 필드가 없는 대신 매번 결과를 계산해 돌려줌
  }
  
  class FacebookUser (val accountId: Int) : User {
    override val nickname = getFacebookName (accountId) // 프로퍼티 초기화 식
  }
  
  println(PrivateUser("text@kotlinlang.org").nickname) //test@kotlinlang.org
  println(SubscribingUser("test@kotlinlang.org").nickname) //test
  ```
</br>

* 게터와 세터에서 뒷받침하는 필드에 접근
  * 코틀린에서 프로퍼티의 값을 바꿀 때는 필드 설정 구문 사용 -> 내부적으로 세터 호출
  * 접근자의 본문에는 field라는 특별한 식별자를 통해 뒷받침하는 필드에 접근 가능
    * 게터에서는 field의 값을 읽을 수 있음
    * 세터에서는 field 값을 읽너가 쓸 수 있음
  * 변경 가능 프로퍼티의 게터와 세터 중 한쪽만 직접 정의해도 됨
  * field를 사용하지 않는 커스텀 접근자 구현을 정의한다면 뒷받침하는 필드는 존재하지 않음
  ```kotlin
  class User(val name: String) {
    var address: String = "a"
      set(value: String) {
        println("""
          Address was changed for $name:
          "$field" -> "$value".""".trimIndent()) // 뒷받침하는 필드 값 얻기
        field = value   // 뒷받침하는 필드 값 변경
      }
  }
  ```   
</br>

* 접근자의 가시성
  * 프로퍼티의 가시성과 같음
  * 원한다면 get이나 set앞에 가시성 변경자를 추가해 접근자의 가시성 변경 가능
  ```kotlin
  // 비공개 세터가 있는 프로퍼티 선언
  class LengthCounter { 
    var counter: Int = 0
      private set // 이 클래스의 밖에서 이 프로퍼티의 값을 바꿀 수 없음
    fun addWord(word: String) {
      counter += word.length
    }
  }
  ``` 
</br>

## 컴파일러가 생성한 메소드: 데이터 클래스와 클래스 위임
* 문자열 표현: toString()
* 객체의 동등성: equals()
  * 코틀린에서 == 연산자는 참조 동일성을 검사하지 않고 객체의 동등성을 검사
* 해시 컨테이너: hashCode()
  * equals를 오버라이드할 때 반드시 hashCode도 함께 오버라이드 해야 함
  * equals()가 true를 반환하는 두 객체는 반드시 같은 hashCode()를 반환
  * processed 집합은 HashSet이고, HashSet은 원소를 비교할 때 비용을 줄이기 위해 객체의 해시 코드를 먼저 비교하고 해시 코드가 같은 경우에만 실제 값을 비교
  ```kotlin
  // Client에 equals() 구현
  class Client (val name: String, val postalCode: Int) {
    override fun equals(other: Any?) : Boolean { // Any는 java.lang.Object에 대응하는 클래스로, 코틀린의 모든 클래스의 최상위 클래스. ?로 null 가능
      if (other == null || other !is Client) // other가 Client인지 검사
        return false
      return name == other.name && postalCode == other.postalCode // 두 객체의 프로퍼티 값이 같은지 검사
    }
    override fun toString() = "Client(name=$name, postalCode=$postalCode)"
    override fun hashCode() : Int = name.hashCode() * 31 + postalCode
  }
  ``` 
</br>

* 데이터 클래스와 불변성: copy() 메소드
  * 데이터 클래스의 모든 프로퍼티를 읽기 전용으로 만들어서 데이터 클래스를 불변 클래스로 만들라고 권장
  * HashMap 등의 컨테이너에 데이터 클래스 객체를 담는 경우 불변성이 필수
  * 데이터 클래스 인스턴스를 불변 객체로 쉽게 활용할 수 있게 객체를 복사하면서 일부 프로퍼티를 바꿀 수 있게 해주는 copy메소드 사용
  ```kotlin
    class Client (val name: String, val postalCode: Int) {
    ...
    fun copy(name: String = this.name, postalCode: Int = this.postalCode) =
      Client(name, postalCode
    }
  ``` 
</br>

* 클래스 위임: by 클래스 사용
  * 상속을 허용하지 않는 클래스에 새로운 동작을 추가해야 할 때 -> 데코레이터 패턴 사용
    * 상속을 허용하지 않는 클래스 대신 사용할 수 있는 새로운 클래스를 만들되 기존 클래스와 같은 인터페이스를 데코레이터가 제공하게 만들고, 기존 클래스를 데코레이터 내부에 필드로 유지
    * 새로 정의해야 하는 기능은 데코레이터 메소드에 새로 정의
    * 기존 기능이 그대로 필요한 부분은 데코레이터의 메소드가 기존 클래스의 메소드에게 요청을 전달
    * -> 준비코드가 많이 필요하다는 단점 존재
  * by 키워드를 통해 인터페이스에 대한 구현을 다른 객체에게 위임 중이라는 사실 명시 가능
    * 메소드 중 일부의 동작을 변경하고 싶은 경우 메소드를 오버라이드
  ```kotlin
  // 클래스 위임 사용
  class CountingSet<T> (
    val innerSet: MutableCollection<T> = HashSet<T>()
  ) : MutableCollection<T> by innerSet { // MutableCollection의 구현을 innerSet에게 위임
    var objectsAdded = 0
    override fun add(element: T) : Boolean { // 위임하지 않고 새로운 구현 제공
      objectAdded++
      return innserSet.add(element)
    }
    override fun addAll(c: Collection<T>) : Boolean { // 위임하지 않고 새로운 구현 제공
      objectsAdded += c.size
      return innerSet.addAll(c)
    }
  }
  ```
</br>

## object 키워드: 클래스 선언과 인스턴스 생성
* 객체 선언은 싱글턴을 정의하는 방법 중 하나
* 동반 객체는 인스턴스 메소드는 아니지만 어떤 클래스와 관련 있는 메소드와 팩토리 메소드를 담을 때 사용. 동반 객체 메소드에 접근할 때는 동반 객체가 포함된 클래스의 이름 사용 가능
* 객체 식은 자바의 무명 내부 클래스 대신 사용
</br>

* 싱글턴 쉽게 만들기
  * 객체 선언 기능을 통해 싱글턴을 언어에서 기본 지원
  * 객체 선언은 클래스 선언과 그 클래스에 속한 단일 인스턴스의 선언을 합친 선언
  * 객체 선언은 object 키워드로 시작
  * 객체 선언은 클래스를 정의하고 그 클래스의 인스턴스를 만들어서 변수에 저장하는 모든 작업을 한 문장으로 처리
  * 싱글턴 객체는 객체 선언문이 있는 위치에서 생성자 호출 없이 즉시 만들어짐 -> 객체 선언에서 생성자 정의 필요 없음
  * 객체 선언에 사용한 이름 뒤에 마침표(.)를 붙이면 객체에 속한 메소드나 프로퍼티에 접근 가능
  ```kotlin
  // 객체 선언을 사용해 Comparator 구현
  object CaseInsensitiveFileComparator : Comparator<File> {
    override fun compare(file1: File, file2: File) : Int {
      return file1.path.compareTo(file2.path, ignoreCase = true)
    }
  }
  ```
</br>

* 동반 객체: 팩토리 메소드와 정적 멤버가 들어갈 장소
  * 팩토리 메소드
    * 클래스의 인스턴스와 관계없이 호출해야 하지만, 클래스 내부 정보에 접근해야 하는 함수가 필요할 때 클래스에 중첩된 객체 선언의 멤버 함수로 정의해야 함
  * 클래스 안에 정의된 객체 중 하나에 companion이라는 표시를 붙이면 클래스의 동반 객체로 만들 수 있음 
  * 동반 객체의 프로퍼티나 메소드에 접근하려면 동반 객체가 정의된 클래스 이름 사용
  ```kotlin
  // 부 생성자를 팩토리 메소드로 대신하기
  class User private constructor(val nickname: String) { // 주 생성자를 비공개로 만듦
    companion object { // 동반 객체 선언
      fun newSubscribingUser(email: String) = 
        User(email.substringBefore('@'))
      fun newFacebookUser(accountId: Int) = // 페이스북 사용자 ID로 사용자를 만드는 팩토리 메소드
        User(getFacebookName(accountId))
    }
  }
  ```
</br>

* 동반 객체를 일반 객체처럼 사용
  * 동반 객체는 클래스 안에 정의된 일반 객체
  * 동반 객체에 이름을 붙이거나, 인터페이스를 상속하거나, 동반 객체 안에 확장 함수와 프로퍼티 정의 가능
  ```kotlin
  // 동반 객체에 대한 확장 함수 정의
  
  // 비즈니스 로직 모듈
  class Person(val firstName: String, val lsatName: String) {
    companion object { // 비어있는 동반 객체 선언
    }
  }
  
  // 클라이언트 서버 통신 모듈
  fun Person.Companion.fromJSON(json: String) : Person { // 확장 함수 선언
    ...
  }
  
  val p = Person.fromJSON(json)
  ```
</br>

* 객체 식: 무명 내부 클래스를 다른 방식으로 작성
  * 무명 객체를 정의할 때도 object 키워드 사용
  * 무명 객체는 자바의 무명 내부 클래스 대신 
  ```kotlin
  // 무명 객체 안에서 로컬 변수 사용
  fun countClicks(window: Window) {
    val clickCount = 0 // 로컬 변수 정의
    window.addMouseListener(object: MouseAdapter() {
      override fun mouseClicked(e: MouseEvent) {
        clickCount++ // 로컬 변수의 값을 변경
      }
    })
    // ...
  }
  ```  
</br>

## 요약
* 코틀린의 인터페이스는 디폴트 구현을 포함할 수 있고, 프로퍼티도 포함할 수 있음
* 모든 코틀린 선언은 기본적으로 final이며 public
* 선언이 final이 되지 않게 만들려면(상속과 오버라이딩이 가능하게 하려면) 앞에 open 사용
* internal 선언은 같은 모듈 안에서만 볼 수 있음
* 중첩 클래스는 기본적으로 내부 클래스가 아님. 바깥쪽 클래스에 대한 참조를 중첩 클래스 안에 포함시키려면 inner 키워드를 중첩 클래스 선언 앞에 붙여 내부 클래스로 변경
* sealed 클래스를 상속하는 캘래스를 정의하려면 반드시 부모 클래스 정의 안에 중첩 또는 내부 클래스로 정의
* 초기화 블록과 부 생성자를 활용해 클래스 인스턴스를 더 유연하게 초기화 가능
* field 식별자를 통해 프로퍼티 접근자(게터와 세터) 안에서 프로퍼티의 데이터를 저장하는 데 쓰이는 뒷받침하는 필드 참조 가능
* 데이터 클래스를 사용하면 컴파일러가 equals, hashCode, toString, copy 등의 메소드를 자동으로 생성
* 클래스 위임을 사용하면 위임 패턴을 구현할 때 필요한 많은 준비 코드 감소
* 객체 선언을 사용하면 코틀린답게 싱글턴 클래스 정의 가능
* 동반 객체는 자바의 정적 메소드와 필드 정의를 대신함
* 동반 객체도 다른 (싱글턴) 객체처럼 인터페이스 구현 가능. 외부에서 동반 객체에 대한 확장 함수와 프로퍼티 정의 가능
* 코틀린의 객체 식은 자바의 무명 내부 클래스를 대신하지만 자바 무명 내부 클래스보다 많은 기능 

</br>
