텐서보드 Profile사용법 
============================
##  실행환경 
* Ubuntu 16.04 kernel 4.15
* python3.5
* tensorflow 2.3.0 
* Keras 2.4.3
```{.shell}
$ python3 -c 'import tensorflow as tf; print(tf.__version__)
$ python3 -c 'import keras; print(keras.__version__)
```
* CUDA 10.1
* cUDNN 7.6
```{.shell}
$ nvidia-smi
```
## 텐서보드 사용
필자의 경우에는 텐서플로를 사용한 학습모델이 하드웨어를 어떻게 사용하는지 확인하기 위해 텐서보드를 접하게 되었다. 

텐서보드의 Profile 기능을 사용하면 CPU, GPU 사용패턴과 어디서 병목현상이 발생하는지 알수 있다고 한다. [공식문서](https://www.tensorflow.org/tensorboard/tensorboard_profiling_keras?hl=ko) 

## Quick start
필는 텐서플로 기반 케라스를 사용하고 있기 때문에  파이썬으로 작성한 코드에 몇줄 추가하면 된다.

코드 상에서 모델 컴파일하는 부분과 훈련(fit) 하는 부분 사이에 아래 코드를 작성했다. 
```{.python}
import datetime
...
#model.compile...
...

# Tensorboard setting
log_dir = "/media/js/test/log/" + datetime.datetime.now().strftime("%Y%m%d-%H%M%S")
tensorboard_callback = tf.keras.callbacks.TensorBoard(log_dir=log_dir, histogram_freq=1,
							profile_batch = '500,520')
...
#model.fit...
							
```
위 코드는 텐서보드가 읽을 로그파일 이름과 경로를 지정하는 부분이다. 경로는 코드가 실행되는 위치에서 log라는 디렉터리를 생성하여 **절대경로**로 지정했다. (#Tensorboard setting 부분 코드만 작성하면 된다. )
 **절.대.경.로** 로 지정하는것이 중요하다.
 다음은```datetime``` 라이브러리를 사용하여 생성될 로그파일의 이름을 지정했다.  실행시점의 날짜가 로그파일의 이름으로 지정된다.  
 나머지도 그대로 따라 작성하면 된다. 자세한 기능은 위 공식문서 링크를 참조할 것. 
 
 그리고 모델 훈련 부분코드에 파라미터 하나만 추가하면 끝이다.
```{.python}
history = model.fit_generator(
    train_gen, 
    steps_per_epoch  = step_per_train, 
    validation_data  = val_gen,
    validation_steps = step_per_val,
    class_weight = class_weights_index,
    use_multiprocessing = True,
    epochs = 1,
    callbacks=[tensorboard_callback]
```
마지막에 ```callbacks=[tensorboard_callback])``` 만 추가해주면 된다.  

그다음에 훈련한번 돌리면 위에서 지정한 경로에 로그파일이 생성된다. 

이렇게 

![](https://user-images.githubusercontent.com/32115744/115707603-f929d180-a3a9-11eb-8df4-e52ace1c3873.png) 

필자의 경우 train, validation 으로 코드를 구성했기 때문에 2개가 생성되었다.

텐서보드 실행은 터미널에서 명령한줄만 입력하면 된다. 
```{.shell}
$ tensorboard --logdir=/media/js/test/log/20210422-174828
```
물론 본인이 지정한 경로로 바꿔서 실행하면 된다. 
그러면 이렇게 출력되는데 
![](https://user-images.githubusercontent.com/32115744/115708879-802b7980-a3ab-11eb-8fcf-f562b759c1f2.png) 

여기서 브라우저를 실행한 다음 ```http://localhost:6006/``` 으로 접속하면 텐서보드를 확인할 수 있다. 

![](https://user-images.githubusercontent.com/32115744/115709195-e0222000-a3ab-11eb-8bf8-20abdb768bfa.png) 
## 문제점
필자의 경우에는 텐서보드를 사용하려고 했던 이유가 Profile 기능인데 이부분이 실행되지 않았다. 

다행히도 플러그인이 설치되지 않아서 설치하라고 텐서보드가 친절하게 알려주었다. 
![](https://user-images.githubusercontent.com/32115744/115709479-3abb7c00-a3ac-11eb-807f-6826218295db.png) 

그래서 ```pip install  -U tensorboard-plugin-profile``` 커맨드로 간단하게 플러그인을 설치하고 실행했으나....
```
I tensorflow/stream_executor/platform/default/dso_loader.cc:48] Successfully opened dynamic library libcudart.so.10.1
E0422 17:55:15.225736 140676461836032 application.py:132] Failed to load plugin ProfilePluginLoader.load; ignoring it.
Traceback (most recent call last):
  File "/usr/local/lib/python3.5/dist-packages/tensorboard/backend/application.py", line 127, in TensorBoardWSGIApp
    plugin = loader.load(context)
  File "/home/js/.local/lib/python3.5/site-packages/tensorboard_plugin_profile/profile_plugin_loader.py", line 71, in load
    from tensorboard_plugin_profile import profile_plugin
  File "/home/js/.local/lib/python3.5/site-packages/tensorboard_plugin_profile/profile_plugin.py", line 35, in <module>
    from tensorboard_plugin_profile.convert import raw_to_tool_data as convert
  File "/home/js/.local/lib/python3.5/site-packages/tensorboard_plugin_profile/convert/raw_to_tool_data.py", line 33, in <module>
    from tensorboard_plugin_profile.convert import tf_data_stats_proto_to_gviz
  File "/home/js/.local/lib/python3.5/site-packages/tensorboard_plugin_profile/convert/tf_data_stats_proto_to_gviz.py", line 36
    int(iterator_stat.start_time_ps / 1000_000),
                                             ^
SyntaxError: invalid syntax
Serving TensorBoard on localhost; to expose to the network, use a proxy or pass --bind_all
TensorBoard 2.4.1 at http://localhost:6006/ (Press CTRL+C to quit)
```
방금설치한 플러그인 코드에서 syntax error 가 발생해서 로드할수 없다고 한다. 
## 해결방법
낮은 버전의 플러그인을 찾아서 설치하니 해결되었다.  [여기](https://pypi.org/project/tensorboard-plugin-profile/2.2.0/#history)에서 버전 정보를 확인할 수 있었는데 2.2.0버전을 설치하니까 해결되었다. 

#### * 기존플러그인 삭제

```python3 -m pip uninstall tensorboard-plugin-profile```

#### * 2.2.0버전 설치

```pip install tensorboard-plugin-profile==2.2.0```

#### * 결과
![](https://user-images.githubusercontent.com/32115744/115711549-9a1a8b80-a3ae-11eb-90e6-55bbd1e90151.png) 

잘 되더라. 


 
