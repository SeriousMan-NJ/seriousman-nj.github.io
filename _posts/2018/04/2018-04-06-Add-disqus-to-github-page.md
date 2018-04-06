---
layout: post
title: Github page에 Disqus 댓글 추가하기
categories: github-page
tags: github-page disqus
comments: true
---

[Github page에 Disqus 추가하기](https://xho95.github.io/blog/jekyll/disqus/migration/2017/01/20/Add-Disqus-to-Jekyll.html)를 보고 댓글 기능을 추가했다.

# 댓글 개수가 표시 안 되는 문제

나는 로컬에서 `bundle exec jekyll serve` 명령어를 이용해서 개발하고 있었다. 이 모드에서는 기본적으로 `http://localhost:4000`을 사용하게 되고, `site.url` 변수도 `http://localhost:4000`으로 바꾸어서 사용한다.

Disqus에서 댓글을 달면 discussion으로 관리가 되는데, 로컬에서 댓글을 등록하게 되면 `this.page.url`이 `http://localhost:4000`으로 등록된다... 따라서 로컬에서는 댓글을 개수가 잘 보이지만, 정작 Github page에서는 보이지 않게 된다. 이것 때문에 몇 시간을 찾아보았지만, 해결책을 써놓은 곳이 없어서 여기에 정리한다.

우선 설정에 다음과 같이 site.url 값과 같은 값으로 site.site_url 변수를 추가한다.

```yml
url: "https://seriousman-nj.github.io" # the base hostname & protocol for your site, e.g. http://example.com
site_url: "https://seriousman-nj.github.io" # MUST be same as site.url. This is for providing environment-variable-consistant site url. ONLY Use it for Disqus
```

그리고 디스코스 스크립트에서 disqus_config의 `this.page.url`을 다음과 같이 수정한다.

<!-- {% raw %} -->
```js
var disqus_config = function () {
this.page.url = "{{ site.site_url }}{{ page.url }}";  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = "{{ page.id }}"; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
```
<!-- {% endraw %} -->

마지막으로 댓글을 표시하고 싶은 링크는 다음과 같이 수정한다.

<!-- {% raw %} -->
```html
<a href="{{ site.site_url }}{{ post.url }}#disqus_thread">0 Comments</a>
```
<!-- {% endraw %} -->

이미 등록된 댓글 개수를 표시하기 위해서는 Disqus에서 Discussion을 수정해야 한다.

Disqus에서 Admin 메뉴에 들어가서 Tools > Discussions 메뉴에 접근한 뒤, `http://localhost:4000`으로 되어 있는 댓글들을 모두 Github page로 수정해준다. *https* 를 사용하는 것에 주의한다.

# 참고문헌

Github page에 Disqus 추가하기 [https://xho95.github.io/blog/jekyll/disqus/migration/2017/01/20/Add-Disqus-to-Jekyll.html](https://xho95.github.io/blog/jekyll/disqus/migration/2017/01/20/Add-Disqus-to-Jekyll.html)
