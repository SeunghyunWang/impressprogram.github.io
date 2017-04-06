---
layout: post
comments: true
title: C++ 기본 연산자 오버로딩(연산자 재정의)
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---

# operator+로 살펴보는 연산자 오버로딩

예시를 먼저 들어서 연산자 오버로딩이 어떤 것인지 알아보겠습니다.  
operator+ 오버로딩을 통해 객체의 덧셈연산자를 오버로딩 해보도록 하겠습니다.    

```
#include <iostream>

using namespace std;

class Point{
private:
    int xpos, ypos;
public:
    Point(int x=0, int y=0) : xpos(x), ypos(y){}
    void ShowPosition() const{
        cout << '[' << xpos << ", " << ypos << ']' << endl;
    }
    Point operator+(const Point &ref){
        Point pos(xpos+ref.xpos, ypos+ref.ypos);
        return pos;
    }
};

int main(){
    Point pos1(3, 4);
    Point pos2(10, 20);
    Point pos3=pos1.operator+(pos2);
    Point pos4 = pos1+pos2;

    pos1.ShowPosition();
    pos2.ShowPosition();
    pos3.ShowPosition();
    pos4.ShowPosition();

    return 0;
}
결과:
[3, 4]
[10, 20]
[13, 24]
[13, 24]
```
operator+ 함수를 정의했는데 덧셈연산자까지 바뀐 것을 알 수 있습니다.  
즉 아래의 두 식은 완전히 동일한 식입니다.  

```
Point pos3 = pos1.operator+(pos2);
Point pos3 = pos1+pos2;
```

이것이 가능한 이유는 C++가 변환규칙을 제공하기 때문입니다.  
즉, 위의 식에서 pos1+pos2를 해줄 때, pos1이 객체라면 pos1.operator+(pos2)로 바꾸어서 해석해줍니다.  
다른 연산자들도 마찬가지고 operator에 연산자를 붙여서 operator- 꼴로 오버로딩 할 수 있습니다.  
많이 착각하는 것들중 하나로 연산자 오버로딩은 연산의 대상이 되는 피연산자의 값을 변경하는게 아니고 새로운 연산의 결과를 만들어내는 것이므로 const 선언이 가능합니다.  

# 연산자 오버로딩 방법

연산자를 오버로딩하는데는 두 가지 방법이 있습니다.  

1. 멤버함수에 의한 연산자 오버로딩
2. 전역함수에 의한 연산자 오버로딩

형태는 pos1+pos2기준으로 아래와 같이 다릅니다.  

```
pos1.operator+(pos2); //멤버함수로 오버로딩 된 경우
operator+(pos1, pos2); //전역함수로 오버로딩 된 경우
```

동일한 자료형을 두 가지 방법으로 동시에 오버로딩 했다면 멤버함수 기반이 우선시됩니다.  
왠만한 경우에는 멤버함수로 오버로딩을 하는 것이 좋습니다.  
(특수한 경우에는 전역함수로 오버로딩을 해야 하는데 뒤에서 다루겠습니다.)  
멤버함수로 오버로딩 하는 것은 앞에서 보여드렸으니 여기서는 같은 operator+를 전역함수로 오버로딩 해보겠습니다.

```
#include <iostream>

using namespace std;

class Point{
private:
    int xpos, ypos;
public:
    Point(int x=0, int y=0) : xpos(x), ypos(y){}
    void ShowPosition() const{
        cout << '[' << xpos << ", " << ypos << ']' << endl;
    }
    friend Point operator+(const Point &pos1, const Point &pos2){
        Point pos(pos1.xpos+pos2.xpos, pos1.ypos+pos2.ypos);
        return pos;
    }
};

int main(){
    Point pos1(3, 4);
    Point pos2(10, 20);
    //Point pos3=pos1.operator+(pos2); 에러
    Point pos3=operator+(pos1, pos2);
    Point pos4 = pos1+pos2;

    pos1.ShowPosition();
    pos2.ShowPosition();
    pos3.ShowPosition();
    pos4.ShowPosition();

    return 0;
}
```

# 연산자 오버로딩 주의사항

- 오버로딩이 불가능한 연산자

```
.   // 멤버 접근 연산자
.*   // 멤버 포인터 연산자
::   // 범위 지정 연산자
?:   // 조건 연산자(3항 연산자)
sizeof   // 바이트 단위 크기 계산
typeid   // RTTI 관련 연산자
static_cast   // 형변환 연산자
dynamic_cast   // 형변환 연산자
const_cast   // 형변환 연산자
reinterpret_cast   // 형변환 연산자
```

- 멤버함수 기반으로만 오버로딩이 가능한 연산자

```
=   // 대입 연산자
()   // 함수 호출 연산자
[]   // 배열 접근 연산자(인덱스 연산자)
->   // 멤버 접근을 위한 포인터 연산자
```

- 연산자 오버로딩시 주의사항

1. 본래의 의도를 벗어난 형태의 연산자 오버로딩을 하지 말자.  

연산자 오버로딩은 잘못 사용하면 프로그램을 복잡하고 어렵게 만듭니다.  
특히 +가 이상한 방향으로 +를 한다거나 엉뚱하게 pos1+pos2에서 pos2 값을 변경시킨다거나 하면 혼란이 올 것입니다.  
이런 예상치 못한 연산자의 사용을 최소화해야 합니다.  

2. 연산자의 우선순위와 결합성은 바뀌지 않습니다.  

3. 매개변수의 디폴트 값 설정이 불가능합니다.  

연산자 오버로딩 특성상 매개변수의 디폴트 값이 설정되면, 함수의 호출관계가 매우 불분명해지므로 허용되지 않습니다.  

