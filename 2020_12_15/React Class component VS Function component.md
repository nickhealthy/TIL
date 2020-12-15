# Today I Learned

* React class vs function style

---



## React class vs function style

React function 스타일에서는 `Props`만 사용이 가능했지만 `React HOOK`의 기능 도입으로 함수형 컴포넌트도 `State` (내장 데이터, 상태변화)를 사용할 수 있게 되었음

### Props

부모에서 자식으로 값을 넘겨 받을 때 사용

#### Class

`ClassComp.jsx`

```react
class ClassComp extends React.Component {
	render() {
		return (
			<div className="container">
			  <p>Number : {this.prorps.initNumber></p>
			</div>
		)
	}
}
```

#### Function

`FuncComp.jsx`

```react
function FuncComp(props) {
  return (
    <div>
      <p>Number : {prorps.initNumber></p>
    </div>
  )
```



## State

**State의 상태를 바꿀 때 사용하는 함수**

* 클래스형 컴포넌트에서는 `this.setState()` 함수 이용
* 함수형 컴포넌트에서는 `useState()` 함수 이용
  * `useState()`를 이용하면 무조건 배열 형태로 return 해주는데,
  * 첫번째 인자는 **초기값**
  * 두번째 인자는 **this.setState() 와 동일한 상태 변화 함수(사용자정의)를 가짐**

#### Class

`ClassComp.jsx`

```react
class ClassComp extends React.Component {
	state = {
		number: this.prorps.initNumber
	}
	render() {
		return (
			<div className="container">
			  <p>Number : {this.state.number></p>
              <input type="button" value="random" onClick={
                              function(){
                                  this.setState({number:Math.random()})
                              }.bind(this)
                          }
              </input>
			</div>
		)
	}
}
```

#### Function

**State의 사용과 값을 넘겨 받으려면 함수 첫번째 인자로 받을 수 있다.**

※ 첫번째 인자의 변수명은 아무거나 지정해도 사용가능 하지만 대부분 `props`로 지정

```react
function FuncComp(props) {
  const [number, setNumber] = useState(props.initNumber);
  return (
   <div className="container">
			  <p>Number : {number}></p>
              <input type="button" value="random" onClick={
                              function(){
                                  setNumber(Math.random())
                              }
                          }
              </input>
	</div>
  )
```



## 생명주기 Life cycle

[참고 사이트](https://m.blog.naver.com/aim4u/221901085338)

![ReactJS-components-life-cycle](https://github.com/nickhealthy/TIL/blob/master/2020_12_15/img/ReactJS-components-life-cycle.png)

#### 클래스형 컴포넌트에는 다양한 생명주기 함수가 있음 (이외 더 있음)

##### 다음의 내용은 render()가 호출 되었을 때 "초기값을 어떻게 설정할지 셋팅하는 것"

1. `componentWillMount()` : 컴포넌트가 생성되기 전에, 즉 처음으로 `render()` 함수가 호출 되기 전에 마운트(해야할 작업)
2. `componentDidMount()` :  컴포넌트가 생성된 후,즉 처음으로 `render()` 함수가 호출되고 마운트(해야할 작업)

#### 함수형 컴포넌트 생명주기 : `useEffect()` 사용

※ 첫번째 인자에는 **함수가 들어가야함**

※ 두번째 인자에는 변한 값에(`state`) 대해서만 처리하는 것 - render()호출이 가능

> ex) [number, _date] 배열 안에 넣은 값들만 추적
>
> [] 선언을 하지 않으면 모든 변환 값들을 추적
>
> [] 선언만 해놓고 비어두면 최초 1회만 실행 = `componentDidMount()` 

* 기본값은 `componentDidMount()` + `componentDidUpdate()`와 동일
  * **`render()` 호출 이후에 호출이 되는 것**
  * 상태 변화시 `componentDidUpdate()`의 역활로 render()호출 이후 계속 호출이 됨
* 복수개를 설치할 수 있음



##### 다음의 내용은 render()가 이루어지고 난 후, "해야할 일을 설정하는 것"(state가 변경되어 render()가 호출될 때마다 계속 실행)

1. `shouldComponentUpdate(prevProps, prevState)` : 초기에 render() 함수가 호출된 이후에 다음 상태 변경 후(state) render()를 재호출 하기전에 해야할 작업

2. `componentDidUPdate(prevProps, prevState)` : 초기에 render()함수가 호출된 이후에 다음 상태 변경 후(state) render()를 재호출 한 이후 해야할 작업



#### Clean up : 일종의 정리정돈

##### 다음의 내용은 render()가 이루어지고 난 후, 다음 State 변경 시 초기화 및 새로운 작업을 정의 및 진행하는 것

`useEffect()`에 `return` 값으로 **함수를 넘겨 주면 됨**

프로세스 구조

1. 초기에 render() 호출이 이루어짐
2. useEffect() 함수 안에 정의한 함수가 실행
3. 새로운 이벤트 발생 및 state의 변경으로 render()가 변경
4. **2번 항목의 useEffect()함수 안의 return으로 담아 놓은 함수가 실행**
5. 2번 항목의 함수가 재 실행



`FuncComp.jsx` 함수 컴포넌트의 생명주기

```react
// 컴포넌트가 실행될 때 최초 1회만 실행 - componentDidMount
  // [] 빈배열
  useEffect(function () {
    console.log(
      '%cfunc => useEffect (componentDidMount)' + ++funcId,
      funcStyle
    );
    document.title = number + ' : ' + _date;
    return function () {
      console.log(
        '%cfunc => useEffect return (componentWillUnMount)' + ++funcId,
        funcStyle
      );
    };
  }, []);

  // number 상태변화에 대해서만 - componentDidMount & componentDidUpdate
  // [number]
  useEffect(
    function () {
      console.log(
        '%cfunc => useEffect number (componentDidMount & componentDidUpdate)' +
          ++funcId,
        funcStyle
      );
      document.title = number + ' : ' + _date;
      return function () {
        console.log(
          '%cfunc => useEffect number return (componentDidMount & componentDidUpdate)' +
            ++funcId,
          funcStyle
        );
      };
    },
    [number]
  );

  // _date 상태변화에 대해서만 - componentDidMount & componentDidUpdate
  // [_date]
  useEffect(
    function () {
      console.log(
        '%cfunc => useEffect _date(componentDidMount & componentDidUpdate)' +
          ++funcId,
        funcStyle
      );
      document.title = number + ' : ' + _date;
      return function () {
        console.log(
          '%cfunc => useEffect _date return (componentDidMount & componentDidUpdate)' +
            ++funcId,
          funcStyle
        );
      };
    },
    [_date]
  );
```



---

