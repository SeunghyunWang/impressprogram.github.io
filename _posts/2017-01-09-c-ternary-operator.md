---
layout: post
comments: true
title: C언어-if문대신 삼항연산자를 써보자
categories: [c]
tags: [c, programming-language]
fullview: true
---
주로 C언어에서 어떤 조건에 따른 출력값을 정해줄때 if문을 많이들 쓰십니다.  
현업에서 많이 쓰이는 다른 방식으로는 삼항 연산자가 있습니다.  
물론 복잡한 조건문은 삼항연산자로 표현하는 것은 오히려 헷갈릴수가 있으니 비추천입니다.  
하지만 간단한 조건문은 삼항연산자로 표현하면 더 빠르면서 가독성이 올라갑니다.  
```
#include <stdio.h>

int main(int argc, const char * argv[]) {
    int a = 5, b = 7, c = 10;

    int result1 = (a>b) ? 1 : 0;
    int result2 = (b>c) ? 1 : 0;
    int result3 = (c>a) ? 1 : 0;

    printf("%d %d %d",result1,result2,result3);
}

결과 : 0 0 1
```
위에서 (a>b) ? 1 : 0 이것이 삼항연산자 입니다.  
괄호안에 있는 것이 조건입니다.  
조건이 참이면 :기준으로 앞의 값을 출력하고 거짓이라면 뒤의 값을 출력합니다.  
참 읽기 편하고 코드도 간편하죠?  
하지만 조건이 복잡하다면 삼항연산자를 쓰면 가독성이 매우 떨어지게 됩니다.  
복잡한 조건에서는 if문을 쓰도록 합시다!
