checked exception : runtime exception이 아님

 - 코드상에서 **무조껀 처리**해줘야하는 exception (IO) -> 안잡으면 **compile error**가 난다. 
 - 컴파일단에서 잡을 수 있는 exception
 - 안돌려봐도 터질 수 있음을 알 수 있음
 - 쓰는 클라이언트에서 불편하다 (try-catch로 다 잡아야 해서)

unchecked exception

- runtime exception 상속받는것 (null point exception, index boundary exception)
- 코드단에서 처리하지 않아도 되는 exception
- 예측이 가능하고 피할 수 있는 exception이다.
- 돌려봐야 터지는줄 알 수 있는!
- 내가 만드는건 대부분 unchecked exception



**CloneNotSupportedException**

애초에 만들었을 때 checked로 만들어서 throw를 하거나 try-catch로 잡아야한다.

CloneNoteSupportedException 는 checkedException인데, effective java item 12에서 말을 하는건, 여기서는 checkedException을 할필요가 없으니 try catch로 묶은 후 이 CloneNotSupportedException을 AssertionError()와 같은 unCheckedException으로 바꾸어주는게 좋다.

```java
@Override
public Stack clone(){
  try{
    return (Stack) super.clone();
  }catch(CloneNotSupportedException e){
    throw new AssertionError();
  }
}
```



### mixin

A 인터페이스를 구현한 객체는 A.method의 기능을 할 수 있는 것이다.

Cloneable 객체에 아무런 메소드가 없음. clone은 실제로 Object에 있음.

Clonable 객체 상속을 안받으면 CloneNotSupportedException이 터지는데, 

Clonable 인터페이스 객체의 문제는 안에 clone 메서드가 없기 때문이다.



```java
// Object.class
protected native Object clone() throws CloneNotSupportedException;
```



### Error vs Exception

error가 터지면 process가 종료된다.







