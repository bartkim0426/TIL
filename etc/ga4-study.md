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
- 보고서: 앱/웹 발생하는 행동을 동시에 확인 가능
- 향상된 측정기능
- 향상된 전자상거래: 기본제공 x
- App event dispatching: background/foreground 전환 등
- 히트 유형
  - 기존에는 hit 유형이 나뉨
    - 거의 pageview, event hit만 씀
  - GA4에서는 pageview가 없어지고 event로 통합
- 이벤트
  - 정해진 파라미터로만 값을 전송
    - event category, event action, event label
  - GA4: 3가지로 수집 방법이 나뉨
    - 1. 자동수집 이벤트
      - pageview 등 page와 관련된 이벤트
      - 사전 정의된 이벤트로 비활성화 불가
    - 2. 향상된 측정 이벤트
    - 3. 맞춤 이벤트
- 사용자 식별 기능
  - UA: clientID (cookie) 기반
    - 별도로 userId 사용 기능 활성화해야함
  - GA4: Device, userID, google signal 등을 사용
    - 쿠키 의존률을 낮춤
- 세션
  - UA: 세션이 끊기는 조건이 있음
    - 30초, 소스, 도메인변경 등
  - GA4: 세션 끊기는 조건이 조금 다름


![image](https://user-images.githubusercontent.com/23415251/165628475-f28d71a3-bca3-41fa-a1b0-8619f2c273e4.png)

- UA: 대부분 세션 기반 모델을 사용
- GA4: 이벤트 기반 모델

## 04. GA4 소개 4 - GA4 장점과 단점, 기초 개념

![image](https://user-images.githubusercontent.com/23415251/165629766-43c84be8-b37f-4765-ae46-b8c6b1cf1953.png)

- 기존 속성 (UA 버전)
	- 장점
		- 보기 단으로 필터링을 해서 원하는 데이터 확인 가능
			- 간단한 방법

	- 단점
		- 앱/웹을 동시에 확인하려면 별도의 보고서를 생성
		- 교차 분석이 힘듦
		- 향상된 측정 기능을 사용 불가

- GA4
	- 장점
		- 앱/웹을 동시 확인할수있는 기본 보고서
		- 향상된 측정을 사용하여 다양한 이벤트 쉽게 수집 가능
		- 데이터 비교 가능: 기존에는 기본 보고서에서 날짜만 비교할 수 있었음

	- 단점
		- GA4는 뷰가 없음 -> 어느정도 단점이 될 수 있음
			- 속성 단 보고서에서 일일히 필터를 해야함
			- 전체적인 흐름을 보는건 GA4로 보는게 효과적
		- 기존 보기의 맞춤 보고서 기능이 얿음
		- 데이터 샘플링 제한: 1000만 이벤트 제한 (데이터 조회 기간 이내)
			- 유료버전이 출시될 가능성이 높음
		- firebase 기반 보고서가 많음
			- 일반 UA를 쓰던 사람들에게 어색하이 많음


<img width="848" alt="image" src="https://user-images.githubusercontent.com/23415251/166557555-318654d2-aec5-4b5e-a119-88060b297666.png">


- GA4 analytics 계정에서 고급 기능 사용 가능
	- 무료 버전에서도 BigQuery export 사용 가능

### 사용자 지표

<img width="878" alt="image" src="https://user-images.githubusercontent.com/23415251/166558693-accff207-929e-4900-b8c7-7dcc3cf2d0db.png">


UA: cid를 기준으로 하게 됨
GA4: 사용자 식별값을 설정할 수 있음 -> ID 공간 (여러 데이터가 포함된 집합)
- User-ID: 로그인한 사용자를 위한 자체 영구 id 데이터
- google 신호 데이터: 고객의 동의를 얻은 이후 수집한 사용자 데이터
	- ga에서 나오는 데이터(성별, 연령값)의 기반
- 기기 id: UA 버전 id와 같음 -> id 공간으로 사용
	- web: browser cookie id
	- app: app instance id

#### UA 속성, GA4 속성의 ID 공간 비교

<img width="800" alt="image" src="https://user-images.githubusercontent.com/23415251/166560904-ad4e4e6c-21b4-4fb0-9a3f-fa6284f9464d.png">

UA 속성
- 대부분 기기 id에 의존
- 여러 기기에서 사용자 여정을 측정하여 사용자 중복을 제거하기 어려움

GA 속성
- 사용가능한 모든 id 공간을 이용해 데이터 처리
- 우선순위를 사용할 수 있음
	- user id -> google signal id -> device id
- 이 로직을 바탕으로 하나의 사용자 여정을 생성
	- 모든 보고서에서 사용자 중복값을 제거
	- 통합적/전체적인 관점으로 파악 가능


## 05. GA4 소개 - UA 버전과이 차이점 (듀얼 태깅)


듀얼태깅: 동시에 데이터를 전송하는 방법 (UA, GA4)

### 추적코드 설치 방법

1. gtag.js
2. GTM
3. gtag.js + GTM

- script 방식의 한계점
	- 개발자/퍼블리셔의 도움이 필요함


- UA, GA4 방식 차이
	- UA: 추적유형 설정 (페이지뷰, 이벤트 등..)
	- GA4: 구성 태그 설정 후 이벤트 태크에 대한 설정
		- GA4는 모두 이벤트를 사용해서 수집하기 때문


GA4 구성 태그
- 데이터를 수집하려는 모든 페이지에서 실행되어야됨
- 다른 GA4 이벤트 태그보다 먼저 실행되어야함
- GA 쿠키 설정, 자동 이벤트 및 향상된 측정 이벤트 전송 등의 동작을 처리
- UA에서 페이지뷰 전송 태그와 흡사하다고 보면 됨: 공통적으로 설정해야하는 부분

GA4 이벤트 태그
- 이벤트 설정 시 GA로 전송할때 사용하는 태그
- 해당 이벤트 전송할때에만 수집하는 데이터 등

### 앱 형태에서 어떻게 처리되는지

![image](https://user-images.githubusercontent.com/23415251/166584105-ca17f141-5fd1-4181-974d-414c12b7d8df.png)

Native 방식
- GA4는 firebase SDK를 설치하는 방식으로 작동
- 앱 개발자의 코드 추가가 필수적임

Hybrid 방식 (hybrid web)
- web view가 로드시에 추가 설정
- app data 형식이 web data 형태로 GTM을 거쳐 GA로 전송됨


## 06. 속성 구조

- UA 계정: 속성 > 보기 구조
	- 앱, 웹 데이터 하나의 속성으로 수집하면서 필터링을 통해 보기를 나누는 구조

- GA4 계정: 속성 구조
	- 웹, 앱 데이터를 하나의 속성으로 수집하면서 속성 상에서 데이터를 구분
	- 통합된 데이터 형태에서 필터링 필요

![image](https://user-images.githubusercontent.com/23415251/166584105-ca17f141-5fd1-4181-974d-414c12b7d8df.png)


## 07. GA4의 향상된 측정과 GA4 이벤트 핵심 지표 및 차이점 (매개변수, 유저프로퍼티의 구조 등)

### 향상된 측정

생성시 기본적으로 togle on 되어있음

- 향상된 측정 이외 자동으로 수집되는 이벤트들도 있음
	- [자동 수집 이벤트](https://support.google.com/analytics/answer/9234069?hl=ko)
- [향상된 측정 이벤트](https://support.google.com/analytics/answer/9216061?hl=ko&ref_topic=9756175)
	- page view: 이벤트 형태로 수집됨
	- 그 외 scroll, click, view_search_results, video_*, file_download


![image](https://user-images.githubusercontent.com/23415251/166586223-6f2cbc85-f778-4e90-8108-f329b98e82cf.png)

- 페이지 조회는 향상 기능을 켜면 추가 설정 없이 바로 사용 가능
- 다른 이벤트들은 자동화된 이벤트는 아니고 GTM을 통해서 좀 더 쉽게 구현할 수 있다고 함
	- scroll, video_* 등은 GTM을 통해서 추가 가능
	- 이탈 클릭 (click)은 스크립트로 추가가 필요하다고함

추차적인 [추천 이벤트](https://support.google.com/analytics/answer/9267735?hl=ko&ref_topic=9756175)들이 있음
- GTM에서 해당 이벤트 명을 그대로 사용해야함 (login, purchase 등)
- 문서에 잘 정리되어있어서 한번 살펴보면 좋음

## 08. 기본 데이터 범위와 기본 지표, 수집되는 이벤트

이전 데이터 모델: 세션 기반의 데이터
- 세션에 따라 다르게 나뉨

새로운 데이터 모델: 이벤트 기반의 데이터

### 용어에 대한 설명

1. 페이지뷰
- UA에서는 이벤트가 아닌 별도의 페이지뷰
- GA4에서는 구성 태그를 통해서 사용

2. 이벤트
- hit를 제외한 모든 개념
- 모든 행동을 다 event로 처리됨: pageview, 전환 등

3. 매개변수 및 사용자 속성
custom dimension
- UA에서는 추가적인 데이터를 전송시킬때 사용하는 기능
- GA에서는 이벤트인지, 사용자 속성인지에 따라서 나눠짐
	- 고객과 관련된 데이터는 사용자 속성 (user property)
	- 이벤트 단위의 특정한 값 (매개변수)

4. 전자상거래
- 기능이 따로 있지 않음
- GA4의 경우에는 무조건 event로 구현
- 매개변수에 상품명, 브랜드, 가격 등 특정 정보를 담아서 전송

### 기본 지표

![image](https://user-images.githubusercontent.com/23415251/166587934-a6445550-77cd-4dff-ba82-036bba698e07.png)

- hit type: GA4는 모두 이벤트로 통합
- custom dimension, metric scope
	- 세션 범위가 없어짐
	- hit 범위가 이벤트 매개변수로 사용됨
	- 사용자 범위가 사용자 속성의 명칭으로 불림
	- event 범위, 사용자 범위 두 개만 남는다고 생각하면 됨


![image](https://user-images.githubusercontent.com/23415251/166588198-197aec68-35fd-4a6e-96d9-06c8dc2d6fd0.png)


- 사용자 속성: 모든 페이지에 필요하기 때문에 GA4 구성 태그에 추가하게 됨
- 이벤트 파라미터
	- 구성 태그가 아닌 이벤트 태그에서 설정
	- 이에 따른 매개변수 등을 설정

### GA4 수집되는 이벤트 총정리

![image](https://user-images.githubusercontent.com/23415251/166588334-d0ec276e-2d4b-4ff2-9b1e-2b5457b3f8b0.png)

### 이벤트 수집시 한도

![image](https://user-images.githubusercontent.com/23415251/166588432-7d154696-cdd7-45ac-b9b0-32733181e12f.png)

- 이벤트 매개변수 값 길이 100자 -> pageview url 등에서 이슈가 될 수 있음
- 사용자 속성 수, 이름 및 값 길이 제한도 꽤나 있음


