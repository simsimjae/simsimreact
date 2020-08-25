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

