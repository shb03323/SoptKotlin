## 3. 함수 정의와 호출



### 3.1 코틀린에서 컬렉션 만들기

코틀린은 자신만의 컬렉션 기능을 제공하지 않고, 자바 컬렉션을 활용한다. 



```kotlin
val set = hashSetOf(1, 7, 53)		// 순서가 없는 해시 테이블, 내부적으로 키와 인덱스를 이용해 검색과 변경 등 빠른 처리 (mutable)
val list = arrayListOf(1, 7, 53)	// 동직인 크기를 가지는 리스트 (mutable)
val map = hashMapOF(1 to "one", 7 to "seven", 53 to "fifty-three")	// key-value 형태의 hash map
val strings = listOf("first", "second", "third")	//	immutable 리스트 (변경하고 싶다면 MutableList 사용)
val numbers = setOf(1, 14, 2)	// 순서가 없는 요소들의 집합 (immutable)
```



### 3.2 함수를 호출하기 쉽게 만들기

자바 컬렉션의 출력은 디폴트 toString이 구현되어있기 때문에, 컬렉션을 String으로 반환하는 커스텀 함수를 구현해본다.

generic으로 구현되어 있기 때문에 타입에 상관없이 지원한다.

```kotlin
fun <T> joinToString(
	collection: Collection<T>,
    separator: String,
    prefix: String,
    postfix: String
): String{
    val result = StringBuilder(prefix)
    for((index, element) in collection.withIndex()){
        if (index > 0) result.append(separator)
        result.append(element)
    }
    result.append(postfix)
    return result.toString()
}
```

이 함수를 개선할 수 있는 방법을 알아본다.



#### 3.2.1 이름 붙인 인자

코틀린에서는 작성한 함수를 호출 할 때 함수에 전달하는 인자 중 일부의 이름을 명시하여 인자의 역할을 구분할 수 있다.

기존 자바에서는 주석으로 표시할 수 있지만, 코틀린에서는 다음과 같이 호출할 수 있다.

```kotlin
joinToString(collection, separator=" ", prefix="<", postfix=">")
```



> 혼동을 막기 위해서 이름을 명시한 인자 뒤에 오는 모든 인자는 이름을 명시하도록 한다.
>
> 자바로 작성한 코드를 호출할 때는 이 방법을 사용할 수 없다.



#### 3.2.2 디폴트 파라미터 값

함수 선언부에서 디폴트 파라미터 값을 지정하면 호출 시에 생략된 파라미터를 자동으로 적용한다.

오버로딩 메소드가 많은 클래스에서 중복을 줄일 수 있다.

```kotlin
fun <T> joinToString{
	collection: Collection<T>,
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
}: String
```



#### 3.2.3 정적인 유틸리티 클래스 없애기: 최상위 함수와 프로퍼티

특별한 상태나 인스턴스 메소드가 없이 정적 메소드만을 모아두는 클래스(ex. Java의 Util)을 없애기 위해서 함수나 프로퍼티를 최상위 수준에 위치시킬 수 있다. (특정 클래스에 종속되지 않는다)



다음과 같이 joinToString 함수를 strings 패키지에 넣을 수 있다.

```kotlin
package strings
fun joinToString( ... ): String { ... }
```



마찬가지로 프로퍼티도 파일의 최상위 수준에 놓을 수 있다.

```kotlin
var opCount = 0
fun performOperation(){
	opCount++
}
```

또한 이를 활용해 코드에 상수를 추가할 수 있다.

```kotlin
val UNIX_LINE_SEPARATOR = "\n"
```



### 3.3 메소드를 다른 클래스에 추가: 확장 함수와 확장 프로퍼티

##### 확장 함수 (extension function)

​	어떤 클래스의 멤버 메소드인 것처럼 호출할 수 있지만 그 클래스의 밖에 선언된 함수

​	함수 이름 앞에 그 함수가 확장할 클래스의 이름을 붙여서 만들 수 있다.

```kotlin
package strings
fun String.lastChar(): Char = this.get(this.length - 1)

println("Kotlin".lastChar())	// 클래스 멤버를 호출하는 구문과 똑같이 확장 함수를 호출할 수 있다.
								// 수신 객체 타입 = String, 수신 객체 = "Kotlin"
```

​	수신 객체 타입 (receiver type) : 확장할 클래스 이름

​	수신 객체 (receiver object) : 확장 함수가 호출되는 대상이 되는 값(객체)



> 확장 함수에서 this 키워드와 프로퍼티를 사용할 수 있다.
>
> 하지만, private / protected로 보호된 멤버에 접근할 수 없다. (캡슐화를 깨지 않는다)



#### 3.3.1 임포트와 확장 함수

