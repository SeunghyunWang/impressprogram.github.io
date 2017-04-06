---
layout: post
comments: true
title: C++ String에 관한 이야기
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---

C++ string을 아주 잘 정리해주신 분이 있어서 글을 대신합니다.  
http://makerj.tistory.com/127  
위의 글에서 다른건 다 이해가 가실거고 string::find함수를 이용해 단어나 문자열을 찾다가 없으면 string::npos를 리턴한다는 것만 알고 계시면 됩니다.

# 위의 내용에 추가

```
strncpy(s1, s2, n)
s1에 s2의 n글자만큼을 복사합니다.
```
