# Item7. 결과 부족이 발생할 경우 null과 Failure를 사용하라

예외는 예외적인 상황이 발생했을 때 사용해야한다. (이유)

- 예외 전파 과정 추적하지 못한다
- 코틀린의 모든 예외는 unchecked 예외이다 : 관련 내용이 문서에 제대로 드러나지 않을 수 있다.
- 예외는 예외적인 상황을 위해 만들어져 명시적인 테스트만큼 빠르지 않다
- try-catch 블록 내부에 코드를 배치하면, 컴파일러가 할 수 있는 최적화가 제한된다.

함수가 원하는 결과를 만들어 낼 수 없을때 예외를 사용한다.

- 예측할 수 있는 범위의 오류 : try-catch 보다 효율적이다.
  - null : 추가적인 정보를 전달하지 않아도 될 때
  - 실패를 나타내는 sealed 클래스 : 추가적인 정보를 전달해야 할 때 sealed result
- 예외적인 범위의 오류
  - 예외 throw 

```kotlin
sealed class Result<out T>
class Success<out T>(val result: T): Result<T>()
class Failure(val throwable: Throwable): Result<Nothing>()
```

외에도 일반적으로 getOrNull, Elvis 연산자(?:) 를 사용하여, 개발자들이 무엇이 리턴되는지 예측할 수 있게 한다.

