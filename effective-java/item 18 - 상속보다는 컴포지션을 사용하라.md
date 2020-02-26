# item 18 - 상속보다는 컴포지션을 사용하라

### 1. 구체클래스 상속의 위험성

다른 패키지의 구체 클래스를 상속하는 일은 위험하다 (인터페이스 상속말고, 구현 상속에서)

메서드 호출과 달리 상속은 캡슐화를 깨뜨린다.
\- 상위 클래스에 따라 하위클래스의 동작에 문제가 생기기 때문 (상위클래스의 릴리즈가 이뤄진다면?)

'자기사용' : 자신의 다른 부분을 사용 > 내부 구현 방식이므로 오픈 api가 아니라 다름 릴리즈 변경, 삭제가능

#### 하위 클래스가 깨지기 쉬운 이유

**ㄱ. 상위 클래스의 메서드를 재정의 하여 하위클래스의 로직을 방어한다 **(HashSet addAll() 예제)

- 상위클래스의 메서드 동작을 다시 구현하는게 어렵다.
- 시간도 더든다.
- 오류나 성능을 떨어뜨릴 수 있다.
- 하위클래스에서 접근 불가한 private 클래스를 써야하면 구현이 불가능하다.

**ㄴ. 상위 클래스 릴리즈에서 새로운 메서드를 추가했을 때를 고려해야한다.**

상위 클래스의 다음 릴리즈에서 나온 새로운 메서드를 이용해 하위 클래스에서 '허용되지 않은' 원소를 추가할 수 잇는 사태가 생 길 수 있다. (예시 : Hashtable, Vector를 컬렉션에 포함했을 때)

**ㄷ. 하위클래스에 추가한 새 메서드가, 상위 클래스 다음 릴리즈에서 같은 시그니처를 가질 때**

운이 없을경우겠지만, 시그니처가 같고 반환 타입이 다르면 컴파일조차 안된다
또한 상위 클래스의 메서드가 요구하는 규약을 만족하지 못할 가능성이 크다.



## 2. 컴포지션 설계

새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조한다.

**컴포지션(composition)** : 기존 클래스가 새로운 클래스의 구성요소로 쓰인다.
**전달(forwarding) **: 새 클래스의 인스턴스 메서드들은 기조 클래스의 대응하는 메서드를 호출해 그 결과를 반환한다.
**전달 메서드(forwarding method)** : 새 클래스의 메서드

새로운 클래스는 기존 클래스의 내부 구현방식의 영향에서 벗어난다.

```
public class InstrumentedSet<E> extends ForwardingSet<E> {
    private int addCount = 0;

    public InstrumentedSet(Set<E> s) {
        super(s);
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override public boolean addAll (Collection< ? extends E > c){
        addCount += c.size();
        return super.addAll(c);

    }

    public int getAddCount () {
        return addCount;
    }
}
```

전달 클래스 ForwardingSet
Set 내부구현에 영향받지 않고 사용하기 위한 class이다.
만약 위에 클래스에서 그대로 implements를 했다면, 모든 메서드를 구현하는데 있어서 @Override를 사용했을 것이므로.

```
public class ForwardingSet<E> implements Set<E> {

    private final Set<E> s;
    public ForwardingSet(Set<E> s) { this.s = s;}

    public void clear() { s.clear(); }
    public boolean contains(Object o) {return s.contains(o); }
    public boolean isEmpty() { return s.isEmpty(); }
    public int size() { return s.size(); }
    public Iterator<E> iterator() { return s. iterator(); }
    public boolean add(E e) { return s.add(e); }
    public boolean remove(Object o) {return s.remove(o); }
    public boolean containsAll(Collection<?> c) { return s.containsAll(c); }
    public boolean addAll(Collection<? extends E> c) { return s.addAll(c); }
    public boolean removeAll(Collection<?> c) { return s.removeAll(c); }
    public boolean retainAll(Collection<?> c) { return s.retainAll(c); }
    public Object[] toArray() { return s.toArray(); }
    public <T> T[] toArray(T[] a) { return s.toArray(a); }
    @Override public boolean equals(Object o) { return s.equals(o); }
    @Override public int hashCode() {return s.hashCode(); }
    @Override public String toString() { return s.toString(); }

}
```

**래퍼클래스(wrapper class) **: 다른 인스턴스를 감싸고 있다.
**위임(delegation) **: composition과 forwarding의 조합 (래퍼 객체가 내부 객체에 자기 자신의 참조를 넘기는 경우)

래퍼클래스 주의점 : **콜백** 프레임워크와는 어울리지 않는다.
참고 자료 : <http://bit.ly/2LepViV>



## 3. 상속의 원칙

하위 클래스가 상위 클래스의 '진짜' 하위 타입인 상황에서만 사용한다.
클래스 B가 클래스 A와 **is-a** 관계일때만 클래스 A를 상속한다. "B가 정말 A인가"
  예 ) Java에서 위반한 클래스 : Stack은 Vector를 확장해서는 안됐다. Properties도 Hashtable을 확장해서는 안됐다.

컴포지션을 써야할 상황에서 상속을 사용하는 건 **내부구현을 불필요하게 노출**시킨다.
상속은 결함까지 전달하지만 합성은 결함 전달을 방어할 수 있다.