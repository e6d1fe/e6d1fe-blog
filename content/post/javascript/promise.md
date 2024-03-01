+++
categories = ['JavaScript']
project_url = 'https://e6d1fe.github.io/e6d1fe-blog/post/javascript/'
title = 'Promise'
tags = ['JavaScript', 'promise']
date = 2024-03-01T16:54:26+09:00
draft = false
author = 'Sehyun'
comments = true
readingTime = true
+++

## Promise

### 비동기 처리를 위한 콜백 패턴의 단점

비동기 함수를 호출하면 함수 내부의 비동기로 동작하는 코드가 완료되지 않았다 해도 기다리지 않고 즉시 종료된다.  
**즉, 비동기 함수 내부의 비동기로 동작하는 코드는 비동기 함수가 종료된 이후에 완료된다.**  
따라서 비동기 함수 내부의 비동기로 동작하는 코드에서 처리 결과를 외부로 반환하거나 상위 스코프의 변수에 할당하면 기대한 대로 동작하지 않는다.  
예를 들어, 비동기 함수인 `setTimeout`의 콜백 함수는 `setTimeout` 함수가 종료된 이후에 호출된다.

비동기 함수는 비동기 처리 결과를 외부에 반환할 수도 없고, 상위 스코프의 변수에 할당할 수도 없기 때문에 비동기 함수의 처리 결과에 대한 후속 처리는 비동기 함수 내부에서 수행해야 한다.  
이때 비동기 함수를 범용적으로 사용하기 위해 후속 처리를 수행하는 콜백 함수를 전달하는 것이 일반적이다.  
이런 구조 때문에 콜백 함수 호출이 중첩되어 복잡도가 높아지는 현상이 발생하는데, 이를 콜백 헬이라고 한다.

```javascript
get('/step1', (a) => {
  get(`/step2/${a}`, (b) => {
    get(`/step3/${b}`, (c) => {
      get(`/step4/${c}`, (d) => {
        console.log(d);
      });
    });
  });
});
```

이에 대한 가장 심각한 문제점은 에러 처리가 곤란하다는 것이다.

```javascript
try {
  setTimeout(() => {
    throw new Error('Error');
  }, 1000);
} catch (e) {
  console.log(e);
}
```

위 예제에서 `setTimeout` 함수의 콜백 함수가 실행될 때 `setTimeout` 함수는 이미 콜 스택에서 제거된 상태다. (`setTimeout`의 콜백 함수를 호출한 것이 `setTimeout`이 아니라는 의미)  
에러는 호출자 방향, 즉 콜 스택의 아래 방향으로 전파된다.  
하지만 여기서 `setTimeout` 함수의 콜백 함수를 호출한 것은 `setTimeout` 함수가 아니므로, 이 콜백 함수가 발생시킨 에러는 `catch` 블록에서 캐치되지 않는다.

### 프로미스의 생성

`Promise` 생성자 함수는 비동기 처리를 수행할 콜백 함수를 인수로 전달받는데 이 콜백 함수는 `resolve`와 `reject` 함수를 인수로 전달받는다.

```javascript
const promise = new Promise((resolve, reject) => {
    if (/* success */) {
        resolve('result');
    } else {
        reject('reason for failure');
    }
});
```

프로미스는 다음과 같이 현재 비동기 처리가 어떻게 진행되고 있는지를 나타내는 상태 정보를 갖는다.

| 프로미스의 상태 정보 | 의미                                  | 상태 변경 조건                   |
| -------------------- | ------------------------------------- | -------------------------------- |
| `pending`            | 비동기 처리가 아직 수행되지 않은 상태 | 프로미스가 생성된 직후 기본 상태 |
| `fulfilled`          | 비동기 처리가 수행된 상태 (성공)      | `resolve` 함수 호출              |
| `rejected`           | 비동기 처리가 수행된 상태 (실패)      | `reject` 함수 호출               |

- 비동기 처리 성공: `resolve` 함수를 호출해 프로미스를 `fulfilled` 상태로 변경한다.
- 비동기 처리 실패: `reject` 함수를 호출해 프로미스를 `rejected` 상태로 변경한다.

이처럼 **프로미스의 상태는 `resolve` 또는 `reject` 함수를 호출하는 것으로 결정된다**.

프로미스는 `pending` 상태에서 `fulfilled`/`rejected` 상태로 변화할 수 있지만, 한 번 `fulfilled`/`rejected` 상태가 되면 더는 다른 상태로 변화할 수 없다.

```javascript
const fulfilled = new Promise((resolve) => resolve(1));

const rejected = new Promise((_, reject) => reject(new Error('error occurred')));
```

