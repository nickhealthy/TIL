# Today I Learned

* MFA (Multi-factor authentication) 다중 요소 인증
* IAM 그룹 권한 관리
* 로깅 및 경고
* P88 Lambda 함수가 오류로 인해 비정상적으로 종료된 회수를 추적
* P91 S3 버킷과 로깅 | 로그를 저장
* P92 일정 간격으로 세 개 이상의 람다 함수 오류가 발생하면 이메일 경보를 발송
* P96 S3 버킷에서 발생하는 데이터 이벤트를 모으는 추적을 생성
* P99 CloudWatch 결제 경보 생성
* 비용 모니터링 및 최적화 | 간이 월 비용 계산기 (simple monthly caculator)
* 서버리스 아키텍처에서의 인증과 권한
* Auto0
* P114 24-Hour-Video 웹사이트 생성

---



### MFA (Multi-factor authentication) 다중 요소 인증

* 다른 보안 계층을 추가 (추가적인 보안 수단)
* 모든 사용자에게 MFA를 사용하게 하는 것을 추천
  * IAM 사용자에서 *Security Credentials*탭에서 **Assign MFC Device** 옵션

---



### IAM 그룹 권한 관리

* AWS에서는 개별적으로 권한을 정의하는 대신 **그룹을 사용해 IAM 사용자에게 권한을 할당**할 것을 권장
  * 그룹에 참여하는 모든 사용자는 **그룹에 할당된 권한을 상속 받음**

#### 자격 증명 기반

* IAM 사용자 또는 역할이 수행할 수 있는 것을 지정

#### 자원기반 권한

* S3 버킷 또는 SNS 토픽과 같은 **AWS 자원에서 할 수 있는 것 또는 누가 그 자원에 접근할 수 있는지 지정**

#### 권한과 정책

* 처음 IAM 사용자를 만들면 계정에 있는 **어떤 자원에 접근하거나 작업을 수행할 수 없음**
  * 사용자가 **수행할 수 있는 작업을 기술하는 정책을 생성해** 사용자 권한을 부여해야 함

* 관리형 정책
  * 관리형 정책은 사용자, 그룹 및 역할에는 적용되지만 자원에는 적용되지 않음
  * 관리형 정책은 독립형
  * 관리형 정책은 재사용 및 변경 관리에 좋음
* 인라인 정책
  * 특정 사용자, 그룹 또는 역할에 직접 생성되고 붙음
  * **자원 기반 정책은 항상 인라인임**
* 정책은 *JSON* 표기법을 사용
  * *Condition*를 이용해 **언제, 어떤 IP 등 조건에 맞춰 설정할 수 있음**

---



### 로깅 및 경고

#### CloudWatch

* **자원 및 서비스를 모니터링**하고 다양한 지표를 기반으로 **경보를 설정**하며 자원 성능에 대한 **통계**를 볼 수 있는 AWS 구성요소
  * CloudWatch를 사용해 지표를 추적하고 예기치 않은 이벤트에 대한 경보를 설정

#### CloudTrail

* API 호출을 기록하는 AWS 서비스

  * API 호출자의 신원, 소스 IP 주소 및 이벤트와 같은 정보를 기록

    > 이 데이터는 S3 버킷에 있는 **로그 파일**에 저장됨
    >
    > 로그를 생성하고 **AWS 서비스가 무엇을 수행하고** 누가 그 **서비스를 호출하는지에 대한 정보**를 수집하는 효과적인 방법임

* 로그를 *CloudWatch* 로그 그룹으로 직접 푸시하도록 구성할 수 있음

* Lambda는 CloudWatch에 자동으로 로그를 남김

#### 필터, 지표, 경보

* 지표 필터는 **들어오는 로그 이벤트**에 대해 **실행되는 패턴을 지정함**
  * 일치하는 항목이 있으면 CloudWatch 지표가 업데이트 되어 그래프를 생성하거나 경보를 생성하는데 사용
  * 패턴 : 로그에서 찾을 용어나 표현을 지정하는데 사용

---



### P88 Lambda 함수가 오류로 인해 비정상적으로 종료된 회수를 추적

* **지표 필터**, 지표 및 경보를 만들어 Lambda 함수가 오류로 인해 비정상적 종료된 횟수를 추적
  * CloudWatch를 이용

---



### P91 S3 버킷과 로깅 | 로그를 저장

* S3는 CloudWatch와 별도로 접근 요청 및 로그 정보를 추적 가능
  * 누가 또는 어떤 서비스가 버킷에 접근하는지에 대한 **감사를 하고 추가 정보를 얻는데 유용**
  * S3 로그는 버킷 이름, 요청시간, 동작, 응답 상태와 같은 정보를 저장
