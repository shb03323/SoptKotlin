## 4. 클래스, 객체, 인터페이스



### 4.1 클래스 계층 정의

가시성/접근 변경자는 자바와 코틀린이 비슷하지만, 기본 가시성은 다르다.

또한 코틀린에는 클래스 상속을 제한하는 sealed 변경자가 추가되었다.



#### 4.1.1 코틀린 인터페이스

인터페이스 안에는 추상 메소드나 구현된 메소드를 정의할 수 있지만, 필드는 들어갈 수 없다.

`interface` 키워드로 정의하고, `:` 을 이용해 구현한다.

자바와 마찬가지로 상속은 하나의 부모 클래스를 가질 수 있고, 인터페이스에는 제한이 없다.

자바의 `@Override`어노테이션 대신 `override` 키워드를 사용한다. (**필수**)

인터페이스의 메소드도 디폴트 구현을 가질 수 있다.

```kotlin
interface Clickable{ 
    fun click()
    fun showOff() = println("I'm clickable")		// 디폴트 구현이 있는 메소드
}

class Button: Clickable{
    override fun click() = println("I was clicked")
}
```



- 서로 이름과 시그니처가 같은 메소드를 가졌고, 둘 이상의 디폴트 구현이 있는 인터페이스들을 구현할 때

  ```kotlin
  interface Focusable{
      fun setFocus(b: Boolean) = println("I ${if (b) "got" else "lost"} focus.")
      fun showOff() = println("I'm focusable")
  }
  
  class Button: Clickable, Focusable{
      override fun click() = println("I was clicked")
      override fun showOff(){
          super<Clickable>.showOff()		// 어떤 상위 타입의 멤버 메소드를 호출할지 지정할 수 있다.
          super<Focusable>.showOff()		// 자바에서는 Focusable.super.showOff()으로 사용한다.
      }
  }
  ```

  물론 원하는 메소드 중 하나만 호출하도록 할 수 있다.

  ```kotlin
  override fun showOff() = super<Clickable>.showOff()
  ```



#### 4.1.2 open, final, abstract 변경자: 기본적으로 final

- 취약한 기반 클래스 (fragile bass class)

  하위 클래스가 기반 클래스에 대해 가졌던 가정이 기반 클래스를 변경함으로써 깨져버린 경우

> 클래스의 클라이언트는 기반 클래스를 작성한 사람의 의도와 다른 방식으로 메소드를 오버라이딩할 위험이 있기 때문에, 상속을 위한 설계와 문서를 갖추지 않은 클래스에서는 상속을 금지하는 것이 좋다.

따라서 자바에서는 기본적으로 상속을 허용하지만, 코틀린에서는 <u>클래스와 메소드가 기본적으로 final이다.</u>

`open` 키워드를 이용해서 상속을 허용할 수 있다.



```kotlin
open class RichButton: Clickable{
    fun disable() { ... }			//  final method
    open fun animate() { ... }		//	오버라이딩할 수 있는 메소드
    override fun click() { ... }	// 오버리이딩한 메소드이기 때문에 기본적으로 open이다.
}
```

기반 클래스에서 오버라이딩한 메소드는 `open`이 default이기 때문에 오버라이드를 금지하기 위해서는 `final`을 명시해야 한다.



추상 클래스는 `abstract` 키워드를 이용해 선언할 수 있으며 abstract 멤버의 default는 `open` 이기 때문에 명시할 필요가 없다.

abstract가 아닌 멤버는 기본적으로 final이기 때문에 open으로 오버라이드를 허용할 수 있다.

```kotlin
abstract class Animated{
	abstract fun animate()					// abstract이기 때문에 override 허용
    open fun stopAnimating(){ ... }			// open으로 override 허용
    fun animateTwice(){ ... }				// default로 final이 적용됨
}
```



##### access modifier

| 변경자   | 변경자가 붙은 멤버는                                   | 설명                                                      |
| -------- | ------------------------------------------------------ | --------------------------------------------------------- |
| final    | 오버라이드 **불가**                                    | 클래스 멤버의 default modifier                            |
| open     | 오버라이드 **가능**                                    | open을 명시해야 오버라이드 가능                           |
| abstract | 오버라이드 **필수**                                    | 추상 클래스의 멤버에만 붙일 수 있으며, 구현이 있으면 안됨 |
| override | 상위 클래스나 상위 인스턴스의 멤버를 오버라이드하는 중 | default로 open이 설정되어 있음                            |



