# why docker and container

aws immersion day에서 들은 강의를 정리한 내용.

# 왜 컨테이너 기술이 생겨났을까?


## 1. 큰 변화의 흐름

- 아키텍쳐의 변화
  - 컨테이너는 변화의 흐름이 이어져서 발생한 기술
  - 글로벌화, 디지털화
  - 민접하고 유연한 아키텍쳐의 중요성 등장
  - 그래서 등장하게 된 마이크로 서비스 아키텍처

- 운영 모델의 변화
  - 마이크로 서비스 아키텍쳐로 여러 컴포넌트를 운영하게 됨
  - 비즈니스 로직에 집중하고 운영 부담은 managed service로 (AWS, GCP)

- 소프트웨어 딜리버리 방식의 변화
  - 개발, 릴리즈 사이클이 아주 짧아짐
  - 자동화, 툴 표준화
  - 수작업보다 코드 레벨로 infra structure를 관리

결국 Container는 이런 모던 애플리케이션으로 가는 좋은 방법

## 2. 새로운 패러다임

### **Immutable Infrastructure** (절대 불변의 인프라)

한번 배포된 이후에는 서버는 절대 변경되지 않는다. 변경이 필요하면 새로운 서버는 공통된 이미지 위에서 변경된 부분만 배포한다.

Snowflake vs Phoenix

- Snowflake: 모양이 모두 제각각. 서버를 운영하면서 서로 다른 디펜던시를 갖게 되면서 Snowflake처럼 각각 다르게 됨.
- 재배포 등에 문제가 발생할 가능성

- Phoenix: 서버에 문제가 있거나 재배포가 필요할 때 유연하게 재생성, recovery가 가능하다

즉, host의 os와 운영 환경을 분리. 서비스가 업데이트 되면 운영 환경은 변경하지 않고 application image를 새로 배포

#### 장점
- 가볍다: application 구동에 필요한 것만 패키징
- Run anywhere
- Consistent environment: 최종 위치와 관계 없이 일관성 있게 배포 가능

결국 container는 이 Immutable Infrastructure를 구현하는 방식.


# 컨테이너란?

- application 구동에 필요한 모든 것을 실행 가능하게 묶은 일종의 소프트웨어 패키지
- CPU, Memory, storage, network 리소스를 OS 수준에서 가상화하여 논리적으로 격리된 샌드박스 환경 제공
- 원래 예전부터 있었지만 여러 불편함으로 사용되지 않았음
- docker의 등장으로 아주 쉬워짐


![docker-img](https://miro.medium.com/max/1886/1*nM48U9Cxi6UeeQyCFZZzMA.png)

- 펭귄: linux. container의 정의서를 확인
- 거북이: application package
- 문어: docker

### VM vs Docker

![vm-vs-docker-img](https://miro.medium.com/max/1153/0*vaQjmFLTAFD5qOJK.png)

### 도커가 포함하는 것들

- Runtime Engine
- dependencies
- code



