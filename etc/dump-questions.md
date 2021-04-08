# AWS Cert SAA dump questions

## 모르는개념

- Lambda
  - pattern: "API gateway + unpredictable request pattern => Lambda"
- EC2
  - instance store: provide temporary block-level storage for insatnces.
  - ENI: Elastic Network Interface
    - VPC에서 가상 네트워크 카드를 나타내는 논리적 네트워킹 구성 요서
    - private IPv4, public IPv4, IPv6, MAC address, Security Groups ... 등이 포하
  - spot block instance
    - 온디맨드 가격보다 저렴하게 사용 할 수 있는 미사용 EC2 인스턴스
    - 애플리케이션 시간을 유연하게 조정할 수 있고 애플리케이션을 중단할 수 있는 경우에 선택
      - 데이터 분석, 배치 작업, 백그라운드 프로세싱 및 선택적 작업
  - on-demand instance: 보통의 instance (사용한만큼)
  - reserved instance
    - 예약 인스턴스. 온디맨드보다 훨씬 저렴 
    - 할인된 시간당 요금과 용량 예약(선택)을 제공
    - EC2 인스턴스 사용 속성이 활성 RI의 속성과 일치하면 할인 요금을 적용
    - Standard RIs (표준 RI)
      - 사용량이 꾸준한 경우에 적합
      - 가장 큰 할인
    - Convertible RIs (컨버터블 RI)
      - RI 속성 변경 가능
      - 꾸준한 경우에 적합
    - Scheduled RIs: scheduled reserved instance (scheduled instance)
        - daily, weekly, monthly basis로 capacity reservation을 가능하게 해줌
        - 특정 시간과 기간에 1년 단위로 예약 가능. 그 시간동안에는 사용 안해도 비용청구
        - 정기적인 스케쥴로 돌아가는, not continuous 한 작업에 적합함
- S3
  - S3 standard-IA: 일반적인 S3
  - S3 Glacier: 데이터 아카이빙 및 백업을 위한 스토리지
  - S3 Intelligent-Tiering: 데이터 엑세스 패턴이 변경될 때 스토리지 비용을 자동으로 최적화
    - frequent access / infrequent access 간에 티어 변경
  - S3 One Zone-Infrequent Access: 엑세시 빈도가 낮은 데이터 + 다중 가용 영역 데이터 복원 모델 X
    - 낮은 복원 기능으로 비용 절감
  - CRR(corss-region replication): used to copy objects across s3 in different region
  - S3 interface, S3 gateway endpoint: 둘 다 PrivateLink라는 기능으로 제공됨
    - PrivateLink: VPC에서 endpoint를 프로비저닝해서 다른 애플리케이션에서 엑세스 가능
    - gateway endpoint: VPC -> aws network -> S3 (요금 미청구)
    - interface endpoint: VPC/on premise -> private IP address -> S3 (요금 청구됨)
      - 다른 region, on premise 서버에서 쓸려면 interface endpoint 사용
- VPC
  - route table이 정확히 뭐지?
    - 서브넷 또는 게이트웨이의 네트워크 트래픽이 전송되는 위치를 결정하는데 사용되는 라우팅 규칙
  - SG (security group); 방화벽
  - Gateway VPC endopints
    - route table에서 트래픽이 어떤 서비스로 갈지 결정해줌
    - S3, DynamoDB 사용 가능
  - alb cross-region vpc
    - 다른 리전의 aws 리소스가 private ip로 통신 가능하게 함
- SNS
- FSx
  - FSx lustre: fast and scalable shared storage -> HPC 등에 적합
- Kinesis data
  - firehose
  - stream
  - analysis
- AWS Global accelerator: 글로벌 네트워크 인프라로 트래픽 전송하여 성능 개선
  - Endpoints in global accelerator: NLB, ALB, EC2, elastic ip 뭐든지 가능
