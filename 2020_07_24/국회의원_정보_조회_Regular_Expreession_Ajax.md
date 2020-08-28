# # 200724

 

- 200723 review (Pandas)

- Melon 100 chart

- BeautifulSoup Parsing 힘들면 : 정규 표현식 쓰기

- 국회의원 정보 조회 : Ajax (데이터 조립 등등)

- 정규 표현식 함수

- 절대path / 상대path

- 국회의원현황(Ajax)\_스크래핑\_분석_저장

 

### DB - Pandas

> pandas 가공하고 전처리 하고 나서 DB에 저장
>
> * DB 데이터 관리하는 디스크가 비싸서



### melon

- div#tb_list – tr - div.wrap_song_info

- #frm > div > table > tbody > tr:nth-child(2) > td:nth-child(4) > div > div > div.ellipsis.rank01 > span > a

 

### 국회의원

```
https://www.assembly.go.kr/assm/memact/congressman/memCond/memCondListAjax.do
```

> network 의 .do 파일의 data
>
> data parsing 값 조립하기
>
> POST 방식 : Form Data

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image1.png)

> & : 구분자 (딕셔너리의 ‘,’)
>
> = : 딕셔너리의 ‘:’

 

### 정규 표현식(Regular Expression) [URL](http://www.nextree.co.kr/p4327/)

* ([A-Z])\w+

> \w << word
>
> \+ << 횟수
>
> 특수문자가 나올 시 ‘’ 사용

 

### Ajax(Asynchronous JavaScript and XML) : 비동기, XML

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image2.png)

 

* 기존웹

  > 기존웹은 싱크로싱?
  >
  > HTML 페이지를 내려보내줌
  >
  > 응답이 다 올때까지 조작 불가능
  >
  > HTML 테이블에서 정보를 조작

HTML 테이블 

```html
<table>
    <tr>
	    <td>홍길동</td>
    </tr>
</table>
```



- Ajax

  > 어크로싱?
  >
  > 응답이 다 안와도 화면 조작 가능
  >
  > 자바 스크립트에서 HTML 테이블을 조작한다. 
  >
  > 원하는 정보를 눌렀을 때 해당 자바스크립트 객체만 실행
  >
  > 새로고침 등 데이터를 바껴도 reload 를 안함
  >
  > open in a new tab >> 페이지 소스 보기 >> Ajax 식 자바스크립트 코드 볼 수 있음

 

### 정규 표현식 함수

- group(‘값’) : 괄호 안에 값에 따라 표현하는 방식이 다름

 

### 절대path / 상대path

- 상대path /photo/9771230.jpg

- 절대 path / https://www.assembly.go.kr/assm/memPop/memPopup.do?dept_cd=9771230

 

### regular expression 임포트 및 함수

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image3.png)