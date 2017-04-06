---
layout: post
comments: true
title: C언어 컴파일 과정을 알아보자
categories: [c]
tags: [c, programming-language]
fullview: true
---
보통은 gcc main.c 이런식으로만 쳐줘도 실행파일이 나와서 딱히 신경을 안써도 쉽게 실행파일을 얻을 수 있었습니다.  
갑자기 어떤식으로 gcc가 실행파일을 만드는지 궁금해져서 알아보니 아래의 블로그에서 너무 친절하게 알려주셔서 주소를 공유합니다.  
http://shinluckyarchive.tistory.com/285  
굉장히 이해하기 쉽게 설명해주시네요!  
추가로 저 블로그에다가 보태자면 윈도우에서 gcc main.c라고 쳐서 나온 실행파일을 리눅스에서는 사용하지 못합니다.  
그 이유는 CPU 아키텍쳐가 같아도 OS에 따라서 OS가 바이너리를 메모리에 올려서 돌리는게 다 다르므로 호환 안됩니다.  
