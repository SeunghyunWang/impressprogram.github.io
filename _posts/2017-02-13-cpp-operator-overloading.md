---
layout: post
comments: true
title: C++ 연산자 오버로딩(대입, 인덱스, new, delete, 포인터)
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---

# 대입 연산자의 오버로딩

```
Point pos1(5, 7);
Point pos2=pos1;
```
우리는 Point라는 클래스를 모르지만 위의 코드에서 Point pos2 = pos1이 Point pos2(pos1)로 묵시적 변환을 한다는 것을 알 수 있습니다.  
하지만 아래의 코드에서는 묵시적 변환이 일어나지 않습니다.

```
Point pos1(5, 7);
Point pos2(9, 10);
pos2=pos1;
```

위의 문장에서 pos2=pos1부분에서는 pos2와 pos1이 이미 생성 및 초기화가 진행된 객체이기 때문에 묵시적 변환이 일어나는 대신 다음의 형태로 해석됩니다.  
pos2.operator=(pos1);  
디폴트 대입연산자가 있기 때문에 위와 같은 pos2 = pos1 문장에서 정상적으로 객체간에 멤버대 멤버의 복사가 이루어지는 것입니다.  
하지만 복사생성자와 같은 이슈를 갖고 있습니다.  
디폴트 대입연산자의 경우 얕은복사 이슈를 갖고 있기때문에 필요에 따라서 재정의(오버로딩)를 해주어야 합니다.  
아래의 경우가 바로 얕은복사 이슈입니다.

```
#include <iostream>

using namespace std;

class Person{
private:
    char * name;
    int age;
public:
    Person(char * myname, int myage) : age(myage){
        int len = strlen(myname)+1;
        name = new char[len];
        strcpy(name, myname);
    }

    void ShowInfo() const{
        cout << "이름 : " << name << endl;
        cout << "나이 : " << age << endl;
    }

    ~Person(){
        delete []name;
        cout << "소멸자 생성" << endl;
    }
};


int main(){
    Person man("김명수", 25);
    Person woman("김현서", 24);
    man = woman;
    man.ShowInfo();
    woman.ShowInfo();

    return 0;
}
결과:
이름 : 김현서
나이 : 24
이름 : 김현서
나이 : 24
소멸자 생성
```

보시다시피 소멸자가 한번만 생성되었습니다.  
name의 값을 복사하는 것이 아니라 주소값을 복사해서 먼저 woman이 소멸되면 그 값을 가르키는 man은 소멸될 수가 없는 것입니다.  
이미 c언어에서 복사생성자를 다룬적이 있기 때문에 이정도로 설명하고 넘어가겠습니다.  
이 문제는 대입 연산자를 재정의하면서 깊은 복사가 이루어지도록 해주면 됩니다.  
그리고 메모리 누수가 발생하지 않도록 깊은 복사에 앞서 메모리 해체의 과정을 거쳐야합니다.
아래의 코드를 참조하면 됩니다.

```
#include <iostream>

using namespace std;

class Person{
private:
    char * name;
    int age;
public:
    Person(char * myname, int myage) : age(myage){
        int len = strlen(myname)+1;
        name = new char[len];
        strcpy(name, myname);
    }

    void ShowInfo() const{
        cout << "이름 : " << name << endl;
        cout << "나이 : " << age << endl;
    }

    Person& operator=(const Person& ref){
        delete []name;
        int len= strlen(ref.name)+1;
        name = new char[len];
        strcpy(name, ref.name);
        age=ref.age;
        return *this;
    }

    ~Person(){
        delete []name;
        cout << "소멸자 생성" << endl;
    }
};


int main(){
    Person man("김명수", 25);
    Person woman("김현서", 24);
    man = woman;
    man.ShowInfo();
    woman.ShowInfo();

    return 0;
}
결과:
이름 : 김현서
나이 : 24
이름 : 김현서
나이 : 24
소멸자 생성
소멸자 생성
```

그리고 대입 연산자는 생성자가 아니기 때문에 유도 클래스에서 명시를 해주지 않으면 기초 클래스의 대입연산자가 호출되지 않습니다.  

