# 7. ConfigMaps and Secrets configuring applications

- Changing the main process of a container
- Passing command-line options to the app
- Setting environment variables exposed to the app
- Configuring apps through ConfigMaps
- Passing sensitive information through Secrets

거의 모든 app이 configuration을 필요로 하고 이를 app 자체에 담을 수 없으므로 kubernetes에서 어떻게 configuration option을 넘겨줄 수 있음

## 7.1. Configuring containerized application

configuration 부분을 건너 뛰면 명령줄의 인자로 이를 넘길 수 있음. configuration이 커질경우 그 때 config file로 만들어도 됨

또 다른 방법은 환경 변수를 사용하는 방법

왜 container 안에서 환경 변수를 사용하는 방법이 널리 사용될까? Docker container 안에서 configuration file을 사용하는게 까다롭기 때문
container image 안에 config file을 함께 넣을 수 없기 때문이다.

이런 문제를 해결하기 위해 Kubernetes의 `ConfigMap` 리소스를 사용 가능

`ConfigMa`을 사용하든 그렇지 않든 다음 방법으로 앱의 config를 수정할 수 있다.
- Passing command-line arguments to containers
- Setting custom environment variables for each container
- Mounting configuration files into containers through a special type of volume

다음 장들에서 위의 내용을 다루면서 각각의 특징을 살펴봄


## 7.2. Passing command-line arguments to containers

### Defining the command and arguments in Docker

컨테이너에서 실행되는 모든 명령어는 두 파트로 나뉜다: `command`와 `arguments`

Dockerfile에서는 이 두 가지로 정의된다

- `ENTRYPOINT`: container가 시작되면 실행
- `CMD`: `ENTRYPOINT`에 전달되는 arguemtns

`CMD`를 사용하여 이미지가 실행될 때 execute 시킬 수 있지만 올바른 방법은 `ENTRYPOINT`를 사용하고 기본 인자를 수정할 필요가 있을 때에만 `CMD`를 사용하는 것이다.

이전 장에서 사용한 fortune image를 config를 변경할 수 있게 수정해보자.

`fortune-args/fortuneloop.sh`

```
#!/bin/bash
trap "exit" SIGINT
INTERVAL=$1
echo Configured to generate new fortune every $INTERVAL seconds
mkdir -p /var/htdocs
while :
do
  echo $(date) Writing fortune to /var/htdocs/index.html
  /usr/games/fortune > /var/htdocs/index.html
  sleep $INTERVAL
done
```

마찬가지로 `fortune-args/Dockerfile`도 만들어주자.

```
FROM ubuntu:latest
RUN apt-get update ; apt-get -y install fortune
ADD fortuneloop.sh /bin/fortuneloop.sh
ENTRYPOINT ["/bin/fortuneloop.sh"]
CMD ["10"]
```

이제 해당 이미지를 run 해보자

```
$ docker build -t docker.io/<username>/fortune:args .
$ docker push docker.io/<username>/fortune:args

$ docker run -it docker.io/<username>/fortune:args
Configured to generate new fortune every 10 seconds
Mon Jun 8 12:04:37 UTC 2020 Writing fortune to /var/htdocs/index.html
```

기본 10으로 설정된 sleep interval을 변경할 수 있다.

```
$ docker run -it docker.io/<username>/fortune:args 15
Configured to generate new fortune every 15 seconds
```

### Overriding the command and arguments in Kubernetes

Kubernetes에서는 `ENTRYPOINT`와 `CMD`를 선택적으로 override 할 수 있다. (`command`와 `args` 사용)

은근 헷갈릴수 있을듯..
- `ENTRYPOINT` -> `command`
- `CMD` -> `args`

```
kind: Pod
spec:
  containers:
  - image: some/image
    command: ["/bin/command"]
    args: ["arg1", "arg2", "arg3"]
```


fortune pod도 argument를 받을 수 있게 수정해보자. `fortune-pod-args.yaml`

```
apiVersion: v1
kind: Pod
metadata:
  name: fortune2s                    1
spec:
  containers:
  - image: luksa/fortune:args        2
    args: ["2"]                      3
    name: html-generator
    volumeMounts:
    - name: html
      mountPath: /var/htdocs
...
```

## 7.3. Setting environment variables for a container