비동기 처리가 성공하면 프로미스는 `pending`에서 `fulfilled` 상태로 변화하고, 비동기 처리 결과인 `1`을 값으로 갖는다. (`PromiseResult`)  
비동기 처리가 실패하면 프로미스는 `pending`에서 `rejected` 상태로 변화하고, 비동기 처리 결과인 `Error` 객체를 값으로 갖는다. (`PromiseResult`)

### 프로미스의 후속 처리 메서드

프로미스가 `fulfilled` 상태가 되면 프로미스의 처리 결과를 가지고 뭘 할지, `rejected` 상태가 되면 프로미스의 처리 결과(에러)를 가지고 뭘 할지 후속 처리를 해야 한다.  
이를 위해 프로미스는 후속 메서드 `then`, `catch`, `finally`를 제공한다.  
프로미스의 비동기 처리 상태가 변화하면 후속 처리 메서드에 인수로 전달한 콜백 함수가 선택적으로 호출된다.

#### 📍 `Promise.prototype.then`

두 개의 콜백 함수를 인수로 전달받는다.

- 첫번째 콜백 함수는 프로미스가 `fulfilled` 상태가 되면 호출되며, 이때 콜백 함수는 프로미스의 비동기 처리 결과를 인수로 전달받는다.
- 두번째 콜백 함수는 프로미스가 `rejected` 상태가 되면 호출되며, 이때 콜백 함수는 프로미스의 에러를 인수로 전달받는다.

```javascript
// fulfilled
new Promise((resolve) => resolve('fulfilled')).then(
  (result) => console.log(result),
  (error) => console.error(error)
); // 'fulfilled'

// rejected
new Promise((_, reject) => reject(new Error('rejected'))).then(
  (value) => console.log(value),
  (error) => console.error(error)
); // 'rejected'
```

`then` 메서드는 언제나 프로미스를 반환한다.  
만약 `then` 메서드의 콜백 함수가 프로미스를 반환하면 그 프로미스를 그대로 반환하고, 프로미스가 아닌 값을 반환하면 그 값을 암묵적으로 `resolve`/`reject`하여 프로미스를 생성해 반환한다.

#### 📍 `Promise.prototype.catch`

한 개의 콜백 함수를 인수로 전달받으며, 프로미스가 `rejected` 상태인 경우에만 호출된다.

```javascript
new Promise((_, reject) => reject(new Error('rejected'))).catch((error) => console.log(error)); // Error: rejected
```

`then`과 동일하게 동작하므로 마찬가지로 언제나 프로미스를 반환한다.

#### 📍 `Promise.prototype.finally`

한 개의 콜백 함수를 인수로 전달받으며, 프로미스의 성공 또는 실패 여부와 상관없이 무조건 한 번 호출된다.  
프로미스의 상태와 상관없이 공통적으로 수행해야 할 처리 내용이 있을 때 유용하며, `then`/`catch` 메서드와 마찬가지로 언제나 프로미스를 반환한다.

```javascript
new Promise(() => {}).finally(() => console.log('finally')); // 'finally'
```

### 프로미스의 에러 처리

비동기 처리 결과에 대한 후속 처리는 프로미스가 제공하는 후속 처리 메서드 `then`, `catch`, `finally`를 사용해 수행한다.  
비동기 처리에서 발생한 에러는 1) `then` 메서드의 두번째 콜백 함수 또는 2) `catch` 메서드를 사용해 처리할 수 있다.

```javascript
promiseGet(wrongURL).then(
  (result) => console.log(result),
  (error) => console.error(error)
);

promiseGet(wrongURL)
  .then((result) => console.log(result))
  .catch((error) => console.error(error));
```

단, `then` 메서드의 두번째 콜백 함수는 첫번째 콜백 함수에서 발생한 에러를 캐치하지 못하고 코드가 복잡해져 가독성이 좋지 않다.  
**`catch` 메서드를 모든 `then` 메서드를 호출한 이후에 호출하면 비동기 처리에서 발생한 에러뿐만 아니라 `then` 메서드 내부에서 발생한 에러까지 모두 캐치할 수 있다.**

### 프로미스 체이닝

`then`, `catch`, `finally` 후속 처리 메서드는 언제나 프로미스를 반환하므로 연속적으로 호출할 수 있다. = **프로미스 체이닝**  
이를 통해 콜백 헬을 피할 수 있지만, 프로미스도 콜백 패턴을 사용하므로 콜백 함수를 아예 사용하지 않는 것은 아니다.  
`async`/`await`를 사용하면 프로미스의 후속 처리 메서드 없이 마치 동기 처리처럼 프로미스가 처리 결과를 반환하도록 구현할 수 있다.

```javascript
(async () => {
  const { userId } = await promiseGet(`${url}/posts/1`);
  const userInfo = await promiseGet(`${url}/users/${userId}`);
  console.log(userInfo);
})();
```

### 마이크로태스크 큐

