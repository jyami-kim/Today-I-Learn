

### 2. VPC, 서브넷

VPC 공유 : 여러 AWS 계정에서 EC2, RDS Redshift 클러스터, AWS Lambda 함수 같은 애플리케이션을 중앙 관리 형 공유 Vertual Private Cloud(VPC)로 생성할 수 있다.

VPC 피어링 : 두 VPC 간에 트래픽을 라우팅 할 수 있게 하기 위한 두 VPC 사이의 네트워킹 연결. 동일 네트워크에 속하는 경우 인스턴스가 서로 통신할 수 있다.

### 3. S3

Kinesis : 실시간 스트리밍 데이터를 손쉽게 수집, 처리 분석 할 수 있음. 모든 규모의 스트리밍 데이터를 비용 효율적으로 처리할 수 있음/

Amazon SNS : 분산 발행-구독 시스템 - 메시지는 게시자가 SNS로 보낼때 처럼 가입자에게 push 된다.
Amazon SQS : 분산 큐잉 시스템 : 메시지 수신자에게 푸시되지 않는다. 수신자는 SQS에서 메시지를 폴링하거나 폴해야한다. 수신자 한명이 메시지를 수신하여 처리하고 삭제한다 (다른 수신자는 나중에 동일한 메시지를 받지 않는다.)

### 4. 리전 서버 연결

Application Load Balancer의 이점

- 경로 조건 지원 : URL을 기반으로 요청을 전달하는 리스너에 대한 규칙을 구성
- 호스트 조건 지원 : HTTP 헤더의 호스트 필드를 기반으로 요청을 전달하는 리스너에 대한 규칙을 구성
- HTTP 헤더 조건, 메서드, 쿼리 파라미터, 소스 IP 주소 등 요청의 필드를 기반으로 하는 라우팅

https://docs.aws.amazon.com/ko_kr/elasticloadbalancing/latest/application/introduction.html

Amazon Route 53

- **지리 위치 라우팅 정책**- 사용자의 위치에 기반하여 트래픽을 라우팅하려는 경우에 사용합니다.
- **가중치 기반 라우팅 정책**— 사용자가 지정하는 비율에 따라 여러 리소스로 트래픽을 라우팅하는 데 사용합니다.

https://docs.aws.amazon.com/ko_kr/Route53/latest/DeveloperGuide/routing-policy.html

### 5. 액세스 규칙 평가

네트워크 ACL 규칙

- 규칙 번호 : 번호가 가장 낮은 규칙부터 평가. 규칙에 일치하는 트래픽이 있으면 모순되는 상위 규칙이 있어도 적용 (발견되면 즉시 실행)
- 유평 : 트래픽 유형 - 모든 트래픽 or 사용자 지정 범위 지정 가능
- 프로토콜 지정 가능
- 포트 범위 지정 가능
- 소스 [인바운드 규칙만] : 트래픽의 소스 범위
- 대상 [아웃바운드 규칙만] : 트래픽의 대상
- 허용/거부 : 지정된 트래픽을 허용할지 거부할지

https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/vpc-network-acls.html#nacl-basics

### 6. 트래픽 분산

https://docs.aws.amazon.com/ko_kr/Route53/latest/DeveloperGuide/routing-policy.html

- **지연 시간 라우팅 정책**— 여러 자원이 있을 때 사용합니다.AWS리전과 왕복 시간이 가장 적합한 리전으로 트래픽을 라우팅하려는 경우

> - AWS DataSync : AWS DataSync는 온프레미스 스토리지 시스템과 AWS 스토리지 서비스 간, 그리고 여러 AWS 스토리지 서비스 간의 데이터 이동을 간소화, 자동화 및 가속화하는 온라인 데이터 전송 서비스입니다. // 데이터를 전송하는 서비스
>
> - Network Load Balancer : 오픈 시스템 상호 연결(OSI) 모델의 네 번째 계층에서 작동합니다. 초당 수백만 개의 요청을 처리 // 로드 밸런서가 정상적인 대상에만 요청을 보낼 수 있도록 함. hash 알고리즘으로 대상 선택
>
>   https://docs.aws.amazon.com/ko_kr/elasticloadbalancing/latest/network/introduction.html

### 7. Kinesis 

Amazon Kinesis Data Streams 은 데이터 스트림의 데이터 레코드 보존 기간에 대한 변경을 지원

