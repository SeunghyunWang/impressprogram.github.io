---
layout: post
comments: true
title: C++ 템플릿(template)에 대하여
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---

템플릿 관련 정보는 : https://wikidocs.net/book/54 이곳에 엄청 풍족하게 있습니다.  
저는 개념을 작성하였고 위의 링크는 레퍼런스 형식으로 자세히 보기 좋습니다.  

# 템플릿의 기본

템플릿은 함수를 만들어 냅니다.  
함수의 기능은 결정되어 있지만, 자료형은 결정되어 있지 않은 경우에 템플릿을 활용할 수 있습니다.  
템플릿은 아래의 두 가지 방법으로 선언이 가능합니다.  

```
template <typename T>
template <class T>
```

사용법은 다음과 같습니다.

```
template <typename T>
T Add(T num1, T num2){
  return num1+num2;
}
```

위의 내용에서 template <typename T> 부분은 T라는 이름을 이용해서 아래의 함수를 템플릿으로 정의한다는 의미입니다.  
위 처럼 함수를 정의하면 int형을 진행하는 Add함수나 double형을 진행하는 Add함수를 쉽게 구동할 수 있습니다.

```
#include <iostream>

using namespace std;

template <typename T>
T Add(T num1, T num2){
    return num1+num2;
}

int main(){
    cout << Add(15, 20) << endl;
    cout << Add(1.5, 2.1) << endl;
    cout << Add<int>(1, 2) << endl;
    cout << Add<int>(1.4, 2.7) << endl;
    return 0;
}
결과:
35
3.6
3
3
```

<int>가 의미하는 바는 T를 int로 해서 만들어진 Add함수를 호출한다는 것입니다.  
붙이지 않으면 자동으로 컴파일러가 판단합니다.  
어떻게 이런 일이 가능할까요?  
함수를 템플릿으로 정의하면 컴파일을 할 때에 함수가 만들어 지는 것입니다.  
따라서 컴파일의 속도가 약간 느려지지만 실행속도에는 문제가 없습니다.  
이렇게 템플릿으로 함수를 정의하는 것을 '함수 템플릿'이라고 합니다.  
그리고 위의 템플릿으로 컴파일러가 만들어내는 아래와 같은 함수를 '템플릿 함수'라고 합니다.

```
int Add<int>(int num1, int num2){
  return num1+num2;
}
```

둘 이상의 형에 대해 템플릿을 만들때도 동일한 방법을 사용하면 됩니다.  
그리고 함수의 호출 시점에서 사용할 자료형을 명시해주면 됩니다.  

```
#include <iostream>

using namespace std;

template <class T1, class T2>
void ShowData(double num){
    cout <<(T1)num << ", " << (T2)num << endl;
}

int main(){
    ShowData<char, int>(65);
    ShowData<char, int>(67);
    ShowData<char, double>(67.9);
    ShowData<short, double>(45.5);

    return 0;
}
결과:
A, 65
C, 67
C, 67.9
45, 45.5
```

# 함수 템플릿의 특수화

물론 사용하지 말라고는 하지만 : https://wikidocs.net/652 어떤 것인지 알고 사용 안하는 것이 낫다고 판단되어 집어 넣었습니다.  
우선 코드를 하나 살펴보겠습니다.  

```
#include <iostream>

using namespace std;

template <class T>
T Max(T a, T b){
    return a>b ? a : b;
}

int main(){
    cout << Max(11, 15) << endl;
    cout << Max('T', 'Q') << endl;
    cout << Max(3.5, 6.5) << endl;
    cout << Max("Simple", "String") << endl;

    return 0;
}
```

위의 코드에서 더 큰 값을 출력해주지만 문자열에서는 별로 의미가 없습니다.  
문자열의 길이비교를 하는 함수와 사전편찬의 순서로 비교하는 함수를 구성하고 싶다고 가정하겠습니다.  
이렇게 상황에 따라서 예외를 두는 것을 '함수 템플릿의 특수화'라고 합니다.  
아래와 같이 동작합니다.  

