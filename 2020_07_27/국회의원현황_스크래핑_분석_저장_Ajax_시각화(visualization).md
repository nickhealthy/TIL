# # 200727

- 국회의원 Network – XHR(XmlHttpRequest)

- Ajax

- 시각화(visualization)
  * Matplotlib, Seaborn 사용해서그래프 그리기

- melon100Chart DB에 저장
  * json 파일 읽어오기 및 데이터프레임에 임포트

- Use DataGrip Tool

 

### Ajax

> 비동기적으로 통신
>
> 비동기 통신을 담당하는 javascript 객체 XHR(XmlHttpRequest) 보고 싶은것만 필터링해서 보겠다
>
> 서버로부터 XML, Json 형태의 데이터만 받아서 동적으로(dynamic) html tag를 생성한다.

 

### Matplotlib

> Figure – 도화지 // plt.subplot(**nrows**=2, **ncols**=1) – 도화지 분할
>
> Axes - plot 이 그려지는 공간
>
> Axis – x, y 축
>
> countplot <> barplot – x,y 축 설정할 수 있는 개수 차이

 

### jupyter에서 warning 메시지

> warning 메시지를 숨기고 싶으면 warnings.filterwarnings(action='ignore')
>
> warning 메시지를 다시 보고 싶으면 warnings.filterwarnings(action='default')

 

### seaborn

> sns.set() >> seaborn의 default 그래프 값으로 그린다.

 

### DB

> cmd >>desc ‘table name’ – 테이블 구조 확인

 

### 다양한 DBMS 툴들

1.  TOAD

2.  orange for oracle

3.  DataGrip >> 이거좋음