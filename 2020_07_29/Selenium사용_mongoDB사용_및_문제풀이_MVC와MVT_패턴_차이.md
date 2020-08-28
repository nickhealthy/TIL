# # 200729

 

- Selenium 개념 및 사용 (데이터 수집과 분석.pdf)

- 8. Selenium 사용.ipynb

- mongoDB 개념 및 설치(설치파일 2개 받음)

- Django 설명: model 클래스 생성 table 자동 생성 및 mapping = sqlalchemy랑 비슷

- 숙제(mvc패턴/mvt패턴) 차이

- 1. mongodb_basic_open (noSQL에 대한 설명)

- DataGrip으로 mongoDB 사용해보기

- mongoDB 문제풀이

 

### Selenium

- 프로그램 안에서 각 브라우저(chrome etc)에 해당하는 웹 드라이버를 띄어준다. (단점: 느림)

- 웹 드라이버 설치

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image1.png)

 

#### 개념

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image2.png)

 

#### 특징

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image3.png)

 

### MongoDB 패키지 다운로드 / robomongo 툴 다운로드

- 클라우드 서비스를 지원 compass(GUI)

- C:\Program Files\MongoDB\Server\4.2\bin >> path 설정

  > cmd 창에서 명령행 할 수 있음(mongo= python이랑 똑같음)

 

 

### SQL 와 NOSQL의 차이

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image4.png)

- 동적으로 테이블 만들어줌 (ALERT 등 테이블 생성 메서드 필요x)

- Document : json 형태의 data.format

- SQL의 columns는 프레임을 정해놓으면 거기서만 설정할 수 있는데

  > NoSQL 은 (fields)는 컬럼개수가 서로 다를 수 있다. (한 table 내에서)

 

### MongoDB 데이터 구조

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image5.png)

 

 

 

### MongoDB

- collection 생성

  > query criteria : 조건 = SQL의 where 절
  >
  > projection : filed = SQL의 columns 절

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image6.png)

 

- 구조

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image7.png)



- 조건 안에 조건 {} 한번 더 묶어서 조건을 준다.

  > ex) {$ne : ‘value’}
  >
  > OR / AND 사용법

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image8.png) 

 

* Regular expression 이 있다.

```
db.people.find( { user_id: { $regex: /bc/ } } ) - SELECT * FROM people WHERE user_id like "%bc%"

db.people.find( { user_id: { $regex: /^bc/ } } ) - SELECT * FROM people WHERE user_id like "bc%"

db.people.find({user_id:{$regex:/01$/}}) - SELECT * from people where user_id like "%01"
```