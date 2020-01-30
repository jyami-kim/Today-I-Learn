# mock server test 구축

먼저 각종 모듈을 build.gradle을 이용해 임포트 해준다.

```groovy
#build.gradle

dependencies {
	testImplementation group: 'org.mock-server', name: 'mockserver-netty', version: '5.6.1'
	testImplementation group: 'org.mock-server', name: 'mockserver-client-java', version: '5.8.1'
}
```



mockserver 설정 방법

@Before를 이용해 테스트 시작전에 mockserver client를 새로 만들어준다.

이때 mockserver client는 실제 서버가 REST API를 호출하면 response를 제공하는 것처럼, test에서도 마찬가지로 특정 request를 호출하면 response를 제공하게 한다.

```java
@Before
public void startServer() {
  mockServer = ClientAndServer.startClientAndServer(7755);

  new MockServerClient("localhost", 7755)
      .when(HttpRequest.request().withMethod("PUT"), 
          Times.exactly(1),
          TimeToLive.exactly(TimeUnit.MINUTES, 1l))
      .respond(HttpResponse.response().withStatusCode(200)
          .withHeaders(new Header("Content-Type", "text/plain")).withBody("OK\n")
          .withDelay(new Delay(TimeUnit.SECONDS, 1)));
}
```

.when() 을 이용해서 mockserver에 들어오는 request를 정의하고

.respond() 를 이용해서 mockserver에 when에서 정의한 request가 들어올 경우 response를 정의한다.





<https://www.programcreek.com/java-api-examples/?class=org.mockserver.integration.ClientAndServer&method=startClientAndServer>

<https://www.baeldung.com/mockserver>





