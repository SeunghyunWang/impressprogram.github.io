---
layout: post
comments: true
title: C++ 생성자와 소멸자
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---

# 생성자의 기본

C++에서는 생성자를 이용하여 편하게 객체의 맴버변수를 초기화 할 수 있습니다.  
클래스의 이름과 함수의 이름이 동일하고 반환형이 선언되어있지 않고 실제로 아무것도 반환하지 않으면 됩니다.  
그리고 이 생성자는 객체 생성시 자동으로 딱 한번 호출된다는 특징이 있습니다.  
아무것도 하지 않아도 디폴트 생성자(아무런 기능은 없지만)라도 자동으로 생성됩니다.  
생성자가 그렇다고 새로 생긴 개념은 아닙니다.  
기본적으로 함수의 일종이므로 오버로딩이 가능하고 매개변수에 디폴트 값을 설정할 수 있습니다.  

```
#include <iostream>
using namespace std;

class Example{
private:
    int num1;
    int num2;
public:
    Example(int n1=0, int n2=0){
        num1 = n1;
        num2 = n2;
    }

    void Show(void) const;
};

int main(void){

    Example ex1;
    ex1.Show();

    Example ex2(10);
    ex2.Show();

    Example ex3(10,10);
    ex3.Show();

    return 0;
}

void Example::Show(void) const {
    cout << num1 << " " << num2 << endl;
}

결과값 :
0 0
10 0
10 10
```

여기서 주의해야 할 것이 Example ex1; 부분입니다.  
언뜻 보면 아래의 두 문장은 같은 문장처럼 보입니다.  

```
Example ex1;
Example ex1();
```

하지만 전혀 다른 두 문장입니다.  
위는 생성자 함수를 호출하고 아래는 함수의 원형을 호출하는 것입니다.  
아래는 Example ex1(void){} 함수 즉, 반환형이 Example 이고 이름이 ex1인 인자가 없는 함수의 원형을 선언한 것이라고 인식한다는 것입니다(물론 보통은 전역적으로 선언하지만 저렇게 함수 내에서 지역적으로도 선언이 가능합니다).

# 멤버 이니셜라이저

생성자를 더욱 편리하게 해주는 것이 바로 멤버 이니셜라이저입니다.  
멤버 이니셜라이저를 통해서 클래스 내에 다른 객체가 있다면 그 객체까지 한번에 초기화를 할 수 있습니다.  
아래의 코드는 Rectangle 클래스 안에 정의된 Point 객체까지 초기화를 해줍니다.

```
class Rectangle{
private:
    Point upLeft;
    Point lowRight;
public:
    Rectangle(const int &x1, const int &y1, const int &x2, const int &y2);
    void ShowRecInfo() const;
};

Rectangle::Rectangle(const int &x1, const int &y1, const int &x2, const int &y2):upLeft(x1,y1), lowRight(x2,y2){

}
```

위의 예제는 Rectangle 객체의 생성자 함수가 Point 객체를 초기화 해줍니다.  
upLeft 객체의 생성과정에서 (x1,y1)를 인자로 전달받는 생성자를 호출 할 것입니다.  
lowRight 객체의 생성과정에서 (x2,y2)를 인자로 전달받는 생성자를 호출 할 것입니다.

물론 멤버 이니셜라이저를 통해서 객체가 아닌 멤버도 초기화 할 수 있습니다.

```
class Example{
private:
    int num1;
    int num2;
public:
    Example(int n1=0, int n2=0):num1(n1), num2(n2){
      //num1 = n1, num2 = n2; 라고 한것과 같은 효과
    }

    void Show(void) const;
};
```

그리고 멤버 이니셜라이저를 사용하면 다음과 같은 효과를 얻을 수 있습니다.  
int num1 = n1;  
이것은 아래의 문장과 약간 다릅니다(주석부분의 문장).  
int num1;  
num1 = n1;  
위와 같은 이유로 const 선언을 할 때에 반드시 선언과 동시에 변수로 초기화가 되어야 하는데 멤버 이니셜라이저를 이용하면 가능해집니다.  
또한 같은 이유로 참조자를 선언할 수도 있게 됩니다.

