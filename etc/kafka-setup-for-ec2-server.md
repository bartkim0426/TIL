# kafka setup for ec2 server


회사에서 kafka를 사용해볼 일이 생겼다. 그동안 카프카에 대해 전혀 아는게 없었기 때문에 AWS의 managed kafka (MSK)로 셋업 해 보기 전에 그냥 개인 ec2 인스턴스에 카프카를 띄워보면서 전체적인 내용을 정리해보고자 한다.

카프카는 대용량 대규모 메세지를 빠르게 처리하기 위해서 linkedin에서 개발한 messaging platform이다.

자세한 설명은 다양하고 좋은 글들이 있어서 참고하면 될 듯 하다. (기회가 되면 한 번 정리해보겠다)


이번 글의 목표는

- AWS ec2 instance에 kafka server 띄우기
- cli를 통해 producer, consumer 개념 이해하기

이다.

> 파이썬 클라이언트를 선택한 이유는 이후에 flask에서 카프카 producer를 호출해야 하기 때문이다.

> client 이전 글까지는 데브원영님의 tacademy 강의를 보면서 참고하였다.

### kafka setup
aws 인스턴스에 로그인 한 뒤 kafka를 설치한다. (aws 인스턴스를 만들고 로그인하는 과정은 생략한다)

> 인스턴스의 security group - inbound rules에 TCP:9092 포트를 client에서 접속하려는 ip에 열어주어야 한다. (그냥 전체 ip에 열어줘도 상관없음)

```
# install java
$ sudo yum install -y java-1.8.0-openjdk-devel.x86_64

# install kafka
# 필요에 따라 버전을 선택하면 된다
$ wget http://mirror.navercorp.com/apache/kafka/2.5.0/kafka_2.12-2.5.0.tgz
$ tar -xvf kafka_2.12-2.5.0.tgz

# kafka directory 확인
$ ls -l kafka_2.12-2.5.0
합계 56
-rw-r--r-- 1 ec2-user ec2-user 32216  4월  8  2020 LICENSE
-rw-r--r-- 1 ec2-user ec2-user   337  4월  8  2020 NOTICE
drwxr-xr-x 3 ec2-user ec2-user  4096  4월  8  2020 bin
drwxr-xr-x 2 ec2-user ec2-user  4096  4월  8  2020 config
drwxr-xr-x 2 ec2-user ec2-user  8192 11월 30 11:36 libs
drwxr-xr-x 2 ec2-user ec2-user    44  4월  8  2020 site-docs

# 메모리 설정; kafka는 기본으로 1GB 메모리가 할당되기 때문에 t2.micro 등 작은 인스턴스를 사용중이면 할당 메모리 영역을 줄여주어야됨
$ export KAFKA_HEAP_OPTS="-Xmx400m -Xms400m"

# kafka listener port 설정
$ cd kafka_2.12-2.5.0
$ vi config/server.properties
...
listeners=PLAINTEXT://:9092
advertised.listeners=PLAINTEXT://{aws ec2 public ip}:9092
...

# zookeeper server 실행
$ bin/zookeeper-server-start.sh -daemon config/zookeeper.properties
# kafka 실행
bin/kafka-server-start.sh -daemon config/server.properties

# 확인
$ jps
1156 Kafka
1173 Jps
790 QuorumPeerMain

# log 확인
$ tail -f logs/*
```


## local에서 kafka cli 실행 및 접속

mac os 기준으로 설명되었다.

### kafka 설치

```
# kafka 설치 (brew)
$ brew cask install java
$ brew instal kafka

# or 직접 파일을 다운로드 받아서 설치도 가능
$ curl http://mirror.navercorp.com/apache/kafka/2.5.0/kafka_2.13-2.5.0.tgz --output kafka.tgz 
$ tar -xvf kafka.tgz
$ cd kafka_2.13-2.5.0/bin
```

### topic 생성

`{aws ec2 public ip}` 대신 위에서 생성한 instance id 필요

```
$ kafka-topics --create --bootstrap-server {aws ec2 public ip}:9092 --replication-factor 1 --partitions 3 --topic test
```

### producer 테스트

실제로 producer로 데이터를 넣어보자.

```
$ kafka-console-producer --bootstrap-server {aws ec2 public ip}:9092 --topic test
>hello
>kafka
>hello
>world
```

### consumer 테스트

위에서 넣은 데이터를 consumer로 받아보자. (위의 producer는 닫지 말고 새로운 탭이나 창을 열어서 실행해보자)

```
$ kafka-console-consumer --bootstrap-server {aws ec2 public ip}:9092 --topic test --from-beginning
hello
kafka
hello
world
```

test group을 지정해서 해볼 수 있다.

group을 지정하지 않은 consumer는 임시 consumer로 끄고 다시 실행시키면 초기화된다 (다른 임시 consumer가 생성됨)

