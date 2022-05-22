# Section0. 아파치 카프카 기초

https://www.inflearn.com/course/%EC%95%84%ED%8C%8C%EC%B9%98-%EC%B9%B4%ED%94%84%EC%B9%B4-%EC%9E%85%EB%AC%B8#curriculum



### 0. 아파치 카프카 개요 및 설명

Source application / Target application 이 점점 N:M 관계로 늘어날 때, 데이터를 전송하는 라인이 많아진다.
데이터 전송 라인이 많아지면 배포와 장애에 대응하기 어렵다.

데이터를 전송할 때 프로토콜 포맷 파편화가 심해졌다. 유지보수가 어려워진다.
복잡함을 해결하기 위해 linked in에서 kafka를 개발하였고, 현재는 오픈소스이다.

source application과 target application사이의 커플링을 약하게 하기위해 만들어진게 kafka

- Source application : 데이터 전송 (크릭 로그,  결제 로그 적재) : json, tsv, avro etc 여러 포맷을 지원. - producer 
- Target application : 데이터 수신 (로그적제, 로그 처리) - consumer

kafka는 각종 데이터를 담는 topic이라는 개념 : queue라고 생각하면된다.

- Fault tolerent : 고가용성
- Latency : 낮은 지연
- Throughput : 높은 처리량



### 1. 토픽이란?

토픽은 데이터가 들어가는 공간이다. 일반적인 AMQP와는 다르게 동작한다.

카프카에서는 토픽을 여러개 생성할 수 있다. (데이터베이스의 테이블이나, 파일시스템의 폴더와 유사한 성질)
토픽은 이름을 가지게 된다.

kafka producer: topic에 데이터를 전송 / kafka conusmer : topic에서 데이터를 받음

카프카 토픽 내부

- 하나의 토픽은 여러개의 파티션으로 구성될 수 있음.
- 파티션의 번호는 0부터 시작
- queue처럼 파티션 끝에서부터 쌓고, 가져갈 때는 오래된 순서대로 가져간다.
- 컨슈머가 record들을 가져가도 데이터는 삭제되지 않는다.
- 새로운 컨슈머가 붙었을 때 0번부터 가져갈 수 있다. : 즉, 동일 데이터를 N번 가져갈 수 있다.
  - 컨슈머 그룹이 다를경우
  - auto.offset.reset = earliest

파티션이 2개 이상인 경우 : 프로듀서가 데이터를 보낼 때 키를 지정할 수 있다.

- 키가 null이고 기본 파티셔너 사용할 경우 : 라운드 로빈(round robin)으로 한다.
- 키가 있고, 기본 파티셔너 사용할 경우 : 키의 해시(hash)값을 구하고 특정 파티션에 할당

파티션을 늘리는 것은 조심해야한다. : 파티션을 늘릴 수는 있지만 줄일 수는 없다.

파티션을 늘리면 컨슈머 개수를 늘려서 데이터 처리를 분산시킬 수 있다.

파티션 데이터 삭제 타이밍 : 옵션

- log.retention.ms : 최대 record 보존 시간
- log.retention.byte : 최대 record 보존 크기 (byte)



### 2. Broker, Replication, ISR


Replication : 카프카에서 장애가 생겼을 때 가용성을 높이기 위한 중요한 요소이다.

kafka broker : 카프카가 설치되어있는 서버 단위 / 3개이상의 broker를 권장한다.

파티션이 1개, replication 1 topic, brocker 3대 : 3개 중 1대에 1개의 토픽 정보가 저장된다.

replication : 파티션의 복제 / partition의 고가용성을 위해 필요하다.

- replication이 1 이면, 파티션이 1개만 존재한다. / 2이면 원본1개, 복제본 1개
- brocker 개수에 따라 replication개수가 정해진다. 보통은 **brocker >= replication**

**ISR (In Sync Replica) = Leader partition (원본) + Follower partition (복제본)**

**Leader partition** : 프로듀서가 토픽의 파티션에 데이터를 전달할 때 전달 받는 주체.

Producer : **ack 상세옵션** : 고가용성 유지 - partition의 replication과 관련있음 **(0,1,all)**

