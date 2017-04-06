---
layout: post
comments: true
title: C언어 파일분할 이야기(static, extern 등)
categories: [c]
tags: [c, programming-language]
fullview: true
---
파일분할은 여러모로 유용합니다.  
너무 한개의 파일이 길어진다면 나누어 주어서 각각의 파일에, 용도 및 특성 별로 함수와 변수를 나눠서 저장하면 소스코드의 관리가 용이해집니다.  
또한 파일분할은 여러명이 대형 프로젝트를 진행할때도 많이 쓰입니다.  
다음의 파일을 보겠습니다.
```
int num=0;
void Increment(void){
  num++;
}
int GetNum(void){
  return num;
}
int main(void){
  printf("num: %d \n", GetNum());
  Increment();
  printf("num: %d \n", GetNum());
}
```
이 파일을 나누어 보겠습니다.  
``` num.c
int num=0;
```
``` func.c
void Increment(void){
  num++;
}
int GetNum(void){
  return num;
}
```
``` main.c
int main(void){
  printf("num: %d \n", GetNum());
  Increment();
  printf("num: %d \n", GetNum());
}
```
이렇게 셋으로 나누고 컴파일을 하면 에러가 뜰 것입니다.  
그 이유는 컴파일러는 파일 단위로 컴파일을 진행하기 때문입니다.  
파일을 저런 형태로 분할하기 위해서는 컴파일러에게 extern 변수를 이용해서 외부에 정의되어 있음을 알려주어야합니다.  
``` num.c
int num=0;
```
``` func.c
extern int num;
void Increment(void){
  num++;
}
int GetNum(void){
  return num;
}
```
``` main.c
extern void Increment(void);
extern void GetNum(void);

int main(void){
  printf("num: %d \n", GetNum());
  Increment();
  printf("num: %d \n", GetNum());
}
```
또는 이와 반대로 다른 파일에서 접근을 못하게 하고싶다면 static변수를 이용하면 됩니다.  
num.c에 선언된 변수를 다음같이 바꿔주면 func.c에서 접근을 못해서 에러가 뜰 것입니다.
```
static int num=0;
```
참 그리고 분할 컴파일을 하는 방법도 간단합니다.
```
gcc -c num.c
gcc -c func.c
gcc -c main.c
gcc -o this num.o func.o main.o
./this
결과 :
num: 0
num: 1
```

헤더파일 또한 분할을 많이 합니다.  
헤더파일은 단순히 파일의 내용을 포함시키는 용도 입니다.  
"이 문장의 위치에 xxxx.h 파일의 내용을 가져다 놓으세요."하는 것입니다.  
주로 꼭 파일마다 있어야 하는 선언부분, 함수의 정의부분, 매크로, 구조체 등을 헤더파일에 놓습니다.  
하지만 헤더파일이 이렇게 나뉘다 보면 c파일마다 헤더파일이 각자 들어가기 때문에 중복문제가 일어날 수 있습니다.  
이를 해결하기 위해 조건부 컴파일을 활용합니다.  
```
#ifndef __STDiV2_H__
#define __STDiV2_H__

typedef struct div{
  int quotient;
  int remainder;
} Div;

#endif
```
저런식으로 활용하면 조건부로 저 헤더파일이 이미 있다면 컴파일을 안해서 헤더파일의 중복삽입을 막아줍니다(이해가 안가시면 이전 포스팅 마지막 부분 참조!).
