---
layout: post
comments: true
title: C언어-short끼리 더하면 int형이 되는 이유
categories: [c]
tags: [c, programming-language]
fullview: true
---
```
#include <stdio.h>

int main(int argc, const char * argv[]) {
    short a=5, b=7;

    printf("%d %d %d", sizeof(a), sizeof(b), sizeof(a+b));
    return 0;
}
```
위의 코드를 살펴보면 short형은 분명 size가 2인데 short형끼리 더해준다고 해도 그대로 short형이 유지되어야 할 것 같지만 a+b를 해보면 자동으로 int형으로 변환되어 sizeof를 해보면 4가 출력됩니다.  
그 이유는 int형이 연산속도가 빠르기 때문에 CPU가 처리하기에 가장 적합한 int형으로 변환하는 것입니다.  
따라서 연산처리가 빈번한 경우 int형 변수를 선언하는 것이 좋습니다.  
그럼 short형이나 char형은 불필요한 것일까요?  
아닙니다.  
데이터의 양이 많아서 연산속도보다 데이터의 크기를 줄이는 것이 더 중요한 데이터들이 있습니다.  
예를들자면 MP3나 영상데이터 같은 경우입니다.  

# 형변환이 이렇게 묵시적으로만 일어나나요?
아닙니다.  
저희가 명시적으로 선언해줄 수도 있습니다.  
```
f2 = (float) 3/2;
```
이런식으로 3과 2는 정수형이지만 앞에 (float)를 붙여준다면 float형으로 형변환이 일어납니다.  
이것을 '명시적 형변환'이라고 합니다.
