# Today I Learned



* C:\Linux 아래에 만들어 놓은 가상머신 이미지 파일들을 다른 폴더 또는 PC로 옮길 경우
* vagrant 로컬 레포지터리 위치
* Vagrantfile로 인프라를 구성했을 때 장점
* 보다 효율적으로 인프라를 관리하기 위한 개선 사항 (=vagrant의 한계)
* 인프라 구성 관리 도구
* 앤서블(Ansible) 기본 사용법
* 의미문자 = 메타문자
* ansible-playbook: 구성 관리 정보(p78 ~ p88)
* [**What is DevOps ?**](https://www.youtube.com/watch?time_continue=1&v=_I94-tJlovg&feature=emb_logo)
* 인프라 테스트 자동화 Serverspec(p90)
* Ansible을 이용해서 Serverspec을 설치
* Ansible을 이용해서 Serverspec에서 사용하는 테스트 케이스(_spec.rb)를 자동으로 생성
* 'Git을 이용하여 필요한 구성 정보를 팀에 공유할 수 있도록 한다' 자율 학습(p104 ~ p145)

---



### C:\Linux 아래에 만들어 놓은 가상머신 이미지 파일들을 다른 폴더 또는 PC로 옮길 경우

1. 가상머신을 종료

2. VirtualBox에서 가상머신을 제거 

3. 가상머신 이미지 파일이 저장된 폴더를 다른 곳으로 이동

4. VirtualBox 관리자에서 **추가**  버튼을 클릭해서 이동한 폴더의 VirualBox 이미지를 선택

   ​	※ 가져오기 : **ovf, ova 형식(가상머신 프로그램 간 상호호환 가능한 포맷)**의 파일을 이용해서 가상머신을 추가

   ​	※ 내보내기 : ovf, ova 형식으로 가상머신 파일을 생성

   ​	※ **추가** : VirtualBox에서 만들어진 가상머신 이미지를 추가

5. 가상머신 추가를 확인



### vagrant 로컬 레포지터리 위치

* C:\Users\myanj\.vagrant.d\boxes

* `vagrant up` 했을 때 생성되는 이미지 파일은 VirtualBox에서 사용하는 기본 저장소에 생성

  ​	※ C:\Users\myanj\VirtualBox VMs



### Vagrantfile로 인프라를 구성했을 때 장점

* 환경 구축 작업이 간소
* 환경 공유 용이
* 환경 파악 용이
* 팀 차원의 유지보수 가능

---



### 보다 효율적으로 인프라를 관리하기 위한 개선 사항 (=vagrant의 한계)

1. 구축 절차를 기술하는 사람에 따라 다양

2. 구축된 환경에 대한 추가 설정이 불가

3. 다양한 환경에 적용하기 어렵다



### 인프라 구성 관리 도구

* 선언적 : 설정 대상의 **상태**가 명확하게 기재되고, 상태를 파악할 수 있는 것

  ```bash
  service:
  	name: nginx
  	state: started
  ```

* 추상화 : 구성 정보를 대상 환경의 미세한 차이에 따라 별도로 구분하여 기술하지 않고, 가능하면 코드 실행의 전문성을 배제

  ※ 상세한 기술은 모두 도구에 포함되어 있다.

  ※ 개발자는 단순히 **"상태"**만을 신경 쓰면 됌.

  ```
  ex) Red Hat 이나 Ubuntu에서 패키지의 설치를 yum, apt-get 명령어를 동일하게 사용 가능
  
  개발자는 단순히 "상태"만을 신경 쓰면 된다.
  ```

* 수렴화 : 상태가 어떠한 상태라고 하더라도 기대했던 상태로 변경되는 것

  ```
  수렴화한다는 것은, 이전의 설정 파일 내용과 관계 없이 해당 파일에 기술되어 있는 결과로만 귀결
  ```

* 멱등성 : 몇 번을 실행해도 같은 결과를 얻을 수 있는 성질

  ​	**※ `선언적`과 `수렴화`의 결합한 개념**

  ​	※ 잘못된 상태로 여러 차례 실행되어도 같은 결과 (안전성 확보)

* 간소화 : 설정을 기술하게 되면 나머지는 전자동으로 되기 때문에 작업의 간소화 실현



### 앤시블(Ansible) 기본 사용법

​	**※ 파이썬으로 만들어진 인프라 구성 관리 도구**

​	**※ 환경 설정 및 구축 절차를 *통일된 형식* 으로 기술 - (사람마다 구축 방법이 다양하기 때문에)**

​	**※ 매개 변수 등 환경의 차이를 관리 - (다른 환경에서도 적용이 가능)**

​	**※ 실행 전에 변경되는 부분을 파악**

* 앤서블 본체 : Ansible 소프트웨어 그 자체
* 인벤토리(inventroy) : 물품 목록(접속지 정보들을 관리) - 조작 대상이 되는 서버 접속 정보를 정의
* 모듈(module) : 명령어 - 조작의 종류



1. nginx 설치되어 있는지 확인

```bash
[vagrant@demo ~]$ systemctl status nginx
```

2. ansible 설치 및 버전 확인

```bash
[vagrant@demo ~]$ sudo systemctl stop nginx.service
[vagrant@demo ~]$ sudo yum install -y epel-release
[vagrant@demo ~]$ sudo yum install -y ansible
[vagrant@demo ~]$ ansible --version
```

3. ansible 명령으로 nginx를 기동

```bash
# 서버 인벤터리 파일에 localhost server를 추가
[vagrant@demo ~]$ sudo sh -c "echo \"localhost\" >> /etc/ansible/hosts"		

# 추가된 것을 확인
[vagrant@demo ~]$ cat /etc/ansible/hosts

# ansible 명령으로 nginx 서비스를 실행
[vagrant@demo ~]$ ansible localhost -b -c local -m service -a "name=nginx state=started"

localhost = 인벤터리 파일에 기재된 서버 중 이번 명령어를 수행할 대상
-b = 원격 실행되는 대상 서버의 사용자 (-b root)
-c local = 대상 서버가 자기 자신이므로 SSH를 사용하지 않고 local로 연결
-m service = service 모듈을 이용
"name=nginx state=started" = 모듈의 추가 인자
```

4. nginx 상태 확인

```bash
[vagrant@demo ~]$ systemctl status nginx.service
```

5. nginx가 실행 상태일 때 ansible 명령(nginx 서비스를 시작)를 재실행

   ​	**※  실행 상태였기 때문에, 상태 변화가 발생하지 않았음**

````bash
[vagrant@demo ~]$ ansible localhost -b -c local -m service -a "name=nginx state=started"
````





### 의미문자 = 메타문자

* 어떤 기능에서 특별한 의미를 가지는 문자 = 특수 기호

```
예를 들면, 
SQL 문을 작성할 때 홑따움표(')는 문자열의 시작과 끝을 나타내는 의미를 가진 문자 
URL에서 &는 요청 파라미터와 파라미터를 구분하는 의미를 가진 문자
```



* 의미 문자를 문자 그 자체로 사용해야 할 경우가 있다.

```
예를 들면, 
content 컬럼에 John's Name 이라는 글자가 들어간 것을 검색하는 쿼리를 만들 때 ⇒ content like '%John's Name%'
요청 파라미터 중 파라미터 이름이 CompanyName이고, 파라미터 값이 Bandi & Luce인 경우 ⇒ …?CompanyName=Bandi & Luce
⇒ 의미 문자를 그대로 사용하면 해석을 할 수 없거나 잘못 해석해서 오류가 발생
⇒ 이런 문제점을 해결하기 위해서는 의미 문자를 문자 그 자체로 해석될 수 있도록 변형해야 한다. 
                                               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
                                               이스케이프 ⇒ 의미 문자에서 의미를 제거하고 문자만 남기는 것
```



* 이스케이프를 구현하는 방법

```
1) 이스케이프를 나타내는 의미 문자를 사용 ⇒ \ (역슬래쉬) ⇒ content like '%John\'s Name%'
2) 해당 기능에서 제공해 주는 규칙을 이용 ⇒ MySQL인 경우 홑따움표를 두 번 사용 ⇒ content like 	'%John''s Name%'
3) 일정한 규칙에 따라서 변경해서 이용 = 인코딩 ⇒ URL의 경우 URL Encoding 기법을 이용해서 표현 ⇒ 	 CompanyName=Bandi %26 Luce
```





### ansible-playbook: 구성 관리 정보(p78 ~ p88)

1. git 설치

```
[vagrant@demo ~]$ sudo yum install -y git
```

2. sible-playbook-sample 레포지터리 클론 생성

```
[vagrant@demo ~]$ git clone https://github.com/devops-book/ansible-playbook-sample.git
```

3. playbook을 실행해서 구축

   ※ playbook 파일은 구축 정보에 대한 정의를 미리 실시해야 함

   ※ playbook 파일을 지정하는 것만으로 자동으로 일련의 처리가 진행되는 구조 

```bash
[vagrant@demo ~]$ cd ansible-playbook-sample/
[vagrant@demo ansible-playbook-sample]$ ansible-playbook -i development site.yml

development : 인벤터리 파일을 지정(/etc/ansible/hosts를 사용하지 않음)

[vagrant@demo ansible-playbook-sample]$ curl localhost
hello, development ansible		⇐ nginx 홈 디렉터리의 index.html 파일의 내용

[vagrant@demo ansible-playbook-sample]$ ansible-playbook -i production site.yml
[vagrant@demo ansible-playbook-sample]$ curl localhost
hello, production ansible
```

[vagrant@demo ansible-playbook-sample]$ vi site.yml

![1. vi site.yml](https://github.com/nickhealthy/TIL/blob/master/2020_09_10/1.%20vi%20site.yml.png)

4. 실행 대상 정의를 확인

**인벤터리 파일은 /etc/ansible/hosts를 사용하지만 -i 옵션을 이용해서 지정할 수도 있음**

~~~~~~~~~~~              bash
[vagrant@demo ansible-playbook-sample]$ ls
`development`  group_vars  `production`  roles  site.yml	⇐ 인벤터리 파일

[vagrant@demo ansible-playbook-sample]$ cat development
[`development-webservers]					⇐ 그룹
localhost

[webservers:children]
development-webservers
[vagrant@demo ansible-playbook-sample]$ cat production
[`production-webservers]
localhost

[webservers:children]
production-webservers
~~~~~~~~~~~

5. 실행 내용 정의를 확인

   ansible-playbook-sample/roles 디렉터리 아래에 tasks 디렉터리에 기록

```bash
[vagrant@demo ansible-playbook-sample]$ ls ./roles
# 롤 별로 실행될 내용을 담고 있는 디렉터리
`common  jenkins  nginx  serverspec  serverspec_sample	

# common 롤에서 수행해야 할 내용을 정의
[vagrant@demo ansible-playbook-sample]$ ls ./roles/common/tasks/
main.yml							

# nginx 롤에서 수행해야 할 내용을 정의
[vagrant@demo ansible-playbook-sample]$ ls ./roles/nginx/tasks/
main.yml							
```

6. 템플릿 확인

```bash
[vagrant@demo ansible-playbook-sample]$ ls ./roles/nginx/templates/
`index.html.j2
```

7. 템플릿에서 사용하는 변수 값을 확인

```bash
[vagrant@demo ansible-playbook-sample]$ ls ./group_vars/
`development-webservers.yml  production-webservers.yml
```

8. 템플릿 내용을 변경

```bash
# 템플릿 내용 변경
[vagrant@demo ansible-playbook-sample]$ vi ./roles/nginx/templates/index.html.j2
`HELLO, {{ env }} ansible !!!				
```

9. `dry-run` 모드로 실행

   ​	※ 실제로 변경된 내용을 반영하지 않고 반영되었을 때 결과를 미리 확인

```bash
[vagrant@demo ansible-playbook-sample]$ ansible-playbook -i development site.yml --check --diff

--check : `dry-run` 모드를 지정
--diff : 옵션 변경 차이를 표시
```

10. 변경 사항을 호스트에 반영

```bash
[vagrant@demo ansible-playbook-sample]$ ansible-playbook -i development site.yml --diff

[vagrant@demo ansible-playbook-sample]$ curl localhost
`HELLO, development ansible !!!	⇐ 변경된 내용이 반영된 것을 확인
```



----

[**What is DevOps ?**](https://www.youtube.com/watch?time_continue=1&v=_I94-tJlovg&feature=emb_logo)

### 인프라 테스트 자동화 Serverspec(p90)

##### 	**※ serverspec은 .rb로 된 파일이 있어야 실행된다. (templates)**

* **Serverspec**

  > * 테스트 수행을 간단하고 쉽게 하기 위한 도구
  > * 인프라(서버) 설정 테스트 가능
  > * 테스트 항목에 대한 목록을 정해진 포맷을 기반으로 기술이 가능
  > * 테스트 결과를 리포트 형식으로 출력이 가능



### Ansible을 이용해서 Serverspec을 설치

1. rvm 및 ruby 설치

```bash
# 3번째 줄 curl 파일을 가져오기 위한 선작업
[vagrant@demo ansible-playbook-sample]$ command curl -sSL https://rvm.io/mpapis.asc | sudo gpg2 --import -
[vagrant@demo ansible-playbook-sample]$ command curl -sSL https://rvm.io/pkuczynski.asc | sudo gpg2--import -

# 파일을 가져온다. / sudo bash -s stable (실행 하는 문장)
[vagrant@demo ansible-playbook-sample]$ curl -L get.rvm.io | sudo bash -s stable
`Downloading https://github.com/rvm/rvm/archive/1.29.10.tar.gz`
`Downloading https://github.com/rvm/rvm/releases/download/1.29.10/1.29.10.tar.gz.asc`
```

2. $USER 권한 설정

```bash
# rvm를 사용할 수 있도록 USER 계정 등록 후 reload
[vagrant@demo ansible-playbook-sample]$ sudo usermod -aG rvm $USER
[vagrant@demo ansible-playbook-sample]$ id $USER
[vagrant@demo ansible-playbook-sample]$ source /etc/profile.d/rvm.sh
[vagrant@demo ansible-playbook-sample]$ rvm reload

# root 계정에서 ruby 설치
# rvm : 루비 버전 매니저
[vagrant@demo ansible-playbook-sample]$ sudo su
# 체크 후 실행
[root@demo ansible-playbook-sample]# rvm requirements run
# 설치 및 default 설정
[root@demo ansible-playbook-sample]# rvm install 2.7
[root@demo ansible-playbook-sample]# rvm use 2.7 --default
[root@demo ansible-playbook-sample]# rvm list
[root@demo ansible-playbook-sample]# exit

# 버전이 바껴있지 않음
[vagrant@demo ansible-playbook-sample]$ ruby -v
`ruby 2.0.0p648`
# 버전 설정
[vagrant@demo ansible-playbook-sample]$ rvm use 2.7 --default
[vagrant@demo ansible-playbook-sample]$ ruby -v
`ruby 2.7.0p0`
# sudo로 실행시 2.0으로 고정
[vagrant@demo ansible-playbook-sample]$ sudo ruby -v
`ruby 2.0.0p648`
# 각자 어느 버전으로 실행되는지 확인
[vagrant@demo ansible-playbook-sample]$ which ruby
[vagrant@demo ansible-playbook-sample]$ sudo which ruby
# 기존파일을 백업 후 심볼릭 링크 파일로 sudo 경로로 실행되는 설정을 변경한다.
[vagrant@demo ansible-playbook-sample]$ sudo mv /bin/ruby /bin/ruby_2.0.0
[vagrant@demo ansible-playbook-sample]$ sudo ln -s /usr/local/rvm/rubies/ruby-2.7.0/bin/ruby /bin/ruby
# 다시 확인 
# (playbook 설정 시 become이 yes라 root계정으로 이용하기 때문에 sudo 경로도 확실히 설정!)
[vagrant@demo ansible-playbook-sample]$ ruby -v
[vagrant@demo ansible-playbook-sample]$ sudo ruby -v
```



2. Playbook 파일(site.yml)에서 serverspec 롤을 추가

```bash
[vagrant@demo ansible-playbook-sample]$ vi site.yml


---
- hosts: webservers
  become: yes
  connection: local
  roles:
    - common
    - nginx
`    - serverspec
#    - serverspec_sample
#    - jenkins
```



3. serverspec 롤을 확인

```bash
cat ./roles/serverspec/tasks/main.yml
```



4. ansible-playbook으로 Serverspec 설치

```bash
[vagrant@demo ansible-playbook-sample]$ ansible-playbook -i development site.yml --diff
```



5. Serverspec 설정

```bash
[vagrant@demo ansible-playbook-sample]$ serverspec-init

Select OS type:

  1) UN*X
  2) Windows

`Select number: 1`

Select a backend type:

  1) SSH
  2) Exec (local)

`Select number: 2`

 + spec/
 + spec/localhost/
 `+ spec/localhost/sample_spec.rb <= 샘플`
 + spec/spec_helper.rb
 + Rakefile
 + .rspec

```



6. sample_spec.rb (샘플)파일을 확인 **⇒ 테스트 케이스 작성법을 확인**

```bash
#서버의 상태를 테스트하는 코드
[vagrant@demo ansible-playbook-sample]$ cat ./spec/localhost/sample_spec.rb 

require 'spec_helper'

describe `package('httpd')`, :if => os[:family] == 'redhat' do
  `it { should be_installed }		⇐ httpd가 설치되어 있어야 한다.`
end
		:
describe `service('httpd')`, :if => os[:family] == 'redhat' do
  `it { should be_enabled }		⇐ httpd 서비스가 활성화되어 있어야 한다.`
  `it { should be_running }		⇐ httpd 서비스가 실행되고 있어야 한다. `
end
		:
describe `port(80)`` do
  `it { should be_listening }	⇐ 80 포트가 동작(listen)하고 있어야 한다.
end
```



7. Serverspec을 이용한 테스트 실행

```bash
[vagrant@demo ansible-playbook-sample]$ rake spec
```



### Ansible을 이용해서 Serverspec에서 사용하는 테스트 케이스(_spec.rb)를 자동으로 생성

1. Playbook 파일(site.yml)에 **serverspec_sample**  롤(role)을 추가

```bash
[vagrant@demo ansible-playbook-sample]$ vi site.yml

---
- hosts: webservers
  become: yes
  connection: local
  roles:
    - common
    - nginx
    - serverspec                 
`    - serverspec_sample           ⇐ 주석(#) 해제 후 저장`
#    - jenkins
```

2. serverspec_sample 롤 정의 파일을 확인 (task 파일)

```bash
[vagrant@demo ansible-playbook-sample]$ cat ./roles/serverspec_sample/tasks/main.yml

---
# tasks file for serverspec_sample
- name: distribute serverspec suite
# /tmp 아래로 serverspec_sample 디렉터리를 복사
  copy: src=serverspec_sample dest={{ serverspec_base_path }} `serverspec_base_path 변수는 /roles/serverspec_sample/vars/main.yml에 정의 되어 있다`

- name: distribute spec file
# 템플릿에 정의된 내용으로 web_spec.rb 파일을 생성
  template: src=`web_spec.rb.j2` dest={{ serverspec_path }}/spec/localhost/web_spec.rb
  

# task에서 사용하는 변수를 정의
[vagrant@demo ansible-playbook-sample]$ cat ./roles/serverspec_sample/vars/main.yml	
serverspec_base_path: "/tmp"
serverspec_path: "{{ serverspec_base_path }}/serverspec_sample"


# templates에 있는 정보
# serverspec에서 사용할 테스트케이스 템플릿
`web_spec.rb.j2`
[vagrant@demo ansible-playbook-sample]$ cat ./roles/serverspec_sample/templates/web_spec.rb.j2

# nginx 설치 여부
describe package('nginx') do		
  it { should be_installed }
end

# nginx 실행/활성화 여부
describe service('nginx') do
  it { should be_enabled }
  it { should be_running }
end

# 80 포트 확인
describe port(80) do
  it { should be_listening }
end

# index.html 파일 존재 여부
describe file('/usr/share/nginx/html/index.html') do
  it { should be_file }		⇐ index.html 파일 존재 여부
  it { should exist }
  # index.html 파일의 내용 검증
  its(:content) { should match /^Hello, {{ env }} ansible!!$/ }	⇐ 
end
```

3. ansible-playbook으로 spec 파일(테스트 케이스 파일)을 배포

```bash
[vagrant@demo ansible-playbook-sample]$ ansible-playbook -i development site.yml

# 위에 생략
  :
  :
TASK [serverspec : install ruby] ******************************************************************
ok: [localhost]

TASK [install serverspec] *************************************************************************
ok: [localhost] => (item=rake)
ok: [localhost] => (item=serverspec)

# 위의 명령어를 통해 추가된 사항
# /tmp 디렉토리 아래로 serverspec_sample 디렉토리를 복사
# /tmp/serverspec_sample 디렉토리는 인프라가 원하는 형태로 구성되었는지 테스트하는 공간
`TASK [serverspec_sample : distribute serverspec suite] ********************************************
`changed: [localhost]

# 템플릿을 이용해서 web_spec.rb 파일을 정상적으로 생성
`TASK [serverspec_sample : distribute spec file] ***************************************************
`changed: [localhost]

```

4. spec 파일(테스트 케이스를 정의) 생성을 확인

```bash
[vagrant@demo ansible-playbook-sample]$ cat /tmp/serverspec_sample/spec/localhost/web_spec.rb

require 'spec_helper'

describe package('nginx') do
  it { should be_installed }
end

describe service('nginx') do
  it { should be_enabled }
  it { should be_running }
end

describe port(80) do
  it { should be_listening }
end

describe file('/usr/share/nginx/html/index.html') do
  it { should be_file }
  it { should exist }
  its(:content) { should match /^Hello, `development` ansible!!$/ }
end
```

5. (ansible을 이용해서 자동으로 생성한 spec 파일을 이용해서) 테스트를 실행

```bash
# 작업 디렉터리(테스트 디렉터리)로 이동
[vagrant@demo ansible-playbook-sample]$ cd /tmp/serverspec_sample/
# 테스트 실행
[vagrant@demo serverspec_sample]$ rake spec

# 오류 생략
# nginx의 index.html 파일 내용이 테스트 케이스에 명싱된 내용과 다르기 때문에 테스트 실패가 발생
```

6. 테스트 케이스를 통과하도록 컨텐츠를 수정 **→ 컨텐츠 형식을 정의하고 있는 템플릿 파일을 수정**

```bash
[vagrant@demo ansible-playbook-sample]$ cat ~/ansible-playbook-sample/roles/nginx/templates/index.html.j2
# 테스트 케이스와 상이 > 테스트 케이스에 맞춰서 수정
`HELLO`, {{ env }} `ansible !!!`

[vagrant@demo ansible-playbook-sample]$ vi ~/ansible-playbook-sample/roles/nginx/templates/index.html.j2
`Hello`, {{ env }} `ansible!!``
```

7.  **ansible-playbook으로 수정한 템플릿에 맞춰서 새롭게 index.html을 생성**

```bash
[vagrant@demo ansible-playbook-sample]$ ansible-playbook -i development site.yml

PLAY [webservers] *********************************************************************************

TASK [Gathering Facts] ****************************************************************************
ok: [localhost]

TASK [common : install epel] **********************************************************************
ok: [localhost]

TASK [install nginx] ******************************************************************************
ok: [localhost]

# 변경사항
`TASK [nginx : replace index.html] *****************************************************************
`changed: [localhost]

TASK [nginx start] ********************************************************************************
ok: [localhost]

TASK [serverspec : install ruby] ******************************************************************
ok: [localhost]

TASK [install serverspec] *************************************************************************
ok: [localhost] => (item=rake)
ok: [localhost] => (item=serverspec)

TASK [serverspec_sample : distribute serverspec suite] ********************************************
ok: [localhost]

TASK [serverspec_sample : distribute spec file] ***************************************************
ok: [localhost]

PLAY RECAP ****************************************************************************************
localhost                  : ok=9    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

8. 테스트 재실행

```bash
[vagrant@demo ansible-playbook-sample]$ cd /tmp/serverspec_sample/
[vagrant@demo serverspec_sample]$ rake spec
```

9. nginx를 중지 후 테스트 실행

```bash
[vagrant@demo serverspec_sample]$ sudo systemctl stop nginx.service
[vagrant@demo serverspec_sample]$ systemctl status nginx.service

# 테스트 실행 > 에러발생
[vagrant@demo serverspec_sample]$ rake spec
`is expected to be running (FAILED - 1)
`is expected to be listening (FAILED - 2)
```

10. 테스트 결과를 HTML 형식으로 출력

```bash
# coderay 패키지를 설치해서 루비 모듈 사용할 수 있도록 설치
[vagrant@demo serverspec_sample]$ sudo gem install coderay

# 옵션을 줘서 html 형태로 출력할 수 있게 리다이렉션
[vagrant@demo serverspec_sample]$ rake spec SPEC_OPTS="--format html" > ~/result.html

# 현재 디렉터리위치를 nginx/html로 옮김
[vagrant@demo serverspec_sample]$ sudo mv ~/result.html /usr/share/nginx/html/
[vagrant@demo serverspec_sample]$ sudo setenforce 0
[vagrant@demo serverspec_sample]$ sudo systemctl start nginx.service

# 접속이 안될때 방화벽 확인 및 내리기
[vagrant@demo serverspec_sample]$ sudo systemctl status firewalld.service
[vagrant@demo serverspec_sample]$ sudo systemctl stop firewalld.service

# 호스트 PC에서 http://192.168.33.10/result.html 으로 접속
```

![2. HTML형식으로 serverspec 확인](https://github.com/nickhealthy/TIL/blob/master/2020_09_10/2.%20HTML%ED%98%95%EC%8B%9D%EC%9C%BC%EB%A1%9C%20serverspec%20%ED%99%95%EC%9D%B8.PNG)

##### 'Git을 이용하여 필요한 구성 정보를 팀에 공유할 수 있도록 한다' 자율 학습(p104 ~ p145)

* 내일 3장부터 진행



---

복습

1. ansible-playbook = `playbook 파일` = `site.yml`

```
site.yml 내에서
hosts: webservers = 각각의 호스트 그룹
```

2. ansible-playbook -i developer site.yml

   ※ playbook를 실행하여 구축 : playbook 파일을 지정하는 것만 자동으로 일련의 처리를 진행

   ※ site.yml 에 있는 task 정의는 `roles` 폴더 안에 정의되어 있다.

3. group_vars / templates

   ※ 환경에 따라 설정 값을 나눠서 기술(표현)

   ※ templates 는 `role` 밑에 기술

   ※ group_vars 는 