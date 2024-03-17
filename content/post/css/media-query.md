+++
categories = ['CSS']
project_url = 'https://e6d1fe.github.io/e6d1fe-blog/post/css/'
title = 'Media Query'
tags = ['css', 'media query']
date = 2024-03-17T21:01:22+09:00
draft = false
author = 'Sehyun'
comments = true
readingTime = true
+++

## Media Query

타겟할 수 있는 대상 2가지: 1) media types 2) media features

### media types

: 디바이스의 전반적 카테고리  
`all` / `print` / `screen`

```css
@media print {
  /* ... */
}
```

한 번에 두 가지도 타겟 가능

```css
@media print, screen {
  /* ... */
}
```

### media features

: media types보다 좀 더 구체적인 특정 특성을 타겟할 때 사용  
ex) widescreen monitors, computers that use mice, or devices that are being used in low-light conditions

#### 가로모드 / 세로모드

```css
@media (orientation: landscape) {
  /* ... */
}
```

#### 화면 너비

```css
@media (max-width: 1280px) {
  /* ... */
}

/* 아래 둘은 같은 기준 */

@media (min-width: 30em) and (max-width: 50em) {
  /* … */
}

@media (30em <= width <= 50em) {
  /* … */
}
```

#### `and` 키워드

```css
@media (min-width: 30em) and (orientation: landscape) {
  /* … */
}
```

#### or (`,`)

```css
@media (min-height: 680px), screen and (orientation: portrait) {
  /* … */
}
```

- 디바이스 너비가 680px 이상이거나
- 디바이스 스크린이 세로 모드이거나

둘 중 하나라도 충족시킬 때 적용

#### `not` 키워드 (부정)

```css
@media not print {
  /* … */
}
```

everything except printed media  
`not`은 미디어 쿼리에서 가장 마지막에 평가되기 때문에 **미디어 쿼리 전체에 적용**됨

```css
@media not all and (monochrome) {
  /* … */
}

/* not (all and (monochrome)) 이렇게 평가된다는 뜻 */
```

전체에 적용시키고 싶지 않다면 `()` 괄호로 감싼 후 사용 !

#### `only` 키워드

- 미디어 쿼리를 지원하지 않는 예전 브라우저들이 해당 css 규칙들을 적용하는 걸 막음 (모던 브라우저에는 영향 X)

### typical breakpoints

```css
/* Extra small devices (phones, 600px and down) */
@media only screen and (max-width: 600px) {
  ...;
}

/* Small devices (portrait tablets and large phones, 600px and up) */
@media only screen and (min-width: 600px) {
  ...;
}

/* Medium devices (landscape tablets, 768px and up) */
@media only screen and (min-width: 768px) {
  ...;
}

/* Large devices (laptops/desktops, 992px and up) */
@media only screen and (min-width: 992px) {
  ...;
}

/* Extra large devices (large laptops and desktops, 1200px and up) */
@media only screen and (min-width: 1200px) {
  ...;
}
```

### references

- https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_media_queries/Using_media_queries
- https://www.w3schools.com/howto/howto_css_media_query_breakpoints.asp
