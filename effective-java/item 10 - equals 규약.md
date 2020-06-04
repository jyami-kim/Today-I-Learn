# item 10 : equals는 일반 규약을 지켜 재 정의하라

### 1. equals를 재정의 하면 안되는 경우

equals는 재정의하기 쉬워보이지만 곳곳에 함정이 있다. 문제를 회피하는 가장 쉬운 길은 **아예 재정의하지 않는 것**

- 각 인스턴스가 본질적으로 고유할 때

  > 값 표현 객체가 없을 때를 이야기 하는 것 같다. Bean에 등록해두는 객체 repository, controller, service 등이 이에 해당할 것 같다.
  >
  > DTO, Domain 객체는 값 검증이 필요할 수 있으니 equals를 재정의 해야할 수도 있다.

- 인스턴스의 논리적 동치성(logical equality)를 검사할 일이 없을 때

  > 논리적 동치성 검사의 1가지 : Pattern의 인스턴스가 같은 정규 표현식을 나타내는 지 검사

- 상위 클래스에서 재정의한 equals가 하위 클래스에도 들어맞을 때

  > 같은 특징을 가지기 때문에 equals를 상속받아 사용하는 걸 권장하는 듯 하다 > 근데 이유가 뒷편에 나옴

- 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없을 때

  > equals가 실수로 호출되는 걸 막고 싶다면
  >
  > ```java
  > @Override public boolean equals (Object o){
  >   throw new AssertionError();
  > }
  > ```
  >




### 2. equals를 재정의 해야하는 경우

객체 식별성(object identity) : X - 두 객체가 물리적으로 같은가

논리적 동치성(logical equality) : O  

객체 식별성이 아니라 논리적 동치성을 확인해야하는데, 상위 클래스의 equals가 **논리적 동치성**을 비교하도록 재정의 되지 않았을 때. (주로 **값 클래스**)

```java
public class Fruit{
  private String name; // name이 같을 경우 두 객체는 같다 (논리적 동치성)
}
```

##### 

##### 2-1. 값 클래스의 equals를 재정의 할 때 기대 효과

- 값을 비교하길 원하는 프로그래머의 기대에 부흥
- Map, Set의 원소로 사용가능



##### 2-2. 값 클래스여도 equals를 재정의 할 필요가 없을 때

인스턴스 통제 클래스 : 값이 같은 인스턴스가 2개 이상 만들어 지지 않음 (ex Enum)

어차피 논리적으로 같은 값이 0개 혹은 1개 이기 때문에 객체 식별성으로 판단한다. (기존의 equals)



### 3. Equals 메서드의 규약 - 동치관계

동치 클래스 (equivalence class) : 집합을 서로 같은 원소들로 이루어진 부분집합으로 나누는 연산

> equals 메서드가 쓸모 있으려면 모든 원소가 같은 동치류에 속한 어떤 원소와도 서로 교환이 가능해야한다.



#### 3-1. 반사성(reflexivity)

> null이 아닌 모든 참조 값 x에 대해 x.equals(y)는 true이다.

객체는 자기 자신과 같아야한다.

```java
public class Fruit{
  private String name;
  
  public Fruit(String name){
    this.name = name;
  }
  
  public static void main(){
    List<Fruit> list = new ArrayList<>();
	Fruit f = new Fruit("apple");
    list.add(f);
    list.contains(f); // false일 경우에는 반사성을 만족하지 못하는 경우이다.
  }
}
```



#### 3-2. 대칭성(symmetry)

> null이 아닌 모든 참조 값 x, y에 대해 x.equals(y)가 true이면 y.equals(x)도 true이다.

두 객체는 서로에 대한 동치 여부에 똑같이 답해야 한다.



대칭성을 위반한 클래스

```java
public final class CaseInsensitiveString{
  private final String s;
  
  public CaseInsensitiveString(String s){
    this.s = Obejcts.requireNonNull(s);
  }
  
  @Override public boolean equals(Object o){
    if(o instanceof CaseInsensitiveString)
      return s.equalsIgnoreCase(((CaseInsensitiveString) o).s);
    if(o instanceof String) // 한방향으로만 작동한다.
      return s.equalsIgnoreCase((String) o);
    return false;
  }
}
```

```java
CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
String s = "polish";

cis.equals(s); // true
s.equals(cis); // false
```



대칭성을 만족하게 수정

```java
@Override public boolean equals(Object o){
  return o instanceof CaseInsensitiveString && ((CaseInsensitiveString) o).s.equalsIgnoreCase(s); // String에 대한 instanceof 부분을 빼고 구현한다.
}
```



