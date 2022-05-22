# Item8. 적절하게 null을 처리하라

null은 최대한 명확한 의미를 갖는 것이 좋다 : nullable 값을 처리해야하기 때문

nullable 타입을 처리하는 방법

- `?.` , 스마트캐스팅, Elvis 연산자 등을 활용하여 안전히 처리한다
- 오류를 throw 한다
- 함수 또는 프로퍼티를 리팩터링해서 nullable 타입이 나오지 않게 한다.



### 1. null 안전 처리

안전 호출 (safe call)

```kotlin
printer?.print()
```

스마트캐스팅 (smart casting) : 코틀린의 규약기능(contracts feature)을 지원

```kotlin
if (printer != null) printer.print()
```

Elvis 연산자

```kotlin
val printerName1 = printer?.name ?: "Unnamed"
val printerName2 = printer?.name ?: null
val printerName3 = printer?.name ?: throw Error("Printer must be named")
```

컬렉션처리를 할 때 무언가가 없다는 것을 나타낼 때는 null이 아닌 빈 컬렉션 사용



### 2. 오류 throw

`throw`, `!!`, `requireNotNull` , `checkNotNull` 등을 활용하여 오류를 강제로 발생 시킬 수 있다.

#### a. not-null assertion(!!)

좋은 방법은 아니다.

- 자바에서 nullable을 처리할 때의 문제와 똑같이 발생한다 : NPE 예외
- 제네릭 예외(generic exception) : 예외가 발생할 때 어떤 설명도 없음
- 사용이 쉬워 남용한다

!! 연산자가 의미있는 경우 : 일반적으로 nullability가 제대로 표현되지 않는 라이브러리를 사용할 때 정도에만 사용해야한다.

일반적으로 !! 연산자를 피해야한다.



#### b. 의미없는 nullability 피하기

- 클래스에서 nullability에 따라 여러 함수를 만들어서 제공한다 : `get` ,`getOrNull`
- 어떤 값이 클래스 생성 이후에 확실하게 설정된다는 보장이 있으면 `lateinit` 프로퍼티와 `notNull` 델리게이트 사용하기
- null 리턴 대신 빈 컬렉션 사용하기 (`Collections.empty()`)
- nullabe enum과 None enum은 다른의미



#### d. lateinit 프로퍼티, notNull 델리게이트

lateinit 한정자 : 나중에 속성을 초기화 할 수 있다. 프로퍼티가 이후에 설정될 것임을 명시한다

```kotlin
class UserControllerTest{
  private lateinit var dao: Userdao
  
  @BeforeEach
  fun init(){
    dao = mockk()
  }
}
```

초기화 전에 값을 사용하려고 하면 예외가 발생한다. 

`lateinit` vs nullable

- !! 연산자로 언팩(unpack) 하지 않아도 된다.
- null 사용하고 싶을때 nullable로 만들 수 있다.
- 프로퍼티가 초기화된 이후에 초기화되지 않은 상태로 돌아갈 수 없다.

`lateinit` 을 사용할 수 없는 경우

- JVM에서 Int, Long, Double, Boolean 과 같은 기본 타입과 연결된 타입으로 프로퍼티를 초기화해야하는 경우 > Deleagets.noNull 사용하기.

```kotlin
class DoctorActivity: Activity(){
  private var doctorId: Int by Delegates.notNull()
}
```



### 참고

Deletegateion pattern : 어떤 기능을 자신이 처리하지 않고 다른 객체에 위임시켜 그 객체가 일을 처리하도록 하는 것

Composition을 사용하는 일반적인 패턴

- Composition 객체의 함수가 많아지면 boilerplate code가 많아지게 된다. 내부에 있는 코드의 메서드를 바깥 클래스가 위임받는다.

코틀린의 by 키워드 : delegate pattern을 쉽게 구현할 수 있으며, 이 과정에서 boilerplate code를 생략할 수 있다.

```kotlin
interface Base {
    fun print()
}

class BaseImpl(val x: Int) : Base {
    override fun print() { print(x) }
}

class Derived(b: Base) : Base by b

fun main() {
    val b = BaseImpl(10)
    Derived(b).print()
}
```



