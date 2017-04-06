---
layout: post
comments: true
title: 스택 중위, 전위, 후위 표기법(c)설명 + 코드
categories: [datastructure]
tags: [c, datastructure]
fullview: true
---

# 표기법에 대한 설명

중위 표기법은 우선 우리가 흔히 쓰는 표기법입니다.  
우리가 보기에는 중위 표기법이 편하지만 연산의 순서에 대한 정보가 담겨있지 않다는 단점이 있습니다.  
반면에 전위 표기법이나 후위 표기법은 연산자의 배치순서에 따라서 연산순서가 결정되기 때문에, 이 두 표기법의 수식을 계산하기 위해서 연산자의 우선순위를 알 필요가 없고 소괄호도 삽입되지 않으니 소괄호의 처리도 불필요해집니다.  
말로만들어도 컴퓨터가 처리하기 좋은 표기법이겠지요?  
그래서 중위표기법으로 사용자로부터 입력을 받고 이를 전위 표기법이나 후위 표기법으로 변환하여 컴퓨터로 처리하곤 합니다.

```
(1+2)*3 -> 중위 표기법
*+123 -> 전위 표기법
12+3* -> 후위 표기법
```

# 중위 표기법을 후위 표기법으로 바꾸는 방법(소괄호고려x)

1. 중위 표기법의 수식을 후위 표기법의 수식으로 바꿉니다.
2. 후위 표기법으로 바뀐 수식을 계산하여 그 결과를 얻습니다.

이 두 가지를 해보겠습니다.  
스택에 대한 개념이 없다면 이전 포스팅을 봐주세요!

변환전 수식 / 쟁반 / 변환된 수식이 있습니다.
쟁반은 연산자를 담으며 담겨진 연산자에 비해 담길 연산자의 우선순위가 높다면 쟁반에 위치한 연산자를 꺼내서 수식이 위치할 자리로 옮기고 새 연산자를 쟁반으로 옮깁니다.  
그렇지 않고 쟁반에 위치한 연산자의 우선순위가 낮다면

```
1+2*3(변환전 수식) / 쟁반 / 변환된 수식
+2*3 / 쟁반 / 5(숫자가 담김)
2*3 / +(연산자가 담김) / 1
*3 / + / 12
3 / +* / 12
/ +* / 123

이제 쟁반에 있는 연산자를 담긴 순서의 역순으로 꺼내주시면 됩니다(스택의 개념).
/+/123*
//123*+
```

# 소괄호를 고려하여 중위 표기법 -> 후위 표기법으로 바꾸는 방법

보통 소괄호를 많이 쓰지요?  
이번에는 소괄호를 고려하여 중위 표기법을 후위 표기법으로 바꾸는 방법을 알아보도록 하겠습니다.  
방법은 간단합니다.  
앞서 했던 것에서 여는 소괄호도 연산자로 인지하면 됩니다.  
그리고 닫는 소괄호는 전혀 고려할 필요가 없습니다.  
아래와 같은 순서로 진행됩니다.

```
(1+2*3)/4 . 쟁반 . 변환된 수식
1+2*3)/4 . ( . 변환된 수식
+2*3)/4 . ( . 1
2*3)/4 . (+ . 1
*3)/4 . (+ . 12
3)/4 . (+* . 12
)/4 . (+* . 123      )닫는 소괄호 연산자는 의미가 없으므로 그냥 없앰
4 . / . 123*+       /가 ()보다 우선순위가 낮으므로 *+을 내보내고 접시에 담김
. / . 123*+4
. . 123*+4/
```

# 어떻게 구현하고 어떻게 계산하나요?

아래의 코드를 보시면 이해가 가실 것입니다.  

**헤더파일**

```
#ifndef __IN_FIX_H__
#define __IN_FIX_H__

// 스택부분(링크드리스트로)
#define TRUE	1
#define FALSE	0

typedef int Data;

typedef struct _node
{
    Data data;
    struct _node * next;
} Node;

typedef struct _listStack
{
    Node * head;
} ListStack;


typedef ListStack Stack;

void StackInit(Stack * pstack);
int SIsEmpty(Stack * pstack);

void SPush(Stack * pstack, Data data);
Data SPop(Stack * pstack);
Data SPeek(Stack * pstack);

//중위 계산법에서 후위 계산법으로 바꾸는 부분
void ConvertToPostFix(char exp[]);

// 계산부분
int EvalRPNExp(char exp[]);

#endif
```

