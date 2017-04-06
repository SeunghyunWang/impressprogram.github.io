---
layout: post
comments: true
title: C++ int형 인자에 문자입력시 생기는 오류 해결책
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---

아래의 코드는 홀수를 입력하기 전까지 정상적으로 "홀수가 아닙니다. 홀수로 다시 입력해주세요." 를 출력하며 입력값을 받을 것으로 예상되는 코드입니다.  
홀수가 입력되면 반복문을 빠져나가고 입력되기 전까지 입력 값을 받아보겠습니다.  

```
#include<iostream>

using namespace std;
int main()
{
    int n;

    cout<<"홀수를 입력해주세요." << endl;
    cout << "숫자 : ";
    cin>>n;

    // 홀수로 입력받았는지를 체크합니다.
    while (1)
    {

        cout<<"홀수가 아닙니다. 홀수로 다시 입력해주세요." << endl;
        cout << "숫자 : ";
        cin >> n;
        if(n%2==1)
            break;
    }

}
```

실제로 숫자만을 대상으로 한다면 잘 동작하지만 문자를 입력하는 순간 계속해서 "홀수가 아닙니다. 홀수로 다시 입력해주세요."를 출력합니다.  
이를 해결하려면 버퍼를 비워주어야합니다.  
버퍼가 무엇인지 모른다면 : https://impressprogram.github.io/c/2017/01/20/c-input-output.html  
버퍼를 비워주는 코드를 추가해보겠습니다.  

```
#include<iostream>
#include<limits>

using namespace std;

int main()
{
    int n;

    cout<<"홀수를 입력해주세요." << endl;
    cout << "숫자 : ";
    cin>>n;

    // 홀수로 입력받았는지를 체크합니다.
    while (1)
    {
        
        cout<<"홀수가 아닙니다. 홀수로 다시 입력해주세요." << endl;
        cout << "숫자 : ";
        cin >> n;
        if(n%2==1)
            break;
        //문자가 입력되었을경우 무한루프에 빠지지 않게 하기 위해서 버퍼를 비워줍니다.
        cin.clear();
        cin.ignore(numeric_limits<streamsize>::max(), '\n');
    }
}
```

cin.clear는 cin객체의 "내부 상태 플래그"를 초기화시켜서 cin이 정상적으로 작동하도록 합니다.
cin.ignore는 cin.ignore(100,'\n') 이런식으로 쓰며 100개의 문자가 입력되거나 엔터가 입력되기 전까지 입력 시퀀스에 있는 내용을 전부 추출해서 버려줍니다.  
numeric_limits<streamsize>::max()는 새로 추가한 헤더파일에서 제공하는 값입니다.  
엄청난 값이 입력되거나 엔터가 입력되기 전까지 입력 시퀀스에 있는 내용들을 전부 버려주는 것입니다.  
위의 코드를 추가함으로써 문자열을 입력하는 사용자도 고려해줄 수 있게 됩니다.
