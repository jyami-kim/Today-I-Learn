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

