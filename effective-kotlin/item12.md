# item12: 연산자 오버로드를 할 때는 의미에 맞게 사용하라

연산자 오버로딩 : 강력하지만 주의해야한다.

연산자 오버로딩은 관례에 어긋나지 않게 해야한다 : 혼란스럽고 오해의 소지가 있기 때문 

> ex : 팩토리얼 표시를 위해 !(not) 연산자를 팩토리얼 연산으로 오버로딩 할 때



### 분명하지 않은경우

관례를 충족하는지 아닌지 확실하지 않을 때 : 

- infix를 활용한 확장함수를 사용한다.

```kotlin
infix fun Int.timesRepeated(operation: ()->Unit) = repeat(this) {operation()}

val tripledHello = 3 timesRepeated { print("hello") }
tripledHello() // hellohellohello
```

- 톱레벨 함수(top-level function) 를 사용한다 : 클래스 또는 다른 대상 내부에 있지 않고, 가장 외부에 있는 함수



### 규칙을 무시해도 되는 경우

연산자 오버로딩 규칙을 무시해도 되는 경우 : 도메인 특화 언어(Domain Specific Language, DSL)를 설계할 때

```kotlin
body{
  div{
   +"Some text"
  }
}
```

DSL 코드이기 때문에 이와 같이 `String.unaryPlus` 사용해도 괜찮음.