**함수부구현 파일**

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include "infix.h"

void StackInit(Stack * pstack)
{
    pstack->head = NULL;
}

int SIsEmpty(Stack * pstack)
{
    if(pstack->head == NULL)
        return TRUE;
    else
        return FALSE;
}

void SPush(Stack * pstack, Data data)
{
    Node * newNode = (Node*)malloc(sizeof(Node));

    newNode->data = data;
    newNode->next = pstack->head;

    pstack->head = newNode;
}

Data SPop(Stack * pstack)
{
    Data rdata;
    Node * rnode;

    if(SIsEmpty(pstack))
    {
        printf("Stack Memory Error!");
        exit(-1);
    }

    rdata = pstack->head->data;
    rnode = pstack->head;

    pstack->head = pstack->head->next;
    free(rnode);

    return rdata;
}

Data SPeek(Stack * pstack)
{
    if(SIsEmpty(pstack))
    {
        printf("Stack Memory Error!");
        exit(-1);
    }

    return pstack->head->data;
}

int EvalRPNExp(char exp[])
{
    Stack stack;
    int expLen = strlen(exp);
    int i;
    char tok, op1, op2;

    StackInit(&stack);

    for(i=0; i<expLen; i++)
    {
        tok = exp[i];

        if(isdigit(tok))
        {
            SPush(&stack, tok - '0');     // 숫자로 변환하여 PUSH!
        }
        else
        {
            op2 = SPop(&stack);     // 먼저 꺼낸 값이 두 번째 피연산자!
            op1 = SPop(&stack);

            switch(tok)
            {
                case '+':
                    SPush(&stack, op1+op2);
                    break;
                case '-':
                    SPush(&stack, op1-op2);
                    break;
                case '*':
                    SPush(&stack, op1*op2);
                    break;
                case '/':
                    SPush(&stack, op1/op2);
                    break;
            }
        }
    }
    return SPop(&stack);
}

int GetOpPrec(char op)
{
    switch(op)
    {
        case '*':
        case '/':
            return 5;
        case '+':
        case '-':
            return 3;
        case '(':
            return 1;
    }

    return -1;   // 등록되지 않은 연산자
}

int WhoPrecOp(char op1, char op2)
{
    int op1Prec = GetOpPrec(op1);
    int op2Prec = GetOpPrec(op2);

    if(op1Prec > op2Prec)
        return 1;
    else if(op1Prec < op2Prec)
        return -1;
    else
        return 0;
}

void ConvertToPostFix(char exp[])
{
    Stack stack;
    int expLen = strlen(exp);
    char * convExp = (char*)malloc(expLen+1);

    int i, idx=0;
    char tok, popOp;

    memset(convExp, 0, sizeof(char)*expLen+1);
    StackInit(&stack);

    for(i=0; i<expLen; i++)
    {
        tok = exp[i];
        if(isdigit(tok))
        {
            convExp[idx++] = tok;
        }
        else
        {
            switch(tok)
            {
                case '(':
                    SPush(&stack, tok);
                    break;

                case ')':
                    while(1)
                    {
                        popOp = SPop(&stack);
                        if(popOp == '(')
                            break;
                        convExp[idx++] = popOp;
                    }
                    break;

                case '+': case '-':
                case '*': case '/':
                    while(!SIsEmpty(&stack) &&
                          WhoPrecOp(SPeek(&stack), tok) >= 0)
                        convExp[idx++] = SPop(&stack);

                    SPush(&stack, tok);
                    break;
            }
        }
    }

    while(!SIsEmpty(&stack))
        convExp[idx++] = SPop(&stack);

    strcpy(exp, convExp);
    free(convExp);
}
```

**main.c**

```
#include <stdio.h>
#include "infix.h"

int main(void)
{
    char cal[50];

    printf("Enter Data : ");
    scanf("%50s", cal);
    printf("Echo data (infix form) : %s \n", cal);
    ConvertToPostFix(cal);
    printf("Conversion (postfix form) : %s \n", cal);
    printf("Result : %d \n",EvalRPNExp(cal));

    return 0;
}
```