#### 4.1.3 가시성 변경자: 기본적으로 공개

자바에서는 default visibility modifier가 `package-private`이지만, 코틀린에서는 `public` 이다.

또한 패키지를 namespace를 관리하기 위한 용도로만 사용하기 때문에 같은 **모듈** 내에서만 볼 수 있는 `internal`이 있다.

- 모듈 (module)

  한 번에 한꺼번에 컴파일되는 코틀린 파일들을 의미한다. 인텔리J나 이클립스, 메이븐, 그레이들 등의 프로젝트가 모듈이 될 수 있다.



##### visibility modifier

| 변경자           | 클래스 멤버          | 최상위 선언               |
| ---------------- | -------------------- | ------------------------- |
| public (default) | 모든 곳에서          | 모든 곳에서               |
| internal         | 같은 모듈 안에서만   | 같은 모듈 안에서만        |
| protected        | 하위 클래스 안에서만 | (최상위 선언에 적용 불가) |
| private          | 같은 클래스 안에서만 | 같은 파일 안에서만        |



- 하위 구조의 가시성이 상위 구조보다 높을 경우 가시성 규칙 위반으로 인한 컴파일 오류가 발생한다.

  ```kotlin
  internal open class TalkactiveButton: Focusable{
      private fun yell() = println("Hey")
      protected fun whisper() = println("Let's talk")
  } 
  fun TalkactiveButton.giveSpeech(){	// 오류 : public 멤버가 internal 타입인 TalkactiveButton을 노출함
      yell()							// 오류 : 접근 불가(private)
      whisper()						// 오류 : 접근 불가(protected)
  }
  ```



자바에서는 같은 패키지 안에서 `protected` 멤버에 접근 가능하지만, 코틀린에서는 상속해야지만 접근 가능하다.



#### 4.1.4 내부 클래스와 중첩된 클래스: 기본적으로 중첩 클래스

코틀린의 중첩 클래스(nested class)는 명시적으로 요청하지 않는 한 바깥 클래스 인스턴스에 대한 접근 권한이 없다. (자바의 `static` nested class와 같다.)

`inner` 클래스로 선언하면 `this@`를 사용해서  외부 클래스에 대한 참조를 사용할 수 있다.



- static nested class를 코틀린에서 구현하였을 때

  ```kotlin
  class Button: View{
      override fun getCurrentState(): State = ButtonState()
      override fun restoreState(state: State) { ... }
      class ButtonState: State { ... }
  }
  ```

- 외부 클래스에 대해 참조를 사용하는 inner 클래스를 사용하였을 때

  ```kotlin
  class Outer{
      inner class Inner{
          fun getOuterReference(): Outer = this@Outer
      }
  }
  ```

  

#### 4.1.5 봉인된 클래스: 클래스 계층 정의 시 계층 확장 제한

`sealed` 클래스의 하위 클래스를 정의할 때는 반드시 상위 클래스 안에 중첩시켜야 한다. (sealed 클래스는 자동으로 open이다.)

when에서 타입 검사를 진행할 때 디폴트 분기인 else를 강제하지만, sealed 클래스의 모든 하위 클래스를 처리한다면 디폴트 분기를 사용하지 않아도 된다.



- 인터페이스를 구현했을 때 타입 검사

  ```kotlin
  interface Expr
  class Num(val value: Int): Expr
  class Sum(val left: Expr, val right: Expr): Expr
  fun eval(e: Expr): Int = 
  	when(e){
          is Num -> e.value
          is Sum -> eval(e.right) + eval(e.left)
          else ->														// else가 반드시 필요
          	throw IllegalArgumentException("Unknown expression")
      }
  ```

- sealed 클래스를 사용했을 때 타입 검사

  ```kotlin
  sealed class Expr{
      class Num(val value: Int): Expr()
      class Sum(val left: Expr, val right: Expr): Expr()
  }
  fun eval(e: Expr): Int = 
  	when(e){													// sealed 클래스의 모든 하위 클래스가 처리되어서 else가 필요없음
          is Expr.Num -> e.value
          is Expr.Sum -> eval(e.right) + eval(e.left)
      }
  ```



