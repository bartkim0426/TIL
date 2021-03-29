# [AWS Well-architected framework](https://d1.awsstatic.com/whitepapers/ko_KR/architecture/AWS_Well-Architected_Framework.pdf)

잘 설계된 클라우드 프레임워크란 무엇인지에 대한 설명.

## 프레임워크의 5가지 부문

### 운영 우수성

효과적인 개발 및 워크로드 실행을 지원하고, 작업에 대한 인사이트를 얻고, 지원 프로세스 및 절차를 지속적으로 개선하여 비즈니스 가치를 제공할 수 있는 능력

- 코드를 통한 운영
- 자주 발생하고 되돌릴 수 있는 사소한 변경 내용 적용
- 운영 절차를 수시 정제하기
- 실패 예측
- 모든 운영상 실패로부터 학습

관련 서비스
- Amazon S3: 로그 데이터 보관
- AWS Glue: 분석을 위해 S3의 로그 데이터를 검색 및 준비
- AWS Glue Data Catalog: 관련된 메타데이터 저장
- Amazon Athena: 쿼리
- QUickSight: BI 도구 (시각화, 탐색, 분선)

### 보안

보안을 강화하고 데이터, 시스템 및 자산을 보호하는 능력

7개의 설계 원칙
- 강력한 자격 증명 기반
- 추적 기능 활성화: 모니터링, 알림, 로그 및 지표 수집
- 모든 계층에 보안 적용
- 보안 모법 사례의 자동 적용
- 전송 및 보관 중인 데이터 보호
- 사람들이 데이터에 쉽게 엑세스 할 수 없도록 유지
- 보안 이벤트에 대비

탐지 관련 서비스
- CloudTrail 로그, AWS API 호출 및 CloudWatch: 경보, 모니터링 제공
- AWS Config: 구성 내역 제공
- Amazon GuardDuty: 악성 또는 인증되지 않은 동작을 모니터링
- S3: 서비스 수준 로그 (액세스 요청 기록 등)

데이터 암호화 관련
- S3 SSE(서버측 암호화)
- SSL termination이라고 부르는 전체 HTTPS 암호화/복화화 프로세스를 Elastic Load Balancing을 통해 설정 가능

### 안정성


