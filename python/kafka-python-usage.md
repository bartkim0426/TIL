# kafka python usage

> 카프카 셋업은 이전 글인 [kafka setup for ec2 server](../etc/kafka-setup-for-ec2-server.md)에서 설명되어있다. 해당 셋업을 완료한 후 테스트를 하면 편리하다.

python client로 실제 메세지 처리하는 방법.

kafka python client는 크게 세가지 정도가 있다.
- [confluent-kafka-python](https://github.com/confluentinc/confluent-kafka-python): 퍼포먼스가 가장 좋음. confluent의 공식 클라이언트.
- [kafka-python](https://github.com/dpkp/kafka-python): pure python. confluent-python에 비해서는 속도가 느림
- [pykafka](https://github.com/Parsely/pykafka): 2018년 이후 업데이트가 잘 안됨..

나는 다음의 이유로 kafka-python을 사용하였다.
- 활발한 contribution 및 활동
- 직관적이고 간결한 사용법
- sync 기준 confluent-kafka python client와 속도가 크게 차이나지 않음
- 순수 파이썬이라 코드를 보기에 더 좋을 것 같음

사용법은 [깃헙 페이지](https://github.com/dpkp/kafka-python)의 README를 잠깐 보기만 해도 쉽게 알 수 있을 정도로 직관적이다.

제공하는 `KafkaConsumer`, `KafkaProducer`를 이용하여 쉽게 메세지를 produce, consume 할 수 있다.


### Produce message

메세지를 퍼블리싱하는 방법.

producer를 만들고 `producer.send()`를 이용하면 아주 간단하다.

```
>>> from kafka import KafkaProducer
>>> producer = KafkaProducer(bootstrap_servers='localhost:1234')
>>> for _ in range(100):
...     producer.send('foobar', b'some_message_bytes')
```

json 형태의 데이터를 사용하면 serializer를 producer를 정의할 때 바로 사용도 가능하다.

```
>>> # Serialize json messages
>>> import json
>>> producer = KafkaProducer(value_serializer=lambda v: json.dumps(v).encode('utf-8'))
>>> producer.send('fizzbuzz', {'foo': 'bar'})
```

[공식 문서](https://kafka-python.readthedocs.io/en/master/apidoc/KafkaProducer.html)에 더 자세한 설명이 있는데 produce는 로직 자체가 간단해서 딱히 정리하지는 않겠따.

### Consume message

메세지를 받는 KafkaConsumer 클라이언트를 만들어서 받는 방식이다.

설정된 timeout만큼 기다리면서 polling할 수도 있고, 바로 받아온 데이터를 처리하는 batch 형태로 처리도 가능하다. 나는 한시간에 한번 정도 처리를 위해서 후자로 선택하였다.

계속 떠 있는 프로그램을 만들어서 멀티프로세스로 처리하는 예시도 많이 보았다.

```
```

### topic 관리 등 작업
