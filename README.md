---
title: React Optimize
subtitle: 리액트 최적화
author: Seungjoon
presentedAt: 2020-10-14
---
# React를 아십니까

![alt text](https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcS4Q-_vNMcbuLHBtsXzw0KEOIJ4ENQThXC3SA&usqp=CAU)
React 는 세 가지를 알면 되징.
---
# React Element
- React 의 가장 작은 단위

- Browser 의 DOM Element 가 아니다!

- **`UI 를 표현하는 일반 객체(Plain Object)다.`**
---

```ts
// Element 의 형태
{
  type: 'button',
  props: {
    className: 'button button-blue',
    children: {
      type: 'b',
      children: 'OK!'
    }
  }
}
```

```html
// 위 엘리먼트는 HTML 을 plainObject 로 표현한 것일 뿐!
<button class='button button-blue'>
  <b>
    OK!
  </b>
</button>
```

- element 는 type 과 props. 두개의 fields 를 가진다.

- type 은 string | Component

- type 이 string 이면 DOM node 의 tag name 에 대응한다.

- props 는 Object
---
# Component
 type 은 react 의 component 를 표현하는 function, 혹은 class 가 될 수 있다.
 **이 부분이 바로 react 의 핵심 아이디어이다**
 
```ts
{
  type: Button,
  props: {
    color: 'blue',
    children: 'OK!'
  }
}
```
---
 React 는 type 이 Component 인 Element 를 보면 해당 컴포넌트에게 주어진 props 로 어떤 element 를 렌더링하는지 물어본다.
 이것을 **Refining ** 이라고 한다.
 
```ts
function Button() {
	return (<button className={'button button-blue'}><b>OK</b></button>);
}

{
  type: Button,
  props: {
  }
}
// React will ask to this element 'what will you rendering'=>
{
  type: 'button',
  props: {
    className: 'button button-blue',
    children: {
      type: 'b',
      children: 'OK!'
    }
  }
}
```
React 는 위 Refine 작업 과정을 element type 이 DOM string type 이 나올 때 까지 반복한다.
---
# React Instance
- class Component 를 통해서만 생성이 된다.

- own properties and states 를 가진다.

- DOM node 에 대한 참조, 혹은 children components 에 대한 참조를 유지, 변경, 삭제 등을 할 수 있다.



> 그러나 사실 개발자가 프로그래밍 함에 있어서 Instance 가 그리 중요하지는 않다.
> 오히려 Element 및 Component 에 대해서 더 많이 신경을 쓰면 썼지...
---
# React's work
리액트에서는 실제 DOM 에 UI 를 생성하기 위해서 다음의 두가지 과정을 거친다

1. 렌더링(rendering) 단계: 변경 해야할 사항을 결정한다. render 함수를 통해서 element 를 구한 후,
 이전 렌더링과 비교(diffing)를 통해서 변경 사항을 결정한다.
 
1. 커밋(commit) 단계: 변경 사항을 실제 DOM 에 적용하는 단계.
(참고로 이 단계에서 componentDidMount 등과 같은 생명 주기 함수들을 호출함)