```
$ kafka-console-consumer --bootstrap-server {aws ec2 public ip}:9092 --topic test -group testgroup --from-beginning
hello
kafka
hello
world
```

이제 consumer를 종료한뒤 (ctrl+c) 위 producer 탭에서 다른 메세지 (ex. 1, 2, 3...)을 입력 다시 켜보면 test group에서 이미 사용한 메세지들은 저장하기 때문에 이후 메세지들만 받아온다

```
$ kafka-console-consumer --bootstrap-server {aws ec2 public ip}:9092 --topic test -group testgroup --from-beginning
1
2
3
```


### 그룹 상태 확인

테스트 그룹 리스트와 상태를 볼 수 있다. (시점에 따라 아까 만든 임시 consumer도 나올 수 있음)

```
$ kafka-consumer-groups --bootstrap-server {aws ec2 public ip}:9092 --list
testgroup
```

describe로 상세한 정보도 볼 수 있다.

```
$ kafka-consumer-groups --bootstrap-server {aws ec2 public ip}:9092 --group testgroup --describe
Consumer group 'testgroup' has no active members.

GROUP           TOPIC           PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             CONSUMER-ID     HOST            CLIENT-ID
testgroup       test            1          1               1               0               -               -               -
testgroup       test            0          6               6               0               -               -               -
testgroup       test            2          5               5               0               -               -               -
```

간단히 설명하면, PARTITION과 CURRENT-OFFSET은 전체 메세지와 받은 메세지를 나타내고, LAG는 지연된 메세지를 나타낸다.

consumer group을 끈 상태에서 producer에서 메세지를 넣고 (ex. a, b, c) 다시 실행시켜보면 실시간으로 LAG이 증가한 것을 볼 수 있다.

```
$ kafka-consumer-groups --bootstrap-server {aws ec2 public ip}:9092 --group testgroup --describe
Consumer group 'testgroup' has no active members.

GROUP           TOPIC           PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             CONSUMER-ID     HOST            CLIENT-ID
testgroup       test            1          1               1               0               -               -               -
testgroup       test            0          6               6               0               -               -               -
testgroup       test            2          5               8               3               -               -               -
```

2번 파티션에 LAG가 3개가 발생했다. 다시 consumer group을 키면 아까 받아오지 못한 데이터를 받아온다.

```
$ kafka-console-consumer --bootstrap-server {aws ec2 public ip} --topic test --group testgroup
1
2
3
```

각 그룹별로 offset을 리셋도 가능하다.

`--to-earliest` flag는 가능한 가장 마지막의 offset으로 리셋하는 것

```
$ kafka-consumer-groups --bootstrap-server {aws ec2 public ip}:9092 --group testgroup --topic test --reset-offsets --to-earliest --execute
GROUP                          TOPIC                          PARTITION  NEW-OFFSET
testgroup                      test                           0          0
testgroup                      test                           1          0
testgroup                      test                           2          0
```

NEW-OFFSET이 다 0으로 바뀌었다. group을 describe해서 다시 상태를 확인해보자.

```
$ kafka-consumer-groups --bootstrap-server {aws ec2 public ip}:9092 --group testgroup --describe
Consumer group 'testgroup' has no active members.

GROUP           TOPIC           PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             CONSUMER-ID     HOST            CLIENT-ID
testgroup       test            1          0               1               1               -               -               -
testgroup       test            0          0               6               6               -               -               -
testgroup       test            2          0               8               8               -               -               -
```

CURRENT-OFFSET이 모두 0으로 되어있고 LAG로 잡혀 있는 것을 볼 수 있다.

특정 파티션만 변경도 가능하다. test 토픽의 1번 파티션만 특정 offset으로 설정 후 reset도 가능하다.

```
# 여기서는 partition 1의 offset이 3개가 안되어서 아래 메세지가 발생하였따.
$ kafka-consumer-groups --bootstrap-server {aws ec2 public ip}:9092 --group testgroup --topic test:1 --reset-offsets --to-offset 3 --execute
[2020-11-30 21:14:32,217] WARN New offset (3) is higher than latest offset for topic partition test-1. Value will be set to 1 (kafka.admin.ConsumerGroupCommand$)

GROUP                          TOPIC                          PARTITION  NEW-OFFSET
testgroup                      test                           1          1
```

partition 1의 offset만 LAG가 없어진 것을 볼 수 있다. (offset 3으로 지정했는데 한개밖에 없었으므로..)

```
$ kafka-consumer-groups --bootstrap-server {aws ec2 public ip}:9092 --group testgroup --describe

Consumer group 'testgroup' has no active members.

GROUP           TOPIC           PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             CONSUMER-ID     HOST            CLIENT-ID
testgroup       test            1          1               1               0               -               -               -
testgroup       test            0          0               6               6               -               -               -
testgroup       test            2          0               8               8               -               -               -
```

