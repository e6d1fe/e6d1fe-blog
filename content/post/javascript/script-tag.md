+++
categories = ['JavaScript']
project_url = 'https://e6d1fe.github.io/e6d1fe-blog/post/javascript/'
title = 'Script Tag'
tags = ['JavaScript', 'script']
date = 2024-03-01T16:54:26+09:00
draft = false
author = 'Sehyun'
comments = true
readingTime = true
+++

# `<Script>` Tag

`<Script>` : `html` 파일에서 자바스크립트 파일을 링크하는 용도로 쓰이는 태그

`html` 파일을 파싱할 때, 맨 위에서부터 아래로 내려가며 `script` 태그에 다다르면 링크된 자바스크립트를 다운로드하고, 다운로드된 자바스크립트를 실행하고, 다시 나머지 `html` 파일을 파싱하는 것으로 돌아감

하지만 이럴 경우 생길 수 있는 문제: 자바스크립트 파일에 이벤트 리스너 등이 있고, `script` 태그의 위치가 그 이벤트 리스너가 있는 부분보다 위에 있다면, 아직 정의되지 않은 element에 이벤트 리스너를 붙이는 꼴이 되기 때문에 의도대로 동작하지 않을 수가 있다!

이럴 때의 해결방법:

1. `window.onload() => {}` 사용하기
2. `script` 태그를 `body` 태그 가장 아래에 배치하기
3. 아니면 `script` 태그에 attribute를 추가함으로써 해결할 수도 있다.  
   ➡️ `async`, `defer`

## `<script async src="..." />`

`html`이 파싱되는 동안 자바스크립트를 다운로드하고, 다운로드가 완료된 시점에 파싱을 멈추고 자바스크립트를 실행함  
실행이 완료되면 다시 `html` 파싱으로 돌아감

## `<script defer src="..." />`

`html`이 파싱되는 동안 자바스크립트를 다운로드하고(여기까지는 `async`와 동일), 파싱이 완료되기까지 기다린 후에 다운로드된 자바스크립트를 실행함  
➡️ DOM이 생성된 후에 실행되기 때문에 위에서 언급한 문제는 발생하지 않는다!
