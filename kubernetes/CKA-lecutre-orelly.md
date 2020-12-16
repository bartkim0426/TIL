# CKA lecture


## Setup k8s nodes

3-4대의 서버 준비
- 한대는 master node용: 2 cpu, 2 GB (or 1GB) mem
- 나머지는 1GB mem (워커용) 2-3대

> lecture에서는 centos 7.x를 사용하는데 나는 ubuntu 20을 사용하였다.


```
$ sudo yum update
$ sudo yum install -y vim git bash-completion
# optional
$ sudo yum install tmux
```

(optional) add bashrc, vimrc for personal setup

`~/.bashrc`

```
# vi mode in sh
set -o vi

alias ll="ls -al"
alias kc=kubectl

# history size
HISTSIZE=1000000
SAVEHIST=1000000
```


`~/.vimrc`

install vim plug

```
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

```
let g:mapleader = ","

set number
set relativenumber

set hlsearch
set incsearch

set history=1000
set nocompatible

set undofile
set undodir=~/.vim/undo/

set ignorecase
set smartcase

set tabstop=4               "How many columns wide is a tab char
set shiftwidth=4            "Hiw much whitespace should be inserted when >, < command set softtabstop=4           "How much whitespace should be inserted when hit tab
set expandtab
set autoindent
set smartindent

syntax on
filetype plugin on

call plug#begin('~/.vim/plugged')
Plug 'scrooloose/nerdtree'
Plug 'jistr/vim-nerdtree-tabs'          "Toggle nerd tree with one key
call plug#end()

" nerdtree
map <Leader>n <plug>NERDTreeTabsToggle<CR>
```


### 셋업 (swap, filewall, SELinux)

> 다음 명령어들은 아래에서 실행할 스크립트들에 포함되어 있기 때문에 스크립트로 실행시키려면 굳이 실행시키지 않아도 됨.

상황에 따라 이미 꺼져있을 수도 있음. `free -m` 명령어로 스왑중인 메모리를 확인 가능하다.

```
$ swapoff -a
$ echo 0 > /proc/sys/vm/swappiness
$ sed -e '/swap/ s/^#*/#/' -i /etc/fstab
```

firewall 해제

```
$ systemctl disalbe filewalld
$ systemctl stop filewalld
```

SELinux (Security-Enhanced Linux; 리눅스 보안 모듈) 끄기

```
$ setenforce 0
$ sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
```

### docker, kubernetes 설치

```
$ git clone https://github.com/bartkim0426/cka.git
$ cd cka
$ sudo ./setup-docker.sh
```

> ubuntu인 경우 해당 명령어가 설치되지 않을 수 있음. 그럼 아래 명령어로 직접 설치하면 된다.

```
$ yum install docker -y
$ systemctl start docker.service
```

`setup-kubetools.sh`를 실행시켜 kubectl, kubeadm 등을 설치한다.


```
$ sudo ./setup-kubetools.sh
```

> 해당 스크립트는 SELinux 셋업과 swap을 끄는 명령어들이 포함되어 있다. ubuntu인 경우나 위 내용을 포함하고 싶지 않으면 그냥 직접 설치해도 무방.

```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kube*
EOF

yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
systemctl enable kubelet && systemctl start kubelet
```


### setup cluster

클러스터를 설치할 마스터 노드에서 kubeadm 셋업을 해준다.

"Your Kubernetes control-plane has initialized successfully!" 메세지와 함께 이후 사용할 명령어들이 나온다.

```
$ kubeadm init

...
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join xxx.xxx.xxx.xxx:6443 --token 1vn3ax.2n4se0yq2sptl4rq \
    --discovery-token-ca-cert-hash sha256:a248ab87a8c08c26424720e1d0b91e5a6a83c92d94d724c42f7fa9a0fa882390
```

위의 `kubeadm join ...` 명령어를 복사해두고 (파일에 써두면 좋다) 나머지 명령어를 실행.

```
$ mkdir -p $HOME/.kube
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

이제 cluster-info를 확인 가능하다.

```
$ kc cluster-info
Kubernetes control plane is running at https://xxx.xx.xx.xxx:6443
KubeDNS is running at https://xxx.xx.xx.xxx:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

### Add pot network add-on

kubernetes network는 기본으로 제공되지 않기 때문에 add-on을 설치해야한다.

여러 CNI add on이 있지만 여기서는 weave를 사용해볼 예정.

> 해당 명령어는 [kubernetes-docs](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/high-availability/#steps-for-the-first-control-plane-node)에서도 찾을 수 있다.

```
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

이제 pod(혹은 service account)를 확인하여 add-on이 정상적으로 설치되었는지 확인해보자.

```
$ kubectl get pods -n kube-system
NAME                                            READY   STATUS    RESTARTS   AGE
coredns-74ff55c5b-jl5qc                         1/1     Running   0          21m
coredns-74ff55c5b-nntz8                         1/1     Running   0          21m
etcd-localhost.localdomain                      1/1     Running   0          21m
kube-apiserver-localhost.localdomain            1/1     Running   0          21m
kube-controller-manager-localhost.localdomain   1/1     Running   0          21m
kube-proxy-fmwfn                                1/1     Running   0          21m
kube-scheduler-localhost.localdomain            1/1     Running   0          21m
weave-net-lj8gw                                 2/2     Running   0          7m50s
```

그리고 위에서 복사해두었던 `kubeadm join ...` 명령어를 cluster, worker 서버 모두에서 실행시켜준다.

```
$ kubeadm join xxx.xx.xx.xxx:6443 --token 1vn3ax.2n4se0yq2sptl4rq \
        --discovery-token-ca-cert-hash sha256:a248ab87a8c08c26424720e1d0b91e5a6a83c92d94d724c42f7fa9a0fa882390
        
$ ssh worker1
$ kubeadm join xxx.xx.xx.xxx:6443 --token 1vn3ax.2n4se0yq2sptl4rq \
        --discovery-token-ca-cert-hash sha256:a248ab87a8c08c26424720e1d0b91e5a6a83c92d94d724c42f7fa9a0fa882390
```

이제 worker의 노드가 잘 연결되었는지 확인.

```
$ kubectl get nodes
NAME                    STATUS   ROLES                  AGE     VERSION
localhost.localdomain   Ready    control-plane,master   21m     v1.20.0
trans1                  Ready    <none>                 9m22s   v1.20.0
```
