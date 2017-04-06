---
layout: post
comments: true
title: C++ 가상의 원리와 다중상속
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---

# 가상의 원리

지난 시간에 가상함수에 대해서 알아보았습니다.  
그렇다면 이번엔 이 가상의 원리에 대해서 알아보겠습니다.  
우선 멤버변수와 멤버함수에 대한 이야기를 해보겠습니다.  
객체가 생성되면 멤버변수와 멤버함수는 둘 다 객체 내에 존재할까요?  
멤버 변수는 객체 내에 존재하지만 멤버함수는 메모리의 한 공간에 별도로 위치하고선, 이 함수가 정의된 클래스의 모든 객체가 이를 공유하도록 하는 형태를 취합니다.  
가상 함수는 실행 시간에 호출될 함수를 결정합니다.  
그럼 어떻게 실행 시간에 객체의 클래스형에 따라 재정의된 가상 함수를 찾아 호출할 수 있는 것일까요?  
C++의 컴파일러는 가상 함수를 처리하기 위해 가상 함수를 갖는 클래스마다 가상 함수 테이블을 생성합니다.
이는 V-Table이라고도 불리는데 실제 호출되어야 할 함수의 위치정보를 담고있습니다.
이 테이블에는 key값으로 호출하고자 하는 함수가 오고 value값으로 함수의 주소 정보를 알려주는 역할을 하는 값이 담깁니다.  
이어서 오버라이딩 된 함수가 온다면 기존의 테이블에 있는 함수를 지우고 자식 클래스의 함수가 테이블에 추가됩니다.  
이런 원리로 무조건 마지막에 오버라이딩을 한 자식 클래스의 멤버함수가 호출되는 것입니다.  

# 다중상속에 대한 이해

상속을 꼭 한군데서 받을 필요가 있을까요?  
두곳에서 상속을 받을 수 있을까요?  
네, 받을 수 있습니다.  
예를들어 다음과 같은 것이 가능합니다.  

```
class Derived : public BaseOne, public BaseTwo{
  //내용
}
```

하지만 이렇게 되면 다중 상속의 모호성이 생깁니다.  
두 자식 클래스에 동일한 이름의 멤버가 존재하는 경우에는 문제가 발생합니다.  
예를들어 두 자식 클래스에 모두 Simple 이라는 함수가 존재한다면 어떨까요?  
당연히 Derived 객체에서 Simple함수를 사용하게 되면 에러가 납니다.  
하지만 에러를 피할 방법이 있기는 합니다.  

```
class Derived : public BaseOne, public BaseTwo{
  void LikeThis(){
    BaseOne::Simple();
    BaseTwo::Simple();
  }
}
```

위와 같이 접근하면 되는 것입니다.  
하지만 다른 상황에서도 문제가 발생할 수 있습니다.  

![다중상속문제점2](https://impressprogram.github.io/assets/media/multi_inherit.png)

위와 같이 한개의 클래스가 두 부모클래스로부터 상속을 받고 있다고 가정해 보겠습니다.  
그런데 이 두 부모클래스는 또 같은 부모로부터 상속을 받은 클래스라고 가정해 보겠습니다.  
그렇다면 Base 클래스의 멤버함수에 LastDerived 클래스가 접근하려면 어떻게 해야할까요?  
반드시 어떤 클래스를 통해서 간접상속할 것인지 명시해야 합니다.  
예를들어 Base 클래스에 Simple함수를 LastDerived 객체가 사용해야 한다고 가정하겠습니다.  

```
MiddleDerivedOne::Simple();
MiddleDerivedTwo::Simple();
```

위와 같이 LastDerived 클래스에서는 꼭 간접상속할 클래스를 지정해주어야 합니다.  
아니면 가상상속을 통해 이 문제를 해결할수도 있습니다.  

```
class MiddleDerivedOne : virtual public Base{
  //내용
}

class MiddleDerivedTwo : virtual public Base{
  //내용
}

class LastDerived : public MiddleDerivedOne, public MiddleDerivedTwo{
  //내용
}
```

이렇게 하면 Base클래스를 딱 한번만 상속하게끔 됩니다.  
하지만 다시 강조하지만 왠만하면 쓰지 않으시는 것을 추천드립니다.  
