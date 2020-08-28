# # 200728

 

- employee 연습문제

  > DML 개념알기
  >
  > DDL 개념알기

- Primary key and Foreign Key

- SQL의 표준 = Ansi SQL

- 서브쿼리 종류

  > 스칼라 서브쿼리(표현 용도) : SELECT 절에 적어준다.
  >
  > 인라인 뷰 (테이블 대체 용도) FROM 절에 적어준다.
  >
  > 일반 서브쿼리 : (서브쿼리의 결과에 따라 달라지는 조건절)

- Pymysql와 MariaDB연동 >> SQL 쿼리문을 프로그래밍

- 팟빵 다운로드 / 팟빵 다운로드 함수로 구현

 

### 개념

- MySQL은 데이터를 검색할 때 첫 필드부터 차례대로 검색한다.

- 인덱스 정보 보기 : SHOW INDEX FROM ‘table’

  > 결과가 출력되지 않는다. >> in 구문은 null 값을 포함한 값을 비교하지 못하기 때문

- commit()

  > 프로그램 툴에서 쿼리문을 자료를 저장할땐 commit 를 항상 날려서 DB에 반영시켜야 한다.
  >
  > DBMS 툴을 이용하면 commit 명령행 안써도 자동으로 commit <> 프로그래밍은 commit

 

### CREATE INDEX

- CREATE INDEX idx_emp ON emp ( deptno );

  > 인덱스 key name 부여 = idx_emp

```mariadb
ALTER TABLE emp ADD CONSTRAINT fk_emp_dept FOREIGN KEY ( deptno ) REFERENCES dept( deptno ) ON DELETE NO ACTION ON UPDATE NO ACTION;

# 중복되는걸 없앰
```

 

### Primary Key and Foreign Key

- Primary Key : NULL 값 허용x, 컬럼의 각 row 마다 유일한 값이어야 한다. ex) 사원번호 등

- Foreign Key : 한 테이블의 field = 속성을 참조(식별) 할 수 있는 키

 

### SQL / Script 의 표준

* Ansi SQL (표준SQL)

* ECMA(단체이름) << Script의 표준

 

### SQL, P/L 연동

- java = JDBC(java database connectivity), MyBatis 프레임워크, JPA(Java Persistence API)

  > JPA(Java Persistence API) << sqlalchemy(쿼리문 작성안해도 알아서 테이블 생성) 같은거

 

- python – pymysql, sqlalchemy, cx_Oracle, django(ORM 포함 = sqlalchemy 기능 지원)

 

 

### python itertools count : count 를 1부터 샘