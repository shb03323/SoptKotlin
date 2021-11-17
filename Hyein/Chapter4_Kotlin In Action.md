# Kotlin In Action

## 4. 클래스, 객체, 인터페이스

#### 4.1 클래스의 계층 정의

##### 4.1.1 코틀린 인터페이스

- **인터페이스**
  
  - `interface` 로 정의
    
    - 추상 메소드
    - 구현이 있는 메소드
    
    :warning: 상태(필드)가 들어갈 수 없다 
  
  ``````kotlin
  interface Clickable {
      fun click()
      fun shwoOff() = println("I'm clickable!") // 디폴트 구현이 있는 메소드
  }
  
  class Button : Clickable {
      override fun click() = println("I was clicked")
  }
  ``````
  
  - `Clickable`을 구현하는 모든 비추상 클래스(or 구체적 클래스)는 `click`에 대한 **구현을 제공해야 한다**
  
  :key: 클래스 이름 뒤에 콜론(:)을 붙이고 인터페이스(or 클래스)이름을 적는 것으로 **클래스 확장**과 **인터페이스 구현** 을 처리할 수 있다. :arrow_forward: 클래스는 오직 하나만 확장 가능
  
  - `override`
    - 프로퍼티나 메소드를 오버라이드
    - 실수로 상위 클래스의 메소드를 오버라이드하는 경우를 **방지**
  
  ``````kotlin
  class Button : Clickable, Focusable {
      overrid fun click() = println("I was clicked")
      overrid fun showOff(){
          super<Clickable>.showOff()
          super<Focusable>.showOff()
      }
  }
  ``````
  
  - 이름과 시그니처가 같은 멤버 메소드에 대해 둘 이상의 디폴트 구현이 있는 경우
  
     :arrow_forward:  하위 클래스에서 명시적으로 새로운 구현을 제공
  
    - 상위 타입의 이름을 꺽쇠 괄호 <> 사이에 넣어서 `super`를 지정



##### 4.1.2 open, final, abstrat 변경자 : 기본적으로 final

- **자바**  :arrow_forward:  `final`로 명시하지 않는 모든 클래스 상속 가능
  - 문제 
    - **취약한 기반 클래스**
      - 하위 클래스가 기반 클래스에 대해 가졌던 자정이 기반 클래스를 변경함으로써 깨져버린 경우
      - 하위 클래스의 동작이 예기치 않게 바뀔 수 도 있다는 면에서 기반 클래스는 '취약'
  - 해결
    - 하위 클래스에서 오버라이드하게 의도된 클래스와 메소드가 아니라면 **모두 final**로 만든다

- **코틀린**  :arrow_forward:  클래스와 모든 메소드는 **기본적으로 final**

  - 상속 허용  :arrow_forward:  `open`변경자 사용 (클래스, 메소드, 프로퍼티)

    ``````kotlin
    open class RichButton : Clickable {
        fun disable(){} // final -> 하위에서 오버라이드 불가
        open fun animate(){} // open -> 하위에서 오버라이드 가능
        override fun click(){} // 상위의 열려있는 메소드에 대해 오버라이드
        // final override fun click(){}
    }
    ``````

    - `override`한 메소드는 기본적으로 **열려있다**

       :arrow_forward:  `final`을 앞에 붙여 하위가 오버라이드 못하게 할 수 있다

- 추상 클래스 `abstract`

  - `abstract`로 선언한 추상 클래스는 인스턴스화 할 수 없다

    :key: 추상 멤버는 항상 열려(open)있다 (추상 멤버는 오버라이드 해야하는 것이 보통)

    ``````kotlin
    abstract class Animated{
        abstract fun animate() // 추상 함수, 구현 x, 하위 클래스에서 반드시 오버라이드
    }
    ``````

- 클래스 내에서 **상속 제어 변경자**의 의미