* 서버 엑세스 로깅 이벤트
  * 대상 접두사 = 폴더(디렉터리) 개념 (로그들을 여러 폴더로 분류)

---



### P92 일정 간격으로 세 개 이상의 람다 함수 오류가 발생하면 이메일 경보를 발송



* 주제 및 이메일 구독 생성
* 경보 생성
* 잘못된 값(오류가 발생할 수 있는 값)으로 람다 함수를 실행 (3번 이상 테스트)
* 경고 메일 확인

---



### P96 S3 버킷에서 발생하는 데이터 이벤트를 모으는 추적을 생성

* CloudTrail은 계정 전반의 걸쳐 사용자 또는 사용자를 대신해 **서비스가 호출한 API를 기록**
  * API 호출을 감사하고 문제를 진단하고 해결하는데 도움을 줌

* 관리 이벤트: 버킷을 만들거나 조회할 때 발생되는 이벤트
* **데이터 이벤트**: 만들어진 버킷에 데이터를 다운로드되거나 업로드 됬을 때 추적 (리소스 사용 등)

#### 절차

1. CloudTrail 생성 및 CloudWatch 연동
2. 전에 만든 Log 수집용 S3 버킷과 연동
3. S3 or 서비스들의 데이터 조작을 통해 쌓여있는 로그를 확인
   * ex) 기존에 있던 리소스 다운로드 등

---



### P99 CloudWatch 결제 경보 생성

* 결제 기준 임계값을 통해 경보를 생성할 수 있음

---



### 비용 모니터링 및 최적화 | 간이 월 비용 계산기 (simple monthly caculator)