4. 연산자의 순수 기능까지 빼앗을 수는 없습니다.  

더하기 연산자를 곱하기로 재정의한다거나.. 하는 것은 안됩니다.

# 단항 연산자의 오버로딩

단항 연산자는 아래와 같은 꼴로 많이 보았을 것입니다.  

```
a++;
a--;
++a;
--a;
```

이것도 operator 키워드와 ++,-- 연산자를 연결해서 재정의가 가능합니다.  
즉 아래와 같은 꼴이 가능합니다.  

```
pos.operator++() // ++pos;
pos.operator--() // --pos;
pos.operator++(int) // pos++;
pos.operator--(int) // pos--;
```

먼저 ++pos, --pos의 예시입니다(전위 증감연산자).  

```
#include <iostream>

using namespace std;

class Point{
private:
    int xpos, ypos;
public:
    Point(int x=0, int y=0) : xpos(x), ypos(y){}
    void ShowPosition() const{
        cout << '[' << xpos << ", " << ypos << ']' << endl;
    }
    Point& operator++(){
        xpos +=1;
        ypos +=1;
        return *this;
    }

    friend Point& operator-- (Point &ref);
};


Point& operator-- (Point &ref){
    ref.xpos -=1;
    ref.ypos -=1;
    return ref;
}

int main(){
    Point pos(1, 1);
    ++pos;
    pos.ShowPosition();
    pos.operator++();
    pos.ShowPosition();
    --pos;
    pos.ShowPosition();
    operator--(pos);
    pos.ShowPosition();


    return 0;
}
결과:
[2, 2]
[3, 3]
[2, 2]
[1, 1]
```

위의 예시에서 operator++나 operator--가 참조값을 반환하는 이유는 다음과 같은 형태의 연산이 가능하게 하기 위함입니다.  
--(--pos);  
다음으로는 후위 증감 연산자에 대한 예시를 보여드리겠습니다.  
키워드 int값을 통해서 후위증감 규칙을 정해놓고 있습니다.  
더불어 const 선언을 하면 어떠한 일이 일어나는지도 살펴보겠습니다.  

```
#include <iostream>

using namespace std;

class Point{
private:
    int xpos, ypos;
public:
    Point(int x=0, int y=0) : xpos(x), ypos(y){}
    void ShowPosition() const{
        cout << '[' << xpos << ", " << ypos << ']' << endl;
    }
    const Point& operator++(int){
        const Point retobj(xpos, ypos);
        xpos +=1;
        ypos +=1;
        return retobj;
    }

    friend const Point& operator-- (Point &ref, int);
};


const Point& operator-- (Point &ref, int){
    const Point retobj(ref);
    ref.xpos -=1;
    ref.ypos -=1;
    return retobj;
}

int main(){
    Point pos(1, 1);
    Point cpy;
    pos++;
    pos.ShowPosition();
    cpy = pos--;
    pos.ShowPosition();
    cpy.ShowPosition();
    cpy = pos++;
    pos.ShowPosition();
    cpy.ShowPosition();


    return 0;
}
결과 :
[2, 2]
[1, 1]
[2, 2]
[2, 2]
[1, 1]
```

반환형을 const 객체로 하는 것과 참조자로 하는 것에는 어떤 차이점이 있을까요?  
바로 (pos++)++; 이런 꼴을 허용하느냐 마느냐의 차이점이 있습니다.  
(pos++)++; -> (const임시 객체)++; 이런 꼴로 변하므로 const로 반환형을 선언하면 이런 문장을 수행하지 못합니다.  
위의 예제에서 cpy를 임시 객체로 생각해본다면 쉽게 이해가 갈 것입니다.  
참고로 C++에서도 ++(++num);은 허용하되 (num++)++;은 허용하지 않고 있습니다.

# 교환법칙

우선 일반적인 연산자 오버로딩이 어떤 문제점이 생길 수 있는지 다음 예제를 통해 보겠습니다.  

```
#include <iostream>

using namespace std;

class Point{
private:
    int xpos, ypos;
public:
    Point(int x=0, int y=0) : xpos(x), ypos(y){}

    void ShowPosition() const{
        cout << '[' << xpos << ", " << ypos << ']' << endl;
    }

    Point operator*(int times){
        Point pos(xpos*times, ypos*times);
        return pos;
    }
};

int main(){
    Point pos1(3, 4);
    Point cpy = pos1 * 3;
    cpy.ShowPosition();
    cpy = cpy * 3;
    cpy.ShowPosition();

    //cpy = 3 * cpy;   오류

    return 0;
}
```

위에서 보듯이 cpy * 3는 정상적으로 재정의가 되지만 3 * cpy는 오류를 일으킵니다.  
즉 교환법칙이 성립되지 않는 것입니다.  
이를 전역함수를 사용해서 해결할 수 있습니다.  

```
#include <iostream>

using namespace std;

class Point{
private:
    int xpos, ypos;
public:
    Point(int x=0, int y=0) : xpos(x), ypos(y){}

    void ShowPosition() const{
        cout << '[' << xpos << ", " << ypos << ']' << endl;
    }

    Point operator*(int times){
        Point pos(xpos*times, ypos*times);
        return pos;
    }
    friend Point operator*(int times, Point& ref);
};

Point operator*(int times, Point& ref){
    return ref*times;
}

int main(){
    Point pos1(3, 4);
    Point cpy = pos1 * 3;
    cpy.ShowPosition();
    cpy = 3 * cpy;
    cpy.ShowPosition();

    return 0;
}
```