### 4.2 뻔하지 않은 생성자와 프로퍼티를 갖는 클래스 선언

주 (primary) 생성자와 부 (secondary) 생성자를 구분하며, initializer block을 통해 초기화 로직을 추가할 수 있다.



#### 4.2.1 클래스 초기화: 주 생성자와 초기화 블록

`constructor` 키워드로 주 생성자나 부 생성자 정의를 할 수 있으며, `init` 키워드로 초기화 블록을 시작할 수 있다.

주 생성자 앞에 별다른 어노테이션이나 가시성 변경자가 없다면 `constructor`를 생략해도 된다.

```kotlin
class User constructor(_nickname: String){
    val nickname: String
    init{
        nickname = _nickname
    }
}
```

`_`을 이용해서 프로퍼티와 생성자 파라미터를 구분한다. (`this`키워드를 사용해서 구분해도 된다.)



- 생성자 파라미터 앞에 `val`을 추가해서 프로퍼티 정의와 초기화를 간략하게 나타낼 수 있다.

  ```kotlin
  class User(val nickname: String)
  ```

- 생성자 파라미터에도 디폴트 값을 정의할 수 있다.

  ```kotlin
  class User(val nickname: String, val isSubscribed: Boolean = true)
  ```

- 클래스의 인스턴스를 생성할 때에는 `new` 키워드 없이 생성자를 호출하면 된다.

  ```kotlin
  val hyeong = User("형기")
  ```

- 생성자 파라미터를 상위 클래스 생성자로 넘겨서 초기화하면 된다. 이 때문에 인터페이스가 아닌 클래스를 상속하면 생성자를 호출하기 위해 `( )`가 붙는다.

  ```kotlin
  open class User(val nickname: String){ ... }
  class TwitterUser(nickname: String): User(nickname) { ... }
  ```

- 주 생성자를 `private`로 선언하여 클래스 외부에서 인스턴스화하지 못하도록 할 수 있다.

  ```kotlin
  class Secretive private constructor() { } 
  ```



#### 4.2.2 부 생성자: 상위 클래스를 다른 방식으로 초기화

`constructor` 키워드로 시작하는 부 생성자를 여러 개 만들 수 있으며, `super()` 키워드를 통해 상위 클래스 생성자를 호출할 수 있다.

```kotlin
open class View{
    constructor(ctx: Context) { ... }
    constructor(ctx: Context, attr: AttributeSet) { ... }
}

class MyButton: View{
    constructor(ctx: Context): super(ctx) { ... }
    constructor(ctx: Context, attr: AttributeSet): super(ctx, attr) { ... }
}
```

> 디폴트 파라미터를 사용해서 해결하는 것이 좋지만, 자바 상호운용성과 파라미터에 따른 다른 인스턴스 생성 방법을 위해 부 생성자를 사용한다.



#### 4.2.3 인터페이스에 선언된 프로퍼티 구현

코틀린에서는 인터페이스에 추상 프로퍼티를 선언할 수 있는데, 인터페이스는 상태(필드)를 포함할 수 없으므로 하위 클래스에서 프로퍼티를 구현해야 한다.



- `override`를 통해 추상 프로퍼티를 구현한다.

  ```kotlin
  interface User{
      val nickname: String
  }
  
  class PrivateUser(override val nickname: String): User		// 주 생성자의 프로퍼티를 이용
  class SubscribingUser(val email: String): User{
      override val nickname: String							
      	get() = email.substringBefore('@')					// 커스텀 게터
  }
  class FacebookUser(val accountId: Int): User{
      override val nickname = getFacebookName(accountId)		// 프로퍼티 초기화식 이용
  }
  ```

- 인터페이스에서 필드가 없는 프로퍼티(상태를 저장하지 않고 결과만 반환)는 오버라이드하지 않아도 상속 가능

  ```kotlin
  interface User{
      val email: String,
      val nickname: String
      	get() = email.substringBefore('@')			// 필드가 없기 때문에 오버라이드하지 않아도 상속할 수 있다.
  }
  ```



#### 4.2.4 게터와 세터에서 뒷받침하는 필드에 접근

세터에서 `field` 식별자를 이용해 프로퍼티의 필드에 접근할 수 있다.

```kotlin
class User(val name: String){
    val address: String = "unspecified"
    	set(value: String){
            println("""
            Address was changed for $name:
            "$field" -> "$value".""".trimIndent()) 		// field 값을 읽어와서 로그를 남긴다.
            field = value								// field 값을 변경한다.
        }
}
```



