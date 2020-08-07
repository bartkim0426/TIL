# 03. Pods: running containers in Kubernetes

## Introducing pods

pod is a co-located group of containers and represents the basic building block in Kubernetes

container를 직접 띄우는 대신에 pod of containers로 배포하게 된다.

항상 하나의 node 위에서만 뜸 (ex. 두 개의 노드에서 한 pod가 같이 뜰수 없음)

### 왜 pod가 필요할까?

왜 pod가 필요할까? 왜 container를 직접 사용하면 안될까? 왜 multiple container를 같이 띄워야할까?

만약 같은 machine 안에서 multiple process를 띄워야 한다고 생각해보자. container는 하나에 한 개의 프로세스만 띄우도록 설계되었기 때문에 여러 프로세스를 띄우면 이를 관리하는건 온전히 사용자의 몫이 된다.

그렇기 때문에 여러 컨테이너를 함께 묶고 관리해줄 고레벨의 구조가 필요한데, 이게 pod가 있는 이유이다.

### Understanding pods

pod에 있는 모든 container들은 같은 hostname과 network interface를 가진다.

반면 filesystem은 다르다. 컨테이너의 filesystem은 컨테이너 이미지에서 오기 때문에 다른 컨테이너의 filesystem과 완전히 독립된다. 하지만 `Volumn` 개념을 통해서 같은 파일시스템 공유가 가능.

pod가 같은 네트워크, 같은 IP주소와 포트를 가지는데 같은 pod에 떠있는 여러 컨테이너의 프로세스들이 같은 포트를 사용하여 충돌하지는 않을까?

일단 파드가 다르면 host 자체가 달라지기 때문에 걱정할필요 없다.
파드 안의 컨테이너들은 localhost로 통신하기 떄문에 충돌하지 않는다 (?)

그럼 다 한 pod에 여러 container를 다 넣어야할까? (당연한 얘기지만) 책에서는 나누기를 권장한다. 프론트엔드/백엔드를 예시로 들면서 CPU 리소스의 휴율성, 관리, 그리고 scaling을 이유로 이는 각각의 pod로 나누는게 좋다.

### 그러면 언제 여러 컨테이너를 한 포드에 넣는게 좋을까?

어플리케이션이 하나의 메인 프로세스와 나머지 부가 프로세스로 나뉘었을 떄 한 pod를 사용하면 좋다.

다른 예시로는 container include log rotators and collectors (?), data processors, communication adapters(?)

### 언제 multiple pod를 쓰는게 좋을까?

이를 결정하기 위해 다음 질문에 대답해보자.

- Do they need to be run together or can they run on different hosts?
- Do they represent a single whole or are they independent components?
- Must they be scaled together or individually?

> 사실 아직도 multiple container를 한 pod에 넣는 경우를 잘 상상하기 힘들다. 득보다 실이 많을듯 한데.. 관련 내용은 6장에서 더 상세하게 설명한다고 하니 일단 넘어가겠다.

## Createing pods from YAML or JSON descriptors

앞 장에서 한 것처럼 `kubectl run` 명령어로 만드는게 가장 간단하지만 아주 제한적인 옵션만 쓸 수 있고 git 등을 쓰기 힘들다.

그래서 pod를 만들때는 대부분 JSON 이나 YAML manifest를 만들어서 Kubernetes REST API endpoint를 사용한다.

