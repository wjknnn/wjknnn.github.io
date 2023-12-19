---
title: this
author: wjknnn
date: 2023-12-15 19:57:00 +0900
categories: [Language, Javascript]
tags: [Javascript, this]
render_with_liquid: false
image:
  path: /assets/img/coverImg/js-this.webp
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Javascript this
---

js에서의 `this`가 상황에 따라서 어떤 값을 가지는지 가끔 헷갈리는 경우가 종종 있다.
하지만 알고보면 꽤 간단하다.
`this`는 다음과 같은 규칙을 따른다.

## 1. new 바인딩

만약 함수를 호출할 때 `new`키워드를 사용한다면, 함수 내부에 있는 `this`는 완전히 새로운 객체를 반환해 준다.

```js
function constructorExample() {
  this.value = 10;
  console.log(this);
}

new constructorExample();

// output : { value: 10 }
```

## 2. 명시적 바인딩

만약 함수를 호출할 때 `apply`나 `call` 또는 `bind`를 사용한다면, 함수 내부의 `this`는 인수로 전달된 객체를 반환해 준다.

```js
function fn() {
  console.log(this);
}
const obj = {
  value: 5
};
const boundFn = fn.bind(obj);

boundFn(); // output : { value: 5 }
fn.call(obj); // output : { value: 5 }
fn.apply(obj); // output : { value: 5 }
```

## 3. 암시적 바인딩

만약 객체 안의 함수를 메서드로 호출한다면, `this`는 함수가 포함된 객체를 반환해 준다.

```js
const obj = {
  value: 5,
  printThis: funtion() {
    console.log(this);
  }
};
obj.printThis(); // output : { value: 5, printThis: f }
```

## 4. 기본 바인딩

함수가 위의 조건 없이 호출된 경우, `this`는 전역 객체이며, 브라우저에서는 Window 객체다.
만약 엄격모드`'use strict'` 일 경우, `this`는 전역 객체 대신 `undefined`가 된다.

```js
function fn() {
  console.log(this);
}
// 만약 브라우저에서 호출했을 경우
fn(); // output : Window { window: Window, self: Window, ... }
```

사실 이 규칙은 3번째 규칙과 동일하다. 차이점은 메서드로 선언되지 않은 함수는 자동으로 전역 객체인 window의 속성이 된다는 것이다.
따라서 이는 암시적 매서드 호출이다. `fn()`을 호출하면 `window.fn()`으로 해석되므로 `this`는 window가 된다.

```js
console.log(fn === window.fn); // output : true
```

## 5. 여러 규칙

위의 규칙이 여러개 적용되는 경우에는 더 높은 우선순위의 규칙이 적용된다.
우선순위는 `new 바인딩` > `명시적 바인딩` > `암시적 바인딩` > `기본 바인딩` 순으로 이루어져 있다.

```js
const obj1 = {
  value: "hi",
  print: function () {
    console.log(this);
  }
};
const obj2 = { value: 17 };
```

아래는 `명시적 바인딩`과 `암시적 바인딩`이 모두 적용된 예시다. 이 때는 `명시적 바인딩`이 우선적으로 적용된다.

```js
obj1.print.call(obj2); // output : { value: 17 }
```

아래는 `new 바인딩`과 `암시적 바인딩`이 모두 적용된 예시다. 이 때는 `new 바인딩`이 우선적으로 적용된다.

```js
new obj1.print(); // output : print {}
```

## 6. 화살표 함수

함수가 ES2015 화살표 함수인 경우에는 위의 모든 규칙을 무시하고, 생성될 때 주변 스코프의 this 값을 받는다.

```js
const obj = {
  value: "abc",
  createArrowFn: function () {
    return () => console.log(this);
  }
};
const arrowFn = obj.createArrowFn();
arrowFn(); // output : { value: 'abc', createArrowFn: f }
```

3번째 규칙으로 돌아가서 `obj.createArrowFn()`을 호출하면 `createArrowFn()`내부의 `this`는 암시적으로 바인딩 되어 `obj`가 된다. 따라서 `obj`는 `arrowFn`의 `this`에 의해 바인딩된다.
만약 전역 범위에서 화살표 함수를 생성한다면 `this`는 `window`가 된다.
