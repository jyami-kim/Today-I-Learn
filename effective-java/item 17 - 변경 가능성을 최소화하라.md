**불변 클래스** : 그 인스턴스의 내부 값을 수정할 수 없는 클래스

*  예 ) String, Wrapper Class, BigInteger, BigDecimal*  - 설계 구현 사용이 쉽다.

*  오류 여지가 적고 안전하다.

   ​

### 1. 불변 클래스 5가지 규칙

ㄱ. 객체의 상태를 변경하는 메서드(변경자)를 제공하지 않는다. (setter)
ㄴ. 클래스를 확장할 수 없게 한다. (extend X)
ㄷ. 모든 필드를 final로 선언한다
ㄹ. 모든 필드를 private으로 선언한다.
​    *[아이템 15] : 클래스와 멤버의 접근권한을 최소화하라*
​    *[아이템 16] : public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라*
ㅁ. 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 한다. 

   - 객체 참조 X   - getter가 그대로 참조값 반환 X   - 생성자, 접근자, readObject에서 방어적 복사

   *[아이템 88] : readObject 메서드는 방어적으로 작성하라*



### 2. 불변 객체의 장점

**ㄱ. 근본적으로 스레드 안전** : 동기화 할 필요가 없다.

**ㄴ. 불변객체는 안심하고 공유 가능** : 스레드 간 영향을 주고받을 수 없기 때문

방어적 복사도 필요없다 > clone메서드 복사 생성자 필요 없다
  String 클래스의 복사생성자는 되도록 사용하지 말자
  *[아이템 50] 적시에 방어적 복사본을 만들라*
  *[아이템 13] clone 재정의는 주의해서 진행하라*
  *[아이템 6] 불필요한 객체 생성을 피하라*

**ㄷ. 한번 만든 인스턴스 최대한 재활용 가능**

자주 사용되는 인스턴스를 생성하여, 정적팩터리를 제공할 수 있다. 
*[아이템 1] 생성자 대신 정적 팩터리 메서드를 고려하라*
메모리 사용량 감소 / 가비지 컬렉션 비용 감소
예시) BigInteger, Wrapper Class

**ㄹ. 불변객체는 자유롭게 공유할 수 있음은 물론, 불변 객체끼리는 내부 데이터를 공유할 수 있다.**

```
public class BigInteger extends Number implements Comparable<BigInteger> {
  final int signum;
  final int[] mag;
  (...)

  public BigInteger negate() {
    return new BigInteger(this.mag, -this.signum);
    //this.mag 배열은 비록 가변이지만 복사하지 않고 원본 인스턴스와 공유한다.
  }

  // 내부 패키지에서만 생성할 수 있음, 
  BigInteger(int[] magnitude, int signum) {
    this.signum = magnitude.length == 0 ? 0 : signum;
    this.mag = magnitude;
    if (this.mag.length >= 67108864) {
      this.checkRange();
    }
  }
}
```

**ㅁ. 객체를 만들 때 다른 불변 객체들을 구성요소로 사용하면 이점이 많다** : 구조가 복잡해도 불변식 유지가 수월

**ㄹ. 불변객체는 그 자체로 실패 원자성을 제공한다 **
  **실패 원자성 :** 메서드에서 예외가 발생한 후에도 그 객체는 메서드 호출전 상태와 같은 유효한 상태를 가진다.
  *[아이템 76] 가능한한 실패 원자적으로 만들어라* 

```
public Object pop(){
	if (size == 0)
    	throw new EmptyStackException();
    Object result = element[--size];
    elements[size];
    return result;
}
```

> 이부분을 제거해도 스택이 비었다면 여전히 예외를 던진다 다만 size의 값이 음수가되어 다음번 호출도 실패하게 만들며, 이때 던지는 ArrayIndexOutOfboundsException은 추상화 수준이 상황에 어울리지 않는다.



### 3. 불변 객체의 단점

값이 다르면 반드시 독립된 객체로 만들어야 한다.값의 가짓수가 많으면 이를 모두 만드는데 큰 비용이 필요하다.

