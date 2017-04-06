---
layout: post
comments: true
title: C++ 소수점 아래 원하는 자리수 만큼 출력하기
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---

# precision
cout.precision(6); 과 같이 씁니다.  
괄호 안에 인자는 표현할 숫자를 의미합니다.  
아래의 예시를 보면 이해가 쉽게 갈 것입니다.  

```
#include <iostream>

using namespace std;

int main(){
    cout.precision(3);

    double a = 32222;
    cout.precision(1);
    cout << a << endl;

    cout.precision(2);
    cout << a << endl;

    cout.precision(3);
    cout << a << endl;

    return 0;
}
결과:
3e+04
3.2e+04
3.22e+04
```

# setf

cout.setf(ios::fixed); 를 사용하면 소수점 아래에 몇자리까지 출력해줄지 지정해줄 수 있습니다.  
cout.precision(숫자); 에서 숫자부분에 원하는 소수점 출력 자리수를 넣어주면 됩니다.  

```
#include <iostream>

using namespace std;

int main(){
    cout.setf(ios::fixed);

    double a = 32222;
    cout.precision(1);
    cout << a << endl;

    cout.precision(2);
    cout << a << endl;

    cout.precision(3);
    cout << a << endl;

    return 0;
}
결과 :
32222.0
32222.00
32222.000
```

# unsetf

cout.unsetf(ios::fixed)를 사용하면 cout.setf(ios::fixed); 를 취소시켜줍니다.  
즉, 이제 소수점 자리수를 강제하지 않습니다.  

```
#include <iostream>

using namespace std;

int main(){
    cout.setf(ios::fixed);
    cout.unsetf(ios::fixed);

    double a = 32222;
    cout.precision(1);
    cout << a << endl;

    cout.precision(2);
    cout << a << endl;

    cout.precision(3);
    cout << a << endl;


    return 0;
}

결과:
3e+04
3.2e+04
3.22e+04
```

위의 precision 예제와 결과가 같은 것을 확인할 수 있습니다.  
