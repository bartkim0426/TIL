# Ch 5. Services: enabling clients to discover and talk to pods

이 챕터는 다음 내용을 다룬다.

- Service 리소스를 만들고 한 주소로 pod 그룹을 expose
- 클러스터 안의 services를 expose
- 외부 서비스를 클러스터 안의 service와 연결
- service의 부분으로 pod가 준비되게 컨트롤
- Troubleshooting service

요즘의 많은 애플리케이션들은 외부 요청에 응답하게 만들어진다.

kubernetes가 아닌 상황에서는 sysadmin이 클라이언트 앱에 각각의 IP주소나 hostname을 부여하지만, Kubernetes에서는 이런 방식으로 작동하지 않는다.
- Pod는 ephemeral (덧없다, 단명한다): 언제든지 생기고 사라질 수 있다
- Kubernetes는 pod가 시작하기 전에, 그리고 pod가 노드에 스케쥴된 이후에 pod의 IP 주소를 부여한다. 따라서 client는 pod의 IP 주소를 찾을 수 없다
- 수평적 스케일링은 여러개의 pod들이 같은 서비스를 제공하는 것을 의미한다: 각각의 pod들은 서로 다른 IP 주소를 갖는데 클라이언트는 이들을알 필요가 없다. 대신, 모든 pod들은 하나의 IP 주소를 통해 접근 가능해야 한다.

이런 문제를 해결하기 위해 Kubernetes는 `Service`라는 resource type을 제공한다.


## 5.1. Introducing Services

Kubernetes Service는 같은 서비스를 제공하는 pod의 그룹에 하나의 지속적인 접속점을 제공해주는 resource이다. 각각의 service는 존재하는 동안 절대 변하지 않는 IP주소와 port를 가진다.

클라이언트는 해당 주소로 pod 그룹 중 하나에 접근 가능하고, 개별 pod의 주소에는 신경을 쓸 필요가 없기 때문에 pod가 언제든지 옮겨질 수 있게 된다.

### Creating services

service에서 load-balance 할 특정 pod는 이전에서 봤던 것과 비슷하게 `label selector`를 사용하여 정한다.

가장 쉽게 service를 만드는 방법은 `kubectl expose` 명령어를 사용하는 것이다. `expose` 명령어는 같은 pod selector를 기반으로 Service 리소스를 만들고 하나의 IP 주소와 포트로 노출시켜준다

이번에는 `expose` 명령어 대신 YAML 파일로 Kubernetes API를 사용하여 service를 직접 만들어보자.

`kubia-svc.yaml`에 다음 내용을 추가

```
apiVersion: v1
kind: Service
metadata:
  name: kubia
spec:
  ports:
  - port: 80
    targetPort: 8080
  selector:
    app: kubia
```

`kubia`라는 service를 만들어 80 port를 받게 하고, `app=kubia` label selector를 가진 pod들에게 8080 포트로 라우팅 해준다는 것을 의미

```
$ kubectl create --f kubia-svc.yaml
service/kubia created

$ kubectl get svc
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP   8d
kubia        ClusterIP   10.97.60.204   <none>        80/TCP    7s
```

위의 IP는 cluster IP이기 때문에 cluster 내부에서만 접속 가능하다. 외부 노출은 조금 이따가 다룰 것이다.

클러스터 안에서 서비스에 요청을 보내는 데에는 몇 가지 방법이 있는데
- 해당 cluster ip에 request를 보내는 pod를 만드는 것
- kubernetes node에 ssh로 접속 후에 curl 명령어 사용
- kubectl exec 명령어로 존재하는 pod에 접속 후 curl 명령어 사용

마지막 옵션을 사용하여 어떻게 존재하는 pod에서 명령어를 실행시키는지도 배워보자.

`kubectl exec` 명령어를 사용하면 pod 안의 container에 자유롭게 명령어를 보낼 수 있다.

`kubectl get pods` 명령어로 나온 pod중 타겟 pod를 선택한 다음 `exec` 명령어를 실행시켜보자.

> `export POD_NAME=$(kubectl get pod -l app=<app_label> -o jsonpath="{.items[0].metadata.name}")`과 같이 변수로 등록시켜서 해도 편리하다.

