# Today I Learned

* 비밀 값 관리
  * 접근 통제 (Access Control)
  * 접근 통제를 우회해서 접근했을 때 - 암호화 관리
  * 비밀 값 관리 원칙
* AWS Secrets Manager
  * KMS(Key Management Service)
* Secrets Manager 사용 실습 p313
  * 절차
  * IAM 사용자 생성
  * Secrets Manager에 비밀 키 저장
  * 비밀 값 요청
* 모니터링 시스템 구축 및 활용 p 190
  * CloudWatch Agent로 메모리, 디스크 사용량 지표, 로그 기록
  * CloudWatch 대시보드
  * CloudWatch 경보 생성
  * CloudWatch Logs
* AWS Elastic Beanstalk

---



### 비밀 값 관리

#### 접근 통제 (Access Control)

접근 통제를 통해서 보호를 함

* 식별
* 인증
* 인가



#### 접근 통제를 우회해서 접근했을 때 - 암호화 관리

* 기밀성: 권한있는 **사용자에게만 그 정보가 보이는 것**
* 무결성: 권한있는 사용자가 인가된 절차에 따라 바꾸기 전까지는 **그 정보는 항상 그대로 유지**되어야 함
* 가용성: 권한있는 사용자가 정보를 요청했을 때 제공될 수 있어야 함



#### 비밀 값 관리 원칙

1. 비밀 값은 버전 관리 시스템에 업로드되면 안된다.
2. 비밀 값은 최소한의 인원만 알고 있어야 한다.
   * 개발자는 로컬이나 테스트 환경에서 개발하면 되기 때문에 테스트 환경용으로 생성된 비밀 값만 알고 있으면 되고, 운영 환경의 값은 알 필요가 없음
3. 비밀 값과 아닌 값은 분리해서 관리해야 한다.

---



### AWS Secrets Manager

* AWS에서 제공하는 비밀 값 관리 서비스
* 비밀 값들을 AWS Secrets Manager에 저장해두고(자동으로 암호화해줌) 애플리케이션에서 AWS의 API를 호출해서 받아가 사용하는 방식

#### KMS(Key Management Service)

* **암호화하는데 사용하는 키를 관리할 수 있는 서비스**

* IAM의 사용자 권한 기능을 사용하면 비밀 값을 관리할 수 있는 일부 관리자만 비밀에 접근하도록 설정 가능\

---



* 대칭키 => 암호화 키와 복호화 키가 동일 (비밀키, 유일키, 관용 암호화 방식)
  * DES, 2DES, 3DES, AES, ARIA, SEED, … 
* 비대칭키 => 암호화 키와 복호화 키가 상이 (개인키, 공개키 쌍으로 구성)
  * 공개키 암호화 방식
* 보안강도 = 비도
  * 암호문으로 암호화에 사용된 키를 찾아내는데 걸리는 시도 회수
  * 비도 128 = 2^128 만큼 시도해야 암호화에 사용된 키를 알아낼 수 있다.

![1. 보안 키](https://github.com/nickhealthy/TIL/blob/master/2020_10_15/img/1.%20%EB%B3%B4%EC%95%88%20%ED%82%A4.PNG)



---



### Secrets Manager 사용 실습 p313

* *Secrets Manager*는 AWS에서 제공하는 비밀 값 관리 서비스
* 비밀 값들을 *AWS Secrets Manager*에 저장해두고 애플리케이션에서 AWS의 API를 호출해서 받아가 사용하는 방식

#### 절차

1. 관리자가 Secrets Manager에 비밀을 만들고 그 안에 여러 *Key, Value* 비밀 값들을 등록
2. 비밀 값들은 **AWS KMS(Key Management Service)라는 키 관리 서비스를 통해 암호화 됨**
3. 비밀 값들을 사용하는 서버에서 **AWS CLI나 SDK**를 이용해 특정 IAM 사용자로 Secrets Manager에 비밀 값을 요청
4. IAM에서 요청한 사용자에게 해당 비밀을 조회할 수 있는 권한이 있다고 판단하면 Secrets Manager가 비밀의 값을 복호화해서 응답으로 줌
5. 애플리케이션에서는 받은 비밀 값을 사용

#### IAM 사용자 생성

* AWS CLI 사용자 생성
  * **프로그래밍 방식 액세스**
    * 프로그램으로 호출하는 방식으로 AWS에 접근하는 아이디
    * AWS API, CLI, SDK 및 기타 개발 도구에 대해 **액세스 키 ID 및 비밀 액세스 키**을 활성화 할 수 있음
  * AWS Management Console 액세스
    * GUI 환경에서 설정
  * 필요한 권한을 부여하기 위해 정책에 연결
  * **엑세스 키 ID**, **비밀 액세스 키** 제공

* AWS CLI 로그인

  * AWS CLI는 AWS 콘솔에 접속하는 대신 *AWS API*를 호출하기 때문에 어떤 사용자로 로그인해서 사용하는지 명시해둬야 함

  ```bash
  $ aws configure
  ```

#### Secrets Manager에 비밀 키 저장

* 비밀 키를 저장하고, KMS(Key Management Service)에서 비밀 키를 암호화 해줌

#### 비밀 값 요청

* 비밀 값들을 요청하는 서버에서 AWS CLI나 SDK를 이용해 비밀 값을 요청
  * CLI는 직접 명령어를 실행하거나 셸 스크립트를 이용할 때 유용
  * 애플리케이션에서 사용해야 하는 경우 SDK를 사용하는 것이 유용

---



### 모니터링 시스템 구축 및 활용 p 190

* CloudWatch: AWS에서 제공하는 AWS 내 자원과 애플리케이션에 대한 모니터링 및 관리 서비스
  * 수집된 값들을 이용해 자동화된 작업을 수행할 수 있게 해주는 관리 서비스의 역할도 가능



#### CloudWatch Agent로 메모리, 디스크 사용량 지표, 로그 기록

* CLI 환경에서 CloudWatch 설정 파일을 설정



#### CloudWatch 대시보드

* 많은 그래프들을 한눈에 보여주기 위해 대시보드 기능을 제공



#### CloudWatch 경보 생성

* 대시보드로 한눈에 모니터링을 할 수 있어도 사람이 24시간 모니터링하고 있을 수는 없음
* 경보 생성
* 경보는 **기관**과 **값**을 이용해 조건을 걸어두어 생성할 수 있음



#### CloudWatch Logs

* CloudWatch의 기능 중 로그를 관리하는 기능

---



### AWS Elastic Beanstalk

* 애플리케이션과 환경을 생성할 수 있게 해줌
  * 가장 짧은 시간 내에 개발자가 애플리케이션을 실행할 수 있는 서버 환경을 만드는 방법의 하나
* **Paas(Platform as a service)**
  * **환경 구성을 해서 제공을 해주면, 내가 원하는 소프트웨어만 올리는 기능**
  * 서버 구성, Auto Scaling, 배포 자동화, 모니터링 등 서버 운영에 필요한 대부분의 작업을 최대한 자동화해서 제공하는 서비스

* IaaS(Infrastructure as a Service)
  * 이전동안 모든 작업 및 설정을 한 것이 IaaS
  * EC2