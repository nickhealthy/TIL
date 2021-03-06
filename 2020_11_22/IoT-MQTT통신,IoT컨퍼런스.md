# Today I Learned

* AWS IoT 디바이스 연결 및 MQTT 통신
* AWS IoT 컨퍼런스

---



## AWS IoT 디바이스 연결 및 MQTT 통신

#### 사물 설정

1. AWS IoT-Core 접속 - 사물 - 사물생성 - 단일 사물 생성 클릭
2. 이름설정 ex) MQTT  - 다음 버튼
3. 사물에 인증서 추가 - 원클릭 인증서 생성 - 인증서 생성 클릭 
4. 인증서 3개 모두 다운로드 + CA 다운로드

#### 정책 설정

1. 이름 설정 : mqtt - policy 
2. 작업 설정 : Iot*
3. 리소스 설정 : *
4. 생성

#### 정책연결

1. 인증서 탭에가서 해당 정책을 설정

#### 사물 - 상호작용 탭

HTTPS 엔드포인트 주소가 실제로 MQTT를 이어주는 주소



# AWS IoT 컨퍼런스

1. IoT 세상에서는 기계가 데이터를 생성하고,

2. 상호작용이 지속적으로 일어나며, 

3. 세상에 대한 전체적인 시각을 가질 수 있음



## IoT 서비스 구현에 필요한 요소들

* 메시지 브로커
* 룰 엔진
* Thing Shadow
* 디바이스 등록



## AWS IoT에서 제공하는 기능들

* DEVICE GATEWAY
  * **MQTT 또는 HTTP 통신을 이용**해서 Thing과의 커뮤니케이션
* DEVICE SDK
  * 디바이스를 디바이스 게이트웨이와 붙이기 위해서는 규약된 프로토콜에 근거해서 코딩해야함
  * **디바이스 SDK**를 이용해서 연결, 인증, 메시지 교환을 위한 클라이언트 라이브러리
    * 손쉽게 디바이스와 클라우드를 연결할 수 있음
* 인증(보안)
  * 디바이스에 대한 인증을 강력하게 해야함
  * 인증서 기반의 상호인증 및 암호화
* RULES ENGINE
  * 어떠한 규칙 기반으로 메시지 변환 및 AWS 서비스로 전달
    * 데이터를 분류 및 처리
* SHADOW - 네트워크 불안정 등
  * Thing 상태를 영구 저장 (불안정한 연결을 대비해서 사용)
  * 디바이스가 오프라인 일지라도 마지막 상태를 알아내거나, 다음 상태를 변경할 수 있는 것이 필요함



## AWS IoT는 AWS 서비스로의 Front Door

많은 데이터들을 디바이스로부터 들어오면 그것들을 다양한 AWS 서비스 or 제3의 서비스들과 연동해서 의미있는 일들을 해야하는데 그것을 가능하게 하는 Front Door 역할을 한다고 할 수 있음

![AWS 서비스로의 Front Door](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/AWS%20%EC%84%9C%EB%B9%84%EC%8A%A4%EB%A1%9C%EC%9D%98%20Front%20Door.PNG)



## AWS IoT Rule 엔진을 통한 디바이스 데이터 활용

#### IoT 데이터를 활용해서 하고 싶은 것들

* 디바이스 데이터에서 가치있는 정보 추출
* 수집된 데이터를 기반으로 머신러닝 예측 등

1. 데이터에서 유용한 정보를 뽑아야 함
2. 디바이스에서 올라오는 로그 데이터를 활용해서 어떤 예방 정비 및 예측을 할 수 있음



#### 데이터에서 가치를 찾아내기 위해서...

* 특정 기준에 대해서 메시지를 **필터링**
* 메시지 내용 **변환**
* 메시지 내용데 따른 **반응**
* 메시지를 다른 **topic**들로 옮기기
* 메시지를 다른 **시스템**들로 옮기기
* 트랜드 기반의 변화 **예측**

등등이 있음



#### RULES ENGINES 기반으로 가치있는 데이터 활용이 가능

**규칙 기반으로 메시지 변환 및 AWS 서비스로 전달**

* SQL 문장으로 토픽 필터 정의
* (옵션) WHERE절로 조건 사용 가능
* 향상된 JSON 지원