```
$ kubectl get pods
NAME                                               READY   STATUS      RESTARTS   AGE
kubia-frqgr                                        1/1     Running     0          18s
kubia-t7c5f                                        1/1     Running     0          18s
kubia-zdzn9                                        1/1     Running     0          18s

# 첫 번 째 pod를 골라서 exec 명령어를 실행시켰다.
$ kubectl exec kubia-frqgr -- curl -s http://10.97.60.204
You've hit kubia-t7c5f
```

> double dash (`--`)는 무엇일까? `--` 이후의 모든 명령어는 pod 안에서 실행된다고 명시. argument가 없으면 따로 적지 않아도 되지만 위에서는 `-s` 인자가 있기 때문에 `--`가 없다면 `exec`의 인자로 인식하여 오류가 난다.

service proxy가 랜덤으로 pod에 포워딩하기 때문에 만약 명령어를 여러 번 실행한다면 다른 pod가 출력될 것이다.

만약 특정 클라이언트가 매번 같은 pod에 요청을 보내고 싶다면 `sessionAffinity` property를 `ClientIP`로 설정하면 된다. (default는 None)

kubernetes는 `sessionAffinity`로 `None`과 `ClientIP`만 제공

```
apiVersion: v1
kind: Service
spec:
  sessionAffinity: ClientIP
  ...
```

> 실제 수정해보려면 `kubectl edit -f kubia-svc.yaml`로 바로 수정 가능하다.

#### Exposing multiple ports in the same service

```
apiVersion: v1
kind: Service
metadata:
  name: kubia
spec:
  ports:
  - name: http              1
    port: 80                1
    targetPort: 8080        1
  - name: https             2
    port: 443               2
    targetPort: 8443        2
  selector:                 3
    app: kubia              3
```

여러 port들에게 각각 port name을 줄 수 있는데, 이렇게 사용하면 port number를 변경하더라고 service spec을 따로 변경할 필요가 없다. (helm 배포가 필요 없어짐)

### Discovering services

kubernetes에서는 client pod가 service의 IP와 port를 발견할 수 있는 방법을 제공

pod가 실행되면 해당 시점에 존재하는 service를 환경 변수로 갖는다.

이를 확인하기 위해 기존 pod를 지워보자

```
$ kubectl delete po --all
...
```

그러면 현재 존재하는 replicaSet이 다시 pod를 띄울 것이다. 새롭게 뜬 pod의 env를 확인해보자.

```
$ kubectl exec kubia-766tj env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=kubia-766tj
KUBERNETES_PORT_443_TCP_PORT=443
KUBIA_SERVICE_HOST=10.100.198.79
KUBIA_PORT_443_TCP_PROTO=tcp
KUBIA_PORT_443_TCP_ADDR=10.100.198.79
...
```

`kubia` service의 post와 port를 볼 수 있다.

환경변수는 service의 IP와 port를 찾는 방법 중 하나이다. 하지만 보통은 DNS domain을 사용하는데 왜 Kubernetes는 이를 사용하지 않는 것일까? 사실은 사용한다!

#### Discovering services through DNS

3장에서 우리는 `kube-system` namespace의 pod를 리스팅 하여 그 중 `kube-dns`를 보았다.

> `kubectl get po --namespace kube-system`으로 다시 확인 가능하다.

```
$ kubectl exec -it kubia-766tj -- bash
root@kubia-766tj:/# curl http://kubia.default.svc.cluster.local
You've hit kubia-qtfdt

root@kubia-766tj:/# curl http://kubia.default
You've hit kubia-qtfdt

root@kubia-766tj:/# curl http://kubia
You've hit kubia-qtfdt

root@kubia-766tj:/# cat /etc/resolv.conf
nameserver 10.96.0.10
search default.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```
## 5.2. Connecting to service living outside the cluster

### Introducing service endpoints

service는 사실 pod와 직접 링크되는게 아니라 Endpoint resource가 중간에 존재한다.

```
$ kubectl describe svc kubia
Name:              kubia
Namespace:         default
Labels:            <none>
Annotations:       <none>
Selector:          app=kubia
Type:              ClusterIP
IP:                10.100.198.79
Port:              http  80/TCP
TargetPort:        8080/TCP
Endpoints:         172.17.0.7:8080,172.17.0.8:8080,172.17.0.9:8080
Port:              https  443/TCP
TargetPort:        8443/TCP
Endpoints:         172.17.0.7:8443,172.17.0.8:8443,172.17.0.9:8443
Session Affinity:  ClientIP
```

