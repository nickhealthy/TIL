# SSH, 런레벨, 마운트, 네트워크 설정



* 서버와 클라이언트 연결하기(SSH)
* 리눅스 기본 명령어 (런레벨)
* 프롬프트 설명
* 명령어 옵션을 사용하는 방법
* 마운트
* 가상머신의 특정 디텍토리를 ISO 파일로 제작(p135)
* 네트워크 (위치: */etc/netplan*)
* 기본 명령어



### 서버와 클라이언트 연결하기

* 스니핑: 두개의 호스트 사이에 통신하는 데이터를 훔쳐보는 것 

* 통신 채널 데이터를 암호화 - 프로토콜 자체가 암호화

  ​	※ 보안 프로토콜 중 하나: SSH, SSL/TLS 등

  

* 사용자 전환

  ```bash
  sudo su : 루트 계정 전환
  su ubuntu : 우분투 계정 전환
  ```



* *cmd*와 [*Bitvise*](https://www.bitvise.com/ssh-client-download) 창에서 SSH 접속

  ​	※ ssh ubuntu(사용자이름)@192.168.35.169(호스트pc_ip) -p(포트번호) 22

  ​	※ 사용자를 지정하지 않으면 호스트 PC의 사용자로 접속

  ​	※ ubuntu@server << 우분투 데스크탑 서버 버전으로 접속된 상태

  ​	※  SSH default 포트는 22번



### 리눅스 기본 명령어 (런레벨)

* 런레벨 정보 확인

```bash
ls -al /lib/systemd/system/runlevel?.target
```

* 런레벨 기본 세팅 확인

```bash
ls -al /lib/systemd/system/default.target
```

* 런레벨 default.target을 변경

```bash
ln -sf /lib/systemd/system/multi-user.target /lib/systemd/system/default.target 

다시 확인: ls -al /lib/systemd/system/default.target 

로그인 후 startx 명령어를 이용해서 x Window 환경을 이용
```

* 원상 복구

```bash
ln -sf /lib/systemd/system/graphical.target /lib/systemd/system/default.target
```



### 프롬프트 설명

사용자를 구분 ⇒ $: 일반사용자, #: 루트사용자

```bash
ubuntu@server:~$ ls -al /lib/systemd/system/default.target 

ubuntu : 로그인한 계정
server : 호스트명/컴퓨터 이름
~$ : 현재 명령어를 입력하고 있는 위치(디렉토리)
	※ ~ ⇒ 로그인한 계정(사용자)의 홈 디렉터리 ⇒ root → /root, 일반사용자 → /home/계정명

ls -al /lib/systemd/system/default.target : 명령어 ⇒ 명령어 [서브명령어] [옵션] [파라미터]
명령어의 옵션 설명서 
	방법 1. --help: 옵션 명령어 + --help 
	방법 2. man ls
```



### 명령어 옵션을 사용하는 방법

* -- 영문자 또는 --단어 => -영문자 방식은 여러개의 옵션을 조합해서 사용할 때

```bash
1. ubuntu@server:~$ ls -a
2. ubuntu@server:~$ ls --all
```

* -영문자영문자 형식으로 조합된 경우 ⇒ -영문자 -영문자 옵션의 결합

```bash
ubuntu@server:~$ ls -al	⇐ -a 옵션과 -l 옵션을 결합 ⇒ ls -a -l 과 동일
```



### 마운트

​	※ 리눅스에서 하드디스크의 파티션, CD/DVD, USB, 메모리 등을 사용하려면 지정한 위치에 연결 해줘야한다.

​	※ 물리적인 장치를 특정한 위치(대개는 디렉토리)에 연결하는 과정

* 플러그앤 플레이: 새롭게 추가된 하드웨어를 감지해주는 동작

```bash
1. cd-rom을 마운트할 디렉토리 생성
root@server-b:~# mkdir -p /mnt/cdrom	⇐ mkdir -p : 디렉토리를 순차적으로 생성하는 옵션(-p)

2. 마운트
root@server-b:~# mount /dev/cdrom /mnt/cdrom	⇐ /dev/cdrom 디바이스를 /mnt/cdrom 디렉터리에 연결
root@server-b:~# cd /mnt/cdrom			⇐ 마운트된 디렉터리로 접근해서 디바이스를 이용
root@server-b:/mnt/cdrom# ./VBoxLinuxAdditions.run --nox11	⇐ VirtualBox 확장 프로그램 실행
root@server-b:/mnt/cdrom# mount ⇐ 정보확인

3. 언마운트
root@server-b:/mnt/cdrom# umount /mnt/cdrom		
```



### 가상머신의 특정 디텍토리를 ISO 파일로 제작(p135)

1. genisoimage 프로그램 설치 여부

   ```bash
   root@server-b:/# dpkg --get-selections genisoimage
   ```

2. genisoimage 설치

   ```bash
   root@server-b:/# apt install genisoimag
   ```

3. /boot디렉토리의 모든 파일을 boot.iso 파일로 생성

   ```
   root@server-b:/# genisoimage -r -J -o boot.iso /boot
   ```

4. 마운트 디렉토리 생성

   ```bash
   root@server-b:/# mkdir -p /mnt/iso
   ```

5. 마운트

   ```bash
   root@server-b:/# mount -o loop boot.iso /mnt/iso
   ```

6.  /bin 디렉터리와 /mnt/iso 디렉터리를 비교

   ```bash
   root@server-b:/# ls -l /mnt/iso
   
   root@server-b:/# ls -l /boot
   ```

7. umount

   ```bash
   root@server-b:/# umount /mnt/iso
   ```

   

### 네트워크 (위치: */etc/netplan*)

* IP 정보를 확인

```bash
ifconfig

inet << ip 주소
netmask << 서브넷 마스크
```

* 가상 라우터(게이트웨이) 주소 확인하기

```bash
ip route

default via << 게이트웨이 주소
```

* 현재 설정된 DNS 정보 확인하기

```bash
systemd-resolve --status enp0s3

DNS Servers: 주버전
             보조버전
```



* `TCP / IP` : 통신의 전송 및 수신을 다루는 TCP(Transmission Control Protocol)와 데이터 통신을 다루는 IP(Internet Protocol)로 구성되어 있음

* OSI 7 layer : 국제표준화기구(ISO)에서 만든 프로토콜 규약

* 호스트 PC : cookbook / 도메인 : hanbit

  ​	※ 전체이름(FQDN: Fully Qualified Domain Name)은 cookbook.hanbit.co.kr

* 브로드캐스트 주소 : 내부 네트워크의 모든 컴퓨터가 수신하는 주소

  ​	※ 마지막 번호를 *255*로 변경

  ​	※ 해당 네트워크를 사용하는 모든 PC에게 정보를 전달

* 넷마스크: 넷마크로 네트워크의 규모가 결정됨

* DNS 서버 주소: URL을 해당 컴퓨터의 IP주소로 변환하는 기술

  ```bash
  nslookup
  
  설정 파일 주소: /etc/resolv.conf
  내용 중에 nameserver DNS서버IP 형식으로 설정되어 있음
  ```

* `DHCP`: 자동으로 IP주소나 서브넷 마스크등을 부여해준다.

* 고정 IP주소 변경(desktop)

  ```bash
  root@server:~# nm-connection-editor
  
  방식 : 수동 => DHCP를 사용하지 않음
  주소: 10.0.2.100 => VirtualBox의 NatNetwork에서 부여할 수 있는 IP 범위내의 값을 사용
  넷마스크: 255.255.255.0
  게이트웨이: 10.0.2.1
  DNS서버: 8.8.8.8 => 구글에서 제공하는 DNS 서버 주소
  ```

* 고정 IP주소 변경(server)

  ```bash
  ubuntu@server-b:~$ sudo su
  [sudo] password for ubuntu: ubuntu
  
  root@server-b:/home/ubuntu# cd
  
  네트워크 설정 파일을 확인
  root@server-b:~# ls /etc/netplan/50-cloud-init.yaml
  
  백업 파일 생성
  root@server-b:~# cp /etc/netplan/50-cloud-init.yaml /etc/netplan/50-cloud-init.yaml.bak	
  
  네트워크 설정 파일을 편집
  root@server-b:~# vi /etc/netplan/50-cloud-init.yaml  
  
  network:
      ethernets:
          enp0s3:
              dhcp4: false << false로 설정
              addresses: [10.0.2.200/24] << IP주소 설정
              gateway4: 10.0.2.1 << 게이트웨이 설정
              nameservers: << 구글에서 제공하는 DNS 서버 주소
                  addresses: [8.8.8.8]
      version: 2
  
  변경사항 적용
  netplan apply
  ```



### 기본 명령어

* 파이프: 두 프로그램을 연결하는 연결 통로를 의미

  ```
  ex) ls -l /etc|less
  ```

* 필터: 필요한 것만 걸러주는 명령

  ```
  1.grep
  ps -ef | grep bash << 현재 내가 작업중인 전체 목록중에 bash만 필터링 하겠다.
  ```

* 리디렉션: 표준 입출력의 방향을 바꾸는 것

  ​	※ `>` 표준 입력은 키보드이고 표준 출력은 화면이지만, 이를 파일로 처리하고 싶을때 주로 사용

  ```bash
  ls -l > list.txt << ls -l의 결과를 화면에 출력하지 않고 list.txt 파일에 저장(파일이 있으면 덮어씀)
  ls -l >> list.txt << ls -l의 결과를 화면에 출력하지 않고 list.txt 파일에 저장(append)
  
  
  sort < list.txt << list 파일을 정렬하여 화면에 출력
  sort <list.txt>out.txt 파일을 정렬하여 파일 저장
  ```

  

