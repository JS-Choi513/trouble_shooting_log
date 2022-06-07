머신러닝 모델 제작 시 활성화함수
============================
## Activation function 은 모델설계에 있어 중요한 요소

활성화 함수는 순전파 시 각 뉴런의 입력값에 대한 출력값을 어떻게 변환할 것인지결정함 

크게 두가지 관점으로 고를 수 있다
* Hidden layer : 입력으로 들어온 데이터를 다음레이어로 얼마나 잘 전달할 것인가
* Output layer : 설계하고자 하는 모델이 해결하고자 하는 문제가 무엇인가

### Hidden layer 활성화 함수
* ReLU : 출력값을 0 ~ N 사이
* Sigmoid : 출력값을 0 ~ 1 사이 
* Tanh : 출력값을 -1 ~ 1 사이 

보통 일반적으로 ReLU를 많이 사용한다. Sigmoid, tanh는 Gradient vanishing 문제가 존재하지만, RNN에서는 hidden layer에 sigmoid, output layer에 tanh를 사용함. 
> **선택방법**
> * 멀티레이어 퍼셉트론 : ReLU
> * CNN : ReLU
> * RNN : sigmoid 

### Output layer 활성화 함수
모델의 결과를 도출하는 부분이기 때문에, 모델이 해결하고자 하는 문제에 따라 선택해야 한다.

> **선택방법**
> * Linear output인 경우 : 출력값이 실수인 경우 activation function을 적용하지 않음
> * Logistic output인 경우 : 출력값이 0~1 이므로 Sigmoid가 적합
> * Softmax output인 경우 : 벡터의 합이 1, softmax가 적합 
