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