기본적으로 레코드를 24시간, 최대 365일 저장한다.

https://docs.aws.amazon.com/ko_kr/streams/latest/dev/kinesis-extended-retention.html

### 8. 온라인 데이터 전송 자동화 가속화

- AWS DataSync : 온프레미스 스토리지 시스템과 AWS 스토리지 서비스 간 및 AWS 스토리지 서비스 간 및 AWS 스토리지 서비스 간 데이터 이동을 단순화, 자동화 및 가속화하는 온라인 데이터 전송 서비스

  - NFS (Network File System)
  - SMB (Server Message Block) 파일 서버
  - 자체 관리 개체 스토리지
  - Amazon Simple Storage Service (Amazon S3) 버킷
  - 파일 시스템
  - Amazon FSx for Windows File Server파일 시스템

  https://docs.aws.amazon.com/ko_kr/datasync/latest/userguide/what-is-datasync.html

- AWS Snowball Edge : 로컬 프로세싱 및 에지 컴퓨팅 워크로드를 수행하고 로컬 환경과 AWS 클라우드 간에 데이터를 전송할 수 있습니다. - Snowball Edge Device 를 사용하면 되며 이때 여러 구성 옵션이 있다 (스토리지 최적화, 컴퓨팅 최적화, GPU를 통한 컴퓨팅 최적화)

  https://docs.aws.amazon.com/ko_kr/snowball/latest/developer-guide/whatisedge.html

- File Gateway : 로컬 캐싱을 이용해 SMB, NFS 파일데이터 저장 및 액세스 // 백업파일 저장

  지연시 사용한다.
  AWS S3에 객체로 저장한다음. EC2에서 NFS, SMB를 통해 이런 파일에 액세스하거나, 직접 파일을 객체로 액세스할 수 있도록 지원하는 파일 인터페이스를 제공함

  AWS Storage Gateway 유형중 하나이다. 

  https://aws.amazon.com/ko/storagegateway/features/

- AWS Transfer Family : AWS S3나 AWS EFS에서 파일을 직접 송수신 할 수 있는 완전 관리형 지원을 제공

  Amazon Route 53를 사용해 DNS 라우팅을 제공함. 파일 전송 워크플로우를 AWS로 원활하게 마이그레이션 할 수 있다.

  - Secure File Transfer Protocol(SFTP)
  - File Transfer Protocol over SSL(FTPS)
  - File Transfer Protocol(FTP)

### 9. 보안그룹 구성법

*Q: SSH 연결을 통해 특정 IP 주소(110.238.98.71)에서만 온디맨드 EC2 인스턴스에 액세스할 수 있는지 확인해야 합니다*

A: 보안그룹 인바운드 / 프로토콜-TCP / 포트범위-22 / 소스 110.238.98.71

### 10. 두번째 인증 방법 추가할 때

Cognito : 웹 및 모바일 앱에 대한 인증, 권한 부여 및 사용자 관리를 제공/  사용자는 사용자 이름과 암호를 사용하여 직접 로그인하거나 Facebook, Amazon, Google 또는 Apple 같은 타사를 통해 로그인할 수 있습니다.
https://docs.aws.amazon.com/ko_kr/cognito/latest/developerguide/what-is-amazon-cognito.html

- IAM : 자격증명 - 사용자, 사용자 그룹, 역할 지정
  https://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/access_policies.html

- Amazon SNS Mobile Push : 모바일 push를 보낸다 (FCM, GCM)
  https://docs.aws.amazon.com/ko_kr/sns/latest/dg/sns-mobile-application-as-subscriber.html

- MFA (멀티 팩터 인증) : 다른 인증 방법을 추가해 사용자 이름과 암호에만 의존하지 않게하여 앱의 보안 강화. 두번째 팩터로 SMS 문자나 일회용 암호 (TOTP) 선택 가능하다.

  https://docs.aws.amazon.com/ko_kr/cognito/latest/developerguide/user-pool-settings-mfa.html

### 11. 온프레미스 => AWS

온프레미스 인프라 : 소프트웨어 등 솔루션을 클라우드 같이 원격 환경이 아닌 자체적으로 보유한 전산실 서버에 직접 설치해 운영하는 방식

