# # 200722



* Jupyter : 02-네이버웹툰++Multiple+Image+Download review

- 정규식 표현 : find() = select_one(), find_all() = select()

- RSS

- CSS SELECT TAG 연습

- 기상청 날씨 데이터

- 기상청 자료구조 연습(json 으로 파일 형식 만드는거까지)

- Pandas

 

### WEB SCRAPING 개념

* requests >> response.text : 문서를 읽어올 때

* requests >> response.content : 이미지를 읽어올 때(binary 파일)

* headers 속성 자체가 ditc 형식이기 때문에 dict 형으로 적어준다.

  > ex) response = requests.get(image_url, headers=headers) << 두번째 인자

* BeautifulSoup = use / don’t use 

  > text 파일같은건 parsing 해서 원하는 값만 추출
  >
  > binary 파일은 parsing 작업 할 필요가 없음

* (BeautifulSoup).select >> CSS select 지정하는 함수, select는 기본적으로 list type

  > (tag, ID, class, attribute), (* =, 부분 매칭 ^= : 시작, $= : 종료)
  >
  > ID << 고유한 이름을 지정

*  HTML 태그

 

### HTML, DOM

* HTML(Hyper Text Markup Language)

* DOM(Document Object Model) : 문서를 객체화 한다.

  > dom tree : 계층형 구조를 나타냄
  >
  > traversing, traverse(순회한다)
  >
  > manipulation(조작) : tree 구조를 바꾸는걸 의미 ex) insert etc..
  >
  > regular expression(정규 표현식) : 

 

![img](https://github.com/nickhealthy/TIL/blob/master/2020_07_22/1.%20regular%20expression.PNG)



**방법 2.**

![img](https://github.com/nickhealthy/TIL/blob/master/2020_07_22/2.%20%EC%9B%B9%EC%97%90%EC%84%9C%20%EB%AC%B8%EC%9E%90%EC%97%B4%20%EA%B0%80%EC%A0%B8%EC%98%A4%EB%8A%94%20%EB%B0%A9%EB%B2%95.PNG)



### Tag 찾는 방법

![img](https://github.com/nickhealthy/TIL/blob/master/2020_07_22/3.%20tag%20%EC%B0%BE%EB%8A%94%20%EB%B0%A9%EB%B2%95.PNG)

 

### RSS

 

### Pandas

> head()
>
> tail()
>
> describe : 집계함수
>
> rename('prev','now')
>
> data = data.rename(columns={'Unnamed: 0':'seq'}) # columns 라는 속성 = ('prev','now')
>
> set_index(‘column name’) : 컬럼을 인덱스로 변경
>
> property(속성) : ()를 안붙임 <> 함수()
>
> columns
>
> index : index 범위 range 랑 똑같음
>
> shape : 몇행 몇열로 나와줌
>
> print(type(data['인구수'])) # 특정 컬럼, 행 하나만 선택하면 Series 자료형 <ㅡ> 전체 or 행, 열 2개이상 = data framework 자료형

 

### 숙제

> Ajax란? // 국회의원 하기 전까지
>
> 기존웹과 Ajax 방식의 웹 차이점
>
> sql group by 란?
>
> Data 딕셔너리 임포트