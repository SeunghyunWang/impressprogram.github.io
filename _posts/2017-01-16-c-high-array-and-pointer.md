---
layout: post
comments: true
title: C언어 다차원 배열과 포인터의 관계
categories: [c]
tags: [c, programming-language]
fullview: true
---
# 2차원 배열에서 흔히 착각하는 것
int 형의 arr2d[2][2]이 있다고 가정해 보겠습니다.  
arr2d와 arr[0]은 같은 것일까요?  
코드를 통해 알아보겠습니다.
```
#include <stdio.h>

int main(int argc, const char * argv[]) {

    int arr2d[2][2] = {1,2,3,4};

    printf("%d, %d",arr2d,arr2d[0]);
}
결과값 : 1606416288, 1606416288
```
둘다 같은 값이 나옵니다.  
그렇다면 같은 것일까요?  
아래 코드를 하나 더 보겠습니다.  
```
#include <stdio.h>

int main(int argc, const char * argv[]) {

    int arr2d[2][2] = {1,2,3,4};

    printf("%d, %d",sizeof(arr2d),sizeof(arr2d[0]));
}
결과값 : 16, 8
```
자, 왜 사이즈가 다를까요?  
우리는 이 결과값으로 다음과 같은 결론을 낼 수 있습니다.  
arr2d는 첫 번째 요소를 가리키면서 배열 전체를 의미합니다.  
arr2d[0]은 첫 번째 요소를 가리키면서 배열의 1행만을 의미합니다.  

그리고 특징을 아래의 코드로 더 살펴보겠습니다.
```
#include <stdio.h>

int main(int argc, const char * argv[]) {

    int arr2d[2][2] = {1,2,3,4};

    printf("%d, %d, %d",arr2d, arr2d+1,arr2d[0]+1);
}
결과값 : 1606416288, 1606416296, 1606416292
```
자, 보시면 arr2d는 1을 더하면 가리키는 행이 바뀌고 arr2d[0]에는 1을 더하면 가리키는 열이 바뀝니다.
