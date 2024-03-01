+++
categories = ['JavaScript']
project_url = 'https://e6d1fe.github.io/e6d1fe-blog/post/javascript/'
title = 'async/await'
tags = ['JavaScript', 'async', 'await']
date = 2024-03-01T16:54:26+09:00
draft = false
author = 'Sehyun'
comments = true
readingTime = true
+++

## async/await

`async`/`await`는 프로미스를 기반으로 동작한다.  
`async`/`await`를 사용하면 프로미스의 `then`/`catch`/`finally` 후속 처리 메서드에 콜백 함수를 전달해서 비동기 처리 결과를 후속 처리할 필요 없이 마치 동기 처리처럼 프로미스를 사용할 수 있다.

```javascript
async function fetchTodo() {
  const url = 'https://someurl.com/1';
  const response = await fetch(url);
  const todo = await response.json();
  console.log(todo);
}

fetchTodo();
```

### `async` 함수

**`await` 키워드는 반드시 `async` 함수 내부에서 사용해야 한다.**  
`async` 함수는 `async` 키워드를 사용해 정의하며 언제나 프로미스를 반환한다.
`async` 함수가 명시적으로 프로미스를 반환하지 않더라도 `async` 함수는 암묵적으로 반환값을 resolve하는 프로미스를 반환한다.

```javascript
// async 함수 선언문
async function foo(n) {
  return n;
}
foo(1).then((value) => console.log(value)); // 1

// async 함수 표현식
const bar = async function (n) {
  return n;
};
bar(2).then((value) => console.log(value)); // 2

// async 화살표 함수
const baz = async (n) => n;
baz(3).then((value) => console.log(value)); // 3

// async 메서드
const obj = {
  async foo(n) {
    return n;
  },
};
obj.foo(4).then((value) => console.log(value)); // 4

// async 클래스 메서드
class MyClass {
  async bar(n) {
    return n;
  }
}
const myClass = new MyClass();
myClass.bar(5).then((value) => console.log(value)); // 5
```

### `await` 키워드

`await` 키워드는 프로미스가 `settled` 상태(비동기 처리가 수행된 상태)가 될 때까지 대기하다가 해당 상태가 되면 프로미스가 resolve한 처리 결과를 반환한다.  
**이 키워드는 반드시 프로미스 앞에서 사용해야 한다.**

```javascript
async function fetchTodo() {
  const url = 'https://someurl.com/1';
  const response = await fetch(url);
  const todo = await response.json();
  console.log(todo);
}

fetchTodo();
```

이 예시의 경우

1. `fetch` 함수가 수행한 HTTP 요청에 대한 서버의 응답이 도착해 `fetch` 함수가 반환한 프로미스가 `settled` 상태가 될 때까지 대기한다.
2. 이후 프로미스가 `settled` 상태가 되면 `resolve`한 처리 결과가 `response` 변수에 할당된다.

### 에러 처리

`async`/`await`에서 에러 처리는 `try ... catch` 문을 사용할 수 있다.  
콜백 함수를 인수로 전달받는 비동기 함수와는 달리 프로미스를 반환하는 비동기 함수는 명시적으로 호출할 수 있기 때문에 호출자가 명확하기 때문이다.

```javascript
async function foo() {
  try {
    const wrongUrl = 'https://wrongUrl.com';
    const response = await wrongUrl;
    const data = await response.json();
    console.log(data);
  } catch (err) {
    console.log(err); // TypeError: Failed to fetch
  }
}
```

`async` 함수 내에서 `catch`문을 사용해 에러 처리를 하지 않으면 `async` 함수는 발생한 에러를 `reject`하는 프로미스를 반환한다.  
따라서 `async` 함수 호출 후 `catch` 후속 처리 메서드를 사용해 에러를 캐치할 수도 있다.