| 변경자     | 이 변경자가 붙은 멤버는..                               | 설명                                                         |
| ---------- | ------------------------------------------------------- | ------------------------------------------------------------ |
| `final`    | 오버라이드할 수 없음                                    | 클래스의 멤버의 기본 변경자다.                               |
| `open`     | 오버라이드할 수 있음                                    | 반드시 `open`을 명시해야 오버라이드할 수 있다                |
| `abstract` | 반드시 오버라이드 해야 함                               | 추상 클래스의 멤버에만 이 변경자를 붙일 수 있다.<br/>추상 멤버에는 구현이 있으면 안된다. |
| `override` | 상위 클래스나 상위 인스턴스의 멤버를 오버라이드 하는 중 | 오버라이드하는 멤버는 기본적으로 열려있다.<br/>하위 클래스의 오버라이드를 금지하려면 `final`을 명시해야 한다. |

:warning: 인터페이스 멤버의 경우 `final`, `open`, `abstract`를 사용하지 않는다. :arrow_forward:  인터페이스 멤버는 항상 열려있다, 자동으로 추상 멤버



##### 4.1.3 가시성 변경자 : 기본적으로 공개

- **가시성 변경자**

  - 클래스 외부 접근을 **제어**
  - 접근 제한 :arrow_forward:  클래스에 의존하는 외부 코드를 깨지 않고 클래스 **내부 구현을 변경**할 수 있다
  - 코틀린의 기본 가시성 : `public`

  | 변경자      | 클래스 멤버                      | 최상위 선언                    |
  | ----------- | -------------------------------- | ------------------------------ |
  | `public`    | 모든 곳에서 볼 수 있다.          | 모든 곳에서 볼 수 있다.        |
  | `internal`  | 같은 모듈 안에서만 볼 수 있다.   | 같은 모듈 안에서만 볼 수 있다. |
  | `protected` | 하위 클래스 안에서만 볼 수 있다. | (최상위 선언에 적용할 수 없음) |
  | `private`   | 같은 클래스 안에서만 볼 수 있다. | 같은 파일 안에서만 볼 수 있다. |

  - 규칙
    - 타입 파마리터에 들어있는 타입의 가시성 >= 클래스 자신의 가시성
    - 메소드의 시그니처에 사용된 모든 타입의 가시성 >= 그 메소드의 가시성

- 자바와의 **차이**

  - 패키지를 가시성 제어에 사용하지 않는다.

    - 패키지 전용 가시성 대안 :arrow_forward:  `internal`

      - 모듈 내부에서만 볼 수 있다.

        :key: **모듈** : 한 번에 한꺼번에 컴파일 되는 코틀린 파일

    - **모듈 내부 가시성** :arrow_forward:  진정한 캡슐화 제공

  - 최상위 선언에 대해 `private` (비공개 가시성)을 허용

    - 최상위 선언 :arrow_forward:  클래스, 함수, 프로퍼티 등 포함
    - private인 최상위 선언은 그 파일 내부에서만 사용 가능

  - `protected` : 오직 어떤 클래스나 그 클래스를 상속한 클래스 안에서만 보인다
  - 외부 클래스가 내부 클래스 or 중첩된 클래스의 private 멤버에 접근할 수 **없다**



##### 4.1.4 내부 클래스와 중첩된 클래스: 기본적으로 중첩 클래스

- **중첩 클래스**

  - 명시적으로 요청하지 않는 한 바깥쪽 클래스 인스턴스에 대한 **접근 권한이 없다**

- **내부 클래스**

  - 자바에서 다른 클래스 안에 정의한 클래스는 자동으로 내부 클래스가 된다

- 자바와 코틀린의 중첩 클래스와 내부 클래스의 관계

  | 클래스 B 안에 정의된 클래스 A                          | 자바에서는     | 코틀린에서는  |
  | ------------------------------------------------------ | -------------- | ------------- |
  | 중첩 클래스(바깥쪽 클래스에 대한 참조를 저장하지 않음) | static class A | class A       |
  | 내부 클래스(바깥쪽 클래스에 대한 참조를 저장함)        | class A        | inner class A |

  - 내부 클래스에서 바깥쪽 클래스의 참조에 접근하려면 `this@바깥쪽클래스이름` 를 사용

  

##### 4.1.5 봉인된 클래스: 클래스 계청 정의 시 계층 확장 제한

