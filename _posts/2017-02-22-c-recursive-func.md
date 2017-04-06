---
layout: post
comments: true
title: 재귀함수란 무엇일까?(C언어)
categories: [datastructure]
tags: [c, datastructure]
fullview: true
---

# 재귀함수의 소개

재귀함수란 함수 내에서 자기 자신을 다시 호출하는 함수를 의미합니다.  
물론 실제 재귀함수가 쓰이는 곳에서는 매 호출시마다 입력값(파라메터)이 변합니다.  
입력값의 변화가 없거나 입력값의 변화가 특정 패턴을 반복하게 되면 그 재귀함수는 영원히 반복되다가 콜스택 초과로 프로그램이 뻗어 버리니 주의해야 합니다.  
따라서 재귀함수 설계시에는 입력값이 종료 조건으로 수렴하는지를 꼭 검증해야 합니다.  
즉, 자기 자신을 계속 호출하다가 일정 조건이 되면 빠져나가야 한다는 것입니다.  
그렇다면 왜 이런 재귀함수를 쓰는 것일까요?  
일정 규칙이 있는 기능을 쉽게 구현할 수 있기 때문인데요!  
아래에서 팩토리얼 예제를 예시로 한번 설명해 보겠습니다.  
팩토리얼의 수식은 n! = nx(n-1)x(n-2)x....x2x1 이런 꼴입니다.  
이것을 구현할 때에 아래와 같이 재귀함수를 이용한다면 쉽게 구현할 수 있습니다.

```
#include <stdio.h>
int factorial(int n);
int factorial(int n)
{
   if(n<=1) return 1;
   return n*factorial(n-1);
}
int main()
{
   int n;
   scanf("%d",&n);
   printf("%d",factorial(n));
   return 0;
}
```
