# springboot 외부 라이브러리 jar 추가

로컬 프로젝트안 라이브러리 추가하는 방법

gradle에 존재하지 않는 외부의 .jar 파일을 스프링 부트 안에 의존성을 추가하는 방법이다. /build.gradle 파일 안에 명시를 해주어야 한다.

### 1. 한 개의 파일을 넣는 방법

```groovy
dependencies {
    implementation files('libs/something_local.jar')
}
```



### 2. 디렉토리에 위치한 모든 라이브러리의 의존성을 추가하는 방법

```groovy
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
}
```



### 3. 로컬 디렉토리를 라이브러리로 만들어서 의존성을 추가하는 방법

여기서도 마찬가지로 libs/something_local.jar 파일을 추가하는 방법이다.

```groovy
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
}

dependencies {
	compile( name: 'something_local')
}
```



### 4. 외부라이브러리 추가시 충돌

```shell
java.lang.NoSuchMethodError: 'com.google.api.client.http.HttpRequest com.google.api.client.http.HttpRequest.setResponseReturnRawInputStream(boolean)'
	at com.google.api.client.googleapis.services.AbstractGoogleClientRequest.buildHttpRequest(AbstractGoogleClientRequest.java:434) ~[google-api-client-1.30.4.jar:1.30.4]
```

외부 라이브러리로 .jar 파일을 추가하면서 정말 골치를 썩었던 문제였다.

분명, 내가 build.gradle에 추가한 dependency는 가장 상위버전이고, NoSuchMethodError 와 관련한 글을 구글링 해보았을 때 하위 버전에서 발생하는 문제이니 상위버전으로 version을 업데이트 하라는 해결을 준다.

그런데 intellij 상으로 아래 메세지를 보면 1.30.4 버전으로 가장 상위버전으로 표시가 된다.

이때 의심해야할 것은 **외부 라이브러리에서도 똑같은 dependency를 사용하는데 외부 라이브러리의 버전이 낮을 경우**이다. 

이 경우에 classPath에 혼동이 생겨서 일어난 에러로 보인다. 내가 build.gradle에 추가한대로 현재 프로젝트의 classpath를 따라야하는데, 외부 라이브러리인 jar 파일의 classpath와의 충돌 때문인 것 같다.

**Solution :** 나는 이경우에 외부 라이브러리인 jar 파일을 압축 해제후, 해당 모듈을 지우고 다시 jar로 압축하는 방식을 사용했다.