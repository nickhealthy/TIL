# Today I Learned

* AWS에서 제공하는 모니터링 기능
  * CloudWatch 
  * CloudTrail 
  * API 작업, 비 API 작업 모두를 기록
  * 이벤트는 형태에 따라 관리 이벤트와 데이터 이벤트로 분류
  * 추적(trail)
* Monitoring and Notifications with CloudWatch Events and SNS
* AWS EC2 Custom Logging with CloudWatch
* AWS Access Control Alerts with CloudWatch and CloudTrail

* Creating a Simple AWS Lambda Function
* Using the AWS CLI to Create an AWS Lambda Function
* Triggering Lambda from SQS

---



### AWS에서 제공하는 모니터링 기능

* CloudTrail, CloudWatch, AWS Config



#### CloudWatch 

* AWS와 비 AWS 리소스에서 발생하는 수치 성능 자료를 수집설정한 임계값을 초과했을 때 알림을 전송하거나 특정 작업을 수행하기 위한 정보를 전달

  

#### CloudTrail 

* AWS 리소스의 모든 읽기, 쓰기 작업의 상세 로그를 보관



#### API 작업, 비 API 작업 모두를 기록

* API 작업 : 인스턴스 시작, S3 버킷 생성, VPC 생성 등
* 비 API 작업 : Management Console 로그인 등



#### 이벤트는 형태에 따라 관리 이벤트와 데이터 이벤트로 분류

* 관리 이벤트 보안 주체가 AWS 리소스에서 실행하는 작업을 포함쓰기 전용과 읽기 전용으로 분류쓰기 전용 이벤트: 리소스를 변경하거나 변경할 수 있는 API 작업읽기 전용 이벤트: 리소스를 읽되, 변경하지 않는 API 작업
* 데이터 이벤트S3 객체 수준의 활동(파일 업로드/다운로드), Lambda 함수 실행



#### 추적(trail)

* 90일이 경과한 이벤트 기록을 저장하거나 CloudTrail이 기록하는 이벤트 유형을 사용자가 정의할 때 추적을 생성
* 특정 이벤트를 기록하고, S3 버킷에 CloudTrail 로그 파일을 전달, 로그 파일에는 JSON 형태의 로그 항목이 하나 이상 포함
  * eventTime
  * userIdentity
  * eventSource
  * eventName
  * awsRegion - 리소스가 있는 리전. 글로벌 서비스인 경우 항상 us-east-1
  * sourceIPAddress

---



### Monitoring and Notifications with CloudWatch Events and SNS

* EC2 인스턴스의 상태 변경이 발생할 때 엔지니어(관리자)에게 알림을 제공

![1. lab_diagram_Monitoring and Notifications with CW Events and SNS](https://github.com/nickhealthy/TIL/blob/master/2020_10_13/img/1.%20lab_diagram_Monitoring%20and%20Notifications%20with%20CW%20Events%20and%20SNS.png)

---



### AWS EC2 Custom Logging with CloudWatch

* EC2 인스턴스에서 발생하는 로그 정보를 수집해서 CloudWatch로 전송

![2. lab_diagram_DevOps Pro Playbook - CustomEC2Logging](https://github.com/nickhealthy/TIL/blob/master/2020_10_13/img/2.%20lab_diagram_DevOps%20Pro%20Playbook%20-%20CustomEC2Logging.png)

---



### AWS Access Control Alerts with CloudWatch and CloudTrail

![3. lab_diagram_Screen Shot 2018-07-05 at 11.15.51 AM](https://github.com/nickhealthy/TIL/blob/master/2020_10_13/img/3.%20lab_diagram_Screen%20Shot%202018-07-05%20at%2011.15.51%20AM.png)

---



### Creating a Simple AWS Lambda Function

프로비저닝: 서비스가 실행될 수 있도록 준비된 상태

사실상 기능을 구현할 코드만 올리면 되기 때문에 **서버리스**

![4. lab_diagram_LDD_Live_Act_1](https://github.com/nickhealthy/TIL/blob/master/2020_10_13/img/4.%20lab_diagram_LDD_Live_Act_1.png)

---



### Using the AWS CLI to Create an AWS Lambda Function

* CLI 환경에서 람다 함수 만들기

![5. lab_diagram_LDD_Live_Act_5_Create_Lambda_CLI](https://github.com/nickhealthy/TIL/blob/master/2020_10_13/img/5.%20lab_diagram_LDD_Live_Act_5_Create_Lambda_CLI.png)

---



### Triggering Lambda from SQS

![6. lab_diagram_Triggering Lambda from SQS](https://github.com/nickhealthy/TIL/blob/master/2020_10_13/img/6.%20lab_diagram_Triggering%20Lambda%20from%20SQS.png)

---

