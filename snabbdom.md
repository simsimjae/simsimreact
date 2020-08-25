---
description: Virtual DOM 라이브러리인 Snabbdom에 대해 살펴봅시다.
---

# Snabbdom 살펴보기

## Virtual DOM이란?

리액트에서 가상돔을 한마디로 설명하면 **"실제로 화면에 이렇게 그려주세요를 JSON으로 표현한것"**이라고 할 수 있을것 같다.우리는 그저 JSON만 UI라이브러리\(리액트와같은\)에 전달만 해주면 그 라이브러리가 JSON을 해석해서 화면을 그리게 만들 수 있는것이다.이렇게 하면 매번 DOM에 직접 접근하지 않아도 UI를 업데이트할 수 있다. DOM에 접근하는것은 생각보다 비용이 비싸다.

이전에 그려놓은 가상돔과 이번에 새로 그린 가상돔을 비교해서 변경된 부분만 real DOM에 반영한다.여기서 이전 가상돔과 현재 가상돔을 비교하는 과정을 리액트에서는 **reconciliaion\(재조정\)**이라고 한다.리액트에서의 가상돔이란 그냥 **JSON으로 표현된 리액트 엘리먼트 트리**이다.

리액트에서는 리액트 내부적으로 Virtual DOM을 구현한것이다.우리는 유명한 가상돔 라이브러리 중 하나인 **Snabbdom**을 살펴보기로한다.이 라이브러리는 Vue.js의 가상돔으로 활용되고 있다고 한다.

## Snabbdom퀵스타트

```javascript
import snabbdom from 'snabbdom';

const patch = snabbdom.init([                   
  require('snabbdom/modules/class'),          
  require('snabbdom/modules/props'),          
  require('snabbdom/modules/style'),          
  require('snabbdom/modules/eventlisteners'), 
]);
```

snabbdom은 기능을 잘게 쪼개놓았다.우리가 필요한것들을 가지고 초기화 해보자. snabbdom의 init메소드는 patch라는 함수를 리턴한다.

## Snabbdom의 가장 간단한 형태
```javascript
import h from 'snabbdom/h';

var vnode = h('div', {style: {fontWeight: 'bold'}}, 'Hello world');
patch(document.getElementById('placeholder'), vnode);
```
snabbdom의 h는 helper function의 h이다. 가상돔을 만들기 위한 헬퍼 함수라고 생각하면 된다. 이 함수를 통해 폰트 사이즈가 굵은 Hello world가 div를 렌더링 해달라고 요구하는 가상돔을 만든 예제이다. __(vnode)__

그리고 그 가상돔 vnode를 placeholder라는 아이디를 가진 real DOM에 반영(__patch__)하고 있다.

```javascript
var vnode = h('div', {style: {fontWeight: 'bold'}}, 'Hello world');
```
이 부분에서 h의 3번쨰 인자에는 가상돔의 자식 가상돔이 들어가는 자리이다.

## 이전 가상돔과 현재 가상돔을 비교하기
```javascript
function view(currentDate) { 
  return h('div', 'Current date ' + currentDate); 
}

var oldVnode = document.getElementById('placeholder');

setInterval( () => {
  const newVnode = view(new Date());
  oldVnode = patch(oldVnode, newVnode);
}, 1000);
```
위에서 view라는 함수는 현재 시간을 나타내는 가상돔을 리턴한다.
즉 위의 코드는 1초마다 반복적으로 새로운 가상돔을 만들고 이전 가상돔과 비교해서 변경된 부분만 real DOM에 반영하는 예제이다.

가상 돔 기반의 UI라이브러리들이 보통 이런식으로 이전 가상돔과 변경된 가상돔을 비교해서 real DOM에 반영하는 방식을 사용한다.(리액트에서도 그렇다.)

## Snabbdom에서의 이벤트 처리
```javascript
function view(name) { 
  return h('div', [
    h('input', {
      props: { type: 'text', placeholder: 'Type  your name' },
      on   : { input: update }
    }),
    h('hr'),
    h('div', 'Hello ' + name)
  ]); 
}
```
위 view는 마찬가지로 가상돔을 리턴한다.
가장 루트에는 div가 있고 div 안에는 3개의 __input__, __hr__, __div__ 자식들이 존재한다.
물론 이 자식들은 마찬가지로 **real DOM**이 아니고 **virtual DOM**이다.

그리고 input virtual DOM은 props와 on을 갖는데, 여기서 on이 이벤트핸들러이다. 
즉 위의 input은 무언가 키보드에 입력되어 input 이벤트가 발생하면 **update**함수를 실행시켜준다.

```javascript
function view(name) { 
  return h('div', [
    h('input', {
      props: { type: 'text', placeholder: 'Type  your name' },
      on   : { input: update }
    }),
    h('hr'),
    h('div', 'Hello ' + name)
  ]); 
}

var oldVnode = document.getElementById('placeholder');

function update(event) {
  const newVnode = view(event.target.value);
  oldVnode = patch(oldVnode, newVnode);
}

oldVnode = patch(oldVnode, view(''));
```
첫번째로 update함수가 실행되면 oldVnode에는 realDOM이 들어있다. 이때는, 이전 가상돔과 현재 가상돔을 비교해서 realDOM에 반영하는게 아니라 바로 newVnode가 realDOM에 반영된다.

그리고 두번째로 update가 실행되면 이때는 oldVnode에는 patch에서 리턴된 이전 가상돔이 들어있기 때문에 이전 가상돔과 현재 가상돔을 비교한 뒤 변경된 부분을 realDOM에 반영한다.
