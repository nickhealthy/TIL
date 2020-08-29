# # 200806

- 시험관련 설명

- Django review(20_08_05)

- 템플릿 상속(84page) ~ 글 삭제(120page)

 

### bootstrapCDN 이용 시: thema and css 링크 참조

  

### 템플릿 상속

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/1.PNG)

1.  extend: base.html 를 상속받는다.

2. block

   > 똑같이 사용할 코드를 ‘Base Template’에 만들고 상속받고, 
   >
   > 나머지 다른 부분은 Template Engine {% block %} 이용

 

### Post Detail (글 상세) 페이지 작성 (93~90)

1.  post_detail.html 만듬

2.  views.py = get_object_or_404 import 및 def post_detail 함수 생성

3.  이거를 불러들이게끔 url path 설정 – urls.py

4.  post_list.html에 태그 안에 ‘url’ 설정>

 

### Form (글 추가) (95)

1.  Form 클래스 정의 (핸들링) blog/forms.py >> 기능 구현

2.  필드 유효성 검사 함수 추가 적용 (raise 등)

3.  폼 클래스 호출 후 validators= 속성을 이용해 2. 필드 유효성 검사 함수 추가

4.  View 함수 내에서 From 인스턴스 생성 / 처음 else 로 들어감(get요청)

5.  post 요청에 한해 입력 값 유효성 검증

6.  템플릿을 통해 HTML폼 생성 (as_table() 속성 사용)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/2.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/3.PNG)

 

### required id 태그는 HTML5에서 자동으로 만들어주는 태그

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/4.PNG)

 

### Form 저장하기 – Form 형식 사용

1.  Form 형식으로 views.py 를 핸들링할 땐 QuerySet 함수를 이용

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/5.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/6.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/7.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/8.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/9.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/10.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/11.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/12.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/13.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/14.PNG)

 

### Form (글 수정) – ModelForm 형식 사용

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/15.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/16.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/17.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/18.PNG)

 

 

### 글 삭제

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/19.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/20.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/21.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_06/22.PNG)