- **클래스 계층**

  ``````kotlin
  interface Expr
  class Num(val value: Int) : Expr
  class Sum(val left: Expr, val right: Expr) : Expr
  
  fun eval(e: Expr) : Int =
  	when(e){
          is Num -> e.value
          is Sum -> eval(e.right) + eval(e.left)
          else -> // 꼭 else 분기 필요
          	throw IllegalArgumentException("Unknown expression")
      }
  ``````

  - 항상 default가 있어야 하는 문제(버그 발생 가능 :o:)

    :arrow_forward: **sealed** 클래스로 해결

- **sealed** 클래스

  - 상위 클래스(sealed)를 상속한 하위 클래스의 정의를 **제한** 가능
  - 하위 클래스를 정의할 때는 반드시 상위 클래스 안에 **중첩**시켜야 한다.

  ``````kotlin
  sealed class Expr{
      class Num(val value: Int) : Expr()
      class Sum(val left:Expr, val right:Expr) : Expr()
  }
  
  fun eval(e : Expr) : Int =
  	when (e) { // when이 모든 하위 클래스를 검사
          is Expr.Num -> e.eval
          is Expr.Sum -> eval(e.right) + eval(e.right)
      }
  ``````

  - 디폴트 분기(else) 가 필요없다.

  - `sealed` 클래스는 자동으로 `open`

    

***



#### 4.2 뻔ㄴ하지 않은 생성자와 프로퍼티를 갖는 클래스 선언

- 코틀린
  - **주 생성자** : 클래스 초기화 시 사용, 클래스 본문 밖
  - **부 생성자** : 클래스 본문 안 정의
  - **초기화 블록** : 초기화 로직 추가



##### 4.2.1 클래스 초기화: 주 생성자와 초기화 블록

- **주 생성자**

  - 클래스 이름 뒤에 오는 괄호로 둘러싸인 코드

    ``````kotlin
    class User(val nickname: String)
    
    //명시적 선언
    class User constructor(_nickname : String){
        val nickname: String
        init{
            nickname = _nickname
        }
    }
    ``````

    - 생성자 파라미터 지정
    - 생성자 파라미터에 의해 초기화 되는 프로퍼티를 정의

  - `constructor` : 주 생성자나 부 생성자 정의 시작 시 사용 :arrow_forward: 생략 가능 (애노테이션, 가시성 변경자 없다면)​

  - `init` : 초기화 블록 시작

  - 주 생성자 제한적 :arrow_forward: 초기화 블록이 필요

    :warning: 초기화 블록 안에서만 **주 생성자의 파라미터를 참조**할 수 있다.

    ``````kotlin
    calss User(val nickname: String, val isSubscribed: Boolean = true)
    ``````

  - 기반 클래스 존재 :arrow_forward: 주 생성자에서 기반 클래스의 생성자를 호출​

    ``````kotlin
    open class User(val nickname: String) {...}
    class TwitterUser(nickname: String) : User(nickname) {...}
    ``````

    :key:클래스 정의 시 별도의 생성자 정의 :x: :arrow_forward: 컴파일러가 자동으로 인자 없는 디폴트 생성자 만듦

    - **하위 클래스는 상위 클래스의 생성자를 호출해야 한다**

      :arrow_forward: 기반 클래스의 이름 뒤에는 꼭 빈괄호가 들어간다(혹은 인자포함)

    

##### 4.2.2 부 생성자: 상위 클래스를 다른 방식으로 초기화

- **오버로드한 생성자가 필요한 상황**에 대한 해결

  :arrow_forward: 한 생성자​

  - 디폴트 파라미타 값
  - 이름 붙인 인자 문법

-  생성자가 여러 필요한 경우 :arrow_forward: **부 생성자** 사용​

  - `constructor` 키워드 이용

  ``````kotlin
  open class View{
      constructor(ctx: Context)
      	: super(ctx){
          
      }
      
      constructor(ctx: Context, attr: AttributeSet)
      	:super(ctx, attr){
          
      }
  }
  ``````

  - `super`키워드로 상위 클래스 생성자를 호출

    :arrow_forward: ​상위 클래스 생성자에게 **객체 생성 위임**

- **주 생성자 없는 부 생성자**

  - 반드시 상위 클래스를 초기화

  - 다른 생성자에게 생성을 위임

    :arrow_forward: 객체 생성을 위임하는 화살표를 따라가면 그 **끝**에는 상위 클래스 생성자를 호출하는 화살표가 있어야 한다.

