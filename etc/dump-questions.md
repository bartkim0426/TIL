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
  - S3 data는 versioning이 되어있으면 recover 가능
    - MFA delete(CLI나 API로 제거), file delete에서 보호 가능
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
  - RDS read replica가 multi-az deployment 제공한다고 함
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
- AWS Storage Gateway: 클라우드 스토리지에 대한 온프레미스 권한을 제공하는 하이브리드 클라우드 스토리지
  - File Gateway: file interface to S3, accessible via NFS or SMB
  - Volume Gateway: 온프레미스 서버에서 iSCSI 장치로 탑재할 수 있도록 스토리지 볼륨 제공(?)
- AWS Orgranizations
  - Service Control Policy (SCP, 서비스 제어 정책)
    - 조직의 권한을 관리하는데 사용할수 있는 조직 정책 유형

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


### 41. :x:

US, Europe. db/web, Mysql in us-east-1, route53 geoproximity routing, EU user performance low

- a. RDS Multi-AZ
- b. DynamoDB -> x
- c. MySQL region, ALB
- d. 3. :white_check_mark:

HPC

b/b


### 44. :x:

EC2, Microsoft Active Directory domain control(?), imporve security & minimize adminitrative demand

- a. AWS Direct Service to managed Active Directory
- b. 
- c. 
- d. 

c/a
- Active Directory 처음 들어봄.
- d는 아님 (public)
개념;
- AWS Managed Microsoft Active Directory:
  - Microsoft AD를 managed로 관리
  - domain controller를 VPC에 연결 가능
- Active Directory? window 기반의 compute 인증과 데이터베이스를 사용하여 다양한 네트워크 서비스를 제공
  - windows 기반의 중앙집중관리 서비스
  - user, policy, connection 등 service 제공

### 45. :x:

static web, S3, data recover for accident

- a. S3 versioning
- b. S3 intelligent tiering
- c. S3 polisy
- d.S3 repilcation

d/a

개념;
- S3 data는 versioning이 되어있으면 recover 가능
- MFA delete(CLI나 API로 제거), file delete에서 보호 가능

### 46. :white_check_mark:

OLTP (online transaction processing) on RDS Mysql. new tool access data -> to not impact production

- a. snapshots -> x
- b. multi-az rds read replica
- c. multiple rds read replica + Auto Scaling group
- d. Single-AZ rds replica -> replica

b/b
- 그냥 b일거같은데.. read reploica를 여러개 만들 이유가 있나?
개념;
- RDS read replica가 multi-az deployment 제공한다고 함

### 47. :x:

app data in NFS volume, daily offsite backup needed

- a. storage gateway file gateay
- b. 
- c. 
- d. 

c/b
- Storage Gateway file gateway vs volume gateway: 잘 모름
- 그냥 c같음. NFS도 뭔지 헷갈린다
-> 잘 모르겠음
개념;
- AWS Storage Gateway: 클라우드 스토리지에 대한 온프레미스 권한을 제공하는 하이브리드 클라우드 스토리지
  - File Gateway: file interface to S3, accessible via NFS or SMB
  - Volume Gateway: 온프레미스 서버에서 iSCSI 장치로 탑재할 수 있도록 스토리지 볼륨 제공(?)
- NFS: network file system

### 48.:warning:

multiple EC2, data in EBS volumes, increase resiliency + storage (atomicity, consistency, isolation and durability - ACID)

- a. EC2, EBS (x)
- b. ALB + instance store
- c. ALB + EFS
- d. ALB + S3 (One zone-ia)

c/c
- resiliency는 ALB
- ACID는?
  - instance store는 뭔지 모르겠음
  - S3는 아닐거같음
  - EFS? instance store?
개념;
- EFS with EC2
  - VPC 안에 EC2에 file system mount
  - 각 availability zone에 mount target 생성 (subnet이 여러개여도 하나에만 생성)

### 49. :x:

security team, maintain permission in single point.

- a. ACL
- b. security group
- c. cross-account role
- d. service control policy

c/d
- 전혀 모르겠음
- sg, ACL은 이 개념이 아닌듯?
- cross-account role vs service control policy
- 원래 root는 다 갖고있으니깐 c?
개념;
- Service Control Policy (SCP, 서비스 제어 정책)
  - 조직의 권한을 관리하는데 사용할수 있는 조직 정책 유형

### 50. :x:

nightly log processing. size & number unknow, will persist 24 hours only. cost-effecitve?

