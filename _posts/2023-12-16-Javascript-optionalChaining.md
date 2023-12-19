---
title: "[Javascript] Optional chaining"
author: wjknnn
date: 2023-12-16 23:35:00 +0900
categories: [Language, Javascript]
tags: [Javascript, Optional chaining]
render_with_liquid: false
image:
  path: /assets/img/coverImg/js-optionalChaining.webp
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Javascript Optional chaining
---

Optional chaining 문법은 프로퍼티가 없는 중첩 객체를 에러 없이 안전하게 접근할 수 있게 해준다.

## 사용 이유

아래의 코드를 보자. user객체의 street값을 콘솔에 출력하려 한다.
그렇다면 출력 코드를 아래와 같이 짜게 될 것이다.

```jsx
let user = {
  name: "John",
  age: 18,
  address: {
    street: "AwesomeLoad"
  }
};

console.log(user.address.street);
// AwesomeLoad
```

그럼 만약 아래와 같은 상활일 때 street을 출력하면 어떻게 될까?

```jsx
let user = {};

console.log(user.address.street);
// TypeError: Cannot read property 'street' of undefined
```

당연하게도 출력하려고 참조하는 값들이 모두 undefined 이기 때문에 오류가 발생하게 된다.
그럴 때 우리가 사용하던 것이 바로 && 연산자이다.

```jsx
console.log(user && user.address && user.address.street);
// undefined (에러가 발생하지 않음)
```

이렇게 AND 연산자를 이용해서 사용하면 에러를 예방할 수 있지만,
코드가 매우 길어질 수 있다는 단점이 있다.
그렇게 해서 나오게 된것이 옵셔널 체이닝(Optional chaining)이라는 문법이다.

## 사용 방법

사용방법은 간단하다. 일단 옵셔널 체이닝의 기본적인 구조는 `?.` 이다.

`?.` 은 앞의 대상이 undefined나 null이면 확인을 멈추고 undefined를 반환한다.
즉, `?.` 앞의 대상의 값이 있을 수도 있고 없을 수도 있을 때 undefined를 반환한다고 보면 된다.

```jsx
let user = {};

console.log(user?.address?.street);
//undefinfed
```

이제 아까와 같은 동작의 코드를 쓰면
AND 연산자를 사용했을 때보다 더 짧은 코드로 평가할 수 있게 되었다.

> 옵셔널 체이닝을 남용해서는 안된다.
> 꼭 있어야 하는데 없는 경우에 ?. 을 사용하면 디버깅이 어려워지니, 지양하도록 하자.
> 그러니 꼭 존재하지 않아도 괜찮은 대상에만 사용하는 것이 바람직한 방법이다.
> {: .prompt-warning }

> ?. 앞의 변수는 꼭 선언되어 있어야 한다.
> user?.address를 사용하려면 let이나 const로 user를 정의 해줘야 한다.
> 이렇게 옵셔널 체이닝은 선언이 완료된 변수를 대상으로만 작동한다.
> {: .prompt-warning }

`?.` 은 왼쪽 평가 대상에 값이 없으면 즉시 평가를 멈춘다.
이런 평가 방법을 단락 평가라고 한다.

그렇기 때문에 함수 호출과 같은 `?.` 오른쪽에 있는 부가 동작들은 평가가 멈췄을때, 작동을 하지 않게 된다.
그리고 `?.` 은 함수 호출을 하는 경우에 이 `?.()` 과,
대괄호를 사용한 객체 프로퍼티 접근에 이 `?.[]` 을 쓴다.

마지막으로, `?.` 은 아래 코드와 같이 읽거나 삭제는 가능하지만, 쓰기에는 사용이 불가능하다.

```js
let user = {
    name: "John",
    age: 18,
    address: {
        street: "AwesomeLoad"
    }
};

console.log(user?.address.street);

delete user?.address.street;

user?.name = "Wick";
```

불가능한 이유는, user?.name이 undefined일 수도 있다는 것이 `?.` 을 붙임으로써
나타났기 때문에, undeifined = “Wick”; 꼴이 되어 에러나 나는 것 같다.

에러 없이 저 조건 그대로 할당하고 싶으면 아래 코드를 쓰면 될 것 같다.

```js
if (user !== undefined) {
  user.name = "Wick";
}
```
