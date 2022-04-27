# AutoEncoder란?

입력 데이터를 압축시켜 압축시킨 데이터로 축소한 후 다시 확장하여 결과 데이터를 입력 데이터와 동일하도록 만드는 모델

manifold learning 중 하나

- Autoencoder의 중요한 특성 하나는 manifold learning을 쓰는것
- manifold: 데이터 차원을 줄일 때 쓰임 (x차원 -> 2D)

### Dimension reduction을 하는 이유?
  - 1. data compression (압축)
  - 2. data visualization
  - 3. curse of dimensionality
    - 차원이 증가할수록 공간의 크기(부피)가 증가하기 때문에 필요한 샘플 데이터의 개수가 기하급수적으로 증가
    - manifold hypothesis
      - 고차원의 데이터는 밀도가 낮지만, 이 집합을 포함하는 저차원의 매니폴드가 있다는 가정
      - 이 저차원의 매니폴드를 벗어나는 순간 밀도가 급격히 낮아짐
  - 4. discovering most important features
    - 중요한 피쳐를 찾는 목적으로

### Dimension reduction 방법들

autoencoder만 있는것은 아니고 다양하게 있음

Linear한 방식
- PCA, LDA ...

Non-Linear한 방식
- Autoencoder

그럼 autoencoder랑 뭐가 다른지?
- 기존의 방법들: neighborhoods based training
- 고차원 데이터간의 유클리디안 거리는 유의미한 거리 개념이 아닐 가능성이 높음

![image](https://user-images.githubusercontent.com/23415251/156154895-48574d4a-204b-4b24-8337-1a0ee77247e5.png)

개념: Encoder, Decoder의 구조를 가짐
- input, output이 똑같은 구조 (입/출력)
- encoder의 역할
  - 들어온 input에 대해서 feature를 extraction 하는 역할
  - 함축
  - x -> F
- decoder의 역할
  - extract 된 feature들을 통해 input을 다시 복원
  - F -> x

# AutoRec: Autoencoders Meet Collaborative Filtering

### Abstract

- AutoRec은 collaborative filtering를 위한 새로운 autoencoder framework
- 이전 state-of-the-art CF 모델들보다 movielens, netflix 데이터셋에 더 좋은 성능

### 1. Introduction
- autoencoder 패러다임에 기반한 AutoRec을 CF에 사용하고자 제안
- 이를 어떻게 쓸 수 있을것인가? vision, speech 분야에서 성공한 neural network가 추천 시스템에서도 잘 적용될 것이라고 영감을 얻어서 적용
- representation, computation 모두 장점이 있음

### 2. AucoRec model

#### user, item, matrix

- m users: r(u) vector
- n item: r(i) vector
- matrix: 부분적으로 관찰된 user-item rating matrix

#### work aim
- 부분적으로 관찰된 벡터를 입력받아서 저차원 잠재 공간에 project 시킬 수 있는 오토인코더를 설계

#### MF와 비교
- MF는 linear representatino. AutoRec은 non-linear
- MF는 user, item 모두 latent space에 두고 AutoRec은 item만 둠


### 3. Experiments

- Item 기반이 User 기반보다 성능이 높다.
  - item별 평균 평점 수가 더 많기 때문
- user rating의 편차가 크면 user-base method에 대한 예측의 신뢰도가 떨어진다.
- 다른 비교군보다 Item-based AutoRec이 가장 뛰어남
# Deep AutoEncoder: Training Deep AutoEncoders for Collaborative Filtering

### Abstract
- AutoEncoder는 deep할수록 일반화 성능이 우수
- Negative부분을 포함한 non-linear activation function은 학습에 매우 중요

### Model

![image](https://user-images.githubusercontent.com/23415251/156227007-20f99a9d-e395-4adf-92dc-9e65d23d59ab.png)

- AutoRec과 거의 흡사
  - 조금 더 deep하게 쌓음

- Dense re-feeding
  - 처음에는 sparse를 feeding하고, 이 결과로 나온 dense를 다시 feeding
  - 1. sparse한 x로 f(x), loss를 구함 (forward pass)
  - 2. gradient, weight를 update (backward pass)
  - 3. f(x)로 dense matrix를 구함 (second forward pass)
  - 4. gradient로 weight를 다시 update (second backward pass)


### Experiment

![image](https://user-images.githubusercontent.com/23415251/156228122-0db3e474-3499-4eb6-82b0-2f3ce186126e.png)

sparse한 x를 dense하게 만들어서 re-feeding한게 효과가 있음을 검증


# Variational Autoencoder for Collaborative Filtering

- Variational Autoencoder를 붙여서 CF를 더 발전시킬 수 있었다는 논문
  - Variational Autoencoder를 조금 이해해야됨
    - AutoEncoder는 Manifold learning
    - VAE는 Generative Model
      - decoder를 학습시키기 위해 encoder를 붙였는데 구조가 AE와 동일

- VAE를 썼더니 잘 된다는 간단한 내용

## VAE?

- AutoEncoder의 목적은 Manifold Learning
  - AE는 네트워크의 앞단을 학습하기 위해 뒷단을 붙인 것
  - 입력 데이터의 압축을 통해 데이터의 의미있는 manifold를 학습
- Variational AutoEncoder는 Generative Model
  - Generative model이란 새로운 datainstance를 생성해내는 모델
  - 뒷단(Decoder, 생성)을 학습시키기 위해 앞단을 붙인 것
  - 그 구조를 보니 결과론적으로 AE와 같음


## Conclusion

- 2015: AutoRec: CF에 기본적인 형태의 AutoRec을 적용한 기초적인 접근
- 2017: Dense repeating같은 알고리즘을 통해 autoencoder의 성능향상을 도모
- 2018: Variational AutoEncoder를 만들어서 CF를 적용


## 참고
- 오토인코더의 모든 것: https://www.youtube.com/watch?v=o_peo6U7IRM