- a. S3 Glacier - x
- b. S3 standard - x
- c. S3 intelligent-tiering
- d. S3 one zone-infrequent access

d/b
- c아니면 d
- 모든 파일에 접속하는지 여부에 따라 c/d
- log processing: d
개념;
- 왜 b인지 잘 모르겠음

### 51. :white_check_mark:

EC2, EBS -> another availability zone + ALB. 서로 EBS에서 보이지 않는 문제.

- a. copy data -> x
- b. ALB to server documents
- c. copy EBS to EFS, serve from EFS
- d. ALB both server -> x

c/c
- c 말고 다른애들은 문제해결 못해줌

### 52. :white_check_mark:

S3, encrypted, don't want manage key

d/d
- 관리 안하려면 kms managed key 쓰면 됨
개념;
- SSE-S3: S3-Managed key
  - S3가 데이터와 master encryption key 관리.
- SSE-C: Customer-Provied Keys
  - customer가 직접 encryption key 관리
- SSE-KMS: KMS-Managed Keys
  - AWS KMS에서 CMK 관리

### 53. :x:

ecommerce EC2, stateless web, min 10 instance, peak 250 instance. 50 instance for 80% of time. minimize cost.

- a. reserved 250
- b. reserved 80, spot to remain
- c. on-demaind 40, spot to remain -> x
- d. reserved 50, on-demand and spot to remain

a/d
- spot -> 중단될수 있어서 안쓰는게 나아보이는데..
- reserved; peak time 예측할수 있으면 그냥 a로 해도 될듯?
- 대부분의 시간: reserved, 나머지: ondemand

- 답은 d. spot fleet (ondemand + spot)라는게 있다고 함
- 가격 저렴 -> 무조건 reserved or spot 생각하면 될듯

### 54. :warning:

API in VPC, ALB. client app in private subnet behind NAT. NAT cost higher. ALB to be internal, reduce cost.

- a. VPC peering. API using private address
- b. AWS Direct Connect. private address
- c. ClassicLink
- d. PrivateLink
- e. Resource Access Manager. private address

d,e/d,e
- 문제: internet connection이라 비싼듯?
- ClassicLink, PrivateLink 모르겠음
- e는 필요해보임 (account가 다르니깐)
- b, c, d중에 하나 하면 될듯 -> direct connect는 다른 계정 안될거같은데
- 정답 분분해보임. 어려움
  - q) vpc peering -> 다른 계정간에 가능?
  - 
개념;
- Resource Access Manager: 다른 계정이나 orgranizations간에 자원 공유하게 해주는 기능
- ClassicLink: 같은 리전 내의 VPC에 인스턴스 연결
- PrivateLink: 퍼블릭 인터넷에 트래픽을 노출하지 않고 VPC, 온프레미스 네트워크 간에 비공개 연결 제공
  - Network Load Balancer needed
- 어려움..

### 55.:warning:

transferrign 750 TB, S3 glacier. 

- a. 
- b. 
- c. 
- d. 

d
- 잘 모르겠음. snowball 사용이 맞아보임.
개념;
- snowball

### 56. :x:

public/private subnet. EC2 -public, database - private.

b,e

### 57. :white_check_mark:

s3, 문서 검토 application. 삭제 방지, versioning.
b,d/b,d

### 58. :white_check_mark:

c/c


### 59. :x:

2 layer appilication. public subnet EC2, database private subnet (EC2, SQL server)

b,e/ac

### 60.  :x:

개발자가 다른 보안 정책 우회

d

### 61. :x:

ABL, auto scaling. 

d/b

### 62. :white_check_mark:

d

### 63. :x:

d/c

### 64. :white_check_mark:

ALB, EC2.DNS, SSL, HTTPS, worldwide

a/a

### 65.:warning:

process: parallel while ...

c/c

### 66. :x:

S3 csv, EC2에서 권한

b/c

### 67. :white_check_mark:

a/a

### 68. :x:

c/b

### 69. :x:

SQS -> RDS

c/d

### 70. :x:

a/c

### 71.

a

### 72. :x:

3 layers: EC2 / frontend EC2 / MySQL

a/d

### 73. :white_check_mark:

솔루션 아키텍트는 시장이 닫혀있는 동안 금융 시장의 성과를 분석하는 시스템을 설계하고 있습니다. 시스템은 매일 밤 4 시간 동안 일련의 컴퓨팅 집약적 인 작업을 실행합니다. 컴퓨팅 작업을 완료하는 데 걸리는 시간은 일정하게 유지 될 것으로 예상되며 일단 시작되면 작업을 중단 할 수 없습니다. 완료되면 시스템은 최소 1 년 동안 실행될 것으로 예상됩니다.
시스템 비용을 줄이기 위해 어떤 유형의 Amazon EC2 인스턴스를 사용해야합니까?

