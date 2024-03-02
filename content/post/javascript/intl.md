+++
categories = ['JavaScript']
project_url = 'https://e6d1fe.github.io/e6d1fe-blog/post/javascript/'
title = 'intl'
tags = ['JavaScript', 'intl']
date = 2024-03-02T16:54:26+09:00
draft = false
author = 'Sehyun'
comments = true
readingTime = true
+++

## Intl

number formatting, date & time formatting 등을 제공하는 객체  
constructor가 아니기 때문에 `new` 연산자와 함께 사용하거나 함수로 invoke할 수 없다. (`Math` 객체처럼)

### argument 1: locales

locale에 대한 정보 제공  
`hi`, `de-AT`, `zh-Hans-CN` 등  
생략하거나 `undefined`일 때는 런타임의 default locale 사용

### argument 2: options

생략하거나 `undefined`일 때는 default value 사용

**예시: date & time formatting**

```javascript
const count = 26254.39;
const date = new Date('2012-05-24');

function log(locale) {
  console.log(
    `${Intl.DateTimeFormat(locale).format(date)} ${Intl.NumberFormat(locale).format(count)}`
  );
}

log('en-US'); // 5/24/2012 26,254.39
log('de-DE'); // 24.5.2012 26.254,39
```

**예시: currency formatting**

```javascript
const amount = 3331245;

function log(locale) {
  console.log(
    `${Intl.NumberFormat(locale, {
      style: 'currency',
      currency: 'USD',
      minimumFractionDigits: 2,
    }).format(amount)}`
  );
}

log('en-US'); // $3,331,245.00
```

reference: [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl)
