# Today I Learend

* JavaScript 함수 정의
  * 함수 표현식 방식으로 함수를 생성 ⇒ 함수 리털러(function키워드)로 함수를 만들고, 생성된 함수를 변수에 할당
  * `Function()` 생성자 함수를 이용한 함수 생성
  * 함수 호스팅(function hosting)
* 함수 종류
  * 콜백 함수 (callback function)
  * 즉시 실행 함수 (immediate function)
  * 함수를 반환하는 함수
  * 내부 함수 (inner function)
* arguments 객체
* 함수 호출 패턴과 `this` 바인딩
  * 함수가 호출되는 방식(호출 패턴)에 따라 this가 다른 객체를 참조
* JavaScript `$`, `!!`
* Storage (저장소)
* html 태그 및 css 선택자
* 10/21 이어서 계속 (Auth0) 사용자 인증
* HTTP Origin (교차 기원 요청)
  * SOP 정책
* CORS (Cross Origin Resource Sharing : 교차 기원 자원 공유)

---



### JavaScript 함수 정의

* 함수 선언문 (function statement)
  * 함수 선언문으로 정의한 함수는 **외부에서 호출이 가능**하도록, 자바스크립트 엔진에 의해서 **함수 이름과 함수 변수 이름이 동일한 기명 함수 표현식**으로 변경

```javascript
function add(x, y) { return x + y; }
	⇓
var add = function add(x, y) { return x + y; }
```

* 함수 표현식 (function expression) 
  * 익명 함수 표현식
  * 기명 함수 표현식
    * 함수 표현식에서 사용된 함수 이름은 **외부 코드에서 접근이 불가능**
    * 함수 내부에서 해당 함수를 재귀적으로 호출할 때 또는 디버깅 할 때 사s용
* Function() 생성자 함수

