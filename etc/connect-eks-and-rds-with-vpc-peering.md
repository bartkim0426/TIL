# connect eks and rds with vpc peering

EKS 클러스터와 RDS가 다른 vpc에 있는 경우 SG만으로 rds 접근을 할 수 없다.

이 경우 vpc peering을 통해 두 vpc를 연결해준 이후에 접속이 가능.

## VPC

상황에 따라 다르겠지만, 아래의 예시로 vpc가 구성되어 있다고 가정하고 진행.

- RDS vpc: cidr - `10.0.0.0/16`
  - 3 subnets: 
  - 2 route tables: rtb-61162b04 (public), rtb-67162b02 (private)
  - db subnet group: default-vpc-dd7658b8
  - db security group: sg-cee1c5aa
- EKS vpc: cidr - `10.3.0.0/16`
  - 2 route tables: rtb-be4cb8d9 (public)

- VPC peering
  - requester: rds vpc (vpc-dd7658b8)
  - accepter: eks vpc (odk-shared)