```sqlite
// FROM 뒤에가 토픽이름
SELECT * FROM 'things/thing-2/color'
WHERE color = 'red'
```

#### 시그널 : 노이즈 비율을 개선하기 위한 함수들 - 데이터를 바로 사용할 수 없을 때

* String 변환 (정규식 지원)
* 수학 연산
* Context 기반의 지원 함수들
* 암호 지원
* UUID, TimeStamp, rand 등

#### AWS IoT Rules Engine 특징 몇가지

* 복잡한 구성 가능
  * 하나의 대상이 아닌 복수에 적용됨. 다양한 함수들을 제공
* 다중 및 동시에 수행될 수 있는 Action들
  * 어떤 경우에는 하나의 메시지에 대해서 여러 action을 구현할 필요가 있음
  * ex) 레드면 그린으로 바꿔줘라 + 블루면 람다를 실행해줘라



## AWS IoT Rules Engine Action

#### Rules Action은 AWS IoT를 외부 Endpoint들과 AWS 서비스를 연결하는 역할을 함

#### 직접 연동되는 AWS Service

![Rules Engine Action과 연동되는 AWS 서비스들](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/Rules%20Engine%20Action%EA%B3%BC%20%EC%97%B0%EB%8F%99%EB%90%98%EB%8A%94%20AWS%20%EC%84%9C%EB%B9%84%EC%8A%A4%EB%93%A4.PNG)

1. Amazon S3 : S3 버킷에 객체를 저장
2. Amazon DynamoDB : 테이블에 insert 및 update
3. Amazon Kinesis : 키네시스 스트림에 값을 Publish / 키네시스 Firehose에 값을 Publish해서 S3에 버킷에 대량으로 저장 가능
4. Amazon SNS : topic 또는 endpoint에 Publish(발행)
5. AWS Lambda : 람다 함수 호출 가능
6. Amazon SQS : SQS queue에 쓰기
7. AWS IoT로 Republish : 다른 토픽으로 Re-publish / 다양한 룰 체인 가능 
8. CloudWatch : metric 생성 또는 알람 변경
9. Elasticserach : Elasticserach로 Publish

#### 간접 연동되는 AWS 서비스

(Kinesis, Lambda, S3 등을 통해서 연동)

1. Amazon RDS
2. Amazon Glacier
3. Amazon EC2
4. Amazon Redshift

※ **써드파티 API를 호출할 땐 외부 Endpoints 이용 (Lambda 및 SNS를 통해서 연동)**



## Connected Car 구현 아키텍처 - 음성인식 및 실시간 위치 파악

![온라인 컨퍼런스 - 커넥티드 카](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/%EC%98%A8%EB%9D%BC%EC%9D%B8%20%EC%BB%A8%ED%8D%BC%EB%9F%B0%EC%8A%A4%20-%20%EC%BB%A4%EB%84%A5%ED%8B%B0%EB%93%9C%20%EC%B9%B4.PNG)



## Connected 트럭 - 이동경로 시각화

![온라인 컨퍼런스 - 커넥티드 트럭시각화](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/%EC%98%A8%EB%9D%BC%EC%9D%B8%20%EC%BB%A8%ED%8D%BC%EB%9F%B0%EC%8A%A4%20-%20%EC%BB%A4%EB%84%A5%ED%8B%B0%EB%93%9C%20%ED%8A%B8%EB%9F%AD%EC%8B%9C%EA%B0%81%ED%99%94.PNG)



## Amazon Machine Learning 예측 함수 - 예방정비를 위한 예측 방법

#### 예측을 잘해야 되는 이유

적절한 균형은 **리스크**와 **비용**에 대한 비율과 관련됨

* 고장 예측을 너무 빨리하면
  * 교체가 필요하지 않은 부품을 교체
  * 손실 발생
* 예측을 하지 않으면
  * 더 큰 손실을 입을 리스크

![AWS IoT에서 메시지 기반의 예측 구현 방법](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/AWS%20IoT%EC%97%90%EC%84%9C%20%EB%A9%94%EC%8B%9C%EC%A7%80%20%EA%B8%B0%EB%B0%98%EC%9D%98%20%EC%98%88%EC%B8%A1%20%EA%B5%AC%ED%98%84%20%EB%B0%A9%EB%B2%95.PNG)