확장 함수를 사용하기 위해서는 그 함수를 다른 클래스나 함수와 마찬가지로 임포트해야만 한다.

```kotlin
import strings.lastChar

import strings.*

import strings.lastChar as last		// as 키워드를 사용하면 임포트한 클래스나 함수를 다른 이름으로 부를 수 있다.
```



> 코틀린 문법상 확장 함수는 반드시 짧은 이름을 써야하며, 따라서 이름 충돌을 피하기 위해 as를 이용하는 것이 좋다.



#### 3.3.2 자바에서 확장 함수 호출

내부적으로 확장 함수는 수신 객체를 첫 번째 인자로 받는 정적 메소드이기에 호출에 어댑터 객체나 부가 비용이 들지 않는다.

만약 StringUtil.kt 파일에 확장 함수를 정의했다면 자바에서 다음과 같이 호출할 수 있다.

```java
char c = StringUtil.lastChar("Java");
```



#### 3.3.3 확장 함수로 유틸리티 함수 정의

joinToString 함수를 확장 함수를 이용해 수정한다.

```kotlin
fun <T> Collection<T>.joinToString(
	separator: String = " "
	prefix: String = ""
	postfix: String = ""
): String{
	val result = StringBuilder(prefix)
	
	for((index, element) in this.withIndex())
		if(index > 0) result.append(separator)
		result.append(element)
}
```



확장 함수로 만들었기 때문에 collection의 클래스 멤버인 것처럼 바로 호출할 수 있다.

```kotlin
val list = arrayListOf(1, 2, 3)
println(list.joinToString(" "))
```



> 확장 함수는 단지 정적 메소드 호출에 대한 문법적인 편의(syntatic sugar)일 뿐이기 때문에 더 구체적인 타입을 수신 객체 타입으로 지정할 수 있다.
>
> ```kotlin
> fun Collection<String>.join(
> 	separator: String = ", ",
> 	prefix: String = "",
> 	postfix: String = ""
> ) = joinToString(separator, prefix, postfix)
> ```



#### 3.3.4 확장 함수는 오버라이드할 수 없다

확장 함수는 클래스 밖에서 선언되며, 클래스의 일부가 아니다.

따라서 이름과 파라미터가 동일한 확장 함수를 상속 관계의 클래스에 대해 정의해도 실행될 메소드를 동적으로 결정하지 않는다.

```kotlin
/* 멤버 함수 오버라이딩 */
open class View{		// 상속될 수 있도록 open class로 선언하였다.
    open fun click() = println("View clicked")
}
class Button: View(){
    override fun click() = println("Button clicked")
}

val view: View = Button()		// 상위 클래스인 View로 타입을 지정하고, 하위 클래스인 Button의 생성자를 호출
view.click()					// Button clicked가 출력된다. 
```



```kotlin
/* 오버라이딩되지 않는 확장 함수 */
fun View.showOff() = println("I'm a view!")
fun Button.showOff() = println("I'm a button!")
val view: View = Button()
view.showOff()				// I'm a view가 출력된다.
```



> 확장 함수는 첫 번째 인자가 수신 객체인 정적 자바 메소드로 컴파일되기 때문에 오버라이딩되지 않는다.
>
> ```java
> View view = new Button();
> ExtensionsKt.showOff(view);		// 정적으로 View.showOff가 결정된다.
> ```



> 우선순위 : 멤버 함수 > 확장 함수



#### 3.3.5 확장 프로퍼티

프로퍼티라는 이름으로 불리기는 하지만, 기존 클래스의 인스턴스에 필드를 추가할 수는 없으므로 확장 프로퍼티가 어떠한 상태를 가질 수는 없다.

필드가 없기 때문에 <u>게터를 반드시 정의</u>해야한다.

```kotlin
val String.lastChar: Char
	get() = get(length - 1)
```



StringBuilder의 경우 맨 마지막 문자는 변경 가능하므로 프로퍼티를 var로 만들 수 있다.

```kotlin
var StringBuilder.lastChar: Char
	get() = get(length - 1)
	set(value: Char){
		this.setCharAt(length - 1, value)
}
```





### 3.4 컬렉션 처리: 가변 길이 인자, 중위 함수 호출, 라이브러리 지원

컬렉션을 처리할 때 쓸 수 있는 코틀린 표준 라이브러리 함수

- vararg 키워드를 사용하면 호출 시 인자 개수가 달라질 수 있는 함수를 정의할 수 있다
- 중위 함수 호출 구문을 사용하면 인자가 하나뿐인 메소드를 간편하게 호출할 수 있다.
- 구조 분해 선언(destructuring declaration)을 사용하면 복합적인 값을 분해해서 여러 변수에 나눠 담을 수 있다.



