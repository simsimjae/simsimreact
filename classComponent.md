### src/simsimReact.js
```javascript
class Component {
	constructor() {}

	componentDidMount() {}

	setState(partialState) {}

	render() {}
}

const simsimReact = {
  // ...
  Component
};

export default simsimReact;
```
우선, 이런식으로 클래스형 컴포넌트의 기본 토대를 만들어주고 simsimReact 객체에 Component 필드를 추가해줍시다.

그럼 이제 이 클래스형 컴포넌트를 상속받는 클래스는 리액트 컴포넌트로써 역할을 할 수 있게 되었습니다.
테스트를 해보기 위해 카운터 컴포넌트를 한번 만들어보겠습니다.

### src/counter.js
```javascript
import simsimReact from "./simsimReact";

class Counter extends simsimReact.Component {
	constructor(props) {
		super(props);

		this.state = {
			count: 0,
		};
	}

	componentDidMount() {
		console.log("카운터 컴포넌트가 마운트 되었습니다.");
	}

	render() {
		return <p>Count: {this.state.count}</p>
	}
}

export default Counter;
```
이제 이 컴포넌트가 마운트되면 콘솔에 **"카운터 컴포넌트가 마운트 되었습니다."**가 출력되어야 합니다.
그럼 이 컴포넌트를 화면에 렌더링 해볼까요?

### src/index.js
```javascript
import simsimReact from "./simsimReact";
import simsimReactDom from "./simsimReactDom";
import Counter from "./counter";

const App = (
	<div>
		<h1 className="primary">simsimreact</h1>
		<p>100줄 이하의 자바스크립트로 리액트 코어를 클론해봅시다.</p>
		<Counter /> // 추가됨
	</div>
);

simsimReactDom.render(App, document.getElementById("root"));
```
여기까지 해주면 문제 없이 Counter 클래스 컴포넌트가 렌더링 될 것 같지만 오류가 납니다.

![](images/2020_08_26_16_55_20.png)

이 오류가 난 원인은, 바벨이 ES6의 class를 함수로 변환하기 떄문입니다.
오류의 원인을 찾기 위해서 simsimReact로 가봅시다.

### src/simsimReact.js
```javascript
import { h } from "snabbdom/build/package/h";

const createElement = (type, props = {}, ...children) => {
	console.log(typeof (type), type); // 로그를 찍어봅시다.
	
  if (typeof type == "function") {
    return type(props);
  }

  return h(type, { props }, children);
};
```

![](images/2020_08_26_16_57_41.png)

위 로그를 잘 보면 우리는 분명히 Counter를 ES6의 클래스로 정의했는데 로그에 찍히는걸 보니 함수로 변환되어있습니다.
바벨이 변환했기 떄문입니다.


그럼 type에 전달되는건 무조건 함수라는 소린데, 우리는 리액트 엘리먼트를 생성할때 클래스형 컴포넌트인지 함수형 컴포넌트인지 어떻게 구분할까요?
클래스인지 함수인지에 따라 내부 동작이 달라져야합니다.

그래서 우리는 클래스형 컴포넌트의 내부 static 속성으로 isSimsimReactClassComponent를 true로 만들것입니다.
createElement함수 내부에서 이 값이 true인지 확인해서 분기처리를 할거에요.


### src/simsimReact.js
```js
import { h } from "snabbdom/build/package/h";

...

class Component {
  constructor() { }

  componentDidMount() { }

  setState(partialState) { }

  render() { }
}

Component.prototype.isSimsimReactClassComponent = true; // 클래스형 컴포넌트임을 표시

const simsimReact = {
  createElement: (type, props = {}, ...children) => {
  if (type.prototype && type.prototype.isSimsimReactClassComponent) {
    const componentInstance = new type(props);
    return componentInstance.render();
	}
	
  if (typeof type == "function") {
    return type(props);
  }

  return h(type, { props }, children);
	},
  Component
};

export default simsimReact;
```
만약에 type의 prototype에 isSimsimReactClassComponent 필드가 true로 설정되어있다면 이 type은 클래스형 컴포넌트라는 뜻입니다.
따라서, 컴포넌트의 인스턴스를 생성해서 render메소드를 호출합니다. 

render메소드 내부에는 JSX들이 들어있고 이것들이 바벨에 의해 리액트 엘리먼트 트리(virtual DOM)으로 변환되기 때문에
결론적으로는 createElement가 virtualDOM을 리턴하게 되는것입니다. 


![](images/2020_08_26_16_54_02.png)

드디어 카운터가 화면에 노출되었습니다.


