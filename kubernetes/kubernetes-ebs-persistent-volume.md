# kubernetes ebs persistent volume

data pipeline을 eks로 migrate 하던 중 luigi history sqlite 파일을 persistent volume으로 저장해야 할 일이 생겼다.

## Create AWS EBS volume

우선 ebs volume을 따로 생성해야한다. eksctl같은 툴로 만들 수 있는지 찾아봤는데 아직까지 그런 기능이 없는듯. 직접 만들어서 붙여야 하는 점은 불편하다.

몇가지 제한 사항이 있는데, 

- pod가 실행되는 node는 aws ec2 instance여야 함 -> EKS를 사용한다면 해당 내용은 자동으로 해당된다.
- 위 instance는 EBS volume과 동일한 지역, 가용영역에 있어야 함 -> zone 뿐 아니라 availability zone까지 같아야 하기 때문에 직접 확인 후 해당 AZ에 만들어줘야한다.
- EBS는 volume을 마운트하는 단일 ec2 instance만 지원

aws cli

```
aws ec2 create-volume --availability-zone=us-west-2c --size=10 --volume-type=gp2
```


