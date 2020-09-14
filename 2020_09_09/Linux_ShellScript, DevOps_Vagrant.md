# Today I Learned

* if문
* case 문
* and, or 관계 연산자
* for ~ in문
* while
* break, continue, exit, return
* eval (명령문인식)
* export(전역 변수)
* set
* shift
* crontab
* DevOps / 개발과 운용
* Vagrant를 이용해서 웹 서버가 설치된 가상 머신을 배포 (P66) [URL](https://www.vagrantup.com/docs/provisioning)

---



### and, or 관계 연산자

* and = `-a` 또는 `&&` 
* or = `-o` 또는 `||`
* `테스트문[]`

---



### for ~ in문

* 방법2.`seq`(시작 증가 끝) : 몇번 반복할지 설정
* 방법3.`{시작..끝..증가}`: 몇번 반복할지 설정 (bash에서만 가능)
* 방법4.`for((i = 0; i < 100; i++))`: 이것도 가능 (bash에서만 가능)

```bash
방법 1.
#!/bin/sh

hap=0
for i in 1 2 3 4 5 6 7 8 9 10
do
	echo $i
	hap=`expr $hap + $i`
done
echo "1부터 10까지의 합은 " $hap
exit 0


방법2.
#!/bin/sh

hap=0
for i in $(seq 1 100)
do
	hap=`expr $hap + $i`
done
echo "1부터 100까지의 합은 " $hap
exit 0


방법3. << 이 경우 `bash sigma.sh` 로 실행
#!/bin/sh

hap=0
for i in {1..100}
do
	hap=`expr $hap + $i`
done
echo "1부터 100까지의 합은 " $hap
exit 0


방법4.
for (( i = 1 ; i <= 100 ; i ++ ))
do
	hap=`expr $hap + $i`
done
echo "1부터 100까지의 합은 " $hap
exit 0
```



* Quiz. 1부터 10까지 숫자 중 짝수의 합을 구하시오.

```bash
#!/bin/sh

hap=0
for i in 2 4 6 8 10            ⇐ 짝수만 나열
#for i in $(seq 2 2 10)        ⇐ seq 시작 증가치 끝
#for i in {2..10..2}           ⇐ {시작..끝..증가치}
#for (( i = 2 ; i <= 10 ; i += 2 ))
do
	hap=`expr $hap + $i`
done
echo "1부터 10까지의 짝수 합은 " $hap
exit 0
```



* Quiz.아래와 같은 형식으로 구구단을 출력하시오.

  2 x 1 = 2	3 x 1 = 3	.. 	9 x 1 = 9

    : 		  			:  			         :

  2 x 9 = 18	3 x 9 = 27	.. 9 x 9 = 81

```bash
#!/bin/bash
for j in $(seq 1 9)
do 
	for i in {1..9}
	do
		printf "%s x %s = %s\t" $i $j `expr $i \* $j`
		if [ $i = 9 ]
		then
			printf "\n"
		fi
	done
done
exit 0

```



* Quiz.아래 조건을 만족하는 quiz.sh 프로그램을 작성하시오.

  ※ **$# : 파라미터가 몇개인지 알려줌**

```bash
1. 프로그램에서 임의의 숫자를 생성 (rand 명령어를 이용) ⇐ apt install rand 명령어로 rand 설치
2. 사용자로부터 숫자를 입력받아서 1)에서 생성한 숫자를 맞추는 프로그램
3. 만약, 사용자가 입력한 숫자가 1)에서 생성한 숫자와 다르면, 크다, 작다 메시지를 출력
4. 사용자가 입력한 숫자가 1)에서 생성한 숫자와 일치하면 맞다 메시지를 출력
5. 단, 사용자가 숫자를 입력하는 회수는 10회로 제한하고, 10회를 초과하는 경우 실패 메시지를 출력

#!/bin/bash
rnum=$(rand)
if [ $# -gt 0 ] && [ $1 = "debug" ]
then
	echo $rnum
fi

for (( i = 0 ; i < 10 ; i ++ ))
do
	echo "input number :"
	read inum
	if [ $inum -eq $rnum ]
	then
		echo "맞다"
		exit 0
	elif [ $inum -lt $rnum ]
	then
		echo "작다"
	else
		echo "크다"
	fi

done
echo "실패"
exit 0
```

---



### eval (명령문인식)

```bash
#!/bin/sh
str="ls -l eval.sh"
echo $str
eval $str
exit 0

결과
ls -l eval.sh
-rw-r--r-- 1 root root 57...
```

---



### export (전역 변수)

```bash
export var="외부 변수"
```

---



### set 

​	※ 공백문자를 기준으로 파라미터로 나눈다.

```bash
set=$(date)
echo $1 = 2020
echo $2 = 09.
echo $# = 6개
```

---



### shift

​	※ 파라미터 변수를 왼쪽으로 한 단계씩 아래로 이동한다

root@server:~# vi shift.sh

```bash
#!/bin/bash
myfunc() {
	str=""
	while [ "$1" != "" ]; do
		str="$str $1"
		shift
	done
	echo $str
}

myfunc AAA BBB CCC DDD EEE FFF GGG
exit 0
```

---



### crontab (크론 등록)

​	※ 주기적으로 실행하는 것

1. crontab -l 		⇐ 등록된 크론을 확인
2. crontab -e 		⇐ 크론을 등록, 수정 

```bash
0  5  *  *  1     tar -zcf   /var/backups/home.tgz   /home/
분 시 일 월 요일 
            0~6 : 일~토 
```

---



* 특정 시간에 특정 디렉터리를 백업

```bash
root@server:~# vi backup.sh
root@server:~# cat backup.sh 
#!/bin/bash
set $(date)
fname="backup$1$2$3tar.xz"			⇐ 2020.09.09.tar.xz
tar cfJ /backup/$fname /home

파일 압축하기
root@server:~# mkdir /backup
root@server:~# bash ./backup.sh
tar: Removing leading `/' from member names
root@server:~# ls /backup/
backup2020.09.09.tar.xz			⇐ 압축 파일
```



* 크론에 등록

![0. 클론에 등록](C:\dev\TIL\2020_09_09\0. 클론에 등록.PNG)

---



### DevOps / 개발과 운용

프로젝트를 수행할 때 절차, 방법, 도구, 산출물 등을 정의하고 있는 것 ⇒ 방법론(method)
애자일SW개발101 [(PDF 파일)](https://sasperger.tistory.com/136) 



* `Vagrant`로 로컬 개발 환경의 ***Infrastructure as Code*** 화 (P58)

  1. 기존에 실행 중 가상머신을 모두 중지 (poweroff)

  2.  Vagrant(가상화 환경 구축) 설치

     ​	※ Vagrant ⇒ 해시코드에서 제공하는 가상 환경 구축 도구

     ​	※ https://www.vagrantup.com/ 환경에 맞는 설치 파일을 다운로드 받아서 설치

  3. 작업 디렉토리 생성

  ```cmd
  C:\> mkdir C:\HashiCorp\WorkDir
  ```

  4. Vagrantfile 파일을 생성

  ```cmd
  C:\HashiCorp\WorkDir> vagrant init
  ```

  5. Vagrantfile을 편집

  ```cmd
  # -*- mode: ruby -*-
  # vi: set ft=ruby :
  
  Vagrant.configure("2") do |config|
  # config.vm.box = "centos/7"
    config.vm.box = "generic/centos7"
    config.vm.hostname = "demo"
    config.vm.network "private_network", ip: "192.168.33.10"
    config.vm.synced_folder ".", "/home/vagrant/sync", disabled: true
  end
  ```

  6. 가상머신을 생성하고 기동

  ```cmd
  C:\HashiCorp\WorkDir> vagrant up
  
  지울땐
  C:\HashiCorp\WorkDir> vagrant destory
  ```

  ※ 아래와 같은 오류가 발생하는 경우

  ```cmd
  Encoding::CompatibilityError: incompatible character encodings: UTF-8 and CP-949
  
  원인 → 윈도우 사용자 계정명에 한글이 포함된 경우
  조치 
  1) 사용자 홈 디렉터리(C:\Users\사용자명) 아래에 있는 .vagrant.d 폴더를 한글이 없는 디렉터리(예: C:\HashCorp)로 복사
  2) 경로 환경 변수에 VAGRANT_HOME 변수를 추가하고 1)에서 설정한 경로를 지정
  ```

  7. vagrant up을 통해서 생성한 가상 머신으로 접속

  ```bash
  방법1 VirtualBox 이용
  계정 : vagrant / vagrant
  
  방법2. vagrant ssh 이용 (별도의 프로그램 설치나 인증이 필요x)
  C:\HashiCorp\WorkDir> vagrant ssh
  
  방법3. ssh 클라이언트 이용(bitvise 푸티 등)
  키 파일이 있는 위치는 아래 명령어로 확인이 가능
  C:\HashiCorp\WorkDir> vagrant ssh-config
  IdentityFile C:/HashiCorp/WorkDir/.vagrant/machines/default/virtualbox/private_key
  ```

  8. 도움말을 참조해서 스냅샷 생성

  ```
  C:\HashiCorp\WorkDir> vagrant --help
  Usage: vagrant [options] <command> [<args>]
  
  C:\HashiCorp\WorkDir> vagrant snapshot --help
  Usage: vagrant snapshot <subcommand> [<args>]
  
  C:\HashiCorp\WorkDir> vagrant snapshot save -h
  Usage: vagrant snapshot save [options] [vm-name] <name>
  
  스냅샷 생성
  C:\HashiCorp\WorkDir> vagrant snapshot save FirstSnapshot
  ```

  ![1. 스냅샷](C:\dev\TIL\2020_09_09\1. 스냅샷.PNG)

  9. 가상 머신을 정지 및 삭제

  ```
  정지
  C:\HashiCorp\WorkDir> vagrant halt
  ==> default: Attempting graceful shutdown of VM...
  
  삭제
  C:\HashiCorp\WorkDir> vagrant destroy
      default: Are you sure you want to destroy the 'default' VM? [y/N] y
  ==> default: Destroying VM and associated drives...
  ```


----



### Vagrant를 이용해서 웹 서버가 설치된 가상 머신을 배포 (P66) [URL](https://www.vagrantup.com/docs/provisioning)

1. Vagrantfile에 provisioning 내용을 추가

   ※ `C:\HashiCorp\WorkDir\Vagrantfile` 위치에서

```vagrant
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
# config.vm.box = "centos/7"
  config.vm.box = "generic/centos7"
  config.vm.hostname = "demo"
  config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.synced_folder ".", "/home/vagrant/sync", disabled: true
  config.vm.provision "shell", inline: $script
end

$script = <<SCRIPT
  yum install -y epel-release
  yum install -y nginx
  echo "Hello, Vagrant" > /usr/share/nginx/html/index.html
  systemctl start nginx
SCRIPT
```

2. provisioning 실행

```
C:\HashiCorp\WorkDir> vagrant up
```

![2. provisioning 실행](C:\dev\TIL\2020_09_09\2. provisioning 실행.PNG)

3. 결과를 확인

![3. 결과를 확인](C:\dev\TIL\2020_09_09\3. 결과를 확인.PNG)