#### 4.2.5 접근자의 가시성 변경

get이나 set 앞에 가시성 변경자를 추가해서 접근자의 가시성을 변경할 수 있다.

```kotlin
class LengthCounter{
    var counter: Int = 0
    	private set				// 이 클래스 밖에서 이 프로퍼티의 값을 바꿀 수 없다. (클라이언트에게 getter만 제공)
    fun addWord(word: String){
        counter += word.length
    }
}
```



### 4.3 컴파일러가 생성한 메소드: 데이터 클래스와 클래스 위임

코틀린은 equals, hashCode, toString 등의 메소드를 컴파일러가 보이지 않는 곳에서 자동으로 생성해준다. 



#### 4.3.1 모든 클래스가 정의해야 하는 메소드

- `toString`, `equals`, `hashCode` 메소드를 클래스에 맞게 직접 오버라이드하는 예제이다.

  ```kotlin
  class Client(val name: String, val postalCode: Int){
      override fun toString() = "Client(name=$name, postalCode=$postalCode)"
      override fun equals(other: Any?): Boolean {		// Any는 자바에서 Object에 대응하는 클래스로, 모든 클래스의 최상위 클래스
          if(other == null || other !is Client)
          	return false
          return name == other.name && postalCode == other.postalCode
      }
      override fun hashCode(): Int = name.hashCode()*31 + postalCode
  }
  ```



#### 4.3.2 데이터 클래스: 모든 클래스가 정의해야 하는 메소드 자동 생성

어떤 클래스가 데이터를 저장하는 역할만 한다면 이런 메소드를 IDE를 통하지 않고, `data` 클래스로 선언하면 코틀린 컴파일러가 모든 메소드를 제공한다.

(`equals`, `hashCode`, `toString` 등)

```kotlin
data class Client(val name: String, val postalCode: Int)
```



데이터 클래스의 프로퍼티는 `val`로 선언하여 immutable class로 만드는 것이 권장된다. (필수는 아니다.)

HashMap 등의 컨테이너에 데이터 클래스를 담기 위해서는 불변성이 필수적이기 때문이다.

따라서 프로퍼티를 불변으로 선언하되, 데이터를 변경할 일이 있다면 `copy()` 메소드를 사용해서 일부 프로퍼티만 수정한 새로운 인스턴스를 생성하는 것이 좋다.

`copy()` 메소드는 default 파라미터가 `this`의 필드를 가리키고 있기 때문에 수정할 값만 다음과 같이 넘겨주면 된다.

```kotlin
val lee = Client("이계영", 4122)
println(lee.copy(postalCode = 4000))
```



#### 4.3.3 클래스 위임: by 키워드 사용

- 데코레이터 (Decorator) 패턴

  상속을 허용하지 않는 클래스에 새로운 동작을 추가하기 위해서 상속할 수 있는 새로운 클래스(데코레이터)를 만들어 기존 클래스와 같은 인터페이스를 제공하며, 기존 클래스를 데코레이터의 필드로 유지하는 것

  기존 기능이 필요할 때는 필드의 클래스에 접근해서 메소드에게 요청을 전달(forwarding)



```kotlin
class DelegatingCollection<T>: Collection<T>{
    private val innerList = arrayListOf<T>()
    override val size: Int get() = innerList.size
    override fun isEmpty(): Boolean = innerList.isEmpty()
    override fun contains(element: T): Boolean = innerList.contains(element)
    override fun iterator(): Iterator<T> = innerList.iterator()
    override fun containsAll(elements: Collection<T>): Boolean = innerList.containsAll(elements)
}
```

다음과 같은 복잡한 데코레이터를 `by`키워드를 사용하면 컴파일러가 <u>forwarding method를 자동으로 생성</u>해준다.

```kotlin
class DelegatingCollection<T>(
	innerList: Collection<T> = ArrayList<T>()
): Collection<T> by innerList { }
```



`by` 키워드를 사용하여 오버라이드 해야하는 메소드를 위임을 하는 예제이다.