- 부 생성자의 **필요 이유**

  - 자바 상호운용성
  - 파라미터 목록이 다른 생성 방법이 여럿 존재하는 경우



##### 4.2.3 인터페이스에 선언된 프로퍼티 구현

- **인터페이스**
  - **추상 프로퍼티 선언** 가능
    - 하위 클래스에서 상태 저장을 위한 프로퍼티 등을 만들어야 한다.
  - **게터와 세터가 있는 프로퍼티 선언** 가능
    - 게터와 세터는 **뒷받침하는 필드를 참조할 수 없다**



##### 4.2.4 게터와 세터에서 뒷받침하는 필드에 접근

- 세터에서 뒷받침하는 필드 접근하기

  ```````kotlin
  class User(val name: String){
      var address: String = "unspecified"
      	set(value : String){
              println("""
              	Address was changed for $name:
              	"$field" -> "$value".""".trimIndent()) //뒷받침하는 필드값 읽기
              field = value
          }
  }
  ```````

  - 프로퍼티 값 바꾸기
    - **필드 설정 구문 사용** :arrow_forward: 내부적으로 address의 **세터 호출**
    - `field` : 뒷받침하는 필드에 접근할 수 있다
      - **게터** : field 값을 읽을 수만 있다.
      - **세터** : field 값을 읽거나 쓸 수 있다.



##### 4.2.5 접근자의 가시성 변경

- **접근자의 가시성**

  - 기본적으로 **프로퍼티의 가시성**과 같다

  - get이나 set 앞에 가시성 변경자를 추가하여 접근자의 가시성을 변경 할 수 있다.

  

***



#### 4.3 컴파일러가 생성한 메소드: 데이터 클래스 위임

- **코틀린 컴파일러**

  - 메소드를 기계적으로 생성하는 작업을 보이지 않는 곳에서 해준다

    :arrow_forward: 필수 메소드로 인한 잡음 없이 소스코드를 깔끔하게 유지할 수 있다.

    ​	ex) 클래스 생성자 or 프로퍼티 접근자 -> 컴파일러가 자동으로 생성

    

##### 4.3.1 모든 클래스가 정의해야 하는 메소드

- 메소드

  - `toString`, `equals`, `hashCode` 오버라이드(자바)

    :arrow_forward: 자동 생성 가능​(코틀린에서)

- **문자열 표현: toString()**

  - 인스턴스의 문자열 표현을 얻을 방법을 제공
    - 기본 구현을 바꾸려면 `toString` 메소드를 오버라이드 가능

- **객체의 동등성: equals()**

  - `==` : 객체의 동등성 검사(주소 검사x)

    :arrow_forward: `equals`를 호출하는 식으로 컴파일, `equals`를 오버라이드 해야한다.

  - `===` : 참조 비교
  - `is` : 어떤 값의 타입 검사
  - 작업 수행 시 제대로 작동하지 않는 경우 발생 :arrow_forward: 대부분 `hashCode`정의를 빠트린 경우

- **해시 컨테이너: hashCode()**

  - JVM 언어에서 `hashCode`가 지켜야 하는 제약

    - `equalse()` 가 true를 반환하는 두 객체는 반드시 같은 `hashCode()`를 반환해야 한다.

  - **HashSet**

    - 원소를 비교할 때 비용을 줄이기 위해 **객체의 해시 코드 비교** 후 해시 코드가 같은 경우에만 **실제 값 비교**를 진행

      :arrow_forward: `hashCode` 오버라이드​

:key: 코틀린 컴파일러는 이 모든 메소드를 자동으로 생성해줄 수 있다.



##### 4.3.2 데이터 클래스: 모든 클래스가 정의해야 하는 메소드 자동 생성

- **데이터 저장 역할 클래스**

  - `toString`, `equals`, `hashCode` 오버라이드

    :arrow_forward: **data class** 사용으로 컴파일러가 자동 생성

- **data class**

  ``````kotlin
  data class Client(val name: String, val postalCode: Int)
  ``````

  - `equals`와 `hashCode`는 **주 생성자에 나열된 모든 프로퍼티**를 고려해 만들어진다.