```
#include <iostream>

using namespace std;

class First{
private:
    int num1, num2;
public:
    First(int n1=0, int n2=0) : num1(n1), num2(n2) {}
    void ShowData() {
        cout << num1 << ", " << num2 << endl;
    }

    First& operator=(const First& ref){
        cout << "First!!" << endl;
        num1 = ref.num1;
        num2 = ref.num2;
        return *this;
    }
};

class Second : public First{
private:
    int num3;
    int num4;
public:
    Second(int n1, int n2, int n3, int n4) : First(n1, n2), num3(n3), num4(n4) {}
    void ShowData(){
        First::ShowData();
        cout << num3 << ", " << num4 << endl;
    }

    Second& operator=(const Second& ref){
        cout << "Second!!" << endl;
        First::operator=(ref);
        num3 = ref.num3;
        num4 = ref.num4;
        return *this;
    }

};

int main(){
    Second s1(1,2,3,4);
    Second s2(5,6,7,8);
    s1 = s2;
    s1.ShowData();

    return 0;
}
```

따라서 First::operator=(ref); 를 통해서 임의로 기초클래스의 대입연산자를 호출해주어야 합니다.  

# 배열의 인덱스 연산자 오버로딩

배열의 인덱스 연산자 오버로딩은 아래의 두 가지로 나누어서 보도록 하겠습니다.  

1. Point 객체의 주소 값을 저장하는 배열 기반의 클래스
2. Point 객체를 저장하는 배열 기반의 클래스

먼저 2번입니다.  
복사생성자와 대입연산자를 private 선언 하였습니다(복사가 일어나지 않고 단순히 객체를 저장하려는 용도이므로).  

```
#include <iostream>
#include <cstdlib>

using namespace std;

class Point{
private:
    int xpos, ypos;
public:
    Point(int x=0, int y=0) : xpos(x), ypos(y) {}
    friend ostream& operator<<(ostream& os, const Point& pos);
};

ostream& operator<< (ostream& os, const Point& pos){
    os<< '[' << pos.xpos << ", " << pos.ypos << ']' << endl;
    return os;
}

class PointArray{
private:
    Point * arr;
    int arrlen;

    PointArray(const PointArray& arr) { } //복사생성자 접근방지(복사가 아니라 저장이 일어나게 해야하므로)
    PointArray& operator=(const PointArray& arr) {return *this; } //대입 연산자 접근 방지(마찬가지 이유)
public:
    PointArray(int len) : arrlen(len){
        arr = new Point[len];
    }
    Point& operator[] (int idx){
        if(idx<0 || idx>arrlen){
            cout << "배열의 크기를 넘어서거나 값이 0보다 작습니다." << endl;
            exit(1);
        }
        return arr[idx];
    }
    Point& operator[] (int idx) const{
        if(idx<0 || idx>arrlen){
            cout << "배열의 크기를 넘어서거나 값이 0보다 작습니다." << endl;
            exit(1);
        }
        return arr[idx];
    }
    int GetArrLen() const {return arrlen;}
    ~PointArray() {delete []arr;}

};

int main(){
    PointArray arr(3);
    arr[0] = Point(3, 4);
    arr[1] = Point(4, 5);
    arr[2] = Point(5, 6);

    for(int i = 0; i < arr.GetArrLen(); i++)
        cout << arr[i];

    return 0;
}
```

다음으로는 1번입니다.  
주소 값을 저장하는 경우, 객체의 생성과 소멸을 위한 new, delete 연산 때문에 더 신경쓸 것이 많아보이지만 깊은 복사냐 얕은 복사냐 하는 문제를 신경쓰지 않아도 되기 때문에 이 방법이 더 많이 사용된다고 합니다.  

