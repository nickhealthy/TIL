# Today I Learned



* VPC (Virtual private Cloud)
* ※ CIDR (Classless Inter-Domain Routing)
* 서브넷 (subnet)
* ENI (Elastic Network Interface: 탄력적 네트워크 인터페이스)
* 인터넷 게이트웨이 (igw)
* 라우팅
* 보안 그룹
* NACL (Network Access Control List)
* Creating a Basic VPC and Associated Components
* Building a Three-Tier Network VPC from Scratch in AWS

---



### VPC (Virtual private Cloud)

* 네트워크 계층
* EC2 인스턴스를 비롯한 여러 AWS 서비스의 리소스를 담을 수 있는 가상 네트워크
* 한 AWS 리전 안에서만 존재할 수 있고, 한 리전에 만든 VPC는 다른 리전에서는 보이지 않음
* 연속적인 IP 주소 범위로 구성 - **CIDR**블록으로 표시
* 10.0.0.0/8 - 10.0.0.0 ~ 10.255.255.255
  * 172.16.0.0/12 - 172.16.0.0 ~ 172.31.255.255
  * 192.168.0.0/16 - 192.168.0.0 ~ 192.168.255.255
  

---

### ※ CIDR (Classless Inter-Domain Routing)

* 사이더라고 불림
* 클래스 없는 도메인간 라우팅 기법

---



### 서브넷 (subnet)

* VPC 내 논리 컨테이너
* EC2 인스턴스를 배치하는 장소 - 인스턴스는 서브넷 안에 위치
  * 한번 서브넷에 인스턴스를 생성하면 다른 서브넷으로 옮길 수 있음
  * 인스턴스를 종료하고 다른 서브넷에 새 인스턴스를 만들 수는 있음
* 인스턴스를 서로 격리하고, 인스턴스 간의 트래픽 흐름을 제어하고, 인스턴스를 기능별로 묶을 수 있음
* 서브넷 CIRD 블록
  * VPC의 일부, VPC 내에서는 유니크해야 함
  * 모든 서브넷에서 처음 4개의 IP와 마지막 1개는 예약되어 있으므로 인스턴스에 할당할 수 없음
    * 예: 서브넷 CIRD이 172.16.100.0/24인 경우
    * 172.16.100.0 ~ 172.16.100.3
    * 172.16.100.255
* 서브넷은 하나의 가용 영역(Available Zone, AZ) 내에서만 존재할 수 있음

---



### ENI (Elastic Network Interface: 탄력적 네트워크 인터페이스)

* 물리 서버의 네트워크 인터페이스와 같은 기능을 수행

---



### 인터넷 게이트웨이 (igw)

* **퍼블릭 IP 주소**를 할당받은 인스턴스가 인터넷과 연결돼서 요청을 수신할 수 있도록 기능을 제공
* 처음 VPC를 만들면 인터넷 게이트웨이가 연결되어 이지 않으므로, 직접 인터넷 게이트웨이를 만들고 VPC와 연결해야 함
* **하나의 VPC는 하나의 인터넷 게이트웨이만 연결할 수 있음**

---



### 라우팅

* VPC는 소프트웨어 함수로 IP 라우팅을 구현 - 사용자는 **라우팅 테이블만 관리**
* 라우팅 테이블 - 하나 이상의 라우팅과 하나 이상의 서브넷 연결로 구성
* 기본 라우팅 - 인터넷 게이트웨이를 가리키는 라우팅
  * 대상 주소: 0.0.0.0/0
    * 인터넷 상의 모든 호스트 IP를 기반
  * 대상: igw-xxxxxxxx
    * 인터넷 게이트웨이
* 기본 라우팅이 포함된 라우팅 테이블과 연결된 서브넷 - 퍼블릭 서브넷
* 기본 라우팅이 포함된 라우팅 테이블과 연결되지 않은 서브넷 - 프라이빗 서브넷

![1](C:\dev\TIL\2020_10_06\img\1.PNG)

---



### 보안 그룹

* 방화벽과 같은 기능을 제공
* 인스턴스의 ENI에서 송수신하는 트래픽을 허가해서 트래픽을 제어
* 모든 ENI는 최소 한개 이상의 보안 그룹과 연결되야 하고, 보안 그룹은 여러 ENI와 연결될 수 있음
* 생성할 때 보안 그룹 이름, 설명, 포함될 VPC를 지정하고, 생성 후에 인바운드, 아웃바운드 규칙을 지정
  * 트래픽을 허용
* 상태 저장 방화벽 역할 - 보안 그룹이 트래픽을 한 방향으로 전달되도록 허용할 때 반대 방향의 응답 트래픽을 지능적으로 허용 (예: 웹 클라이언트에서 HTTPS로 요청을 허용했다면 요청에 대한 응답도 허용)

---



### NACL (Network Access Control List)

* 보안 그룹과 유사
  * 원본 또는 대상 주소 CIDR, 프로토콜, 포트를 기반으로 트래픽을 **인바운드, 아웃바운드 규칙으로 제어**
    * 방화벽과 같은 역할
  * VPC에 삭제할 수 없는 기본 NACL이 있음
* 서브넷에 연결되어 해당 서브넷과 송수신되는 트래픽을 제어
* 상태 비저장
  * NACL을 통과한 연결 상태를 추적하지 않는다.
  * 모든 인바운드와 아웃바운드 트래픽의 허용 규칙을 별도로 작성해야 함
* 규칙을 적용할 때 규칙 번호의 오름차순으로 처리
* 화이트 리스트 / 블랙리스트
  * 화이트 리스트: 전체에 대해서 막은 후, 필요한 통신 채널들만 열어 놓음
  * 

---



