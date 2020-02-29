# mac에서 git 커맨드 alias로 편하게 쓰기

맨날치는 git command ```git add .``` ```git commit``` ```git status``` 치다가 너무 익숙해서 막쓰다보면 오타도 나고ㅋㅋ

그래서 alias로 별명설정을 해줬다!

```shell
vim ~/.bash_profile
```

```shell
alias gc='git commit'
alias gal='git add .'
alias gst='git status'
```

bash 설정을 새로 해줘야하므로

```shell
source ~/.bash_profile
```