#### 3.4.1 자바 컬렉션 API 확장

코틀린 컬렉션은 자바와 같은 클래스를 이용하지만, 확장 함수를 통해 API를 제공한다. 

(코틀린의 표준 라이브러리는 많은 확장 함수를 포함한다)



#### 3.4.2 가변 인자 함수: 인자의 개수가 달라질 수 있는 함수 정의

##### vararg 변경자

자바에서는 타입...변수명 으로 가변 인자를 사용했다면,

코틀린에서는 파라미터 앞에 변경자를 붙여서 가변 인자를 사용할 수 있다.

```kotlin
fun listOf<T>(vararg values: T): List<T> { ... }
val list = listOf(2, 3, 4, 5, 32)
```



##### spread 연산자

이미 배열에 들어있는 원소를 가변 길이 인자로 넘길 때에 배열 앞에 `*`를 붙여서 spread 연산자를 사용할 수 있다.

```kotlin
val arr1 = listOf(1, 2, 3, 4, 5)
val arr2 = listOf(*arr1, 6, 7, 8, 9)
```



다음 예제처럼도 사용할 수 있다.

```kotlin
fun main(args: Array<String>){
	val list = listOf("args: ", *args)
	println(list)
}
```



#### 3.4.3 값의 쌍 다루기: 중위 호출과 구조 분해 선언

##### 중위 호출 (infix call)

수신 객체와 유일한 메소드 인자 사이에 메소드 이름을 넣어서 호출한다.

이 때 사이에는 공백이 들어가야 한다. 다음의 두 호출은 동일하다.

```kotlin
1.to("one")
1 to "one"
```



##### infix 변경자

함수 선언 앞에 infix 변경자를 추가하여 함수를 중위 호출할 수 있도록 선언할 수 있다.

```kotlin
infix fun Any.to(otehr: Any) = Pair(this, other)	// infinx 변경자를 통해 중위 호출 가능하도록 선언

val map = mapOf(1 to "one", 7 to "seven", 53 to "fifty-three")		// 중위 호출을 이용해 매개변수와 수신 객체를 Pair로 만들어줌
```



##### 구조 분해 선언 (destructing declaration)

객체가 가지고 있는 여러 값을 분해해서 여러 변수에 한꺼번에 초기화할 수 있다.

```kotlin
val (number, name) = 1 to "one"
```



루프에서도 다음과 같이 구조 분해 선언을 활용할 수 있다.

```kotlin
for((index, element) in collection.withIndex()){
	println("$index: $element")
}
```



### 3.5 문자열과 정규식 다루기

코틀린 문자열과 자바 문자열은 같으며, 특별한 변환이나 wrapper도 필요하지 않다.

코틀린에서는 혼동이 생길 수 있는 일부 메소드에 대해 더 명확한 코틀린 확장 함수를 제공한다.



#### 3.5.1 문자열 나누기

자바의 String.split 메소드에 사용되는 구분 문자열은 정규식이기 때문에  "."을 사용하면 의도했던 것과 다르게 와일드 카드로 해석된다.

코틀린에서는 이런 혼동을 방지하기 위해 정규식은 String이 아닌 Regex 값을 받도록 확장 함수를 지원한다.

```kotlin
println("12.345-6.A".split("\\.|-".toRegex()))	// 정규식을 명시적으로 생성하여 이용한다. 
												// .의 경우 이스케이프를 통해 와일드카드가 아닌 리터럴로 인식하도록 하였다.
```



또한 구분 문자열 여러개를 인자로 받는 확장 함수가 존재하기 때문에 다음과 같이 사용할 수 있다.

```kotlin
println("12.345-6.A".split(".", "-"))
```



#### 3.5.2 정규식과 3중 따옴표로 묶은 문자열

3중 따옴표 문자열 내부에서는 문자를 이스케이프할 필요가 없다.

따라서 일반 문자열을 사용해 정규식을 작성할 때 `\\.`라고 쓰지 않고 `\.`로 써서 마침표 기호를 이스케이프할 수 있다.

다음은 정규식을 이용해 경로를 파싱하는 예이다.

```kotlin
fun parsePath(path: String){
	val reges = """(.+)/(.+)\.(.+)""".toRegex()
	val matchResult = regex.matchEntire(path)
	if(matchResult != null){
		val (directory, filename, extension) = matchResult.destructured
		println("Dir: $directory, name: $filename, extension: $extension")
	}
}
```



#### 3.5.3 여러 줄 3중 따옴표 문자열

3중 따옴표를 사용하면 줄바꿈을 표현하는 문자열도 그대로 들어간다.