```kotlin
class CountingSet<T>(
	val innerSet: MutableCollection<T> = HashSet<T>()
): MutableCollection<T> by innerSet{		// MutableCollection의 구현을 by 키워드를 이용해 innerSet에게 위임한다.
    var objectsAdded = 0
    
    override fun add(element: T): Boolean{		// HashSet의 add 대신 override해서 사용한다. 
        objectsAdded++
        return innerSet.add(element)
    }
    
    override fun addAll(c: Collection<T>): Boolean{		// HashSet의 addAll 대신 override해서 사용한다.
    	objectsAdded += c.size
        return innerSet.addAll(c)
    }
}
```



### 4.4 object 키워드: 클래스 선언과 인스턴스 생성

`object` 키워드는 사용하면 클래스를 정의하면서 동시에 인스턴스를 생성하며, 다음과 같은 상황에 사용한다.

- **객체 선언(object declaration)**은 싱글턴을 정의하는 방법 중 하나
- **동반 객체(companion object)**는 인스턴스 메소드는 아니지만 어떤 클래스와 관련 있는 메소드와 팩토리 메소드를 담을 때 쓰인다. 동반 객체 메소드에 접근할 때는 동반 객체가 포함된 클래스의 이름을 사용할 수 있다.
- 객체 식은 자바의 **무명 내부 클래스(anonymous inner class)** 대신 쓰인다



#### 4.4.1 객체 선언: 싱글턴을 쉽게 만들기

인스턴스가 하나만 필요한 클래스가 유용한 경우, 자바에서는 생성자를 private로 제한하고 정적인 필드에 그 클래스의 유일한 객체를 저장하는 싱글턴 패턴으로 구현한다.

코틀린은 `object`키워드로 **객체 선언** 기능을 통해 싱글턴을 지원한다.

```kotlin
object Payroll{
    val allEmployees = arrayListOf<Person>()
    fun calculateSalary(){
        for(person in allEmployees){
            ...
        }
    }
}
```

클래스와 마찬가지로 프로퍼티, 메소드, 초기화 블록 등이 들어갈 수 있지만 생성자는 쓸 수 없다.



다음과 같이 `.`을 이용해서 메소드나 프로퍼티에 접근할 수 있다.

```kotlin
Payroll.allEmployees.add(Person(...))
Payroll.calculateSalary()
```



object도 클래스나 인터페이스를 상속하거나, 클래스에 중첩될 수 있다.

```kotlin
data class Person(val name: String){
    object NameComparator: Comparator<Person>{
        override fun compare(p1: Person, p2: Person): Int = 
        	p1.name.compareTo(p2.name)
    }
}
```



#### 4.4.2 동반 객체: 팩토리 메소드와 정적 멤버가 들어갈 장소

##### 팩토리 메소드

코틀린 클래스에는 static 멤버가 없으며 그 대신 패키지 수준의 최상위 함수와 객체 선언을 활용한다.

하지만 최상위 함수는 private 멤버에 접근할 수 없기 때문에, 클래스 내부 정보에 접근해야 하는 함수가 필요할 때는 중첩된 객체 선언의 멤버 함수로 정의해야 한다.

`companion` 키워드를 사용하면 companion object가 정의된 클래스 이름을 사용해 객체의 프로퍼티나 메소드에 접근할 수 있다.

```kotlin
class A{
	companion obejct{
        fun bar(){
            println("Companion object called")
        }
    }
}
```

위의 코드에서 A 클래스를 통해 companion object의 멤버에 접근할 수 있다.

```kotlin
A.bar()
```



##### 정적 멤버의 장소

동반 객체 안에서 팩토리 클래스를 정의할 수 있다. 기존 예제에서 부 생성자를 이용했지만, 

동반 객체 내부에서 private constructor를 호출한다면 다음과 같이 활용할 수 있다.

```kotlin
class User private constructor(val nickname: String){	// 주 생성자를 private로 만든다
	companion object{									// 동반 객체 선언
        fun newSubscribingUser(email: String) = User(email.substringBefore('@'))
        fun newFacebookUser(accountId: Int) = User(getFacebookName(accountId))
    }
}
```



그리고 다음과 같이 호출할 수 있다.

```kotlin
val subscribingUser = User.newSubscribingUser("bob@gmail.com")
```



#### 4.4.3 동반 객체를 일반 객체처럼 사용

동반 객체는 클래스 안에 정의된 일반 객체이다. 따라서 이름을 붙이거나, 인터페이스를 상속하거나, 확장 함수와 프로퍼티를 정의할 수 있다.