Kubernetes에서는 각각의 컨테이너에 환경변수를 지정해 줄 수 있다. 다만 아직 pod레벨에서 지정하는 방법은 없다.

환경변수로 위에서 만든 fortune image의 interval을 설정해보자. `fortune-env/fortuneloop.sh`


```
#!/bin/bash
trap "exit" SIGINT
echo Configured to generate new fortune every $INTERVAL seconds
mkdir -p /var/htdocs
while :
do
  echo $(date) Writing fortune to /var/htdocs/index.html
  /usr/games/fortune > /var/htdocs/index.html
  sleep $INTERVAL
done
```


위에서 바뀐건 `INTERVAL=$1` 라인을 제거한 것 뿐이다.

### Specifiying environment variables in a container definition

새로운 이미지를 빌드하고 푸시한 이후 `fortune-pod-env.yaml`을 만들어보자.

> 저자가 이미 올린 `luksa/fortune:env`를 사용해도 된다.

```
apiVersion: v1
kind: Pod
metadata:
  name: fortune2s
spec:
  containers:
    - image: luksa/fortune:args
      env:
      - name: INTERVAL
        value: "30"
...
```

`arugs: ["2"]` 줄을 제거하고 `env:` spec을 추가해주었다.


### Referring other environment variables in a variable's value

위의 예시에서는 고정된 값을 환경변수로 사용하였지만, 이 또한 다른 변수를 사용할 수 있다. `$(VAR)` 사용가능

```
env:
- name: FIRST_VAR
  value: "foo"
- name: SECOND_VAR
  value: "$(FIRST_VAR)bar"
```

이 경우 `SECOND_VAR`의 값은 `foobar`이 된다.

### Understanding the drawback of hardcoding environment variables

이렇게 환경변수를 사용하여 하드코드 되게 되면 production이나 development같은 환경에 따라 pod 정의가 바뀌어야 한다. 이를 위해서 아래에서 `ConfigMap`에 대한 자세한 설명

## 7.4. Decoupling configuration with a configmap

### introducing ConfigMaps

Kubernetes는 `ConfigMap`이라는 객체를 사용하여 설정 옵션들을 별개로 나눌 수 있다.

application은 `ConfigMap`에 직접 접근하여 읽을 필요가 없고 존재하는지도 알 필요가 없다. map의 내용들은 환경변수나 볼륨의 파일로 컨테이너에 전달된다.

app이 어떻게 `ConfigMap`을 사용하는 것과 상관 없이 설정을 별개의 object로 관리하는 것은 다른 환경들(dev, QA, production 등)에 대해 별개의 `ConfigMaps`을 사용할 수 있게 해준다.

### Creating a ConfigMap

어떻게 `ConfigMap`을 pod에서 사용할 수 있는지 살펴보자. 간단하게 이전에 사용한 `INTERVAL` 환경변수를 설정에 넣고 `kubectl create configmap` 명령어를 사용하셔 생성 가능하다.

```
$ kubectl create configmap fortune-config --from-literal=sleep-interval=25
configmap "fortune-config" created

$ kubectl get configmap
NAME                   DATA   AGE
fortune-config         1      11s

$ kubectl describe configmap fortune-config
Name:         fortune-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
sleep-interval:
----
25
Events:  <none>
```

보통 configmap은 한 개 이상을 담는다. `--from-literal`을 여러개 사용해서 여러개를 추가할 수 있다.

```
$ kubectl create configmap myconfigmap --from-literal=foo=bar --from-literal=bar=baz --from-literal=one=two
```

`kubectl get` 명령어를 사용해서 만든 configmap을 YAML 형태로 확인 가능하다.

```
$ kubectl get configmap fortune-config -o yaml

apiVersion: v1
data:
  sleep-interval: "25"
kind: ConfigMap
metadata:
  creationTimestamp: "2020-06-09T16:06:54Z"
  name: fortune-config
  namespace: default
  resourceVersion: "4181096"
  selfLink: /api/v1/namespaces/default/configmaps/fortune-config
  uid: 9e1b8166-9a1f-4c43-8e06-65d856f9cb0b
```

특별한 것은 없다. 이와 반대로 위 YAML을 통해서 만들수도 있다. (`metadata` 섹션에 있는 것은 이름 빼고는 적을 필요는 없다.)

`fortune-config.yaml`

