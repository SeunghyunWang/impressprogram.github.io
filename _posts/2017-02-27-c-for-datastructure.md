---
layout: post
comments: true
title: 자료구조를 위한 C언어의 기본
categories: [datastructure]
tags: [c, datastructure]
fullview: true
---

# 메모리와 포인터

컴퓨터의 메모리는 데이터를 보관하는 장소입니다.
바이트(8 bits) 단위로 주소가 지정됩니다.
모든 변수는 주소를 가지게 됩니다.

포인터는 메모리 주소를 값으로 가지는 변수입니다.
Type-name * variable-name 꼴로 선언됩니다.

```
int * ii; ( int 형의 데이터의 주소를 가리키는 포인터변수 ii)
int a = 1;
ii = &a;(포인터 변수 ii에 변수 a의 주소를 저장한다)

*기호 선언할때 쓰면 -> 포인터변수를 나타내는 기호
일반적인 문장에서 포인터 변수앞에 등장하면 -> 포인터 변수가 저장한 주소를 의미
```

포인터와 배열은 매우 긴밀히 연결되어 있습니다.
int a[10]의 경우 배열의 이름인 a는 배열의 시작 주소를 저장하는 포인터 변수입니다(단 그 값을 변경할 수 없음).
아래의 예제에서 어떤식으로 함수에서 배열인자를 넘겨받을 수 있는지 알려줍니다.

```
#include <stdio.h>

int calculate_sum(int * array);

int main(){
    int sum, i, average;
    int num[10];

    for(i=0; i<10; i++)
        scanf("%d", &num[i]);

    sum = calculate_sum(num);

    average=sum/10;

    printf("%d\n", average);
    return 0;
}

int calculate_sum(int array[]){

    int sum, i;
    sum = 0;
    for(i=0; i<10; i++){
        sum += array[i];
    }

    return sum;

}
int array[] 대신 int * array도 가능
```

또한 배열의 값은 포인터로 나타낼 수 있습니다.


a[1]은 *(a+1)과 동일
a가 1000번지라고 치면 a+1은 a의 자료형에 따라서 주소값이 더해짐 예를들면 int형은 4바이트이므로 a+1이 1004번지를 가리키게 됨
```
#include <stdio.h>

int main(int argc, const char * argv[]) {
    int data[] = {1, 2, 3};
    char data2[] = {'a','b','c'};
    long long data3[] = {1, 2, 3};

    int *p = &data[0];
    char *q = &data2[0];
    long long *r = &data3[0];

    printf("%d %x\n",p, p);
    printf("%d %x\n",p+1, p+1);
    printf("%d %x\n",p+2, p+2);

    printf("%d %x\n",q, q);
    printf("%d %x\n",q+1, q+1);
    printf("%d %x\n",q+2, q+2);

    printf("%d %x\n",r, r);
    printf("%d %x\n",r+1, r+1);
}


결과:
1606416220 5fbff75c
1606416224 5fbff760
1606416228 5fbff764
1606416173 5fbff72d
1606416174 5fbff72e
1606416175 5fbff72f
1606416192 5fbff740
1606416200 5fbff748
```
