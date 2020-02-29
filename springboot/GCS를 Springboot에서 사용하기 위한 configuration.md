# GCS를 Springboot에서 사용하기 위한 configuration

나는 아래와 같이 storage 옵션 빌더를 이용해서, application.properties에서 주입한 json.key를 그대로 넣어주었다.

```java
@Configuration
public class GCPConfig {

    @Value("${spring.cloud.gcp.storage.credentials.location}")
    private Resource credentialLocation;

    @Bean
    public Storage injectGCPStorage() throws IOException {
        return StorageOptions.newBuilder()
                .setCredentials(ServiceAccountCredentials.fromStream(credentialLocation.getInputStream())).build().getService();
    }

}
```

하지만 찾아보니, spring.cloud.gcp.storage.credientials.location을 지정한 값은 자동으로 Configuration에 등록됨을 알게되었다.

즉, 위와 같이 내가 Storage를 springboot bean으로 등록하지 않아도, 내가 application.properties에서 지정한 값으로 자동적으로 bean 등록이 된다는 뜻이다.



spring cloud Core 모듈에서

```java
public interface GcpProjectIdProvider {
	String getProjectId();
}
```

```java
public interface CredentialsProvider {
  Credentials getCredentials() throws IOException;
}
```

이 두 인터페이스를 이용해서 application properties에 있는 값이 자동으로 주입됨을 알았는데, 이를 사용하는 방법을 찾아보려고 java doc을 살펴보다

<https://docs.spring.io/spring-cloud-gcp/docs/1.0.0.RELEASE/api/org/springframework/cloud/gcp/core/class-use/GcpProjectIdProvider.html#org.springframework.cloud.gcp.autoconfigure.storage>

<https://docs.spring.io/spring-cloud-gcp/docs/1.0.0.RELEASE/api/org/springframework/cloud/gcp/autoconfigure/storage/GcpStorageAutoConfiguration.html>

```java
@Configuration
 @ConditionalOnClass(value={GoogleStorageProtocolResolverSettings.class,com.google.cloud.storage.Storage.class})
 @EnableConfigurationProperties(value={GcpProperties.class,GcpStorageProperties.class})
 @Import(value=GoogleStorageProtocolResolver.class)
public class GcpStorageAutoConfiguration
```

GCPSTorageAutoConfiguration이라는 클래스가 spring storage 모듈내에 존재하여 자동으로 bean으로 등록됨을 알게 되면서 깨닫게 되었다.



추가로 위에 내가 스스로 storage 값을 빈으로 등록하는 방법을 찾아보면서 공식문서를 꼼꼼히 보다가. 대부분의 코드가 아래로 되어있어서 추가한다.

```
StorageOptions.getDefaultInstance().getService();
```

이 방법은 내 local pc의 GOOGLE_APPLICATION_CREDENTIALS 환경변수를 직접적으로 지정한 경우에 사용할 수 있는데, 내가 지정한 instance에 있는 storage 찾아오는 경우이다. 
내 pc에서 default로 설정한 instance 환경변수를 변경함에 따라서 storage option이 달라지기 때문에 되도록 사용하지 말자.

나같은 경우도 내가 gsutil에 설정한 default project로 의존성이 들어옴을 확인 할 수 있었다.