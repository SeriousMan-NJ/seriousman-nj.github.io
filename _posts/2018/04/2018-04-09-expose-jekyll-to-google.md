---
layout: post
title: Github 블로그를 구글 검색 엔진에 노출하는 방법
categories: github-page
tags: github-page sitemap search google-search jekyll
comments: true
---

1. Google의 Search Console
[구글 웹마스터 도구](https://www.google.com/webmasters/tools/home)에 접속하여 속성을 추가한다.

2. sitemap.xml을 작성하고 업로드한다.
sitemap.xml 내용은 [이곳](https://github.com/SeriousMan-NJ/seriousman-nj.github.io/blob/master/sitemap.xml)을 참고한다.

```xml
---
---
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
    {% for post in site.posts %}
    <url>
        <loc>{{ site.url }}{{ post.url | remove: 'index.html' }}</loc>
    </url>
    {% endfor %}

    {% for page in site.pages %}
    {% if page.layout != nil %}
    {% if page.layout != 'feed' %}
    <url>
        <loc>{{ site.url }}{{ page.url | remove: 'index.html' }}</loc>
    </url>
    {% endif %}
    {% endif %}
    {% endfor %}
</urlset>
```

# 참고문헌

Sitemaps for Jekyll sites: [http://joelglovier.com/writing/sitemaps-for-jekyll-sites](http://joelglovier.com/writing/sitemaps-for-jekyll-sites)

지킬 블로그 구글 검색 가능하게 하는 방법: [https://wayhome25.github.io/etc/2017/02/20/google-search-sitemap-jekyll/](https://wayhome25.github.io/etc/2017/02/20/google-search-sitemap-jekyll/)
