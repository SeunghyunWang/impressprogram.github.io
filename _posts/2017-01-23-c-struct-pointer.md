---
layout: post
comments: true
title: C언어 구조체를 포인터로 받고 사용하기
categories: [c]
tags: [c, programming-language]
fullview: true
---
아래의 코드로 학습해보겠습니다.
```
#include <stdio.h>

typedef struct point{
    int xpos;
    int ypos;
} Point;

void OrgSymTrans(Point * ptr){
    ptr -> xpos = (ptr->xpos)* -1;
    ptr -> ypos = (ptr->ypos)* -1;
}

void ShowPosition(Point pos){
    printf("[%d, %d] \n", pos.xpos, pos.ypos);
}

int main(int argc, const char * argv[]) {

    Point pos={7, -5};

    OrgSymTrans(&pos);

    ShowPosition(pos);

    return 0;
}

```
주어진 점을 원점대칭 이동을 시킨 뒤에 보여주는 코드입니다.  
포인터로 받는 것은 같고 포인터로 받은 구조체 안의 변수들에 접근할 때에 ptr -> xpos 꼴로 쉽게 접근할 수 있음을 알 수 있습니다.
