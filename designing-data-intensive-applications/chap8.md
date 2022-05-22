# 8. 분산 시스템의 골칫거리



부분장애(partial failure) : 예측할 수 없는 방식. 비 결정적



### 클라우드 컴퓨팅과 슈퍼컴퓨팅

- 슈퍼 컴퓨터 : 고성능 컴퓨팅 (high-performance computing, HPC)
- 클라우드 컴퓨팅 : 분산 시스템. 네트워크를 연결해서 동작한다.

클라우드 컴퓨팅과 같은 분산시스템은 결함이 생길것이고, 소프트웨어는 어떤식으로든 이걸 처리해야한다



### 신뢰성 없는 네트워크

분산시스템 = 비공유 시스템 = 네트워크로 연결된 다수의 장비

비동기 패킷 네트워크 (asynchronous packet network) : 네트워크는 메시지가 언제 도착할지 알 수 없다.

다른 노드로 요청을 보내서 응답을 받지 못했을 때 이유를 아는게 불가능하다. > **타임아웃**)



### 현실의 네트워크 결합

네트워크 결함이 드물더라도 일어날 수 있고, 이를 처리할 수 있어야한다.

반드시 네트워크 결함을 견뎌내도록(tolerating) 처리할 필요는 없다.



### 결함 감지

네트워크에 관한 불확실성 때문에 노드가 동작중인지 아닌지 구별하기 어렵다.

=> 타임아웃이 만료되기를 기다렸다가 판단한다.



### 타임아웃과 기약없는 지역

타임아웃만이 결함을 감지하는 수단이라면 타임아웃의 시간은? > 답이없다.

- 2d+r t시간내에 응답을 받는다고 보장을 할 수 있다.
- 기약없는 지연 (unbounded deploy) : 패킷을 가능한 한 빨리 보내려고하지만 패킷이 도착하는 데 걸리는 시간에 상한치는 없다



### 네트워크 혼잡과 큐 대기

고정된 타임아웃을 설정하는 대신, 타임아웃을 자동으로 조절하게 하는것



### 동기 네트워크 대 비동기 네트워크

패킷 전송 지연 시간의 최대치가 고정되어있고, 패킷을 유실하지 않는 네트워크에 기댈 수 있다면 문제가 간단해진다.

네트워크 = 회선(circuit)이 만들어진다 = 통화에 대해 고정되고 보장된 양의 대역폭이 할당된다. > **동기식이다**



인터넷을 사용하는 경우 파일크기가 들쭉날쭉하다 (가변크기의 데이터블록) : 



### 단조 시계 일 기준시계

#### 일 기준 시계

어떤 달력에 따라 현재 날짜와 시간을 반환한다. 직관적으로 시계에 기대하는 일을 한다 



#### 단조 시계

두 값 사이의 차이로 시간의 흐름을 보는 것이다. 시계의 절대적인 값은 의미가 없다.



#### 시계 동기화와 정확도

단조 시계는 동기화가 필요없지만 일 기준시계는 NTP 서버나 다른 외부 시간 출처에 있어야 좋다.



### 이벤트 순서화용 타임스탬프

충돌 해소 전략 : 최종 쓰기 승리 (last write wins, LWW) 

최근 의 정의 : 로컬 일 기준 시계에 의존하며, 그 시계는 틀릴 수도 있다는걸 아는게 중요하다.

논리적 시계 (Logical clock) : 증가하는 카운터를 기반으로 하여 이벤트 순서화의 안전한 대안. 이벤트의 상대적인 순서만 측적한다. 

일 기준 시계와 단조 시계 = 물리적 시계 (piysical clock)



#### 시계 읽기는 신뢰 구간이 있다.

시계읽기는 어떤 신뢰 구간에 속하는 시간의 범위로 읽는게 낫다.



### 프로세스 중단

시계를 위험하게 사용하는 다른 예

- 리더만 쓰기를 받아들이도록 허용한다.



#### 응답 시간 보장

중단의 원인을 제거할 수 있다.

시계 문제 때문에 세밀한 시간 보장이 필요한경우 : 실시간 운영체제 (real-teim operating system. RTOS) : 시간이 중요한



#### 가비지 컬렉션의 영향을 제한하기

GC 과정에서 지연이 있을 수 있기 때문에, 멈추거나 특정 시간에만 발생하도록 한다.



### 지식, 진실, 그리고 거짓말

분산시스템 : 공유 메모리가 없고 지연 변동이 큰 신뢰할 수 없는 네트워크를 통해 메시지를 보낼 수 있을 뿐 > 부분장애, 신뢰성 없는 시계, 프로세스 중단에 시달릴 수 있다.



#### 진실은 다수결로 결정된다

분산 알고리즘은 정족수(quorum), 즉, 노드들 사이의 투표에 의존한다.



#### 펜싱 토큰

**펜싱 토큰** : 임차권을 승인할 대마다 해당 펜싱토큰을 반환한다. (오랫동안 중단되면 임차권이 만료된다.)



#### 비잔틴 결함

펜싱 토큰 : 부주의에 의한 오류에 빠진 노드를 감지하고 차단할 수 있다.

비잔틴 결함 (Byzntine fault)

이책에서는 비잔틴 결함이 없다고 가정한다 : 노드가 정직하다고 가정을 한다.



#### 시스템 모델과 현실

시스템 모델(system modle)을 정의해서 정형화한다 - 알고리즘이 가정하는 것을 기술한 추상화



#### 알고리즘의 정확성

알고리즘이 정확하다  = 알고리즘의 속성(property)을 기술 할 수 있다.

- 유일성 : 펜싱 토큰 요청이 같은 값을 반환하지 않는다
- 단조 일련변호
- 가용성 : 펜싱 토큰을 요청하고 죽지 않는 노드에는 결국에는 응답을 받는다.



#### 안전성과 활동성

안정성(safety) : 유일성, 단조 일련변호 = 나쁜 일은 일어나지 않는다 = 속성이 깨진 특정 시점을 가리킬 수 있다. 이 속성이 위반되면, 이 위반을 취소할 수 없다.

활동성(liveness) : 가용성 = 좋은 일은 결국 일어난다. = 어떤 시점을 정하지 못할 수 있지만, 항상 미래에 그 속성을 만족시킬거라는 희망이 있다.

분산 알고리즘 = 시스템 모델의 모든 상황에서 안전성 속성이 항상 만족되기를 요구하는 게 일반적이다. / 활동성 속성에 대해서는 경고를 하는 게 허용된다.






