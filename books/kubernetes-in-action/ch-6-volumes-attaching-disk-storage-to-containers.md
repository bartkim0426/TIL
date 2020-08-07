# Chapter 6. Volumes: attaching disk storage to containers

- Creating multi-container pods
- Creating a volume to share disk storage between containers
- Using a Git repository inside a pod
- Attaching persistent storage such as a GCE Persistent Disk to pods
- Using pre-provisioned persistent storage
- Dynamic provisioning of persistent storage

## 6.1. INTRODUCING VOLUMES

![image](https://learning.oreilly.com/library/view/kubernetes-in-action/9781617293726/06fig02.jpg)

## 6.2. Usnig volumes to share data between containers

### Using an emptyDir volume

가장 쉽게 사용할 수 있는 volume. 보통 containers 간의 파일 sharing에 주로 쓰이지만 single container에서 disk에 data를 적어야 할 일이 있을때에도 사용됨

위 장에서 본 예시 (webserver, contentAgent, LogRotator가 두 volume을 공유하는)를 직접 만들어보자

우선 UNIX `fortune` 커맨드를 사용하여 랜덤으로 HTLM 컨텐츠를 10초마다 만들어주도록 하자

`fortuneloop.sh`

```
#!/bin/bash
trap "exit" SIGINT
mkdir /var/htdocs
while :
do
  echo $(date) Writing fortune to /var/htdocs/index.html
  /usr/games/fortune > /var/htdocs/index.html
  sleep 10
done
```

dockerfile은 다음과 같이

```
FROM ubuntu:latest
RUN apt-get update ; apt-get -y install fortune
ADD fortuneloop.sh /bin/fortuneloop.sh
ENTRYPOINT /bin/fortuneloop.sh
```

이를 빌드하고 push해주자 (`luska`를 자신의 `username`로 변경)

```
$ docker build -t luksa/fortune .
$ docker push luksa/fortune
```

이제 push된 fortune 이미지를 기반으로 `fortune-pod.yaml`을 만들자 (`luska`대신 자신의 `username` 사용)

```
apiVersion: v1
kind: Pod
metadata:
  name: fortune
spec:
  containers:
  - image: luksa/fortune                   1
    name: html-generator                   1
    volumeMounts:                          2
    - name: html                           2
      mountPath: /var/htdocs               2
  - image: nginx:alpine                    3
    name: web-server                       3
    volumeMounts:                          4
    - name: html                           4
      mountPath: /usr/share/nginx/html     4
      readOnly: true                       4
    ports:
    - containerPort: 80
      protocol: TCP
  volumes:                                 5
  - name: html                             5
    emptyDir: {}                           5
```

- 1: 이미지 `luksa/fortune`을 사용하여 `html-generator` container 생성
- 2: `/var/htdocs`에 `html` volumne mount
- 3: 두 번 째 컨테이너는 `nginx:alphine` 이미지를 사용하여 `web-server`
- 4: 두번째 컨테이넌도 `htlm` 볼륨 사용하여 `/usr/share/nginx/html`에 마운트 (read-only)
- 5: html이라는 `emptyDir` 볼륨 생성

```
$ kubectl apply -f fortune-pod.yaml
pod/fortune created

$ kubectl port-forward fortune 8080:80

$ curl http://localhost:8080
```

### Using a Git repository as the starting point for a volume

`emptyDir`와 비슷하지만 GIT repository를 클론해서 해당 내용을 volume에서 사용

예를들면 static HTML 파일을 올려놓고 gitRepo volume을 사용해서 웹서버 컨테이너에서 서빙 가능.
다만 이런 경우 git에 코드를 수정한 경우 gitRepo에 변화를 푸시하기 위해 매번 pod를 삭제해야 한다는것이 단점

예시 Git repository는 https://github.com/luksa/kubia-website-example.git 

이후에 push하기 위해 이를 자기의 github로 fork해서 진행해보자.

그리고 `gitrepo-volume-pod.yaml` 생성

```
apiVersion: v1
kind: Pod
metadata:
  name: gitrepo-volume-pod
spec:
  containser:
  - image: nginx:alpine
    name: web-server
    volumeMounts:
    - name: html
      mountPath: /usr/share/nginx/html
      readOnly: true
    ports:
    - containerPort: 80
      protocol: TCP
  volumes:
  - name: html
    gitrepo: https://github.com/<username>/kubia-website-example.git
    revision: master
    directory: .
```

위의 `username` 대신 fork한 깃헙 계정을 넣어주자. `revision`은 가져올 브랜치 명이고, `directory`는 volume의 어디에 넣을지를 얘기 (`.`은 root)

위 pod를 돌리고 port forwarding을 해보면 깃헙에 올라간 html이 실제로 서빙이 되는 것을 볼 수 있다.

```
$ kubectl apply -f gitrepo-volume-pod.yaml
pod/gitrepo-volume-pod created

$ kubectl port-forward gitrepo-volume-pod 8080:80

# 다른 shell에서 실행하면 결과를 확인할 수 있다.
$ curl localhost:8080
<html>
<body>
Hello there.
</body>
</html>
```

이제 실제로 github에 올라간 파일을 변경해보자 (local에서 하지 못하겠다면 그냥 github에서 수정 가능)

`index.html`을 수정해도 그대로인 것을 볼 수 있다.  pod를 지우고 다시 만들면 변경사항이 적용되겠지만, 매번 변경할 때마다 pod를 다시 재시작 할 수 없기 때문에 volume을 Git repository에 sync하는 추가적인 프로세스를 실행하면 된다.

Git sync 프로세스는 같은 Nginx web server가 아닌 `sidecar` 컨테이너에 띄워야 한다.

> 자세한 내용은 18장에서 설명한다고 함. sidecar container가 private repository에 접근할 수 있어야 하기 때문. (언제 18장까지 다 읽을지...)

## 6.3. Accessing files on the worker node's filesystem

대부분의 pod는 host node를 염두에 두고 있지 않기 때문에 node의 파일시스템에 접근할 필요가 없다.

하지만 몇몇 특정한 system-level pod들은 node의 파일시스템에 접근하거나 파일을 읽을 필요가 있을 수 있는데, 이를 가능하게 해 주는게 kubernetes의 `hostPath` volume

### Introducing the hostPath volume

hostPath volumes are the first type of persistent storage we’re introducing

`hostpath` 볼륨을 database의 데이터 디렉토리로 사용할 것을 생각했다면 다시 생각해라.

pod가 어떤 노드에 스케줄링되는지에 민감해지기 때문 (database가 다른 node에 스케줄링될수도 있고)


### Examining system pods that use hostPath volumes

실제로 `hostPath` volume이 적절하게 사용되는 것을 보자. 새로운 포드 예시가 아니라 이미 system-wide로 존재하는 pod

```
$ kubectl get pod --namespace kube-system
NAME                          READY     STATUS    RESTARTS   AGE
fluentd-kubia-4ebc2f1e-9a3e   1/1       Running   1          4d
fluentd-kubia-4ebc2f1e-e2vz   1/1       Running   1          31d
...
```

> minikube의 경우에는 `kube-addon-manager-minikube`를 보면 되는데 `v1.8.2` 버전에서 해당 pod가 나오지를 않는다. 자료를 찾아봐도 딱히 발견을 못했는데 추후 발견시 추가할 예정


hostPath volume을 system 파일에 read/write 해야 할 때에만 사용하고 절대 pod간의 데이터를 저장하기 위해 사용하지 말것!

## 6.4. Using Persistent Storage

data를 지속시켜주는 volume을 배우기 위해 MongoDb document-oriented NOSQL db pod를 만들어 볼 것이다.

### 6.4.1. Using a GCE persistent Disk in a pod volume

만약 Google Kubernetes Engine을 사용하고 GEC 위에서 cluster 노드를 사용하고 있다면 `GCE Persistent Disk`를 사용할 수 있다.

이전 버전에서는 Kubernetes는 자동으로 underlying storage provisioning을 하지 않았지만 이제는 가능하다. (이는 다음 챕터에서 다루고 지금은 manually 하는 방법을 배울것)

> minikube의 경우 `GCE PD` 대신 `hostPath` volume을 사용해서 해볼 수 있다.


```
apiVersion: v1
kind: Pod
metadata:
  name: mongodb
spec:
  volumes:
  - name: mongodb-data           1
    gcePersistentDisk:           2
      pdName: mongodb            3
      fsType: ext4               4
  containers:
  - image: mongo
    name: mongodb
    volumeMounts:
    - name: mongodb-data         1
      mountPath: /data/db        5
    ports:
    - containerPort: 27017
      protocol: TCP
```

### 6.4.2. Using other types of volumes with underlying persistent storage

GCE Persistent Disk volume을 사용한 것은 Kubernetes가 GKE에서 돌기 때문

AWS (EKS)라면 `awsElasticBlockStore`를, Azure라면 `auzreFile`이나 `azureDisk`를 사용하면 된다.

책에서는 각각에 대해 자세한 예시를 설명하는데 이는 실제로 필요할 때 해당 문서를 보는 것이 나을듯하다.


## 6.5. Decoupling pods from the underlying storage technology

위에서 다룬 persistent volume은 개발자가 실제로 해당 network storage infra를 알아야 한다

이는 Kubernetes의 아이디어인 실제 infrastructure는 application이나 개발자에게서 숨기고 infra에 대한 걱정 없이 사용하게 하는 것과는 거리가 있다.

### 6.5.1 Introducing PersistentVolumes and [PersistentVolumeClaims](PersistentVolumeClaims)

![persistentVolumes](https://learning.oreilly.com/library/view/kubernetes-in-action/9781617293726/06fig06_alt.jpg)

실제 개발자가 기술 기반 volume을 pod에 추가하는게 아니라, cluster 관리자가 PersistentVolume 리소스를 만들어 놓는다. 그러면 사용자는 `PersistentVolumeClaims` manifest를 사용하여 적절한 volume을 사용 가능

PersistentVolume, PersistentVolumeClaims을 자세히 설명해 두었는데 요 부분은 추후에 사용할 일이 있으면 다시 보고 정리.

## 6.6. Dynamic provisioning of persistentVolumes

요부분도 마찬가지



