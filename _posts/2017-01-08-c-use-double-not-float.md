---
layout: post
comments: true
title: C언어-float말고 double을 쓰는 이유
categories: [c]
tags: [c, programming-language]
fullview: true
---
정수형에서 int를 보편적으로 쓰듯이 실수형에서는 double을 주로 사용합니다.  
왜 float형보다 double형을 많이 사용할까요?  
float형은 4바이트를 차지하고 double형은 8바이트를 차지합니다.  
상대적으로 float가 덜 부담스럽긴하지만 그에 따라 숫자를 표현하는 범위도 float가 훨씬 작습니다.  
아래의 코드를 보면 알 수 있습니다.  
```
int main(int argc, const char * argv[]) {
    printf("%f \n",2147483648.0F);
    printf("%f \n",2147483648.0F-20);
    printf("%f \n",2147483648.0F-40);
    printf("%f \n",2147483648.0F-60);
    printf("%f \n",2147483648.0F-70);
    printf("%f \n",2147483648.0F-80);

    return 0;
}
```
결과값입니다.  
```결과값
2147483648.000000
2147483648.000000
2147483648.000000
2147483648.000000
2147483520.000000
2147483520.000000
```

오차가 상당하죠?  
float형은 10자리 넘어가면 정신을 못차립니다.  
왜 정신을 못차리는지는 https://impressprogram.github.io/c/2017/01/05/how-to-express-float-in-c.html 에서 다루었습니다.  
물론 double형보다도 long double형이 더 정밀하지만 12바이트의 공간을 차지해서 너무 부담스러워서 잘 쓰지 않습니다.  
