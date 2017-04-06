---
layout: post
comments: true
title: 추상 자료형이란 무엇일까? -ADT with (배열)리스트
categories: [datastructure]
tags: [datastructure]
fullview: true
---

# 추상 자료형이란?

추상 자료형이란 구체적인 기능의 완성과정을 언급하지 않고, 순수하게 기능이 무엇인지를 나열한 것입니다.  
바꿔서 말하자면 자료들과 그 자료들에 대한 연산들을 명기한 것입니다.  
구현 방법을 명시하고 있지 않다는 점에서 자료 구조와 다릅니다.  
구체적으로 추상 자료형이 무엇인지 핸드폰을 예제로 예를들어서 쉽게 설명해보겠습니다.  

```
typedef struct {
  int power;
  int home;
  int leftButton;
  int rightButton;
} 핸드폰;

void PowerOn(핸드폰 * phone); // 핸드폰 켜주는 함수
void PowerOff(핸드폰 * phone)); // 해드폰 꺼주는 함수
void LeftButtonClick(핸드폰 * phone)); //왼쪽 버튼 클릭 반응 함수
void RightButtonClick(핸드폰 * phone)); //오른쪽 버튼 클릭 반응 함수

int main(){
  PowerOn(핸드폰);
  LeftButtonClick(핸드폰);
  RightButtonClick(핸드폰);
  PowerOff(핸드폰);
}
```

위에서 보면 핸드폰을 쓰는데 있어서 구조체 핸드폰의 멤버가 어떻게 되든지 알 필요가 없이 그냥 함수 4가지가 알아서 해줍니다.  
사실 사용자 입장에서는 핸드폰의 내부 구조를 알 필요가 없지요.  
핸드폰 관련 함수들을 제공해줌으로써 편하게 핸드폰을 사용할 수 있게 합니다.  
저 함수들을 가리켜 추상 자료형, 즉 ADT라고 합니다.  
자료구조에서 갑자기 이것을 포스팅 한 이유가 무엇일까요?  
학습 순서를 아래와 하는 것이 좋다고 (윤성우느님께서) 하기 때문입니다.

리스트를 예로들자면...
1. 리스트 자료구조의 ADT를 정의한다.
2. ADT를 근거로 리스트 자료구조를 활용하는 main 함수를 정의한다.
3. ADT를 근거로 리스트를 구현한다.

잘 알려진 추상 자료형에는 복소수, 리스트, 스택, 큐, 맵, 우선순위 큐, 집합 등이 있습니다.  

# 배열을 이용한 리스트의 구현(ADT)

리스트는 연결 리스트를 의미할까요?  
리스트는 크게 두가지로 나뉠 수 있으므로 정답은 '아니다' 입니다.  

- 순차 리스트 : 배열을 기반으로 구현된 리스트
- 연결 리스트 : 메모리의 동적 할당을 기반으로 구현된 리스트

자 여기서는 순차 리스트의 ADT를 정의해보도록 하겠습니다.  
우선 다음과 같은 특성을 알아야합니다.  
'리스트 자료구조는 데이터를 나란히 저장합니다. 그리고 중복된 데이터의 저장을 막지 않습니다.'  
자 그렇다면 다음으로는 리스트 자료구조의 ADT, 즉 리스트 자료구조가 제공해야 할 기능들을 생각해 보겠습니다.  
나란히 저장된다는 특성을 기반으로 제공해야 할 기능들을 정의해보도록 하겠습니다.  

```
typedef struct{
  LData arr[LIST_LEN];
  int numOfData;
  int curPosition;
} ArrayList;

void ListInit(List * plist);
// 초기화할 리스트의 주소값을 인자로 전달합니다.
// 리스트 생성 후에 초기화를 시켜주는 함수입니다.

void LInsert(List *plist, LData data);
// 매개변수 data를 통해 리스트에 데이터를 저장합니다.

int LFirst(List * plist, LData * data);
// 첫 번째 데이터가 pdata가 가리키는 메모리에 저장됩니다.
// 데이터의 참조를 위한 초기화가 진행됩니다. 성공시 1 실패시 0반환

int LNext(List * plist, LData * pdata);
// 참조된 데이터의 다음 데이터가 pdata가 가리키는 메모리에 저장됩니다.
// 순차적인 참조를 위해서 반복 호출이 가능합니다. 성공시 1 실패시 0반환
// 참조를 새로 시작하려면 먼저 LFirst 함수를 호출해야 합니다.

LData LRemove(List * plist);
// LFirst 또는 LNext 함수의 마지막 반환 데이터를 삭제합니다(삭제된 데이터는 반환됩니다).
// 마지막 데이터를 삭제하므로 연이은 반복 호출을 허용하지 않습니다.

int LCount(List * plist);
// 리스트에 저장되어 있는 데이터 수를 반환합니다.
```

대략적으로 아래와 같은 형태로 쓰일 것입니다.  
위에 선언된 함수가 잘 이해가 안가시는 것이 당연합니다.  
우선 한번씩 읽고 아래로 넘어가시면 이해가 되실 것입니다.  

