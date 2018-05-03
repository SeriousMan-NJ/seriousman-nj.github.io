---
layout: post
title: Singleton Pattern
categories: design-pattern
tags: design-pattern singleton
comments: true
---

<img src="{{ "/assets/images/2018/05/singleton.png" | absolute_url }}" alt="Singleton" style="width: 200px;" />

# Summary

어떤 클래스는 하나의 인스턴스만을 갖고 있어야 할 때가 있습니다. 예를 들어, API 호출을 담당하는 서비스 클래스의 경우에는 하나의 인스턴스를 재활용하는 것이 효율적입니다. 단 하나의 객체 생성을 보장하는 디자인 패턴이 'Singleton Pattern' 입니다.

싱글톤 패턴은 다음과 같은 특징을 갖고 있습니다.

1. 객체를 임의로 생성할 수 없도록 생성자를 `private`으로 만든다.
2. 객체를 생성하는 메소드는 어느 클래스에서나 호출할 수 있어야 하므로 `static`이다.
3. 이 메소드는 단 하나의 메소드 생성을 보장해야 한다.

자바를 이용하여 싱글톤 패턴을 다음과 같이 구현할 수 있습니다.

```java
public class Singleton {
  // Java는 멤버 변수 선언과 동시에 초기값을 객체로 지정할 수 있다.
  private static Singleton single = new Singleton();

  // 생성자를 private으로 선언하여 임의의 객체 생성을 막는다.
  private Singleton() {}

  // 어느 클래스에서나 접근가능하도록 static 메소드로 선언한다.
  public static Singleton getSingleton() {
    return single;
  }

  public static void main(String[] args) {
    Singleton s1 = Singleton.getSingleton();
    Singleton s2 = Singleton.getSingleton();
    System.out.println(s1 == s2); // true
  }
}
```

멤버 변수를 객체로 초기화 할 수 없는 php의 경우에는 다음과 같이 구현할 수 있습니다.

```php
<?php

class Singleton {
  private static $single;

  // 생성자는 private으로 구현한다.
  private function __construct() {}

  public static function getSingleton() {
    // 객체가 한 번도 생성되지 않았다면, 생성해준다.
    if(empty(Singleton::$single)) {
      Singleton::$single = new Singleton();
    }
    return Singleton::$single;
  }
}

$s1 = Singleton::getSingleton();
$s2 = Singleton::getSingleton();
print_r($s1 === $s2); // true
```
