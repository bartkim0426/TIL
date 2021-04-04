# amazon sqs faq


## SQS introduction

Amazon Simple Queue (SQS) service: 완전관리형 메세지 대기열 서비스

### 기능

#### 대기열 유형
- 표준 대기열
  - 거의 무제한의 초당 트랜젝션 (TPS)
  - 최소한 한번 전달 (가끔 2개 전달될수 있음)
  - 전송된 순서와 다르게 전달될 수 있음
- FIFO 대기열
  - 정확히 한번, FIFO 
  - 초당 300개의 메세지
  - -> 이벤트 순서가 중요하거나 중복 항목이 허용되지 않는 경우에 사용

## FAQ

### 개요, 특징, 기능, 인터페이스

- SQS vs SNS vs MQ
  - SNS: 애플리케이션에서 no polling. push 메커니즘을 통해 메세지 보낼 수 있음.
  - SQS: 분산 애플리케이션에서 polling 모델을 통해 메세지를 교환하는데 사용 (송신/수신)
  - MQ: 관리형 rabbitmq. 기존 메세징 애플리케이션에서 클라우드로 빠르게 이동하고 싶으면 사용

- SQS vs Kinesis Stream
  - SQS: 애플리케이션 or 마이크로서비스 간 이동할 때 메세지를 저장할 수 있는 대기열 제공
    - 분산된 애플리케이션 요소간 데이터 이동 가능
  - Kinesis stream
    - 빅데이터 스트리밍을 실시간으로 처리
    - 여러 kinesis 애플리케이션 레코드를 읽고 응답 가능

- SQS long polling이란?
  - SQS 대기열에서 메세지를 검색하는 방법
  - short polling: 메세지 대기열이 비어있는 경우에도 즉시 응답
  - long polling: 응답 수신 횟수를 줄여 비용 절감 가능
- 언제 long, short 사용해야하는지?
  - 거의 모든 경우 long polling 사용하는게 좋음: 비용 감소, 성능 향상

### FIFO 대기열
- message group이란?
  - FIFO 대기열에서 메세지는 서로 구별되고 순서가 지정된 번들로 그룹화
  - 각 그룹 내에서 모든 메세지가 순서대로 전송/수신
  - 그룹 id가 다른 메세지는 다른 순서로 전송/수신

### SSE (서버측암호화)
- SSE 이점?
  - 암호화된 대기열에서 민감한 데이터 전송 가능
- 암호화된 대기열에서도 SNS, CloudWatch, S3 event 사용 가능
- SSE를 사용하려면 필요한 권한
  - AWS KMS 키 정책 구성 (CMK - 고객 마스터 키 사용)
  - 송신: 생산자가 CMK에 대한 kms:GenerateDataKey, kms:Decrpyt
  - 수신: 수신자가 위에서 사용한 CMK에 대한 kms:Decrypt
