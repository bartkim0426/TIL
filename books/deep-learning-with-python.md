# deep learning with python

## ch 01. What is deep learning?

### Machine learning이란?

![ai-ML-DL-img](https://learning.oreilly.com/library/view/deep-learning-with/9781617294433/OEBPS/Images/01fig01.jpg)

machine learning이 등장하기 전에도 AI의 개념은 오래전(1950s~)부터 있었다고 함.

ML이 등장하기 이전의 방식 (Symbolic AI)에는 data와 rule을 넣으면 프로그램이 answers를 반환하는 구조였다.

ML의 큰 차이점은 기존처럼 rule을 직접 명시적으로 프로그래밍 하는 것이 아니라 data와 answer를 넣으면 이에 맞는 rule을 반환해주는 것.

이를 위해서 ML에서는 크게 3가지가 필요

- Input data points
- Examples of the expected output
- A way to measure whether the algorithm is doing a good job
  - 해당 알고리즘이 잘 작동하는지를 확인해줄 방식이 필요함.
  - 이 측정이 알고리즘의 동작에 피드백을 줌
  - 이런 피드백을 받아서 조정하는 스텝이 *learning*

ML의 핵심: input data의 useful representations를 학습하여 expected output에 더 가까워지게 하는것

> representations: different way to look at data - to represent or encode data

> Learning, in the context of machine learning, describes an automatic search process for better representations.


모든 ML 알고리즘은 input data를 더 유용한 representations으로 변경하는 transformation 작업을 자동으로 찾는 것들로 구성되어 있다.

![coordinate-change-img](https://learning.oreilly.com/library/view/deep-learning-with/9781617294433/OEBPS/Images/01fig04.jpg)

> 위 사진에서 보듯이 raw input data를 유용한 representations으로 변경하면 색이 다른 좌표를 x축을 기준으로 분류하기가 쉬워진다.


정리하자면 Machine learning은 다음과 같다.

피드백 신호의 가이드를 받아 가능한 미리 정의된 가능한 확률적 공간 내에서 input data의 유용한 representations을 찾는것

> Searching for useful representations of some input data, within a predefined space of possibilities, using guidance from a feedback signal


### Deep learning이란?

deep learning은 machine learning의 subfield이다.

`deep`은 더 깊은 이해도 등을 의미하는 게 아니라 representations의 sucessive layers의 아이디어에 가깝다.

모델에 관여하는 layer가 얼마나 많은지를 모델의 `depth`라고 한다.
현대의 deep learning은 수십개, 많게는 수백개의 representations의 성공적인 layers가 관여된다.

(거의 대부분의) Deep learning에서는 이런 레이어드된 representations은 `neural networks`라는 모델을 통해서 학습됨.

> neural network는 neurobiology에서 나온 말이지만 deep learning이 뇌에서 간략한 영감을 받았기 때문에 사용하는 용어. 하지만 실제로 이런 learning 메카니즘이 실제 뇌의 모델과는 다르다. 생물학과 deep learning 사이의 연결고리는 모두 잊는게 좋음. deep learning은 데이터에서 representations을 학습하는 수학적인 framework이다.

그럼 이 딥러닝 알고리즘을 통해 배우는 representations은 어떤 모양일까? 숫자를 적은 이미지를 숫자로 변환하는 여러 레이러를 살펴보자.

![deep-neural-network-img](https://learning.oreilly.com/library/view/deep-learning-with/9781617294433/OEBPS/Images/01fig05.jpg)

deep network를 정보에서 sucessive filter를 사용하여 불순물을 제거하는(?) 정보 증류 작업의 multistage로 생각하면 된다.

![deep-representations-img](https://learning.oreilly.com/library/view/deep-learning-with/9781617294433/OEBPS/Images/01fig06_alt.jpg)

결국, 기술적으로 deep learning은: data representations를 학습하는 multistage way이다.

> a multistage way to learn data representations.

### Deep learning이 어떻게 동작하는지: 3 figures

**weights**

각 layer가 input data에 하는 역할은 layer의 `weights`에 저장되어있다.

기술적인 접근으로는 layer로 인해서 transformation하는 것은 layer의 weights에 의해서 parameterized 되는 것이다.

이 맥락에서 learning은 example input이 그에 맞는 타겟에 정확히 매핑될 수 있도록 해주는 네트워크의 모든 레이어의 weights 값의 집합을 구하는 것이다.

![weights](https://learning.oreilly.com/library/view/deep-learning-with/9781617294433/OEBPS/Images/01fig07.jpg)

**loss function**

이를 컨트롤 하기 위해서 우선 관찰이 필요하다. output이 얼마나 틀렸는지 (far) 측정할 수 있어야 하는데 이를 network의 `loss function` (`objective function`)이라고 부른다.

**optimizer**

딥러닝의 기본적인 트릭은 `loss function`에서 반환된 점수를 피드백 신호로 사용하여 기존 `weights`을 변경하는 것이다.

이런 조절을 `optimizer`라고 한다. (보통 `Backpropagation algorithm`을 사용한다고함)

![optimizer](https://learning.oreilly.com/library/view/deep-learning-with/9781617294433/OEBPS/Images/01fig09.jpg)


처음에는 랜덤한 `weights` 값으로 시작해서 `loss function`을 거쳐 `optimizer`가 동작하며 점점 결과값에 가까워진다. 

이를 `traning loop`라고 부른다: 충분한 수준으로 아주 많이 반복하여 loss function을 minimize 할 수 있는 수준의 weight를 산출한다.