- **데이터 클래스와 불변성: copy() 메소드**

  - 데이터 클래스의 모든 프로퍼티 **읽기 전용**(불변 클래스)로 만들기를 권장

    - 데이터 클래스의 인스턴스를 불변 객체로 더 쉽게 활용

      :arrow_forward: **copy 메소드**

      - 객체를 복사하면서 일부 프로퍼티를 바꿀 수 있게 해준다.



##### 4.3.3 클래스 위임: by 키워드 사용

- **데코레이터 패턴**

  - 상속을 허용하지 않는 클래스에 **새로운 동작 추가** 시 사용할 수 있는 방법
    - 사용할 수 있는 새로운 클래스(데코레이터)를 만든다.
    - 같은 인터페이스를 데코레이터가 제공
    - 기존 클래스를 데코레이터 내부 필드로 유지
    - 새로 정의해야 하는 기능을 데코레이터의 메소드로 정의
    - 기존 기능이 그대로 필요한 부분은 데코레이터의 메소드가 기존 클래스 메소드에 요청을 **전달(forwarding)**

  - 단점 : 준비 코드가 많이 필요하다.

    :arrow_forward: 해결: **by**

- **by**

  - 인터페이스에 대한 구현을 다른 객체에 위임 중이라는 사실을 명시 가능

    ``````kotlin
    class DelegatingCollection<T>(
    	innerList: Collection<T> = ArrayList<T>()
    ) : Collection<T> by innerList{}
    ``````



***



#### 4.4 object 키워드: 클래스 선언과 인스턴스 생성

- **object**
  - 모든 경우 클래스를 정의하면서 동시에 인스턴스를 생성
  - object를 사용하는 **상황**
    - **객체 선언** : 싱글턴을 정의하는 방법 중 하나
    - **동반 객체** : 인스턴스 메소드 :x:, 어떤 클래스와 관련있는 메소드와 팩토리 메소드를 담을 때 사용
    - **객체 식** : 자바의 무명 내부 클래스 대신 사용



##### 4.4.1 객체 선언: 싱글턴을 쉽게 만들기

- **싱글턴 패턴**
  - 인스턴스가 하나만 필요한 경우, 이를 통해 구현
- **객체 선언**
  - 객체 선언을 통해 싱글턴을 언어에서 기본 지원
  - 클래스 선언 + 단일 인스턴스 선언
  - `object` 키워드로 시작
    - 클래스 정의 + 클래스의 인스턴스 만들어서 변수에 저장 => 한 문장으로 처리
  - **생성자 사용 불가능**



##### 4.4.2 동반 객체: 팩토리 메소드와 정적 멤버가 들어갈 장소

- 정적 멤버 :x:

  :arrow_forward: **최상위 함수** + **객체 선언**​

  - 클래스 내부 정보에 접근해야 하는 함수가 필요할 때

    :arrow_forward: **팩토리 메소드**

- **companion 객체**

  - 클래스의 동반객체
  - 동반 객체의 프로퍼티나 메소드에 접근하려면 그 동반객체 정의된 **클래스 이름**을 사용
  - 객체의 이름을 따로 지정할 필요 :x:

  

##### 4.4.3 동반 객체를 일반 객체처럼 사용

- **동반 객체**
  - 클래스 안에 정의 된 **일반 객체**
    - 이름 붙이기
    - 인터페이스 상속
    - 확장함수와 프로퍼티 정의
- **동반 객체에서 인터페이스 구현**
  - 동반 객체도 인터페이스 구현 가능
  - 동반 객체에 이름을 붙이지 않았다면 자바 쪽에서 **Companion**이라는 이름으로 그 참조에 접근할 수 있다.

- **동반 객체 확장**
  - 동반 객체 안에 **함수를 정의**함으로써 클래스에 대해 호출할 수 있는 **확장 함수**를 만들 수 있다.
  - 동반 객체에 대한 확장 함수를 작성할 수 있으려면 원래 클래스에 **동반 객체를 선언**해야 한다.



##### 4.4.4 객체 식: 무명 내부 클래스를 다른 방식으로 작성

- **무명 객체**
  - 무명 객체 정의 시 `object`키워드를 사용
  - 객체 이름이 빠진다.
    - **객체 식**
      - 클래스를 정의, 인스턴스를 생성, but 이름을 붙이지는 않는다.
  - **싱글턴** :heavy_multiplication_x: