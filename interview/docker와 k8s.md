### 1. ELK 스택

Logstash : 비정형 비정형 로그를 정형화해서 저장할 수 있다. 모든 형태, 크기 소스의 데이터를 수집한다. (실제 앱의 로그를 수집하는)

ElasticSearch : 검색 엔진, logstash로 수집된 로그를 탐색하기 좋다. (query를 )

Kibana : 대시보드 Elastic Search로 url / url.keyword 형식으로 정리된 데이터를 기반으로 시각화한다.

Beats: logstash가 수집하지 못하는 로그를 커버하기 위해 나왔다. 그것이 file 데이터나 (로그파일) metric을 수집한다 : api count, cpu 사용량

필터링은 kibana에서 es에 요청을 보내는 것이고,

ES는 쿼리를 빠르게 찾기쉽게 하는 검색엔진이군ㄴ,,,!!! 개쩜 ES의 인덱싱 구조가 찾기 쉬워서,,ㅇㅂㅇ 대박쓰



### 2. Kibana / Grafana 둘다 모니터링 툴

grafana : 

- metric이라고하는 cpu 사용량과 같이 cluster, pod, container의 상태를 관제할 때 사용
- 현재 상태에 관련된 모니터링이 강점



kibana : 

- accesslog에 대한 분석, 즉 사용자 로그에 대한 분석이 주 되다
- 과거의 데이터를 모아서 통계, 필터링에서 강점



### 3. kubernetes : probe

컨테이너 프로브

- livenessProbe : 컨테이너가 살아있는지 본다. 이게 되어야지만 컨테이너가 살아났다고 본다. 이게 잘못되면 컨테이너를 재시작한다. (httpGet + path 설정으로 해당 요청이 안오면 헬쓰체크가 안되있는 걸로 본다) - kube-probe
- readinessProbe : 컨테이너가 준비가되었는지, 지금 만든 컨테이너가 service할 준비가되었는지를 확인한다.
- startupProbe : 애플리케이션이 시작되었는지를 본다

startupProbe -> livenessProbe -> readinessProbe

현재 내 NELO에는 키바나가 자동으로 probe 체크하는 로그가 쌓여있다.





### DB / Cache

DB : 파일 시스템 기반 DB

- 여기에 저장하는 내용을 실제로 파일에 저장
- 로딩이 오래걸리는 데이터들 (게시글)
- 저장 데이터가 일반적으로 영구적이다

redis DB : 메모리 DB이기 때문에 캐시 용도로 쓰인다 (inmemory DB)

- 파일에 저장이 아니라 메모리에 저장한다.
- 캐싱을 해야하는 데이터들? 캐시나 유저 세션 -> 응답이 잘 되어야 하는 것들 // session데이터를 mysql에 저장하는건 아니다 ()
- Expire time 설정 : 사라질 데이터라고 가정한다.



### Docker

**Container 가상화** 의 종류 중 하나임



### Docker vs VM

![Docker와 VM - 서강혁 - Medium](https://miro.medium.com/max/862/1*wOBkzBpi1Hl9Nr__Jszplg.png)

- Guest OS가 있느냐 없느냐의 차이
- 추가적인 kernel 설치가 필요한지 여부 차이

Guest OS안에 우리가 생각하는 OS와 kernel이 깔려있음..?

바이너리라고 하는 것들이 우리가 알고있는 os의 기능들 : 탐색기 등등 

커널이 없어지면서 사용하는 메모리나 CPU가 줄어든다.

Binary : OS에서 제공하는 ls 같은 것들! (여기서의 app)

container와 virtualization

VM (가상머신) : 하이퍼바이저 기반 - 하드웨어 가상화(vm1이랑 vm2랑 각각 사용할 CPU를 논리적ㅇ르ㅗ 분리한다.)



##### 내 컴퓨터가 Cent OS에서 Ubuntu를 application을 실행하고 싶을 때

- 하이퍼 바이저를 이용하면, Guest OS를 설치하고 그 위에서 app을 실행한다. 그러면 guest OS에 잇는 kernel이 하이퍼바이저에 명령을 전달 하고, 하이퍼바이저가 다시 cent OS의 kernel로 명령어를 전달한다. 그러면 이때 커널,  OS가 두개임!! 많은 리소스를 소모함!

- 이때 하이퍼바이저는 논리적으로 cpu랑 memory자원을 분리하는 역할을 함.

- 반면 도커 엔진은 Guest OS랑 Guest OS의 kernel없이 앱의 명령을 Cent OS의 커널에 전달할 수 있다. 그러면 리소스가 덜든다!

  그이유는 도커엔진이 Ubuntu app의 명령어를 Cent OS의 커널이 이해할 수 있게 바꿔주는 역할을 하기 때문이다!

도커 엔진 : 컨테이너 가상화 기술의 하나인 것...ㅇㅂㅇ...



### Docker image

내가 만들어둔 컨테이너의 상태를 파일로 저장하였음!!



### Helm

원래 k8s배포를 한다하면 여러 Object spec을 만듬. 근데 이들간에 중복되는 변수나 일치해야하는 변수들이 있는데, 이런것들을 관리하기 쉽게 해주고!

쿠버네티스에서 배포할 때 한번에 배포되어야하는 오브젝트 스펙들을 한번에 배포해주는 역할도 한당!

**여러 오프젝트 스펙들을 하나의 패키지로 만들어주는 것이다!**

외에도 dependencies를 걸어서 내chart아래 다른 chart을 참조할 수 있음 ``charts/[압축파일]``



### k8s란

**컨테이너 기반의 오케스트레이션 툴**

컨테이너 - 위에 참조

오케스트레이션 - 구성을 자동화해주고 조정해주고 관리를 해준다.

- 라이프사이클관리 / 오토스케일링 / 로드밸런싱
- 컨테이너가 많아질 수록 관리하기 힘들어지는데 이걸 k8s가 해준다.

오토스케일링이 없을때는 pod를 하나 내가 그냥 추가해줘야함. 지속적인 모니터링을 해서 필요하다고 느낄 때 해야함.
근데 오토스케일링을 지원하면, hpa가 모니터링도 대신하고, 내가 설정한 값을 넘어갈 때 판단을 해서 오토스케일링을 한다.

서비스가 커질수록 컨테이너가 많아지고 관리하기 힘들어지는데, 얘네들의 관리를 편하게해주는게 k8s임.

- 서비스가 커지면 트래픽이 커진다는거고, 로드밸런싱이랑 스케일링으로 (규모가 커진다)
- 로드밸런서 서버가 늘어날수있고,서비스 앞단에 여러개가 붙는것처럼,, (기능 커짐)
- 기능이 커진다 / 규모가 커진다.

**pod이 갑자기 죽어버릴 때의 생명주기!!** : 지맘대로 죽는다 신기하넹,,

팟이 가벼워서 빨리뜨는건 컨테이너의 장점

부하율에 따른 분산처리 - 로드밸런싱 : 라운드로빈...(반반쪼갬)

##### 이렇게 3개가 가능해서 블루그린이 가능해짐

로드밸런싱이 가능하다는건 



### nginx

프록시 : 받은 요청을 다른곳에 넘긴다

##### 리버스 프록시의 역할

내가 만든 서버의 앞단에 붙여서 내 실제 서버의 망을 숨긴다.

Login / application 분리하기 위한 용도
location을 여러게 설정을 해서, path별로 server를 다르게 지정할 수 있게함.

- proxy_pass: 데이터만 전송
- redirect : 클라이언트의 url자체를 바꾼다. (클라이언트의 재접속을 유도)

##### 포워드 프록시

프록시를 통해서 망에 접속한다 (망에 프록시가 붙어있음)

내가 누군지를 숨긴다 (client가 나를 숨길때?)

vpn

##### 하 프록시 (ha proxy : high ability proxy) - 프록시의 기능

로드밸런서



### Nginx vs Apache

apache는 정적 호스팅만 해주는거고,

nginx는 정적 호스팅도 할 수 있다 : 정적파일을 가르키게 할 수 있다. (프록시임, 프록시의 타겟이 서비스가 아니라 파일일뿐)

```shell
location / { # url을 호출한게 다른서비스를 안부르고 파일의 내용을 가져온다.
root /user/share/nginx/html;
index index.html
}
```



### Jenkins CI/CD

jenkins를 CD목적으로 안쓰고있음 : ncc로 사용중 -> ncc에 배포를 요청하게 되었음

원래는 deploy stage를 두고, kubectl create, helm install로 사용을할 수도 있음.

CI는 계속함 : commit이 올라오고 다함!!

- pr / branch에 따라서 기준에 따라서 CI를 함

CD는 어느 브랜치에서 일어나느냐에 따라서 stage에 배포를 할수도 있고, test 서버에 배포를 할수도 있고..!

나대신 코드를 머지해서 테스트



##### CI를 하는 이유

git merge : 코드 comflict만 검증을 해줌 -> 의미적으로는 겹치는 수정부분이 있는지만 확인 (어떤코드가 최신인지만 찾는다.)

> 그러나 이 코드는 테스트가 안된 코드임

협업을통해 코드를 같이 짤 때, merge한 코드에 대한 test여부는 확인을 할 수 없다. : 지속적인 테스트를 위해서

협업을할때 그때그때 테스트를 해야하고 그걸 누군가가 한사람이 맡아서 하기엔 시간적으로 손해고 귀찮은일임.



##### CD를 하는 이유

배포를 하는데 시간이 오래걸리기 때문에

- test서버를 한다는건, 
- 배포는 다운로드, 설치, 실행이 필요함 근데 각각의 작업이 시간이 오래걸림 이걸 기다릴 수 없음.

```shell
ssh
git pull (30분걸림;;) > 다른거 하다 옴 (까먹음ㅋㅋㅋ)
gradle build
netstate kill
nohup java jar sdfkaf.jar &
-----
helm install (30분 걸림): 이미지 다 받아오고 해야함.
```



센트럴 로깅 (central logging : centralized logging)
내가 띄운 모든 서비스의 로그를 한군데서 본다. 이러기 위해서 이걸 쓴다.



### 분산 cluster redis?

여러개의 묶음

- 용량 / 안정성 (하나가 죽으면 안되니까)
- 안정성 == 정전

read는 빠른데 write가 느려질 수 있는 가능성이 있음.

master : read / writer

slave : read

client가 write를 하면, master에서 slave에 write를 해서 sync를 맞춘다.











