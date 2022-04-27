# GA4 study

# Part 1. GA4 & 구글 애널리틱스


## 01. GA4 소개 1 - 타임라인, 출시의도

![image](https://user-images.githubusercontent.com/23415251/165624546-88de0897-cb61-464a-9877-e4b118e1740b.png)

- 2006년도 최초 런칭
- 2012년 핵심적인 업데이트로 Universal Analytics
  - GA3, UA라고 불림
  - 현재까지 계속 업데이트된 버전
- 2016년 Firebase
  - google에서 firebase를 인수하면서 해당 툴 런칭
  - GA: 웹로그 분석 툴
    - 마케터 등이 더 많이 사용
    - 웹 보고서 위주로 구성
  - firebase
    - analytics 기능도 있지만 프론트/백엔드 개발이 가능한 툴
    - 개발자 (특히 앱 개발자)가 많이 사용
    - 앱 중심의 보고서로 구성
- 2019년 App&Web property beta
  - 위 두가지 (웹, 앱 보고서)를 합친 버전
- 2020년 GA4
  - 위의 App&web property의 beta 버전이 없어지고 정식 출시된 버전
  - GA의 web 버전과 firebase가 합쳐진 버전

### Google Analytics Time Line
- GA4에서 새롭게 제공하는 속성
- 웹 사이트 및 모바일 앱의 데이터를 단일 보고서 셋트로 통합
- 플랫폼간 분석 수행 가능

![image](https://user-images.githubusercontent.com/23415251/165625324-fe792bb1-8837-414e-9be9-683dd93b0ae8.png)

### GA4 소개 - 출시의도
- 차세대 Google Analytics
- 비즈니스 성장에 맞춰 확장
- 여러 기기와 플랫폼에서 측정할 수 있도록 설계
  - web뿐만 아니라 애플리케이션도
- 변화하는 디지털 환경 (어플리케이션의 성장)에 적응할 수 있도록 측정 전략을 조정
- 머신러닝을 사용하여 마케팅 개선을 위한 예측 제공
- 개인정보 보호를 우선시하는 플랫폼
  - 개인정보 보호법 개선으로 인한 영향
  - 데이터 삭제 및 수집, 보관 기능

## 02. GA4 소개 2 - 출시의도
- UA (GAv3) 앱 보고서 형식의 제한
  - UA에도 앱 보고서가 있지만 제한점이 있었음
    - 앱 설치, 업데이트, 최초 실행, 제거 등
    - 앱 관점의 데이터 지표 부재
- GA SDK 구축 방식의 제한
  - 2019년에 SDK 업데이트 종료
  - firebase SDK로 앱을 구축하라는 권장사항
- 많이 활용하지 않는 앱 보고서 형식
  - 첫번째와 비슷
  - web: page view
  - app: screen view
- 행동 흐름 보고서 형식 차이
  - web에서는 이전/다음페이지 등 유입 경로 파악 가능
  - app에서는 기능을 제공하지 않음
- 그래서 현재는 대부분 app 보고서를 따로 만들지 않고 웹 보고서 형태로 구축하고 있음

## 03. GA4 소개 3- UA 버전과의 차이점

![image](https://user-images.githubusercontent.com/23415251/165626828-8a1b6760-ac2f-4ba6-961c-5a8e9a934395.png)