- A. 스팟 인스턴스
- B. 온 디맨드 인스턴스
- C. 표준 예약 인스턴스
- D. 예약 된 예약 인스턴스

> 매일 밤 4시간, 최소 1년

d/d

### 74. :x:

한 회사가 사용자 데이터를 캡처하고 향후 분석을 위해 저장하는 음식 주문 애플리케이션을 구축했습니다. 애플리케이션의 정적 프런트 엔드는 Amazon
EC2 인스턴스 에 배포됩니다 . 프런트 엔드 애플리케이션은 별도의 EC2 인스턴스에서 실행되는 백엔드 애플리케이션에 요청을 보냅니다. 그런 다음 백엔드 애플리케이션은 Amazon RDS에 데이터를 저장합니다.
솔루션 아키텍트는 아키텍처를 분리하고 확장 가능하게 만들기 위해 무엇을해야합니까?

- A. Amazon S3를 사용하여 백엔드 애플리케이션을 실행하기 위해 Amazon EC2에 요청을 보내는 프런트 엔드 애플리케이션을 제공합니다. 백엔드 애플리케이션은 Amazon RDS에서 데이터를 처리하고 저장합니다.
- B. Amazon S3를 사용하여 프런트 엔드 애플리케이션을 제공하고 Amazon Simple Notification Service (Amazon SNS) 주제에 요청을 작성합니다. 주제의 HTTP / HTTPS 엔드 포인트에 Amazon EC2 인스턴스를 구독하고 Amazon RDS에서 데이터를 처리 및 저장합니다.
- C. EC2 인스턴스를 사용하여 프런트 엔드를 제공하고 Amazon SQS 대기열에 요청을 작성합니다. 백엔드 인스턴스를 Auto Scaling 그룹에 배치하고 대기열 깊이에 따라 조정하여 Amazon RDS에서 데이터를 처리하고 저장합니다.
- D. Amazon S3를 사용하여 정적 프런트 엔드 애플리케이션을 제공하고 Amazon API Gateway에 요청을 보내면 요청이 Amazon SQS 대기열에 기록됩니다. 백엔드 인스턴스를 Auto Scaling 그룹에 배치하고 대기열 깊이에 따라 조정하여 Amazon RDS에서 데이터를 처리하고 저장합니다.

> static frontend, ec2, rds, scalable

a/d
- scalable 하게 만드려면 d가 맞음

### 75. :warning:

솔루션 아키텍트는 고성능 기계 학습을 포함하는 회사의 애플리케이션을위한 관리 형 스토리지 솔루션을 설계해야합니다. 이 애플리케이션은 AWS Fargate에서 실행되며 연결된 스토리지는 파일에 동시에 액세스하고 고성능을 제공해야합니다.
솔루션 설계자가 권장해야하는 스토리지 옵션은 무엇입니까?

- A. 애플리케이션에 대한 Amazon S3 버킷을 생성하고 Fargate가 Amazon S3와 통신 할 수 있도록 IAM 역할을 설정합니다.
- B. Lustre 용 Amazon FSx 파일 공유를 생성하고 Fargate가 Lustre 용 FSx와 통신 할 수 있도록하는 IAM 역할을 설정합니다.
- C. Amazon Elastic File System (Amazon EFS) 파일 공유를 생성하고 Fargate가 Amazon EFS와 통신 할 수 있도록하는 IAM 역할을 설정합니다.
- D. 애플리케이션에 대한 Amazon Elastic Block Store (Amazon EBS) 볼륨을 생성하고 Fargate가 Amazon EBS와 통신 할 수 있도록하는 IAM 역할을 설정합니다.

> 고성능 기계학습, Fargate, storage - concurrent access, high performance

b/b
- b,c 논란이 있음
- c: Fargate는 EFS와만 동작한다는 말이 있음


###  76. :white_check_mark:

자전거 공유 회사는 피크 운영 시간에 자전거의 위치를 추적하기 위해 다 계층 아키텍처를 개발하고 있습니다. 이 회사는 기존 분석 플랫폼에서 이러한 데이터 포인트를 사용하려고합니다. 솔루션 아키텍트는이 아키텍처를 지원하기 위해 가장 실행 가능한 다중 계층 옵션을 결정해야합니다. REST API에서 데이터 포인트에 액세스 할 수 있어야합니다.
위치 데이터 저장 및 검색에 대한 이러한 요구 사항을 충족하는 작업은 무엇입니까?

