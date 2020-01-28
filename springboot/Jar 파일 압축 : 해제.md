# Jar 파일 압축 / 해제

jar 파일을 라이브러리로 만드려는 과정에서, 
jar 파일안에 사용되는 라이브러리 gson, slf4j 두개가 내가 사용하는 springboot dependency와 충돌이 나는 상황이 있었다. 

그래서 처음에 해당하는 모듈 두개를 아래 방식으로 삭제하려고 했다.

```groovy
dependencies {
  compile (files('libs/something_local.jar')){
    exclude group: 'org.slf4j';
  }
}
```

그런데 적용이 되지 않는 방법이라서 결국 포기하고 외부 라이브러리인 jar 파일을 압축 해제하고 duplicate를 일으키는 모듈 그룹을 삭제하기로 결정했다.



### 압축 풀기

shell 열기 > jar 파일이 위치하는 directory로 이동하기

jar파일의 이름이 example.jar 라고 하자.

```shell
jar xvf [파일명].jar
jar xvf example.jar
```



### 압축 하기

shell 열기 > jar 파일이 위치하는 directory로 이동하기

압축하려는 directory 명이 dir 이고 현재 디렉토리에 압축된 파일을 넣으며, 압축 파일의 이름을 example.jar 라고 하자. 

```shell
jar cvf [파일명].jar -C dir/ /
```