- 동반 객체에 이름 붙이기

  ```kotlin
  class Person(val name: String){
      companion object Loader{
          fun fromJSON(jsonText: String): Person = ...
      }
  }
  ```

- 동반 객체에서 인터페이스 구현

  ```kotlin
  interface JSONFactory<T>{
      fun fromJSON(jsonText: String): T
  }
  class Person(val name: String){
      companion obejct: JSONFactory<Person>{
          override fun fromJSON(jsonText: String): Person = ...
      }
  }
  ```

- 동반 객체 확장(extend)

  ```kotlin
  // 비즈니스 로직 모듈
  class Person(val firstName: String, val lastName: String){
      companion object{		// 비어있는 동반 객체 선언
          
      }
  }
  // 클라이언트/서버 통신 모듈
  fun Person.Companion.fromJSON(json: String): Person{
      ...
  }
  ```

  anonymous companion object를 지정하기 위해서는 기본 이름인`Companion`를 사용한다.



#### 4.4.4 객체 식: 무명 내부 클래스를 다른 방식으로 작성

anonymous object를 정의할 때도 `object` 키워드를 사용한다.

```kotlin
window.addMouseListener{
    object: MouseAdapter(){
        override fun mouseClicked(e: MouseEvent){ ... }
        override fun mouseEntered(e: MouseEvent){ ... }
    }
}
```

위의 코드처럼 무명 객체를 이용해서 이벤트 리스너를 구현할 수도 있다. 

> 무명 객체는 싱글턴이 아니며, 객체 식이 쓰일 때마다 새로운 인스턴스가 생성된다.



또한 final이 아닌 변수도 객체 식 안에서 사용할 수 있어서 그 변수의 값을 변경할 수 있다.

```kotlin
fun countClicks(window: Window){
    var clickCount = 0										// 로컬 변수를 정의한다.
    window.addMouseListener(object: MouseAdapter() {
        override fun mouseClicked(e: MouseEvent){
            clickCount++									// 로컬 변수의 값을 변경한다.
        }
    })      
}
```





### 4.5 요약

- 코틀린의 인터페이스는 자바 인터페이스와 비슷하지만 디폴트 구현을 포함할 수 있고, 프로퍼티도 포함할 수 있다.
- 모든 코틀린 선언은 기본적으로 final이며 public이다.
- 선언이 final이 되지 않게 만드려면 앞에 open을 붙여야 한다.
- internal 선언은 같은 모듈 안에서만 볼 수 있다.
- 중첩 클래스는 기본적으로 내부 클래스가 아니다. 바깥 클래스에 대한 참조를 중첩 클래스 안에 포함시키려면 inner 키워드를 중첩 클래스 선언 앞에 붙여서 내부 클래스로 만들어야 한다.
- sealed 클래스를 상속하는 클래스를 정의하려면 반드시 부모 클래스 정의 안에 중첩 클래스로 정의해야 한다.
- 초기화 블록과 부 생성자를활용해 클래스 인스턴스를 더 유연하게 초기화할 수 있다.
- field 식별자를 통해 프로퍼티 접근자(게터와 세터) 안에서 프로퍼티의 데이터를 저장하는 데 쓰이는 뒷받침하는 필드를 참조할 수 있다.
- 데이터 클래스를 사용하면 컴파일러가 equals, hashCode, toString, copy 등의 메소드를 자동으로 생성해준다.
- 클래스 위임을 사용하면 위임 패턴을 구현할 대 필요한 수 많은 성가신 준비 코드를 줄일 수 있다.
- 객체 선언을 사용하면 코틀린답게 싱글턴 클래스를 정의할 수 있다.
- 동반 객체는 자바의 정적 메소드와 필드 정의를 대신한다.
- 동반 객체도 다른 객체와 마찬가지로 인터페이스를 구현할 수 있다. 외부에서 동반 객체에 대한 확장 함수와 프로퍼티를 정의할 수 있다.
- 코틀린의 객체 식은 자바의 무명 내부 클래스를 대신한다. 하지만 코틀린 객체 식은 여러 인스턴스를 구현하거나 객체가 포함된 영역에 있는 변수의 값을 변경할 수 있는 등 자바 무명 내부 클래스보다 더 많은 기능을 제공한다.
