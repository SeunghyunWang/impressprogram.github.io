---
layout: post
comments: true
title: C++ 캡슐화에 대한 이야기
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---
캡슐화란 관련있는 함수와 변수를 하나의 클래스 안에 묶는 것입니다.  
캡슐화의 범위를 지정하는 것이 어려워서 많이들 어려워하는 개념이기도 합니다.  
예를들어 열혈강의에서는 콘택600으로 설명을 합니다(매우 적절한 예시인 것 같습니다).  
콘택600이 어떤 것인지 모르는 분들을 위해서 설명드리자면 콘택600은 코감기의 처방을 위한 알약입니다.  

```
#include <iostream>
using namespace std;

class SinivelCap{
public:
    void Take() const {cout << "콧물이 싹~ 납니다." <<endl;}
};

class SneezeCap{
public:
    void Take() const {cout << "재채기가 멎습니다." << endl;}
};

class SnuffleCap{
public:
    void Take() const {cout << "코가 뻥 뚫립니다." <<endl;}
};

class ColdPatient{
public:
    void TakeSinivelCap(const SinivelCap &cap) const {cap.Take();}
    void TakeSneezeCap(const SneezeCap &cap) const {cap.Take();}
    void TakeSnuffleCap(const SnuffleCap &cap) const {cap.Take();}
};

int main(void){
    SinivelCap scap;
    SneezeCap zcap;
    SnuffleCap ncap;

    ColdPatient sufferer;
    sufferer.TakeSinivelCap(scap);
    sufferer.TakeSneezeCap(zcap);
    sufferer.TakeSnuffleCap(ncap);

    return 0;
}
```

만일 코감기는 항상 콧물, 재채기, 코막힘을 동반한다고 가정하겠습니다.  
그렇다면 위의 사례는 캡슐화가 제대로 적용되지 않은 것입니다.  
우선 이러한 가정을 한다면 위의 코드는 복용의 절차가 너무 복잡하다는 단점이 있습니다.  
그리고 복용의 순서가 있다면 그 순서대로 사용자가 임의로 먹기가 힘들다는 단점도 있습니다.  
이렇게 캡슐화가 무너지면 객체의 활용이 어려워지고 프로그램 전체의 복잡도를 높이게 됩니다.  
아래는 캡슐화를 잘 적용한 예입니다.  

```
#include <iostream>
using namespace std;

class SinivelCap{
public:
    void Take() const {cout << "콧물이 싹~ 납니다." <<endl;}
};

class SneezeCap{
public:
    void Take() const {cout << "재채기가 멎습니다." << endl;}
};

class SnuffleCap{
public:
    void Take() const {cout << "코가 뻥 뚫립니다." <<endl;}
};

class CONTAC600{
private:
    SinivelCap sin;
    SneezeCap sne;
    SnuffleCap snu;

public:
    void Take() const{
        sin.Take();
        sne.Take();
        snu.Take();
    }
};

class ColdPatient{
public:
    void TakeCONTAC600(const CONTAC600 &cap) const {cap.Take();}
};

int main(void){
    CONTAC600 cap;
    ColdPatient sufferer;
    sufferer.TakeCONTAC600(cap);

    return 0;
}

```

하지만 현실세계에서는 프로그래밍을 할 때에 이렇게 명확하게 범위를 지정할 수가 없는 일이 대부분입니다.  
위의 코드에서 예시를 들자면 감기가 몸살까지 동반하는지 아니면 기침까지 동반하는지, 항상동반하는지 가끔 동반하는지 정확하게 정의할 수 없는 일들이 많기 때문입니다.
