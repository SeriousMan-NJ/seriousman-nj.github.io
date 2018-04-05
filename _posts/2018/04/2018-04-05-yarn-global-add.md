---
layout: post
title: yarn global add serve 후 serve 명령어를 찾을 수 없다는 오류
categories: yarn
tags: react yarn ubuntu ubuntu-16.04
---

Ubuntu 16.04 LTS에 React를 static server를 이용한 방법으로 deploy하기 위해서, `$ yarn global add serve` 명령을 통해 serve 패키지를 글로벌로 설치했다.

그런데 글로벌 serve 명령어를 찾을 수 없다는 오류가 발생한다. (macOS에서는 발생하지 않는다. 우분투에서 발생한다)

이럴 경우에 `$ yarn global bin` 명령어가 반환하는 경로를 PATH 환경변수가 추가해주면 된다. 우분투 환경변수는 `~/.bash_profile`에서 추가할 수 있다. 다음 문장을 `~/.bash_profile`에 추가해준다.

```bash
export $PATH = /path/will/be/added:$PATH
```

# 참고문헌

yarn global add doesn't install binaries properly: [https://github.com/yarnpkg/yarn/issues/648](https://github.com/yarnpkg/yarn/issues/648)