#### 예측 모델 생성

1. 트레이닝 데이터를 모아서 S3에 데이터를 쌓는다.
2. 내가 원하는 예측하는 확률로 예측 모델을 생성

#### 실시간 예측 및 대응

1. IoT Rule 엔진에서 예측 함수를 제공

![예측 기반의 예방 기능 구현 예](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/%EC%98%88%EC%B8%A1%20%EA%B8%B0%EB%B0%98%EC%9D%98%20%EC%98%88%EB%B0%A9%20%EA%B8%B0%EB%8A%A5%20%EA%B5%AC%ED%98%84%20%EC%98%88.PNG)

1. 아마존 머신러닝으로 예측 모델 생성
2. Thing에서 여러 메트릭 정보들을 AWS - IoT에 올림
3. 아마존 머신러닝에서 만든 예측 모델을 사용해서 예측을 한 결과를 다른 IoT 토픽으로 Re-pub 또는,

3. CloudWatch metric에 저장해서 시각화한 후, 직접 관리자에게 제공해 관리자가 판단하는 시스템

![AWS IoT에서 메시지 기반의 예측 구현 방법예제](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/AWS%20IoT%EC%97%90%EC%84%9C%20%EB%A9%94%EC%8B%9C%EC%A7%80%20%EA%B8%B0%EB%B0%98%EC%9D%98%20%EC%98%88%EC%B8%A1%20%EA%B5%AC%ED%98%84%20%EB%B0%A9%EB%B2%95%EC%98%88%EC%A0%9C.PNG)

1. 드론이 망가질걸 미리 예측해서 다시 원래 자리로 돌아오는 시스템
2. 좋은 예측을 위해선 아마존 머신러닝 사용 - IoT와 동기화
3. 각종 텔레메트릭 데이터들을 대시보드화 하는 것이 필요 - 다이나모 디비에 저장해서 대시보드화까지 진행

※ IoT Rule 엔진을 활용한 것

#### Dynamo DB와 Elasticsearch를 이용한 시각화

![다이나모DB-엘라스틱서치를 통한 시각화](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/%EB%8B%A4%EC%9D%B4%EB%82%98%EB%AA%A8DB-%EC%97%98%EB%9D%BC%EC%8A%A4%ED%8B%B1%EC%84%9C%EC%B9%98%EB%A5%BC%20%ED%86%B5%ED%95%9C%20%EC%8B%9C%EA%B0%81%ED%99%94.PNG)





## 디바이스 데이터 특징에 따른 접근 방법

#### 데이터 온도에 따라 다른 접근이 필요함

* 실시간성으로 이용해야 하는 데이터
* 저장을 한 다음에 빅데이터로 만들어서 분석을 해야하는 데이터
* 가지고 있다가 문제가 있을 때 찾아봐야 하는 데이터 등등..

![데이터 온도에 따라 다른 접근이 필요](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EC%98%A8%EB%8F%84%EC%97%90%20%EB%94%B0%EB%9D%BC%20%EB%8B%A4%EB%A5%B8%20%EC%A0%91%EA%B7%BC%EC%9D%B4%20%ED%95%84%EC%9A%94.PNG)

![데이터 온도에 따라 다른 접근이 필요2](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EC%98%A8%EB%8F%84%EC%97%90%20%EB%94%B0%EB%9D%BC%20%EB%8B%A4%EB%A5%B8%20%EC%A0%91%EA%B7%BC%EC%9D%B4%20%ED%95%84%EC%9A%942.PNG)

* 데이터 성격에 따라서 활용할 수 있는 AWS 서비스가 달라질 수 있음



## 로컬 IoT, AWS Greengrass

이때까지의 모든 IoT는 네트워크를 통한 데이터 전송를 클라우드에 얘기했지만, 

실상은 모든 디바이스가 인터넷을 사용하고 있지 않은 경우도 많음

* 예시 : 의료 장치, 산업용 기계, 극한의 환경 등

#### 왜 모든 데이터를 클라우드에 사용하지 않는가

* 물리 법칙 : 
* 경제성에 대한 법칙 : 사용량에 따라 과금 / 수많은 데이터를 1차적으로 어그리게이션 한 후 클라우드에 올려서 2차 분석을 하는 경우도 있음
* 위치에 따른 법칙 : 데이터가 못 올라가거나, 프라이버시 데이터 같은 경우에는 클라우드에 올리기가 힘듬

