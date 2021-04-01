# aws cdk for deploy

CDK (Cloud Development Kit): 프로그래밍 언어로 cloud application 리소스를 정의할 수 있는 software development framework.

테라폼과 흡사하지만 yaml이 아니라 python, jaav, typescript 등의 프로그래밍 언어로 작성할 수 있고, 결과물은 cloudformation으로 나온다.

테라폼을 선호하거나 기존에 테라폼을 사용하고 있었으면 [cdk tf](https://github.com/hashicorp/terraform-cdk)도 지원한다.


[aws CDK workshop](https://cdkworkshop.com/15-prerequisites.html)을 통해서 기본적인 튜토리얼을 따라 해 볼 수 있다.

> 아래 명령어들은 aws profile이 설정된 이후에 진행하여야 한다.  `~/.aws/credentials`에 등록한 이후에 `AWS_PROFILE` 환경변수를 지정해주자.


## 프로젝트 구조

### entry point

`app.py`

```
#!/usr/bin/env python3

from aws_cdk import core
from cdkworkshop.cdkworkshop_stack import CdkWorkshopStack

app = core.App()
CdkWorkshopStack(app, "cdkworkshop", env={'region': 'us-west-2'})

app.synth()
```

### main stack

`cdkworkshop/cdkworkshop_stack.py`

```
from aws_cdk import (
    aws_iam as iam,
    aws_sqs as sqs,
    aws_sns as sns,
    aws_sns_subscriptions as subs,
    core
)

class CdkWorkshopStack(core.Stack):

    def __init__(self, scope: core.Construct, id: str, **kwargs) -> None:
        super().__init__(scope, id, **kwargs)

        queue = sqs.Queue(
            self, "CdkWorkshopQueue",
            visibility_timeout=core.Duration.seconds(300),
        )

        topic = sns.Topic(
            self, "CdkWorkshopTopic"
        )

        topic.add_subscription(subs.SqsSubscription(queue))
```

## commands

### cdk synth

cdk 앱이 실행되면 cdk는 python 코드에서 cloudfromation 템플릿을 생성하는데 이를 `synthesize`라고 부른다 (좀 쉬운 단어좀 써주지...)

명령어를 실행시키면 cloudformation 템플릿이 나온다.

```
$ cdk synth

Resources:
  CdkworkshopQueue18864164:
    Type: AWS::SQS::Queue
...
```

### cdk deploy

처음 배포를 하게 되면, "bootstrap stack"을 설치해야한다. 이는 operation에 필요한 자원들을 포함하고 있다. (s3 bucket 등)

```
$ cdk bootstrap

 ⏳  Bootstrapping environment aws://622385363639/us-west-2...
CDKToolkit: creating CloudFormation changeset...
[██████████████████████████████████████████████████████████] (3/3)

 ✅  Environment aws://622385363639/us-west-2 bootstrapped.
```

이제 배포를 할 수 있다.

```
$ cdk deploy cdkworkshop

This deployment will make potentially sensitive changes according to your current security approval level (--require-approval broadening).
Please confirm you intend to make the following modifications:

IAM Statement Changes
┌───┬─────────────────────────┬────────┬─────────────────┬───────────────────────────┬─────────────────────────────────────────────────────────┐
│   │ Resource                │ Effect │ Action          │ Principal                 │ Condition                                               │
├───┼─────────────────────────┼────────┼─────────────────┼───────────────────────────┼─────────────────────────────────────────────────────────┤
│ + │ ${CdkworkshopQueue.Arn} │ Allow  │ sqs:SendMessage │ Service:sns.amazonaws.com │ "ArnEquals": {                                          │
│   │                         │        │                 │                           │   "aws:SourceArn": "${CdkworkshopTopic}"                │
│   │                         │        │                 │                           │ }                                                       │
└───┴─────────────────────────┴────────┴─────────────────┴───────────────────────────┴─────────────────────────────────────────────────────────┘
(NOTE: There may be security-related changes not in this list. See https://github.com/aws/aws-cdk/issues/1299)

Do you wish to deploy these changes (y/n)? y
```

warning이 나오지만 테스트 앱이기 떄문에 무시하고 진행해보자.


```
Stack ARN:
arn:aws:cloudformation:us-west-2:622385363639:stack/cdkworkshop/xxxxx....
```

완료되면 arn이 반환된다. 

aws cli나 console에서 cloudformation이 생성된 것을 볼 수 있다.

![image](https://i.imgur.com/nLlw76c.png)