- RDS
  - read replicas
  - encrypt RDS snapshot
    - MySQL, Oracle, SQL Server, PostgreSQL, MariaDB 지원
    - Auroua: unencrypted snapshot을 restore 할 때 KMS key를 지정해줘야됨
    - Amazon RDS DB 인스턴스에 대한 암호화는 암호화를 생성할 때에만 활성화할 수 있으며 DB 인스턴스가 생성된 후에는 불가능합니다.
      - not encrypted RDS에서 바로 encryption 할 수 없음
      - not encrypted RDS <-> not encrypted Snapshot
      - encrypted RDS <-> encrypted Snapshot
      - not encrypted snapshot <-> encrypted snapshot
      - 암호화된 DB 인스턴스의 스냅샷은 DB 인스턴스와 동일한 CMK를 사용하여 암호화해야 합니다.
      - 암호화되지 않은 DB 인스턴스의 암호화된 읽기 전용 복제본이나 암호화된 DB 인스턴스의 암호화되지 않은 읽기 전용 복제본은 보유할 수 없습니다.
  - 암호화된 스냅샷을 한 AWS 리전에서 다른 리전으로 복사하려면 대상 AWS 리전에 CMK를 지정해야 합니다. 이는 CMK가 생성된 AWS 리전에만 해당하기 때문입니다.
  - failover: 장애 조치. 
    - 보통 multi-AZ를 활성화하면 장애시 다른 가용 영역의 복제본으로 전환해줌 (60-120초)
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
  - latency-based route53: 지연 속도 기반 라우팅
    - routing시 request의 지역 기반으로 더 지연이 낮은 리전의 주소 반환
  - route53 policy; 레코드를 생성할 떄 선택. route53이 쿼리에 응답하는 방식을 결정
    - simple routing; 도메인에 대해 특정 기능을 수행하는 하나의 리소스만 있는 경우
    - failover routing: active-passive 장애 조치 구성
      - 리소스가 정상일 경우 해당 리소스로 라우팅
      - 비정상일 경우 다른 리소스로 라우팅
    - geolocation routing
      - 사용자 위치 (DNS 쿼리가 발생한 위치의 IP) 기반으로 트래픽 제공하려는 리소스 선택 가능
    - geoproximity routing
      - 리소스 위치 기반으로 트래픽 라우팅.
      - traffic flow를 사용해야됨
    - latency-based routing; 여러 리전에 리소스가 있고 최상의 지연 시간을 제공하는 리전으로 라우팅
    - multivalue answer routing; 무작위로 선택된 최대 8개의 정상 레코드로 응답
    - weighted routing; 사용자가 정한 비율에 따라 여러 소스로 라우팅
- WAF: Web Application Firewall
  - 웹 공격으로부터 application을 보호하는 방화벽
  - ALB에 붙여야됨
    - CLB에는 붙일 수 없음


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

wordwide, EC2 in private subnet ALB. block access from countries

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

### 17. :warning:

us-east-1 EC2 (multi region ALB), grow in us-west-1 -> low latency, high availablity

- a. network load balancer to cross-region load balancing
- b. load balancer distribute traffic based on location
- c. global accelerator
- d. route53

c/c
- 전체적으로 잘 모르겠음
- load balancer configuration 필요한지?
- a; 이미 cross-region 되고 있어서 아닌듯
- b; distribute도 이미 하고있을듯? ALB를 만들필요는 없음
- c; global accelerator가 뭐하는지
- d; route53에서 나눌필요는 없어보임

### 18. :white_check_mark:

access a catalog and customize image, API gateway, received link. MOST cost-effective

- a. EC2, S3, ELB -> high cost
- b. Lambda, S3, cloudfront
- c. Lambda, S3, DynamoDB, ELB, EC2
- d. EC2, S3, Dynamodb, cloudfront

b/b
- lambda 쓰는게 맞아보임 -> b, c
- s3에 manipulated image 저장 가능? metadata는 저장할 필요 없으니깐

### 19. 
migrate busniess-critical dataset to S3, us-east-1 single bucket -> multiple regions

c/c
- cross-region replication vs CORS -> cross-region
- bucket with vs without versioning -> with (지금 versioning 되어있으니깐?)

### 20. :warning:

EC2 in VPC, S3 API to store and read, restrict internet-bound traffic.

- a. S3 interface
- b. S3 gateway
- c. S3 bucket in private subnet
- d. S3 bucket in same region -> X