- A. Amazon S3와 함께 Amazon Athena를 사용합니다.
- B. AWS Lambda와 함께 Amazon API Gateway를 사용합니다.
- C. Amazon Redshift와 함께 Amazon QuickSight를 사용합니다.
- D. Amazon Kinesis Data Analytics와 함께 Amazon API Gateway를 사용합니다.

> 분석 플랫폼, rest api, 저장 및 검색

d/d
- a라는 의견도 많음: s3 -> athena

### 77. :warning:

솔루션 아키텍트는 ALB (Application Load Balancer) 뒤의 Amazon EC2 인스턴스에서 실행될 웹 애플리케이션을 설계하고 있습니다. 회사는 애플리케이션이 악의적 인 인터넷 활동 및 공격에 대해 복원력이 있고 새로운 일반적인 취약성과 노출로부터 보호 할 것을 엄격히 요구합니다.
솔루션 아키텍트는 무엇을 권장해야합니까?

- A. ALB 엔드 포인트를 오리진으로 사용하여 Amazon CloudFront를 활용합니다.
- B. AWS WAF에 대한 적절한 관리 형 규칙을 배포하고 ALB와 연결합니다.
- C. AWS Shield Advanced를 구독하고 일반적인 취약성과 노출이 차단되었는지 확인합니다.
- D. 포트 80 및 443 만 EC2 인스턴스에 액세스 할 수 있도록 네트워크 ACL 및 보안 그룹을 구성합니다.

> ALB, vulnerability

b/b
- 방화벽 설정해주는 b가 가장 적절 (Web Application Firewall)
- c라는 의견도 있음: AWS Shield Advanced 에서는 WAF가 포함된다고 함
  - Shield + WAF


### 78.  :white_check_mark:

회사에 AWS Lambda 함수를 호출하는 애플리케이션이 있습니다. 최근 코드 검토에서 소스 코드에 저장된 데이터베이스 자격 증명을 찾았습니다. Lambda 소스 코드에서 데이터베이스 자격 증명을 제거해야합니다. 그런 다음 보안 정책 요구 사항을 충족하기 위해 자격 증명을 안전하게 저장하고 지속적으로 교체해야합니다.
솔루션 설계자는 이러한 요구 사항을 충족하기 위해 무엇을 권장해야합니까?

- A. AWS CloudHSM에 암호를 저장합니다. Lambda 함수를 해당 키 ID가 지정된 CloudHSM에서 암호를 검색 할 수있는 역할과 연결합니다.
- B. AWS Secrets Manager에 암호를 저장합니다. Lambda 함수를 암호 ID가 지정된 Secrets Manager에서 암호를 검색 할 수있는 역할과 연결합니다.
- C. 데이터베이스 암호를 Lambda 함수와 연결된 환경 변수로 이동합니다. 실행시 환경 변수에서 암호를 검색합니다.
- D. AWS Key Management Service (AWS KMS)에 암호를 저장합니다. Lambda 함수를 키 ID가 지정된 AWS KMS에서 암호를 검색 할 수있는 역할과 연결합니다.

> lambda, db 자격증명 제거 -> 저장 및 교체

b/b

### 79. :white_check_mark:

한 회사가 온 프레미스에서 건강 기록을 관리하고 있습니다. 회사는 이러한 기록을 무기한 보관하고, 저장된 기록에 대한 수정을 비활성화하고, 모든 수준에서 액세스를 세부적으로 감사해야합니다. CTO (최고 기술 책임자)는 이미 애플리케이션에서 사용하지 않는 수백만 개의 레코드가 있고 현재 인프라의 공간이 부족하기 때문에 우려합니다. CTO는 솔루션 설계자에게 기존 데이터를 이동하고 향후 기록을 지원하는 솔루션을 설계하도록 요청했습니다.
솔루션 아키텍트는 이러한 요구 사항을 충족하기 위해 어떤 서비스를 권장 할 수 있습니까?

