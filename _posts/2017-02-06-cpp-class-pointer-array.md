---
layout: post
comments: true
title: C++ 객체 배열과 객체 포인터 배열(+this)
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---

# 객체 배열

객체 기반의 배열은 다음과 같은 방식으로 선언합니다.

```
Example arr[10]; //배열 선언
Example * arr = new Example[10]; //동적 할당
```

이렇게 생성한다면 열개의 Example객체가 모여서 배열을 구성합니다.  
단 위의 형태로 배열이 생성되려면 당연히 아래와 같은 생성자가 반드시 정의되어 있어야 합니다.  
(왜 그런지 모르신다면 이전 포스팅을...)

```
Example() {내용}
```

객체 배열을 통해 객체를 생성하고 초기화 하려면 아래와 같은 방식을 취해야합니다.  
객체를 우선 생성하고 멤버변수에 접근할 수 있는 함수를 제공해줍니다.

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

    Person() {
        name = NULL;
        age = 0;
        cout << "Person객체 생성" << endl;
    }

    void SetPerson(char * myname, int myage){
        name = myname;
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

    Person per[3];
    char namestr[100];
    char * strptr;
    int age;
    int len;

    for(int i=0; i<3; i++){
        cout << "이름 : ";
        cin >> namestr;
        cout <<"나이 : ";
        cin >> age;
        len = strlen(namestr)+1;
        strptr=new char[len];
        strcpy(strptr,namestr);
        per[i].SetPerson(strptr, age);
    }

    return 0;
}
```

# 객체 포인터 배열

객체 배열이 객체로 이루어진 배열이라면, 객체 포인터 배열은 객체의 주소 값 저장이 가능한 포인터 변수로 이뤄진 배열입니다.  
앞의 예제를 한번 객체 포인터 배열 기반으로 변경해 보겠습니다.

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

    Person() {
        name = NULL;
        age = 0;
        cout << "Person객체 생성" << endl;
    }

    void SetPerson(char * myname, int myage){
        name = myname;
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

    Person * per[3];
    char namestr[100];
    int age;

    for(int i=0; i<3; i++){
        cout << "이름 : ";
        cin >> namestr;
        cout << "나이 : ";
        cin >> age;
        per[i] = new Person(namestr, age);
    }
    per[0] -> ShowInfo();
    per[1] -> ShowInfo();
    per[2] -> ShowInfo();
    delete per[0];
    delete per[1];
    delete per[2];

    return 0;
}

```

위 처럼 객체를 저장할때 배열의 형식을 쓰려면 두 가지를 선택해야 합니다.  
저장의 대상을 객체로 하느냐, 객체의 주소 값으로 하느냐를 결정해야 합니다.  

# this 포인터

멤버함수 내에서는 this라는 이름의 포인터를 사용할 수 있는데, 이는 객체 자신을 가리키는 용도로 사용되는 포인터입니다.  
아래의 예제를 통해 쉽게 확인할 수 있습니다.  

```
#include <iostream>
#include <cstring>

using namespace std;

class Example {
private:
    int num;
public:
    Example(int n) :num(n){
        cout << "num : " << num << ", ";
        cout << "address : " << this << endl;
    }

    void ShowNum() {
        cout << num << endl;
    }

    Example * getPointer() {
        return this;
    }
};

int main(void){

    Example ex1(100);
    Example * ptr1 = ex1.getPointer();
    cout << ptr1 << endl;
    ptr1 -> ShowNum();

    Example ex2(200);
    Example * ptr2 = ex2.getPointer();
    cout << ptr2 << endl;
    ptr2 -> ShowNum();

    return 0;
}
결과 :
num : 100, address : 0x7fff5fbff788
0x7fff5fbff788
100
num : 200, address : 0x7fff5fbff778
0x7fff5fbff778
200
```

이 this는 여러 환경에서 유용하게 쓰이는데 아래와 같이 매개변수와 멤버변수의 이름이 같을 때에 특히 유용합니다.

```
class Example {
private:
    int num;
public:
    void ThisFunc(int num){
        this -> num=207; //멤버변수의 값을 207로 변경함
        num = 105; //매개변수의 값을 105로 변경함
    }
};
```

# self-reference

self-reference란 객체 자신을 참조할 수 있는 참조자를 의미합니다.  
우리는 this포인터를 이용해서 쉽게 구성할 수 있습니다.

```
#include <iostream>
#include <cstring>

using namespace std;

class Example {
private:
    int num;
public:
    Example(int n) : num(n){
        cout << "객체생성" << endl;
    }

    Example& Adder(int n){
        num += n;
        return *this;
    }

    Example& ShowNum(){
        cout << num << endl;
        return *this;
    }
};

int main(void){

    Example ex(3);
    Example &ref = ex.Adder(2);

    ex.ShowNum();
    ref.ShowNum();

    ref.Adder(1).ShowNum().Adder(2).ShowNum();

    return 0;
}
결과 :
5
5
6
8
```

위와 같이 우리는 this 포인터를 이용해서, 객체가 자신의 참조에 사용할 수 있는 참조자의 반환문을 구성할 수 있습니다.