### Creating a Basic VPC and Associated Components

[URL](https://learn.acloud.guru/handson/934b78e6-5327-4ed3-a369-1b60b382722f/course/178db59b-70f1-4bd8-8d74-9ab9263f8f9a)

* `VPC` 가상 라우터와 `Subnet`, `NACL`, `IGW`, `Route Table` 통신 설정

![1.1](C:\dev\TIL\2020_10_06\img\1.1.PNG)

---



### Building a Three-Tier Network VPC from Scratch in AWS

[URL](https://learn.acloud.guru/handson/f2a24706-8b7b-4a21-9ad6-859bd5595215/course/178db59b-70f1-4bd8-8d74-9ab9263f8f9a)

* 퍼블릭으로 외부 네트워크랑 통신을 `IGW`로 연결
* 프라이빗으로 내부적으로 `라우팅테이블`을 통해 서로 연동하고, 필요시 `NAT` 네트워크로 외부로 통신

**6개 서브넷을 생성할 때 사용하는 네이밍 규칙**

서비스구분 + 가용영역 + 내부/외부구분

* 서비스 구분
  * DMZ
  * AppLayer
  * DBLayer
* 가용영역
  1. `-->` us-east-1a
  2. `-->` us-east-1b
* 내부/외부구분
  * public
  * private

![2. lab_diagram_LabDiag_AWS-SysOps_Building_3-tier](C:\dev\TIL\2020_10_06\img\2. lab_diagram_LabDiag_AWS-SysOps_Building_3-tier.png)

![2-1. lab_diagram_LabDiag_AWS-SysOps_Building_3-tier](C:\dev\TIL\2020_10_06\img\2-1. lab_diagram_LabDiag_AWS-SysOps_Building_3-tier.png)

---



### Troubleshooting AWS Network Connectivity: Security Groups and NACLs

[URL](https://learn.acloud.guru/course/178db59b-70f1-4bd8-8d74-9ab9263f8f9a/learn/57ffccd1-7969-4bdf-a4fd-1024f0bb0a28/e58d4249-e494-400a-9bd0-ef2846a5503c/lab/cffb7f13-1c46-45cb-886a-f0bb12ff038c)

* 학습 목표
  * 인스턴스가 인터넷에 연결할 수없는 이유 확인
  * 인스턴스가 인터넷에 연결하지 못하게하는 두 번째 문제 식별

---



### Create a Linux EC2 Instance in AWS and Connect Using SSH

[URL](https://learn.acloud.guru/handson/7819103c-5e59-4179-b635-9fc284e46c5a/course/178db59b-70f1-4bd8-8d74-9ab9263f8f9a)





---

### 엘리스 코딩 강좌

* 자료구조: 데이터의 흐름과 저장

* 알고리즘 : 시간과 공간의 효율성을 고려해서 만든 어떤 프로그램을 실행하는데 필요한 계산 절차



* 시간복잡도: 코드가 얼마나 빠르게 동작하는가
  * 시간 복잡도 ↑ : 코드는 느리게
  * 시간 복잡도 ↓ : 코드가 빠르게
* 공간 복잡도: 얼마나 많은 메모리를 사용하는가
  * 공간 복잡도 ↑ : 메모리 많이
  * 공간 복잡도 ↓ : 메모리 적게



#### 시간 복잡도(Time-complexify)

* 알고리즘에 사용되는 **총 연산횟수 (+, -, *, / = == <> 등)**

※ **실행시간이 아니라** 알고리즘에 사용하는 총 연산 횟수 

* 시간 복잡도 맛보기

```python
sum = 0 # 1회
for i in [1,2,3,4]: 4회
    sum += i; 
# 총 5회

randomNumber = 0 # 1회
nums = [1, 2, 3, 4] # 1회
for i in range(len(nums)):
	for j in range(len(nums)):
		randomNumber += nums[i] * num [j] # 16회
# 총 18회
```

* N에 대한 복잡도

```python
# 시간 복잡도 = 1
def doNothing(nums): 
	return nums # 연산횟수 1
```

```python
def doSomething(nums):
	sum = 0 # 1회
	for num in nums:
		sum += num	# n회
	return sum # 1회
	
# 총 N + 2
```



#### Big-O 시간 복잡도란?

* **Big-O = 시간복잡도 함수의 가장 높은 차수**
* aN + b = O(N)
* aNlogN + b = O(NlogN)
* aN^2 + bN +C = O(N^2)



#### Big-O 시간 복잡도 계산법칙 

* #### for / While loop가 한 번 중첩될 때마다 O(n)

```python
for num in nums: # O(n)

for i in range(len(nums)): # O(n*n) 
	for j in range(len(nums)): # O(n^2)
```

* #### 자료구조 사용, 다른 함수 호출에는 각각의 O(n)을 파악

```python
nums = [1, 2, 3, 4, 5, 6] # O(n)
nums = {1, 2, 3, 4, 5, 6} # set / O(1)
nums.sort() # O(N*logN)
```

* #### 매번 절반씩 입력값이 줄어들면 O(logN)

```python
1,2,3,5,8,6,0,4
# 1회
8,6,0,4
# 2회
8,6
# 3회
6

# 이진 탐색
N = 8
실행 횟수 = log(8) = 3
```



#### 공간 복잡도 (Space-com)

* 알고리즘에 사용되는 메모리 공간의 총량
* Big-O 공간 복잡도 = 

```python
a = 1 # O(1)

a = [num for num in nums] # O(n)

a = [[num for num in nums] for num in nums] # O(n^2)
```

---



### 네트워크 책

해킹 입문자를 위한 TCP/IP 이론과 보안 2/e

쉽게 배우는 데이터 통신과 컴퓨터 네트워크