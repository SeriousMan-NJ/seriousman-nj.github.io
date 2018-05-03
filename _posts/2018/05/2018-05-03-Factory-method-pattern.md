---
layout: post
title: Factory method Pattern
categories: design-pattern
tags: design-pattern factory-method
comments: true
---

<img src="{{ "/assets/images/2018/05/factory-method.png" | absolute_url }}" alt="Singleton" style="width: 400px;" />

# Summary

객체를 생성하는 방법 중 하나이다. 객체를 생성자를 통해 생성하게 되면, 객체 생성 방법이 변경되어 생성자가 수정될 경우, 모든 객체를 수정해주어야 하는 문제가 있다. 따라서 객체 생성을 전담하는 클래스를 생성하여, 이 클래스에서 객체 생성을 전담하게 하는 것이 "Factory method" 패턴이다.