```javascript
setTimeout(() => console.log(1), 0);

Promise.resolve()
  .then(() => console.log(2))
  .then(() => console.log(3));
```

위 코드는 2 - 3 - 1 순으로 출력된다.  
**프로미스의 후속 처리 메서드의 콜백 함수는 태스크 큐가 아니라 마이크로태스크 큐에 저장되기 때문이다.**  
마이크로태스크 큐는 태스크 큐와는 별도의 큐로, 프로미스의 후속 처리 메서드의 콜백 함수가 일시 저장된다.  
그 외의 비동기 함수의 콜백 함수나 이벤트 핸들러는 태스크 큐에 일시 저장된다.  
콜백 함수나 이벤트 핸들러를 일시 저장한다는 점에서 둘은 비슷하지만, **마이크로태스크 큐는 태스크 큐보다 우선순위가 높다**.

### `fetch`

`XMLHttpRequest` 객체와 마찬가지로 HTTP 요청 전송 기능을 제공하는 클라이언트 사이드 Web API다.  
사용법이 간단하고 프로미스를 지원하기 때문에 비동기 처리를 위한 콜백 패턴의 단점에서 자유롭다.  
**`fetch` 함수는 HTTP 응답을 나타내는 `Response` 객체를 래핑한 `Promise` 객체를 반환한다.**

```javascript
fetch('https://jsonplaceholder.typicode.com/todos/1').then((response) => console.log(response));
```

`fetch` 함수는 HTTP 응답을 나타내는 `Response` 객체를 래핑한 프로미스를 반환하므로 후속 처리 메서드 `then`을 통해 프로미스가 resolve한 `Response` 객체를 전달받을 수 있다.

`fetch` 함수 사용 시 에러 처리에 주의해야 한다.

```javascript
fetch(wrongURL)
  .then(() => console.log('ok'))
  .catch(() => console.log('error'));
```

위 코드의 예시에서 부적절한 URL이 지정되었음에도 `ok`가 출력된다.  
**`fetch` 함수가 반환하는 프로미스는 기본적으로 HTTP 에러가 발생해도 에러를 reject하지 않고 불리언 타입의 `ok`를 `false`로 설정한 `Response` 객체를 resolve하기 때문이다.**  
**오프라인 등의 네트워크 장애나 CORS 에러에 의해 요청이 완료되지 못한 경우에만 프로미스를 reject한다.**  
따라서 다음과 같이 `fetch` 함수가 반환한 프로미스가 resolve한 불리언 타입의 `ok`를 확인해 명시적으로 에러를 처리할 필요가 있다.

```javascript
fetch(wrongURL)
  .then((response) => {
    if (!response.ok) {
      throw new Error(response.statusText);
    }
  })
  .catch(() => console.log('error'));
```

참고로 axios는 모든 HTTP 에러를 reject하는 프로미스를 반환하기 때문에 모든 에러를 `catch`에서 처리할 수 있어 편리하다.

```javascript
const request = {
  get(url) {
    return fetch(url);
  },

  post(url, payload) {
    return fetch(url, {
      method: 'POST',
      headers: { 'content-Type': 'application/json' },
      body: JSON.stringify(payload),
    });
  },

  patch(url, payload) {
    return fetch(url, {
      method: 'PATCH',
      headers: { 'content-Type': 'application/json' },
      body: JSON.stringify(payload),
    });
  },

  delete(url) {
    return fetch(url, { method: 'DELETE' });
  },
};
```

#### 📍 1. GET 요청

```javascript
request
  .get('https://jsonplaceholder.typicode.com/todos/1')
  .then((response) => {
    if (!response.ok) {
      throw new Error(response.statusText);
    }
    return response.json();
  })
  .then((todos) => console.log(todos))
  .catch((error) => console.error(error));
```

#### 📍 2. POST 요청

```javascript
request
  .post('https://jsonplaceholder.typicode.com/todos', {
    userId: 1,
    title: 'JavaScript',
    completed: false,
  })
  .then((response) => {
    if (!response.ok) {
      throw new Error(response.statusText);
    }
    return response.json();
  })
  .then((todos) => console.log(todos))
  .catch((error) => console.error(error));
```

#### 📍 3. PATCH 요청

```javascript
request
  .patch('https://jsonplaceholder.typicode.com/todos/1', {
    completed: true,
  })
  .then((response) => {
    if (!response.ok) {
      throw new Error(response.statusText);
    }
    return response.json();
  })
  .then((todos) => console.log(todos))
  .catch((error) => console.error(error));
```

#### 📍 4. DELETE 요청

```javascript
request
  .delete('https://jsonplaceholder.typicode.com/todos/1')
  .then((response) => {
    if (!response.ok) {
      throw new Error(response.statusText);
    }
    return response.json();
  })
  .then((todos) => console.log(todos))
  .catch((error) => console.error(error));
```
