# Chapter1. 신뢰할 수 있고 확장 가능하며 유지보수하기 쉬운 애플리케이션

오늘날 애플리케이션은 계산 중심(compute-intensive)과는 다르게 데이터 중심(data-intensive)덧리가.

데이터 양, 데이터 복잡도, 데이터 변화속도가 좀더 문제가 되게 되었다 (CPU 성능보다는)

데이터 중심 애플리케이션은 공통으로 필요로 하는 기능을 제공하는 표준 구성 요소(standard building block)으로 만든다.



### 데이터 시스템에 대한 생각

**데이터 시스템** 이라는 포괄정 용어로 묶어야 하는이유

1. 새로운 도구들은 다양한 사용사례(use case)에 최적화되었기 때문에 더이상 전통적인 분류에 들어맞지 않는다,
2. 점점 더 많은 애플리케이션이 단일 도구로는 더이상 데이터 처리와 저장 모두를 만족시킬수 없어서 : 작업(work)는 단일 도구에서 효율적으로 수행할 수 잇는 태스크(task)로 나누고, 다양한 도구들은 애플리케이션 코드를 이용해 서로 연결한다.

개발자는 점차 데이터 시스템 설계자가 되어간다.



소프트웨어 시스템에서 중요하게 여기는 세가지 관심사

- 신뢰성 (reliability)
- 확장성 (scalability)
- 유지보수성 (maintainability)



### 신뢰성(reliability)

무언가가 잘못되더라도 지속적으로 올바르게 동작함

- 결함(fault) : 잘못될 수 있는 일 - 사양에서 벗어난 시스템의 한 구성요소
- 내결함성(fault-tolerant) / 탄력성(resilient) : 결함을 예측하고 대처할 수 있는 시스템
- 장애(failiure) : 사용자에게 필요한 서비스를 제공하지 못하고 시스템 전체가 멈춘 경우

카오스 몽키 (Chaos Monkey) : 고의적으로 결함을 유도함으로써 내결함성 시스템을 지속적으로 훈련하고 테스트해서 결함이 자연적으로 발생했을 대 올바르게 처리할 수 있다.

 

#### 하드웨어 결함

중복(redundancy) : 구성요소 하나가 죽으면 고장 난 구성 요소가 교체되는 동안 중복된 구성 요소를 대신 사용할 수 있다.

단일 장비 신뢰성보다 유연성(flexibility) 탄력성(elasticity)을 우선적으로 처리한다.



#### 소프트웨어 오류

하드웨어 결함은 무작위적이고 서로 독립적이다.

다른 부류의 결함으로 시스템 내 체계적 오류(systematic error)가 있다. 예상이 어렵고, 노드간 상관관계 때문에 하드웨어 결함보다 오히려 시스템 오류를 더욱 많이 유발할 수 있다.



#### 인적 오류

운영자의 설정 오류가 중단의 주요 원인.



### 확장성(scalability)

증가한 부하에 대처하는 시스템 능력



#### 부하 기술하기

부하 매개변수(load parameter)

-  웹서버의 초당 요청 수
- 데이터베이스의 읽기 대 쓰기 비율
- 대화방의 동시 활성 사용자
- 캐시 적중률

트위터 트윗시 홈 타임라인 구현 예시 > 팬아웃 현상



#### 성능 기술하기

부하가 증가할 대 

- 부하 매개변수를 증가시키고 시스템 자원은 변경하지 않고 유지하면 시스템 성능이 어떤 영향을 받을까
- 부하 매개변수를 증가시켰을 대 성능이 변하지 않고 유지되길 원한다면 얼마나 자원을?



- 처리량(throughtput) : 초당 처리할 수 있는 레코드 수나 일정 크기의 데이터 집합으로 작업을 수행할 대 걸리는 전체 시간
- 서비스 응답시간(response time) : 클라이언트가 요청을 보내고 응답을 받는 사이의 시간 (클라이언트 관점)
- 지연시간(latency) : 요청이 처리되기를 기다리는 시간, 서비스를 기다리며 휴지 상태인 시간

일반적으로는 평균보다 백분위(percentile)를 사용하는 편이 더 좋다.

50p (중앙 값, 50분위), p95 (95분위), p99 (99분위), p999 (99.9분위)



꼬리 지연 시간(tail latency) : 상위 백분위 응답시간 - 고객중에서 계정에 가장 많은 데이터를 갖고있음 (소중한 고객)

백분위는 서비스 수준 목표(service level objective, SLO) 서비스 수준 협약서(service level agreement, SLA)에 자주 사용한다.

선두 차단(head-of-line blocking) : 소수의 느린 요청 처리만으로도 후속 요청 처리가 지체된다.



#### 부하 대응 접근 방식

- 용량 확장(scaling up) : 수직 확장 (vertical scaling) : 좀 더 강력한 장비로 이동
- 규모 확장(scaling out) 수평 확장 (horizontal scaling) : 다수의 낮은 사양 장비에 부하를 분산 > 비공유(shared-nothing) 아키텍처

탄력적(elastic) 시스템 : 부하 증가를 감지하면 컴퓨팅 자원을 자동으로 추가할 수 있음

분산 시스템을 위한 도구와 추상화가 좋아지면서, 수평 확장이 좀더 대세인듯하다.

대개 시스템 아키텍처는 해당 시스템을 사용하는 애플리케이션에 특화되어 잇어 범용적이고 모든 상황에 맞는 확장 아키텍처는 없다. > 그러나 보통 익숙한 패턴으로 나열된 범용적인 구성요소로 구축한다.

아키텍처 결정 요소 : 읽기 양, 쓰기 양, 저장 데이터양, 데이터 복잡도, 응답시간 요구하상, 접근 패턴 등



### 유지보수성 (maintainability)

레거시 시스템 유지보수 작업 > 레거시 소프트웨어를 직접 만들지 않게끔 소프트웨어 설계를 할 수 있어야한다.

- 운용성(operability) : 운영팀이 시스템을 원활하게 운영할 수 있게 쉽게 만들어라
- 단순성(simplicity) : 시스템에서 복잡도를 최대한 제거해 새로운 엔지니어가 시스템을 이해하기 쉽게 만들어라
- 발전성(eveolvability) : 엔지니어가 이후에 시스템을 쉽게 변경할 수 있게하라



#### 운용성 : 운영의 편리함 만들기

좋은 운영성 : 동일하게 반복되는 태스크를 쉽게 수행하게끔 만들어 운영팀이 고부가가치 활동에 노력을 집중한다



#### 단순성: 복잡도 관리