b/b
- interface, gateway를 잘 모르겠음. gateway가 더 맞는듯?
- private subnet에 s3 bucket을 만들수 있나? 상관 없음
개념;
- S3 interface, S3 gateway endpoint: 둘 다 PrivateLink라는 기능으로 제공됨
  - PrivateLink: VPC에서 endpoint를 프로비저닝해서 다른 애플리케이션에서 엑세스 가능
  - gateway endpoint: VPC -> aws network -> S3 (요금 미청구)
  - interface endpoint: VPC/on premise -> private IP address -> S3 (요금 청구됨)
    - 다른 region, on premise 서버에서 쓸려면 interface endpoint 사용

### 21. :white_check_mark:

RDS postgres db, large query in every start of month, minimize impact to web application, LEAST amount of effort?

- a. read replica
- b. multi-AZ database -> X
- c. cross-region read replica
- d. redshift -> X

a/a
- d -> X

### 22. :warning:

on-premise HPC application and data to AWS, tiered storage(?), hot parallel storage during run, cold strage for hold data when not running.

- a. S3 for cold
- b. EFS for cold
- c. S3 for high -> X
- d. FSx for Lustre for high
- e. FSx for windows for high

a,d/ad
- S3 vs EFS -> 둘다 가능해보이는데, S3가 더 저렴?
- FSx Lustre vs windows -> Lustre가 뭔지 모르겠음

### 23. :x:

EC2 single Region, deployed second region when disaster occured.

- a. detach volume on EC2 and copy it to S3
- b. Ec2 from AMI
- c. EC2 in new region and copy volume for S3 -> x
- d. copy AMI and specify different region
- e. copy EBS from S3 and launce EC2 -> x


a,d/b,d
- AMI 가 들어가긴 해야할듯
- can be deployed니깐 지금 ec2 배포할필요는 없음?
- volume 고려해야하나?
- d에 런칭이 포함된줄 알았는데 아니였네;; 이런 경우 a-z를 고려해야될듯

### 24. :x:

call api to DynamoDB from EC2 in VPC (do not traverse the internet)

- a. route table entry for endpoint
- b. gateway endopint for dynamodb
- c. new table -> x
- d. ENI for the endpoint in each subnets of VPC 
- e. SG entry in default SG to privide access

a,d/a,b
- network쪽은 잘 모르겠음
- endpoint + vpc 작업일듯: a or b / d or e
- ENI 뭔지 전혀 모르겠음
- endpoint가 꼭 생성되어야하는지도 잘 모르겠음.
개념;
- VPC endpoint 는 PrivateLink 기능을 통해 internet gateway 없이 연결 가능
- route table이 정확히 뭐지?
  - 서브넷 또는 게이트웨이의 네트워크 트래픽이 전송되는 위치를 결정하는데 사용되는 라우팅 규칙
- gateway endopints: route table에서 트래픽이 어떤 서비스로 갈지 결정해줌
  - S3, DynamoDB 사용 가능
- SG; 방화벽
- ENI: Elastic Network Interface

### 25. :white_check_mark:

RDS MySQL without encryption -> encrypted

- a. data to S3 server-side encryption -> x
- b. RDS Multi-AZ with encryption -> failover
- c. snapshot -> encrypted copy of it -> restore
- d. rds read replica -> promote master and switch

c/c
- S3는 말이 안됨
- failover가 뭔지 잘 모르겠음
- read replica도 이상한방법
개념;
- encrypt RDS snapshot
  - MySQL, Oracle, SQL Server, PostgreSQL, MariaDB 지원
  - Auroua: unencrypted snapshot을 restore 할 때 KMS key를 지정해줘야됨
- failover: 장애 조치. 
  - 보통 multi-AZ를 활성화하면 장애시 다른 가용 영역의 복제본으로 전환해줌 (60-120초)

### 26. :warning:

implement predictive maintenance on its machinery equipment, IoT sensors -> real-time send data to AWS,
- receive event ordered manner for each asset, data is saved
- MOST efficient?


