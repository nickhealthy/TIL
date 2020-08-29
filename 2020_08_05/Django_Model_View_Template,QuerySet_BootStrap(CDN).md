# # 200805

 

- Django 개념 및 Model class review

- Django migrate // QuerySet // views // template 

- Bootstrap (CDN)

- favicon.io

 

### Django-south

1.  모델 변경 히스토리를 관리해준다.

2.  변경내용을 Schema 를 통해 생성 및 반영함

 

### migrate 명령어 및 구조

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image1.png)

 

1.  python manage.py showmigrations >> DB적용 여부를 물음

 ![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image2.png)



![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image3.png)

 

 

### URL path 설정

1.  각 URL 마다 독립적으로 분리해서 소스코드를 작성

2.  프로젝트 메인 폴더(mydjango/urls.py)에 한번에 저장하면 각 url 관리하기가 불편함

3.  각 URL(ex.blog) 소스 작성후 메인 폴더에 path( ‘inClude’)를 추가

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image4.png)

 

### View 설정 (이거는 view에 template(HTML)를 박은거임

1.  각 URL(blog) 밑에 views.py 소스 파일에서 HttpResponse 클래스를 추가해준다. (핸들링 작업)

```django
from django.http import HttpResponse
```

 

2.  메인 프로젝트 파일 안에 있는 urls.py 모듈에 path 추가 (include 클래스 호출)

```django
from django.urls import path, include
```



### Template

1.  APP 디렉토리.Templates 는 폴더이름이 정해져있음

blog

 templates <<

blog

 post_list.html

----------------

board

 templates <<

board

 post_list.html

 

2. Templates 밑에 blog, board 등 서브폴더를 생성해줘야함.

   > 생성을 안해줄 시 Templates 밑에 만든 모든 HTML을 적용시킴

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image5.png)



### ORM과 QuerySet 

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image6.png)

 

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image7.png)

 

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image8.png)

 

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image9.png)

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image10.png)

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image11.png)

 

 

### View에서 동적 데이터 생성하기

1.  내가 원하는대로 소스 핸들링 후

2.  return 해준다. (동적으로 만들 template.html 지정해줘야함)

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image12.png)

 

### Template에서 동적 데이터 생성하기

1.  보여지는 화면 HTML 태그들로 핸들링

2.  자료 여러 개면 for 문으로

3.  여러 개의 자료를 for문으로 뿌렸을 때 div 태그가 하나여도 여러 개로 나옴 (페이지 소스보기로 창을 열었을 때)

 

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image13.png)

 

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image14.png)

 

### Template 상속

 

 

### Template CSS 적용 (static 정적 파일)

1.  static 밑에 css 파일 생성

2.  blog.css 파일

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image15.png)

 

### Bootstrap : HTML, CSS, JS 클라이언트 사이드 (웹 프레임워크)

1. CDN (Content Delivery Network) 서비스

   > 네트워크에서 컨텐츠 정보를 땡겨와서 HTML, CSS, JS를 쓸 수 있다.

2.  link 태그와 rel 속성 값을 주고 URL를 적어주면 된다.

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image16.png)

 

 