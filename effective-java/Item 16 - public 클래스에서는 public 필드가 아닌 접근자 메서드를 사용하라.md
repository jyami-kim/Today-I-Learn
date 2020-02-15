# Item 16 - public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라

### 1. 접근자와 변경자(mutator) 메서드를 활용해 데이터를 캡슐화 한다.

#### ㄱ. public class

```java
@Getter
@AllArgsConstructor
public class Point{
  private double x;
  private double y;
}
```

public class에서는 이 방식이 확실히 맞다.

**접근자 제공**으로 필드 공개를 안해서 클래스 내부를 마음대로 바꿀 수 있다.



#### ㄴ. Package-private class || private 중첩 클래스

**필드를 노출해도 문제가 없다.**

클래스가 표현하려는 추상 개념만 올바르게 표현한다.

클라이언트 코드가 이 클래스 내부 표현에 묶이지만, 클라이언트도 어차피 이 클래스를 포함하는 패키지 안에서만 동작한다. 

예제 코드 : <https://github.com/Java-Bom/ReadingRecord/issues/42>



### 2. 규칙을 어기는 사례

java.awt.package.Point

java.awt.package.Dimension



### 3. 불변 필드를 노출한 public 클래스

직접 노출보다는 단점이 줄어들지만,,! 좋지 않다.

API 변경하지 않고는 표현방식을 바꿀 수 없다.
필드를 읽을 때 부수 작업을 수행할 수 없다.

