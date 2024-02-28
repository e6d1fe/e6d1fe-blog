+++
categories = ['JavaScript']
project_url = 'https://e6d1fe.github.io/e6d1fe-blog/post/javascript/'
title = '배열 초기화'
tags = ['JavaScript', 'array']
date = 2024-02-28T16:54:26+09:00
draft = false
author = 'Sehyun'
comments = true
readingTime = true
+++

# 배열 초기화 (이중배열)

배열을 초기화할 때 쓸 수 있는 두 가지 방법:

- `Array.prototype.fill()`
- `Array.from()`

## `Array.prototype.fill()`

배열을 채울 값으로 객체를 받을 경우 그 **참조만 복사**해서 배열을 채운다.

```
function solution(n, arr1, arr2) {
    const map = new Array(n).fill(new Array(n).fill("*"));
    map[1][2] = "H";
    return map;
}

solution(5)
```

위 코드의 결과가

```
['*', '*', '*', '*', '*'],
['*', '*', 'H', '*', '*'],
['*', '*', '*', '*', '*'],
['*', '*', '*', '*', '*'],
['*', '*', '*', '*', '*']
```

이게 아니라

```
['*', '*', 'H', '*', '*'],
['*', '*', 'H', '*', '*'],
['*', '*', 'H', '*', '*'],
['*', '*', 'H', '*', '*'],
['*', '*', 'H', '*', '*']
```

이렇게 나온다는 뜻이다! (이렇게 배열을 초기화하면 배열 속의 모든 배열이 똑같은 참조값을 가지게 되어버리기 때문)

그래서 의도대로 `map[1][2] = "H`를 했을 때 `map[1][2]` 자리의 아이템만 `H`로 바꾸고 싶다면 `Array.prototype.fill()`이 아닌 `Array.from()`을 사용해 배열을 초기화해야 한다.

```
function solution(n, arr1, arr2) {
    const map = Array.from({ length: n }, () => Array(n).fill("*"));
    map[1][2] = "H";
    return map;
}
```
