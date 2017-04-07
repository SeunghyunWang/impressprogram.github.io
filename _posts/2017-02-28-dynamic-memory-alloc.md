---
layout: post
comments: true
title: 동적 메모리할당(c언어)
categories: [datastructure]
tags: [c, datastructure]
fullview: true
---

![mem_alloc](https://impressprogram.github.io/assets/media/mem_alloc.png)

동적메모리 할당이라는 것은 변수를 선언하는 대신 프로그램의 요청으로 메모리를 할당 할 수 있도록 하는 것입니다.  
malloc함수를 호출하여 동적메모리 할당을 요청하면 요구하는 크기의 메모리를 할당하고 그 시작 주소를 반환합니다.  
동적으로 할당된 배열은 공간이 부족할 경우 더 큰 배열을 할당하여 사용할 수 있습니다.  


```
//garbage 관련해서는 나중에 다루겠습니다~! 우선그냥 봐주세요
#include <stdio.h>
#include <stdlib.h>

int main(){
int *array=(int*)malloc(4*sizeof(int));
int *tmp=(int*)malloc(8*sizeof(int));
int i;

array[0]=1;
array[1]=2;
*(array+2)=3;

for(i=0;i<4;i++)
tmp[i]=array[i];

array=tmp;

for(i=0;i<8;i++)
printf("%d\n",array[i]);


return0;
}
결과값:
1 
2 
3 
268435456 
-978190321 
32767 
-1136037739 
32767 

```

위의 코드를 보시면 원래 array는 int형의 데이터를 4개까지만 저장할 수 있었습니다.  
하지만 tmp를 별도로 더 크게 선언해서 array의 데이터를 tmp에 넣어주면 보시다시피 8개의 값을 저장할 수 있습니다(선언을 안해준 부분들은 쓰레기값이 들어갑니다).  
위의 코드는 물론 메모리 해제를 안해주었기 때문에 오류가 있지만 나중에 다룰 내용이므로 넘어가겠습니다.  
그리고 사진에서 40 이런식으로 malloc에 값을 준 대신 4*sizeof(int), 8*sizeof(int) 이런식으로 값을 주었습니다.  
위처럼 쓰는 이유는 코드의 호환성때문입니다.  
4*(sizeof(int))는 16과 같은 것 같지만 int가 무조건 4바이트라는 보장은 없습니다.  
플랫폼의 환경에 따라서 2바이트나 8바이트가 될 수도 있습니다.  
이럴 경우에는 의도한 코드와 다르게 동작하겠지요.  
코드의 호환성을 위해서 위와 같은 표기법을 씁시다.

```
int main(){
int array[4];
int *tmp=(int*)malloc(8*sizeof(int));
int i;

array[0]=1;
array[1]=2;
*(array+2)=3;

for(i=0; i<4; i++)
tmp[i] = array[i];

array = tmp;

for(i=0; i<8; i++)
printf("%d\n",array[i]);


return 0;
}
결과 : 오류
```

위의 예제가 오류인 이유는 array 배열의 주소값을 변경하려고 했기 때문입니다.  
int array[4] 꼴로 선언된 배열에서 물론 array가 원소들의 첫 값을 가리킨다는 점에서는 맨 처음의 예제와 의미가 같지만 그 값을 변경할 수 없다는 차이점이 있습니다.