* CloudCheckr(http://cloudcheckr.com) 와 같은 서비스는 사용중인 서비스와 리소스를 분석해 **비용을 추적하고, 경보를 보내며, 비용 절감에 도움을 줌**

---



### 서버리스 아키텍처에서의 인증과 권한

#### Stateful => 웹서버

* 클라이언트의 상태 정보를 유지하는 서버

  * 세션을 이용해서 구현

    > 세션(Session) : 서버와 클라이언트 호스트와 호스트 사이에서 연결을 보장(유지)하고 있는 형태

* HTTP 프로토콜은 기본적으로 클라이언트와 서버와의 연결을 유지하지 않는 프로토콜 형태

#### Stateless

* 서버로부터 발급 받은 토큰을 이용해서 신분을 증명 및 인증
  * **서비스 간에 사용자 정보를 교환하고 확인하기 위해 토큰을 사용할 수 있음**
  * JSON 웹 토큰 (JWT) = JSON Web Token
    * **Lambda함수는 토큰이 합법적인지 확인한 다음** 모든 것이 정상이면 실행을 계속할 수 있음

#### 절차

1. 사용자는 *Auth0*와 같은 **인증 서비스를 사용해** 로그인
2. 인증 서비스는 사용자에 대한 정보가 들어있는 **JSON 웹 토큰을 반환**
   * 클라이언트는 특정 서비스에 대해 위임 토큰을 요청할 수 있음 (ex: 데이터베이스)
3. 클라이언트가 데이터베이스에 기록
4. 클라이언트가 **AWS API Gateway에 요청을 보냄**
   * API Gateway에서 **정상적인 토큰인지 검증**
5. Lambda 함수는 비밀 정보를 관리하는데 사용되는 **타사 서비스에서 데이터베이스 자격 증명을 조회**
6. 함수가 조회된 자격증명을 사용해 데이터베이스에 기록

> 1. 프레젠테이션 계층은 API Gateway를 통해 서비스, 데이터베이스 또는 컴퓨팅 기능과 직접 통신
>
> 2. 프론트엔드에서 많은 서비스에 직접 접근할 수 있지만, 일부 서비스는 추가 보안 조치 및 유효성 확인이 가능한 컴퓨팅 서비스 기능에 숨겨져 있어야 함

![2. 24-hour-video 인증추가](https://github.com/nickhealthy/TIL/blob/master/2020_10_21/img/2.%2024-hour-video%20%EC%9D%B8%EC%A6%9D%EC%B6%94%EA%B0%80.PNG)

---



### Auto0

* **범용 자격증명 플랫폼**
  * 다중 요소 인증 MFA(Multi-Factor Authentication) 및 TouchID 지원 가능
* Auto0으로 사용자를 인증하면 클라이언트 애플리케이션이 **JSON 웹 토큰을 받음**
  * 사용자의 식별이 필요하거나 다른 서비스에 대한 위임 토큰(Auth0)을 요청하는데 사용할 수 있는 경우라면 Lambda 함수에서 사용 가능
* AWS와 호환성이 좋음

---



### 24-Hour-Video 웹사이트 생성 (P114)

* Bootstrp + jQuery 기반으로 작성

* 실제 서비스 환경에서는 SPA 형태의 웹 애플리케이션을 구현 ⇒ Agular, React, … JavaScript Framework을 이용해서 구현



#### 1. 프로젝트 생성

```bash
c:\serverless> mkdir 24-hour-video

c:\serverless> cd 24-hour-video

# 초기화
c:\serverless\24-hour-video> npm init -y

# 필요한 패키지 다운 및 로컬 환경에서 테스트 할 수 있도록 설정 다운
# local-web-server : 웹 사이트를 호스팅하려면 웹 서버가 필요함
C:\serverless\24-hour-video>npm install local-web-server --save-dev

# package.json를 만드는 이유
`배포할 때 필요한 라이브러리만 다운받고 편리하게 설치하기위해

# package.json 파일에 start 스크립트를 추가
{
  "name": "24-hour-video",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
  # 웹 서버를 시작하고 웹 사이트를 호스팅
`    "start": "ws", 
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "local-web-server": "^4.2.1"
  }
}
```

#### 2. UI 템플릿 다운로드

[부트스트랩 기본값 다운로드](http://www.initializr.com/)

* 다운로드 받은 템플릿 파일을 프로젝트 폴더(c:\serverless\24-hour-video\)로 압축 해제

* 웹 서버 실행 및 확인

```cmd
c:\serverless\24-hour-video> npm start
```

#### 3. Auth0 구성 (p116)

 **1. https://auth0.com/ 사이트에 회원 가입**

* TENANT DOMAIN에 본인이 기억할 수 있는 이름을 입력

* REGION을 US로 설정

  

2. Application 생성

* Applications 메뉴 > CREATE APPLICATION 버튼 클릭
* Name: 24-Hour-Video
  * Type : Single Page Web Applications



3. Auth0를 사용해 사용자 등록 및 인증을 처리
   * https://developers.kakao.com/ 에서 애플리케이션을 등록 및 JWT 발행
   * Connection > Socail 메뉴에서 제공하는 소셜 로그인 사이트에 앱 추가 후 호출에 필요한 **클라이언트 ID와 클라이언트 Secret를 확인**
     * TRY CONNECTION 기능을 이용해서 쇼셜 로그인 후 사용자 정보가 **JSON 형태로 제공되는 것을 확인**

#### 4. 웹 사이트에 Auth0 추가 (p119)

1. Auth0 Lock 스크립트, Domain, Client ID 등을 확인

```javascript
<script src="https://cdn.auth0.com/js/lock/11.27/lock.min.js"></script>
```

2. Auth0 Lock 스크립트 추가

   * 로그인 / 가입 대화 상자를 제공하는 Auto0의 무료 위젯

   * c:\serverless\24-hour-video\index.html 파일에 Auth0 Lock 스크립트 추가

```html
								:
        <script src="js/vendor/bootstrap.min.js"></script>

        <script src="https://cdn.auth0.com/js/lock/11.27/lock.min.js"></script>
        
        <script src="js/main.js"></script>
```

3. Domain, Client ID 설정
   * c:\serverless\24-hour-video\js\config.js 파일 생성

```javascript
var configConstants= {
    auth0: {
        // auth0.com > Applications > 24-Hour-Vide - Settings 에서 확인
        domain: 'alskadmlcraz.us.auth0.com',
        clientId: '7mKxNlK9dD6lrswC2Jn9aSllrQf5sUkL'
    }
};
```

4. c:\serverless\24-hour-video\index.html 파일에 로그인, 로그아웃, 사용자 프로필 버튼을 추가

```html
                        :
        <div id="navbar" class="navbar-collapse collapse">

          <!--
          <form class="navbar-form navbar-right" role="form">
            <div class="form-group">
              <input type="text" placeholder="Email" class="form-control">
            </div>
            <div class="form-group">
              <input type="password" placeholder="Password" class="form-control">
            </div>
            <button type="submit" class="btn btn-success">Sign in</button>
          </form>
          -->
          <!-- 121 페이지 참조 -->
          <div class="navbar-form navbar-right">
            <button id="user-profile" class="btn btn-default">
              <img id="profilepicture" />&nbsp;<span id="profilename"></span>
            </button>
            <button id="auth0-login" class="btn btn-success">Sign in</button>
            <button id="auth0-logout" class="btn btn-success">Sign out</button>
          </div>

        </div><!--/.navbar-collapse -->
                        :
```

5. c:\serverless\24-hour-video\user-controller.js 파일 생성
   * 로그인, 로그아웃 버튼을 클릭했을 때 액션을 정의하는 역할

![3. Auto 범용 인증 구현](https://github.com/nickhealthy/TIL/blob/master/2020_10_21/img/3.%20Auto%20%EB%B2%94%EC%9A%A9%20%EC%9D%B8%EC%A6%9D%20%EA%B5%AC%ED%98%84.PNG)