이 API 명세는 [여기](https://kubernetes.io/docs/reference/)를 참고하는게 좋다.


일단 저번 장에서 `kubectl run`으로 pod를 띄웠으므로 이 pod의 YAML 정를 확인해보자.

```
$ kubectl get po kubia -o yaml
apiVersion: v1                                            1
kind: Pod                                                 2
metadata:                                                 3
  annotations:                                            3
    kubernetes.io/created-by: ...                         3
  creationTimestamp: 2016-03-18T12:37:50Z                 3
  generateName: kubia-                                    3
  labels:                                                 3
    run: kubia                                            3
  name: kubia-zxzij                                       3
  namespace: default                                      3
  resourceVersion: "294"                                  3
  selfLink: /api/v1/namespaces/default/pods/kubia-zxzij   3
  uid: 3a564dc0-ed06-11e5-ba3b-42010af00004               3
spec:                                                     4
  containers:                                             4
  - image: luksa/kubia                                    4
    imagePullPolicy: IfNotPresent                         4
    name: kubia                                           4
    ports:                                                4
    - containerPort: 8080                                 4
      protocol: TCP                                       4
    resources:                                            4
      requests:                                           4
        cpu: 100m                                         4
    terminationMessagePath: /dev/termination-log          4
    volumeMounts:                                         4
    - mountPath: /var/run/secrets/k8s.io/servacc          4
      name: default-token-kvcqa                           4
      readOnly: true                                      4
  dnsPolicy: ClusterFirst                                 4
  nodeName: gke-kubia-e8fe08b8-node-txje                  4
  restartPolicy: Always                                   4
  serviceAccount: default                                 4
  serviceAccountName: default                             4
  terminationGracePeriodSeconds: 30                       4
  volumes:                                                4
  - name: default-token-kvcqa                             4
    secret:                                               4
      secretName: default-token-kvcqa                     4
status:                                                   5
  conditions:                                             5
  - lastProbeTime: null                                   5
    lastTransitionTime: null                              5
    status: "True"                                        5
    type: Ready                                           5
  containerStatuses:                                      5
  - containerID: docker://f0276994322d247ba...            5
    image: luksa/kubia                                    5
    imageID: docker://4c325bcc6b40c110226b89fe...         5
    lastState: {}                                         5
    name: kubia                                           5
    ready: true                                           5
    restartCount: 0                                       5
    state:                                                5
      running:                                            5
        startedAt: 2016-03-18T12:46:05Z                   5
  hostIP: 10.132.0.4                                      5
  phase: Running                                          5
  podIP: 10.0.2.3                                         5
  startTime: 2016-03-18T12:44:32Z                         5
```

1. Kubernetes API version used in this YAML descriptor
2. Type of Kubernetes object/resource
3. Pod metadata (name, labels, annotations, and so on)
4. Pod specification/contents (list of pod’s containers, volumes, and so on)
5. Detailed status of the pod and its containers

복잡해 보이지만 기본을 이해하고 중요한 파트와 디테일을 구분하는 법을 알면 간단해질 것이다.

일단 사용하는 Kubernetes API 버전이 나오고, 거의 모든 kubernetes resource에서 쓰이는 세가지 중요한 섹션이 나옴

- `Metadata`: name, namespace, label 등 pod에 대한 정보
- `Spec`: pod의 컨텐츠에 대한 실제 description - contaniers, volumns 등
- `Status`: running pod의 현재 정보 - pod의 컨디션, 각 컨테이너의 description과 status, internal IP 등

> `status` 파트는 runtime data에 한정된 read-only 데이터이다. 새로 만들때는 필요없음

### Creating a simple YAML descriptor for a pod

위의 YAML은 꽤 복잡하기 때문에 더 단순한 YAML 파일을 만들어보자. `kubia-manual.yaml`을 만들거나 (아무데나 만들어도 상관없다) 책의 코드를 다운받자


```
apiVersion: v1               1
kind: Pod                    2
metadata:
  name: kubia-manual         3
spec:
  containers:
  - image: luksa/kubia       4
    name: kubia              5
    ports:
    - containerPort: 8080    6
      protocol: TCP
```

1. Descriptor conforms to version v1 of Kubernetes API
2. You’re describing a pod.
3. The name of the pod
4. Container image to create the container from
5. Name of the container
6. The port the app is listening on

`v1` 버전은 Kubernetes API 버전. pod의 이름은 `kubia-manual`. 한개의 컨테이너를 가지는데 이는 `bartkim0426/kubia` 이미지 기반. 포트는 8080

pod 정의에서 port를 지정하는 것은 순전히 informational이다. (t실제로는 효과가 없음) 만약 `0.0.0.0` 커넥션으로 열려있다면 따로 지정하지 않은 아무 포트로도 접근이 가능하다. 하지만 명시적으로 적어두는게 사용자가 쉽게 파악이 가능하기 때문.

manifest를 준비할 때 위에서 얘기한 [Kubernetes reference documentatino](https://kubernetes.io/docs/reference/)를 참고하거나 `kubectl explain` 명령어를 쓰면 유용하다.


```
$ kubectl explain pods
DESCRIPTION:
Pod is a collection of containers that can run on a host. This resource
             is created by clients and scheduled onto hosts.
FIELDS:
   kind      <string>
     Kind is a string value representing the REST resource this object
     represents...
   metadata  <Object>
     Standard object's metadata...
   spec      <Object>
     Specification of the desired behavior of the pod...
   status    <Object>
     Most recently observed status of the pod. This data may not be up to
     date...
```

상세하게 볼 수도 있다.

```
$ kubectl explain pod.spec
RESOURCE: spec <Object>

DESCRIPTION:
    Specification of the desired behavior of the pod...
    podSpec is a description of a pod.

FIELDS:
   hostPID   <boolean>
     Use the host's pid namespace. Optional: Default to false.
   ...

   volumes   <[]Object>
     List of volumes that can be mounted by containers belonging to the
     pod.

   Containers  <[]Object> -required-
     List of containers belonging to the pod. Containers cannot currently
     Be added or removed. There must be at least one container in a pod.
     Cannot be updated. More info:
     http://releases.k8s.io/release-1.4/docs/user-guide/containers.md
```

### Using kubectl create to create the pod

YAML 파일을 기반으로 pod를 만드려면 `kubectl create` 명령어를 쓰면 된다.

```
$ kubectl create -f kubia-manual.yaml
pod/kubia-manual created
```

```
$ kubectl get po kubia-manual -o yaml
$ kubectl get po kubia-manual -o json
$ kubectl get pods
NAME                                 READY   STATUS    RESTARTS   AGE
kubia                                1/1     Running   0          47h
kubia-manual                         1/1     Running   0          63s
```

### Viewing application logs

> 실제로 Kubernetes를 사용할 때 로그 확인이 어려웠던 기억이 있다. 로그 부분을 잘 봐두면 추후에 큰 도움이 될것이다. (아마도)

ssh로 컨테이너에 접속해서 `docker log`를 사용해도 되지만 훨씬 간단한 명령어가 있다.

```
$ kubectl logs kubia-manual
Kubia server starting...
```

> Container 로그는 매일 or 컨테이너 로그 파일이 10MB가 될 때마다 rotated된다. `kubectl logs`는 가장 최근 rotation의 로그만 보여줌

`-c` 옵션으로 컨테이너를 지정할수도 있다.

```
$ kubectl logs kubia-manual -c kubia
Kubia server starting...
```

pod이 없어지면 log도 모두 제거되므로 중앙 집중화된 cluster-wide logging을 셋업하는게 필요하다. 이는 17장에서 다룬다고 한다. (언제 17장까지 읽지...)

### Sending requests to the pod

pod는 일단 동작한다. 근데 어떻게 확인할지? 저번 장에서는 `kubectl expose` 명령어를 사용하였다. 요번에는 테스트와 debug 목적으로 연결하는 방법을 사용할것. (service로 한 챕터가 할애되어서 그때 설명한다는 말인듯 하다) 그 중 하나는 port forwarding이다.

```
$ kubectl port-forward kubia-manual 8888:8080
Forwarding from 127.0.0.1:8888 -> 8080
Forwarding from [::1]:8888 -> 8080
```

이제 `localhost:8888`로 접근이 가능하다.

```
$ curl localhost:8888
You've hit kubia-manual
```

포트포워딩을 사용하면 Kubernetes 클러스터의 pod를 로컬 머신의 `kuberctl port-forward` 프로세스가 연결시켜준다. 하나의 pod를 테스트하기에 효율적인 방법이다.


## Organizing pods with labels

pod가 점점 늘어날수록 분류해야 할 필요성이 생긴다. 특히 microservice 구조에서는 아주 많은 pod가 생길테고 이들은 대부분 replicated이거나 다른 릴리즈일 것이다.

이들을 분류하고 관리하는 kubernetes obejct는 `label`이다.

`label`은 리소스에 붙이는 단순한 키-밸류 (key-value) 쌍이다. `label selectors`를 사용하여 리소스들을 선택할때 사용된다.

각 리소스는 여러개의 라벨을 가질 수 있음. 다만 리소스 내에서 각 key들은 unique해야함

책의 예시에서는 `app`(ui, as, os, pc ...), `rel`(stable, beta,, canary) 라벨을 통해 많은 pod을 가진 시스템의 구조를 쉽게 볼 수 있게 하였다.

### Specifying labels when creating a pod

pod를 만들떄 두 개의 라벨을 붙여서 만들어보자. `kubia-manual-with-labels.yaml` 파일에 다음 내용을 추가

```
apiVersion: v1
kind: Pod
metadata:
  name: kubia-manual-v2
  labels:
    creation_method: manual          1
    env: prod                        1
spec:
  containers:
  - image: luksa/kubia
    name: kubia
    ports:
    - containerPort: 8080
      protocol: TCP
```

이제 pod를 만들어보자

```
$ kubectl create -f kubia-manual-with-labels.yaml
pod "kubia-manual-v2" created
```

`--with-labels`로 label 확인 가능

```
$ kubectl get po --show-labels
NAME            READY  STATUS   RESTARTS  AGE LABELS
kubia-manual    1/1    Running  0         16m <none>
kubia-manual-v2 1/1    Running  0         2m  creation_method=manual,env=prod
kubia-zxzij     1/1    Running  0         1d  run=kubia
```

원하는 라벨만 볼 수도 있다. (`-L` flag)

```
$ kubectl get po -L creation_method,env
NAME            READY   STATUS    RESTARTS   AGE   CREATION_METHOD   ENV
kubia-manual    1/1     Running   0          16m   <none>            <none>
kubia-manual-v2 1/1     Running   0          2m    manual            prod
kubia-zxzij     1/1     Running   0          1d    <none>            <none>
```

### Modifying labels of existing pods

기존 pod에 `label` 명령어를 통해 라벨을 추가하거나 수정할 수 있다.

```
$ kubectl label po kubia-manual creation_method=manual
pod "kubia-manual" labeled
```

기존 라벨을 수정할 때에는 `--overwrite` flag 함께 사용

```
$ kubectl label po kubia-manual-v2 env=debug --overwrite
pod "kubia-manual-v2" labeled
```

## Listing Subsets of pods throug label selectors

label selector는 다음과 같은 리소스를 선택할 수 있다.
- Contains (or doesn't contain) a label with a certain key
- Contains a label with a certain key and value
- Contains a label with a certain key, but with a value not equal to the one you specific

### Listing pods using a label selector

`creation_method=manual`인 pod 선택

```
$ kubectl get po -l creation_method=manual
NAME              READY     STATUS    RESTARTS   AGE
kubia-manual      1/1       Running   0          51m
kubia-manual-v2   1/1       Running   0          37m
```

`env` 라벨을 가지고 있는 pod 선택

```
$ kubectl get po -l env
NAME              READY     STATUS    RESTARTS   AGE
kubia-manual-v2   1/1       Running   0          37m
```

`env` 라벨이 없는 pod

```
$ kubectl get po -l '!env'
NAME           READY     STATUS    RESTARTS   AGE
kubia-manual   1/1       Running   0          51m
kubia-zxzij    1/1       Running   0          10d
```

이것과 비슷하게 다음 label selector들도 사용 가능하다. 아주 직관적이니 그냥 사용하면 될듯?

- `creation_method!=manual`
- `env in (prod, devel)`
- `env notin (prod,devel)`

### Using multiple conditions in a label selector

콤마(`,`)로 그냥 연결시켜주면 된다. `app=pc,rel=beta`

## Using labels and selectors to contrain pod scheduling

지금까지 만든 pod들은 워커 노드에 걸쳐 랜덤으로 스케쥴링 되었다. Kubernetes는 원래 그렇게 설계된 것이기 때문에 보통은 이를 지정할 필요가 없다.

하지만 특정 pod를 스케쥴해야할 상황이 생길 수 있다. 예를 들면 하드웨어 구조가 다른 경우. (특정 워커 노드는 HDD, 다른건 SSD일 경우 혹은 GPU 기반 연산이 필요한 경우 등)

Kubernetes의 기본은 실제 구조를 숩기고 이를 작동시킨는 것이기 때문에 pod가 어떤 노드에 스케쥴 될지를 따로 지정할 일은 없을 것.

특정 노드를 선택하는 게 아니라 node requirements를 작성하고 이이 맞게 kubernetes가 노드를 선택하게 해 주어야 한다. 이는 node label과 node labe select가 가능하게 해줌

### Using labels for categorizing worker nodes

pod 뿐만 아니라 node를 포함한 모든 kubernetes의 object에 label을 사용할 수 있다. 보통은 clsuter에 node를 추가할 때 하드웨어 타입 등을 라벨로 붙임

클러스터의 노드 중 하나가 GPU를 사용한다고 가정하고 `gpu=true` 라벨을 붙여보자

```
# 이 명령어는 해당 노드가 없으니 작동하지 않는다.
$ kubectl label node gke-kubia-85f6-node-0rrx gpu=true
node "gke-kubia-85f6-node-0rrx" labeled
```

이제 라벨로 노드를 셀렉트할 수 있다.

```
# 요것도 마찬가지로 이런 결과는 안나올것이다.
$ kubectl get nodes -l gpu=true
NAME                      STATUS AGE
gke-kubia-85f6-node-0rrx  Ready  1d
```

### Scheduling pods to specific nodes

pod를 GPU 노드에서 작동하게 하려고 상상해보자. `kubia-gpu.yalm`을 만들고 pod을 시작시켜보자. (`kubectl create -f kubia-gpu.yalm`)

```
apiVersion: v1
kind: Pod
metadata:
  name: kubia-gpu
spec:
  nodeSelector:               1
    gpu: "true"               1
  containers:
  - image: luksa/kubia
    name: kubia
```

> `minikube`의 경우에는 위에서 `node`에 `gpu` label을 안 붙였으므로 `0/1 nodes are available: 1 node(s) didn't match node selector.`와 같은 에러 메세지를 반환한다. 이를 테스트해보려면 기본 node인 `m01` 노드에 `gpu=true` 라벨을 붙이고 다시 시도해보면 된다.


## Annotating pods

label과 함께, pod와 다른 object들은 `annotation`을 가질 수 있다.
- annotation은 라벨과 비슷하게 key-value 쌍으로 이뤄짐
- 라벨처럼 object를 그루핑 할 수 없음
- 많은 정보 저장 가능

### Looking up an object's annotations

```
$ kubectl get po kubia-zxzij -o yaml
apiVersion: v1
kind: pod
metadata:
  annotations:
    kubernetes.io/created-by: |
      {"kind":"SerializedReference", "apiVersion":"v1",
      "reference":{"kind":"ReplicationController", "namespace":"default", ...
```

### Adding and modifying annotations

```
$ kubectl annotate pod kubia-manual mycompany.com/someannotation="foo bar"
pod "kubia-manual" annotated

$ kubectl describe pod kubia-manual
...
Annotations:    mycompany.com/someannotation=foo bar
...
```

## Using Namespaces to group resources

만약 object들을 겹치지 않는 그룹으로 나누고 싶으면 어떻게 할까?

이를 위해서 Kubernetes는 objects들을 namespaces로 묶는다
  - 이는 챕터2에서 다뤘던 `Linux namespace`와는 완전 별개이다.
  - Kubernetes namespace는 object name의 스코프를 제공
  - resource들을 여러 namespace로 분리

### Understanding the need for namespaces

여러 namespace를 사용하면 복잡한 시스템을 작은 구분된 그룹으로 나눌 수 있게 해준다.
  - 이는 리소스를 multi-tenant(다중의) 환경으로 나눌 수 있게 해줌 (production, development, QA ...)
  - resource 이름은 namespace 안에서는 유일해야함 (다른 namespace간에서는 같은 이름 사용 가능)
  - 몇몇 resource는 namespace에 포함되지 않음; Node 등

### Discovering other namespaces and their pods

일단 namespace를 리스팅 해보자.

```
$ kubectl get ns
NAME          LABELS    STATUS    AGE
default       <none>    Active    1h
kube-public   <none>    Active    1h
kube-system   <none>    Active    1h
```

지금까지는 `default` namespace만 사용하였다.
- `kubectl get` 명령어를 사용할 때 namespace를 지정한 적이 없기 때문에 디폴트로 설정된 `default` namespace 사용
- `kube-system`은 Kubernetes system 자체와 관련된 리소스들을 모은 namespace

namespaces는 서로 겹치지 않는 그룹간에 리소스를 분리시켜준다.
- 만약 몇몇 유저나 그룹이 같은 클러스터를 사용하고 각각 다른 리소스를 사용하면 각자의 namespace를 가지면 된다
- namespace가 resorce name scope를 보호해 주므로 이 그룹끼리 충돌을 방지하기 위해 어떤 특별한 케어를 할 필요가 없어짐

### Creating a namespace

namespace는 Kubernetes의 다른 리소스와 똑같이 YALM 파일로 Kubernetes API를 통해 만들 수 있다.

`custom-namespace.yaml`

```
apiVersion: v1
kind: Namespace                  1
metadata:
  name: custom-namespace         2
```

```
$ kubectl create -f custom-namespace.yaml
namespace "custom-namespace" created
```

따로 YAML 파일 없이 `kubectl create namespace` 명령어를 통해서도 만들 수 있다.
- 위에서 YAML 파일을 사용한 이유는 Kubernetes가 적절한 API object를 위해 YAML 파일을 API server에 post 한다는 것을 보여주기 위해서라고 함

```
$ kubectl create namespace custom-namespace
namespace "custom-namespace" created
```

### Managing objects in other namespaces

만든 namespace에 리소스를 추가하려면 `namespace: custom-namespace`를 `metadata` 섹션에 추가하거나 `kubectl create` 명령어에 추가하면 된다.

```
$ kubectl create -f kubia-manual.yaml -n custom-namespace
```

### Understangding the isolation provided by namespaces

namespace가 그룹 간에 분리된 object를 사용할 수 있게 해주지만, 모든 running object에 대해서 isolation을 제공해주는 것은 아님
- 다른 사용자가 서로 다른 nanmespace에 pod를 배포하였어도 필요한 경우 이들끼리 communicate 가능

## Stopping and removing pods

### Deleting a pod by name

`kubectl delete` 명령어로 삭제 가능

```
$ kubectl delete po kubia-gpu
pod "kubia-gpu" deleted
```

> 한번에 여러개의 pod를 지울 수도 있다. (`kubectl delete po pod1 pod`)

### Deleting pods using label selectors

`creation-method=manual` 라벨이 붙은 pod들만 제거해보자.

```
$ kubectl delete po -l creation_method=manual
pod "kubia-manual" deleted
pod "kubia-manual-v2" deleted
```

### Deleting pods by deleting the whole namespace

만약 더 이상 해당 namespace에 있는 pod들이나 namespace 자체가 필요 없다면 namespace를 제거하면 그 안의 pod들은 모두 자동으로 제거된다.

```
$ kubectl delete ns custom-namespace
namespace "custom-namespace" deleted
```

### Delete all pods in a namespace

이제 `kubernetes create`로 띄운 단 하나의 pod를 제거해보자.

```
$ kc delete po --all
pod "kubia" deleted
```

### Deleting (almos) all resources in a namespace

거의 모든 리소스를 다음 명령어로 제거할 수 있다.

```
$ kubectl delete all --all
pod "kubernetes-bootcamp-b4d9f565-rfhdj" deleted
pod "kubernetes-bootcamp-b4d9f565-thnvk" deleted
service "kubernetes" deleted
service "kubernetes-bootcamp" deleted
service "kubia-http" deleted
deployment.apps "kubernetes-bootcamp" deleted
replicaset.apps "kubernetes-bootcamp-69fbc6f4cf" deleted
replicaset.apps "kubernetes-bootcamp-6b4c55d8fc" deleted
replicaset.apps "kubernetes-bootcamp-b4d9f565" deleted
```

> 나는 `Kubernetes-bootcamp` 튜토리얼을 따라하여 위 파드가 떠 있었는데 이들이 모두 한 번에 제거되었다.