a/a
- realtime events: Kinesis data stream vs SQS vs SQS FIFO
- save data: kinesis firehose to S3/EBS vs Lambda to EFS/S3
- 다 가능한데 효율적인 방법을 찾아야됨
  - EFS, EBS보다 S3가 가성비
  - order -> SQS FIFO, 센서가 많아서 비용이 높을듯
  - kinesis도 순서를 지켜주는지?
    - partition 안에서는 유지
개념;
- SQS FIFO는 IOT에 연결 못한다고함
- Kinbesis는 EBS와 사용 못함

### 27. :x:

EC2 in ALB, dynamic + static content, slow website, wordwide

- a. cloudfront, route53
- b. latency-based route53 for ALB, large CE2
- c. dfferent region Ec2, cross-region VPC
- d. S3 bucket -> X

d/a
- dynamic이기 때문에 d는 아님
- a; route53에서 cloudfront 바라볼수있나? 맞을거같은데. dynamic도 걱정
- b: latency-based route53은 잘 모름, large EC2가 의미가 있을지?
  - users around the globe: 전체적으로 느리기때문에 서버 증설도 효과가 있을듯
  - latency-based; 느려질때 처리하는거같아서 아닐듯
- c: ec2 증설 + alb cross-region -> 효과있을듯

- 함정문제인줄 알고 너무 많이 생각함
개념;
- 다른건 왜 안되는지?
- latency-based route53: 지연 속도 기반 라우팅
  - routing시 더 지연이 낮은 리전의 주소 반환
  - alb가 그대로라서 의미가 없는듯
- alb cross-region vpc
  - 다른 리전의 aws 리소스가 private ip로 통신 가능하게 함
  - 웹과는 맞지 않음

### 28.  :white_check_mark:

analytics data in RDS, users to access data using API, not cold but could bursts.

- a. ECS
- b. Elastic Beanstalk
- c. Lambda
- d. EC2

c/c
- 당연히 lambda

### 29. :warning:

every month beginning, 20 EC2 instances, runs 7 days, cannot interrupped, minimize cost

- a. reserved instance
- b. spot block
- c. on-demand
- d. scheduled reserved

d/d
- 들어는 봤는데 뭔지 잘 모르겠음
- spot이 제일 싼데 interrupped 될수있어서 안됨
- reserved는 비쌀듯
- on-demnad, sceduled-reserved는 잘 모르겠음, 이름에 따라서 d인거같음
개념;
- spot block instance
  - 온디맨드 가격보다 저렴하게 사용 할 수 있는 미사용 EC2 인스턴스
  - 애플리케이션 시간을 유연하게 조정할 수 있고 애플리케이션을 중단할 수 있는 경우에 선택
    - 데이터 분석, 배치 작업, 백그라운드 프로세싱 및 선택적 작업
- on-demand instance: 보통의 instance (사용한만큼)
- reserved instance
  - 예약 인스턴스. 온디맨드보다 훨씬 저렴 
  - 할인된 시간당 요금과 용량 예약(선택)을 제공
  - EC2 인스턴스 사용 속성이 활성 RI의 속성과 일치하면 할인 요금을 적용
  - Standard RIs (표준 RI)
    - 사용량이 꾸준한 경우에 적합
    - 가장 큰 할인
  - Convertible RIs (컨버터블 RI)
    - RI 속성 변경 가능
    - 꾸준한 경우에 적합
  - Scheduled RIs: scheduled reserved instance (scheduled instance)
      - daily, weekly, monthly basis로 capacity reservation을 가능하게 해줌
      - 특정 시간과 기간에 1년 단위로 예약 가능. 그 시간동안에는 사용 안해도 비용청구
      - 정기적인 스케쥴로 돌아가는, not continuous 한 작업에 적합함

### 30. :warning:

multiple EC2 in single AZ, multiplayer game, communicate with Layer 4, HA, cost-effeciive

- a. increase EC2
- b. decrease EC2
- c. NLB
- d. ALB
- e. configure Auto Scaling group in multi-AZ