```
apiVersion: v1
data:
  sleep-interval: "25"
kind: ConfigMap
metadata:
  name: fortune-config-from-file
```

```
$ kubectl create -f fortune-config.yaml
```

`kubectl create configmap` 명령어로 완성된 config 파일로도 ConfigMap을 만들수 있다.

```
$ kubectl create configmap my-config --from-file=config-file.conf
```

각각의 파일을 하나씩 생성하는 방법 말고 해당 디렉토리의 파일들을 한 번에 생성시킬 수 있따.

```
$ kubectl create configmap my-config --from-file=/path/to/dir
```

위에서 얘기한 모든 조건들을 함계 만들 수도 있다.

```
$ kubectl create configmap my-config
    --from-file=foo.json
    --from-file=bar=foobar.conf
    --from-file=config-opts/
    --from-literal=some=thing
```

![image](https://learning.oreilly.com/library/view/kubernetes-in-action/9781617293726/07fig05_alt.jpg)

### Passing a configmap entry to a container as an environment variables

그럼 실제로 pod의 컨테이너에서는 값을 어떻게 가져올까? 세 가지 방법이 있는데 가장 심플한 방법인 "환경변수"를 사용해보자.
`valueFrom` 필드를 사용해서 container에서 환경 변수로 사용이 가능하다.

`fortune-pod-env-configmap.yaml`

```
apiVersion: v1
kind: Pod
metadata:
  name: fortune-env-from-configmap
spec:
  containers:
  - image: luksa/fortune:env
    env:                             1
    - name: INTERVAL                 1
      valueFrom:                     2
        configMapKeyRef:             2
          name: fortune-config       3
          key: sleep-interval        4
...
```

- 1. INTERVAL이라는 이름의 환경변수를 세팅
- 2. 고정값 대신 `ConfigMapKey`를 사용하여 init
- 3. ConfigMap의 이름
- 4. ConfigMap에서 사용할 변수의 키

> ConfigMap을 optional로 사용할 수 있다. `configMapKeyRef.optional: true`를 세팅하면 된다. 이 경우 ConfigMap이 없어도 컨테이너가 실행된다.


### Passing all entries of a ConfigMap as environment variables at once

ConfigMap이 여러 entry들을 가질 경우 복잡하고 에러가 날 가능성이 높다. 이를 해결하기 위해 Kubernetes 1.6 qjwjsdptjsms 모든 ConfigMap의 entry들을 환경변수로 사용할 수 있게 해 준다.

```
spec:
  containers:
  - image: some-image
    envFrom:                      1
    - prefix: CONFIG_             2
      configMapRef:               3
        name: my-config-map       3
...
```

- 1. `env` 대신 `envFrom` 사용
- 2. 모든 환경변수들은 `CONFIG_` prefix됨
- 3. `my-config-map` 이름의 ConfigMap 사용

> prefix는 optional이기 때문에 사용하지 않으면 원래 이름을 사용한다.


### Passing a ConfigMap entry as a command-line argument

이제 ConfigMap의 변수가 컨테이너에서 돌고 있는 메인 process에 어떻게 전달되는지 살펴보자.
`pod.spec.container.args` 필드로 바로 ConfigMap의 엔트리들을 사용할 수는 없지만, 이들을 최초 init될 때 환경변수로 주입할 수 있다.

`fortune-pod-args-configmap.yaml`

```
apiVersion: v1
kind: Pod
metadata:
  name: fortune-args-from-configmap
spec:
  containers:
  - image: luksa/fortune:args          1
    env:                               2
    - name: INTERVAL                   2
      valueFrom:                       2
        configMapKeyRef:               2
          name: fortune-config         2
          key: sleep-interval          2
    args: ["$(INTERVAL)"]              3
...
```

이전에 사용한 것과 동일하게 환경 변수를 사용하지만, `$(ENV_VARIABLE_NAME)` 문법을 사용하여 변수의 값을 argument로 사용할 수 있다.

### Using a configMap volume to expose ConfigMap entries as files

환경 변수나 command line 인자를 사용하는 것은 보통 짧은 변수값에 사용된다.
ConfigMap은 전체 config file을 사용한다. 이를 컨테이너에 노출시킬 때 이전 장에서 언급했던 `configMap` volume을 사용할 수 있다.

`configMap` volume은 `ConfigMap` 파일의 각 entry를 노출시킨다.

위의 `fortuneloop.sh`를 수정하는 대신 조금 다른 예시를 사용해보자. `fortune` pod 안의 Nginx 웹서버를 설정하는 설정 파일을 사용할 것이다.

`my-nginx-config.conf`

```
server {
  listen              80;
  server_name         www.kubia-example.com;

  gzip on;                                       1
  gzip_types text/plain application/xml;         1

  location / {
    root   /usr/share/nginx/html;
    index  index.html index.htm;
  }
}
```

이제 `kubectl delete configmap fortune-config`를 사용하여 기존에 있던 configMap을 제거하고 새로 만들어보자.

`configmap-files` 디렉토리를 새로 만들고 nginx confg 파일을 추가해보자. (`configmap-files/my-nginx-config.conf`)
이전과 같이 `ConfigMap`에 `sleep-interval` entry를 추가하기 위해 `sleep-interval`이라는 파일을 만들고 `25`를 추가해보자

![image](https://learning.oreilly.com/library/view/kubernetes-in-action/9781617293726/07fig08.jpg)

그리고 디렉토리의 파일을 `ConfigMap`으로 만들자

```
$ kubectl create configmap fortune-config --from-file=configmap-files
configmap/fortune-config created
```

해당 configMap의 `YAML` 파일을 확인해보자

```
$ kubectl get configmap fortune-config -o yaml
apiVersion: v1
data:
  my-nginx-config.conf: |+
    server {
      listen              80;
      server_name         www.kubia-example.com;

      gzip on;
      gzip_types text/plain application/xml;

      location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
      }
    }

  sleep-interval: |
    25
```

nginx config의 내용과 sleep-interval이 모두 들어있는 것을 볼 수 있다.

이제 해당 변수를 volume으로 사용해보자.

Nginx는 `etc/nginx/nginx.conf` 파일을 읽는다.
하지만 기본 파일을 override 하지 않기 위해서 기본 파일이 포함시키는 `etc/nginx/conf.d/` 디렉토리에 해당 설정을 넣을 것이다.

`fortune-pod-configmap-volume.yaml`

```
apiVersion: v1
kind: Pod
metadata:
  name: fortune-configmap-volume
spec:
  containers:
  - image: nginx:alpine
    name: web-server
    volumeMounts:
    ...
    - name: config
      mountPath: /etc/nginx/conf.d      1
      readOnly: true
    ...
  volumes:
  ...
  - name: config
    configMap:                          2
      name: fortune-config              2
  ...
```

- 1. configMap의 volume을 해당 경로로 설정
- 2. volume은 `fortune-config` ConfigMap 사용

```
$ kubectl apply -f fortune-pod-configmap-volume.yaml
pod/fortune-configmap-volume created

$ kubectl port-forward fortune-configmap-volume 8080:80 &
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80

$ curl -H "Accept-Encoding: gzip" -I localhost:8080
HTTP/1.1 200 OK
Server: nginx/1.11.1
Date: Thu, 18 Aug 2016 11:52:57 GMT
Content-Type: text/html
Last-Modified: Thu, 18 Aug 2016 11:52:55 GMT
Connection: keep-alive
ETag: W/"57b5a197-37"
Content-Encoding: gzip     
```

`gzip`으로 정상적인 요청이 가는 것을 볼 수 있다.

실제로 `/etc/nginx/conf.d/` 디렉토리에 잘 들어있는지 살펴보자.

```
$ kubectl exec fortune-configmap-volume -c web-server -- ls /etc/nginx/conf.d
my-nginx-config.conf
sleep-interval
```

`sleep-interval`은 실제로 nginx가 사용하지 않음에도 불구하고 해당 폴더에 같이 들어있는 것을 볼 수 있다.
이를 두 개의 ConfigMap으로 나누면 문제가 해결되지만 한 pod에 사용할 ConfigMap을 두개로 구성하는 것도 좋아 보이지는 않는다.

다행히도 configMap volume에서 필요한 것만 따로 사용이 가능하다. (여기서는 `my-nginx-config.conf`)

`fortune-pod-configmap-volume-with-items.yaml`

```
...
  volumes:
  - name: config
    configMap:
      name: fortune-config
      items:                             1
      - key: my-nginx-config.conf        2
        path: gzip.conf                  3
...
```

- 1. 어떤 엔트리만 volume에 포함할건지 리스팅
- 2. 해당 key에 포함된 엔트리만 사용
- 3. entry의 값은 해당 경로의 파일에 저장


기존 `/etc/nginx/conf.d` 디렉토리에 있던 항목들은 숨겨진다는 것을 알 수 있다.
fliesystem에 volume이 마운트 되면 기존에 있던 해당 파일들은 더 이상 접근할 수 없다.

지금은 큰 사이드 이펙트가 없지만, `/etc` 디렉토리와 같은 많은 중요한 파일들이 있는 경우에는 문제가 심각할 수 있다.

따라서 이제 `ConfigMap`에서 이미 존재하는 디렉토리의 파일들은 hidden 시키지 않고 개별 파일만 추가하는 방법이 궁금할 것이다.

`volumeMount`에 `subPath` 프로퍼티를 추가함으로써 전체 volume을 mount 하는 것이 아니라 특정 경로나 파일만 mount 시킬 수 있다.

```
spec:
  containers:
  - image: some/image
    volumeMounts:
    - name: myvolume
      mountPath: /etc/someconfig.conf      1
      subPath: myconfig.conf               2
```

- 1. 디렉토리가 아니라 파일을 마운팅
- 2. 전체 볼륨을 마운트하지 않고 `myconfig.conf`만 마운팅

모든 `configMap` 볼륨의 permission은 기본적으로 644(`-rw-r-r--`)읻다. `defaultMode` 프로퍼티를 사용하여 이를 변경할 수 있다.

```
  volumes:
  - name: config
    configMap:
      name: fortune-config
      defaultMode: "6600"           1
```

- 1. 모든 파일의 권한을 `-rw-rw----`로 변경


### Updating an app's config withou having to restart the app

이전에 얘기했던 환경변수나 cmd 인자로 설정을 넘기는 것의 단점 (프로세스가 작동중일 때 설정을 변경할 수 없다)과는 달리,

`ConfigMap`을 volume을 활용하여 노출시키면 pod를 다시 만들거나 재시작 할 필요 없이 설정을 변경할 수 있다.

`ConfigMap`을 업데이트 하면 해당 volume을 referencing하는 모든 파일이 업데이트 된다.

> 업데이트 된 ConfigMap이 모든 파일에 적용되기까지에는 꽤 많은 시간이 걸리는 것에 주의하라 (대략 1분 정도)

실제로 `ConfigMap`을 업데이트 하려먼 `kubectl edit` 명령어를 사용하면 된다

```
$ kubectl edit configmap fortune-config
```

에디터가 열리면 `gzip on`을 `gzip off`로 변경하고 파일을 저장하자.

`kubectl exec` 명령어를 사용하여 실제 파일이 변경되었는지 보자.

```
$ kubectl exec fortune-configmap-volume -c web-server cat /etc/nginx/conf.d/my-nginx-config.conf
server {
  listen              80;
  server_name         www.kubia-example.com;

  gzip off;
  gzip_types text/plain application/xml;

  location / {
    root   /usr/share/nginx/html;
    index  index.html index.htm;
  }
}
```

업데이트가 된 것을 볼 수 있다. (만약 업데이트가 되지 않았다면 조금 기다리거나 다시 명령어를 실행시켜보자.)

Nginx는 리로드를 하기 전까지 아직 기존의 설정에 따라 요청을 처리하고 있을 것이다. 이를 리로드 해주자.

```
$ kubectl exec fortune-configmap-volume -c web-server -- nginx -s reload
2020/06/14 12:43:27 [notice] 42#42: signal process started
```

이제 curl 요청을 보내보면 더 이상 response가 압축되지 않는 것을 볼 수 있다. (더 이상 `Content-Encoding: gzip` 헤더를 포함하지 않는다)

```
$ curl -H "Accept-Encoding: gzip" -I localhost:8080
HTTP/1.1 200 OK
Server: nginx/1.19.0
Date: Sun, 14 Jun 2020 12:43:47 GMT
Content-Type: text/html
Content-Length: 491
Last-Modified: Sun, 14 Jun 2020 12:43:29 GMT
Connection: keep-alive
ETag: "5ee61b71-1eb"
Accept-Ranges: bytes
```

Kubernetes는 어떻게 리로드 없이 파일의 변화를 감지한 것일까?
이는 symbolic links 덕분이다.

```
$ kubectl exec -it fortune-configmap-volume -c web-server -- ls -lA /etc/nginx/conf.d
total 4
drwxr-xr-x    2 root     root          4096 Jun 14 12:40 ..2020_06_14_12_40_56.647424611
lrwxrwxrwx    1 root     root            31 Jun 14 12:40 ..data -> ..2020_06_14_12_40_56.647424611
lrwxrwxrwx    1 root     root            27 Jun 12 15:06 my-nginx-config.conf -> ..data/my-nginx-config.conf
lrwxrwxrwx    1 root     root            21 Jun 12 15:06 sleep-interval -> ..data/sleep-interval
```

위에서 볼 수 있듯이 `configMap` 볼륨에 마운트 된 파일은 `..data` 디렉토리의 파일을 심볼릭 링크로 가리킨다. `..data` 디렉토리는 또한 `..2020_06_14_xxxx` 디렉토리를 가리킨다.

`ConfigMap`이 업데이트 되면 Kubernetes는 이에 맞는 새로운 디렉토리를 다시 생성하고 이를 `..data` symbolic link에 re-link 하여 한번에 모든 파일을 효과적으로 변경시킨다.

**Understanding that files mounted into existing directories don’t get updated**

ConfigMap-backed volume을 업데이트 할 때 주의해야할 점이 있다. 컨테이너의 전체 볼륨이 아니라 단일 파일을 마운팅한 경우에는 해당 파일은 업데이트 되지 않는다.

## 7.5. Using secrets to pass sensitive data to containers

지금까지는 민감하지 않은 정보를 container에 일반적인 방식으로 전달하는 방식을 살펴봤지만, 대개 설정은 민감한 정보를 포함하기도 한다. (credential이나 private encryption key)

### Introducing Secrets

그런 정보를 저장하고 분배하기 위해서 Kubernetes는 `Secret`이라고 불리는 별개의 object를 제공한다. `Secret`은 key-value 쌍으로 이뤄지는 등 `ConfigMap`과 거의 흡사하다.

- Secret을 container의 환경 변수로 전달할 수 있다.
- Secret을 파일로 volume에 노출시킬 수 있다.

Secret은 오직 해당 Secret의 접근이 필요한 pod가 실행중인 노드에서만 사용되게 한다. 또한 node 자체에서도 Secret은 항상 메모리에만 저장되고 실제 스토리지에는 저장되지 않는다.

### Introducing the default token Secret

`kubectl describe` 명령엉어를 pod에 실행시키면 다음과 같은 결과물이 있는 것을 보았을 것이다.

```
Volumes:
  default-token-mxz2t:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-mxz2t
```

각각의 pod는 자동으로 `secret` volume와 연결된다. `secret` 또한 리소스이기 때문에 `kubectl get` 명령어로 나열할 수 있다.


```
$ kubectl get secrets
NAME                               TYPE                                  DATA   AGE
default-token-mxz2t                kubernetes.io/service-account-token   3      54d
sh.helm.release.v1.full-coral.v1   helm.sh/release.v1                    1      34d
tls-secret                         kubernetes.io/tls                     2      17d
```

`kubectl describe`로 더 자세히 볼 수 있다.

```
$ kubectl describe secrets
Name:         default-token-mxz2t
Namespace:    default
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: default
              kubernetes.io/service-account.uid: 044bc544-6160-4ede-9034-046c5c5da3ca

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1066 bytes
namespace:  7 bytes
token:      eyJhbGciOiJS...
```

위에서 secret은 세 엔트리 (`ca.crt`, `namespace`, `token`)를 가지고 있는 것을 볼 수 있다.

secret volume은 다음 주소에 mount 된다 (`kubectl describe pod` 명령어로 볼 수 있음)

```
Mounts:
  /var/run/secrets/kubernetes.io/serviceaccount from default-token-mxz2t
```

![image](https://learning.oreilly.com/library/view/kubernetes-in-action/9781617293726/07fig11_alt.jpg)

실제로 pod에 해당 경로에 volume이 마운트 되어서 세 개의 파일이 있는 것을 확인할 수 있다.

```
$ kubectl exec mypod ls /var/run/secrets/kubernetes.io/serviceaccount/
ca.crt
namespace
token
```

다음 장에서 실제로 이 파일들을 사용하여 어떻게 API 서버에 접속하는지 볼 것이다.

### Creating Secret

fortune-serving Nginx container를 HTTPS도 지원하게 해보자. 이를 위해서는 certificate와 private key가 필요하다.

우선 local machine에서 certificate와 private key 파일을 만들자.

```
$ openssl genrsa -out https.key 2048
$ openssl req -new -x509 -key https.key -out https.cert -days 3650 -subj /CN=www.kubia-example.com
```

그리고 Secret의 몇몇 설명을 위해 더미 파일을 하나 만든다.

```
$ echo bar > foo
```

`kubectl create secret` 명령어로 세 파일에 대해 `Secret` 을 만들자.

```
$ kubectl create secret generic fortune-https --from-file=https.key --from-file=https.cert --from-file=foo
```


### Comparing ConfigMaps and Secrets

`Secret`과 `ConfigMaps`은 큰 차이가 있다.

```
$ kubectl get secret fortune-https -o yaml
apiVersion: v1
data:
  foo: YmFyCg==
  https.cert: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCekNDQ...
  https.key: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFcE...
kind: Secret
...
```

```
$ kubectl get configmap fortune-config -o yaml
apiVersion: v1
data:
  my-nginx-config.conf: |
    server {
      ...
    }
  sleep-interval: |
    25
kind: ConfigMap
...
```

Secret의 엔트리들은 Base64-encoded 되어있는 반변에 ConfigMap의 엔트리들은 일반 텍스트로 보여진다.

**Introducing the stringData field**

모든 민감한 정보가 binary form이 아니기 때문에 Kubernetes는 Secrets의 값을 `stringData` 필드로 설정할 수 있게 한다.

```
kind: Secret
apiVersion: v1
stringData:
  foo: plain text
data:
  https.cert: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCekNDQ...
  https.key: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFcE...
```

### Using the Secret in a pod

이제 cert와 key 파일을 nginx의 설정에서 사용하게 하면 된다.

이를 위해서 ConfigMap을 수정하자

```
$ kubectl edit configmap fortune-config
...
data:
  my-nginx-config.conf: |
    server {
      listen              80;
      listen              443 ssl;
      server_name         www.kubia-example.com;
      ssl_certificate     certs/https.cert;           1
      ssl_certificate_key certs/https.key;            1
      ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
      ssl_ciphers         HIGH:!aNULL:!MD5;

      location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
      }
    }
  sleep-interval: |
...
```

`fortune-pods-htts.yaml`에 `fortune-htts` Secret을 mounnt 시키자.

```
apiVersion: v1
kind: Pod
metadata:
  name: fortune-https
spec:
  containers:
  - image: luksa/fortune:env
    name: html-generator
    env:
    - name: INTERVAL
      valueFrom:
        configMapKeyRef:
          name: fortune-config
          key: sleep-interval
    volumeMounts:
    - name: html
      mountPath: /var/htdocs
  - image: nginx:alpine
    name: web-server
    volumeMounts:
    - name: html
      mountPath: /usr/share/nginx/html
      readOnly: true
    - name: config
      mountPath: /etc/nginx/conf.d
      readOnly: true
    - name: certs                         1
      mountPath: /etc/nginx/certs/        1
      readOnly: true                      1
    ports:
    - containerPort: 80
    - containerPort: 443
  volumes:
  - name: html
    emptyDir: {}
  - name: config
    configMap:
      name: fortune-config
      items:
      - key: my-nginx-config.conf
        path: https.conf
  - name: certs                            2
    secret:                                2
      secretName: fortune-https            2
```

- 1. Nginx가 `/etc/ngnix/certs/` 경로를 읽게 하였으니 secret volume을 해당 경로에 주어야 한다.
- 2. secret volume을 `fortune-https`에 주었다

![image](https://learning.oreilly.com/library/view/kubernetes-in-action/9781617293726/07fig12_alt.jpg)

**Testing whether Nginx is using the cert and key from the Secret**

```
$ kubectl create -f fortune-pods-https.yaml
pod/fortune-https created

$ kubectl fort-forward fortune-https 8443:443 &
Forwarding from 127.0.0.1:8443 -> 443
Forwarding from [::1]:8443 -> 443

$ curl https://localhost:8443 -k
```

제대로 설정이 되어있는지 `-v` verbose logging option으로 확인할 수 있따.

```
$ curl https://localhost:8443 -k -v
* About to connect() to localhost port 8443 (#0)
*   Trying ::1...
* Connected to localhost (::1) port 8443 (#0)
* Initializing NSS with certpath: sql:/etc/pki/nssdb
* skipping SSL peer certificate verification
* SSL connection using TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* Server certificate:
*   subject: CN=www.kubia-example.com          1
*   start date: aug 16 18:43:13 2016 GMT       1
*   expire date: aug 14 18:43:13 2026 GMT      1
*   common name: www.kubia-example.com         1
*   issuer: CN=www.kubia-example.com           1
```

- 1. certificate가 우리가 만든 것과 일치하는 것을 볼 수 있따.

`secret` 볼륨은 in-memory filesystem (tmpfs)를 사용한다. 이게 컨테이너에 마운트 되어있는 것을 확인할 수 있다.

```
$ kubectl exec fortune-https -c web-server -- mount | grep certs
tmpfs on /etc/nginx/certs type tmpfs (ro,relatime)
```

tmpfs가 사용되기 때문에 Secret 데이터는 disk에 절대 작성되지 않는다.

**Exposing a Secret's entries thourgh environment variables**

volume을 사용하는 대신 `secret`의 엔트리를 환경변수로 사용할 수 있다.

예를 들면 우리가 추가한 `foo`에 있는 변수를 `FOO_SECRET` 환경변수로 사용하기 위해서는 다음과 같이 설정해주면 된다.

```
    env:
    - name: FOO_SECRET
      valueFrom:                   1
        secretKeyRef:              1
          name: fortune-https      2
          key: foo                 3
```

`secretKeyRef`를 지정한 것만 빼면 `INTERVAL` 환경변수를 추가한 것과 거의 흡사하다.

Kubernetes에서 환경 변수로 `Secret`을 노출시킬 수 있음에도 이는 좋은 방법이 아니다.
애플리케이션은 대개 환경변수를 에러 리포트나 로그 등에 노출시키기 때문에 의도하지 않게 노출될 가능성이 있다.

### Understanding image pull Secrets

때떄로 kubernetes 자체에서 credential을 패스할 필요가 있다.
예를 들면 private 컨테이너 레지스트리에서 이미지를 다운로드하는 경우.

**Using a private image repository on Docker Hub**

도커 허브에서는 public image repository와 더불어 private repository도 지원한다.

[도커 허브](https://hub.docker.com/)에 접속하여 레파지토리를 private으로 만들 수 있다.

private 레파지토리의 이미지를 사용하기 위해서는 두 가지가 필요하다
- Docker registry의 credential을 가지고 있는 Secret을 생성
- pod의 manifest의 `imagePullSecret` 필드에서 해당 Secret을 사용

**Create a Secret for authenticating with a Docker registry**

credential을 가진 Secret을 만드는 것은 일반적인 Secret을 만드는 것과 흡사하다.

```
$ kubectl create secret docker-registry mydockerhubsecret \
  --docker-username=myusername --docker-password=mypassword \
  --docker-email=my.email@provider.com
```

`generic` secret을 만드는 대신 `mydockerhubsecret`이라는 `docker-registry` secret을 만들자.

새로 생긴 Secret을 `kubectl describe secret mydockerhubsecret`으로 살펴보면 `.dockercfg`라는 하나의 엔트리를 볼 수 있다.

> 2020-06 기준 `.dockerconfigjson`이 생성된다.

**Using the docker-registry Secret in a pod definition**

이를 pod spec에 추가해주어야 한다. `pod-with-private-image.yaml`

```
apiVersion: v1
kind: Pod
metadata:
  name: private-pod
spec:
  imagePullSecrets:                 1
  - name: mydockerhubsecret         1
  containers:
  - image: username/private:tag
    name: main
```

> image는 도커 허브에서 private으로 변경한 이미지 이름을 넣어주자

실제로 작동하는지 확인해보자.

```
$ kubectl create -f pod-with-private-image.yaml
pod/private-pod created

$ kubectl describe your_pod_name
```


시스템에서 많은 다른 pod를 띄우기 때문에 이를 모든 pod에 넣는 것이 이상할 수 있다.
다행히 12장에서 `ServiceAccount`를 사용하여 이미지를 pull 할 때 자동으로 pod에 이를 추가해주는 방법을 배울 것이다.

