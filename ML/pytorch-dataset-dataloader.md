# Pytorch dataset dataloader

회사에서 Machine Learning을 사용할 일이 생겨 tensorflow와 pytorch의 튜토리얼을 따라해 보고 있다.

대부분의 튜토리얼은 기존의 dataset을 로드해서 하는 방식인데 새로운 이미지 셋으로 만들어진 모델을 테스트하려고 보니 데이터를 다루는 방식이 달라서 정리해본다.

pytorch에서는 `Dataset`, `Dataloader` 객체를 사용하여 데이터를 다룬다.

### Dataset



