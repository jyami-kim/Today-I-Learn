### 1. Server - Client

Server 서비스 제공자 / client 사비스 요청자, 사용자, 다른 서버



### 2. Http 프로토콜

status less (연결상태를 유지하지 않음)

80포트를 사용

클라이언트와 서버 사이이루어지는 req/res 프로토콜이다.



### 3. Http Method

get / post / put / delete



### 4. MVC

model view controller architecture

contoller는 model을 통해서 데이터를 가져오고 그 정보를 바탕으로 view를 통해 시각적으로 데이터 전달



### 5. Spring MVC

Request -> Dispatcher Servlet 

Dispatcher Servlet -> Handler Mapping(정보를 참조해 Controller 찾음) -> Controller

Controller -> ModelAndView

ModelAndView (ViewResolver를 참고해서 View를 찾음) - > View



### 6. Servlet

java를 이용해서 웹페이지를 동적으로 생성하는 서버 프로그램 (자바 클래스)
jsp : html에 java코드가 포함 / Servent : java에 html코드가 포함 



### 7. WAS

인터넷에서 HTTP로 사용자 애플리케이션을 동적으로 수행 할 수 있게 해준다. DB등을 연결하기 때문이다. 자바기반이다. Web Serverd의 개념을 포함한다 Tomcat이 대표적이다.

tomcat - Webserver와 연동해 실행 할 수 있는 자바 환경을 제공한다. servlet, jsp 등을 제공한다.



### 8. Web Server

HTTP를 통해 웹 브라우저에 요청을 하면, html css image등의 정적 데이터를 전송해준다. Nginx



### 9. Spring IoC

IoC : 제어의 역전

객체의 생성과 소멸을 스프링 컨테이너가 대신해주는 것이다. 원래 객체의 생성과 소멸은 객체 자신에게 제어권이 있는데, 이 제어권을 스프링이 갖게 되어서 제어의 역전이라는 말을 붙이게 되었다. 스프링 객체 Bean의 생성과 소멸을 스프링 컨테이너가 대신해준다.



### 10. Spring DI

DI : 의존성 주입

인스턴스를 자신이 아닌 SpringIoC에서 생성한 후, 주입한다.

내부적으로 new 키워드 대신에 constructor나 setter를 이용한다.



### 11. Spring Container

인스턴스의 생명주기를 관리한다. IoC의 기능을 수행한다. 즉 객체의 생성 및 소멸에 대한 제어권을 갖고있다. IoC를 사용해 어플리케이션의 Bean을 관리한다.

- BeanFactory

  DI의 기본 사항을 제공한다. 팩토리 패턴이 구현되어있다. Bean을 생성하고 분배한다. LazyLoading (getBean이 생성된 시점에서 빈을 생성한다.)

- ApplicationContext

  BeanFactory 인터페이스의 부모 인터페이스이다. 

- Spring Bean

  스프링 컨테이너에서 만들고 관리되어지는 자바 객체이다. 다양한 어노테이션을 이용해서 스프링 컨테이너에 빈을 등록한다.

- POJO

  자바 객체



### 12. Spring Business Logic Layer

트랜잭션 단위로 이루어진다.

트랜잭션의 속성

- 원자성 : 트랜잭션의 모든 처리는 전부 성공하거나 전부 실패
- 일관성 : 데이터에 일관성이 있어야한다.
- 고립성 : 서로 다른 트랜잭션이 영향줄 수 없다.
- 영속성 : 트랜 잭션 결과는 지속되어야한다.



### 13. Cloud Computing

클라우드를 통해 자원 등 서비스를 제공하는것

- IasS : 서버를 운영하는 자원 등을 가상 환경에서 제공한다. (EC2)
- PaaS : 인프라 위에 서비스를 개발 할 수 있는 환경을 제공한다. 인프라 유지보수 작업 들지 않는다. (구글 네이버 카카오 제공 open API)
- SaaS : 클라우드 환경에서 운영되는 애플리케이션 서비스 : 구글 앱, 클라우드 스토리지 서비스



### 14. Database

실시간 접근 / 지속적인 변화 / 동시 공유 / 내용에 대한 참조 / 데이터 논리적 독립성

- relationship : 1-1, 1-N, N-M

- 데이터 무결성 : 데이터의 정확성과 일관성을 보증한다. 

  Entity Integrity : PK가 고유해야한다. 

  Reference Integrity : 외래키 값이 특정 테이블의 기본 키 값을 참조한다.

  Domain Integrity : 정의된 범위에서 RDB모든 컬럼이 선언되어야한다.

- 트랜잭션의 속성

  원자성 : 트랜잭션안 모든 처리는 전부 성공하거나 전부 실패

  일관성 : 트랜잭션 성공하면 DB는 언제나 일관성 있는 상태를 유지한다.

  고립성 : 서로 다른 트랜잭션이 영향 줄 수 없다.

  영속성 : 트랜 잭션 결과는 지속되어야한다.



### 15. Index

RDBMS에서 검색속도를 높이기 위해 사용하는 기술

테이블 전체를 전부 스캔하지 않고 , index 파일을 검사한다. 

Where의 대상을 index로 만드는게 좋다. / 최대한 중복도가 높은 칼럼을 지정하는게 좋다.



### 16. 상태이상

삭제이상 - 원하지 않는 자료 삭제, 원하는 자료가 삭제되지 않음

삽입이상 - 원하지 않는 자료 삽입, 원하는 자료가 삽입되지 않음

갱신이상 - 데이터를 조회할 때 데이터가 모호해지거나 일관성이 없어진다.



### 17. MySQL

다중 사용자 , 다중쓰레드

이노 디비 : 기본값 엔진 - 트랜잭션, 롤백, 데이터 복구 등 / FK제약 및 PK제약에 대한게 있다.



### 18. 쿠키

웹브라우저에 저장되는 키 발류가 들어가있는 데이터 파일이다.

일정 시간동안 클라이언트에 데이터를 저장할 수 있다.

클라이언트에 저장해둔다.

브라우저 자체가 요청할 때 Header에 넣어서 자동으로 서버에 전송한다 (HTTP특징)



### 19. 세션

같은 브라우저에서 들어오는 요구를 하나의 상태로 보고 그 상태값을 서버에 저장해둔다. 

세션은 서버 메모리에 저장된다.

세션을 구분하기 위한 ID를 쿠키를 이용한다.



### 20. Spring AOP

관점지향 프로그래밍

클라이언트가 서버에게 정보를 주기전에 AOP에서 우선적으로 어떤 내용을 처리하고 서버에 전달한다. 

공통된 부분을 잘라냈다고해서 크로스 커팅이라고도한다.

로깅, 트랜잭션, 보안에 사용된다.