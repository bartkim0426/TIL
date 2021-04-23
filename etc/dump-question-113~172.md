# dump question 113~172

### 113.:x:
VPC-A의 Amazon EC2 인스턴스에서 실행되는 애플리케이션은 VPC-B의 다른 EC2 인스턴스에있는 파일에 액세스해야합니다. 둘 다 분리되어 있습니다. AWS 계정. 네트워크 관리자는 VPC-A에서 VPC-B의 EC2 인스턴스에 안전하게 액세스 할 수있는 솔루션을 설계해야합니다. 연결에는 단일 실패 지점이나 대역폭 문제가 없어야합니다.
이러한 요구 사항을 충족하는 솔루션은 무엇입니까?
- A. VPC-A와 VPC-B간에 VPC 피어링 연결을 설정합니다.
- B. VPC-B에서 실행되는 EC2 인스턴스에 대한 VPC 게이트웨이 엔드 포인트를 설정합니다.
- C. 가상 프라이빗 게이트웨이를 VPC-B에 연결하고 VPC-A에서 라우팅을 활성화합니다.
- D. VPC-B에서 실행되는 EC2 인스턴스에 대한 프라이빗 가상 인터페이스 (VIF)를 생성하고 VPC-B에서 적절한 경로를 추가합니다.

a/d
> 분리된 vpc, 연결 필요. 대역폭 문제, 실패 지점 없어야됨
- a or b?
- 그냥 peering하면 될거같은데 왜 안되는지 잘 모르겠음

### 114.:white_check_mark:
한 회사는 현재 하드웨어 보안 모듈 (HSM)에 대칭 암호화 키를 저장합니다. 솔루션 아키텍트는 키 관리를 AWS로 마이그레이션하는 솔루션을 설계해야합니다. 이 솔루션은 키 순환을 허용하고 고객 제공 키 사용을 지원해야합니다.
이러한 요구 사항을 충족하려면 키 자료를 어디에 저장해야합니까?
- A. Amazon S3
- B. AWS Secrets Manager
- C. AWS Systems Manager 파라미터 스토어
- D. AWS Key Management Service (AWS KMS)

d/d 

### 115. :x:

a/d

테이프가 뭔지 잘 모르겠음..


### 116.:x:

ssd중 하나

c/b

- EBS 범용 / IOPS 차이
- 범용은 최대 16000IOPS 지원하며 IO보다 저렴하다고함

### 117.:white_check_mark:

b/b

### 118.:x:

b/d
- lambda vs ec2 -> lambda같은데..
- 파일 동시 접근 -> EFS

### 119.:white_check_mark:
c/c

### 120.:white_check_mark:
d/d

### 121.:x:
b/d
- b가 안되는 이유를 잘 모르겠음

### 122.:warning:
c/c

### 123.:x:
b, e / c,e
- FSx 파일 스토리지: aws 및 온프레미스 등 다양한 환경에서 액세스 가능

### 124.:x:
c/a
- Spot fleet instance: 장점은 비용, 단점은 불안성
- 답변이 갈리는듯

### 125.:white_check_mark:
c/c
- 공유파일시스템 + window 기반 = window FSx

