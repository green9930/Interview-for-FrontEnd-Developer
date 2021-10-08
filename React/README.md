# Index

[1. Virtual DOM](#Virtual-DOM)  
[2. JSX](#JSX)  
[3. 엘리먼트 렌더링](#엘리먼트-렌더링)  
[4. Components and Props](#Components-and-Props)  
[5. state와 Lifecycle](#state와-Lifecycle)  
[6. 이벤트 처리하기](#이벤트-처리하기)  
[7. List와 key](#List와-key)  
[8. form](#form)


---

# Virtual DOM

DOM을 추상화한 자바스크립트 객체. 실제 DOM을 조작하는 것이 아니라 Virtual DOM에 업데이트된 후 실제 DOM과 비교하여 변경된 부분만 업데이트하여 성능을 향상시킬 수 있었다.

## 라이브러리 중 리액트를 사용하는 이유

- Virtual DOM이 가장 잘 구현된 라이브러리이다.
- Flux 패턴 사용 : 리액트는 단방향 데이터 흐름 구조이다.
  - MVC 패턴의 문제점 : 양방향 데이터 흐름은 Model이 업데이트 되면 View에 이 업데이트가 반영되고, View가 Model을 업데이트 할 수도 있다. 소규모 어플리케이션은 괜찮지만 앱의 규모가 커지면 시스템의 복잡도가 커져 성능 저하의 원인이 될 수 있다.
- component 단위 개발 : 재사용과 유지, 보수가 용이하다.

---

# JSX

- 자바스크립트를 확장한 문법으로 React element를 생성하여 리액트에서 뷰를 구현한다.
- 리액트는 '컴포넌트'로 관심사를 분리한다.
- JSX도 표현식이다. 따라서 반복문 등에 사용할 수 있고, 변수 할당, 매개변수 할당, 함수 return문에 사용할 수 있다.


## JSX의 규칙

1. 태그는 반드시 닫혀야 한다.
  ```js
  function App() {
    return (
      <div>
        <p>hello</p>
        <div> /* 에러 발생 */
      </div>
    )
  }
  ```
2. 두 개 이상의 태그는 반드시 하나의 태그로 감싸져야 한다.
  ```js
  function App() {
    /* 감싸는 태그가 없으므로 에러 발생 */
    return 
      <div>
        <p>hello</p>
        <div>
          <button>Click me!</button>
        </div>
      </div>
  }
  ```
  - 단순히 태그를 감싸는 용도라면 Fragment(`<></>`, 브라우저 상에서 별도의 엘리먼트로 나타나지 않는다.)를 사용한다.

## JSX 안에 자바스크립트 값 사용하기

JSX 내부에 자바스크립트 변수를 보여줘야 할 때에는 `{}`로 감싸서 보여준다.

## JSX와 XSS(cross-site-scripting)공격 방지

(참조: https://ddingg.tistory.com/107)

- React DOM은 JSX에 삽입된 모든 값을 렌더링하기 전에 이스케이프하기 때문에 애플리케이션에서 명시적으로 작성되지 않은 내용은 주입되지 않는다. (모든 항목은 렌더링되기 전에 문자열로 변환)  
*escape function : 문자열을 컴퓨터에서 읽을 수 있도록 암호화하는 함수  
*escape 시퀀스: 키보드로 입력할 수 없는 문자를 다른 문자의 조합으로 표현하는 것 (EX. `\n` : 개행 의미)

## JSX는 객체를 표현한다.

Babel은 JSX를 `React.createElement()` 호출로 컴파일한다.  

```js
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```
```js
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```
(위 두 예제는 동일)

---

# 엘리먼트 렌더링

- 엘리먼트(element)는 React 앱의 가장 작은 단위로 화면에 표시할 내용을 기술한다.  
- 엘리먼트는 컴포넌트의 '구성 요소'이다.  
- React 엘리먼트는 '불변객체'이다. 따라서 생성 이후 엘리먼트의 자식이나 속성을 변경할 수 없다. 

React 엘리먼트는 DOM 엘리먼트와 달리 일반 객체이므로 생성이 쉽고, React DOM은 React 엘리먼트와 일치하도록 DOM을 업데이트한다.  

React DOM은 해당 엘리먼트와 그 자식을 이전의 엘리먼트와 비교하여 내용이 변경된 DOM만을 업데이트한다. 

```js
const element = <h1>Hello world!</h1>
```

## DOM에 엘리먼트 렌더링하기

리액트로 구현된 애플리케이션은 일반적으로 하나의 루트 DOM을 갖고 있다. 리액트 엘리먼트를 루트 DOM 노드에 렌더링하려면 `ReactDOM.render()`로 전달한다.

```js
const element = <h1>Hello World!</h1>
ReactDOM.render(element, document.getElementById('root'));
```

---

# Components and Props

- component : 재사용이 가능한 최소 단위. 리액트에서 기능을 캡슐화한 단위 
- 컴포넌트를 통해 UI를 재사용 가능한 개별적인 여러 조각으로 나누고 각 조각을 개별적으로 살펴볼 수 있다. 
- props : 컴포넌트에 전달되는 값

*컴포넌트는 반드시 대문자로 시작해야 한다. 리액트는 소문자로 시작하는 컴포넌트를 'DOM 태그'로 처리한다.

## 컴포넌트 정의 방법

1. 함수
  ```js
  function Welcome(props) {
    return <h1>Hello, {props.name}</h1>;
  }
  ```
2. ES6 클래스
  ```js
  class Welcome extends React.Component {
    render() {
      return <h1>Hello, {this.props.name}</h1>;
    }
  }
  ```

## 컴포넌트 렌더링

```js
const element = <Welcome name="Sara" />;
```

리액트 엘리먼트는 사용자 정의 컴포넌트로도 나타낼 수 있다.  

*컴포넌트 렌더링 예제  
```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

## 컴포넌트 합성

컴포넌트는 return문에서 다른 컴포넌트를 참조할 수 있다.   

```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

## 컴포넌트 추출

컴포넌트를 분리하면 재사용에 용이하다. 반복되는 UI, 구현이 복잡한 UI는 별도의 컴포넌트로 만드는 것이 좋다.

## props는 readonly

리액트의 규칙 "모든 React 컴포넌트는 자신의 props를 다룰 때 반드시 순수 함수처럼 동작해야 한다."  
함수 컴포넌트, 클래스 컴포넌트 모두 컴포넌트의 자체 props를 수정하면 안된다. 리액트는 state를 통해 규칙을 위반하지 않고 값을 변경한다.

---

# state와 Lifecycle

**state**  
컴포넌트 내부에서 선언하며 내부에서 값을 변경할 수 있다.(단 직접 변경은 할 수 없음)

**props vs. state**  
- props : 부모 컴포넌트가 자식 컴포넌트에게 주는 값 (직접 수정 불가)
- state : 컴포넌트 내부에서 선언하며 내부에서 값을 변경할 수 있다. (간접적으로 수정 가능)

## 시계 만들기

```js
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}

function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```
- Clock이 스스로 업데이트 되려면 Clock 컴포넌트에 state가 추가되어야 한다.

### 함수에서 클래스로 변환하기

1. `React.Component`를 확장하는 동일한 이름의 ES6 class를 생성한다.
2. 빈 메서드 `render()`를 추가한다.
3. 함수의 내용을 `render()` 메서드 안으로 옮긴다.
4. `render()` 내용 안에 있는 props를 this.props로 변경한다.
5. 남아있는 빈 함수 선언을 삭제한다.

```js
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```
- `render()` 메서드는 업데이트가 발생할 때마다 호출되지만, 같은 DOM 노드로 `<Clock />`을 렌더링하는 경우 `Clock` 클래스의 단일 인스턴스만 사용된다.

### 클래스에 로컬 state 추가하기

1. `render()` 메서드 안에 있는 `this.props.date`를 `this.state.date`로 변경한다.
  ```js
  class Clock extends React.Component {
    render() {
      return (
        <div>
          <h1>Hello, world!</h1>
          <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
        </div>
      );
    }
  }
  ```
2. 초기 `this.state`를 지정하는 class constructor를 추가한다.
  **Result**  
  ```js
  class Clock extends React.Component {
    constructor(props) {
      super(props);
      this.state = {date: new Date()};
    }

    render() {
      return (
        <div>
          <h1>Hello, world!</h1>
          <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
        </div>
      );
    }
  }

  ReactDOM.render(
    <Clock />,
    document.getElementById('root')
  );
  ```
  - 클래스 컴포넌트는 항상 props로 기본 constructor를 호출해야 한다.

### Lifecycle 메서드를 클래스에 추가하기

- 마운트 : DOM 객체가 생성되고 렌더링되어 브라우저에 나타나는 것
- 언마운트 : 컴포넌트가 DOM에서 제거되는 것 
- Lifecycle 메서드 : 컴포넌트가 마운트/언마운트 될 때 작동되는 코드

```js
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```
1. `<Clock />`가 ReactDOM.render()로 전달되었을 때 React는 Clock 컴포넌트의 constructor를 호출한다.
2. Clock이 현재 시각을 표시해야 하므로 현재 시각이 포함된 객체로 this.state를 초기화합니다.
3. React는 Clock 컴포넌트의 `render()` 메서드를 호출한다. 
4. React는 Clock의 렌더링 출력값을 일치시키기 위해 DOM을 업데이트한다.
5. Clock 출력값이 DOM에 삽입되면 React는 `componentDidMount()` 생명주기 메서드를 호출한다. 그 안에서 Clock 컴포넌트는 매초마다 컴포넌트의 `tick()` 메서드를 호출하기 위한 타이머를 설정하도록 브라우저에 요청한다.
5. 매초마다 브라우저가 `tick()` 메서드를 호출한다. 그 안에서 Clock 컴포넌트는 `setState()`에 현재 시각을 포함하는 객체를 호출하면서 UI 업데이트를 진행합니다. setState() 호출 덕분에 React는 state가 변경된 것을 인지하고 화면에 표시될 내용을 알아내기 위해 render() 메서드를 다시 호출합니다. 이 때 render() 메서드 안의 this.state.date가 달라지고 렌더링 출력값은 업데이트된 시각을 포함합니다. React는 이에 따라 DOM을 업데이트합니다.
Clock 컴포넌트가 DOM으로부터 한 번이라도 삭제된 적이 있다면 React는 타이머를 멈추기 위해 componentWillUnmount() 생명주기 메서드를 호출합니다.

## setState 사용하기

state는 직접 수정해서는 안된다. 

```js
this.state.comment = 'Hello';
```
- 컴포넌트는 리렌더링되지 않는다.

```js
this.setState({comment: 'Hello'});
```
- `setState()`로 수정해야 리렌더링할 수 있다.

- state 업데이트는 비동기적으로 발생할 수 있다. 따라서 state를 계산할 때 객체보다 함수를 매개변수로 사용하여 업데이트가 적용되도록 만들어야 한다.

  ```js
  // Wrong
  this.setState({
    counter: this.state.counter + this.props.increment,
  });
  ```

  ```js
  // Correct
  this.setState((state, props) => ({
    counter: state.counter + props.increment
  }));
  ```
- state 업데이트는 병합된다. (React는 `setState()`를 호출할 때 객체를 현재 state로 병합한다.)
  ```jsx
    componentDidMount() {
      fetchPosts().then(response => {
        this.setState({
          posts: response.posts
        });
      });

      fetchComments().then(response => {
        this.setState({
          comments: response.comments
        });
      });
    }
  ```

## 데이터는 아래로 흐른다.

컴포넌트는 자신의 state를 자식 컴포넌트에 props로 전달할 수 있다.  
모든 state는 특정 컴포넌트가 소유하고 있으며, 이 state에서 파생된 UI or 데이터는 트리구조에서 자신의 하위에 있는 컴포넌트에만 영향을 끼친다.

---

# 이벤트 처리하기

리액트의 이벤트 처리 방식 유의사항
- React의 이벤트는 캐멀 케이스(camelCase)를 사용한다.
- JSX를 사용하여 문자열이 아닌 함수로 이벤트 핸들러를 전달한다.  
  *html  
  ```html
  <button onclick="activateLasers()">
    Activate Lasers
  </button>
  ```
  *React  
  ```jsx
  <button onClick={activateLasers}>
    Activate Lasers
  </button>
  ```
- React 이벤트는 브라우저 고유 이벤트와 정확히 동작이 일치하진 않는다.
- React에서 이벤트의 기본 동작을 방지하려면 `preventDefault`를 명시적으로 호출해야 한다.
  *html
  ```html
  <a href="#" onclick="console.log('The link was clicked.'); return false">
    Click me
  </a>
  ```
  *React  
  ```jsx
  function ActionLink() {
    function handleClick(e) {
      e.preventDefault();
      console.log('The link was clicked.');
    }

    return (
      <a href="#" onClick={handleClick}>
        Click me
      </a>
    );
  }
  ```

## this 바인딩하기

- 생성자 내부에서 바인딩하거나 클래스 필드 문법 사용 권장

**화살표 함수**
```js
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    // 이 문법은 `this`가 handleClick 내에서 바인딩되도록 합니다.
    return (
      <button onClick={() => this.handleClick()}>
        Click me
      </button>
    );
  }
}
```

---

# List와 key

## List

```js
// 각 항목에 key를 넣어야 한다는 경고문구가 뜬다.
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

- 기본적으로 컴포넌트 내부에서 리스트를 렌더링한다.

## Key

```js
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li key={number.toString()}>
    {number}
  </li>
);
```

- 엘리먼트 리스트를 만들 때 포함해야 하는 특수한 문자열 어트리뷰트
- 리액트가 어떤 항목을 변경/추가/삭제할지 식별하는 것을 돕는다.
- element에 안정적인 고유성을 부여하기 위해 배열 내부의 엘리먼트에 지정해야 한다.
- key 지정하기
  - 고유하게 식별 가능한 문자열 사용 (대부분 데이터의 ID를 사용한다.)
  - 항목의 인덱스 (항목 순서가 바뀔 수 있는 경우 권장하지 않는다. 성능 저하, state에 문제가 발생할 수 있다.)
  - 리스트 항목에 명시적으로 

**key가 필요한 이유**  

```js
<ul>
  <li>Duke</li>
  <li>Villanova</li>
</ul>

<ul>
  <li>Connecticut</li>
  <li>Duke</li>
  <li>Villanova</li>
</ul>
```

- key가 없는 상태에서 위처럼 요소가 추가되면 Duke, Villanova 노드가 유지된 채 Connecticut이 추가되는 것이 아니라 모든 노드가 새로 만들어진다.

```js
<ul>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>

<ul>
  <li key="2014">Connecticut</li>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>
```

- Connecticut 노드가 추가되어도 Duke, Villanova는 새로 만들어지는 것이 아니라 이동하기만 한다.

**key 사용시 주의사항**  

- li 요소 노드인 ListItem 컴포넌트가 있을 때, li 요소에 key를 지정하는 것은 무의미하다. NumberList에서 배열을 생성할 때 ListItem 컴포넌트에 key를 지정해야 한다.

  ```js
  // wrong
  function ListItem(props) {
    const value = props.value;
    return (
      // 이곳에 key를 지정하면 무의미하다.
      <li key={value.toString()}>
        {value}
      </li>
    );
  }

  function NumberList(props) {
    const numbers = props.numbers;
    const listItems = numbers.map((number) =>
      // 배열의 ListItem 엘리먼트가 key를 가져야 한다.
      <ListItem value={number} />
    );
    return (
      <ul>
        {listItems}
      </ul>
    );
  }

  const numbers = [1, 2, 3, 4, 5];
  ReactDOM.render(
    <NumberList numbers={numbers} />,
    document.getElementById('root')
  );
  ```

  ```js
  // right
  function ListItem(props) {
    return <li>{props.value}</li>;
  }

  function NumberList(props) {
    const numbers = props.numbers;
    const listItems = numbers.map((number) =>
      <ListItem key={number.toString()} value={number} />
    );
    return (
      <ul>
        {listItems}
      </ul>
    );
  }

  const numbers = [1, 2, 3, 4, 5];
  ReactDOM.render(
    <NumberList numbers={numbers} />,
    document.getElementById('root')
  );
  ```
- Key는 형제 사이에서만 고유한 값이어야 한다. 전체 범위에서 고유할 필요는 없다. 따라서 두 개의 다른 배열을 만들 때에도 동일한 key를 사용할 수 있다.

  ```js
  function Blog(props) {
    const sidebar = (
      <ul>
        {props.posts.map((post) =>
          <li key={post.id}>
            {post.title}
          </li>
        )}
      </ul>
    );
    const content = props.posts.map((post) =>
      <div key={post.id}>
        <h3>{post.title}</h3>
        <p>{post.content}</p>
      </div>
    );
    return (
      <div>
        {sidebar}
        <hr />
        {content}
      </div>
    );
  }

  const posts = [
    {id: 1, title: 'Hello World', content: 'Welcome to learning React!'},
    {id: 2, title: 'Installation', content: 'You can install React from npm.'}
  ];
  ReactDOM.render(
    <Blog posts={posts} />,
    document.getElementById('root')
  );
  ```

## JSX에 map() 포함시키기

- JSX는 {} 내부에 모든 표현식을 포함할 수 있으므로 map() 함수의 결과를 인라인으로 처리할 수 있다.
- 단, 이 방식을 남발하는 것은 피해야 한다. map() 함수가 너무 중첩된다면 컴포넌트 추출이 더 바람직하다. 

```js
// 별도의 listItems 변수를 선언한 경우
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <ListItem key={number.toString()}
              value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}
```

```js
// {}으로 감싸서 인라인으로 처리
function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />
      )}
    </ul>
  );
}
```

---

# form

## 제어 컴포넌트 (Controlled Component)

- 리액트에 의해 값이 제어되는 입력 폼 엘리먼트
- HTML의 `<input>`, `<textarea>`, `<select>` 등의 폼 엘리먼트는 일반적으로 사용자의 입력을 기반으로 자신의 state를 관리하고 업데이트한다. React는 변경할 수 있는 state가 일반적으로 컴포넌트의 state 속성에 유지되며 `setState()`에 의해 업데이트된다.
-  React state를 '신뢰 가능한 단일 출처 (single source of truth)'로 만들어 두 요소를 결합할 수 있다. 이로 인해 폼을 렌더링하는 React 컴포넌트는 폼에 발생하는 사용자 입력값을 제어한다.

```html
<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

## textarea 태그

- HTML : 텍스트를 자식으로 정의
```html
<textarea>
  Hello there, this is some text in a text area
</textarea>
```

- React : value 어트리뷰트에 텍스트 입력
```js
class EssayForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 'Please write an essay about your favorite DOM element.'
    };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('An essay was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Essay:
          <textarea value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

## select 태그

- HTML : 드롭다운 목록을 만드는 태그

```html
<select>
  <option value="grapefruit">Grapefruit</option>
  <option value="lime">Lime</option>
  <option selected value="coconut">Coconut</option>
  <option value="mango">Mango</option>
</select>
```

- React : 최상단 select 태그에 value 어트리뷰트 사용
```js
class FlavorForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'coconut'};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('Your favorite flavor is: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Pick your favorite flavor:
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="grapefruit">Grapefruit</option>
            <option value="lime">Lime</option>
            <option value="coconut">Coconut</option>
            <option value="mango">Mango</option>
          </select>
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

## 다중 입력 제어

- 여러 input 엘리먼트를 제어해야 할 때, 각 엘리먼트에 name 어트리뷰트를 추가하고 event.target.name 값을 통해 핸들러가 어떤 작업을 할 지 선택할 수 있게 해준다.

```js
class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 2
    };

    this.handleInputChange = this.handleInputChange.bind(this);
  }

  handleInputChange(event) {
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    const name = target.name;

    this.setState({
      [name]: value
    });
  }

  render() {
    return (
      <form>
        <label>
          Is going:
          <input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          Number of guests:
          <input
            name="numberOfGuests"
            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}
```

---

# 리액트 최상위 API

- React.createElement()
  - 리액트는 JSX를 사용함으로써 `createElement()`를 직접 호출하지 않아도 DOM 요소를 생성할 수 있다. 
- React.memo
  - 성능 최적화를 위해 사용되는 고차 컴포넌트(Higher Order Component)
  - 컴포넌트가 동일한 props로 동일한 결과를 렌더링하면 React.memo를 호출하고 결과를 메모이징(Memoizing)하도록 래핑한다. 즉, React는 컴포넌트를 렌더링하지 않고 마지막으로 렌더링된 결과를 재사용한다.
  - React.memo는 props 변화에만 영향을 준다. React.memo로 감싸진 함수 컴포넌트 구현에 useState, useReducer 또는 useContext 훅을 사용한다면, 여전히 state나 context가 변할 때 다시 렌더링된다.

## render()

## constructor()

- this.state에 객체를 할당하여 지역 state를 초기화할 때 사용한다.
- 인스턴스에 이벤트 처리 메서드를 바인딩할 때 사용한다.

## refs

- `React.createRef`
  - React 엘리먼트에 ref 어트리뷰트로 붙일 수 있는 ref를 생성한다.
  ```js
  class MyComponent extends React.Component {
    constructor(props) {
      super(props);

      this.inputRef = React.createRef();
    }

    render() {
      return <input type="text" ref={this.inputRef} />;
    }

    componentDidMount() {
      this.inputRef.current.focus();
    }
  }
  ```
- `React.forwardRef`
  - 전달받은 ref 어트리뷰트를 하부 트리 내의 다른 컴포넌트로 전달하는 React 컴포넌트를 생성한다.
  - 활용
    - DOM 엘리먼트로 ref 전달하기
    - 고차 컴포넌트(Higher Order Component)로 ref 전달하기
  - React.forwardRef는 렌더링에 사용될 함수를 매개변수로 받을 수 있다. React는 이 함수를 두 개 인자 props와 ref를 사용하여 호출하고, 이 함수는 React 노드를 반환합니다.

```js
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// You can now get a ref directly to the DOM button:
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```
- 위에서 React는 `<FancyButton ref={ref}>` 엘리먼트에 주어진 ref를 React.forwardRef 호출시 렌더링 함수에 2번째 인자로 전달하고 이 렌더링 함수는 ref를 `<button ref={ref}>` 엘리먼트에 전달한다.
- 따라서 React가 해당 ref를 붙이고 난 뒤, ref.current는 `<button>` DOM 엘리먼트 인스턴스를 직접 가리키게 된다.

**ref를 남용하면 안되는 이유**  
부모 컴포넌트에게 DOM ref를 공개하기
보기 드문 경우지만, 부모 컴포넌트에서 자식 컴포넌트의 DOM 노드에 접근하려 하는 경우도 있습니다. 자식 컴포넌트의 DOM 노드에 접근하는 것은 컴포넌트의 캡슐화를 파괴하기 떄문에 권장되지 않습니다. 그렇지만 가끔가다 자식 컴포넌트의 DOM 노드를 포커스하는 일이나, 크기 또는 위치를 계산하는 일 등을 할 때에는 효과적인 방법이 될 수 있습니다.

자식 컴포넌트에 ref를 사용할 수 있지만, 이 방법은 자식 컴포넌트의 인스턴스의 DOM 노드가 아닌 자식 컴포넌트의 인스턴스를 가져온다는 점에서, 자식 컴포넌트가 함수 컴포넌트인 경우에는 동작하지 않는다는 점에서, 좋은 방법이 아닙니다.

React 16.3 이후 버전의 React를 사용하신다면 위와 같은 경우에서 ref 전달하기(ref forwarding)을 사용하는 것이 권장됩니다. Ref 전달하기는 컴포넌트가 자식 컴포넌트의 ref를 자신의 ref로서 외부에 노출시키게 합니다. 자식 컴포넌트의 DOM 노드를 부모 컴포넌트에게 공개하는 방법에 대한 자세한 예시는 ref 넘겨주기 문서에서 볼 수 있습니다..

React 16.2 이전 버전을 사용하시거나 ref 전달하기보다 더 유연한 방법을 원한다면 이런 대안을 사용할 수 있습니다.

가능하다면 DOM 노드를 외부에 공개하는 일을 지양해야 합니다만 DOM 노드를 외부에 공개하는 일은 유용한 해결책이 될 수 있습니다. 또한 이 방법들은 자식 컴포넌트의 코드 수정을 요한다는 점을 기억하세요. 만약 자식 컴포넌트의 코드를 수정할 수 없다면 최후의 방법인 findDOMNode()를 사용하는 방법이 있지만 findDOMNode()는 좋지 못한 방법일 뿐더러 StrictMode에서 사용할 수 없습니다.


**Code Spliting**  
(참고 : https://velopert.com/3421)
SPA는 자바스크립트 번들 파일에 어플리케이션의 모든 로직을 불러오기 때문에 규모가 커지면 용량도 커진다. 용량이 커지면 페이지 로딩 속도가 느려져 이용자들이 불편을 겪는다.  
코드 스플리팅은 코드가 커지는 것을 방지하기 위해 분할하는 방법이다. 당장 필요한 코드가 아닌 것들은 분리시켜 나중에 불러오게 만들어 로딩 속도를 개선시킬 수 있다.

**React.lazy**  
React.lazy()를 사용하면 동적으로 불러오는 컴포넌트를 정의할 수 있다. 번들의 크기를 줄이고, 초기 렌더링에서 사용되지 않는 컴포넌트를 불러오는 작업을 지연시킬 수 있다.


```js
// 이 컴포넌트는 동적으로 불러옵니다
const SomeComponent = React.lazy(() => import('./SomeComponent'));
```
lazy한 컴포넌트를 렌더링하려면 렌더링 트리 상위에 `<React.Suspense>` 컴포넌트가 존재해야 한다는 점에 유의하세요. 이를 활용하여 로딩 지시기(Loading indicator)를 나타낼 수 있습니다.


**컴포넌트가 업데이트 되는 경우**  
- props가 바뀔 때
- state가 바뀔 때
- 부모 컴포넌트가 리렌더링될 때
- this.forceUpdate로 강제로 렌더링을 트리거 할 때  

## componentDidMount()

```js
componentDidMount()
```
- 컴포넌트가 마운트되어 DOM트리에 삽입된 후 호출된다.
- DOM 노드가 필요한 초기화 작업이 이루어진다. (EX. 외부에서 데이터를 불러오기 위해 네트워크 요청 보내기)

## shouldComponentUpdate()

## componentDidUpdate()

```js
componentDidUpdate(prevProps, prevState, snapshot)
```

- 컴포넌트가 갱신된 직후 호출된다. (최초 렌더링에서는 호출되지 않음)
- 컴포넌트가 갱신되었을 때 DOM을 조작하기 위해 메서드를 활용한다. (EX. 이전과 현재의 props를 비교하여 네크워크 요청 보내기)

## componentWillUnmount()

```js
componentWillUnmount()
```
- 컴포넌트가 마운트 해제되어 제거되기 직전에 호출된다.
- 타이머 제거, 네트워크 요청 취소 등 필요한 정리 작업이 이루어진다.



---

# ReactDOM

리액트를 `<script>` 태그로 불러온다면 ReactDOM 글로벌 영역에서 상위 레벨 API를 사용할 수 있다.   

react-dom package는 앱의 최상위 레벨에서 사용할 수 있는 DOM에 특화된 메서드와 React 모델 외부로 나갈 수 있는 해결책을 제공한다.

## render()

```js
ReactDOM.render(element, container[, callback])
```
- React 엘리먼트를 container DOM에 렌더링하고 컴포넌트에 대한 참조를 반환한다. (상태가 없으면 null 반환)
- React 엘리먼트가 이전에 container 내부에 렌더링 되었다면 해당 엘리먼트는 업데이트하고 최신의 React 엘리먼트를 반영하는 데 필요한 DOM만 변경한다.
- 추가적인 콜백이 제공된다면 컴포넌트가 렌더링되거나 업데이트된 후 실행된다.

- `ReactDOM.render()`는 전달한 컨테이너 노드의 콘텐츠를 제어한다. 처음 호출할 때 기존의 DOM 엘리먼트를 교체하며 이후의 호출은 React의 DOM diffing 알고리즘을 사용하여 효율적으로 업데이트한다.
- `ReactDOM.render()`는 컨테이너 노드를 수정하지 않고 컨테이너의 하위 노드만 수정한다. 따라서 자식 노드를 덮어쓸 필요 없이 기존의 DOM 노드에 컴포넌트를 추가할 수 있다.

## constructor()

- 컴포넌트의 생성자 메서드로 컴포넌트를 만들 때 처음으로 실행된다. 
- 이 메서드에서 초기 state를 정할 수 있다.
- 메서드를 바인딩하거나 state를 초기화하는 작업이 없다면 해당 React 컴포넌트에는 생성자를 구현하지 않아도 된다.

---

# ReactDOMServer

- ReactDOMServer 객체를 통해 컴포넌트를 정적 마크업으로 렌더링할 수 있다. (보통 서버사이드렌더링에서 사용)
- 서버와 브라우저 환경에서 사용할 수 있는 메서드
  - renderToString()
  - renderToStaticMarkup()
- 서버에서만 사용할 수 있는 메서드
  - renderToNodeStream()
  - renderToStaticNodeStream()

## renderToString()

```js
ReactDOMServer.renderToString(element)
```
- React 엘리먼트의 초기 HTML을 렌더링하고 React는 HTML 문자열을 반환한다.
- 빠른 페이지 로드를 위해 초기 요청 시 서버에서 HTML을 생성하여 마크업을 보내거나, 검색 엔진 최적화를 위해 검색 엔진이 페이지를 크롤링할 수 있도록 하는데 사용할 수 있다.

## renderToStaticMarkup()

```js
ReactDOMServer.renderToStaticMarkup(element)
```
- renderToString과 유사하나 data-reactroot와 같이 React에서 내부적으로 사용하는 추가적인 DOM 어트리뷰트를 만들지 않는다.
- 약간의 메모리를 절약할 수 있어 React로 간단한 정적 페이지를 만들 때 유용하다.

## renderToNodeStream()

```js
ReactDOMServer.renderToNodeStream(element)
```
- React 엘리먼트의 초기 HTML을 렌더링하고 HTML 문자열을 출력하는 Readable 스트림을 반환한다.
- 빠른 페이지 로드를 위해 초기 요청 시 서버에서 HTML을 생성하여 마크업을 보내거나, 검색 엔진 최적화를 위해 검색 엔진이 페이지를 크롤링할 수 있도록 하는데 사용할 수 있다.
- 서버에서만 사용 가능하다.

## renderToStaticNodeStream()

```js
ReactDOMServer.renderToStaticNodeStream(element)
```
- renderToNodeStream과 유사하나 data-reactroot와 같이 React에서 내부적으로 사용하는 추가적인 DOM 어트리뷰트를 만들지 않는다.
- 약간의 메모리를 절약할 수 있어 React로 간단한 정적 페이지를 만들 때 유용하다.
- 서버에서만 사용 가능하다.

---

# DOM 엘리먼트

## HTML 어트리뷰트와 다르게 동작하는 React 어트리뷰트

- checked : checkbox, radio 타입의 `<input>` 컴포넌트에 지원되는 어트리뷰트로 컴포넌트의 선택 여부를 설정할 수 있다.
- className : CSS class를 사용할 때 쓰는 어트리뷰트
- dangerouslySetInnerHTML : 브라우저 DOM에서 innerHTML을 사용하기 위한 React의 대체 방법 
  ```js
  function createMarkup() {
    return {__html: 'First &middot; Second'};
  }

  function MyComponent() {
    return <div dangerouslySetInnerHTML={createMarkup()} />;
  }
  ```
- htmlFor : for은 자바스크립트에서 예약어이므로 React에서는 htmlFor을 대신 사용한다.
- onChange : 예상한 대로 폼 필드가 변경될 때 이벤트가 발생한다.
- selected : `<option>`을 선택됐다고 표시하고 싶을 때 `<select>`의 value에서 해당 option의 값을 대신 참조
- value : `<input>`, `<select>`, `<textarea>` 컴포넌트에 의해 지원되며, 이를 이용해 컴포넌트의 값을 설정할 수 있다. 
- style : 문서 일부에서 편의상 사용할 수 있으나 style 어트리뷰트를 스타일링의 주요 수단으로 사용하는 것은 권장되지 않는다.

위 항목을 제외하고 React 어트리뷰트에서 대부분의 HTML 어트리뷰트를 지원한다.  

---

# 합성 이벤트 (SyntheticEvent)

브라우저마다 이벤트 이름, 종류, 처리 방식이 다르다. React는 이를 모든 브라우저에서 이벤트를 동일하게 처리하기 위한 이벤트 래퍼 SyntheticEvent 객체를 전달받는다. 따라서 React 개발자는 브라우저마다 다른 Native 이벤트를 신경쓰지 않고 React가 제공하는 이벤트만 사용할 수 있다.  

**React에서 지원하는 이벤트 목록**  

https://ko.reactjs.org/docs/events.html

---

# React 기술 용어

## 싱글 페이지 애플리케이션(Single-page application, SPA)

하나의 HTML 페이지와 애플리케이션 실행에 필요한 JavaScript와 CSS 같은 모든 자산을 로드하는 애플리케이션이다. 

## 컴파일러 (Compiler)

JavaScript 컴파일러는 JavaScript 코드를 변환하고 다른 형식으로 JavaScript 코드를 반환한다. 보통 ES6 문법을 구형 브라우저에서 동작할 수 있도록 변환하는 데 많이 사용합니다. (EX. Babel)  

**컴파일과 트랜스파일**  
- 컴파일 : 한 언어로 작성된 소스 코드를 다른 언어로 변환하는 것을 의미한다.
- 트랜스파일 : 한언어로 작성된 소스 코드를 비슷한 수준의 추상화를 가진 다른 언어로 변환하는 것을 말한다.

## 번들러 (Bundler)

웹 어플리케이션을 동작시키기 위한 서로 연관 관계가 있는 웹 구성 자원(HTML, CSS, JavaScript, 이미지 등)을 각각 모듈로 보고 이들의 의존성을 묶고 조합해서 합쳐진 하나의 결과물로 만들어주는 도구이다.   
분리된 JavaScript와 CSS 모듈 코드를 브라우저에 최적화된 여러 개의 파일로 결합한다. (EX. Webpack, Browserify)  

![webpack-process](../src/webpack-process.jpg)

## Package Manager

프로젝트의 종속성을 관리할 수 있는 도구이다. (EX. npm, Yarn)

## CDN (Content Delivery Network)

CDN은 전 세계의 서버 네트워크에서 캐시된 정적 콘텐츠를 제공한다. (컨텐츠 로딩 속도를 줄일 수 있다.)

## JSX

JavaScript의 확장 문법으로 JavaScript의 기능을 모두 사용할 수 있다. JSX는 `React.createElement()` 호출을 통해 일반 JavaScript 객체인 React 엘리먼트로 컴파일된다.

## 엘리먼트

React 애플리캐이션을 구성하는 블록. 엘리먼트는 화면에 보이는 것들을 기술하며, React 엘리먼트는 변경되지 않는다. (일반적으로 엘리먼트는 직접 사용되지 않고 컴포넌트로부터 반환된다.)

```js
const element = <h1>Hello, world</h1>;
```

## 컴포넌트

React 컴포넌트는 페이지에 렌더링할 React 엘리먼트를 반환하는 작고 재사용 가능한 코드 조각입니다. 가장 간단한 React 컴포넌트는 React 엘리먼트를 반환하는 일반 JavaScript 함수입니다.

```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```
- 컴포넌트는 ES6 class로도 작성할 수 있다.

```js
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```
컴포넌트는 기능별로 나눌 수 있으며 다른 컴포넌트 안에서 사용할 수 있습니다. 컴포넌트는 다른 컴포넌트, 배열, 문자열 그리고 숫자를 반환할 수 있습니다. 화면을 구성하는 데 자주 사용되는 UI(Button, Panel, Avatar), 혹은 복잡한 UI(App, FeedStory, Comment) 컴포넌트는 재사용 가능한 컴포넌트가 될 수 있습니다. 컴포넌트의 이름은 항상 대문자로 시작해야 합니다 (<Wrapper/> (o) <wrapper/> (x)). 컴포넌트 렌더링에 대한 자세한 내용은 이 문서를 참고하세요.

## props

컴포넌트의 입력값으로 props는 부모 컴포넌트로부터 자식 컴포넌트로 전달된 데이터입니다.

props는 읽기 전용이라는 것에 주의하세요. props는 어떤 방식으로든 수정해서는 안 됩니다.

// 틀린 예
props.number = 42;
사용자의 입력 또는 네트워크 응답에 반응하여 어떤 값을 수정해야 한다면 state를 사용하세요.

props.children
모든 컴포넌트에서 props.children를 사용할 수 있습니다. props.children은 컴포넌트의 여는 태그와 닫는 태그 사이의 내용을 포함합니다. 예를 들어,

```js
<Welcome>Hello world!</Welcome>
```
Hello world! 문자열은 Welcome 컴포넌트의 props.children으로 사용할 수 있습니다.

```js
function Welcome(props) {
  return <p>{props.children}</p>;
}
```
Class로 정의된 컴포넌트에서는 this.props.children을 사용합니다.

class Welcome extends React.Component {
  render() {
    return <p>{this.props.children}</p>;
  }
}

## state

컴포넌트와 관련된 일부 데이터가 시간에 따라 변경될 경우 state가 필요합니다. 예를 들어, Checkbox 컴포넌트는 isChecked state가 필요할 수 있으며, NewsFeed 컴포넌트는 fetchedPosts를 컴포넌트의 state를 통해 계속 주시하려고 할 수 있습니다.

state와 props의 가장 중요한 차이점은 props는 부모 컴포넌트로부터 전달받지만, state는 컴포넌트에서 관리된다는 것입니다. 컴포넌트는 props를 변경할 수 없지만, state는 변경할 수 있습니다.

데이터가 변경되는 각 특정한 부분에 대해, 해당 상태(state)를 “소유”하는 컴포넌트는 하나만 존재해야 합니다. 서로 다른 두 컴포넌트의 상태를 동기화하려고 하지 마십시오. 대신, 공통 상태를 두 컴포넌트의 공통 조상으로 끌어올리고 해당 데이터를 두 컴포넌트에 props로 전달하세요.

## 생명주기 메서드 (Lifecycle method)

컴포넌트의 각각의 단계에서 실행되는 커스텀 기능으로 컴포넌트가 만들어지고 DOM에 삽입될 때(mounting), 컴포넌트가 업데이트될 때 및 컴포넌트가 DOM에서 마운트 해제될 때(unmounted) 혹은 제거될 때 사용할 수 있는 기능을 제공한다.

## Ref

React는 컴포넌트에 접근할 수 있는 특수한 어트리뷰트를 지원합니다. ref 어트리뷰트 React.createRef() 함수, 콜백 함수, 혹은 문자열(레거시 API에서)로 생성할 수 있습니다. ref 어트리뷰트가 콜백 함수인 경우, 함수는 DOM 엘리먼트나 class 인스턴스를 인자로 받습니다. 이를 통해 컴포넌트 인스턴스나 DOM 엘리먼트에 직접 접근할 수 있습니다.

Ref를 가능한 한 적게 사용하세요. 만약 앱에서 Ref를 사용하여 “작동되는 부분”이 많다면 하향식 데이터 흐름을 사용하는 것이 더 좋습니다.

## 재조정 (Reconciliation)

컴포넌트의 state나 props가 변경되면 React는 새로 반환된 컴포넌트를 이전에 렌더링된 컴포넌트와 비교하여 실제 DOM을 업데이트 해야하는지 결정한다. 두 컴포넌트가 동일하지 않다면 React는 DOM을 업데이트 한다. 

---

# Hook

## 개념

Hook은 함수 컴포넌트에서 클래스처럼 React State와 라이프 사이클 기능을 연동(hook into)할 수 있게 해주는 함수이다. (클래스에서는 작동하지 않는다.)


## 등장 이유

1. 컴포넌트 사이에서 상태 로직을 재사용하기 어렵다.
  - render props, HOC (고차 컴포넌트)등을 쓰면 wrapper hell 발생 가능성이 커진다.
  - 훅을 사용하면 컴포넌트로부터 상태 관련 로직을 추상화할 수 있기 때문에 재사용이 가능하다. 즉, 훅은 계층 변화 없이 상태 관련 로직을 재사용할 수 있게 도와준다.
2. 중복된 로직
  - 클래스형 컴포넌트에서 사용할 수 있는 라이프 사이클 메서드는 중복되는 로직을 넣어야 하는 경우가 있었다.
3. 복잡한 컴포넌트들은 이해하기 어렵다. 
  - 라이프 사이클을 기반으로 컴포넌트를 구분하면 관련 없는 로직이 섞여 들어가는 경우가 잦다. (컴포넌트 재사용이 어려워진다.)
  - 훅을 통해 서로 비슷한 기능을 가진 함수의 묶음으로 컴포넌트를 나누면서 상태 관리가 편리해졌다.
4. 클래스는 함수보다 사용 방법이 복잡하기 때문에 혼란을 준다.

클래스형 컴포넌트는 상태를 가지고 있어 상태 관리를 할 수 있고, 라이프 사이클 관련 기능을 사용할 수 있었지만 함수형 컴포넌트에 비해 복잡하다는 단점이 있다.  

함수형 컴포넌트는 클래스보다는 간단하지만 상태 관리를 할 수 없었다. 리액트 훅(Hook)이 등장하면서 함수형 컴포넌트에서도 상태 관리와 라이프 사이클 활용이 가능해졌다.  

## 사용 규칙

1. 최상위(at the top level)에서만 Hook을 호출해야 한다. (반복문, 조건문, 중첩 함수에서는 실행하지 말 것) 그래야 컴포넌트가 렌더링 될 때마다 동일한 순서로 Hook이 호출되는 것이 보장된다.
2. 리액트 함수 컴포넌트나 Custom Hook 내에서만 훅을 호출해야 한다. (일반 자바스크립트 함수에서 호출하면 안된다.) 이 규칙을 지켜야 모든 상태 관련 로직을 소스코드에서 명확하게 보이도록 할 수 있다.

- 한 컴포넌트 안에서 State나 Effect Hook을 여러 개 사용할 수 있다. 리액트는 Hook이 호출되는 순서에 의존하기 때문에 특정 state가 어떤 useState 호출에 해당하는지 알 수 있다. 순서에 의존하기 때문에 조건문 안에서 호출되면 순서가 보장되지 않아 최상위에서만 호출해야 한다는 것이다.

---

# State Hook

```js
const Example = (props) => {
  // Hook
  return <div />;
}
```
```js
function Example(props) {
  // Hook
  return <div />;
}
```

함수 컴포넌트 내부에서 side effects를 수행할 수 있게 해주는 Hook (componentDidMount, componentDidUpdate, componentWillUnmount와 같은 목적으로 제공)  

**side effect(effects)**  
리액트 컴포넌트 내에서 데이터를 가져오거나 읽고 DOM을 직접 조작하는 동작들 (다른 컴포넌트에 영향을 줄 수 있고 렌더링할 때 구현할 수 없는 작업)

## 예제

```js
import { useState } from "react";

function Example() {
  // 새로운 state 변수 선언 (count)
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times.</p>
      <button onClick={() => setCount(count + 1)}>Click me!</button>
    </div>
  );
}
```
- useState 호출 : state 변수 선언(count)
- useState의 매개변수 : state의 초기 값 (숫자 또는 문자열)
- useState의 return 값 : state 변수, 해당 변수를 갱신할 수 있는 함수

- 버튼을 클릭하면 setCount 함수를 호출하여 state 변수를 갱신한다.
- React는 새로운 count 변수를 Example 컴포넌트에 넘기며 해당 컴포넌트를 리렌더링한다.

---

# Effect Hook

- 구독을 추가하고 제거하는 로직처럼 기능별로 서로 연관되어 있는 코드를 한군데에 모아서 작성할 수 있다. (클래스는 라이프 사이클에 따라 묶여있어 기능별로 구분되지 않는다는 단점이 있었다.)

## clean-up

### clean-up을 사용하지 않는 Effects

network request, DOM 수동 조작, 로깅 등은 clean-up이 필요없다.

리액트가 DOM을 업데이트한 뒤 추가로 코드를 실행해야 하는 경우가 

### 예제

```js
import { useState, useEffect } from "react";

function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times.`;
  });

  return (
    <div>
      <p>You clicked {count} times.</p>
      <button onClick={() => setCount(count + 1)}>Click me!</button>
    </div>
  );
}
```
- useEffect가 하는 일 : 리액트에게 컴포넌트가 렌더링 후 어떤 일을 수행해야할 지 알린다. 리액트는 useEffect 함수를 DOM 업데이트 이후 호출한다.
- useEffect를 컴포넌트 안에서 호출하는 이유 : useEffect 함수를 통해 count state 변수에 접근할 수 있기 때문이다.
- useEffect의 실행 : 첫 렌더링과 그 이후 모든 업데이트에서 실행된다.

### clean-up을 사용하는 Effects

외부 데이터에 subscription을 설정해야 하는 경우 메모리 누수가 발생하지 않도록 clean-up이 필요하다.

### 예제

```js
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    // effect 이후에 어떻게 clean-up할 것인지 표시한다.
    return function cleanup() {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```
- useEffect에서 함수를 반환하는 이유 : effect를 위한 추가적인 clean-up 메커니즘 (모든 effect는 clean-up 함수를 반환할 수 있다.)
- 리액트가 effect를 clean-up하는 시점 : 리액트는 컴포넌트가 마운트 해제될 때 clean-up을 실행한다. 하지만 effect는 렌더링될 때마다 실행된다. 따라서 다음 차례의 effect를 실행하기 전에 이전 렌더링에서 파생된 effect를 정리하는 것이다. 
- effect에서 반드시 유명함수(named-function)를 반환해야 하는 것은 아니다. 화살표 함수를 반환해도 무방하다.

## 기타

### useEffect vs. useLayoutEffect

- useEffect 
  - 컴포넌트 렌더링 -> 화면 업데이트 -> useEffect 실행
  - 비동기적으로 실행 (DOM과 인터렉션이 없는 경우에 사용)
- useLayoutEffect
  - 컴포넌트 렌더링 -> useLayoutEffect 실행 -> 화면 업데이트
  - 동기적으로 실행 (렌더링 직후 DOM 요소의 값을 읽을 때 사용 - scroll, position 등)

### 관심사를 구분하고 싶다면 Multiple Effect를 사용한다.

```js
function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
  // ...
}
```
- effect Hook도 여러 번 사용할 수 있으며 라이프 사이클 메서드가 아니라 코드의 역할에 따라 나눌 수 있다. (리액트는 컴포넌트에 사용된 모든 effect를 지정된 순서에 맞춰 적용한다.)

### effect가 업데이트 될 때마다 실행되는 이유

```js
function FriendStatus(props) {
  // ...
  useEffect(() => {
    // ...
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
```
- 이러한 방식으로 동작하는 것이 일관성을 유지해주며 클래스 컴포넌트에서는 흔히 업데이트 로직을 빼먹으면서 발생할 수 있는 버그를 예방한다.

### effect를 건너뛰어 성능 최적화하기

```js
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // count가 바뀔 때만 effect를 재실행한다.
```
- useEffect의 두번째 인수로 의존성 배열을 준다. count에 변화가 없다면 useEffect를 건너뛰게 된다.(최적화) count가 업데이트되 뒤 렌더링하면 리액트는 count의 값을 비교하고 (변화가 생겼으므로) effect를 재실행한다.
- clean-up을 사용하는 effect도 동일하게 적용된다.
- effect를 실행하고 clean-up하는 과정을 (마운트와 마운트 해제 시)한 번만 실행하고 싶다면 빈 배열([])을 두 번째 인수로 넘기면 된다. (어떤 값에도 의존하지 않으므로 재실행 할 필요가 없다.)
- 빈 배열을 넘기면 effect 안의 props와 state는 초기 값을 유지하게 된다.

---

# custom Hook

- 상태 관련 로직을 컴포넌트 간에 재사용하고 싶을 때 고차 컴포넌트(HOC, Higher-Order Components), render props를 사용했다. 커스텀 훅은 두 방법과 달리 컴포넌트 트리에 새 컴포넌트를 추가하지 않고 가능하게 한다.
- 커스텀 훅을 만들면 컴포넌트 로직을 함수로 뽑아내어 재사용 할 수 있다.
- 커스텀 훅의 이름이 'use'로 시작하지 않으면 특정 함수가 그 안에서 Hook을 호출하는지 알 수 없기 때문에 Hook 규칙의 위반 여부를 자동으로 체크할 수 없다.
- 다수의 컴포넌트에서 같은 Hook을 사용하더라도 state는 공유하지 않는다. 커스텀 훅을 사용할 때마다 그 안의 useState와 useEffect는 완전히 독립되어 있기 때문이다.
- 커스텀 훅을 호출하더라도 리액트 과점에서는 useState나 useEffect를 호출한 것과 마찬가지이다. 따라서 독립된 state를 받을 수 있다. (하나의 컴포넌트 안에서 useState, useEffect는 여러 번 부를 수 있고 이들은 모두 독립적이기 때문)

---

# Hook API 정리

## useState

```js
const [state, setState] = useState(initialState);
```
- 상태와 그 값을 갱신하는 함수를 반환한다.
- 최초 렌더링 시 state의 값은 initialState 값이다.

```js
setState(newState);
```
- setState는 새 state 값을 받아 컴포넌트 리렌더링을 큐에 등록한다.
- 다음 리렌더링 시 useState를 통해 반환받은 첫 번째 값은 가장 최신의 state가 된다.

```js
const [state, setState] = useState(() => {
  const initialState = someExpensiveComputation(props);
  return initialState;
});
```
- 지연 초기 state : initialState 는 초기 상태값이다. 이후 렌더링에서 이 값은 무시되나, 만약 초기 state가 높은 수준의 연산을 필요로 한다면 초기 렌더링 시에만 실행될 함수를 대신 제공할 수 있다.

- State Hook을 현재 상태와 동일한 값으로 갱신하는 경우 리액트는 자식을 렌더링하거나 다른 실행을 하지 않고 처리를 종료한다.

### 예제

```js
function Counter ({initialCount}) {
  const [count, setCount] = useState(initialCount);

  return (
    <>
      Count: {count}
      <button onClick={() => setCount(initialCount)}>Reset
      </button>
      <button onclick={() => setCount(prevCount => prevCount - 1)}>-</button>
      <button onClick={() => setCount(prevCount => prevCount + 1)}>+</button>
    </>
  )
}
```

## useEffect

```js
useEffect(() => {
  const subscription = props.source.subscribe();
  return () => {
    // Clean up the subscription
    subscription.unsubscribe();
  };
}, [props.source]);
```

- 렌더링 이후 특정 작업을 수행해야 할 때 사용한다. 
- 기본적으로 렌더링이 될 때마다 useEffect 훅이 실행되지만, 두 번째 인수로 의존성 배열을 전달하여 이 배열이 변경될 때만 useEffect가 실행되도록 할 수 있다.
- 최초 렌더링 시 한 번만 실행하고 싶다면 두 번째 인수로 빈 배열([])을 전달한다.
- useEffect는 비동기적으로 발생한다. 모든 DOM 변경 후 동기적으로 실행하고 싶다면(DOM에서 레이아웃을 읽고 동기적 리렌더링하는 경우 등) useLayoutEffect를 사용한다.

## useContext

- context 객체(React.createContext에서 반환된 값)를 받아 그 context의 현재 값을 반환한다.
- context의 현재 값은 트리 안에서 이 Hook을 호출하는 컴포넌트에 가장 가까이 있는 `<MyContext.Provider>`의 value prop에 의해 결정된다.
- 가장 가까운 `<MyContext.Provider>`가 갱신되면 이 Hook은 그 MyContext.Provider에게 전달된 가장 최신의 context value를 사용하여 렌더러를 실행시킨다.
- useContext로 전달한 인자는 context 그 자체여야 한다.
  - 맞는 사용: useContext(MyContext)
  - 틀린 사용: useContext(MyContext.Consumer)
  - 틀린 사용: useContext(MyContext.Provider)
- useContext를 호출한 컴포넌트는 context 값이 변경되면 항상 리렌더링된다. 따라서 리렌더링하는 비용을 줄여 최적화하려면 '메모이제이션'을 사용한다.
- useContext는 context를 읽고 context의 변경을 구독하는 것만 가능하다. context의 값을 설정하려면 트리 상위 계층의 `<MyContext.Provider>`가 필요하다.

```js
const themes = {
  light: {
    foreground: "#000000",
    background: "#eeeeee"
  },
  dark: {
    foreground: "#ffffff",
    background: "#222222"
  }
};

const ThemeContext = React.createContext(themes.light);

function App() {
  return (
    <ThemeContext.Provider value={themes.dark}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

function ThemedButton() {
  const theme = useContext(ThemeContext);
  return (
    <button style={{ background: theme.background, color: theme.foreground }}>
      I am styled by theme context!
    </button>
  );
}
```

## useReducer

```js
const [state, dispatch] = useReducer(reducer, initialArg, init);
```

```js
function useReducer(reducer, initialState) {
  const [state, setState] = useState(initialState);

  function dispatch(action) {
    const nextState = reducer(state, action);
    setState(nextState);
  }

  return [state, dispatch];
}
```
- 복잡한 컴포넌트 안에서 다양한 상황에 따라 다양한 상태를 업데이트 하고 싶을 때
- 복잡한 로직을 만드는 경우, 다음 상태가 이전 상태에 의존하는 경우 useState보다 useReducer를 사용한다.

```js
const initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```

### 지연 초기화
(참조 : https://ui.toast.com/weekly-pick/ko_20201022)

## useCallback

**메모이제이션(memoization)**  
(참조 : https://ko.wikipedia.org/wiki/%EB%A9%94%EB%AA%A8%EC%9D%B4%EC%A0%9C%EC%9D%B4%EC%85%98)  
컴퓨터 프로그램이 동일한 계산을 반복해야 할 때, 이전에 계산한 값을 메모리에 저장함으로써 동일한 계산의 반복 수행을 제거하여 프로그램 실행 속도를 빠르게 하는 기술이다. 동적 계획법의 핵심이 되는 기술이다. 

## useMemo

```js
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

- 메모이제이션된 값을 반환한다.  
- 단, useMemo는 deps가 변경된 경우에 메모이제이션된 값만 다시 계산한다. (최적화 유리)
- useMemo로 전달된 함수는 렌더링 중에 실행된다. 렌더링 중 발생하지 않는 것은 (EX. side effect) useMemo에서 하는 일이 아니다.
- 의존성 배열이 없는 경우 매 렌더링 때마다 새 값을 계산한다.

## useRef
```js
const refContainer = useRef(initialValue);
```
- useRef는 .current 프로퍼티로 전달된 인자(initialValue)로 초기화된 변경 가능한 ref 객체를 반환한다. (이때 반환된 객체는 컴포넌트의 전 생애주기를 통해 유지된다.)
- useRef의 매개변수 : ref 객체의 초기값으로 ref의 current 속성에 할당된다. (ref.current로 접근 가능)
- ref 객체는 업데이트 되어도 리렌더링하지 않는다. (`.current` 프로퍼티를 변형해도 리렌더링은 발생하지 않음)

```js
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` points to the mounted text input element
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

---

# 참조

React 공식문서 https://ko.reactjs.org/docs/getting-started.html