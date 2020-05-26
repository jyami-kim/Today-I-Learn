# tflite 변환

```shell
curl https://storage.googleapis.com/download.tensorflow.org/models/mobilenet_v1_0.50_128_frozen.tgz \
  | tar xzv -C /tmp
```

```shell
tflite_convert --output_file=/tmp/foo.tflite --saved_model_dir=/tmp/saved_model
```

tflite 변환시 1번 명령어를 이용하셔 tflite_convert 모듈을 다운받고, 

2번명령어를 이용하여 output_file, saved_model_dir을 지정해준다.

이때 saved_model_dir에는 pb 파일이 위치해야하며

varialbes 폴더 위치도 함께 있어야한다.

```shell
- /tmp/saved_model
  - saved_model.pb
  - variables
    - variables.data-00000-of-00001
    - variables.index
```

위와같은 구조를 가져야함