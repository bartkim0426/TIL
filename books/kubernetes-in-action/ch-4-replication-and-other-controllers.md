# ch4. Replication and other controllers: deploying managed pods

This chapter covers

- Keeping pods healthy
- Running multiple instances of the same pod
- Automatically rescheduling pods after a node fails
- Scaling pods horizontally
- Running system-level pods on each cluster node
- Running batch jobs
- Scheduling jobs to run periodically or once in the future

저번 장에서 본 것 처럼 pod는 kubernetes의 가장 기본적인 deployable unit이다. 하지만 실제 유즈케이스에서는 이를 자동으로 실행시키고 직접 조작 없이 건강한 (healthy) 상태로 유지시켜야함. 이를 위해서는 직접 pod를 만들 일은 거의 없다. 대신 다른 resource (deployments, replicationController)를 사용해서 이들이 pods를 만들고 관리하게 할 것이다.

## 4.1 Keeping pods healthy

kubernetes를 사용하는 주요 이점 중 하나는 컨테이너의 리스트를 가질 수 있고 이들을 어떤 방식으로든 cluster에서 작동하게 할 수 있다는 것이다.

pod가 node에 스케쥴링되면 node의 Kubelet이 컨테이너를 작동시킨다. 컨테이너의 프로세스에 문제가 생기면 Kubelet이 container을 자동으로 재시작 해 줄 것이다.

하지만 프로세스 crashing이 발생하지 않고 앱이 죽는 경우도 있다.

이런 경우에 재시작을 가능하기 위해서 app 내부에서 어떻게 작동하는지와 상관 없이 바깥에서 application의 health를 체크해 줄 필요가 있다.

### Introducing liveness probes

kubernetes는 `liveness probes`를 사용하여 컨테이너가 살아있는지 확인할 수 있다. pod의 명세에서 liveness probe를 정의 가능

kubernetes는 다음과 같은 메커니즘으로 작동한다.
- `HTTP GET` probe는 container의 IP 주소로 GET 리퀘스트를 보낸다. 만약 response를 받고 resopnse code가 에러 코드가 아니라면 (2xx or 3xx) 이를 성공으로 간주한다. 만약 error response code를 리턴하거나 아무런 응답이 없다면 probe는 실패로 간주하고 컨테이너가 재시작된다.
- `TCP Socket` probe는 컨테이너의 특정 port와 TCP 커넥션을 시도한다. 커넥션이 성공하면 성공으로 간주하고 아니라면 컨테이너가 재시작된다.
- `Exec` probe는 컨테이너에서 커맨드 명령을 실행시켜 해당 명령어의 최종 status code를 확인한다. 코드가 `o`라면 성공으로 간주하고 아니라면 실패로 간주한다.

### Creating an HTTP-based liveness probe

Node.js app에 어떻게 liveness probe를 추가하는지 살펴보자. 이는 web app이기 때문에 웹서버에 요청을 보내는 것이 맞을 것이다.

demo liveness probe를 만들기 위해 다서번째 리퀘스트마다 500 에러를 반환하게 앱을 조금 수정해보자.