```
# include <stdio.h>
# include "ArrayList.h"

int main(void){
    //ArrayList의 생성 및 초기화
    List list;
    int data;
    ListInit(&list);

    //데이터 저장
    LInsert(&list, 1);
    LInsert(&list, 2);
    LInsert(&list, 3);

    //저장된 데이터의 전체 출력
    printf("현재 저장된 데이터의 수: %d \n", LCount(&list)); //첫 번째 데이터 조회

    if(LFirst(&list, &data)){
        printf("%d", data);

        while(LNext(&list, &data)) // 두 번째 이후의 데이터 조회
            printf("%d", data);
    }

    //숫자 1을 탐색하여 모두 삭제
    if(LFirst(&list, &data)){
        if(data==22)
            LRemove(&list);
        while(LNext(&list, &data)){
            if(data ==22)
                LRemove(&list);
        }
    }

    return 0;

}
```

위를 보면 List를 기반으로 변수 list를 선언하고 있는데 이것을 리스트라고 지칭하겠습니다.  
왜 굳이 LFirst와 LNext를 나눈 것일까요?  
그 이유는 리스트 내에서 '데이터의 참조위치'를 기록하기 때문입니다.  
따라서 처음부터 참조를 새롭게 시작하기 위해서는 이 정보를 초기화 해야하고 LFirst가 그 일을 수행합니다.  

그럼 위의 예제를 ArrayList.h(리스트 자료구조의 헤더파일), ArrayList.c(리스트 자료구조의 소스파일), ListMain.c(리스트 관련 main 함수가 담긴 소스파일)로 나누어 보도록 하겠습니다(소스출처 : 윤성우님의 열혈자료구조 책).  

**ArrayList.h입니다.**

```
#ifndef __ARRAY_LIST_H__
#define __ARRAY_LIST_H__

#define TRUE	1
#define FALSE	0

/*** ArrayList의 정의 ****/
#define LIST_LEN	100
typedef int LData;

typedef struct __ArrayList
{
	LData arr[LIST_LEN];
	int numOfData;
	int curPosition;
} ArrayList;


/*** ArrayList와 관련된 연산들 ****/
typedef ArrayList List;

void ListInit(List * plist);
void LInsert(List * plist, LData data);

int LFirst(List * plist, LData * pdata);
int LNext(List * plist, LData * pdata);

LData LRemove(List * plist);
int LCount(List * plist);

#endif
```

**ArrayList.c입니다.**

```
#include <stdio.h>
#include "ArrayList.h"

void ListInit(List * plist)
{
	(plist->numOfData) = 0;
	(plist->curPosition) = -1;
}

void LInsert(List * plist, LData data)
{
	if(plist->numOfData > LIST_LEN)
	{
		puts("저장이 불가능합니다.");
		return;
	}

	plist->arr[plist->numOfData] = data;
	(plist->numOfData)++;
}

int LFirst(List * plist, LData * pdata)
{
	if(plist->numOfData == 0)
		return FALSE;

	(plist->curPosition) = 0;
	*pdata = plist->arr[0];
	return TRUE;
}

int LNext(List * plist, LData * pdata)
{
	if(plist->curPosition >= (plist->numOfData)-1)
		return FALSE;

	(plist->curPosition)++;
	*pdata = plist->arr[plist->curPosition];
	return TRUE;
}

LData LRemove(List * plist)
{
	int rpos = plist->curPosition;
	int num = plist->numOfData;
	int i;
	LData rdata = plist->arr[rpos];

	for(i=rpos; i<num-1; i++)
		plist->arr[i] = plist->arr[i+1];

	(plist->numOfData)--;
	(plist->curPosition)--;
	return rdata;
}

int LCount(List * plist)
{
	return plist->numOfData;
}
```

**main.c 입니다.**

```
#include <stdio.h>
#include "ArrayList.h"

int main(void)
{
	/*** ArrayList의 생성 및 초기화 ***/
	List list;
	int data;
	ListInit(&list);

	/*** 5개의 데이터 저장 ***/
	LInsert(&list, 11);  LInsert(&list, 11);
	LInsert(&list, 22);  LInsert(&list, 22);
	LInsert(&list, 33);

	/*** 저장된 데이터의 전체 출력 ***/
	printf("현재 데이터의 수: %d \n", LCount(&list));

	if(LFirst(&list, &data))    // 첫 번째 데이터 조회
	{
		printf("%d ", data);

		while(LNext(&list, &data))    // 두 번째 이후의 데이터 조회
			printf("%d ", data);
	}
	printf("\n\n");

	/*** 숫자 22을 탐색하여 모두 삭제 ***/
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

	/*** 삭제 후 저장된 데이터 전체 출력 ***/
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

물론 이러한 배열 기반의 리스트는 배열의 길이가 초기에 정해져야하고 변경이 불가하고 삭제의 과정에서 데이터의 이동(복사)이 빈번하게 일어난다는 단점이 있습니다(데이터가 삭제가 되면 그 시점부터 뒤에 있는 것들이 한 칸씩 앞으로 오기 때문입니다).  
하지만 데이터의 참조가 쉽고 인덱스 값을 기준으로 어디든 한 번에 참조가 가능하다는 장점도 있습니다.  