- A. AWS DataSync를 사용하여 기존 데이터를 AWS로 이동합니다. Amazon S3를 사용하여 기존 데이터와 새 데이터를 저장합니다. Amazon S3 객체 잠금을 활성화하고 데이터 이벤트로 AWS CloudTrail을 활성화합니다.
- B. AWS Storage Gateway를 사용하여 기존 데이터를 AWS로 이동합니다. Amazon S3를 사용하여 기존 데이터와 새 데이터를 저장합니다. Amazon S3 객체 잠금을 활성화하고 관리 이벤트로 AWS CloudTrail을 활성화합니다.
- C. AWS DataSync를 사용하여 기존 데이터를 AWS로 이동합니다. Amazon S3를 사용하여 기존 데이터와 새 데이터를 저장합니다. Amazon S3 객체 잠금을 활성화하고 관리 이벤트로 AWS CloudTrail을 활성화합니다.
- D. AWS Storage Gateway를 사용하여 기존 데이터를 AWS로 이동합니다. Amazon Elastic Block Store (Amazon EBS)를 사용하여 기존 데이터와 새 데이터를 저장합니다. Amazon S3 객체 잠금을 활성화하고 Amazon S3 서버 액세스 로깅을 활성화합니다.

> onpremise, 이동 / 보관 / 수정 비활성화 / 액세스 감사


a/a
- storage gateway vs datasync -> datasync
- s3 vs ebs -> s3
- cloudtrail: management event vs data event -> data event

### 80. :white_check_mark:

한 회사에서 온 프레미스 데이터 세트의 보조 복사본에 Amazon S3를 사용하려고합니다. 회사는이 사본에 거의 액세스 할 필요가 없습니다. 스토리지 솔루션의 비용은 최소화되어야합니다.
이러한 요구 사항을 충족하는 스토리지 솔루션은 무엇입니까?

- A. S3 표준
- B. S3 지능형 계층화
- C. S3 Standard-Infrequent Access (S3 Standard-IA)
- D. S3 One Zone-Infrequent Access (S3 One Zone-IA)

> replica, S3, access 안함, 비용 최소화

d/d
- 가장 저렴한 d


### 81. :x:


회사의 운영 팀에는 버킷 내에 새 객체가 생성 될 때 Amazon SQS 대기열에 알리도록 구성된 기존 Amazon S3 버킷이 있습니다. 개발 팀은 또한 새 개체가 생성 될 때 이벤트를 받기를 원합니다. 기존 운영 팀 워크 플로는 그대로 유지되어야합니다.
이러한 요구 사항을 충족하는 솔루션은 무엇입니까?

- A. 다른 SQS 대기열을 만듭니다. 새 객체가 생성 될 때 새 대기열도 업데이트하도록 버킷의 S3 이벤트를 업데이트합니다.
- B. Amazon S3 만 대기열에 액세스 할 수 있도록 허용하는 새 SQS 대기열을 생성합니다. 새 객체가 생성 될 때이 대기열을 업데이트하도록 Amazon S3를 업데이트합니다.
- C. 버킷 업데이트를위한 Amazon SNS 주제 및 SQS 대기열을 생성합니다. 새 주제에 이벤트를 보내도록 버킷을 업데이트하십시오. Amazon SNS를 폴링하도록 두 대기열을 모두 업데이트합니다.
- D. 버킷 업데이트를위한 Amazon SNS 주제 및 SQS 대기열을 생성합니다. 새 주제에 이벤트를 보내도록 버킷을 업데이트하십시오. 주제의 두 큐에 대한 구독을 추가하십시오.

> 객체 생성시 S3 -> SQS. 이벤트를 받기 원함

c/d
- c도 가능하겠지만 기존 운영팀 워크플로우가 변경됨

### 82. :x:
애플리케이션은 프라이빗 서브넷의 Amazon EC2 인스턴스에서 실행됩니다. 애플리케이션은 Amazon DynamoDB 테이블에 액세스해야합니다. 트래픽이 AWS 네트워크를 벗어나지 않도록 보장하면서 테이블에 액세스하는 가장 안전한 방법은 무엇입니까?
- A. DynamoDB 용 VPC 엔드 포인트를 사용합니다.
- B. 퍼블릭 서브넷에서 NAT 게이트웨이를 사용합니다.
- C. 프라이빗 서브넷에서 NAT 인스턴스를 사용합니다.
- D. VPC에 연결된 인터넷 게이트웨이를 사용합니다.

 > private subnet, dynamoDB, aws network를 벗어나지 않도록 보장

c/a
- NAT는 퍼블릭 서브넷에 있어야됨
- 인터넷이 없어야되려면 VPC endpoint