```java
// bigInteger.flipBit(0);  O(n)
public BigInteger flipBit(int n) {
  if (n < 0) {
    throw new ArithmeticException("Negative bit address");
  } else {
    int intNum = n >>> 5;
    int[] result = new int[Math.max(this.intLength(), intNum + 2)];

    for(int i = 0; i < result.length; ++i) {
      result[result.length - i - 1] = this.getInt(i);
    }

    result[result.length - intNum - 1] ^= 1 << (n & 31);
    return valueOf(result);
  }
}
// bitset.flip(0);  O(1)
public void flip(int bitIndex) {
  if (bitIndex < 0) {
    throw new IndexOutOfBoundsException("bitIndex < 0: " + bitIndex);
  } else {
    int wordIndex = wordIndex(bitIndex);
    this.expandTo(wordIndex);
    long[] var10000 = this.words;
    var10000[wordIndex] ^= 1L << bitIndex;
    this.recalculateWordsInUse();
    this.checkInvariants();
  }
}
```

##### 3-1. 흔히 쓰일 다단계 연산들을 예측이 될 때.

다단계 연산 속도를 높여주는 가변 **동반클래스를 package-privated**으로 둔다.

```java
public BigInteger add(BigInteger val) {} // public api
BigInteger add(long val) {}
private static int[] add(int[] x, long val) {} // 실제 연산 메서드
```

##### 3-2. 흔히 쓰일 다단계 연산들이 예측이 안될 때.

다단계 연산 속도를 높여주는 가변 동반클래스를 public 으로 둔다.
예시 ) String 클래스의 연산속도 높이기 : StringBuilder / StringBuffer

자바봄 이슈 내용: <https://github.com/Java-Bom/ReadingRecord/issues/47>



### 4. 불변클래스를 만드는 설계 방법

**ㄱ. final 클래스** : 상속을 막는다.

**ㄴ. 정적 팩터리를 제공하는 방법 **

모든 생성자를 private 혹은 package-private으로 만들고 public 정적 팩터리를 제공한다. (캐싱 기능 추가도 가능하다.) public이나 protected 생성자가 없으니 다른 패키지에서는 이 클래스를 확장하는게 불가능하기 때문, 

  *[아이템 1] 생성자 대신 정적 팩터리 메서드를 고려하라*



### 5. BigInteger, BigDecimal 설계시 주의점

이 두 클래스의 메서드가 모두 재정의할 수 있게 설계되어있음. 
인수로 받은 객체가 '진짜'인지 확인하고, 이 인수들은 가변으로 가정하고 방어적으로 복사해서 사용해야한다.
  *[아이템 50] 적시에 방어적 복사본을 만들라*

자바봄 이슈 내용 : <https://github.com/Java-Bom/ReadingRecord/issues/48>



### 6. 불변 객체 기준 완화

"어떤 메서드도 객체의 상태 중 외부에 비치는 값을 변경할 수 없다."
계산 비용이 큰 값을 나중에 계산하여 final 이 아닌 필드에 캐싱해둔다.

실제로 String 클래스는 불변객체이지만, final이 아닌 필드 hash를 이용해 캐시하여 hashcode 재 연산 비용을 줄인다.
  *[아이템 83] 지연초기화는 신중히 사용하라*

자바봄 이슈 내용 : <https://github.com/Java-Bom/ReadingRecord/issues/51



### 7. 정리

- 클래스가 꼭 필요한 경우가 아니면 불변이어야 한다. (getter가 있다고 해서 setter를 무조껀 만들지는 말자)
- 무거운 값객체의 경우 성능때문에 어쩔수 없다면, 불변 클래스와 쌍을 이루는 가변 동반 클래스를 public 클래스로 제공하자.
  *[아이템 67] 최적화는 신중히 하라*
- 불변으로 만들 수 없는 클래스라도 변경할 수 있는 부분을 최소한으로 줄이자
- 다른 합당한 이유가 없다면 모든 필드는 private final이어야 한다.
- 생성자는 불변식 설정이 모두 완료된, 초기화가 완벽히 끝난 상태의 객체를 생성해야한다. 