# item11: 가독성을 목표로 설계하라

### 인식 부하 감소

가독성 : 코드를 읽고 얼마나 빠르게 이해할 수 있는지

코틀린의 여러 관용구들을 한 문장 내부에 조합해서 사용했을 때 복잡성이 더 빠르게 증가하는 경우가 있다.

또한 코틀린의 여러 관용구를 사용한 '굉장히 창의적인 구조'는 유연하지 않고, 지원도 제대로받지 못한다. 최악의 경우 실행 결과가 다를 수도 있다.

기본적으로 "인지 부하"를 줄이는 방향으로 코드를 작성하자.

```kotlin
// 구현 A
if (person != null && person.isAdult) {
  view.showPerson(person)
} else {
  view.showError()
}
//구현 B
person?.takeIf {it.isAdult}
  ?.let(view::showPerson)
  ?: view.showError()
```



### 극단적이 되지 않기

`let` : 좋은 코드를 만들기 위해서 다양하게 활용되는 인기 있는 관용구

- 연산을 아규먼트 처리 후로 이동시킬 때

  ```kotlin
  print(students.filter{}.jointToString{}) 
  students.filter{}.joinToString{}.let(::print)
  ```

- 데코레이터를 사용해서 객체를 랩할 때

  ```kotlin
  FileInputStream("/file.gz")
    .let(::BufferedInputStream)
    .let(::ZipInputStream)
    .let(::ObjectInputStream)
    .readObject() as SomeObject 
  ```

어떤 구조들이 복잡성을 가져오는지를 파악하고, 이 구조를 사용했을 때의 복잡하지는 않은지, 가독성 비용이 높지는 않은지 균형을 맞추기



