---
layout: post
comments: true
title: C++ 연산자 오버로딩(스마트 포인터, 펑터, 형변환)
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---

# 스마트 포인터

스마트 포인터는 일반 포인터와 다르게 자기 스스로 하는 일이 존재하는 포인터입니다.  
많은 라이브러리들에서 스마트 포인터를 제공하고 있지만 직접 가벼운 스마트 포인터를 구현해보겠습니다.  

```
#include <iostream>

using namespace std;

class Point{
private:
    int xpos, ypos;
public:
    Point(int x=0, int y=0) : xpos(x), ypos(y) {
        cout << "객체 생성" << endl;
    }

    void SetPos(int x, int y){
        xpos = x;
        ypos = y;
    }

    ~Point(){
        cout << "객체 소멸" << endl;
    }
    friend ostream& operator<<(ostream& os, const Point& pos);
};

ostream& operator<< (ostream& os, const Point& pos){
    os<< '[' << pos.xpos << ", " << pos.ypos << ']' << endl;
    return os;
}

class SmartPtr{
private:
    Point * posptr;
public:
    SmartPtr(Point * ptr) : posptr(ptr) { }
    Point& operator*() const{
        return *posptr;
    }
    Point* operator->() const{
        return posptr;
    }
    ~SmartPtr(){
        delete posptr;
    }
};

int main(){
    SmartPtr sptr1(new Point(1, 2));
    SmartPtr sptr2(new Point(2, 3));
    SmartPtr sptr3(new Point(3, 4));
    cout << *sptr1;
    cout << *sptr2;
    cout << *sptr3;

    sptr1->SetPos(10,20);
    sptr2->SetPos(20, 30);
    sptr3->SetPos(30, 40);
    cout << *sptr1;
    cout << *sptr2;
    cout << *sptr3;

    return 0;
}
결과:
객체 생성
객체 생성
객체 생성
[1, 2]
[2, 3]
[3, 4]
[10, 20]
[20, 30]
[30, 40]
객체 소멸
객체 소멸
객체 소멸
```
보시다시피 SmartPtr을 통해서 객체를 자동으로 소멸시켜 주었습니다.  
즉, delete연산을 자동으로 이루어지게 해 주었다는 것입니다.  
물론 실무에서도 직접 구현할 기회는 드물며 라이브러리의 일부로 포함되어 있는 것을 쓰는 경우가 대부분입니다.  
스마트 포인터가 더 궁금하다면 아래의 블로그를 공부하는 것을 추천드립니다.  
http://jason-heo.github.io/cpp/2014/03/15/smart-pointer2.html

# ()연산자의 오버로딩과 펑터(Functor)

함수의 호출에 이용되는, 인자의 전달에 사용되는 ()도 연산자입니다.  
당연히 오버로딩이 됩니다.  
그리고 이 연산자를 오버로딩하면, 객체를 함수처럼 사용할 수 있습니다.

```
#include <iostream>

using namespace std;

class Point{
private:
    int xpos, ypos;
public:
    Point(int x=0, int y=0) : xpos(x), ypos(y) {}
    Point operator+(const Point& pos) const{
        return Point(xpos+pos.xpos, ypos+pos.ypos);
    }

    void SetPos(int x, int y){
        xpos = x;
        ypos = y;
    }

    friend ostream& operator<<(ostream& os, const Point& pos);
};

ostream& operator<< (ostream& os, const Point& pos){
    os<< '[' << pos.xpos << ", " << pos.ypos << ']' << endl;
    return os;
}

class Adder{
public:
    int operator()(const int& n1, const int&n2){
        return n1+n2;
    }
    double operator()(const double& d1, const double& d2){
        return d1+d2;
    }
    Point operator()(const Point& pos1, const Point& pos2){
        return pos1+pos2;
    }
};

int main(){
    Adder adder;
    cout << adder(1, 3) << endl;
    cout << adder(1.5, 3.7) << endl;
    cout << adder(Point(1,2), Point(2,3));

    return 0;
}
```

위의 예제에서 정의한 Adder 클래스와 같이 함수처럼 동작하는 클래스를 펑터(Functor)라고 부릅니다.  
또는 함수 오브젝트(Function Object)라고도 불립니다.  
이는 보통 객체의 동작방식에 유연함을 제공할 때 주로 사용됩니다.  

```
#include <iostream>

using namespace std;

class SortRule{
public:
    virtual bool operator()(int num1, int num2) const = 0;
};

class AscendingSort : public SortRule{
public:
    bool operator()(int num1, int num2) const {
        if(num1>num2)
            return true;
        else
            return false;
    }
};

class DescendingSort : public SortRule{
public:
    bool operator()(int num1, int num2) const {
        if(num1<num2)
            return true;
        else
            return false;
    }
};

class DataStorage{
private:
    int * arr;
    int idx;
    const int MAX_LEN;
public:
    DataStorage(int arrlen) :idx(0), MAX_LEN(arrlen){
        arr = new int[MAX_LEN];
    }
    void AddData(int num){
        if(MAX_LEN<=idx){
            cout << "더 이상 저장이 불가능합니다." << endl;
            return;
        }
        arr[idx++]=num;
    }
    void ShowData(){
        for(int i = 0; i < idx; i++)
            cout << arr[i] << ' ';
        cout << endl;
    }
    void SortData(const SortRule& functor){
        for(int i = 0; i<(idx-1); i++){
            for(int j = 0; j<(idx-1); j++){
                if(functor(arr[j], arr[j+1])){
                    int temp = arr[j];
                    arr[j]=arr[j+1];
                    arr[j+1]=temp;
                }
            }
        }
    }
};

int main(){
    DataStorage storage(5);
    storage.AddData(10);
    storage.AddData(55);
    storage.AddData(25);
    storage.AddData(44);
    storage.AddData(33);

    storage.SortData(AscendingSort());
    storage.ShowData();

    storage.SortData(DescendingSort());
    storage.ShowData();

    return 0;
}
결과:
10 25 33 44 55
55 44 33 25 10
```

# 형변환 연산자 오버로딩

형 변환 연산자는 아래와 같은 문법으로 정의합니다.  

operator int (){
  return num;
}
위 처럼 하면 int형으로 변환하는 경우에 대해 num을 리턴해달라고 하는 뜻입니다.  
반환형이 없는데 return값이 존재하는 것이 큰 특징입니다.  
아래에 예시를 보여드리겠습니다.

```
#include <iostream>

using namespace std;

class Number{
private:
    int num;
public:
    Number(int n=0) :num(n){
        cout << "객체 생성" << endl;
    }
    Number& operator=(const Number& ref){
        cout << "대입 연산자 사용" << endl;
        num = ref.num;
        return *this;
    }
    operator int (){
        return num;
    }
    void ShowNumber(){
        cout << num << endl;
    }
};

int main(){
    Number num1;
    num1=30;
    Number num2=num1+20;
    num2.ShowNumber();

    return 0;
}
결과:
객체 생성
객체 생성
대입 연산자 사용
객체 생성
50
```

Number num2 = num1+20;  
위의 예제에서 num1이 형변환 연산자에 의해 int num꼴로 바뀌고 이것이 20과 더해져 num2에 대입된 것입니다.  
