마지막으로 컴포넌트형 클래스에 이벤트 리스너를 달아봅시다.
이것도 마찬가지로 snabbdom에서 eventListener를 지원해주기 떄문에 가져다 사용해봅시다.

이벤트 리스너를 테스트하기 위해서 이번에는 1초마다 카운트가 증가되는게 아니라
버튼을 눌렀을때 카운트가 증가되게끔 해보겠습니다.

### src/counter.js
```js
class Counter extends QndReact.Component {
  constructor(props) {
    super(props);

    this.state = {
      count: 0
    }
  }

  componentDidMount() {
    console.log('컴포넌트가 마운트 되었습니다.');
  }

  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={() => this.setState({
          count: this.state.count + 1
        })}>Increment</button>
      </div>
    )
  }
}
```

아직은 snabbdom이 이벤트를 수신할 수 없기 때문에 버튼을 눌러도 아무런 동작을 하지 않을것입니다.

### src/simsimReactDom.js
```js
import { eventListenersModule } from "snabbdom/build/package/modules/eventlisteners";

const reconcile = init([propsModule, eventListenersModule]);
```
snabbdom에서 eventListenersModule를 붙입시다. 이렇게 되면 이제 snabbdom으로 만든 가상돔은 이벤트를 수신할 수 있습니다.


### src/simsimReact.js
```js
const createElement = (type, props = {}, ...children) => {
	// ... 생략
	
	props = props || {};
	let dataProps = {};  // 일반 props들 (class, id, data, style등)
	let eventProps = {}; // onClick, onKeyUp... 등의 이벤트핸들러

	for (let propKey in props) {
		if (propKey.startsWith("on")) {
			const event = propKey.substring(2).toLowerCase(); // 이벤트 이름만 추출

			eventProps[event] = props[propKey];
		} else {
			dataProps[propKey] = props[propKey];
		}
	}

	return h(type, { props: dataProps, on: eventProps }, children);
};
```

리액트에서는 이벤트 핸들러를 onClick과 같이 표현합니다. 하지만 자바스크립트에서는 onclick과 같이 표현하죠
그래서 위와 같이 변환 과정이 필요한겁니다.

그리고 나서 snabbdom의 헬퍼함수에 props와 on 필드에 두 props를 구분해서 담아줍니다.

eventProps는 아래와 같은 형태일겁니다.
```js
{
	onclick : () => {}
	onkeyup: () => {}
	// ...
}
```

![](images/20200826.gif)

이제 버튼을 클릭하면 카운터가 증가됩니다.

이로써 리액트 코어를 간단하게 클론해 보았습니다.