다른 resource 처럼 `kubectl get`으로 볼 수 있음

```
$ kubectl get endpoint kubia
NAME    ENDPOINTS                                                     AGE
kubia   172.17.0.7:8443,172.17.0.8:8443,172.17.0.9:8443 + 3 more...   22h
```

### Manually configuring service endpoints

service와 endpoint를 모두 직접 만들어야 수동으로 endpoints를 관리하는 service를 생성 가능하다.

`external-service.yaml`

```
apiVersion: v1
kind: Service
metadata:
  name: external-service          1
spec:                             2
  ports:
  - port: 80
```
80포트로 열린 `external-service`라는 service를 만들었지만 따로 pod selector를 지정하지 않았기 때문에 endpoint가 자동으로 만들어지지 않는다.

`external-service-endpoints.yaml`


```
apiVersion: v1
kind: Endpoints
metadata:
  name: external-service      1
subsets:
  - addresses:
    - ip: 11.11.11.11         2
    - ip: 22.22.22.22         2
    ports:
    - port: 80                3
```


## 5.3. Exposing services to external clients

지금까지는 클러스터 안의 pod들이 어떻게 service에 접근하는지를 다뤘다. 하지만 frontend webserver와 같이 외부에 노출이 필요한 서비스들이 존재한다. (사실 내가 다루는 대부분의 서비스...)

여러 가지 방법으로 외부에서 접근 가능한 서비스를 만들 수 있따.
- `service type`을 `NodePort`를 설정: 각 클러스터의 노드는 자신의 포트를 열어 traffic을 service로 리다이렉트
- `service type`을 `LoadBalance`로 설정 (extension of the NodePort type):
- `Ingress` 리소스 생성(radically different mechanism for exposing multiple services through a single IP address)

### Using a NodePort service

`kubia-svc-nodeport.yaml`

```
apiVersion: v1
kind: Service
metadata:
  name: kubia-nodeport
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: 8080
    nodePort: 30123
  selector:
    app: kubia
```

> 다음 예시는 GKE를 기준으로 설명한 것. minikube의 경우 `minikube service <serevice_name>`로 그냥 띄울 수 있다.

```
$ kubectl create -f kubia-svc-nodeport.yaml
service/kubia-nodeport created

$ kubectl get svc kubia-nodeport
NAME             TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubia-nodeport   NodePort   172.20.22.132   <none>        80:30123/TCP   13s
```

> node의 IP를 보는방법

```
$ kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="ExternalIP")].address}'
35.167.185.8 52.25.169.64
```

이제 30123 포트로 어떤 노드로든지 접근이 가능하다. 하지만 첫 번째 노드가 실패하면 클라이언트는 더 이상 service에 접근이 불가능하다. 그래서 node의 앞단에 load balancer를 배치하여 healthy node에만 request를 보내야 하는 이유

### Exposing a servic through an external load balancer

대부분의 cloud infrastructure의 kubernetes는 automatic provision을 제공하기 때문에 `NodePort` 대신 `LoadBalance`를 사용하기만 하면 된다.

> minikube는 `LoadBalance`를 제공하지 않는다. 아래 예시는 GKE 예시.

`kubia-svc-load_balancer.yaml`

```
apiVersion: v1
kind: Service
metadata:
  name: kubia-loadbalancer
spec:
  type: LoadBalancer                1
  ports:
  - port: 80
    targetPort: 8080
  selector:
    app: kubia
```

```
$ kubectl get svc kubia-loadbalancer
NAME                 CLUSTER-IP       EXTERNAL-IP      PORT(S)         AGE
kubia-loadbalancer   10.111.241.153   130.211.53.173   80:32143/TCP    1m

$ curl http://130.211.53.173
You've hit kubia-xueq1
```

### Understading thee peculiarities of external connections

외부 클라이언트가 node port를 통해 service에 접속하면 랜덤으로 선택된 pod가 connection을 받는데 이는 같은 노드에 있을수도, 그렇지 않을수도 있다. 그렇지 않을 경우 additional network hop이 발생한다.

service의 설정에서 외부 트래픽의 connection을 받은 node가 해당 노드의 pod에만 redirect되게 가능하다.

