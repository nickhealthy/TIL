# Today I Learned

* 리눅스 기반 파일 통신 (SCP)

---



## 리눅스 기반 파일 통신 (SCP)

※ 아마존EC2를 기준으로 작성함

SCP란 *Security Copy*의 약자로 보안이 보장된 복사

* SSH와 동일한 SSL 기반의 세션 보안 프로토콜을 사용
* 포트번호 또한 SSH와 동일한 **22번** 포트를 사용

[참고사이트](https://jojoldu.tistory.com/430)

[참고사이트](https://dearsikandarkhan.medium.com/files-copying-between-aws-ec2-and-local-d07ed205eefa#:~:text=Copying%20files%20from%20local%20to%20EC2&text=To%20copy%20files%20between%20your,Unix%2FLinux%2FMac%20system.)

[참고사이트](https://ourcstory.tistory.com/162)

[참고사이트](https://mentha2.tistory.com/93)

[참고사이트](https://www.sallys.space/blog/2017/11/28/aws-scp/)

[참고사이트](https://twpower.github.io/138-send-file-using-scp-command)

1. AWS - EC2 인스턴스 2개를 각각 생성 (Amazon Linux 이미지를 기준으로 설정함)
   * 첫번째 인스턴스는 전송용(Pub),
   * 두번째 인스턴스는 수신용(Sub)로 칭함
2. SSH-Client 프로그램을 사용하여 접속(putty, bitvise 등)
3. Pub-EC2 인스턴스에 Sub-EC2 인스턴스 `.pem`(키패어-인증키) 를 넣어줌
   * 나중에 `identity_file` 로 이용될 예정
     * 직역시 **정체_파일**로 해석할 수 있는데 개별 EC2에 접근하려면 기본적으로 `.pem`(키패어)가 있어야 접근(접속)이 가능하기 때문에 Sub-EC2 인스턴스에 파일을 보낼 때 명령어에 옵션으로 인증키를 넣어서 **즉시 접속 >> 원하는 파일 보내기**를 하는 것 같음
4. `.pem` 인증키는 보안이 중요시 되기 때문에 `chmod` 명령어로 루트계정에만 할당 (옵션 사항 아님)
   * 해당 파일 권한 설정을 안해주면 `bad permissions` 에러가 발생

```bash
$ chmod 400 file_name.pem
```

5. Sub-EC2 접속 및 프롬프트에 있는 [유저이름@IP주소]를 확인
6. `scp` 프로토콜로 Pub-EC2에서 Sub-EC2로 보내기

* `-i` 옵션 : ssh 키파일과 같은 identity file의 경로를 지정하는 옵션
* `-r` 옵션 : 폴더를 복사하는 경우
* `-P` 옵션 : 포트 지정 (※ 대문자임)

```bash
scp -i newKeypair2.pem -r /home/ec2-user/img/ ec2-user@172.31.82.177:/home/ec2-user/img/
```

* scp[명령어] 
* -i newKeyPair2.pem[SSH키파일] 
* -r [내 파일 폴더 경로] 
* ec2-user@172.31.82.177[5번 항목의 유저이름@IP주소]:(파일을 저장할 경로)



**Usage : scp [옵션] [Source 경로] [Target 경로]**

- 아래 3가지 방법으로 로컬과 원격지 간에 자유로운 파일 전송 가능
- \# 로컬 -> 원격지 
- scp 전송할파일 유저명@네트워크주소(IP주소):목적지경로
- \# 원격지 -> 로컬
- scp 유저명@네트워크주소(IP주소):전송할파일 목적지경로
- \# 원격지 -> 원격지 
- scp 유저명@네트워크주소(IP주소):전송할파일 유저명@네트워크주소:목적지경로

