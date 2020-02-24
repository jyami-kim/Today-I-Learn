#### 2. 함수형 프로그래밍

피연산자에 함수를 적용해 결과를 반환하지만, 피연산자 자체는 그대로이다.

// 예시 코드

#### 3. 불변 객체의 장점

ㄱ. 근본적으로 스레드 안전 : 동기화 할 필요가 없다.

ㄴ. 불변객체는 안심하고 공유 가능 : 스레드 간 영향을 주고받을 수 없기 때문

방어적 복사도 필요없다 > clone메서드 복사 생성자 필요 없다
String 클래스의 복사생성자는 되도록 사용하지 말자

 *[아이템 50] 적시에 방어적 복사본을 만들라*
 *[아이템 13] clone 재정의는 주의해서 진행하라*
 *[아이템 6] 불필요한 객체 생성을 피하라*

ㄷ. 한번 만든 인스턴스 최대한 **재활용** 가능

자주 사용되는 인스턴스를 생성하여, 정적팩터리를 제공할 수 있다.
   *[아이템 1] 생성자 대신 정적 팩터리 메서드를 고려하라*

메모리 사용량 감소 / 가비지 컬렉션 비용 감소
예시) BigInteger, Wrapper Class



ㄹ. 불변객체는 자유롭게 공유할 수 있음은 물론, 불변 객체끼리는 내부 데이터를 공유할 수 있다.

```java
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



ㅁ. 객체를 만들 때 다른 불변 객체들을 구성요소로 사용하면 이점이 많다 
​    : 구조가 복잡해도 불변식 유지가 수월

ㄹ. 불변객체는 그 자체로 실패 원자성을 제공한다
  [아이템 76] 가능한한 실패 원자적으로 만들어라
  실패 원자성 : 메서드에서 예외가 발생한 후에도 그 객체는 메서드 호출전 상태와 같은 유효한 상태를 가진다.



#### 4. 불변 객체의 단점

값이 다르면 반드시 독립된 객체로 만들어야 한다.
값의 가짓수가 많으면 이를 모두 만드는데 큰 비용이 필요하다.

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



##### 4-1. 대처법1 흔히 쓰일 다단계 연산들을 예측하여 기본 기능으로 제공하는 방법

ex ) BigInteger: 다단계 연산 속도를 높여주는 가변 동반클래스를 package-privated으로 두고있다.

 ```java
public BigInteger add(BigInteger val) {} // public api
BigInteger add(long val) {}
private static int[] add(int[] x, long val) {} // 실제 연산 메서드
 ```



##### 4-2. 대처법2 