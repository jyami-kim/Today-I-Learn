### 1. 싱글턴 패턴

객체를 하나만 생성한다.

Private static 객체를 만들고, 
Class를 public으로 만든다.
Constructor는 private으로 만든다. 추가로 혹시 constructor가 이상한 경우에 호출될 경우, error를 보낸다.

그리고 get메서드로 instance를 얻어온다.

생성한 객체를 참조하는 방식으로! : 모든 클라리언트가 동일한 인스턴스를 갖는다.



### 2. 팩토리 메서드 패턴

Factory라는 말 그대로, 객체를 생성하는 것만을 전담하는 클래스를 만든다.

static factoryMethod(); 를 이용해서 Factory class가 아닌 다른 클래스를 생성 할 수 있다.

Id 값 부여할 때 좋을 것 같다.