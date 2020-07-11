# volatile 과 long double

https://github.com/Java-Bom/ReadingRecord/issues/152

> p.445 모든 스레드가 필드의 값을 다시 계산해도 상관없고 필드의 타입이 long과 double을 제외한 다른 기본 타입이라면,
> 단일 검사의 필드 선언에서 volatile 한정자를 없애도 된다.

질문 : 왜 long과 double 이 아닐때만 없애도 된다는건지 모르겠어..



### volatile

volatile은 variable에 접근하는 것에 대한 원자성을 보장해준다.

> in which case the Java memory model ensures that all threads see a consistent value for the variable

어쨋든 volatile은 여러 쓰레드에서 하나의 변수가 같은 값을 읽도록 보장을 하는 것이 메인이기 때문에.
메모리를 2번을 접근을 한다고 해도 어쨋든 같은 값을 읽도록 보장을 무조껀 해줌 따라서 variable 값에 ㄸ라느 원자성을 보장.



### double과 long의 특징

double과 long은 non-atomic이라서 그래. (long과 double은 64bit의 메모리로 돌아가서 앞의 32비트, 뒤의 32비트를 합쳐서 사용하기 때문)
다른 privmitive 타입은 atomic이라서 다시 계산해도 상관없다면 없애도 된다고 했는데 이들은 32bit의 메모리공간을 가지고 있기 때문이야.

실제로 아래 링크의 oracle java docs를 보면 다음을 이야기 하고 있어

> For the purposes of the Java programming language memory model, a single write to a non-volatile long or double value is treated as two separate writes: one to each 32-bit half. This can result in a situation where a thread sees the first 32 bits of a 64-bit value from one write, and the second 32 bits from another write.

Writes and reads of volatile long and double values are always atomic.

Writes to and reads of references are always atomic, regardless of whether they are implemented as 32-bit or 64-bit values.

Some implementations may find it convenient to divide a single write action on a 64-bit long or double value into two write actions on adjacent 32-bit values. For efficiency's sake, this behavior is implementation-specific; an implementation of the Java Virtual Machine is free to perform writes to long and double values atomically or in two parts.

Implementations of the Java Virtual Machine are encouraged to avoid splitting 64-bit values where possible. Programmers are encouraged to declare shared 64-bit values as volatile or synchronize their programs correctly to avoid possible complications.

요약하자면

- volatile이 설정되지 않은 long, doulbe에 대한 쓰기는 두번에 이루어짐 : 첫번째 32비트 쓰고 다음 쓰기에서 두번째 32비트를 쓴다.
- volatile이 설정된 long, double이라면 항상 원자적임
- 프로그래머는 shared 되는 62bit 값은 volatile이나 synchronize 로 선언하는게 좋다. complication을 피하기 위해!

https://docs.oracle.com/javase/specs/jls/se7/html/jls-17.html#jls-17.7

- https://stackoverflow.com/questions/3038203/is-there-any-point-in-using-a-volatile-long

