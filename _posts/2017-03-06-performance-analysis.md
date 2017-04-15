---
layout: post
comments: true
title: 프로그래밍 성능 분석하기
categories: [datastructure]
tags: [c, datastructure]
fullview: true
---

국민대학교 우종우 교수님의 수업을 듣고 작성한 문서입니다.  

# Program 을 평가하는 요소  
(1) 본래의 개발요구사항의 충족여부  
(2) 정확성 (works correctly?)  
(3) 충분한 documentation 의 여부 (how to use it and how it works)  
(4) Logical unit 별로 module 화 여부  
(5) Readability  
(6) Space complexity (memory utilization, 효율성)  
(7) Time complexity (efficiency of running time)

# 알고리즘 분석시 고려사항  
(1) 공간 복잡도(space complexity)  
프로그램을 실행시켜 완료하는데 필요한 공간의 양으로 요새는 메모리가 넉넉해서 별로 고려를 하지 않는 경우도 많습니다.  

(2)시간 복잡도(time complexity)  
프로그램을 실행시켜 완료하는데 필요한 컴퓨터 시간의 소요시간입니다.  

# 공간 복잡도를 계산하는 방법  
프로그램의실행에필요한공간 : S(P)=c+SP(I)  
(S(P): 전체공간, c: 고정공간, SP(I): 가변공간)  

고정기억공간요구 : 프로그램입출력횟수나크기와관계없는공간요구 (fixed space requirements)  
ex) 프로그램 코드(명령어) 저장 공간, 단순변수, 고정 크기의 구조화 변수, 상수 등 저장할 공간  
 
가변공간요구: variablespacerequirements  
a. 가변크기의 구조체 (예: A[])  
b. 문제의 instance i 에 의존하는 공간  
c. recursion 의 경우 추가공간 소요 (지역변수, 매개변수, return address 등)

예제1)
```
float sum(float list[], int n)
{
float tsum = 0; int i;
for (i = 0; i < n; i++) tsum += list[i];
return tsum;
소요공간:
1) C/C++배열을 전달할 때, 배열의 주소를 전달함 (call by ref), 따라서 소요공간은 배열의 주소 값 => 4bytes
2) 매개변수 n, 지역변수 tsum, I 의 공간 (4*3=12 bytes) 
3) 가변 공간 없음... 모두 16bytes
}
```

예제2)
```
float rsum(float list[], int n)
{
if (n) return rsum(list, n-1) + list[n-1];
return 0;
}
소요공간:
1) 배열주소 list 값을 위한 data 변수 => 4bytes
2) 매개변수 n=4bytes
3) 반환주소 공간: 4bytes
4) 따라서 매회 호출시 마다 3*4=12bytes 소요됨. n 번 호출시, 총 소요공간은 12*n.
```

# 시간 복잡도를 계산하는 방법  
프로그램의 실행에 필요한 시간 (프로그램 P 에 의해 소요되는 시간 : T(P))  
(1) 컴파일 시간 + 실행 시간 (Tp) 으로 구성됨  
(2) 컴파일 시간은 프로그램의 특성에 영향없음 -> run time 만 고려
(3) 가장 좋은 방법: 시스템 clock 사용  
(4) 다른 방법: 프로그램이 수행하는 연산의 횟수계산  

예제1)
```
1. float sum (float list[], int n); 0
2. {                                0
3. float tempsum=0;                 1
4. int i;                           0
5. for(i=0;i<n;i++) n+1             n+1
6.      tempsum += list[i];         n
7. return tempsum;                  1
8. }                                0
Total Steps: 2n+3
```

예제2)
```
float sum(float list[], int n) 0
{                              0 
    float tempsum = 0;         1 
    int i;                     0
    for(i=0;i<n;i++)           n+1
        count += 2;            n
    count += 3;                1
    return 0;                  1
}
total steps in counts : 2n + 4 steps
```

# 점근 표기법

Ordernotation: 상수 인자나 적은 수의 자료는 무시하고 함수를 정의하거나 비교하는 방법:
1) 알고리즘의 시간 복잡도를 표기하기 위한 방법  
2) 알고리즘의 실제 수행시간이 아니라 명령어의 실행 빈도수를 함수로 표현한 것  
3) 동일한 일을 수행하는 2 개의 서로 다른 알고리즘의 time complexity 를 비교할 수 있다.  
4) 어떤 알고리즘의 특성변화에 따른 실행시간의 증가 추이를 예측할 수 있다.  
(Ex. 처리해야 할 자료의 개수 변화에 따른 실행시간 증가 추이 예측)

```
* Some Rule
1) 상수는 무시한다.
O ( cf(n)) = O(f(n)), ex) O(3n2) = O(n2)
2) 더 할 때는 max 를 택한다.
O(f(n) + g(n)) = O(max(f(n), g(n))
ex) O(2n3+108n)=O(n3)
3)O(f(n)) * O(g(n))=O(f(n)*g(n))
ex) O(n)*O(n-1)*O(nlogn) = O(n(n-1)nlogn) = O(n3logn)
```

예제1)
```
fori=2tondo a[i]=0      => n

fori=1tondo
    forj=1tondo 
        a[i]:=a[i]+a[j] => n^2
        
O(n)+O(n^2)=O(n^2)
```