```
class Rectangle{
private:
    Point &upLeft;
    Point &lowRight;
    const int num;
public:
    Rectangle(Point &pt1, Point &pt2, const int &n)
    : upLeft(pt1), lowRight(pt2), num(n){

    }
};
```

# 디폴트 생성자

디폴트 생성자는 아무런 생성자가 정의되지 않았을 때 자동으로 생성됩니다.  
아래와 같은 코드가 가능한 것도 디폴트 생성자 덕분입니다.  

```
#include <iostream>
using namespace std;

class Example{
private:
    int num;
public:
    void Show() const;
};

void Example::Show() const {cout << num << endl;}

int main(void){

    Example ex1;

    return 0;
}
```

디폴트 생성자는 생성자를 정의하는 시점부터 삽입되지 않습니다.  
즉 아래의 코드는 성립되지 않음을 유의해야합니다.  

```
#include <iostream>
using namespace std;

class Example{
private:
    int num;
public:
    Example(int n)
        : num(n)
    {}
    void Show() const;
};

void Example::Show() const {cout << num << endl;}

int main(void){

    Example ex1;

    return 0;
}
```

인자 하나를 받는 생성자밖에 없으므로 반드시 인자 하나를 줘야합니다.  
위의 코드를 옳게 고치려면 아래의 코드처럼 인자 하나를 받는 생성자를 추가해줘야 합니다.

```
#include <iostream>
using namespace std;

class Example{
private:
    int num;
public:
    Example(int n)
        : num(n)
    {}
    Example() :num(0) {}
    void Show() const;
};

void Example::Show() const {cout << num << endl;}

int main(void){

    Example ex1;

    return 0;
}
```

# private 생성자

앞서 보여드린 예시는 전부다 public으로 선언하였습니다.  
클래스 내부에서만 객체의 생성을 허용하려고 한다면 private으로 선언해도 됩니다.  

```
#include <iostream>
using namespace std;

class Example{
private:
    int num;
public:
    Example () : num(0) {}
    Example& CreateInitObj(int n) const{
        Example * ptr = new Example(n);
        return *ptr;
    }
    void Show() const;

private:
    Example(int n): num(n) {}
};

void Example::Show() const {cout << num << endl;}

int main(void){

    Example base;

    base.Show();

    Example &obj1 = base.CreateInitObj(5);
    obj1.Show();
    base.Show();


    return 0;
}

결과:
0
5
0
```

# 소멸자

객체생성시 밥ㄴ드시 호출되는 것이 생성자라면, 객체소멸시 반드시 호출되는 것이 소멸자입니다.  
소멸자는 클래스의 이름 앞에 ~가 붙은 형태이며 반환형이 선언되어
생성자를 정의하지 않아도 디폴트 생성자가 생기는 것처럼 소멸자도 디폴트 소멸자가 있습니다.  
예를들어 AAA라는 클래스가 정의되어 있다면 아래와 같은 형태의 소멸자도 생깁니다.  

```
~AAA() {내용}
```

이러한 소멸자는 대게 생성자에서 할당한 리소스의 소멸에 사용됩니다.  
예를들어 생성자 내에서 new 연산자를 이용해서 할당해 놓은 메모리 공간이 있다면, 소멸자에서는 delete 연산자를 이용해서 이 메모리 공간을 소멸하는 것입니다.  
아래에 샘플 예제를 보여드리겠습니다.  

```
#include <iostream>
#include <cstring>

using namespace std;

class Person {
private:
    char * name;
    int age;
public:
    Person(char * myname, int myage){
        int len = strlen(myname)+1;
        name = new char[len];
        strcpy(name,myname);
        age = myage;
    }

    void ShowInfo() const {
        cout <<"이름 : " << name << endl;
        cout <<"나이 : " << age << endl;
    }

    ~Person(){
        delete []name;
        cout <<"소멸자 생성!" << endl;
    }
};

int main(void){

    Person man1("김명수",25);
    Person woman1("김현서",24);

    man1.ShowInfo();
    woman1.ShowInfo();

    return 0;
}

결과:
이름 : 김명수
나이 : 25
이름 : 김현서
나이 : 24
소멸자 생성!
소멸자 생성!
```

소멸자를 따로 호출하지 않아도 자동으로 호출되는 것을 볼 수 있습니다.