![alt text](https://image.toast.com/aaaadh/real/2018/techblog/9e96aa72130711e78c05dfb7b33cd7be.png)

** UI 에 대한 제어를 최소화 할 수 있다** (실제 DOM 을 다루는 것에 비해서 훨씬 가벼운 element 를 다루므로 성능상 이점)
---
# 최적화는 어떻게 할까?

![alt text](data:image/png;base64,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)






--- 

# Use keys
React 에서는 기본적으로 자식 엘리먼트들에 대해 반복적인 비교를 할 때, 
React는 이전/다음 상태의 자식 엘리먼트 목록을 함께 반복하고 그 차이를 본다. 때문에 정렬과 같은 다음의 상황에 취약하다.

```html
{/* Before */}
<ul>
  <li>first</li> {/* prev-first */}
  <li>second</li>  {/* prev-second */}
</ul>

{/* After (with reordering) */}
<ul>
  <li>second</li> {/* Compares prev-first --> Update dom */}
  <li>first</li> {/* Compares prev-second --> Update dom */}
  <li>third</li> {/* Compares prev --> Insert dom */}
</ul>
```
---
엘리먼트들에게 Key 속성을 명시적으로 부여하여 위와 같은 상황에 발생하는 필요 없는 업데이트를 최소화시킬 수 있다.

```html
{/* Before */}
<ul>
  <li key="first">first</li> {/* prev-first */}
  <li key="second">second</li>  {/* prev-second */}
</ul>

{/* After (with reordering) */}
<ul>
  <li key="second">second</li> {/* Compares prev-second --> Update X, Reorder dom */}
  <li key="first">first</li> {/* Compares prev-first, --> Update X, Reorder dom */}
  <li key="thrid">third</li> {/* Compares prev --> Insert dom */}
</ul>
{*/ But!!! only same level! */}
```

---
# Avoid Reconciliation
# Refining 작업과 diffing 작업을 skip 하자!
- 이전 input(props) 값과의 비교를 통해서! shallow-compare 는 빠르다구!
---
# shouldComponentUpdate
- 컴포넌트가 렌더링 전에 호출하는 라이프사이클 메서드.
- return 하는 boolean 값을 통해서 리렌더링 여부를 결정할 수 있다. (default true)
- false 면 render 함수도 실행이 안됨


![alt text](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/5e833b9e-dec9-4e76-9d91-3378245233b9/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20201013%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20201013T232707Z&X-Amz-Expires=86400&X-Amz-Signature=4a3bebaa4cc9e54ec43fb844167a347c5f5a32279423213f0c9796daff0d39f4&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)
---
```ts
class CounterButton extends React.Component {
  constructor(props) {
    super(props);
    this.state = {count: 1};
  }

  shouldComponentUpdate(nextProps, nextState) {
    if (this.props.color !== nextProps.color) {
      return true;
    }
    if (this.state.count !== nextState.count) {
      return true;
    }
    return false;
  }

  render() {
    return (
      <button
        color={this.props.color}
        onClick={() => this.setState(state => ({count: state.count + 1}))}>
        Count: {this.state.count}
      </button>
    );
  }
}
```
---
# PureComponent
- shouldComponentUpdate 를 수행할 때 기본적으로 shallow-compare 를 통해 render 여부를 결정함.
- PureComponent 에서 shouldComponentUpdate 를 구현하는 것은 PureComponent 의 구현을 무시하는 것이다. (쓰지 말자)

```ts
class CounterButton extends React.PureComponent {
  constructor(props) {
    super(props);
    this.state = {count: 1};
  }

  render() {
    return (
      <button
        color={this.props.color}
        onClick={() => this.setState(state => ({count: state.count + 1}))}>
        Count: {this.state.count}
      </button>
    );
  }
}
```
---
# React.memo
- functional component 의 경우 React.memo 를 사용한다.
- 고차 컴포넌트.(HOC)
- 렌더링 결괴(element tree)를 메모이징 하여 동일한 props 를 입력한다면 렌더링하지 않고 마지막으로 렌더링 된 결과
재사용
- default 로 props 의 shallow-compare 를 수행하지만 areEqual 함수를 통해서 커스텀하게 rendering 여부 결정 가능


```ts
function MyComponent(props) {
  /* props를 사용하여 렌더링 */
}
function areEqual(prevProps, nextProps) {
  /*
  nextProp가 prevProps와 동일한 값을 가지면 true를 반환하고, 그렇지 않다면 false를 반환
  */
}
export default React.memo(MyComponent, areEqual);
```
# with Mobx

> Mobx 로 React 를 최적화하려면??
---
## 1. observable 한 값을 읽는(read) 모든 컴포넌트에 observer(mobx-react)를 사용한다.
- observer 함수는 컴포넌트를 향상시키는 곳에만 사용되는 함수일 뿐.
- 최적화가 자동이다!
  - read 하는 값만 tacking 한다.
  - read 하는 값이 변경될 때만 re-render 가 실행됨.
  - observer 는 observable 한 객체(or array, map)에서 dereference 를 할 때 반응한다.

```ts
const myTimer = new Timer() // observable

const TimerView = observer(({ timer }) => <span>Seconds passed: {timer.secondsPassed}</span>)

ReactDOM.render(<TimerView timer={myTimer} />, document.body)

```

[CodeSnadBox](https://codesandbox.io/s/minimal-observer-forked-uufiy?file=/src/index.tsx)
---
## 2. dereference 를 최대한 늦게한다.
- 가능한 오랫동안 객체(및 배열 등등) 참조를 전달하고 DOM 을 랜더링하는 component 와 같이 low-level component 내에서 해당 property 들을 read 한다.


```ts
const TimerView = observer(({ secondsPassed }) => <span>Seconds passed: {secondsPassed}</span>)

React.render(<TimerViewer secondPassed={myTimer.secondsPassed} />, document.body)
// 위와 같이 구현되어 있다면, TimerView 는 React 하지 않을 것이다
```
제대로 이해하려면 
[mobx 의 react](https://mobx.js.org/understanding-reactivity.html) 를 읽어보는 것이...

---
# ETC
## 그 외의 방법들
---
# use virtualized long list
- 아주 많은 row 를 포함한 list 데이터를 표현해야 한다면, windowing 이라고 알려진 기술을 사용하면 성능상 이점이 있다
- 실제 많은 row 들 중 일부의 rows 만 렌더링 하는 기술.
-  react(웹) 에서는 react-window, react-virtualized 가 유명한 window 라이브러리이다.
- react-native 에서는 VirtualizedList, FlatList, SectionList 를 사용하면 된다!

![alt text](https://webdev.imgix.net/virtualize-long-lists-react-window/difference-in-scrolling.jpg)
---

# 컴포넌트 분리

> 컴포넌트가 적절히 분리 되지 않으면 가독성, 유지 보수성에만 문제가 생기는 것이 아니라 성능적으로도 손실을 볼 수 있는 경우가 있다.


```ts
class App extends Component {
	render() {
		return (
			<div className="app">
		    ...
		    <div className="app-intro">
		      {this.state.title}
		    </div>
		    <div className="list-container">
		      <ul>
		      {
		        this.state.items.map((item) => {
		          return <Item key={item.id} {...item} />
		        })
		      }
		      </ul>
		    </div>
		  </div>
		)
	}
}
```
위 코드의 경우 title 이 변경이 된다면 어떻게 될까?
[CodeSandBox](https://codesandbox.io/s/tender-breeze-21kfx?file=/src/App.tsx)
---
# send appropriate props

```jsx
render() {
  return (
    <div className="app">
      ...
      <div className="app-intro">
        {this.state.title}
      </div>
      <List items={this.state.items} deleteItem={id => this.deleteItem(id)}/>
    </div>
  );
}

```
위 코드에서는 List 가 PureComponent 로 구현되어 있다고 해도 렌더링이 일어난다!

왜냐하면 render 가 실행되면서 deleteItem 의 함수를 새로 생성하기 때문이다. 때문에 함수는 생성자에서 미리 바인딩하고 새로운 함수를 생성하지 않고 전달하는 것이 일반적으로 성능 이슈에 이점이 있다.