- 0 : leader partition에 전송 후 return 은 받지 않는다. - leader partition에 데이터가 잘 도착했는지, 데이터 유실은 없는지 등을 확인 할 수 없다. / 속도는 빠르지만 데이터 유실 가능성
- 1 : leader partition에 전송 후, 데이터를 잘 받았는지 return 을 받는다. 대신 나머지 파티션에 잘 복제되었는지 알 수 없다. / 만약 leader partition의 응답을 받은 직후 장애가 난다면, 나머지 파티션에 복제가 되지 않은 상태이므로, 마찬가지로 데이터 유실 가능성이 있다.
- all : 1 옵션에 추가로, follower partiton 복제가 잘 되었는지 응답값을 받는다. Follower partition에도 데이터가 저장됐는지 확인하는 절차를 가진다. 데이터 유실이 없다. 그러나 확인하는 부분이 많아, 속도가 현저히 느리다.

| ack option | leader partition 전송 후 데이터 return 을 받는다. | 파티션 복제 여부까지 확인한다. |
| ---------- | ------------------------------------------------- | ------------------------------ |
| 0          | X                                                 | X                              |
| 1          | O                                                 | X                              |
| all        | O                                                 | O                              |

replication

- 개수가 많아지게되면, 그만큼 브로커의 리소스 사용량이 늘어나게 된다.
- 카프카에 들어오는 데이터, retnetion date (저장시간)을 잘 고려해야한다.
- 3개 이상의 브로커 : replication 3을 추천한다.



### 3. 파티셔너

프로듀서의 파티셔너 : 파티션을 좀더 효과적으로 사용 할 수 있다.

- 프로듀서가 데이터를 보내면 무조껀 파티셔너를 통해서 브로커에 전송된다.
- 데이터를 어떤 파티셔너에 넣을지 결정한다. 레코드에 포함된 메시지 키 혹은 메시지 값에 따라 결정된다.
- 따로 설정되지 않으면 : UniformStickyPartitiloner로 설정

파티셔너는

- 메시지 키가 있는 경우 : 파티셔너의 **해쉬로직** 의해서 hash(메시지 키) = 파티션 번호 로 배분되게 된다.
  - 동일한 키를 가진 레코드는 동일한 해시값을 만든다.
  - 동일한 파티션에 들어간다
  - 순서를 지켜서 데이터를 처리할 수 있다.
- 메시지 키가 없는 경우 : 라운드 로빈으로 파티션에 들어가게 된다. 
  - UniformStickyPartitioner : 프로듀서에서 배치로 모을 수 잇는 최대한의 레코드를 모아서 파티션으로 보낸다.
  - 파티션에 적절히 분배된다.

기본 파티셔너가 아니라, 직접 개발한 파티셔너도 만들 수 있다. (Partitioner interface를 이용하여 커스텀할 수 있다.)

- ex ) VIP 고객을 위해 데이터 처리를 좀더 빠르게 처리 (데이터 처리량을 )
- AMQP 기반 메시징 시스템에서 우선순위 큐를 만드는 것과 비슷하다.



### 4. 컨슈머 랙 (lag)

데이터는 오프셋이라는 숫자가 붙게 된다.

파티션이 1개인 토픽에 프로듀서가 데이터를 넣는 경우, 0부터 숫자가 매겨진다. 

- 프로듀서는 계속 데이터를 넣고 / 컨슈머는 계속 데이터를 가져간다.
- 프로듀서가 데이터를 넣는 속도 > 컨슈머가 가져가는 속도
- Kafka consumer lag : `(프로듀서가 넣은 데이터의 오프셋) - (컨슈머가 가져간 데이터의 오프셋)`
  - 주로 컨슈머의 상태를 볼 때 사용한다.

토픽에 여러 파티션이 존재할 경우, lag은 여러개가 존재할 수 있다
ex ) 컨슈머 그룹이 1개이고, 파티션이 2개인 토픽에서 데이터를 가져간다면 lag은 2개.

**records-lag-max** : 한개의 토픽과 컨슈머 그룹에 대한 lag이 여러개 존재할 수 있을 때. 그 중 높은 숫자의 lag

lag 모니터링 : consumer 성능이 안나오거나, 비정상 동작이 발생할 때

- 프로듀서 오프셋과 컨슈머 오프셋간의 차이이다.
- lag은 여러개가 존재할 수 있다.

