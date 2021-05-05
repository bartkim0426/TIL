# Codebuild, Codepipeline, CodeDeploy


## Codebuild

![image](https://docs.aws.amazon.com/codebuild/latest/userguide/images/arch.png)

### Codebuild FAQ

Q: AWS Codebuild란?
- 클라우드에서 제공되는 완전관리형의 지속적 통합 서비스

Q: 빌드 프로젝트는 무엇인가요?
- CodeBuild에서 빌드를 실행하는 방법을 정의하는데 사용
- 소스코드를 가져올 위치, 사용할 빌드 환경, 실행할 빌드 명령, 빌드 출력을 저장할 위치 등의 정보를 포함

Q: 빌드 프로젝트 구성방법
- 콘솔, AWS CLI를 통해 구성. 혹은 `buildspec.yml` 파일에서 명령 지정 가능


Q: 코드빌드가 지원하는 소스 리포지토리 및 프로그래밍 프레임워크
- AWS CodeCommit, S3, GitHub, GitHub Enterprise, Bitbucket
- Java, Ruby, Python, Go, Node.js, Android, NET Core, PHP 및 Docker에 사전 구성된 환경
- 도커 이미지를 생성한 후 ECR이나 docker hub에 업로드하면 현재 환경을 별도 사용자 지정도 가능

Q: 빌드가 실행되면 어떻게 되는지?
- 프로젝트에 정의된 클래스의 임시 컴퓨팅 컨테이너 생성
- 해당 컨테이너를 지정된 런타임 환경으로 로드
- 소스 코드를 다운로드하여 구성된 명령 실행
- 생성된 아티팩트를 S3 버킷에 업로드
- 컴퓨팅 컨테이너 삭제
- 실행되는동안 출력은 service console 및 CloudWatch에 스트리밍됨

Q: 이벤트에 대한 알림 및 경고를 수신하려면?
- Amazon SNS 알림 형식으로 수신.
- 또한 AWS Chatbot을 사용하여 Slack 채널 등으로 알림 전송되게 구성 가능

Q: 저장된 빌드 아티팩트를 암호화 할 수 있는지?
- AWS KMS를 사용하여 암호화 가능


### buildspec example

다양한 sample이 [user guide](https://docs.aws.amazon.com/codebuild/latest/userguide/use-case-based-samples.html)에서 제공

docker sample

```
version: 0.2

phases:
  pre_build:
    commands:
      - echo Logging in to Amazon ECR...
      - aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com
  build:
    commands:
      - echo Build started on `date`
      - echo Building the Docker image...          
      - docker build -t $IMAGE_REPO_NAME:$IMAGE_TAG .
      - docker tag $IMAGE_REPO_NAME:$IMAGE_TAG $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG      
  post_build:
    commands:
      - echo Build completed on `date`
      - echo Pushing the Docker image...
      - docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG
```



## CodePipeline

![image](https://d1.awsstatic.com/product-marketing/CodePipeline/CodePipeline_Elements.1390531beabe7fd38b2414c39800136eed24e9c8.png)

### CodePipeline FAQ
Q: AWS CodePipeline이란?
- 소프트웨어를 릴리즈하는데 필요한 단계를 모델링, 시각화 및 자동화 할 수 있게 해주는 지속적 전달 서비스

Q: 파이프라인이란?
- 소프트웨어 변경 사항이 릴리즈 프로세스에 적용되는 방법을 설명하는 워크플로우 구성

Q: 수정 버전이란?
- 파이프라인에서 정의한 소스 위치에 이뤄진 변경사항
- 소스코드, 빌드 결과, 구성, 또는 데이터

Q: 스테이지란?
- 하나 이상의 작업으로 이뤄진 그룹

Q: 작업이란?
- 수행 버전에서 수행한 일.
- 단계 구성에서 정의한대로 지정된 순서나 직렬/병렬로 발생

Q: 아티팩트란?
- 작업은 파일 또는 파일 세트에 따라 실행되는데 이러한 파일을 아티팩트라고 함.
- 파이프라인의 이후 작업에 따라 작동함. ex) 소스 작업은 코드의 최신버전을 소스 아티팩트로 출력 -> 빌드 작업이 이를 읽음

Q: CodePipeline과 통합할 수 있는 제품은?
- CodeCommit, S3, CodeBuild, CodeDeploy, Elsatic Beanstalk, CloudFormation, OpsWorks, ECS, Lambda

## CodeDeploy

![image](https://docs.aws.amazon.com/codedeploy/latest/userguide/images/sds_architecture.png)

### CodeDeploy FAQ

Q: AWS CodeDeploy란?
- EC2 인스턴스 및 온프레미스 인스턴스를 비롯한 모든 인스턴스에 대한 코드 배포를 자동화하는 서비스

Q: Elsatic Beanstalk 및 OpsWorks등 다른 배포 및 관리 서비스와 어떻게 다른지?
- CodeDeploy는 인스턴스에서 소프트웨러를 배포하고 업데이트하도록 지원하는데 초점을 맞춘 빌딩 블록 서비스
- EB, OpsWork는 end-to-end application 관리 솔루션 (instance 컨트롤 불가)

Q: 애플리케이션이란?
- 인스턴스 그룹에 배포할 소프트웨어와 구성의 모음

Q: 수정 버전이란?
- AppSpec 파일과 함께 배포할 수 있는 컨텐츠의 특정 버전

Q: 배포 그룹이란?
- instance 및 람다 함수를 배포에 그룹화하기 위한 CodeDeploy 엔터티.
- 스테이징, 프로덕션 등 여러 배포 그룹을 정의 가능

Q: 배포 구성이란?
- 배포 그룹을 통해 배포가 진행되는 방식을 지정 (장애 처리 방법 등)
- 구성을 설정하여 다운타임 없이 배포 수행 가능

Q: 배포에 지정해야하는 파라미터?
- 1. 수정 버전: 배포할 것을 지정
- 2. 배포 그룹: 배포할 위치를 지정
- 3. 배포 구성: 배포할 방법을 지정 (optional)

Q: AppSpec 파일이란?
- 복사할 파일과 실행할 스크립트를 지정하는 구성 파일.

```
version: 0.0
os: linux
files: 
# You can specify one or more mappings in the files section.
  - source: /
    destination: /var/www/html/WordPress
hooks:
 # The lifecycle hooks sections allows you to specify deployment scripts.
ApplicationStop: 
# Step 1: Stop Apache and MySQL if running.
    - location: helper_scripts/stop_server.sh
BeforeInstall: 

# Step 2: Install Apache and MySQL.
# You can specify one or more scripts per deployment lifecycle event.
    - location: deploy_hooks/puppet-apply-apache.sh
    - location: deploy_hooks/puppet-apply-mysql.sh 
 AfterInstall: 

# Step 3: Set permissions.
    - location: deploy_hooks /change_permissions.sh
      timeout: 30
      runas: root

# Step 4: Start the server.
    - location: helper_scripts/start_server.sh
      timeout: 30
      runas: root
```

Q: 배포 수명 주기 이벤트는?
- 배포는 사전에 설정된 일련의 단계를 거침 -> 이를 배포 수명 주기 이벤트로 사용할 수 있음
- ApplicatinoStop -> DownloadBundle -> BeforeInstall -> Install -> AfterInstall -> ApplicationStart -> ValidateService

Q: CodeDeploy를 사용하여 배포하려면 코드를 어떻게 변경해야하는지?
- 코드를 변경할 필요 없이 AppSpec 파일을 루트 디렉토리에 추가하면 됨


## codebuild + codedeploy + codepipeline

![image](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2020/09/30/Screen-Shot-2020-09-30-at-6.05.53-PM.png)


