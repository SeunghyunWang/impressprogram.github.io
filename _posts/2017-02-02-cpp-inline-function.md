---
layout: post
comments: true
title: C++ 인라인 함수 설명
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---
# 인라인 함수란 무엇일까요?  

컴파일러가 함수를 호출하는 대신, 그에 대응하는 함수 코드로 대체한다는 것을 의미하며(이해가 안가신다면 아래의 예제를!) 함수 호출없이 삽입된 함수 코드를 그 자리에서 처리하므로 해당 함수를 수행하기 위해 프로그램이 다른 주소로 점프했다가 되돌아 올 필요가 없어 속도면에서 유리합니다.  

인라인 함수는 프로그램의 실행 속도를 높이기 위해 추가된 기능이며 C언어의 매크로 함수와 비교됩니다.  
매크로 함수가 인라인 함수보다 뛰어난 점은 자료형에 구애받지 않는다는 것입니다(물론 인라인함수도 템플릿을 사용하면 자료형 문제를 해결할 수 있습니다).

매크로 함수가 선행처리기에 의해 처리된다면 인라인함수는 컴파일러에 의해 처리가 되어서 디버깅도 용이하다는 장점이 있습니다.  
그리고 컴파일러는 함수의 인라인화가 오히려 성능에 해가된다고 판단하면 무시하기도 하고(재귀함수이거나 덩치가 큰 함수) 도움이 된다고 생각하면 일부 함수들을 임의로 인라인 처리를 하기도 합니다(이래서 요새 사실 인라인함수의 기능이 자동화로 인해 그 의미가 많이 퇴색되었다고 합니다).  

인라인 함수는 다음과 같이 작성합니다.  

```
#include <iostream>

inline int SQUARE(int x){
  return x*x;
}

int main(void){
  std::cout << SQUARE(5) << std::endl;
  std::cout << SQUARE(12) << std::endl;
  return 0;
}
```

그렇다면 컴파일러에 의해 아래처럼 SQUARE부분이 치환됩니다.

```
#include <iostream>

inline int SQUARE(int x){
  return x*x;
}

int main(void){
  std::cout << 5*5 << std::endl;
  std::cout << 12*12 << std::endl;
  return 0;
}
```

위와 같이 짧은 코드에만 쓰는 것이 좋습니다.  
코드가 길다면 컴파일러가 소스마다 inline함수로 치환하는 과정에서 executable 코드가 기계어단에서 너무 길어져서 오히려 성능이 저하됩니다.  

# 실수하는 부분

주로 클래스를 해더파일과 함수 몸체부분으로 나눌 때 실수를 많이 합니다.  
말도 안되는 기능이지만 차의 아이디와 스피드를 입력받고 초기화해서 보여주는 클래스를 정의해 보겠습니다.  
헤더파일에 클래스 정의부를 담고, Car.cpp부분에 클래스 함수부분의 정의를 담고 main에서 호출하겠습니다.  
(참고 : const 선언은 이 함수 내에서는 멤버변수에 저장된 값을 변경하지 않겠다는 것을 의미합니다.)
```
//Car.h
#ifndef __CAR_H__
#define __CAR_H__

#include <iostream>
using namespace std;

class Car{
private:
  char ID[10];
  int speed;
public:
  void Init(char * ID, int speed);
  void Show() const;
}

#endif
```
```
//Car.cpp
#include <cstring>
#include "Car.h"
using namespace std;

void Car::Init (char * user_ID, int speed){
  strcpy(ID, user_ID);
  speed = 0;
}

inline void Car::Show() const{
  cout << "사용자 아이디 : " << ID << endl;
  cout << "스피드 :" << speed << endl;
}
```
```
//main.cpp
#include "Car.h"

int main(void){
  Car user1;
  user1.Init("kms",100);
  user1.Show();
}
```

언뜻보면 굉장히 자연스럽지만 큰 오류가 있습니다.  
인라인 함수의 특성때문인데요.  
인라인함수는 컴파일러에 의해서 해당 부분으로 대체가 됩니다.  
그리고 컴파일은 파일단위로 이루어지기 때문에 각 파일은 컴파일과정에서 서로를 간섭하지 않습니다.  
즉, inline으로 선언된 부분에 inline함수가 교체되어야 하는데 정의부분과 선언부분이 다른 파일에 있어서 오류가 나는 것입니다.  
따라서 inline정의 부분이 헤더파일에 같이 들어가야합니다.  
즉 아래처럼 바뀌어야 합니다.  

```
//Car.h
#ifndef __CAR_H__
#define __CAR_H__

#include <iostream>
using namespace std;

class Car{
private:
  char ID[10];
  int speed;
public:
  void Init(char * ID, int speed);
  void Show() const;
}

inline void Car::Show() const{
  cout << "사용자 아이디 : " << ID << endl;
  cout << "스피드 :" << speed << endl;
}

#endif
```
```
//Car.cpp
#include <cstring>
#include "Car.h"
using namespace std;

void Car::Init (char * user_ID, int speed){
  strcpy(ID, user_ID);
  speed = 0;
}

```
```
//main.cpp
#include "Car.h"

int main(void){
  Car user1;
  user1.Init("kms",100);
  user1.Show();
}
```