> [책의 code archieve](https://github.com/luksa/kubernetes-in-action/blob/master/Chapter04/kubia-unhealthy/Dockerfile)에서 코드를 확인할 수 있고, 저자가 이미 Docker Hub에 컨테이너 이미지를 푸쉬해 두었기 때문에 따로 빌드하지 않아도 된다.

HTTP GET liveness probe를 포함하는 새로운 pod를 만들어보자.

```
apiVersion: v1
kind: Pod
metadata:
  name: kubia-liveness
spec:
  containers:
  - image: luksa/kubia-unhealthy       1
    name: kubia
    livenessProbe:                     2
      httpGet:                         2
        path: /                        3
        port: 8080                     4
```

- `1` 줄은 broken app의 이미지
- `2` 줄은 httpGet을 사용하는 liveness probe
- `3` 줄은 해당 request를 보낼 path
- `4` 줄은 연결할 포트

### Seeing a liveness probe in action

이제 pod를 만들어보자.

```
$ kubectl create -f kubia-liveness
pod/kubia-liveness created
```

몇 분이 지난 후에 상태를 확인해보자.

> 바로 확인하면 `RESTARTS` 칼럼이 0으로 나올 수 있다. 조금 더 시간이 지난 이후에 확인해보자. 조금 더 시간이 지나면 `RESTARTS`가 늘어나느 것을 볼 수 있다.

```
$ kubectl get po kubia-livenetss
NAME             READY   STATUS    RESTARTS   AGE
kubia-liveness   1/1     Running   1          2m53s
```

왜 container가 재시작 되었는지를 보려면 application log를 보면 된다.

이전 장에서는 `kubectl logs`를 통해 컨테이너의 로그를 보는 법을 배웠다. 만약 terminated `--previous` 옵션을 사용하면 이전에 terminated 된 container의 로그가 나온다.

```
$ kubectl logs mypod --previous
```

왜 컨테이너가 재시작되었는지 보기 위해 `kubectl describe`를 사용해보자.

```
$ kubectl describe po kubia-liveness
Name:           kubia-liveness
...
Containers:
  kubia:
    Container ID:       docker://480986f8
    Image:              luksa/kubia-unhealthy
    Image ID:           docker://sha256:2b208508
    Port:
    State:              Running                                         1
      Started:          Sun, 14 May 2017 11:41:40 +0200                 1
    Last State:         Terminated                                      2
      Reason:           Error                                           2
      Exit Code:        137                                             2
      Started:          Mon, 01 Jan 0001 00:00:00 +0000                 2
      Finished:         Sun, 14 May 2017 11:41:38 +0200                 2
    Ready:              True
    Restart Count:      1                                               3
    Liveness:           http-get http://:8080/ delay=0s timeout=1s
                        period=10s #success=1 #failure=3
    ...
Events:
... Killing container with id docker://95246981:pod "kubia-liveness ..."
    container "kubia" is unhealthy, it will be killed and re-created.
```


`2`번 줄에서 보이는 `Exit Code` 137은 특별한 뜻을 가지는데, 이는 프로세스가 외부 signal에 의해서 종료되었음을 의미한다. 이는 `128+x`를 나타낸다. `x`는 프로세스가 죽은 원인이 되는 숫자이다. 위 경우에는 `9`인데 이는 `SIGKILL` (process was killed forcibly)을 나타낸다.

### Configuring additional properties of the liveness probe

`kubectl describe`가 liveness probe에 대해서도 다음 정보를 주는 것을 볼 수 있다.

```
    Liveness:       http-get http://:8080/ delay=0s timeout=1s period=10s #success=1 #failure=3
```

명시적으로 지정했던 옵션 이외에도 `delay`, `timeout`, `period` 등도 볼 수 있다. `delay=0s`는 probing이 컨테이너가 시작되자 마자 실행된다는 것을 의미한다. `timeout`은 1초로 설정되었는데 이는 컨테이너가 1초 이내에 답장하지 않으면 실패로 간주한다는 것이다. `period=10s`은 매 10초마다 probe를 실행시키는 것을 얘기하고 3번 실패 이후에 (`#fauilre=3`) 컨테이너를 다시 시작한다.

이 추가적인 파라미터들은 probe를 정의할 때 커스터마이징이 가능하다. 만약 initial delay를 세팅하기 위해서는 `initialDelaySeconds`를 추가해주면 된다.

### Creating effective liveness probes

프로덕션에서 사용하는 pod들에 대해서는 반드시 liveness probe를 추가해야 한다. 그렇지 않으면 kubernetes는 앱이 아직 살아있는지 아닌지를 알 수 없다.

간단하게 서버가 response를 보내는 것만 체크하여 liveness probe를 만들 수도 있다. 아주 간단해 보이지만 컨테이너가 더이상 HTTP request에 응답을 하지 않을 때 재시작 될 것이다. liveness probe가 없는 것 보다는 훨씬 더 낫다.

하지만 더 나은 liveness 확인을 위해서 probe가 특별한 URL 경로(`/health`같은)를 만들어서 앱의 중요한 컴포넌트들의 상태를 내부적으로 확인해 주는 것이 좋다.

> Tip. `/health`가 authentication을 가지지 않도록 해야한다.

앱의 내부적인 요소만 체크하고, 외부적인 요인에 영향을 받지 않도록 주의하라. 예를 들면 프론트엔드 웹 서버의 liveness probe는 서버가 백엔드 데이터베이스에 연결되지 못해 실패하는 것에 영향을 받으면 안 된다.


## 4.2 Introducing replicationcontrollers

> 참고: 최신 Kubernetes 문서에 따르면 `ReplicaSett`을 구성하는 `Deployment`가 현재 권장되는 설정 방법이라고 한다. 따라서 요번 장은 별로 정리하지 않음. [문서](https://kubernetes.io/ko/docs/concepts/workloads/controllers/replicationcontroller/) 참고.

`ReplicationController`는 세가지 필수적인 파트로 나뉨
- `label selector`
- `replica count`
- `pod template`

[!image](https://learning.oreilly.com/library/view/kubernetes-in-action/9781617293726/04fig02_alt.jpg)

### Creating a ReplicationController

`kubia-rc.yaml`을 통해 ReplicationController를 만들어 보자

```
apiVersion: v1
kind: ReplicationController        1
metadata:
  name: kubia                      2
spec:
  replicas: 3                      3
  selector:                        4
    app: kubia                     4
  template:                        5
    metadata:                      5
      labels:                      5
        app: kubia                 5
    spec:                          5
      containers:                  5
      - name: kubia                5
        image: luksa/kubia         5
        ports:                     5
        - containerPort: 8080      5
```

- `4`번은 ReplicationController가 사용할 pod selector
- `5`번은 ReplicationController가 참고하여 만들 pod 정보.

> `selector`를 지정하지 않으면 Kubernetes가 pod template에서 참고하여 만든다. 이렇게 하는게 더 쉽고 심플함

```
$ kubectl create -f kubia-rc.yaml
replicationcontroller/kubia created
```

### Seeing the ReplicationController in action

기존에 `app=kubia`인 pod가 없었기 때문에 ReplicationController는 이를 pod template으로부터 만든다.

```
$ kubectl get pods
NAME          READY   STATUS              RESTARTS   AGE
kubia-5pcc6   0/1     ContainerCreating   0          15s
kubia-c6fzz   0/1     ContainerCreating   0          15s
kubia-z4z9n   0/1     ContainerCreating   0          15s
```

이제 이 중 하나의 pod를 없애서 ReplicationController가 이를 다시 만들어내는지 확인해보자.

```
$ kubectl delete pod kubia-5pcc6
pod "kubia-5pcc6" deleted
```

```
$ kubectl get pods
NAME          READY   STATUS        RESTARTS   AGE
kubia-5pcc6   1/1     Terminating   0          2m46s
kubia-c7tpw   1/1     Running       0          6s
kubia-lc8hh   1/1     Running       0          56s
kubia-z4z9n   1/1     Running       0          2m46s
```

새로운 pod가 생긴 것을 볼 수 있다

`kubectl get`을 통해 ReplicationController의 상태를 볼 수 있다.

```
$ kubectl get rc
NAME    DESIRED   CURRENT   READY   AGE
kubia   3         3         3       3m21s
```

다른 object들과 마찬가지로 `kubectl describe`를 사용하여 더 자세한 정보를 볼 수 있따.

### Moving pods in and out of the scope of a ReplicationController

ReplicationController에 의해 만들어진 pod들은 이들과 전혀 묶일 수 없다. ReplicationController는 label selector를 사용하여 pod들과 매칭된다. 따라서 pod의 label을 변경하면 ReplicationController의 scope에 추가하거나 빠질 수 있다.

```
$ kubectl label pod kubia-z4z9n type=special
pod/kubia-z4z9n labeled

$ kc get po --show-labels
NAME          READY   STATUS    RESTARTS   AGE   LABELS
kubia-c7tpw   1/1     Running   0          11m   app=kubia
kubia-lc8hh   1/1     Running   0          12m   app=kubia
kubia-z4z9n   1/1     Running   0          14m   app=kubia,type=special

$ kubectl label pod kubia-z4z9n app=foo --overwrite
pod/kubia-z4z9n labeled

$ kubectl get pods -L app
NAME          READY   STATUS              RESTARTS   AGE   APP
kubia-c7tpw   1/1     Running             0          12m   kubia
kubia-lc8hh   1/1     Running             0          13m   kubia
kubia-wlzb4   0/1     ContainerCreating   0          3s    kubia
kubia-z4z9n   1/1     Running             0          15m   foo
```

### Changing the pod template

ReplicationController의 pod template은 아무때나 변경이 가능하다. 이는 pod template이 변경 된 이후에 생성되는 pod에만 적용된다. 이전 pod를 수정하기 위해서는 기존 pod들을 지워서 ReplicationController가 이들을 대체하는 pod를 만들게 하여야한다.

예시로 `kubia` replicationcontroller를 수정해보자.

```
$ kubectl edit rc kubia
```

이는 ReplicationController의 YAML 정의를 열어준다. `template.metadata`에 새로운 라벨을 추가한 후 저장해보자. 

```
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: kubia
        newlabel: kubia  # 요걸 추가함
```

> kubectl edit이 사용할 에디터를 변경하려면 다음 내용을 `~/.bashrc`나 `~/.zshrc`에 추가해주자.

```
export KUBE_EDITOR="/usr/bin/vim"
```

### Horizontally scaling pods

```
$ kubectl scale rc kubia --replicas=10
replicationcontroller/kubia scaled
```

`kubectl scsle` 옵션 대신 `ReplicationController`의 정의를 수정할 수도 있다.

```
$ kubectl edit rc kubia
```

replicas를 10으로 수정하고 확인해보자.

```
$ kubectl get rc
NAME    DESIRED   CURRENT   READY   AGE
kubia   10        10        0       47h
```

scale down도 동일하게  해주면 된다.

### Deleting a ReplicationController

`kubectl delete` 명령어를 사용하여 ReplicationController를 제거하면 pod들도 같이 지워진다.그렇게 하지 않고 `RC`만 지울 수도 있음 (`--cascade=false` 옵션)

```
$ kubectl delete rc kubia --cascade=false
replicationcontroller "kubia" deleted
```

## 4.3 Using Replicasets instead of replicationController

처음에는 ReplicationController가 pod를 replicating해주고 rescheduling 해주는 유일한 component였다. 나중에 비슷한 리소스인 ReplicaSet이 추가됨

> ReplicationController는 결국 deprecated 될 예정

Kubernetes에서 처음 시작된 ReplicationController를 설명하는게 좋다고 생각하여 먼저 설명하였지만, 앞으로는 무조건 ReplicationController 대신 ReplicaSets을 사용하는게 좋음

### Comparing a ReplicaSet to a ReplicationController

Replicasets은 ReplicationController와 동일하게 동작하지만, 더 express한 pod selector를 가진다.

ReplicaSet은 일치하는 label을 가진 pod 뿐만 아니라 value와 상관 없이 특정 label key를 가진 pod들을 사용할 수도 있다.

### Defining a ReplicaSet

비슷하게 YAML을 만들어보지. `kubia-replicaset.yaml`


```
apiVersion: apps/v1beta2            1
kind: ReplicaSet                    1
metadata:
  name: kubia
spec:
  replicas: 3
  selector:
    matchLabels:                    2
      app: kubia                    2
  template:                         3
    metadata:                       3
      labels:                       3
        app: kubia                  3
    spec:                           3
      containers:                   3
      - name: kubia                 3
        image: luksa/kubia          3
```

- 1. ReplicaSet은 v1 API 버전이 아니기 때문에 v1beta2를 사용
- 2. ReplicationController에서 사용했던 것과 동일하지만 `selector.matchLabels` 형태로 사용

> 현재 (2020-05) 기준 ReplicaSet은 `apps/v1` api version을 사용한다. 위 코드를 `apiVersion: appds/v1`로 변경하여야 작동한다.

### Creating and examining a ReplicaSet

```
$ kubectl create -f kubia-replicaset.yaml
replicaset.apps/kubia created

$ kubectl get rs
NAME      DESIRED   CURRENT   READY     AGE
kubia     3         3         3         3s
```

### Using the ReplicaSet's more expressive label selectors

ReplicationController에 비한 ReplicaSet의 주요 improvement는 더 expressive한 label selector를 사용할 수 있다는 것이다.

`kubia-replicaest-matchexpressions.yaml`을 만들고 기존의 selector를 다음과 같이 변경해보자.

```
selector:
   matchExpressions:
     - key: app                      1
       operator: In                  2
       values:                       2
         - kubia                     2
```

다음 4가지 operator 사용 가능
- `In`
- `NotIn`
- `Exists`
- `DoesNotExist`

### Wrapping up ReplicaSets

다음과 같이 쉽게 제거 가능

```
$ kubectl delete rs kubia
replicaset.apps "kubia" deleted
```

물론 관리하는 pod들도 같이 사라진다.

## 4.4. Running exactly one pod on each node with daemonsets


### Using a DaemonSet to run a pod on every node

DaemonSet object를 만들면 ReplicationController나 ReplicaSet과 흡사하지만 각각의 노드에 하나의 pod를 띄워준다.

### Using a DaemonSet to run pods only on certain nodes

pod template의 `node-Selector` property를 사용해서 pod를 띄울 특정 node를 선택할 수 있다.

ssd 노드에서만 작동하는 `ssd-monitor` daemon을 만든다고 상상해보자. SSD를 가지고 있는 노드에서만 작동하는 DaemonSet을 만들고 SSD가 있는 노드에는 `disk=ssd` 라벨을 붙여준다.

5초마다 "SSD OK"를 출력하는 DaemonSet을 만들어보자. 저자가 이미 Docker Hub에 해당 이미지를 배포해 놓았기 때문ㅇ에 `ssd-monitor-daemonset.yaml`만 추가해보자.

```
apiVersion: apps/v1beta2           1
kind: DaemonSet                    1
metadata:
  name: ssd-monitor
spec:
  selector:
    matchLabels:
      app: ssd-monitor
  template:
    metadata:
      labels:
        app: ssd-monitor
    spec:
      nodeSelector:                2
        disk: ssd                  2
      containers:
      - name: main
        image: luksa/ssd-monitor
```

- 2: pod template은 node selector 옵션을 포함하고, 이는 `disk=ssd` 라벨이 있는 노드에서만 pod를 띄워준다.

> DaemonSet object도 이제 `apps/v1` 버전에서 작동한다. 위 예시에서 apiVersion을 변경해야 작동한다.

```
$ kubectl create -f ssd-monitor-daemonset.yaml
daemonset.apps/ssd-monitor created

$ kubectl get ds
NAME          DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
ssd-monitor   0         0         0       0            0           disk=ssd        42s

$ kubectl get po
No resources found.
```

아직 node에 `disk=ssd` 라벨을 붙이지 않았기 때문에 아무것도 나오지 않는다. node에 라벨을 붙여보자.

```
$ kubectl get node
NAME   STATUS     ROLES    AGE   VERSION
m01    NotReady   master   31d   v1.17.3

$ kubectl label node m01 disk=ssd
node/m01 labeled

$ kubectl get op
NAME                READY     STATUS    RESTARTS   AGE
ssd-monitor-hgxwq   1/1       Running   0          35s
```

이제 node의 라벨을 변경하면 pod가 내려가는 것을 볼 수 있다.

```
$ kubectl label node m01 disk=hdd --overwrite
node/m01 labeled

$ kubectl get po
NAME                READY     STATUS        RESTARTS   AGE
ssd-monitor-hgxwq   1/1       Terminating   0          35s
```

## 4.5. Running pods  that perform a single completable task

지금까지는 지속적으로 동작하는 pod에 대해서만 얘기했다. ReplicationController, ReplicaSets, DaemonSet은 모두 지속적이고 완료되지 않는 태스크들을 다룬다. 하지만 완료 가능한 태스크 (completable task)에서는 프로세스가 종료되면 다시 시작될 필요가 없다.

### Introducing the Job resource

Kubernetes는 Job resource를 지원한다; 이는 pod 안의 process가 완료되면 contariner가 다시 시작하지 않게 해 준다.

이런 job의 예시는 어딘가에 저장된 data를 transform하여 다른 곳으로 export 할 때 등에 쓰인다.

> ETL 프로세스같이 1회성 작업을 하는 태스크들은 Job을 사용하여 띄우면 좋을듯하다.

`busybox`라는, 2분이 지나면 `sleep` command를 시랭시키는 이미지를 띄우는 컨테이너를 만들어 볼 것이다. 이미 `luksa/batch-job`으로 저자가 Docker Hub에 올려두었기 때문에 따로 빌드할 필요는 없다.

### Defining a Job resource

`exporter.yaml` 파일을 만들어보자.

```
apiVersion: batch/v1                  1
kind: Job                             1
metadata:
  name: batch-job
spec:                                 2
  template:
    metadata:
      labels:                         2
        app: batch-job                2
    spec:
      restartPolicy: OnFailure        3
      containers:
      - name: main
        image: luksa/batch-job
```

- 1. Job은 batch API group, v1에 있다.
- 2. 따로 pod selector를 지정하지 않았다. (pod template의 라벨에 의해 생성됨)
- 3. Job은 default restart policy (Always)를 사용할 수 없다.

### Seeing a Job run a pod

`kubectl create` 명령어로 Job을 생성해보자.

```
$ kubectl create -f exporter.yaml
job.batch/batch-job created

$ kubectl get jobs
NAME        COMPLETIONS   DURATION   AGE
batch-job   0/1           7s         7s

$ kubectl get po
NAME              READY   STATUS    RESTARTS   AGE
batch-job-7n8dq   1/1     Running   0          16s
```

2분이 지나면 job의 COMPLETION이 변경되고 pod는 더 이상 나타나지 않는다. 기본으로 완료된 pod는 `--show-all`(`-a`) 옵션을 주지 않으면 따로 나타나지 않는다.

> 현재 (2020-05) 기준으로는 `--show-all` 옵션은 deprecated 되었고 그냥 `kubectl get`을 사용하여도 complated 된 pod가 나온다.

```
$ kubecto get po
NAME              READY   STATUS      RESTARTS   AGE
batch-job-7n8dq   0/1     Completed   0          2m21s
```

실제로 해당 job이 어떻게 작동했는지 `logs`를 사용하여 확인할 수 있다.

```
$ kubectl logs batch-job-7n8dq
Sat May 23 14:30:41 UTC 2020 Batch job starting
Sat May 23 14:32:41 UTC 2020 Finished succesfully
```

실제로 job도 완료 된 것을 볼 수 있다.

```
NAME        COMPLETIONS   DURATION   AGE
batch-job   1/1           2m7s       3m9s
```

### Running multiple pod instances in a job

Job은 여러 pod instance를 병렬, 혹은 연속적으로 생성하게 설정 가능하다.

#### Running job pods sequentially

`completions`를 설정하면 Job의 pod가 몇 번 실행될 것인지 지정할 수 있다.

`multi-completion-batch-job.yaml`을 만들어보자.

```
apiVersion: batch/v1
kind: Job
metadata:
  name: multi-completion-batch-job
spec:
  completions: 5                                1
  template:
    <template is the same as in listing 4.11>
```

위의 Job은 다섯개의 pod를 순차적으로 실행시킬 것이다. 하나의 pod가 실패하면 새로운 pod를 만들고, 총 5개의 pod가 실행될 때까지 계속될 것이다.

#### Running job pods in parallel

Job pods가 하나씩 실행되게 하는 대신에 병렬로 실행되게 할 수 있다. `parallelism` 옵션으로 가능

`multi-completion-parallel-batch-job.yaml`

```
apiVersion: batch/v1
kind: Job
metadata:
  name: multi-completion-batch-job
spec:
  completions: 5
  parallelism: 2
  template:
    <same as in listing 4.11>
```

`parallelism`이 2로 설정되었기 때문에 2개의 pod가 병렬로 실행된다.


```
$ kubectl create -f multi-completion-parallel-batch-job.yaml
job.batch/multi-completion-batch-job created

$ kubectl get jobs
NAME                         COMPLETIONS   DURATION   AGE
batch-job                    1/1           2m7s       11m
multi-completion-batch-job   0/5           21s        21s

$ kubectl get po
NAME                               READY   STATUS      RESTARTS   AGE
batch-job-7n8dq                    0/1     Completed   0          11m
multi-completion-batch-job-5s4dc   1/1     Running     0          31s
multi-completion-batch-job-qt8qw   1/1     Running     0          31s
```

두개가 병렬적으로 실행되고 있는 것을 볼 수 있다.

#### Scaling a job

Job이 실행되는 도중에도 `parallelism`을 변경할 수 있다.

`kubectl scale` 명령어를 사용해보자.

```
$ kubectl scale job multi-completion-batch-job --replicas 3
```

> `kubectl scale job`은 더 이상 사용하지 못하는 듯 하다.


### Limiting the time allowed for a Job pod to complete

`activeDeadlineSeconds` property를 사용하여 job이 실행시킬 수 있는 pod의 time limit을 걸 수 있다.

> `spec.backoffLimit`을 사용하여 얼마나 retried 될 것인지도 정할 수 있다. default는 6


## 4.6. Scheduling Jobs to run periodically or once in the future


Job resource는 생성되자마자 즉시 pod를 실행시킨다. 하지만 이들을 미래 특정 시간에 실행시키거나 반복적으로 실행시킬 일이 있을 수 있다.

Kubernetes의 cron job은 `CronJob` resource로 생성 가능하다.

### Creating a CronJob

15분마다 작동하는 batch job이 필요하다고 상상해보자.

`cronjob.yaml`을 만들고 CronJob resource를 만들어보자

```
apiVersion: batch/v1beta1                  1

kind: CronJob
metadata:
  name: batch-job-every-fifteen-minutes
spec:
  schedule: "0,15,30,45 * * * *"           2
  jobTemplate:
    spec:
      template:                            3
        metadata:                          3
          labels:                          3
            app: periodic-batch-job        3
        spec:                              3
          restartPolicy: OnFailure         3
          containers:                      3
          - name: main                     3
            image: luksa/batch-job         3
```

- 2: `spec.schedule`로 일반 형태의 cron schedule을 추가하면 된다
- 3. CronJob이 생성할 pod template

```
$ kubectl create -f cronjob.yaml
cronjob.batch/batch-job-every-fifteen-minutes created

$ kubectl get cj
NAME                              SCHEDULE             SUSPEND   ACTIVE   LAST SCHEDULE   AGE
batch-job-every-fifteen-minutes   0,15,30,45 * * * *   False     0        <none>          15s
```

### Understanding how scheduled jobs are run

Job이나 pod가 생성되었지만 늦게 실행되는 경우가 있을 수 있다. 이런 경우를 위해서 `startingDeadlineSeconds`를 추가할 수 있다.

```
apiVersion: batch/v1beta1
kind: CronJob
spec:
  schedule: "0,15,30,45 * * * *"
  startingDeadlineSeconds: 15           1
  ...
```

이 경우 15초가 지날때 까지 실행이 되지 않으면 실패한 것으로 간주된다.

