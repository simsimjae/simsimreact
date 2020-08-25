이제 바벨 세팅이 끝났으니, 실제로 가상돔을 만들어봅시다. 리액트에서의 가상돔은 사실상 리액트 엘리먼트 트리입니다.

React.createElement 메소드를 직접 구현해봅시다.

**아래 내용들은 src/simsimReact.js에 작성되어 있습니다.**

# React.creatElement
```javascript
const simsimReact = {
  createElement: (type, props = {}, ...children) => {
      console.log(type, props, children);
  };
};

export default simsimReact;
```

이 createElement는 3개의 인자를 받습니다.
### type
type에는 ``div``, ``span`` 등과 같은 html 태그가 올 수도 있고 리액트 컴포넌트가 들어올수도 있습니다.
리액트 컴포넌트는 함수나 객체입니다. 따라서 type에는 함수, 문자열, 객체 3가지 타입만 올 수 있습니다.

### props
리액트 컴포넌트에 전달된 props를 두번째 인자로 전달합니다.

### children
마지막 인자에는 리액트 컴포넌트의 자식컴포넌트들을 배열의 형태로 전달받습니다.


### 실행해보기
<img src='./images/2020-08-25-22-02-17.png' width="50%" >

# Snabbdom 으로 virtual DOM만들기
```bash
npm install snabbdom
```
snabbdom을 먼저 설치해주고 나서 아래 코드를 봅시다.
```javascript
import { h } from 'snabbdom/build/package/h';

const simsimReact = {
  createElement: (type, props = {}, ...children) => {
     return h(type, { props }, children);
  }
};

export default simsimReact;
```
snabbdom의 헬퍼함수 h를 통해서 가상돔을 만들어주고 있습니다. 

정리를 해보자면, 이제 우리는 JSX를 우리만의 함수로 트랜스파일링 한 뒤, virtual DOM까지 만들 수 있는 상태가 되었습니다. 이제 다음 스텝은 이 virtual DOM을 real DOM에 반영하는 작업입니다.