```
#include <iostream>
#include <cstdlib>

using namespace std;

class Point{
private:
    int xpos, ypos;
public:
    Point(int x=0, int y=0) : xpos(x), ypos(y) {}
    friend ostream& operator<<(ostream& os, const Point& pos);
};

ostream& operator<< (ostream& os, const Point& pos){
    os<< '[' << pos.xpos << ", " << pos.ypos << ']' << endl;
    return os;
}

typedef Point * POINT_PTR;

class PointArray{
private:
    POINT_PTR * arr;
    int arrlen;

    PointArray(const PointArray& arr) { } //복사생성자 접근방지(복사가 아니라 저장이 일어나게 해야하므로)
    PointArray& operator=(const PointArray& arr) {return *this; } //대입 연산자 접근 방지(마찬가지 이유)
public:
    PointArray(int len) : arrlen(len){
        arr = new POINT_PTR[len];
    }
    POINT_PTR& operator[] (int idx){
        if(idx<0 || idx>arrlen){
            cout << "배열의 크기를 넘어서거나 값이 0보다 작습니다." << endl;
            exit(1);
        }
        return arr[idx];
    }
    POINT_PTR& operator[] (int idx) const{
        if(idx<0 || idx>arrlen){
            cout << "배열의 크기를 넘어서거나 값이 0보다 작습니다." << endl;
            exit(1);
        }
        return arr[idx];
    }
    int GetArrLen() const {return arrlen;}
    ~PointArray() {delete []arr;}

};

int main(){
    PointArray arr(3);
    arr[0] = new Point(3, 4);
    arr[1] = new Point(4, 5);
    arr[2] = new Point(5, 6);

    for(int i = 0; i < arr.GetArrLen(); i++)
        cout << *(arr[i]);

    delete arr[0];
    delete arr[1];
    delete arr[2];

    return 0;
}
```

# new, delete 연산자 오버로딩

당연히 new와 delete연산자도 오버로딩이 됩니다.  
특이한 점이 있다면 static으로 선언된 함수이므로 멤버함수로 선언한다고 해도 static 함수로 간주가 됩니다.  
따라서 객체생성의 과정에서도 호출이 가능합니다.  
그리고 반환형이 반드시 void형이어야 하고 매개변수형은 size_t여야 합니다.  

```
#include <iostream>

using namespace std;

class Point{
private:
    int xpos, ypos;
public:
    Point(int x=0, int y=0) : xpos(x), ypos(y) {}
    friend ostream& operator<<(ostream& os, const Point& pos);

    void * operator new (size_t size){
        cout << size << " 크기의 객체 생성" << endl;
        void * adr = new char[size];
        return adr;
    }

    void operator delete (void * adr){
        cout << "객체가 삭제됨" << endl;
    }
};

ostream& operator<< (ostream& os, const Point& pos){
    os<< '[' << pos.xpos << ", " << pos.ypos << ']' << endl;
    return os;
}

int main(){
    Point * ptr = new Point(3,4);
    cout << *ptr;
    delete ptr;

    return 0;
}
결과:
8 크기의 객체 생성
[3, 4]
객체가 삭제됨
```

배열은 void * operator new[] (size_t size){} 꼴과 void operator delete(void * adr){} 꼴로 오버로딩합니다.  

# 포인터 연산자 오버로딩

```
#include <iostream>

using namespace std;

class Number{
private:
    int num;
public:
    Number(int n) : num(n) { }
    void ShowData() { cout << num << endl; }

    Number * operator->(){
        return this;
    }
    Number& operator*(){
        return *this;
    }
};

int main(){
    Number num(20);
    num.ShowData();

    (*num) = 30;
    num->ShowData();
    (*num).ShowData();

    return 0;
}
결과:
20
30
30
```

다른 것은 이해가 쉽게 가겠지만 num->ShowData(); 부분이 이해가 안 갈수 있습니다.  
num->가 num.operator->()와 같으므로 주소값을 리턴할텐데 num의주소ShowData가 되면 문법적으로 성립하지 않기 때문입니다.  
따라서 C++는 반환되는 주소 값을 대상으로 적절한 연산이 가능하도록 -> 연산자를 하나 더 추가하여 해석을 하게됩니다.  
num.operator->() -> ShowData(); 가 되는 것입니다.  
물론 이 예시는 private 으로 선언된 num의 값을 main함수에서 변경하고 있으니 이는 적절치 못한 예제이기는 합니다.
