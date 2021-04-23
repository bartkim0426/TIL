# EKS workshop

[EKS workshop](https://www.eksworkshop.com/010_introduction/)을 진행하면서 정리한 내용.


## Introduction

Kubernetes: container orchestrator
- microservices 등을 사용하다보면 아주 많은 container들을 관리해야됨
- 이 경우 직접 관리하기 어려움


EKS: AWS에서 제공하는 Elastic Kubernetes Service
- k8s를 구성하는 operation resources를 줄이기 위해 대부분을 AWS에서 제공해줌


### Kubernetes nodes

kubernetes cluster에서 만들어진 모든 machine을 `nodes`라고 부름.

두가지 종류의 노드가 있음
- control-plane-node type
  - control plane을 만드는 노드
- worker-node type
  - 실제 application container가 실행되는 노드

### Kubernetes Objects overview

kubernetes objects는 클러스터의 상태를 나타내기 위해 사용되는 entity

kubernetes는 이 object의 '현재 상태'를 'desired state'와 같게 만들기 위해서 작동

다양한 종류의 objects가 있음
- pods
  - 실제 컨테이너를 담고 있는 래퍼
  - pod != container: 한개의 pod이 여러 컨테이너를 담고있을수도 있음
    - 함께 scaling 할것인지에 따라 결정하면 좋음
  - compute, network interface를 공유하는 개념
- Daemonset
  - 워커 노드에서 구현된 pod의 instance
- Deployment
- ReplicaSet
- Job
- Service
  - Maps a fixed IP address to a logical group of pods
- Label
  - key/value pairs. 주로 associating과 filtering에 쓰임

### Architectural overview

![image](https://i.imgur.com/yTX2jMq.png)

#### Control Plane

- control-plane의 `API Server`를 통해서만 접근
  - kubectl이나 Entry point for REST
- api server가 worker의 `kubelet`에 명령을 내림