### 126.:x:
d/c
- b/c가 답이 갈리는듯
- active-passive 장애 조치 레코드가 있는 route-53 상태 확인 (https://docs.aws.amazon.com/ko_kr/Route53/latest/DeveloperGuide/dns-failover.html)

### 127.:x:
b/a
- DAX: DynamoDB Accelerator: 가용성이 뛰어난 완전관리형 메모리 캐시. 최대 10배의 성능을 제공

### 128.:white_check_mark:
c/c

### 129.:white_check_mark:
a/a

### 130.:white_check_mark:
a/a

### 131.:white_check_mark:
a/a
### 132.:white_check_mark:
a/a
### 133.:white_check_mark:
c/c
### 134.:white_check_mark:
a,c/a,c
- d는 안되나?
### 135.:white_check_mark:
a/a
### 136.:white_check_mark:
d/d
### 137.:white_check_mark:
d/d
### 138. :x:
a/c
- 8개씩이나 써야하는 이유가 뭔지 잘 모르겠음

### 139.:warning:
b/b

### 140.:x:
d/b
- 인스턴스간 짧은 지연 시간 -> 클러스터 배치

### 141.:x:
c/a
- 인터넷 대기시간 감소 + 공용 고정 IP 주소 노출 -> Global Accelerator
  - 고정 ip 주소의 경우에만 확실히 GA라고 함
### 142.:x:
d/a
- CloudHSM이 뭔지?
- a, b, d 논란
  - d가 안되는 이유를 잘 모르겠음

### 143.:x:
b/a
- NAT가 프라이빗 서브넷에 있으면 쓸수 없다고함
- NAT Gateway: public subnet

### 144.:warning:
a,a
- a,c,d 헷갈림
- 거의 실시간이라 a인듯?
- d도 될거같은데.. 

### 145.:white_check_mark:
a,d

### 146.:white_check_mark:
c

### 147.:x:
a,c/a,b
- a: 맞음
- b. x
- c. database가 아니라 datalake: x
- d. 확장되지 않음
- e
- ab, ad, ae중 갈리는듯

### 148.:warning:
b/b
- a, b, c 갈리고있음

### 149.:white_check_mark:
a/a

### 150.:warning:
a/a

### 151.:x:

DX: Direct connect

### 152. :white_check_mark:
b/b

### 153.:white_check_mark:
d
### 154.:white_check_mark:
b
### 155.:white_check_mark:
a
### 156.:white_check_mark:
a
### 157.:x:
b/c
### 158.:white_check_mark:
b
### 159.:white_check_mark:
a,c
### 160.:white_check_mark:
b

### 161. :x:


한 회사에서 테이프 백업 솔루션을 사용하여 주요 애플리케이션 데이터를 오프 사이트에 저장하고 있습니다. 일일 데이터 볼륨은 약 50TB입니다. 회사는 규제 목적으로 7 년 동안 백업을 보관해야합니다. 백업에는 거의 액세스하지 않으며 백업을 복원해야하는 경우 일반적으로 일주일 전에 알림이 제공됩니다.
이 회사는 현재 테이프 관리의 스토리지 비용과 운영 부담을 줄이기 위해 클라우드 기반 옵션을 고려하고 있습니다. 이 회사는 또한 테이프 백업에서 클라우드로의 전환이 중단을 최소화하기를 원합니다.
가장 비용 효율적인 스토리지 솔루션은 무엇입니까?

- A. Amazon Storage Gateway를 사용하여 Amazon Glacier Deep Archive에 백업합니다.
- B. AWS Snowball Edge를 사용하여 백업을 Amazon S3 Glacier와 직접 통합합니다.
- C. 백업 데이터를 Amazon S3에 복사하고 수명주기 정책을 생성하여 데이터를 Amazon S3 Glacier로 이동합니다.
- D. Amazon Storage Gateway를 사용하여 Amazon S3에 백업하고 수명주기 정책을 생성하여 백업을 Amazon S3 Glacier로 이동합니다.

> daily 50TB, 7 year, 거의 액세스 안함, 복원시 일주일 걸림, 전환 중단 최소화, 비용 효율

b/a
- snowball: 한 번 옮길때 좋음
  - 지속적으로 일일 50TB정도를 옮겨야하기때문에 X
-  거의 엑세스 안함 -> glacier deep archive
- storage tape gateway
  - faq: https://aws.amazon.com/ko/storagegateway/faqs/?nc=sn&loc=6

### 162. :x:

회사는 빠른 복구를 위해 온 프레미스 애플리케이션이 이러한 백업에 대한 액세스를 유지하도록 보장하면서 온 프레미스 데이터베이스 서버를위한 내구성있는 백업 스토리지 솔루션이 필요합니다. 회사는 이러한 백업의 대상으로 AWS 스토리지 서비스를 사용합니다. 솔루션 아키텍트는 최소한의 운영 오버 헤드로 솔루션을 설계하고 있습니다.
솔루션 아키텍트는 어떤 솔루션을 구현해야합니까?

- A. 온 프레미스에 AWS Storage Gateway 파일 게이트웨이를 배포하고 Amazon S3 버킷과 연결합니다.
- B. 데이터베이스를 AWS Storage Gateway 볼륨 게이트웨이에 백업하고 Amazon S3 API를 사용하여 액세스합니다.
  - aws s3 api를 통해서는 volume data에 접근 불가능함
- C. 데이터베이스 백업 파일을 Amazon EC2 인스턴스에 연결된 Amazon Elastic Block Store (Amazon EBS) 볼륨으로 전송합니다.
- D. 데이터베이스를 AWS Snowball 디바이스에 직접 백업하고 수명주기 규칙을 사용하여 데이터를 Amazon S3 Glacier Deep Archive로 이동합니다.

> 온프레미스, 백업 스토리지 솔루션, 백업에 대한 액세스 유지, 최소한의 운영 오버헤드

b/a

storage file gateway faq: https://aws.amazon.com/ko/storagegateway/faqs/?nc=sn&loc=6
- Q: 파일 게이트웨이로 어떤 작업을 수행할 수 있습니까?
- A: 파일 게이트웨이의 사용 사례로는
  - (a) 최근에 액세스한 데이터에 대해 빠른 로컬 액세스를 유지하면서 온프레미스 파일 데이터를 Amazon S3로 마이그레이션, 
  - (b) 온프레미스 파일 데이터를 Amazon S3에 객체로 백업하고(Microsoft SQL Server와 Oracle 데이터베이스 및 로고 포함) 수명 주기 관리 및 교차 리전 복제와 같은 S3 기능을 사용, 
  - (c) 기계 학습, 빅 데이터 분석 또는 서버리스 함수와 같은 AWS 서비스로 처리하기 위해 온프레미스 애플리케이션에서 생성한 데이터를 사용하는 하이브리드 클라우드 워크플로를 들 수 있습니다.

### 163.:white_check_mark:

한 회사가 3 계층 웹 애플리케이션을 온 프레미스에서 AWS 클라우드로 마이그레이션하기로 결정합니다. 새 데이터베이스는 스토리지 용량을 동적으로 확장하고 테이블 조인을 수행 할 수 있어야합니다.
이러한 요구 사항을 충족하는 AWS 서비스는 무엇입니까?
- A. 아마존 오로라
- B. SqlServer 용 Amazon RDS
  - auto scaling과 쓰면 가능은 하겠지만 그런 얘기 없으니 x
- C. Amazon DynamoDB 스트림
  - nosql이라 x
- D. Amazon DynamoDB 온 디맨드
  - nosql이라 x

a/a
> 3계층 웹, 온프레미스 -> AWS, db 스토리지 용량 동적 + 테이블 조인

Aurora storage: 클러스터 볼륨에 저장된 데이터에 따라 자동 조정
- [Aurora FAQ](https://aws.amazon.com/ko/rds/aurora/faqs/)

### 164.:white_check_mark:
회사는 Amazon S3 게이트웨이 엔드 포인트가 신뢰할 수있는 버킷에 대한 트래픽 만 허용하도록 요구합니다.
솔루션 아키텍트는이 요구 사항을 충족하기 위해 어떤 방법을 구현해야합니까?
- A. 회사의 신뢰할 수있는 VPC의 트래픽 만 허용하는 각 회사의 신뢰할 수있는 S3 버킷에 대한 버킷 정책을 생성합니다.
- B. 회사의 S3 게이트웨이 엔드 포인트 ID에서만 트래픽을 허용하는 회사의 신뢰할 수있는 S3 버킷 각각에 대한 버킷 정책을 생성합니다.
- C. 회사의 신뢰할 수있는 VPC가 아닌 VPC의 액세스를 차단하는 각 회사의 S3 게이트웨이 엔드 포인트에 대한 S3 엔드 포인트 정책을 생성합니다.
- D. 신뢰할 수있는 S3 버킷의 Amazon 리소스 이름 (ARN)에 대한 액세스를 제공하는 회사의 각 S3 게이트웨이 엔드 포인트에 대한 S3 엔드 포인트 정책을 생성합니다.

A company mandates that an Amazon S3 gateway endpoint must allow traffic to trusted buckets only.
Which method should a solutions architect implement to meet this requirement?
- A. Create a bucket policy for each of the company's trusted S3 buckets that allows traffic only from the company's trusted VPCs.
- B. Create a bucket policy for each of the company's trusted S3 buckets that allows traffic only from the company's S3 gateway endpoint IDs.
- C. Create an S3 endpoint policy for each of the company's S3 gateway endpoints that blocks access from any VPC other than the company's trusted VPCs.
- D. Create an S3 endpoint policy for each of the company's S3 gateway endpoints that provides access to the Amazon Resource Name (ARN) of the trusted S3 buckets.

> S3 gateway endpoint -> 신뢰할 수 있는 버킷에 대한 트래픽만 "허용"

d/d
- s3 gateway endpoint: endpoint policy에 arn 추가하는 방식으로 작동함
- bucket policy를 하나하나 추가해줘도 가능은 하겠지만 매우 비효율적임

일반적인 S3 흐름도

![image](https://www.lucidchart.com/publicSegments/view/f5ea9c18-af70-48ee-8657-42b960618ca9/image.jpeg)

이 경우 private subnet에 있는 ec2는 internel access가 없기 때문에 s3에 접근 안됨

VPC endpoint는 이런 문제를 해결해줌

![image](https://www.lucidchart.com/publicSegments/view/ca1be1f8-c23c-466b-a25b-4fedcc691242/image.png)

- [잘 설명된 블로그 글](https://tomgregory.com/when-to-use-an-aws-s3-vpc-endpoint/)

### 165.:warning:

회사는 VPC 피어링 전략을 사용하여 단일 리전의 VPC를 연결하여 교차 통신을 허용합니다. 최근 계정 생성 및
VPC의 증가로 인해 VPC 피어링 전략을 유지하기가 어려웠으며 회사는 수백 개의 VPC로 성장할 것으로 예상합니다. 또한 일부 VPC를 사용하여 사이트 간 VPN을 생성하라는 새로운 요청이 있습니다. 솔루션 아키텍트는 여러 계정, VPC 및 VPN에 대해 중앙에서 관리되는 네트워킹 설정을 만드는 임무를 맡았습니다.
이러한 요구 사항을 충족하는 네트워킹 솔루션은 무엇입니까?
- A. 공유 VPC 및 VPN을 구성하고 서로 공유합니다.
  - 이 방식이 안된다고 한거라 x
- B. 허브 앤 스포크 VPC를 구성하고 VPC 피어링을 통해 모든 트래픽을 라우팅합니다.
  - ??
- C. 모든 VPC와 VPN간에 AWS Direct Connect 연결을 구성합니다.
  - Direct Connect는 상관 없는 개념
- D. AWS Transit Gateway로 전송 게이트웨이를 구성하고 모든 VPC와 VPN을 연결합니다.

d/d
> VPC peering -> 수백개의 VPC로 피어링 전략 유지 어려움, 일부 VPC로 사이트간 VPN 생성. "여러 계정, VPC, VPN 중앙 관리"

- AWS Transit Gateway: 중앙 허브를 통해 VPC와 온프레미스 네트워크를 연결. 복잡한 피어링 관계를 제거하여 네트워크를 간소화
  - [AWS Transit Gateway FAQ](https://aws.amazon.com/ko/transit-gateway/faqs/)
- hub and spoke VPC?
  - 중앙의 hub를 두고 나머지는 spoke vpc로 만든 뒤 모든 통신은 HUB vpc를 경유하게 하는 구성
  - 이 방식도 논리적으로는 가능하지만 vpc peering만으로는 구현에 한계가 있어서 아닐듯

[Transit VPC를 통한 Cloud HUB 디자인하기 – AWS 서드 파티 도구 기반](https://aws.amazon.com/ko/blogs/korea/transit-vpc-cloud-hub-design-using-aws-marketplace-tools/)


### 166.:white_check_mark:

솔루션 아키텍트는 개발자가 AWS 서비스를 사용하여 새로운 전자 상거래 장바구니 애플리케이션을 설계하도록 돕고 있습니다. 개발자는 현재 데이터베이스 스키마를 잘 모르며 전자 상거래 사이트가 성장함에 따라 변경을 기대합니다. 솔루션은 복원력이 뛰어나야하며 읽기 및 쓰기 용량을 자동으로 확장 할 수 있어야합니다.
이러한 요구 사항을 충족하는 데이터베이스 솔루션은 무엇입니까?
- A. Amazon Aurora PostgreSQL
- B. 온 디맨드가 활성화 된 Amazon DynamoDB
- C. DynamoDB 스트림이 활성화 된 Amazon DynamoDB
- D. Amazon SQS 및 Amazon Aurora PostgreSQL

> db schema 잘 모름, 성장함에 따라 변경. 복원력, 읽기&쓰기 용량 자동 확장

b/b
- schmea 잘 모름 -> NoSQL
- 읽기&쓰기 자동 확장 -> dynamo db auto scaling
- [dynamoDB stream](https://docs.aws.amazon.com/ko_kr/amazondynamodb/latest/developerguide/Streams.html): 시간 순서에 따라 항목 수정 사항을 캡처하여 이 정보를 최대 24시간동안 로그에 저장

### 167.:warning:

솔루션 아키텍트는 Windows 인터넷 정보 서비스 (IIS) 웹 애플리케이션을 AWS로 마이그레이션해야합니다. 이 애플리케이션은 현재 사용자의 온-프레미스 NAS (Network-Attached Storage)에서 호스팅되는 파일 공유에 의존합니다. 설계된 솔루션은 스토리지 솔루션에 연결된 여러 가용 영역의 Amazon EC2 인스턴스로 IIS 웹 서버를 마이그레이션하고 인스턴스에 연결된 Elastic Load Balancer를 구성 할 것을 제안했습니다.
온-프레미스 파일 공유를 대체하는 것이 가장 탄력적이고 내구성이 있습니까?
- A. 파일 공유를 Amazon RDS로 마이그레이션합니다.
  - 파일공유기때문에 x
- B. 파일 공유를 AWS Storage Gateway로 마이그레이션
  - 공유를 대체해주지 못함
- C. 파일 공유를 Windows 파일 서버용 Amazon FSx로 마이그레이션합니다.
- D. 파일 공유를 Amazon Elastic File System (Amazon EFS)으로 마이그레이션

c/c
> IIS(?) -> AWS (multi-AZ EC2 + ELB), 온프레미스 NAS 파일공유 -> 뭘로 대체?

- EFS는 SMB를 지원하지 않아서 안된다고함
- IIS: Internet Information Service. MS 윈도우 시스템에서 사용하는 웹서버 프로그램

### 168.:warning:

회사는 다중 지역 재해 복구 RPO (복구 지점 목표)가 1 초이고 복구 시간
목표 (RTO)가 1 분인 관계형 데이터베이스를 구현해야합니다 .
이를 달성 할 수있는 AWS 솔루션은 무엇입니까?
- A. Amazon Aurora 글로벌 데이터베이스
- B. Amazon DynamoDB 전역 테이블
  - RDBMS x
- C. 다중 AZ가 활성화 된 MySQL 용 Amazon RDS
- D. 교차 리전 스냅 샷 사본이있는 MySQL 용 Amazon RDS

> RPO 1sec, RTO 1min, RDBMS

a/a

- RPO: Recovery Point Objectvie
- RTO: Recovery Time Objective

- [Aurora global database](https://aws.amazon.com/rds/aurora/global-database/): 전 세계적으로 분산도니 애플리케이션을 위해 설계. 단일 데이터베이스를 여러 AWS 리전으로 확장해줌.
  - 전용 인프라를 사용하여 1초 미만의 일반적인 지연 시간으로 스토리지 기반 복제를 사용
  - 교차 리전 재해 복구:  효율적인 1초의 RPO(복구 목표 지점) 및 1분 미만의 RTO(복구 목표 시간)를 제공

### 169.
회사는 Application Load Balancer 뒤의 Amazon EC2 인스턴스에서 웹 서비스를 실행합니다. 인스턴스는 두 가용 영역에 걸쳐 Amazon EC2 Auto Scaling 그룹에서 실행됩니다. 회사는 비용을 낮게 유지하면서 필요한 SLA (서비스 수준 계약)를 충족하기 위해 항상 최소 4 개의 인스턴스가 필요합니다.
가용 영역이 실패하는 경우 회사는 어떻게 SLA를 계속 준수 할 수 있습니까?
- A. 휴지 기간이 짧은 대상 추적 조정 정책을 추가합니다. (A. Add a target tracking scaling policy with a short cooldown period.)
- B. 더 큰 인스턴스 유형을 사용하도록 Auto Scaling 그룹 시작 구성을 변경합니다.
  - 4개가 아니라서 x
- C. 3 개의 가용 영역에서 6 개의 서버를 사용하도록 Auto Scaling 그룹을 변경합니다.
- D. 2 개의 가용 영역에 걸쳐 8 개의 서버를 사용하도록 Auto Scaling 그룹을 변경합니다.

> EC2 (ALB), 2 AZ, auto scaling group. 비용 낮게, 최소 4개의 인스턴스. 가용 영역 실패시 SLA 유지

a/
- SLA (service level aggrement): 고객이 공급업체에게 기대하는 서비스 수준
- 정답이 분분함
- a/c/d중에 정답 분분
- a, b: AZ fail시 4개의 instance를 보장해주지 못한다는 의견
- SLA에 명시적인 downtime이 없어서 더 논란이 되는듯함
- [추적 조정 정책 문서](https://docs.aws.amazon.com/ko_kr/autoscaling/ec2/userguide/as-scaling-target-tracking.html); 조정 정책을 트리거하는 cloudwatch 경보를 생성/관리하여 이 지표를 기준으로 조정
  - 예를 들어, 다음과 같은 경우에 대상 추적 조정을 사용할 수 있습니다.
    - 대상 추적 조정 정책을 구성하여 Auto Scaling 그룹의 평균 총 CPU 사용량을 40%로 유지하는 경우
    - 대상 추적 조정 정책을 구성하여 Application Load Balancer 대상 그룹의 대상 1개당 요청 수를 Auto Scaling 그룹에 필요한 1000개로 유지하는 경우


### 170.:white_check_mark:

한 회사가 AWS 클라우드 배포를 검토하여 적절한 권한 없이는 누구도 데이터에 액세스하지 못하도록합니다. 솔루션 아키텍트는 열려있는 모든 Amazon S3 버킷을 식별하고 S3 버킷 구성 변경 사항을 기록해야합니다.
솔루션 아키텍트는이를 위해 무엇을해야합니까?
- A. 적절한 규칙으로 AWS Config 서비스 활성화
- B. 적절한 확인을 통해 AWS Trusted Advisor를 활성화합니다.
- C. AWS SDK를 사용하여 스크립트를 작성하여 버킷 보고서 생성
- D. Amazon S3 서버 액세스 로깅을 활성화하고 Amazon CloudWatch Events를 구성합니다.

> 권한 없이는 데이터 엑세스 못함, S3 식별 + 구성 변경사항 기록

a/a

- AWS Config: aws 리소스 구성을 측정, 감사, 평가할 수 있는 서비스


### 171.:white_check_mark:

한 회사가 AWS에서 새로운 웹 애플리케이션을 구축 할 계획입니다. 회사는 대부분의 경우 예측 가능한 트래픽과 경우에 따라 매우 높은 트래픽을 예상합니다. 웹 애플리케이션은 최소한의 대기 시간으로 가용성이 높고 내결함성이 있어야합니다.
솔루션 아키텍트는 이러한 요구 사항을 충족하기 위해 무엇을 권장해야합니까?
- A. Amazon Route 53 라우팅 정책을 사용하여 각각 하나의 Amazon EC2 인스턴스가있는 두 개의 AWS 리전에 요청을 배포합니다.
- B. 여러 가용 영역에서 Application Load Balancer가있는 Auto Scaling 그룹의 Amazon EC2 인스턴스를 사용합니다.
- C. 여러 가용 영역에서 Application Load Balancer가있는 클러스터 배치 그룹에서 Amazon EC2 인스턴스를 사용합니다.
- D. 클러스터 배치 그룹에서 Amazon EC2 인스턴스를 사용하고 새 Auto Scaling 그룹에 클러스터 배치 그룹을 포함합니다.

> 경우에 따라 매우 높은 트래픽, 가용성, 내결함성

b/b
- 높은 트래픽: auto scaling
- 가용성, 내결함성: 여러 가용영역

### 172.:x:
한 회사가 보험 견적을 처리하는 AWS를 사용하여 웹 애플리케이션을 설계하고 있습니다. 사용자는 응용 프로그램에서 견적을 요청합니다. 견적은 견적 유형으로 구분해야하며 24 시간 이내에 응답해야하며 손실되지 않아야합니다. 솔루션은 설정 및 유지 관리가 간단해야합니다.
이러한 요구 사항을 충족하는 솔루션은 무엇입니까?
- A. 견적 유형에 따라 여러 Amazon Kinesis 데이터 스트림을 생성합니다. 적절한 데이터 스트림에 메시지를 보내도록 웹 애플리케이션을 구성하십시오. Kinesis Client Library (KCL)를 사용하여 자체 데이터 스트림에서 메시지를 풀링하도록 애플리케이션 서버의 각 백엔드 그룹을 구성합니다.
- B. 여러 Amazon Simple Notification Service (Amazon SNS) 주제를 생성하고 견적 유형에 따라 Amazon SQS 대기열을 자체 SNS 주제에 등록합니다. SNS 주제 대기열에 메시지를 게시하도록 웹 애플리케이션을 구성합니다. 자체 SQS 대기열을 작동하도록 각 백엔드 애플리케이션 서버를 구성합니다.
- C. 단일 Amazon Simple Notification Service (Amazon SNS) 주제를 생성하고 Amazon SQS 대기열을 SNS 주제에 구독합니다. 견적 유형에 따라 적절한 SQS 대기열에 메시지를 게시하도록 SNS 메시지 필터링을 구성합니다. 자체 SQS 대기열을 작동하도록 각 백엔드 애플리케이션 서버를 구성합니다.
- D. 견적 유형에 따라 여러 Amazon Kinesis Data Firehose 전송 스트림을 생성하여 Amazon Elasticsearch Service (Amazon ES) 클러스터에 데이터 스트림을 전송합니다. 적절한 배달 스트림으로 메시지를 보내도록 웹 응용 프로그램을 구성합니다. Amazon ES에서 메시지를 검색하고 그에 따라 처리하도록 애플리케이션 서버의 각 백엔드 그룹을 구성합니다.

> 24시간 이내 응답, 손실되지 않아야됨, 설정 및 유지관리 간단

b/d
- 답은 d로 나와있는데 정보가 거의 없음
- kinesis: 실시간 처리인데 24시간 이내에만 응답하면 되어서 관리 부담이 적은 sns, sqs쪽이 맞다고 생각
- sqs, sns를 사용하면 C가 맞는듯: [hands-on example](https://aws.amazon.com/ko/getting-started/hands-on/filter-messages-published-to-topics/)
- b를 사용하면 한개의 SQS로 가능할거라고 생각해서 b를 했는데 오히려 개발시에 유지관리 부담이 늘어날 가능성도 있어서 애매함



