# declarative management of kubernetes object using kustomize

# Overview of kustomize

kustomize는 kubernetes configurations을 커스터마이징 하는 툴.
- 다른 source로부터 k8s resource 생성
- resource의 cross-cutting field 세팅
- resource의 집합을 구성하고 커스터마이징

## Generating resources

`ConfigMaps`과 `Secrets`은 여러 kubernetes objects들에서 쓰이는 민감한 데이터들을 들고있다.

ConfigMaps과 Secrets의 실제 데이터들은 대개 cluster 밖에 있음. (`.properties` or ssh keyfile)

kustomize는 `secretGenerator`와 `configMapGenerator`를 제공.

### configMapGenerator

file로부터 configMap을 만드려면 `files` entry를 사용하면 됨.

아래는 `.properties` 파일의 데이터로 ConfigMap을 생성하는 예시.

```
# Create a application.properties file
cat <<EOF >application.properties
FOO=Bar
EOF

cat <<EOF >./kustomization.yaml
configMapGenerator:
- name: example-configmap-1
  files:
  - application.properties
EOF
```

생성될 resource는 다음과 같이 확인이 가능.

```
$ kc kustomize ./
apiVersion: v1
data:
  application.properties: |
      FOO=BAR
      kind: ConfigMap
      metadata:
        name: example-configmap-1-988h4dd7k7
```

env file에서 ConfigMap을 생성하려면 files 대신 `envs`를 사용하면 된다.

```
# Create a .env file
cat <<EOF >.env
FOO=Bar
EOF

cat <<EOF >./kustomization.yaml
configMapGenerator:
- name: example-configmap-1
  envs:
  - .env
EOF
```

```
$ kustomize build ./
apiVersion: v1
data:
  FOO: BAR
kind: ConfigMap
metadata:
  name: example-configmap-1-m2mg5mb749
```

> 2021.05 기준 kubectl의 kustomize 버전은 envs를 파싱하지 못해 `Error: json: unknown field "envs"`에러가 발생한다.

