---
layout: post
title: Adapter Pattern
categories: design-pattern
tags: design-pattern adapter
comments: true
---

# Summary

이전부터 쓰던 클래스에서 제공하는 인터페이스 하나가 있다고 합시다. 새롭게 추가된 클래스는 기존 클래스에서 확장된 기능을 제공해서, 이 기능도 추가하고 싶습니다. 그런데 문제가 있습니다. 기존 클라이언트 코드를 수정하기 힘든데, 새롭게 추가된 클래스의 인터페이스가 기존에 사용하던 클래스와 동일하지 않습니다. 이럴 때 사용하는 것이 어댑터 패턴입니다. 어댑터는 '서로 호환되지 않는 두 개를 이어주는 매개체'라고 정의할 수 있습니다. 위의 예시에서 어댑터는 서로 호환되지 않는 인터페이스를 이어주는 매개체인 셈입니다.

어댑터는 두 가지 종류가 있습니다.

1. Class Adapter
2. Object Adapter

![Adpater]({{ "/assets/images/2018/05/W3sDesign_Adapter_Design_Pattern_UML.jpg" }})

클래스 어댑터는 상속(inheritance)을 이용하여 호환되지 않는 클래스를 감쌉니다. 따라서 인터페이스처럼 상속이 불가능한 경우에는 사용할 수 없습니다

객체 어댑터는 합성(composition)을 이용하여 호환되지 않는 클래스나 인터페이스를 감쌉니다. 클래스 어댑터와 비교했을 때, 인터페이스처럼 상속이 불가능한 경우에도 적용할 수 있다는 점이 장점입니다.

클래스 어댑터는 상속을 이용해서 어댑터를 구현하기 때문에 LSP를 위반할 가능성이 있으므로, 적절하게 사용되지 않을 경우 악취를 풍길 수 있습니다.

# Example

tutorialspoint에 소개된 예시를 어댑터 패턴에 부합하도록 수정해봅니다.

![Adapter]({{ "/assets/images/2018/05/adapter_pattern_uml_diagram.jpg" }})

위 그림을 보면, `AudioPlayer`와 `MediaAdapter` 사이에 불필요한 결합이 있다는 것을 알 수 있습니다. 어댑터는 구체화된 클래스에 의존하는 것이 아니라, 인터페이스에 의존해야 하므로, 다음과 같이 `mediaAdapter` 필드의 타입을 `MediaAdapter`에서 `MediaPlayer`로 바꾸어줍니다.

```java
public class AudioPlayer implements MediaPlayer {
  // MediaAdapter mediaAdapter;
  MediaPlayer mediaAdapter;

  @Override
  public void play(String audioType, String fileName) {

    //inbuilt support to play mp3 music files
    if(audioType.equalsIgnoreCase("mp3")){
       System.out.println("Playing mp3 file. Name: " + fileName);
    }

    //mediaAdapter is providing support to play other file formats
    else if(audioType.equalsIgnoreCase("vlc") || audioType.equalsIgnoreCase("mp4")){
       mediaAdapter = new MediaAdapter(audioType);
       mediaAdapter.play(audioType, fileName);
    }

    else{
       System.out.println("Invalid media. " + audioType + " format not supported");
    }
  }
}

``` 

# 참고문헌

Class Adapter vs Object Adapter: [https://stackoverflow.com/questions/9978477/difference-between-object-adapter-pattern-and-class-adapter-pattern](https://stackoverflow.com/questions/9978477/difference-between-object-adapter-pattern-and-class-adapter-pattern)

Adapter Pattern: [https://en.wikipedia.org/wiki/Adapter_pattern](https://en.wikipedia.org/wiki/Adapter_pattern)

Adapter Pattern2: [https://www.tutorialspoint.com/design_pattern/adapter_pattern.htm](https://www.tutorialspoint.com/design_pattern/adapter_pattern.htm)
