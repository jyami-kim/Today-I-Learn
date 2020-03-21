# 제네릭(Generic)

컬렉션 안에 제네릭 형변환 코드들이 들어가있음

제네릭 타입을 넣어주는건 컴파일 타임에 넣어준다. (캐스팅 변환정보 같은 것들을)
Cast E / E -> String 으로 캐스팅해! 이거는 컴파일 타임에 이루어진다. / 런타임에는 타입이 이루어지지 않는다.

----

List와 Array의 가장 큰 차이점을 생각했을 때!

List는 애초에 컴파일 타임에 형변환을 하고, 불공변이기 때문에 타입캐스팅 자체가 컴파일 타임에 이루어지고, 런타임에는 캐스팅 된 것을 그저 겟을 하기만한다.

하지만 Array는 런타임에 타입 정보를 갖고 형변환을 하기 때문에, 런타임에 타입캐스팅, 혹은 저장이 이루어진다.

```java
Obejct[] objects = new String[3];
objects[0] = "abc";
objects[1] = 1;	
// ArrayStoreException (Runtime에 난다.)
// 컴파일 타임에는 안전한데, 런타임에는 안전하지 않다고 뜨는것.
// 안전하지 않다는 
```

```java
List<Object> objectList = new ArrayList<>();	// Object 타입이 들어간다.
objectList.add("abc");
objectList.add(1);	// 문제가 없다.
// 그러나 이후에 Casting을 잘못하면 안된다. 
```

---

List에서는 불공변이라서 뒤에 있는 arrayList를 String으로 캐스팅이 불가능 해서 위 Array와 같은 사례가 이루어지지 않는다.

```java
List<Object> list = new ArrayList<Object>(); // 불공변이다.
Object[] array = new String[] // 공변이다.
```

---

``List<?> lists``

add는 안된다. law 타입에서는 add가 가능했으나 사용하지 말아야할 것으로 취급된다.
타입 값은 몰라도 되는데, 받아서 확인해야하는 경우 (모든 타입을 상관 없이 받아야 할 때 사용)

>  그 안에 같은 오브젝트가 있는지 등의 요구사항을 실현할 때
>
> 타입이 아무렇게나 들어와도 주소값 만을 비교해서 같은게 있는지 확인하는 코

---

제네릭 = 실체화되지 않는 타입이다.

컴파일 타임에서 타입체킹을 하고, 런타임에서는 타입정보를 가지고잇지 않는다 -> 실체화 되지 않는 타입이다.

----

```java
// source, destination에 들어가는게 Number 타입이기만 하면 상관없다.
// 값을 갱신하지 못한다. (읽기)
// (Integer, Float)
// (Integer, Integer)
// (Float, Integer)
// (Float, float)
private void count(Set<? extends Number> source, Set<? extends Number> destination);

// source destination에 들어가는게 Number 타입이고, 두 타입이 같아야한다.
// 값을 갱신 할 수 있다. (읽기 쓰기)
// (Integer, Integer)
// (Float, Float)
private <E extends Number> void count2(Set<E>source, Set<E> destination);
```

제네릭을 사용한다고 ``<E extends Number>`` 표기를 무조껀 해주어야한다.

```java
// (Integer, Integer)
// (Float, Float)
private <E extends Number> void count2(Set<E>source, Set<E> destination);

// (Integer, Float)
// (Integer, Integer)
// (Float, Integer)
// (Float, float)
private <E,T extends Number> void count2(Set<E>source, Set<T> destination);
```