![1. 자바스크립트 함수정의](https://github.com/nickhealthy/TIL/blob/master/2020_10_22/img/1.%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%20%ED%95%A8%EC%88%98%EC%A0%95%EC%9D%98.PNG)



#### 함수 표현식 방식으로 함수를 생성 ⇒ 함수 리털러로 함수를 만들고, 생성된 함수를 변수에 할당

* JavaScript에서는 함수도 하나의 값으로 취급

```javascript
var x = 1;   			// x라는 변수에 1을 할당
var y = 2;				// y라는 변수에 2를 할당

// add라는 변수에 매개변수로 전달되는 두수를 더한 값을 반환하는 익명함수를 할당
var add = function(x, y) {   
	return x + y; 
};

// 변수처럼 다른 변수에 재할당
var sum = add;
console.log(add(3, 4));	// 7
console.log(sum(3, 4));	// 7
```

* 기명 함수 표현식
  * 함수 표현식에서 사용된 함수 이름은 **외부 코드에서 접근이 불가능**
  * 함수 내부에서 해당 함수를 재귀적으로 호출할 때 또는 디버깅 할 때 사용

```javascript
var add = function sum(x, y) { return x + y; };

console.log(add(3, 4));     // 7
console.log(sum(3, 4));     // sum is not defined
```



#### Function() 생성자 함수를 이용한 함수 생성

* [Function() 기본 내장 생성자 함수](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function)
* 함수 선언문, 함수 표현식 방식도 내부적으로는 **Function() 생성자 함수를 이용해서 생성**

new Function ([arg1[, arg2[, ...argN]],] functionBody)

```javascript
var add = new Function('x', 'y', 'return x + y');
console.log(add(3, 4));
```



#### 함수 호스팅(function hosting)

* **함수 선언문 형태**로 정의한 **함수는 함수의 유효 범위가 코드의 맨 처음부터 시작**
  * 함수를 정의한 위치와 관계 없이 호출이 가능

```javascript
// 함수 선언문 형태는 어디에서든 호출 가능
console.log(add(1, 2));     // 3
 
function add(x, y) { 
    return x + y;
}
 
console.log(add(3, 4));     // 7
```

* 함수 호이스팅이 발생하는 원인(이유)
  * JavaScript의 변수 생성(instantiation)과 초기화(initialization) 작업이 분리되어 진행되기 때문

---



### 함수 종류

#### 콜백 함수 (callback function)

* 개발자가 명시적으로 코드를 통해 호출하는 함수가 아니고, 개발자는 함수를 등록만 하고, **이벤트가 발생했을 때 또는 특정 시점에 도달했을 때** 시스템에서 호출하는 함수
* 특정 함수의 인자로 넘겨서 코드 내부에서 호출되는 함수
  * 대표적인 예시 : 이벤트 핸들러

#### 즉시 실행 함수 (immediate function)

* 함수를 **정의함과 동시에 바로 실행하는 함수**
* **최초 한번**의 실행만을 필요로하는 **초기화 구문에 사용**
* **함수 리터럴을 괄호**로 둘러싼 후 함수가 **바로 호출될 수 있도록 괄호를 추가**

```javascript
(function add(x, y) {
    console.log(x + y);
})(3, 4);

// 한번만 실행하기 때문에 익명 함수를 이용하는 것이 나음
(function (x, y) {
    console.log(x + y);
})(3, 4);
 
(function add(x, y) {
    console.log(x + y);
}(3, 4));
 
(function (x, y) {
    console.log(x + y);
}(3, 4));
```

#### 함수를 반환하는 함수

```javascript
var self = function() {
    console.log('a');
 
    return function() {
        console.log('b');
    };
};
 
self();         // a
console.log('------------');
self = self();  // a
self();         // b
```

#### 내부 함수 (inner function)

* 함수 내부에 정의된 함수

```javascript
function parent() {
    var a = 100;
    var b = 200;
 
    function child() {
        var b = 300;
 
        console.log(a);     // 100
        console.log(b);     // 300
    }
 
    child();
}
 
parent();
child();                    // child is not defined
```

* 부모 함수에서 내부 함수를 **외부로 반환하면(return)**, 부모 함수 밖에서도 내부 함수를 호출이 가능

```javascript
function parent() {
    var a = 100;
    var b = 200;
 
    return function child() {
        var b = 300;
 
        console.log(a);     // 100
        console.log(b);     // 300
    };
}
 
var inner = parent();
inner();                    
// 클로져(closure)                               
//https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Closures
// 실행이 끝난 parent()와 같은 부모 함수 스코프의 변수를 참조하는 함수
```

---



### arguments 객체

```javascript
function add(x, y) {
    console.dir(arguments);
    return x + y;
}
 
console.log(add(1, 2));     // 3
console.log(add(1, 2, 3));  // 3    인자 개수가 초과하면 -> 무시
console.log(add(1));        // NaN  인자 개수가 모자라면 -> undefined 값이 할당
```

* 함수가 호출될 때 파라미터(인자)와 함께 암묵적으로 **arguments 객체**가 함수 내부로 전달
* arguments 객체
  * 함수를 호출할 때 넘긴 파라미터를 **배열 형태**로 저장한 객체
    *  실제 배열이 아닌 유사 배열 객체
  * **파라미터의 개수가 정확하게 정해지지 않은 함수**를 구현할 때 또는 **파라미터의 개수에 따라서 처리를 다르게 구현해야 하는 함수를 정의할 때 사용**

```javascript
function sum() {
    var result = 0;
    // arguments 객체를 이용
    for (var i = 0; i < arguments.length; i ++) {
        result += arguments[i];
    }
    return result;
}
 
console.log(sum(1));                // 1
console.log(sum(1, 2));             // 3
console.log(sum(1, 2, 3, 4, 5));    // 15
```

---



### 함수 호출 패턴과 this 바인딩

* 자바스크립트에서 함수를 호출하면 **파라미터와 함께 arguments 객체와 this 인자가 함수 내부로 암묵적으로 전달**

* 함수가 호출되는 방식(호출 패턴)에 따라 this가 다른 객체를 참조



#### 객체의 메서드를 호출할 때 this 바인딩

* 객체의 메서드 = 객체의 property가 함수인 경우 ⇒ 해당 함수를 메서드라고 함

* **메서드 내부 코드에서 사용된 this** ⇒ 해당 메서드를 호출한 객체를 참조(바인딩)

```javascript
var myObj = {
    name: 'crpark', 
    sayName: function() {
        console.log(this.name);
    }
};
 
var otherObj = {
    name: 'other'
};
 
otherObj.sayName = myObj.sayName;
 
myObj.sayName();        // crpark
otherObj.sayName();     // other
```



#### 함수를 호출할 때 this 바인딩

* 함수 내부 코드에 사용된 this는 **전역 객체**에 바인딩
  * 전역 객체
    * 브라우저 → windows 객체
    * node.js → global 객체

```javascript
var text = "This is JavaScript";
console.log(text);          // This is JavaScript
console.log(window.text);   // This is JavaScript
console.dir(window);
 
var say = function() {
    console.log(this);      // Window {window: Window, self: Window, document: document, name: "", location: Location, …}
    console.log(this.text); // This is JavaScript
};
say();
```

* 객체 메서드 안에 내부 함수(inner function)의 this는 **전역 객체 or 변수에 바인딩**

```javascript
var value = 100;
 
var myObject = {
    value: 1,
    func1: function() {
        this.value += 1;
        console.log(`func1() called. this.value = ${this.value}`);          // #1 → 2
 
        func2 = function() {
            this.value += 1;
            console.log(`func2() called. this.value = ${this.value}`);   // #2 → 101
 
            func3 = function() {
                this.value += 1;
                console.log(`func3() called. this.value = ${this.value}`);// #3 → 102
            }
            func3();
        }
        func2();
    }
};
myObject.func1();
```

* #1 (object method) ⇒ myObject 객체의 메서드 코드에 포함된 this 
  * **호출한 객체에 바인딩되므로** myObject.value property 값을 증가

* #2, #3 (inner function) ⇒ func1 함수 내부에 정의된 func2 함수와 func2 함수 내부에 정의된 func3 함수 내부 코드에서 사용하는 this
  * **글로벌 객체를 참조**하므로 글로벌 변수로 정의(var value = 100;)된 값이 증가
* \#2와 #3에서 사용하는 **this를 myObject를 참조하도록 하고 싶을 땐**??? 
  * 객체를 나타내는 **this를 값을 가지는 변수 that** (단순한 변수 이름)을 정의

```javascript
var value = 100;
 
var myObject = {
    value: 1,
    func1: function() {
        // 객체에 포함된 property를 참조하기 위해서 씀 <> 전역이 아닌 것을 참조하지 않기 위해
        var that = this;	// = var _this = this;
        this.value += 1;    
        console.log(`func1() called. this.value = ${this.value}`);          // 2
 
        func2 = function() {
            that.value += 1;
            console.log(`func2() called. this.value = ${that.value}`);      // 3
 
            func3 = function() {
                that.value += 1;
                console.log(`func3() called. this.value = ${that.value}`);  // 4
            }
            func3();
        }
        func2();
    }
};
myObject.func1();
```

* 객체는 프로그램이 커질 때 함수의 호스팅(function hosting)등으로 인해 변수, 혹은 함수가 겹쳐져 프로그램의 오류가 발생할 수 있으므로 주로 소스코드의 이름과 동일한 이름으로 객체 및 메서드를 만들어서 사용함

  * 장점

    1. 같은 이름으로 변수 or 함수를 지정해도 객체 안해서 관리되기 때문에 충돌이 안일어남
    2. 코드의 재사용성이 증가


---



### JavaScript $, !!

* `$`는 `jQuery`와 똑같음

```javascript
<script src="//ajax.googleapis.com/ajax/libs/jquery/1.11.2/jquery.min.js"></script>
<script>
	console.log(window);		// 글로벌 객체인 window에 jQuery, $ 가 등록된 것을 확인
</script>
```

```javascript
<script>
 
    //  HTML 문서가 준비되면 매개변수로 전달된 콜백 함수를 실행
    window.onload = () => {
        console.log("#1");
    };
 
    jQuery(document).ready(function() {
        console.log("#2");
    });
 
    $(document).ready(function() {
        console.log("#3");
    });
 
    jQuery(function() {
        console.log("#4");
    });
 
    $(function() {
        console.log("#5");
    });
    
</script>
```



#### !! (not not)

값이 없는 (null) 형태를 boolean 타입으로 만들어서 false 를 출력할 수 있도록 논리 연산을 사용

* x = !!x;	⇒ x가 true이면 x는 true가 되고, **x가 false이거나 undefine이면 x는 false가 됨**

---



### Storage (저장소)

HTML5 스펙에 추가된 개념

일정 규모의 데이터를 저장할 수 있도록 만들어 놓은 개념

![3. storage 저장소](https://github.com/nickhealthy/TIL/blob/master/2020_10_22/img/3.%20storage%20%EC%A0%80%EC%9E%A5%EC%86%8C.PNG)

![2. storage 확인](https://github.com/nickhealthy/TIL/blob/master/2020_10_22/img/2.%20storage%20%ED%99%95%EC%9D%B8.PNG)

---



### html 태그 및 css 선택자

![4. html 태그 및 css 선택자](https://github.com/nickhealthy/TIL/blob/master/2020_10_22/img/4.%20html%20%ED%83%9C%EA%B7%B8%20%EB%B0%8F%20css%20%EC%84%A0%ED%83%9D%EC%9E%90.PNG)

---



### 10/21 이어서 계속 (Auth0) 사용자 인증

* Auth0 lock를 통해 로그인(인증)에 성공했을 때 반환되는 값
  * accessToken을 localStorage에 저장

![5. accessToken을 localStorage에 저장](https://github.com/nickhealthy/TIL/blob/master/2020_10_22/img/5.%20accessToken%EC%9D%84%20localStorage%EC%97%90%20%EC%A0%80%EC%9E%A5.PNG)

* **로그인한 사용자 정보를 조회할 때** localStorage에 저장된 access token을 authentication **요청 헤더를 통해서 전달**

![6. access token을 authentication 요청 헤더를 통해서 전달](https://github.com/nickhealthy/TIL/blob/master/2020_10_22/img/6.%20access%20token%EC%9D%84%20authentication%20%EC%9A%94%EC%B2%AD%20%ED%97%A4%EB%8D%94%EB%A5%BC%20%ED%86%B5%ED%95%B4%EC%84%9C%20%EC%A0%84%EB%8B%AC.PNG)

* 사용자 프로필 정보를 반환

![7. 사용자 프로필정보를 반환](https://github.com/nickhealthy/TIL/blob/master/2020_10_22/img/7.%20%EC%82%AC%EC%9A%A9%EC%9E%90%20%ED%94%84%EB%A1%9C%ED%95%84%EC%A0%95%EB%B3%B4%EB%A5%BC%20%EB%B0%98%ED%99%98.PNG)

* auth0.com 인증 내역 확인

![8. auth0 인증 내역 확인](https://github.com/nickhealthy/TIL/blob/master/2020_10_22/img/8.%20auth0%20%EC%9D%B8%EC%A6%9D%20%EB%82%B4%EC%97%AD%20%ED%99%95%EC%9D%B8.PNG)

#### Auto0 서비스를 이용하지 않을 시

* 클라이언트ID와 키를 입력해주고, 타 **SNS 인증 API를 받아와서 연동해줌**
* Auto0에서 **인증 정보들에 대한 기록**들을 직접 남기는 코드를 짜야함

---



### 웹 Origin (교차 기원 요청)

웹 철학 = 공유

* **교차 기원 요청이 가능**

* origin = 기원 = 출처

  * 기원 = **스킴(프로토콜) + 호스트명 + 포트번호**

  ```
  http://www.example.com:8888/abc.html
  http://www.sample.com:8080/xyz.html  	⇒ 기원이 다르다.
  ```

![9. origin(기원)](https://github.com/nickhealthy/TIL/blob/master/2020_10_22/img/9.%20origin(%EA%B8%B0%EC%9B%90).PNG)

#### HTTP 요청은 기본적으로 교차 기원 요청이 가능

```html
<html>
    <head></head>
    <body>
        <img src="https://s.pstatic.net/shopping.phinf/20201015_8/65c73d10-71c2-4c20-a416-ed94184f1456.jpg?type=f214_292" />
        <img src="tile.png" />
 
        <script src="//ajax.googleapis.com/ajax/libs/jquery/1.11.2/jquery.min.js"></script>
        <script src="https://pm.pstatic.net/dist/lib/search.jindo.20200326.js"></script>
    </body>
</html>
```

* 기원이 다른 js 파일과 이미지 파일이 서비스 (스크립트 파일은 다운로드되어서 실행되고, 이미지 파일은 다운로드되어서 화면에 출력)
* 위의 예시에서 나온 목록
  * http://127.0.0.1:8000/
  * https://s.pstatic.net
  * //ajax.googleapis.com
  * https://pm.pstatic.net

#### 스크립트 내에서의 교차 기원 요청은 보안 상의 이유로 제한

⇒ Ajax = XMLHttpRequest 객체를 이용한 비동기 통신

* **SOP(Same Origin Policy: 동일 기원 정책, 동일 출처 정책)** 
  * 브라우저가 메모리에 못올라가게 제어(막음)
    * ex) 127.0.0.1에서 orign.com/test.jpg 호출시 도메인이 달라서 jpg 이미지가 차단돼서 보이지 않음
* XMLHttpRequest 객체을 사용해서 가져오는 리소스는 해당 웹 애플리케이션과 **동일 기원으로 제한**

![10. SOP(교차 기원 제한)](https://github.com/nickhealthy/TIL/blob/master/2020_10_22/img/10.%20SOP(%EA%B5%90%EC%B0%A8%20%EA%B8%B0%EC%9B%90%20%EC%A0%9C%ED%95%9C).PNG)

---



### CORS (Cross Origin Resource Sharing : 교차 기원 자원 공유)

* 서비스가 점점 커지고, 거대해지면서 필요한 API 등 다른 기원에서 데이터를 받아오는 (비동기 통신 등)이 필요한 경우 SOP가 제한되어 있으면 서비스를 정상적으로 작동하기 힘듬
  * **SOP 완화 정책 필요= CORS**
    * 브라우저가 **XMLHttpRequest로 교차 기원 요청이 가능**하도록 해주는 것

* **Access-Control-Allow-Orgin 응답 헤더를 이용해서 자원 사용 여부를 허가함** (다른 도메인, 서버 간에)
  * 콘텐츠를 제공해주는 사이트에서 우리의 **Access-Control-Allow-Orgin를 넣어주면 사용이 가능**
  * 범용적으로 풀어주고 싶으면 * 를 넣어줌 (보안적으로 올바른 방법 X)

![11. CORS(SOP완화 정책)](https://github.com/nickhealthy/TIL/blob/master/2020_10_22/img/11.%20CORS(SOP%EC%99%84%ED%99%94%20%EC%A0%95%EC%B1%85).PNG)

---


