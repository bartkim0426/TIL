# kubectl alpha debug command

Kubecon Europe 2020 세션에서 k8s new feature 설명을 듣다가 괜찮은 alpha 기능이 있어서 정리해본다.

kubernetes를 쓰면서 어렵다고 느낀 점 중 하나는 디버깅이다.

`logs`나 `describe pod`로 해당 pod의 상태/로그를 확인할 수 있고 `exec`로 pod에 실제 명령어를 실행시켜볼 수 있지만  어디까지나 해당 pod이 running중일 때의 얘기이고, pod 자체가 잘 뜨지 않거나 `exec` 명령어를 사용하기 힘든 상황일 경우에는 디버깅이 정말 어렵다. shell이나 디버깅 툴이 없는 컨테이너일 경우에도 마찬가지이다.

`kubectl alpha debug` 기능은 이럴 경우 pod 안에 디버깅에 필요한 툴들을 포함시킨 임시 컨테이너를 띄울 수 있게 해준다.

### 작동방법

`alpha debug`는 `Ephemeral container`(임시 컨테이너)를 사용한다. 아직 alpha이기 때문에 프로덕션 환경에서 사용을 권장하지는 않지만 어짜피 디버그 용도이니..

### 사전 조건

`Ephemeral container`는 기본적으로 1.16에서 나왔지만 `alpha debug`는 아쉽게도 kubernetes 1.18부터 지원한다고 한다. (kubectl도 1.18버전 이상이여야 한다)

> [임시 컨테이너 공식문서](https://kubernetes.io/ko/docs/concepts/workloads/pods/ephemeral-containers/)

```
$ kubectl version
```

그리고 `ephemeral container` 기능을 사용하겠다고 명시적으로 enable 해줘야한다.

> 아래의 예시부터는 minikube를 사용한 예시이다

```
$ minikube config set feature-gates EphemeralContainers=true

# config가 제대로 되었는지 확인
$ minikube config get feature-gates
EphemeralContainers=true
```

### Example

예시 pod를 만들어서 `alpha debug`를 사용해보자.

`crash.yaml` 파일을 만들고 다음 내용을 넣자. 실제로는 running 되지 않고 에러가 나게 된다.

```
apiVersion: v1
kind: Pod
metadata:
  name: simple
spec:
  containers:
  - image: alpine
    name: simple
    command:
    - "/bin/sh"
    - "-c"
    - "sleep 3 && whatisthis"
```

이제 해당 pod를 띄워보자.

```
$ kubectl apply -f crash.yaml
pod/crash created

$ kubectl get pods
NAME     READY   STATUS             RESTARTS   AGE
crash    0/1     CrashLoopBackOff   2          58s
```

logs 명령어는 가능하지만 container가 뜨지 않았기 떄문에 exec 명령어는 사용이 불가능하다.

```
$ kubectl logs crasdh
/bin/sh: whatisthis: not found

$ kubectl exec -it crash -- sh
error: unable to upgrade connection: container not found ("crash")
```

하지만 `alpha debug`를 사용하면 직접 해당 컨테이너에 들어가지 않고 base image를 변경하여 디버그용 컨테이너를 띄울 수 있다. (crash가 난 경우에는 `target` 없이 띄우면 된다)

```
$ kubectl alpha debug -it crash --image ubuntu --container=debug-no-target 
```

> ubuntu 이미지가 없기 떄문에 이를 받는데 시간이 오래 걸린다. 따로 이를 표시해주지 않기 때문에 조금 기다리자.


참고

- https://kubernetes.io/ko/docs/concepts/workloads/pods/ephemeral-containers/
- https://medium.com/icetek/new-in-kubernetes-1-18-kubectl-alpha-debug-2f5243581025