c,e/c,e
- Layer 4? network 레이어를 말하는건지.. 잘 모르겠음
- multiplayer game, 실시간 communicate?
- increase는 비용떄문에 아닐듯
- decrease + auto scaling? e를 하면 자동으로 감소할거라서 x
- NLB/ALB중 하나 붙이고 auto scaling? -> NLB가 맞는듯 (layer 4라서)
개념;
- Elastic Load Balancer는 둘 이상의 가용 영역에서 EC2 인스턴스, 컨테이너, IP 주소 등 여러 대상에 걸쳐 수신되는 트래픽을 자동으로 분산
  - ALB, NLB, Gateway Load Balancer, Classic Load Balancer 등 지원
- Network Load Balancer
  - NLB 자체가 OSI 모델의 layer 4
  - OSI(Open Systme Interconnection) 모델의 네번째 계층에서 작동
- OSI 모델에 대해서 다시 공부..
  - OS

### 31. :x:

RDS mysql, backup daily, not encrypted -> encrypted.

- a. enbale default encryption for S3 -> X
- b. backup section in rds config (Enable encryption)
- c. snaption -> encrypted snapshot -> restore
- d. encrypted read replica -> primary

b/c
- 잘 모르겠음.. backup 지우기 전에 one encrypted backup 만들어야되는게 핵심인듯
- rds 바로 encryption 만들수 있는지? 그럼 b
- snapshot -> backup 만들어주지는 못함
- replica?
개념;
- Amazon RDS DB 인스턴스에 대한 암호화는 암호화를 생성할 때에만 활성화할 수 있으며 DB 인스턴스가 생성된 후에는 불가능합니다.
  - not encrypted RDS에서 바로 encryption 할 수 없음
  - not encrypted RDS <-> not encrypted Snapshot
  - encrypted RDS <-> encrypted Snapshot
  - not encrypted snapshot <-> encrypted snapshot
  - 암호화된 DB 인스턴스의 스냅샷은 DB 인스턴스와 동일한 CMK를 사용하여 암호화해야 합니다.
  - 암호화되지 않은 DB 인스턴스의 암호화된 읽기 전용 복제본이나 암호화된 DB 인스턴스의 암호화되지 않은 읽기 전용 복제본은 보유할 수 없습니다.
  - 암호화된 스냅샷을 한 AWS 리전에서 다른 리전으로 복사하려면 대상 AWS 리전에 CMK를 지정해야 합니다. 이는 CMK가 생성된 AWS 리전에만 해당하기 때문입니다.


### 32. :warning:

multple ALB, worldwide, different distribution right. serve correct content per region.

- a. cloudfront with WAF
- b. ALB with WAF
- c. Route53 with geolocation policy
- d. Route53 with geoproximity routing policy

c/c
- WAF 모르겠음. World ...?
- route53이나 cloudfront에서 하는게 맞아보임
- route53 option도 잘 모르겠음. c가 맞아보임
개념;
- WAF: Web Application Firewall
  - 웹 공격으로부터 application을 보호하는 방화벽
- route53 policy; 레코드를 생성할 떄 선택. route53이 쿼리에 응답하는 방식을 결정
  - simple routing; 도메인에 대해 특정 기능을 수행하는 하나의 리소스만 있는 경우
  - failover routing: active-passive 장애 조치 구성
    - 리소스가 정상일 경우 해당 리소스로 라우팅
    - 비정상일 경우 다른 리소스로 라우팅
  - geolocation routing
    - 사용자 위치 (DNS 쿼리가 발생한 위치의 IP) 기반으로 트래픽 제공하려는 리소스 선택 가능
  - geoproximity routing
    - 리소스 위치 기반으로 트래픽 라우팅.
    - traffic flow를 사용해야됨
  - latency-based routing; 여러 리전에 리소스가 있고 최상의 지연 시간을 제공하는 리전으로 라우팅
  - multivalue answer routing; 무작위로 선택된 최대 8개의 정상 레코드로 응답
  - weighted routing; 사용자가 정한 비율에 따라 여러 소스로 라우팅

### 33. :x:

aws account, secure root access

- a. strong password
- b. multi-factor auth for root
- c. access key to s3
- d. add to group admin permission
- c.required permission with inline policy

