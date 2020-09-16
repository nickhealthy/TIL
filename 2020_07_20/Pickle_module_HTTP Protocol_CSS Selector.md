# # 200720

- Pickle 모듈
- Anaconda : 파이썬 써드파티 모듈 저장소
1. Add Anaconda to the system PATH environment variable(체크사항) : CLI 방식으로 입력시킬 수 있다
- npmjs : 자바스크립트 써드파티 모듈 저장소
- maven repository : 자바 써드파티 모듈 저장소
- requests : http 통신 역활을 담당한다.
- parsing : 태그와 태그 사이에 있는 컨텐츠를 뽑아내는 것
1. 정규표현식(regular expression) 사용
2. beautifulsoup 사용: parse tree(트리 구조로 되어있다.)
- pycon/ 온라인 컨퍼런스 9/26(토) - 9/27(일) : 파이썬 기술 컨퍼런스 
- beautifulsoup : data parsing
- pandas : 표 데이터 처리(very powerful!) / 만든분 : Wes McKinney
- matplotlib : 시각화 
- seaborn : 시각화
- pymysql : 오픈소스와 데이터 디비 연동
- pymongo : mongodb driver
- sqlalchemy : DB데이터 연동할때 자동 테이블 완성기능 = RDB
: ORM Mapping rule
1. class <=> Table
2. object <=> (Row = Record, 행)
3. variable <=> column
### Jupyter Notebook (jupyter lab) : 라이브 코드, 등식, 시각화와 설명을 위한 텍스트 등을 포함한 문서를 만들고 공유가 가능한 오픈소스 웹 어플리케이션입니다. 

: ipython 기반으로 되어있다.
: ipython <> python : input, output 존재
: 경로 설정

1. cmd 창에서 jupyter notebook 입력
2. cd "경로"
3. jupyter notebook
: .ipynb : ipython notebook 확장자
: Alt + 엔터 : 실행 후 셀 하나 생성, Shift + 엔터 : 셀 실행후 밑에 셀 선택, Ctrl + 엔터 : 해당 셀만 실행
: 나갔다 들어오면 Cell.Run All 실행 시켜줘야함 - 그 전에 작성한 코드는 메모리상에 없어짐(나갔다들어올시)
: 메모리를 다시 초기화 시키고 싶으면 Kernel.Restart & Run All 실행
: 확장자 변경 가능 (File Tab - Download as - select)
- Mark down : 이미지, 링크, 리스트, 글자 크기 등등 레이아웃 및 스타일 지정
- Web Scraping
- NAVER OPEN API (PAPAGO)



### import Pickle : class_object 파일 저장하고 불러들일때 사용
- dump() : 저장하는것
- load() : 불러오는것



### Web Scraping
- Chrome 개발자 도구 이용



### http protocol(request, response 상호 교류가 이루어져야함) : http method
- GET : url에 append 해서 보내는 방식, 조회
클라이언트 --> 클라우드 요청시 --> 지정했던 경로 그대로 들어가는것(KEY : VALUE) 값이 들어감
- POST : body stream에 데이터 담아서 보내는 방식, 등록, 갱신
ex) 게시글 올리기 등등
- HTTP 상태 코드 : 프로토콜 송신 상태를 나타냄 (http status)
- response.text # 페이지 소스보기랑 동일한 값
- cookie, session
: http 프로토콜은 connectionless
: why ? : server 의 트래픽을 줄여주기 위해서
- connectionless 가 된 이후에 정보를 저장하기 위해서, (다시 읽어들이기 위해)
: 클라이언트에 저장은 cookie
: 클라우드에 저장은 session



### CSS Selector

```html
<div id="myDiv">			 #ID selector
<div>
<div class = "myClass"> 		#CLASS selector
</div>
<ul>
  <li>aaa</li>			#리스트 TAG
  <li>aaa</li>
</ul>
<a href = "www.naver.com"?</a>
<img src="">
```



1) Tag Selector : tag명 입력해서 특정 tag을 선택
: soup.select('li') : 괄호안에 tag 입력

```html
<ul>
<li>aaa</li>
<li>aaa</li>
</ul>
```

2) ID selector : #
: soup.select('#myDiv') : 괄호안에 '#특정 이름'

```html
<div id="myDiv">
</div>
<div>
</div>
```

3) Class selector : .
: soup.select('div.myClass') : . 은 해당 클래스의 경로를 의미함

```html
<div id="myDiv">
</div>
<div class = "myClass">
</div>
```

4) attribute selector : []
soup.select("a[href='www.naver.com']")
*= : 부분 매칭
^= : 시작
$= : 종료

```html
<a href="www.naver.com">기사제목</a>
a_tag.text
a_tag['href'] << a_tag 에 있는 속성 값을 가져온다
```

```
5) Edit Attribute 클릭 -> 복사
헤드라인 :  /main/read.nhn?mode=LSD&mid=shm&sid1=100&oid=011&aid=0003771063
정치 : https://news.naver.com/main/read.nhn?mode=LSD&mid=shm&sid1=100&oid=015&aid=0004383703
```

```
6) Copy -> Copy selector : 태그 계층 구조를 나타냄
```



# = ID
> #today_main_news > div.hdline_news > ul > li:nth-child(1) > div.hdline_article_tit > a
> #section_politics > div.com_list > div > ul > li:nth-child(1) > a