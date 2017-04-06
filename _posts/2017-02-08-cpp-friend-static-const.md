---
layout: post
comments: true
title: C++ const, friend, static, mutable에 대한 이야기
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---
# const

```
const object p1(10);
```

위 코드는 객체의 데이터 변경을 허용하지 않겠다는 문장입니다.  
객체에 const 선언이 붙게되면 이 객체를 대상으로는 const 멤버함수만이 호출이 가능합니다.  
또한 아래처럼 const 선언유무도 함수 오버로딩의 조건에 해당이 됩니다.

```
void func() {....}
void func() const {....}
```

위 함수의 경우에 객체의 const 선언유무에 따라 호출되는 함수가 다릅니다.

# friend

이건 아래의 블로그에서 너무 완벽하게 설명해 놓았습니다.  
깡패와 바보의 비유로 너무 이해하기 쉽게 설명했습니다.  
하지만 객체지향의 개념을 해치는 함수이니 되도록 사용을 자제합시다!  
http://genesis8.tistory.com/98  

# static

- 전역변수에 선언된 static

선언된 파일 내에서만 참조를 허용하겠다는 의미입니다.

- 함수 내에 선언된 static

한번만 초기화되고, 지역변수와 달리 함수를 빠져나가도 소멸되지 않고 남아있습니다.

- static 멤버변수(클래스 변수)

static 멤버변수는 '클래스 변수'라고도 불립니다.  
일반적인 멤버변수와 달리 클래스당 하나씩만 생성되기 때문입니다.  
클래스 안에 선언된 static 변수의 경우 객체별로 별개의 메모리에 할당되는 변수가 아닌 모든 객체가 공유하는 구조가 됩니다.  
static 멤버변수는 생성자에서 초기화하면 안됩니다.  
static 멤버변수는 객체가 생성될때 동시에 생성되는 변수가 아니고, 이미 메모리공간에 할당이 이뤄진 변수이기 때문입니다.  
따라서 static 멤버변수의 초기화 문법은 다음과 같이 별도로 정의되어 있습니다.

```
int SoSimple::simObjCnt=0;
```

static 멤버변수는 사실 어디서든 접근이 가능합니다.  
static멤버가 private로 선언되면, 해당 클래스의 객체들만 접근이 가능하지만, public으로 선언되면 클래스의 이름 또는 객체의 이름을 통해서 어디서든 접근이 가능합니다.

```
class SoSimple
{
public:
    static int simObjCnt;
public:
    SoSimple()
    {
        simObjCnt++;
    }
};
int SoSimple::simObjCnt=0;
```

위와 같이 객체를 만든다면 아래처럼 어디서든 클래스명으로도 접근이 됩니다(객체명도 물론 가능).  

```
cout << SoSimple::simObjCnt << endl; // 0
SoSimple sim1;
cout << sim1.simObjCnt << endl; // 1
```

- static 멤버함수

 static 멤버함수 역시 그 특성이 static 멤버변수와 동일합니다.  

 1. 선언된 클래스의 모든 객체가 공유한다.
 2. public으로 선언이 되면, 클래스의 이름을 이용해서 호출이 가능하다.
 3. 객체의 멤버로 존재하는 것이 아니다.

참고 : static 멤버함수 내에서는 static 멤버변수와 static 멤버함수만 호출이 가능합니다(객체의 멤버가 아니므로 당연한 이야기겠죠?).  
이러한 특성을 잘 활용하면 대부분의 경우에 있어서 전역변수와 전역함수를 대체할 수 있습니다.

- const static 멤버

const 멤버변수(상수)의 초기화는 이니셜라이저를 통해야만 가능합니다.  
그러나 const static으로 선언되는 멤버변수는 다음과 같이 선언과 동시에 초기화가 가능합니다.  

```
class CountryArea
{
public:
    const static int RUSSIA         =1707540;
    const static int CANADA         =998467;
    const static int CHINA          =957290;
    const static int SOUTH_KOREA    =9922;
};
```

참고 : const static 멤버변수는 클래스가 정의될 때 지정된 값이 유지되는 상수이기 때문에, 위 예제처럼 초기화가 가능하도록 문법으로 정의하고 있습니다.

# mutable 키워드

const 함수 내에서의 값의 변경을 예외적으로 허용하는 키워드입니다.  
mutable을 선언할 경우 const 선언이 무의미해지기 때문에 가급적 사용을 자제해야 합니다.

```
#include <iostream>
#include <cstring>

using namespace std;

class Example {
private:
    int num;
    mutable int num2;
public:
    Example(int n1, int n2) : num(n1), num2(n2){
        cout << "객체생성" << endl;
    }

    void Copy(int n_) const {
        num2 = n_;
    }

    void Show() const {
        cout << num << ", " << num2 << endl;
    }
};

int main(void){

    Example ex(1,2);

    ex.Copy(5);

    ex.Show();

    return 0;
}
```
