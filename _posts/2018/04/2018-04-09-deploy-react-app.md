---
layout: post
title: React를 express로 배포하기
categories: react
tags: react, express, deploy
comments: true
---

대부분의 코드는 [Facebook에서 제공하는 공식 문서](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#other-solutions)와 같다. 다만, 라우팅을 원한다면 `app.get('/', ...)` 대신 모든 경로를 매칭시키는 `app.get('*', ...)`를 사용하면 된다.

express로 배포하면, pm2를 이용해서 노드 프로세스를 쉽게 관리할 수 있다.

```js
const express = require('express')
const path = require('path')
const app = express()

app.use(express.static(path.join(__dirname, 'build')))

app.get('*', function (req, res) {
  res.sendFile(path.join(__dirname, 'build', 'index.html'))
});

app.listen(9000)
```

# 참고문헌

Facebook 공식 문서: [https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#other-solutions](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#other-solutions)