- VPC(Virtual Private Cloud) Peering : 프라이빗 IPv4 주소 또는 IPv6 주소를 사용하여 두 VPC 간에 트래픽을 라우팅할 수 있도록 하기 위한 두 VPC 사이의 네트워킹 연결

  https://docs.aws.amazon.com/ko_kr/vpc/latest/peering/what-is-vpc-peering.html

- Ipsec VPN connection : VPN 연결 옵션 중 하나 - VPC와 원격 네트워크 사이에 IPsec VPN 연결을 생성할 수 있습니다 : 온프레미스 인프라와 AWS 클라우드 연결 가능 (AWS 클라이언트 VPN)

  https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html

- NAT(네트워크 주소 변환) Gateway : 프라이빗 서브넷의 인스턴스를 인터넷 또는 기타 AWS에 연결/ 인터넷에서 해당 인스턴스와의 연결을 시작하지 못하게 할 수 있음

  https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/vpc-nat-gateway.html

- AWS Direct Connect : 온프레미스에서 AWS로 전용 네트워크 연결을 쉽게 설정할 수 있는 클라우드 서비스 솔루션/ 데이터 센터, 사무실 등의 환경에서 AWS 리전으로 직접 비즈니스 데이터 전송이나 AWS에서 직접 전송이 가능함.
  https://aws.amazon.com/ko/directconnect/features/?nc=sn&loc=2&whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc

- Amazon Connect : 클라우드 콜센터

### 12. RDS

다중 AZ 배포 : 자동으로 하나의 기본 DB 인스턴스를 생성하고, 동시에 다른 가용영역(AZ)의 예비 인스턴스에 데이터를 복제함.

장애가 발생해도 RDS가 예비 인스턴스로 자동 장애조치를 수행함 : CNAME이 예비 복제본을 가리키도록 변경된다.

https://aws.amazon.com/ko/rds/features/multi-az/

### 13. CloudFront

Lambda@Edge 통합하여 사용자와 지리적으로 가까운 곳에 데이터를 처리하고 낮은 지연시간에 사용자 요청에 응답이 가능함

이미지 같은 스트림데이터 리전별 캐싱을 할 수 있음.

CloudFront with Route 53

- 컴퓨팅 기능이 없기 때문에 Route 53을 사용하여 트래픽만 라우팅 할 수 있어 둘 다 잘못되었습니다. 이 솔루션은 Lambda @ Edge를 사용하지 않기 때문에 사용자와 가까운 지리적으로 데이터를 처리하고 반환할 수 없습니다.

클릭 스트림 분석을 못해서 컴퓨팅 기능이 없다고함

### 14. API Gateway

API Gateway에 제한 설정 및 결과 캐싱을 사용하도록 설정합니다

https://aws.amazon.com/ko/api-gateway/faqs/

### 15. EC2 Autoscaling

https://docs.aws.amazon.com/ko_kr/autoscaling/ec2/userguide/AutoScalingGroup.html

https://docs.aws.amazon.com/ko_kr/autoscaling/ec2/userguide/as-scaling-target-tracking.html

https://docs.aws.amazon.com/ko_kr/autoscaling/ec2/userguide/as-scaling-simple-step.html

- 단순 조정 정책, 단계 조정 정책 :  // 50%넘으면 인스턴스 그룹 N개 추가하고 빼라를 해두는 것
- 단순 조정 정책 : 한번 50%까지 차서 사용자가 알람 받아서 스케일 아웃을 할 수 있을지 아닐지. 휴지 기간이 만료될 때까지
- 단계 조정정책 : 일시 단계 조정이 적용된다. 용량을 늘리거나 줄이는 경우 경보 위반의 크기에 따라 달라짐
- 대상 조정 정책 : 50% 넘으면 알아서 조정하라

### 16. 라우팅 구성

가중치 라우팅 정책을 사용해 액티브-액티브 장애 조치를 구성함

장애 

- 액티브-액티브 장애 조치

모든 리소스를 대부분의 시간 동안 사용 가능하도록 하려면 이 장애 조치 구성을 사용하십시오. 리소스를 사용할 수 없을 때는 Route 53이 비정상 상태임을 판별하여 쿼리에 응답할 때 그 리소스를 포함하지 않습니다.

액티브-액티브 장애 조치에서 동일한 이름, 동일한 유형(예: A 또는 AAAA) 및 동일한 라우팅 정책(예: 가중치 또는 지연 시간)를 보유한 모든 레코드는 Route 53이 이를 비정상으로 간주하지 않는 이상 활성 상태입니다. Route 53은 정상 레코드를 사용하여 DNS 쿼리에 응답할 수 있습니다.