※ 이러한 이유들로 로컬 IoT는 여전히 필요함

![왜 모든 데이터를 클라우드에 사용하지 않는가](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/%EC%99%9C%20%EB%AA%A8%EB%93%A0%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%A5%BC%20%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C%EC%97%90%20%EC%82%AC%EC%9A%A9%ED%95%98%EC%A7%80%20%EC%95%8A%EB%8A%94%EA%B0%80.PNG)



#### IoT를 구성하는 3가지 축

![IoT를 구성하는 3가지 축](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/IoT%EB%A5%BC%20%EA%B5%AC%EC%84%B1%ED%95%98%EB%8A%94%203%EA%B0%80%EC%A7%80%20%EC%B6%95.PNG)

* Things : 데이터를 가져(센싱)오고 액션을 취함
* Cloud : 저장소 기능과 컴퓨팅 시스템을 제공함
* Intelligence : 인싸이트와 로직을 통해 어떤 액션을 도출해야 함

#### 클라우드에서 시작한 IoT

![클라우드에서 시작한 IoT](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C%EC%97%90%EC%84%9C%20%EC%8B%9C%EC%9E%91%ED%95%9C%20IoT.PNG)

* 중간에 있는 AWS IoT가 스토리지와 컴퓨팅을 담당

#### 엣지로 확장

![엣지로 확장](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/%EC%97%A3%EC%A7%80%EB%A1%9C%20%ED%99%95%EC%9E%A5.PNG)

* **로컬 안에서** 디바이스 게이트웨이 
* 람다로 수행할 수 있는 무엇이 있고,
* 디바이스 스테이츠를 저장할 수 있는 어떤 스토리지가 모두 로컬에 있는 것이 **AWS Greengrass**

![AWS Greengrass 장점](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/AWS%20Greengrass%20%EC%9E%A5%EC%A0%90.PNG)

* 람다 베이스의 컴퓨팅을 제공 - 디바이스 프로그래밍 단순화 가능

![AWS Greengrass 기능](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/AWS%20Greengrass%20%EA%B8%B0%EB%8A%A5.PNG)

* 4가지 기능을 가진 소프트웨어를 제공해줌
* 디바이스에 이 소프트웨어를 설치해서, 로컬 IoT를 꾸미고 여전히 AWS IoT와는 연동이 되도록 할 수 있음

![AWS Greengrass 활용 예](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/AWS%20Greengrass%20%ED%99%9C%EC%9A%A9%20%EC%98%88.PNG)

#### 이점들

1. 빨리 반응할 수 있음
2. 운용 비용을 절감할 수 있음
3. 인터넷이 끊어져있어도 컨맨딩 컨트롤 가능

![AWS Greengrass 활용 예2](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/AWS%20Greengrass%20%ED%99%9C%EC%9A%A9%20%EC%98%882.PNG)

현장에 수많은 센서들이 있는데 모두 인터넷 커넥션 기능을 가지고 있지 않음

* AWS Greengrass core를 이용해 수집된 데이터를 내부에서 처리 또는,
* 다양한 AWS 서비스들을 활용해야 하는 데이터면 AWS-IoT에 데이터를 보내 활용이 가능

![AWS Greengrass 활용 예3](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/AWS%20Greengrass%20%ED%99%9C%EC%9A%A9%20%EC%98%883.PNG)

상태 정보 저장 및 AWS-IoT 연동 부분

* 컴퓨트 부분은 로컬 람다로 처리
* 스토리지 서비스는 로컬 쉐도우로 스토리지를 활용 ex) 센서 데이터들이 올라왔을 때 최대 값을 저장하고 싶을 때 / 클라우드에 언제 마지막 메시지를 보냈는지 저장하고 싶을 때 >> 쉐도우를 사용



![마무리 결론](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/%EB%A7%88%EB%AC%B4%EB%A6%AC%20%EA%B2%B0%EB%A1%A0.PNG)

#### 참고 사이트

![참고 사이트](https://github.com/nickhealthy/TIL/blob/master/2020_11_22/img/%EC%B0%B8%EA%B3%A0%20%EC%82%AC%EC%9D%B4%ED%8A%B8.PNG)




