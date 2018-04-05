---
layout: post
categories: github-page
---
# Github page 만들기

(TODO)

# 일반 가이드

## Code block

Code block은 \`\`\`를 이용하여 지정한다. \`\`\`다음에는 js, jsx 등 github page가 지원하는 언어를 적어준다. (예: \`\`\`jsx)

다음은 jsx로 하이라이트한 코드 조각이다.

```jsx
import React, { component } from 'react'
...
```

## JSX 주의사항

jsx는 "\{\{ *var* \}\}"를 사용하여 변수를 지정한다. 그런데 중괄호는 Github page의 템플릿 엔진에서 사용하는 문법과 같다. 별도의 처리를 해주지 않으면 다음과 같이 이중 중괄호가 무시된다.

이중 중괄호가 무시됨

```jsx
const test = ({{ obj }}) => {
  console.log(obj)
}
```

의도했던 코드

<!-- {% raw %} -->
```jsx
const test = ({{ obj }}) => {
  console.log(obj)
}
```
<!-- {% endraw %} -->

따라서 다음과 같이 {{ "{% raw " }}%}와 {{ "{% endraw " }}%}를 이용해서 코드 블럭을 감싸주어야 한다. Atom 등에서 Markdown preview를 이용하여 글을 작성할 때에는 <\!\-\- {{ "{% raw " }}%} \-\-> 이렇게 주석처리를 해주면, preview 모드에서는 html 주석이기 때문에 보이지 않게 되지만, 템플릿 엔진은 이 태그를 처리한다.

```
<!-- {{ "{% raw " }}%} -->
// Code block with ```jsx
<!-- {{ "{% endraw " }}%} -->
```

# Atom 가이드

나는 Atom에서 Markdown preview를 보며 아래 화면과 같이 작성하고 있다.

![Atom 작업화면]({{ "/assets/images/2018/04/Screen Shot 2018-04-02 at 7.06.58 PM.png" | absolute_url }})

Github page + Atom을 조합할 경우에 2가지 버그를 만나게 된다. 이것 때문에 엄청 삽질했다.

**(버그)** language-babel(2.84.0 기준)을 사용하게 되면 코드 블럭에서 JSX에 맞지 않는 문법이 있을 경우, 아래에 있는 모든 텍스트가 하이라이트가 되어 버린다... language-babel을 꺼놓는 것을 추천한다. jsx가 편집기에서는 강조되지 않지만, Markdown preview를 통해서 보면 된다.

**(버그)** Github page에서 쓰는 Syntax highlighter가 React의 JSX 중 일부 ECMA7 문법을 파싱하지 못하는 경우가 있다. (예: object spread syntax) 나는 다음과 같이 커스텀으로 CSS를 추가했다.

assets/css/style.scss

```css
---
---

@import "{{ site.theme }}";

.highlight .err {
  color: #567482;
  background: none;
}
```
