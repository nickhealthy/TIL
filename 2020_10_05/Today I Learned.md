# Today I Learned

* A Cloud Guru : 클라우드 강좌 사이트에서 실습 연습
  * 실습 - Introduction to AWS Identity and Access Management (IAM)
  * Introduction To Amazon S3 (Simple Storage Service)
  * Creating a Basic Amazon S3 Lifecycle Policy
  * Configuring Amazon S3 Buckets to Host a Static Website with a Custom Domain
* 리다이렉트 vs 포워드
* 엘리스 코딩 알고리즘 강좌

---



### A Cloud Guru - Introduction to AWS Identity and Access Management (IAM)

[URL](https://learn.acloud.guru/handson/e907e3ec-1021-4208-8bb4-04ddfd99280d)

* **IAM 서비스는 사용 가능한 API / 서비스에 대한 사용자의 액세스 및 권한을 관리 할 수 있도록 하는 서비스**
* 사용자와 그룹 확인
* 스크립트가 어떤 식으로 설정 되어 있는지 확인이 가능함

![0. lab_diagram_Screen Shot 2018-03-27 at 8.40.46 AM](https://github.com/nickhealthy/TIL/blob/master/2020_10_05/img/0.%20lab_diagram_Screen%20Shot%202018-03-27%20at%208.40.46%20AM.png)

----



### Introduction To Amazon S3 (Simple Storage Service)

[URL](https://learn.acloud.guru/handson/01b6c36f-f1e9-4067-b9d9-5ef24bae56ad)

* 리포지토리를 생성하는 것이라고 생각하면 됨
* S3는 클라우드의 확장 가능한 스토리지 서비스
  * 버킷의 이름은 **유니크**해야함
* 버킷 설정에서 **버전관리 선택 시** 파일마다 버전 설정을 다르게 해줄 수 있음(권한 설정 등)
* **Lab Diagram**

![1. lab_diagram_Introduction to Amazon S3](https://github.com/nickhealthy/TIL/blob/master/2020_10_05/img/1.%20lab_diagram_Introduction%20to%20Amazon%20S3.png)

---



### Creating a Basic Amazon S3 Lifecycle Policy

[URL](https://learn.acloud.guru/handson/d485772f-3128-4e2e-99c8-e948bbb95fc2)

* AWS Glacier는 다른 AWS 스토리지 옵션보다 저렴한 스토리지를 제공하는 장기 아카이브 스토리지 서비스입니다.
* 특정 기간 동안 데이터에 액세스하지 않은 경우 수명주기 정책을 사용하여 S3 스토리지 클래스간에 자동으로 이동할 수 있습니다. 이 실습에서는 기본 Amazon S3 수명주기 정책을 생성합니다.

![2. lab_diagram_CCP_Diagrams - s3 lifecycle policy (1)](https://github.com/nickhealthy/TIL/blob/master/2020_10_05/img/2-1.%20lab_diagram_CCP_Diagrams%20-%20s3%20lifecycle%20policy%20(1).png)

![2-1. lab_diagram_CCP_Diagrams - s3 lifecycle policy (1)](https://github.com/nickhealthy/TIL/blob/master/2020_10_05/img/2.%20lab_diagram_CCP_Diagrams%20-%20s3%20lifecycle%20policy%20(1).png)

---



### 접근 통제 3단계

- 식별 (Identification)
- 인증 (Authentication)
  - 지식: 패스워드
  - 소유: OTP, 스마트폰, 인증서, 주민등록증
  - 특징: 성문, 지문, 정맥, 홍채, 필기체 서명
- 인가 (Authorization): 권한을 부여하는 것
  - 화면, 기능, 데이터

---



### Configuring Amazon S3 Buckets to Host a Static Website with a Custom Domain

[URL](https://learn.acloud.guru/handson/2d9e37e1-3733-4227-8e46-8d22dc519585)

* S3 서비스 - 버킷을 만든 후 버킷 정책 편집기에서 올라온 데이터들은 퍼블릭 값으로 되도록 설정
  * S3 서비스에서는 **자체적으로 URL**를 제공하지만, **사용자들이 이용하기 편리하게 도메인 이름을 설정해야함**
  * 정적 웹사이트를 생성
* Route 53 서비스에서 생성된 레코드 이름(cmcloudlab373.info)을 가지고 똑같은 이름으로 S3 저장소를 만듬
  * Route 53 서비스에서 - 레코드를 생성 및 설정

![3. lab_diagram_static-site](https://github.com/nickhealthy/TIL/blob/master/2020_10_05/img/3.%20lab_diagram_static-site.png)

---



### 리다이렉트 vs 포워드

* 300번대의 응답헤더와 리다이렉트 주소를 브라우저로 전달하고, 브라우저가 전달받은 주소로 재요청

![4. 리다이렉트](https://github.com/nickhealthy/TIL/blob/master/2020_10_05/img/4.%20%EB%A6%AC%EB%8B%A4%EC%9D%B4%EB%A0%89%ED%8A%B8.PNG)

---



### Creating Amazon S3 Buckets, Managing Objects, and Enabling Versioning

[URL](https://learn.acloud.guru/handson/c3870bf7-5d98-44fe-acf5-4c0bbdddb3d9)

* 개체 관리 (프라이빗, 퍼블릭 설정)
* 버전 관리 활성화를 통해 버전을 원하는 형태로 바꿀 수 있음

---



### 엘리스 코딩 알고리즘 강좌

1. 프로그래머의 사고방식
   * 데이터의 흐름과 저장 - 자료구조
   * 시간과 공간 효율성 - 알고리즘
2. 자료구조 (Data Structure)
   * 자료구조 종류: 정수, 실수, 문자, 배열, 해쉬, 링크드리스트, 스택, 큐, 트리
   * Optimized(최적화)된 자료구조가 가장 좋음
3. 좋은 알고리즘의 조건
   * 명확성: 코드가 어떤 수행을 하기 위해 존재
   * 유한성: 무한루트 X
   * 효율성: 효율성이 높을수록 좋은 알고리즘
4. 자료구조와 알고리즘의 관계
   * 자료구조: 자료구조를 활용하여 어떤 문제를 해결
   * 알고리즘: 때와 장소에 맞는 적절한 자료구조를 구현하기 위해서 필요

#### 객체 지향 프로그래밍

* 언어들 : JAVA, C++, C#, Python
* 캡슐화: 코드를 묶어서 정리 정보 은닉
* 상속: 기존 코드 재활용
* 다형성: 코드를 더 간단하게