# Today I Learned



* 알고리즘 - [programmers]_모의고사
  * `from itertools import cycle` 사용하기

* [programmers]\_서울에서_김서방찾기
  * `index()`를 이용해 위치 `list`안에서 위치찾기

* DevStack 설치

---



### 알고리즘 - [programmers]_모의고사

* **`from itertools import cycle` 사용하기**
  * 문제에서 주어진 배열의 길이가 얼만큼인지 몰랐고, 각각의 정의한 배열은 길이가 정해져있었음
  * `zip()` 함수와 `cycle(정의된 배열)`  를 이용해 문제에서 주어진 배열과 매핑
  * 결과 : 문제에서 주어진 배열이 끝날 때까지 정의된 배열도 순차적으로 반복을 계속하였음

```python
from itertools import cycle

answer = [1,2,3,4,5,6,7,8,9]
a1 = [1,2,3,4,5]
a2 = [2,1,2,3,2,4,2,5]
a3 = [3,3,1,1,2,2,4,4,5,5]

for i, j, k, z in zip(cycle(a1), cycle(a2), cycle(a3), answers):
        if i == z : correct[0] += 1
        if j == z : correct[1] += 1
        if k == z : correct[2] += 1

print(correct)            
# [2,2,2]         
```

---



### [programmers]\_서울에서_김서방찾기

* `index()`를 이용해 위치 `list`안에서 위치찾기

---



### DevStack 설치

[참고 URL](https://myanjini.tistory.com/entry/DevStack-%EC%84%A4%EC%B9%98)

1. **호스트 네트워크 설정**
   * IPv4 주소 : 192.168.56.103
   * IPv4 서브넷 마스크: 255.255.255.0
2. **가상머신 새로 만들기**

```bashdvs
이름 : openstack
머신폴더 VirtualBox VMs
종류 : 리눅스
버전: 우분투

메모리 8G(8192MB)
새 가상 하드 디스크 : VDI(VirtualBox 디스크 이미지) - 동적할당 : 20기가 설정
시스템 설정 창: 프로세서 cpu코어 수 4개 / PAE/NX 사용하기
네트워크 설정
- 어댑터 1 : 호스트 전용 어댑터 / 무작위 모드 : 모두 허용
- 어댑터 2: 어댑터에 브리지 / 무작위 모드 : 모두 허용
- 어댑터 3: NAT 설정
우분투 데스크탑 이미지 Version : Ubuntu 18.04.5 LTS -DESKTOP 버전으로 이미지 다운
```

3. **DevStack 설치 및 설정**

```bash
# 루트 계정 전환
sudo su
cd

# 네트워크 설정 및 확인
vi /etc/netplan/01-network-manager-all.yaml
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    enp0s3:
      addresses:
      - 192.168.56.103/24
      nameservers: {}
    enp0s8:
      addresses:
      - 192.168.0.22/24
      nameservers: {}
    enp0s9:
      dhcp4: true
      
netplan apply

ifconfig  or ip a
```

4. **방화벽 비활성화**

```bash
systemctl stop ufw
systemctl disable ufw
```

5. **기본 패키지 설치**

```bash
sed -i 's/kr.archive.ubuntu.com/mirror.kakao.com/g' /etc/apt/sources.list
sed -i 's/security.ubuntu.com/mirror.kakao.com/g' /etc/apt/sources.list

apt update
apt upgrade

apt install -y vim
apt install -y git

apt install -y python3-dev
apt install -y python3-pip
```

6. **stack 계정 생성 및 전환**

```bash
useradd -s /bin/bash -d /opt/stack -m stack
echo "stack ALL=(ALL) NOPASSWD:ALL" | tee /etc/sudoers.d/stack
su stack
cd
```

7. **DevStack 쉘 스크립트 가져오기**

```bash
git clone https://git.openstack.org/openstack-dev/devstack -b stable/stein
```

8. **local.conf 파일 작성**

```bash
cd devstack/
vim ./local.conf

[[local|localrc]]
ADMIN_PASSWORD = devstack
DATABASE_PASSWORD = $(ADMIN_PASSWORD)
RABBIT_PASSWORD = $(ADMIN_PASSWORD)
SERVICE_PASSWORD = $(ADMIN_PASSWORD)

HOST_IP = 192.168.56.103
SERVICE_HOST = 192.168.56.103
MYSQL_HOST = 192.168.56.103
RABBIT_HOST = 192.168.56.103
GLANCE_HOSTPORT = 192.168.56.103:9292

# nova-network 비활성화
disable_service n-net

# Neutron 활성화
ENABLED_SERVICES+=,q-svc,q-dhcp,q-meta,q-apt,q-l3

## Neutron network 네트워크 옵션
Q_USE_SECGROUP = True
FLOATING_RANGE = "192.168.0.0/24"
FIXED_RANGE = "10.0.0.0/24"
Q_FLOATING_ALLOCATION_POOL=start=192.168.0.100,end=192.168.0.200
PUBLIC_NETWORK_GATEWAY = "192.168.0.1"
PUBLIC_INTERFACE = enp0s8

# OpenSwitch 프로바이더 네트워킹 환경설정
Q_USE_PROVIDERNET_FOR_PUBLIC = True
OVS_PHYSICAL_BRIDGE = br-enp0s8
PUBLIC_BRIDGE = br-enp0s8
OVS_BRIDGE_MAPPINGS = public:br-enp0s8
```

**※ 가상머신 스냅샷 생성**

9. **stack.sh 실행 및 DevStack 설치 확인**

```bash
# stack.sh 실행
./stack.sh

# DevStack 설치 확인
source userrc_early << 관리자 인증 정보를 다시 읽어들임
openstack user list
openstack service list
openstack endpoint list
openstack image list
```

---

