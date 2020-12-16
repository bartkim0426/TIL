## setup hostname

kubernetes를 셋업하기 전에 master, worker로 쓸 서버의 hostname을 셋업해주어야됨. worker, master로 변경하는게 가장 좋지만 그렇지 못하면 `/etc/hosts`에 추가만 해주기


## Kubelet cgroup driver: "cgroupfs" is different from docker cgroup driver: "systemd"

kubelet과 docker의 cgroup driver가 일치하지 않아서 생긴 문제였음.

`/var/lib/kubelet/kubeadm-flags.env`에 `--cgroup-driver`를 systemd로 추가해주니 해결되었다.

```
KUBELET_KUBEADM_ARGS="--network-plugin=cni --pod-infra-container-image=k8s.gcr.io/pause:3.2 --cgroup-driver=systemd"
```

kubelet도 재시작해야됨

```
systemctl daemon-reload && systemctl restart kubelet
```

## kubeadm reset

https://stackoverflow.com/questions/55767652/kubernetes-master-worker-node-kubeadm-join-issue
