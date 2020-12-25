---
description: 100줄 이내 자바스크립트만으로 리액트의 코어를 클론해봅니다.
---

# simsimreact - 리액트 코어 클론

100줄 이내 자바스크립트만으로 리액트의 코어를 클론해봅니다.

repository: [https://github.com/depromeet/simsimjae](https://github.com/depromeet/simsimjae) 

parcel을 사용하여 번들링 하였습니다.

## 이 튜토리얼에서 만들고자 하는것

* JSX
* 함수형컴포넌트
* 클래스형컴포넌트 with State
* 라이프사이클 훅스\(componentDidMount\)

## 이 튜토리얼에서 만들지 않을것

### Virtual DOM

virtual DOM은 직접 만들지 않고 snabbdom 이라는 가상 돔 라이브러리를 가져다 사용할것입니다.리액트의 렌더링 로직이 어떻게 돌아가는지를 이해하기 위한 목적의 튜토리얼이므로 가상돔은 라이브러리를 통해 구현합니다. [Snabbdom 살펴보기](https://github.com/simsimjae/simsimreact/tree/91803ec9d3d7848150fc56519af3b150039e60f9/snabbdom.html)

### React Hooks

우리는 이 튜토리얼에서 가장 기초적인 부분만 다룰것이기 때문에 훅스는 다루지 않습니다.

### 디버깅

마찬가지로 디버깅도 신경쓰지 않습니다.

### 퍼포먼스

퍼포먼스도 신경쓰지 않고 최대한 간단하게 만들어 보겠습니다

