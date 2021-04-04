# AWS Cert SAA dump questions

## 모르는개념

- Lambda
  - pattern: "API gateway + unpredictable request pattern => Lambda"
- EC2
  - instance store: provide temporary block-level storage for insatnces.
- S3
  - S3 glacier
  - standard-IA
  - CRR(corss-region replication): used to copy objects across s3 in different region
- SNS
- FSx
- Kinesis data
  - firehose
  - stream
  - analysis
- AWS Global accelerator
- RDS
  - read replicas
- Aurora
  - multi-az standby instance
- Direct Connect: AWS로 전용 네트워크 연결을 쉽게 설정할 수 있는 클라우드 서비스 솔루션
- Site-to-site VPN
- Snowball
- Cloudfront
  - geo restriction
- amazon Elasitc File System (EFS): NFS file system
- Route53
  - geoproximity feature: Use when you want to route traffic based on the location of your users.


## 1. :white_check_mark:

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

### 2. :warning:

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

### 3. :x:

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

### 4 :white_check_mark:

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

### 5. :white_check_mark:

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

### 6. :x:

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

### 7. :white_check_mark:

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

### 9.  :warning:

Aurora multi-az deployment db. read -> high I/O. read/write request 분리하는 방법?

- A. Enable read-through caching on the Amazon Aurora database. -> X
- B. Update the application to read from the Multi-AZ standby instance.
- C. Create a read replica and modify the application to use the appropriate endpoint.
- D. Create a second Amazon Aurora database and link it to the primary database as a read replica. -> X

c/c
- aurora를 안써봐서 잘 모르겠음
- a가 가장 적절해보이지만, 분리가 되지 않음. d는 무조건 아님
- b, c중에? multi-az standby instance가 뭔지 모르겠음.


### 10.  :x:

migrate to cloud multiple applications within a month. each 50TB to be transferred. secure network & consistent throughput for data center. must one-time data migrations and ongoing network connect

- a. direct connect for initial transfer and ongoing connectivity
- b. site-to-site vpn for inital transfer and ongoing connectivity
- c. snowball for initial transfer and direct connect to ongoing
- d. snwoball for initla and site-to-site vpn for ongoing

d/c
- 전체적으로 모르는 개념
- transfer에는 snowball이 맞을거같음
- direct connect vs site-to-site vpn? 기존 앱들이 연결되어야하니 site-to-site가 맞을듯
- direct connect에 대해서 공부해야함:  AWS로 전용 네트워크 연결을 쉽게 설정할 수 있는 클라우드 서비스 솔루션


### 11. :x:

worldwild, EC2 in private subnet ALB. block access from countries

- a. ALB security group -> O
- b. sg in EC2 -> X
- c. Cloudfront -> 흠.. 
- d. ALB listeners rule -> 잘 모름

a/c
- cloudfront로 막을 수 있는지 잘 모르겠음
- ALB listeners rule을 잘 모르겠음
- cloudfront의 geo restriction을 활용해야한다고 함
- network, sg등에 대해 기본기를 쌓아야할듯

개념: geo restriction

### 12. :x:

store large data. analyzed hourly, modified by EC2. more space needed grow for 6 month

- a. EBS volume; mount volume to application instance
- b. EFS; mount file system on instance
- c. S3 glacier; update valut policy to allow access
- d. S3 standard-Infrequenent Access; update bucket policy

a/b

- 전체적으로 모르겠음
- hourly 분석이니 d는 아닌거같음
- b file system은 적절하지 않아보임
- S3 glacier, standard IA는 모르겠음
- > 아닐거라고 생각헀던 b 가 답

개념;
- amazon Elasitc File System (EFS)
- S3 glacier, standard-IA

### 13. :warning:

3-tier app. MySQL db. poor performance when create. (generating real-time reports during work hours)

- a. DynamoDB
- b. database in EC2 -> X
- c. Aurora mysql with read replicas -> create라서 X
- d. Aurora mysql, report endpoint로 backup instance

c/c
- c, d중 하나일거같음
- dynamo를 사용하게 해도 될거같긴 한데..
- c인거같음. backupd instance는 왜 써야하는지 잘 몰겠음
- Q) generating인데 왜 read replica 생성하는게 답인지??

### 14. :x:
distributed db on multiple EC2. requires block storage to several million transactions per second per server

- a. EBS
- b. EC2 instance store -> X
- c. EFS -> X
- d. S3 -> X

a/b
- 당연히 a일듯
- EC2 instance store라는게 있는줄 몰랐음
- EBS도 사용 가능하지만, 64K i/o ps라서 아닌듯
- EC2 -> local storage이기 때문에 less latency

### 15. :warning:

daily reports in static html. millions of views from world. file is in S3. efficient & effective?

- a. presigned URLs
- b. corss-Region replication
- c. geoproximity feature of Route53
- d. cloudfront

d/d
- 얼핏 d가 맞아 보이긴 함
- geoproximity feature, cross-region replication이 뭔지 모르겠음
- a도 은근 맞을거같기도.. 하지만 static serving에 적합하지 않아보임

개념;
- geoproximity feature: Use when you want to route traffic based on the location of your users.
  - 이경우 다른곳으로 routing 할 필요가 없기 때문에 X
- CRR(corss-region replication): used to copy objects across s3 in different region
  - 전혀 다름

### 16. :x:

API Gateway. unpredictable reequests, suddenly 0 to 500 ps, data size persisted to 1GB, quried key-value (chose 2)

- a. Fargate
- b. Lambda
- c. DynamoDB -> O
- d. EC2 Auto scailing
- e. MySQL-compatible Aurora -> X (key-value)


c,d/b,c
- 일단 key-value -> e는 아니고 c가 적합
- 나머지 한개는 갑자기 증가 -> d?
- Fargate는 잘 모르겠음
- -> lambda가 정답: 왜?
  - "API gateway + unpredictable request pattern => Lambda" 라고 함
  - lambda autoscale은 500 ps까지 손쉽게 확장 가능
  - EC2 autoscale은 그만큼 빠르게 스케일링 되지 않는다고 함

### 17.