> `kustomize build ./`로 결과를 확인할수 있지만 envs를 사용하는 경우 kubectl을 사용하지 못하는건 조금 아쉬움. [관련 github issue](https://github.com/kubernetes-sigs/kustomize/issues/1069)

`.env` 파일의 값은 각각이 분리된 key로 configmap에 추가됨. 반면 `.properties`는 하나의 entry로 추가된다.

`literals`를 사용해 바로 key-value 페어를 넣어줄수도 있다.

```
cat <<EOF >./kustomization.yaml
configMapGenerator:
- name: example-configmap-2
  literals:
  - FOO=Bar
EOF
```

실제 resource에서 configMap을 사용하기 위해서는 configMapGenerator의 이름으로 사용하면 된다.

```
# Create a application.properties file
cat <<EOF >application.properties
FOO=Bar
EOF

cat <<EOF >deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  labels:
    app: my-app
spec:
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: app
        image: my-app
        volumeMount:
        - name: config
          mountPath: /config
      volumes:
      - name: config
        configMap:
          name: example-configmap-1
EOF

cat <<EOF >./kustomization.yaml
resources:
- deployment.yaml
configMapGenerator:
- name: example-configmap-1
  files:
  - application.properties
EOF
```

실제로 build를 실행시키면 configmap.metadata.name이 그대로 deployment에서 사용되는 것을 볼 수 있음.

```
$ kc kustomize ./

apiVersion: v1
data:
  application.properties: |
    FOO=BAR
kind: ConfigMap
metadata:
  name: example-configmap-1-988h4dd7k7
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: my-app
  name: my-app
spec:
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - image: my-app
        name: app
        volumeMount:
        - mountPath: /config
          name: config
      volumes:
      - configMap:
          name: example-configmap-1-988h4dd7k7
        name: config
```

### secretGenerator

lteral key-value 쌍이나 파일로 Secret을 만들 수 있다. `secretGenerator`에 `files` entry를 추가해서 사용하면 됨.

아래는 `password.txt`로부터 Secret을 생성하는 예시.

```
# Create a password.txt file
cat <<EOF >./password.txt
username=admin
password=secret
EOF

cat <<EOF >./kustomization.yaml
secretGenerator:
- name: example-secret-1
  files:
  - password.txt
EOF
```

다음과 같이 생성된다

```
apiVersion: v1
data:
  password.txt: dXNlcm5hbWU9YWRtaW4KcGFzc3dvcmQ9c2VjcmV0Cg==
kind: Secret
metadata:
  name: example-secret-1-t2kt65hgtb
type: Opaque
```

literal key-value로 추가하면 secret도 key-value pair로 추가된다.

deployment에서 사용할 때는 configMap과 동일하게 이름으로 사용하면 된다.

### generatorOptions

ConfigMap과 Secret을 새성할 때 content hash suffix를 붙일 수 있다. 이는 content가 변경될 때마다 새로운 ConfigMap과 Secret이 생성되는 것을 보장함. 이 기능을 안쓰려면 `generatorOptoin` 사용하면 됨.

```
cat <<EOF >./kustomization.yaml
configMapGenerator:
- name: example-configmap-3
  literals:
  - FOO=Bar
generatorOptions:
  disableNameSuffixHash: true
  labels:
    type: generated
  annotations:
    note: generated
EOF
```

### Setting cross-cutting fields

프로젝트의 k8s resoure에 cross-cutting 필드를 설정하는 것은 흔한 일. 보통 helm에서는 values를 사용한다.
- 모든 리소스에 같은 namespace 적용
- 같은 prefix와 suffix 추가
- 같은 labels set 추가
- 같은 annotations set 추가

아래는 간단한 예시.

```
# Create a deployment.yaml
cat <<EOF >./deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
EOF

cat <<EOF >./kustomization.yaml
namespace: my-namespace
namePrefix: dev-
nameSuffix: "-001"
commonLabels:
  app: bingo
commonAnnotations:
  oncallPager: 800-555-1212
resources:
- deployment.yaml
EOF
```

실제 생성되는 리소스에서는 namespace, label, annotation, prefix, suffix가 적용된 것을 볼 수 있음

```
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    oncallPager: 800-555-1212
  labels:
    app: bingo
  name: dev-nginx-deployment-001
  namespace: my-namespace
spec:
  selector:
    matchLabels:
      app: bingo
  template:
    metadata:
      annotations:
        oncallPager: 800-555-1212
      labels:
        app: bingo
    spec:
      containers:
      - image: nginx
        name: nginx
```

## Composing and Customizing resources

같은 파일이나 디렉토리에서 resource set을 구성하는 경우가 흔하기 때문에 kustomize에서는 여러 파일을 하나로 patch 해주거나 customize 해주는 기능을 제공함

### Composing

kustomize는 여러 resource composition을 제공한다. `kustomization.yaml` 파일의 `resources` 필드에 리소스를 추가하면 됨.

```
# Create a deployment.yaml file
cat <<EOF > deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
EOF

# Create a service.yaml file
cat <<EOF > service.yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx
  labels:
    run: my-nginx
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    run: my-nginx
EOF

# Create a kustomization.yaml composing them
cat <<EOF >./kustomization.yaml
resources:
- deployment.yaml
- service.yaml
EOF
```

### Customizing

patch를 통해 리소스마다 다른 커스터마이징 가능.
`patchesStrategicMerge`
- list of file paths. Each file should be resolved to a [strategic merge patch](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-api-machinery/strategic-merge-patch.md).
- the name inside patches must match resource names that are already loaded
- 한가지 일을 하는 small patch로 사용하는걸 추천
  - ex. 한개는 deployment replica number, 한개는 memory limit

```
# Create a deployment.yaml file
cat <<EOF > deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
EOF

# Create a patch increase_replicas.yaml
cat <<EOF > increase_replicas.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  replicas: 3
EOF

# Create another patch set_memory.yaml
cat <<EOF > set_memory.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  template:
    spec:
      containers:
      - name: my-nginx
        resources:
          limits:
            memory: 512Mi
EOF

cat <<EOF >./kustomization.yaml
resources:
- deployment.yaml
patchesStrategicMerge:
- increase_replicas.yaml
- set_memory.yaml
EOF
```

모든 리소스나 필드가 strategic merge patch를 지원하지는 않음. kustomize는 `patchesJson6902`를 통해 JSON patch를 제공.

```
# Create a deployment.yaml file
cat <<EOF > deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
EOF

# Create a json patch
cat <<EOF > patch.yaml
- op: replace
  path: /spec/replicas
  value: 3
EOF

# Create a kustomization.yaml
cat <<EOF >./kustomization.yaml
resources:
- deployment.yaml

patchesJson6902:
- target:
    group: apps
    version: v1
    kind: Deployment
    name: my-nginx
  path: patch.yaml
EOF
```

kustomize는 patch 생성 없이 container image나 다른 필드 값을 inject 해줄수 있다.

```
cat <<EOF > deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
EOF

cat <<EOF >./kustomization.yaml
resources:
- deployment.yaml
images:
- name: nginx
  newName: my.image.registry/nginx
  newTag: 1.4.0
EOF
```

가끔은 다른 object로부터 configuration value를 추가해야할 필요가 있음. (ex. deployment pod에서 service의 name이 필요한 경우)

kustomization.yaml에 추가된 `namePrefix`나 `nameSuffix`에 따라 이름이 변경될 수 있으므로 하드코딩은 피하는게 좋음.

대신 `vars`라는 필드를 통해서 변수를 inject 시켜줄 수 있음.

```
# Create a deployment.yaml file
cat <<EOF > deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        command: ["start", "--host", "$(MY_SERVICE_NAME)"]
EOF

# Create a service.yaml file
cat <<EOF > service.yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx
  labels:
    run: my-nginx
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    run: my-nginx
EOF

cat <<EOF >./kustomization.yaml
namePrefix: dev-
nameSuffix: "-001"

resources:
- deployment.yaml
- service.yaml

vars:
- name: MY_SERVICE_NAME
  objref:
    kind: Service
    name: my-nginx
    apiVersion: v1
EOF
```

## Bases and Overlays

kustomize는 "bases"와 "overlays" 컨셉이 있다.
- base: `kustomization.yaml`이 있는 디렉토리
- overlay: kustomization.yaml이 참고하는 다른 kustomization 디렉토리

아래는 base의 예시

```
# Create a directory to hold the base
mkdir base
# Create a base/deployment.yaml
cat <<EOF > base/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
EOF

# Create a base/service.yaml file
cat <<EOF > base/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx
  labels:
    run: my-nginx
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    run: my-nginx
EOF
# Create a base/kustomization.yaml
cat <<EOF > base/kustomization.yaml
resources:
- deployment.yaml
- service.yaml
EOF
```

이렇게 만들어진 base는 여러 overlay에서 사용될 수 있음. 각각의 overlay에서 다른 namePrefix나 nameSuffix를 사용할 수 있다.

```
mkdir dev
cat <<EOF > dev/kustomization.yaml
bases:
- ../base
namePrefix: dev-
EOF

mkdir prod
cat <<EOF > prod/kustomization.yaml
bases:
- ../base
namePrefix: prod-
EOF
```

### How to apply/view/delete objects using kustomize

`-k`나 `--kustomize` flag를 사용해서 kubectl 명령어를 실행시킬 수 있음.

```
kubectl apply -f <kustomization directory>/
```

아래 kustomization.yaml 예시

```
# Create a deployment.yaml file
cat <<EOF > deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
EOF

# Create a kustomization.yaml
cat <<EOF >./kustomization.yaml
namePrefix: dev-
commonLabels:
  app: my-nginx
resources:
- deployment.yaml
EOF
```

apply뿐만 아니라 get, describe, diff, delete 등도 해당 경로로 사용할 수 있다.

```
$ kubectl apply -k ./
deployment.apps/dev-my-nginx created

$ kubectl get -k ./

$ kubectl describe -k ./

$ kubectl diff -k ./

$ kubectl delete -k ./
```

### Kustomize features list

[feature list](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/#kustomize-feature-list)