```
#include <iostream>
#include <cstring>

using namespace std;

template <class T>
T Max(T a, T b){
    return a>b ? a : b;
}
template<>
char * Max(char* a, char* b){
    cout << "문자의 길이가 더 긴쪽은 " << endl;
    return strlen(a) > strlen(b) ? a : b;
}
template<>
const char* Max(const char* a, const char* b){
    cout << "문자열의 사전편찬순서가 더 큰 쪽은 " << endl;
    return strcmp(a, b) > 0 ? a : b;
}

int main(){
    cout << Max(11, 15) << endl;
    cout << Max('T', 'Q') << endl;
    cout << Max(3.5, 6.5) << endl;
    cout << Max("Simple", "Strin") << endl;
    char a[] = "Simple";
    char b[] = "strin";
    cout << Max(a,b) << endl;

    return 0;
}
결과:
15
T
6.5
문자열의 사전편찬순서가 더 큰 쪽은
Strin
문자의 길이가 더 긴쪽은
Simple
```

template<> 부분은 컴파일러에게 "아래의 형식의 함수는 내가 이렇게 제시를 하니 이 형식의 템플릿 함수가 필요한 경우에는 별도로 만들지말고 아래의 형식으로 써주라!" 하는 것입니다.

# 클래스 템플릿

함수를 템플릿으로 정의했듯이 클래스도 템플릿으로 정의가 가능합니다.  
이렇게 정의된 템플릿을 가리켜 '클래스 템플릿'이라고 하고 이를 기반으로 컴파일러가 만들어 내는 클래스를 가리켜 '템플릿 클래스'라고 합니다.  
동일한 저장 기능을 하는 클래스인데 저장의 대상이 다르다고 생각해 봅시다.  
예를 들자면 정수 두 개를 저장하는 클래스, 실수 두 개를 저장하는 클래스, 문자 두 개를 저장하는 클래스가 있다고 가정합시다.  
클래스 템플릿을 만든다면 한번에 다 만들 수 있습니다.  

```
#include <iostream>

using namespace std;

template <class T>
class Point{
private:
    T xpos, ypos;
public:
    Point(T x=0, T y=0) : xpos(x), ypos(y) {}
    void ShowPosition() const{
        cout << '[' << xpos << ", " << ypos << ']' << endl;
    }
};

int main(){
    Point<int> pos1(1,2);
    pos1.ShowPosition();

    Point<double> pos2(1.1, 2.2);
    pos2.ShowPosition();

    Point<char> pos3('A', 'B');
    pos3.ShowPosition();

    return 0;
}
결과:
[1, 2]
[1.1, 2.2]
[A, B]
```

함수 템플릿과 다른점은 호출할 때에 반드시 <int>, <double> 같이 자료형을 명시해주어야 한다는 점입니다.  
이렇게 우리는 손쉽게 같은 기능을 하는데 다른 자료형을 저장하는 클래스 템플릿을 구현하였습니다.  

클래스 템플릿도 선언과 정의를 분리할 수 있습니다.
주의해야할 점은 정의할 때에도 template <class T>를 별도로 선언해주어야 한다는 것입니다.

```
#include <iostream>

using namespace std;

template <class T>
class Point{
private:
    T xpos, ypos;
public:
    Point(T x=0, T y=0) : xpos(x), ypos(y) {}
    void ShowPosition() const;
};

template <class T>
void Point<T>::ShowPosition() const{
    cout << '[' << xpos << ", " << ypos << ']' << endl;
}

int main(){
    Point<int> pos1(1,2);
    pos1.ShowPosition();

    Point<double> pos2(1.1, 2.2);
    pos2.ShowPosition();

    Point<char> pos3('A', 'B');
    pos3.ShowPosition();

    return 0;
}
```

특히 배열 클래스 템플릿이 헷갈릴 수가 있으니 좀 더 보도록 하겠습니다.  
아래와 같이 정의된 배열 클래스 템플릿이 있다고 하고 설명하겠습니다.

```
template <class T>
class Point{
private:
    T xpos, ypos;
public:
    Point(T x=0, T y=0) : xpos(x), ypos(y) {}
    void ShowPosition() const;
};

template <class T>
class Simple{
private:
    T * arr;
    int arrlen;
    Simple(const Simple& arr){ }
    Simple& operator=(const Simple& arr){ }
public:
    Simple(int len);
    T& operator[] (int adx);
    T operator[] (int adx) const;
    int GetArrLen() const;
    ~Simple();
};
```

이에 대한 객체를 생성하려면 Simple<int> iarr(50); 꼴로 선언하면 될 것입니다.