```
spec:
  externalTrafficPolicy: Local
  ...
```

문제점도 있다.

- 이 경우 local pod가 선택되는데 local pod가 없으면 커넥션이 global pod로 포워드되지 않고 hang됨
- 커넥션이 균등하게 분배되지 않음 (노드 2개에 pod가 1개, 2개 있는 경우 각 포드는 50%, 25%, 25%로 나뉘게 될것)


## 5.4 Exposing services externally through an Ingress resource

> Ingress (noun)—The act of going in or entering; the right to enter; a means or place of entering; entryway.

#### Understanding why Ingresses are needed

`LoadBalancer` service는 각각의 load balancer마다 각자의 IP 주소가 있어야 하지만, `Ingress`는 여러 서비스에 한개의 IP 주소만 필요하다.

client가 HTTP 요청을 Ingress에게 보내면 request의 host와 path로 어떤 서비스로 포워딩할지를 결정한다.

#### Understanding that an Ingress controller is required

Ingress object를 사용하려면 Ingress controller가 필요하다.

GKE는 GCP의 HTTP 로드밸런싱 기능을 사용한다. Minikube의 경우에는 따로 controller를 제공하지 않지만 add-on을 사용하여 Ingress controller를 사용할 수 있다.


#### Enabling the Ingress add-on in Minikube

```
$ minikube addons list
|-----------------------------|----------|--------------|
|         ADDON NAME          | PROFILE  |    STATUS    |
|-----------------------------|----------|--------------|
| dashboard                   | minikube | disabled     |
| default-storageclass        | minikube | enabled ✅   |
| efk                         | minikube | disabled     |
| freshpod                    | minikube | disabled     |
| gvisor                      | minikube | disabled     |
| helm-tiller                 | minikube | disabled     |
| ingress                     | minikube | disabled     |
| ingress-dns                 | minikube | disabled     |
| istio                       | minikube | disabled     |
| istio-provisioner           | minikube | disabled     |
| logviewer                   | minikube | disabled     |
| metrics-server              | minikube | disabled     |
| nvidia-driver-installer     | minikube | disabled     |
| nvidia-gpu-device-plugin    | minikube | disabled     |
| registry                    | minikube | disabled     |
| registry-creds              | minikube | disabled     |
| storage-provisioner         | minikube | enabled ✅   |
| storage-provisioner-gluster | minikube | disabled     |
|-----------------------------|----------|--------------|
```

위에 `ingress`가 `disabled`인 것을 볼 수 있다.

```
$ minikube addons enable ingress
🌟  The 'ingress' addon is enabled

$ minikube addons list | grep ingress
| ingress                     | minikube | enabled ✅   |
| ingress-dns                 | minikube | disabled     |
```

```
$ kubectl get po --all-namespaces
NAMESPACE     NAME                                               READY   STATUS             RESTARTS   AGE
default       kubia-766tj                                        1/1     Running            0          47h
default       kubia-qtfdt                                        1/1     Running            0          47h
default       kubia-zphvm                                        1/1     Running            0          47h
kube-system   coredns-6955765f44-9swsw                           1/1     Running            0          6d9h
kube-system   coredns-6955765f44-gllc6                           1/1     Running            0          6d9h
kube-system   etcd-m01                                           1/1     Running            4          36d
kube-system   kube-apiserver-m01                                 1/1     Running            4          36d
kube-system   kube-controller-manager-m01                        1/1     Running            28         36d
kube-system   kube-proxy-9558l                                   1/1     Running            4          36d
kube-system   kube-scheduler-m01                                 1/1     Running            29         36d
kube-system   nginx-ingress-controller-6fc5bcc8c9-zj58m          1/1     Running            0          53s
```

### Creating an Ingress resource

`kubia-ingress.yaml`

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: kubia
spec:
  rules:
  - host: kubia.example.com
    http:
      paths:
      - path: /
        backend:
          serviceName: kubia-nodeport
          servicePort: 80
```

`kubia.example.com`으로 request가 들어오면 `kubia-nodeport` 서비스의 `80` 포트로 보낸다.

### Accessing the service through the ingress

```
$ kubectl create -f kubia-ingress.yaml
ingress.extensions/kubia created

