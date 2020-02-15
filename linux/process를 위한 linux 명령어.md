### Linux

포트가 8080인 프로세스 확인하기

```shell
 lsof -i TCP:8080
```

```shell
netstat -ano | grep 9090
```

PID 1000인 프로세스 실행종료하기

```shell
kill 1000
```

Jar 파일 실행하기

```shell
nohup java -jar [jar_file_name].jar &
```



### window

window 에서 jar파일의 task를 종료하는 방법

```shell
taskkill /F /PID 9104
```