#### 3-3. 추이성(trasitivity)

> null이 아닌 모든 참조 값 x, y, z에 대해 x.equals(y)가 true이고 y.equals(z)도 true이면, x.equals(z)도 true이다.

첫번째 객체와 두번째 객체가 같고, 두번째 객체와 세번째 객체가 같다면 첫번째 객체와 세번째 객체도 같아야 한다. (**삼단논법**)

**이 추이성 조건때문에, equals를 재 정의 하면 안되는 경우에 superclass에서 equals를 정의한 경우를 언급함**



[문제점]

1. 대칭성 위배 문제에 빠질 수 있다.

   ```java
   // ColorPoint.java 의 equals
   @Override public boolean equals(Object o){
     if(!(o instanceof ColorPoint))
       return false;
     return super.equals(o) && ((ColorPoint) o).color == color;
   }

   public static void main(){
     Point p = new Point(1,2);
     ColorPoint cp = new ColorPoint(1,2, Color.RED);
     p.equals(cp);	// true (Point의 equals로 계산)
     cp.equals(p);	// false (ColorPoint의 equals로 계산: color 필드 부분에서 false)
   }
   ```

2. 추이성 위배 문제에 빠질 수 있다.

   ```java
   //ColorPoint.java의 equals
   @Override public boolean equals(Obejct o){
     if(!(o instanceof Point))
       return false;
     if(!(o instanceof ColorPoint))
       return o.equals(this);
     return super.equals(o) && ((ColorPoint) o).color == color;
   }

   public static void main(){
     ColorPoint p1 = new ColorPoint(1,2, Color.RED);
     Point p2 = new Point(1,2);
     ColorPoint p3 = new ColorPoint(1,2, Color.BLUE);
     p1.equals(p2);	// true (ColorPoint의 equals 비교 //2번째 if문에서 Point의 equals로 변환)
     p2.equals(p3);	// true (Point의 equals 비교 // x,y 같으니 true)
     p1.equals(p3);	// false (ColorPoint의 equals 비교)
   }
   ```

3. 무한 재귀에 빠질 수 있다.

   ```java
   //SmellPoint.java의 equals
   @Override public boolean equals(Obejct o){
     if(!(o instanceof Point))
       return false;
     if(!(o instanceof SmellPoint))
       return o.equals(this);
     return super.equals(o) && ((SmellPoint) o).color == color;
   }

   public static void main(){
     ColorPoint p1 = new ColorPoint(1,2, Color.RED);
     SmellPoint p2 = new SmellPoint(1,2);
     p1.equals(p2);
     // 처음에 ColorPoint의 equals로 비교 : 2번째 if문 때문에 SmellPoint의 equals로 비교
     // 이후 SmellPoint의 equals로 비교 : 2번째 if문 때문에 ColorPoint의 equals로 비교
     // 무한 재귀의 상태!
   }
   ```




구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재하지 않는다.

그렇다고 instanceof 검사 대신 **getClass** 검사를 하라는 것은 **아니다.** 

```java
@Override public boolean equals(Object o){
  if(o == null || o.getClass() != getClass())
    return false;
  Point p = (Point) o;
  return p.x == x && p.y == y;
}
```

**리스코프 치환원칙을 위배한다** : Point의 하위클래스는 정의상 여전히 Point이기 때문에 어디서든 Point로 활용되어야한다.

**리스코프 치환원칙 (Liskov substitution principle)** : 어떤 타입에 있어 중요한 속성이라면 그 하위 타입에서도 마찬가지로 중요하다. 따라서 그 타입의 모든 메서드가 하위 타입에서도 똑같이 잘 작동해야한다.

> Point의 하위클래스는 정의상 여전히 Point이므로 어디서든 Point로 활용가능해야한다.



**[우회 방법 : Solution]**

##### 3-3-1. 상속대신 컴포지션을 사용하라

```java
public class ColorPoint{
  private final Point point;
  private final Color color;
 
  public Point asPoint(){ //view 메서드 패턴
    return point
  }
  
  @Override public boolean equals(Object o){
    if(!(o instanceof ColorPoint)){
      return false;
    }
    ColorPoint cp = (ColorPoin) o;
    return cp.point.equals(point) && cp.color.equals(color);
  }
}
```

ColorPoint - ColorPoint : ColorPoint의 equals를 이용하여 color 값까지 모두 비교

