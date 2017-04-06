---
layout: post
comments: true
title: C언어 함수포인터 사용방법
categories: [c]
tags: [c, programming-language]
fullview: true
---
함수를 포인터로 표현하려면 어떻게 해야할까요?  
아니, 그 전에 함수포인터란 무엇일까요?  
특정 변수에 대한 메모리 주소를 담을 수 있는 변수를 포인터변수라고 합니다.  
함수포인터란, 특정 함수에 대한 메모리 주소를 담을 수 있는 것입니다.  
아래의 코드를 살펴보겠습니다.
```
#include <stdio.h>

void SimpleAdder(int n1, int n2){
    printf("%d + %d = %d \n", n1, n2, n1+n2);
}

void ShowString(char * str){
    printf("%s \n", str);
}

int main(int argc, const char * argv[]) {

    char * str = "hi I'm impressprogram";
    int num1 = 10, num2 = 20;

    void (*fptr1)(int,int) = SimpleAdder;
    void (*fptr2)(char *) = ShowString;

    fptr1(num1,num2);
    fptr2(str);

    return 0;

}
```

위의 예시에서 SimpleAdder나 ShowString과 같은 함수의 주소값만을 담을 수 있는 것을 볼 수 있습니다.

```
void (*fptr1)(int,int) = SimpleAdder; 부분으로 설명해 보겠습니다.  
```

우선 앞부분에는 반환형이 들어갑니다.  
그리고 포인터가 들어가고 그 다음에 인자의 형태만 써주시면 됩니다.  
기존의 함수 원형과 등호표시를 해주시면 해당 함수에 대한 메모리 주소를 받을 수 있습니다.  
위의 코드의 결과값은 아래와 같습니다.  

```
10 + 20 = 30
hi I'm impressprogram
```
