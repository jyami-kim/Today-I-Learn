# 잔디콘

### 1. 정원사들 시즌4 출석부 제작 후기 - 김준호

초반에는 커밋을 엑셀로 하나하나 적어서 확인했다.

slack API를 이용해서 커밋을 확인하는 방법을 사용함 (csv로 넣을 수 있어졌다! > 엑셀 붙여넣기)

mongo DB 세팅 - Docker로 간단 설치

출석부 갱신이 늦어지면 > 독촉했음 : 귀찮아짐 -> **자동화**

Google Sheet API X 그냥 웹사이트로 만들자!

django를 이용한 개발을 하게되었다.



##### 버전1 : 정원사들 리스트와 특정 날자의 출석부 확인

##### 버전2 : 전체 출석부를 확인 (코드가 알아서 해주니까! : 출석률 순위, OX 표시)

...



### 2. DSC HUFS : 잔디왕 김훕스

잔디 쉬는날 = 잔디 물주는날

TIL도 commit과 별개로 꼼꼼하게! : 슬랙채널을 팠다.

보증금 : 부담되지 않는 선에서 진행하기 : 하루 1000원 21000 * 3 =63000원

심심하니가 React로 만들어보기! -> 게임 스코어 보드!

slack에 TIL!

정리 - 세부적인 목표를 세우게끔 유도하는 것이 좋다. / 많은 학생들은 누군가 가르쳐주는 것에 익숙해져 있다.

알고리즘 스터디 - 첫주에는 greedy 마스터 하겠다 등등 거리를 던져줌? 뭐할래요? 서로서로 물어보고 목표를 정해줄 수 있도록. 목표가 비슷한 사람들끼리 고나심사를 공유하면 좋을 것 같다.

잡초심기 : README.md 업데이트 - 좋은 커밋이 아니다!



### 3. 크로스 플랫폼 앱 개발기

#### 3-1. 기획 + 디자인

예쁜 디자인을 갖고 기능을 만드는게 아니라

기능을 따라가야한다!!> 기능을 서포트해주는 디자인을 차용해줘야한다.

화면별 가장 중요한 기능 TOP3를 정하고 그에 맞는 디자인을 차용해라

**pinterest : stackoverflow = 디자이너 : 개발자**



초기 기획 디자인 단계에 시간을 많이 투자하자.

좋은 디자인 사례를 참고하자

핵심 기능에 초점을 맞춰서 디자인을 선택하자.



#### 3-2. Flutter

[flutter의 디자인 원리]

모든것은 Widge이라는 컴포넌트로 이루어져있다 : **composition over inheritance**

Text / Padding / decoratedBox / GestureDetector

컴포지션 방식! 위젯들이 재사용이 잘되고 유연해 진다. (익숙해지면 개발 속도도 빨라진다)



[flutter의 동작원리]

모두 canvas로 그려주는 방식이다! : 일일히 그려주는 방식이다

Flutter 공부법 : 

1. 공식 홈페이지 튜토리얼 
2. It's All Widgets (코드 구조를 잘 짜둔 앱을 이용한다.)
3. The Boring Flutter Development Show (Youtube)
4. Flutter 플러그인 개발 (Hardware Buttons)



[개발 결과]

스스로 개발을 하는거여도, issue를 따고 resolve 하는 방식으로 진행하라.



[느낀점]

첫 러닝커브가 높다.

- Dart 언어를 새로 배워야 하는데 조금 특이한 문법들이 있다. 
- Widget을 컴포지션 하면서 진행하는데, Widget이 엄청 많다. 이 위젯중에 어떤게 중요한지 모르니까 위젯에 압도 당할 수 있다.
- 그러나 튜토리얼이 잘되있어서 몇 번 따라하다보면 익숙해진다.
- 고급 기능을 쓸수록 난관 -> stackoverflow가 활성화되어있지 않기 때문이다.
- Flutter로 안정성 : 애매하다 : Flutter + Firebase // stable에 도달하지 못한 plugin이 일어나긴 한다. 



#### 3-3. 배포 및 결과

