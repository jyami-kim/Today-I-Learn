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
  > ​



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



##### 3-1. 반사성(reflexivity)

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



##### 3-2. 대칭성(symmetry)

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



##### 3-3. 추이성(trasitivity)

> null이 아닌 모든 참조 값 x, y, z에 대해 x.equals(y)가 true이고 y.equals(z)도 true이면, x.equals(z)도 true이다.

첫번째 객체와 두번째 객체가 같고, 두번째 객체와 세번째 객체가 같다면 첫번째 객체와 세번째 객체도 같아야 한다. (**삼단논법**)

**이 추이성 조건때문에, equals를 재 정의 하면 안되는 경우에 superclass에서 equals를 정의한 경우를 언급함**



[문제점]

1. 대칭성 위배 문제에 빠질 수 있다.
2. 추이성 위배 문제에 빠질 수 있다.
3. 무한 재귀에 빠질 수 있다.
4. ​



구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재하지 않는다.





##### 3-4. 일관성(consistency)

> null이 아닌 모든 참조 값 x, y에 대해 x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다.



##### 3-5. null-아님

> null이 아닌 모든 참조값 x에 대해, x.equals(null)은 false이다.





