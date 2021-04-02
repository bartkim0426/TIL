# AWS Cert SAA dump questions

## 모르는개념

- S3
- SNS
- FSx
- Kinesis data
  - firehose
  - stream
  - analysis
- AWS Global accelerator


## 1. O

route53 & ALB: backup static error page를 효율적으로 붙이는 방법
- a. cloudfront? 굳이 쓸필요 없음
- b. S3: cloudfront보다 비싼가? active-passive failover configuration을 모르겠음
- c. latency-based routing policy가 뭔지 모르겠음
- d. EC2: 비쌈

b/b
- c 아닐거같은 이유: latency-based면 서버가 괜찮은 상황에서도 static error redirection이 발생할수도 있음

#### 개념정리
- active-passive failover
  - primary resource or group이 unavailable 해졌을 경우에 second group에게 넘김

### 2. O

:warning:

EC2에 HPC (high performance computing)
- need to communicate each other frequently
- require network performance with low latency and high throughput
- a. cluster placement group
- b. spread placement group -> 'spread'라서 아닐거같음
- c. auto scaling group in two regions -> two region? 당연히 아님
- d. auto scaling group in multiple availability zone -> 당연히 이거일거같은데

a/a

- a, d 헷갈림: d가 함정인거같고, cluster placement group에 대해 모름
- multiple 보다 one이 빠를거같아서 a

#### 개념
EC2
- placement groups
  - EC2 instance를 런칭하면 기본적으로는 correlated failures를 최소화하기 위해 instance를 spread out 하려고 함
  - placement groups을 사용하여 연관된 인스턴스를 묶을 수 있음

### 3. X 

:x:

[ ] 논의

scalable web app (?) -> 여러 지역에서 접근. GB 사이즈로 데이터 업로드/다운로드.
- minimize upload & download latency / maximize performance
- a. S3 transfer acceleration -> 정확히 모름. 뭔가 맞을거같은데..
- b. S3 cachecontrol headers -> 정확히 모름
- c. EC2 auto scaling and cloudfront -> 당연히 맞는거같은데.. cloudfront 쓰는정도로는 부족 + host하는게 말이 안맞음
- d. EC2 auto scaling and elasticcache -> 아님


a/c
- a/b중 하나. 이름으로 봤을땐 a같음
- 답은 c. a, b는 함정인듯 ㅠㅠ
- discussion 보니 a, c 갈리는듯.
- 한번 논의해보면 좋을듯. 잘 모르겠다

### 4 O

윈도우 파일서버인 DFSR (distributed file system replication)을 대체해야됨
- a. EFS
- b. FSx
- c. S3 -> X
- d. Storage Gateway -> X

b/b
- DFSR가 뭔진 잘 모르겠지만 파일시스템같음
- c, d는 아님. b가 File System 약자같아서 선택

#### 개념
- FSx: File System
  - FSx with AWS DataSync로 migrate 가능

### 5. O

레거시 application. 2번쨰가 첫번째보다 오래걸림. scale independently하게 two microservice로 만들기. integrate 하는 방법은?
- api gateway, sqs, sns정도?

- a. s3 - event notification -> X
- b. sns - subscribe  -> noti만 주고 coupling이 안됨
- c. kinesis data firehose -> 잘 모르겠지만 kinesis면 데이터 파이프라인?
- d. SQS


d/d
- 두 앱이 얼마나 밀접하게 연관되었는지에 따라 다를듯한데.. 
- 1 -> 2로 태스크 결과가 의존성이 있다면 sqs가 맞을듯하여 d

#### 개념
- SNS; simple notification service
- Kinesis data firehose: 데이트 스트리밍 (data lake, data store)

### 6. X
:x:

여러 웹에서 clickstream data -> batch analysis -> redshift.

real time data processing을 하고싶어함. MOST cost-effective combination (2개)
- 생각나는 방법: kinesis + lambda

- a. ec2
- b. lambda
- c. kinesis data stream
- d. kinesis data firehose
- e. kinesis data analytics

c,d/bd
- kinesis를 상세히 몰라서 고르기 어려움. stream + forehose 써야할거같아서 고름
- 역시 처음 생각했던 lambda가 맞음.. ㅠㅠ
- redshift 사용중이라 warehouse는 필요없다고함

### 7. O

ALB 안에 EC2 (auto scaling in multiple zones) month-end에 batch가 돌아서 느려짐 (cpu 100%)

downtime을 피하고 workload를 handle할수있는 방법은?

- a. cloudfront in ALB -> X
- b. simple scaling policy based on CPU -> 이거같음
- c. scheduled scaling policy -> 이것도 맞아보임
- d. Elasticcache -> X

c/c
- b, c 고민됨. immediately peak이라서 c 고름 (반응이 느릴거같음)

### 8.  :warning:

multi-tier web application. EC2 (ALB), auto scaling (multi zone), Aurora db.

more resilient to periodic increases in request rate (2개)

- a. AWS shield -> 모름
- b. Aurora replica -> 
- c. Direct Connect -> 모름
- d. Global accelerator -> 모름. accelerator라 맞을거같기도
- e. cloudfront -> O


d,e/d,e
- periodic increase라고 했으니 e는 맞을듯
- d가 맞을거같기도..
- b, c, d중에 고민. b는 replica라 아닐듯. c는 뭔지 모름

#### 개념
- AWS Global accelerator
  - Acceleration for latency-sensitive applications
  - Global Accelerator directs user traffic to the application endpoint 
  - cdn이랑 다른건 뭔지?

### 9.



