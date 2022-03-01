# autoencoder의 모든것

## 2. Manifold learning

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


## 3. Autoencoders

- 입력과 출력이 갖게 하는 구조