```kotlin
val kotlinLogo = """|  //
                    .| //
                    .|/ \"""
println(kotlinLogo.trimMargin("."))		// trimMargin은 그 문자열과 직전의 공백을 제거한다.
```



>  이스케이프 문자를 사용할 수 없기 때문에 $는 문자열 템플릿으로 적용된다. 따라서 `$` 문자를 사용하고 싶다면 `${'$'}`를 사용해야 한다.



### 3.6 코드 다듬기: 로컬 함수와 확장

코틀린에서는 함수에서 추출한 함수를 원 함수 내부에 중첩시켜서 문법적인 부가 비용 없이 코드를 조직할 수 있다.



- 코드 중복의 예제

  ```kotlin
  class User(val id: Int, val name: String, val address: String)
  fun saveUser(user: User){
  	if(user.name.isEmpty()){
  		throw IllegalArgumentException(
  			"Can't save user ${user.id}: empty Name")
  	}
  	if(user.address.isEmpty()){
  		throw IllegalArugmentException(
  			"Can't save user ${user.id}: empty Address")
  	}
  	// user를 데이터베이스에 저장한다.
  }
  ```

  

- 로컬 함수를 사용해 코드 중복 줄이기

  ```kotlin
  class User(val id:Int, val name: String, val address: String)
  fun saveUser(user: User){
  	fun validate(user: User,
  				value: String,
                  fieldName: String){
      	if(value.isEmpty()){
      		throw IllegalArgumentException(
      			"Can't save user ${user.id}: empty $fieldName")
      	}            
  	}
  	validate(user, user.name, "Name")
  	validate(user, user.address, "Address")
  	// user를 데이터베이스에 저장한다.
  }
  ```

  

- 로컬 함수에서 바깥 함수의 파라미터 접근하기

  ```kotlin
  class User(val id: Int, val name: String, val address: String)
  fun saveUser(user: User){
      fun validate(value: String, filedName: String){		// 파라미터에서 user를 중복하지 않는다
          if(value.isEmpty()){
              throw IllegalArgumentException(
              	"Can't save user ${user.id}: " +		// 바깥 함수의 파라미터에 직접 접근할 수 있다. (user)
              		"empty $fieldName")
          }
      }
      validate(user.name, "Name")
      validate(user.address, "Address")
      // user를 데이터베이스에 저장한다.
  }
  ```

  

- 검증 로직을 확장 함수로 추출하기

  ```kotlin
  class User(val id: Int, val name: String, val address: String)
  fun User.validateBeforeSave(){
      fun validate(value: String, filedName: String){
          if(value.isEmpty()){
              throw IllegalArgumentException(
              	"Can't save user $id: empty $fieldName")		// User의 프로퍼티를 직접 사용 가능
          }
      }
      validate(name, "Name")
      validate(address, "Address")
  }
  
  fun saveUser(user: User){
      user.validateBeforeSave()		// 확장 함수 호출
      // user를 데이터베이스에 저장한다.
  }
  ```

  

> User은 라이브러리에 있는 클래스는 아니지만, 검증 로직은 User를 사용하는 다른 곳에서는 쓰이지 않기 때문에 User를 간결하게 유지하기 위해 확장 함수를 사용할 수 있다.



### 3.7 요약

- 코틀린은 자체 컬렉션 클래스를 정의하지 않지만 자바 클래스를 확장해서 더 풍부한 API를 제공한다.
- 함수 파라미터의 디폴트 값을 정의하면 오버로딩한 함수를 정의할 필요성이 줄어든다. 이름 붙인 인자를 사용하면 함수의 인자가 많을 때 함수 호출의 가독성을 더 향상시킬 수 있다.
- 코틀린 파일에서 클래스 멤버가 아닌 최상위 함수와 프로퍼티를 직접 선언할 수 있다.
- 확장 함수와 프로퍼티를 사용하면 외부 라이브러리에 정의도니 클래스를 포함해 모든 클래스의 API를 그 클래스의 소스코드를 바꿀 필요 없이 확장할 수 있다. 확장 함수를 사용해도 실행 시점에 부가 비용이 들지 않는다.
- 중위 호출을 통해 인자가 하나 밖에 없는 메소드나 확장 함수를 더 깔끔한 구문으로 호출할 수 있다.
- 코틀린은 정규식과 일반 문자열을 처리할 때 유용한 다양한 문자열 처리 함수를 제공한다.
- 자바 문자열로 표현하려면 수많은 이스케이프가 필요한 문자열의 경우 3중 따옴표 문자열을 사용하면 더 깔끔하게 표현할 수 있다.
- 로컬 함수를 써서 코드를 더 깔끔하게 유지하면서 중복을 제거할 수 있다.

