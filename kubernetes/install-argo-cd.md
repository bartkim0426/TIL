## Install and setup ArgooCD

### Install ArgoCD

```
# create argocd namespace
kubectl create namespace argocd

# install argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# check installed
kubectl get all -n argocd
```

### Download ArgoCD CLI

```
# Install argocd CLI (mac os X)
brew tap argoproj/tap
brew install argoproj/tap/argocd
```

### Accessing ArgoCD API

3가지 방법중에 하나 선택

- Load balancer

```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

- Ingress: [argocd documentation](https://argoproj.github.io/argo-cd/operator-manual/ingress/) 참고

- port-forward: local minikube에서 설치하기에 가장 간단하므로 이걸로 테스트해봄.

```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

### Login using CLI

최초 패스워드는 Argo CD API server의 pod name. 다음 명령어로 구할 수 있다.

```
kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o name | cut -d'/' -f 2
```

username은 `admin`이다. 다음 명령어로 로그인

```
$ argocd login :8080
WARNING: server certificate had error: tls: either ServerName or InsecureSkipVerify must be specified in the tls.Config. Proceed insecurely (y/n)? y
Username: admin
Password: <위에서 구한 pod anme>

# Update passsword
argocd account update-password
```

이제 `localhost:8080`으로 접근 가능하다.

### Comments

argo cd ui를 보니 아주 훌륭하다! 물론 대부분의 개발자들은 k8s를 cli로 컨트롤하고 모니터링 하겠지만, 그럼에도 시각적으로 상태를 보여주고 많은 기능을 제공한다는 점에서 충분히 사용해 볼 법 하다.

실제 회사 클러스터의 대시보드 정도로 사용을 시작해서 실제 CD에 붙여보면 어떨까 생각이 들었다.

helm 패키지를 어떤식으로 배포 가능할지는 한 번 살펴보는게 좋겠다.