```  
그런데 Point객체를 이곳에 저장한다고 가정하겠습니다.  
그렇다면 Simple<Point<int>> oarr(50); 꼴이 되어야 할 것입니다.  
Point<int>형 포인터를 저장해야 한다면? Simple<Point<int>*> oparr(50); 꼴이 되어야 할 것입니다.
```

또한 템플릿 클래스의 객체를 인자로 받는다거나 friend선언을 한다거나 하는 것도 가능합니다.

# 클래스 템플릿의 특수화

앞서 본 함수 템플릿의 특수화와 마찬가지로 특정 자료형을 기반으로 생성된 객체에 대해, 구분이 되는 다른 행동양식을 적용하기 위해 클래스 템플릿의 특수화를 할 수 있습니다.  

```
template <class T>
class Simple{
public:
    T SimpleFunc(T num){ 내용 }  
};
//int형에 대한 특수화 한 템플릿 클래스
template<>
class Simple<int>{
public:
    int SimpleFunc(int num){ 내용 }
};
```

위 처럼 정의한다면 Simple<int> obj; 꼴로 객체생성시 Simple<int>를 대상으로 객체가 생성됩니다.  
또한 아래처럼 부분 특수화도 가능합니다.  

```
template <class T1, class T2>
class Simple{};
//char와 int로 특수화
template <>
class Simple<char, int>
//T2에 대해서만 부분 특수화
template<>
class Simple<T1, int>
```

# 템플릿 인자

템플릿 매개변수의 선언에 마치 함수처럼 변수의 선언을 할 수도 있습니다.  
이것이 어떤 의미를 갖는지 아래의 예제로 살펴보겠습니다.  

```
#include <iostream>

using namespace std;

template <class T, int len>
class Simple{
private:
    T arr[len];
public:
    T& operator[] (int idx) {return arr[idx];}
    Simple<T, len>& operator=(const array<T, len>& ref){
        for(int i=0; i<len; i++)
            arr[i]=ref.arr[i];
        return *this;
    }
};
int main(){
    Simple<int, 5> i5arr1;
    for(int i=0; i<5; i++)
        i5arr1[i]=i*5;

    Simple<int, 5> i5arr2;
    i5arr2=i5arr1;

    for(int i=0; i<5; i++)
        cout << i5arr2[i] <<", ";
    cout << endl;

    Simple<int, 7> i7arr1;
    for(int i=0; i<7; i++)
        i7arr1[i]=i*7;

    Simple<int, 7> i7arr2;
    i7arr2=i7arr1;

    for(int i=0; i<7; i++)
        cout << i7arr2[i] << ", ";
    cout << endl;

    return 0;
}
```

왜 굳이 이런식으로 생성자를 이용하지 않고 템플릿 매개변수에 정수를 전달했을까요?  
이렇게 하면 Simple<int, 5>와 Simple<int, 7>이 다른 형으로 취급됩니다.  
따라서 길이가 다른 두 배열 객체의 대입을 허용안한다던가 할 수가 있습니다.  
즉, 서로 다른 길이를 갖은 배열 객체를 신경안써도 된다는 말입니다.  

또한 템플릿 매개변수는 디폴트 값도 지정이 가능합니다.

# 템플릿과 static

위의 내용으로도 짐작이 가겠지만 static변수와 클래스 템플릿이 결합되면 재미있는 현상이 벌어집니다.  

```
#include <iostream>

using namespace std;

template <class T>
class Simple{
private:
    static T mem;
public:
    void AddMem(int num){
        mem += num;
    }
    void ShowMem() const {
        cout << mem << endl;
    }
};

template <class T>
T Simple<T>::mem=0;
template <>
long Simple<long>::mem=5;

int main(){
    Simple<int> obj1;

    obj1.AddMem(2);
    obj1.AddMem(3);
    obj1.ShowMem();

    Simple<long> obj3;
    obj3.AddMem(5);
    obj3.ShowMem();

    return 0;
}
```

템플릿에 정의된 자료형에 따라서 static 멤버변수를 객체간에 공유합니다.  
즉 int형으로 정의된 클래스끼리, long형으로 정의된 클래스끼리 서로 static 멤버변수를 공유하는 것입니다.  
초기화는 위에 보이듯이 T Simple<T>::mem=0; 꼴로 진행합니다.