ColorPoint - Point : ColorPoint를 asPoint() 메소드를 이용해 Point로 바꾸어서, Point의 equlas를 이용해 x,y만 비교

Point - Point : Point의 equals를 이용하여 x,y 값 모두 비교



##### 3-3-2. 추상클래스의 하위클래스 사용하기

추상클래스의 하위클래스에서는 equals 규약을 지키면서도 값을 추가할 수 있다.

> 상위 클래스를 직접 인스턴스로 만드는게 불가능하기 때문에 하위클래스끼리의 비교가 가능해지기 때문인 것 같다.



#### 3-4. 일관성(consistency)

> null이 아닌 모든 참조 값 x, y에 대해 x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다.

두 객체가 같다면 (어느 하나 혹은 두 객체 모두가 수정되지 않는한) 앞으로도 영원히 같아야한다.

가변객체 = 비교 시점에 따라 서로 다를 수 있다.
불변객체 = 한번 다르면 끝까지 달라야한다. 

equals는 항시 메모리에 존재하는 객체만을 사용한 결정적(deterministic) 계산만 수행해야한다.

클래스가 불변이든 가변이든 equals의 판단에 신뢰할 수 없는 자원이 끼어들면 안된다 : ex URL과 매핑된 호스트의 IP 주소



#### 3-5. null-아님

> null이 아닌 모든 참조값 x에 대해, x.equals(null)은 false이다.

모든 객체가 null 과 같지 않아야한다.

1) 명시적 null 검사

```java
@Override public boolean equals(Object o){
  if( o == null){
    return false;
  }
}
```

2) 묵시적 null 검사

```java
@Override public boolean equals(Obejct o){
  if(!(o instanceof MyType)) 	// instanceof 자체가 타입과 무관하게 null이면 false 반환함.
    return false;
  MyType mt = (MyType) o;
}
```



### 4. 양질의 equals 메서드를 구현하는 4단계

##### 4-1. == 연산자를 사용해 입력이 자기 자신의 참조인지 확인한다.

object identity를 검사한다.



##### 4-2. instanceof 연산자로 입력이 올바른 타입인지 확인한다.

올바른 타입의 경우 : equals가 정의된 클래스로 리턴이 되는가

올바른 타입이 아닌경우 ( == 구현한 특정 인터페이스) :구현한 (서로다른) 클래스간 비교가 가능하게 해야함

: 이런 구현체들은 인터페이스의 equals를 이용하여 비교해야한다.



##### 4-3. 입력을 올바른 타입으로 형변환 한다.

2번에서 instatnceof를 이용했기 때문에 100% 성공함



##### 4-4. 입력 객체와 자기 자신의 대응되는 '핵심' 필드들이 모두 일치하는지 하나씩 검사한다.

2번에서 인터페이스를 사용했다면 입력의 필드값을 가져올 때도 인터페이스 메서드를 사용해야한다.



### 5. Equals 구현할 때 주의할 추가사항

##### 5-1. 기본타입과 참조타입

기본타입 : == 연산자 비교

참조타입 : equals() 비교

float, double 필드 : Float.compare(float, float), Double.compare(double, double) 비교 (부동 소수 값)

> Float.equals(float)나 Double.equals(double) 은 오토 박싱을 수반할 수 있어 성능상 좋지 않다.

배열 필드 : 원소 각각을 지침대로 비교한다. / 모두가 핵심 필드라면 Arrays.equals()를 사용한다.

##### 5-2. null 정상값 취급 방지

Object.equals(object, object) 로 비교하여 NullPointException 발생을 예방하자

##### 5-3. 필드의 표준형을 저장하자.

비교하기 복잡한 필드는 필드의 표준형(canonical form)을 저장한후 비교 : 불변 클래스에 제격이다.

##### 5-4. 필드 비교 순서는 equals 성능을 좌우한다.

다를 가능성이 높은 필드 우선

비교 비용이 싼 필드 우선

핵심필드 / 파생필드 구분하자.

##### 5-5. equals 재정의할 땐 hashCode도 반드시 재정의하자.

##### 5-6. 너무 복잡하게 해결하려 들지 말자.

##### 5-7. Object 외의 타입을 매개변수로 받는 equals 메서드는 선언하지 말자

```java
public boolean equals(MyClass o) // @ㅒverrider가 아니다! // 재정의가 아니라 다중정의 상태임!
```

##### 5-8. AutoValue 프레임워크

<https://www.baeldung.com/introduction-to-autovalue>

> Lombok이랑 비슷한 느낌의 라이브러리인것 같다.