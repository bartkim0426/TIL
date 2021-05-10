## Gitops?

gitops is a way of implementing Continuous Deployment for cloud native applications.

https://www.gitops.tech/#what-is-gitops

### Gitops의 장점

- Deploy faster more often
- Easy and fast error recovery
  - `git revert`만으로 롤백 가능
- Easier credential management: 요부분은 잘 이해가 안됨
- Self-documenting deployments
- Shared knowledge in teams

### gitops가 동작하는 방식

#### Environment configuration as Git repository

최소 두개의 레파지토리로 구성
- application repository: 소스코드
- environment configuration repository: 배포 환경에 대한 manifest

### Push-based vs Pull-based deployment

GitOps에는 두가지 방식이 있음.

가능하다면 pull-based로 구현하는게 좋음

#### push-based deployment

일반적인 CI/CD에서 사용하는 방식

application의 source code와 kubernetes yaml이 같이 있고, repository가 업데이트 되면 빌드 pipeline이 트리거 되는 방식.

![image](https://www.gitops.tech/images/push.png)

- 배포 환경에 대한 credential을 넣어주여야됨
- 배포 환경의 변경을 인식하지 못함 (desired state)

#### pull-based deployment

push-based와 같은 컨셉이지만, pipeline이 작동하는 방식이 다름.

전통적인 CI/CD pipeline은 외부 이벤트에 의해서 트리거됨 (ex. repository에 코드가 푸쉬되는 등)

pull-based deployment에서는 "operator" 개념이 도입됨
- environment repository의 desired state와 실제 배포된 infrastructure 상태를 계속 비교하는 역할
- 차이가 발견되면 operator가 infrastructure를 environment repository와 같도록 업뎅티ㅡ
- image repository에 새로운 버전의 image가 있는지 모니터링도 해 줄 수 있음

![image](https://www.gitops.tech/images/pull.png)

push-based 배포처럼 environment repository의 변화에 의해서 업데이트가 발생.

이와 더불어 반대 방향으로도 변경 가능.

배포된 인프라 환경이 environment repository에서 정의된것과 다르게 변하면, 이 변경 사항은 reverted됨.
이는 모든 변경사항이 git log로 추적 가능하게 하고, cluster에 직접적인 변경을 가하는것은 불가능하게 만들어줌.

그렇다고 모든 monitoring이 필요 없어지는것은 아님. 어떤 이유로 environment의 desired state를 가져오는데 실패하면 (ex. cannot pull a container image) 이를 slack이나 mail로 noti 해줄수 있음.

Operator는 애플리케이션이 배포된 같은 환경(혹은 클러스터)에 있어야됨.
- 기존 CI/CD 파이프라인에서 credential이 필요한 문제 등을 해결할 수 있음

### Working with multiple applications and environments

environment repository에서 여러 branch를 나눠서 (ex. staging, master) 여러 환경을 구성할 수 있다.
operator나 deployment pipeline이 각각의 branch의 변화를 알맞는 환경에 배포하게 설정 가능.


