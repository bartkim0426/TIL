# Kubernetes in action

## 1. Introducing Kubernetes


- 단일규모의 큰 앱의 다양한 단점
- 마이크로서비스들의 등장
- 이들을 오케스트레이션해줄 기능이 필요

- 적은 수일 경우에는 VM으로도 가능
  - hardware resources
  - human resources

- VM 대신 Linux container technologies
  - run multiple services on the same host machine
  - isolating environment like VM; less overhead

- container는 host의 OS에서 작동하지만 각 프로세스들은 isolated

#### VM과 비교하면?

- single isolated process running in the host OS, consuming only the resources that the app consumes and without the overhead of any additional processes. 
 
[!image](https://learning.oreilly.com/library/view/kubernetes-in-action/9781617293726/01fig05.jpg)

- VM의: 각각의 linux kernel 사용 -> 완전히 독립
- Container: run greater numbers of isolated processes on the same machine, containers are a much better choice because of their low overhead.


#### 어떻게 각 컨테이너들이 독립적으로 운영되는지?
- Linux Namespaces: 각각의 프로세스들이 각자의 system을 보게 (파일, process, network interface ...)
- Linux Control Groups (cgroups): 각각의 프로세스들이 쓸 수 있는 리소스 제한 (CPU, memory, network bandwidth ...)

#### Linux Namespace
- 기본으로 리눅스 시스템은 한개의 namespace를 가짐
- 추가 가능
- 다음과 같은 namespace 종류가 있음
  - Mount (mnt)
  - Process ID (pid)
  - Network (net)
  - Inter-process communication (ipc)
  - UTS
  - User ID (user)



