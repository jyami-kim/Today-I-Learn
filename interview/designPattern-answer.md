### 1. 싱글턴 패턴

객체를 하나만 생성한다.

Private static 객체를 만들고, 
Class를 public으로 만든다.
Constructor는 private으로 만든다. 추가로 혹시 constructor가 이상한 경우에 호출될 경우, error를 보낸다.

그리고 get메서드로 instance를 얻어온다.

생성한 객체를 참조하는 방식으로! : 모든 클라리언트가 동일한 인스턴스를 갖는다.



### 2. 팩토리 메서드 패턴

Factory라는 말 그대로, 객체를 생성하는 것만을 전담하는 클래스를 만든다.

부모클래스에 알려지지 않은 자식 클래스를 생성하는 패턴이며 팩터리 클래스에서의 메서드를 이용해서 어떤 자식클래스를 생성할지 결정하는 패턴이다.

그래서 Robot을 리턴값으로, 실제는 BakeRobot으로 구현된, 실제는 SingingRobot으로 구현된 여러 자식 클래스를 만들 수 있다.



### 3. 템플릿 메서드 패턴

추상클래스를 하나 정의하고, 이 추상클래스를 상속받아 구체클래스에서 각 클래스별로 동작을 다르게 하는 코드를 짜는 패턴을 이야기 한다. 다형성이 여기에 해당한다.


