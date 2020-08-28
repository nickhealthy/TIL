# # 200723

 

- 200722 review

- data.loc(location) = DataFrame

- data.iloc (index location)= DataFrame

- Pandas활용한 행정구역정보 분석

- kaggle

- matplotlib

- 한글 폰트 타입 위치

- database

- Pymysql 설치

- MySQL 설치




### pandas 개념

> 행은 어떤 값들을 정할 수 있게 해주고,
>
> 열은 내가 보고 싶은 자료(조건)을 정한다.

 

### iloc(index location)

> iloc 은 index 값으로 주고 loc은 colunm name 이나 row name 으로 지정

 

### 함수

> unique() = list, type : numpy.ndarray, 자료형은 동일한 자료형만 가능
>
> value_counts() : 특정 colunm별로 counting

 

### kaggle

- trainig set : 데이터 가공 후 훈련시킴

- test set : 프로토타입 ?

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image1.png)

 

### Jupyter notebook

> shift + tab : 함수 인자, 설명

 

### matplotlib

> scatter plot : 분포도
>
> jupyter note 상에서 ‘plt.show()’ 를 명시해 주고 싶지 않으면 %matplotlib inline 를 입력

 

 

### 한글 폰트 타입 위치

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image2.png)

 



### database 

* MySQL Database 생성

  > mysql -u root –p
  >
  > show databases;
  >
  > use mysql;  
  >
  > * 내가 사용하고 있는 디비명 할당
  >
  > create user python@localhost identified by 'python'; 
  >
  > * create user python@localhost – 아이디, 
  >
  > * identified by 'python' : 비밀번호
  >
  > select user, host from user;
  >
  > * user 로컬 호스트 지정
  >
  >  grant all on *.* to python@localhost;
  >
  > * grant all on >> 권한을 다 주겠다.
  >
  > flush privileges;
  >
  > * 변경 내용 반영시키기
  >
  > exit;
  >
  > * root 사용자 아웃
  >
  > mysql -u python –p
  >
  > create database python_db;
  >
  > show databases;
  >
  > use python_db;