$ kubectl get ingresses
NAME    HOSTS               ADDRESS   PORTS   AGE
kubia   kubia.example.com             80      14s
```

> GKE의 경우에는 `ADDRESS`에 나온 결과를 사용하면 되고, minikube는 `minikube service kubia-nodeport --url`의 address를 사용하면 된다.

`/etc/hosts` (window의 경우에는 `C:\windows\system32\drivers\etc\hosts`)에 DNS를 수정해주어야한다.

```
192.168.64.4    kubia.example.com
```

이제 curl로 호출이 가능하다.

```
$ curl http://kubia.example.com
You've hit kubia-zphvm
```

![image](https://learning.oreilly.com/library/view/kubernetes-in-action/9781617293726/05fig10_alt.jpg)


### Exposing multiple servies through the same Imgress

`Ingress` resource를 잘 살펴보면 `rules`와 `paths`는 array로 되어 있어서 여러개를 담을 수 있다.

우선 여러 개의 `paths`를 가져 같은 호스트에 다른 서비스를 추가할 수 있다.

```
...
  - host: kubia.example.com
    http:
      paths:
      - path: /kubia                1
        backend:                    1
          serviceName: kubia        1
          servicePort: 80           1
      - path: /foo                  2
        backend:                    2
          serviceName: bar          2
          servicePort: 80           2
```

- 1: `kubia.example.com/kubia`로 들어오는 requets는 `kubia` 서비스로 라우트
- 2: `kubia.example.com/foo`로 들어오는 requets는 `bar` 서비스로 라우트


이와 비슷하게 path 대신 `host`를 기준으로 다른 서비스로 매핑시킬 수 있다.


```
spec:
  rules:
  - host: foo.example.com          1
    http:
      paths:
      - path: /
        backend:
          serviceName: foo         1
          servicePort: 80
  - host: bar.example.com          2
    http:
      paths:
      - path: /
        backend:
          serviceName: bar         2
          servicePort: 80
```


- 1 Requests for foo.example.com will be routed to service foo.
- 2 Requests for bar.example.com will be routed to service bar.


### Configuring Ingress to handle TLS traffic

pod가 web server를 돌리면 이는 HTTP traffic밖에 받지 못하고 TLS와 관련된 모든 것은 Ingress controller가 처리하게 한다. 이를 위해서는 Ingress에 certificate와 private key가 필요하다.

이는 Kubernetes의 `Secret`이라는 리소스에 저장된다. 자세한 내용은 7장에서 설명되기 때문에 여기서는 그냥 사용하는 방법만 보면됨

우선 private key와 certificate 생성

```
$ openssl genrsa -out tls.key 2048
$ openssl req -new -x509 -key tls.key -out tls.cert -days 360 -subj /CN=kubia.example.com

$ kubectl create secret tls tls-secret --cert=tls.cert --key=tls.key
secret/tls-secret created
```

private key와 certificate는 `tls-secret`이라는 secret resource에 저장되었다. 이제 Ingress를 HTTPS 리퀘스트를 받게 수정하자.

`kubia-ingress-tls.yaml`

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: kubia
spec:
  tls:
  - hosts: 
    - kubia.example.com
    secretName: tls-secret
  rules:
  - host: kubia.example.com
    http:
      paths:
      - path: /
        backend:
          serviceName: kubia-nodeport
          servicePort: 80
```

> 기존 Ingress를 지우고 다시 만드는 대신 `kubectl apply -f kubia-ingress-tls.yaml`로 기존 Ingress resource를 update 하여도 된다.

이제 HTTPS로 서비스 접속이 가능하다.

```
$ curl -k -v https://kubia.example.com/kubia
* About to connect() to kubia.example.com port 443 (#0)
...
* Server certificate:
*   subject: CN=kubia.example.com
...
> GET /kubia HTTP/1.1
> ...
You've hit kubia-xueq1
```

## 5.5. Signaling when a pod is ready to accept connections

### Introducing readiness probes

container의 readiness probe가 성공하면 해당 컨테이너는 request를 받을 준비가 된 것

liveness probe와는 다르게 readiness가 실패해도 pod가 제거되거나 다시 시작되지는 않는다.


### Adding a readiness probe to a pod

```
$ kubectl edit rc kubia
```


Add `spec.template.spec.containers.` in `kubia-rc-readnessprobe.yaml`

