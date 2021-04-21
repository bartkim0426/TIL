# debug crashLoopBackOff pods trick

crashLoopBackOff status mean the pod cannot be created, so it's impossible to debug with log or inside the pod container.

In this case, it's better to check Dockerfile is worked well.

For test this easily, you can simply create test pod and see inside the container.

```
kubectl run test-pod <image/image_name> -- sleep infinity
kubectl exec -it <pod_name>
```
