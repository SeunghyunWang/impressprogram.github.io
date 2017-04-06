---
layout: post
comments: true
title: C언어 free이후에도 메모리에 남아있는 이유
categories: [c]
tags: [c, programming-language]
fullview: true
---

일반적으로 malloc/free를 할 때마다 커널을 불러서 메모리를 받아오고 반환을 해줍니다.  
이러한 연산이 지속적으로 반복된다면 시간 낭비이기 때문에, 일반적으로 generic memory allocator는 free한 메모리를 시스템에 직접 반환하지 않고 자체 pool에서 들고 있다가 다시 malloc 요청이 들어오면 적당히 잘라서 다시 돌려주는 방식을 채택하고 있습니다.

아래의 예시로 보충설명을 해보겠습니다.  

```
#include <stdio.h>
#include <stdlib.h>

typedef struct{
    int xpos;
    int ypos;
} Point;

void SetPointPos(Point * ppos, int xpos, int ypos){
    ppos->xpos = xpos;
    ppos->ypos = ypos;
}


int main(void)
{
    Point * ppos;

    ppos = (Point*)malloc(sizeof(Point));
    SetPointPos(ppos, 1, 2);

    free(ppos);

    printf("%d %d",ppos->xpos,ppos->ypos);

    return 0;
}
결과 : 1 2
```

free(ppos);로 메모리의 자원을 반환한 것처럼 보이지만 1 2가 출력되는 것을 볼 수 있습니다.  
그 이유는 앞서 설명했듯이 자체 pool에서 free한 메모리를 시스템에 아직 반환하지 않고 다시 malloc요청이 올 것을 대비하기 때문입니다.  
