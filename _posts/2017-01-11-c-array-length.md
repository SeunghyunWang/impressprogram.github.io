---
layout: post
comments: true
title: C언어에서 배열의 길이를 계산하는 방법
categories: [c]
tags: [c, programming-language]
fullview: true
---
개인적으로 스크립트 언어를 해오던 입장이라 배열의 길이를 구해주는 api가 기본적으로 제공되지 않는다는 것이 신선하게 느껴졌습니다.  
대신 아래와 같은 방식으로 배열의 길이를 계산하더군요.  
```배열이 int로 이루어져 있을 경우
arrLen = sizeof(arr)/sizeof(int);
```
우선 arr(배열)가 차지하는 size를 알아낸 뒤에 이 배열 한칸이 차지하는 size를 알아내서 나누어줍니다.  
이렇게 하면 C언어에서도 쉽게 배열의 길이를 계산할 수 있습니다.  
