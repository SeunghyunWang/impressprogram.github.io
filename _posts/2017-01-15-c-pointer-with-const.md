---
layout: post
comments: true
title: C언어 포인터 대상으로 const 선언 주의점
categories: [c]
tags: [c, programming-language]
fullview: true
---
```
#include <stdio.h>

int main(int argc, const char * argv[]) {

    int num = 20;
    const int * ptr = &num;
    *ptr = 30; //에러!
    num = 40; //성공!
}
```
위의 예시에서 const int * ptr = &num; 가 말하는 바는 아래와 같습니다.  
"포인터 변수 ptr을 이용해서 ptr이 가리키는 변수에 저장되는 값을 변경하는 것을 허용하지 않겠습니다!"  
그렇다고 해서 포인터 변수 ptr이 가리키는 변수 num이 상수화되는 것은 아닙니다.  
```
#include <stdio.h>

int main(int argc, const char * argv[]) {

    int num1 = 10;
    int num2 = 20;
    int * const ptr = &num1;
    ptr = &num2; //컴파일 에러!
    *ptr = 40; //컴파일 성공!
}
```
위와 같이 선언을 하면 가리키는 대상을 바꾸는 것은 안되지만 저장된 값을 변경하는 것은 문제가 되지 않습니다.  
const int * const ptr = &num; 이런식으로 선언하는 것도 가능합니다.  
개인적으로 자주 헷갈려서 정리해 보았습니다.