### 83. :white_check_mark:
한 회사는 사용자가 방문한 장소를 체크인하고 장소의 순위를 매기고 경험에 대한 리뷰를 추가 할 수있는 애플리케이션을 구축했습니다. 이 응용 프로그램은 매달 사용자 수가 급격히 증가하면서 성공적입니다.
CTO는 단일 Amazon
RDS for MySQL 인스턴스가 읽기 요청으로 인해 리소스 고갈과 관련된 경보를 트리거 했기 때문에 현재 인프라를 지원하는 데이터베이스가 다음 달에 새로운로드를 처리하지 못할 수 있다고 우려 합니다.
솔루션 아키텍트는 최소한의 코드 변경으로 데이터베이스 계층에서 서비스 중단을 방지하기 위해 무엇을 권장 할 수 있습니까?
- A. RDS 읽기 전용 복제본을 생성하고 읽기 전용 트래픽을 읽기 전용 복제본 엔드 포인트로 리디렉션합니다. 다중 AZ 배포를 활성화합니다.
- B. Amazon EMR 클러스터를 생성하고 복제 요소가 3 인 HDFS (Hadoop Distributed File System)로 데이터를 마이그레이션합니다.
- C. Amazon ElastiCache 클러스터를 생성하고 모든 읽기 전용 트래픽을 클러스터로 리디렉션합니다. 3 개의 가용 영역에 배포 할 클러스터를 설정합니다.
- D. Amazon DynamoDB 테이블을 생성하여 RDS 인스턴스를 교체하고 모든 읽기 전용 트래픽을 DynamoDB 테이블로 리디렉션합니다. DynamoDB Accelerator를 활성화하여 기본 테이블에서 트래픽을 오프로드합니다.

> 사용자 증가, rds 단일 인스턴스, 읽기 요청으로 경보, 서비스 중단 방지

a/a

### 84. :white_check_mark:

한 회사가 오래된 뉴스 영상에서 AWS에 비디오 아카이브를 저장할 수있는 솔루션을 찾고 있습니다. 회사는 비용을 최소화해야하며 이러한 파일을 거의 복원 할 필요가 없습니다. 파일이 필요한 경우 최대 5 분 내에 사용할 수 있어야합니다.
가장 비용 효율적인 솔루션은 무엇입니까?
- A. Amazon S3 Glacier에 비디오 아카이브를 저장하고 신속 검색을 사용합니다.
- B. Amazon S3 Glacier에 비디오 아카이브를 저장하고 표준 검색을 사용합니다.
- C. Amazon S3 Standard-Infrequent Access (S3 Standard-IA)에 비디오 아카이브를 저장합니다.
- D. Amazon S3 One Zone-Infrequent Access (S3 One Zone-IA)에 비디오 아카이브를 저장합니다.

> 비디오 아카이브 저장, 복원 필요 없음, 비용 최소화, 최대 5분 이내 사용

a/a

### 85.
한 회사에서 여러 가용 영역 (AZ)에 여러 프라이빗 서브넷이 있고 AZ 중 하나에 하나의 퍼블릭 서브넷이있는 VPC를 생성했습니다. 퍼블릭 서브넷은 NAT 게이트웨이를 시작하는 데 사용됩니다. NAT 게이트웨이를 사용하여 인터넷에 연결하는 프라이빗 서브넷에는 인스턴스가 있습니다. AZ 오류가 발생한 경우 회사는 인스턴스에 모두 인터넷 연결 문제가 발생하지 않고 백업 계획이 준비되어 있는지 확인하려고합니다.
솔루션 설계자가 가장 가용성이 높은 솔루션을 권장해야합니까?
- A. 동일한 AZ에 NAT 게이트웨이가있는 새 퍼블릭 서브넷을 생성합니다. 두 NAT 게이트웨이간에 트래픽을 분산합니다.
- B. 새 퍼블릭 서브넷에 Amazon EC2 NAT 인스턴스를 생성합니다. NAT 게이트웨이와 NAT 인스턴스간에 트래픽을 분산합니다.
- C. 각 AZ에서 퍼블릭 서브넷을 생성하고 각 서브넷에서 NAT 게이트웨이를 시작합니다. 각 AZ의 프라이빗 서브넷에서 각 NAT 게이트웨이로의 트래픽을 구성합니다.
- D. 동일한 퍼블릭 서브넷에 Amazon EC2 NAT 인스턴스를 생성합니다. NAT 게이트웨이를 NAT 인스턴스로 교체하고 적절한 조정 정책을 사용하여 인스턴스를 Auto Scaling 그룹에 연결합니다.

> multi-AZ private subnet, public subnet in one az -> NAT, 
