# Kustomize

원래 helm만 사용하다가 GitOps를 도입 할 일이 있어서 flux를 보던 중 kustomize에 대해 알게 되었다.

[kustomize](https://kustomize.io/) is kubernetes native configuration management.


For the first time, I was very confused between [helm](helm.sh/) and kustomize.

## Helm vs Kustomize

They have different roles while seem pretty similar.

Helm is package manager for kubernetes. Templating via helm is so powerful but it's helm's secondary purpose.

Kustomize is a templating utility.


kustomize는 kubernetes object를 kustomization 파일을 통해 커스터마이징 하는 tool.

k8s 1.14부터 kubectl에서 kustomization file로 k8s object를 관리하는게 가능해짐.

```
kubectl kustomize <kustomization_directory>
```

## Install kustomize cli

mac os라면 brew로 설치하는게 좋다. 그 이외에도 go source나 docker image로 사용할 수 있음.

```
brew install kustomize
```

> kubectl에서도 kustomize 명령어를 사용할 수 있기 때문에 굳이 설치하지 않아도 될듯?

## Usage

### 1. make a kustomization file

k8s resource yaml 파일이 있는 directory에 `kustomization.yaml` 생성

```
~/someApp
├── deployment.yaml
├── kustomization.yaml
└── service.yaml
```

customized YAML 을 생성하려면 `kustomize build` 명령어를 사용하면 된다.

```
kustomize build ~/someApp
```

1.14부터 kubectl을 통해서도 관리가 가능함.

```
kubectl kustomize <kustomization_directory>
```

`-k`(`--kustomize`) flag와 함께 바로 apply도 가능하다.

```
kubectl apply -k <kustomization_directory>
```

### 2. Create variants using overlays

dev, stging, production 등의 환경을 관리하려면 `base`를 덮어 씌워주는 `overlays`를 사용 가능하다.

```
~/someApp
├── base
│   ├── deployment.yaml
│   ├── kustomization.yaml
│   └── service.yaml
└── overlays
    ├── development
    │   ├── cpu_count.yaml
    │   ├── kustomization.yaml
    │   └── replica_count.yaml
    └── production
        ├── cpu_count.yaml
        ├── kustomization.yaml
        └── replica_count.yaml
```



