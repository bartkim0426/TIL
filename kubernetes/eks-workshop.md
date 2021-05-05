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

## Deploy example microservices

간단하게 kubectl을 사용하여 리소스를 생성/제거하는 부분이라 따로 정리하지는 않음


## Helm

helm is a package manager and application management tool for kubernetes that packages multiple k8s resources into a single logical deployment unit called a "Chart"

아래 일들을 해줌.

- 간단하고 재사용 가능한 deployment
- 특정 버전의 application이나 services 등 dependencies를 관리
- test, staging, production 등 여러 설정 관리
- application 배포중 post/pre deployment job을 실행
- update/rollback 가능

### Deploy nginx with helm

helm 설치 및 repo add, install, uninstall 등 간단한 명령어라 정리하지 않음

### Delpoy example microservices using helm

helm create로 프로젝트를 만들 수 있음. (처름 알았다)

```
foo/
├── .helmignore   # Contains patterns to ignore when packaging Helm charts.
├── Chart.yaml    # Information about your chart
├── values.yaml   # The default values for your templates
├── charts/       # Charts that this chart depends on
└── templates/    # The template files
    └── tests/    # The test files
```

> 더 자세한 내용은 `helm create --help`

새로 생성된 파일들 중 `templates/` 안에 있는 내용은 다음과 같음
- `deployments.yaml`: k8s deployment를 생성하는 basic manifest
- ingress.yaml, serviceaccount.yaml, service.yaml: k8s object
- `_helpers.tpl`: chart에서 재사용하는 helper template
- `NOTES.txt`: help text for your chart. helm install 할때 user에게 노출됨
- `tests/`: chart test (test도 있따니..)

### Upgrade and rollback

upgrade

```
helm list
helm upgrade <chartname>
```

rollback

```
helm status <chartname>
helm history <chartname>

helm rollback <chartname> <revision_number>
```

## Health check

- liveness: fail시 pod를 restart
- readiness: fail시 해당 pod으로 요청 보내지 않음. restart는 안시킴.

## Autoscaling with HPA and CA

- HPA: High Pod Autoscaler
- CA: Cluster Autoscaler


