# Item26. 함수 내부의 추상화 레벨을 통일하라

### 추상화레벨

높은레벨 

- 물리장치로부터 멀어진다.
- 프로세서부터 멀어진다
- 단순함을 얻지만, 제어력을 잃는다.



### 추상화 레벨 통일

Single Lvel of Abstraction, SLA : 함수도 높은 레벨과 낮은 레벨을 구분해서 사용해야한다.

- 함수를 계층처럼 나누어서 사용한다.
- 함수를 사용할 때 일부분을 추출해서 추상화하는 것이 좋다.
- 함수는 작아야하며, 최소한의 책임을 가져야한다.



### 프로그램 아키텍처의 추상 레벨

서브시스템의 세부 사항을 숨김으로써 상호 운영성(interoperability)과 플랫폼 독립성을 얻기 위함이다. (문제 중심 프로그래밍)

- 4 : 높은 레벨 문제 중심
- 3 : 낮은 레벨 문제 중심
- 2 : 낮은 레벨 구현 구조
- 1 : 프로그래밍 언어 구조와 도구
- 0 : 운영체제 연산과 머신 명령

계층이 잘 분리된 프로젝트 : 어떤 계층위치에서 코드를 보아도 일관적인 관점을 얻을 수 있다.



