---
layout: post
comments: true
title: C언어 포인터 배열과 배열 포인터의 차이점
categories: [c]
tags: [c, programming-language]
fullview: true
---
많이들 헷갈리는 것입니다.
```
int * a[4]; //포인터 배열
int (*a)[4]; //배열 포인터
```
저 위의 두가지는 어떻게 다를까요?  

포인터 배열은 말 그대로 포인터를 담아두는 배열입니다.  
아래와 같이 사용하시면 됩니다.  
```
int * a[4] = {&num1, &num2, &num3, &num4};
```
주소값들을 저장하는 포인터 배열이 완성되었지요?  
배열 포인터는 아래와 같이 사용됩니다.
```
\는 빼고 읽어주세요.. \가 없으면 올라가질 않아서 어쩔수없이 넣었습니다.
#include <stdio.h>

int main(int argc, const char * argv[]) {

    int i = 0, j = 0;
    int arr2d[2][4] = \{\{1,2,3,4\},\{5,6,7,8\}\};

    int (*a)[4] = arr2d;
    for (i=0; i<2; i++) {
        for (j=0; j<4; j++) {
            printf("%d", a[i][j]);
        }
    }
}
```
위처럼 표현하게 되면 a의 포인터가 int형 자료형을 4개씩 건너뜁니다.  
따라서 위의 코드처럼 배열인자로 쓸 수 있게 됩니다.  
3차원의 배열도 한번 해보겠습니다.  
```
#include <stdio.h>

int main(int argc, const char * argv[]) {

    int arr[3][2][2] = {1,2,3,4,5,6,7,8,9,10,11,12};
    int (*a)[2][2] = arr;
    int i=0, j=0, k=0;

    for (i=0; i<3; i++) {
        for (j=0; j<2; j++) {
            for (k=0; k<2; k++) {
                printf("%d ",a[i][j][k]);
            }
        }
    }

}
결과 : 1 2 3 4 5 6 7 8 9 10 11 12
```
