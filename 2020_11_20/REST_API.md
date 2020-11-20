# Today I Learned

* REST API

---



## REST API

**HTTP를 이용해서 기계들이 통신할 때 HTTP가 가진 잠재력을 최대한 이끌어 내기 위한 기술**

**URI를 통해서 표현됨**

#### Resource

* 데이터를 그 자체를 지칭하는 것

#### Collection

* 토픽 전체 또는 여러개를 표현하는 것

#### Element

* `Collection` 하나하나의 데이터를 `Element` 라고 불림



**URI 그 자체로는 아무것도 할 수 있는것이 없음 = 데이터를 식별하는 것에 불과**

* 데이터(정보)를 가공할 줄 알아야 함



### 정보에 대한 가공 방법

* 총 4가지 CRUD = method라고 불림

  * Create : 생성
    * POST
  * Read : 읽기
    * GET
  * Update : 수정
    * PUT, PATCH
  * Delete : 삭제
  * DELETE 



### 개발자도구에서 확인하는 방법

#### Request Headers

클라이언트가 서버에게 알려주는 것

```
POST /topics HTTP/1.1
# POST : 메서드 방식
# /topics : 리소스

# 실제 데이터 내용
Requests payload 에서 확인

# 데이터 타입
content-type 에서 확인 : application/json

```

#### Response Headers

서버가 클라이언트에게 알려주는 것

```
201 CREATED
# 201 상태 코드 = 생성됬다.
```



### REST API 가 규정하는 것 vs 규정하지 않는 것

#### 규정하는것

* 리소스를 식별할 땐 **URI** 를 통해서 식별함

* 어떤 행위를 할땐 http의 고유한 **method**를 사용함 (POST, GET etc..)

#### 규정하지 않는 것

* 어떤 데이터 타입으로 통신할 것인지 규정하지 않음 ex) XML, JSON



### CRUD 방법

* 읽기
  * 전체 읽기 : Collection 전체를 조회할 수 있음 
    * ex : (get / topics)
  * 부분 읽기 : Element 각각의 객체를 조회할 수 있음
    * ex : (get / topics/2) : 식별자를 부여
* 수정
  * 전체 수정 : PUT
    * ex : PUT /topics/2 
  * 부분 수정 : PATCH
    * ex : PATCH /topics/2

* 삭제

  * 전체 삭제 : Collenction를 이용 (하지만 위험한 방법)

  * 부분 삭제 : 식별자를 부여

* 관계

  * 리소스와 리소스가 어떤 관계를 맺고 있을 때 (종속적인 관계일 때)



### REST API 요약

* 기계와 기계가 **HTTP를 이용해서 통신할 때**, 
* **리소스는 URI**로, 
* **행위는 method**로, 
* **결과는 응답 코드**로 HTTP가 원래 가지는 의미를 잘 활용하자는 것


