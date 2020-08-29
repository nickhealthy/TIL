# # 200807

 

- @login_required



* Model class와 Form의 연동 및 처리 프로세스
  1. models.py (model class)에 class 정의
  2. forms.py에서 ‘model class’ 객체를 호출해주고 내가 원하는 것만 보이게 핸들링
  3. views.py에서 forms.py(여기 안에 models.py의 객체를 가지고 있음)랑 Template(HTML, CSS).py를 묶음

 

### 로그인 안한 상태에서 @login_required 설정했는데 정상 작동할 경우

1.  개발자 도구 > 어플리케이션 > 쿠키 clear

 

### @login required / 로그인 처리

1.  next= 라는 QueryString으로 날려서 에러를 발생시킴

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/1.PNG)

 

2.  

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/2.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/3.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/4.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/5.PNG)

맞춰줘야함

 

3.  메인 프로젝트 폴더 urls.py 경로(path)에 지정 (장고에서 정해준 path라서 저걸로 맞춰줘야함)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/6.PNG)

 

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/7.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/8.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/9.PNG)

 

### 로그아웃 처리

1.  

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/10.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/11.PNG)

 

### 댓글 모델(comment model 작성)

* 모델 가이드 라인 

1.  모델.py에 class 정의 및 핸들링

2.  makemigrations 이후 migration 해서 데이터 베이스에 등록

3.  python manage.py sqlmigrate blog 0005 로 바뀐 데이터 베이스 테이블 확인

4.  blog/admin.py 폴더에서 admin 페이지에서 볼 수 있도록 설정 admin.site.register(Comment)

 

#### Post class에 여러 ‘comment’ 들을 달도록 설정했으니까 ‘comments’ 라고 이름 지정

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/12.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/13.PNG)



## Model 및 admin 계정 핸들링 끝 

여러 comments을 `Template Engine` `%for%` 문으로 뿌려줌 

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/14.PNG) 

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/15.PNG)

 



### 화면에 post_list.html에서 설정 - link와 comments 개수를 출력

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/16.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/17.PNG)

 

### 댓글(Comment) 작성 – Form 등록하기

- **Form class 가이드 라인**

1.  blog/forms.py 에 보이게(원하는) 하고 싶은 ‘form’ 핸들링

2.  blog/urls.py에 path 추가

3.  blog/views.py 에 Form get/post 설정 (97page 참고)

 

* **views.py에서 - Form 테이블 생성 및 구동 설계**

1.  HTML 생성

2.  views.py 로 핸들링하여(함수 추가) Form 처리방식 연동 (97page 참고)

3.  from .forms.py import CommentModelForm 추가 후 forms.py에서 만든 class - CommentModelForm 추가

4.  Form 처리 방식은 get method부터 들어가서 처리해야 하기 때문에 else:에(get방식) : form = CommentModelForm() - class 추가

 

### 댓글 수정 / 삭제

 

##### 댓글 승인을 눌러야 Post_list.html에 count가 세지게함 (model class에서 승인요청 함수 추가)

 

### mariaDB에 Django연동 / 데이터 임포트

1. mySQL cmd에서 python 계정에 들어가서 django_db 데이터 베이스 생성
2. pyCharm에서 pip로 pymysql과 mysqlclient 설치
3. import 설정

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/18.PNG)

4. Django에서 settings.py DATABASES={}에 설정 (80번째 줄)![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/19.PNG)
5. DB 업로드 : python manage.py migrate
6. pyCharm cmd에서 python manage.py createsuperuser 생성
7. DB 생성 및 연동

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/20.PNG)

8. desc blog_post; 로 테이블 확인 (desc = describe)

## DB생성 및 연동 완료

 

 

# JavaScript_confirm() 사용법

1.  post_detail.html에 있다.

2. 삭제버튼 클릭시

   ![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/22.PNG)

   \<Script 설정 화면>

   ![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_07/21.PNG)

