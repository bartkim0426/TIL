# recommend study

https://velog.io/@yst3147/Autorec-Recsys

## AutoEncoder란?

입력 데이터를 압축시켜 압축시킨 데이터로 축소한 후 다시 확장하여 결과 데이터를 입력 데이터와 동일하도록 만드는 일종의 딥 뉴럴 네트워크 모델

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

