# Kubernetes CRD (Custom Resource Definition)

kubernetes api에 자체 리소스를 추가할 수 있음.

CRD를 통해 CLI를 지원하고 persistent storage를 제공하는 CRUD API가 제공됨. object 모델을 만들고 원하는만큼 리소스를 생성/삭제/사용 가능하다.

뿐만 아니라 이를 감시하고 컨트롤하는 컨트롤러를 가질수도 있다. (ArgoCD가 이런 방식으로 작동한다)

## Example

`superhero.yaml`로 `SuperHero` CRD를 만들어보자.

```
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  # spec과 일치해야됨. (<plural>.<group>)
  name: superheros.example.org
spec:
  # REST API에서 사용할 그룹명 (/apis/<group>/<version>)
  group: example.org
  versions:
    - name: v1
      # 활성화 여부.
      served: true
      storage: true
  # 해당 CRD를 사용할 범위. Cluster나 Namespace
  scope: Cluster
  names:
    plural: superheros
    singular: superhero
    # 종류는 보통 camel casses 단수
    kind: SuperHero
    # CLI 리소스와 일치하는 짧은 문자열 사용 가능
    shortNames:
      - hr

```

`antman.yaml`, `hulk.yaml`을 추가하자

```
apiVersion: "example.org/v1"
kind: SuperHero
metadata:
  name: antman
spec:
  superpower: "can shrink"
  size: "tiny"
```

```
apiVersion: "example.org/v1"
kind: SuperHero
metadata:
  name: hulk
spec:
  superpower: "super strong"
  size: "big"
  color: "green"
```

이제 명령어로 실제 CRD를 만들자

```
$ kubectl create -f superheros-crd.yaml
customresourcedefinition.apiextensions.k8s.io/superheros.example.org created

$ kubectl create -f antman.yaml
superhero.example.org/antman created

$ kubectl create -f hulk.yaml
superhero.example.org/hulk created
```

만들어졌는지 확인해보자. 위에서 추가한 shortname인 `hr`로 확인이 가능하다.

```
$ kubectl get superheros
NAME     AGE
antman   35s
hulk     21s

$ kubectl get hr
NAME     AGE
antman   46s
hulk     32s
```

## Comments

- 예시가 아주 흥미로워서 기록해두기는 했지만 실제로 어떻게 사용해야 할 것인지는 명확하게 떠오르지 않는다. 아주 큰 회사나 큰 FOSS에서 이런 방식으로 컨트롤러를 만들어 k8s 리소스를 운영할 수 있을 듯 하다.

