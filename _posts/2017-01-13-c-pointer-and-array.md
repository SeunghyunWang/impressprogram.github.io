---
layout: post
comments: true
title: C언어- 배열과 포인터의 관계
categories: [c]
tags: [c, programming-language]
fullview: true
---
배열과 포인터는 엄청 밀접한 관계가 있습니다.  
배열의 이름이 사실 포인터이거든요.  
단, 그 값을 바꿀 수 없는 상수 형태의 포인터입니다.  
한마디로 아래의 코드가 성립되게 되는 것입니다.
```
int arr[3] = {11, 22, 33};
int * ptr = arr;
printf("%d %d %d \n",*ptr,*(ptr+1),*(ptr+2));
printf("%d %d %d \n",ptr[0], ptr[1], ptr[2]);
printf("%d %d %d \n",*arr,*(arr+1),*(arr+2));
printf("%d %d %d \n",arr[0], arr[1], arr[2]);
```
위의 printf 결과는 모두 11 22 33이 나오게 됩니다.  
아! 주소값에 1을 더했는데 왜 주소값이 4씩 더해지지 하는 분들을 위해 설명드리자면 포인터는 선언된 값을 메모리에 참조값으로 사용합니다.  
위의 경우에 int형 포인터이므로 4바이트 크기의 메모리를 정수의 형태로 참조하게 되는 것입니다.  
이유도 궁금하신 분은 아래의 블로그를 읽어보시는 것을 추천드립니다.  
그림과 함께 굉장히 쉽게 설명해 주셨습니다.  
http://citynetc.tistory.com/64
