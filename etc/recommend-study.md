## AutoEncoder란?

입력 데이터를 압축시켜 압축시킨 데이터로 축소한 후 다시 확장하여 결과 데이터를 입력 데이터와 동일하도록 만드는 일종의 딥 뉴럴 네트워크 모델

![image](https://user-images.githubusercontent.com/23415251/156225131-9b2f5d3e-989c-46ac-9b11-065bec92aecb.png)

개념: Encoder, Decoder의 구조를 가진 
- input, output이 똑같은 구조
- encoder의 역할
  - 들어온 input에 대해서 feature를 extraction 하는 역할
  - 함축
  - x -> F
- decoder의 역할
  - extract 된 feature들을 통해 input을 다시 복원
  - F -> x

관련 강의
- 오토인코더의 모든 것: https://www.youtube.com/watch?v=o_peo6U7IRM


## AutoRec: Autoencoders Meet Collaborative Filtering

### Abstracdt

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


## 3. Experiments

- Item 기반이 User 기반보다 성능이 높다.
  - item별 평균 평점 수가 더 많기 때문
- user rating의 편차가 크면 user-base method에 대한 예측의 신뢰도가 떨어진다.
- 다른 비교군보다 Item-based AutoRec이 가장 뛰어남

## Deep AutoEncoder: Training Deep AutoEncoders for Collaborative Filtering

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


## Variational Autoencoder for Collaborative Filtering

- Variational Autoencoder를 붙여서 CF를 더 발전시킬 수 있었다는 논문
  - Variational Autoencoder를 조금 이해해야됨
    - AutoEncoder는 Manifold learning
    - VAE는 Generative Model
      - decoder를 학습시키기 위해 encoder를 붙였는데 구조가 AE와 동일

- VAE를 썼더니 잘 된다는 간단한 내용


## Conclusion

- 2015: AutoRec: CF에 기본적인 형태의 AutoRec을 적용한 기초적인 접근
- 2017: Dense repeating같은 알고리즘을 통해 autoencoder의 성능향상을 도모
- 2018: Variational AutoEncoder를 만들어서 CF를 적용






