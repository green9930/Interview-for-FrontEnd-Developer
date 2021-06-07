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






---

# 참조

React 공식문서 https://ko.reactjs.org/docs/getting-started.html