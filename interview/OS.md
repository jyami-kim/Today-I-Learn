### 1. Process vs Thread

Process : 메모리에 올라와 실행되고 있는 프로그램

각 프로세스는 Code Data Stack Heap구조에 메모리영역을 할당한다.

Thread: 프로세스 내에서 실행되는 작업단위를 말한다.

한 프로세스에 N개 Thread가 있을 수 있으며, Thread N개가 프로세스내에서 같이 실행이 된다.

서로 Code Data Heap 영역을 공유 // stack 은 thread마다 갖고있음.

JavaThread : JVM에 의해 관리된다. 우선순위, 메모리 위치, 상태, 개수 등을 관리



### 2. MultiProcess vs MultiThread

장점 : Code Data Heap 부분이 서로 공유되어 자원을 아낄 수 있다. // 스레드간 통신비용이 더 적다.(응답 시간 빠르다.)

문제 : 동기화 문제가 -> Thread Safe 하게 await 등의 메소드를 사용하여  Thread 사이의 작업 순서를 조절한다.



### 3. Thread-safe

### 4. 프로세스 동기화

### 5. 데드락

### 6. 사용자 수준 















