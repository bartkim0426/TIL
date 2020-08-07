## 2. First steps with Docker and Kubernetes

- 도커에 대한 기본적인 설명. 도커 사용법을 안다면 생략해도 될듯하다.

- kubenertes 띄우기: minikube 설치 및 사용법에 대한 설명
- minikube나 GKE(Google Kubernetes Engine)으로 설명이 되어있다.
- GKE는 [GKE Quickstart](https://cloud.google.com/kubernetes-engine/docs/quickstart)를 따라하면 됨.

> google은 12 month free trial를 제공하지만 이미 사용하였을 경우에는 금액이 청구된다.

이후에 kubernetes 명령어에 사용할 docker image를 빌드해서 push 해주어야한다

`app.js`에 다음 내용을 추가

```
const http = require('http');
const os = require('os');

console.log("Kubia server starting...");

var handler = function(request, response) {
  console.log("Received request from " + request.connection.remoteAddress);
  response.writeHead(200);
  response.end("You've hit " + os.hostname() + "\n");
};

var www = http.createServer(handler);
www.listen(8080);
```

`Dockerfile` 작성

```
FROM node:7
ADD app.js /app.js
ENTRYPOINT ["node", "app.js"]
```

build & push

```
docker build -t kubia .
docker tag kubie <dockerhub_username>/kubia
docker push <dockerhub_username>/kubia
```


### kubectl alias 만들기

- `kubectl` 명령어를 자주 사용하게 될 것이므로 alias 만들기를 권장함
- 다음을 `~/.bashrc`나 `~/.zshrc`에 추가
 
```
# 나는 개인적을 kc를 사용한다.
alias k=kubectl
```

- tab completion을 지원하기 때문에 일일히 칠 필요 없다.

```
$ source <(kubectl completion bash)
# kc (k) alias에서도 사용하려면 다음 명령어 실행
$ source <(kubectl completion bash | sed s/kubectl/k/g)
```

### Running your first app on kubernetes

`Node.js` 앱을 배포

- JSON, YAML 파일 없이 배포하는 가장 쉬운 방법은 `kubectl run` 명령어를 사용하는 것
- 아까 올린 `<dockerhub_username>/kubia` 이미지를 사용해 kubectl을 띄워보자

```
kubetl run kubia --image=bartkim0426/kubia --port=8080 --generator=run/v1
```

> `--generator` 옵션으로 pod 대신 `ReplicationController`를 띄운다고 적혀있지만 `kubectl run`은 이제 Pods만 띄울 수 있다.
> `--generator` 옵션은 모두 deprecated 되었지만 본문에 나와있어 적어둔다.


#### Introducing pods

- kubernetes는 각각의 container로 작동하지 않는다
- 대신 multiple co-located container 사용
- 이 그룹이 "Pod"
- Pod는 같은 Linux namespace와 같은 워커 노드에서 함께 실행되는 한개나 그 이상의 밀접하게 관련된 컨테이너들의 그룹

```
kubectl get pods

# 더 상세한 정보는 describe 명령어 사용
kubectl describe pod
```

[!image](https://learning.oreilly.com/library/view/kubernetes-in-action/9781617293726/02fig06_alt.jpg)

#### Application에 접근하기

- pod는 각각의 IP 주소를 가지고 있찌만 이는 클러스터의 internal address라서 외부에서 접속이 불가하다.
- 외부에서 접속할 수 있게 하려면 이를 Service obejct를 통해 외부로 expose 하여야 한다.
- `LoadBalancer` 타입의 서비스를 사용할 것인데, `ClusterIP service`같은 일반적인 서비스로는 cluster 안에서만 접근이 가능하기 때문

```
kubectl expose rc kubia --type=LoadBalancer --name kubia-http

# rc(ReplicationController)는 deprecated 되었으므로 pod로 변경해서 실행시켜야한다.
kubectl expose pod kubia --type=LoadBalancer --name kubia-http

kubectl get services
NAME                  TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
kubernetes            ClusterIP      10.96.0.1       <none>        443/TCP          21d
kubia-http            LoadBalancer   10.108.174.19   <pending>     8080:31555/TCP   47s
```

external ip가 나올때까지는 시간이 조금 걸린다. Load balancer가 실행되면 external IP 주소가 나오고 해당 주소로 접근이 가능하다.

> Minikube는 LoadBalancer 서비스를 제공하지 않기 때문에 external IP가 나오지 않는다. 이를 external port를 사용하여 접근이 가능

```
# minikube의 경우
minikube service kuiba-http
```

#### Logical parts of your system

- 지금까지는 시스템의 실제 physical components에 대한 설명
  - worker nodes (VMs running Docker and the Kubelet), master node (control whole system)
- physical view 말고 전혀 별개의 logical view가 있음
  - Pod, ReplicationController, Service 등
  - container를 직접 접근하는 대신 kubernetes의 pod 사용
- Service
  - service를 이해하기 위해서는 pod에 대해 알아야한다
  - pod는 "ephemeral" (덧없는, 단명하는)
  - pod는 여러 가지 이유로(에러, 삭제 등) 언제든지 사라질 수 있다
  - pod가 사라지면 새로운 pod로 대체되고 새로운 IP 주소를 갖는다
  - 매번 변경되는 IP 주소의 문제를 해결하기 위해 다양한 pod들을 하나의 constant IP와 포트로 expose

#### Horizontally scaling the application

- Kubernetes의 가장 주요 이점 중 하나는 배포한 것을 쉽게 scaling 할 수 있는 것이다.

```
kubectl scale rc kubia --replicas=3
```

> ReplicationController가 deprecated 되었기 때문에 위 명령어는 더이상 작동하지 않는다. 실제 scaling을 위해서는 앞으로 나오는 deployment를 사용하여야 한다. 그래서 관련 설명은 그냥 이하 생략함

#### Examining what noddes your app is running on

- 어떤 노드에 pods들이 스케쥴 되어있는지 궁금할 수 있다. k8s에서 딱히 중요하진 않지만 CPU나 메모리 때문에 이를 보아야 할 경우가 종종 있다.
  - `kubectl get pods` 명령어는 어떤 노드에서 스케쥴링되는지 보여주지도 않음
  - `-o wide` 옵션으로 추가적인 칼럼 볼 수있다
  - `kubectl describe` 명령어로도 볼 수 있다

```
kubectl get pods -o wide
NAME                                 READY   STATUS    RESTARTS   AGE     IP           NODE   NOMINATED NODE   READINESS GATES
kubia                                1/1     Running   0          23h     172.17.0.4   m01    <none>           <none>
```

### Introducing the Kubernetes dashboard

> 책에서는 GKE (Google Kubernetes Engine)을 쓴다고 가정하고 설명한다.

```
$ kubectl cluster-info | grep dashboard

# username, password는 아래 명령어로
$ gcloud container clusters describe kubia | grep -E "(username|password):"

# 미니쿠베에서는 아래 명령어로 dashboard 띄울 수 있음
minikube dashboard
```


