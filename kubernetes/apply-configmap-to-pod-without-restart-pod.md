# Apply configmap to pod without restart or recreate pod with volume


Initially, you have to restart or recreate pod to apply the chagne after change configmap value.

But when using volume, don't have to restart pod to apply it.


### Example

Let's create simple pod and configmap

`example-pod.yaml`

```
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
    - name: example-container
      image: busybox
      command: ["/bin/sh", "-c", "watch \"cat /etc/config/mycat\""] 
      volumeMounts:
        - name: config-volume
          mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: example-config
```

`example-config.yaml`

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-config
  namespace: default
data:
  mycat: |+
    simba: 3
    nana: 2
```

And create those pod and configmap

```
$ kubectl apply -f example-config.yaml
$ kubectl apply -f example-pod.yaml
```

Now see it's well create with logs after pod is running

```
$ kubectl logs example-pod
Every 2.0s: cat /etc/config/mycat                           2020-07-24 09:34:12

simba: 3
nana: 2
```

Now change only configmap value and apply it (Or you just can edit with `kubectl edit example-config`)

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-config
  namespace: default
data:
  mycat: |+
    simba: 4
    nana: 3
```

```
$ kubectl apply -f example-config.yaml
$ kubectl get configmap example-config -o yaml
```

Now just see the log without restart or recreate pod

```
$ kubectl logs -f example-pod
Every 2.0s: cat /etc/config/mycat                           2020-07-24 09:37:12

simba: 4
nana: 3
```

You can see the changed values are applied! Cooool!

