### Docker build시 주의점

docker를 빌드할 때 cache에 유의해야한다. 명령어 기준으로 캐싱되어 에러가 의심될 경우엔 아래 명령어를 입력한다.

```shell
docker build --no-cache -t wiing-wiing-backend:0.3 .
```



### Docker run

```shell
docker run wiing-wiing:0.1
```

그냥 돌린다 : 터미널 꺼지면 컨테이너도 내려간다.

```shell
docker run -d wiing-wiing:0.1
```

데몬 환경에서 돌린다



### gunicorn

flask와 함께 사용하는 WAS

```shell
gunicorn --bind 0.0.0.0:5000 app:app # [파일 명]:[실행 main method 명]
```