- 액티브-패시브 장애 조치

기본 리소스 또는 리소스 그룹이 대부분의 시간 동안 사용 가능하도록 하고 보조 리소스 또는 리소스 그룹은 기본 리소스가 사용 불가능할 경우를 대비해 대기 중에 있도록 하고 싶다면 이 장애 조치 구성을 사용하십시오. 쿼리에 응답할 때 Route 53은 정상적인 1차 리소스만을 포함합니다. 모든 1차 리소스가 비정상이라면, Route 53은 DNS 쿼리에 응답할 때 정상적인 2차 리소스만을 포함시키기 시작합니다.

>  장애 조치 라우팅 정책일 때는 액티브-패시브 장애조치를 구성할 수 있음.

### 17. S3

CloudTrail : 데이터에 대한 로깅은 모르고 설정값 히스토리를 알 수 있는 것

### 18. EC2 상태확인 실패

ALB, ELB : 타겟 그룹에 속하면 타겟 그룹이 있는데, 인스턴스 하나의 응답이 이상하면 타겟 그룹에서 제거하는 방식으로 동작한다.

### 19. CloudFormation

https://docs.aws.amazon.com/ko_kr/AWSCloudFormation/latest/UserGuide/template-anatomy.html

### 20. 온프레미스 공유 책임 모델 AWS

물리적 네트워크 인프라를 제외하 ㄴ나머지는 고객책임

### 21. Amazon MySQL RDS

- 다중 AZ : 동기 복제 - 최소 두개의 가용 영역을 포함
- 읽기 전용 : 비동기 복제 - 가용 영역, 교차 AZ 교차 리전에 있을 수 있음

### 22. ELB 문제 해결 기능

https://docs.aws.amazon.com/ko_kr/elasticloadbalancing/latest/classic/config-conn-drain.html

- 연결 드레이닝

로드 밸런서가 인스턴스의 등록 취소를 보고하기 전에 연결을 유지할 수 있는 최대 시간을 지정할 수 있습니다. 최대 제한 시간 값의 범위는 1 ~ 3,600초입니다(기본은 300초). 최대 제한 시간에 도달하면 로드 밸런서는 등록 취소 중인 인스턴스로의 연결을 강제로 종료합니다.

https://docs.aws.amazon.com/ko_kr/elasticloadbalancing/latest/userguide/how-elastic-load-balancing-works.html

### 23. 외부 인스턴스 연결

모든리소스 : 0.0.0.0/0 : /가 0이라서 모든 옥텟을 사용할 수 있으니까

https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/VPC_Route_Tables.html

### 24. 고가용성

ELB는 리전단위로 할 수 없음.

고가용성 : 리전을 의미하는게 아님. // 서울 - 울산으로 나누는 것. : 리전단위를 할 때는 리전단위를 무졲너 들어감

### 25. EC2 인스턴스 연결 순서

1. 퍼블릿 서브넷과 인터넷 게이트 웨이가 있는 VPC 설정
2. 퍼블릭 IP로 EC2 인스턴스 설정
3. 라우팅 테이블에 인터넷 게이트웨이 (IGW)에 대한 올바른 라우팅 항목이 구성되어있는지 확인

### 26. RDS

RDS 를 IP 기반으로 연결하지 않고 도메인 기반으로 연결함 CNAME 기반

그래서 죽었을 때 IP 기반으로 변경하는게 아니라 CNAME 기반으로 변경하는 것임

### 27. EBS / IOPS

IOPS 성능 : 일정한 IO 속도를 유지하는 것

EBS 볼륨 : 인스턴스와는 독립적이다.

### 28. AWS 비용

https://aws.amazon.com/ko/premiumsupport/technology/trusted-advisor/

리소스 프로비저닝에 대한 실시간 지침을 제공하는 시스템 들어가면 AWS Trusted Advisor

### 29. 엣지 로케이션

엣지 로케이션은 전 세계 어디서나 모든 고객에게 애플리케이션의 고가용성, 확장성 및 성능을 제공 할 수 있도록 도와줍니다. 이는 Lambda 및 Amazon CloudFront와 같은 다른 서비스에서 사용됩니다.



보안그룹 (security group / sg)

### 30. 