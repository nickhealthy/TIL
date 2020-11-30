# Today I Learned

* LEFT JOIN
* INNER JOIN
* FULL OUTER JOIN
* EXCLUSIVE JOIN
* 마무리 및 정리

---

[참고하면 좋은 사이트](https://sql-joins.leopard.in.ua/)

## LEFT JOIN

* 왼쪽 테이블에는 데이터가 있고, 오른쪽에는 데이터가 없을 때 오른쪽 테이블 값에는 `NULL` 값이 자동으로 들어가게 됨
* **관계형 데이터 베이스 안에 테이블은 하나의 주제만 들어가게 나눈다.**
  * 하지만 주제를 나누는 것이 쉬운 작업은 아님, 주로 값이 많이 겹칠 때 사용

#### 실습

* 테이블 `topic`, `author`, `profile` 생성

![1. DatabaseTables](https://github.com/nickhealthy/TIL/blob/master/2020_12_01/img/1.%20DatabaseTables.PNG)



* 테이블 두개 합치기

```mysql
SELECT * FROM topic LEFT JOIN author ON topic.author_id = author.aid
```

![2. LEFTJOIN](https://github.com/nickhealthy/TIL/blob/master/2020_12_01/img/2.%20LEFTJOIN.PNG)



* 테이블 세개 합치기

```mysql
SELECT * FROM topic LEFT JOIN author ON topic.author_id = author.aid LEFT JOIN profile ON profile_id = profile.pid
```

![3. LEFTJOIN](https://github.com/nickhealthy/TIL/blob/master/2020_12_01/img/3.%20LEFTJOIN.PNG)



* `Alias`(별칭) 및 해당 **테이블에서 원하는 컬럼만** 사용하기

```mysql
SELECT tid, topic.title, author_id, name, profile.title AS job_title FROM topic LEFT JOIN author ON topic.author_id = author.aid LEFT JOIN profile ON author.profile_id = profile.pid;
```

![4. LEFTJOIN](https://github.com/nickhealthy/TIL/blob/master/2020_12_01/img/4.%20LEFTJOIN.PNG)



* 해당 사용자만 가져오기

```mysql
# author.aid (사용자 정보가 들어있는 테이블)의 프라이머리 키로 WHERE 절에 조건을 걸음
SELECT tid, topic.title, author_id, name, profile.title AS job_title FROM topic LEFT JOIN author ON topic.author_id = author.aid LEFT JOIN profile ON author.profile_id = profile.pid WHERE aid = 1;
```

![5. LEFTJOIN](https://github.com/nickhealthy/TIL/blob/master/2020_12_01/img/5.%20LEFTJOIN.PNG)



## INNER JOIN

* 양쪽 모두에만 존재하는 값을 이용해서 새로운 테이블을 만듬
* `NULL`행이 존재하지 않음

```mysql
SELECT * FROM topic INNER JOIN author ON topic.author_id = author.aid;
```

![6. INNER JOIN](https://github.com/nickhealthy/TIL/blob/master/2020_12_01/img/6.%20INNER%20JOIN.PNG)



```mysql
SELECT * FROM topic INNER JOIN author ON topic.author_id = author.aid INNER JOIN profile ON profile.pid = author.profile_id;
```

![7. INNER JOIN](https://github.com/nickhealthy/TIL/blob/master/2020_12_01/img/7.%20INNER%20JOIN.PNG)



## FULL OUTER JOIN

* 차이점을 비교할 때 좋음
* 왼쪽, 오른쪽 테이블 모두한테(교집합) 있는 값이 나옴 + 왼쪽에만 있는 값, 오른쪽에만 있는 값도 나옴
  * 값이 왼쪽 테이블이든 오른쪽 테이블이든 없다면, `NULL` 값을 출력하게 됨
* 중복된 값은 지움

※ 많은 데이터베이스 시스템에서 `FULL OUTER JOIN`를 지원하지 않지만, 

**LEFT 조인**과 **RIGHT 조인**를 합쳐서 중복을 제거하면 가능함. 그때 합치고 중복된 값을 지워주는 명령어는 `UNION`

* UNION 뒤에 `DISTINCT` 는 생략 가능

```mysql
(SELECT * FROM topic LEFT JOIN author ON topic.author_id = author.aid) UNION (SELECT * FROM topic RIGHT JOIN author ON topic.author_id = author.aid);
```

![8. FULL OUTER JOIN](https://github.com/nickhealthy/TIL/blob/master/2020_12_01/img/8.%20FULL%20OUTER%20JOIN.PNG)



## EXCLUSIVE JOIN

* 배제하는 것 = 특정 테이블에만 있는 것을 가져올 수 있음
  * 차집합 개념
* TIP : 상대 열에 `NULL`이 존재하는 행을 기준으로 조건을 잡으면 됨
  *  `=` NULL 값을 제외하고 모두 사라지게 하면 됨

```mysql
SELECT * FROM topic LEFT JOIN author ON topic.author_id = author.aid WHERE author.aid is NULL;
```

![9. EXCLUSIVE JOIN](https://github.com/nickhealthy/TIL/blob/master/2020_12_01/img/9.%20EXCLUSIVE%20JOIN.PNG)



## 마무리 및 정리

![10. 마무리](https://github.com/nickhealthy/TIL/blob/master/2020_12_01/img/10.%20%EB%A7%88%EB%AC%B4%EB%A6%AC.PNG)



#### 성능상의 병목현상 찾는법

`explain` 명령어를 앞에 붙여서 찾을 수 있음



#### 분해를 잘해놔야 결합하는 방법도 쉬워진다.

어떻게 분해하는게 좋을지는 데이터베이스 `modeling` or ` normalization`(정규화)를 통해서 공부하자.



## 연습한 DB 테이블 쿼리

```mysql
DROP TABLE IF EXISTS `author`;
CREATE TABLE `author` (
  `aid` int(11) NOT NULL,
  `name` varchar(10) DEFAULT NULL,
  `city` varchar(10) DEFAULT NULL,
  `profile_id` int(11) DEFAULT NULL,
  PRIMARY KEY (`aid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
INSERT INTO `author` VALUES (1,'egoing','seoul',1),(2,'leezche','jeju',2),(3,'blackdew','namhae',3);

DROP TABLE IF EXISTS `profile`;
CREATE TABLE `profile` (
  `pid` int(11) NOT NULL,
  `title` varchar(10) DEFAULT NULL,
  `description` tinytext,
  PRIMARY KEY (`pid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
INSERT INTO `profile` VALUES (1,'developer','developer is ...'),(2,'designer','designer is ..'),(3,'DBA','DBA is ...');

DROP TABLE IF EXISTS `topic`;
CREATE TABLE `topic` (
  `tid` int(11) NOT NULL,
  `title` varchar(45) DEFAULT NULL,
  `description` tinytext,
  `author_id` varchar(45) DEFAULT NULL,
  PRIMARY KEY (`tid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
INSERT INTO `topic` VALUES (1,'HTML','HTML is ...','1'),(2,'CSS','CSS is ...','2'),(3,'JavaScript','JavaScript is ..','1'),(4,'Database','Database is ...',NULL);
```

