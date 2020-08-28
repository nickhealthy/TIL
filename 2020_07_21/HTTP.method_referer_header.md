# # 200721

- http.method

- referer header

- httpbin.org : http 정보 알 수 있는곳

 

### http.method

1. GET : URL의 ?를 기준으로 뒤에 쿼리 문자열(key = value) 이 올 수 있다.
   : https://api.github.com/search/repositiories?q=java

> 1. 내가 원하는 정보를 보낸다. = URL 화면 창에서 볼 수 있다.
>
> 2. URL 사이즈에 제한이 있다.
>
> 3. 속성이 parmars

![img](https://github.com/nickhealthy/TIL/blob/master/2020_07_21/clip_html0_image1.png)

2. POST : Body에 데이터를 추가해서 보낸다. 

> 1. postman chrome = get, post 연습
> 2. URL 사이즈에 제한이 없다 = form-data 에 저장
> 3. Attribute(속성)이 data=, files = 

![img](https://github.com/nickhealthy/TIL/blob/master/2020_07_21/clip_html0_image2.png)

 

### Referer

* Referer라는 header값 설정 안해주면 URL 주소를 알아도 참조할 수가 없다. 