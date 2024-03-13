+++
categories = ['JavaScript']
project_url = 'https://e6d1fe.github.io/e6d1fe-blog/post/javascript/'
title = 'Closure'
tags = ['JavaScript', 'intl']
date = 2024-03-13T09:43:22+09:00
draft = false
author = 'Sehyun'
comments = true
readingTime = true
+++

## Closure

**"the combination of a function bundled together (enclosed) with references to its surrounding state (the lexical environment)"**

➡️ 클로저는 내부 함수가 외부 함수의 스코프에 접근할 수 있도록 함

**lexical scoping**: 함수가 사용 가능한 범위가 **함수가 선언된 위치**에 따라 결정되는 것

---

```javascript
function makeFunc() {
  const name = 'Mozilla';
  function displayName() {
    console.log(name);
  }
  return displayName;
}

const myFunc = makeFunc();
myFunc(); // Mozilla
```

`displayName`이라는 내부 함수는 실행되기 전에 반환되는데, 이 코드는 어떻게 작동하는 걸까?  
`makeFunc` 함수가 실행 종료되면 그 안에 있는 로컬 변수 `name`은 더이상 접근 불가능한 것 아닌가?

이게 가능한 이유: **자바스크립트 함수는 클로저를 생성하기 때문**

- 렉시컬 환경은 클로저가 생성되었을 때 스코프 내에 있던 로컬 변수를 포함함
- 위 에제에서 `myFunc`: `makeFunc`가 실행됐을 때 생성된 `displayName` 함수의 instance의 참조
- `displayName`의 instance는 **생성될 당시의 lexical environment의 참조**를 유지함 (그 lexical environment 내에 `name` 변수가 존재)
- **그래서 `myFunc` 함수를 실행했을 때 `name` 변수에 접근이 가능한 것 !**

---

You can use a closure anywhere that you might normally use an object with only a single method.  
It can also be used to define public functions that can access private functions and variables.
