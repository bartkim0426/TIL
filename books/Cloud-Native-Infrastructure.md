# Cloud Native Infrastructure

## 01. what is cloud native infrastructure?

### Cloud Native Definition

여러가지 정의가 있음. [CNCF의 cloud native 정의](https://github.com/cncf/toc/blob/master/DEFINITION.md)

책에서는 다음 항목들이 보통 cloud native application에서 추구하는 방법들이라고 소개한다.

- Microservices
- Health reporting
- Telemetry data
  - information necessary for making decisions
  - health reporing과 비슷해 보일 수 있지만 목적이 다름
  - health reporting: application life cycle state를 알려줌
  - telemetry data: application business objectives를 알려줌 (?)
  - 다른 용어로 SLIs (sevice-level indicators), KPIs (key performance indicators)
  - 최소한 `RED` metrics (Rate, Error, Duration 수집)
  - alerting에 사용되는게 맞음
  - 디버깅에 쓰이는 logging과도 헷갈리면 안됨. 
    - 종종 Metric이 logs에서 계산되기도 하지만 추가적인 aggregation이나 processcing을 필요로함
- Resiliency
  - Infrastructure는 실패를 지양하게 설계된다.
  - cloud native application에서는 애플리케이션이 실패를 피하기보다는 이를 수용하게 설계되어야 함
  - 두가지 관점: design for failure, graceful degradation
  - `design for failure`
    - 서비스가 절대 죽지 않으면, business value보다 실패를 피하기 위해 많은 엔지니어링 시간을 쏟고 있는 것
    - application에 가장 좋은 상태는 healthy. 두번째로 좋은 상태는 failed.
      - 이 두 가지 상태가 아닌 다른 모든 상태는 모니터링과 트러블슈팅에 어려움을 겪는다
  - `graceeful degradation`
- Declarative, Not Reactivate


