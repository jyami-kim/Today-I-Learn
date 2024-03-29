# 10장 일괄처리

`

### 유닉스 도구로 일괄 처리하기



#### 단순 로그 분석

다양한 도구로 로그를 처리하고 웹사이트 트래픽에 관한 보고서를 깔끔하게 만들 수 있다.

유닉스 파이프라인 예제

```
cat /var/log/nginx/access.log | awk 'print $7' | sort | uniq -c | sort -r -n | head -n 5
```



#### 연쇄 명령 대 맞춤형 프로그램

루비는 URL 해시테이블을 메모리에 유지한다. 그러나 유닉스 파이프라인 예제에는 이런 해시 테이블이 없다.

```
counts = Hash.new(0)
File.open('var/log/nginx/access.log') do |file|
	file.each do |line|
		url = line.split[6]
		counts[url] += 1
	end
end

top5 = counts.map{|url, count| [count, url]}.sort.reverse[0...5]
top5.each{|count, url| puts "#{count} #{url}"}
```



#### 정렬 대 인메모리 집계

URL의 양에 따라 어떤 접근법이 좋을지가 다르다.

CNU Coreutils(리눅스)에 포함된 sort 유틸리티는 메모리보다 큰 데이터셋을 자동으로 디스크로 보내고 자동으로 여러 CPU 코어에서 병렬로 정렬한다



#### 유닉스 철학

연쇄 명령을 사용해 쉽게 로그파일을 분석할 수 있는게 유닉스의 핵심 설꼐 아이디처였다.

"다른 방법으로 데이터 처리가 필요할 때 정원 호스와 같이 여러 다른 프로그램을 연결하는 방법이 필요하다. 이것은 IO 방식이기도 하다"

1. 프로그램이 한 가지 일만 하도록 작성하라
2. 모든 프로그램의 출력은 아직 알려지지 않은 다른 프로그램의 입력으로 쓰일 수 있다고 생각하라
3. 소프트웨어를 바르게 서볼 수 있게 설계하고 구축하라
4. 프로그래밍 작업을 줄이려면 미숙한 도움보단 도구를 사용하라



### 동일 인터페이스

어떤 프로그램의 출력을 다른 프로그램의 입력으로 쓰고자 한다면 이들 프로그램은 같은 데이터형식을 써야한다. 즉, 호환 가능한 인터페이스를 써야한다

유닉스의 인터페이스 : 파일(파일디스크립터)

파일 : 단지 순서대로 정렬된 바이트의 연속

같은 인터페이스로 파일 시스템의 실제 파일, 통신채널, 장치 드라이버, TCP 연결을 나타내는 소켓 등 여러가지를 표현할 수 있다.

**유닉스의 동일 인터페이스는 여전히 대단하다.**
도ㅇ일한 데이터 모델인 데이터베이스 간에도 한쪽에서 다른 쪽으로 데이터를 옮기는게 쉽지않다.



#### 로직과 연결의 분리

특징1 : 표준 입력과 표준 출력을 사용한다.

느슨한 결합(loose coupling) 지연 바인딩(late binding) 제어 반전(inversion of control) : 프로그램은 입력이 어디서부터 들어오는지 출력이 어디로 나가는지 신경쓰거나 알 필요조차 없다



#### 투명성과 실험

유닉스 도구가 성공중인 이유 : 진행 사항 파악이 쉽다

유닉스 도구 사용에 가장 큰 제약 : 단일 장비에서만 실행된다 (하둡 같은 도구가 필요한 이유)



### 맵 리듀스와 분산 파일 시스템

맵 리듀스는 유닉스 도구와 마찬가지로 상당히 불친절하고 무차별 대입 방법이지만 대신 엄청나게 효율적인 도구다 (단일 맵 리듀스 작업은 단일 유닉스 프로세스와 유사하다)

- 유닉스 도구 :  stdin, stdout을 입출력으로 사용함
  - 맵리듀스 작업 : 분산 파일 시스템상의 파일을 입출력으로 사용함. HDFS(Hadoop Distributed File System)

HDFS : 개념적으로는 매우 큰 하나의 파일 시스템이고 데몬이 실행중인 모든 장비의 디스크를 사용할 수 있다.

- 네임노드(NameNode) : 중앙 서버 : 특정 파일 블록이 어떤 장비에 저장됐는지 추적
- 확장성이 뛰어나다. (수만대의 장비를 묶어서사용)

#### 

#### 맵리듀스 작업 실행하기

1. 입력파일을 읽어 레코드로 쪼갠다. 레코드 분리자로 \n을 사용한다
2. 매퍼함수를 호출해 키와 값을 추출한다 **(맵)**
3. 키를 기준으로 키-값 쌍 정렬
4. 정렬된 키-값 쌍을 대상으로 리듀스 함수를 호출한다. **(리듀스)**

맵과 리듀스는 사용자가 직접 작성한 데이터 처리 코드이다.



#### 맵리듀스의 분산실행

맵리듀스가 병렬로 수행하는 코드를 직접 작성하지 않고도 여러 장비에서 동시에 처리가 가능하다.

분산 연산에서 매퍼와 리듀서로 표준 유닉스 도구를 사용하는 것도 가능하다 > 하지만 대개 일반적인 프로그래밍 언어로 함수를 작성한다.



**데이터 가까이에서 연산하기** : 네트워크를 통해 입력파일을 복사하는 부담과 네트워크 부하가 감소하고 지역성이 증가한다.

> 각 매퍼 입력 파일의 복제본이 있는 장비에 RAM CPU 여유가 충분하면 맵리듀스 스케줄러가 입력 파일이 있는 장비에서 작업을 수행하려한다.

맵 태스크 수는 입력 파일의 블록 수로 결정되지만 리듀스 태스크 수는 사용자가 설정한다.

파이션을 매퍼의 로컬 디스크에 정렬된 파일로 기록한다 >.SS 테이블, LSM 트리 에서 설명한 기술과 유사하다



리듀서를 기준으로 파티셔닝하고 정렬한 뒤 매퍼로부터 데이터 파티션을 복사하는 과정을 셔플(suffle)이라 한다.

연결된 맵 리듀스 작업 : 각 명령의 출력을 임시 파일에 쓰고 다음 명령이 그 임시 파일로부터 입력을 읽는 방식에 가깝다



#### 리듀스 사이드 조인과 그룹화

맵리듀스는 적어도 일반적으로 이야기하는 색인 혹은 조인 개념이 없다.

전체 테이블 스캔(full table scan) : 맵리듀스 작업은 입력 파일 전체 내용을 읽음



#### 사용자 활동 이벤트 분석 예제

조인을 가장 간단하게 : 하나씩 활동 이벤트를 훑으면서 나오는 모든 사용자 ID 마다 원격 서버에 잇는 사용자 DB에 질의를 보낸다. (과부하 걸리기 쉽다)

일괄처리에서 처리량을 높이기 위해서 가능한 한 장비내에서 연산을 수행한다

사용자 데이터베이스의 사본을 가져와 사용자 활동 이벤트 로그가 저장된 분산 파일 시스템에 넣는 방법



#### 정렬 병합 조인

매퍼 : 입력 레코드로부터 키와 값을 추출하는 것이 목적이다

리듀서 : 특정 사용자 ID의 모든 레코드를 한번에 처리함. 따라서 한번에 사용자 한명의 레코드만 메모리에 유지하면되고 네트워크로 아무 요청도 보낼 필요가 없다.

정렬 병합 조인(sort-merge join) 



#### 같은 곳으로 연관된 데이터 가져오기

필요한 데이터를 사전에 줄을 세워서, 처리량을 높게 유지하면서도 메모리 부담을 줄일 수 있다.

매퍼와 리듀서가 다른 머신에 존재할 수 있음 : 메시지 전송 방식? : 프로그램이 해줌

> 맴 리듀스 프로그래밍 모델은 올바른 장비로 데이터를 모으는 연산의 물리적 네트워크 통신 측면과 받은 데이터를 처리하는 애플리케이션 로직을 분리한다.



#### 그룹화

집계 연산 : 그룹화와 조인의 구현은 상당히 유사하다

특정 사용자가 취한 일련의 활동을 찾기위해 사용자 세션별 활동이벤트를 수집 분석할 때도 일반적으로 그룹화를 사용함 **(세션화)**



#### 쏠림 다루기

불균형환 활성 데이터베이스 레코드를 린치핀 객체 또는 **핫 키**라한다.

> 쏠린 파티션을 한번더 파티셔닝 하여 쏠림을 제어함



#### 맵 사이드 조인

매핑할 때 조인을 하는 방법 : 데이터를 시작부터 모아두고 조인을 하니까 맵사이드 조인





#### 브로드캐스트 해시조인

- 브로드캐스트 : 큰 입력의 파티션 하나를 담당하는 각 매퍼는 작은 입력 전체를 읽는 것
- 해시 : 해시테이블을 사용한다



