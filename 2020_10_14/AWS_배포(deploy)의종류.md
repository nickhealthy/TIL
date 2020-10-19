# Today I Learned

* 배포(deploy)의 종류
  * Rolling (현재 위치 배포) - 무중단 배포
  * Blue/Green - 무중단 배포
  * Canary
* API Gateway의 개념
* API Gateway Canary Release Deployment
* Deploying a Basic Infrastructure Using CloudFormation Templates
* AWS 인프라 구축 가이드 05장 배포과정

---



### 배포(deploy)의 종류

#### Rolling (현재 위치 배포) - 무중단 배포

* 생길 수 있는 문제점
  * 부하가 집중될 때 부하를 견딜 수 있는지 예측
  * Rollback(v2 --> v1)에 많은 시간이 소요될 수 있음

![image-rollback](https://github.com/nickhealthy/TIL/blob/master/2020_10_14/img/1.%20rollback.PNG)



#### Blue/Green - 무중단 배포

* 구버전과 신버전이 동시에 서비스 (주소 또는 포트번호로 구분) 됨 - 접속 경로로 엑세스 및 접속
* 구버전에서 신번전으로 일제히 변경
  * 버전을 동시에 돌리고 있다가 안정화가 되었을 시, 블루 그룹 연결을 **로드 밸런스로부터** 연결 해제

![image-blue,green](https://github.com/nickhealthy/TIL/blob/master/2020_10_14/img/2.%20blue%2Cgreen.PNG)



#### Canary

* 위험을 빠르게 감지할 수 있는 배포기법
* **점진적으로 신버전**으로 증가

![image-20201014095252886](https://github.com/nickhealthy/TIL/blob/master/2020_10_14/img/3.%20canary.PNG)

---

#### API Gateway 개념

* 개발자가 쉽게 API를 만들고, 게시, 유지보수, 모니터링할 수 있는 서비스
* Serverless
  * Lambda
  * API Gateway --> Canary Deployment

![3-2 New-API-GW-Diagram.c9fc9835d2a9aa00ef90d0ddc4c6402a2536de0d](https://github.com/nickhealthy/TIL/blob/master/2020_10_14/img/3-2%20New-API-GW-Diagram.c9fc9835d2a9aa00ef90d0ddc4c6402a2536de0d.png)

---



### API Gateway Canary Release Deployment

* 버전 Iv1에서 Iv2로 버전 업데이트를 할때 퍼센트지로 옮겨갈 수 있는 기법
* 안정화될 시 점진적으로 새로운 버전으로 증가
* API Gateway 서비스에서 **Canary** 설정

![3-1. lab_diagram_AWS DevOps Pro 2019 - APIGatewayCanary](https://github.com/nickhealthy/TIL/blob/master/2020_10_14/img/3-1.%20lab_diagram_AWS%20DevOps%20Pro%202019%20-%20APIGatewayCanary.png)

---



### Deploying a Basic Infrastructure Using CloudFormation Templates

* InfraStructure as Code
  * 일정한 양식의 코드 기반으로  AWS 서비스 및 인프라를 구축
* CloudFormation **스택 기능**을 이용

![4. lab_diagram_cftemp](https://github.com/nickhealthy/TIL/blob/master/2020_10_14/img/4.%20lab_diagram_cftemp.png)

---



### AWS 인프라 구축 가이드 05장 배포과정

#### 인스턴스 생성 후 배포와 버전관리

* 인스턴스 생성 - AMI - launch Template - ASG - 인스턴스 다수 생성 - 로드 밸런스 생성 후 각 인스턴스들에게 골고루 배포
* 배포(deploy) **blue/green**을 통한 버전 업 - 무중단 배포
  * Iv2 - AMI - Launch Template - ASG - 인스턴스 다수 생성 - 기존 로드 밸런스에 추가
  * Iv1 버전과 Iv2 버전 인스턴스에 골고루 배포
  * Iv2 버전이 안정화 되었다고 판단될 시, 로드 밸런스에서 Iv1 버전을 제거 후 Blue그룹인 Iv1 버전을 삭제