배포 과정은 간단하다. -> 튜토리얼이 잘되어있다.
IOS 10만원ㅠ 
Privacy policy 필수 제출
마켓 이미지 만들기



### 3. 웹 개발로 알아보는 함수형 패러다임

Springboot + Kotlin + Arrow

Functional programming : no variable, no loop, no side effect



Side Effect를 한쪽으로 보내 많은 코드를 순수하게 만들자

함수형 프로그래밍을 어떻게 적용할 것인가?



**Side effect가 발생하는 부분?** 
Domin의 throw exception 
repository : 사용자가 없거나 그럴 때 throw를 하고 있어서 (throw side effect) / db call 자체 (io side effect)

> 의존되고 있는 다른 컴포넌트에 영향을 줄 수 밖에 없다.



**Side Effect를 보낼 부분 : layer의 바깥 부분**

Entry point라는 외부로 바꾸는게 좋을 텐데 외부세계(Spring Framework에선 X)

**solve : ** side effect를 사용자와 제일 밀접하게 붙어있는 Controller쪽으로 보내자



##### Error Data

예외로 Error 표현하기 : throw 대신, error 값을 return 하면 되긴할텐데,, 둘중에 하나를 표현하는 새로운 타입을 만들어서 리턴하자.

Result interface 안에 두가지 타입 : Error 타입과 Result 타입

Either 타입으로 Error 표현하기.

Left : IllegarArgumentExcpetion

Right : x+y



##### Either 타입 값 바꾸기 : map

```kotlin
fun addOne(x: Int): Either<Throwable, Int> = 
	if (x == 0) Left(RuntimeExcpetion("zero"))
	esle Right(x+1)

val result = addOne(1).map{x -> x+1}
result // Right(3)

var result2 = addOne(0).map(x -> x+1) // left에 map을 해봤자..!
result2 // Left(RuntimeException("zero"))
```



##### Either 타입 연속해서 사용하기

```kotlin
fun addOne(x: Int): Either<Throwable, Int> = 
	if (x == 0) Left(RuntimeExcpetion("zero"))
	esle Right(x+1)

val result = addOne(1).map{x -> x+1}
result // Right(3)

var result2 = result.map{x -> addOne(x)}
result2 // ?? !! Right(Right(3) + 1) ? 이상!

// Right를 한번 더 감싼상태가 될 것이다. 원래는 Int여야하는데 한번더 감싸진 형태가 되버린다.

var result3 = addOne(1).flatMapp{x -> addOne(x).flatMap{y -> addOne(y)}}
result3 // Right(4) // 안에 바인딩 되어있는 값을 한번 풀어서!

val result4 = addOne(1).flatMap{x ->addOne(0).flatMap{y -> addone(y)}}
result4 // Left(RuntimeExcpetion : zero) // Throw 인경우 optional 처리를 그냥 해준다.
```



[UserRepository 부분]

throw 부분에서는 Left 타입으로 주기 / 올바른 return은 Right 타입으로 주기

[UserService 부분]

Map 함수안에서 domain 타입을 바꾸어 주어야지 error가 발생해도, optional 처리가 잘 된다. context는 유지하면서 안에있는 data만 바꾼다. 

[UserController 부분]

실제로 throw를 발생하는 부분이다. Right값이면 그대로! / Left 값이면 throw

[Exception in Domain]

조건에 따라서 Left/Right 처리를 한다. 
변경에 따라서 영향받는 곳이 생기게 된다 > map flatmap을 이용해서 안에있는 데이터를 연쇄적으로 변경 및 작업해주는 작업을 해준다.

변경해줌에 따라서 연쇄적으로 기존의 Domain이 아니라, Either 타입으로 변경된다.

> sysntactic sugar 적용

[IOMongo]

```kotlin
IO<Either<UserException, UserDocument>> = IO{
  try{
   	Right()
  }catch(){
    Left()
  }
}
```

내생각에 Java에서 참고하면 좋을 것 같은 자료.

<https://www.baeldung.com/vavr-either>

eunmin/spring-boot-arrow