# GCS와 Springboot 연동하기

Google Cloud Storage와 Springboot를 연동하여 파일을 업로드 다운로드하는 코드를 짰다.

```java
@Component
@Slf4j
@RequiredArgsConstructor
public class GCPFileIOUtil {

    @Value("${cloud.bucket}")
    private String bucketName;

    private final Storage storage;

    public Blob getFileFromGCS(String objectLocation) {
        Blob blob = storage.get(bucketName, objectLocation);
        log.info("download File From GCS : " + blob.toString());
        return blob;
    }

    @SuppressWarnings("deprecation")
    public String uploadFileToGCS(String localFileLocation) throws IOException {

        String fileName = getFileNameWithOutLocalFolder(localFileLocation);
        log.info("fileName : " + fileName);
        BlobInfo blobInfo =storage.create(
                        BlobInfo.newBuilder(bucketName, fileName) // 폴더 만들 때는 맨 앞에 / 빼고 만들
                                .setContentType("application/pdf")
                                .setAcl(new ArrayList<>(Arrays.asList(Acl.of(Acl.User.ofAllAuthenticatedUsers(), Acl.Role.READER))))
                                .build(),
                        new FileInputStream(localFileLocation));

        log.info("upload File to GCS : " + blobInfo.toString());
//         파일 지우는 로직 필요할 때
//        Files.deleteIfExists(new File(localFileLocation).toPath());
        return blobInfo.toString();
    }


}
```

```java
@Component
public class LocalFileIOUtil {

    @Value("${local.file.storage.folder}")
    private String localDownLoadFolder;

    public String downloadBlobFile(Blob blob, String filePath) throws IOException {
        String localLocation = localDownLoadFolder + filePath;
        ReadChannel readChannel = blob.reader();
        FileOutputStream fileOutputStream = new FileOutputStream(localLocation);
        fileOutputStream.getChannel().transferFrom(readChannel, 0, Long.MAX_VALUE);
        fileOutputStream.close();
        return localLocation;
    }

}
```

```java
@Configuration
public class GCPConfig {

    @Value("${spring.cloud.gcp.project-id}")
    private String projectID;

    @Value("${spring.cloud.gcp.storage.credentials.location}")
    private Resource credentialLocation;

    @Bean
    public Storage injectGCPStorage() throws IOException {
        return StorageOptions.newBuilder()
                .setCredentials(ServiceAccountCredentials.fromStream(credentialLocation.getInputStream()))
                .setProjectId(projectID).build().getService();
    }

}
```

```properties
# application.properties
cloud.bucket=[CUSTOM - my bucket name]
local.file.storage.folder = [CUSTOM - my local saving folder]

spring.cloud.gcp.project-id=[REQUIRED - My GCP project id]
spring.cloud.gcp.storage.credentials.location=classpath:[REQUIRED - MY key.json]
```



상세히 들어가보자면

### 1. GCP Credential 설정 + Storage 세팅

```java
@Bean
    public Storage injectGCPStorage() throws IOException {
        return StorageOptions.newBuilder()
                .setCredentials(ServiceAccountCredentials.fromStream(credentialLocation.getInputStream()))
                .setProjectId(projectID).build().getService();
    }
```

나는 위와 같이 storage 객체를 bean으로 만드는 작업을 했다. 이 작업을 하면서 엄청 애먹었었다.

CredentailLocation이라는 변수는 accesskey.json 파일의 경로를 담고 있다. 따라서 이 변수는 application.properties를 이용해서 숨겨둔 변수이다.

이후 StorageOptions 라는 static 클래스를 이용하여, 사용할 storage의 project id를 지정한다. 이렇게 할 경우, 내가 가진 계정 정보를 담은 storage 변수가 생성된다.



### 2. GCS에서 파일 다운로드

```java
public Blob getFileFromGCS(String objectLocation) {
        Blob blob = storage.get(bucketName, objectLocation);
        log.info("download File From GCS : " + blob.toString());
        return blob;
    }

 public String downloadBlobFile(Blob blob, String filePath) throws IOException {
        String localLocation = localDownLoadFolder + filePath;
        ReadChannel readChannel = blob.reader();
        FileOutputStream fileOutputStream = new FileOutputStream(localLocation);
        fileOutputStream.getChannel().transferFrom(readChannel, 0, Long.MAX_VALUE);
        fileOutputStream.close();
        return localLocation;
    }
```

두 메소드를 활용하면 되는데, 이때 GCS에 있는 파일에 접근하는 과정과, 접근한 파일을 실제 local에 다운로드 하는 과정을 분리하였다. 어떤 버킷(bucketName)에서 어떤 파일(objectLocation)을 가져올지를 설정하고 .get 메소드를 사용하면, 내가 접근하려고 하는 파일이 Blob 변수안에 저장된다.

이후 이 Blob 변수를 fileOutputStream을 이용해서 다운로드 받으면 끝난다.



### 3. GCS에 로컬 파일 업로드

```java
 @SuppressWarnings("deprecation")
    public String uploadFileToGCS(String localFileLocation) throws IOException {

        String fileName = getFileNameWithOutLocalFolder(localFileLocation);
        log.info("fileName : " + fileName);
        BlobInfo blobInfo =storage.create(
                        BlobInfo.newBuilder(bucketName, fileName) // 폴더 만들 때는 맨 앞에 / 빼고 만들
                                .setContentType("application/pdf")
                                .setAcl(new ArrayList<>(Arrays.asList(Acl.of(Acl.User.ofAllAuthenticatedUsers(), Acl.Role.READER))))
                                .build(),
                        new FileInputStream(localFileLocation));

        log.info("upload File to GCS : " + blobInfo.toString());
//         파일 지우는 로직 필요할 때
//        Files.deleteIfExists(new File(localFileLocation).toPath());
        return blobInfo.toString();
    }
```

이 메소드안에 모든 내용이 담겨있다.

먼저, GCS 업로드시 ```/[folderName]/[fileName]``` 이 형식으로 저장하면 안되고 대신 ```[folderName]/[fileName]``` 이 방식으로 저장해야한다. 그래서 또 따로 함수를 빼서 위와같이 작성한 상태이다.

이후 storage.create() 메서드를 사용해서 지정한 파일을 해당 프로젝트의 bucket에 업로드한다.

newBuilder() 메서드에, 내가 지정한 bucket에 해당하는 file을 작성해주고, Acl도 작성해준다. Acl은 해당 파일의 읽기 쓰기 권한을 나타내는 것 같다. 이후 FileInputStream을 이용해서 내가 업로드 하고자 하는 파일을 넣어준다. 

*이때 BlobInfo.builder 메소드가 자체로 stream.close()를 해주기 때문에 걱정하지 말자.*

upload가 완료되면 GCS에서 확인할 수 있으며 추가로 ```blobInfo.getMediaLink();```를 부르게되면, 만약 Acl.User를 allUser로 공개했을 경우에는 url로 다운로드 할 수 있는 링크를 제공받을 수 있다.



[참고 사이트]

<https://cloud.google.com/java/getting-started/using-cloud-storage?hl=ko>

<https://guitaryc.tistory.com/30>

<https://github.com/spring-cloud/spring-cloud-gcp/blob/master/spring-cloud-gcp-samples/spring-cloud-gcp-storage-resource-sample/src/main/java/com/example/WebController.java>