```
apiVersion: v1
kind: ReplicationController
metadata:
  name: kubia
spec:
  replicas: 3
  selector:
    app: kubia
  template:
    metadata:
      labels:
        app: kubia
    spec:
      containers:
      - name: kubia
        image: luksa/kubia
        ports:
        - name: http
          containerPort: 8080
        readinessProbe:
          exec:
            command:
            - ls
            - /var/ready
```

```
$ kubectl get po
NAME                                               READY   STATUS              RESTARTS   AGE
kubia-b2dgr                                        0/1     Running            0          32s
kubia-m7cff                                        0/1     Running            0          32s
kubia-nj6vj                                        0/1     Running            0          32s
```

준비될 때까지 `READY`가 활성화 되지 않는 것을 볼 수 있음

이제 `kubia-b2dgr`에만 `/var/ready` 파일을 만들어보자.

```
$ kubectl exec kubia-b2dgr -- touch /var/ready

$ kubectl get po
NAME                                               READY   STATUS              RESTARTS   AGE
kubia-b2dgr                                        1/1     Running            0          82s
kubia-m7cff                                        0/1     Running            0          82s
kubia-nj6vj                                        0/1     Running            0          82s
```

### Understanding what real-world readiness probes should do

아주 간단한 (HTTP request를 받는 서버) 앱이라도 readniess probe를 두어야 한다.

## 5.6. Using a headless service for discovering individual pods

지금까지는 service가 stable IP 주소를 제공하고, client가 endpoint를 통해서 랜덤으로 선택된 pod에 접근할 수 있는 것을 보았다.

하지만 만약 클라이언트가 모든 pod에 접근하고 싶다면? kubernetes는 이런 상황에 DNS lookup을 통해 pod IP들을 모두 알 수 있다. (`clusterIP` 필드를 `None`으로)

### Creating a headless service

service의 spec에서 `clusterIp` 필드를 `None`으로 만들면 서비스를 `headless`로 만들고, kubernetes는 클라이언트가 pod에 붙을 수 없도록 cluster IP를 부여하지 않는다.

`kubia-svc-headless.yaml`

```
apiVersion: v1
kind: Service
metadata:
  name: kubia-headless
spec:
  clusterIP: None
  ports:
  - port: 80
    targetPort: 8080
  selector:
    app: kubia
```

```
$ kubectl create -f kubia-svc-headless.yaml
service/kubia-headless created

# make pod avaliable
$ kubectl exec <pod name> -- touch /var/ready
```

### Discovering pods through DNS

pod가 준비되었으면 이제 DNS lookup을 사용하여 실제 pod의 IP를 확인할 수 있다.

하지만 pod 안의 컨테이너 이미지는 `nslookup`이나 `dig` 명령어를 포함하고 있지 않기 때문에 이를 포함하는 컨테이너 이미지 (`tutum/dnsutils`)를 사용하려면 다시 YAML manifest를 작성하고 `kubectl create`를 해야 한다.

다행히 더 쉬운 길이 있다. YAML manifest 없이 `kubectl run`을 사용하여 pod를 생성할 수 있다.

```
$ kubectl run dnsutils --image=tutum/dnsutils --generator=run-pod/v1 --command -- sleep infinity
pod/dnsutils created

$ kubectl get po -l run=dnsutils
NAME       READY   STATUS    RESTARTS   AGE
dnsutils   1/1     Running   0          34s

$ kubectl exec dnsutils nslookup kubia-headless
Server:         10.96.0.10
Address:        10.96.0.10#53

Name:   kubia-headless.default.svc.cluster.local
Address: 172.17.0.13
Name:   kubia-headless.default.svc.cluster.local
Address: 172.17.0.12

```


## 5.7. Troubleshooting services

pod에 접속이 되지 않으면 다음 리스트를 확인

- 먼저, 외부가 아니라 cluster 안에서 service의 클러스터 IP로 접근이 가능한지 확인
- service가 접속 가능한지 확인하기 위해 service IP에 ping을 날리지 말것 (virtual IP이기 때문에 pinging은 작동하지 않음)
- readiness probe를 정의했다면 작동하는지 확인
- `kubectl get endpoints`를 사용하여 pod가 service의 부분인지 확인
- service의 target port가 아니라 노출된 port로 연결을 시도하고 있는지 확인
- pod IP에 직접 접근하여 pod가 제대로 된 포트의 접근을 받고 있는지 확인
