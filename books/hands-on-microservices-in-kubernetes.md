# Hands on microservices in kubernetes#


- Start reading: 2020-07-15
- End reading:


## 6장. 쿠버네티스에서 마이크로서비스 보안


### 사용자 어카운트, 서비스 어카운트

> account는 k8s의 핵심 개념. k8s API 서버에 들어오는 모든 요청은 API 서버를 사용하기 전에 API 서버의 인증, 승인할 특정 어카운터에서 시작해야한다.

사용자, 서비스 두 account는 전혀 다른 것으로 생각해야하는게 맞을듯 하다.

### 사용자 어카운트

kubernetes와는 무관하고 `kubectl`이나 프로그래밍 방식으로 외부에서 k8s를 운영하는 클러스터 관리자 또는 개발자를 위한 account이다.

`~/.kube/config/`에 저장되며 여러개 클러스터를 작업하면 여러 클러스터, 사용자, 컨텍스트가 있다.

클러스터별로 별도의 구성 파일을 갖고 `KUBECONFIG` 환경변수를 사용해 전환하는 것도 가능하다고 함.

### 서비스 어카운트



