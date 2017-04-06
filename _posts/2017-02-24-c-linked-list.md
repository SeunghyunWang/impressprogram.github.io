---
layout: post
comments: true
title: 연결 리스트(링크드 리스트)에 대하여(c)
categories: [datastructure]
tags: [c, datastructure]
fullview: true
---

# 링크드 리스트란?

앞서 살펴본 배열 리스트와 한번 비교해보도록 하겠습니다.  
배열 리스트는 자료가 하나 삭제되면 그 자료 뒤의 자료들이 한 칸씩 위치를 당겨야 해서 데이터의 복사가 빈번하게 일어난다는 단점이 있었습니다.  
하지만 데이터가 생성되고 다른 데이터가 추가된다면 이전 데이터가 다음 데이터를 가리키는 식으로 리스트를 짠다면 어떨까요?  
데이터가 삭제되더라도 가리키는 포인터부분만 수정해주면 되므로 파일의 복사가 빈번하게 일어나지 않을 것입니다.  
![simplelinkedlist](https://impressprogram.github.io/assets/media/linkedlist.png)  

자, 위의 그림에서 어느 한 곳이 빠지더라도 화살표부분만 수정해준다면 되겠죠?  
혹은 하나를 중간에 추가하더라도 데이터를 가리키는 부분만 수정해주면 될 것입니다.  
저런 형태로 구성된 리스트를 링크드 리스트(연결 리스트) 라고 부릅니다.  

# 단순 연결 리스트 소스

열혈 자료구조에서 윤성우님의 책에서 발췌한 소스코드입니다.  

**헤더파일**

```
#ifndef __D_LINKED_LIST_H__
#define __D_LINKED_LIST_H__

#define TRUE	1
#define FALSE	0

typedef int LData;

typedef struct _node
{
	LData data;
	struct _node * next;
} Node;

typedef struct _linkedList
{
	Node * head; //더미 노드를 가리키는 멤버
	Node * cur; //참조 및 삭제를 돕는 멤버
	Node * before; //삭제를 돕는 멤버
	int numOfData; //저장된 데이터의 수를 기록하기 위한 멤버
	int (*comp)(LData d1, LData d2); //정렬의 기준을 등록하기 위한 멤버
} LinkedList;


typedef LinkedList List;

void ListInit(List * plist);
void LInsert(List * plist, LData data);

int LFirst(List * plist, LData * pdata);
int LNext(List * plist, LData * pdata);

LData LRemove(List * plist);
int LCount(List * plist);

void SetSortRule(List * plist, int (*comp)(LData d1, LData d2));

#endif
```

**함수 구현부 파일**

```
#include <stdio.h>
#include <stdlib.h>
#include "DLinkedList.h"

void ListInit(List * plist) // 초기화를 진행시켜줌
{
	plist->head = (Node*)malloc(sizeof(Node)); //더미 노드의 생성
	plist->head->next = NULL;
	plist->comp = NULL;
	plist->numOfData = 0;
}

void FInsert(List * plist, LData data) // comp가 NULL일 때 호출되는 함수
{
	Node * newNode = (Node*)malloc(sizeof(Node)); // 새 노드 생성
	newNode->data = data; // 새 노드에 데이터 저장

	newNode->next = plist->head->next; // 새 노드가 다른 노드를 가리키게 함
	plist->head->next = newNode; // 더미 노드가 새 노드를 가리키게 함

	(plist->numOfData)++; // 저장된 노드의 수를 하나 증가시킴
}

void SInsert(List * plist, LData data)
{
	Node * newNode = (Node*)malloc(sizeof(Node));
	Node * pred = plist->head;
	newNode->data = data;

	while(pred->next != NULL &&
		plist->comp(data, pred->next->data) != 0)
	{
		pred = pred->next;
	}

	newNode->next = pred->next;
	pred->next = newNode;

	(plist->numOfData)++;
}


void LInsert(List * plist, LData data)
{
	if(plist->comp == NULL) //정렬기준이 마련되지 않았다면
		FInsert(plist, data); //머리에 노드를 추가!
	else //정렬기준이 마련되었다면
		SInsert(plist, data); //정렬기준에 근거하여 노드를 추가!
}

int LFirst(List * plist, LData * pdata)
{
	if(plist->head->next == NULL) // 더미 노드가 NULL을 가리킨다면,
		return FALSE; // FALSE를 반환한다.

	plist->before = plist->head; // before는 더미 노드를 가리키게 한다.
	plist->cur = plist->head->next; // cur은 첫 번째 노드를 가리키게 한다.

	*pdata = plist->cur->data; // 첫 번째 노드의 데이터를 전달한다.
	return TRUE; // 데이터 반환 성공!
}

int LNext(List * plist, LData * pdata)
{
	if(plist->cur->next == NULL) // 더미 노드가 NULL을 가리킨다면
		return FALSE; // 반환할 데이터가 없다!

	plist->before = plist->cur; // cur이 카리키던 것을 before이 가리킴
	plist->cur = plist->cur->next; // cur은 그 다음 노드를 가리킴

	*pdata = plist->cur->data; // cur이 가리키는 노드의 데이터 전달
	return TRUE; // 데이터 반환 성공
}

LData LRemove(List * plist)
{
	Node * rpos = plist->cur; // 소멸 대상의 주소 값을 rpos에 저장
	LData rdata = rpos->data; // 소멸 대상의 데이터를 rdata에 저장

	plist->before->next = plist->cur->next; // 소멸 대상을 리스트에서 제거
	plist->cur = plist->before; // cur이 가리키는 위치를 재조정!

	free(rpos); // 리스트에서 제거된 노드 소멸
	(plist->numOfData)--; // 저장된 데이터의 수 하나 감소
	return rdata; // 제거된 노드의 데이터 반환
}

int LCount(List * plist)
{
	return plist->numOfData;
}

void SetSortRule(List * plist, int (*comp)(LData d1, LData d2))
{
	plist->comp = comp;
}
```

참고로 위에서 head가 더미파일을 가리키게 한 이유는 더미가 없다면 노드를 추가, 삭제, 조회하는 방법에 있어서 첫 번째 노드와 두 번째 이후의 노드에 차이가 있게 됩니다.  
더미를 넣어둔다면 처음 추가되는 노드가 구조상 두 번째 노드가 되므로 그 과정을 일관된 형태로 구현할 수 있습니다.  
쉽게 이야기 하자면 head가 첫 데이터를 가리켜야 하는 특수성으로 코드를 head와 그렇지 않은 노드를 구분해서 짜야 하는데 더미파일을 삽입한다면 일관되게 코드를 짤 수 있다는 말입니다.

**메인 파일**

```
#include <stdio.h>
#include "DLinkedList.h"

int WhoIsPrecede(int d1, int d2)
{
	if(d1 < d2)
		return 0;    // d1이 정렬 순서상 앞선다.
	else
		return 1;    // d2가 정렬 순서상 앞서거나 같다.
}

int main(void)
{
	// List의 생성 및 초기화  ////////////
	List list;
	int data;
	ListInit(&list);

	SetSortRule(&list, WhoIsPrecede); // 정렬의 기준을 등록한다.

	// 5개의 데이터 저장  ///////////////
	LInsert(&list, 11);  LInsert(&list, 11);
	LInsert(&list, 22);  LInsert(&list, 22);
	LInsert(&list, 33);

	// 저장된 데이터의 전체 출력 ////////////
	printf("현재 데이터의 수: %d \n", LCount(&list));

	if(LFirst(&list, &data))
	{
		printf("%d ", data);

		while(LNext(&list, &data))
			printf("%d ", data);
	}
	printf("\n\n");

	// 숫자 22을 검색하여 모두 삭제 ////////////
	if(LFirst(&list, &data))
	{
		if(data == 22)
			LRemove(&list);

		while(LNext(&list, &data))
		{
			if(data == 22)
				LRemove(&list);
		}
	}

	// 삭제 후 저장된 데이터 전체 출력 ////////////
	printf("현재 데이터의 수: %d \n", LCount(&list));

	if(LFirst(&list, &data))
	{
		printf("%d ", data);

		while(LNext(&list, &data))
			printf("%d ", data);
	}
	printf("\n\n");
	return 0;
}
```

# 원형 연결 리스트

![circleLinkedList](https://upload.wikimedia.org/wikipedia/commons/thumb/9/98/Circurlar_linked_list.png/800px-Circurlar_linked_list.png)  
원형 연결 리스트에서는 마지막 노드가 첫 번째 노드를 가리켜서, 연결의 형태가 원을 이루는 형태가 나오게됩니다.  
앞서 본 단순연결 리스트와 다른점이라면 tail이 head 노릇을 겸할수 있다는 것입니다.  
원래 맨 앞의 노드가 head이고 맨 뒤의 노드가 tail이 되지만 원형 연결 리스트에서는 tail->next가 head가 되기 때문에 head가 따로 필요 없는 것입니다.  

윤성우의 열혈 자료구조 책에 있는 원형 연결 리스트 코드를 살펴보겠습니다.  

**헤더파일**

```
#ifndef __C_LINKED_LIST_H__
#define __C_LINKED_LIST_H__

#define TRUE	1
#define FALSE	0

typedef int Data;

typedef struct _node
{
	Data data;
	struct _node * next;
} Node;

typedef struct _CLL
{
	Node * tail;
	Node * cur;
	Node * before;
	int numOfData;
} CList;


typedef CList List;

void ListInit(List * plist);
void LInsert(List * plist, Data data); // 꼬리에 노드를 추가
void LInsertFront(List * plist, Data data); // 머리에 노드를 추가

int LFirst(List * plist, Data * pdata);
int LNext(List * plist, Data * pdata);
Data LRemove(List * plist);
int LCount(List * plist);

#endif
```

**함수 구현 부분**

```
#include <stdio.h>
#include <stdlib.h>
#include "CLinkedList.h"

void ListInit(List * plist) // 초기화
{
	plist->tail = NULL;
	plist->cur = NULL;
	plist->before = NULL;
	plist->numOfData = 0;
}

void LInsertFront(List * plist, Data data)
{
	Node * newNode = (Node*)malloc(sizeof(Node)); // 새 노드 생성
	newNode->data = data; // 새 노드에 데이터 저장

	if(plist->tail == NULL) // 첫 번째 노드라면,
	{
		plist->tail = newNode; // tail이 새 노드를 가리키게 함
		newNode->next = newNode; // 새 노드 자신을 가리키게 함
	}
	else
	{
		newNode->next = plist->tail->next; // 새로운 node의 next가 tail->next 즉 head를 가리키게 함
		plist->tail->next = newNode; // 새로운 node를 tail->next가 가리킴. 새로운 node가 head가 됨
	}

	(plist->numOfData)++;
}

void LInsert(List * plist, Data data) // LInsertFront함수와 비슷하지만 tail이 가리키는 노드를 newNode로 한다는 것이 유일한 차이점입니다.
{
	Node * newNode = (Node*)malloc(sizeof(Node));
	newNode->data = data;

	if(plist->tail == NULL)
	{
		plist->tail = newNode;
		newNode->next = newNode;
	}
	else
	{
		newNode->next = plist->tail->next;
		plist->tail->next = newNode;
		plist->tail = newNode;
	}

	(plist->numOfData)++;
}

int LFirst(List * plist, Data * pdata)
{
	if(plist->tail == NULL)    // 저장된 노드가 없다면
		return FALSE;

	plist->before = plist->tail; //before가 꼬리를 가리키게 한다.
	plist->cur = plist->tail->next; // cur이 머리를 가리키게 한다.

	*pdata = plist->cur->data; // cur이 가리키는 노드의 데이터 반환
	return TRUE;
}

int LNext(List * plist, Data * pdata)
{
	if(plist->tail == NULL)    // 저장된 노드가 없다면
		return FALSE;

	plist->before = plist->cur; // before가 다음 노드를 가리키게 한다.
	plist->cur = plist->cur->next; // cur이 다음 노드를 가리키게 한다.

	*pdata = plist->cur->data; // cur이 가리키는 노드의 데이터 반환
	return TRUE;
}

Data LRemove(List * plist)
{
	Node * rpos = plist->cur;
	Data rdata = rpos->data;

	if(rpos == plist->tail)    // 삭제 대상을 tail이 가리킨다면
	{
		if(plist->tail == plist->tail->next)    // 그리고 마지막 남은 노드라면
			plist->tail = NULL;
		else
			plist->tail = plist->before;
	}

	plist->before->next = plist->cur->next;
	plist->cur = plist->before;

	free(rpos);
	(plist->numOfData)--;
	return rdata;
}

int LCount(List * plist)
{
	return plist->numOfData;
}
```

**메인.c**

```
#include <stdio.h>
#include "CLinkedList.h"

int main(void)
{
	// 원형 연결 리스트의 생성 및 초기화 ///////
	List list;
	int data, i, nodeNum;
	ListInit(&list);

	// 리스트에 5개의 데이터를 저장 ///////
	LInsert(&list, 3);
	LInsert(&list, 4);
	LInsert(&list, 5);
	LInsertFront(&list, 2);
	LInsertFront(&list, 1);

	// 리스트에 저장된 데이터를 연속 3회 출력 ///////
	if(LFirst(&list, &data))
	{
		printf("%d ", data);

		for(i=0; i<LCount(&list)*3-1; i++)
		{
			if(LNext(&list, &data))
				printf("%d ", data);
		}
	}
	printf("\n");

	// 2의 배수를 찾아서 모두 삭제 ///////
	nodeNum = LCount(&list);

	if(nodeNum != 0)
	{
		LFirst(&list, &data);
		if(data%2 == 0)
			LRemove(&list);

		for(i=0; i < nodeNum-1; i++)
		{
			LNext(&list, &data);
			if(data%2 == 0)
				LRemove(&list);
		}
	}

	// 전체 데이터 1회 출력 ///////
	if(LFirst(&list, &data))
	{
		printf("%d ", data);

		for(i=0; i<LCount(&list)-1; i++)
		{
			if(LNext(&list, &data))
				printf("%d ", data);
		}
	}
	return 0;
}
```

# 양방향 연결 리스트

양방향 연결 리스트 또는 이중 연결 리스트라고도 불리는 이 자료구조는 이름 그대로 양쪽 방향으로 연결된 구조의 리스트입니다.  
왼쪽 노드가 오른쪽 노드를 가리키고 오른쪽 노드도 왼쪽 노드를 가리키는 구조입니다.  
![양방향연결리스트](https://upload.wikimedia.org/wikipedia/commons/thumb/c/ca/Doubly_linked_list.png/800px-Doubly_linked_list.png)  
복잡할 것 같지만 단방향에 비해 before 같은 포인터 변수가 불필요해 지고 구현부의 코드도 오히려 간단합니다.  
물론 노드를 양방향으로 연결하기 위해서는 코드가 약간 더 추가되지만 구현하기 쉽습니다.  
코드를 예로 띄워두겠습니다.

**헤더파일**

```
#ifndef __DB_LINKED_LIST_H__
#define __DB_LINKED_LIST_H__

#define TRUE	1
#define FALSE	0

typedef int Data;

typedef struct _node
{
    Data data;
    struct _node * next;
    struct _node * prev;
} Node;

typedef struct _dbLinkedList
{
    Node * head;
    Node * cur;
    Node * tail;
    int numOfData;
} DBLinkedList;

typedef DBLinkedList List;

void ListInit(List * plist);
void LInsert(List * plist, Data data);

int LFirst(List * plist, Data * pdata);
int LNext(List * plist, Data * pdata);
int LPrevious(List * plist, Data * pdata);
Data LRemove(List * plist);
int LCount(List * plist);

#endif
```

**함수 구현부 파일**

```
#include <stdio.h>
#include <stdlib.h>
#include "CLinkedList.h"

void ListInit(List * plist)
{
    plist->head = (Node*)malloc(sizeof(Node));
    plist->tail = (Node*)malloc(sizeof(Node));

    plist->head->prev = NULL;
    plist->head->next = plist->tail;

    plist->tail->prev = plist->head;
    plist->tail->next = NULL;

    plist->numOfData = 0;
}

void LInsert(List * plist, Data data)
{
    Node * newNode = (Node*)malloc(sizeof(Node));
    newNode->data = data;

    newNode->prev = plist->tail->prev;
    plist->tail->prev->next = newNode;

    newNode->next = plist->tail;
    plist->tail->prev = newNode;

    (plist->numOfData)++;
}

int LFirst(List * plist, Data * pdata)
{
    if(plist->head == NULL)
        return FALSE;

    plist->cur = plist->head;
    *pdata = plist->cur->data;

    return TRUE;
}

int LNext(List * plist, Data * pdata)
{
    if(plist->cur->next == NULL)
        return FALSE;

    plist->cur = plist->cur->next;
    *pdata = plist->cur->data;

    return TRUE;
}

int LPrevious(List * plist, Data * pdata)
{
    if(plist->cur->prev == NULL)
        return FALSE;

    plist->cur = plist->cur->prev;
    *pdata = plist->cur->data;

    return TRUE;
}

Data LRemove(List * plist){
    Node * rpos = plist->cur;
    Data remv = rpos->data;

    if(rpos->prev==NULL || rpos->next==NULL)
        return -1;

    plist->cur->prev->next = plist->cur->next;
    plist->cur->next->prev = plist->cur->prev;

    plist->cur = plist->cur->prev;

    free(rpos);
    (plist->numOfData)--;
    return remv;
}

int LCount(List * plist)
{
    return plist->numOfData;
}
```

**메인.c**

```
#include <stdio.h>
#include "CLinkedList.h"

int main(void)
{
    // 양방향 연결 리스트의 생성 및 초기화  ///////
    List list;
    int data;
    ListInit(&list);

    // 8개의 데이터 저장  ///////
    LInsert(&list, 1);  LInsert(&list, 2);
    LInsert(&list, 3);  LInsert(&list, 4);
    LInsert(&list, 5);  LInsert(&list, 6);
    LInsert(&list, 7);  LInsert(&list, 8);

    // 저장된 데이터의 조회  ///////
    if(LFirst(&list, &data))
    {
        printf("%d ", data);

        while(LNext(&list, &data))
            printf("%d ", data);

        while(LPrevious(&list, &data))
            printf("%d ", data);

        printf("\n\n");
    }
    // 2의 배수 삭제
    if(LFirst(&list, &data)){
        if(data%2==0)
            LRemove(&list);

        while (LNext(&list, &data)) {
            if(data%2==0)
                LRemove(&list);
        }
    }
    // 저장된 데이터의 조회  ///////
    if(LFirst(&list, &data))
    {
        printf("%d ", data);

        while(LNext(&list, &data))
            printf("%d ", data);

        while(LPrevious(&list, &data))
            printf("%d ", data);

        printf("\n\n");
    }

    return 0;
}
```