b,e/a,b
- 애매한 문제.. account -> root 권한을 막는 문제인가? 문제가 잘 이해 안됨
- b는 맞는듯
- c는 아닌거같음. 
- d, e도 아닌거같은데.. 더 권한 줄게 있나.. e?
- 너무 당연해보여서 a 안했는데 함정문제였음


### 34. :warning:

log data to S3, don't know frequency, low cost

- a. S3 Glacier
- b. S3 Intelligent-Tiering
- c. S3 Standard-Infrequent Access (S3 Standard-IA)
- d. S3 One Zone-Infrequent Access (S3 One Zone-IA)

b/b
- 개념을 잘 모르겠음. c는 아닌듯
- 얼마나 쓸지 모른다 -> 사용량에 따라서 티어 매기는 b?

개념; 
- S3 Intelligent-Tiering: 데이터 엑세스 패턴이 변경될 때 스토리지 비용을 자동으로 최적화
  - frequent access / infrequent access 간에 티어 변경
- S3 Glacier: 데이터 아카이빙 및 백업을 위한 스토리지
- S3 One Zone-Infrequent Access: 엑세시 빈도가 낮은 데이터 + 다중 가용 영역 데이터 복원 모델 X
  - 낮은 복원 기능으로 비용 절감
  - 이것도 답이 될 수 있지만 얼마나 쓸지 모르므로 나중에 변경하는게 좋음


### 35. :warning:

EC2, Auto scaling, ALB. Cloudfront, WAF. malicious IP need to blocked

- a. network ACL in cloudfront
- b. configure WAF
- c. ACL in EC2 -> x
- d. SG in Ec2 -> x

b/b
- a 아니면 b
- WAF가 언제 작동하는지 잘 모르겠음. b가 더 적절해보임
개념;
- NACL is within the VPC; with WAF, block IP before it reach VPC
- cloudfront: 적합하지 않음

### 36. :white_check_mark:

2-step order process. 1: synchronous + little latency, 2: long, separate component. exactly once.

- a. SQS FIFO -> o
- b. Lambda + SQS standard
- c. SNS + SQS Fifo -> o
- d. SNS + SQS standard

c/c
- fifo 필요. sns는 필요할까? 두개 이어주려면 필요해보임


### 37. :white_check_mark:

2-tier architecture: web + database, web is vulnerable to XSS

- a. CLB. WAF
- b. NLB, WAF -> x
- c. ALB, WAF
- d. ALB, sheld

c/c
- WAF 쓰는건 맞음. ALB vs CLB?
- clb 요즘도 쓰나? ALB로 할듯
개념;
- WAF는 CLB에는 붙일 수 없음

### 38. :white_check_mark:

RDS mysql multi-az, transaction + batch, batch -> slow down.

d/d
- read replica가 답일듯

### 39. :x:

EC2 multiple zone, Auto Scaling group (ALB), CPU 40%일때 best

- a. simple scaling policy
- b. target tracking policy
- c. lambda to update? -> x
- d. scheduled scaling action -> x

a/b
- a,b중 하나. 그냥 cpu로 scaling 되는데 a 아닐까?
- target tracking policdy가 뭔지 모르겠음
개념;
- scaling policy types (dynamic scaling)
  - target tracking scaling: 특정 지표의 목표 값을 기준으로. 온도조절기
  - step scaling: 일련의 조정 조절에 따라 늘리거나 줄임
  - simple scaling: 단일 조정 조절에 따라 늘리거나 줄임(?)
- target tracking policy: 조정 지표를 선택하고 대상 값 설정

### 40. :warning:

EC2 (ALB), Auto scaling multi-az. work hours-20, overnight-2, mid-morning에 스케쥴드 되어도 매우 느림.
keep cost to minimum.

- a. scheduled action
- b. step scaling. deecrese cooldown period
- c. target tracking. decrease cooldown period -> x
- d. scheduled action. minimum 20 before open

a/a
- 다 맞아보임. c는 안됨
- b는 step이라서 느릴거라 의미 없을듯
- a, d중에 더 효율적인건? desired capacity vs minimum capacity 차이를 모르겠음. a가 맞을듯?
- 정답이 갈리는거같음
